---
title: Controles de Interface do usuário do Xamarin do watchOS
description: Este documento descreve os vários controles que estão disponíveis para uso nas interfaces do usuário do watchOS. Ele fornece uma descrição de rótulos, botões, comutadores, controles deslizantes, imagens, separadores, mapas e muito mais.
ms.prod: xamarin
ms.assetid: EDFAD203-02EA-4A74-9CE2-7B8513BC90E1
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/19/2016
ms.openlocfilehash: c8ef76f24b017f5e3e6bec9d39534f3626e79147
ms.sourcegitcommit: 2713f2c1d74e3582704c3d0ca65b6651119ed489
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/15/2019
ms.locfileid: "56321097"
---
# <a name="watchos-user-interface-controls-in-xamarin"></a>Controles de Interface do usuário do Xamarin do watchOS

O [ **WatchKitCatalog** ](https://github.com/xamarin/monotouch-samples/tree/master/watchOS/WatchKitCatalog) exemplo demonstra vários controles de watchOS. Storyboard do aplicativo é mostrado aqui (clique para ampliar):

[![](images/storyboard-sml.png "Layout de watchOS de exemplo")](images/storyboard.png#lightbox)

Os nomes de todos os controles de programáticos é prefixado com `WKInterface` (por exemplo. `WKInterfaceLabel`, `WKInterfaceButton`).

|Controle|Descrição|Captura de tela|
|---|---|---|
|Rotular|Use `SetText` e outras propriedades para controlar a aparência do texto em um controle de rótulo. `NSAttributedString` também é suportado.<br />[Código de catálogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/LabelDetailController.cs)|![](Images/label.png)|
|Botão|Criar e definir propriedades no storyboard. CTRL + arrastar para adicionar um `Action` para implementar um manipulador para quando ele for clicado.<br />[Código de catálogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/ButtonDetailController.cs)|![](Images/button.png)|
|Alternar|Use `SetOn` para controlar o estado do comutador.<br />[Código de catálogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/SwitchDetailController.cs)|![](Images/switch.png)|
|Controle deslizante|Vários estilos diferentes são possíveis.<br />[Código de catálogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/SliderDetailController.cs)|![](Images/slider.png)|
|Image|Use `myImage.SetImage("MyWatchImage")` para carregar imagens no watch, ou `WKInterfaceDevice.CurrentDevice.AddCachedImage` -los em cache para uso repetido no relógio.<br />[Documentação do controle de imagem](~/ios/watchos/user-interface/image.md)<br />[Código de catálogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/ImageDetailController.cs)|![](Images/image.png)|
|Separador|Use separadores para ajudar a criar interfaces do usuário de inspeção atraente.<br />[Código de catálogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/SeparatorDetailController.cs)|![](Images/separator.png)| 
|Mapa|A imagem do mapa estaticamente é exibida no relógio, mas você pode controlar vários aspectos da sua aparência, incluindo a adição de pinos.<br />[Código de catálogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/MapDetailController.cs)|![](Images/map.png)|
|Filme & InlineMove|Filmes podem aberto por conta própria ou embutido<br />[Código de catálogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/MovieDetailController.cs)|![](Images/movie.png)|
|Grupo|Use grupos para ajudar a criar interfaces do usuário de inspeção atraente.<br />[Código de catálogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/GroupDetailController.cs)|![](Images/group.png)|
|Tabela|Uma versão simplificada das tabelas no iOS. Implemente `DidSelectRow` para responder a seleção do usuário (ou usar um segue).<br />[Documentação do controle de tabela](~/ios/watchos/user-interface/table.md)<br />[Código de catálogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/Table%20Detail%20Controller/TableDetailController.cs)|![](Images/table.png)|
|Dispositivo|`WKInterfaceDevice.CurrentDevice` inclui propriedades como `ScreenBounds`, `ScreenScale`, e `PreferredContentSizeCategory`.<br />[Código de catálogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/DeviceDetailController.cs)|![](Images/device.png)|
|[Menu](~/ios/watchos/user-interface/menu.md)|Definir menu pressione força no storyboard e implementar as ações para cada botão no código.<br />[Documentação de menu de controle (Force Touch)](~/ios/watchos/user-interface/menu.md)<br />[Código de catálogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/ControllerDetailController.cs)|![](Images/controller.png)|
|Entrada de Texto|Use `PresentTextInputController` e o `WKTextInputMode` enumeração.<br />[Documentação da entrada de texto](~/ios/watchos/user-interface/text-input.md)<br />[Código de catálogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/TextInputController.cs)|![](Images/textinput.png)|
|Coroa digital|O Crown Digital pode ser usado para orientar um seletor ou rotação dele pode ser controlada no código.<br />[Código de catálogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/CrownDetailController.cs)|![](Images/digital-crown.png)|
|Gestos|Há quatro tipos de reconhecimento de gesto que podem ser adicionados a uma cena: Toque, passe o dedo, Panorâmica e LongPress.<br />[Código de catálogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/GestureDetailController.cs)|![](Images/gestures.png)|


## <a name="related-links"></a>Links relacionados

- [WatchKitCatalog (amostra)](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [Assista a referência de API de Kit](xref:WatchKit)
