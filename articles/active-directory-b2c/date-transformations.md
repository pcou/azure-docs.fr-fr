---
title: Exemples de transformation de revendications de date pour les stratégies personnalisées
description: Exemples de transformation de revendications de date pour le schéma IEF (Identity Experience Framework) d’Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/03/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: f3e5a7b90892f0ed0243d448ea1ac63fb56f277f
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78188832"
---
# <a name="date-claims-transformations"></a>Transformations de revendications Date

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Cet article fournit des exemples pour l’utilisation de transformations de revendications de date du schéma Identity Experience Framework dans Azure Active Directory B2C (Azure AD B2C). Pour plus d’informations, voir [ClaimsTransformations](claimstransformations.md).

## <a name="assertdatetimeisgreaterthan"></a>AssertDateTimeIsGreaterThan

Vérifie qu’une revendication de date et d’heure (type de données string) est postérieure à une deuxième revendication de date et d’heure (type de données string) et lève une exception.

| Élément | TransformationClaimType | Type de données | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | leftOperand | string | Type de la première revendication, qui doit être postérieure à la deuxième revendication. |
| InputClaim | rightOperand | string | Type de la deuxième revendication, qui doit être antérieure à la première revendication. |
| InputParameter | AssertIfEqualTo | boolean | Spécifie si cette assertion doit passer si l’opérande gauche est égal à l’opérande droit. |
| InputParameter | AssertIfRightOperandIsNotPresent | boolean | Spécifie si cette assertion doit passer si l’opérande droit est manquante. |
| InputParameter | TreatAsEqualIfWithinMillseconds | int | Spécifie le nombre de millisecondes autorisées entre les deux dates pour considérer les heures comme égales (par exemple, pour tenir compte du décalage d’horloge). |

La transformation de revendication **AssertDateTimeIsGreaterThan** est toujours exécutée à partir d’un [profil technique de validation](validation-technical-profile.md) appelé par un [profil technique autodéclaré](self-asserted-technical-profile.md). Les métadonnées de profil technique autodéclaré **DateTimeGreaterThan** contrôlent le message d’erreur présenté à l’utilisateur par le profil technique.

![Exécution de AssertStringClaimsAreEqual](./media/date-transformations/assert-execution.png)

L’exemple suivant compare la revendication `currentDateTime` à la revendication `approvedDateTime`. Une erreur est générée si `currentDateTime` est postérieur à `approvedDateTime`. La transformation traite les valeurs comme étant égales si elles ont une différence de 5 minutes (30 000 millisecondes).

```XML
<ClaimsTransformation Id="AssertApprovedDateTimeLaterThanCurrentDateTime" TransformationMethod="AssertDateTimeIsGreaterThan">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="approvedDateTime" TransformationClaimType="leftOperand" />
    <InputClaim ClaimTypeReferenceId="currentDateTime" TransformationClaimType="rightOperand" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="AssertIfEqualTo" DataType="boolean" Value="false" />
    <InputParameter Id="AssertIfRightOperandIsNotPresent" DataType="boolean" Value="true" />
    <InputParameter Id="TreatAsEqualIfWithinMillseconds" DataType="int" Value="300000" />
  </InputParameters>
</ClaimsTransformation>
```

Le profil technique de validation `login-NonInteractive` appelle la transformation de revendication `AssertApprovedDateTimeLaterThanCurrentDateTime`.
```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertApprovedDateTimeLaterThanCurrentDateTime" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

Le profil technique autodéclaré appelle le profil technique de validation **login-NonInteractive**.

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <Metadata>
    <Item Key="DateTimeGreaterThan">Custom error message if the provided left operand is greater than the right operand.</Item>
  </Metadata>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="login-NonInteractive" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```

### <a name="example"></a>Exemple

- Revendications d’entrée :
    - **leftOperand** : 2018-10-01T15:00:00.0000000Z
    - **rightOperand** : 2018-10-01T14:00:00.0000000Z
- Résultat : Erreur levée

## <a name="convertdatetodatetimeclaim"></a>ConvertDateToDateTimeClaim

Convertit un ClaimType **Date** en un ClaimType **DateTime**. La transformation des revendications convertit le format d’heure et ajoute 12:00:00 AM à la date.

| Élément | TransformationClaimType | Type de données | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | Date | Le ClaimType à convertir. |
| OutputClaim | outputClaim | dateTime | ClaimType généré après l’appel de cette ClaimsTransformation. |

L’exemple suivant illustre la conversion de la revendication `dateOfBirth` (type de données de date) en une autre revendication `dateOfBirthWithTime` (type de données date/heure).

