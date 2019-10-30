---
title: Reprodução de som com AVAudioPlayer no Xamarin. Mac
description: Este documento descreve como reproduzir som com AVAudioPlayer em um aplicativo Xamarin. Mac. Ele aborda o AVAudioPlayer em um alto nível e links para outras documentações que o exploram mais completamente.
ms.prod: xamarin
ms.assetid: 4A683A94-F75D-4EAF-8497-E9443653250B
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 10/19/2016
ms.openlocfilehash: 18043a88a129d48a1cad3b9ee15b6989d50ad126
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030052"
---
# <a name="playing-sound-with-avaudioplayer-in-xamarinmac"></a>Reprodução de som com AVAudioPlayer no Xamarin. Mac

## <a name="about-the-avaudioplayer"></a>Sobre o AVAudioPlayer

A classe `AVAudioPlayer` é usada para reproduzir dados de áudio de memória ou de um arquivo. A Apple recomenda usar essa classe para reproduzir áudio em seu aplicativo, a menos que você esteja fazendo streaming de rede ou exija e/s de áudio de baixa latência.

Você pode usar a classe `AVAudioPlayer` para fazer o seguinte:

- Jogue sons de qualquer duração com loop opcional.
- Jogue vários sons ao mesmo tempo com a sincronização opcional.
- Volume de controle, taxa de reprodução e posicionamento de estéreo para cada som em execução.
- Suporte a recursos como avançar ou retroceder.
- Obter dados de medição de nível de reprodução.

o `AVAudioPlayer` dá suporte a sons em qualquer formato de áudio fornecido pelo iOS, tvOS e macOS, como. aif,. wav ou. mp3.

## <a name="playing-sounds-in-macos"></a>Tocando sons no macOS

Como o macOS dá suporte às mesmas classes de caixa de ferramentas de áudio que o iOS, consulte nosso [som de reprodução](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/sound/avaudioplayer) de Ios com a documentação do AVAudioPlayer para obter os detalhes completos da reprodução de áudio em um aplicativo Xamarin. Mac.

## <a name="related-links"></a>Links relacionados

- [Referência de AVAudioPlayer](https://developer.apple.com/documentation/avfoundation/avaudioplayer)
