---
title: Télécharger des profils VPN Azure Virtual WAN mondiaux ou basés sur des hubs | Microsoft Docs
description: Découvrez plus d’informations sur la connectivité branche à branche scalable et automatisée de Virtual WAN, les régions disponibles et les partenaires.
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 1/31/2020
ms.author: alzam
ms.openlocfilehash: 3b7e765dbd024d46939e8989993f0c882b2a8f4b
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/02/2020
ms.locfileid: "76965346"
---
# <a name="download-a-global-or-hub-based-profile-for-user-vpn-clients"></a>Télécharger un profil mondial ou basé sur un hub pour les clients VPN utilisateurs

Azure Virtual WAN offre deux types de connectivité pour les utilisateurs distants : mondial et basé sur un hub. Utilisez les sections suivantes pour en savoir plus sur ce sujet et télécharger un profil.

## <a name="global-profile"></a>Profil mondial

Le profil pointe vers un équilibreur de charge qui comprend tous les hubs VPN utilisateurs actifs. L’utilisateur est dirigé vers le hub le plus proche de l’emplacement géographique de l’utilisateur. Ce type de connectivité est utile lorsque les utilisateurs se rendent fréquemment dans différents endroits. Pour télécharger le profil **mondial** :

1. Accédez au WAN virtuel.
2. Cliquez sur **Configuration du VPN utilisateur**.
3. Mettez en surbrillance la configuration pour laquelle vous souhaitez télécharger le profil.
4. Cliquez sur **Télécharger le profil VPN utilisateur du WAN virtuel**.

   ![Profil mondial](./media/global-hub-profile/global1.png)

## <a name="hub-based-profile"></a>Profil basé sur un hub

Le profil pointe vers un hub unique. L’utilisateur peut se connecter uniquement au hub en question à l’aide de ce profil. Pour télécharger le profil **basé sur un hub** :

1. Accédez au WAN virtuel.
2. Dans la page de présentation, cliquez sur **Hub**.

    ![Profil du hub n° 1](./media/global-hub-profile/hub1.png)
3. Cliquez sur **VPN utilisateur (point à site)** .
4. Cliquez sur **Télécharger le profil VPN utilisateur du hub virtuel**.

   ![Profil du hub n° 2](./media/global-hub-profile/hub2.png)
5. Cochez la case **EAPTLS**.
6. Cliquez sur **Générer et télécharger un profil**.

   ![Profil du hub n° 3](./media/global-hub-profile/download.png)

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le WAN virtuel, consultez la page [Vue d'ensemble de WAN virtuel](virtual-wan-about.md).
