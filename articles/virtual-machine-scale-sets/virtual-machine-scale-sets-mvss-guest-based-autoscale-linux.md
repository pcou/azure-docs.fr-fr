---
title: Utiliser la mise à l’échelle automatique Azure avec des mesures invitées dans un modèle de groupe identique Linux
description: Découvrez comment effectuer la mise à l’échelle automatique en utilisant des mesures invitées dans un modèle de groupe de machines virtuelles identiques Linux
author: mayanknayar
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: manayar
ms.openlocfilehash: 88f839b281e4d345b012a7e6acff3770dc536045
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2020
ms.locfileid: "76271951"
---
# <a name="autoscale-using-guest-metrics-in-a-linux-scale-set-template"></a>Mise à l’échelle automatique en utilisant des mesures invitées dans un modèle de groupe identique Linux

Il existe deux grands types de mesures dans Azure, collectées à partir des machines virtuelles et des groupes identiques : les mesures d’hôte et invitées. En général, si vous utilisez des mesures de processeur, de disque et de réseau standard, les mesures d’hôte sont bien adaptées. Si, en revanche, vous avez besoin d’un éventail plus large de mesures, alors les mesures invitées conviennent mieux.

Les mesures d’hôte ne requièrent aucune configuration supplémentaire, car elles sont collectées par la machine virtuelle hôte, tandis que les mesures invitées nécessitent d’installer [l’extension Diagnostics Azure pour Windows](../virtual-machines/windows/extensions-diagnostics-template.md) ou [l’extension Diagnostics Azure pour Linux](../virtual-machines/linux/diagnostic-extension.md) sur la machine virtuelle invitée. L’utilisation des mesures invitées à la place des mesures hôtes est courante, car les premières sont plus variées que les dernières. Les mesures de consommation de la mémoire, notamment, ne sont disponibles que via les mesures invitées. Les mesures hôtes prises en charge sont répertoriées [ici](../azure-monitor/platform/metrics-supported.md), et les mesures invitées couramment utilisées [ici](../azure-monitor/platform/autoscale-common-metrics.md). Cet article explique comment modifier le [modèle de groupe identique viable de base](virtual-machine-scale-sets-mvss-start.md) pour qu’il utilise des règles de mise à l’échelle automatique en fonction des mesures invitées des groupes identiques Linux.

## <a name="change-the-template-definition"></a>Modifier la définition du modèle

Dans un [article précédent](virtual-machine-scale-sets-mvss-start.md), nous avions créé un modèle de groupe identique de base. Nous allons maintenant utiliser ce modèle antérieur et le modifier pour créer un modèle qui déploie un groupe identique Linux avec une mise à l’échelle automatique basée sur des mesures invitées.

