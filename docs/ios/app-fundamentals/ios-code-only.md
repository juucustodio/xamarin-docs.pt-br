---
title: Criando Interfaces do usuário do iOS no código no xamarin. IOS
description: Este documento descreve como usar o código para criar uma interface do usuário para um aplicativo xamarin. IOS. Ele aborda os controladores de exibição, criação de uma hierarquia de exibição, tratamento de uma rotação e muito mais.
ms.prod: xamarin
ms.assetid: 7CB1FEAE-0BB3-4CDC-9076-5BD555003F1D
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/03/2018
ms.openlocfilehash: 28475df4baa225cc9a608607be6ed673ad0e6e8a
ms.sourcegitcommit: 650458de1d362cd7de174cacef7838f0e74426f3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/15/2019
ms.locfileid: "57981699"
---
# <a name="creating-ios-user-interfaces-in-code-in-xamarinios"></a>Criando interfaces do usuário do iOS no código no xamarin. IOS

A interface do usuário de um aplicativo iOS é como uma vitrine – o aplicativo normalmente obtém uma janela, mas ele pode preencher a janela de com como muitos objetos à medida que ele precisa, e as disposições e objetos podem ser alteradas dependendo do que o aplicativo quer exibir. Os objetos nesse cenário – as coisas que o usuário vê – são chamados de exibições. Para criar uma única tela em um aplicativo, as exibições são empilhadas umas sobre as outras em uma hierarquia de exibição de conteúdo e a hierarquia é gerenciada por um único controlador de exibição. Aplicativos com várias telas têm várias hierarquias de exibição de conteúdo, cada uma com seu próprio controlador de exibição; o aplicativo coloca as exibições na janela para criar uma hierarquia de exibição de conteúdo diferente com base na tela na qual o usuário está.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

O diagrama a seguir ilustra as relações entre a janela, exibições, subexibições e controlador de exibição que levam a interface do usuário para a tela do dispositivo:

