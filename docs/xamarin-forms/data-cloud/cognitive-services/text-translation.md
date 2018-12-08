---
title: Tradução de texto usando a API do Translator
description: API do Microsoft Translator pode ser usado para converter fala e texto por meio de uma API REST. Este artigo explica como usar a API de texto do Microsoft Translator para traduzir o texto de um idioma para outro em um aplicativo xamarin. Forms.
ms.prod: xamarin
ms.assetid: 68330242-92C5-46F1-B1E3-2395D8823B0C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
ms.openlocfilehash: 18e5b430d9a56b22a0b4cc72d6aff1c4e3049362
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2018
ms.locfileid: "53050559"
---
# <a name="text-translation-using-the-translator-api"></a>Tradução de texto usando a API do Translator

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoCognitiveServices/)

_API do Microsoft Translator pode ser usado para converter fala e texto por meio de uma API REST. Este artigo explica como usar a API de texto do Microsoft Translator para traduzir o texto de um idioma para outro em um aplicativo xamarin. Forms._

## <a name="overview"></a>Visão geral

A API de tradução tem dois componentes:

- Uma tradução de texto da API REST para traduzir o texto de um idioma para o texto de outro idioma. A API automaticamente detecta o idioma do texto que foi enviado antes de converter.
- Uma tradução de fala a API REST para transcrição de fala de um idioma no texto de outro idioma. A API também integra os recursos de texto em fala para falar o texto traduzido novamente.

Este artigo se concentra na tradução de texto de um idioma para outro usando a API de tradução de texto.

Uma chave de API deve ser obtida para usar a API de tradução de texto. Isso pode ser obtido no [como se inscrever para a API do Microsoft Translator texto](/azure/cognitive-services/translator/translator-text-how-to-signup/).

Para obter mais informações sobre a API de texto do Microsoft Translator, consulte [documentação da API do Translator texto](/azure/cognitive-services/translator/).

## <a name="authentication"></a>Autenticação

Todas as solicitações feitas para a API de tradução de texto requer um token de acesso do JSON Web Token (JWT), que pode ser obtido do serviço de token dos serviços cognitivos `https://api.cognitive.microsoft.com/sts/v1.0/issueToken`. Um token pode ser obtido, fazendo uma solicitação POST para o serviço de token, especificando um `Ocp-Apim-Subscription-Key` cabeçalho que contém a chave de API como seu valor.

O exemplo de código a seguir mostra como solicitar um acesso de token do serviço de token:

```csharp
public AuthenticationService(string apiKey)
{
    subscriptionKey = apiKey;
    httpClient = new HttpClient();
    httpClient.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);
}
...
async Task<string> FetchTokenAsync(string fetchUri)
{
    UriBuilder uriBuilder = new UriBuilder(fetchUri);
    uriBuilder.Path += "/issueToken";
    var result = await httpClient.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
    return await result.Content.ReadAsStringAsync();
}
```

O token de acesso retornado, o que é um texto Base64, tem uma hora de expiração de 10 minutos. Portanto, o aplicativo de exemplo renova o token de acesso a cada 9 minutos.

O token de acesso deve ser especificado em cada API de tradução de texto chamada como um `Authorization` cabeçalho prefixado com a cadeia de caracteres `Bearer`, conforme mostrado no exemplo de código a seguir:

```csharp
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", bearerToken);
```

