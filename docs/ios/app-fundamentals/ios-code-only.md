---
title: Criando interfaces de usuário do iOS no código no Xamarin. iOS
description: Este documento descreve como usar o código para criar uma interface do usuário para um aplicativo Xamarin. iOS. Ele aborda os controladores de exibição, a criação de uma hierarquia de exibição, a manipulação de uma rotação e muito mais.
ms.prod: xamarin
ms.assetid: 7CB1FEAE-0BB3-4CDC-9076-5BD555003F1D
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/03/2018
ms.openlocfilehash: edd49cc891a86d3323bab319ab811e85f9148640
ms.sourcegitcommit: 952db1983c0bc373844c5fbe9d185e04a87d8fb4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "86997092"
---
# <a name="creating-ios-user-interfaces-in-code-in-xamarinios"></a>Criando interfaces de usuário do iOS no código no Xamarin. iOS

A interface do usuário de um aplicativo iOS é como uma vitrine – o aplicativo normalmente Obtém uma janela, mas pode preencher a janela com quantos objetos forem necessários, e os objetos e as disposições podem ser alterados dependendo do que o aplicativo deseja exibir. Os objetos nesse cenário – as coisas que o usuário vê – são chamados de exibições. Para criar uma única tela em um aplicativo, as exibições são empilhadas umas sobre as outras em uma hierarquia de exibição de conteúdo e a hierarquia é gerenciada por um único controlador de exibição. Aplicativos com várias telas têm várias hierarquias de exibição de conteúdo, cada uma com seu próprio controlador de exibição; o aplicativo coloca as exibições na janela para criar uma hierarquia de exibição de conteúdo diferente com base na tela na qual o usuário está.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

O diagrama a seguir ilustra as relações entre a janela, exibições, subexibições e controlador de exibição que levam a interface do usuário para a tela do dispositivo:

