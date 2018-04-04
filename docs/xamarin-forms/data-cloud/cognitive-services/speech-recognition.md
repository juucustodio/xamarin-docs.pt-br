---
title: Usando a API do Microsoft Speech o reconhecimento de fala
description: A API de fala da Microsoft é uma API baseada em nuvem que fornece algoritmos para processar o idioma. Este artigo explica como usar a API de REST de reconhecimento de fala Microsoft para converter áudio em texto em um aplicativo xamarin. Forms.
ms.prod: xamarin
ms.assetid: B435FF6B-8785-48D9-B2D9-1893F5A87EA1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
ms.openlocfilehash: 81e645749d239f8964047e92255e786c9b35409d
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="speech-recognition-using-the-microsoft-speech-api"></a>Usando a API do Microsoft Speech o reconhecimento de fala

_A API de fala da Microsoft é uma API baseada em nuvem que fornece algoritmos para processar o idioma. Este artigo explica como usar a API de REST de reconhecimento de fala Microsoft para converter áudio em texto em um aplicativo xamarin. Forms._

## <a name="overview"></a>Visão geral

API do Microsoft Speech tem dois componentes:

- Um reconhecimento de fala API para converter palavras faladas em texto. O reconhecimento de voz pode ser executado por meio de uma API REST, a biblioteca de cliente ou a biblioteca de serviço.
- Um texto em fala API para converter texto em palavras faladas. Conversão de texto em fala é executada por meio de uma API REST.

Este artigo se concentra na execução de reconhecimento de fala através da API REST. Enquanto as bibliotecas de cliente e serviço de suporte ao retorno resultados parciais, a API REST só pode retornar um resultado de reconhecimento único, sem qualquer resultado parcial.

