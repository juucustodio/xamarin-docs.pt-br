---
title: Linguagens no xamarin. Mac e padrões comuns
description: Este documento descreve os padrões de design comuns usados na criação de aplicativos xamarin. Mac. Ele aborda o padrão model-view-controller, padrões de dados de origem e de delegado e protocolos.
ms.prod: xamarin
ms.assetid: BF0A3517-17D8-453D-87F7-C8A34BEA8FF5
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 06/17/2016
ms.openlocfilehash: b4266582ce0cec522e207cd06987f2d0eeff8b2d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61090840"
---
# <a name="common-patterns-and-idioms-in-xamarinmac"></a>Linguagens no xamarin. Mac e padrões comuns

Ao longo da Apple APIs expostas por meio do c#, determinadas linguagens e padrões surgem repetidamente. Se você tiver experiência com programação com xamarin. IOS, elas podem parecer familiares. Documentação geralmente fará referência a essas linguagens e padrões repetidamente, portanto, ter uma compreensão sólida deles ajudará você a dar sentido a documentação que você encontrar.

## <a name="mvc---model-view-controller"></a>MVC - Model View Controller

Model View Controller, ou MVC para abreviar, é um padrão muito comum encontrado em todo o Cocoa. Uma discussão detalhada está além do escopo deste documento, mas em resumo é uma maneira de estruturar seu aplicativo em componentes:

- **Modelo** objetos representam dados subjacente sendo exibido e manipulado (como endereços em um catálogo de endereços)
- **Modo de exibição** objetos lidar com o desenho de um determinado objeto na tela e manipular a interação do usuário (um campo de texto mostrando o endereço na tela)
- **Controlador** objetos manipular a interação entre o modelo e o modo de exibição. Aplicar alterações no modelo de "Ascensão" para atualizar a exibição e enviar por push "para baixo" alterações da exibição quando os usuários a fazer alterações na interface do usuário.

Se você estiver familiarizado com o MVVM (Model-View-ViewModel) de outras bibliotecas, como o WPF, o controlador age de forma semelhante ao ViewModel, mas geralmente é mais de perto associado aos elementos da interface do usuário específicos.

Mais detalhes podem ser encontrados aqui:

- [Aprendizado do MVC no site da Apple](https://developer.apple.com/library/ios/documentation/general/conceptual/devpedia-cocoacore/MVC.html)

- [Model View Controller em Objective-C](https://developer.apple.com/library/ios/documentation/general/conceptual/CocoaEncyclopedia/Model-View-Controller/Model-View-Controller.html)
- [Trabalhando com o Windows](~/mac/user-interface/window.md)

## <a name="data-source--delegate--subclassing"></a>Fonte de dados / delegar / subclasses

Outro padrão muito comum em Cocoa lida com a fornecer dados para elementos de interface do usuário e a reagir às interações do usuário. Usando `NSTableView` como exemplo, você precisa fornecer alguma forma, os dados para cada linha, alguns definidos de elementos de interface do usuário que representam essa linha, alguns conjunto de comportamentos para reagir às interações do usuário e, possivelmente, alguma quantidade de personalização. Os padrões de código-fonte e o delegado de dados permitem que você lidar com a maioria dos casos sem precisar recorrer à criação de subclasses `NSTableView` por conta própria.

- O `DataSource` é atribuído a uma instância de uma subclasse personalizada da propriedade `NSTableViewDataSource` que é chamado para popular a tabela com dados (via `GetRowCount` e `GetObjectValue`).

- O `Delegate` é atribuído a uma instância de uma subclasse personalizada da propriedade `NSTableViewDelegate` que fornece o modo de exibição para um objeto de modelo fornecido (via `GetViewForItem`) e manipula as interações de interface do usuário (por meio `DidClickTableColumn`, `MouseDownInHeaderOfTableColumn`, etc).

Em alguns casos, convém personalizar um controle de uma maneira além os ganchos fornecido na fonte de dados ou delegado e você pode organizar em subclasses a exibição diretamente. Tenha cuidado no entanto, em muitos casos, substituindo padrão de comportamento, em seguida, exigirá que você trate todos esse comportamento (como personalizar o comportamento de seleção pode exigir a implementar todos os comportamentos de seleção).

No xamarin. IOS, algumas APIs, como `UITableView` foram estendidos com uma propriedade que implementa o delegado e a fonte de dados (`UITableViewSource`). -La para contornar a limitação comum que um único C# classe pode ter somente uma classe base, e nosso identificando dos protocolos é feita por meio de classes base.

Para obter mais informações sobre como trabalhar com modos de exibição de tabela em um aplicativo xamarin. Mac, consulte nosso [exibição de tabela](~/mac/user-interface/table-view.md) documentação.

## <a name="protocols"></a>Protocolos

Protocolos em Objective-C podem ser comparados para interfaces em C#e em muitos casos, são usados em situações semelhantes. Por exemplo o `NSTableView` exemplo acima, o delegado e a fonte de dados são, na verdade, os protocolos. Xamarin. Mac expõe como classes base com métodos virtuais, que você pode substituir. A principal diferença entre C# interfaces e protocolos de Objective-C é que alguns métodos em um protocolo podem ser opcionais para implementar. Você precisará examinar a documentação e/ou a definição de uma API para determinar o que é opcional.

Obter mais informações, consulte nosso [eventos, protocolos e delegados](~/ios/app-fundamentals/delegates-protocols-and-events.md) documentação.



## <a name="related-links"></a>Links relacionados

- [Modos de exibição de tabela](~/mac/user-interface/table-view.md)
- [Trabalhando com o windows](~/mac/user-interface/window.md)
- [Eventos, protocolos e delegados](~/ios/app-fundamentals/delegates-protocols-and-events.md)
- [Model-View-Controller](https://developer.apple.com/library/ios/documentation/general/conceptual/CocoaEncyclopedia/Model-View-Controller/Model-View-Controller.html)
