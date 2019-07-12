---
title: Introdução ao Storyboards no xamarin. Mac
description: Este artigo fornece uma introdução ao trabalhar com Storyboards em um aplicativo xamarin. Mac. Ele aborda a criação e manutenção da interface do usuário do aplicativo usando storyboards e o Interface Builder do Xcode.
ms.prod: xamarin
ms.assetid: F37BA503-0B25-489F-80A8-58C493291A55
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 8a5a2f87c16a5dd040cefb2fbc615b01431ebcf5
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832298"
---
# <a name="introduction-to-storyboards-in-xamarinmac"></a>Introdução ao Storyboards no xamarin. Mac

_Este artigo fornece uma introdução ao trabalhar com Storyboards em um aplicativo xamarin. Mac. Ele aborda a criação e manutenção da interface do usuário do aplicativo usando storyboards e Interface Builder do Xcode._

Storyboards permitem que você desenvolva uma Interface do usuário para seu aplicativo xamarin. MAC que inclui não apenas as definições de janela e os controles, mas também contém os links entre janelas diferentes (por meio de segues) e estados de exibição.

[![](images/intro01.png "Um exemplo de interface do usuário no Xcode")](images/intro01.png#lightbox)

Este artigo fornece uma introdução ao uso de Storyboards para definir a Interface do usuário de um aplicativo xamarin. Mac.

<a name="What-are-Storyboards" />

## <a name="what-are-storyboards"></a>Quais são os Storyboards?

Ao usar Storyboards, todos da interface do usuário de um aplicativo xamarin. Mac podem ser definidos em um único local com toda a navegação entre seus elementos individuais e interfaces do usuário. Storyboards para xamarin. Mac, funciona de maneira muito semelhante aos Storyboards para xamarin. IOS. No entanto, eles contêm um conjunto diferente de _Segue tipos_ devido às linguagens de interface diferente.

<a name="Working-with-Scenes" />

### <a name="working-with-scenes"></a>Trabalhando com cenas

Como mencionado acima, um Storyboard define todos a interface do usuário para um determinado aplicativo dividido em uma visão funcional de seu _controladores de exibição_. No Interface Builder do Xcode, cada um desses controladores reside em seu próprio _cena_.

[![](images/intro02.png "Um controlador de exibição de exemplo")](images/intro02.png#lightbox)

Cada cena representa um determinado modo de exibição e par de controlador de exibição com um conjunto de linhas (chamado de transições) que se conectam cada cena na interface do usuário, mostrando assim suas relações. Alguns transições definem como um controlador de exibição contém um ou mais modos de exibição filho ou controladores de exibição. Outras transições, definir transições entre o controlador de exibição (como exibir uma caixa de diálogo ou pop-over). 

[![](images/intro03.png "Segue um exemplo")](images/intro03.png#lightbox)

A coisa mais importante a observar é que cada Segue representa o fluxo de alguma forma de dados entre o determinado elemento de interface do usuário do aplicativo.

<a name="Working-with-View-Controllers" />

### <a name="working-with-view-controllers"></a>Trabalhar com controladores de exibição

Controladores de exibição definem as relações entre um determinado modo de exibição de informações dentro de um aplicativo do Mac e o modelo de dados que fornece essas informações. Cada cena de nível superior no Storyboard representa um controlador de exibição no código do aplicativo xamarin. Mac.

[![](images/intro04.png "Um exemplo guias de controlador de exibição")](images/intro04.png#lightbox)

Dessa forma, cada controlador de exibição é um emparelhamento reutilizável, independente da representação visual das informações (exibição) e a lógica para apresentar e controlar essas informações.

Dentro de uma cena determinada, você pode fazer todas as coisas que normalmente seriam terem sido manipuladas por indivíduo `.xib` arquivos: 

- Local subviews e controles (como botões e caixas de texto).
- Defina posições de elemento e restrições do layout automático.
- Wire-up ações e as saídas para expor elementos de interface do usuário ao código.

<a name="Working-with-Segues" />

### <a name="working-with-segues"></a>Trabalhar com Segues

Como mencionado acima, transições fornecem as relações entre todas as cenas que definem a interface de usuário do seu aplicativo. Se você estiver familiarizado com trabalhando em Storyboards no iOS, você sabe que Segues para iOS normalmente define transições entre modos de exibição de tela inteira. Isso difere do macOS, quando transições geralmente definem "contenção" (onde uma cena é o filho de uma cena pai).

No macOS, a maioria dos aplicativos tendem a agrupar seus modos de exibição juntos na mesma janela usando elementos de interface do usuário, como modos de exibição de divisão e guias. Ao contrário do iOS, onde as exibições precisam ser transferidas e desativar a tela, devido a física limitada exiba espaço.

Considerando as tendências do macOS em direção a contenção, há situações em que _apresentação Segues_ forem usados, como Windows Modal, modos de exibição da folha e Popovers.

Ao usar Segues de apresentação, você pode substituir o `PrepareForSegue` método do controlador de exibição de pai para a apresentação para inicializar e variáveis e forneça todos os dados para o controlador de exibição que está sendo apresentado.

<a name="Design-and-Run-Times" />

### <a name="design-and-run-times"></a>Design e tempos de execução

Em tempo de Design (ao layout de interface do usuário no Interface Builder do Xcode), cada elemento da interface do usuário do aplicativo é dividido em itens constituintes de TI:

- **Cenas** - que são compostos de:
    - **Exibir controlador** -que definem as relações entre os modos de exibição e os dados que dão suporte a eles.
    - **Exibições e subexibições** -os elementos reais que compõem a interface do usuário.
    - **Transições de confinamento** -que definem as relações pai-filho entre as cenas.
- **Transições de apresentação** -que definem os modos de apresentação individuais. 

Com a definição de cada elemento dessa forma, ele permite o carregamento lento de cada elemento conforme o necessário durante o tempo de execução. No macOS, todo o processo foi projetado para permitir que o desenvolvedor criar Interfaces do usuário complexa e flexível que exigem um mínimo de fazer o código para torná-los funcionar, ao mesmo tempo, sendo o mais eficiente com recursos de sistema quanto possível.

<a name="Storyboard-Quick-Start" />

## <a name="storyboard-quick-start"></a>Início rápido do storyboard

No [início rápido do Storyboard](~/mac/platform/storyboards/quickstart.md) guia, vamos criar um aplicativo xamarin. Mac simples que apresenta os principais conceitos de trabalhar com storyboards para criar uma Interface do usuário. O aplicativo de exemplo consistirá em uma exibição distribuídos que contém um _área de conteúdo_ e uma _Inspetor área_ e ele apresentará uma janela de caixa de diálogo Preferências simples. Usaremos transições para reunir todos os elementos de Interface do usuário.

<a name="Working-with-Storyboards" />

## <a name="working-with-storyboards"></a>Trabalhando com Storyboards

Esta seção aborda os detalhes do [trabalhar com Storyboards](~/mac/platform/storyboards/indepth.md) em um aplicativo xamarin. Mac. Vamos dar uma visão detalhada de cenas e como eles são compostos de controladores de exibição e modo de exibição. Em seguida, vamos dar uma olhada em como as cenas são reunidas com transições. Por fim, vamos dar uma olhada em como trabalhar com tipos personalizados de Segue. 

<a name="Complex-Storyboard-Example" />

## <a name="complex-storyboard-example"></a>Exemplo de Storyboard complexos

Para obter um exemplo de um exemplo complexo de como trabalhar com Storyboards em um aplicativo xamarin. Mac, consulte a [aplicativo de exemplo SourceWriter](https://developer.xamarin.com/samples/mac/SourceWriter/). SourceWriter é um editor de código-fonte simples que dá suporte para a conclusão de código e realce de sintaxe simples.

O código do SourceWriter foi totalmente comentado e, quando disponível, foram fornecidos links de tecnologias-chave ou métodos para informações relevantes na Documentação de Guias do Xamarin.Mac.

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo apresentou uma visão geral de trabalhar com Storyboards em um aplicativo xamarin. Mac. Vimos como criar um novo aplicativo usando storyboards e como definir uma interface do usuário. Também vimos como navegar entre janelas diferentes e transições de estados de exibição usando o.


## <a name="related-links"></a>Links relacionados

- [Hello, Mac (amostra)](https://developer.xamarin.com/samples/mac/Hello_Mac/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Trabalhando com o Windows](~/mac/user-interface/window.md)
- [Diretrizes de interface humana do OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Introdução ao Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
