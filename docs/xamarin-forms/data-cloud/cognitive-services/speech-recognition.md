---
title: Reconhecimento de fala, usando a API de voz do Bing
description: "A API do Bing fala é uma API baseada em nuvem que fornece algoritmos para processar o idioma. Este artigo explica como usar a API de REST de reconhecimento de fala Bing para converter áudio em texto em um aplicativo xamarin. Forms."
ms.topic: article
ms.prod: xamarin
ms.assetid: B435FF6B-8785-48D9-B2D9-1893F5A87EA1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
ms.openlocfilehash: 186ea6277ec7bd4ceb52855186e6fd88344b1b86
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2018
---
# <a name="speech-recognition-using-the-bing-speech-api"></a>Reconhecimento de fala, usando a API de voz do Bing

_A API do Bing fala é uma API baseada em nuvem que fornece algoritmos para processar o idioma. Este artigo explica como usar a API de REST de reconhecimento de fala Bing para converter áudio em texto em um aplicativo xamarin. Forms._

![](~/media/shared/preview.png "Esta API é atualmente pré-lançamento")

> [!NOTE]
> A API do Bing fala ainda está em visualização. Há pode ser alterações significativas para a API antes da versão final.

## <a name="overview"></a>Visão geral

A API do Bing fala tem dois componentes:

- Um reconhecimento de fala API para converter palavras faladas em texto. O reconhecimento de voz pode ser executado por meio de uma API REST, a biblioteca de cliente ou a biblioteca de serviço.
- Um texto em fala API para converter texto em palavras faladas. Conversão de texto em fala é executada por meio de uma API REST.

Este artigo se concentra na execução de reconhecimento de fala através da API REST. Enquanto as bibliotecas de cliente e serviço de suporte ao retorno resultados parciais, a API REST só pode retornar um resultado de reconhecimento único, sem qualquer resultado parcial.

