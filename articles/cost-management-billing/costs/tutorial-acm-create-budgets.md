---
title: Tutoriel - Créer et gérer des budgets Azure | Microsoft Docs
description: Ce tutoriel vous aide à planifier et à prendre en compte les coûts de services Azure que vous consommez.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/11/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: adwise
ms.custom: seodec18
ms.openlocfilehash: b81236fd63d9289f797056cf7aaceb7d826511af
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/11/2020
ms.locfileid: "79128354"
---
# <a name="tutorial-create-and-manage-azure-budgets"></a>Tutoriel : Créer et gérer des budgets Azure

Les budgets dans Cost Management vous aident à planifier et à suivre la comptabilité de l’organisation. Avec les budgets, vous pouvez prendre en compte les services Azure que vous consommez ou auxquels vous vous abonnez pendant une période spécifique. Ils vous permettent d’informer les autres utilisateurs de leurs dépenses pour gérer les coûts de manière proactive, ainsi que pour superviser la progression des dépenses. En cas de dépassement des seuils budgétaires que vous avez créés, seules des notifications sont déclenchées. Aucune de vos ressources n’est affectée et votre consommation n’est pas arrêtée. Vous pouvez utiliser des budgets pour comparer et suivre les dépenses lors de l’analyse des coûts.

Les données de coûts et d’utilisation sont généralement disponibles dans un délai de 12 à 16 heures, et les budgets sont évalués par rapport à ces coûts toutes les quatre heures. Les notifications par e-mail sont normalement reçues dans un délai de 12 à 16 heures.

Les budgets sont automatiquement réinitialisés à la fin d’une période (mensuelle, trimestrielle ou annuelle) pour le même montant lorsque vous sélectionnez une date d’expiration ultérieure. Étant donné qu’ils sont réinitialisés avec le même montant de budget, vous devez créer des budgets distincts quand les montants budgétisés diffèrent pour des périodes ultérieures.

Les exemples de ce tutoriel expliquent comment créer et modifier un budget pour un abonnement Azure Contrat Entreprise (EA).

