---
title: watchOS Interface de usuário
ms.prod: xamarin
ms.assetid: EDFAD203-02EA-4A74-9CE2-7B8513BC90E1
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/19/2016
ms.openlocfilehash: 73099768d876cad08571c3d0bf8340535eb1307b
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="watchos-user-interface"></a>watchOS Interface de usuário

O [ **WatchKitCatalog** ](https://github.com/xamarin/monotouch-samples/tree/master/watchOS/WatchKitCatalog) demonstra vários controles watchOS. Storyboard do aplicativo é mostrada aqui (clique para ampliar):

[![](images/storyboard-sml.png "Layout de watchOS de exemplo")](images/storyboard.png#lightbox)

Os nomes de todos os controles de programáticos é prefixado com `WKInterface` (por exemplo. `WKInterfaceLabel`, `WKInterfaceButton`).

|Controle|Descrição|captura de tela|
|---|---|---|
|Rotular|Use `SetText` e outras propriedades para controlar a aparência do texto em um controle de rótulo. `NSAttributedString` também é suportado.<br />[Código do catálogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/LabelDetailController.cs)|![](Images/label.png)|
|Botão|Criar e definir propriedades no storyboard. CTRL + arrastar para adicionar um `Action` para implementar um manipulador quando ele for clicado.<br />[Código do catálogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/ButtonDetailController.cs)|![](Images/button.png)|
|Alternar|Use `SetOn` para controlar o estado do comutador.<br />[Código do catálogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/SwitchDetailController.cs)|![](Images/switch.png)|
|Controle deslizante|Vários estilos diferentes são possíveis.<br />[Código do catálogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/SliderDetailController.cs)|![](Images/slider.png)|
|Image|Use `myImage.SetImage("MyWatchImage")` para carregar imagens no watch, ou `WKInterfaceDevice.CurrentDevice.AddCachedImage` -los em cache para uso repetido no relógio.<br />[Documentação do controle de imagem](~/ios/watchos/user-interface/image.md)<br />[Código do catálogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/ImageDetailController.cs)|![](Images/image.png)|
|Separador|Use separadores para ajudar a criar interfaces do usuário de inspeção atraente.<br />[Código do catálogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/SeparatorDetailController.cs)|![](Images/separator.png)| 
|Mapa|A imagem do mapa estaticamente é exibida no relógio, mas você pode controlar vários aspectos de sua aparência, incluindo a adição de pins.<br />[Código do catálogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/MapDetailController.cs)|![](Images/map.png)|
|Filme & InlineMove|Filmes podem abrir por conta própria ou embutido<br />[Código do catálogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/MovieDetailController.cs)|![](Images/movie.png)|
|Grupo|Use grupos para ajudar a criar interfaces do usuário de inspeção atraente.<br />[Código do catálogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/GroupDetailController.cs)|![](Images/group.png)|
|Tabela|Uma versão simplificada de tabelas no iOS. Implementar `DidSelectRow` para responder a seleção do usuário (ou usar um segue).<br />[Documentação de controle de tabela](~/ios/watchos/user-interface/table.md)<br />[Código do catálogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/Table%20Detail%20Controller/TableDetailController.cs)|![](Images/table.png)|
|Dispositivo|`WKInterfaceDevice.CurrentDevice` inclui propriedades como `ScreenBounds`, `ScreenScale`, e `PreferredContentSizeCategory`.<br />[Código do catálogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/DeviceDetailController.cs)|![](Images/device.png)|
|[Menu](~/ios/watchos/user-interface/menu.md)|Definir o menu pressione force no storyboard e implementar as ações de cada botão no código.<br />[Documentação do menu controle (forçar Touch)](~/ios/watchos/user-interface/menu.md)<br />[Código do catálogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/ControllerDetailController.cs)|![](Images/controller.png)|
|Entrada de Texto|Use `PresentTextInputController` e `WKTextInputMode` enumeração.<br />[Documentação de entrada de texto](~/ios/watchos/user-interface/text-input.md)<br />[Código do catálogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/TextInputController.cs)|![](Images/textinput.png)|
|Coroa digital|O coroa Digital pode ser usada para um seletor de unidade ou sua rotação pode ser acompanhada no código.<br />[Código do catálogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/CrownDetailController.cs)|![](Images/digital-crown.png)|
|Gestos|Há quatro tipos de reconhecimento de gesto que podem ser adicionados a uma cena: toque, passe o dedo, Panorâmica e LongPress.<br />[Código do catálogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/GestureDetailController.cs)|![](Images/gestures.png)|


## <a name="related-links"></a>Links relacionados

- [WatchKitCatalog (exemplo)](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [Referência de API do Kit de inspeção](https://developer.xamarin.com/api/namespace/WatchKit/)
