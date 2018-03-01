---
title: "watchOS Interface de usuário"
ms.topic: article
ms.prod: xamarin
ms.assetid: EDFAD203-02EA-4A74-9CE2-7B8513BC90E1
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/19/2016
ms.openlocfilehash: 34063be728f8a13bbe5d68440d9aceba417c5627
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="watchos-user-interface"></a>watchOS Interface de usuário

O [ **WatchKitCatalog** ](https://github.com/xamarin/monotouch-samples/tree/master/watchOS/WatchKitCatalog) demonstra vários controles watchOS. Storyboard do aplicativo é mostrada aqui (clique para ampliar):

[ ![](images/storyboard-sml.png "Layout de watchOS de exemplo")](images/storyboard.png)

Os nomes de todos os controles de programáticos é prefixado com `WKInterface` (por exemplo. `WKInterfaceLabel`, `WKInterfaceButton`).


<table align="center" border="1" cellpadding="1" cellspacing="1">
  <thead>
      <th>
        <strong>Controle</strong>
      </th>
      <th>
        <strong>Descrição</strong>
      </th>
      <th>
        <strong>captura de tela</strong>
      </th>
    </thead>
    <tbody>
    <tr>
      <td valign="top">
Rotular </td>
      <td valign="top">
Use <code>SetText</code> e outras propriedades para controlar a aparência do texto em um controle de rótulo. <code>NSAttributedString</code> também é suportado.
        <br />
        <a href="https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/LabelDetailController.cs">Código do catálogo</a>
      </td>
      <td>
        <img src="Images/label.png" class="tableimg">
      </td>
    </tr>
    <tr>
      <td valign="top">
Botão </td>
      <td valign="top">
Criar e definir propriedades no storyboard. <kbd>CTRL + arrastar</kbd> para adicionar um <code>Action</code> para implementar um manipulador quando ele for clicado.
        <br />
        <a href="https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/ButtonDetailController.cs">Código do catálogo</a>
      </td>
      <td>
        <img src="Images/button.png" class="tableimg">
      </td>
    </tr>
    <tr>
      <td valign="top">
Alternar </td>
      <td valign="top">
Use <code>SetOn</code> para controlar o estado do comutador.
        <br />
        <a href="https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/SwitchDetailController.cs">Código do catálogo</a>
      </td>
      <td>
        <img src="Images/switch.png" class="tableimg">
      </td>
    </tr>
    <tr>
      <td valign="top">
Controle deslizante </td>
      <td valign="top">
Vários estilos diferentes são possíveis.
        <br />
        <a href="https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/SliderDetailController.cs">Código do catálogo</a>
      </td>
      <td>
        <img src="Images/slider.png" class="tableimg">
      </td>
    </tr>
    <tr>
      <td valign="top">
Image </td>
      <td valign="top">
Use <code>myImage.SetImage("MyWatchImage")</code> para carregar imagens no watch, ou <code>WKInterfaceDevice.CurrentDevice.AddCachedImage</code> -los em cache para uso repetido no relógio.
        <br />
        <a href="~/ios/watchos/user-interface/image.md">Documentação do controle de imagem</a>
        <br />
        <a href="https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/ImageDetailController.cs">Código do catálogo</a>
      </td>
      <td>
        <img src="Images/image.png" class="tableimg">
      </td>
    </tr>
    <tr>
      <td valign="top">
Separador </td>
      <td valign="top">
Use separadores para ajudar a criar interfaces do usuário de inspeção atraente.
        <br />
        <a href="https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/SeparatorDetailController.cs">Código do catálogo</a>
      </td>
      <td>
        <img src="Images/separator.png" class="tableimg">
      </td>
    </tr>
    <tr>
      <td valign="top">
Mapa </td>
      <td valign="top">
A imagem do mapa estaticamente é exibida no relógio, mas você pode controlar vários aspectos de sua aparência, incluindo a adição de pins.
        <br />
        <a href="https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/MapDetailController.cs">Código do catálogo</a>
      </td>
      <td>
        <img src="Images/map.png" class="tableimg">
      </td>
    </tr>
    <tr>
      <td valign="top">
Filme & InlineMove </td>
      <td valign="top">
Filmes podem abrir por conta própria ou embutido <br />
        <a href="https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/MovieDetailController.cs">Código do catálogo</a>
      </td>
      <td>
        <img src="Images/movie.png" class="tableimg">
      </td>
    </tr>
    <tr>
      <td valign="top">
Grupo </td>
      <td valign="top">
Use grupos para ajudar a criar interfaces do usuário de inspeção atraente.
        <br />
        <a href="https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/GroupDetailController.cs">Código do catálogo</a>
      </td>
      <td>
        <img src="Images/group.png" class="tableimg">
      </td>
    </tr>
    <tr>
      <td valign="top">
Tabela </td>
      <td valign="top">
Uma versão simplificada de tabelas no iOS.
Implementar <code>DidSelectRow</code> para responder a seleção do usuário (ou usar um segue).
        <br />
        <a href="~/ios/watchos/user-interface/table.md">Documentação de controle de tabela</a>
        <br />
        <a href="https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/TableDetailController.cs">Código do catálogo</a>
      </td>
      <td>
        <img src="Images/table.png" class="tableimg">
      </td>
    </tr>
    <tr>
      <td valign="top">
Dispositivo </td>
      <td valign="top">
        <code>WKInterfaceDevice.CurrentDevice</code> inclui propriedades como <code>ScreenBounds</code>, <code>ScreenScale</code>, e <code>PreferredContentSizeCategory</code>.
        <br />
        <a href="https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/DeviceDetailController.cs">Código do catálogo</a>
      </td>
      <td>
        <img src="Images/device.png" class="tableimg">
      </td>
    </tr>
    <tr>
      <td valign="top">
        <a href="~/ios/watchos/user-interface/menu.md">Menu</a>
      </td>
      <td valign="top">
Definir o menu pressione force no storyboard e implementar as ações de cada botão no código.
        <br />
        <a href="~/ios/watchos/user-interface/menu.md">Documentação do menu controle (forçar Touch)</a>
        <br />
        <a href="https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/ControllerDetailController.cs">Código do catálogo</a>
      </td>
      <td>
        <img src="Images/controller.png" class="tableimg">
      </td>
    </tr>
    <tr>
      <td valign="top">
Entrada de Texto </td>
      <td valign="top">
Use <code>PresentTextInputController</code> e <code>WKTextInputMode</code> enumeração.
        <br />
        <a href="~/ios/watchos/user-interface/text-input.md">Documentação de entrada de texto</a>
        <br />
        <a href="https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/TextInputDetailController.cs">Código do catálogo</a>
      </td>
      <td>
        <img src="Images/textinput.png" class="tableimg">
      </td>
    </tr>
    <tr>
      <td valign="top">
Coroa digital </td>
      <td valign="top">
O coroa Digital pode ser usada para um seletor de unidade ou sua rotação pode ser acompanhada no código.
        <br />
        <a href="https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/CrownDetailController.cs">Código do catálogo</a>
      </td>
      <td>
        <img src="Images/digital-crown.png" class="tableimg">
      </td>
    </tr>
    <tr>
      <td valign="top">
Gestos </td>
      <td valign="top">
Há quatro tipos de reconhecimento de gesto que podem ser adicionados a uma cena: toque, passe o dedo, Panorâmica e LongPress.
        <br />
        <a href="https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/GestureDetailController.cs">Código do catálogo</a>
      </td>
      <td>
        <img src="Images/gestures.png" class="tableimg">
      </td>
    </tr>
    </tbody>
</table>



## <a name="related-links"></a>Links relacionados

- [WatchKitCatalog (exemplo)](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [Referência de API do Kit de inspeção](https://developer.xamarin.com/api/namespace/WatchKit/)
