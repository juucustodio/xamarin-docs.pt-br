---
title: Padrões e os idiomas em Xamarin.Mac comuns
description: Este documento descreve os padrões comuns de design usados na criação de aplicativos Xamarin.Mac. Ele aborda o padrão model-view-controller, padrões de dados de origem e o delegado e protocolos.
ms.prod: xamarin
ms.assetid: BF0A3517-17D8-453D-87F7-C8A34BEA8FF5
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/17/2016
ms.openlocfilehash: f6bba5575edf2dcbddbd354b590e03f9fed06291
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34791531"
---
# <a name="common-patterns-and-idioms-in-xamarinmac"></a>Padrões e os idiomas em Xamarin.Mac comuns

Ao longo das APIs de Apple expostos por meio do c#, determinados idiomas e os padrões surgem repetidamente. Se você tiver experiência com programação com xamarin, eles podem parecer familiares. Documentação geralmente fará referência a esses padrões e as linguagens repetidamente, para que ter uma compreensão sólida deles ajudará você a compreender a documentação que você encontrar.

## <a name="mvc---model-view-controller"></a>MVC - Model View Controller

Model View Controller, ou MVC para resumir, é um padrão muito comum em toda a Cocoa. Uma discussão detalhada está além do escopo deste documento, mas em suma é uma maneira de organizar seu aplicativo em componentes:

- **Modelo** objetos representam os dados subjacentes sendo exibido e manipulado (como endereços no catálogo de endereços)
- **Exibição** objetos tratar o desenho de um determinado objeto na tela e tratamento de interação do usuário (um campo de texto mostrando o endereço na tela)
- **Controlador** objetos tratar a interação entre o modelo e a exibição. Eles por push as alterações do modelo "até" para atualizar o modo de exibição e pressionar "" alterações da exibição quando os usuários fazer alterações na interface de usuário.

Se você estiver familiarizado com o modelo MVVM (Model-View-ViewModel) de outras bibliotecas, como WPF, o controlador age de forma semelhante ao ViewModel, mas geralmente é mais de perto associado para os elementos de interface do usuário específicos.

Mais detalhes podem ser encontrados aqui:

- [Aprendizado MVC no site da Apple](https://developer.apple.com/library/ios/documentation/general/conceptual/devpedia-cocoacore/MVC.html)

- [Model View Controller em Objective-C](https://developer.apple.com/library/ios/documentation/general/conceptual/CocoaEncyclopedia/Model-View-Controller/Model-View-Controller.html)
- [Trabalhando com o Windows](~/mac/user-interface/window.md)

## <a name="data-source--delegate--subclassing"></a>Fonte de dados / delegar / subclasses

Outro padrão muito comum em Cocoa lida com fornece dados aos elementos de interface do usuário e reagir a interação do usuário. Usando `NSTableView` como exemplo, você precisa fornecer alguma forma os dados para cada linha, alguns conjunto de elementos de interface do usuário que representam que linha alguns conjunto de comportamentos para reagir a interação do usuário e possivelmente algum tempo de personalização. Os padrões de origem e o representante de dados permitem que você lidar com a maioria dos casos sem precisar recorrer a subclassificação `NSTableView` por conta própria.

- O `DataSource` é atribuído a uma instância de uma subclasse personalizada de propriedade `NSTableViewDataSource` que é chamado para popular a tabela com dados (por meio de `GetRowCount` e `GetObjectValue`).

- O `Delegate` é atribuído a uma instância de uma subclasse personalizada de propriedade `NSTableViewDelegate` que fornece o modo de exibição para um objeto de modelo especificado (por meio de `GetViewForItem`) e manipula as interações de interface do usuário (por meio de `DidClickTableColumn`, `MouseDownInHeaderOfTableColumn`, etc).

Em alguns casos, você desejará personalizar um controle de forma além de fixação de dado na fonte de dados ou representante e você pode subclasse a exibição diretamente. Tenha cuidado no entanto, em muitos casos, substituindo padrão de comportamento, em seguida, exigirá que você trate todos desse comportamento (como personalizar o comportamento de seleção pode exigir a implementar todos os comportamentos de seleção).

No xamarin, algumas APIs, como `UITableView` foram estendidos com uma propriedade que implementa o delegado e a fonte de dados (`UITableViewSource`). Isso para resolver a limitação comuns que uma única classe c# só pode ter uma classe base e nossa exibição dos protocolos é feito por meio de classes base.

Para obter mais informações sobre como trabalhar com modos de exibição de tabela em um aplicativo de Xamarin.Mac, consulte nosso [tabela exibição](~/mac/user-interface/table-view.md) documentação.

## <a name="protocols"></a>Protocolos

Protocolos em Objective-C podem ser comparados com interfaces em c# e em muitos casos, são usados em situações semelhantes. Por exemplo o `NSTableView` exemplo acima, o delegado e a fonte de dados são realmente protocolos. Xamarin.Mac expõe como classes base com métodos virtuais, que você pode substituir. A principal diferença entre as interfaces do c# e protocolos Objective-C é que alguns métodos em um protocolo podem ser opcionais para implementar. Você precisará examinar a documentação e/ou a definição de uma API para determinar o que é opcional.

Obter mais informações, consulte nosso [delegados, protocolos e eventos](~/ios/app-fundamentals/delegates-protocols-and-events.md) documentação.



## <a name="related-links"></a>Links relacionados

- [Modos de exibição de tabela](~/mac/user-interface/table-view.md)
- [Trabalhando com o windows](~/mac/user-interface/window.md)
- [Delegados, protocolos e eventos](~/ios/app-fundamentals/delegates-protocols-and-events.md)
- [Model-View-Controller](https://developer.apple.com/library/ios/documentation/general/conceptual/CocoaEncyclopedia/Model-View-Controller/Model-View-Controller.html)
