---
title: Xamarin. Forms em projetos do Xamarin nativo
description: Este artigo explica como consumir páginas ContentPage derivadas que são adicionadas diretamente a projetos de Xamarin nativos e como navegar entre eles.
ms.prod: xamarin
ms.assetid: f343fc21-dfb1-4364-a332-9da6705d36bc
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/19/2019
ms.openlocfilehash: 83b445b8379ad5181ab1dce142cca06625be79ad
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75487328"
---
# <a name="xamarinforms-in-xamarin-native-projects"></a>Xamarin. Forms em projetos do Xamarin nativo

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/native2forms)

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

O `FinishedLaunching` método realiza as seguintes tarefas:

- Xamarin. Forms é inicializado chamando o `Forms.Init` método.
- Uma referência para o `AppDelegate` classe é armazenada na `static` `Instance` campo. Isso é fornecer um mecanismo para chamar métodos definidos em outras classes de `AppDelegate` classe.
- O `UIWindow`, que é o principal contêiner para modos de exibição em aplicativos nativos iOS, é criada.
- A propriedade `FolderPath` é inicializada para um caminho no dispositivo em que os dados de observação serão armazenados.
- O `NotesPage` classe, que é um xamarin. Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-derivado da página definida em XAML, que é construído e convertido em um `UIViewController` usando o `CreateViewController` método de extensão.
- O `Title` propriedade do `UIViewController` for definido, que será exibido o `UINavigationBar`.
- Um `AppNavigationController` é criada para gerenciar a navegação hierárquica. Essa é uma classe de controlador de navegação personalizada, que deriva de `UINavigationController`. O objeto `AppNavigationController` gerencia uma pilha de controladores de exibição, e o `UIViewController` passado para o Construtor será apresentado inicialmente quando o `AppNavigationController` for carregado.
- O objeto `AppNavigationController` é definido como o `UIViewController` de nível superior para o `UIWindow`, e o `UIWindow` é definido como a janela de chave para o aplicativo e torna-se visível.

Uma vez a `FinishedLaunching` método foi executado, a interface do usuário definido no xamarin. Forms `NotesPage` classe será exibida, conforme mostrado na seguinte captura de tela:

[![Captura de tela de um aplicativo Xamarin. iOS que usa uma interface do usuário definida em XAML](native-forms-images/ios-notespage.png "Aplicativo Xamarin. iOS com uma interface do usuário XAML")](native-forms-images/ios-notespage-large.png#lightbox "Aplicativo Xamarin. iOS com uma interface do usuário XAML")

Interagir com a interface do usuário, por exemplo, tocando no [`Button`](xref:Xamarin.Forms.Button)de+, resultará no seguinte manipulador de eventos na execução do code-behind `NotesPage`:

```csharp
void OnNoteAddedClicked(object sender, EventArgs e)
{
    AppDelegate.Instance.NavigateToNoteEntryPage(new Note());
}
```

O `static` `AppDelegate.Instance` campo permite que o `AppDelegate.NavigateToNoteEntryPage` método a ser invocado, que é mostrado no exemplo de código a seguir:

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

O `NavigateToNoteEntryPage` método converte o xamarin. Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-derivado da página para um `UIViewController` com o `CreateViewController` o método de extensão e conjuntos do `Title` propriedade do `UIViewController`. O `UIViewController` , em seguida, é enviado por push `AppNavigationController` pelo `PushViewController` método. Portanto, a interface do usuário definido no xamarin. Forms `NoteEntryPage` classe será exibida, conforme mostrado na seguinte captura de tela:

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

No Android, o `OnCreate` substituir no `MainActivity` classe geralmente é o lugar para executar o aplicativo de tarefas relacionadas à inicialização. O seguinte exemplo de código mostra o `MainActivity` classe no aplicativo de exemplo:

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

O `OnCreate` método realiza as seguintes tarefas:

- Xamarin. Forms é inicializado chamando o `Forms.Init` método.
- Uma referência para o `MainActivity` classe é armazenada na `static` `Instance` campo. Isso é fornecer um mecanismo para chamar métodos definidos em outras classes de `MainActivity` classe.
- O `Activity` o conteúdo está definido de um recurso de layout. No aplicativo de exemplo, o layout consiste em uma `LinearLayout` que contém uma `Toolbar`e um `FrameLayout` para atuar como um contêiner de fragmento.
- O `Toolbar` é recuperado e definido como a barra de ação para o `Activity`, e o título da barra de ação está definido.
- A propriedade `FolderPath` é inicializada para um caminho no dispositivo em que os dados de observação serão armazenados.
- O `NotesPage` classe, que é um xamarin. Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-derivado da página definida em XAML, que é construído e convertido em um `Fragment` usando o `CreateSupportFragment` método de extensão.
- O `SupportFragmentManager` classe cria e confirma uma transação que substitui o `FrameLayout` da instância com o `Fragment` para o `NotesPage` classe.

Para obter mais informações sobre fragmentos, consulte [fragmentos](~/android/platform/fragments/index.md).

Uma vez a `OnCreate` método foi executado, a interface do usuário definido no xamarin. Forms `NotesPage` classe será exibida, conforme mostrado na seguinte captura de tela:

[![Captura de tela de um aplicativo Xamarin. Android que usa uma interface do usuário definida em XAML](native-forms-images/android-notespage.png "Aplicativo Xamarin. Android com uma interface do usuário XAML")](native-forms-images/android-notespage-large.png#lightbox "Aplicativo Xamarin. Android com uma interface do usuário XAML")

Interagir com a interface do usuário, por exemplo, tocando no [`Button`](xref:Xamarin.Forms.Button)de+, resultará no seguinte manipulador de eventos na execução do code-behind `NotesPage`:

```csharp
void OnNoteAddedClicked(object sender, EventArgs e)
{
    MainActivity.Instance.NavigateToNoteEntryPage(new Note());
}
```

O `static` `MainActivity.Instance` campo permite que o `MainActivity.NavigateToNoteEntryPage` método a ser invocado, que é mostrado no exemplo de código a seguir:

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

O `NavigateToNoteEntryPage` método converte o xamarin. Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-derivado da página para um `Fragment` com o `CreateSupportFragment` o método de extensão e adiciona o `Fragment` pilha de volta para o fragmento. Portanto, a interface do usuário definido no xamarin. Forms `NoteEntryPage` será exibida, conforme mostrado na seguinte captura de tela:

[![Captura de tela de um aplicativo Xamarin. Android que usa uma interface do usuário definida em XAML](native-forms-images/android-noteentrypage.png "Aplicativo Xamarin. Android com uma interface do usuário XAML")](native-forms-images/android-noteentrypage-large.png#lightbox "Aplicativo Xamarin. Android com uma interface do usuário XAML")

Quando o `NoteEntryPage` for exibida, tocando o back seta será exibida a `Fragment` para o `NoteEntryPage` da pilha voltar de fragmento, retornando o usuário para o `Fragment` para o `NotesPage` classe.

### <a name="enable-back-navigation-support"></a>Habilitar suporte à navegação de volta

O `SupportFragmentManager` classe tem um `BackStackChanged` evento é acionado sempre que o conteúdo da pilha voltar de fragmento é alterado. O `OnCreate` método no `MainActivity` classe contém um manipulador de eventos anônimos para este evento:

```csharp
SupportFragmentManager.BackStackChanged += (sender, e) =>
{
    bool hasBack = SupportFragmentManager.BackStackEntryCount > 0;
    SupportActionBar.SetHomeButtonEnabled(hasBack);
    SupportActionBar.SetDisplayHomeAsUpEnabled(hasBack);
    SupportActionBar.Title = hasBack ? "Note Entry" : "Notes";
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

### <a name="choose-a-file"></a>Escolher um arquivo

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
    NotesPage notesPage;
    NoteEntryPage noteEntryPage;
    
    public static MainPage Instance;
    public static string FolderPath { get; private set; }

    public MainPage()
    {
        this.InitializeComponent();
        this.NavigationCacheMode = NavigationCacheMode.Enabled;
        Instance = this;
        FolderPath = Path.Combine(System.Environment.GetFolderPath(System.Environment.SpecialFolder.LocalApplicationData));
        notesPage = new Notes.UWP.Views.NotesPage();
        this.Content = notesPage.CreateFrameworkElement();
        // ...        
    } 
    // ...
}
```

O `MainPage` construtor executa as seguintes tarefas:

- O cache é habilitado para a página, para que um novo `MainPage` não é construída quando um usuário navega para a página.
- Uma referência para o `MainPage` classe é armazenada na `static` `Instance` campo. Isso é fornecer um mecanismo para chamar métodos definidos em outras classes de `MainPage` classe.
- A propriedade `FolderPath` é inicializada para um caminho no dispositivo em que os dados de observação serão armazenados.
- O `NotesPage` classe, que é um xamarin. Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-derivado da página definida em XAML, que é construído e convertido em um `FrameworkElement` usando o `CreateFrameworkElement` método de extensão e, em seguida, defina como o conteúdo da `MainPage` classe.

Uma vez a `MainPage` construtor foi executado, a interface do usuário definido no xamarin. Forms `NotesPage` classe será exibida, conforme mostrado na seguinte captura de tela:

[![Captura de tela de um aplicativo UWP que usa uma interface do usuário definida com o XAML do Xamarin. Forms](native-forms-images/uwp-notespage.png "Aplicativo UWP com uma IU do XAML do Xamarin. Forms")](native-forms-images/uwp-notespage-large.png#lightbox "Aplicativo UWP com uma IU do XAML do Xamarin. Forms")

Interagir com a interface do usuário, por exemplo, tocando no [`Button`](xref:Xamarin.Forms.Button)de+, resultará no seguinte manipulador de eventos na execução do code-behind `NotesPage`:

```csharp
void OnNoteAddedClicked(object sender, EventArgs e)
{
    MainPage.Instance.NavigateToNoteEntryPage(new Note());
}
```

O `static` `MainPage.Instance` campo permite que o `MainPage.NavigateToNoteEntryPage` método a ser invocado, que é mostrado no exemplo de código a seguir:

```csharp
public void NavigateToNoteEntryPage(Note note)
{
    noteEntryPage = new Notes.UWP.Views.NoteEntryPage
    {
        BindingContext = note
    };
    this.Frame.Navigate(noteEntryPage);
}
```

Navegação no UWP normalmente é executada com o `Frame.Navigate` método, que usa um `Page` argumento. Xamarin. Forms define uma `Frame.Navigate` método de extensão que usa um [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-instância de página derivada. Portanto, quando o `NavigateToNoteEntryPage` método é executado, a interface do usuário definido no xamarin. Forms `NoteEntryPage` será exibida, conforme mostrado na seguinte captura de tela:

[![Captura de tela de um aplicativo UWP que usa uma interface do usuário definida com o XAML do Xamarin. Forms](native-forms-images/uwp-noteentrypage.png "Aplicativo UWP com uma IU do XAML do Xamarin. Forms")](native-forms-images/uwp-noteentrypage-large.png#lightbox "Aplicativo UWP com uma IU do XAML do Xamarin. Forms")

Quando o `NoteEntryPage` for exibida, tocando o back seta será exibida a `FrameworkElement` para o `NoteEntryPage` da pilha Voltar no aplicativo, retornando o usuário para o `FrameworkElement` para o `NotesPage` classe.

### <a name="enable-page-resizing-support"></a>Habilitar o suporte ao redimensionamento de página

Quando a janela do aplicativo UWP é redimensionada, o conteúdo do Xamarin. Forms também deve ser redimensionado. Isso é feito registrando um manipulador de eventos para o evento `Loaded`, no Construtor `MainPage`:

```csharp
public MainPage()
{
    // ...
    this.Loaded += OnMainPageLoaded;
    // ...
}
```

O evento `Loaded` é acionado quando a página é disposta, processada e está pronta para interação e executa o método `OnMainPageLoaded` em resposta:

```csharp
void OnMainPageLoaded(object sender, RoutedEventArgs e)
{
    this.Frame.SizeChanged += (o, args) =>
    {
        if (noteEntryPage != null)
            noteEntryPage.Layout(new Xamarin.Forms.Rectangle(0, 0, args.NewSize.Width, args.NewSize.Height));
        else
            notesPage.Layout(new Xamarin.Forms.Rectangle(0, 0, args.NewSize.Width, args.NewSize.Height));
    };
}
```

O método `OnMainPageLoaded` registra um manipulador de eventos anônimo para o evento `Frame.SizeChanged`, que é gerado quando as propriedades `ActualHeight` ou `ActualWidth` são alteradas na `Frame`. Em resposta, o conteúdo do Xamarin. Forms para a página ativa é redimensionado chamando o método `Layout`.

### <a name="enable-back-navigation-support"></a>Habilitar suporte à navegação de volta

Na UWP, aplicativos devem habilitar a navegação para todos os software e hardware back botões, em fatores forma de dispositivo diferentes. Isso pode ser feito registrando um manipulador de eventos para o evento `BackRequested`, que pode ser executado no Construtor `MainPage`:

```csharp
public MainPage()
{
    // ...
    SystemNavigationManager.GetForCurrentView().BackRequested += OnBackRequested;
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
        noteEntryPage = null;
    }
}
```

O `OnBackRequested` chamadas do manipulador de eventos a `GoBack` método no quadro da raiz do aplicativo e conjuntos da `BackRequestedEventArgs.Handled` propriedade `true` para marcar o evento como manipulado. Falha ao marcar o evento como manipulado pode fazer com que o evento seja ignorado.

O aplicativo escolhe se um botão voltar deve ser exibido na barra de título. Isso é feito definindo a propriedade `AppViewBackButtonVisibility` como um dos valores de enumeração `AppViewBackButtonVisibility`, na classe `App`:

```csharp
void OnNavigated(object sender, NavigationEventArgs e)
{
    SystemNavigationManager.GetForCurrentView().AppViewBackButtonVisibility =
        ((Frame)sender).CanGoBack ? AppViewBackButtonVisibility.Visible : AppViewBackButtonVisibility.Collapsed;
}
```

O `OnNavigated` manipulador de eventos, que é executado em resposta ao `Navigated` evento disparando, atualiza a visibilidade do botão Voltar da barra de título quando ocorrer a navegação de página. Isso garante que o botão Voltar de barra de título é visível se a pilha Voltar no aplicativo não está vazia ou removido da barra de título se a pilha Voltar no aplicativo está vazia.

Para obter mais informações sobre o suporte de navegação regressiva na UWP, consulte [histórico de navegação e para trás a navegação para aplicativos UWP](/windows/uwp/design/basics/navigation-history-and-backwards-navigation/).

## <a name="related-links"></a>Links relacionados

- [NativeForms (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/native2forms)
- [Exibições nativas](~/xamarin-forms/platform/native-views/index.md)