Regardez la vidéo [Appliquer des budgets aux abonnements à l’aide du Portail Azure](https://www.youtube.com/watch?v=UrkHiUx19Po) pour voir comment vous pouvez créer des budgets dans Azure afin de surveiller les dépenses.


Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créez un budget dans le portail Azure
> * Créer et modifier des budgets avec PowerShell
> * Créer un budget avec un modèle Azure Resource Manager

## <a name="prerequisites"></a>Prérequis

Les budgets sont pris en charge pour différents types de comptes Azure. Pour accéder à la liste complète des types de comptes pris en charge, voir [Comprendre les données de Cost Management](understand-cost-mgt-data.md). Pour afficher les budgets, vous devez au minimum disposer d'un accès en lecture à votre compte Azure.

Si vous disposez d’un nouvel abonnement, vous ne pouvez pas créer un budget ou utiliser les autres fonctionnalités de Cost Management tout de suite. Vous risquez de devoir attendre jusqu’à 48 heures avant de pouvoir utiliser toutes les fonctionnalités de Cost Management.

Dans le cadre des abonnements Azure EA, vous devez disposer d'un accès en lecture pour afficher les budgets. Pour créer et gérer des budgets, vous devez disposer d’une autorisation de contributeur. Vous pouvez créer des budgets individuels pour les abonnements EA et les groupes de ressources. Vous ne pouvez cependant pas créer des budgets pour les comptes de facturation Contrat Entreprise.

Les autorisations, ou étendues, Azure suivantes sont prises en charge par abonnement aux budgets par utilisateur et par groupe. Pour plus d’informations sur les étendues, consultez [Comprendre et utiliser les étendues](understand-work-scopes.md).

- Propriétaire : peut créer, modifier ou supprimer des budgets pour un abonnement.
- Contributeur et Contributeur Cost Management : peut créer, modifier ou supprimer ses propres budgets. Peut modifier le montant des budgets créés par d’autres utilisateurs.
- Lecteur et Lecteur Cost Management : peut afficher les budgets pour lesquels ils disposent des autorisations adéquates.

Pour plus d’informations sur l’affectation d’une autorisation d’accès aux données Cost Management, consultez [Affecter une autorisation d’accès aux données Cost Management](../../cost-management/assign-access-acm-data.md).

## <a name="sign-in-to-azure"></a>Connexion à Azure

- Connectez-vous au portail Azure sur [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-budget-in-the-azure-portal"></a>Créez un budget dans le portail Azure

Vous pouvez créer un budget d’abonnement Azure pour un mois, un trimestre ou un an. Votre navigation dans le Portail Azure détermine si vous créez un budget pour un abonnement ou pour un groupe d’administration.

Pour créer ou afficher un budget, ouvrez l’étendue souhaitée dans le Portail Azure et sélectionnez **Budgets** dans le menu. Par exemple, accédez à **Abonnements**, sélectionnez un abonnement dans la liste, puis sélectionnez **Budgets** dans le menu. Utilisez la pastille **Étendue** pour basculer vers une autre étendue, un groupe d’administration par exemple, dans Budgets. Pour plus d’informations sur les étendues, consultez [Comprendre et utiliser les étendues](understand-work-scopes.md).

Une fois des budgets créés, ils affichent une vue simple de vos dépenses actuelles par rapport à ces budgets.

Sélectionnez **Ajouter**.

![Exemple de liste de budgets déjà créés](./media/tutorial-acm-create-budgets/budgets01.png)

Dans la fenêtre **Créer un budget**, assurez-vous que l’étendue affichée est correcte. Choisissez les filtres que vous souhaitez ajouter. Les filtres vous permettent de créer des budgets afférents à des coûts spécifiques, tels que des groupes de ressources dans un abonnement ou un service tel que des machines virtuelles. Tout filtre que vous pouvez utiliser dans l’analyse du coût peut également être appliqué à un budget.

Après avoir identifié votre étendue et vos filtres, tapez un nom de budget. Choisissez ensuite une période de réinitialisation de budget mensuelle, trimestrielle ou annuelle. Cette période de réinitialisation détermine la fenêtre de temps analysée par le budget. Le coût évalué par le budget commence à zéro au début de chaque nouvelle période. Quand vous créez un budget trimestriel, il fonctionne de la même façon qu’un budget mensuel. La différence est que le montant du budget pour le trimestre est divisé de manière équitable entre les trois mois du trimestre. Un montant de budget annuel est divisé de manière équitable entre les 12 mois de l’année civile.

Si vous avez un abonnement de paiement à l’utilisation, MSDN ou Visual Studio, votre période de facturation peut ne pas être alignée sur le mois calendaire. Pour ces types d’abonnements et groupes de ressources, vous pouvez créer un budget aligné sur votre période de facturation ou sur les mois calendaires. Pour créer un budget aligné sur votre période de facturation, sélectionnez une période de réinitialisation : **Mois de facturation**, **Trimestre de facturation** ou **Année de facturation**. Pour créer un budget aligné sur le mois calendaire, sélectionnez une période de réinitialisation : **Mensuelle**, **Trimestrielle** ou **Annuelle**.

Ensuite, identifiez la date d’expiration à laquelle le budget ne sera plus valide et cessera d’évaluer vos coûts.

Selon les champs choisis dans le budget jusqu’à présent, un graphique s’affiche pour vous aider à sélectionner un seuil à utiliser pour votre budget. Le budget suggéré est basé sur le coût prévu le plus élevé que vous pourriez rencontrer durant les périodes à venir. Vous pouvez modifier le montant du budget.

![Exemple de création de budget avec des données de coût mensuel ](./media/tutorial-acm-create-budgets/monthly-budget01.png)

Après avoir configuré le montant du budget, sélectionnez **Suivant** pour configurer des alertes budgétaires. Les budgets nécessitent au moins un seuil de coût (% du budget) et une adresse e-mail correspondante. Si vous le souhaitez, vous pouvez inclure jusqu’à cinq seuils et cinq adresses e-mail dans un seul budget. Quand un seuil budgétaire est atteint, des notifications par e-mail sont normalement reçues en moins de 20 heures.

Si vous voulez recevoir des e-mails, ajoutez azure-noreply@microsoft.com à votre liste d’expéditeurs approuvés afin que les e-mails ne soient pas placés dans votre dossier de courrier indésirable. Pour plus d'informations sur les notifications, consultez [Utiliser les alertes de coût](../../cost-management/cost-mgt-alerts-monitor-usage-spending.md).

Dans l’exemple ci-dessous, une alerte par e-mail est générée quand 90 % du budget sont atteints. Si vous créez un budget avec l’API Budgets, vous pouvez également attribuer des rôles à des personnes pour qu’elles reçoivent des alertes. L’attribution de rôles à des personnes n’est pas prise en charge dans le Portail Azure. Pour plus d’informations sur l’API Budgets d’Azure, consultez [API Budgets](/rest/api/consumption/budgets).

![Exemple de conditions d’alerte](./media/tutorial-acm-create-budgets/monthly-budget-alert.png)

Après avoir créé un budget, il est indiqué dans l’analyse des coûts. Visualiser votre budget par rapport à la tendance de vos dépenses est une des premières étapes quand vous commencez à [analyser vos coûts et vos dépenses](../../cost-management/quick-acm-cost-analysis.md).

![Exemple de budget et de dépenses affichés dans l’analyse des coûts](./media/tutorial-acm-create-budgets/cost-analysis.png)

Dans l’exemple précédent, vous avez créé un budget pour un abonnement. Vous pouvez aussi créer un budget pour un groupe de ressources. Si vous voulez créer un budget pour un groupe de ressources, accédez à **Gestion des coûts + facturation** &gt; **Abonnements** &gt; sélectionnez un abonnement > **Groupes de ressource** > sélectionnez un groupe de ressources > **Budgets** > puis **Ajouter** un budget.

## <a name="costs-in-budget-evaluations"></a>Coûts des évaluations de budget

Les évaluations des coûts budgétaires incluent désormais les données d’instance réservée et d’achat. Si les frais vous concernent, vous pouvez recevoir des alertes à mesure que des frais sont incorporés dans vos évaluations. Nous vous recommandons de vous connecter au [portail Azure](https://portal.azure.com) pour vérifier que les seuils budgétaires sont configurés correctement afin de tenir compte des nouveaux coûts. Vos frais facturés Azure ne changent pas. Les budgets sont désormais évalués pour un ensemble plus complet de vos coûts. Si les frais ne vous concernent pas, le comportement de votre budget reste inchangé.

Si vous souhaitez filtrer les nouveaux coûts afin que les budgets soient évalués par rapport aux seuls frais de consommation Azure de premier tiers, ajoutez les filtres suivants à votre budget :

- Type de serveur de publication : Azure
- Type de frais : Usage

Les évaluations des coûts budgétaires sont basées sur le coût réel. Elles n’incluent pas l’amortissement. Pour plus d’informations sur les options de filtrage mises à votre disposition dans les budgets, consultez [Comprendre les options de regroupement et de filtrage](quick-acm-cost-analysis.md#understanding-grouping-and-filtering-options).


## <a name="trigger-an-action-group"></a>Déclencher un groupe d’actions

Lorsque vous créez ou modifiez un budget pour l’étendue d’un abonnement ou d’un groupe de ressources, vous pouvez le configurer pour qu’il appelle un groupe d’actions. Le groupe d’actions peut effectuer différentes actions quand votre seuil budgétaire est atteint. Les groupes d’actions sont actuellement pris en charge uniquement pour les étendues d’abonnement et de groupe de ressources. Pour plus d’informations sur les groupes d’actions, consultez [Créer et gérer des groupes d’actions dans le Portail Azure](../../azure-monitor/platform/action-groups.md). Pour plus d’informations sur l’utilisation d’une automatisation basée sur le budget avec des groupes d’actions, consultez [Gérer les coûts avec Azure Budgets](../manage/cost-management-budget-scenario.md).



Pour créer ou mettre à jour des groupes d’actions, sélectionnez **Gérer les groupes d’actions** pendant la création ou la modification d’un budget.

![Exemple de création d’un budget pour afficher Gérer les groupes d’actions](./media/tutorial-acm-create-budgets/manage-action-groups01.png)


Sélectionnez ensuite **Ajouter un groupe d’actions** et créez le groupe d’actions.


![Image de la zone Ajouter un groupe d’actions](./media/tutorial-acm-create-budgets/manage-action-groups02.png)

Lorsque le groupe d’actions est créé, fermez la zone pour revenir à votre budget.

Configurez votre budget pour qu’il utilise votre groupe d’actions lorsqu’un seuil individuel est atteint. Jusqu’à cinq seuils différents sont pris en charge.

![Exemple montrant la sélection de groupe d’actions pour une condition d’alerte](./media/tutorial-acm-create-budgets/manage-action-groups03.png)

L’exemple suivant montre des seuils budgétaires définis sur 50 %, 75 % et 100 %. Chacun est configuré pour déclencher les actions spécifiées dans le groupe d’actions désigné.

![Exemple montrant des conditions d’alerte configurées avec divers groupes d’actions et le type d’actions](./media/tutorial-acm-create-budgets/manage-action-groups04.png)

L’intégration au budget avec les groupes d’actions ne fonctionne que pour les groupes d’actions pour lesquels le schéma d’alerte commun est désactivé. Pour plus d’informations sur la désactivation du schéma, consultez [Comment activer le schéma d’alerte commun ?](../../azure-monitor/platform/alerts-common-schema.md#how-do-i-enable-the-common-alert-schema)

## <a name="create-and-edit-budgets-with-powershell"></a>Créer et modifier des budgets avec PowerShell

Les clients Contrat Entreprise peuvent créer et modifier des budgets par programmation avec le module Azure PowerShell.  Pour télécharger la dernière version d’Azure PowerShell, exécutez la commande suivante :

```azurepowershell-interactive
install-module -name AzureRm
```

Les exemples de commandes suivantes créent un budget.

```azurepowershell-interactive
#Sign into Azure Powershell with your account

Connect-AzureRmAccount

#Select a subscription to to monitor with a budget

select-AzureRmSubscription -Subscription "Your Subscription"

#Create an action group email receiver and corresponding action group

$email1 = New-AzureRmActionGroupReceiver -EmailAddress test@test.com -Name EmailReceiver1
$ActionGroupId = (Set-AzureRmActionGroup -ResourceGroupName YourResourceGroup -Name TestAG -ShortName TestAG -Receiver $email1).Id

#Create a monthly budget that sends an email and triggers an Action Group to send a second email. Make sure the StartDate for your monthly budget is set to the first day of the current month. Note that Action Groups can also be used to trigger automation such as Azure Functions or Webhooks.

New-AzureRmConsumptionBudget -Amount 100 -Name TestPSBudget -Category Cost -StartDate 2020-02-01 -TimeGrain Monthly -EndDate 2022-12-31 -ContactEmail test@test.com -NotificationKey Key1 -NotificationThreshold 0.8 -NotificationEnabled -ContactGroup $ActionGroupId
```
## <a name="create-a-budget-with-an-azure-resource-manager-template"></a>Créer un budget avec un modèle Azure Resource Manager

Vous pouvez créer un budget en utilisant un modèle Azure Resource Manager. Le modèle vous aide à créer un budget pour un groupe de ressources. 

Sélectionnez l’image suivante pour vous connecter au portail Azure et ouvrir le modèle :

[![Déployer le modèle Créer un budget dans Azure](./media/tutorial-acm-create-budgets/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2fcreate-budget%2fazuredeploy.json)

Pour voir la liste de tous les paramètres de modèle et leurs descriptions, consultez le modèle [Créer un budget](https://azure.microsoft.com/resources/templates/create-budget/).


## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Créez un budget dans le portail Azure
> * Créer et modifier des budgets avec PowerShell
> * Créer un budget avec un modèle Azure Resource Manager

Passez au tutoriel suivant pour créer une exportation récurrente de vos données de gestion des coûts.

> [!div class="nextstepaction"]
> [Créer et gérer des données exportées](tutorial-export-acm-data.md)