[![](ios-code-only-images/image9.png "Este diagrama ilustra as relações entre a janela, exibições, subexibições e controlador de exibição")](ios-code-only-images/image9.png#lightbox)

Essas hierarquias de exibição podem ser criadas usando o [Designer de Xamarin para iOS](~/ios/user-interface/designer/index.md) no Visual Studio, no entanto é bom ter um entendimento fundamental de como trabalhar inteiramente no código. Este artigo explica alguns pontos básicos para colocá-lo em funcionamento com o desenvolvimento de interface de usuário somente código.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

O diagrama a seguir ilustra as relações entre a janela, exibições, subexibições e controlador de exibição que levam a interface do usuário para a tela do dispositivo:

[![](ios-code-only-images/image9.png "Este diagrama ilustra as relações entre a janela, exibições, subexibições e controlador de exibição")](ios-code-only-images/image9.png#lightbox)

Essas hierarquias de exibição podem ser criadas usando o [Designer de Xamarin para iOS](~/ios/user-interface/designer/index.md) no Visual Studio para Mac, no entanto é bom ter um entendimento fundamental de como trabalhar inteiramente no código. Este artigo explica alguns pontos básicos para colocá-lo em funcionamento com o desenvolvimento de interface de usuário somente código.

-----

## <a name="creating-a-code-only-project"></a>Criando um projeto somente de código

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="ios-blank-project-template"></a>modelo de projeto em branco do iOS

Primeiro, crie um projeto do iOS no Visual Studio usando o **arquivo > Novo projeto > Visual c# > iPhone & iPad > aplicativo iOS (Xamarin)** projeto, como mostrado abaixo:

[![Caixa de diálogo Novo projeto](ios-code-only-images/blankapp.w157-sml.png)](ios-code-only-images/blankapp.w157.png#lightbox)

Em seguida, selecione a **aplicativo em branco** modelo de projeto:

[![Selecione uma caixa de diálogo do modelo](ios-code-only-images/blankapp-2.w157-sml.png)](ios-code-only-images/blankapp-2.w157.png#lightbox)

O modelo de projeto vazio adiciona 4 arquivos ao projeto:

[![Arquivos de projeto](ios-code-only-images/empty-project.w157-sml.png "arquivos de projeto")](ios-code-only-images/empty-project.w157.png#lightbox)

1. **AppDelegate.cs** -contém uma `UIApplicationDelegate` subclasse, `AppDelegate` , que é usado para manipular eventos de aplicativo do iOS. A janela do aplicativo é criada na `AppDelegate`do `FinishedLaunching` método.
1. **Main.CS** -contém o ponto de entrada para o aplicativo, que especifica a classe para o `AppDelegate` .
1. **Info. plist** -arquivo de lista de propriedade que contém informações de configuração do aplicativo.
1. **Entitlements. plist** – arquivo de lista de propriedade que contém informações sobre os recursos e as permissões do aplicativo.


# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

## <a name="ios-templates"></a>modelos de iOS


O Visual Studio para Mac não fornece um modelo vazio. Todos os modelos são fornecidos com o suporte a Storyboard, que a Apple recomenda como a principal maneira de criar uma interface do usuário. No entanto, é possível criar sua interface do usuário completamente no código.

As etapas a seguir explicarão como remover o Storyboard de um aplicativo:


1. Use o modelo de aplicativo de exibição única para criar um novo projeto do iOS:

    [![](ios-code-only-images/single-view-app.png "Use o modelo de aplicativo de exibição única")](ios-code-only-images/single-view-app.png#lightbox)

1. Excluir o `Main.Storyboard` e `ViewController.cs` arquivos. Fazer **não** excluir o `LaunchScreen.Storyboard`. O controlador de exibição deve ser excluído porque é o code-behind para o controlador de exibição é criado no Storyboard:
1. Certifique-se de selecionar **excluir** na caixa de diálogo pop-up:

    [![](ios-code-only-images/delete.png "Selecione Excluir na caixa de diálogo pop-up")](ios-code-only-images/delete.png#lightbox)

1. No Info. plist, exclua as informações dentro de **informações de implantação > Interface principal** opção:

    [![](ios-code-only-images/main-interface.png "Exclui as informações a opção de Interface principal")](ios-code-only-images/main-interface.png#lightbox)

1. Por fim, adicione o seguinte código ao seu `FinishedLaunching` método na classe AppDelegate:

    ```csharp
    public override bool FinishedLaunching(UIApplication app, NSDictionary options)
    {
        // create a new window instance based on the screen size
        window = new UIWindow(UIScreen.MainScreen.Bounds);

        // make the window visible
        window.MakeKeyAndVisible();

        return true;
    }
    ```

O código que foi adicionado para o `FinishedLaunching` método na etapa 5 acima, é a quantidade mínima de código necessário para criar uma janela para seu aplicativo iOS.

-----

aplicativos do iOS são criados usando o [padrão MVC](~/ios/get-started/hello-ios-multiscreen/hello-ios-multiscreen-deepdive.md#model-view-controller-mvc). A primeira tela que exibe um aplicativo é criada a partir do controlador de exibição de raiz da janela. Consulte a [Multitela Hello, iOS](~/ios/get-started/hello-ios-multiscreen/index.md) guia para obter mais detalhes sobre o MVC padrão em si.

A implementação para o `AppDelegate` adicionado pelo modelo cria a janela do aplicativo, do qual não há apenas um para cada aplicativo do iOS e se torna visível com o código a seguir:

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
        window = new UIWindow(UIScreen.MainScreen.Bounds);

        // make the window visible
        window.MakeKeyAndVisible();

        return true;
    }
}
```

Se você executar esse aplicativo agora, você provavelmente obteria uma exceção lançada informando que `Application windows are expected to have a root view controller at the end of application launch`. Vamos adicionar um controlador e torná-lo o controlador de exibição de raiz do aplicativo.

## <a name="adding-a-controller"></a>Adicionando um controlador

Seu aplicativo pode conter muitos controladores de exibição, mas ela precisa ter um controlador de exibição de raiz para controlar todos os controladores de exibição.  Adicionar um controlador para a janela, criando uma `UIViewController` instância e defini-lo como o `window.RootViewController` propriedade:

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

Cada controlador tem uma exibição associada, o que é acessível a partir de `View` propriedade. O código acima altera o modo de exibição `BackgroundColor` propriedade para `UIColor.LightGray` para que ela será visível, conforme mostrado abaixo:

 [![](ios-code-only-images/image1.png "Plano de fundo da exibição é um visível cinza-claro")](ios-code-only-images/image1.png#lightbox)

Poderíamos qualquer definido `UIViewController` subclasse como o `RootViewController` dessa forma também, incluindo controladores de UIKit, bem como aqueles que escrevemos sozinhos. Por exemplo, o código a seguir adiciona uma `UINavigationController` como o `RootViewController`:

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

Isso produz o controlador aninhado no controlador de navegação, conforme mostrado abaixo:

 [![](ios-code-only-images/image2.png "O controlador aninhado no controlador de navegação")](ios-code-only-images/image2.png#lightbox)

## <a name="creating-a-view-controller"></a>Criar um controlador de exibição

Agora que já vimos como adicionar um controlador como o `RootViewController` da janela, vamos ver como criar um controlador de exibição personalizados no código.

Adicionar uma nova classe chamada `CustomViewController` conforme mostrado abaixo:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](ios-code-only-images/customviewcontroller.w157-sml.png "Adicionar uma nova classe chamada CustomViewController")](ios-code-only-images/customviewcontroller.w157.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![](ios-code-only-images/new-file.png "Adicionar uma nova classe chamada CustomViewController")](ios-code-only-images/new-file.png#lightbox)

-----

A classe deve herdar de `UIViewController`, que está no `UIKit` namespace, conforme mostrado:

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

## <a name="initializing-the-view"></a>Inicializando o modo de exibição

`UIViewController` contém um método chamado `ViewDidLoad` que é chamado quando o controlador de exibição é carregado pela primeira vez na memória. Isso é um local adequado para fazer a inicialização do modo de exibição, como a configuração de suas propriedades.

Por exemplo, o código a seguir adiciona um botão e um manipulador de eventos para enviar por push a um novo controlador de exibição para a pilha de navegação quando o botão for pressionado:

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

Para carregar este controlador em seu aplicativo e demonstrar a navegação simple, criar uma nova instância da `CustomViewController`. Criar um novo controlador de navegação, passe em sua instância de controlador de exibição e definir o novo controlador de navegação para a janela `RootViewController` no `AppDelegate` como antes:

```csharp
var cvc = new CustomViewController ();

var navController = new UINavigationController (cvc);

Window.RootViewController = navController;
```

Agora quando o aplicativo é carregado, o `CustomViewController` é carregado dentro de um controlador de navegação:

 [![](ios-code-only-images/customvc.png "O CustomViewController é carregado dentro de um controlador de navegação")](ios-code-only-images/customvc.png#lightbox)

Clicar no botão, será _push_ um novo controlador de exibição para a pilha de navegação:

[![](ios-code-only-images/customvca.png "Um novo controlador de exibição é enviado para a pilha de navegação")](ios-code-only-images/customvca.png#lightbox)

## <a name="building-the-view-hierarchy"></a>Criando a hierarquia de exibição

No exemplo acima, começamos a criar uma interface do usuário no código, adicionando um botão para o controlador de exibição.

interfaces de usuário do iOS são compostos por uma hierarquia de exibição. Exibições adicionais, como rótulos, botões, controles deslizantes, etc. são adicionadas como subexibições de algum modo de exibição pai.

Por exemplo, vamos editar o `CustomViewController` para criar uma tela de logon em que o usuário pode inserir um nome de usuário e senha. A tela consistirá em dois campos de texto e um botão.

### <a name="adding-the-text-fields"></a>Adicionando campos de texto

Primeiro, remova o manipulador de evento e o botão que foi adicionado a [Inicializando o modo de exibição](#initializing-the-view) seção. 

Adicione um controle para o nome de usuário criando e inicializando uma `UITextField` e, em seguida, adicioná-lo para a hierarquia de exibição, conforme mostrado abaixo:

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

Quando criamos o `UITextField`, definimos o `Frame` propriedade para definir seu local e tamanho. No iOS a coordenada 0,0 é na parte superior esquerda com + x à direita e + y para baixo. Depois de definir a `Frame` juntamente com algumas outras propriedades, chamamos `View.AddSubview` para adicionar o `UITextField` para a hierarquia de exibição. Isso faz com que o `usernameField` um modo de exibição secundário do `UIView` instância em que o `View` referências de propriedade. Um modo de exibição secundário é adicionado com uma ordem z é maior do que o modo de exibição pai, para que ele apareça na frente da exibição pai na tela.

O aplicativo com o `UITextField` incluído é mostrado abaixo:

 [![](ios-code-only-images/image4.png "O aplicativo com o UITextField incluído")](ios-code-only-images/image4.png#lightbox)

Podemos adicionar um `UITextField` para a senha de maneira semelhante, desta vez, definimos o `SecureTextEntry` a propriedade como true, conforme mostrado abaixo:

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

Definindo `SecureTextEntry = true` oculta o texto inserido no `UITextField` pelo usuário, conforme mostrado abaixo:

 [![](ios-code-only-images/image4a.png "Definindo SecureTextEntry true oculta o texto inserido pelo usuário")](ios-code-only-images/image4a.png#lightbox)

### <a name="adding-the-button"></a>Adicionando o botão

Em seguida, adicionaremos um botão para que o usuário pode enviar o nome de usuário e senha. O botão é adicionado à hierarquia de exibição como qualquer outro controle, passando-o como um argumento para o modo de exibição de pai `AddSubview` método novamente.

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

Com isso em vigor, a tela de logon agora aparece conforme mostrado abaixo:

 [![](ios-code-only-images/image5.png "A tela de logon")](ios-code-only-images/image5.png#lightbox)

Ao contrário em versões anteriores do iOS, o plano de fundo do botão padrão é transparente. Alterando o botão `BackgroundColor` propriedade muda isso:

```csharp
submitButton.BackgroundColor = UIColor.White;
```

Isso resultará em um botão quadrado em vez de arredondada típico com borda do botão. Para obter a borda arredondada, use o seguinte trecho:

```csharp
submitButton.Layer.CornerRadius = 5f;
```

Com essas alterações, o modo de exibição terá esta aparência:

[![](ios-code-only-images/image6.png "Executar um exemplo do modo de exibição")](ios-code-only-images/image6.png#lightbox)

## <a name="adding-multiple-views-to-the-view-hierarchy"></a>Adicionando várias exibições para a hierarquia de exibição

iOS fornece um recurso para adicionar vários modos de exibição para a hierarquia de exibição usando `AddSubviews`.

```csharp
View.AddSubviews(new UIView[] { usernameField, passwordField, submitButton });
```

## <a name="adding-button-functionality"></a>Adicionando a funcionalidade do botão

Quando um botão é clicado, o que os usuários esperarão que algo aconteça. Por exemplo, um alerta é mostrado ou navegação é executada para outra tela.

Vamos adicionar algum código para enviar por push a um segundo controlador de exibição para a pilha de navegação.

Primeiro, crie o segundo controlador de exibição:

```csharp
var loginVC = new UIViewController () { Title = "Login Success!"};
loginVC.View.BackgroundColor = UIColor.Purple;
```

Em seguida, adicione a funcionalidade para o `TouchUpInside` evento:

```csharp
submitButton.TouchUpInside += (sender, e) => {
                this.NavigationController.PushViewController (loginVC, true);
            };
```

A navegação é ilustrada abaixo:

[![](ios-code-only-images/navigation.png "A navegação é ilustrada neste gráfico")](ios-code-only-images/navigation.png#lightbox)

Observe que por padrão, quando você usa um controlador de navegação, iOS dá ao aplicativo uma barra de navegação e um botão Voltar para que você possa mover de volta por meio da pilha.

## <a name="iterating-through-the-view-hierarchy"></a>Iterando por meio da hierarquia de exibição

É possível iterar por meio da hierarquia do modo de exibição secundário e selecionar qualquer modo de exibição específico. Por exemplo, para localizar cada `UIButton` e dê a esse botão outro `BackgroundColor`, o trecho a seguir pode ser usado.

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

Isso, no entanto, não funcionará se for o modo de exibição que está sendo iterado para um `UIView` como todas as exibições voltará como sendo um `UIView` como objetos adicionados ao modo de exibição pai em si herdam `UIView`.

## <a name="handling-rotation"></a>Tratamento de rotação

Se o usuário gira o dispositivo para paisagem, os controles não são redimensionados adequadamente, como mostra a captura de tela a seguir:

[![](ios-code-only-images/image7.png "Se o usuário gira o dispositivo para paisagem, os controles não são redimensionados adequadamente")](ios-code-only-images/image7.png#lightbox)

Uma maneira de corrigir esse problema é, definindo o `AutoresizingMask` propriedade em cada modo de exibição. Nesse caso, queremos que os controles para ampliar horizontalmente, para nós a definiríamos cada `AutoresizingMask`. O exemplo a seguir é para `usernameField`, mas o mesmo precisaria ser aplicado a cada gadget na hierarquia de exibição.

```csharp
usernameField.AutoresizingMask = UIViewAutoresizing.FlexibleWidth;
```

Agora quando estamos gira o simulador ou dispositivo, tudo é alongado para preencher o espaço adicional, conforme mostrado abaixo:

[![](ios-code-only-images/image8.png "Todos os controles ser esticada para preencher o espaço adicional")](ios-code-only-images/image8.png#lightbox)

## <a name="creating-custom-views"></a>Criando exibições personalizadas

Além de usar controles que fazem parte de UIKit, modos de exibição personalizados também podem ser usados. Uma exibição personalizada pode ser criada herdando `UIView` e substituindo `Draw`. Vamos criar uma exibição personalizada e adicioná-lo para a hierarquia de exibição para demonstrar.

### <a name="inheriting-from-uiview"></a>Herdando UIView

A primeira coisa que precisamos fazer é criar uma classe para o modo de exibição personalizado. Podemos fazer isso usando o **classe** modelo no Visual Studio para adicionar uma classe vazia chamada `CircleView`. A classe base deve ser definida como `UIView`, que é recuperada está no `UIKit` namespace. Também será necessário o `System.Drawing` namespace também. Os outros vários `System.*` namespaces não será usado neste exemplo, fique à vontade para removê-los.

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

### <a name="drawing-in-a-uiview"></a>Desenhando em um UIView

Cada `UIView` tem um `Draw` método é chamado pelo sistema quando ele precisa ser desenhado. `Draw` nunca deve ser chamada diretamente. Ele é chamado pelo sistema durante o processamento de loop de execução. Na primeira vez pelo loop execução depois que um modo de exibição é adicionado à hierarquia de exibição, seu `Draw` método é chamado. As chamadas subsequentes para `Draw` ocorrem quando o modo de exibição é marcado como a necessidade de ser desenhada com uma chamada a `SetNeedsDisplay` ou `SetNeedsDisplayInRect` no modo de exibição.

Podemos adicionar código de desenho à nossa exibição adicionando esse código dentro de substituído `Draw` método conforme mostrado abaixo:

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

Uma vez que `CircleView` é um `UIView`, também podemos definir `UIView` propriedades também. Por exemplo, podemos definir o `BackgroundColor` no construtor:

```csharp
public CircleView()
{
    BackgroundColor = UIColor.White;
}
```

Para usar o `CircleView` que acabamos de criar, podemos pode adicioná-lo como um modo de exibição de secundário para a hierarquia de exibição em um controlador existente, como fizemos com o `UILabels` e `UIButton` anteriormente, ou podemos carregá-lo como o modo de exibição de um novo controlador. Vamos fazer o último.

### <a name="loading-a-view"></a>Carregamento de um modo de exibição

 `UIViewController` tem um método chamado `LoadView` que é chamado pelo controlador para criar sua exibição. Isso é um local adequado para criar um modo de exibição e atribuí-lo para o controlador `View` propriedade.

Primeiro, precisamos de um controlador, portanto, crie uma nova classe vazia chamada `CircleController`.

Na `CircleController` adicione o seguinte código para definir o `View` para um `CircleView` (você não deve chamar o `base` implementação em sua substituição):

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

Por fim, precisamos apresentar o controlador em tempo de execução. Vamos fazer isso adicionando um manipulador de eventos no botão Enviar que adicionamos anteriormente, da seguinte maneira:

```csharp
submitButton.TouchUpInside += delegate
{
    Console.WriteLine("Submit button clicked");

    //circleController is declared as class variable
    circleController = new CircleController();
    PresentViewController(circleController, true, null);
};
```

Agora, quando executar o aplicativo e toque no botão Enviar, a nova exibição com um círculo é exibida:

[![](ios-code-only-images/circles.png "A nova exibição com um círculo é exibida")](ios-code-only-images/circles.png#lightbox)

## <a name="creating-a-launch-screen"></a>Criando uma tela de inicialização

Um [tela de inicialização](~/ios/app-fundamentals/images-icons/launch-screens.md) é exibido quando seu aplicativo é iniciado como uma maneira de exibir para os usuários se ele está respondendo. Como uma tela de inicialização é exibida quando seu aplicativo está sendo carregado, não pode ser criado no código como o aplicativo ainda está sendo carregado na memória.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Quando você cria um projeto do iOS no Visual Studio, uma tela de inicialização é fornecida para você na forma de um arquivo. XIB, que pode ser encontrado na **recursos** pasta dentro de seu projeto.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Quando você cria um projeto do iOS no Visual Studio para Mac, uma tela de inicialização é fornecida para você na forma de um arquivo de Storyboard.

-----

Isso pode ser editado por double clicando nele e abri-lo no Designer do iOS.

Apple recomenda que um arquivo de Storyboard ou. XIB é usado para aplicativos destinados ao iOS 8 ou posterior, quando você inicia o arquivo no Designer do iOS, você usará as Classes de tamanho e o Layout automático para adaptar seu layout de forma que ele parece bom e exibe corretamente, para todos os dispositivos tamanhos. Uma imagem de inicialização estática pode ser usada, além de um. XIB ou um Storyboard para permitir que o suporte para aplicativos destinados a versões anteriores.

Para obter mais informações sobre como criar uma tela de inicialização, consulte os documentos a seguir:

- [Criando uma tela de inicialização usando um. XIB](https://github.com/xamarin/recipes/tree/master/Recipes/ios/general/templates/launchscreen-xib)
- [Gerenciando as telas de inicialização com Storyboards](~/ios/app-fundamentals/images-icons/launch-screens.md)

> [!IMPORTANT]
> A partir do iOS 9, a Apple recomenda que os Storyboards devem ser usados como o principal método de criação de uma tela de inicialização.

### <a name="creating-a-launch-image-for-pre-ios-8-applications"></a>Criar uma imagem de inicialização para pré-iOS 8 aplicativos

Uma imagem estática pode ser usada juntamente com um. XIB ou tela de inicialização de Storyboard se seu aplicativo tem como alvo as versões anteriores ao iOS 8.

Essa imagem estática pode ser definida no arquivo Info. plist, ou como um catálogo de ativos (para iOS 7) em seu aplicativo. Você precisará fornecer imagens separadas para cada tamanho de dispositivo (320 x 480, 640 x 960, 640 x 1136) que pode ser executados em seu aplicativo. Para obter mais informações sobre tamanhos de tela de inicialização, exibir o [imagens de tela de inicialização](~/ios/app-fundamentals/images-icons/launch-screens.md) guia.

> [!IMPORTANT]
> Se seu aplicativo não tiver nenhuma tela Iniciar, você pode perceber que ela não couber totalmente a tela. Se esse for o caso, assegure-se de incluir, pelo menos, uma imagem de 640 x 1136 chamada `Default-568@2x.png` ao seu Info. plist.

## <a name="summary"></a>Resumo

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Este artigo abordou como desenvolver aplicativos iOS por meio de programação no Visual Studio. Vimos como criar um projeto de um modelo de projeto vazio, discutindo como criar e adicionar um controlador de exibição de raiz para a janela. Em seguida, mostramos como usar controles de UIKit para criar uma hierarquia de exibição dentro de um controlador para desenvolver uma tela de aplicativo. Em seguida, examinamos como fazer os modos de exibição dispor adequadamente em diferentes orientações e vimos como criar uma exibição personalizada Subclassificando `UIView`, bem como carregar o modo de exibição dentro de um controlador. Por fim, exploramos como adicionar uma tela de inicialização para um aplicativo.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Esse artigo discutiu como desenvolver aplicativos do iOS por meio de programação no Visual Studio para Mac. Vimos como criar um projeto de um modelo de exibição única, discutindo como criar e adicionar um controlador de exibição de raiz para a janela. Em seguida, mostramos como usar controles de UIKit para criar uma hierarquia de exibição dentro de um controlador para desenvolver uma tela de aplicativo. Em seguida, examinamos como fazer os modos de exibição dispor adequadamente em diferentes orientações e vimos como criar uma exibição personalizada Subclassificando `UIView`, bem como carregar o modo de exibição dentro de um controlador. Por fim, exploramos como adicionar uma tela de inicialização para um aplicativo.

-----

## <a name="related-links"></a>Links relacionados

- [SimpleLogin (amostra)](https://developer.xamarin.com/samples/monotouch/SimpleLogin)
