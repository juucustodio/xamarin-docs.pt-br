---
title: Criando objetos de Interface do usuário
ms.prod: xamarin
ms.assetid: 4D6B136C-744A-4936-8655-A77E62BA7A60
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 5229beb7a882049daf58d3a3e62da6fed25a1f48
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="creating-user-interface-objects"></a>Criando objetos de Interface do usuário

Grupos de Apple relacionadas das funções "estruturas", que correspondem aos namespaces xamarin. `UIKit` é o namespace que contém todos os controles de interface de usuário para iOS.

Sempre que seu código precisa para fazer referência a um controle de interface do usuário, como um rótulo ou botão, lembre-se de incluir o seguinte usando a instrução:

```csharp
using UIKit;
```


Todos os controles discutidos neste capítulo estão no namespace UIKit e nome de cada classe de controle de usuário tem o `UI` prefixo.

Você pode editar os controles de interface do usuário e layouts de três maneiras:

-  **[Xamarin iOS Designer](~/ios/user-interface/designer/index.md)**  – designer do layout interno do uso Xamarin projetar as telas. Clique duas vezes no storyboard ou arquivos XIB para editar com o designer interno.
-  **Construtor de Interface do Xcode** – arraste os controles para os layouts de tela com o construtor de Interface. Abra o storyboard ou o arquivo XIB no Xcode clicando com o arquivo no **solução preenchimento** e escolhendo **abrir com > Xcode Interface Builder**.
-  **Usando o c#** – controles também podem ser programaticamente construídos com o código e adicionados à hierarquia do modo de exibição.

Novos arquivos de Storyboard e XIB podem ser adicionados clicando duas vezes em um projeto de iOS e escolhendo **Adicionar > novo arquivo...** .

Independentemente do método que você usa, as propriedades de controle e eventos ainda podem ser manipulados com c# na lógica do seu aplicativo.

## <a name="using-xamarin-ios-designer"></a>Usando o Xamarin iOS Designer

