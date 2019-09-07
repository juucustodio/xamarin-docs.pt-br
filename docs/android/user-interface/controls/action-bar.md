---
title: ActionBar para Xamarin. Android
ms.prod: xamarin
ms.assetid: 84A79F1F-9E73-4E3E-80FA-B72E5686900B
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: f472f19429f21c659e28ba1c7a8d2670e22ea6a4
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70758949"
---
# <a name="actionbar-for-xamarinandroid"></a>ActionBar para Xamarin. Android

Ao usar `TabActivity`o, o código para criar os ícones de guia não tem nenhum efeito quando executado na estrutura do Android 4,0. Embora funcione de forma funcional como fazia em versões do Android anteriores a 2,3, a `TabActivity` própria classe foi preterida em 4,0. Foi introduzida uma nova maneira de criar uma interface com guias que usa a barra de ação, que abordaremos a seguir.

## <a name="action-bar-tabs"></a>Guias da barra de ações

A barra de ação inclui suporte para adicionar interfaces com guias no Android 4,0.
A captura de tela a seguir mostra um exemplo dessa interface.

[![Captura de tela do aplicativo em execução em um emulador; são mostradas duas guias](action-bar-images/25-actionbartabs.png)](action-bar-images/25-actionbartabs.png#lightbox)

Para criar guias na barra de ação, primeiro precisamos definir sua `NavigationMode` propriedade para dar suporte a guias. No Android 4, uma `ActionBar` Propriedade está disponível na classe Activity, que podemos usar para definir o `NavigationMode` seguinte:

```csharp
this.ActionBar.NavigationMode = ActionBarNavigationMode.Tabs;
```

Quando isso for feito, podemos criar uma guia chamando o `NewTab` método na barra de ação. Com essa instância de guia, podemos chamar os `SetText` métodos `SetIcon` e para definir o texto do rótulo da guia e o ícone; essas chamadas são feitas em ordem no código mostrado abaixo:

```csharp
var tab = this.ActionBar.NewTab ();
tab.SetText (tabText);
tab.SetIcon (Resource.Drawable.ic_tab_white);
```

Para poder adicionar a guia no entanto, precisamos manipular o `TabSelected` evento. Nesse manipulador, podemos criar o conteúdo para a guia. As guias da barra de ações são projetadas para trabalhar com *fragmentos*, que são classes que representam uma parte da interface do usuário em uma atividade. Para este exemplo, a exibição do fragmento contém um único `TextView`, que vamos inflar em `Fragment` nossa subclasse da seguinte maneira:

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

O argumento de evento passado no `TabSelected` evento é do tipo `TabEventArgs`, que inclui uma `FragmentTransaction` propriedade que podemos usar para adicionar o fragmento, conforme mostrado abaixo:

```csharp
tab.TabSelected += delegate(object sender, ActionBar.TabEventArgs e) {             
    e.FragmentTransaction.Add (Resource.Id.fragmentContainer,
        new SampleTabFragment ());
};
```

Por fim, podemos adicionar a guia à barra de ação chamando o `AddTab` método, conforme mostrado neste código:

```csharp
this.ActionBar.AddTab (tab);
```

Para obter o exemplo completo, consulte o projeto *HelloTabsICS* no código de exemplo deste documento.

## <a name="shareactionprovider"></a>ShareActionProvider

A `ShareActionProvider` classe permite que uma ação de compartilhamento ocorra a partir de uma barra de ação. Ele cuida da criação de uma exibição de ação com uma lista de aplicativos que podem lidar com uma intenção de compartilhamento e mantém um histórico dos aplicativos usados anteriormente para facilitar o acesso a eles posteriormente na barra de ação. Isso permite que os aplicativos compartilhem dados por meio de uma experiência de usuário consistente em todo o Android.

### <a name="image-sharing-example"></a>Exemplo de compartilhamento de imagem

Por exemplo, abaixo está uma captura de tela de uma barra de ação com um item de menu para compartilhar uma imagem (obtida do exemplo [ShareActionProvider](https://docs.microsoft.com/samples/xamarin/monodroid-samples/shareactionproviderdemo) ). Quando o usuário toca no item de menu na barra de ação, o ShareActionProvider carrega o aplicativo para lidar com `ShareActionProvider`uma intenção associada ao. Neste exemplo, o aplicativo de mensagens foi usado anteriormente, portanto, ele é apresentado na barra de ação.

[![Captura de tela do ícone do aplicativo de mensagens na barra de ações](action-bar-images/09-shareactionprovider.png)](action-bar-images/09-shareactionprovider.png#lightbox)

Quando o usuário clica no item na barra de ações, o aplicativo de mensagens que contém a imagem compartilhada é iniciado, conforme mostrado abaixo:

[![Captura de tela do aplicativo de mensagens exibindo imagem de macaco](action-bar-images/10-messagewithimage.png)](action-bar-images/10-messagewithimage.png#lightbox)

### <a name="specifying-the-action-provider-class"></a>Especificando a classe do provedor de ação

Para usar o `ShareActionProvider`, defina o `android:actionProviderClass` atributo em um item de menu no XML para o menu da barra de ação da seguinte maneira:

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
  <item android:id="@+id/shareMenuItem"
      android:showAsAction="always"
      android:title="@string/sharePicture"
      android:actionProviderClass="android.widget.ShareActionProvider" />
</menu>
```

### <a name="inflating-the-menu"></a>Replanando o menu

Para inflar o menu, substituímos `OnCreateOptionsMenu` na subclasse Activity. Assim que tivermos uma referência ao menu, podemos obter o `ShareActionProvider` `ActionProvider` da Propriedade do item de menu e, em seguida, usar o método SetShareIntent para definir `ShareActionProvider`a intenção do, como mostrado abaixo:

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

O `ShareActionProvider` usará a intenção, passada para o `SetShareIntent` método no código acima, para iniciar a atividade apropriada. Nesse caso, criamos uma intenção de enviar uma imagem usando o seguinte código:

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

A imagem no exemplo de código acima é incluída como um ativo com o aplicativo e copiada para um local publicamente acessível quando a atividade é criada, portanto, ela poderá ser acessada por outros aplicativos, como o aplicativo de mensagens. O código de exemplo que acompanha este artigo contém a fonte completa deste exemplo, ilustrando seu uso.

## <a name="related-links"></a>Links relacionados

- [As guias de saudação do ICS (exemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/hellotabsics)
- [Demonstração do ShareActionProvider (exemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/shareactionproviderdemo)
- [Introdução ao sanduíche de sorvete](http://www.android.com/about/ice-cream-sandwich/)
- [Plataforma Android 4,0](https://developer.android.com/sdk/android-4.0.html)
