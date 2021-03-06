---
title: Format de données - API Service d’exploration des connaissances
titlesuffix: Azure Cognitive Services
description: Découvrez le format de données dans l’API Service d’exploration des connaissances (KES).
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: bba257c61509d862bb3161625750f05a86af8770
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60815089"
---
# <a name="data-format"></a>Format de données

Le fichier de données décrit la liste des objets à indexer.
Chaque ligne du fichier spécifie les valeurs des attributs d’un objet au [format JSON](https://json.org/) avec encodage UTF-8.
Outre les attributs définis dans le [schéma](SchemaFormat.md), chaque objet possède un attribut « logprob » facultatif qui spécifie la probabilité logarithmique relative entre les objets.
Lorsque le service renvoie les objets dans un ordre de probabilité décroissant, nous pouvons utiliser « logprob » pour indiquer l’ordre de retour des objets correspondants.
Par exemple, pour une probabilité *p* comprise entre 0 et 1, la probabilité logarithmique correspondante peut être calculée à l’aide de log(*p*), où log() représente la fonction du logarithme naturel.
Si aucune valeur n’est spécifiée pour logprob, la valeur par défaut 0 est utilisée.

```json
{"logprob":-5.3, "Title":"latent dirichlet allocation", "Year":2003, "Author":{"Name":"david m blei", "Affiliation":"uc berkeley"}, "Author":{"Name":"andrew y ng", "Affiliation":"stanford"}, "Author":{"Name":"michael i jordan", "Affiliation":"uc berkeley"}}
{"logprob":-6.1, "Title":"probabilistic latent semantic indexing", "Year":1999, "Author":{"Name":"thomas hofmann", "Affiliation":"uc berkeley"}}
...
```

Pour les attributs de type Chaîne, GUID et Blob, la valeur est représentée sous forme de chaîne JSON entre guillemets.  Pour les attributs numériques (Int32, Int64, Double), la valeur est représentée sous forme de nombre JSON.  Pour les attributs composites, la valeur est un objet JSON qui spécifie les valeurs des sous-attributs.  Pour créer les index plus rapidement, triez au préalable les objets par ordre de probabilité logarithmique décroissant.

Il se peut qu’un attribut n’ait aucune valeur ou qu’il en ait plusieurs.  Si un attribut n’a aucune valeur, il est simplement retiré de l’objet JSON.  Si un attribut dispose de 2 valeurs ou plus, nous pouvons répéter la paire de valeurs de l’attribut dans l’objet JSON.  Nous pouvons également assigner à l’attribut un tableau JSON contenant les différentes valeurs.

```json
{"logprob":0, "Title":"0 keyword"}
{"logprob":0, "Title":"1 keyword", "Keyword":"foo"}
{"logprob":0, "Title":"2 keywords", "Keyword":"foo", "Keyword":"bar"}
{"logprob":0, "Title":"2 keywords (alt)", "Keyword":["foo", "bar"]}
```
