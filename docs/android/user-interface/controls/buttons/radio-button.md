---
title: RadioButton
ms.topic: article
ms.prod: xamarin
ms.assetid: 3C32EA3F-D917-C988-72C5-A17354DA791E
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: c4f4909813f5c82a49ec51278b3b50cc36a8e17b
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="radiobutton"></a>RadioButton

Nesta seção, você criará dois botões de opção de mutuamente exclusivas (habilitar um desabilita outros), usando o [ `RadioGroup` ](https://developer.xamarin.com/api/type/Android.Widget.RadioGroup/) e [ `RadioButton` ](https://developer.xamarin.com/api/type/Android.Widget.RadioButton/) widgets. Quando o botão é pressionado, uma mensagem de notificação do sistema será exibida.


Abra o **Resources/layout/Main.axml** de arquivos e adicionar dois [ `RadioButton` ](https://developer.xamarin.com/api/type/Android.Widget.RadioButton/)s, aninhado em uma [ `RadioGroup` ](https://developer.xamarin.com/api/type/Android.Widget.RadioGroup/) (dentro a [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)):

```xml
<RadioGroup
  android:layout_width="fill_parent"
  android:layout_height="wrap_content"
  android:orientation="vertical">
  <RadioButton android:id="@+id/radio_red"
      android:layout_width="wrap_content"
      android:layout_height="wrap_content"
      android:text="Red" />
  <RadioButton android:id="@+id/radio_blue"
      android:layout_width="wrap_content"
      android:layout_height="wrap_content"
      android:text="Blue" />
</RadioGroup>
```

É importante que o [ `RadioButton` ](https://developer.xamarin.com/api/type/Android.Widget.RadioButton/)s são agrupados pelo [ `RadioGroup` ](https://developer.xamarin.com/api/type/Android.Widget.RadioGroup/) elemento para que não mais de um pode ser selecionado por vez. Essa lógica é tratada automaticamente pelo sistema Android. Quando um [ `RadioButton` ](https://developer.xamarin.com/api/type/Android.Widget.RadioButton/) dentro de um grupo é selecionado, todas as outras são desmarcadas automaticamente.

Para fazer algo quando cada [ `RadioButton` ](https://developer.xamarin.com/api/type/Android.Widget.RadioButton/) é selecionado, é necessário gravar um manipulador de eventos:

```csharp
private void RadioButtonClick (object sender, EventArgs e)
{
    RadioButton rb = (RadioButton)sender;
    Toast.MakeText (this, rb.Text, ToastLength.Short).Show ();
}
```

Primeiro, o remetente passado é convertido em um botão de opção.
Em seguida, um [ `Toast` ](https://developer.xamarin.com/api/type/Android.Widget.Toast/) mensagem exibe o texto do botão de opção selecionado.

Agora, na parte inferior do [ `OnCreate()` ](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/Android.OS.PersistableBundle) método, adicione o seguinte:

```csharp
RadioButton radio_red = FindViewById<RadioButton>(Resource.Id.radio_red);
RadioButton radio_blue = FindViewById<RadioButton>(Resource.Id.radio_blue);

radio_red.Click += RadioButtonClick;
radio_blue.Click += RadioButtonClick;
```

Isso captura cada o [ `RadioButton` ](https://developer.xamarin.com/api/type/Android.Widget.RadioButton/)s do layout e adiciona o handlerto evento recém-criado.

Execute o aplicativo.

**Dica:** se você precisar alterar o estado por conta própria (como quando carregar um salvo [ `CheckBoxPreference` ](https://developer.xamarin.com/api/type/Android.Preferences.CheckBoxPreference/)), use o [ `Checked` ](https://developer.xamarin.com/api/property/Android.Widget.CompoundButton.Checked/) setter de propriedade ou [ `Toggle()` ](https://developer.xamarin.com/api/member/Android.Widget.CompoundButton.Toggle/) método.

*Partes desta página são modificações com base no trabalho criado e compartilhado pelo Android Abrir projeto de origem e usada de acordo com condições descritas no*
[*Creative Commons 2.5 atribuição de licença* ](http://creativecommons.org/licenses/by/2.5/). 
