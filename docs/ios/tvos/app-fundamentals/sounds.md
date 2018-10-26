---
title: Reprodução de som com AVAudioPlayer no Xamarin do tvOS
description: Este artigo mostra como usar uma classe auxiliar para controlar a reprodução do som usando um AVAudioPlayer em um aplicativo xamarin. IOS.
ms.prod: xamarin
ms.assetid: E0305572-DC64-48BB-BD97-0A5096E6CA04
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: bc1e9febea9529c3fadbacd689404562952276fe
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50103628"
---
# <a name="playing-sound-in-tvos-with-avaudioplayer-in-xamarin"></a>Reprodução de som com AVAudioPlayer no Xamarin do tvOS

## <a name="about-the-avaudioplayer"></a>Sobre o AVAudioPlayer

O `AVAudioPlayer` é usado para dados de áudio de reprodução de memória ou um arquivo. Apple recomenda o uso dessa classe para reproduzir áudio no seu aplicativo, a menos que você estiver fazendo streaming de rede ou exige e/s de áudio de baixa latência.

Você pode usar o `AVAudioPlayer` para fazer o seguinte:

- Reproduzir sons de qualquer duração de repetição opcional.
- Reproduzir sons vários ao mesmo tempo com a sincronização opcional.
- Controlar o volume, velocidade de reprodução e posicionamento estéreo para cada reprodução de sons.
- Suporte a recursos como Avançar ou retroceder.
- Obter dados de medição de nível de reprodução.

`AVAudioPlayer` dá suporte a sons em qualquer formato de áudio fornecido pelo iOS, tvOS e OS X, tal como `.aif`, `.wav` ou `.mp3`.

## <a name="playing-sounds-in-tvos"></a>Reprodução de sons em tvOS

Como o tvOS suporta as mesmas classes de caixa de ferramentas de áudio como iOS, consulte nosso iOS [tocando som com AVAudioPlayer](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/sound/avaudioplayer) documentação para os detalhes completos de reproduzir áudio em um aplicativo xamarin. tvos.



## <a name="related-links"></a>Links relacionados

- [Referência de AVAudioPlayer](https://developer.apple.com/library/ios/documentation/AVFoundation/Reference/AVAudioPlayerClassReference/)
