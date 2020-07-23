---
title: Reconhecimento de emoções percebido usando o API de Detecção Facial
description: O API de Detecção Facial usa uma expressão facial em uma imagem como uma entrada e retorna dados que incluem níveis de confiança em um conjunto de emoções para cada face na imagem. Este artigo explica como usar o API de Detecção Facial para reconhecer emoções, para classificar um Xamarin.Forms aplicativo.
ms.prod: xamarin
ms.assetid: 19D36A7C-E8D8-43D1-BE80-48DE6C02879A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/10/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: ac7b90fb3e70fd07fcafe78a68136338469862e0
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86936351"
---
# <a name="perceived-emotion-recognition-using-the-face-api"></a>Reconhecimento de emoções percebido usando o API de Detecção Facial

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)

O API de Detecção Facial pode executar a detecção de emoções para detectar raiva, con, aversão, medo, felicidade, neutro, tristeza e surpresa, em uma expressão facial baseada em anotações observadas por codificadores humanos. No entanto, é importante observar que as expressões faciais sozinhas podem não representar necessariamente os Estados internos das pessoas.

Além de retornar um resultado de emoção para uma expressão facial, a API de Detecção Facial também pode retornar uma caixa delimitadora para faces detectadas.

O reconhecimento de emoções pode ser executado por meio de uma biblioteca de cliente e por meio de uma API REST. Este artigo se concentra na execução do reconhecimento de emoções por meio da API REST. Para obter mais informações sobre a API REST, consulte [face REST API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

O API de Detecção Facial também pode ser usado para reconhecer as expressões faciais de pessoas em vídeo e pode retornar um resumo de suas emoções. Para obter mais informações, consulte [como analisar vídeos em tempo real](/azure/cognitive-services/face/face-api-how-to-topics/howtoanalyzevideo_face/).

> [!NOTE]
> Se você não tiver uma [assinatura do Azure](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), crie uma [conta gratuita](https://aka.ms/azfree-docs-mobileapps) antes de começar.

Uma chave de API deve ser obtida para usar o API de Detecção Facial. Isso pode ser obtido em [experimentar serviços cognitivas](https://azure.microsoft.com/try/cognitive-services/?api=face-api).

Para obter mais informações sobre o API de Detecção Facial, consulte [API de detecção facial](/azure/cognitive-services/face/overview/).

## <a name="authentication"></a>Autenticação

Cada solicitação feita ao API de Detecção Facial requer uma chave de API que deve ser especificada como o valor do `Ocp-Apim-Subscription-Key` cabeçalho. O exemplo de código a seguir mostra como adicionar a chave de API ao `Ocp-Apim-Subscription-Key` cabeçalho de uma solicitação:

```csharp
public FaceRecognitionService()
{
  _client = new HttpClient();
  _client.DefaultRequestHeaders.Add("ocp-apim-subscription-key", Constants.FaceApiKey);
}
```

A falha ao passar uma chave de API válida para o API de Detecção Facial resultará em um erro de resposta 401.

## <a name="perform-emotion-recognition"></a>Executar reconhecimento de emoções

O reconhecimento de emoções é executado fazendo uma solicitação POST contendo uma imagem para a `detect` API em `https://[location].api.cognitive.microsoft.com/face/v1.0` , em que `[location]]` é a região que você usou para obter sua chave de API. Os parâmetros de solicitação opcionais são:

- `returnFaceId`– se deve retornar faceIds das faces detectadas. O valor padrão é `true`.
- `returnFaceLandmarks`– se deve retornar pontos de referência de face das faces detectadas. O valor padrão é `false`.
- `returnFaceAttributes`– Se é para analisar e retornar um ou mais atributos de face especificados. Os atributos de face com suporte incluem,,,,,,, `age` ,, `gender` ,, `headPose` `smile` `facialHair` `glasses` `emotion` `hair` `makeup` `occlusion` `accessories` `blur` , `exposure` e `noise` . Observe que a análise de atributo facial tem custo adicional computacional e tempo.

O conteúdo da imagem deve ser colocado no corpo da solicitação POST como uma URL ou dados binários.

> [!NOTE]
> Os formatos de arquivo de imagem com suporte são JPEG, PNG, GIF e BMP, e o tamanho do arquivo permitido é de 1 KB para 4MB.

No aplicativo de exemplo, o processo de reconhecimento de emoções é invocado chamando o `DetectAsync` método:

```csharp
Face[] faces = await _faceRecognitionService.DetectAsync(photoStream, true, false, new FaceAttributeType[] { FaceAttributeType.Emotion });
```

Essa chamada de método especifica o fluxo que contém os dados da imagem, que faceIds deve ser retornado, que os pontos de referência de face não devem ser retornados e que a emoção da imagem deve ser analisada. Ele também especifica que os resultados serão retornados como uma matriz de `Face` objetos. Por sua vez, o `DetectAsync` método invoca a `detect` API REST que executa o reconhecimento de emoções:

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

Esse método gera um URI de solicitação e, em seguida, envia a solicitação para a `detect` API por meio do `SendRequestAsync` método.

> [!NOTE]
> Você deve usar a mesma região em suas chamadas de API de Detecção Facial, conforme usado para obter suas chaves de assinatura. Por exemplo, se você tiver obtido as chaves de assinatura da `westus` região, o ponto de extremidade de detecção facial será `https://westus.api.cognitive.microsoft.com/face/v1.0/detect` .

### <a name="send-the-request"></a>Enviar a solicitação

O `SendRequestAsync` método faz a solicitação post para o API de detecção facial e retorna o resultado como uma `Face` matriz:

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

Se a imagem for fornecida por meio de um fluxo, o método criará a solicitação POST encapsulando o fluxo de imagem em uma `StreamContent` instância, que fornece conteúdo http com base em um fluxo. Como alternativa, se a imagem for fornecida por meio de uma URL, o método criará a solicitação POST encapsulando a URL em uma `StringContent` instância, que fornece conteúdo http com base em uma cadeia de caracteres.

A solicitação POST é enviada para a `detect` API. A resposta é leitura, desserializada e retornada ao método de chamada.

A `detect` API enviará o código de status HTTP 200 (OK) na resposta, desde que a solicitação seja válida, o que indica que a solicitação foi bem-sucedida e que as informações solicitadas estão na resposta. Para obter uma lista de possíveis respostas de erro, consulte [API REST de face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

### <a name="process-the-response"></a>Processar a resposta

A resposta da API é retornada no formato JSON. Os dados JSON a seguir mostram uma mensagem de resposta típica com êxito que fornece os dados solicitados pelo aplicativo de exemplo:

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

Uma mensagem de resposta bem-sucedida consiste em uma matriz de entradas de face classificadas pelo tamanho do retângulo de face em ordem decrescente, enquanto uma resposta vazia indica que não há rostos detectadas. Cada face reconhecida inclui uma série de atributos de face opcionais, que são especificados pelo `returnFaceAttributes` argumento para o `DetectAsync` método.

No aplicativo de exemplo, a resposta JSON é desserializada em uma matriz de `Face` objetos. Ao interpretar os resultados da API de Detecção Facial, a emoção detectada deve ser interpretada como a emoção com a pontuação mais alta, pois as pontuações são normalizadas para somar uma. Portanto, o aplicativo de exemplo exibe a emoção reconhecida com a pontuação mais alta para a maior face detectada na imagem. Isso é obtido com o seguinte código:

```csharp
emotionResultLabel.Text = faces.FirstOrDefault().FaceAttributes.Emotion.ToRankedList().FirstOrDefault().Key;
```

A captura de tela a seguir mostra o resultado do processo de reconhecimento de emoções no aplicativo de exemplo:

![Reconhecimento de emoções](emotion-recognition-images/emotion-recognition.png)

## <a name="related-links"></a>Links relacionados

- [API de detecção facial](/azure/cognitive-services/face/overview/).
- [Serviços cognitivas todo (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)
- [API REST de face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
