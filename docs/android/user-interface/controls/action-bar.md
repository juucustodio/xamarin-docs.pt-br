---
title: ActionBar
ms.prod: xamarin
ms.assetid: 84A79F1F-9E73-4E3E-80FA-B72E5686900B
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 9d9bef6d1a0817abc12b5a9bd266b1e1e7d38348
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2019
ms.locfileid: "57667523"
---
# <a name="actionbar"></a>ActionBar


## <a name="overview"></a>Visão geral

Ao usar `TabActivity`, o código para criar os ícones de guia não tem nenhum efeito quando é executado em relação a estrutura do Android 4.0. Embora funcionalmente ele funciona de forma que nas versões do Android anteriores a 2.3, o `TabActivity` própria classe foi preterida no 4.0. Foi introduzida uma nova maneira de criar uma interface com guias que usa a barra de ação, que será abordado posteriormente.


## <a name="action-bar-tabs"></a>Guias de barra de ação

Barra de ação inclui suporte para adicionar interfaces com guias no Android 4.0.
Captura de tela a seguir mostra um exemplo de uma interface desse tipo.

[![Captura de tela do aplicativo em execução em um emulador. duas guias são mostradas](action-bar-images/25-actionbartabs.png)](action-bar-images/25-actionbartabs.png#lightbox)

Para criar guias na barra de ação, primeiro precisamos definir seu `NavigationMode` propriedade para dar suporte a guias. No Android 4, uma `ActionBar` propriedade está disponível na classe de atividade, que podemos usar para definir o `NavigationMode` como este:

```csharp
this.ActionBar.NavigationMode = ActionBarNavigationMode.Tabs;
```

Depois que isso for feito, podemos criar uma guia chamando o `NewTab` método na barra de ação. Com essa instância de guia, podemos chamar o `SetText` e `SetIcon` métodos para definir o texto do rótulo da guia e o ícone; estas chamadas são feitas na ordem em que o código mostrado abaixo:

```csharp
var tab = this.ActionBar.NewTab ();
tab.SetText (tabText);
tab.SetIcon (Resource.Drawable.ic_tab_white);
```

Antes de podermos Adicionar guia no entanto, precisamos manipular o `TabSelected` eventos. Nesse manipulador, podemos criar o conteúdo da guia. Guias de barra de ação são projetadas para trabalhar com *fragmentos*, que são classes que representam uma parte da interface do usuário em uma atividade. Neste exemplo, o modo de exibição do fragmento contém uma única `TextView`, que podemos inflar em nosso `Fragment` subclasse como este:

```csharp
class SampleTabFragment: Fragment
{           
    public override View OnCreateView (LayoutInflater inflater,
        ViewGroup container, Bundle savedInstanceState)
    {
        base.OnCreateView (inflater, container, savedInstanceState);
       
        var view = inflater.Inflate (
            Resource.Layout.Tab, container, false);

        var sampleTextView =
            view.FindViewById<TextView> (Resource.Id.sampleTextView);            
        sampleTextView.Text = "sample fragment text";


        return view;
    }
}
```

O argumento do evento passado a `TabSelected` evento é do tipo `TabEventArgs`, que inclui um `FragmentTransaction` propriedade que podemos usar para adicionar o fragmento, conforme mostrado abaixo:

```csharp
tab.TabSelected += delegate(object sender, ActionBar.TabEventArgs e) {             
    e.FragmentTransaction.Add (Resource.Id.fragmentContainer,
        new SampleTabFragment ());
};
```

Por fim, podemos adicionar guia à barra de ação chamando o `AddTab` método conforme mostrado neste código:

```csharp
this.ActionBar.AddTab (tab);
```

Para o exemplo completo, consulte o *HelloTabsICS* projeto no código de exemplo para este documento.


## <a name="shareactionprovider"></a>ShareActionProvider

O `ShareActionProvider` classe permite que uma ação de compartilhamento entrar em vigor a partir de uma barra de ação. Ele se encarrega de criar uma exibição de ação com uma lista de aplicativos que podem lidar com uma intenção de compartilhamento e mantém um histórico dos aplicativos usados anteriormente para facilitar o acesso a eles mais tarde na barra de ação. Isso permite que aplicativos compartilhem dados por meio de uma experiência de usuário que seja consistente em Android.


### <a name="image-sharing-example"></a>Exemplo de compartilhamento de imagem

Por exemplo, abaixo está uma captura de tela de uma barra de ação com um item de menu para compartilhar uma imagem (tiradas de [ShareActionProvider](https://developer.xamarin.com/samples/monodroid/ShareActionProviderDemo/) exemplo). Quando o usuário toca o item de menu na barra de ação, o ShareActionProvider carrega o aplicativo para lidar com uma intenção que está associada com o `ShareActionProvider`. Neste exemplo, o aplicativo de mensagens foi usado anteriormente, portanto, ela é apresentada na barra de ação.

[![Captura de tela do ícone do aplicativo na barra de ação de sistema de mensagens](action-bar-images/09-shareactionprovider.png)](action-bar-images/09-shareactionprovider.png#lightbox)


Quando o usuário clica no item na barra de ação, o aplicativo de mensagens que contém a imagem compartilhada é iniciado, conforme mostrado abaixo:

[![Captura de tela do aplicativo de mensagens exibindo monkey imagem](action-bar-images/10-messagewithimage.png)](action-bar-images/10-messagewithimage.png#lightbox)


### <a name="specifying-the-action-provider-class"></a>Especifica a ação a classe de provedor

Para usar o `ShareActionProvider`, defina o `android:actionProviderClass` o atributo em um item de menu no XML para o menu da barra de ação da seguinte maneira:

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
  <item android:id="@+id/shareMenuItem"
      android:showAsAction="always"
      android:title="@string/sharePicture"
      android:actionProviderClass="android.widget.ShareActionProvider" />
</menu>
```


### <a name="inflating-the-menu"></a>Aumentando o Menu

Para aumentar o menu, podemos substituir `OnCreateOptionsMenu` na subclasse de atividade. Assim que tivermos uma referência para o menu, podemos obter os `ShareActionProvider` do `ActionProvider` propriedade do item de menu e, em seguida, use o método SetShareIntent para definir o `ShareActionProvider`da intenção, conforme mostrado abaixo:

```csharp
public override bool OnCreateOptionsMenu (IMenu menu)
{
    MenuInflater.Inflate (Resource.Menu.ActionBarMenu, menu);       
           
    var shareMenuItem = menu.FindItem (Resource.Id.shareMenuItem);           
    var shareActionProvider =
       (ShareActionProvider)shareMenuItem.ActionProvider;
    shareActionProvider.SetShareIntent (CreateIntent ());
}
```


### <a name="creating-the-intent"></a>Criando a intenção

O `ShareActionProvider` usará a intenção, passada para o `SetShareIntent` método no código acima, para iniciar a atividade adequada. Nesse caso, podemos criar uma intenção para enviar uma imagem usando o código a seguir:

```csharp
Intent CreateIntent ()
{  
    var sendPictureIntent = new Intent (Intent.ActionSend);
    sendPictureIntent.SetType ("image/*");
    var uri = Android.Net.Uri.FromFile (GetFileStreamPath ("monkey.png"));          
    sendPictureIntent.PutExtra (Intent.ExtraStream, uri);
    return sendPictureIntent;
}
```

A imagem no exemplo de código acima é incluída como um ativo com o aplicativo e copiada para um local acessível publicamente quando a atividade é criada, para que seja acessível para outros aplicativos, como o aplicativo de mensagens. O código de exemplo que acompanha este artigo contém a fonte completa deste exemplo que ilustram seu uso.



## <a name="related-links"></a>Links relacionados

- [Olá guias ICS (amostra)](https://developer.xamarin.com/samples/HelloTabsICS/)
- [Demonstração de ShareActionProvider (amostra)](https://developer.xamarin.com/samples/monodroid/ShareActionProviderDemo/)
- [Apresentando o Ice Cream Sandwich](http://www.android.com/about/ice-cream-sandwich/)
- [Plataforma 4.0 Android](https://developer.android.com/sdk/android-4.0.html)
