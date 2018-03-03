---
title: "Conversão de texto usando o tradutor de API"
description: "A API do Microsoft Translator pode ser usada para traduzir fala e texto por meio de uma API REST. Este artigo explica como usar a API de conversão de texto do Microsoft para traduzir o texto de um idioma para outro em um aplicativo xamarin. Forms."
ms.topic: article
ms.prod: xamarin
ms.assetid: 68330242-92C5-46F1-B1E3-2395D8823B0C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
ms.openlocfilehash: f403ebaffdf742c22e61b73aee7a42648fe597dc
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2018
---
# <a name="text-translation-using-the-translator-api"></a>Conversão de texto usando o tradutor de API

_A API do Microsoft Translator pode ser usada para traduzir fala e texto por meio de uma API REST. Este artigo explica como usar a API de conversão de texto do Microsoft para traduzir o texto de um idioma para outro em um aplicativo xamarin. Forms._

## <a name="overview"></a>Visão geral

A API de conversor tem dois componentes:

- Uma conversão de texto da API REST para converter um texto em texto de outro idioma. A API detecta automaticamente o idioma do texto que foi enviado antes de converter.
- Uma tradução de fala a API REST para transcrever fala de um idioma para o texto de outro idioma. A API também integra recursos de texto em fala para dizer o texto traduzido novamente.

Este artigo se concentra em traduções de um idioma para outro usando a API de conversão de texto.

