---
title: 'Démarrage rapide : Bibliothèque de client Suggestion automatique Bing pour Go | Microsoft Docs'
description: Découvrez la bibliothèque de client Suggestion automatique Bing pour Go, ainsi que les suggestions de recherche qu’elle propose et qui sont basées sur des chaînes de requête partielles.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: quickstart
ms.date: 12/18/2019
ms.author: aahi
ms.openlocfilehash: e826d69bbf0f94d02799e9b93b26db8d4cbbba54
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75451160"
---
# <a name="quickstart-bing-autosuggest-client-library-for-go"></a>Démarrage rapide : Bibliothèque de client Suggestion automatique Bing pour Go

Bien démarrer avec la bibliothèque de client Suggestion automatique Bing pour Go Suivez les étapes suivantes pour installer la bibliothèque et essayer nos exemples de tâches de base. 

Utilisez la bibliothèque de client Suggestion automatique Bing pour Go pour obtenir des suggestions de recherche basées sur des chaînes de requête partielles.

[Documentation de référence](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/autosuggest) | [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics) | [Exemple de code](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/BingAutoSuggest/BingAutoSuggestQuickstart.go)

## <a name="prerequisites"></a>Conditions préalables requises

* Un abonnement Azure : [créez-en un gratuitement](https://azure.microsoft.com/free/)
* La dernière version de [Go](https://golang.org/dl/)

## <a name="setting-up"></a>Configuration

### <a name="create-an-azure-resource"></a>Créer une ressource Azure 

Vous pouvez commencer à utiliser la bibliothèque de client Suggestion automatique Bing en créant une ressource Azure. Choisissez le type de ressource qui vous convient :

[!INCLUDE [cognitive-services-bing-autosuggest-signup-requirements](../../../../includes/cognitive-services-bing-autosuggest-signup-requirements.md)]

### <a name="create-an-environment-variable"></a>Créer une variable d’environnement

>[!NOTE]
> Les points de terminaison pour les ressources autres que d’essai créées après le 1er juillet 2019 utilisent le format de sous-domaine personnalisé indiqué ci-dessous. Pour obtenir plus d’informations et une liste complète des points de terminaison régionaux, consultez [Noms de sous-domaines personnalisés pour Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains). 

En utilisant votre clé et le point de terminaison de la ressource que vous avez créée, créez deux variables d’environnement pour l’authentification :
<!-- replace the below variable names with the names expected in the code sample.-->
* `AUTOSUGGEST_SUBSCRIPTION_KEY` : clé de ressource pour l’authentification de vos requêtes.
* `AUTOSUGGEST_ENDPOINT` : point de terminaison de ressource pour l’envoi de requêtes d’API. Il se présente comme suit : 
  * `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

Utilisez les instructions pour votre système d’exploitation.
<!-- replace the below endpoint and key examples -->
#### <a name="windowstabwindows"></a>[Windows](#tab/windows)

```console
setx BING_AUTOSUGGEST_SUBSCRIPTION_KEY <replace-with-your-autosuggest-api-key>
setx BING_AUTOSUGGEST_ENDPOINT <replace-with-your-autosuggest-api-endpoint>
```

Après avoir ajouté la variable d’environnement, redémarrez la fenêtre de console.

#### <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export AUTOSUGGEST_SUBSCRIPTION_KEY=<replace-with-your-autosuggest-api-key>
export AUTOSUGGEST_ENDPOINT=<replace-with-your-autosuggest-api-endpoint>
```

Après avoir ajouté la variable d’environnement, exécutez `source ~/.bashrc` depuis la fenêtre de console pour appliquer les changements.

#### <a name="macostabunix"></a>[macOS](#tab/unix)

Modifiez votre profil `.bash_profile` et ajoutez la variable d’environnement :

```bash
export AUTOSUGGEST_SUBSCRIPTION_KEY=<replace-with-your-autosuggest-api-key>
export AUTOSUGGEST_ENDPOINT=<replace-with-your-autosuggest-api-endpoint>
```

Après avoir ajouté la variable d’environnement, exécutez `source .bash_profile` depuis la fenêtre de console pour appliquer les changements.
***

### <a name="create-a-new-go-project"></a>Créer un projet Go

Dans une fenêtre de console (cmd, PowerShell, terminal, bash), créez un espace de travail pour votre projet Go et accédez-y. Votre espace de travail contiendra trois dossiers : 

* **SRC** : ce répertoire contient le code source et les packages. Tous les packages installés à l’aide de la commande `go get` s’y trouvent.
* **pkg** : ce répertoire contient les objets de package Go compilés. Ces fichiers ont tous une extension `.a`.
* **bin** : ce répertoire contient les fichiers exécutables binaires créés lors de l’exécution de `go install`.

> [!TIP]
> En savoir plus sur la structure d'un [espace de travail Go](https://golang.org/doc/code.html#Workspaces). Ce guide comprend des informations pour configurer `$GOPATH` et `$GOROOT`.

Nous allons créer un espace de travail appelé `my-app`, ainsi que les sous-répertoires nécessaires pour `src`, `pkg` et `bin` :

```
$ mkdir -p my-app/{src, bin, pkg}  
$ cd my-app
```

### <a name="install-the-client-library-for-go"></a>Installer la bibliothèque de client pour Go

Ensuite, nous allons installer la bibliothèque de client pour Go : 

```bash
$ go get -u <library-location-or-url>
```

Ou, si vous utilisez dep, au sein de votre dépôt, exécutez :

```bash
$ dep ensure -add <library-location-or-url>
```

### <a name="create-your-go-application"></a>Créer votre application Go

Maintenant, nous allons créer un fichier nommé `src/sample-app.go` :

```bash
$ cd src
$ touch sample-app.go
```

Ouvrez `sample-app.go`, puis ajoutez le nom du package et importez les bibliothèques suivantes :

```Go
package main

import (
    "context"
    "fmt"
    "github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/autosuggest"
    "github.com/Azure/go-autorest/autorest"
    "log"
    "os"
)
```

Créez une fonction nommée `main`. Ensuite, créez des variables d’environnement pour la clé et le point de terminaison Suggestion automatique Bing.

```go
func main() {
    // Add your Bing Autosuggest subscription key to your environment variables.
    if "" == os.Getenv("BING_AUTOSUGGEST_SUBSCRIPTION_KEY") {
        log.Fatal("Please set/export the environment variable BING_AUTOSUGGEST_SUBSCRIPTION_KEY.")
    }
    // Add your Bing Autosuggest endpoint to your environment variables.
    var subscription_key string = os.Getenv("BING_AUTOSUGGEST_ENDPOINT")
    if "" == os.Getenv("BING_AUTOSUGGEST_ENDPOINT") {
        log.Fatal("Please set/export the environment variable BING_AUTOSUGGEST_ENDPOINT.")
    }
    var endpoint string = os.Getenv("BING_AUTOSUGGEST_ENDPOINT")
}
```

## <a name="code-examples"></a>Exemples de code

Ces exemples de code vous montrent comment effectuer des tâches de base à l’aide de la bibliothèque de client Suggestion automatique Bing pour Go :

* [Authentifier le client](#authenticate-the-client)
* [Envoyer une requête d’API](#send-an-api-request)

## <a name="authenticate-the-client"></a>Authentifier le client

> [!NOTE] 
> Ce guide de démarrage rapide part du principe que vous avez [créé une variable d’environnement](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pour votre clé Suggestion automatique Bing nommée `BING_AUTOSUGGEST_SUBSCRIPTION_KEY`, et une autre pour votre point de terminaison nommée `BING_AUTOSUGGEST_ENDPOINT`.

Dans la fonction `main()`, instanciez un client avec votre point de terminaison et la clé. 

```go
// Get the context, which is required by the SDK methods.
ctx := context.Background()

client := autosuggest.New()
// Set the subscription key on the client.
client.Authorizer = autorest.NewCognitiveServicesAuthorizer(subscription_key)
client.Endpoint = endpoint
```

## <a name="send-an-api-request"></a>Envoyer une requête d’API

Dans la même méthode, utilisez la méthode [AutoSuggestMethodAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.autosuggestclientextensions.autosuggestmethodasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Search_AutoSuggest_AutoSuggestClientExtensions_AutoSuggestMethodAsync_Microsoft_Azure_CognitiveServices_Search_AutoSuggest_IAutoSuggestClient_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_Collections_Generic_IList_System_String__System_Threading_CancellationToken_) du client pour envoyer une requête à Bing. Itérez ensuite la réponse [Suggestions](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.models.suggestions?view=azure-dotnet) et affichez la première suggestion.

```Go
// This should return the query suggestion "xbox."
result, err := client.AutoSuggest (ctx, "xb", "", "", "", "", "", "", "", "", "", "", []autosuggest.ResponseFormat{"Json"})
if nil != err {
    log.Fatal(err)
}

groups := *result.SuggestionGroups
if len(groups) > 0 {
    group, _ := groups[0].AsSuggestionsSuggestionGroup()
    fmt.Printf ("First suggestion group: %s\n", (*group).Name)
    suggestions := *(*group).SearchSuggestions
    if len(suggestions) > 0 {
        fmt.Println("First suggestion:")
        fmt.Printf("Query: %s\n", *suggestions[0].Query)
        fmt.Printf("Display text: %s\n", *suggestions[0].DisplayText)
    } else {
        fmt.Println("No suggestions found in this group.")
    }
} else {
    fmt.Println("No suggestions found.")
}
```

## <a name="run-the-application"></a>Exécution de l'application

Exécutez l’application Go avec la commande `go run [arguments]` à partir du répertoire de votre application.

```Go
go run sample-app.go
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous souhaitez nettoyer et supprimer un abonnement Cognitive Services, vous pouvez supprimer la ressource ou le groupe de ressources. La suppression du groupe de ressources efface également les autres ressources qui y sont associées.

* [Portail](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Didacticiel Suggestion automatique Bing](../tutorials/autosuggest.md)

## <a name="see-also"></a>Voir aussi

- [Qu’est-ce que la Suggestion automatique Bing ?](../get-suggested-search-terms.md)
- [Informations de référence sur l’API Suggestion automatique Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference)
