---
title: "Formulários nativo"
description: "Formulários nativos permitem derivado xamarin. Forms ContentPage páginas a serem consumidos por projetos nativos do xamarin, xamarin e Windows UWP (plataforma Universal). Projetos nativos podem consumir páginas ContentPage derivadas que são adicionadas diretamente ao projeto ou de uma biblioteca de classe portátil (PCL), a biblioteca padrão do .NET ou o projeto compartilhado. Este artigo explica como utilizar páginas ContentPage derivadas que são adicionadas diretamente ao projetos nativos e como navegar entre eles."
ms.topic: article
ms.prod: xamarin
ms.assetid: f343fc21-dfb1-4364-a332-9da6705d36bc
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/11/2018
ms.openlocfilehash: 5a7d4ab69ff3ccd1a50ea4fccb6e494f7c73fc72
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="native-forms"></a>Formulários nativo

_Formulários nativos permitem derivado xamarin. Forms ContentPage páginas a serem consumidos por projetos nativos do xamarin, xamarin e Windows UWP (plataforma Universal). Projetos nativos podem consumir páginas ContentPage derivadas que são adicionadas diretamente ao projeto ou de uma biblioteca de classe portátil (PCL), a biblioteca padrão do .NET ou o projeto compartilhado. Este artigo explica como utilizar páginas ContentPage derivadas que são adicionadas diretamente ao projetos nativos e como navegar entre eles._

Normalmente, um aplicativo xamarin. Forms inclui uma ou mais páginas que derivam de [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/), e essas páginas são compartilhadas por todas as plataformas em PCL, biblioteca padrão do .NET ou projeto compartilhado. No entanto, formulários nativo permite `ContentPage`-derivado páginas a serem adicionadas diretamente a aplicativos nativos do xamarin, xamarin e UWP. Em comparação com a ter o projeto nativo consumir `ContentPage`-páginas derivadas de PCL, biblioteca padrão do .NET ou projeto compartilhado, a vantagem de adição de páginas diretamente para projetos nativos é que as páginas podem ser estendidas com exibições nativo. Exibições nativo, em seguida, podem ser nomeadas em XAML com `x:Name` e referenciados no code-behind. Para obter mais informações sobre modos de exibição nativo, consulte [exibições nativo](~/xamarin-forms/platform/native-views/index.md).

