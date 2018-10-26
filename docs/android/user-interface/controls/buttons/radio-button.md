---
title: RadioButton
ms.prod: xamarin
ms.assetid: 3C32EA3F-D917-C988-72C5-A17354DA791E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: be473580b24dba6b4f08384771e2097d368f8dc8
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50123590"
---
# <a name="radiobutton"></a>RadioButton

Nesta seção, você criará dois botões de opção de mutuamente exclusivas (habilitar um desabilita o outro), usando o [`RadioGroup`](https://developer.xamarin.com/api/type/Android.Widget.RadioGroup/)
e [`RadioButton`](https://developer.xamarin.com/api/type/Android.Widget.RadioButton/)
widgets. Quando qualquer botão de opção é pressionado, uma mensagem de notificação do sistema será exibida.


Abra o **Resources/layout/Main.axml** arquivo e adicione dois [ `RadioButton` ](https://developer.xamarin.com/api/type/Android.Widget.RadioButton/)s, aninhada em uma [ `RadioGroup` ](https://developer.xamarin.com/api/type/Android.Widget.RadioGroup/) (dentro a [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)):

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

É importante que o [ `RadioButton` ](https://developer.xamarin.com/api/type/Android.Widget.RadioButton/)s são agrupados pela [ `RadioGroup` ](https://developer.xamarin.com/api/type/Android.Widget.RadioGroup/) elemento para que não mais de um pode ser selecionado por vez. Essa lógica é tratada automaticamente pelo sistema do Android. Quando um [`RadioButton`](https://developer.xamarin.com/api/type/Android.Widget.RadioButton/)
dentro de um grupo for selecionado, todos os outros são desmarcados automaticamente.

Para fazer algo quando cada [ `RadioButton` ](https://developer.xamarin.com/api/type/Android.Widget.RadioButton/) é selecionada, precisamos escrever um manipulador de eventos:

```csharp
private void RadioButtonClick (object sender, EventArgs e)
{
    RadioButton rb = (RadioButton)sender;
    Toast.MakeText (this, rb.Text, ToastLength.Short).Show ();
}
```

Primeiro, o remetente passado for convertido em um botão de opção.
Em seguida, um [`Toast`](https://developer.xamarin.com/api/type/Android.Widget.Toast/)
mensagem exibe o texto do botão de opção selecionado.

Agora, na parte inferior das [`OnCreate()`](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/Android.OS.PersistableBundle)
método, adicione o seguinte:

```csharp
RadioButton radio_red = FindViewById<RadioButton>(Resource.Id.radio_red);
RadioButton radio_blue = FindViewById<RadioButton>(Resource.Id.radio_blue);

radio_red.Click += RadioButtonClick;
radio_blue.Click += RadioButtonClick;
```

Isso captura cada um dos [ `RadioButton` ](https://developer.xamarin.com/api/type/Android.Widget.RadioButton/)s do layout e adiciona o handlerto evento recém-criado.

Execute o aplicativo.

**Dica:** se você precisar alterar o estado por conta própria (por exemplo, quando carregar um salvo [ `CheckBoxPreference` ](https://developer.xamarin.com/api/type/Android.Preferences.CheckBoxPreference/)), use o [`Checked`](https://developer.xamarin.com/api/property/Android.Widget.CompoundButton.Checked/)
setter de propriedade ou [`Toggle()`](https://developer.xamarin.com/api/member/Android.Widget.CompoundButton.Toggle/)
método.

*Partes desta página são modificações com base no trabalho criado e compartilhado por Android Open Source Project e usadas de acordo com os termos descritos na*
[*2.5 atribuição de licença da Creative Commons* ](http://creativecommons.org/licenses/by/2.5/). 
