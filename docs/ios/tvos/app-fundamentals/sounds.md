---
title: Reprodução de som em tvOS com AVAudioPlayer no Xamarin
description: Este artigo mostra como usar uma classe auxiliar para controlar a reprodução de som usando um AVAudioPlayer em um aplicativo Xamarin. iOS.
ms.prod: xamarin
ms.assetid: E0305572-DC64-48BB-BD97-0A5096E6CA04
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/16/2017
ms.openlocfilehash: b34c769eaa3aef5bf47a9bfa891859289b195f03
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70283778"
---
# <a name="playing-sound-in-tvos-with-avaudioplayer-in-xamarin"></a>Reprodução de som em tvOS com AVAudioPlayer no Xamarin

## <a name="about-the-avaudioplayer"></a>Sobre o AVAudioPlayer

O `AVAudioPlayer` é usado para reproduzir dados de áudio de uma memória ou de um arquivo. A Apple recomenda usar essa classe para reproduzir áudio em seu aplicativo, a menos que você esteja fazendo streaming de rede ou exija e/s de áudio de baixa latência.

Você pode usar o `AVAudioPlayer` para fazer o seguinte:

- Jogue sons de qualquer duração com loop opcional.
- Jogue vários sons ao mesmo tempo com a sincronização opcional.
- Volume de controle, taxa de reprodução e posicionamento de estéreo para cada som em execução.
- Suporte a recursos como avançar ou retroceder.
- Obter dados de medição de nível de reprodução.

`AVAudioPlayer`dá suporte a sons em qualquer formato de áudio fornecido pelo Ios, tvOS e os `.aif`X `.wav` , `.mp3`como, ou.

## <a name="playing-sounds-in-tvos"></a>Tocando sons em tvOS

Como o tvOS dá suporte às mesmas classes de caixa de ferramentas de áudio que o iOS, consulte nosso [som de reprodução](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/sound/avaudioplayer) de Ios com a documentação do AVAudioPlayer para obter os detalhes completos da reprodução de áudio em um aplicativo Xamarin. tvOS.



## <a name="related-links"></a>Links relacionados

- [Referência de AVAudioPlayer](https://developer.apple.com/library/ios/documentation/AVFoundation/Reference/AVAudioPlayerClassReference/)
