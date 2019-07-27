---
title: RadioButton
ms.prod: xamarin
ms.assetid: 3C32EA3F-D917-C988-72C5-A17354DA791E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 2279282b08c9d97b239de424cf38aa6f1463dc4d
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68510363"
---
# <a name="radiobutton"></a>RadioButton

Nesta seção, você criará dois botões de opção mutuamente exclusivos (a habilitação de um desabilita o outro), usando o[`RadioGroup`](xref:Android.Widget.RadioGroup)
e[`RadioButton`](xref:Android.Widget.RadioButton)
widgets. Quando o botão de opção for pressionado, uma mensagem do sistema será exibida.


Abra o arquivo Resources **/layout/Main. axml** e [`RadioButton`](xref:Android.Widget.RadioButton)adicione dois s, aninhados em um [`LinearLayout`](xref:Android.Widget.LinearLayout) [`RadioGroup`](xref:Android.Widget.RadioGroup) (dentro do):

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

É importante que os [`RadioButton`](xref:Android.Widget.RadioButton)s sejam agrupados [`RadioGroup`](xref:Android.Widget.RadioGroup) pelo elemento para que não haja mais de um pode ser selecionado de cada vez. Essa lógica é manipulada automaticamente pelo sistema Android. Quando um[`RadioButton`](xref:Android.Widget.RadioButton)
dentro de um grupo é selecionado, todos os outros são desmarcados automaticamente.

Para fazer algo quando cada [`RadioButton`](xref:Android.Widget.RadioButton) um é selecionado, precisamos escrever um manipulador de eventos:

```csharp
private void RadioButtonClick (object sender, EventArgs e)
{
    RadioButton rb = (RadioButton)sender;
    Toast.MakeText (this, rb.Text, ToastLength.Short).Show ();
}
```

Primeiro, o remetente passado é convertido em um RadioButton.
Em seguida, um[`Toast`](xref:Android.Widget.Toast)
mensagem exibe o texto do botão de opção selecionado.

Agora, na parte inferior da[`OnCreate()`](xref:Android.App.Activity.OnCreate*)
, adicione o seguinte:

```csharp
RadioButton radio_red = FindViewById<RadioButton>(Resource.Id.radio_red);
RadioButton radio_blue = FindViewById<RadioButton>(Resource.Id.radio_blue);

radio_red.Click += RadioButtonClick;
radio_blue.Click += RadioButtonClick;
```

Isso captura cada um dos [`RadioButton`](xref:Android.Widget.RadioButton)s do layout e adiciona o manipulador de eventos recém-criado a cada um.

Execute o aplicativo.

> [!TIP]
> Se você precisar alterar o estado por conta própria (por exemplo, ao carregar [`CheckBoxPreference`](xref:Android.Preferences.CheckBoxPreference)um salvo), use o[`Checked`](xref:Android.Widget.CompoundButton.Checked)
> setter de propriedade ou[`Toggle()`](xref:Android.Widget.CompoundButton.Toggle)
> método.

*Partes desta página são modificações com base no trabalho criado e compartilhado pelo projeto de software livre do Android e usadas de acordo com os termos descritos na licença de atribuição do*
[*Creative Commons 2,5*](http://creativecommons.org/licenses/by/2.5/). 
