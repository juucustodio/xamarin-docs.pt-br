---
title: Criando objetos de Interface do usuário no xamarin. IOS
description: Este documento fornece uma visão geral de como criar uma interface do usuário no xamarin. IOS. Ele discute o Designer do iOS, Xcode Interface Builder, C#e storyboards.
ms.prod: xamarin
ms.assetid: 4D6B136C-744A-4936-8655-A77E62BA7A60
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: a4cf63b84d0686bc28b02b18a6266908251bdf6f
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50121913"
---
# <a name="creating-user-interface-objects-in-xamarinios"></a>Criando objetos de Interface do usuário no xamarin. IOS

Partes relacionadas de grupos de Apple de funcionalidade em "estruturas", que correspondem aos namespaces do xamarin. IOS. `UIKit` é o namespace que contém todos os controles de interface do usuário para iOS.

Sempre que seu código precisa para fazer referência a um controle de interface do usuário, como um rótulo ou um botão, lembre-se de incluir a seguinte instrução using:

```csharp
using UIKit;
```

Todos os controles discutidos neste capítulo estão no namespace UIKit, e cada nome de classe de controle de usuário tem o `UI` prefixo.

Você pode editar os controles de interface do usuário e layouts de três maneiras:

-  **[Designer do iOS Xamarin](~/ios/user-interface/designer/index.md)**  – designer de layout interno de uso do Xamarin para criar as telas. Clique duas vezes no storyboard ou os arquivos XIB para editar com o designer interno.
-  **Interface Builder do Xcode** – arrastar controles para os layouts de tela com o Interface Builder. Abra o storyboard ou um arquivo XIB no Xcode, clicando com o arquivo na **painel de soluções** e escolhendo **abrir com > Interface Builder do Xcode**.
-  **Usando o C#**  – controles também podem ser programaticamente construídos com o código e adicionados à hierarquia de exibição.

Novos arquivos de Storyboard e XIB podem ser adicionados clicando duas vezes em um projeto do iOS e escolhendo **Adicionar > novo arquivo...** .

Independentemente do método que você usa, as propriedades de controle e eventos podem ainda ser manipulados com C# em sua lógica de aplicativo.

## <a name="using-xamarin-ios-designer"></a>Usando o Designer do iOS Xamarin

