---
title: 'Xamarin.Essentials: Ações do aplicativo'
description: A classe acelerômetro no Xamarin.Essentials permite que você crie e responda a atalhos do aplicativo no ícone do aplicativo.
ms.assetid: 5edf9bc5-b721-448c-a8a2-0a9d4d0c792c
author: jamesmontemagno
ms.author: jamont
ms.date: 01/04/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8b1da7d07c042ff10b48948c4f411d65c6c05002
ms.sourcegitcommit: d4d293174a8324ce82b8f961ae6eadce294cafd7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98194881"
---
# <a name="no-locxamarinessentials-app-actions"></a>Xamarin.Essentials: Ações do aplicativo

A classe **AppActions** permite que você crie e responda a atalhos de aplicativo no ícone do aplicativo.

## <a name="get-started"></a>Introdução

[!include[](~/essentials/includes/get-started.md)]

Para acessar a funcionalidade **AppActions** , a configuração específica de plataforma a seguir é necessária.

# <a name="android"></a>[Android](#tab/android)

Adicione o filtro de intenção à sua `MainActivity` classe:

```csharp
[IntentFilter(
        new[] { Xamarin.Essentials.Platform.Intent.ActionAppAction },
        Categories = new[] { Android.Content.Intent.CategoryDefault })]
public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity
{
    ...
```

Em seguida, adicione a seguinte lógica para manipular ações:

```csharp
protected override void OnResume()
{
    base.OnResume();

    Xamarin.Essentials.Platform.OnResume(this);
}

protected override void OnNewIntent(Android.Content.Intent intent)
{
    base.OnNewIntent(intent);

    Xamarin.Essentials.Platform.OnNewIntent(intent);
}
```

# <a name="ios"></a>[iOS](#tab/ios)

Na, `AppDelegate.cs` adicione a seguinte lógica para tratar as ações:

```csharp
public override void PerformActionForShortcutItem(UIApplication application, UIApplicationShortcutItem shortcutItem, UIOperationHandler completionHandler)
    => Xamarin.Essentials.Platform.PerformActionForShortcutItem(application, shortcutItem, completionHandler);
```

# <a name="uwp"></a>[UWP](#tab/uwp)

No `App.xaml.cs` arquivo no `OnLaunched` método, adicione a seguinte lógica na parte inferior do método:

```csharp
Xamarin.Essentials.Platform.OnLaunched(e);
```

-----

## <a name="create-actions"></a>Criar ações

Adicione uma referência a Xamarin.Essentials em sua classe:

```csharp
using Xamarin.Essentials;
```
As ações do aplicativo podem ser criadas a qualquer momento, mas geralmente são criadas quando um aplicativo é iniciado. Chame o `SetAsync` método para criar a lista de ações para seu aplicativo.


```csharp
try
{
    await AppActions.SetAsync(
        new AppAction("app_info", "App Info", icon: "app_info_action_icon"),
        new AppAction("battery_info", "Battery Info"));
}
catch (FeatureNotSupportedException ex)
{
    Debug.WriteLine("App Actions not supported");
}
```

Se as ações do aplicativo não forem suportadas na versão específica do sistema operacional `FeatureNotSupportedException` , será lançada uma. 

As propriedades a seguir podem ser definidas em um `AppAction` :

* ID: um identificador exclusivo usado para responder ao toque da ação.
* Título: o título visível a ser exibido.
* Subtítulo: se houver suporte para um subtítulo a ser exibido sob o título.
* Ícone: deve corresponder ícones no diretório de recursos correspondente em cada plataforma.

![Ações de aplicativo em homescreen](images/appactions.png)

## <a name="responding-to-actions"></a>Respondendo a ações

Quando seu aplicativo inicia o registro para o `OnAppAction` evento. Quando uma ação do aplicativo for selecionada, o evento será enviado com informações sobre qual ação foi selecionada.

```csharp
public App()
{
    //...
    AppActions.OnAppAction += AppActions_OnAppAction;
}

void AppActions_OnAppAction(object sender, AppActionEventArgs e)
{
    // Don't handle events fired for old application instances
    // and cleanup the old instance's event handler
    if (Application.Current != this && Application.Current is App app)
    {
        AppActions.OnAppAction -= app.AppActions_OnAppAction;
        return;
    }
    MainThread.BeginInvokeOnMainThread(async () =>
    {
        await Shell.Current.GoToAsync($"//{e.AppAction.Id}");
    });
}
```

## <a name="getactions"></a>Getações
Você pode obter a lista atual de ações do aplicativo chamando `AppActions.GetAsync()` .

## <a name="api"></a>API

- [AppActions código-fonte](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/AppActions)
- [Documentação da API do AppActions](xref:Xamarin.Essentials.AppActions)
