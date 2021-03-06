---
title: Exécuter Azure Functions à partir d’un package
description: Exécutez vos fonctions avec le runtime Azure Functions, en montant un fichier de package de déploiement qui contient les fichiers projet de votre application de fonction.
ms.topic: conceptual
ms.date: 07/15/2019
ms.openlocfilehash: 3ae287939f22469b03f0e10f184f067274464905
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/11/2020
ms.locfileid: "79087020"
---
# <a name="run-your-azure-functions-from-a-package-file"></a>Exécuter des fonctions Azure à partir d’un fichier de package

Dans Azure, vous pouvez exécuter vos fonctions directement dans un fichier de package de déploiement de votre application de fonction. L’autre option consiste à déployer vos fichiers dans le répertoire `d:\home\site\wwwroot` de votre application de fonction.

Cet article décrit les avantages que présente l’exécution de vos fonctions à partir d’un package. Il explique également comment activer cette fonctionnalité dans votre application de fonction.

> [!IMPORTANT]
> Lorsque vous déployez vos fonctions vers une application de fonction Linux dans un abonnement [Premium](functions-scale.md#premium-plan), vous devez toujours exécuter à partir du fichier de package et [publier votre application en utilisant Azure Functions Core Tools](functions-run-local.md#project-file-deployment).

## <a name="benefits-of-running-from-a-package-file"></a>Avantages de l’exécution à partir d’un fichier de package
  
L’exécution à partir d’un fichier de package présente plusieurs avantages :

+ Réduction des risques de verrouillage lors de la copie de fichiers
+ Déploiement possible sur une application de production (après redémarrage)
+ Connaissance des fichiers qui sont exécutés dans votre application
+ Amélioration des performances des [déploiements Azure Resource Manager](functions-infrastructure-as-code.md)
+ Peut réduire les temps de démarrage à froid, en particulier pour les fonctions JavaScript avec des arborescences de package npm de grande taille.

Pour plus d’informations, lisez [cette annonce](https://github.com/Azure/app-service-announcements/issues/84).

## <a name="enabling-functions-to-run-from-a-package"></a>Permettre aux fonctions de s’exécuter à partir d’un package

Pour permettre à votre application de fonction de s’exécuter à partir d’un package, il suffit d’ajouter un paramètre `WEBSITE_RUN_FROM_PACKAGE` à vos paramètres d’application de fonction. Le paramètre `WEBSITE_RUN_FROM_PACKAGE` doit avoir l’une des valeurs suivantes :

| Valeur  | Description  |
|---------|---------|
| **`1`**  | Recommandé pour les applications de fonction qui s’exécutent sur Windows. Lancez l’exécution à partir d’un fichier de package dans le dossier `d:\home\data\SitePackages` de votre application de fonction. Si vous ne [déployez pas le fichier avec la fonction de déploiement zip](#integration-with-zip-deployment), le dossier doit également contenir un fichier appelé `packagename.txt` pour que cette option puisse être utilisée. Ce fichier contient uniquement le nom du fichier de package du dossier, sans espace. |
|**`<URL>`**  | Emplacement d’un fichier de package que vous souhaitez exécuter. Lorsque vous utilisez le stockage Blob, vous devez utiliser un conteneur privé avec une [signature d’accès partagé (SAP)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) pour permettre au runtime Functions d’accéder au package. Vous pouvez utiliser [l’Explorateur Stockage Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) pour charger des fichiers de package sur votre compte Stockage Blob. Lorsque vous spécifiez une URL, vous devez également [synchroniser les déclencheurs](functions-deployment-technologies.md#trigger-syncing) après avoir publié un package mis à jour. |

> [!CAUTION]
> Lorsque vous exécutez une application de fonction sur Windows, l’option d’URL externe génère les performances de démarrage à froid les moins élevées. Lorsque vous déployez votre application de fonction sur Windows, vous devez définir `WEBSITE_RUN_FROM_PACKAGE` sur `1` et effectuer la publication avec le déploiement zip.

L’exemple suivant montre une application de fonction configurée pour s’exécuter à partir d’un fichier zip hébergé dans le stockage Blob Azure :

![Paramètre d’application WEBSITE_RUN_FROM_ZIP](./media/run-functions-from-deployment-package/run-from-zip-app-setting-portal.png)

> [!NOTE]
> Actuellement, seuls les fichiers de package zip sont pris en charge.

## <a name="integration-with-zip-deployment"></a>Intégration au déploiement zip

Le [déploiement zip][Zip deployment for Azure Functions] est une fonctionnalité d’Azure App Service qui vous permet de déployer votre projet d’application de fonction dans le répertoire `wwwroot`. Le projet est empaqueté dans un fichier de déploiement zip. Les mêmes API peuvent être utilisées pour déployer votre package dans le dossier `d:\home\data\SitePackages`. Quand le paramètre d’application `WEBSITE_RUN_FROM_PACKAGE` est défini sur `1`, les API de déploiement zip copient votre package dans le dossier `d:\home\data\SitePackages` au lieu d’extraire les fichiers dans `d:\home\site\wwwroot`. Le fichier `packagename.txt` est également créé. Après un redémarrage, le package est monté sur `wwwroot` en tant que système de fichiers en lecture seule. Pour plus d’informations sur le déploiement zip, consultez [Déploiement zip pour Azure Functions](deployment-zip-push.md).

## <a name="adding-the-website_run_from_package-setting"></a>Ajout du paramètre WEBSITE_RUN_FROM_PACKAGE

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

### <a name="use-key-vault-references"></a>Utiliser des références Key Vault

Pour plus de sécurité, vous pouvez utiliser des références Key Vault en conjonction avec votre URL externe. L'URL reste ainsi chiffrée au repos et vous pouvez tirer parti de Key Vault pour la gestion et la rotation des secrets. Nous vous recommandons d'utiliser le stockage Blob Azure afin de pouvoir faire pivoter facilement la clé SAS associée. Le stockage Blob Azure est chiffré au repos, ce qui protège vos données d'application lorsqu'elles ne sont pas déployées sur App Service.

1. Créez un coffre de clés Azure.

    ```azurecli
    az keyvault create --name "Contoso-Vault" --resource-group <group-name> --location eastus
    ```

1. Ajoutez votre URL externe en tant que secret dans Key Vault.

    ```azurecli
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<insert-your-URL>"
    ```

1. Créez le paramètre d'application `WEBSITE_RUN_FROM_PACKAGE` et définissez la valeur comme référence Key Vault à l'URL externe.

    ```azurecli
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"
    ```

Pour plus d'informations, consultez les articles suivants.

- [Références Key Vault pour App Service](../app-service/app-service-key-vault-references.md)
- [Chiffrement du stockage Azure pour les données au repos](../storage/common/storage-service-encryption.md)

## <a name="troubleshooting"></a>Dépannage

- L’option d’exécution à partir d’un package rend `wwwroot` accessible en lecture seule. Donc, vous recevez une erreur lors de l’écriture de fichiers dans ce répertoire.
- Les formats TAR et GZIP ne sont pas pris en charge.
- Cette fonctionnalité ne tient pas compte du cache local.
- Pour améliorer les performances de démarrage à froid, utilisez l’option zip locale (`WEBSITE_RUN_FROM_PACKAGE`= 1).
- L’option d’exécution à partir d’un package n’est pas compatible avec l’option de personnalisation du déploiement (`SCM_DO_BUILD_DURING_DEPLOYMENT=true`), l’étape de génération est donc ignorée pendant le déploiement.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Déploiement continu pour Azure Functions](functions-continuous-deployment.md)

[Zip deployment for Azure Functions]: deployment-zip-push.md