Ajoutez tout d’abord les paramètres de `storageAccountName` et `storageAccountSasToken`. L’agent de diagnostics stocke les données métriques dans une [table](../cosmos-db/table-storage-how-to-use-dotnet.md) de ce compte de stockage. À compter de la version 3.0 de l’agent de diagnostic Linux, l’utilisation d’une clé d’accès de stockage n’est plus prise en charge. Utilisez plutôt un [jeton SAP](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

```diff
     },
     "adminPassword": {
       "type": "securestring"
+    },
+    "storageAccountName": {
+      "type": "string"
+    },
+    "storageAccountSasToken": {
+      "type": "securestring"
     }
   },
```

Ensuite, modifiez le groupe identique `extensionProfile` pour y inclure l’extension Diagnostics. Dans cette configuration, spécifiez l’ID de ressource du groupe identique à partir duquel collecter les métriques, ainsi que le compte de stockage et le jeton SAP à utiliser pour les stocker. Indiquez la fréquence d’agrégation des métriques (dans ce cas, chaque minute) et les métriques à suivre (dans ce cas, le pourcentage de mémoire utilisée). Pour plus d’informations sur cette configuration et les mesures autres que le pourcentage de mémoire utilisée, consultez [cette documentation](../virtual-machines/linux/diagnostic-extension.md).

```diff
                 }
               }
             ]
+          },
+          "extensionProfile": {
+            "extensions": [
+              {
+                "name": "LinuxDiagnosticExtension",
+                "properties": {
+                  "publisher": "Microsoft.Azure.Diagnostics",
+                  "type": "LinuxDiagnostic",
+                  "typeHandlerVersion": "3.0",
+                  "settings": {
+                    "StorageAccount": "[parameters('storageAccountName')]",
+                    "ladCfg": {
+                      "diagnosticMonitorConfiguration": {
+                        "performanceCounters": {
+                          "sinks": "WADMetricJsonBlob",
+                          "performanceCounterConfiguration": [
+                            {
+                              "unit": "percent",
+                              "type": "builtin",
+                              "class": "memory",
+                              "counter": "percentUsedMemory",
+                              "counterSpecifier": "/builtin/memory/percentUsedMemory",
+                              "condition": "IsAggregate=TRUE"
+                            }
+                          ]
+                        },
+                        "metrics": {
+                          "metricAggregation": [
+                            {
+                              "scheduledTransferPeriod": "PT1M"
+                            }
+                          ],
+                          "resourceId": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]"
+                        }
+                      }
+                    }
+                  },
+                  "protectedSettings": {
+                    "storageAccountName": "[parameters('storageAccountName')]",
+                    "storageAccountSasToken": "[parameters('storageAccountSasToken')]",
+                    "sinksConfig": {
+                      "sink": [
+                        {
+                          "name": "WADMetricJsonBlob",
+                          "type": "JsonBlob"
+                        }
+                      ]
+                    }
+                  }
+                }
+              }
+            ]
           }
         }
       }
```

Enfin, ajoutez une ressource `autoscaleSettings` pour configurer la mise à l’échelle automatique en fonction de ces métriques. Cette ressource contient une clause `dependsOn` qui fait référence au groupe identique pour s’assurer que ce groupe existe avant d’essayer de le soumettre à une mise à l’échelle automatique. Pour baser la mise à l’échelle automatique sur une autre métrique, utilisez la propriété `counterSpecifier` de la configuration de l’extension Diagnostics en tant que propriété `metricName` dans la configuration de la mise à l’échelle automatique. Pour plus d’informations sur la configuration de la mise à l’échelle automatique, consultez les [meilleures pratiques en matière de mise à l’échelle automatique](../azure-monitor/platform/autoscale-best-practices.md) et la [documentation de référence de l’API REST Azure Monitor](/rest/api/monitor/autoscalesettings).

```diff
+    },
+    {
+      "type": "Microsoft.Insights/autoscaleSettings",
+      "apiVersion": "2015-04-01",
+      "name": "guestMetricsAutoscale",
+      "location": "[resourceGroup().location]",
+      "dependsOn": [
+        "Microsoft.Compute/virtualMachineScaleSets/myScaleSet"
+      ],
+      "properties": {
+        "name": "guestMetricsAutoscale",
+        "targetResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]",
+        "enabled": true,
+        "profiles": [
+          {
+            "name": "Profile1",
+            "capacity": {
+              "minimum": "1",
+              "maximum": "10",
+              "default": "3"
+            },
+            "rules": [
+              {
+                "metricTrigger": {
+                  "metricName": "/builtin/memory/percentUsedMemory",
+                  "metricNamespace": "",
+                  "metricResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]",
+                  "timeGrain": "PT1M",
+                  "statistic": "Average",
+                  "timeWindow": "PT5M",
+                  "timeAggregation": "Average",
+                  "operator": "GreaterThan",
+                  "threshold": 60
+                },
+                "scaleAction": {
+                  "direction": "Increase",
+                  "type": "ChangeCount",
+                  "value": "1",
+                  "cooldown": "PT1M"
+                }
+              },
+              {
+                "metricTrigger": {
+                  "metricName": "/builtin/memory/percentUsedMemory",
+                  "metricNamespace": "",
+                  "metricResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]",
+                  "timeGrain": "PT1M",
+                  "statistic": "Average",
+                  "timeWindow": "PT5M",
+                  "timeAggregation": "Average",
+                  "operator": "LessThan",
+                  "threshold": 30
+                },
+                "scaleAction": {
+                  "direction": "Decrease",
+                  "type": "ChangeCount",
+                  "value": "1",
+                  "cooldown": "PT1M"
+                }
+              }
+            ]
+          }
+        ]
+      }
     }
   ]
 }
```





## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
