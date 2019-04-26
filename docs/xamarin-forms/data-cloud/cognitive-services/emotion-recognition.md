---
title: Reconhecimento de emoções usando a API de detecção facial
description: API de detecção facial usa uma expressão facial em uma imagem como uma entrada e retorna dados que incluem os níveis de confiança entre um conjunto de emoções para cada face na imagem. Este artigo explica como usar a API de detecção facial para reconhecer emoções, a taxa de um aplicativo xamarin. Forms.
ms.prod: xamarin
ms.assetid: 19D36A7C-E8D8-43D1-BE80-48DE6C02879A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/10/2018
ms.openlocfilehash: d703de90378991d262a4b056b9ebc98d183e3fb8
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61330566"
---
# <a name="emotion-recognition-using-the-face-api"></a>Reconhecimento de emoções usando a API de detecção facial

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoCognitiveServices/)

_API de detecção facial usa uma expressão facial em uma imagem como uma entrada e retorna dados que incluem os níveis de confiança entre um conjunto de emoções para cada face na imagem. Este artigo explica como usar a API de detecção facial para reconhecer emoções, a taxa de um aplicativo xamarin. Forms._

## <a name="overview"></a>Visão geral

API de detecção facial pode executar a detecção de emoções para detectar raiva, desdém, aversão, medo, felicidade, indiferença, tristeza e surpresa, em uma expressão facial. Essas emoções são universalmente e entre culturas comunicadas por meio das mesmas expressões faciais básicas. Bem como retornar um resultado de emoções para uma expressão facial, API de detecção facial pode também retorna uma caixa delimitadora para faces detectadas. Observe que uma chave de API deve ser obtida para usar a API de detecção facial. Isso pode ser obtido no [Experimente os serviços Cognitivos](https://azure.microsoft.com/try/cognitive-services/?api=face-api).

Reconhecimento de emoções pode ser executado por meio de uma biblioteca de cliente e por meio da API REST. Este artigo se concentra em fazer o reconhecimento de emoção por meio da API REST. Para obter mais informações sobre a API REST, consulte [API REST de detecção facial](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

API de detecção facial também pode ser usada para reconhecer as expressões faciais das pessoas em vídeo e pode retornar um resumo de suas emoções. Para obter mais informações, consulte [como analisar vídeos em tempo real](/azure/cognitive-services/face/face-api-how-to-topics/howtoanalyzevideo_face/).

Para obter mais informações sobre a API de detecção facial, consulte [API de detecção facial](/azure/cognitive-services/face/overview/).

## <a name="authentication"></a>Autenticação

Todas as solicitações feitas para a API de detecção facial requer uma chave de API que deve ser especificada como o valor da `Ocp-Apim-Subscription-Key` cabeçalho. O exemplo de código a seguir mostra como adicionar a chave de API para o `Ocp-Apim-Subscription-Key` cabeçalho de uma solicitação:

```csharp
public FaceRecognitionService()
{
  _client = new HttpClient();
  _client.DefaultRequestHeaders.Add("ocp-apim-subscription-key", Constants.FaceApiKey);
}
```

Falha ao passar uma chave de API válida para a API de detecção facial resultará em um erro de 401 resposta.

## <a name="performing-emotion-recognition"></a>Executar o reconhecimento de emoções

Reconhecimento de emoções é executado fazendo uma solicitação POST que contém uma imagem para o `detect` API em `https://[location].api.cognitive.microsoft.com/face/v1.0`, onde `[location]]` é a região que você usou para obter sua chave de API. Os parâmetros de solicitação opcionais são:

- `returnFaceId` – Se é necessário retornar faceIds as faces detectadas. O valor padrão é `true`.
- `returnFaceLandmarks` – Se é necessário retornar marcos faciais as faces detectadas. O valor padrão é `false`.
- `returnFaceAttributes` – Se deseja analisar e de retorno especificada de um ou mais atributos faciais. Atributos faciais com suporte incluem `age`, `gender`, `headPose`, `smile`, `facialHair`, `glasses`, `emotion`, `hair`, `makeup`, `occlusion`, `accessories`, `blur`, `exposure`, e `noise`. Observe que a análise de atributo de face tem custo computacional e de tempo adicional.

Conteúdo da imagem deve ser colocado no corpo da solicitação POST como uma URL ou dados binários.

> [!NOTE]
> Formatos de arquivo de imagem com suporte são PNG, JPEG, GIF e BMP, e o tamanho de arquivo permitido é de 1KB a 4MB.

No aplicativo de exemplo, o processo de reconhecimento de emoções é invocado chamando o `DetectAsync` método:

```csharp
Face[] faces = await _faceRecognitionService.DetectAsync(photoStream, true, false, new FaceAttributeType[] { FaceAttributeType.Emotion });
```

Esta chamada de método Especifica o fluxo que contém os dados da imagem, que devem ser retornadas faceIds, que não devem ser retornados marcos faciais e que a emoção da imagem deve ser analisada. Ela também especifica que os resultados serão retornados como uma matriz de `Face` objetos. Por sua vez, o `DetectAsync` método invoca o `detect` API REST que executa o reconhecimento de emoções:

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

Esse método gera um URI de solicitação e, em seguida, envia a solicitação para o `detect` API por meio de `SendRequestAsync` método.

> [!NOTE]
> Você deve usar a mesma região em suas chamadas de API de detecção facial que você usou para obter as chaves de assinatura. Por exemplo, se você adquiriu suas chaves de assinatura do `westus` região, o ponto de extremidade de detecção de face será `https://westus.api.cognitive.microsoft.com/face/v1.0/detect`.

### <a name="sending-the-request"></a>Enviar a solicitação

O `SendRequestAsync` método faz a solicitação POST para a API de detecção facial e retorna o resultado como um `Face` matriz:

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

Se a imagem é fornecida por meio de um fluxo, o método compila a solicitação de POSTAGEM envolvendo o fluxo de imagem em um `StreamContent` instância, que fornece conteúdo HTTP com base em um fluxo. Como alternativa, se a imagem é fornecida por meio de uma URL, o método se baseia a solicitação de POSTAGEM envolvendo a URL em um `StringContent` instância, que fornece conteúdo HTTP com base em uma cadeia de caracteres.

A solicitação POST é enviada para `detect` API. A resposta é ler, desserializada e retornada para o método de chamada.

O `detect` API enviará o código de status HTTP 200 (Okey) em resposta, fornecida que a solicitação é válida, que indica que a solicitação foi bem-sucedida e que as informações solicitadas estão na resposta. Para obter uma lista de possíveis respostas de erro, consulte [API REST de detecção facial](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

### <a name="processing-the-response"></a>Processar a resposta

A resposta da API é retornada em formato JSON. Os dados JSON a seguir mostram uma mensagem de resposta bem-sucedida típico que fornece os dados solicitados pelo aplicativo de exemplo:

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

Uma mensagem de resposta bem-sucedida consiste em uma matriz de entradas de face, classificados pelo tamanho do retângulo facial em ordem decrescente, enquanto uma resposta vazia não indica que nenhuma face detectada. Cada reconhecido face inclui uma série de atributos de face opcional, que são especificados pelo `returnFaceAttributes` argumento para o `DetectAsync` método.

No aplicativo de exemplo, a resposta JSON é desserializada em uma matriz de `Face` objetos. Ao interpretar os resultados da API de detecção facial, a emoção detectada deve ser interpretada como a emoção com a pontuação mais alta, como as pontuações são normalizadas para soma a um. Portanto, o aplicativo de exemplo exibe a emoção reconhecida com a pontuação mais alta para o rosto detectado maior na imagem. Isso é obtido com o código a seguir:

```csharp
emotionResultLabel.Text = faces.FirstOrDefault().FaceAttributes.Emotion.ToRankedList().FirstOrDefault().Key;
```

Captura de tela a seguir mostra o resultado do processo de reconhecimento de emoções no aplicativo de exemplo:

![](emotion-recognition-images/emotion-recognition.png "Reconhecimento de emoções")

## <a name="summary"></a>Resumo

Este artigo explicou como usar a API de detecção facial para reconhecer emoções, a taxa de um aplicativo xamarin. Forms. API de detecção facial usa uma expressão facial em uma imagem como uma entrada e retorna dados que inclui a confiança entre um conjunto de emoções para cada face na imagem.

## <a name="related-links"></a>Links relacionados

- [API de detecção facial](/azure/cognitive-services/face/overview/).
- [Serviços Cognitivos de tarefas pendentes (amostra)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoCognitiveServices/)
- [REST API de detecção facial](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
