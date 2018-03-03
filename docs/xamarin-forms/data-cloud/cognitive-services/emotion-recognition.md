---
title: "Reconhecimento de emoção usando a API de emoção"
description: "A API de emoção usa uma expressão facial em uma imagem como uma entrada e retorna os níveis de confiança em um conjunto de emoções para cada face na imagem. Este artigo explica como usar a API de emoção reconhecer emoção, avalie se um aplicativo xamarin. Forms."
ms.topic: article
ms.prod: xamarin
ms.assetid: 19D36A7C-E8D8-43D1-BE80-48DE6C02879A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
ms.openlocfilehash: 159bd1b23eb7505c5d5629570a34d54e0525567e
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2018
---
# <a name="emotion-recognition-using-the-emotion-api"></a>Reconhecimento de emoção usando a API de emoção

_A API de emoção usa uma expressão facial em uma imagem como uma entrada e retorna os níveis de confiança em um conjunto de emoções para cada face na imagem. Este artigo explica como usar a API de emoção reconhecer emoção, avalie se um aplicativo xamarin. Forms._

![](~/media/shared/preview.png "Esta API é atualmente pré-lançamento")

> [!NOTE]
> A API de emoção ainda está em visualização. Há pode ser alterações significativas para a API antes da versão final.

## <a name="overview"></a>Visão geral

A API de emoção pode detectar raiva, contempt, desista, medo, felicidade, neutral, tristeza e surpresa, em uma expressão facial. Esses emoções sejam universalmente e cross-culturally comunicadas por meio das mesmas expressões faciais básico. Assim como retornar um resultado de emoção para uma expressão facial, a API de emoção também retorna uma caixa delimitadora para faces detectadas usando a API de Face. Se um usuário já chamou a API de Face, eles podem enviar o retângulo de face como uma entrada opcional. Observe que uma chave de API deve ser obtida para usar a API de emoção. Isso pode ser obtido em [Introdução gratuitamente](https://www.microsoft.com/cognitive-services/sign-up) em microsoft.com.

Reconhecimento de emoção pode ser executado por meio de uma biblioteca de cliente e por meio de uma API REST. Este artigo se concentra em fazer o reconhecimento de emoção por meio de [Microsoft.ProjectOxford.Emotion](https://www.nuget.org/packages/Microsoft.ProjectOxford.Emotion/) biblioteca de cliente, que pode ser baixada do NuGet.

A API de emoção também pode ser usada para reconhecer expressões de pessoas no vídeo faciais e retorna um resumo de suas emoções. Para obter mais informações, consulte [emoção vídeo](https://www.microsoft.com/cognitive-services/emotion-api/documentation#emotion-in-video) em microsoft.com.

Para obter mais informações sobre a API de emoção, consulte [documentação da API emoção](https://www.microsoft.com/cognitive-services/emotion-api/documentation) em microsoft.com.

## <a name="performing-emotion-recognition"></a>Executar o reconhecimento de emoção

Reconhecimento de emoção é obtido, carregando um fluxo de imagem para a API de emoção. O tamanho do arquivo de imagem não deve ser maior que 4MB e formatos de arquivo suportados são BMP, PNG, GIF e JPEG. Dentro da imagem, o intervalo de tamanho de face detectáveis é 36 36 para 4096 x 4096 pixels. Todas as faces fora desse intervalo não seja detectadas.

O exemplo de código a seguir mostra o processo de reconhecimento de emoção:

```csharp
using Microsoft.ProjectOxford.Emotion;
using Microsoft.ProjectOxford.Emotion.Contract;

var emotionClient = new EmotionServiceClient(Constants.EmotionApiKey);

using (var photoStream = photo.GetStream())
{
  Emotion[] emotionResult = await emotionClient.RecognizeAsync(photoStream);
  if (emotionResult.Any())
  {
    // Emotions detected are happiness, sadness, surprise, anger, fear, contempt, disgust, or neutral.
    emotionResultLabel.Text = emotionResult.FirstOrDefault().Scores.ToRankedList().FirstOrDefault().Key;
  }
  // Store emotion as app rating
  ...
}
```

Um `EmotionServiceClient` instância deve ser criada para executar o reconhecimento de emoção, com a chave de API de emoção sendo passada como um argumento para o `EmotionServiceClient` construtor.

O `RecognizeAsync` método, que é chamado no `EmotionServiceClient` da instância, carrega uma imagem para a API de emoção, como um `Stream`. A chave de API será enviada para a API de emoção quando esta operação é invocada. Falha ao enviar uma chave de API válida resultará em um `Microsoft.ProjectOxford.Common.ClientException` que está sendo gerada, com a mensagem de exceção que indica que uma chave de API inválida foi enviada.

O `RecognizeAsync` método retornará um `Emotion` de matriz, desde que uma face foi reconhecida. Para cada imagem, o número máximo de faces que podem ser detectados é 64 e as faces são classificadas pelo tamanho do retângulo de face em ordem decrescente. Se nenhum face for detectado, vazio `Emotion` matriz será retornada.

Ao interpretar os resultados da API emoção, a emoção detectada deve ser interpretada como emoção com a pontuação mais alta, como as pontuações são normalizadas para soma para um. Portanto, o aplicativo de exemplo exibe a emoção reconhecida com a pontuação mais alta para a maior face detectada na imagem, conforme mostrado nas capturas de tela seguir:

![](emotion-recognition-images/emotion-recognition.png "Reconhecimento de emoção")

## <a name="summary"></a>Resumo

Este artigo explicou como usar a API de emoção reconhecer emoção, avalie se um aplicativo xamarin. Forms. A API de emoção usa uma expressão facial em uma imagem como uma entrada e retorna a confiança em um conjunto de emoções para cada face na imagem.


## <a name="related-links"></a>Links relacionados

- [Documentação da API de emoção](https://www.microsoft.com/cognitive-services/emotion-api/documentation)
- [Serviços Cognitivos de tarefas (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoCognitiveServices/)
- [Microsoft.ProjectOxford.Emotion](https://www.nuget.org/packages/Microsoft.ProjectOxford.Emotion/)
- [Emoção API](https://dev.projectoxford.ai/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa)
