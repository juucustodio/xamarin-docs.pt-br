---
title: Criando objetos da interface do usuário no Xamarin. iOS
description: Este documento fornece uma visão geral de como criar uma interface do usuário no Xamarin. iOS. Ele aborda o designer do iOS, o Xcode Interface Builder C#, e os storyboards.
ms.prod: xamarin
ms.assetid: 4D6B136C-744A-4936-8655-A77E62BA7A60
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 58a1fd68dda2216a62fe6f30cf61d6d2ec7d40d5
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73003664"
---
# <a name="creating-user-interface-objects-in-xamarinios"></a>Criando objetos da interface do usuário no Xamarin. iOS

A Apple agrupa partes de funcionalidade relacionadas a "estruturas" que são equivalentes aos namespaces do Xamarin. iOS. `UIKit` é o namespace que contém todos os controles de interface do usuário para iOS.

Sempre que seu código precisar fazer referência a um controle de interface do usuário, como um rótulo ou um botão, lembre-se de incluir a seguinte instrução Using:

```csharp
using UIKit;
```

Todos os controles discutidos neste capítulo estão no namespace UIKit e cada nome de classe de controle de usuário tem o prefixo `UI`.

Você pode editar os controles e layouts da interface do usuário de três maneiras:

- **[Xamarin Ios designer](~/ios/user-interface/designer/index.md)** – use o designer de layout interno do xamarin para criar telas. Clique duas vezes em arquivos storyboard ou XIB para editar com o designer interno.
- **Interface Builder do Xcode** – arraste os controles para os layouts de tela com interface Builder. Abra o arquivo storyboard ou XIB no Xcode clicando com o botão direito do mouse no arquivo na **painel de soluções** e escolhendo **abrir com > Xcode Interface Builder**.
- **O C# uso** de – controles também pode ser construído programaticamente com o código e adicionado à hierarquia de exibição.

Novos arquivos de storyboard e XIB podem ser adicionados clicando com o botão direito do mouse em um projeto do iOS e escolhendo **adicionar > novo arquivo...** .

Seja qual for o método usado, as propriedades de controle e os eventos ainda C# podem ser manipulados com a lógica do aplicativo.

## <a name="using-xamarin-ios-designer"></a>Usando o Xamarin iOS designer

