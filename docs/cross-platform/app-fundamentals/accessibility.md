---
title: Acessibilidade em aplicativos Xamarin
description: Este documento fornece várias dicas para a criação de aplicativos acessíveis. Por exemplo, ele inclui recomendações sobre fontes grandes, alto contraste, interfaces autodescritivas e muito mais.
ms.prod: xamarin
ms.assetid: E587F0CF-7C1D-41F8-B5A8-DA3E738EDA81
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: df042521d4e9852d6e23c2bbdf24484f9068250d
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84571253"
---
# <a name="accessibility-in-xamarin-apps"></a>Acessibilidade em aplicativos Xamarin

_Certifique-se de que seus aplicativos sejam utilizáveis pelo público mais amplo possível_

Acessibilidade refere-se ao conceito de criação de interfaces do usuário do aplicativo que funcionam bem com recursos de entrada e de exibição do sistema operacional, como tipo grande, alto contraste, zoom, leitura de tela (conversão de texto em fala), indicações de comentários Visual ou Haptic e métodos de entrada alternativos.

As plataformas desktop e móvel, como iOS, Android e Windows, fornecem APIs internas que ajudam os desenvolvedores a criarem aplicativos acessíveis, como o [Google Talkback](https://play.google.com/store/apps/details?id=com.google.android.marvin.talkback) e o [VoiceOver da Apple](https://www.apple.com/accessibility/ios/voiceover/).

## <a name="platform-specific-apis"></a>APIs específicas da plataforma

Para implementar as diretrizes deste documento, use as APIs fornecidas por cada plataforma:

- [**Acessibilidade do Android**](~/android/app-fundamentals/accessibility.md)
- [**Acessibilidade do iOS**](~/ios/app-fundamentals/accessibility.md)
- [**Acessibilidade do OS X**](~/mac/app-fundamentals/accessibility.md)
- [**Xamarin. Forms**](~/xamarin-forms/app-fundamentals/accessibility/index.md)

<a name="checklist"></a>

## <a name="accessibility-checklist"></a>Lista de verificação de acessibilidade

Siga estas dicas para garantir que seus aplicativos estejam acessíveis para o público mais amplo possível. Confira a [lista de verificação de testes de acessibilidade do Android](https://developer.android.com/training/accessibility/testing.html) e a página de [acessibilidade da Apple](https://www.apple.com/accessibility/) para obter informações adicionais.

### <a name="support-large-fonts-and-high-contrast"></a>Dar suporte a grandes fontes e alto contraste

Evite codificar dimensões de controle e, em vez disso, prefira layouts que podem ser redimensionados para acomodar tamanhos de fonte maiores.
Testar esquemas de cores em modo de alto contraste para garantir que eles sejam legíveis.

### <a name="make-the-user-interface-self-describing"></a>Tornar a interface do usuário autodescreveda

Marque todos os elementos da interface do usuário com texto descritivo e dicas que são compatíveis com as APIs de leitura de tela em cada plataforma.

### <a name="ensure-that-images-and-icons-have-an-alternate-text-description"></a>Verifique se as imagens e os ícones têm uma descrição de texto alternativo

Imagens e ícones que fazem parte da interface do usuário do aplicativo (como botões ou indicadores de status, por exemplo) devem ser marcados com uma descrição acessível.

### <a name="design-the-visual-tree-with-accessible-navigation-in-mind"></a>Projete a árvore visual com a navegação acessível em mente

Use as APIs ou controles de layout apropriados para que a navegação entre controles usando métodos de entrada alternativos siga o mesmo fluxo lógico que o uso da tela sensível ao toque.

Exclua elementos desnecessários dos leitores de tela (imagens decorativas ou rótulos para campos que já estão acessíveis, por exemplo).

### <a name="dont-rely-on-audio-or-color-cues-alone"></a>Não confie apenas em indicações de áudio ou cor

Evite situações em que a única indicação de progresso, conclusão ou outro Estado é um som ou uma alteração de cor. Crie a interface do usuário para incluir indicações visuais claras (com som e cor somente para reforço) ou adicione indicadores de acessibilidade específicos.

Ao escolher as cores, tente evitar uma paleta que seja difícil de distinguir para usuários com cegueira de cor.

### <a name="captioning-for-video-text-for-audio"></a>Legenda para vídeo, texto para áudio

Forneça legendas para conteúdo de vídeo e um script legível para conteúdo de áudio. Também é útil fornecer controles que ajustem a velocidade do conteúdo de áudio ou vídeo e certifique-se de que os botões volume e reproduzir/pausar sejam fáceis de localizar e usar.

### <a name="localize"></a>Localize

Descrições de acessibilidade podem (e devem) ser localizadas onde o aplicativo dá suporte a vários idiomas.

## <a name="related-links"></a>Links relacionados

- [Acessibilidade do Android](~/android/app-fundamentals/accessibility.md)
- [Acessibilidade do iOS](~/ios/app-fundamentals/accessibility.md)
- [Acessibilidade do OS X](~/mac/app-fundamentals/accessibility.md)
- [Acessibilidade do Xamarin.Forms](~/xamarin-forms/app-fundamentals/accessibility/index.md)
