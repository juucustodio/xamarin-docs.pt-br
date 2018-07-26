---
title: Exibição da Web
ms.prod: xamarin
ms.assetid: 807F214A-166D-B342-0BBA-525517577F6B
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 8d7b0e1abc8eb11bf812a111764b9cccfb41e041
ms.sourcegitcommit: b56b3f906d2c05a3f1be219ef41be8b79e519b8e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2018
ms.locfileid: "39241169"
---
# <a name="web-view"></a>Exibição da Web

[`WebView`](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) permite que você crie sua própria janela para exibir páginas da web (ou até mesmo desenvolver um navegador concluído). Neste tutorial, você criará um simples [ `Activity` ](https://developer.xamarin.com/api/type/Android.App.Activity/) que pode exibir e navegar em páginas da web.

Criar um novo projeto chamado **HelloWebView**.

Abra **Resources/Layout/Main.axml** e insira o seguinte:

```xml
<?xml version="1.0" encoding="utf-8"?>
<WebView  xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/webview"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent" />
```

Como esse aplicativo for acessar a Internet, você deve adicionar que as permissões apropriadas para o Android o arquivo de manifesto. Abra as propriedades do projeto para especificar quais permissões seu aplicativo requer para operar. Habilitar o `INTERNET` permissão conforme mostrado abaixo:

![Definir a permissão de INTERNET no manifesto do Android](web-view-images/01-set-internet-permissions.png)

Agora, abra **MainActivity.cs** e adicionar uma diretiva para Webkit:

```csharp
using Android.Webkit;
```

Na parte superior do `MainActivity` classe, declare uma [ `WebView` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) objeto:

```csharp
WebView web_view;
```

Quando o **WebView** é solicitado a carregar uma URL, ele por padrão delegará a solicitação para o navegador padrão. Para ter o **WebView** carregar a URL (em vez do navegador padrão), você deve ter como subclasse `Android.Webkit.WebViewClient` e substitua o `ShouldOverriderUrlLoading` método. Uma instância dessa prática `WebViewClient` é fornecido para o `WebView`. Para fazer isso, adicione o seguinte aninhados `HelloWebViewClient` dentro de classe `MainActivity`:

```csharp
public class HelloWebViewClient : WebViewClient
{
    public override bool ShouldOverrideUrlLoading (WebView view, string url)
    {
        view.LoadUrl(url);
        return false;
    }
}
```

Quando `ShouldOverrideUrlLoading` retorna `false`, ele sinaliza para o Android que atual `WebView` instância tratou a solicitação e nenhuma ação adicional é necessária. 

Se você estiver direcionando o nível de API 24 ou posterior, use a sobrecarga de `ShouldOverrideUrlLoading` que usa um `IWebResourceRequest` para o segundo argumento, em vez de um `string`:

```csharp
public class HelloWebViewClient : WebViewClient
{
    // For API level 24 and later
    public override bool ShouldOverrideUrlLoading (WebView view, IWebResourceRequest request)
    {
        view.LoadUrl(request.Url.ToString());
        return false;
    }
}
```

Em seguida, use o seguinte código para o [ `OnCreate()` ](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/(Android.OS.Bundle)) método:

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    // Set our view from the "main" layout resource
    SetContentView (Resource.Layout.Main);

    web_view = FindViewById<WebView> (Resource.Id.webview);
    web_view.Settings.JavaScriptEnabled = true;
    web_view.SetWebViewClient(new HelloWebViewClient());
    web_view.LoadUrl ("https://www.xamarin.com/university");
}
```

Isso inicializa o membro [ `WebView` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) com do [ `Activity` ](https://developer.xamarin.com/api/type/Android.App.Activity/) layout e permite que o JavaScript para o [ `WebView` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) com [ `JavaScriptEnabled` ](https://developer.xamarin.com/api/property/Android.Webkit.WebSettings.JavaScriptEnabled/) 
 `= true` (consulte a [C chamar\# do JavaScript](https://github.com/xamarin/recipes/tree/master/Recipes/android/controls/webview/call_csharp_from_javascript) receita para obter informações sobre como chamar o C\# funções do JavaScript). Por fim, uma página da web inicial é carregado com [ `LoadUrl(String)` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/%2fM%2fLoadUrl).

Compile e execute o aplicativo. Você deve ver um aplicativo de Visualizador de página da web simples como aquele mostrado na seguinte captura de tela:

[![Exemplo de aplicativo, exibindo um WebView](web-view-images/02-simple-webview-app-sml.png)](web-view-images/02-simple-webview-app.png#lightbox)

Para lidar com o **volta** botão pressionamento de tecla, adicione a seguinte instrução using:

```csharp
using Android.Views;
```

Em seguida, adicione o seguinte método dentro de `HelloWebView` atividade:

```csharp
public override bool OnKeyDown (Android.Views.Keycode keyCode, Android.Views.KeyEvent e)
{
    if (keyCode == Keycode.Back && web_view.CanGoBack ())
    {
        web_view.GoBack ();
        return true;
    }
    return base.OnKeyDown (keyCode, e);
}
```

Isso [ `OnKeyDown(int, KeyEvent)` ](https://developer.xamarin.com/api/member/Android.App.Activity.OnKeyDown/(Android.Views.Keycode%2cAndroid.Views.KeyEvent)) o método de retorno de chamada será chamado sempre que um botão é pressionado enquanto a atividade está em execução. A condição de usa a [ `KeyEvent` ](https://developer.xamarin.com/api/type/Android.Views.KeyEvent/) verificar se a tecla pressionada é o **volta** botão e se a [ `WebView` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) é realmente capaz de navegar de volta (se ele tem um histórico). Se ambos forem verdadeiros, em seguida, a [ `GoBack()` ](https://developer.xamarin.com/api/member/Android.Webkit.WebView.GoBack/) método é chamado, o que navegará de volta uma etapa a [ `WebView` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) histórico. Retornando `true` indica que o evento foi tratado. Se essa condição não for atendida, o evento é enviado para o sistema.

Execute o aplicativo novamente. Agora você deve ser capaz de seguir os links e navegar para trás no histórico de página:

[![Capturas de tela de exemplo do botão Voltar em ação](web-view-images/03-back-button-sml.png)](web-view-images/03-back-button.png#lightbox)


*Partes desta página são modificações com base no trabalho criado e compartilhado por Android Open Source Project e usadas de acordo com os termos descritos na*
[*2.5 atribuição de licença da Creative Commons* ](http://creativecommons.org/licenses/by/2.5/).


## <a name="related-links"></a>Links relacionados

- [Chamada de c# do JavaScript](https://github.com/xamarin/recipes/tree/master/Recipes/android/controls/webview/call_csharp_from_javascript)
- [Android.Webkit.WebView](https://developer.xamarin.com/api/type/Android.Webkit.WebView)
- [KeyEvent](https://developer.xamarin.com/api/type/Android.Webkit.WebView/Client)