Uma chave de API deve ser obtida para usar a API de reconhecimento de fala do Bing. Isso pode ser obtido em [Introdução gratuitamente](https://www.microsoft.com/cognitive-services/sign-up?ReturnUrl=/cognitive-services/subscriptions?productId=%2fproducts%2fBing.Speech.Preview) em microsoft.com.

Para obter mais informações sobre a API do Bing fala, consulte [Bing fala documentação](https://www.microsoft.com/cognitive-services/Speech-api/documentation/overview) em microsoft.com.

## <a name="authentication"></a>Autenticação

Todas as solicitações feitas para a API de REST de reconhecimento de fala Bing requer um token de acesso JSON Web Token (JWT), que pode ser obtido do serviço de token de serviços cognitivos em `https://api.cognitive.microsoft.com/sts/v1.0/issueToken`. Um token pode ser obtido fazendo uma solicitação POST para o serviço de token, especificando um `Ocp-Apim-Subscription-Key` cabeçalho que contém a chave de API como seu valor.

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

O token de acesso deve ser especificado em cada API de REST de reconhecimento de fala do Bing chamar como um `Authorization` cabeçalho prefixado com a cadeia de caracteres `Bearer`, conforme mostrado no exemplo de código a seguir:

```csharp
using (var httpClient = new HttpClient())
{
  httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", bearerToken);
  ...
}  
```

Falha ao passar um token de acesso válido para a API de REST de reconhecimento de fala Bing resultará em um erro de 403 resposta.

## <a name="performing-speech-recognition"></a>Executar o reconhecimento de fala

O reconhecimento de fala é obtido fazendo uma solicitação POST para `recognize` API em `https://speech.platform.bing.com/recognize`. Uma única solicitação não pode conter mais de 10 segundos de áudio e a duração total de solicitação não pode exceder 14 segundos.

Conteúdo de áudio deve ser colocado no corpo da solicitação no formato wav POST. Para obter informações sobre os codecs com suporte, consulte [suporte Codecs](https://www.microsoft.com/cognitive-services/Speech-api/documentation/API-Reference-REST/BingVoiceRecognition#supported-codecs) em microsoft.com.

O aplicativo de exemplo, o `RecognizeSpeechAsync` método invoca o processo de reconhecimento de fala:

```csharp
public async Task<SpeechResult> RecognizeSpeechAsync(string filename)
{
    ...

    // Read audio file to a stream
    var file = await PCLStorage.FileSystem.Current.LocalStorage.GetFileAsync(filename);
    var fileStream = await file.OpenAsync(PCLStorage.FileAccess.Read);

    // Send audio stream to Bing and deserialize the response
    string requestUri = GenerateRequestUri(Constants.SpeechRecognitionEndpoint);
    string accessToken = authenticationService.GetAccessToken();
    var response = await SendRequestAsync(fileStream, requestUri, accessToken, Constants.AudioContentType);
    var speechResults = JsonConvert.DeserializeObject<SpeechResults>(response);

    fileStream.Dispose();
    return speechResults.results.FirstOrDefault();
}
```

Áudio é gravado em cada projeto específico de plataforma como dados de wav PCM e o `RecognizeSpeechAsync` método usa o `PCLStorage` pacote NuGet para abrir o arquivo como um fluxo de áudio. A solicitação de reconhecimento de fala URI é gerado e um token de acesso é recuperado do serviço de token. A solicitação de reconhecimento de fala é postada para o `recognize` API, que retorna uma resposta JSON que contém o resultado. A resposta JSON é desserializada, com o resultado retornado para o método de chamada para exibição.

### <a name="configuring-speech-recognition"></a>Configurando o reconhecimento de fala

O processo de reconhecimento de fala pode ser configurado com a especificação de parâmetros de consulta HTTP. Há parâmetros obrigatórias e opcionais, com o seguinte método mostrando os parâmetros compulsórios que devem ser definidos:

```csharp
string GenerateRequestUri(string speechEndpoint)
{
    string requestUri = speechEndpoint;
    requestUri += @"?scenarios=ulm";                                    // websearch is the other option
    requestUri += @"&appid=D4D52672-91D7-4C74-8AD8-42B1D98141A5";       // You must use this ID
    requestUri += @"&locale=en-US";                                     // Other languages supported
    requestUri += string.Format("&device.os={0}", operatingSystem);     // Open field
    requestUri += @"&version=3.0";                                      // Required value
    requestUri += @"&format=json";                                      // Required value
    requestUri += @"&instanceid=fe34a4de-7927-4e24-be60-f0629ce1d808";  // GUID for device making the request
    requestUri += @"&requestid=" + Guid.NewGuid().ToString();           // GUID for the request
    return requestUri;
}
```

A principal configuração executada pelo `GenerateRequestUri` método é definir a localidade do conteúdo de áudio. Para obter uma lista das localidades com suporte, consulte [localidades com suporte ](https://www.microsoft.com/cognitive-services/Speech-api/documentation/API-Reference-REST/BingVoiceRecognition#supported-locales) em microsoft.com.

Para obter mais informações sobre os possíveis valores para os parâmetros obrigatórias, consulte [parâmetros necessários](https://www.microsoft.com/cognitive-services/Speech-api/documentation/API-Reference-REST/BingVoiceRecognition#required-parameters) em microsoft.com. Para obter informações sobre os parâmetros opcionais, consulte [parâmetros opcionais](https://www.microsoft.com/cognitive-services/Speech-api/documentation/API-Reference-REST/BingVoiceRecognition) em microsoft.com.

### <a name="sending-the-request"></a>Enviar a solicitação

O `SendRequestAsync` método faz a solicitação POST para a API de REST de reconhecimento de fala Bing e retorna a resposta:

```csharp
async Task<string> SendRequestAsync(Stream fileStream, string url, string bearerToken, string contentType)
{
    var content = new StreamContent(fileStream);
    content.Headers.TryAddWithoutValidation("Content-Type", contentType);

    using (var httpClient = new HttpClient())
    {
        httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", bearerToken);
        var response = await httpClient.PostAsync(url, content);

        return await response.Content.ReadAsStringAsync();
    }
}
```

Esse método cria a solicitação POST por:

- Quebra automática do fluxo de áudio em um `StreamContent` instância, que fornece conteúdo HTTP baseado em um fluxo.
- Definindo o `Content-Type` cabeçalho da solicitação para `audio/wav; codec="audio/pcm"; samplerate=16000`.
- Adicionar o token de acesso para o `Authorization` cabeçalho, prefixado com a cadeia de caracteres `Bearer`.

A solicitação POST é enviada para `recognize` API. A resposta é, em seguida, ler e retornada para o método de chamada.

O `recognize` API enviará o código de status HTTP 200 (Okey) na resposta, fornecida que a solicitação é válida, que indica que a solicitação foi bem-sucedida e se as informações solicitadas estão na resposta. Para obter uma lista de respostas de erro possíveis, consulte [respostas de erro](https://www.microsoft.com/cognitive-services/Speech-api/documentation/API-Reference-REST/BingVoiceRecognition#error-responses) em microsoft.com.

### <a name="processing-the-response"></a>Processamento da resposta

A resposta de API é retornada no formato JSON, com o texto reconhecido sendo contido o `name` marca. Os dados JSON a seguir mostram uma mensagem de resposta bem-sucedida típico:

```csharp
{
  "version": "3.0",
  "header": {
    "status": "success",
    "scenario": "ulm",
    "name": "go shopping tomorrow",
    "lexical": "go shopping tomorrow",
    "properties": {
      "requestid": "e06c059d-fa37-4bb1-843f-4914350279a8",
      "HIGHCONF": "1"
    }
  },
  "results": [
    {
      "scenario": "ulm",
      "name": "go shopping tomorrow",
      "lexical": "go shopping tomorrow",
      "confidence": "0.9493451",
      "properties": {
        "HIGHCONF": "1"
      }
    }
  ]
}
```

O aplicativo de exemplo, a resposta JSON é desserializada em um `SpeechResult` instância, com o resultado retornado para o método de chamada para a exibição, conforme mostrado nas capturas de tela seguir:

![](speech-recognition-images/speech-recognition.png "Reconhecimento de fala")

Para obter informações sobre os valores de cada marca JSON, consulte [respostas de reconhecimento de fala](https://www.microsoft.com/cognitive-services/Speech-api/documentation/API-Reference-REST/BingVoiceRecognition#speech-recognition-responses) em microsoft.com.

## <a name="summary"></a>Resumo

Este artigo explicou como usar a API de REST de reconhecimento de fala Bing para converter áudio em texto em um aplicativo xamarin. Forms. Além de realizar o reconhecimento de fala, a API do Bing fala também pode converter texto em palavras faladas.



## <a name="related-links"></a>Links relacionados

- [Documentação de fala do Bing](https://www.microsoft.com/cognitive-services/Speech-api/documentation/overview)
- [Consumir um serviço Web RESTful](~/xamarin-forms/data-cloud/consuming/rest.md)
- [Serviços Cognitivos de tarefas (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoCognitiveServices/)
- [Reconhecimento de fala API REST do Bing](https://www.microsoft.com/cognitive-services/Speech-api/documentation/API-Reference-REST/BingVoiceRecognition)