Uma chave de API deve ser obtida para usar a API de fala da Microsoft. Isso pode ser obtido em [tente serviços Cognitivos](https://azure.microsoft.com/try/cognitive-services/).

Para obter mais informações sobre a API de fala da Microsoft, consulte [documentação da API Microsoft fala](/azure/cognitive-services/speech/home/).

## <a name="authentication"></a>Autenticação

Todas as solicitações feitas para a API REST do Microsoft fala requer um token de acesso JSON Web Token (JWT), que pode ser obtido do serviço de token de serviços cognitivos em `https://api.cognitive.microsoft.com/sts/v1.0/issueToken`. Um token pode ser obtido fazendo uma solicitação POST para o serviço de token, especificando um `Ocp-Apim-Subscription-Key` cabeçalho que contém a chave de API como seu valor.

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

O token de acesso retornado, que é o texto Base64, tem uma hora de expiração de 10 minutos. Portanto, o aplicativo de exemplo renova o token de acesso a cada 9 minutos.

O token de acesso deve ser especificado em cada API REST do Microsoft fala chamar como um `Authorization` cabeçalho prefixado com a cadeia de caracteres `Bearer`, conforme mostrado no exemplo de código a seguir:

```csharp
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", bearerToken);
```

Falha ao passar um token de acesso válido para a API REST do Microsoft fala resultará em um erro de 403 resposta.

## <a name="performing-speech-recognition"></a>Executar o reconhecimento de fala

O reconhecimento de fala é obtido fazendo uma solicitação POST para o `recognition` API em `https://speech.platform.bing.com/speech/recognition/`. Uma única solicitação não pode conter mais de 10 segundos de áudio e a duração total de solicitação não pode exceder 14 segundos.

Conteúdo de áudio deve ser colocado no corpo da solicitação no formato wav POST.

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
    var speechResult = JsonConvert.DeserializeObject<SpeechResult>(response);

    fileStream.Dispose();
    return speechResult;
}
```

Áudio é gravado em cada projeto específico de plataforma como dados de wav PCM e o `RecognizeSpeechAsync` método usa o `PCLStorage` pacote NuGet para abrir o arquivo como um fluxo de áudio. A solicitação de reconhecimento de fala URI é gerado e um token de acesso é recuperado do serviço de token. A solicitação de reconhecimento de fala é postada para o `recognition` API, que retorna uma resposta JSON que contém o resultado. A resposta JSON é desserializada, com o resultado retornado para o método de chamada para exibição.

### <a name="configuring-speech-recognition"></a>Configurando o reconhecimento de fala

O processo de reconhecimento de fala pode ser configurado com a especificação de parâmetros de consulta HTTP:

```csharp
string GenerateRequestUri(string speechEndpoint)
{
    // To build a request URL, you should follow:
    // https://docs.microsoft.com/en-us/azure/cognitive-services/speech/getstarted/getstartedrest
    string requestUri = speechEndpoint;
    requestUri += @"dictation/cognitiveservices/v1?";
    requestUri += @"language=en-us";
    requestUri += @"&format=simple";
    System.Diagnostics.Debug.WriteLine(requestUri.ToString());
    return requestUri;
}
```

A principal configuração executada pelo `GenerateRequestUri` método é definir a localidade do conteúdo de áudio. Para obter uma lista das localidades com suporte, consulte [idiomas com suporte ](/azure/cognitive-services/speech/api-reference-rest/supportedlanguages/).

### <a name="sending-the-request"></a>Enviar a solicitação

O `SendRequestAsync` método faz a solicitação POST para a API REST do Microsoft fala e retorna a resposta:

```csharp
async Task<string> SendRequestAsync(Stream fileStream, string url, string bearerToken, string contentType)
{
    if (httpClient == null)
    {
        httpClient = new HttpClient();
    }
    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", bearerToken);

    var content = new StreamContent(fileStream);
    content.Headers.TryAddWithoutValidation("Content-Type", contentType);
    var response = await httpClient.PostAsync(url, content);
    return await response.Content.ReadAsStringAsync();
}
```

Esse método cria a solicitação POST por:

- Quebra automática do fluxo de áudio em um `StreamContent` instância, que fornece conteúdo HTTP baseado em um fluxo.
- Definindo o `Content-Type` cabeçalho da solicitação para `audio/wav; codec="audio/pcm"; samplerate=16000`.
- Adicionar o token de acesso para o `Authorization` cabeçalho, prefixado com a cadeia de caracteres `Bearer`.

A solicitação POST é enviada para `recognition` API. A resposta é, em seguida, ler e retornada para o método de chamada.

O `recognition` API enviará o código de status HTTP 200 (Okey) na resposta, fornecida que a solicitação é válida, que indica que a solicitação foi bem-sucedida e se as informações solicitadas estão na resposta. Para obter uma lista de respostas de erro possíveis, consulte [solução de problemas](/azure/cognitive-services/speech/troubleshooting).

### <a name="processing-the-response"></a>Processamento da resposta

A resposta de API é retornada no formato JSON, com o texto reconhecido sendo contido o `name` marca. Os dados JSON a seguir mostram uma mensagem de resposta bem-sucedida típico:

```json
{  
   "RecognitionStatus":"Success",
   "DisplayText":"Go shopping tomorrow.",
   "Offset":16000000,
   "Duration":17100000
}
```

O aplicativo de exemplo, a resposta JSON é desserializada em um `SpeechResult` instância, com o resultado retornado para o método de chamada para a exibição, conforme mostrado nas capturas de tela seguir:

![](speech-recognition-images/speech-recognition.png "Reconhecimento de fala")

## <a name="summary"></a>Resumo

Este artigo explicou como usar a API REST do Microsoft fala para converter áudio em texto em um aplicativo xamarin. Forms. Além de realizar o reconhecimento de fala, a API de fala da Microsoft também pode converter texto em palavras faladas.

## <a name="related-links"></a>Links relacionados

- [Documentação da API do Microsoft Speech](/azure/cognitive-services/speech/home/).
- [Consumir um serviço Web RESTful](~/xamarin-forms/data-cloud/consuming/rest.md)
- [Serviços Cognitivos de tarefas (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoCognitiveServices/)
