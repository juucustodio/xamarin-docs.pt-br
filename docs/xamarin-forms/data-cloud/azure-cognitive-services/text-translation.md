---
title: Conversão de texto usando a API do Tradutor
description: A API do Microsoft Translator pode ser usada para traduzir a fala e o texto por meio de uma API REST. Este artigo explica como usar o API de Tradução de Texto da Microsoft para traduzir texto de um idioma para outro em um aplicativo Xamarin. Forms.
ms.prod: xamarin
ms.assetid: 68330242-92C5-46F1-B1E3-2395D8823B0C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
ms.openlocfilehash: 50d13532585e6edc3dac530558937ee6e0a02268
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032792"
---
# <a name="text-translation-using-the-translator-api"></a>Conversão de texto usando a API do Tradutor

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)

_A API do Microsoft Translator pode ser usada para traduzir a fala e o texto por meio de uma API REST. Este artigo explica como usar o API de Tradução de Texto da Microsoft para traduzir texto de um idioma para outro em um aplicativo Xamarin. Forms._

## <a name="overview"></a>Visão Geral

A API do tradutor tem dois componentes:

- Uma API REST de tradução de texto para converter texto de um idioma em texto de outro idioma. A API detecta automaticamente o idioma do texto que foi enviado antes de sua tradução.
- Uma API REST de tradução de fala para transcrever a fala de um idioma para o texto de outra linguagem. A API também integra recursos de conversão de texto em fala para falar o texto traduzido de volta.

Este artigo se concentra na tradução de texto de um idioma para outro usando o API de Tradução de Texto.

Uma chave de API deve ser obtida para usar o API de Tradução de Texto. Isso pode ser obtido em [como se inscrever no Microsoft API de tradução de texto](/azure/cognitive-services/translator/translator-text-how-to-signup/).

Para obter mais informações sobre o Microsoft API de Tradução de Texto, consulte [API de tradução de texto documentação](/azure/cognitive-services/translator/).

## <a name="authentication"></a>Autenticação

Cada solicitação feita ao API de Tradução de Texto requer um token de acesso JWT (token Web JSON), que pode ser obtido do serviço de token de serviços cognitivas em `https://api.cognitive.microsoft.com/sts/v1.0/issueToken`. Um token pode ser obtido fazendo uma solicitação POST para o serviço de token, especificando um cabeçalho de `Ocp-Apim-Subscription-Key` que contém a chave de API como seu valor.

O exemplo de código a seguir mostra como solicitar um token de acesso do serviço de token:

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

O token de acesso retornado, que é texto base64, tem um tempo de expiração de 10 minutos. Portanto, o aplicativo de exemplo renova o token de acesso a cada 9 minutos.

O token de acesso deve ser especificado em cada chamada de API de Tradução de Texto como um cabeçalho de `Authorization` prefixado com a cadeia de caracteres `Bearer`, conforme mostrado no exemplo de código a seguir:

```csharp
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", bearerToken);
```

Para obter mais informações sobre o serviço de token de serviços cognitivas, consulte [API de token de autenticação](https://docs.microsofttranslator.com/oauth-token.html).

## <a name="performing-text-translation"></a>Executando a tradução de texto

A conversão de texto pode ser obtida fazendo uma solicitação GET para a API de `translate` em `https://api.microsofttranslator.com/v2/http.svc/translate`. No aplicativo de exemplo, o método `TranslateTextAsync` invoca o processo de tradução de texto:

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

O método `TranslateTextAsync` gera um URI de solicitação e recupera um token de acesso do serviço de token. A solicitação de conversão de texto é enviada para a API de `translate`, que retorna uma resposta XML que contém o resultado. A resposta XML é analisada e o resultado da conversão é retornado para o método de chamada para exibição.

Para obter mais informações sobre as APIs REST de tradução de texto, consulte [Microsoft API de tradução de texto](https://docs.microsofttranslator.com/text-translate.html).

### <a name="configuring-text-translation"></a>Configurando a conversão de texto

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

Esse método define o texto a ser traduzido e o idioma no qual converter o texto. Para obter uma lista dos idiomas com suporte do Microsoft Translator, consulte [idiomas com suporte no microsoft API de tradução de texto](/azure/cognitive-services/translator/languages/).

> [!NOTE]
> Se um aplicativo precisar saber em qual idioma o texto está, a API `Detect` pode ser chamada para detectar o idioma da cadeia de texto.

### <a name="sending-the-request"></a>Enviando a solicitação

O método `SendRequestAsync` faz a solicitação GET para a API REST de tradução de texto e retorna a resposta:

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

Esse método cria a solicitação GET adicionando o token de acesso ao cabeçalho `Authorization`, prefixado com a cadeia de caracteres `Bearer`. Em seguida, a solicitação GET é enviada para a API de `translate`, com a URL de solicitação especificando o texto a ser traduzido e o idioma para o qual converter o texto. Em seguida, a resposta é lida e retornada ao método de chamada.

A API de `translate` enviará o código de status HTTP 200 (OK) na resposta, desde que a solicitação seja válida, o que indica que a solicitação foi bem-sucedida e que as informações solicitadas estão na resposta. Para obter uma lista de possíveis respostas de erro, consulte mensagens de resposta em [obter tradução](https://docs.microsofttranslator.com/text-translate.html#!/default/get_Translate).

### <a name="processing-the-response"></a>Processando a resposta

A resposta da API é retornada no formato XML. Os dados XML a seguir mostram uma mensagem típica de resposta bem-sucedida:

```xml
<string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">Morgen kaufen gehen ein</string>
```

No aplicativo de exemplo, a resposta XML é analisada em uma instância `XDocument`, com o valor raiz XML que está sendo retornado ao método de chamada para exibição, conforme mostrado nas seguintes capturas de tela:

![](text-translation-images/text-translation.png "Text Translation to German")

## <a name="summary"></a>Resumo

Este artigo explicou como usar o API de Tradução de Texto da Microsoft para converter texto de um idioma em um texto de outro idioma em um aplicativo Xamarin. Forms. Além de traduzir o texto, a API do Microsoft Translator também pode transcrever a fala de um idioma para o texto de outra linguagem.

## <a name="related-links"></a>Links relacionados

- [API de tradução de texto documentação](/azure/cognitive-services/translator/).
- [Consumir um serviço Web RESTful](~/xamarin-forms/data-cloud/web-services/rest.md)
- [Serviços cognitivas todo (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)
- [Microsoft API de tradução de texto](https://docs.microsofttranslator.com/text-translate.html).
