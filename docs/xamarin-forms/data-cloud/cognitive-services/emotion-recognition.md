---
title: Reconhecimento de emoção usando a API de Face
description: A API de Face usa uma expressão facial em uma imagem como uma entrada e retorna os dados que incluem os níveis de confiança entre um conjunto de emoções para cada face na imagem. Este artigo explica como usar a API de Face para reconhecer emoção, avalie se um aplicativo xamarin. Forms.
ms.prod: xamarin
ms.assetid: 19D36A7C-E8D8-43D1-BE80-48DE6C02879A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/10/2018
ms.openlocfilehash: 4dc04cb077b894b255eb496b2cb2983626573897
ms.sourcegitcommit: b0a1c3969ab2a7b7fe961f4f470d1aa57b1ff2c6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2018
ms.locfileid: "34049760"
---
# <a name="emotion-recognition-using-the-face-api"></a>Reconhecimento de emoção usando a API de Face

_A API de Face usa uma expressão facial em uma imagem como uma entrada e retorna os dados que incluem os níveis de confiança entre um conjunto de emoções para cada face na imagem. Este artigo explica como usar a API de Face para reconhecer emoção, avalie se um aplicativo xamarin. Forms._

## <a name="overview"></a>Visão geral

A API de Face pode executar detecção de emoção detectar raiva, contempt, desista, medo, felicidade, neutra, tristeza e surpresa, em uma expressão facial. Esses emoções sejam universalmente e cross-culturally comunicadas por meio das mesmas expressões faciais básico. Bem como retornar um resultado de emoção para uma expressão facial, a API de Face pode também retorna uma caixa delimitadora para faces detectadas. Observe que uma chave de API deve ser obtida para usar a API de Face. Isso pode ser obtido em [tente serviços Cognitivos](https://azure.microsoft.com/try/cognitive-services/?api=face-api).

Reconhecimento de emoção pode ser executado por meio de uma biblioteca de cliente e por meio de uma API REST. Este artigo se concentra na execução do reconhecimento de emoção por meio da API REST. Para obter mais informações sobre a API REST, consulte [Face REST API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

A API de Face também pode ser usada para reconhecer expressões de pessoas no vídeo faciais e pode retornar um resumo das suas emoções. Para obter mais informações, consulte [como analisar vídeos em tempo real](/azure/cognitive-services/face/face-api-how-to-topics/howtoanalyzevideo_face/).

Para obter mais informações sobre a API de Face, consulte [Face API](/azure/cognitive-services/face/overview/).

## <a name="authentication"></a>Autenticação

Todas as solicitações feitas para a API de Face requer uma chave de API deve ser especificada como o valor de `Ocp-Apim-Subscription-Key` cabeçalho. O exemplo de código a seguir mostra como adicionar a chave de API para o `Ocp-Apim-Subscription-Key` cabeçalho de uma solicitação:

```csharp
public FaceRecognitionService()
{
  _client = new HttpClient();
  _client.DefaultRequestHeaders.Add("ocp-apim-subscription-key", Constants.FaceApiKey);
}
```

Falha ao passar uma chave de API válida para a API de Face resultará em um erro de 401 resposta.

## <a name="performing-emotion-recognition"></a>Executar o reconhecimento de emoção

Reconhecimento de emoção é executado, fazendo uma solicitação POST que contém uma imagem para o `detect` API em `https://[location].api.cognitive.microsoft.com/face/v1.0`, onde `[location]]` é a região que você usou para obter sua chave de API. Os parâmetros opcionais da solicitação são:

- `returnFaceId` – Se é necessário retornar faceIds das faces detectadas. O valor padrão é `true`.
- `returnFaceLandmarks` – Se é necessário retornar os pontos de referência de face das faces detectados. O valor padrão é `false`.
- `returnFaceAttributes` – Se deseja analisar e retornar um ou mais especificados enfrentam atributos. Atributos de face com suporte incluem `age`, `gender`, `headPose`, `smile`, `facialHair`, `glasses`, `emotion`, `hair`, `makeup`, `occlusion`, `accessories`, `blur`, `exposure`, e `noise`. Observe que a análise do atributo de face tem custo adicional de computação e de tempo.

Conteúdo da imagem deve ser colocado no corpo da solicitação POST como uma URL, ou dados binários.

> [!NOTE]
> Formatos de arquivo de imagem com suporte são PNG, JPEG, GIF e BMP, e o tamanho de arquivo permitido é de 1KB a 4MB.

O aplicativo de exemplo, o processo de reconhecimento de emoção é invocado por chamar o `DetectAsync` método:

```csharp
Face[] faces = await _faceRecognitionService.DetectAsync(photoStream, true, false, new FaceAttributeType[] { FaceAttributeType.Emotion });
```

Esta chamada de método Especifica o fluxo que contém os dados da imagem, que faceIds devem ser retornados que pontos de referência de face não devem ser retornados e que emoção da imagem deve ser analisada. Ela também especifica que os resultados serão retornados como uma matriz de `Face` objetos. Por sua vez, o `DetectAsync` método invoca o `detect` API REST que executa o reconhecimento de emoção:

```csharp
public async Task<Face[]> DetectAsync(Stream imageStream, bool returnFaceId, bool returnFaceLandmarks, IEnumerable<FaceAttributeType> returnFaceAttributes)
{
  var requestUrl =
    $"{Constants.FaceEndpoint}/detect?returnFaceId={returnFaceId}" +
    "&returnFaceLandmarks={returnFaceLandmarks}" +
    "&returnFaceAttributes={GetAttributeString(returnFaceAttributes)}";
  return await SendRequestAsync<Stream, Face[]>(HttpMethod.Post, requestUrl, imageStream);
}
```

Este método gera um URI de solicitação e, em seguida, envia a solicitação para o `detect` API por meio de `SendRequestAsync` método.

> [!NOTE]
> Você deve usar a mesma região em suas chamadas de API de Face como é usada para obter as chaves de assinatura. Por exemplo, se você adquiriu suas chaves de assinatura no `westus` região, o ponto de extremidade de detecção de rosto será `https://westus.api.cognitive.microsoft.com/face/v1.0/detect`.

### <a name="sending-the-request"></a>Enviar a solicitação

O `SendRequestAsync` método faz a solicitação POST para a API de Face e retorna o resultado como uma `Face` matriz:

```csharp
async Task<TResponse> SendRequestAsync<TRequest, TResponse>(HttpMethod httpMethod, string requestUrl, TRequest requestBody)
{
  var request = new HttpRequestMessage(httpMethod, Constants.FaceEndpoint);
  request.RequestUri = new Uri(requestUrl);
  if (requestBody != null)
  {
    if (requestBody is Stream)
    {
      request.Content = new StreamContent(requestBody as Stream);
      request.Content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");
    }
    else
    {
      // If the image is supplied via a URL
      request.Content = new StringContent(JsonConvert.SerializeObject(requestBody, s_settings), Encoding.UTF8, "application/json");
    }
  }

  HttpResponseMessage responseMessage = await _client.SendAsync(request);
  if (responseMessage.IsSuccessStatusCode)
  {
    string responseContent = null;
    if (responseMessage.Content != null)
    {
      responseContent = await responseMessage.Content.ReadAsStringAsync();
    }
    if (!string.IsNullOrWhiteSpace(responseContent))
    {
      return JsonConvert.DeserializeObject<TResponse>(responseContent, s_settings);
    }
    return default(TResponse);
  }
  else
  {
    ...
  }
  return default(TResponse);
}
```

Se a imagem é fornecida por meio de um fluxo, o método cria a solicitação POST encapsulando o fluxo de imagem em um `StreamContent` instância, que fornece conteúdo HTTP baseado em um fluxo. Como alternativa, se a imagem é fornecida por meio de uma URL, o método cria a solicitação POST encapsulando a URL em um `StringContent` instância, que fornece conteúdo HTTP com base em uma cadeia de caracteres.

A solicitação POST é enviada para `detect` API. A resposta é de leitura, desserializada e retornada para o método de chamada.

O `detect` API enviará o código de status HTTP 200 (Okey) na resposta, fornecida que a solicitação é válida, que indica que a solicitação foi bem-sucedida e se as informações solicitadas estão na resposta. Para obter uma lista de respostas de erro possíveis, consulte [Face REST API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

### <a name="processing-the-response"></a>Processamento da resposta

A resposta de API é retornada no formato JSON. Os dados JSON a seguir mostram uma mensagem de resposta bem-sucedida típica que fornece os dados solicitados pelo aplicativo de exemplo:

```json
[  
   {  
      "faceId":"8a1a80fe-1027-48cf-a7f0-e61c0f005051",
      "faceRectangle":{  
         "top":192,
         "left":164,
         "width":339,
         "height":339
      },
      "faceAttributes":{  
         "emotion":{  
            "anger":0.0,
            "contempt":0.0,
            "disgust":0.0,
            "fear":0.0,
            "happiness":1.0,
            "neutral":0.0,
            "sadness":0.0,
            "surprise":0.0
         }
      }
   }
]
```

Uma mensagem de resposta bem-sucedida consiste em uma matriz de entradas de face classificados pelo tamanho do retângulo de face em ordem decrescente, enquanto uma resposta vazia não indica nenhum faces detectadas. Cada reconhecido face inclui uma série de atributos de face opcional, que são específicos a `returnFaceAttributes` argumento para o `DetectAsync` método.

O aplicativo de exemplo, a resposta JSON é desserializada em uma matriz de `Face` objetos. Ao interpretar os resultados da API Face, a emoção detectada deve ser interpretada como emoção com a pontuação mais alta, como as pontuações são normalizadas para soma para um. Portanto, o aplicativo de exemplo exibe a emoção reconhecida com a pontuação mais alta para a maior face detectada na imagem. Isso é feito com o código a seguir:

```csharp
emotionResultLabel.Text = faces.FirstOrDefault().FaceAttributes.Emotion.ToRankedList().FirstOrDefault().Key;
```

Captura de tela a seguir mostra o resultado do processo de reconhecimento de emoção no aplicativo de exemplo:

![](emotion-recognition-images/emotion-recognition.png "Reconhecimento de emoção")

## <a name="summary"></a>Resumo

Este artigo explicou como usar a API de Face para reconhecer emoção, avalie se um aplicativo xamarin. Forms. A API de Face usa uma expressão facial em uma imagem como uma entrada e retorna os dados que inclui a confiança em um conjunto de emoções para cada face na imagem.

## <a name="related-links"></a>Links relacionados

- [Enfrentam API](/azure/cognitive-services/face/overview/).
- [Serviços Cognitivos de tarefas (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoCognitiveServices/)
- [Face API REST](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
