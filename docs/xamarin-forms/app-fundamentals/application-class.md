---
title: Classe de aplicativo
description: Recursos da classe de aplicativo padrão, que pode ser c# ou XAML
ms.prod: xamarin
ms.assetid: 421F8294-1944-46A4-8459-D2BD5AAABC9D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/19/2016
ms.openlocfilehash: 5c9eed8f48a40bc7feaadd0c644610f691713e9b
ms.sourcegitcommit: 1561c8022c3585655229a869d9ef3510bf83f00a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2018
---
# <a name="app-class"></a>Classe de aplicativo

O `Application` classe base oferece os seguintes recursos, que são expostos em seu padrão de projetos `App` subclasse:

* Um `MainPage` propriedade, que é onde definir a página inicial do aplicativo.
* Um persistente [ `Properties` dicionário](#Properties_Dictionary) para armazenar valores simples entre as alterações de estado do ciclo de vida.
* Static `Current` propriedade que contém uma referência ao objeto de aplicativo atual.

Ele também expõe [métodos de ciclo de vida](~/xamarin-forms/app-fundamentals/app-lifecycle.md) como `OnStart`, `OnSleep`, e `OnResume` , bem como eventos de navegação modal.

Dependendo do modelo escolhido, o `App` classe pode ser definida em uma das duas maneiras:

* **C#**, ou
* **XAML E C#**

Para criar um **aplicativo** usando XAML, o padrão de classe **aplicativo** classe deve ser substituída por uma XAML **aplicativo** classe e o code-behind associado, conforme mostrado no exemplo de código a seguir:

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

Bem como a configuração de [ `MainPage` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.MainPage/) propriedade, o code-behind também deve chamar o `InitializeComponent` método para carregar e analisar o XAML associado.

## <a name="mainpage-property"></a>Propriedade MainPage

O `MainPage` propriedade o `Application` classe define a página de raiz do aplicativo.

Por exemplo, você pode criar lógica em seu `App` classe para exibir uma página diferente dependendo se o usuário estiver conectado ou não.

O `MainPage` propriedade deve ser definida `App` construtor,

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

O `Application` subclasse tem um estático `Properties` dicionário que pode ser usado para armazenar dados, em particular para uso no `OnStart`, `OnSleep`, e `OnResume` métodos. Isso pode ser acessado de qualquer lugar no seu código, xamarin. Forms usando `Application.Current.Properties`.

O `Properties` dicionário usa um `string` chave e armazena uma `object` valor.

Por exemplo, você pode definir um persistente `"id"` propriedade em qualquer lugar no seu código (quando um item é selecionado em uma página `OnDisappearing` método, ou o `OnSleep` método) como este:

```csharp
Application.Current.Properties ["id"] = someClass.ID;
```

No `OnStart` ou `OnResume` métodos que você pode usar esse valor para recriar a experiência do usuário de alguma forma. O `Properties` dicionário repositórios `object`s, você precisa converter o valor antes de usá-lo.

```csharp
if (Application.Current.Properties.ContainsKey("id"))
{
    var id = Application.Current.Properties ["id"] as int;
    // do something with id
}
```

Sempre verifique a presença da chave para acessá-lo para evitar erros inesperados.

> [!NOTE]
> O `Properties` dicionário somente pode serializar tipos primitivos para o armazenamento. Tentativa de outros tipos de armazenamento (como `List<string>`) pode falhar em modo silencioso.

<!-- bugzilla 28657 -->

### <a name="persistence"></a>Persistência

O `Properties` dicionário é salvo automaticamente para o dispositivo.
Adicionada ao dicionário de dados estarão disponíveis quando o aplicativo retorna do plano de fundo ou mesmo depois que ele seja reiniciado.

Xamarin. Forms 1.4 introduziu um método adicional sobre o `Application` classe - `SavePropertiesAsync()` -que pode ser chamado de maneira proativa persistir o `Properties` dicionário. Isso é para que você possa salvar propriedades após as atualizações importantes em vez de risco que eles não obtendo serializados devido a uma falha ou que está sendo interrompida pelo sistema operacional.

Você pode encontrar referências usando o `Properties` dicionário no **criação de aplicativos móveis com o xamarin. Forms** livro capítulos [6](https://developer.xamarin.com/r/xamarin-forms/book/chapter06.pdf), [15](https://developer.xamarin.com/r/xamarin-forms/book/chapter15.pdf), e [20 ](https://developer.xamarin.com/r/xamarin-forms/book/chapter20.pdf)e a ele associada [exemplos](https://github.com/xamarin/xamarin-forms-book-preview-2).



## <a name="the-application-class"></a>A classe do aplicativo

Um conjunto completo `Application` implementação da classe é mostrada abaixo para referência:

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

Essa classe é instanciada em cada projeto específico da plataforma e passada para o `LoadApplication` método que é o local onde o `MainPage` é carregado e exibido ao usuário.
O código para cada plataforma é mostrado nas seções a seguir. Os modelos mais recentes de solução do xamarin. Forms já contenham todo esse código, pré-configurado para seu aplicativo.


### <a name="ios-project"></a>Projeto do iOS

O iOS `AppDelegate` classe agora herda de `FormsApplicationDelegate`. É necessário:

* Chamar `LoadApplication` com uma instância do `App` classe.

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

### <a name="android-project"></a>Projeto Android

O Android `MainActivity` agora herda de `FormsApplicationActivity`. No `OnCreate` substituir o `LoadApplication` método for chamado com uma instância do `App` classe.

```csharp
[Activity (Label = "App Lifecycle Sample", Icon = "@drawable/icon", MainLauncher = true,
    ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
public class MainActivity :
    global::Xamarin.Forms.Platform.Android.FormsApplicationActivity // superclass new in 1.3
{
    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        global::Xamarin.Forms.Forms.Init (this, bundle);

        LoadApplication (new App ()); // method is new in 1.3
    }
}
```

> [!NOTE]
> Há uma versão mais recente [ `FormsAppCompatActivity` ](~/xamarin-forms/platform/android/appcompat.md) classe base que pode ser usado para oferecer melhor suporte a estrutura de Material Android.
> Isso se tornará o modelo Android padrão no futuro, mas você pode seguir [estas instruções](~/xamarin-forms/platform/android/appcompat.md) para atualizar seus aplicativos Android existentes.

### <a name="universal-windows-project-uwp-for-windows-10"></a>Projeto universal do Windows (UWP) para Windows 10

Consulte [projetos de instalação do Windows](~/xamarin-forms/platform/windows/installation/index.md) para obter informações sobre o suporte UWP no xamarin. Forms.

A página principal do projeto UWP deve herdar de `WindowsPage`. Isso significa que o XAML e c# para `MainPage` referência a `FormsApplicationPage` classe como mostrado.

O XAML usa um espaço para nome personalizado para que reflita o elemento raiz de `FormsApplicationPage` classe:

```xaml
<forms:WindowsPage
   ...
   xmlns:forms="using:Xamarin.Forms.Platform.UWP"
   ...>
</forms:WindowsPage>
```

Construção do c# code-behind deve chamar `LoadApplication` para criar uma instância do seu xamarin. Forms `App`. Observe que é uma boa prática usar explicitamente o namespace do aplicativo para qualificar o `App` como aplicativos UWP também têm seus próprios `App` classe não relacionado com o xamarin. Forms.

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

Observe que `Forms.Init()` deve ser chamado **App.xaml.cs** em torno da linha 63.
