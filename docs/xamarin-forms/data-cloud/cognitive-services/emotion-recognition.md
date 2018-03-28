---
title: Reconhecimento de emoção usando a API de Face
description: A API de Face usa uma expressão facial em uma imagem como uma entrada e retorna os dados que incluem os níveis de confiança entre um conjunto de emoções para cada face na imagem. Este artigo explica como usar a API de Face para reconhecer emoção, avalie se um aplicativo xamarin. Forms.
ms.topic: article
ms.prod: xamarin
ms.assetid: 19D36A7C-E8D8-43D1-BE80-48DE6C02879A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
ms.openlocfilehash: 0fc69fb1283ea2afd95900348cdecec5d6514ae0
ms.sourcegitcommit: 20ca85ff638dbe3a85e601b5eb09b2f95bda2807
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2018
---
# <a name="emotion-recognition-using-the-face-api"></a>Reconhecimento de emoção usando a API de Face

_A API de Face usa uma expressão facial em uma imagem como uma entrada e retorna os dados que incluem os níveis de confiança entre um conjunto de emoções para cada face na imagem. Este artigo explica como usar a API de Face para reconhecer emoção, avalie se um aplicativo xamarin. Forms._

## <a name="overview"></a>Visão geral

A API de Face pode executar detecção de emoção detectar raiva, contempt, desista, medo, felicidade, neutra, tristeza e surpresa, em uma expressão facial. Esses emoções sejam universalmente e cross-culturally comunicadas por meio das mesmas expressões faciais básico. Bem como retornar um resultado de emoção para uma expressão facial, a API de Face pode também retorna uma caixa delimitadora para faces detectadas. Observe que uma chave de API deve ser obtida para usar a API de Face. Isso pode ser obtido em [tente serviços Cognitivos](https://azure.microsoft.com/try/cognitive-services/?api=face-api).

Reconhecimento de emoção pode ser executado por meio de uma biblioteca de cliente e por meio de uma API REST. Este artigo se concentra em fazer o reconhecimento de emoção por meio de [Microsoft.ProjectOxford.Face](https://www.nuget.org/packages/Microsoft.ProjectOxford.Face/) biblioteca de cliente, que pode ser baixada do NuGet.

A API de Face também pode ser usada para reconhecer expressões de pessoas no vídeo faciais e pode retornar um resumo das suas emoções. Para obter mais informações, consulte [como analisar vídeos em tempo real](/azure/cognitive-services/face/face-api-how-to-topics/howtoanalyzevideo_face/).

Para obter mais informações sobre a API de Face, consulte [Face API](/azure/cognitive-services/face/overview/).

## <a name="performing-emotion-recognition"></a>Executar o reconhecimento de emoção

Reconhecimento de emoção é obtido, carregando um fluxo de imagem para a API de Face. O tamanho do arquivo de imagem não deve ser maior que 4MB e formatos de arquivo suportados são BMP, PNG, GIF e JPEG.

O exemplo de código a seguir mostra o processo de reconhecimento de emoção:

```csharp
using Microsoft.ProjectOxford.Face;
using Microsoft.ProjectOxford.Face.Contract;

var faceServiceClient = new FaceServiceClient(Constants.FaceApiKey, Constants.FaceEndpoint);
// e.g. var faceServiceClient = new FaceServiceClient("a3dbe2ed6a5a9231bb66f9a964d64a12", "https://westus.api.cognitive.microsoft.com/face/v1.0/detect");

var faceAttributes = new FaceAttributeType[] { FaceAttributeType.Emotion };
using (var photoStream = photo.GetStream())
{
    Face[] faces = await faceServiceClient.DetectAsync(photoStream, true, false, faceAttributes);
    if (faces.Any())
    {
        // Emotions detected are happiness, sadness, surprise, anger, fear, contempt, disgust, or neutral.
        emotionResultLabel.Text = faces.FirstOrDefault().FaceAttributes.Emotion.ToRankedList().FirstOrDefault().Key;
    }
    // Store emotion as app rating
    ...
}
```

Um `FaceServiceClient` instância deve ser criada para executar o reconhecimento de emoção, com a chave de API de Face e o ponto de extremidade que estão sendo transmitidos como argumentos para o `FaceServiceClient` construtor.

> [!NOTE]
> Você deve usar a mesma região em suas chamadas de API de Face como é usada para obter as chaves de assinatura. Por exemplo, se você adquiriu suas chaves de assinatura no `westus` região, o ponto de extremidade de detecção de rosto será `https://westus.api.cognitive.microsoft.com/face/v1.0/detect`.

O `DetectAsync` método, que é chamado no `FaceServiceClient` da instância, carrega uma imagem para a API de Face, como um `Stream`. A chave de API será enviada para a API de Face quando esta operação é invocada. Falha ao enviar uma chave de API válida resultará em um `Microsoft.ProjectOxford.Face.FaceAPIException` que está sendo gerada, com a mensagem de exceção que indica que uma chave de API inválida foi enviada.

O `DetectAsync` método retornará um `Face` de matriz, desde que uma face foi reconhecida. Cada retornado face contém um retângulo para indicar sua localização, combinada com uma série de atributos de face opcional, que são especificados pelo `faceAttributes` argumento para o `DetectAsync` método. Se nenhum face for detectado, vazio `Face` matriz será retornada.

Ao interpretar os resultados da API Face, a emoção detectada deve ser interpretada como emoção com a pontuação mais alta, como as pontuações são normalizadas para soma para um. Portanto, o aplicativo de exemplo exibe a emoção reconhecida com a pontuação mais alta para a maior face detectada na imagem, conforme mostrado nas capturas de tela seguir:

![](emotion-recognition-images/emotion-recognition.png "Reconhecimento de emoção")

## <a name="summary"></a>Resumo

Este artigo explicou como usar a API de Face para reconhecer emoção, avalie se um aplicativo xamarin. Forms. A API de Face usa uma expressão facial em uma imagem como uma entrada e retorna os dados que inclui a confiança em um conjunto de emoções para cada face na imagem.

## <a name="related-links"></a>Links relacionados

- [Enfrentam API](/azure/cognitive-services/face/overview/).
- [Serviços Cognitivos de tarefas (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoCognitiveServices/)
- [Microsoft.ProjectOxford.Face](https://www.nuget.org/packages/Microsoft.ProjectOxford.Face/)