Para começar a criar sua interface do usuário no iOS Designer, clique duas vezes em um arquivo de storyboard. Controles podem ser arrastados para a superfície de design do **caixa de ferramentas** conforme ilustrado abaixo:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

 [![](creating-ui-objects-images/image2b.png "Preenchimento da caixa de ferramentas")](creating-ui-objects-images/image2b.png#lightbox)
 
# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

 [![](creating-ui-objects-images/image2b-vs.png "Painel de caixa de ferramentas - Visual Studio")](creating-ui-objects-images/image2b.png#lightbox)
 
-----

Quando um controle for selecionado na superfície de design do **painel de propriedades** mostrará os atributos para o controle. O **Widget > identidade > nome** campo, que é populado na captura de tela abaixo, é usado como o *tomada* nome. Isso é como você pode referenciar o controle no C#:

 [![](creating-ui-objects-images/image3b.png "Painel do Widget de propriedades")](creating-ui-objects-images/image3b.png#lightbox)

Para aprofundar em usando o designer do iOS, consulte a [Introdução ao Designer do iOS](~/ios/user-interface/designer/introduction.md) guia.

## <a name="using-xcode-interface-builder"></a>Usando o Xcode Interface Builder

Se você não estiver familiarizado com o uso do construtor de Interface, consulte da Apple [Interface Builder](https://developer.apple.com/xcode/interface-builder/) documentos.

Para abrir um Storyboard no Xcode, clique com botão direito para acessar o menu de contexto para o arquivo de storyboard e escolha Abrir com o **Xcode Interface Builder**:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

 [![](creating-ui-objects-images/imagexcode.png "Menu de contexto do storyboard - Xcode")](creating-ui-objects-images/imagexcode.png#lightbox)
 
# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](creating-ui-objects-images/imagexcode-vs.png "Menu de contexto do storyboard - Xcode")](creating-ui-objects-images/imagexcode-vs.png#lightbox)

-----

Controles podem ser arrastados para a superfície de Design do **biblioteca de objetos** ilustrado abaixo:

 [![](creating-ui-objects-images/image5a.png "Biblioteca de objetos do Xcode")](creating-ui-objects-images/image5a.png#lightbox)

Ao projetar sua interface do usuário com o Interface Builder, você deve criar uma **tomada** para cada controle que você deseja referenciar no C#. Isso é feito por meio da ativação do **Editor assistente** usando o centro **Editor** botão no botão de barra de ferramentas do Xcode:

 [![](creating-ui-objects-images/image6a.png "Botão Editor Assistente")](creating-ui-objects-images/image6a.png#lightbox)

Clique em um objeto de interface do usuário; em seguida **arrastar controle** no arquivo. h. Para * * controle arraste * *, mantenha pressionada a tecla control e em seguida, clique e mantenha sobre o objeto de interface do usuário que você está criando a saída (ou a ação) para. Manter pressionada a tecla Control enquanto você arrasta para o arquivo de cabeçalho. Concluir arrastando abaixo o `@interface` definição. Uma linha azul deve aparecer com uma legenda inserir tomada ou a coleção de saída, conforme ilustrado na captura de tela abaixo.

Quando você soltar o clique, você será solicitado a fornecer um nome para a saída, que será usado para criar um C# propriedade que pode ser referenciada no código:

 [![](creating-ui-objects-images/image8a.png "Criando uma saída")](creating-ui-objects-images/image8a.png#lightbox)

Para obter mais informações sobre como o Interface Builder do Xcode se integra com o Visual Studio para Mac, consulte a [geração de código Xib](~/ios/internals/xib-code-generation.md#generated) documento.

##  <a name="using-c"></a>Usando oC#

Se você decidir criar programaticamente um objeto de interface de usuário usando C# (em um modo de exibição ou controlador de exibição, por exemplo), siga estas etapas:

-  Declare um campo de nível de classe para o objeto de interface do usuário. Criar o próprio controle uma vez, nos `ViewDidLoad` por exemplo. O objeto, em seguida, pode ser referenciado em todo os métodos de ciclo de vida do controlador de exibição (por exemplo.
`ViewWillAppear`).
-  Criar um `CGRect` que define o quadro do controle (suas coordenadas X e Y na tela, bem como sua largura e altura). Você precisará certificar-se de que você tem um `using CoreGraphics` diretriz para isso.
-  Chame o construtor para criar e atribuir o controle.
-  Defina quaisquer propriedades ou manipuladores de eventos.
-  Chamar `Add()` para adicionar o controle para a hierarquia de exibição.

Aqui está um exemplo simples de criar uma `UILabel` em um controlador de exibição usando C#:

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

## <a name="using-c-and-storyboards"></a>Usando o C# e Storyboards

Quando os controladores de exibição são adicionados à superfície de Design, dois correspondente C# arquivos são criados no projeto. Neste exemplo, `ControlsViewController.cs` e `ControlsViewController.designer.cs` foram criadas automaticamente:

 [![](creating-ui-objects-images/image9b.png "Classe parcial do ViewController")](creating-ui-objects-images/image9b.png#lightbox)

O `MainViewController.cs` arquivo destina *seu código*. É aí que o `View` métodos de ciclo de vida, como `ViewDidLoad` e `ViewWillAppear` são implementadas e onde você pode adicionar seus próprios campos, métodos e propriedades.

O `ControlsViewController.designer.cs` é código gerado que contém uma classe parcial. Quando você nomeia um controle sobre o design de superfície no Visual Studio para Mac ou criar uma ação ou tomada no Xcode, uma propriedade correspondente ou o método parcial, é adicionado ao arquivo de designer (designer.cs). O código a seguir mostra um exemplo de código gerado para os dois botões e uma exibição de texto, em que um dos botões também tem um `TouchUpInside` eventos.

Esses elementos da classe parcial permitem que o seu código referenciar os controles e responder às ações que são declaradas na superfície de design:

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

O `designer.cs` arquivo não deve ser editado manualmente – o IDE (Visual Studio para Mac ou Visual Studio) é responsável por mantê-lo sincronizado com o Storyboard.

Quando os objetos de interface do usuário são adicionados por meio de programação para um `View` ou `ViewController`, instanciar e gerenciar as referências de objeto por conta própria e, portanto, nenhum arquivo de designer é necessário.



## <a name="related-links"></a>Links relacionados

- [Controles (amostra)](https://developer.xamarin.com/samples/Controls/)
