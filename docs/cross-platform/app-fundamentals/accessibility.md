---
title: Acessibilidade em aplicativos Xamarin
description: Este documento fornece várias dicas para a criação de aplicativos acessíveis. Por exemplo, ele inclui recomendações sobre fontes grandes, alto contraste, as interfaces autodescritivas e muito mais.
ms.prod: xamarin
ms.assetid: E587F0CF-7C1D-41F8-B5A8-DA3E738EDA81
author: asb3993
ms.author: amburns
ms.date: 03/22/2017
ms.openlocfilehash: 0ec264e0f3d381fdac46c79dd479da2bc768954f
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61282183"
---
# <a name="accessibility-in-xamarin-apps"></a>Acessibilidade em aplicativos Xamarin

_Certifique-se de que seus aplicativos são utilizáveis pelo público mais amplo possível_

Acessibilidade se refere ao conceito de leitura de tela (texto em fala), Criando interfaces do usuário de aplicativo que funcionam bem os recursos de assistência de entrada e de exibição do sistema operacional, como o tipo grande, alto contraste, ampliar, indicações de comentários hápticos ou visual, e métodos alternativos de entrada.

Plataformas de desktops e móveis como iOS, Android e Windows fornecem criados em APIs que ajudam os desenvolvedores a criar aplicativos acessíveis, tais como [Google TalkBack](https://play.google.com/store/apps/details?id=com.google.android.marvin.talkback) e [VoiceOver da Apple](http://www.apple.com/accessibility/ios/voiceover/).

## <a name="platform-specific-apis"></a>APIs específicas da plataforma

Para implementar as diretrizes neste documento, use as APIs fornecidas pelo cada plataforma:

- [**Acessibilidade do Android**](~/android/app-fundamentals/accessibility.md)
- [**Acessibilidade do iOS**](~/ios/app-fundamentals/accessibility.md)
- [**Acessibilidade do sistema operacional X**](~/mac/app-fundamentals/accessibility.md)
- [**Xamarin.Forms**](~/xamarin-forms/app-fundamentals/accessibility/index.md)

<a name="checklist" />

## <a name="accessibility-checklist"></a>Lista de verificação de acessibilidade

Siga estas dicas para garantir que seus aplicativos estejam acessíveis para o maior público possível. Confira a [Android acessibilidade teste lista de verificação](https://developer.android.com/training/accessibility/testing.html) e [página de acessibilidade da Apple](http://www.apple.com/accessibility/) para obter informações adicionais.

### <a name="support-large-fonts-and-high-contrast"></a>Dar suporte a fontes grandes e alto contraste

Evite dimensões de controle de codificar e, em vez disso, prefira layouts que podem ser redimensionada para acomodar os maiores tamanhos de fonte.
Teste os esquemas de cores no modo de alto contraste para garantir que eles possam ser lidos.

### <a name="make-the-user-interface-self-describing"></a>Tornar o usuário interface autodescritivos

Marca todos os elementos da interface do usuário com o texto descritivo e dicas que são compatíveis com a APIs de leitura em cada plataforma de tela.

### <a name="ensure-that-images-and-icons-have-an-alternate-text-description"></a>Certifique-se de que as imagens e ícones tem uma descrição de texto alternativo

Imagens e ícones que fazem parte da interface do usuário do aplicativo (como botões ou indicadores de status, por exemplo) devem ser marcadas com uma descrição acessível.

### <a name="design-the-visual-tree-with-accessible-navigation-in-mind"></a>A árvore visual com navegação acessível em mente de design

Use controles de layout apropriado ou APIs para que a navegação entre controles usando métodos alternativos de entrada segue o mesmo fluxo lógico como usando a tela sensível ao toque.

Exclua elementos desnecessários de leitores de tela (imagens decorativas ou rótulos para os campos que já estão acessíveis, por exemplo).

### <a name="dont-rely-on-audio-or-color-cues-alone"></a>Não confie em indicações de áudio ou cor sozinhas

Evite situações em que a única indicação de progresso, conclusão ou algum outro estado é um som ou alteração de cor. Tanto a interface do usuário para incluir indicações visuais (com som e cor de reforço somente), ou adicionar indicadores de acessibilidade específicos de design.

Ao escolher as cores, tente evitar uma paleta que é difícil distinguir para usuários com daltonismo.

### <a name="captioning-for-video-text-for-audio"></a>Legendagem oculta para vídeo, texto para áudio

Forneça legendas para o conteúdo de vídeo e um script legível para conteúdo de áudio. Também é útil fornecer controles que se ajustam a velocidade de conteúdo de áudio ou vídeo e verifique se esse volume e botões Reproduzir/Pausar são fáceis de localizar e usar.

### <a name="localize"></a>Localize

Descrições de acessibilidade podem (e deve) ser localizadas em que o aplicativo dá suporte a vários idiomas.



## <a name="related-links"></a>Links relacionados

- [Acessibilidade do Android](~/android/app-fundamentals/accessibility.md)
- [Acessibilidade do iOS](~/ios/app-fundamentals/accessibility.md)
- [Acessibilidade do sistema operacional X](~/mac/app-fundamentals/accessibility.md)
- [Acessibilidade do xamarin. Forms](~/xamarin-forms/app-fundamentals/accessibility/index.md)
