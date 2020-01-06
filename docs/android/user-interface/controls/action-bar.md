---
title: ActionBar para Xamarin. Android
ms.prod: xamarin
ms.assetid: 84A79F1F-9E73-4E3E-80FA-B72E5686900B
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/06/2018
ms.openlocfilehash: f64b57e73b69b3111087ca1352f5fb9536f855e5
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75488953"
---
# <a name="actionbar-for-xamarinandroid"></a>ActionBar para Xamarin. Android

Ao usar `TabActivity`, o código para criar os ícones de guia não tem nenhum efeito quando executado na estrutura do Android 4,0. Embora funcione de forma funcional como fazia em versões do Android anteriores a 2,3, a própria classe `TabActivity` foi preterida em 4,0. Foi introduzida uma nova maneira de criar uma interface com guias que usa a barra de ação, que abordaremos a seguir.

## <a name="action-bar-tabs"></a>Guias da barra de ações

A barra de ação inclui suporte para adicionar interfaces com guias no Android 4,0.
A captura de tela a seguir mostra um exemplo dessa interface.

[![captura de tela do aplicativo em execução em um emulador; são mostradas duas guias](action-bar-images/25-actionbartabs.png)](action-bar-images/25-actionbartabs.png#lightbox)

Para criar guias na barra de ação, primeiro precisamos definir sua propriedade `NavigationMode` para dar suporte a guias. No Android 4, uma propriedade `ActionBar` está disponível na classe Activity, que podemos usar para definir o `NavigationMode` assim:

```csharp
this.ActionBar.NavigationMode = ActionBarNavigationMode.Tabs;
```

Quando isso for feito, podemos criar uma guia chamando o método `NewTab` na barra de ação. Com essa instância de guia, podemos chamar os métodos `SetText` e `SetIcon` para definir o texto e o ícone do rótulo da guia; essas chamadas são feitas em ordem no código mostrado abaixo:

```csharp
var tab = this.ActionBar.NewTab ();
tab.SetText (tabText);
tab.SetIcon (Resource.Drawable.ic_tab_white);
```

Para poder adicionar a guia no entanto, precisamos manipular o evento `TabSelected`. Nesse manipulador, podemos criar o conteúdo para a guia. as guias da barra de ações são projetadas para trabalhar com *fragmentos*, que são classes que representam uma parte da interface do usuário em uma atividade. Para este exemplo, a exibição do fragmento contém um único `TextView`, que vamos inflar em nossa subclasse de `Fragment` como esta:

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

O argumento de evento passado no evento `TabSelected` é do tipo `TabEventArgs`, que inclui uma propriedade `FragmentTransaction` que podemos usar para adicionar o fragmento, conforme mostrado abaixo:

```csharp
tab.TabSelected += delegate(object sender, ActionBar.TabEventArgs e) {             
    e.FragmentTransaction.Add (Resource.Id.fragmentContainer,
        new SampleTabFragment ());
};
```

Por fim, podemos adicionar a guia à barra de ação chamando o método `AddTab`, conforme mostrado neste código:

```csharp
this.ActionBar.AddTab (tab);
```

Para obter o exemplo completo, consulte o projeto *HelloTabsICS* no código de exemplo deste documento.

## <a name="shareactionprovider"></a>ShareActionProvider

A classe `ShareActionProvider` permite que uma ação de compartilhamento ocorra a partir de uma barra de ação. Ele cuida da criação de uma exibição de ação com uma lista de aplicativos que podem lidar com uma intenção de compartilhamento e mantém um histórico dos aplicativos usados anteriormente para facilitar o acesso a eles posteriormente na barra de ação. Isso permite que os aplicativos compartilhem dados por meio de uma experiência de usuário consistente em todo o Android.

### <a name="image-sharing-example"></a>Exemplo de compartilhamento de imagem

Por exemplo, abaixo está uma captura de tela de uma barra de ação com um item de menu para compartilhar uma imagem (obtida do exemplo [ShareActionProvider](https://docs.microsoft.com/samples/xamarin/monodroid-samples/shareactionproviderdemo) ). Quando o usuário toca no item de menu na barra de ação, o ShareActionProvider carrega o aplicativo para lidar com uma intenção associada ao `ShareActionProvider`. Neste exemplo, o aplicativo de mensagens foi usado anteriormente, portanto, ele é apresentado na barra de ação.

[![captura de tela do ícone do aplicativo de mensagens na barra de ação](action-bar-images/09-shareactionprovider.png)](action-bar-images/09-shareactionprovider.png#lightbox)

Quando o usuário clica no item na barra de ações, o aplicativo de mensagens que contém a imagem compartilhada é iniciado, conforme mostrado abaixo:

[![captura de tela do aplicativo de mensagens exibindo imagem de macaco](action-bar-images/10-messagewithimage.png)](action-bar-images/10-messagewithimage.png#lightbox)

### <a name="specifying-the-action-provider-class"></a>Especificando a classe do provedor de ação

Para usar o `ShareActionProvider`, defina o atributo `android:actionProviderClass` em um item de menu no XML para o menu da barra de ações da seguinte maneira:

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

Para inflar o menu, substituimos `OnCreateOptionsMenu` na subclasse Activity. Assim que tivermos uma referência ao menu, podemos obter a `ShareActionProvider` da propriedade `ActionProvider` do item de menu e, em seguida, usar o método SetShareIntent para definir a intenção do `ShareActionProvider`, como mostrado abaixo:

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

O `ShareActionProvider` usará a intenção, passada para o método `SetShareIntent` no código acima, para iniciar a atividade apropriada. Nesse caso, criamos uma intenção de enviar uma imagem usando o seguinte código:

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

## <a name="related-links"></a>Links Relacionados

- [As guias de saudação do ICS (exemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/hellotabsics)
- [Demonstração do ShareActionProvider (exemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/shareactionproviderdemo)