```XML
  <ClaimsTransformation Id="ConvertToDateTime" TransformationMethod="ConvertDateToDateTimeClaim">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="dateOfBirth" TransformationClaimType="inputClaim" />
    </InputClaims>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="dateOfBirthWithTime" TransformationClaimType="outputClaim" />
    </OutputClaims>
  </ClaimsTransformation>
```

### <a name="example"></a>Exemple

- Revendications d’entrée :
    - **inputClaim** : 2019-06-01
- Revendications de sortie :
    - **outputClaim** : 1559347200 (1er juin 2019 12:00:00)

## <a name="convertdatetimetodateclaim"></a>ConvertDateTimeToDateClaim

Convertit un ClaimType **DateTime** en un ClaimType **Date**. La transformation de revendications supprime le format d’heure de la date.

| Élément | TransformationClaimType | Type de données | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | dateTime | Le ClaimType à convertir. |
| OutputClaim | outputClaim | Date | ClaimType généré après l’appel de cette ClaimsTransformation. |

L’exemple suivant illustre la conversion de la revendication `systemDateTime` (type de données dateTime ) en une autre revendication `systemDate` (type de données date).

```XML
<ClaimsTransformation Id="ConvertToDate" TransformationMethod="ConvertDateTimeToDateClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="systemDateTime" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="systemDate" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemple

- Revendications d’entrée :
  - **inputClaim** : 1559347200 (1er juin 2019 12:00:00)
- Revendications de sortie :
  - **outputClaim** : 2019-06-01

## <a name="getcurrentdatetime"></a>GetCurrentDateTime

Obtient la date et l’heure UTC actuelles et ajoute la valeur à un ClaimType.

| Élément | TransformationClaimType | Type de données | Notes |
| ---- | ----------------------- | --------- | ----- |
| OutputClaim | currentDateTime | dateTime | ClaimType généré après l’appel de cette ClaimsTransformation. |

```XML
<ClaimsTransformation Id="GetSystemDateTime" TransformationMethod="GetCurrentDateTime">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="systemDateTime" TransformationClaimType="currentDateTime" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemple

* Revendications de sortie :
    * **currentDateTime** : 1534418820 (16 août 2018 11:27:00)

## <a name="datetimecomparison"></a>DateTimeComparison

Détermine si un dateTime est postérieur, antérieur ou égal à un autre. Le résultat est un nouveau booléen ClaimType avec une valeur `true` ou `false`.

| Élément | TransformationClaimType | Type de données | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | firstDateTime | dateTime | La première valeur dateTime à comparer, pour voir si elle est antérieure ou postérieure à la deuxième valeur dateTime. Une valeur null lève une exception. |
| InputClaim | secondDateTime | dateTime | La deuxième valeur dateTime à comparer, pour voir si elle est antérieure ou postérieure à la première valeur dateTime. La valeur NULL est considérée comme la valeur datetTime actuelle. |
| InputParameter | operator | string | Une des valeurs suivantes : identique, plus tard ou antérieur. |
| InputParameter | timeSpanInSeconds | int | Ajoute l’intervalle de temps au premier dateTime. |
| OutputClaim | result | boolean | ClaimType généré après l’appel de cette ClaimsTransformation. |

Utilisez cette transformation de revendication pour déterminer si deux ClaimTypes sont égales, ou antérieure ou postérieure à l’autre. Par exemple, vous pouvez stocker la dernière fois qu’un utilisateur a accepté vos conditions d’utilisation du service. Après 3 mois, vous pouvez de nouveau lui demander d’accepter les conditions d’utilisation.
Pour exécuter la transformation de revendication,vous devez d’abord obtenir le dateTime actuel et la dernière fois que l’utilisateur a accepté les conditions d’utilisation.

```XML
<ClaimsTransformation Id="CompareLastTOSAcceptedWithCurrentDateTime" TransformationMethod="DateTimeComparison">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="currentDateTime" TransformationClaimType="firstDateTime" />
    <InputClaim ClaimTypeReferenceId="extension_LastTOSAccepted" TransformationClaimType="secondDateTime" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="operator" DataType="string" Value="later than" />
    <InputParameter Id="timeSpanInSeconds" DataType="int" Value="7776000" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isLastTOSAcceptedGreaterThanNow" TransformationClaimType="result" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemple

- Revendications d’entrée :
    - **firstDateTime** : 2018-01-01T00:00:00.100000Z
    - **secondDateTime** : 2018-04-01T00:00:00.100000Z
- Paramètres d’entrée :
    - **opérateur** : après
    - **timeSpanInSeconds** : 7776000 (90 jours)
- Revendications de sortie :
    - **résultat** : true
