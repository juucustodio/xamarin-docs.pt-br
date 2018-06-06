---
title: Reproduzir som com AVAudioPlayer em Xamarin.Mac
description: Este documento descreve como reproduzir som com AVAudioPlayer em um aplicativo Xamarin.Mac. Ele discute AVAudioPlayer em um alto nível e links para documentação adicional que explora-la mais detalhadamente.
ms.prod: xamarin
ms.assetid: 4A683A94-F75D-4EAF-8497-E9443653250B
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 10/19/2016
ms.openlocfilehash: 9e5b9ec43189999f8a0aee29eb50221b494e2133
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34791849"
---
# <a name="playing-sound-with-avaudioplayer-in-xamarinmac"></a>Reproduzir som com AVAudioPlayer em Xamarin.Mac

## <a name="about-the-avaudioplayer"></a>Sobre o AVAudioPlayer

O `AVAudioPlayer` classe é usada para dados de áudio de reprodução de memória ou um arquivo. Apple recomenda usar essa classe para executar o áudio em seu aplicativo, a menos que você está fazendo o streaming de rede ou requerem e/s de áudio de baixa latência.

Você pode usar o `AVAudioPlayer` classe para fazer o seguinte:

- Tocar sons de qualquer duração de repetição opcional.
- Tocar sons vários ao mesmo tempo com a sincronização opcional.
- Controle de volume, velocidade de reprodução e posicionamento estéreo para cada reprodução de sons.
- Suporte a recursos como Avançar ou retroceder.
- Obter dados de medição de nível de reprodução.

`AVAudioPlayer` oferece suporte a sons em qualquer formato de áudio fornecido pelo iOS, tvOS e macOS como. AIF,. wav ou. mp3.

## <a name="playing-sounds-in-macos"></a>Reprodução de sons em macOS

Como macOS suporta as mesmas classes de caixa de ferramentas de áudio como iOS, consulte nosso iOS [tocando som com AVAudioPlayer](https://developer.xamarin.com/recipes/ios/media/sound/avaudioplayer/) documentação para obter detalhes completos de áudio em execução em um aplicativo Xamarin.Mac.

## <a name="related-links"></a>Links relacionados

- [Referência de AVAudioPlayer](https://developer.apple.com/documentation/avfoundation/avaudioplayer)
