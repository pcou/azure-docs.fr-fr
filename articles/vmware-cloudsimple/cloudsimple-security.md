---
title: Azure VMware Solutions (AVS) - Sécurité des services AVS
description: Décrit les modèles de responsabilité partagée pour la sécurité des services AVS
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d2d45f827d4165175a4a5429f0b9393a55e2ff1e
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024991"
---
# <a name="avs-security-overview"></a>Vue d'ensemble de la sécurité AVS

Cet article présente une vue d’ensemble de l’implémentation de la sécurité sur le service, l’infrastructure et le centre de données Azure VMware Solution by AVS. Vous allez en apprendre plus sur la protection et la sécurité de vos données, la sécurité réseau et la gestion des correctifs et vulnérabilités.

## <a name="shared-responsibility"></a>Responsabilité partagée

Azure VMware Solution by AVS utilise un modèle de responsabilité partagée pour la sécurité. Une sécurité fiable dans le cloud est possible via les responsabilités partagées des clients et de Microsoft en tant que fournisseur de service. Cette matrice de responsabilité garantit une sécurité optimisée et élimine les points de défaillance uniques.

## <a name="azure-infrastructure"></a>Infrastructure Azure

Les considérations en matière de sécurité de l’infrastructure Azure incluent l’emplacement des centres de données et des équipements.

### <a name="datacenter-security"></a>Sécurité des centres de données

Microsoft compte une division entière dédiée à la conception, à la création et au fonctionnement des installations physiques qui gèrent Azure. Cette équipe est investie dans la conservation d’une sécurité physique à la pointe. Pour avoir des détails sur la sécurité physique, consultez [Sécurité locale et physique des centres de données Azure](../security/azure-physical-security.md).

### <a name="equipment-location"></a>Emplacement de l’équipement

L’équipement matériel nu qui exécute vos clouds privés AVS est hébergé dans des emplacements de centre de données Azure. L’authentification à deux facteurs basée sur la biométrie est requise pour accéder aux cages où se trouve cet équipement.

## <a name="dedicated-hardware"></a>Matériel dédié

Dans le cadre du service AVS, tous les clients AVS profitent d’hôtes nus dédiés avec des disques locaux joints physiquement isolés du matériel d’autres clients. Un hyperviseur ESXi avec vSAN s’exécute sur chaque nœud. Les nœuds sont gérés via les services dédiés au client VMware, vCenter et NSX. Ne partagez pas de matériel entre locataires pour profiter d’une couche supplémentaire d’isolement et de protection de sécurité.

## <a name="data-security"></a>Sécurité des données

Les clients conservent le contrôle et demeurent propriétaires de leurs données. La gestion des données client est de la responsabilité du client.

### <a name="data-protection-for-data-at-rest-and-data-in-motion-within-internal-networks"></a>Protection des données au repos et des données en mouvement au sein de réseaux internes

Pour les données au repos dans l’environnement de cloud privé AVS, vous pouvez utiliser le chiffrement vSAN. Le chiffrement vSAN fonctionne avec des serveurs de gestion de clé externes certifiés par VMware (KMS) dans votre réseau virtuel ou local. Vous contrôlez vous-même les clés de chiffrement de données. Pour les données en mouvement dans le cloud privé AVS, vSphere prend en charge le chiffrement des données sur le réseau pour tout trafic VMkernel (notamment le trafic vMotion).

### <a name="data-protection-for-data-that-is-required-to-move-through-public-networks"></a>Protection des données requises pour vous déplacer dans les réseaux publics

Pour protéger les données transmises via des réseaux publics, vous pouvez créer des tunnels VPN SSL et IPsec pour vos clouds privés AVS. Les méthodes de chiffrement courantes, comme le chiffrement AES 128 bits ou 256 bits, sont prises en charge. Les données en transit (notamment les données d’authentification, d’accès administrateur et client) sont chiffrées avec des mécanismes de chiffrement standard (SSH, TLS 1.2 et RDP sécurisé). Les communications véhiculant des informations sensibles utilisent des mécanismes de chiffrement standard.

### <a name="secure-disposal"></a>Élimination en toute sécurité

