---
title: Xamarin. Forms em projetos do Xamarin nativo
description: Este artigo explica como consumir páginas ContentPage derivadas que são adicionadas diretamente a projetos de Xamarin nativos e como navegar entre eles.
ms.prod: xamarin
ms.assetid: f343fc21-dfb1-4364-a332-9da6705d36bc
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/09/2018
ms.openlocfilehash: e02c04afe656b0eca3b7ae12b8b30f35836b9368
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60954862"
---
# <a name="xamarinforms-in-xamarin-native-projects"></a>Xamarin. Forms em projetos do Xamarin nativo

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/Native2Forms/)

_Formulários nativos permitem que páginas ContentPage do xamarin. Forms derivado a ser consumido por projetos nativos do xamarin. IOS, xamarin. Android e plataforma Universal do Windows (UWP). Projetos nativos podem consumir páginas com ContentPage derivada que são adicionadas diretamente ao projeto ou de uma biblioteca .NET Standard, um projeto compartilhado ou uma biblioteca .NET Standard. Este artigo explica como consumir páginas ContentPage derivadas que são adicionadas diretamente ao projetos nativos e como navegar entre eles._

Normalmente, um aplicativo xamarin. Forms inclui uma ou mais páginas que derivam de [ `ContentPage` ](xref:Xamarin.Forms.ContentPage), e essas páginas são compartilhadas por todas as plataformas em um projeto compartilhado ou o projeto de biblioteca .NET Standard. No entanto, formas nativas permite `ContentPage`-derivado de páginas a serem adicionados diretamente a aplicativos nativos do xamarin. IOS, xamarin. Android e UWP. Em comparação com a ter o projeto nativo consumir `ContentPage`-páginas derivadas de um projeto de biblioteca .NET Standard ou o projeto compartilhado, a vantagem de adicionar páginas diretamente em projetos nativos é que as páginas podem ser estendidas com exibições nativas. Exibições nativas, em seguida, podem ser nomeadas no XAML com `x:Name` e referenciada de code-behind. Para obter mais informações sobre exibições nativas, consulte [exibições nativas](~/xamarin-forms/platform/native-views/index.md).

O processo para o consumo de um xamarin. Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-página derivada em um projeto nativo é o seguinte:

1. Adicione o pacote do NuGet xamarin. Forms para o projeto nativo.
1. Adicione a [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-derivado de página e quaisquer dependências, para o projeto nativo.
1. Chame o método `Forms.Init`.
1. Construir uma instância de [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-página derivada e convertê-lo para o tipo apropriado de nativo usando um dos seguintes métodos de extensão: `CreateViewController` para iOS, `CreateSupportFragment` para o Android, ou `CreateFrameworkElement` para UWP.
1. Navegue até a representação de tipo nativo do [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-usando a API nativa de navegação de página derivada.

Xamarin. Forms deve ser inicializado chamando o `Forms.Init` método antes de um projeto nativo pode construir uma [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-página derivada. Escolher quando fazer isso principalmente depende de quando é mais conveniente em seu fluxo de aplicativo – pode ser executada na inicialização do aplicativo, ou imediatamente antes do `ContentPage`-página derivada é construído. Neste artigo, e os aplicativos de exemplo que acompanha este artigo, o `Forms.Init` método é chamado na inicialização do aplicativo.

> [!NOTE]
> O **NativeForms** solução de aplicativo de exemplo não contém todos os projetos xamarin. Forms. Em vez disso, ele consiste em um projeto xamarin. IOS, um projeto xamarin. Android e um projeto UWP. Cada projeto é um projeto nativo que usa formulários nativos para consumir [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-derivado de páginas. No entanto, não há nenhum motivo por que os projetos nativos não podiam consumir `ContentPage`-derivado de páginas de um projeto de biblioteca .NET Standard ou o projeto compartilhado.

Ao usar formulários Native, xamarin. Forms recursos como [ `DependencyService` ](xref:Xamarin.Forms.DependencyService), [ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter)e o mecanismo de associação de dados, todo o trabalho ainda. No entanto, a navegação de página deve ser executada usando a API nativa de navegação.

## <a name="ios"></a>iOS

No iOS, o `FinishedLaunching` substituir no `AppDelegate` classe geralmente é o lugar para executar o aplicativo de tarefas relacionadas à inicialização. Ele é chamado depois que o aplicativo foi iniciado e geralmente é substituído para configurar a janela principal e exibir o controlador. O seguinte exemplo de código mostra o `AppDelegate` classe no aplicativo de exemplo:

```csharp
[Register("AppDelegate")]
public class AppDelegate : UIApplicationDelegate
{
    public static AppDelegate Instance;

    UIWindow _window;
    UINavigationController _navigation;

    public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
    {
        Forms.Init();

        Instance = this;
        _window = new UIWindow(UIScreen.MainScreen.Bounds);

        UINavigationBar.Appearance.SetTitleTextAttributes(new UITextAttributes
        {
            TextColor = UIColor.Black
        });

        var mainPage = new PhonewordPage().CreateViewController();
        mainPage.Title = "Phoneword";

        _navigation = new UINavigationController(mainPage);
        _window.RootViewController = _navigation;
        _window.MakeKeyAndVisible();

        return true;
    }
    ...
}
```

O `FinishedLaunching` método realiza as seguintes tarefas:

- Xamarin. Forms é inicializado chamando o `Forms.Init` método.
- Uma referência para o `AppDelegate` classe é armazenada na `static` `Instance` campo. Isso é fornecer um mecanismo para chamar métodos definidos em outras classes de `AppDelegate` classe.
- O `UIWindow`, que é o principal contêiner para modos de exibição em aplicativos nativos iOS, é criada.
- O `PhonewordPage` classe, que é um xamarin. Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-derivado da página definida em XAML, que é construído e convertido em um `UIViewController` usando o `CreateViewController` método de extensão.
- O `Title` propriedade do `UIViewController` for definido, que será exibido o `UINavigationBar`.
- Um `UINavigationController` é criada para gerenciar a navegação hierárquica. O `UINavigationController` classe gerencia uma pilha de controladores de exibição e o `UIViewController` passado para o construtor será exibido inicialmente quando o `UINavigationController` é carregado.
- O `UINavigationController` instância é definida como o nível superior `UIViewController` para o `UIWindow`e o `UIWindow` é definida como a janela de chave para o aplicativo e se torna visível.

Uma vez a `FinishedLaunching` método foi executado, a interface do usuário definido no xamarin. Forms `PhonewordPage` classe será exibida, conforme mostrado na seguinte captura de tela:

[![](native-forms-images/ios-phonewordpage.png "iOS PhonewordPage")](native-forms-images/ios-phonewordpage-large.png#lightbox "iOS PhonewordPage")

Interagindo com a interface do usuário, por exemplo, tocando em uma [ `Button` ](xref:Xamarin.Forms.Button), resultará em manipuladores de eventos no `PhonewordPage` de lógica de execução. Por exemplo, quando um usuário toca a **histórico de chamadas** botão, o manipulador de eventos a seguir é executado:

```csharp
void OnCallHistory(object sender, EventArgs e)
{
    AppDelegate.Instance.NavigateToCallHistoryPage();
}
```

O `static` `AppDelegate.Instance` campo permite que o `AppDelegate.NavigateToCallHistoryPage` método a ser invocado, que é mostrado no exemplo de código a seguir:

```csharp
public void NavigateToCallHistoryPage()
{
    var callHistoryPage = new CallHistoryPage().CreateViewController();
    callHistoryPage.Title = "Call History";
    _navigation.PushViewController(callHistoryPage, true);
}
```

O `NavigateToCallHistoryPage` método converte o xamarin. Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-derivado da página para um `UIViewController` com o `CreateViewController` o método de extensão e conjuntos do `Title` propriedade do `UIViewController`. O `UIViewController` , em seguida, é enviado por push `UINavigationController` pelo `PushViewController` método. Portanto, a interface do usuário definido no xamarin. Forms `CallHistoryPage` classe será exibida, conforme mostrado na seguinte captura de tela:

[![](native-forms-images/ios-callhistorypage.png "iOS CallHistoryPage")](native-forms-images/ios-callhistorypage-large.png#lightbox "CallHistoryPage do iOS")

Quando o `CallHistoryPage` for exibida, tocando o back seta será exibida a `UIViewController` para o `CallHistoryPage` de classe da `UINavigationController`, retornando o usuário para o `UIViewController` para o `PhonewordPage` classe.

## <a name="android"></a>Android

No Android, o `OnCreate` substituir no `MainActivity` classe geralmente é o lugar para executar o aplicativo de tarefas relacionadas à inicialização. O seguinte exemplo de código mostra o `MainActivity` classe no aplicativo de exemplo:

```csharp
public class MainActivity : AppCompatActivity
{
    public static MainActivity Instance;

    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);

        Forms.Init(this, bundle);
        Instance = this;

        SetContentView(Resource.Layout.Main);
        var toolbar = FindViewById<Toolbar>(Resource.Id.toolbar);
        SetSupportActionBar(toolbar);
        SupportActionBar.Title = "Phoneword";

        var mainPage = new PhonewordPage().CreateSupportFragment(this);
        SupportFragmentManager
            .BeginTransaction()
            .Replace(Resource.Id.fragment_frame_layout, mainPage)
            .Commit();
        ...
    }
    ...
}
```

O `OnCreate` método realiza as seguintes tarefas:

- Xamarin. Forms é inicializado chamando o `Forms.Init` método.
- Uma referência para o `MainActivity` classe é armazenada na `static` `Instance` campo. Isso é fornecer um mecanismo para chamar métodos definidos em outras classes de `MainActivity` classe.
- O `Activity` o conteúdo está definido de um recurso de layout. No aplicativo de exemplo, o layout consiste em uma `LinearLayout` que contém uma `Toolbar`e um `FrameLayout` para atuar como um contêiner de fragmento.
- O `Toolbar` é recuperado e definido como a barra de ação para o `Activity`, e o título da barra de ação está definido.
- O `PhonewordPage` classe, que é um xamarin. Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-derivado da página definida em XAML, que é construído e convertido em um `Fragment` usando o `CreateSupportFragment` método de extensão.
- O `SupportFragmentManager` classe cria e confirma uma transação que substitui o `FrameLayout` da instância com o `Fragment` para o `PhonewordPage` classe.

Para obter mais informações sobre fragmentos, consulte [fragmentos](~/android/platform/fragments/index.md).

Uma vez a `OnCreate` método foi executado, a interface do usuário definido no xamarin. Forms `PhonewordPage` classe será exibida, conforme mostrado na seguinte captura de tela:

[![](native-forms-images/android-phonewordpage.png "Android PhonewordPage")](native-forms-images/android-phonewordpage-large.png#lightbox "PhonewordPage Android")

Interagindo com a interface do usuário, por exemplo, tocando em uma [ `Button` ](xref:Xamarin.Forms.Button), resultará em manipuladores de eventos no `PhonewordPage` de lógica de execução. Por exemplo, quando um usuário toca a **histórico de chamadas** botão, o manipulador de eventos a seguir é executado:

```csharp
void OnCallHistory(object sender, EventArgs e)
{
    MainActivity.Instance.NavigateToCallHistoryPage();
}
```

O `static` `MainActivity.Instance` campo permite que o `MainActivity.NavigateToCallHistoryPage` método a ser invocado, que é mostrado no exemplo de código a seguir:

```csharp
public void NavigateToCallHistoryPage()
{
    var callHistoryPage = new CallHistoryPage().CreateSupportFragment(this);
    SupportFragmentManager
        .BeginTransaction()
        .AddToBackStack(null)
        .Replace(Resource.Id.fragment_frame_layout, callHistoryPage)
        .Commit();
}
```

O `NavigateToCallHistoryPage` método converte o xamarin. Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-derivado da página para um `Fragment` com o `CreateSupportFragment` o método de extensão e adiciona o `Fragment` pilha de volta para o fragmento. Portanto, a interface do usuário definido no xamarin. Forms `CallHistoryPage` será exibida, conforme mostrado na seguinte captura de tela:

[![](native-forms-images/android-callhistorypage.png "Android CallHistoryPage")](native-forms-images/android-callhistorypage-large.png#lightbox "CallHistoryPage Android")

Quando o `CallHistoryPage` for exibida, tocando o back seta será exibida a `Fragment` para o `CallHistoryPage` da pilha voltar de fragmento, retornando o usuário para o `Fragment` para o `PhonewordPage` classe.

### <a name="enabling-back-navigation-support"></a>Habilitando o suporte de navegação regressiva

O `SupportFragmentManager` classe tem um `BackStackChanged` evento é acionado sempre que o conteúdo da pilha voltar de fragmento é alterado. O `OnCreate` método no `MainActivity` classe contém um manipulador de eventos anônimos para este evento:

```csharp
SupportFragmentManager.BackStackChanged += (sender, e) =>
{
    bool hasBack = SupportFragmentManager.BackStackEntryCount > 0;
    SupportActionBar.SetHomeButtonEnabled(hasBack);
    SupportActionBar.SetDisplayHomeAsUpEnabled(hasBack);
    SupportActionBar.Title = hasBack ? "Call History" : "Phoneword";
};
```

Esse manipulador de eventos exibe um botão Voltar na barra de ação, desde que haja um ou mais `Fragment` instâncias no fragmento de pilha de volta. A resposta ao tocar no botão Voltar é tratada pelo `OnOptionsItemSelected` substituir:

```csharp
public override bool OnOptionsItemSelected(Android.Views.IMenuItem item)
{
    if (item.ItemId == global::Android.Resource.Id.Home && SupportFragmentManager.BackStackEntryCount > 0)
    {
        SupportFragmentManager.PopBackStack();
        return true;
    }
    return base.OnOptionsItemSelected(item);
}
```

O `OnOptionsItemSelected` substituição é chamada sempre que um item no menu de opções está selecionado. Essa implementação retira o fragmento atual da pilha voltar de fragmento, desde que foi selecionado o botão Voltar e há um ou mais `Fragment` instâncias no fragmento de pilha de volta.

### <a name="multiple-activities"></a>Várias atividades

Quando um aplicativo é composto por várias atividades, [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-páginas derivadas podem ser inseridas em cada uma das atividades. Nesse cenário, o `Forms.Init` método precisa ser chamado somente em de `OnCreate` substituir da primeira `Activity` que incorpora um xamarin. Forms `ContentPage`. No entanto, isso tem o seguinte impacto:

- O valor de `Xamarin.Forms.Color.Accent` será extraído o `Activity` que chamou o `Forms.Init` método.
- O valor de `Xamarin.Forms.Application.Current` será associado a `Activity` que chamou o `Forms.Init` método.

### <a name="choosing-a-file"></a>Escolher um arquivo

Ao inserir uma [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-derivado de página que usa um [ `WebView` ](xref:Xamarin.Forms.WebView) que precisa para dar suporte um HTML "Escolher arquivo" botão, o `Activity` precisará substituir o `OnActivityResult` método:

```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);
    ActivityResultCallbackRegistry.InvokeCallback(requestCode, resultCode, data);
}
```

## <a name="uwp"></a>UWP

Na UWP, nativo `App` classe geralmente é o lugar para executar o aplicativo de tarefas relacionadas à inicialização. Xamarin. Forms é geralmente inicializado, em aplicativos da UWP do xamarin. Forms, na `OnLaunched` substituir em nativo `App` classe, para passar o `LaunchActivatedEventArgs` argumento para o `Forms.Init` método. Por esse motivo, os aplicativos nativos de UWP que consomem um xamarin. Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-página derivada pode chamar mais facilmente os `Forms.Init` método a partir de `App.OnLaunched` método.

Por padrão, nativo `App` classe inicia o `MainPage` classe como a primeira página do aplicativo. O seguinte exemplo de código mostra o `MainPage` classe no aplicativo de exemplo:

```csharp
public sealed partial class MainPage : Page
{
    public static MainPage Instance;

    public MainPage()
    {
        this.InitializeComponent();
        this.NavigationCacheMode = NavigationCacheMode.Enabled;
        Instance = this;
        this.Content = new Phoneword.UWP.Views.PhonewordPage().CreateFrameworkElement();
    }
    ...
}
```

O `MainPage` construtor executa as seguintes tarefas:

- O cache é habilitado para a página, para que um novo `MainPage` não é construída quando um usuário navega para a página.
- Uma referência para o `MainPage` classe é armazenada na `static` `Instance` campo. Isso é fornecer um mecanismo para chamar métodos definidos em outras classes de `MainPage` classe.
- O `PhonewordPage` classe, que é um xamarin. Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-derivado da página definida em XAML, que é construído e convertido em um `FrameworkElement` usando o `CreateFrameworkElement` método de extensão e, em seguida, defina como o conteúdo da `MainPage` classe.

Uma vez a `MainPage` construtor foi executado, a interface do usuário definido no xamarin. Forms `PhonewordPage` classe será exibida, conforme mostrado na seguinte captura de tela:

[![](native-forms-images/uwp-phonewordpage.png "UWP PhonewordPage")](native-forms-images/uwp-phonewordpage-large.png#lightbox "PhonewordPage UWP")

Interagindo com a interface do usuário, por exemplo, tocando em uma [ `Button` ](xref:Xamarin.Forms.Button), resultará em manipuladores de eventos no `PhonewordPage` de lógica de execução. Por exemplo, quando um usuário toca a **histórico de chamadas** botão, o manipulador de eventos a seguir é executado:

```csharp
void OnCallHistory(object sender, EventArgs e)
{
    Phoneword.UWP.MainPage.Instance.NavigateToCallHistoryPage();
}
```

O `static` `MainPage.Instance` campo permite que o `MainPage.NavigateToCallHistoryPage` método a ser invocado, que é mostrado no exemplo de código a seguir:

```csharp
public void NavigateToCallHistoryPage()
{
    this.Frame.Navigate(new CallHistoryPage());
}
```

Navegação no UWP normalmente é executada com o `Frame.Navigate` método, que usa um `Page` argumento. Xamarin. Forms define uma `Frame.Navigate` método de extensão que usa um [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-instância de página derivada. Portanto, quando o `NavigateToCallHistoryPage` método é executado, a interface do usuário definido no xamarin. Forms `CallHistoryPage` será exibida, conforme mostrado na seguinte captura de tela:

[![](native-forms-images/uwp-callhistorypage.png "UWP CallHistoryPage")](native-forms-images/uwp-callhistorypage-large.png#lightbox "CallHistoryPage UWP")

Quando o `CallHistoryPage` for exibida, tocando o back seta será exibida a `FrameworkElement` para o `CallHistoryPage` da pilha Voltar no aplicativo, retornando o usuário para o `FrameworkElement` para o `PhonewordPage` classe.

### <a name="enabling-back-navigation-support"></a>Habilitando o suporte de navegação regressiva

Na UWP, aplicativos devem habilitar a navegação para todos os software e hardware back botões, em fatores forma de dispositivo diferentes. Isso pode ser feito ao registrar um manipulador de eventos para o `BackRequested` evento, que pode ser executado em de `OnLaunched` método nativo `App` classe:

```csharp
protected override void OnLaunched(LaunchActivatedEventArgs e)
{
    Frame rootFrame = Window.Current.Content as Frame;

    if (rootFrame == null)
    {
        ...      
        // Place the frame in the current Window
        Window.Current.Content = rootFrame;

        SystemNavigationManager.GetForCurrentView().BackRequested += OnBackRequested;
    }
    ...
}
```

Quando o aplicativo é iniciado, o `GetForCurrentView` método recupera o `SystemNavigationManager` objeto associado com o modo de exibição atual, em seguida, registra um manipulador de eventos para o `BackRequested` eventos. O aplicativo recebe esse evento somente se ele é o aplicativo de primeiro plano e, em resposta, chama o `OnBackRequested` manipulador de eventos:

```csharp
void OnBackRequested(object sender, BackRequestedEventArgs e)
{
    Frame rootFrame = Window.Current.Content as Frame;
    if (rootFrame.CanGoBack)
    {
        e.Handled = true;
        rootFrame.GoBack();
    }
}
```

O `OnBackRequested` chamadas do manipulador de eventos a `GoBack` método no quadro da raiz do aplicativo e conjuntos da `BackRequestedEventArgs.Handled` propriedade `true` para marcar o evento como manipulado. Não marcar o evento como manipulado pode resultar no sistema de navegar para fora do aplicativo (na família de dispositivo móvel) ou ignorando o evento (na família de dispositivo de desktop).

O aplicativo se baseia em um botão Voltar do sistema fornecidos em um telefone, mas escolhe se deseja mostrar um botão Voltar na barra de título em dispositivos da área de trabalho. Isso é feito definindo a `AppViewBackButtonVisibility` propriedade para um do `AppViewBackButtonVisibility` valores de enumeração:

```csharp
void OnNavigated(object sender, NavigationEventArgs e)
{
    SystemNavigationManager.GetForCurrentView().AppViewBackButtonVisibility =
        ((Frame)sender).CanGoBack ? AppViewBackButtonVisibility.Visible : AppViewBackButtonVisibility.Collapsed;
}
```

O `OnNavigated` manipulador de eventos, que é executado em resposta ao `Navigated` evento disparando, atualiza a visibilidade do botão Voltar da barra de título quando ocorrer a navegação de página. Isso garante que o botão Voltar de barra de título é visível se a pilha Voltar no aplicativo não está vazia ou removido da barra de título se a pilha Voltar no aplicativo está vazia.

Para obter mais informações sobre o suporte de navegação regressiva na UWP, consulte [histórico de navegação e para trás a navegação para aplicativos UWP](/windows/uwp/design/basics/navigation-history-and-backwards-navigation/).

## <a name="summary"></a>Resumo

Formulários nativos permitem que o xamarin. Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-derivado de páginas a ser consumido por projetos nativos do xamarin. IOS, xamarin. Android e plataforma Universal do Windows (UWP). Projetos nativos podem consumir `ContentPage`-derivado de páginas que são adicionadas diretamente ao projeto, ou de um projeto de biblioteca .NET Standard ou o projeto compartilhado. Este artigo explicou como consumir `ContentPage`-derivado de páginas que são adicionadas diretamente para projetos nativos e como navegar entre eles.


## <a name="related-links"></a>Links relacionados

- [NativeForms (amostra)](https://developer.xamarin.com/samples/xamarin-forms/Native2Forms/)
- [Exibições nativas](~/xamarin-forms/platform/native-views/index.md)
