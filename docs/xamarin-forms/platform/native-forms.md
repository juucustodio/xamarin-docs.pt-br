---
title: Xamarin. Forms em projetos nativos do Xamarin
description: Este artigo explica como consumir páginas derivadas de ContentPage que são adicionadas diretamente aos projetos nativos do Xamarin e como navegar entre elas.
ms.prod: xamarin
ms.assetid: f343fc21-dfb1-4364-a332-9da6705d36bc
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/19/2019
ms.openlocfilehash: 0c84b844455b8a792b8cbe2f4dac97097e5ebd97
ms.sourcegitcommit: dad4dfcd194b63ec9e903363351b6d9e543d4888
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/18/2019
ms.locfileid: "69621053"
---
# <a name="xamarinforms-in-xamarin-native-projects"></a>Xamarin. Forms em projetos nativos do Xamarin

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/native2forms)

Normalmente, um aplicativo Xamarin. Forms inclui uma ou mais páginas que derivam de [`ContentPage`](xref:Xamarin.Forms.ContentPage), e essas páginas são compartilhadas por todas as plataformas em um projeto de biblioteca .net Standard ou projeto compartilhado. No entanto, os formulários nativos permitem que páginas derivadas de `ContentPage` sejam adicionadas diretamente a aplicativos Xamarin. iOS, Xamarin. Android e UWP nativos. Em comparação com o fato de o projeto nativo consumir páginas derivadas `ContentPage` de um projeto de biblioteca .NET Standard ou de um projeto compartilhado, a vantagem de adicionar páginas diretamente a projetos nativos é que as páginas podem ser estendidas com exibições nativas. Exibições nativas podem então ser nomeadas em XAML com `x:Name` e referenciadas do code-behind. Para obter mais informações sobre exibições nativas, consulte [exibições nativas](~/xamarin-forms/platform/native-views/index.md).

O processo para consumir uma página derivada de [`ContentPage`](xref:Xamarin.Forms.ContentPage)do Xamarin. Forms em um projeto nativo é o seguinte:

1. Adicione o pacote NuGet do Xamarin. Forms ao projeto nativo.
1. Adicione a página derivada de [`ContentPage`](xref:Xamarin.Forms.ContentPage)e quaisquer dependências ao projeto nativo.
1. Chame o método `Forms.Init`.
1. Construa uma instância da página derivada de [`ContentPage`](xref:Xamarin.Forms.ContentPage)e converta-a para o tipo nativo apropriado usando um dos seguintes métodos de extensão: `CreateViewController` para iOS, `CreateSupportFragment` para Android ou `CreateFrameworkElement` para UWP.
1. Navegue até a representação de tipo nativo da página derivada de [`ContentPage`](xref:Xamarin.Forms.ContentPage)usando a API de navegação nativa.

O Xamarin. Forms deve ser inicializado chamando o método `Forms.Init` antes que um projeto nativo possa construir uma página derivada de [`ContentPage`](xref:Xamarin.Forms.ContentPage). Escolher quando fazer isso depende principalmente de quando é mais conveniente em seu fluxo de aplicativo – ele pode ser executado na inicialização do aplicativo ou logo antes da construção da página derivada de `ContentPage`. Neste artigo e os aplicativos de exemplo que o acompanham, o método `Forms.Init` é chamado na inicialização do aplicativo.

> [!NOTE]
> A solução de aplicativo de exemplo **NativeForms** não contém nenhum projeto Xamarin. Forms. Em vez disso, ele consiste em um projeto Xamarin. iOS, um projeto Xamarin. Android e um projeto UWP. Cada projeto é um projeto nativo que usa formulários nativos para consumir páginas derivadas de [`ContentPage`](xref:Xamarin.Forms.ContentPage). No entanto, não há motivo para que os projetos nativos não consumam páginas derivadas `ContentPage` de um projeto de biblioteca .NET Standard ou projeto compartilhado.

Ao usar formulários nativos, os recursos do Xamarin. Forms, como [`DependencyService`](xref:Xamarin.Forms.DependencyService), [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter)e o mecanismo de vinculação de dados, tudo ainda funcionam. No entanto, a navegação de página deve ser executada usando a API de navegação nativa.