Para começar a criar sua interface de usuário no iOS Designer, clique duas vezes em um arquivo de storyboard. Controles podem ser arrastados para a superfície de design do **caixa de ferramentas** conforme ilustrado abaixo:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

 [![](creating-ui-objects-images/image2b.png "Preenchimento da caixa de ferramentas")](creating-ui-objects-images/image2b.png#lightbox)
 
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

 [![](creating-ui-objects-images/image2b-vs.png "Preenchimento da caixa de ferramentas - Visual Studio")](creating-ui-objects-images/image2b.png#lightbox)
 
-----

Quando um controle está selecionado na superfície de design de **propriedades de preenchimento** mostrará os atributos para o controle. O **Widget > identidade > nome** campo, que é populado na captura de tela abaixo, é usado como o *tomada* nome. Isso é como você pode referenciar o controle no c#:

 [![](creating-ui-objects-images/image3b.png "Preenchimento de Widget de propriedades")](creating-ui-objects-images/image3b.png#lightbox)

Para se aprofundar usando o designer do iOS, consulte o [introdução para o Designer do iOS](~/ios/user-interface/designer/introduction.md) guia.

## <a name="using-xcode-interface-builder"></a>Usando o construtor Xcode Interface

Se você estiver familiarizado com usando o construtor de Interface, consulte da Apple [Interface construtor](https://developer.apple.com/xcode/interface-builder/) documentos.

Para abrir um Storyboard no Xcode, clique com botão direito para acessar o menu de contexto para o arquivo de storyboard e optar por abrir com o **Xcode Interface Builder**:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

 [![](creating-ui-objects-images/imagexcode.png "Menu de contexto de storyboard - Xcode")](creating-ui-objects-images/imagexcode.png#lightbox)
 
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](creating-ui-objects-images/imagexcode-vs.png "Menu de contexto de storyboard - Xcode")](creating-ui-objects-images/imagexcode-vs.png#lightbox)

-----

Controles podem ser arrastados para a superfície de Design do **biblioteca de objeto** ilustrado abaixo:

 [![](creating-ui-objects-images/image5a.png "Biblioteca de objetos do Xcode")](creating-ui-objects-images/image5a.png#lightbox)

Quando você cria sua interface do usuário com o construtor de Interface, você deve criar um **tomada** para cada controle que você deseja referenciar no c#. Isso é feito ao ativar o **Assistente Editor** usando o Centro de **Editor** botão do botão de barra de ferramentas Xcode:

 [![](creating-ui-objects-images/image6a.png "Botão de Assistente de Editor")](creating-ui-objects-images/image6a.png#lightbox)

Clique em um objeto de interface do usuário; em seguida, **controle arraste** no arquivo. h. Para * * controle arraste * *, mantenha pressionada a tecla de controle, em seguida, clique e mantenha sobre o objeto de interface de usuário que você está criando a (ou ação) para. Mantenha pressionada a tecla Control enquanto você arrasta para o arquivo de cabeçalho. Concluir arrastando abaixo o `@interface` definição. Uma linha azul deve aparecer com uma legenda tomada Insert ou uma coleção de tomada, conforme ilustrado na captura de tela abaixo.

Quando você liberar o clique você deverá fornecer um nome para a loja, o que será usado para criar uma propriedade em c# que pode ser referenciada no código:

 [![](creating-ui-objects-images/image8a.png "Criando uma tomada")](creating-ui-objects-images/image8a.png#lightbox)

Para obter mais informações sobre como o construtor de Interface do Xcode se integra com o Visual Studio para Mac, consulte o [geração de código Xib](~/ios/internals/xib-code-generation.md#generated) documento.

##  <a name="using-c"></a>Usando o c#

Se você optar por criar programaticamente um objeto de interface do usuário usando c# (em uma exibição ou controlador de exibição, por exemplo), siga estas etapas:

-  Declare um campo de nível de classe para o objeto de interface do usuário. Criar o controle em si vez, em `ViewDidLoad` por exemplo. O objeto pode ser referenciado em toda os métodos de ciclo de vida do controlador de exibição (por exemplo.
`ViewWillAppear`).
-  Criar um `CGRect` que define o quadro do controle (suas coordenadas X e Y na tela, bem como sua largura e altura). Você precisará certificar-se de que você tem um `using CoreGraphics` diretiva para isso.
-  Chame o construtor para criar e atribuir o controle.
-  Defina quaisquer propriedades ou manipuladores de eventos.
-  Chamar `Add()` para adicionar o controle para a hierarquia do modo de exibição.

Aqui está um exemplo simples de criar um `UILabel` em um controlador de exibição usando o c#:

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

## <a name="using-c-and-storyboards"></a>Usando o c# e Storyboards

Quando exibir controladores são adicionados à superfície de Design, dois c# arquivos correspondentes são criados no projeto. Neste exemplo, `ControlsViewController.cs` e `ControlsViewController.designer.cs` foram criadas automaticamente:

 [![](creating-ui-objects-images/image9b.png "Classe parcial ViewController")](creating-ui-objects-images/image9b.png#lightbox)

O `MainViewController.cs` arquivo destina *seu código*. Isso é onde o `View` métodos de ciclo de vida como `ViewDidLoad` e `ViewWillAppear` são implementados e onde você pode adicionar seus próprios campos, métodos e propriedades.

O `ControlsViewController.designer.cs` é código gerado que contém uma classe parcial. Quando você nomeia um controle sobre o design de superfície no Visual Studio para Mac, ou criar uma loja ou ação em Xcode, uma propriedade correspondente ou um método parcial, é adicionada ao arquivo de designer (designer.cs). O código a seguir mostra um exemplo de código gerado para dois botões e um modo de exibição de texto, onde um dos botões também tem um `TouchUpInside` eventos.

Esses elementos de classe parcial permitem que o seu código referenciar os controles e responder às ações que são declaradas na superfície de design:

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

O `designer.cs` arquivo não deve ser editado manualmente – o IDE (Visual Studio para Mac ou o Visual Studio) é responsável por mantê-lo sincronizado com o Storyboard.

Quando os objetos de interface do usuário são adicionados por meio de programação para uma `View` ou `ViewController`, instanciar e gerenciar as referências de objeto por conta própria e, portanto, nenhum arquivo de designer é necessário.



## <a name="related-links"></a>Links relacionados

- [Controles (exemplo)](https://developer.xamarin.com/samples/Controls/)
