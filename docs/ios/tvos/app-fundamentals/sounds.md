---
title: Reproduzir som com AVAudioPlayer
description: "Este artigo mostra como usar uma classe auxiliar para controlar a reprodução do som usando um AVAudioPlayer."
ms.topic: article
ms.prod: xamarin
ms.assetid: E0305572-DC64-48BB-BD97-0A5096E6CA04
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 59b91c22060fbbc0d1855db59f234048d4673110
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="playing-sound-with-avaudioplayer"></a>Reproduzir som com AVAudioPlayer

_Este artigo mostra como usar uma classe auxiliar para controlar a reprodução do som usando um AVAudioPlayer._

## <a name="about-the-avaudioplayer"></a>Sobre o AVAudioPlayer

O `AVAudioPlayer` é usado para dados de áudio de reprodução de memória ou um arquivo. Apple recomenda usar essa classe para executar o áudio em seu aplicativo, a menos que você está fazendo o streaming de rede ou requerem e/s de áudio de baixa latência.

Você pode usar o `AVAudioPlayer` para fazer o seguinte:

- Tocar sons de qualquer duração de repetição opcional.
- Tocar sons vários ao mesmo tempo com a sincronização opcional.
- Controle de volume, velocidade de reprodução e posicionamento estéreo para cada reprodução de sons.
- Suporte a recursos como Avançar ou retroceder.
- Obter dados de medição de nível de reprodução.

`AVAudioPlayer` oferece suporte a sons em qualquer formato de áudio fornecido por tvOS, iOS e OS X como `.aif`, `.wav` ou `.mp3`.

## <a name="playing-sounds-in-tvos"></a>Reprodução de sons em tvOS

Como tvOS oferece suporte as mesmas classes de caixa de ferramentas de áudio como iOS, consulte nosso iOS [tocando som com AVAudioPlayer](http://developer.xamarin.com/recipes/ios/media/sound/avaudioplayer/) documentação para obter detalhes completos de áudio em execução em um aplicativo Xamarin.tvOS.



## <a name="related-links"></a>Links relacionados

- [Referência de AVAudioPlayer](https://developer.apple.com/library/ios/documentation/AVFoundation/Reference/AVAudioPlayerClassReference/)
