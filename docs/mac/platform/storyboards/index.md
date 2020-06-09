---
title: Introdução aos storyboards no Xamarin. Mac
description: Este artigo fornece uma introdução ao trabalho com storyboards em um aplicativo Xamarin. Mac. Ele aborda a criação e manutenção da interface do usuário do aplicativo usando storyboards e o Interface Builder do Xcode.
ms.prod: xamarin
ms.assetid: F37BA503-0B25-489F-80A8-58C493291A55
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 395ac62cd4db62c02863e352ed5643958348dcff
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84574384"
---
# <a name="introduction-to-storyboards-in-xamarinmac"></a>Introdução aos storyboards no Xamarin. Mac

_Este artigo fornece uma introdução ao trabalho com storyboards em um aplicativo Xamarin. Mac. Ele aborda a criação e a manutenção da interface do usuário do aplicativo usando storyboards e Interface Builder do Xcode._

Os storyboards permitem desenvolver uma interface do usuário para seu aplicativo Xamarin. Mac que não inclui apenas as definições e controles de janela, mas também contém os links entre janelas diferentes (via continuações) e Estados de exibição.

[![](images/intro01.png "A sample UI in Xcode")](images/intro01.png#lightbox)

Este artigo fornecerá uma introdução ao uso de storyboards para definir uma interface do usuário do aplicativo Xamarin. Mac.

<a name="What-are-Storyboards"></a>

## <a name="what-are-storyboards"></a>O que são storyboards?

Usando storyboards, toda a interface do usuário de um aplicativo Xamarin. Mac pode ser definida em um único local com toda a navegação entre seus elementos individuais e interfaces de usuário. Os storyboards para Xamarin. Mac funcionam de maneira muito semelhante aos storyboards para Xamarin. iOS. No entanto, eles contêm um conjunto diferente de _tipos transição_ por causa dos diferentes idiomas da interface.

<a name="Working-with-Scenes"></a>

### <a name="working-with-scenes"></a>Trabalhando com cenas

Como mencionado acima, um storyboard define toda a interface do usuário para um determinado aplicativo dividido em uma visão geral funcional de seus _controladores de exibição_. No Interface Builder do Xcode, cada um desses controladores reside em sua própria _cena_.

[![](images/intro02.png "An example view controller")](images/intro02.png#lightbox)

Cada cena representa um determinado par de controlador de exibição e exibição com um conjunto de linhas (chamado continuações) que conectam cada cena na interface do usuário, mostrando assim suas relações. Alguns continuações definem como um controlador de exibição contém uma ou mais exibições filho ou controladores de exibição. Outros continuações, definem transições entre o controlador de exibição (como exibir um popover ou uma caixa de diálogo). 

[![](images/intro03.png "A sample segue")](images/intro03.png#lightbox)

A coisa mais importante a observar é que cada transição representa o fluxo de alguma forma de dados entre o elemento fornecido da interface do usuário do aplicativo.

<a name="Working-with-View-Controllers"></a>

### <a name="working-with-view-controllers"></a>Trabalhando com controladores de exibição

Os controladores de exibição definem as relações entre uma determinada exibição de informações em um aplicativo Mac e o modelo de dados que fornece essas informações. Cada cena de nível superior no storyboard representa um controlador de exibição no código do aplicativo Xamarin. Mac.

[![](images/intro04.png "An example slips view controller")](images/intro04.png#lightbox)

Dessa forma, cada controlador de exibição é um emparelhamento reutilizável e independente da representação visual da informação (exibição) e da lógica para apresentar e controlar essas informações.

Em uma determinada cena, você pode fazer todas as coisas que normalmente teriam sido tratadas por `.xib` arquivos individuais: 

- Coloque subexibições e controles (como botões e caixas de texto).
- Definir posições de elemento e restrições de layout automático.
- Ações de conexão e saídas para expor elementos de interface do usuário ao código.

<a name="Working-with-Segues"></a>

### <a name="working-with-segues"></a>Trabalhando com continuações

Como mencionado acima, o continuações fornece as relações entre todos os bastidores que definem a interface do usuário do aplicativo. Se você estiver familiarizado com o trabalho em storyboards no iOS, saberá que continuações para iOS geralmente definem transições entre exibições de tela inteira. Isso difere do macOS, quando continuações geralmente define "contenção" (em que uma cena é o filho de uma cena pai).

No macOS, a maioria dos aplicativos tendem a agrupar suas exibições na mesma janela usando elementos de interface do usuário, como exibições divididas e guias. Ao contrário do iOS, em que as exibições precisam ser transferidas e desativadas na tela, devido ao espaço de exibição físico limitado.

Considerando o as tendências da macOS em relação à contenção, há situações em que as _continuações de apresentação_ são usadas, como janelas restritas, exibições de planilha e popovers.

Ao usar a apresentação continuações, você pode substituir o `PrepareForSegue` método do controlador de exibição pai para que a apresentação seja inicializada e as variáveis e forneça todos os dados para o controlador de exibição que está sendo apresentado.

<a name="Design-and-Run-Times"></a>

### <a name="design-and-run-times"></a>Tempos de design e de execução

Em tempo de design (ao definir o layout da interface do usuário no Interface Builder do Xcode), cada elemento da interface do usuário do aplicativo é dividido nos itens constituintes:

- **Cenas** – que são compostas por:
  - **Exibir controlador** – que definem as relações entre exibições e os dados que dão suporte a eles.
  - **Exibições e subexibições** – os elementos reais que compõem a interface do usuário.
  - **Continuações de contenção** – que definem as relações pai-filho entre cenas.
- **Continuações de apresentação** -que define modos de apresentação individuais. 

Ao definir cada elemento dessa forma, ele permite o carregamento lento de cada elemento apenas conforme necessário durante o tempo de execução. No macOS, todo o processo foi projetado para permitir que o desenvolvedor criasse interfaces de usuário complexas e flexíveis que exigem um mínimo de suporte de código para fazer com que funcionem, tudo isso enquanto fosse eficiente com os recursos do sistema como possível.

<a name="Storyboard-Quick-Start"></a>

## <a name="storyboard-quick-start"></a>Início Rápido de storyboard

No guia de [início rápido do storyboard](~/mac/platform/storyboards/quickstart.md) , criaremos um aplicativo Xamarin. Mac simples que apresenta os principais conceitos de trabalhar com storyboards para criar uma interface do usuário. O aplicativo de exemplo consistirá em uma exibição distribuídos contendo uma _área de conteúdo_ e uma área de _Inspetor_ e apresentará uma janela de diálogo de preferências simples. Vamos usar continuações para reunir todos os elementos da interface do usuário.

<a name="Working-with-Storyboards"></a>

## <a name="working-with-storyboards"></a>Trabalhando com Storyboards

Esta seção aborda os detalhes detalhados de [como trabalhar com storyboards](~/mac/platform/storyboards/indepth.md) em um aplicativo Xamarin. Mac. Vamos dar uma olhada aprofundada nos bastidores e como eles são compostos de modos de exibição e de exibição. Em seguida, vamos dar uma olhada em como os bastidores estão vinculados com continuações. Por fim, vamos dar uma olhada no trabalho com tipos transição personalizados. 

<a name="Complex-Storyboard-Example"></a>

## <a name="complex-storyboard-example"></a>Exemplo de storyboard complexo

Para obter um exemplo de um exemplo complexo de como trabalhar com storyboards em um aplicativo Xamarin. Mac, consulte o [aplicativo de exemplo SourceWriter](https://docs.microsoft.com/samples/xamarin/mac-samples/sourcewriter). SourceWriter é um editor de código-fonte simples que dá suporte para a conclusão de código e realce de sintaxe simples.

O código do SourceWriter foi totalmente comentado e, quando disponível, foram fornecidos links de tecnologias-chave ou métodos para informações relevantes na Documentação de Guias do Xamarin.Mac.

<a name="Summary"></a>

## <a name="summary"></a>Resumo

Este artigo deu uma olhada rápida no trabalho com storyboards em um aplicativo Xamarin. Mac. Vimos como criar um novo aplicativo usando storyboards e como definir uma interface do usuário. Também vimos como navegar entre diferentes janelas e Estados de exibição usando o continuações.

## <a name="related-links"></a>Links relacionados

- [Hello, Mac (exemplo)](https://docs.microsoft.com/samples/xamarin/mac-samples/hello-mac)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Trabalhando com o Windows](~/mac/user-interface/window.md)
- [Diretrizes de interface humana do OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Introdução ao Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
