---
title: Reproduzir som com AVAudioPlayer no xamarin. Mac
description: Este documento descreve como reproduzir um som com AVAudioPlayer em um aplicativo xamarin. Mac. Ele aborda AVAudioPlayer em um alto nível e links para outros documentos que explora a ele mais detalhadamente.
ms.prod: xamarin
ms.assetid: 4A683A94-F75D-4EAF-8497-E9443653250B
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 10/19/2016
ms.openlocfilehash: 9aeb7bbfc2fddef1f690b5299ec060c475ea1ce7
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50120210"
---
# <a name="playing-sound-with-avaudioplayer-in-xamarinmac"></a>Reproduzir som com AVAudioPlayer no xamarin. Mac

## <a name="about-the-avaudioplayer"></a>Sobre o AVAudioPlayer

O `AVAudioPlayer` classe é usada para dados de áudio de reprodução de memória ou um arquivo. Apple recomenda o uso dessa classe para reproduzir áudio no seu aplicativo, a menos que você estiver fazendo streaming de rede ou exige e/s de áudio de baixa latência.

Você pode usar o `AVAudioPlayer` classe para fazer o seguinte:

- Reproduzir sons de qualquer duração de repetição opcional.
- Reproduzir sons vários ao mesmo tempo com a sincronização opcional.
- Controlar o volume, velocidade de reprodução e posicionamento estéreo para cada reprodução de sons.
- Suporte a recursos como Avançar ou retroceder.
- Obter dados de medição de nível de reprodução.

`AVAudioPlayer` dá suporte a sons em qualquer formato de áudio fornecido pelo iOS, tvOS e macOS, como. AIF,. wav ou. mp3.

## <a name="playing-sounds-in-macos"></a>Reprodução de sons no macOS

Como o macOS suporta as mesmas classes de caixa de ferramentas de áudio como iOS, consulte nosso iOS [tocando som com AVAudioPlayer](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/sound/avaudioplayer) documentação para os detalhes completos de reprodução de áudio em um aplicativo xamarin. Mac.

## <a name="related-links"></a>Links relacionados

- [Referência de AVAudioPlayer](https://developer.apple.com/documentation/avfoundation/avaudioplayer)
