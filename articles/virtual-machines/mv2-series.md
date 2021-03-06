---
title: Série Mv2 – Machines virtuelles Microsoft Azure
description: Spécifications pour les machines virtuelles de la série Mv2.
services: virtual-machines
author: ayshakeen
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 6fb0f93d3ac124b21bbc52ddc57bc720de6406e6
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2020
ms.locfileid: "78163894"
---
# <a name="mv2-series"></a>Série Mv2

La série Mv2 offre une plateforme à haut débit et faible latence sur un processeur Intel® Xeon® Platinum 8180M 2,5 GHz (Skylake) à technologie Hyper-thread avec une fréquence de base principale de 2,5 GHz et une fréquence turbo max de 3,8 GHz. Quelle que soit leur taille, toutes les machines virtuelles de la série Mv2 peuvent utiliser des disques persistants Standard et Premium. Les instances de série Mv2 sont des tailles de machine virtuelle à mémoire optimisée fournissant des performances de calcul inégalées pour prendre en charge des bases de données et des charges de travail en mémoire volumineuses, avec un ratio mémoire/processeur élevé idéal pour les serveurs de base de données relationnelle, les caches volumineux et l’analytique en mémoire.

Machines virtuelles de série Mv2 dotées de la technologie Hyper-Threading d’Intel®

Premium Storage : Prise en charge

Mise en cache du Stockage Premium : Prise en charge

Migration dynamique : Non prise en charge

Mises à jour avec préservation de la mémoire : Non prises en charge

Accélérateur d’écriture : [Pris en charge](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

|Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit de stockage temporaire et mis en cache max. : IOPS / MBps (taille du cache en Gio) | Débit du disque non mis en cache max. : IOPS / MBps | Nombre max de cartes réseau / Bande passante réseau attendue (MBps) |
|---|---|---|---|---|---|---|---|
| Standard_M208ms_v2<sup>1</sup> | 208 | 5700 | 4096 | 64 | 80000 / 800 (7040) | 40000 / 1000 | 8 / 16 000 |
| Standard_M208s_v2<sup>1</sup> | 208 | 2 850 | 4096 | 64 | 80000 / 800 (7040) | 40000 / 1000 | 8 / 16 000 |
| Standard_M416ms_v2<sup>1, 2</sup> | 416 | 11400 | 8 192 | 64 | 250000 / 1600 (14080) | 80000 / 2000 | 8 / 32000 |
| Standard_M416s_v2<sup>1, 2</sup> | 416 | 5700 | 8 192 | 64 | 250000 / 1600 (14080) | 80000 / 2000 | 8 / 32000 |

<sup>1</sup> Les machines virtuelles de la série Mv2 sont de la 2e génération uniquement. Si vous utilisez Linux, consultez [Support des machines virtuelles de 2e génération dans Azure](./linux/generation-2.md) pour obtenir des instructions sur la façon de rechercher et sélectionner une image.

<sup>2</sup> Pour les tailles M416ms_v2 et M416s_v2, notez qu’il existe une prise en charge initiale de l’image suivante uniquement : « GEN2 : SUSE Linux Enterprise Server (SLES) 12 SP4 pour applications SAP. »

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>Autres tailles

- [Usage général](sizes-general.md)
- [Mémoire optimisée](sizes-memory.md)
- [Optimisé pour le stockage](sizes-storage.md)
- [Optimisé pour le GPU](sizes-gpu.md)
- [Calcul haute performance](sizes-hpc.md)
- [Générations précédentes](sizes-previous-gen.md)

## <a name="next-steps"></a>Étapes suivantes

Lisez-en davantage sur les [Unités de calcul Azure (ACU)](acu.md) pour découvrir comment comparer les performances de calcul entre les références Azure.
