---
title: Xamarin.Forms em projetos nativos do Xamarin
description: Este artigo explica como consumir páginas derivadas de ContentPage que são adicionadas diretamente aos projetos nativos do Xamarin e como navegar entre elas.
ms.prod: xamarin
ms.assetid: f343fc21-dfb1-4364-a332-9da6705d36bc
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/19/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 241aa896cb66c4ff594c786ad484781adcddffa1
ms.sourcegitcommit: 63029dd7ea4edb707a53ea936ddbee684a926204
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/20/2021
ms.locfileid: "98609125"
---
# <a name="no-locxamarinforms-in-xamarin-native-projects"></a>Xamarin.Forms em projetos nativos do Xamarin

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/native2forms)

Normalmente, um Xamarin.Forms aplicativo inclui uma ou mais páginas que derivam de [`ContentPage`](xref:Xamarin.Forms.ContentPage) e essas páginas são compartilhadas por todas as plataformas em um projeto de biblioteca .net Standard ou projeto compartilhado. No entanto, formulários nativos permitem que `ContentPage` páginas derivadas sejam adicionadas diretamente a aplicativos xamarin. Ios, xamarin. Android e UWP nativos. Em comparação com o projeto nativo consumir `ContentPage` páginas derivadas de um projeto de biblioteca .net Standard ou de um projeto compartilhado, a vantagem de adicionar páginas diretamente a projetos nativos é que as páginas podem ser estendidas com exibições nativas. Exibições nativas podem então ser nomeadas em XAML com `x:Name` e referenciadas do code-behind. Para obter mais informações sobre exibições nativas, consulte [exibições nativas](~/xamarin-forms/platform/native-views/index.md).

O processo de consumo de uma Xamarin.Forms [`ContentPage`](xref:Xamarin.Forms.ContentPage) página derivada em um projeto nativo é o seguinte:

1. Adicione o Xamarin.Forms pacote NuGet ao projeto nativo.
1. Adicione a [`ContentPage`](xref:Xamarin.Forms.ContentPage) página derivada e quaisquer dependências ao projeto nativo.
1. Chame o método `Forms.Init` .
1. Construa uma instância da [`ContentPage`](xref:Xamarin.Forms.ContentPage) página derivada e converta-a para o tipo nativo apropriado usando um dos seguintes métodos de extensão: `CreateViewController` para IOS, `CreateSupportFragment` para Android ou `CreateFrameworkElement` para UWP.
1. Navegue até a representação do tipo nativo da [`ContentPage`](xref:Xamarin.Forms.ContentPage) página derivada usando a API de navegação nativa.

Xamarin.Forms deve ser inicializado chamando o `Forms.Init` método antes que um projeto nativo possa construir uma [`ContentPage`](xref:Xamarin.Forms.ContentPage) página derivada. Escolher quando fazer isso depende principalmente de quando é mais conveniente em seu fluxo de aplicativo – ele pode ser executado na inicialização do aplicativo ou logo antes da `ContentPage` construção da página derivada. Neste artigo e os aplicativos de exemplo que o acompanham, o `Forms.Init` método é chamado na inicialização do aplicativo.

> [!NOTE]
> A solução de aplicativo de exemplo **NativeForms** não contém nenhum Xamarin.Forms projeto. Em vez disso, ele consiste em um projeto Xamarin. iOS, um projeto Xamarin. Android e um projeto UWP. Cada projeto é um projeto nativo que usa formulários nativos para consumir [`ContentPage`](xref:Xamarin.Forms.ContentPage) páginas derivadas. No entanto, não há motivo para que os projetos nativos não consumam `ContentPage` páginas derivadas de um projeto de biblioteca .net Standard ou de um projeto compartilhado.

Ao usar formulários nativos, Xamarin.Forms recursos como [`DependencyService`](xref:Xamarin.Forms.DependencyService) , [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) e o mecanismo de vinculação de dados, todos ainda funcionam. No entanto, a navegação de página deve ser executada usando a API de navegação nativa.

## <a name="ios"></a>iOS

No iOS, a `FinishedLaunching` substituição na `AppDelegate` classe normalmente é o local para executar tarefas relacionadas à inicialização do aplicativo. Ele é chamado depois que o aplicativo é iniciado e é geralmente substituído para configurar a janela principal e o controlador de exibição. O exemplo de código a seguir mostra a `AppDelegate` classe no aplicativo de exemplo:

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

Esse método`FinishedLaunching`   executa as seguintes tarefas:

- Xamarin.Forms é inicializado chamando o `Forms.Init` método.
- Uma referência à `AppDelegate` classe é armazenada no `static` `Instance` campo. Isso é para fornecer um mecanismo para outras classes chamarem métodos definidos na `AppDelegate` classe.
- O `UIWindow` , que é o contêiner principal para exibições em aplicativos nativos do IOS, é criado.
- A `FolderPath` propriedade é inicializada para um caminho no dispositivo em que os dados de observação serão armazenados.
- A `NotesPage` classe, que é uma Xamarin.Forms [`ContentPage`](xref:Xamarin.Forms.ContentPage) página derivada definida em XAML, é construída e convertida em um `UIViewController` usando o `CreateViewController` método de extensão.
- A `Title` propriedade de `UIViewController` é definida, que será exibida no `UINavigationBar` .
- Um `AppNavigationController` é criado para gerenciar a navegação hierárquica. Essa é uma classe de controlador de navegação personalizada, que deriva de `UINavigationController` . O `AppNavigationController` objeto gerencia uma pilha de controladores de exibição, e o `UIViewController` passado para o Construtor será apresentado inicialmente quando o `AppNavigationController` for carregado.
- O `AppNavigationController` objeto é definido como o nível superior `UIViewController` para o `UIWindow` , e o `UIWindow` é definido como a janela de chave para o aplicativo e torna-se visível.

Depois que o `FinishedLaunching` método for executado, a interface do usuário definida na Xamarin.Forms `NotesPage` classe será exibida, conforme mostrado na seguinte captura de tela:

[![A captura de tela mostra um ecrã de anotações em um dispositivo móvel.](native-forms-images/ios-notespage.png "Aplicativo Xamarin. iOS com uma interface do usuário XAML")](native-forms-images/ios-notespage-large.png#lightbox "Aplicativo Xamarin. iOS com uma interface do usuário XAML")

Interagir com a interface do usuário, por exemplo, tocando em **+** [`Button`](xref:Xamarin.Forms.Button) , resultará no seguinte manipulador de eventos na `NotesPage` execução do code-behind:

```csharp
void OnNoteAddedClicked(object sender, EventArgs e)
{
    AppDelegate.Instance.NavigateToNoteEntryPage(new Note());
}
```

O `static` `AppDelegate.Instance` campo permite que o `AppDelegate.NavigateToNoteEntryPage` método seja invocado, que é mostrado no seguinte exemplo de código:

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

O `NavigateToNoteEntryPage` método converte a Xamarin.Forms [`ContentPage`](xref:Xamarin.Forms.ContentPage) página derivada de-a `UIViewController` com o `CreateViewController` método de extensão e define a `Title` Propriedade do `UIViewController` . `UIViewController`Em seguida, o é enviado `AppNavigationController` por push pelo `PushViewController` método. Portanto, a interface do usuário definida na Xamarin.Forms `NoteEntryPage` classe será exibida, conforme mostrado na seguinte captura de tela:

[![Captura de tela mostra uma entrada de nota em um dispositivo móvel.](native-forms-images/ios-noteentrypage.png "Aplicativo Xamarin. iOS com uma interface do usuário XAML")](native-forms-images/ios-noteentrypage-large.png#lightbox "Aplicativo Xamarin. iOS com uma interface do usuário XAML")

Quando o `NoteEntryPage` for exibido, a navegação de volta exibirá o `UIViewController` para a `NoteEntryPage` classe do `AppNavigationController` , retornando o usuário para o `UIViewController` para a `NotesPage` classe. No entanto, o pop-up `UIViewController` da pilha de navegação nativa do IOS não descarta automaticamente o `UIViewController` e o `Page` objeto anexado. Portanto, a `AppNavigationController` classe substitui o `PopViewController` método para descartar os controladores de exibição na navegação para trás:

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

A `PopViewController` substituição chama o `Dispose` método no `UIViewController` objeto que foi retirado da pilha de navegação nativa do Ios. A falha em fazer isso fará com `UIViewController` `Page` que o objeto anexado seja órfão.

> [!IMPORTANT]
> Os objetos órfãos não podem ser coletados pelo lixo e, portanto, resultar em um vazamento de memória.

## <a name="android"></a>Android

No Android, a `OnCreate` substituição na `MainActivity` classe normalmente é o lugar para executar tarefas relacionadas à inicialização do aplicativo. O exemplo de código a seguir mostra a `MainActivity` classe no aplicativo de exemplo:

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
        AndroidX.Fragment.App.Fragment mainPage = new NotesPage().CreateSupportFragment(this);
        SupportFragmentManager
            .BeginTransaction()
            .Replace(Resource.Id.fragment_frame_layout, mainPage)
            .Commit();
        ...
    }
    ...
}
```

Esse método`OnCreate`   executa as seguintes tarefas:

- Xamarin.Forms é inicializado chamando o `Forms.Init` método.
- Uma referência à `MainActivity` classe é armazenada no `static` `Instance` campo. Isso é para fornecer um mecanismo para outras classes chamarem métodos definidos na `MainActivity` classe.
- O `Activity` conteúdo é definido de um recurso de layout. No aplicativo de exemplo, o layout consiste em um `LinearLayout` que contém um `Toolbar` e um `FrameLayout` para atuar como um contêiner de fragmento.
- O `Toolbar` é recuperado e definido como a barra de ação para o `Activity` , e o título da barra de ação é definido.
- A `FolderPath` propriedade é inicializada para um caminho no dispositivo em que os dados de observação serão armazenados.
- A `NotesPage` classe, que é uma Xamarin.Forms [`ContentPage`](xref:Xamarin.Forms.ContentPage) página derivada definida em XAML, é construída e convertida em um `Fragment` usando o `CreateSupportFragment` método de extensão.
- A `SupportFragmentManager` classe cria e confirma uma transação que substitui a `FrameLayout` instância pelo `Fragment` para a `NotesPage` classe.

Para obter mais informações sobre fragmentos, consulte [fragmentos](~/android/platform/fragments/index.md).

Depois que o `OnCreate` método for executado, a interface do usuário definida na Xamarin.Forms `NotesPage` classe será exibida, conforme mostrado na seguinte captura de tela:

[![Captura de tela mostra um ecrã de anotações em um dispositivo móvel com uma faixa azul e um texto de nota colorido.](native-forms-images/android-notespage.png "Aplicativo Xamarin. Android com uma interface do usuário XAML")](native-forms-images/android-notespage-large.png#lightbox "Aplicativo Xamarin. Android com uma interface do usuário XAML")

Interagir com a interface do usuário, por exemplo, tocando em **+** [`Button`](xref:Xamarin.Forms.Button) , resultará no seguinte manipulador de eventos na `NotesPage` execução do code-behind:

```csharp
void OnNoteAddedClicked(object sender, EventArgs e)
{
    MainActivity.Instance.NavigateToNoteEntryPage(new Note());
}
```

O `static` `MainActivity.Instance` campo permite que o `MainActivity.NavigateToNoteEntryPage` método seja invocado, que é mostrado no seguinte exemplo de código:

```csharp
public void NavigateToNoteEntryPage(Note note)
{
    AndroidX.Fragment.App.Fragment noteEntryPage = new NoteEntryPage
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

O `NavigateToNoteEntryPage` método converte a Xamarin.Forms [`ContentPage`](xref:Xamarin.Forms.ContentPage) página derivada de-derivado em um `Fragment` com o `CreateSupportFragment` método de extensão e adiciona o `Fragment` à pilha de retorno do fragmento. Portanto, a interface do usuário definida no Xamarin.Forms `NoteEntryPage` será exibida, conforme mostrado na seguinte captura de tela:

[![Captura de tela mostra uma entrada de nota em um dispositivo móvel com uma faixa azul.](native-forms-images/android-noteentrypage.png "Aplicativo Xamarin. Android com uma interface do usuário XAML")](native-forms-images/android-noteentrypage-large.png#lightbox "Aplicativo Xamarin. Android com uma interface do usuário XAML")

Quando o `NoteEntryPage` for exibido, tocar na seta voltar exibirá o da `Fragment` `NoteEntryPage` pilha voltar do fragmento, retornando o usuário para o `Fragment` para a `NotesPage` classe.

### <a name="enable-back-navigation-support"></a>Habilitar suporte à navegação de volta

A `SupportFragmentManager` classe tem um `BackStackChanged` evento que é acionado sempre que o conteúdo da pilha de trás do fragmento é alterado. O `OnCreate` método na `MainActivity` classe contém um manipulador de eventos anônimo para este evento:

```csharp
SupportFragmentManager.BackStackChanged += (sender, e) =>
{
    bool hasBack = SupportFragmentManager.BackStackEntryCount > 0;
    SupportActionBar.SetHomeButtonEnabled(hasBack);
    SupportActionBar.SetDisplayHomeAsUpEnabled(hasBack);
    SupportActionBar.Title = hasBack ? "Note Entry" : "Notes";
};
```

Esse manipulador de eventos exibe um botão voltar na barra de ação, desde que haja uma ou mais `Fragment` instâncias na pilha de retorno do fragmento. A resposta para tocar no botão voltar é manipulada pela `OnOptionsItemSelected` substituição:

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

A `OnOptionsItemSelected` substituição é chamada sempre que um item no menu opções é selecionado. Essa implementação exibe o fragmento atual da pilha de fundo do fragmento, desde que o botão voltar tenha sido selecionado e haja uma ou mais `Fragment` instâncias na pilha de retorno do fragmento.

### <a name="multiple-activities"></a>Várias atividades

Quando um aplicativo é composto por várias atividades, as [`ContentPage`](xref:Xamarin.Forms.ContentPage) páginas derivadas podem ser inseridas em cada uma das atividades. Nesse cenário, o `Forms.Init` método precisa ser chamado apenas na `OnCreate` substituição do primeiro `Activity` que incorpora um Xamarin.Forms `ContentPage` . No entanto, isso tem o seguinte impacto:

- O valor de `Xamarin.Forms.Color.Accent` será obtido do `Activity` que chamou o `Forms.Init` método.
- O valor de `Xamarin.Forms.Application.Current` será associado ao `Activity` que chamou o `Forms.Init` método.

### <a name="choose-a-file"></a>Escolher um arquivo

Ao inserir uma [`ContentPage`](xref:Xamarin.Forms.ContentPage) página derivada que usa um [`WebView`](xref:Xamarin.Forms.WebView) que precisa dar suporte a um botão HTML "escolher arquivo", o precisará `Activity` substituir o `OnActivityResult` método:

```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);
    ActivityResultCallbackRegistry.InvokeCallback(requestCode, resultCode, data);
}
```

## <a name="uwp"></a>UWP

No UWP, a `App` classe nativa normalmente é o lugar para executar tarefas relacionadas à inicialização do aplicativo. Xamarin.Forms geralmente é inicializado, em Xamarin.Forms aplicativos UWP, na `OnLaunched` substituição na classe nativa `App` , para passar o `LaunchActivatedEventArgs` argumento para o `Forms.Init` método. Por esse motivo, os aplicativos UWP nativos que consomem uma Xamarin.Forms [`ContentPage`](xref:Xamarin.Forms.ContentPage) página derivada podem chamar com mais facilidade o `Forms.Init` método do `App.OnLaunched` método.

Por padrão, a `App` classe nativa inicia a `MainPage` classe como a primeira página do aplicativo. O exemplo de código a seguir mostra a `MainPage` classe no aplicativo de exemplo:

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

- O Caching está habilitado para a página, de forma que um novo `MainPage` não seja construído quando um usuário navegar de volta para a página.
- Uma referência à `MainPage` classe é armazenada no `static` `Instance` campo. Isso é para fornecer um mecanismo para outras classes chamarem métodos definidos na `MainPage` classe.
- A `FolderPath` propriedade é inicializada para um caminho no dispositivo em que os dados de observação serão armazenados.
- A `NotesPage` classe, que é uma Xamarin.Forms [`ContentPage`](xref:Xamarin.Forms.ContentPage) página derivada definida em XAML, é construída e convertida em um `FrameworkElement` usando o `CreateFrameworkElement` método de extensão e, em seguida, definida como o conteúdo da `MainPage` classe.

Depois que o `MainPage` Construtor for executado, a interface do usuário definida na Xamarin.Forms `NotesPage` classe será exibida, conforme mostrado na seguinte captura de tela:

[![Captura de tela mostra uma página de anotações com anotações e data/hora.](native-forms-images/uwp-notespage.png "Aplicativo UWP com uma interface do usuário::: não-Loc (Xamarin. Forms)::: XAML")](native-forms-images/uwp-notespage-large.png#lightbox "Aplicativo UWP com uma interface do usuário::: não-Loc (Xamarin. Forms)::: XAML")

Interagir com a interface do usuário, por exemplo, tocando em **+** [`Button`](xref:Xamarin.Forms.Button) , resultará no seguinte manipulador de eventos na `NotesPage` execução do code-behind:

```csharp
void OnNoteAddedClicked(object sender, EventArgs e)
{
    MainPage.Instance.NavigateToNoteEntryPage(new Note());
}
```

O `static` `MainPage.Instance` campo permite que o `MainPage.NavigateToNoteEntryPage` método seja invocado, que é mostrado no seguinte exemplo de código:

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

A navegação em UWP normalmente é executada com o `Frame.Navigate` método, que usa um `Page` argumento. Xamarin.Forms define um `Frame.Navigate` método de extensão que usa uma [`ContentPage`](xref:Xamarin.Forms.ContentPage) instância de página derivada. Portanto, quando o `NavigateToNoteEntryPage` método é executado, a interface do usuário definida no Xamarin.Forms `NoteEntryPage` será exibida, conforme mostrado na seguinte captura de tela:

[![Captura de tela mostra uma página de anotações com uma caixa de texto com uma observação inserida.](native-forms-images/uwp-noteentrypage.png "Aplicativo UWP com uma interface do usuário::: não-Loc (Xamarin. Forms)::: XAML")](native-forms-images/uwp-noteentrypage-large.png#lightbox "Aplicativo UWP com uma interface do usuário::: não-Loc (Xamarin. Forms)::: XAML")

Quando o `NoteEntryPage` for exibido, tocar na seta voltar exibirá o da `FrameworkElement` `NoteEntryPage` pilha de retorno no aplicativo, retornando o usuário para o `FrameworkElement` para a `NotesPage` classe.

### <a name="enable-page-resizing-support"></a>Habilitar o suporte ao redimensionamento de página

Quando a janela do aplicativo UWP é redimensionada, o Xamarin.Forms conteúdo também deve ser redimensionado. Isso é feito pelo registro de um manipulador de eventos para o `Loaded` evento, no `MainPage` Construtor:

```csharp
public MainPage()
{
    // ...
    this.Loaded += OnMainPageLoaded;
    // ...
}
```

O `Loaded` evento é acionado quando a página é disposta, processada e está pronta para interação e executa o `OnMainPageLoaded` método em resposta:

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

O `OnMainPageLoaded` método registra um manipulador de eventos anônimo para o `Frame.SizeChanged` evento, que é gerado quando as `ActualHeight` Propriedades ou são `ActualWidth` alteradas no `Frame` . Em resposta, o Xamarin.Forms conteúdo da página ativa é redimensionado chamando o `Layout` método.

### <a name="enable-back-navigation-support"></a>Habilitar suporte à navegação de volta

No UWP, os aplicativos devem habilitar a navegação regressiva para todos os botões de backup de hardware e software, em fatores forma de dispositivo diferentes. Isso pode ser feito registrando um manipulador de eventos para o `BackRequested` evento, que pode ser executado no `MainPage` Construtor:

```csharp
public MainPage()
{
    // ...
    SystemNavigationManager.GetForCurrentView().BackRequested += OnBackRequested;
}
```

Quando o aplicativo é iniciado, o `GetForCurrentView` método recupera o `SystemNavigationManager` objeto associado ao modo de exibição atual e registra um manipulador de eventos para `BackRequested` o evento. O aplicativo só receberá esse evento se for o aplicativo em primeiro plano e, em resposta, chamará o `OnBackRequested` manipulador de eventos:

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

O `OnBackRequested` manipulador de eventos chama o `GoBack` método no quadro raiz do aplicativo e define a `BackRequestedEventArgs.Handled` propriedade como `true` para marcar o evento como manipulado. Falha ao marcar o evento como manipulado pode fazer com que o evento seja ignorado.

O aplicativo escolhe se um botão voltar deve ser exibido na barra de título. Isso é feito definindo a `AppViewBackButtonVisibility` propriedade como um dos valores de `AppViewBackButtonVisibility` enumeração, na `App` classe:

```csharp
void OnNavigated(object sender, NavigationEventArgs e)
{
    SystemNavigationManager.GetForCurrentView().AppViewBackButtonVisibility =
        ((Frame)sender).CanGoBack ? AppViewBackButtonVisibility.Visible : AppViewBackButtonVisibility.Collapsed;
}
```

O `OnNavigated` manipulador de eventos, que é executado em resposta ao `Navigated` acionamento do evento, atualiza a visibilidade do botão voltar da barra de título quando ocorre a navegação da página. Isso garante que o botão voltar da barra de título fique visível se a pilha voltar no aplicativo não estiver vazia ou removida da barra de título se a pilha voltar no aplicativo estiver vazia.

Para obter mais informações sobre o suporte à navegação de volta no UWP, consulte [histórico de navegação e navegação para aplicativos UWP](/windows/uwp/design/basics/navigation-history-and-backwards-navigation/).

## <a name="related-links"></a>Links relacionados

- [NativeForms (exemplo)](/samples/xamarin/xamarin-forms-samples/native2forms)
- [Exibições nativas](~/xamarin-forms/platform/native-views/index.md)