---
title: Classe de aplicativo do xamarin. Forms
description: Este artigo explica os recursos da classe de aplicativo padrão, que inclui uma propriedade para definir a página inicial para o aplicativo, e um dicionário persistente para armazenar valores simples entre as alterações de estado do ciclo de vida.
ms.prod: xamarin
ms.assetid: 421F8294-1944-46A4-8459-D2BD5AAABC9D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/19/2016
ms.openlocfilehash: cebdd6fafafae7d1cd6258e6200808731e3c4f29
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50118806"
---
# <a name="xamarinforms-app-class"></a>Classe de aplicativo do xamarin. Forms

O `Application` classe base oferece os seguintes recursos, que são expostos no seu padrão de projetos `App` subclasse:

* Um `MainPage` propriedade, que é onde definir a página inicial do aplicativo.
* Um persistente [ `Properties` dicionário](#Properties_Dictionary) para armazenar valores simples entre as alterações de estado do ciclo de vida.
* Um estático `Current` propriedade que contém uma referência ao objeto de aplicativo atual.

Ele também expõe [métodos de ciclo de vida](~/xamarin-forms/app-fundamentals/app-lifecycle.md) tais como `OnStart`, `OnSleep`, e `OnResume` , bem como eventos de navegação modal.

Dependendo do modelo escolhido, o `App` classe pode ser definido em uma das duas maneiras:

* **C#**, ou
* **XAML E C#**

Para criar uma **App** classe usando XAML, o padrão **App** classe deve ser substituído por um XAML **aplicativo** classe e code-behind associado, conforme mostrado no exemplo de código a seguir:

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Photos.App">

</Application>
```

O exemplo de código a seguir mostra o code-behind associado:

```csharp
public partial class App : Application
{
    public App ()
    {
        InitializeComponent ();
        MainPage = new HomePage ();
    }
    ...
}
```

Bem como a configuração de [ `MainPage` ](xref:Xamarin.Forms.Application.MainPage) propriedade, o code-behind também deve chamar o `InitializeComponent` método para carregar e analisar o XAML associado.

## <a name="mainpage-property"></a>Propriedade MainPage

O `MainPage` propriedade no `Application` classe define a página de raiz do aplicativo.

Por exemplo, você pode criar lógica em seu `App` classe para exibir uma página diferente, dependendo se o usuário estiver conectado ou não.

O `MainPage` propriedade deve ser definida no `App` construtor,

```csharp
public class App : Xamarin.Forms.Application
{
    public App ()
    {
        MainPage = new ContentPage { Title = "App Lifecycle Sample" }; // your page here
    }
}
```

<a name="Properties_Dictionary" />

## <a name="properties-dictionary"></a>Dicionário de propriedades

O `Application` subclasse tem um estático `Properties` dicionário que pode ser usado para armazenar dados, em particular para uso nas `OnStart`, `OnSleep`, e `OnResume` métodos. Isso pode ser acessado de qualquer lugar no seu código do xamarin. Forms usando `Application.Current.Properties`.

O `Properties` dictionary usa um `string` chave e armazena um `object` valor.

Por exemplo, você pode definir um persistente `"id"` propriedade em qualquer lugar no seu código (quando um item é selecionado em uma página `OnDisappearing` método, ou no `OnSleep` método) semelhante a esta:

```csharp
Application.Current.Properties ["id"] = someClass.ID;
```

No `OnStart` ou `OnResume` métodos, em seguida, você pode usar esse valor para recriar a experiência do usuário de alguma forma. O `Properties` armazenamentos de dicionário `object`s, portanto, você precisa converter seu valor antes de usá-lo.

```csharp
if (Application.Current.Properties.ContainsKey("id"))
{
    var id = Application.Current.Properties ["id"] as int;
    // do something with id
}
```

Sempre verifique a presença da chave para acessá-lo para evitar erros inesperados.

> [!NOTE]
> O `Properties` dicionário pode serializar apenas tipos primitivos para o armazenamento. A tentativa de armazenar outros tipos (como `List<string>`) pode falhar em modo silencioso.

<!-- bugzilla 28657 -->

### <a name="persistence"></a>Persistência

O `Properties` dicionário é salvo automaticamente no dispositivo.
Adicionada ao dicionário de dados estarão disponíveis quando o aplicativo de segundo plano ou até mesmo depois de ser reiniciado.

Xamarin. Forms 1.4 introduzido um método adicional sobre o `Application` class - `SavePropertiesAsync()` -que pode ser chamado para manter proativamente o `Properties` dicionário. Isso é para que você possa salvar propriedades após as atualizações importantes em vez de risco que eles não obtendo serializados devido a uma falha ou que está sendo interrompido pelo sistema operacional.

Você pode encontrar referências usando o `Properties` dicionário na **criação de aplicativos móveis com xamarin. Forms** capítulos de livro [6](https://developer.xamarin.com/r/xamarin-forms/book/chapter06.pdf), [15](https://developer.xamarin.com/r/xamarin-forms/book/chapter15.pdf), e [20 ](https://developer.xamarin.com/r/xamarin-forms/book/chapter20.pdf)e a ele associada [exemplos](https://github.com/xamarin/xamarin-forms-book-preview-2).



## <a name="the-application-class"></a>A classe do aplicativo

Um completo `Application` implementação da classe é mostrada abaixo para referência:

```csharp
public class App : Xamarin.Forms.Application
{
    public App ()
    {
        MainPage = new ContentPage { Title = "App Lifecycle Sample" }; // your page here
    }

    protected override void OnStart()
    {
        // Handle when your app starts
        Debug.WriteLine ("OnStart");
    }

    protected override void OnSleep()
    {
        // Handle when your app sleeps
        Debug.WriteLine ("OnSleep");
    }

    protected override void OnResume()
    {
        // Handle when your app resumes
        Debug.WriteLine ("OnResume");
    }
}

```

Essa classe é instanciada em cada projeto específico da plataforma e passada para o `LoadApplication` método que é onde o `MainPage` é carregado e exibido ao usuário.
O código para cada plataforma é mostrado nas seções a seguir. Os modelos de solução do xamarin. Forms mais recentes já contenham todo esse código, pré-configurado para seu aplicativo.


### <a name="ios-project"></a>Projeto do iOS

O iOS `AppDelegate` classe herda de `FormsApplicationDelegate`. Ele deve:

* Chame `LoadApplication` com uma instância da `App` classe.

* Sempre retorna `base.FinishedLaunching (app, options);`.

```csharp
[Register ("AppDelegate")]
public partial class AppDelegate :
    global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate // superclass new in 1.3
{
    public override bool FinishedLaunching (UIApplication app, NSDictionary options)
    {
        global::Xamarin.Forms.Forms.Init ();

        LoadApplication (new App ());  // method is new in 1.3

        return base.FinishedLaunching (app, options);
    }
}
```

### <a name="android-project"></a>Projeto do Android

O Android `MainActivity` herda de `FormsAppCompatActivity`. No `OnCreate` substituir a `LoadApplication` método for chamado com uma instância da `App` classe.

```csharp
[Activity (Label = "App Lifecycle Sample", Icon = "@drawable/icon", Theme = "@style/MainTheme", MainLauncher = true,
    ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
public class MainActivity : FormsAppCompatActivity
{
    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        global::Xamarin.Forms.Forms.Init (this, bundle);

        LoadApplication (new App ()); // method is new in 1.3
    }
}
```

### <a name="universal-windows-project-uwp-for-windows-10"></a>Projeto do universal do Windows (UWP) para Windows 10

Ver [projetos de instalação do Windows](~/xamarin-forms/platform/windows/installation/index.md) para obter informações sobre o suporte da UWP no xamarin. Forms.

A página principal no projeto UWP deve herdar de `WindowsPage`:

```xaml
<forms:WindowsPage
   ...
   xmlns:forms="using:Xamarin.Forms.Platform.UWP"
   ...>
</forms:WindowsPage>
```

Construção do codebehind c# deve chamar `LoadApplication` para criar uma instância do seu xamarin. Forms `App`. Observe que é uma boa prática usar explicitamente o namespace do aplicativo para qualificar os `App` como os aplicativos UWP também têm suas próprias `App` classe não relacionado ao xamarin. Forms.

```csharp
public sealed partial class MainPage
{
    public MainPage()
    {
        InitializeComponent();

        LoadApplication(new YOUR_NAMESPACE.App());
    }
 }
```

Observe que `Forms.Init()` deve ser chamado **App.xaml.cs** perto da linha 63.