Para obter mais informações sobre o serviço de token de serviços cognitivos, consulte [API de Token de autenticação](http://docs.microsofttranslator.com/oauth-token.html).

## <a name="performing-text-translation"></a>Executar tradução de texto

Tradução de texto pode ser obtida por meio de uma solicitação GET para o `translate` API em `https://api.microsofttranslator.com/v2/http.svc/translate`. No aplicativo de exemplo, o `TranslateTextAsync` método invoca o processo de tradução de texto:

```csharp
public async Task<string> TranslateTextAsync(string text)
{
  ...
  string requestUri = GenerateRequestUri(Constants.TextTranslatorEndpoint, text, "en", "de");
  string accessToken = authenticationService.GetAccessToken();
  var response = await SendRequestAsync(requestUri, accessToken);
  var xml = XDocument.Parse(response);
  return xml.Root.Value;
}
```

O `TranslateTextAsync` método gera um URI de solicitação e recupera um token de acesso ao serviço de token. A solicitação de tradução de texto é enviada para o `translate` API, que retorna uma resposta em XML que contém o resultado. A resposta XML é analisada, e o resultado da conversão é retornado para o método de chamada para exibição.

Para obter mais informações sobre as APIs de REST de tradução de texto, consulte [API do Microsoft Translator texto](http://docs.microsofttranslator.com/text-translate.html).

### <a name="configuring-text-translation"></a>Configurando a tradução de texto

O processo de tradução de texto pode ser configurado especificando parâmetros de consulta HTTP:

```csharp
string GenerateRequestUri(string endpoint, string text, string to)
{
  string requestUri = endpoint;
  requestUri += string.Format("?text={0}", Uri.EscapeUriString(text));
  requestUri += string.Format("&to={0}", to);
  return requestUri;
}
```

Esse método define o texto a ser convertido e o idioma para traduzir o texto a ser. Para obter uma lista dos idiomas com suporte pelo Microsoft Translator, consulte [idiomas com suporte na API do Microsoft Translator texto](/azure/cognitive-services/translator/languages/).

> [!NOTE]
> Se um aplicativo precisa saber qual idioma o texto é, o `Detect` API pode ser chamada para detectar o idioma da cadeia de caracteres de texto.

### <a name="sending-the-request"></a>Enviar a solicitação

O `SendRequestAsync` método faz a solicitação GET para a API de REST de tradução de texto e retorna a resposta:

```csharp
async Task<string> SendRequestAsync(string url, string bearerToken)
{
    if (httpClient == null)
    {
        httpClient = new HttpClient();
    }
    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", bearerToken);

    var response = await httpClient.GetAsync(url);
    return await response.Content.ReadAsStringAsync();
}
```

Esse método cria a solicitação GET, adicionando o token de acesso para o `Authorization` cabeçalho, prefixado com a cadeia de caracteres `Bearer`. A solicitação GET é enviada para o `translate` API, com a URL da solicitação especifica o texto a ser traduzido e o idioma para traduzir o texto a ser. A resposta é, em seguida, ler e retornada para o método de chamada.

O `translate` API enviará o código de status HTTP 200 (Okey) em resposta, fornecida que a solicitação é válida, que indica que a solicitação foi bem-sucedida e que as informações solicitadas estão na resposta. Para obter uma lista de possíveis respostas de erro, consulte as mensagens de resposta em [obter traduzir](http://docs.microsofttranslator.com/text-translate.html#!/default/get_Translate).

### <a name="processing-the-response"></a>Processar a resposta

A resposta da API é retornada em formato XML. Os dados XML a seguir mostram uma mensagem de resposta bem-sucedida típico:

```xml
<string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">Morgen kaufen gehen ein</string>
```

No aplicativo de exemplo, a resposta XML é analisada em um `XDocument` instância, com o valor de raiz XML que está sendo retornado para o método de chamada para exibição, conforme mostrado nas capturas de tela seguir:

![](text-translation-images/text-translation.png "Tradução de texto para o idioma alemão")

## <a name="summary"></a>Resumo

Este artigo explicou como usar a API de texto do Microsoft Translator para traduzir o texto de um idioma para o texto de outro idioma em um aplicativo xamarin. Forms. Além de traduzir o texto, API do Microsoft Translator pode transcrever também fala de um idioma no texto de outro idioma.

## <a name="related-links"></a>Links relacionados

- [Documentação de API de texto do tradutor](/azure/cognitive-services/translator/).
- [Consumir um serviço Web RESTful](~/xamarin-forms/data-cloud/consuming/rest.md)
- [Serviços Cognitivos de tarefas pendentes (amostra)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoCognitiveServices/)
- [API de texto do Microsoft Translator](http://docs.microsofttranslator.com/text-translate.html).
