---
title: Padrões e linguagens comuns no Xamarin. Mac
description: Este documento descreve os padrões de design comuns usados durante a criação de aplicativos Xamarin. Mac. Ele aborda o padrão Model-View-Controller, a fonte de dados e os padrões de representante e os protocolos.
ms.prod: xamarin
ms.assetid: BF0A3517-17D8-453D-87F7-C8A34BEA8FF5
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 06/17/2016
ms.openlocfilehash: b4934fa82d862ad2e8ab53579137873ed9e4bcca
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70770172"
---
# <a name="common-patterns-and-idioms-in-xamarinmac"></a>Padrões e linguagens comuns no Xamarin. Mac

Em todas as APIs da Apple C#expostas por meio de, determinados idiomas e padrões surgem repetidamente. Se você tiver experiência com programação com Xamarin. iOS, elas podem parecer familiares. A documentação geralmente se referirá a esses padrões e idiomas repetidamente, então ter um entendimento sólido deles ajudará você a compreender a documentação que encontrar.

## <a name="mvc---model-view-controller"></a>MVC-controlador de exibição de modelo

O controlador de exibição de modelo, ou MVC, é um padrão muito comum encontrado em todo o Cocoa. Uma discussão detalhada está além do escopo deste documento, mas, em suma, é uma maneira de estruturar seu aplicativo em componentes:

- Objetos de **modelo** representam os dados subjacentes sendo exibidos e manipulados (como endereços em um catálogo de endereços)
- Os objetos de **exibição** manipulam o desenho de um determinado objeto na tela e manipulam a interação do usuário (um campo de texto mostrando o endereço na tela)
- Os objetos do **controlador** manipulam a interação entre o modelo e a exibição. Eles enviam as alterações de modelo "para cima" para atualizar a exibição e enviar por push as alterações de "para baixo" da exibição quando os usuários fizerem alterações na interface do usuário.

Se você estiver familiarizado com o MVVM (Model View ViewModel) de outras bibliotecas, como o WPF, o controlador age de forma semelhante ao ViewModel, mas muitas vezes está mais intimamente associado aos elementos específicos da interface do usuário.

Mais detalhes podem ser encontrados aqui:

- [Aprendendo o MVC no site da Apple](https://developer.apple.com/library/ios/documentation/general/conceptual/devpedia-cocoacore/MVC.html)

- [Controlador de exibição de modelo em Objective-C](https://developer.apple.com/library/ios/documentation/general/conceptual/CocoaEncyclopedia/Model-View-Controller/Model-View-Controller.html)
- [Trabalhando com o Windows](~/mac/user-interface/window.md)

## <a name="data-source--delegate--subclassing"></a>Fonte de dados/delegar/subclasse

Outro padrão muito comum no Cocoa lida com o fornecimento de dados para elementos de interface do usuário e a reagindo para interações de usuários. Usando `NSTableView` como exemplo, você precisa fornecer de alguma forma os dados para cada linha, alguns conjuntos de elementos da interface do usuário que representam essa linha, algum conjunto de comportamentos para reagir a interações do usuário e, possivelmente, alguma quantidade de personalização. A fonte de dados e os padrões de representante permitem lidar com a maioria dos casos sem precisar recorrer a uma subclasse `NSTableView` .

- A `DataSource` propriedade é atribuída a uma instância de uma `NSTableViewDataSource` subclasse personalizada, que é chamada para popular a tabela com dados (via `GetRowCount` e `GetObjectValue`).

- A `Delegate` propriedade é atribuída a uma instância de uma `NSTableViewDelegate` subclasse personalizada que fornece a exibição de um determinado objeto de modelo (via `GetViewForItem`) e manipula as interações de `DidClickTableColumn`interface `MouseDownInHeaderOfTableColumn`do usuário (via, etc.).

Em alguns casos, você desejará personalizar um controle de uma maneira além dos ganchos fornecidos no delegado ou na fonte de dados e você pode criar uma subclasse do modo de exibição diretamente. No entanto, tenha cuidado, em muitos casos, substituir o comportamento padrão exigirá que você manipule todo esse comportamento por conta própria (personalizar o comportamento de seleção pode exigir que você implemente todos os comportamentos de seleção por conta própria).

No Xamarin. Ios, algumas APIs, `UITableView` como foram estendidas com uma propriedade que implementa o delegado e a fonte de dados (`UITableViewSource`). Isso para contornar a limitação comum de que uma única C# classe só pode ter uma classe base, e nosso identificando de protocolos é feito por meio de classes base.

Para obter mais informações sobre como trabalhar com exibições de tabela em um aplicativo Xamarin. Mac, consulte nossa documentação de [exibição de tabela](~/mac/user-interface/table-view.md) .

## <a name="protocols"></a>Protocolos

Os protocolos em Objective-C podem ser comparados a interfaces no C#e, em muitos casos, são usados em situações semelhantes. Por exemplo, `NSTableView` o exemplo acima, o delegado e a fonte de dados são realmente protocolos. O Xamarin. Mac os expõe como classes base com métodos virtuais que você pode substituir. A principal diferença entre C# interfaces e protocolos Objective-C é que alguns métodos em um protocolo podem ser opcionais para implementar. Você precisará examinar a documentação e/ou a definição de uma API para determinar o que é opcional.

Para obter mais informações, consulte nossa documentação [de delegados, protocolos e eventos](~/ios/app-fundamentals/delegates-protocols-and-events.md) .

## <a name="related-links"></a>Links relacionados

- [Exibições de tabela](~/mac/user-interface/table-view.md)
- [Trabalhando com o Windows](~/mac/user-interface/window.md)
- [Delegados, protocolos e eventos](~/ios/app-fundamentals/delegates-protocols-and-events.md)
- [Model-View-Controller](https://developer.apple.com/library/ios/documentation/general/conceptual/CocoaEncyclopedia/Model-View-Controller/Model-View-Controller.html)