Uma chave de API deve ser obtida para usar a API de conversão de texto. Isso pode ser obtido seguindo as instruções em [Introdução](http://docs.microsofttranslator.com/text-translate.html) na [docs.microsofttranslator.com](http://docs.microsofttranslator.com/).

Para obter mais informações sobre a API do Microsoft Translator, consulte [Microsoft Translator documentação](https://www.microsoft.com/cognitive-services/translator-api/documentation/TranslatorInfo/overview) em microsoft.com.

## <a name="authentication"></a>Autenticação

Todas as solicitações feitas para a API de conversão de texto requer um token de acesso de JSON Web Token (JWT), que pode ser obtido do serviço de token de serviços cognitivos em `https://api.cognitive.microsoft.com/sts/v1.0/issueToken`. Um token pode ser obtido fazendo uma solicitação POST para o serviço de token, especificando um `Ocp-Apim-Subscription-Key` cabeçalho que contém a chave de API como seu valor.

O exemplo de código a seguir mostra como solicitar um acesso de token do serviço de token:

```csharp
async Task<string> FetchTokenAsync(string fetchUri, string apiKey)
{
  using (var client = new HttpClient())
  {
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);
    UriBuilder uriBuilder = new UriBuilder(fetchUri);
    uriBuilder.Path += "/issueToken";

    var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
    return await result.Content.ReadAsStringAsync();
  }
}
```

O token de acesso retornado, que é o texto Base64, tem uma hora de expiração de 10 minutos. Portanto, o aplicativo de exemplo renova o token de acesso a cada 9 minutos.

O token de acesso deve ser especificado em cada API de conversão de texto chamada como um `Authorization` cabeçalho prefixado com a cadeia de caracteres `Bearer`, conforme mostrado no exemplo de código a seguir:

```csharp
using (var httpClient = new HttpClient())
{
  httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", bearerToken);
  ...
}  
```

Para obter mais informações sobre o serviço de token de serviços cognitivas, consulte [API de Token de autenticação](http://docs.microsofttranslator.com/oauth-token.html) na [docs.microsofttranslator.com](http://docs.microsofttranslator.com/).

## <a name="performing-text-translation"></a>Executar a conversão de texto

Conversão de texto pode ser obtido fazendo uma solicitação GET para a `Translate` API em `https://api.microsofttranslator.com/v2/http.svc/Translate`. O aplicativo de exemplo, o `TranslateTextAsync` método invoca o processo de conversão de texto:

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

O `TranslateTextAsync` método gera um URI de solicitação e recupera um token de acesso do serviço de token. A solicitação de conversão de texto é enviada para o `Translate` API, que retorna uma resposta XML que contém o resultado. A resposta XML é analisada e o resultado da conversão é retornado para o método de chamada para exibição.

Para obter mais informações sobre as APIs de REST de conversão de texto, consulte [código de exemplo](http://docs.microsofttranslator.com/text-translate.html#/default) na [docs.microsofttranslator.com](http://docs.microsofttranslator.com/).

### <a name="configuring-text-translation"></a>Configuração da conversão de texto

O processo de conversão de texto pode ser configurado com a especificação de parâmetros de consulta HTTP. Há parâmetros obrigatórias e opcionais, com o seguinte método mostrando os parâmetros compulsórios que devem ser definidos:

```csharp
string GenerateRequestUri(string endpoint, string text, string to)
{
  string requestUri = endpoint;
  requestUri += string.Format("?text={0}", Uri.EscapeUriString(text));
  requestUri += string.Format("&to={0}", to);
  return requestUri;
}
```

Esse método define o texto a ser convertido e o idioma para traduzir o texto. Para obter uma lista dos idiomas suportados pelo Microsoft Translator, consulte [idiomas](https://www.microsoft.com/translator/languages.aspx) em microsoft.com.

> [!NOTE]
> Se um aplicativo precisa saber a linguagem em que o texto está no, o `Detect` API pode ser chamada para detectar o idioma da cadeia de caracteres de texto.

Para obter mais informações sobre os parâmetros obrigatórias e opcionais, consulte [API de conversão de texto](http://docs.microsofttranslator.com/text-translate.html#!/default/get_Translate) na [docs.microsofttranslator.com](http://docs.microsofttranslator.com/).

### <a name="sending-the-request"></a>Enviar a solicitação

O `SendRequestAsync` método faz a solicitação GET para a API de REST de conversão de texto e retorna a resposta:

```csharp
async Task<string> SendRequestAsync(string url, string bearerToken)
{
  using (var httpClient = new HttpClient())
  {
    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", bearerToken);
    var response = await httpClient.GetAsync(url);
    return await response.Content.ReadAsStringAsync();
  }
}
```

Este método cria a solicitação de obtenção, adicionando o token de acesso para o `Authorization` cabeçalho, prefixado com a cadeia de caracteres `Bearer`. A solicitação GET é enviada para o `Translate` API, com a URL da solicitação especifica o texto a ser traduzido e o idioma para traduzir o texto. A resposta é, em seguida, ler e retornada para o método de chamada.

O `Translate` API enviará o código de status HTTP 200 (Okey) na resposta, fornecida que a solicitação é válida, que indica que a solicitação foi bem-sucedida e se as informações solicitadas estão na resposta. Para obter uma lista de respostas de erro possíveis, consulte as mensagens de resposta em [obter traduzir](http://docs.microsofttranslator.com/text-translate.html#!/default/get_Translate) na [docs.microsofttranslator.com](http://docs.microsofttranslator.com/).

### <a name="processing-the-response"></a>Processamento da resposta

A resposta da API é retornada em formato XML. Os dados XML a seguir mostram uma mensagem de resposta bem-sucedida típico:

```xml
<string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">Morgen kaufen gehen ein</string>
```

O aplicativo de exemplo, a resposta XML é analisada em um `XDocument` instância com o valor de raiz XML que está sendo retornado para o método de chamada para exibição conforme mostrado nas capturas de tela seguir:

![](text-translation-images/text-translation.png "Conversão de texto em alemão")

## <a name="summary"></a>Resumo

Este artigo explicou como usar a API de conversão de texto do Microsoft para converter um texto em texto de outro idioma em um aplicativo xamarin. Forms. Além de traduzir o texto, a API do Microsoft Translator pode transcrever também fala de um idioma em texto de outro idioma.



## <a name="related-links"></a>Links relacionados

- [Documentação do Microsoft Translator](https://www.microsoft.com/cognitive-services/translator-api/documentation/TranslatorInfo/overview)
- [Consumir um serviço Web RESTful](~/xamarin-forms/data-cloud/consuming/rest.md)
- [Serviços Cognitivos de tarefas (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoCognitiveServices/)
- [API de conversão de texto](http://docs.microsofttranslator.com/text-translate.html)