## <a name="ios"></a>iOS

No iOS, a substituição de `FinishedLaunching` na classe `AppDelegate` é normalmente o local para executar tarefas relacionadas à inicialização do aplicativo. Ele é chamado depois que o aplicativo é iniciado e é geralmente substituído para configurar a janela principal e o controlador de exibição. O exemplo de código a seguir mostra a classe `AppDelegate` no aplicativo de exemplo:

```csharp
[Register("AppDelegate")]
public class AppDelegate : UIApplicationDelegate
{
    public static AppDelegate Instance;
    UIWindow _window;
    AppNavigationController _navigation;

    public static string FolderPath { get; private set; }

    public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
    {
        Forms.Init();

        Instance = this;
        _window = new UIWindow(UIScreen.MainScreen.Bounds);

        UINavigationBar.Appearance.SetTitleTextAttributes(new UITextAttributes
        {
            TextColor = UIColor.Black
        });

        FolderPath = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData));
        UIViewController mainPage = new NotesPage().CreateViewController();
        mainPage.Title = "Notes";

        _navigation = new AppNavigationController(mainPage);
        _window.RootViewController = _navigation;
        _window.MakeKeyAndVisible();

        return true;
    }
    // ...
}
```

O método `FinishedLaunching` executa as seguintes tarefas:

- O Xamarin. Forms é inicializado chamando o método `Forms.Init`.
- Uma referência à classe `AppDelegate` é armazenada no campo `static` `Instance`. Isso é para fornecer um mecanismo para outras classes chamarem métodos definidos na classe `AppDelegate`.
- O `UIWindow`, que é o contêiner principal para exibições em aplicativos nativos do iOS, é criado.
- A propriedade `FolderPath` é inicializada para um caminho no dispositivo em que os dados de observação serão armazenados.
- A classe `NotesPage`, que é uma página derivada de [`ContentPage`](xref:Xamarin.Forms.ContentPage)do Xamarin. Forms definida em XAML, é construída e convertida em um `UIViewController` usando o método de extensão `CreateViewController`.
- A propriedade `Title` da `UIViewController` é definida, que será exibida no `UINavigationBar`.
- Um `AppNavigationController` é criado para gerenciar a navegação hierárquica. Essa é uma classe de controlador de navegação personalizada, que deriva de `UINavigationController`. O objeto `AppNavigationController` gerencia uma pilha de controladores de exibição, e o `UIViewController` passado para o Construtor será apresentado inicialmente quando o `AppNavigationController` for carregado.
- O objeto `AppNavigationController` é definido como o `UIViewController` de nível superior para o `UIWindow`, e o `UIWindow` é definido como a janela de chave para o aplicativo e torna-se visível.

Depois que o método de `FinishedLaunching` for executado, a interface do usuário definida na classe do Xamarin. Forms `NotesPage` será exibida, conforme mostrado na seguinte captura de tela:

[![Captura de tela de um aplicativo Xamarin. iOS que usa uma interface do usuário definida em XAML](native-forms-images/ios-notespage.png "Aplicativo Xamarin. iOS com uma interface do usuário XAML")](native-forms-images/ios-notespage-large.png#lightbox "Aplicativo Xamarin. iOS com uma interface do usuário XAML")

Interagir com a interface do usuário, por exemplo, tocando no [`Button`](xref:Xamarin.Forms.Button)de +, resultará no seguinte manipulador de eventos na execução do code-behind `NotesPage`:

```csharp
void OnNoteAddedClicked(object sender, EventArgs e)
{
    AppDelegate.Instance.NavigateToNoteEntryPage(new Note());
}
```

O campo `static` `AppDelegate.Instance` permite que o método `AppDelegate.NavigateToNoteEntryPage` seja invocado, que é mostrado no exemplo de código a seguir:

```csharp
public void NavigateToNoteEntryPage(Note note)
{
    var noteEntryPage = new NoteEntryPage
    {
        BindingContext = note
    }.CreateViewController();
    noteEntryPage.Title = "Note Entry";
    _navigation.PushViewController(noteEntryPage, true);
}
```

O método `NavigateToNoteEntryPage` converte a página derivada de [`ContentPage`](xref:Xamarin.Forms.ContentPage)do Xamarin. Forms em uma `UIViewController` com o método de extensão `CreateViewController` e define a propriedade `Title` do `UIViewController`. Em seguida, o `UIViewController` é enviado para `AppNavigationController` pelo método `PushViewController`. Portanto, a interface do usuário definida na classe `NoteEntryPage` Xamarin. Forms será exibida, conforme mostrado na seguinte captura de tela:

[![Captura de tela de um aplicativo Xamarin. iOS que usa uma interface do usuário definida em XAML](native-forms-images/ios-noteentrypage.png "Aplicativo Xamarin. iOS com uma interface do usuário XAML")](native-forms-images/ios-noteentrypage-large.png#lightbox "Aplicativo Xamarin. iOS com uma interface do usuário XAML")

Quando o `NoteEntryPage` for exibido, a navegação de volta exibirá o `UIViewController` da classe `NoteEntryPage` da `AppNavigationController`, retornando o usuário para a `UIViewController` da classe `NotesPage`. No entanto, o pop-up de uma `UIViewController` da pilha de navegação nativa do iOS não descarta automaticamente o `UIViewController` e o objeto `Page` anexado. Portanto, a classe `AppNavigationController` substitui o método `PopViewController` para descartar os controladores de exibição na navegação para trás:

```csharp
public class AppNavigationController : UINavigationController
{
    //...
    public override UIViewController PopViewController(bool animated)
    {
        UIViewController topView = TopViewController;
        if (topView != null)
        {
            // Dispose of ViewController on back navigation.
            topView.Dispose();
        }
        return base.PopViewController(animated);
    }
}
```

A substituição de `PopViewController` chama o método `Dispose` no objeto `UIViewController` que foi tirado da pilha de navegação nativa do iOS. A falha em fazer isso resultará na `UIViewController` e anexada `Page` objeto que está sendo órfão.

> [!IMPORTANT]
> Os objetos órfãos não podem ser coletados pelo lixo e, portanto, resultar em um vazamento de memória.

## <a name="android"></a>Android

No Android, a substituição de `OnCreate` na classe `MainActivity` é normalmente o local para executar tarefas relacionadas à inicialização do aplicativo. O exemplo de código a seguir mostra a classe `MainActivity` no aplicativo de exemplo:

```csharp
public class MainActivity : AppCompatActivity
{
    public static string FolderPath { get; private set; }

    public static MainActivity Instance;

    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);

        Forms.Init(this, bundle);
        Instance = this;

        SetContentView(Resource.Layout.Main);
        var toolbar = FindViewById<Toolbar>(Resource.Id.toolbar);
        SetSupportActionBar(toolbar);
        SupportActionBar.Title = "Notes";

        FolderPath = Path.Combine(System.Environment.GetFolderPath(System.Environment.SpecialFolder.LocalApplicationData));
        Android.Support.V4.App.Fragment mainPage = new NotesPage().CreateSupportFragment(this);
        SupportFragmentManager
            .BeginTransaction()
            .Replace(Resource.Id.fragment_frame_layout, mainPage)
            .Commit();
        ...
    }
    ...
}
```

O método `OnCreate` executa as seguintes tarefas:

- O Xamarin. Forms é inicializado chamando o método `Forms.Init`.
- Uma referência à classe `MainActivity` é armazenada no campo `static` `Instance`. Isso é para fornecer um mecanismo para outras classes chamarem métodos definidos na classe `MainActivity`.
- O conteúdo do `Activity` é definido de um recurso de layout. No aplicativo de exemplo, o layout consiste em um `LinearLayout` que contém um `Toolbar` e um `FrameLayout` para atuar como um contêiner de fragmento.
- O `Toolbar` é recuperado e definido como a barra de ação para a `Activity` e o título da barra de ação é definido.
- A propriedade `FolderPath` é inicializada para um caminho no dispositivo em que os dados de observação serão armazenados.
- A classe `NotesPage`, que é uma página derivada de [`ContentPage`](xref:Xamarin.Forms.ContentPage)do Xamarin. Forms definida em XAML, é construída e convertida em um `Fragment` usando o método de extensão `CreateSupportFragment`.
- A classe `SupportFragmentManager` cria e confirma uma transação que substitui a instância `FrameLayout` pela `Fragment` da classe `NotesPage`.

Para obter mais informações sobre fragmentos, consulte [fragmentos](~/android/platform/fragments/index.md).

Depois que o método de `OnCreate` for executado, a interface do usuário definida na classe do Xamarin. Forms `NotesPage` será exibida, conforme mostrado na seguinte captura de tela:

[![Captura de tela de um aplicativo Xamarin. Android que usa uma interface do usuário definida em XAML](native-forms-images/android-notespage.png "Aplicativo Xamarin. Android com uma interface do usuário XAML")](native-forms-images/android-notespage-large.png#lightbox "Aplicativo Xamarin. Android com uma interface do usuário XAML")

Interagir com a interface do usuário, por exemplo, tocando no [`Button`](xref:Xamarin.Forms.Button)de +, resultará no seguinte manipulador de eventos na execução do code-behind `NotesPage`:

```csharp
void OnNoteAddedClicked(object sender, EventArgs e)
{
    MainActivity.Instance.NavigateToNoteEntryPage(new Note());
}
```

O campo `static` `MainActivity.Instance` permite que o método `MainActivity.NavigateToNoteEntryPage` seja invocado, que é mostrado no exemplo de código a seguir:

```csharp
public void NavigateToNoteEntryPage(Note note)
{
    Android.Support.V4.App.Fragment noteEntryPage = new NoteEntryPage
    {
        BindingContext = note
    }.CreateSupportFragment(this);
    SupportFragmentManager
        .BeginTransaction()
        .AddToBackStack(null)
        .Replace(Resource.Id.fragment_frame_layout, noteEntryPage)
        .Commit();
}
```

O método `NavigateToNoteEntryPage` converte a página derivada do Xamarin. Forms [`ContentPage`](xref:Xamarin.Forms.ContentPage)em uma `Fragment` com o método de extensão `CreateSupportFragment` e adiciona o `Fragment` à pilha de retorno do fragmento. Portanto, a interface do usuário definida no `NoteEntryPage` Xamarin. Forms será exibida, conforme mostrado na seguinte captura de tela:

[![Captura de tela de um aplicativo Xamarin. Android que usa uma interface do usuário definida em XAML](native-forms-images/android-noteentrypage.png "Aplicativo Xamarin. Android com uma interface do usuário XAML")](native-forms-images/android-noteentrypage-large.png#lightbox "Aplicativo Xamarin. Android com uma interface do usuário XAML")

Quando o `NoteEntryPage` for exibido, tocar na seta voltar exibirá o `Fragment` do `NoteEntryPage` da pilha de retorno do fragmento, retornando o usuário para a `Fragment` da classe `NotesPage`.

### <a name="enable-back-navigation-support"></a>Habilitar suporte à navegação de volta

A classe `SupportFragmentManager` tem um evento `BackStackChanged` que é acionado sempre que o conteúdo da pilha de trás do fragmento é alterado. O método `OnCreate` na classe `MainActivity` contém um manipulador de eventos anônimo para este evento:

```csharp
SupportFragmentManager.BackStackChanged += (sender, e) =>
{
    bool hasBack = SupportFragmentManager.BackStackEntryCount > 0;
    SupportActionBar.SetHomeButtonEnabled(hasBack);
    SupportActionBar.SetDisplayHomeAsUpEnabled(hasBack);
    SupportActionBar.Title = hasBack ? "Note Entry" : "Notes";
};
```

Esse manipulador de eventos exibe um botão voltar na barra de ação, desde que haja uma ou mais instâncias de `Fragment` na pilha de retorno do fragmento. A resposta para tocar no botão voltar é manipulada pela substituição de `OnOptionsItemSelected`:

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

A substituição de `OnOptionsItemSelected` é chamada sempre que um item no menu opções é selecionado. Essa implementação exibe o fragmento atual da pilha de fundo do fragmento, desde que o botão voltar tenha sido selecionado e haja uma ou mais instâncias de `Fragment` na pilha de retorno do fragmento.

### <a name="multiple-activities"></a>Várias atividades

Quando um aplicativo é composto por várias atividades, as páginas derivadas de [`ContentPage`](xref:Xamarin.Forms.ContentPage)podem ser inseridas em cada uma das atividades. Nesse cenário, o método `Forms.Init` precisa ser chamado apenas na substituição `OnCreate` do primeiro `Activity` que incorpora um Xamarin. Forms `ContentPage`. No entanto, isso tem o seguinte impacto:

- O valor de `Xamarin.Forms.Color.Accent` será obtido do `Activity` que chamou o método `Forms.Init`.
- O valor de `Xamarin.Forms.Application.Current` será associado ao `Activity` que chamou o método `Forms.Init`.

### <a name="choose-a-file"></a>Escolher um arquivo

Ao inserir uma página derivada de [`ContentPage`](xref:Xamarin.Forms.ContentPage)que usa um [`WebView`](xref:Xamarin.Forms.WebView) que precisa dar suporte a um botão HTML "Choose File", a `Activity` precisará substituir o método `OnActivityResult`:

```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);
    ActivityResultCallbackRegistry.InvokeCallback(requestCode, resultCode, data);
}
```

## <a name="uwp"></a>UWP

No UWP, a classe de `App` nativa normalmente é o local para executar tarefas relacionadas à inicialização do aplicativo. O xamarin. Forms geralmente é inicializado, em aplicativos UWP do Xamarin. Forms, na `OnLaunched` substituir na classe nativa `App`, para passar o argumento `LaunchActivatedEventArgs` para o método `Forms.Init`. Por esse motivo, os aplicativos UWP nativos que consomem uma página derivada de [`ContentPage`](xref:Xamarin.Forms.ContentPage)do Xamarin. Forms podem chamar com mais facilidade o método `Forms.Init` do método `App.OnLaunched`.

Por padrão, a classe de `App` nativa inicia a classe `MainPage` como a primeira página do aplicativo. O exemplo de código a seguir mostra a classe `MainPage` no aplicativo de exemplo:

```csharp
public sealed partial class MainPage : Page
{
    public static MainPage Instance;

    public static string FolderPath { get; private set; }

    public MainPage()
    {
        this.InitializeComponent();
        this.NavigationCacheMode = NavigationCacheMode.Enabled;
        Instance = this;
        FolderPath = Path.Combine(System.Environment.GetFolderPath(System.Environment.SpecialFolder.LocalApplicationData));
        this.Content = new Notes.UWP.Views.NotesPage().CreateFrameworkElement();
    }
    ...
}
```

O construtor de `MainPage` executa as seguintes tarefas:

- O Caching está habilitado para a página, de forma que um novo `MainPage` não seja construído quando um usuário navega de volta para a página.
- Uma referência à classe `MainPage` é armazenada no campo `static` `Instance`. Isso é para fornecer um mecanismo para outras classes chamarem métodos definidos na classe `MainPage`.
- A propriedade `FolderPath` é inicializada para um caminho no dispositivo em que os dados de observação serão armazenados.
- A classe `NotesPage`, que é uma página derivada de [`ContentPage`](xref:Xamarin.Forms.ContentPage)do Xamarin. Forms definida em XAML, é construída e convertida em um `FrameworkElement` usando o método de extensão `CreateFrameworkElement` e, em seguida, definida como o conteúdo da classe `MainPage`.

Depois que o construtor de `MainPage` for executado, a interface do usuário definida na classe do Xamarin. Forms `NotesPage` será exibida, conforme mostrado na seguinte captura de tela:

[![Captura de tela de um aplicativo UWP que usa uma interface do usuário definida com o XAML do Xamarin. Forms](native-forms-images/uwp-notespage.png "Aplicativo UWP com uma IU do XAML do Xamarin. Forms")](native-forms-images/uwp-notespage-large.png#lightbox "Aplicativo UWP com uma IU do XAML do Xamarin. Forms")

Interagir com a interface do usuário, por exemplo, tocando no [`Button`](xref:Xamarin.Forms.Button)de +, resultará no seguinte manipulador de eventos na execução do code-behind `NotesPage`:

```csharp
void OnNoteAddedClicked(object sender, EventArgs e)
{
    MainPage.Instance.NavigateToNoteEntryPage(new Note());
}
```

O campo `static` `MainPage.Instance` permite que o método `MainPage.NavigateToNoteEntryPage` seja invocado, que é mostrado no exemplo de código a seguir:

```csharp
public void NavigateToNoteEntryPage(Note note)
{
    this.Frame.Navigate(new NoteEntryPage
    {
        BindingContext = note
    });
}
```

A navegação em UWP normalmente é executada com o método `Frame.Navigate`, que usa um argumento `Page`. O Xamarin. Forms define um método de extensão de `Frame.Navigate` que usa uma instância de página derivada de [`ContentPage`](xref:Xamarin.Forms.ContentPage). Portanto, quando o método `NavigateToNoteEntryPage` for executado, a interface do usuário definida na `NoteEntryPage` Xamarin. Forms será exibida, conforme mostrado na seguinte captura de tela:

[![Captura de tela de um aplicativo UWP que usa uma interface do usuário definida com o XAML do Xamarin. Forms](native-forms-images/uwp-noteentrypage.png "Aplicativo UWP com uma IU do XAML do Xamarin. Forms")](native-forms-images/uwp-noteentrypage-large.png#lightbox "Aplicativo UWP com uma IU do XAML do Xamarin. Forms")

Quando o `NoteEntryPage` for exibido, tocar na seta voltar exibirá o `FrameworkElement` do `NoteEntryPage` da pilha voltar no aplicativo, retornando o usuário para a `FrameworkElement` da classe `NotesPage`.

### <a name="enable-back-navigation-support"></a>Habilitar suporte à navegação de volta

No UWP, os aplicativos devem habilitar a navegação regressiva para todos os botões de backup de hardware e software, em fatores forma de dispositivo diferentes. Isso pode ser feito registrando um manipulador de eventos para o evento `BackRequested`, que pode ser executado no método `OnLaunched` na classe `App` nativa:

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

Quando o aplicativo é iniciado, o método `GetForCurrentView` recupera o objeto `SystemNavigationManager` associado ao modo de exibição atual e registra um manipulador de eventos para o evento `BackRequested`. O aplicativo só receberá esse evento se for o aplicativo em primeiro plano e, em resposta, chamará o manipulador de eventos `OnBackRequested`:

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

O manipulador de eventos `OnBackRequested` chama o método `GoBack` no quadro raiz do aplicativo e define a propriedade `BackRequestedEventArgs.Handled` como `true` para marcar o evento como manipulado. Falha ao marcar o evento como manipulado pode fazer com que o evento seja ignorado.

O aplicativo escolhe se um botão voltar deve ser exibido na barra de título. Isso é feito definindo a propriedade `AppViewBackButtonVisibility` como um dos valores de enumeração `AppViewBackButtonVisibility`:

```csharp
void OnNavigated(object sender, NavigationEventArgs e)
{
    SystemNavigationManager.GetForCurrentView().AppViewBackButtonVisibility =
        ((Frame)sender).CanGoBack ? AppViewBackButtonVisibility.Visible : AppViewBackButtonVisibility.Collapsed;
}
```

O manipulador de eventos `OnNavigated`, que é executado em resposta ao acionamento do evento de `Navigated`, atualiza a visibilidade do botão voltar da barra de título quando ocorre a navegação da página. Isso garante que o botão voltar da barra de título fique visível se a pilha voltar no aplicativo não estiver vazia ou removida da barra de título se a pilha voltar no aplicativo estiver vazia.

Para obter mais informações sobre o suporte à navegação de volta no UWP, consulte [histórico de navegação e navegação para aplicativos UWP](/windows/uwp/design/basics/navigation-history-and-backwards-navigation/).

## <a name="related-links"></a>Links relacionados

- [NativeForms (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/native2forms)
- [Exibições nativas](~/xamarin-forms/platform/native-views/index.md)
