---
title: Reconhecimento de fala usando a API de fala da Microsoft
description: A API de fala da Microsoft é uma API baseada em nuvem que fornece algoritmos para processar a linguagem falada. Este artigo explica como usar a API de REST de reconhecimento de fala Microsoft para converter áudio em texto em um aplicativo xamarin. Forms.
ms.prod: xamarin
ms.assetid: B435FF6B-8785-48D9-B2D9-1893F5A87EA1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
ms.openlocfilehash: 175c7f17d70c1aa6b8d6bf388cc24fd0f97e7f00
ms.sourcegitcommit: 6be6374664cd96a7d924c2e0c37aeec4adf8be13
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2018
ms.locfileid: "51617638"
---
# <a name="speech-recognition-using-the-microsoft-speech-api"></a>Reconhecimento de fala usando a API de fala da Microsoft

_A API de fala da Microsoft é uma API baseada em nuvem que fornece algoritmos para processar a linguagem falada. Este artigo explica como usar a API de REST de reconhecimento de fala Microsoft para converter áudio em texto em um aplicativo xamarin. Forms._

## <a name="overview"></a>Visão geral

A API de fala da Microsoft tem dois componentes:

- Um reconhecimento de fala API para a conversão de palavras faladas em texto. Reconhecimento de fala pode ser executado por meio de uma API REST, a biblioteca de cliente ou a biblioteca de serviço.
- Um texto em fala API para converter texto em palavras faladas. Conversão de texto em fala é realizada por meio da API REST.

Este artigo se concentra em fazer o reconhecimento de fala por meio da API REST. Embora as bibliotecas de cliente e serviço de suporte ao retorno de resultados parciais, a API REST só pode retornar um resultado de reconhecimento única, sem quaisquer resultados parciais.

Uma chave de API deve ser obtida para usar a API de fala da Microsoft. Isso pode ser obtido do Azure [portal](https://portal.azure.com/). Para obter mais informações, consulte [criar uma conta de serviços Cognitivos no portal do Azure](/azure/cognitive-services/cognitive-services-apis-create-account).

Para obter mais informações sobre a API de fala da Microsoft, consulte [documentação de API de fala do Microsoft](/azure/cognitive-services/speech/home/).

## <a name="authentication"></a>Autenticação

Todas as solicitações feitas para a API de REST de fala da Microsoft exigem um token de acesso do JSON Web Token (JWT), que pode ser obtido do serviço de token dos serviços cognitivos `https://api.cognitive.microsoft.com/sts/v1.0/issueToken`. Um token pode ser obtido, fazendo uma solicitação POST para o serviço de token, especificando um `Ocp-Apim-Subscription-Key` cabeçalho que contém a chave de API como seu valor.

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

O token de acesso deve ser especificado em cada API de REST de fala da Microsoft chamada como um `Authorization` cabeçalho prefixado com a cadeia de caracteres `Bearer`, conforme mostrado no exemplo de código a seguir:

```csharp
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", bearerToken);
```

Falha ao passar um token de acesso válido para a API de REST de fala da Microsoft resultará em um erro de 403 resposta.

## <a name="performing-speech-recognition"></a>Fazer o reconhecimento de fala

Reconhecimento de fala é obtido por meio de uma solicitação POST para o `recognition` API em `https://speech.platform.bing.com/speech/recognition/`. Uma única solicitação não pode conter mais de 10 segundos de áudio e a duração da solicitação total não pode exceder 14 segundos.

Conteúdo de áudio deve ser colocado no corpo POST da solicitação no formato wav.

No aplicativo de exemplo, o `RecognizeSpeechAsync` método invoca o processo de reconhecimento de fala:

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

Áudio é gravado em cada projeto específico da plataforma, como dados de wav do PCM e o `RecognizeSpeechAsync` usa o `PCLStorage` pacote do NuGet para abrir o arquivo como um fluxo de áudio. A solicitação de reconhecimento de fala URI é gerado e um token de acesso é recuperado do serviço de token. A solicitação de reconhecimento de fala é postada para o `recognition` API, que retorna uma resposta JSON que contém o resultado. A resposta JSON é desserializada, com o resultado retornado para o método de chamada para exibição.

### <a name="configuring-speech-recognition"></a>Configurando o reconhecimento de fala

O processo de reconhecimento de fala pode ser configurado especificando parâmetros de consulta HTTP:

```csharp
string GenerateRequestUri(string speechEndpoint)
{
    // To build a request URL, you should follow:
    // https://docs.microsoft.com/azure/cognitive-services/speech/getstarted/getstartedrest
    string requestUri = speechEndpoint;
    requestUri += @"dictation/cognitiveservices/v1?";
    requestUri += @"language=en-us";
    requestUri += @"&format=simple";
    System.Diagnostics.Debug.WriteLine(requestUri.ToString());
    return requestUri;
}
```

A principal configuração executada pelo `GenerateRequestUri` método é definir a localidade do conteúdo de áudio. Para obter uma lista de localidades com suporte, consulte [idiomas com suporte ](/azure/cognitive-services/speech/api-reference-rest/supportedlanguages/).

### <a name="sending-the-request"></a>Enviar a solicitação

O `SendRequestAsync` método faz a solicitação POST para a API de REST de fala da Microsoft e retorna a resposta:

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

Este método constrói a solicitação POST por:

- Envolve o fluxo de áudio em um `StreamContent` instância, que fornece conteúdo HTTP com base em um fluxo.
- Definindo o `Content-Type` cabeçalho da solicitação para `audio/wav; codec="audio/pcm"; samplerate=16000`.
- Adicionando o token de acesso para o `Authorization` cabeçalho, prefixado com a cadeia de caracteres `Bearer`.

A solicitação POST é enviada para `recognition` API. A resposta é, em seguida, ler e retornada para o método de chamada.

O `recognition` API enviará o código de status HTTP 200 (Okey) em resposta, fornecida que a solicitação é válida, que indica que a solicitação foi bem-sucedida e que as informações solicitadas estão na resposta. Para obter uma lista de possíveis respostas de erro, consulte [solução de problemas](/azure/cognitive-services/speech/troubleshooting).

### <a name="processing-the-response"></a>Processar a resposta

A resposta da API é retornada no formato JSON, com o texto reconhecido sendo contido no `name` marca. Os dados JSON a seguir mostram uma mensagem de resposta bem-sucedida típico:

```json
{  
   "RecognitionStatus":"Success",
   "DisplayText":"Go shopping tomorrow.",
   "Offset":16000000,
   "Duration":17100000
}
```

No aplicativo de exemplo, a resposta JSON é desserializada em um `SpeechResult` instância, com o resultado retornado para o método de chamada para exibição, conforme mostrado nas capturas de tela seguir:

![](speech-recognition-images/speech-recognition.png "Reconhecimento de fala")

## <a name="summary"></a>Resumo

Este artigo explicou como usar a API de REST de fala da Microsoft para converter áudio em texto em um aplicativo xamarin. Forms. Além de realizar o reconhecimento de fala, API de fala da Microsoft também pode converter o texto em palavras faladas.

## <a name="related-links"></a>Links relacionados

- [Documentação da API de fala da Microsoft](/azure/cognitive-services/speech/home/).
- [Consumir um serviço Web RESTful](~/xamarin-forms/data-cloud/consuming/rest.md)
- [Serviços Cognitivos de tarefas pendentes (amostra)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoCognitiveServices/)
