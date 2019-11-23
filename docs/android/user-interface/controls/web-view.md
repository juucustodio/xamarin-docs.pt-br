---
title: Exibição da Web
ms.prod: xamarin
ms.assetid: 807F214A-166D-B342-0BBA-525517577F6B
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/06/2018
ms.openlocfilehash: c1fcb16bd40b818b27b57b877534e051a789a6c9
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029013"
---
# <a name="xamarinandroid-web-view"></a>Exibição da Web do Xamarin. Android

[`WebView`](xref:Android.Webkit.WebView) permite que você crie sua própria janela para exibir páginas da Web (ou até mesmo desenvolver um navegador completo). Neste tutorial, você criará um [`Activity`](xref:Android.App.Activity) simples
Isso pode exibir e navegar em páginas da Web.

Crie um novo projeto chamado **HelloWebView**.

Abra **Resources/layout/Main. axml** e insira o seguinte:

```xml
<?xml version="1.0" encoding="utf-8"?>
<WebView  xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/webview"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent" />
```

Como esse aplicativo acessará a Internet, você deve adicionar as permissões apropriadas ao arquivo de manifesto do Android. Abra as propriedades do projeto para especificar quais permissões seu aplicativo precisa para operar. Habilite a permissão `INTERNET`, conforme mostrado abaixo:

![Configurando a permissão de INTERNET no manifesto do Android](web-view-images/01-set-internet-permissions.png)

Agora abra **MainActivity.cs** e adicione uma diretiva using para WebKit:

```csharp
using Android.Webkit;
```

Na parte superior da classe `MainActivity`, declare um objeto [`WebView`](xref:Android.Webkit.WebView) :

```csharp
WebView web_view;
```

Quando a **WebView** é solicitada a carregar uma URL, ela, por padrão, delegará a solicitação ao navegador padrão. Para que o **WebView** carregue a URL (em vez do navegador padrão), você deve subclasse `Android.Webkit.WebViewClient` e substituir o método `ShouldOverriderUrlLoading`. Uma instância desse `WebViewClient` personalizado é fornecida para o `WebView`. Para fazer isso, adicione a seguinte classe de `HelloWebViewClient` aninhada dentro de `MainActivity`:

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

Quando `ShouldOverrideUrlLoading` retorna `false`, ele sinaliza ao Android que a instância de `WebView` atual tratou a solicitação e que nenhuma ação adicional é necessária. 

Se você estiver direcionando o nível de API 24 ou posterior, use a sobrecarga de `ShouldOverrideUrlLoading` que usa um `IWebResourceRequest` para o segundo argumento em vez de um `string`:

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

Em seguida, use o seguinte código para o método [`OnCreate()`](xref:Android.App.Activity.OnCreate*)):

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

Isso inicializa o membro [`WebView`](xref:Android.Webkit.WebView) com o do layout de [`Activity`](xref:Android.App.Activity) e habilita o JavaScript para o [`WebView`](xref:Android.Webkit.WebView) com [`JavaScriptEnabled`](xref:Android.Webkit.WebSettings.JavaScriptEnabled)
`= true` (consulte a [chamada c\# da receita do JavaScript](https://github.com/xamarin/recipes/tree/master/Recipes/android/controls/webview/call_csharp_from_javascript) para obter informações sobre como chamar as funções de\# de dados do JavaScript). Por fim, uma página da Web inicial é carregada com [`LoadUrl(String)`](xref:Android.Webkit.WebView).

Compile e execute o aplicativo. Você deve ver um aplicativo Web Viewer simples como aquele visto na seguinte captura de tela:

[![exemplo de aplicativo exibindo uma WebView](web-view-images/02-simple-webview-app-sml.png)](web-view-images/02-simple-webview-app.png#lightbox)

Para manipular o pressionamento da tecla do botão **voltar** , adicione a seguinte instrução Using:

```csharp
using Android.Views;
```

Em seguida, adicione o seguinte método dentro da atividade de `HelloWebView`:

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

Este [`OnKeyDown(int, KeyEvent)`](xref:Android.App.Activity.OnKeyDown*)
o método de retorno de chamada será chamado sempre que um botão for pressionado enquanto a atividade estiver em execução. A condição dentro usa a [`KeyEvent`](xref:Android.Views.KeyEvent) para verificar se a tecla pressionada é o botão **voltar** e se a [`WebView`](xref:Android.Webkit.WebView) é realmente capaz de navegar de volta (se tiver um histórico). Se ambos forem verdadeiros, o método [`GoBack()`](xref:Android.Webkit.WebView.GoBack) será chamado, que navegará de volta uma etapa no histórico de [`WebView`](xref:Android.Webkit.WebView) . Retornar `true` indica que o evento foi tratado. Se essa condição não for atendida, o evento será enviado de volta para o sistema.

Execute o aplicativo novamente. Agora você deve ser capaz de seguir os links e navegar de volta pelo histórico de página:

[![capturas de tela de exemplo do botão voltar em ação](web-view-images/03-back-button-sml.png)](web-view-images/03-back-button.png#lightbox)

*Partes desta página são modificações com base no trabalho criado e compartilhado pelo projeto de software livre do Android e usadas de acordo com os termos descritos na licença de* [*atribuição
Creative Commons 2,5*](https://creativecommons.org/licenses/by/2.5/).

## <a name="related-links"></a>Links relacionados

- [Chamar C# do JavaScript](https://github.com/xamarin/recipes/tree/master/Recipes/android/controls/webview/call_csharp_from_javascript)
- [Android.Webkit.WebView](xref:Android.Webkit.WebView)
- [KeyEvent](xref:Android.Webkit.WebView)
