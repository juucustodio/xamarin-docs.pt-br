---
title: Introdução ao Storyboards no Xamarin.Mac
description: Este artigo fornece uma introdução ao trabalho com Storyboards em um aplicativo Xamarin.Mac. Ele aborda a criação e manutenção da interface do usuário do aplicativo usando storyboards e o Interface Builder do Xcode.
ms.prod: xamarin
ms.assetid: F37BA503-0B25-489F-80A8-58C493291A55
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 027998d6aff8aba4e5621b1cde51a24e18821ff9
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34792659"
---
# <a name="introduction-to-storyboards-in-xamarinmac"></a>Introdução ao Storyboards no Xamarin.Mac

_Este artigo fornece uma introdução ao trabalho com Storyboards em um aplicativo Xamarin.Mac. Ele aborda a criação e manutenção da interface do usuário do aplicativo usando storyboards e construtor de Interface do Xcode._

Storyboards permitem que você desenvolva uma Interface do usuário para seu aplicativo Xamarin.Mac que inclui não apenas as definições de janela e controles, mas também contém links entre diferentes janelas (via segues) e exibir estados.

[![](images/intro01.png "Um exemplo da interface do usuário no Xcode")](images/intro01.png#lightbox)

Este artigo fornecem uma introdução ao uso de Storyboards para definir a Interface do usuário do aplicativo um Xamarin.Mac.

<a name="What-are-Storyboards" />

## <a name="what-are-storyboards"></a>Quais são os Storyboards?

Usando Storyboards, da interface do usuário de um aplicativo Xamarin.Mac podem ser definidos em um único local com toda a navegação entre seus elementos individuais e interfaces do usuário. Storyboards para Xamarin.Mac, funciona de maneira muito semelhante a Storyboards para xamarin. No entanto, eles contêm um conjunto diferente de _atender tipos_ devido os idiomas de interface diferente.

<a name="Working-with-Scenes" />

### <a name="working-with-scenes"></a>Trabalhando com o segundo plano

Como mencionado acima, um Storyboard define todos os da interface do usuário para um determinado aplicativo dividido em uma visão geral das funções de seu _exibir controladores_. No construtor de Interface do Xcode, cada um desses controladores reside em seu próprio _cena_.

[![](images/intro02.png "Um controlador de exibição de exemplo")](images/intro02.png#lightbox)

Cada cena representa um determinado modo de exibição e exibição controlador par com um conjunto de linhas (chamado Segues) que se conectam cada cena na interface de usuário, mostrando as relações. Alguns Segues definir como um controlador de exibição contém uma ou mais exibições filho ou exibir controladores. Outros Segues, definir transições entre o controlador de exibição (como exibir uma caixa de diálogo ou popover). 

[![](images/intro03.png "Segue um exemplo")](images/intro03.png#lightbox)

O mais importante a observar é que cada Segue representa o fluxo de alguma forma de dados entre o determinado elemento de interface de usuário do aplicativo.

<a name="Working-with-View-Controllers" />

### <a name="working-with-view-controllers"></a>Trabalhando com controladores de exibição

Exibir controladores definem as relações entre um determinado modo de exibição de informações dentro de um aplicativo do Mac e o modelo de dados que fornece essas informações. Cada cena de nível superior no Storyboard representa um controlador de exibição de código do aplicativo Xamarin.Mac.

[![](images/intro04.png "Um exemplo guias de controlador de exibição")](images/intro04.png#lightbox)

Dessa forma, cada controlador de exibição é um emparelhamento autônomas e reutilizável de representação visual da informação (exibição) e a lógica para apresentar e controlar essas informações.

Dentro de uma determinado cena, você pode fazer todas as coisas que normalmente seriam terem sido manipuladas por indivíduo `.xib` arquivos: 

 - Local subviews e controles (como botões e caixas de texto).
 - Defina as posições de elemento e restrições de layout automático.
 - Durante a transmissão ações e tomadas para expor elementos de interface do usuário ao código.

<a name="Working-with-Segues" />

### <a name="working-with-segues"></a>Trabalhando com Segues

Como mencionado acima, Segues fornecem as relações entre todos nos bastidores que definem a interface de usuário do aplicativo. Se você estiver familiarizado com trabalhando em Storyboards no iOS, você sabe que Segues de iOS geralmente definem as transições entre modos de exibição de tela inteira. Isso difere do macOS, quando Segues geralmente define "contenção" (onde uma cena é o filho de um pai cena).

Em macOS, a maioria dos aplicativos tendem a suas exibições juntos dentro da mesma janela usando elementos de interface do usuário, como modos de exibição de divisão e guias de grupo. Ao contrário do iOS, onde modos de exibição precisam ser transferido e desativar a tela, devido a físico limitado exibe espaço.

Considerando as tendências do macOS em direção à contenção, há situações em que _Segues apresentação_ forem usados, como janelas modais, modos de exibição de planilha e Popovers.

Ao usar Segues de apresentação, você pode substituir o `PrepareForSegue` método do controlador de exibição pai para a apresentação de inicialização e variáveis e fornecer os dados para o controlador de exibição que está sendo exibida.

<a name="Design-and-Run-Times" />

### <a name="design-and-run-times"></a>Design e tempos de execução

Em tempo de Design (ao layout de interface do usuário no construtor de Interface do Xcode), cada elemento da interface de usuário do aplicativo é dividido em seu constituintes itens:

- **Nos bastidores** - que são compostos de:
    - **Exibir controlador** -que definem as relações entre os modos de exibição e os dados que dão suporte a eles.
    - **Modos de exibição e sub-visualizações** -reais elementos que compõem a interface do usuário.
    - **Contenção Segues** -que definem as relações pai-filho nos bastidores.
- **Apresentação Segues** -que definem os modos de apresentação individuais. 

Definindo cada elemento dessa maneira, ela permite o carregamento lento de cada elemento somente conforme necessário durante a execução. Em macOS, todo o processo foi projetado para permitir que o desenvolvedor criar Interfaces de usuário complexas e flexível que exigem um mínimo de backup de código para torná-los a trabalhar, ao mesmo tempo, sendo mais eficiente com recursos do sistema quanto possível.

<a name="Storyboard-Quick-Start" />

## <a name="storyboard-quick-start"></a>Início rápido do storyboard

No [início rápido do Storyboard](~/mac/platform/storyboards/quickstart.md) guia, vamos criar um aplicativo simples de Xamarin.Mac que apresenta os conceitos fundamentais de como trabalhar com storyboards para criar uma Interface do usuário. O aplicativo de exemplo consiste em uma exibição distribuídos que contém um _área de conteúdo_ e um _Inspetor área_ e apresentará uma janela de diálogo de preferências simples. Usaremos Segues para reunir todos os elementos de Interface do usuário.

<a name="Working-with-Storyboards" />

## <a name="working-with-storyboards"></a>Trabalhando com Storyboards

Esta seção aborda os detalhes do [trabalhando com Storyboards](~/mac/platform/storyboards/indepth.md) em um aplicativo Xamarin.Mac. Vamos dar uma Visão aprofundada em segundo plano e como elas são compostas de controladores de exibição e o modo de exibição. Em seguida, vamos dar uma olhada em como segundo plano é associados com Segues. Por fim, vamos dar uma olhada em trabalhar com tipos personalizados de Segue. 

<a name="Complex-Storyboard-Example" />

## <a name="complex-storyboard-example"></a>Exemplo de Storyboard complexo

Para obter um exemplo de um exemplo complexo de trabalhar com Storyboards em um aplicativo de Xamarin.Mac, consulte o [aplicativo de exemplo SourceWriter](https://developer.xamarin.com/samples/mac/SourceWriter/). SourceWriter é um editor de código-fonte simples que dá suporte para a conclusão de código e realce de sintaxe simples.

O código do SourceWriter foi totalmente comentado e, quando disponível, foram fornecidos links de tecnologias-chave ou métodos para informações relevantes na Documentação de Guias do Xamarin.Mac.

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo obteve uma visão geral de trabalhar com Storyboards em um aplicativo Xamarin.Mac. Vimos como criar um novo aplicativo usando storyboards e como definir uma interface do usuário. Também vimos como navegar entre diferentes janelas e estados de exibição usando segues.


## <a name="related-links"></a>Links relacionados

- [Hello, Mac (amostra)](https://developer.xamarin.com/samples/mac/Hello_Mac/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Trabalhando com o Windows](~/mac/user-interface/window.md)
- [Diretrizes de interface humana do OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Introdução ao Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
