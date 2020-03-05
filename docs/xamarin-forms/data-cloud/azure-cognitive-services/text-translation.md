---
title: Tradução de texto usando a API do Translator
description: API do Microsoft Translator pode ser usado para converter fala e texto por meio de uma API REST. Este artigo explica como usar a API de texto do Microsoft Translator para traduzir o texto de um idioma para outro em um aplicativo xamarin. Forms.
ms.prod: xamarin
ms.assetid: 68330242-92C5-46F1-B1E3-2395D8823B0C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
ms.openlocfilehash: 841b1d4abab5e4c09249174b221da20794771a86
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2020
ms.locfileid: "78292469"
---
# <a name="text-translation-using-the-translator-api"></a>Tradução de texto usando a API do Translator

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)

_A API do Microsoft Translator pode ser usada para traduzir a fala e o texto por meio de uma API REST. Este artigo explica como usar o API de Tradução de Texto da Microsoft para traduzir texto de um idioma para outro em um aplicativo Xamarin. Forms._

## <a name="overview"></a>Visão geral

A API de tradução tem dois componentes:

- Uma tradução de texto da API REST para traduzir o texto de um idioma para o texto de outro idioma. A API automaticamente detecta o idioma do texto que foi enviado antes de converter.
- Uma tradução de fala a API REST para transcrição de fala de um idioma no texto de outro idioma. A API também integra funcionalidades de conversão de texto em fala para falar o texto traduzido novamente.

Este artigo se concentra na tradução de texto de um idioma para outro usando a API de tradução de texto.

> [!NOTE]
> Se você não tiver uma [assinatura do Azure](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), crie uma [conta gratuita](https://aka.ms/azfree-docs-mobileapps) antes de começar.

Uma chave de API deve ser obtida para usar a API de tradução de texto. Isso pode ser obtido em [como se inscrever no Microsoft API de tradução de texto](/azure/cognitive-services/translator/translator-text-how-to-signup/).

Para obter mais informações sobre o Microsoft API de Tradução de Texto, consulte [API de tradução de texto documentação](/azure/cognitive-services/translator/).

## <a name="authentication"></a>Autenticação

Cada solicitação feita ao API de Tradução de Texto requer um token de acesso JWT (token Web JSON), que pode ser obtido do serviço de token de serviços cognitivas em `https://api.cognitive.microsoft.com/sts/v1.0/issueToken`. Um token pode ser obtido fazendo uma solicitação POST para o serviço de token, especificando um cabeçalho de `Ocp-Apim-Subscription-Key` que contém a chave de API como seu valor.

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

O token de acesso deve ser especificado em cada chamada de API de Tradução de Texto como um cabeçalho de `Authorization` prefixado com a cadeia de caracteres `Bearer`, conforme mostrado no exemplo de código a seguir:

```csharp
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", bearerToken);
```

Para obter mais informações sobre o serviço de token de serviços cognitivas, consulte [autenticação](/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="performing-text-translation"></a>Executar tradução de texto

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

O método `TranslateTextAsync` gera um URI de solicitação e recupera um token de acesso do serviço de token. A solicitação de conversão de texto é enviada para a API de `translate`, que retorna uma resposta XML que contém o resultado. A resposta XML é analisada, e o resultado da conversão é retornado para o método de chamada para exibição.

Para obter mais informações sobre as APIs REST de tradução de texto, consulte [API de tradução de texto](/azure/cognitive-services/translator/reference/v3-0-reference).

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

Esse método define o texto a ser convertido e o idioma para traduzir o texto a ser. Para obter uma lista dos idiomas com suporte do Microsoft Translator, consulte [idiomas com suporte no microsoft API de tradução de texto](/azure/cognitive-services/translator/languages/).

> [!NOTE]
> Se um aplicativo precisar saber em qual idioma o texto está, a API `Detect` pode ser chamada para detectar o idioma da cadeia de texto.

### <a name="sending-the-request"></a>Enviar a solicitação

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

Esse método cria a solicitação GET adicionando o token de acesso ao cabeçalho `Authorization`, prefixado com a cadeia de caracteres `Bearer`. Em seguida, a solicitação GET é enviada para a API de `translate`, com a URL de solicitação especificando o texto a ser traduzido e o idioma para o qual converter o texto. A resposta é, em seguida, ler e retornada para o método de chamada.

A API de `translate` enviará o código de status HTTP 200 (OK) na resposta, desde que a solicitação seja válida, o que indica que a solicitação foi bem-sucedida e que as informações solicitadas estão na resposta. Para obter uma lista de possíveis respostas de erro, consulte mensagens de resposta em [obter tradução](/azure/cognitive-services/translator/reference/v3-0-translate).

### <a name="processing-the-response"></a>Processar a resposta

A resposta da API é retornada em formato XML. Os dados XML a seguir mostram uma mensagem de resposta bem-sucedida típico:

```xml
<string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">Morgen kaufen gehen ein</string>
```

No aplicativo de exemplo, a resposta XML é analisada em uma instância `XDocument`, com o valor raiz XML que está sendo retornado ao método de chamada para exibição, conforme mostrado nas seguintes capturas de tela:

![](text-translation-images/text-translation.png "Text Translation to German")

## <a name="summary"></a>Resumo

Este artigo explicou como usar a API de texto do Microsoft Translator para traduzir o texto de um idioma para o texto de outro idioma em um aplicativo xamarin. Forms. Além de traduzir o texto, API do Microsoft Translator pode transcrever também fala de um idioma no texto de outro idioma.

## <a name="related-links"></a>Links relacionados

- [Documentação do API de Tradução de Texto](/azure/cognitive-services/translator/)
- [Consumir um serviço Web RESTful](~/xamarin-forms/data-cloud/web-services/rest.md)
- [Serviços cognitivas todo (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)
- [API de Tradução de Texto](/azure/cognitive-services/translator/reference/v3-0-reference)
