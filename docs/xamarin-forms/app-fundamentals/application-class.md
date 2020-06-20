---
title: Xamarin.FormsClasse de aplicativo
description: Este artigo explica os recursos da classe App padrão, que inclui uma propriedade para definir a página inicial do aplicativo e um dicionário persistente para armazenar valores simples durante as alterações de estado do ciclo de vida.
ms.prod: xamarin
ms.assetid: 421F8294-1944-46A4-8459-D2BD5AAABC9D
ms.technology: xamarin-forms
ms.custom: video
author: davidbritch
ms.author: dabritch
ms.date: 02/19/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1e761830fb08662a82a8bf98b4544aafaa6eccf7
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84573346"
---
# <a name="xamarinforms-app-class"></a>Xamarin.FormsClasse de aplicativo

A classe base `Application` oferece os seguintes recursos, que são expostos na subclasse `App` padrão de seus projetos:

* Uma propriedade `MainPage`, que é onde a página inicial do aplicativo deve ser definida.
* Um [ `Properties` dicionário](#properties-dictionary) persistente para armazenar valores simples nas alterações de estado do ciclo de vida.
* Uma propriedade `Current` estática que contém uma referência ao objeto do aplicativo atual.

Ele também expõe [métodos de ciclo de vida](~/xamarin-forms/app-fundamentals/app-lifecycle.md) , como `OnStart` , e, `OnSleep` `OnResume` bem como eventos de navegação modais.

Dependendo do modelo escolhido, a classe `App` poderá ser definida de uma das duas maneiras:

* **C#** ou
* **XAML e C#**

Para criar uma classe **App** usando XAML, a classe **App** padrão deve ser substituída por uma classe **App** XAML e pelo code-behind associado, conforme mostrado no exemplo de código a seguir:

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Photos.App">

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

Além de definir a [`MainPage`](xref:Xamarin.Forms.Application.MainPage) propriedade, o code-behind também deve chamar o `InitializeComponent` método para carregar e analisar o XAML associado.

## <a name="mainpage-property"></a>Propriedade MainPage

A propriedade `MainPage` na classe `Application` define a página raiz do aplicativo.

Por exemplo, você pode criar a lógica em sua classe `App` para exibir uma página diferente se o usuário estiver conectado ou não.

A propriedade `MainPage` deve ser definida no construtor `App`,

```csharp
public class App : Xamarin.Forms.Application
{
    public App ()
    {
        MainPage = new ContentPage { Title = "App Lifecycle Sample" }; // your page here
    }
}
```

## <a name="properties-dictionary"></a>Dicionário de propriedades

A subclasse `Application` tem um dicionário `Properties` estático que pode ser usado para armazenar dados, em particular para uso nos métodos `OnStart`, `OnSleep` e `OnResume`. Isso pode ser acessado de qualquer lugar no seu Xamarin.Forms código usando `Application.Current.Properties` .

O dicionário `Properties` usa uma chave `string` e armazena um valor de `object`.

Por exemplo, você poderia definir uma propriedade `"id"` persistente em qualquer lugar de seu código (quando um item é selecionado, no método `OnDisappearing` de uma página ou no método `OnSleep`), da seguinte forma:

```csharp
Application.Current.Properties ["id"] = someClass.ID;
```

Nos métodos `OnStart` ou `OnResume`, você pode usar esse valor para recriar a experiência do usuário de alguma forma. O dicionário `Properties` armazena `object`s, de modo que você precisa converter seu valor antes de usá-lo.

```csharp
if (Application.Current.Properties.ContainsKey("id"))
{
    var id = Application.Current.Properties ["id"] as int;
    // do something with id
}
```

Sempre verifique a presença da chave antes de acessá-la para evitar erros inesperados.

> [!NOTE]
> O dicionário `Properties` pode serializar apenas tipos primitivos para armazenamento. Tentar armazenar outros tipos (como `List<string>`) pode falhar de modo silencioso.

<!-- bugzilla 28657 -->

### <a name="persistence"></a>Persistência

O dicionário `Properties` é salvo automaticamente no dispositivo.
Dados adicionados ao dicionário estarão disponíveis quando o aplicativo voltar do segundo plano ou até mesmo após ser reiniciado.

Xamarin.Forms1,4 introduziu um método adicional na `Application` classe, `SavePropertiesAsync()` que pode ser chamado para persistir proativamente o `Properties` dicionário. A finalidade disso é permitir que você salve propriedades após atualizações importantes, em vez de correr o risco de que elas não sejam serializadas devido a uma falha ou a uma interrupção do sistema operacional.

Você pode encontrar referências para usar o `Properties` dicionário na **criação de aplicativos móveis com Xamarin.Forms ** os capítulos do livro [6](https://developer.xamarin.com/r/xamarin-forms/book/chapter06.pdf), [15](https://developer.xamarin.com/r/xamarin-forms/book/chapter15.pdf)e [20](https://developer.xamarin.com/r/xamarin-forms/book/chapter20.pdf)e nos [exemplos](https://github.com/xamarin/xamarin-forms-book-preview-2)associados.

## <a name="the-application-class"></a>A classe do aplicativo

Uma implementação completa da classe `Application` é mostrada abaixo para referência:

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

Em seguida, essa classe é instanciada em cada projeto específico da plataforma e passada para o método `LoadApplication`, em que o `MainPage` é carregado e exibido para o usuário.
O código para cada plataforma é mostrado nas seções a seguir. Os modelos de solução mais recentes Xamarin.Forms já contêm todo esse código, pré-configurado para seu aplicativo.

### <a name="ios-project"></a>Projeto do iOS

A classe `AppDelegate` do iOS herda de `FormsApplicationDelegate`. Ele deve:

* Chamar `LoadApplication` com uma instância da classe `App`.

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

O `MainActivity` do Android herda de `FormsAppCompatActivity`. Na substituição `OnCreate`, o método `LoadApplication` é chamado com uma instância da classe `App`.

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

### <a name="universal-windows-project-uwp-for-windows-10"></a>UWP (Plataforma Universal do Windows) para Windows 10

A página principal no projeto da UWP deve herdar de `WindowsPage`:

```xaml
<forms:WindowsPage
   ...
   xmlns:forms="using:Xamarin.Forms.Platform.UWP"
   ...>
</forms:WindowsPage>
```

A construção code-behind do C# deve chamar `LoadApplication` para criar uma instância do seu Xamarin.Forms `App` . Observe que é uma prática recomendada usar explicitamente o namespace do aplicativo para qualificar o `App` porque os aplicativos UWP também têm sua própria `App` classe não relacionada ao Xamarin.Forms .

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

Observe que `Forms.Init()` precisa ser chamado por **App.xaml.cs** no projeto UWP.

Para obter mais informações, consulte [Configurar projetos do Windows](~/xamarin-forms/platform/windows/installation/index.md), que inclui etapas para adicionar um projeto UWP a uma Xamarin.Forms solução existente que não se destina a UWP.

## <a name="related-video"></a>Vídeo relacionado

> [!Video https://channel9.msdn.com/Series/Xamarin-101/Xamarin-Solution-Architecture-4-of-11/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
