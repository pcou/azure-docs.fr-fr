---
title: (DÉCONSEILLÉ) Superviser un cluster Azure Container Service avec Sysdig
description: Surveillez un cluster Azure Container Service avec Sysdig.
author: sauryadas
ms.service: container-service
ms.topic: conceptual
ms.date: 08/08/2016
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: a22d48554573e2517b318f6172b759864bf46612
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2020
ms.locfileid: "76277730"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-sysdig"></a>(DÉCONSEILLÉ) Superviser un cluster Azure Container Service avec Sysdig

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Dans cet article, nous allons déployer des agents Sysdig sur tous les nœuds d’agent de votre cluster Azure Container Service. Vous avez besoin d’un compte Sysdig pour cette configuration. 

## <a name="prerequisites"></a>Conditions préalables requises
[Déployez](container-service-deployment.md) et [connectez](../container-service-connect.md) un cluster configuré par Azure Container Service. Explorez [l’interface utilisateur Marathon](container-service-mesos-marathon-ui.md). Accédez à [https://app.sysdigcloud.com](https://app.sysdigcloud.com) pour configurer un compte cloud Sysdig. 

## <a name="sysdig"></a>Sysdig
Sysdig est un service qui vous permet de surveiller vos conteneurs au sein de votre cluster. Sysdig est connu pour aider à résoudre les problèmes, mais il propose également des mesures de surveillance de base pour le processeur, la mise en réseau, la mémoire et les E/S. Sysdig permet de voir facilement quels conteneurs travaillent le plus ou utilisent essentiellement le plus de mémoire et de ressources processeur. Cette vue se trouve dans la section Overview (Vue d’ensemble), actuellement disponible en version bêta. 

![IU Sysdig](./media/container-service-monitoring-sysdig/sysdig6.png) 

## <a name="configure-a-sysdig-deployment-with-marathon"></a>Configurer un déploiement Sysdig avec Marathon
Ces étapes vous expliquent comment configurer et déployer des applications Sysdig dans votre cluster avec Marathon. 

Accédez à l’IU DC/OS via [http://localhost:80/](http://localhost:80/). Une fois dans l’IU DC/OS, accédez à Universe (Univers) en bas à gauche de la page, puis recherchez « Sysdig ».

![Sysdig dans l’Univers DC/OS](./media/container-service-monitoring-sysdig/sysdig1.png)

Pour terminer la configuration, vous avez besoin d’un compte cloud ou d’un compte d’essai gratuit Sysdig. Une fois connecté au site web du cloud Sysdig, cliquez sur votre nom d’utilisateur. La page qui s’affiche comporte votre clé d’accès (Access Key). 

![Clé d’API Sysdig](./media/container-service-monitoring-sysdig/sysdig2.png) 

Entrez votre clé d’accès dans la configuration de Sysdig dans l’Univers DC/OS. 

![Configuration de Sysdig dans l’Univers DC/OS](./media/container-service-monitoring-sysdig/sysdig3.png)

Définissez le nombre d’instances sur 10000000 pour que chaque fois qu’un nouveau nœud est ajouté au cluster, Sysdig déploie automatiquement un agent sur ce nouveau nœud. Cette solution temporaire permet de garantir le déploiement de Sysdig sur tous les nouveaux agents au sein du cluster. 

![Configuration de Sysdig dans l’Univers DC/OS : instances](./media/container-service-monitoring-sysdig/sysdig4.png)

Une fois le package installé, revenez à l’IU Sysdig. Vous devez être en mesure d’explorer les différentes mesures d’utilisation pour les conteneurs de votre cluster. 

Vous pouvez également installer des tableaux de bords spécifiques de Mesos et Marathon en utilisant [l’Assistant Nouveau tableau de bord](https://app.sysdigcloud.com/#/dashboards/new).