[![Este diagrama ilustra as relações entre a janela, as exibições, as subexibições e o controlador de exibição](ios-code-only-images/image9.png)](ios-code-only-images/image9.png#lightbox)

Essas hierarquias de exibição podem ser construídas usando o [Xamarin designer para IOS](~/ios/user-interface/designer/index.md) no Visual Studio, no entanto, é bom ter um entendimento fundamental de como trabalhar inteiramente no código. Este artigo apresenta alguns pontos básicos para começar a execução com o desenvolvimento de interface do usuário somente de código.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

O diagrama a seguir ilustra as relações entre a janela, exibições, subexibições e controlador de exibição que levam a interface do usuário para a tela do dispositivo:

[![Este diagrama ilustra as relações entre a janela, as exibições, as subexibições e o controlador de exibição](ios-code-only-images/image9.png)](ios-code-only-images/image9.png#lightbox)

Essas hierarquias de exibição podem ser construídas usando o [Xamarin designer para IOS](~/ios/user-interface/designer/index.md) em Visual Studio para Mac, no entanto, é bom ter um entendimento fundamental de como trabalhar inteiramente no código. Este artigo apresenta alguns pontos básicos para começar a execução com o desenvolvimento de interface do usuário somente de código.

-----

## <a name="creating-a-code-only-project"></a>Criando um projeto somente de código

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

## <a name="ios-blank-project-template"></a>modelo de projeto em branco do iOS

Primeiro, crie um projeto do iOS no Visual Studio usando o **arquivo > novo projeto > Visual C# > iPhone & iPad > o aplicativo do IOS (Xamarin)** , mostrado abaixo:

[![Caixa de diálogo novo projeto](ios-code-only-images/blankapp.w157-sml.png)](ios-code-only-images/blankapp.w157.png#lightbox)

Em seguida, selecione o modelo de projeto de **aplicativo em branco** :

[![Caixa de diálogo Selecionar um modelo](ios-code-only-images/blankapp-2.w157-sml.png)](ios-code-only-images/blankapp-2.w157.png#lightbox)

O modelo de projeto vazio adiciona quatro arquivos ao projeto:

[![Arquivos de projeto](ios-code-only-images/empty-project.w157-sml.png "Arquivos de projeto")](ios-code-only-images/empty-project.w157.png#lightbox)

1. **AppDelegate.cs** -contém uma `UIApplicationDelegate` subclasse, `AppDelegate` , que é usada para manipular eventos de aplicativo do Ios. A janela do aplicativo é criada no `AppDelegate` `FinishedLaunching` método do.
1. **Main.cs** -contém o ponto de entrada para o aplicativo, que especifica a classe para o `AppDelegate` .
1. **Info. plist** -arquivo de lista de propriedades que contém informações de configuração de aplicativo.
1. **Intitulars. plist** – arquivo de lista de propriedades que contém informações sobre os recursos e as permissões do aplicativo.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

## <a name="ios-templates"></a>modelos do iOS

Visual Studio para Mac não fornece um modelo vazio. Todos os modelos vêm com suporte a Storyboard, que a Apple recomenda como a principal maneira de criar uma interface do usuário. No entanto, é possível criar sua interface do usuário completamente no código.

As etapas a seguir orientam você durante a remoção do storyboard de um aplicativo:

1. Use o modelo de aplicativo de modo de exibição único para criar um novo projeto do iOS:

    [![Usar o modelo de aplicativo de modo de exibição único](ios-code-only-images/single-view-app.png)](ios-code-only-images/single-view-app.png#lightbox)

1. Exclua `Main.Storyboard` os `ViewController.cs` arquivos e. Não **exclua** o `LaunchScreen.Storyboard` . O controlador de exibição deve ser excluído, pois é o code-behind do controlador de exibição criado no storyboard:
1. Certifique-se de selecionar **excluir** na caixa de diálogo pop-up:

    [![Selecione Excluir na caixa de diálogo pop-up](ios-code-only-images/delete.png)](ios-code-only-images/delete.png#lightbox)

1. No info. plist, exclua as informações dentro da opção de **implantação informações de > interface principal** :

    [![Excluir as informações dentro da opção de interface principal](ios-code-only-images/main-interface.png)](ios-code-only-images/main-interface.png#lightbox)

1. Por fim, adicione o seguinte código ao seu `FinishedLaunching` método na classe AppDelegate:

    ```csharp
    public override bool FinishedLaunching(UIApplication app, NSDictionary options)
    {
        // create a new window instance based on the screen size
        Window = new UIWindow(UIScreen.MainScreen.Bounds);

        // make the window visible
        Window.MakeKeyAndVisible();

        return true;
    }
    ```

O código que foi adicionado ao `FinishedLaunching` método na etapa 5 acima é a quantidade mínima de código necessária para criar uma janela para seu aplicativo Ios.

-----

os aplicativos iOS são criados usando o [padrão MVC](~/ios/get-started/hello-ios-multiscreen/hello-ios-multiscreen-deepdive.md#model-view-controller-mvc). A primeira tela que um aplicativo exibe é criada a partir do controlador de exibição raiz da janela. Confira o guia de [saudação do IOS](~/ios/get-started/hello-ios-multiscreen/index.md) para obter mais detalhes sobre o próprio padrão MVC.

A implementação para `AppDelegate` adicionado pelo modelo cria a janela do aplicativo, da qual há apenas um para cada aplicativo do IOS e torna-o visível com o código a seguir:

```csharp
public class AppDelegate : UIApplicationDelegate
{
    public override UIWindow Window
            {
                get;
                set;
            }

    public override bool FinishedLaunching(UIApplication app, NSDictionary options)
    {
        // create a new window instance based on the screen size
        Window = new UIWindow(UIScreen.MainScreen.Bounds);

        // make the window visible
        Window.MakeKeyAndVisible();

        return true;
    }
}
```

Se você fosse executar esse aplicativo agora, provavelmente receberia uma exceção gerada dizendo isso `Application windows are expected to have a root view controller at the end of application launch` . Vamos adicionar um controlador e torná-lo o controlador de exibição raiz do aplicativo.

## <a name="adding-a-controller"></a>Adicionando um controlador

Seu aplicativo pode conter muitos controladores de exibição, mas ele precisa ter um controlador de exibição raiz para controlar todos os controladores de exibição.  Adicione um controlador à janela criando uma `UIViewController` instância e configurando-a para a `Window.RootViewController` Propriedade:

```csharp
public class AppDelegate : UIApplicationDelegate
{
    // class-level declarations

    public override UIWindow Window
    {
        get;
        set;
    }

    public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
    {
        // create a new window instance based on the screen size
        Window = new UIWindow(UIScreen.MainScreen.Bounds);

        var controller = new UIViewController();
        controller.View.BackgroundColor = UIColor.LightGray;

        Window.RootViewController = controller;

        // make the window visible
        Window.MakeKeyAndVisible();

        return true;
    }
}
```

Cada controlador tem uma exibição associada, que é acessível a partir da `View` propriedade. O código acima altera a propriedade da exibição `BackgroundColor` para para `UIColor.LightGray` que ela fique visível, conforme mostrado abaixo:

 [![O plano de fundo da exibição é um cinza claro visível](ios-code-only-images/image1.png)](ios-code-only-images/image1.png#lightbox)

Poderíamos definir qualquer `UIViewController` subclasse como a `RootViewController` dessa maneira também, incluindo controladores de UIKit, bem como aqueles que nos escrevemos. Por exemplo, o código a seguir adiciona um `UINavigationController` como `RootViewController` :

```csharp
public class AppDelegate : UIApplicationDelegate
{
    // class-level declarations

    public override UIWindow Window
    {
        get;
        set;
    }

    public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
    {
        // create a new window instance based on the screen size
        Window = new UIWindow(UIScreen.MainScreen.Bounds);

        var controller = new UIViewController();
        controller.View.BackgroundColor = UIColor.LightGray;
        controller.Title = "My Controller";

        var navController = new UINavigationController(controller);

        Window.RootViewController = navController;

        // make the window visible
        Window.MakeKeyAndVisible();

        return true;
    }
}
```

Isso produz o controlador aninhado dentro do controlador de navegação, conforme mostrado abaixo:

 [![O controlador aninhado dentro do controlador de navegação](ios-code-only-images/image2.png)](ios-code-only-images/image2.png#lightbox)

## <a name="creating-a-view-controller"></a>Criando um controlador de exibição

Agora que vimos como adicionar um controlador como o `RootViewController` da janela, vamos ver como criar um controlador de exibição personalizado no código.

Adicione uma nova classe chamada `CustomViewController` , conforme mostrado abaixo:

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![Adicione uma nova classe chamada CustomViewController](ios-code-only-images/customviewcontroller.w157-sml.png)](ios-code-only-images/customviewcontroller.w157.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

[![Adicione uma nova classe chamada CustomViewController](ios-code-only-images/new-file.png)](ios-code-only-images/new-file.png#lightbox)

-----

A classe deve herdar de `UIViewController` , que está no `UIKit` namespace, conforme mostrado:

```csharp
using System;
using UIKit;

namespace CodeOnlyDemo
{
    class CustomViewController : UIViewController
    {
    }
}
```

## <a name="initializing-the-view"></a>Inicializando a exibição

`UIViewController`contém um método chamado `ViewDidLoad` que é chamado quando o controlador de exibição é carregado pela primeira vez na memória. Esse é um local apropriado para fazer a inicialização do modo de exibição, como definir suas propriedades.

Por exemplo, o código a seguir adiciona um botão e um manipulador de eventos para enviar por push um novo controlador de exibição para a pilha de navegação quando o botão é pressionado:

```csharp
using System;
using CoreGraphics;
using UIKit;

namespace CodyOnlyDemo
{
    public class CustomViewController : UIViewController
    {
        public CustomViewController ()
        {
        }

        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            View.BackgroundColor = UIColor.White;
            Title = "My Custom View Controller";

            var btn = UIButton.FromType (UIButtonType.System);
            btn.Frame = new CGRect (20, 200, 280, 44);
            btn.SetTitle ("Click Me", UIControlState.Normal);

            var user = new UIViewController ();
            user.View.BackgroundColor = UIColor.Magenta;

            btn.TouchUpInside += (sender, e) => {
                this.NavigationController.PushViewController (user, true);
            };

            View.AddSubview (btn);

        }
    }
}
```

Para carregar esse controlador em seu aplicativo e demonstrar a navegação simples, crie uma nova instância do `CustomViewController` . Crie um novo controlador de navegação, passe a sua instância do controlador de exibição e defina o novo controlador de navegação para a janela `RootViewController` no `AppDelegate` como antes:

```csharp
var cvc = new CustomViewController ();

var navController = new UINavigationController (cvc);

Window.RootViewController = navController;
```

Agora, quando o aplicativo é carregado, o `CustomViewController` é carregado dentro de um controlador de navegação:

 [![O CustomViewController é carregado dentro de um controlador de navegação](ios-code-only-images/customvc.png)](ios-code-only-images/customvc.png#lightbox)

Clicar no botão _enviará por push_ um novo controlador de exibição para a pilha de navegação:

[![Um novo controlador de exibição enviado para a pilha de navegação](ios-code-only-images/customvca.png)](ios-code-only-images/customvca.png#lightbox)

## <a name="building-the-view-hierarchy"></a>Criando a hierarquia de exibição

No exemplo acima, começamos a criar uma interface do usuário no código adicionando um botão ao controlador de exibição.

as interfaces de usuário do iOS são compostas de uma hierarquia de exibição. Exibições adicionais, como rótulos, botões, controles deslizantes, etc., são adicionadas como subexibições de alguma exibição pai.

Por exemplo, vamos editar o `CustomViewController` para criar uma tela de logon na qual o usuário possa inserir um nome de usuário e uma senha. A tela consistirá em dois campos de texto e um botão.

### <a name="adding-the-text-fields"></a>Adicionando os campos de texto

Primeiro, remova o botão e manipulador de eventos que foi adicionado na seção [inicializando a exibição](#initializing-the-view) .

Adicione um controle para o nome de usuário criando e inicializando um `UITextField` e, em seguida, adicionando-o à hierarquia de exibição, conforme mostrado abaixo:

```csharp
class CustomViewController : UIViewController
{
    UITextField usernameField;

    public override void ViewDidLoad()
    {
        base.ViewDidLoad();

        View.BackgroundColor = UIColor.Gray;

        nfloat h = 31.0f;
        nfloat w = View.Bounds.Width;

        usernameField = new UITextField
        {
            Placeholder = "Enter your username",
            BorderStyle = UITextBorderStyle.RoundedRect,
            Frame = new CGRect(10, 82, w - 20, h)
        };

        View.AddSubview(usernameField);
    }
}
```

Quando criamos `UITextField` , definimos a `Frame` propriedade para definir seu local e tamanho. No iOS, a coordenada 0, 0 está no canto superior esquerdo com + x à direita e + y para baixo. Depois de definir o `Frame` junto com algumas outras propriedades, chamamos `View.AddSubview` para adicionar o `UITextField` à hierarquia de exibição. Isso torna a `usernameField` subexibição da `UIView` instância `View` referenciada pela propriedade. Uma subexibição é adicionada com uma ordem z que é maior do que sua exibição pai e, portanto, aparece na frente da exibição pai na tela.

O aplicativo com o `UITextField` incluído é mostrado abaixo:

 [![O aplicativo com o UITextField incluído](ios-code-only-images/image4.png)](ios-code-only-images/image4.png#lightbox)

Podemos adicionar um `UITextField` para a senha de maneira semelhante, apenas desta vez, definimos a `SecureTextEntry` propriedade como true, conforme mostrado abaixo:

```csharp
public class CustomViewController : UIViewController
{
    UITextField usernameField, passwordField;
    public override void ViewDidLoad()
    {
       // keep the code the username UITextField
        passwordField = new UITextField
        {
            Placeholder = "Enter your password",
            BorderStyle = UITextBorderStyle.RoundedRect,
            Frame = new CGRect(10, 114, w - 20, h),
            SecureTextEntry = true
        };

      View.AddSubview(usernameField);
      View.AddSubview(passwordField);
   }
}

```

`SecureTextEntry = true`A configuração oculta o texto inserido no `UITextField` pelo usuário, conforme mostrado abaixo:

 [![Definir SecureTextEntry true oculta o texto digitado pelo usuário](ios-code-only-images/image4a.png)](ios-code-only-images/image4a.png#lightbox)

### <a name="adding-the-button"></a>Adicionando o botão

Em seguida, adicionaremos um botão para que o usuário possa enviar o nome de usuário e a senha. O botão é adicionado à hierarquia de exibição como qualquer outro controle, passando-o como um argumento para o método da exibição pai `AddSubview` novamente.

O código a seguir adiciona o botão e registra um manipulador de eventos para o `TouchUpInside` evento:

```csharp
var submitButton = UIButton.FromType (UIButtonType.RoundedRect);

submitButton.Frame = new CGRect (10, 170, w - 20, 44);
submitButton.SetTitle ("Submit", UIControlState.Normal);

submitButton.TouchUpInside += (sender, e) => {
    Console.WriteLine ("Submit button pressed");
};

View.AddSubview(submitButton);
```

Com isso em vigor, a tela de logon aparecerá como mostrado abaixo:

 [![A tela de logon](ios-code-only-images/image5.png)](ios-code-only-images/image5.png#lightbox)

Ao contrário das versões anteriores do iOS, o plano de fundo do botão padrão é transparente. Alterar a propriedade do botão `BackgroundColor` altera isso:

```csharp
submitButton.BackgroundColor = UIColor.White;
```

Isso resultará em um botão quadrado em vez do botão arredondado típico. Para obter a borda arredondada, use o seguinte trecho:

```csharp
submitButton.Layer.CornerRadius = 5f;
```

Com essas alterações, a exibição terá a seguinte aparência:

[![Um exemplo de execução da exibição](ios-code-only-images/image6.png)](ios-code-only-images/image6.png#lightbox)

## <a name="adding-multiple-views-to-the-view-hierarchy"></a>Adicionando várias exibições à hierarquia de exibição

o iOS fornece uma instalação para adicionar várias exibições à hierarquia de exibição usando o `AddSubviews` .

```csharp
View.AddSubviews(new UIView[] { usernameField, passwordField, submitButton });
```

## <a name="adding-button-functionality"></a>Adicionando funcionalidade de botão

Quando um botão é clicado, os usuários esperam que algo aconteça. Por exemplo, um alerta é mostrado ou a navegação é executada em outra tela.

Vamos adicionar um código para enviar por push um segundo controlador de exibição para a pilha de navegação.

Primeiro, crie o segundo controlador de exibição:

```csharp
var loginVC = new UIViewController () { Title = "Login Success!"};
loginVC.View.BackgroundColor = UIColor.Purple;
```

Em seguida, adicione a funcionalidade ao `TouchUpInside` evento:

```csharp
submitButton.TouchUpInside += (sender, e) => {
                this.NavigationController.PushViewController (loginVC, true);
            };
```

A navegação é ilustrada abaixo:

[![A navegação é ilustrada neste gráfico](ios-code-only-images/navigation.png)](ios-code-only-images/navigation.png#lightbox)

Observe que, por padrão, quando você usa um controlador de navegação, o iOS dá ao aplicativo uma barra de navegação e um botão voltar para permitir que você passe de volta pela pilha.

## <a name="iterating-through-the-view-hierarchy"></a>Iterando pela hierarquia de exibição

É possível iterar pela hierarquia de subexibição e escolher qualquer exibição específica. Por exemplo, para localizar cada `UIButton` um e dar a esse botão um diferente `BackgroundColor` , o trecho a seguir pode ser usado

```csharp
foreach(var subview in View.Subviews)
{
    if (subview is UIButton)
    {
         var btn = subview as UIButton;
         btn.BackgroundColor = UIColor.Green;
    }
}
```

No entanto, isso não funcionará se a exibição que está sendo iterada for `UIView` como todas as exibições retornarão como sendo os `UIView` objetos adicionados à própria exibição pai herdadas `UIView` .

## <a name="handling-rotation"></a>Manipulação de rotação

Se o usuário girar o dispositivo para paisagem, os controles não são redimensionados adequadamente, como mostra a captura de tela a seguir:

[![Se o usuário girar o dispositivo para paisagem, os controles não são redimensionados adequadamente](ios-code-only-images/image7.png)](ios-code-only-images/image7.png#lightbox)

Uma maneira de corrigir isso é definindo a `AutoresizingMask` propriedade em cada exibição. Nesse caso, queremos que os controles Alonguem horizontalmente, portanto, definimos cada um `AutoresizingMask` . O exemplo a seguir é para `usernameField` , mas o mesmo precisa ser aplicado a cada gadget na hierarquia de exibição.

```csharp
usernameField.AutoresizingMask = UIViewAutoresizing.FlexibleWidth;
```

Agora, quando girarmos o dispositivo ou o simulador, tudo se estenderá para preencher o espaço adicional, como mostrado abaixo:

[![Todos os controles são ampliados para preencher o espaço adicional](ios-code-only-images/image8.png)](ios-code-only-images/image8.png#lightbox)

## <a name="creating-custom-views"></a>Criando exibições personalizadas

Além de usar controles que fazem parte do UIKit, os modos de exibição personalizados também podem ser usados. Uma exibição personalizada pode ser criada herdando-se de `UIView` e substituindo `Draw` . Vamos criar uma exibição personalizada e adicioná-la à hierarquia de exibição para demonstrar.

### <a name="inheriting-from-uiview"></a>Herdando de UIView

A primeira coisa que precisamos fazer é criar uma classe para a exibição personalizada. Faremos isso usando o modelo de **classe** no Visual Studio para adicionar uma classe vazia chamada `CircleView` . A classe base deve ser definida como `UIView` , que recuperamos está no `UIKit` namespace. Também precisaremos do `System.Drawing` namespace. Os outros vários `System.*` namespaces não serão usados neste exemplo, portanto, sinta-se à vontade para removê-los.

A classe deve ter esta aparência:

```csharp
using System;

namespace CodeOnlyDemo
{
    class CircleView : UIView
    {
    }
}
```

### <a name="drawing-in-a-uiview"></a>Desenho em um UIView

Cada `UIView` tem um `Draw` método que é chamado pelo sistema quando ele precisa ser desenhado. `Draw`Nunca deve ser chamado diretamente. Ele é chamado pelo sistema durante o processamento do loop de execução. Na primeira vez que o loop de execução é adicionado após uma exibição ser adicionada à hierarquia de exibição, seu `Draw` método é chamado. As chamadas subsequentes `Draw` ocorrem quando a exibição é marcada como precisa ser desenhada chamando `SetNeedsDisplay` ou `SetNeedsDisplayInRect` na exibição.

Podemos adicionar o código de desenho à nossa exibição adicionando esse código dentro do `Draw` método substituído, conforme mostrado abaixo:

```csharp
public override void Draw(CGRect rect)
{
    base.Draw(rect);

    //get graphics context
    using (var g = UIGraphics.GetCurrentContext())
    {
        // set up drawing attributes
        g.SetLineWidth(10.0f);
        UIColor.Green.SetFill();
        UIColor.Blue.SetStroke();

        // create geometry
        var path = new CGPath();
        path.AddArc(Bounds.GetMidX(), Bounds.GetMidY(), 50f, 0, 2.0f * (float)Math.PI, true);

        // add geometry to graphics context and draw
        g.AddPath(path);
        g.DrawPath(CGPathDrawingMode.FillStroke);
    }
}
```

Como `CircleView` é um `UIView` , também podemos definir `UIView` Propriedades. Por exemplo, podemos definir o `BackgroundColor` no construtor:

```csharp
public CircleView()
{
    BackgroundColor = UIColor.White;
}
```

Para usar o `CircleView` que acabamos de criar, podemos adicioná-lo como uma subexibição à hierarquia de exibição em um controlador existente, como fizemos com o `UILabels` e `UIButton` anterior, ou podemos carregá-lo como a exibição de um novo controlador. Vamos fazer o último.

### <a name="loading-a-view"></a>Carregando uma exibição

 `UIViewController`tem um método chamado `LoadView` que é chamado pelo controlador para criar sua exibição. Esse é um local apropriado para criar uma exibição e atribuí-la à propriedade do controlador `View` .

Primeiro, precisamos de um controlador, portanto, crie uma nova classe vazia chamada `CircleController` .

Em `CircleController` Adicionar o código a seguir para definir o `View` como um `CircleView` (você não deve chamar a `base` implementação em sua substituição):

```csharp
using UIKit;

namespace CodeOnlyDemo
{
    class CircleController : UIViewController
    {
        CircleView view;

        public override void LoadView()
        {
            view = new CircleView();
            View = view;
        }
    }
}
```

Por fim, precisamos apresentar o controlador em tempo de execução. Vamos fazer isso adicionando um manipulador de eventos no botão enviar que adicionamos anteriormente, da seguinte maneira:

```csharp
submitButton.TouchUpInside += delegate
{
    Console.WriteLine("Submit button clicked");

    //circleController is declared as class variable
    circleController = new CircleController();
    PresentViewController(circleController, true, null);
};
```

Agora, quando executamos o aplicativo e tocamos no botão enviar, o novo modo de exibição com um círculo é exibido:

[![A nova exibição com um círculo é exibida](ios-code-only-images/circles.png)](ios-code-only-images/circles.png#lightbox)

## <a name="creating-a-launch-screen"></a>Criando uma tela de inicialização

Uma [tela de inicialização](~/ios/app-fundamentals/images-icons/launch-screens.md) é exibida quando seu aplicativo é iniciado como uma maneira de exibir para os usuários que ele está respondendo. Como uma tela de inicialização é exibida quando seu aplicativo está sendo carregado, ela não pode ser criada no código, pois o aplicativo ainda está sendo carregado na memória.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Quando você cria um projeto do iOS no Visual Studio, uma tela de inicialização é fornecida para você na forma de um arquivo. xib, que pode ser encontrado na pasta **recursos** dentro do seu projeto.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Quando você cria um projeto do iOS no Visual Studio para Mac, uma tela de inicialização é fornecida para você na forma de um arquivo de storyboard.

-----

Isso pode ser editado clicando duas vezes nele e abrindo-o no designer do iOS.

A Apple recomenda que um arquivo. xib ou storyboard seja usado para aplicativos destinados ao iOS 8 ou posterior, quando você inicia um arquivo no designer do iOS, você usará as classes de tamanho e o layout automático para adaptar seu layout para que ele fique bom e seja exibido corretamente para todos os tamanhos de dispositivo. Uma imagem de inicialização estática pode ser usada além de um. xib ou Storyboard para permitir o suporte a aplicativos destinados a versões anteriores.

Para obter mais informações sobre como criar uma tela de inicialização, consulte os documentos abaixo:

- [Criando uma tela de inicialização usando um. xib](https://github.com/xamarin/recipes/tree/master/Recipes/ios/general/templates/launchscreen-xib)
- [Gerenciando telas de inicialização com storyboards](~/ios/app-fundamentals/images-icons/launch-screens.md)

> [!IMPORTANT]
> A partir do iOS 9, a Apple recomenda que os storyboards sejam usados como o principal método de criação de uma tela de inicialização.

### <a name="creating-a-launch-image-for-pre-ios-8-applications"></a>Criando uma imagem de inicialização para aplicativos anteriores ao iOS 8

Uma imagem estática pode ser usada além de uma tela de inicialização. xib ou storyboard se o aplicativo tiver como alvo versões anteriores ao iOS 8.

Essa imagem estática pode ser definida no arquivo info. plist ou como um catálogo de ativos (para iOS 7) em seu aplicativo. Você precisará fornecer imagens separadas para cada tamanho de dispositivo (320x480, 640x960, 640x1136) em que seu aplicativo pode ser executado. Para obter mais informações sobre tamanhos de tela de inicialização, veja o guia de [imagens da tela de inicialização](~/ios/app-fundamentals/images-icons/launch-screens.md) .

> [!IMPORTANT]
> Se seu aplicativo não tiver tela de inicialização, você poderá notar que ele não se ajusta totalmente à tela. Se esse for o caso, você deve se certificar de incluir, pelo menos, uma imagem 640x1136 chamada `Default-568@2x.png` para seu info. plist.

## <a name="summary"></a>Resumo

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Este artigo abordou como desenvolver aplicativos iOS programaticamente no Visual Studio. Vimos como criar um projeto a partir de um modelo de projeto vazio, discutindo como criar e adicionar um controlador de exibição raiz à janela. Em seguida, mostramos como usar controles de UIKit para criar uma hierarquia de exibição em um controlador para desenvolver uma tela de aplicativo. Em seguida, examinamos como fazer com que as exibições sejam modeladas adequadamente em orientações diferentes e vimos como criar uma exibição personalizada por meio de subclasse `UIView` , bem como carregar a exibição em um controlador. Por fim, exploramos como adicionar uma tela de inicialização a um aplicativo.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Este artigo abordou como desenvolver aplicativos iOS programaticamente no Visual Studio para Mac. Vimos como criar um projeto de um modelo de exibição única, discutindo como criar e adicionar um controlador de exibição raiz à janela. Em seguida, mostramos como usar controles de UIKit para criar uma hierarquia de exibição em um controlador para desenvolver uma tela de aplicativo. Em seguida, examinamos como fazer com que as exibições sejam modeladas adequadamente em orientações diferentes e vimos como criar uma exibição personalizada por meio de subclasse `UIView` , bem como carregar a exibição em um controlador. Por fim, exploramos como adicionar uma tela de inicialização a um aplicativo.

-----

## <a name="related-links"></a>Links Relacionados

- [SimpleLogin (exemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/simplelogin)