Si votre service AVS expire ou est interrompu, vous êtes responsable de la suppression de vos données. AVS collaborera avec vous pour supprimer ou retourner toutes les données client comme indiqué dans le contrat de client, sauf si la loi exige d'AVS la conservation, partielle ou totale, des données personnelles. S’il est nécessaire de conserver des données personnelles, AVS archivera les données et implémente des mesures raisonnables pour empêcher tout traitement supplémentaire des données client.

### <a name="data-location"></a>Emplacement des données

Lorsque vous configurez vos clouds privés AVS, vous choisissez la région Azure où ils sont déployés. Les données de machine virtuelle VMware ne sont pas déplacées depuis ce centre de données physique, sauf si vous effectuez la migration des données ou la sauvegarde des données hors site. Vous pouvez également héberger des charges de travail et stocker des données dans plusieurs régions Azure, si vous en avez besoin.

Les données client qui résident dans des nœuds hyperconvergés de cloud privé AVS ne transitent pas par des emplacements sans action explicite de l’administrateur. Il vous incombe d’implémenter vos charges de travail de manière hautement disponible.

### <a name="data-backups"></a>Sauvegarde de données

AVS ne sauvegarde ni n’archive pas les données client. AVS effectue une sauvegarde périodique des données vCenter et NSX pour fournir une haute disponibilité des serveurs d’administration. Avant la sauvegarde, toutes les données sont chiffrées au niveau de la source de vCenter à l’aide des APIs VMware. Les données chiffrées sont transportées et stockées dans un objet blob Azure. Les clés de chiffrement pour les sauvegardes sont stockées dans un coffre managé AVS hautement sécurisé s’exécutant dans le réseau virtuel AVS dans Azure.

## <a name="network-security"></a>Sécurité réseau

La solution AVS repose sur les couches de sécurité réseau.

### <a name="azure-edge-security"></a>Sécurité de périphérie Azure

Les services AVS reposent sur la sécurité réseau de base fournie par Azure. Azure applique des techniques de défense profonde et des réponses en temps utile aux attaques réseau associées à des modèles de trafic entrant ou sortant anormaux et aux attaques de déni de service distribué (DDoS). Ce contrôle de sécurité s’applique aux environnements de cloud privé AVS et au logiciel de plan de contrôle développé par AVS.

### <a name="segmentation"></a>Segmentation

Le service AVS possède des réseaux de couche 2 logiquement distincts qui limitent l’accès à vos propres réseaux privés dans votre environnement de cloud privé AVS. Vous pouvez protéger davantage vos réseaux cloud privé AVS à l’aide d’un pare-feu. Le portail AVS vous permet de définir des règles de contrôle de trafic réseau EO et NS pour tout le trafic réseau, notamment le trafic cloud intra-privé AVS, le trafic cloud inter-privé AVS, le trafic général vers Internet et le trafic réseau au niveau local sur la connexion VPN IPsec ou ExpressRoute.

## <a name="vulnerability-and-patch-management"></a>Gestion des correctifs et des vulnérabilités

AVS est responsable de la mise à jour corrective de sécurité des logiciels VMware gérés (ESXi, vCenter et NSX).

## <a name="identity-and-access-management"></a>Gestion de l’identité et de l’accès

Les clients peuvent s’authentifier à leur compte Azure (dans Azure AD) à l’aide de l’authentification multifacteur ou l’authentification unique (selon préférence). À partir du portail Azure, vous pouvez lancer le portail AVS sans avoir à saisir les informations d’identification de nouveau.

AVS prend en charge la configuration facultative d’une source d’identité pour le vCenter du cloud privé AVS. Vous pouvez utiliser une [source d’identité locale](set-vcenter-identity.md), une nouvelle source d’identité pour le cloud privé AVS, ou [Azure AD](azure-ad.md).

Par défaut, les clients reçoivent les privilèges nécessaires pour les opérations quotidiennes de vCenter dans le cloud privé AVS. Ce niveau d’autorisation n’inclut pas l’accès d’administration à vCenter. Si l’accès d’administration est temporairement requis, vous pouvez [élever vos privilèges](escalate-private-cloud-privileges.md) pendant une période limitée, le temps d’effectuer les tâches d’administration.
