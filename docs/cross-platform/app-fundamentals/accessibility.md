---
title: Acessibilidade no aplicativos Xamarin
description: Este documento fornece diversas dicas para a criação de aplicativos acessíveis. Por exemplo, ele inclui recomendações sobre fontes grandes, alto contraste, interfaces autodescritivos e muito mais.
ms.prod: xamarin
ms.assetid: E587F0CF-7C1D-41F8-B5A8-DA3E738EDA81
author: asb3993
ms.author: amburns
ms.date: 03/22/2017
ms.openlocfilehash: 97cd3655ac47a017d9590e1890b93d74f10a9c34
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34780298"
---
# <a name="accessibility-in-xamarin-apps"></a>Acessibilidade no aplicativos Xamarin

_Certifique-se de que seus aplicativos são úteis para o público mais amplo possível_

Acessibilidade refere-se ao conceito de criação de interfaces de usuário do aplicativo que funcionam bem os recursos de assistência de entrada e de exibição do sistema operacional, como tipo grande, alto contraste, aplicar zoom, leitura de tela (texto em fala), indicações de visual ou haptic comentários e métodos alternativos de entrada.

Plataformas de desktop e móveis como iOS, Android e Windows fornecem criados nas APIs que ajudam os desenvolvedores a criar aplicativos acessíveis, como [o Google TalkBack](https://play.google.com/store/apps/details?id=com.google.android.marvin.talkback) e [VoiceOver da Apple](http://www.apple.com/accessibility/ios/voiceover/).

## <a name="platform-specific-apis"></a>APIs específicas de plataforma

Para implementar as diretrizes neste documento, use as APIs fornecidas pela cada plataforma:

- [**Acessibilidade do Android**](~/android/app-fundamentals/accessibility.md)
- [**Acessibilidade do iOS**](~/ios/app-fundamentals/accessibility.md)
- [**Acessibilidade de X do sistema operacional**](~/mac/app-fundamentals/accessibility.md)
- [**Xamarin.Forms**](~/xamarin-forms/app-fundamentals/accessibility/index.md)

<a name="checklist" />

## <a name="accessibility-checklist"></a>Lista de verificação de acessibilidade

Siga estas dicas para garantir que seus aplicativos estejam acessíveis para o público-alvo mais amplo possível. Check-out de [Android acessibilidade teste lista de verificação](http://developer.android.com/training/accessibility/testing.html) e [página de acessibilidade da Apple](http://www.apple.com/accessibility/) para obter informações adicionais.

### <a name="support-large-fonts-and-high-contrast"></a>Suporte a fontes grandes e alto contraste

Evite dimensões de controle de codificar e, em vez disso, preferir layouts que podem ser redimensionada para acomodar os maiores tamanhos de fonte.
Teste os esquemas de cores no modo de alto contraste para garantir que eles são legíveis.

### <a name="make-the-user-interface-self-describing"></a>Verifique o usuário interface autodescritivos

Marca todos os elementos da interface do usuário com o texto descritivo e dicas que são compatíveis com a tela de APIs de leitura em cada plataforma.

### <a name="ensure-that-images-and-icons-have-an-alternate-text-description"></a>Certifique-se de que as imagens e ícones tem uma descrição de texto alternativo

Imagens e ícones que fazem parte da interface do usuário do aplicativo (como botões ou indicadores de status, por exemplo) devem ser marcadas com uma descrição acessível.

### <a name="design-the-visual-tree-with-accessible-navigation-in-mind"></a>Criar a árvore visual com navegação acessível em mente

Use controles de layout apropriado ou APIs para que a navegação entre controles usando métodos alternativos de entrada segue o mesmo fluxo lógico como usando a tela sensível ao toque.

Exclua elementos desnecessários de leitores de tela (imagens decorativas ou rótulos para os campos que já estão acessíveis, por exemplo).

### <a name="dont-rely-on-audio-or-color-cues-alone"></a>Não confie no indicações de áudio ou cor sozinha

Evite situações em que a única indicação de progresso, conclusão ou algum outro estado é um som ou alteração de cor. O design de interface do usuário para incluir desmarque visuais (com som e cores para Reforce seu somente), ou adicionar indicadores de acessibilidade específico.

Ao escolher as cores, tente evitar uma paleta que é difícil distinguir para usuários com deficiência visual de cor.

### <a name="captioning-for-video-text-for-audio"></a>Legenda de texto de vídeo, áudio

Fornece legendas para conteúdo de vídeo e um script legível para conteúdo de áudio. Também é útil fornecer controles que se ajustam a velocidade de conteúdo de áudio ou vídeo e certifique-se de que o volume e executar/pausar botões são fáceis de localizar e usar.

### <a name="localize"></a>Localize

Descrições de acessibilidade pode (e deve) ser localizadas em que o aplicativo dá suporte a vários idiomas.



## <a name="related-links"></a>Links relacionados

- [Acessibilidade do Android](~/android/app-fundamentals/accessibility.md)
- [Acessibilidade do iOS](~/ios/app-fundamentals/accessibility.md)
- [Acessibilidade de X do sistema operacional](~/mac/app-fundamentals/accessibility.md)
- [Acessibilidade xamarin. Forms](~/xamarin-forms/app-fundamentals/accessibility/index.md)
