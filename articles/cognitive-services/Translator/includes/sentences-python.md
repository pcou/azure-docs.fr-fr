---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 9c7385d3457f3f5dbed2633c20445bb9ef0b1638
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69906860"
---
[!INCLUDE [Prerequisites](prerequisites-python.md)]

[!INCLUDE [Set up and use environment variables](setup-env-variables.md)]

## <a name="create-a-project-and-import-required-modules"></a>Créez un projet et importez les modules requis

Créez un projet Python dans votre IDE ou votre éditeur favori. Copiez cet extrait de code dans votre projet, dans un fichier nommé `sentence-length.py`.

```python
# -*- coding: utf-8 -*-
import os, requests, uuid, json
```

> [!NOTE]
> Si vous n’avez jamais utilisé ces modules auparavant, vous devrez les installer avant d’exécuter votre programme. Pour installer ces packages, exécuter `pip install requests uuid`.

Le premier commentaire ordonne à votre interpréteur Python d’utiliser l’encodage UTF-8. Puis, les modules requis sont importés pour lire votre clé d’abonnement depuis une variable d’environnement, élaborer la requête http, créer un identificateur unique et gérer la réponse JSON rendue par l’API Translator Text.

## <a name="set-the-subscription-key-endpoint-and-path"></a>Définir la clé d’abonnement, le point de terminaison et le chemin

Cet exemple tente de lire la clé d’abonnement et le point de terminaison Translator Text depuis les variables d’environnement : `TRANSLATOR_TEXT_KEY` et `TRANSLATOR_TEXT_ENDPOINT`. Si vous n’êtes pas familiarisé avec les variables d’environnement, vous pouvez définir `subscription_key` et `endpoint` en tant que chaînes et commenter les instructions conditionnelles.

Copiez ce code dans votre projet :

```python
key_var_name = 'TRANSLATOR_TEXT_SUBSCRIPTION_KEY'
if not key_var_name in os.environ:
    raise Exception('Please set/export the environment variable: {}'.format(key_var_name))
subscription_key = os.environ[key_var_name]

endpoint_var_name = 'TRANSLATOR_TEXT_ENDPOINT'
if not endpoint_var_name in os.environ:
    raise Exception('Please set/export the environment variable: {}'.format(endpoint_var_name))
endpoint = os.environ[endpoint_var_name]
```

Le point de terminaison global de l’API de traduction de texte Translator Text est défini sur `endpoint`. `path` définit le chemin des `breaksentence` et reconnait que nous souhaitons atteindre la version 3 de l’API.

Les `params` dans cet exemple sont utilisés pour définir la langue du texte fourni. `params` ne sont pas nécessaires pour l’itinéraire `breaksentence`. Si cela ne fait pas partie de la requête, l’API tente de détecter la langue du texte fourni, et donne cette information accompagnée d’un score de confiance dans la réponse.

>[!NOTE]
> Pour plus d’informations sur les points de terminaison, les routes et les paramètres de requête, consultez [API de traduction de texte Translator Text 3.0 : Langues](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-break-sentence).

```python
path = '/breaksentence?api-version=3.0'
params = '&language=en'
constructed_url = endpoint + path + params
```

## <a name="add-headers"></a>Ajouter les en-têtes

La manière la plus simple d’authentifier une requête consiste à transmettre votre clé d’abonnement comme `Ocp-Apim-Subscription-Key`en-tête. C’est ce que nous avons fait dans ce modèle. Vous pouvez aussi échanger votre clé d’abonnement pour un jeton d’accès et transmettre le jeton d’accès en tant qu’`Authorization`en-tête pour valider votre requête. Pour en savoir plus, consultez [Authentification](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

Copiez cet extrait de code dans votre projet :

```python
headers = {
    'Ocp-Apim-Subscription-Key': subscription_key,
    'Content-type': 'application/json',
    'X-ClientTraceId': str(uuid.uuid4())
}
```

Si vous utilisez un abonnement multiservice Cognitive Services, vous devez également inclure la `Ocp-Apim-Subscription-Region` dans vos paramètres de requête. [En savoir plus sur l’authentification sur l’abonnement multiservice](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="create-a-request-to-determine-sentence-length"></a>Créer une requête pour déterminer la longueur de phrase

Indiquez la phrase (ou les phrases) dont vous souhaitez déterminer la longueur :

```python
# You can pass more than one object in body.
body = [{
    'text': 'How are you? I am fine. What did you do today?'
}]
```

Ensuite, nous allons créer une requête POST à l’aide du module `requests`. Il tient compte de trois arguments : l’URL concaténée, les en-têtes de requête et le corps de la demande :

```python
request = requests.post(constructed_url, headers=headers, json=body)
response = request.json()
```

## <a name="print-the-response"></a>Imprimer la réponse

La dernière étape consiste à imprimer les résultats. Cet extrait de code agrémente les résultats en triant les clés, en configurant la mise en retrait et en déclarant des séparateurs d’élément et de clés.

```python
print(json.dumps(response, sort_keys=True, indent=4,
                 ensure_ascii=False, separators=(',', ': ')))
```

## <a name="put-it-all-together"></a>Assemblage

Voilà, vous avez installé un programme simple qui appellera l’API de traduction de texte Translator Text et enverra une réponse JSON. Il est maintenant temps d’exécuter votre programme :

```console
python sentence-length.py
```

Si vous souhaitez comparer votre code avec le nôtre, l’exemple de code complet est disponible sur [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Python).

## <a name="sample-response"></a>Exemple de réponse

```json
[
    {
        "sentLen": [
            13,
            11,
            22
        ]
    }
]
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous avez codé en dur votre clé d’abonnement dans votre programme, veillez à supprimer la clé d’abonnement une fois ce démarrage rapide terminé.

## <a name="next-steps"></a>Étapes suivantes

Consultez les informations de référence sur l’API pour comprendre tout ce que vous pouvez faire avec l’API Traduction de texte Translator Text.

> [!div class="nextstepaction"]
> [Informations de référence sur l'API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