Para começar a criar sua interface do usuário no designer do iOS, clique duas vezes em um arquivo de storyboard. Os controles podem ser arrastados para a superfície de design da **caixa de ferramentas** , conforme ilustrado abaixo:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

 [![](creating-ui-objects-images/image2b.png "Toolbox Pad")](creating-ui-objects-images/image2b.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

 [![](creating-ui-objects-images/image2b-vs.png "Toolbox Pad - Visual Stuio")](creating-ui-objects-images/image2b.png#lightbox)

-----

Quando um controle é selecionado na superfície de design, o **painel de propriedades** mostrará os atributos para esse controle. O **Widget > identidade > campo nome** , que é preenchido na captura de tela abaixo, é usado como o nome da *tomada* . É assim que você pode referenciar o controle C#em:

 [![](creating-ui-objects-images/image3b.png "Properties Widget Pad")](creating-ui-objects-images/image3b.png#lightbox)

Para obter mais detalhes sobre como usar o designer do iOS, consulte a [introdução ao guia do designer do IOS](~/ios/user-interface/designer/introduction.md) .

## <a name="using-xcode-interface-builder"></a>Usando o Xcode Interface Builder

Se você não estiver familiarizado com o uso de Interface Builder, consulte os documentos de [interface Builder](https://developer.apple.com/xcode/interface-builder/) da Apple.

Para abrir um storyboard no Xcode, clique com o botão direito do mouse para acessar o menu de contexto do arquivo de storyboard e escolha abrir com o **interface Builder do Xcode**:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

 [![](creating-ui-objects-images/imagexcode.png "Storyboard context menu - Xcode")](creating-ui-objects-images/imagexcode.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](creating-ui-objects-images/imagexcode-vs.png "Storyboard context menu - Xcode")](creating-ui-objects-images/imagexcode-vs.png#lightbox)

-----

Os controles podem ser arrastados para a Design Surface da **biblioteca de objetos** ilustrada abaixo:

 [![](creating-ui-objects-images/image5a.png "Xcode Object Library")](creating-ui-objects-images/image5a.png#lightbox)

Ao projetar sua interface do usuário com o Interface Builder você deve criar uma **tomada** para cada controle no C#qual você deseja fazer referência. Isso é feito ativando o **Editor do assistente** usando o botão central **Editor** no botão da barra de ferramentas do Xcode:

 [![](creating-ui-objects-images/image6a.png "Assistant Editor button")](creating-ui-objects-images/image6a.png#lightbox)

Clique em um objeto de interface do usuário; em seguida, **controle arraste** para o arquivo. h. Para **controlar arrastar**, mantenha pressionada a tecla Control e, em seguida, clique e mantenha pressionado o objeto de interface do usuário para o qual você está criando a tomada (ou ação). Mantenha a tecla Control pressionada enquanto você arrasta para o arquivo de cabeçalho. Termine de arrastar para a definição de `@interface`. Uma linha azul deve aparecer com uma inserção de legenda ou coleção de imtomada, conforme ilustrado na captura de tela abaixo.

Ao liberar o clique, você será solicitado a fornecer um nome para a tomada, que será usado para criar uma C# propriedade que pode ser referenciada no código:

 [![](creating-ui-objects-images/image8a.png "Creating an outlet")](creating-ui-objects-images/image8a.png#lightbox)

Para obter mais informações sobre como o Interface Builder do Xcode se integra ao Visual Studio para Mac, consulte o documento [geração de código XIB](~/ios/internals/xib-code-generation.md#generated) .

## <a name="using-c"></a>Usando o C \#

Se você decidir criar programaticamente um objeto de interface C# do usuário usando (em um modo de exibição ou controlador de exibição, por exemplo), siga estas etapas:

- Declare um campo de nível de classe para o objeto da interface do usuário. Crie o próprio controle uma vez, em `ViewDidLoad` por exemplo. O objeto pode então ser referenciado em todos os métodos de ciclo de vida do controlador de exibição (por exemplo,
`ViewWillAppear`).
- Crie um `CGRect` que define o quadro do controle (suas coordenadas X e Y na tela, bem como sua largura e altura). Você precisará certificar-se de ter uma diretiva de `using CoreGraphics` para isso.
- Chame o construtor para criar e atribuir o controle.
- Defina quaisquer propriedades ou manipuladores de eventos.
- Chame `Add()` para adicionar o controle à hierarquia de exibição.

Aqui está um exemplo simples de criação de um `UILabel` em um controlador de C#exibição usando:

```csharp
UILabel label1;
public override void ViewDidLoad () {
    base.ViewDidLoad ();
    var frame = new CGRect(10, 10, 300, 30);
    label1 = new UILabel(frame);
    label1.Text = "New Label";
    View.Add (label1);
}
```

<a name="partial_classes" />

## <a name="using-c-and-storyboards"></a>Usando C# storyboards e

Quando os controladores de exibição são adicionados ao Design Surface, dois C# arquivos correspondentes são criados no projeto. Neste exemplo, `ControlsViewController.cs` e `ControlsViewController.designer.cs` foram criados automaticamente:

 [![](creating-ui-objects-images/image9b.png "ViewController partial class")](creating-ui-objects-images/image9b.png#lightbox)

O arquivo de `ControlsViewController.cs` é destinado ao *seu código*. É aí que os métodos de ciclo de vida de `View` como `ViewDidLoad` e `ViewWillAppear` são implementados e onde você pode adicionar suas próprias propriedades, campos e métodos.

O `ControlsViewController.designer.cs` é um código gerado que contém uma classe parcial. Quando você nomear um controle na superfície de design em Visual Studio para Mac, ou criar uma tomada ou ação no Xcode, uma propriedade correspondente ou um método parcial será adicionado ao arquivo do designer (designer.cs). O código a seguir mostra um exemplo de código gerado para dois botões e uma exibição de texto, em que um dos botões também tem um evento de `TouchUpInside`.

Esses elementos da classe parcial permitem que seu código referencie os controles e responda às ações declaradas na superfície de design:

```csharp
[Register ("ControlsViewController")]
    partial class ControlsViewController
    {
        [Outlet]
        [GeneratedCodeAttribute ("iOS Designer", "1.0")]
        UIKit.UIButton Button1 { get; set; }

        [Outlet]
        [GeneratedCodeAttribute ("iOS Designer", "1.0")]
        UIKit.UIButton Button2 { get; set; }

        [Outlet]
        [GeneratedCodeAttribute ("iOS Designer", "1.0")]
        UIKit.UITextField textfield1 { get; set; }

        [Action ("button2_TouchUpInside:")]
        [GeneratedCodeAttribute ("iOS Designer", "1.0")]
        partial void button2_TouchUpInside (UIButton sender);

        void ReleaseDesignerOutlets ()
        {
            if (Button1 != null) {
                Button1.Dispose ();
                Button1 = null;
            }
            if (Button2 != null) {
                Button2.Dispose ();
                Button2 = null;
            }
            if (textfield1 != null) {
                textfield1.Dispose ();
                textfield1 = null;
            }
        }
    }
}
```

O arquivo de `designer.cs` não deve ser editado manualmente – o IDE (Visual Studio para Mac ou o Visual Studio) é responsável por mantê-lo sincronizado com o storyboard.

Quando os objetos da interface do usuário são adicionados programaticamente a um `View` ou `ViewController`, você instancia e gerencia as referências de objeto por conta própria e, portanto, nenhum arquivo de designer é necessário.

## <a name="related-links"></a>Links relacionados

- [Controles (exemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/controls)