O processo para consumir um xamarin. Forms [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-página derivada em um projeto nativo é o seguinte:

1. Adicione o pacote NuGet do xamarin. Forms ao projeto nativo.
1. Adicionar o [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-derivado de página e as dependências, ao projeto nativo.
1. Chame o método `Forms.Init`.
1. Construir uma instância do [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-página derivada e convertê-lo para o tipo apropriado de nativo usando um dos seguintes métodos de extensão: `CreateViewController` para iOS, `CreateFragment` ou `CreateSupportFragment` para o Android, ou `CreateFrameworkElement` para UWP.
1. Navegue para a representação de tipo nativo do [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-derivadas usando a API nativa de navegação de página.

Xamarin. Forms devem ser inicializados chamando o `Forms.Init` método antes de um projeto nativo pode construir um [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-página derivada. Escolher quando fazer isso basicamente depende de quando for mais conveniente em seu fluxo de aplicativo – pode ser executada na inicialização do aplicativo, ou simplesmente antes do `ContentPage`-página derivada é construído. Neste artigo, e os aplicativos de exemplo fornecido, o `Forms.Init` método é chamado na inicialização do aplicativo.

> [!NOTE]
> O **NativeForms** solução de aplicativo de exemplo não contém projetos xamarin. Forms. Em vez disso, ele consiste em um projeto de xamarin, um projeto de xamarin e um projeto UWP. Cada projeto é um projeto nativo que usa formulários nativo para consumir [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-derivado de páginas. No entanto, não há nenhum motivo por que os projetos nativo não podem consumir `ContentPage`-derivado páginas de um PCL, a biblioteca padrão do .NET ou o projeto compartilhado.

Ao usar formulários nativo, xamarin. Forms recursos como [ `DependencyService` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DependencyService/), [ `MessagingCenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MessagingCenter/)e o mecanismo de associação de dados, todo o trabalho ainda.

## <a name="ios"></a>iOS

No iOS, o `FinishedLaunching` substituir no `AppDelegate` classe normalmente é o local para executar o aplicativo tarefas relacionadas a inicialização. Ele é chamado depois que o aplicativo é iniciado e geralmente é substituído para configurar a janela principal e exibir o controlador. O seguinte exemplo de código mostra o `AppDelegate` classe no aplicativo de exemplo:

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

O `FinishedLaunching` método executa as seguintes tarefas:

- Xamarin. Forms é inicializada, chamando o `Forms.Init` método.
- Uma referência para o `AppDelegate` classe é armazenada no `static` `Instance` campo. Isso é fornecer um mecanismo para outras classes chamar os métodos definidos no `AppDelegate` classe.
- O `UIWindow`, que é o contêiner principal para exibições em aplicativos do iOS nativo, é criado.
- O `PhonewordPage` classe, que é um xamarin. Forms [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-derivado de página definido em XAML, que é construído e convertido em um `UIViewController` usando o `CreateViewController` método de extensão.
- O `Title` propriedade do `UIViewController` for definido, que será exibido o `UINavigationBar`.
- Um `UINavigationController` é criada para gerenciar a navegação hierárquica. O `UINavigationController` classe gerencia uma pilha de controladores de exibição e o `UIViewController` passado para o construtor será exibido inicialmente quando o `UINavigationController` é carregado.
- O `UINavigationController` instância é definida como o nível superior `UIViewController` para o `UIWindow`e o `UIWindow` é definido como a janela principal do aplicativo e ficam visíveis.

Uma vez o `FinishedLaunching` método executado, a interface do usuário definido no xamarin. Forms `PhonewordPage` classe será exibida, conforme mostrado na seguinte captura de tela:

[![](native-forms-images/ios-phonewordpage.png "iOS PhonewordPage")](native-forms-images/ios-phonewordpage-large.png#lightbox "iOS PhonewordPage")

Interagir com a interface do usuário, por exemplo, tocando em uma [ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/), resultará em manipuladores de eventos de `PhonewordPage` por trás do código em execução. Por exemplo, quando um usuário toca o **histórico de chamadas** botão, o manipulador de eventos a seguir é executado:

```csharp
void OnCallHistory(object sender, EventArgs e)
{
    AppDelegate.Instance.NavigateToCallHistoryPage();
}
```

O `static` `AppDelegate.Instance` campo permite que o `AppDelegate.NavigateToCallHistoryPage` método ser chamado, o que é mostrado no exemplo de código a seguir:

```csharp
public void NavigateToCallHistoryPage()
{
    var callHistoryPage = new CallHistoryPage().CreateViewController();
    callHistoryPage.Title = "Call History";
    _navigation.PushViewController(callHistoryPage, true);
}
```

O `NavigateToCallHistoryPage` método converte o xamarin. Forms [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-derivado de página para um `UIViewController` com o `CreateViewController` método de extensão e conjuntos a `Title` propriedade do `UIViewController`. O `UIViewController` , em seguida, é colocado na `UINavigationController` pelo `PushViewController` método. Portanto, a interface do usuário definido no xamarin. Forms `CallHistoryPage` classe será exibida, conforme mostrado na seguinte captura de tela:

[![](native-forms-images/ios-callhistorypage.png "iOS CallHistoryPage")](native-forms-images/ios-callhistorypage-large.png#lightbox "iOS CallHistoryPage")

Quando o `CallHistoryPage` é exibido, tocar a parte de trás seta será exibida a `UIViewController` para o `CallHistoryPage` classe o `UINavigationController`, retornando o usuário para o `UIViewController` para o `PhonewordPage` classe.

## <a name="android"></a>Android

No Android, o `OnCreate` substituir no `MainActivity` classe normalmente é o local para executar o aplicativo tarefas relacionadas a inicialização. O seguinte exemplo de código mostra o `MainActivity` classe no aplicativo de exemplo:

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

        var mainPage = new PhonewordPage().CreateFragment(this);
        FragmentManager
            .BeginTransaction()
            .Replace(Resource.Id.fragment_frame_layout, mainPage)
            .Commit();
        ...
    }
    ...
}
```

O `OnCreate` método executa as seguintes tarefas:

- Xamarin. Forms é inicializada, chamando o `Forms.Init` método.
- Uma referência para o `MainActivity` classe é armazenada no `static` `Instance` campo. Isso é fornecer um mecanismo para outras classes chamar os métodos definidos no `MainActivity` classe.
- O `Activity` o conteúdo está definido de um recurso de layout. O aplicativo de exemplo, o layout consiste em uma `LinearLayout` que contém um `Toolbar`e um `FrameLayout` para atuar como um contêiner de fragmento.
- O `Toolbar` é recuperado e definido como a barra de ação para o `Activity`, e o título da barra de ação é definido.
- O `PhonewordPage` classe, que é um xamarin. Forms [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-derivado de página definido em XAML, que é construído e convertido em um `Fragment` usando o `CreateFragment` método de extensão.
- O `FragmentManager` classe cria e confirma uma transação que substitui o `FrameLayout` instância com o `Fragment` para o `PhonewordPage` classe.

Para obter mais informações sobre fragmentos, consulte [fragmentos](~/android/platform/fragments/index.md).

> [!NOTE]
> Além de `CreateFragment` método de extensão, xamarin. Forms também inclui um `CreateSupportFragment` método. O `CreateFragment` método cria um `Android.App.Fragment` que pode ser usado em aplicativos que se destinam a API de 11 ou maior. O `CreateSupportFragment` método cria um `Android.Support.V4.App.Fragment` que podem ser usados em aplicativos que usam versões de API antes de 11.

Uma vez o `OnCreate` método executado, a interface do usuário definido no xamarin. Forms `PhonewordPage` classe será exibida, conforme mostrado na seguinte captura de tela:

[![](native-forms-images/android-phonewordpage.png "Android PhonewordPage")](native-forms-images/android-phonewordpage-large.png#lightbox "Android PhonewordPage")

Interagir com a interface do usuário, por exemplo, tocando em uma [ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/), resultará em manipuladores de eventos de `PhonewordPage` por trás do código em execução. Por exemplo, quando um usuário toca o **histórico de chamadas** botão, o manipulador de eventos a seguir é executado:

```csharp
void OnCallHistory(object sender, EventArgs e)
{
    MainActivity.Instance.NavigateToCallHistoryPage();
}
```

O `static` `MainActivity.Instance` campo permite que o `MainActivity.NavigateToCallHistoryPage` método ser chamado, o que é mostrado no exemplo de código a seguir:

```csharp
public void NavigateToCallHistoryPage()
{
    var callHistoryPage = new CallHistoryPage().CreateFragment(this);
    FragmentManager
        .BeginTransaction()
        .AddToBackStack(null)
        .Replace(Resource.Id.fragment_frame_layout, callHistoryPage)
        .Commit();
}
```

O `NavigateToCallHistoryPage` método converte o xamarin. Forms [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-derivado de página para um `Fragment` com o `CreateFragment` método de extensão e adiciona o `Fragment` pilha de volta para o fragmento. Portanto, a interface do usuário definido no xamarin. Forms `CallHistoryPage` será exibida, conforme mostrado na seguinte captura de tela:

[![](native-forms-images/android-callhistorypage.png "Android CallHistoryPage")](native-forms-images/android-callhistorypage-large.png#lightbox "CallHistoryPage Android")

Quando o `CallHistoryPage` é exibido, tocar a parte de trás seta será exibida a `Fragment` para o `CallHistoryPage` da pilha voltar do fragmento, retornando o usuário para o `Fragment` para o `PhonewordPage` classe.

### <a name="enabling-back-navigation-support"></a>Habilitar o suporte a navegação regressiva

O `FragmentManager` classe tiver um `BackStackChanged` evento é acionado sempre que o conteúdo da pilha voltar do fragmento é alterado. O `OnCreate` método o `MainActivity` classe contém um manipulador de eventos de anônimo para este evento:

```csharp
FragmentManager.BackStackChanged += (sender, e) =>
{
    bool hasBack = FragmentManager.BackStackEntryCount > 0;
    SupportActionBar.SetHomeButtonEnabled(hasBack);
    SupportActionBar.SetDisplayHomeAsUpEnabled(hasBack);
    SupportActionBar.Title = hasBack ? "Call History" : "Phoneword";
};
```

Este manipulador de eventos exibe um botão Voltar na barra de ação, desde que haja um ou mais `Fragment` instâncias no fragmento de pilha de volta. A resposta ao tocar no botão Voltar é tratada pelo `OnOptionsItemSelected` substituir:

```csharp
public override bool OnOptionsItemSelected(Android.Views.IMenuItem item)
{
    if (item.ItemId == global::Android.Resource.Id.Home && FragmentManager.BackStackEntryCount > 0)
    {
        FragmentManager.PopBackStack();
        return true;
    }
    return base.OnOptionsItemSelected(item);
}
```

O `OnOptionsItemSelected` substituição é chamada sempre que um item no menu de opções é selecionado. Essa implementação aparece o fragmento atual da pilha voltar do fragmento, desde que foi selecionado no botão Voltar e há um ou mais `Fragment` instâncias no fragmento de pilha de volta.

### <a name="multiple-activities"></a>Várias atividades

Quando um aplicativo é composto por várias atividades, [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-páginas derivadas podem ser inseridas em cada uma das atividades. Nesse cenário, o `Forms.Init` método precisa ser chamado somente no `OnCreate` substituir do primeiro `Activity` que incorpora um xamarin. Forms `ContentPage`. No entanto, isso tem o seguinte impacto:

- O valor de `Xamarin.Forms.Color.Accent` será extraído o `Activity` que chamou o `Forms.Init` método.
- O valor de `Xamarin.Forms.Application.Current` será associado a `Activity` que chamou o `Forms.Init` método.

### <a name="choosing-a-file"></a>Escolher um arquivo

Ao incorporar uma [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-derivado de página que usa um [ `WebView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/) que precisa para dar suporte um HTML "Escolher arquivo" botão, o `Activity` precisará substituir o `OnActivityResult` método:

```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);
    ActivityResultCallbackRegistry.InvokeCallback(requestCode, resultCode, data);
}
```

## <a name="uwp"></a>UWP

Em UWP, nativo `App` classe normalmente é o local para executar o aplicativo tarefas relacionadas a inicialização. Xamarin. Forms é normalmente inicializado, em aplicativos UWP xamarin. Forms, no `OnLaunched` substituir em nativo `App` classe, para passar o `LaunchActivatedEventArgs` argumento para o `Forms.Init` método. Por esse motivo, os aplicativos UWP nativos que consomem uma xamarin. Forms [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-página derivada pode chamar mais facilmente o `Forms.Init` método do `App.OnLaunched` método.

Por padrão, o nativo `App` classe inicia o `MainPage` classe como a primeira página do aplicativo. O seguinte exemplo de código mostra o `MainPage` classe no aplicativo de exemplo:

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

- O cache está habilitado para a página, para que um novo `MainPage` não é criada quando um usuário navega de volta para a página.
- Uma referência para o `MainPage` classe é armazenada no `static` `Instance` campo. Isso é fornecer um mecanismo para outras classes chamar os métodos definidos no `MainPage` classe.
- O `PhonewordPage` classe, que é um xamarin. Forms [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-derivado de página definido em XAML, que é construído e convertido em um `FrameworkElement` usando o `CreateFrameworkElement` método de extensão e defina como o conteúdo da `MainPage` classe.

Uma vez o `MainPage` construtor foi executada, a interface do usuário definido no xamarin. Forms `PhonewordPage` classe será exibida, conforme mostrado na seguinte captura de tela:

[![](native-forms-images/uwp-phonewordpage.png "UWP PhonewordPage")](native-forms-images/uwp-phonewordpage-large.png#lightbox "PhonewordPage UWP")

Interagir com a interface do usuário, por exemplo, tocando em uma [ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/), resultará em manipuladores de eventos de `PhonewordPage` por trás do código em execução. Por exemplo, quando um usuário toca o **histórico de chamadas** botão, o manipulador de eventos a seguir é executado:

```csharp
void OnCallHistory(object sender, EventArgs e)
{
    Phoneword.UWP.MainPage.Instance.NavigateToCallHistoryPage();
}
```

O `static` `MainPage.Instance` campo permite que o `MainPage.NavigateToCallHistoryPage` método ser chamado, o que é mostrado no exemplo de código a seguir:

```csharp
public void NavigateToCallHistoryPage()
{
    this.Frame.Navigate(new CallHistoryPage());
}
```

Navegação no UWP normalmente é executada com o `Frame.Navigate` método, que usa um `Page` argumento. Xamarin. Forms define uma `Frame.Navigate` método de extensão que utiliza um [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-derivado instância page. Portanto, quando o `NavigateToCallHistoryPage` método é executado, a interface do usuário definido no xamarin. Forms `CallHistoryPage` será exibida, conforme mostrado na seguinte captura de tela:

[![](native-forms-images/uwp-callhistorypage.png "UWP CallHistoryPage")](native-forms-images/uwp-callhistorypage-large.png#lightbox "CallHistoryPage UWP")

Quando o `CallHistoryPage` é exibido, tocar a parte de trás seta será exibida a `FrameworkElement` para o `CallHistoryPage` da pilha de volta no aplicativo, retornando o usuário para o `FrameworkElement` para o `PhonewordPage` classe.

### <a name="enabling-back-navigation-support"></a>Habilitar o suporte a navegação regressiva

Em UWP, aplicativos devem habilitar navegação regressiva para todos os hardware e software back botões, em diferentes fatores. Isso pode ser feito ao registrar um manipulador de eventos para o `BackRequested` evento, que pode ser executado no `OnLaunched` método nativo `App` classe:

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

Quando o aplicativo é iniciado, o `GetForCurrentView` método recupera o `SystemNavigationManager` objeto associado com o modo de exibição atual, em seguida, registra um manipulador de eventos para o `BackRequested` evento. O aplicativo recebe esse evento somente se ele é o aplicativo de primeiro plano e, em resposta, chama o `OnBackRequested` manipulador de eventos:

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

O `OnBackRequested` chamadas do manipulador de eventos de `GoBack` método no quadro raiz do aplicativo e define o `BackRequestedEventArgs.Handled` propriedade `true` para marcar o evento como manipulado. Falha ao marcar o evento como manipulado pode resultar no sistema de navegar para fora do aplicativo (na família do dispositivo móvel) ou ignorando o evento (na família do dispositivo de área de trabalho).

O aplicativo se baseia em um botão Voltar do sistema fornecido em um telefone, mas escolhe se deseja mostrar um botão Voltar na barra de título em dispositivos de área de trabalho. Isso é feito definindo o `AppViewBackButtonVisibility` propriedade para um do `AppViewBackButtonVisibility` valores de enumeração:

```csharp
void OnNavigated(object sender, NavigationEventArgs e)
{
    SystemNavigationManager.GetForCurrentView().AppViewBackButtonVisibility =
        ((Frame)sender).CanGoBack ? AppViewBackButtonVisibility.Visible : AppViewBackButtonVisibility.Collapsed;
}
```

O `OnNavigated` manipulador de eventos, que é executado em resposta ao `Navigated` eventos acionados, atualiza a visibilidade do título da barra para trás quando a navegação de página ocorre. Isso garante que o botão Voltar de barra de título fica visível quando a pilha de volta no aplicativo não está vazia ou removido da barra de título, se a pilha de volta no aplicativo está vazia.

Para obter mais informações sobre o suporte de navegação regressiva UWP, consulte [histórico de navegação e para trás navegação para aplicativos UWP](/windows/uwp/design/basics/navigation-history-and-backwards-navigation/).

## <a name="summary"></a>Resumo

Formulários nativo permitem xamarin. Forms [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-derivado páginas a serem consumidos por projetos nativos do xamarin, xamarin e Windows UWP (plataforma Universal). Projetos nativos podem consumir `ContentPage`-derivado páginas que são adicionadas diretamente ao projeto, ou de um PCL, a biblioteca padrão do .NET ou o projeto compartilhado. Este artigo explicou como consumir `ContentPage`-derivado páginas que são adicionadas diretamente aos projetos nativos e como navegar entre eles.


## <a name="related-links"></a>Links relacionados

- [NativeForms (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/Native2Forms/)
- [Exibições nativas](~/xamarin-forms/platform/native-views/index.md)
