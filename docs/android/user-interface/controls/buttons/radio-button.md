---
title: RadioButton
ms.prod: xamarin
ms.assetid: 3C32EA3F-D917-C988-72C5-A17354DA791E
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/06/2018
ms.openlocfilehash: 9f51adcbd1accb4f780318cc0853e612ed8e5bed
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029308"
---
# <a name="radiobutton"></a>RadioButton

Nesta seção, você criará dois botões de opção mutuamente exclusivos (a habilitação de um desabilita o outro), usando o [`RadioGroup`](xref:Android.Widget.RadioGroup)
e [`RadioButton`](xref:Android.Widget.RadioButton)
widgets. Quando o botão de opção for pressionado, uma mensagem do sistema será exibida.

Abra o arquivo **Resources/layout/Main. axml** e adicione dois [`RadioButton`](xref:Android.Widget.RadioButton)s, aninhados em uma [`RadioGroup`](xref:Android.Widget.RadioGroup) (dentro do [`LinearLayout`](xref:Android.Widget.LinearLayout)):

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

É importante que os [`RadioButton`](xref:Android.Widget.RadioButton)s sejam agrupados pelo elemento [`RadioGroup`](xref:Android.Widget.RadioGroup) para que não haja mais de um pode ser selecionado de cada vez. Essa lógica é manipulada automaticamente pelo sistema Android. Quando um [`RadioButton`](xref:Android.Widget.RadioButton)
dentro de um grupo é selecionado, todos os outros são desmarcados automaticamente.

Para fazer algo quando cada [`RadioButton`](xref:Android.Widget.RadioButton) é selecionado, precisamos escrever um manipulador de eventos:

```csharp
private void RadioButtonClick (object sender, EventArgs e)
{
    RadioButton rb = (RadioButton)sender;
    Toast.MakeText (this, rb.Text, ToastLength.Short).Show ();
}
```

Primeiro, o remetente passado é convertido em um RadioButton.
Em seguida, um [`Toast`](xref:Android.Widget.Toast)
mensagem exibe o texto do botão de opção selecionado.

Agora, na parte inferior da [`OnCreate()`](xref:Android.App.Activity.OnCreate*)
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
> Se você precisar alterar o estado por conta própria (por exemplo, ao carregar um [`CheckBoxPreference`](xref:Android.Preferences.CheckBoxPreference)salvo), use o [`Checked`](xref:Android.Widget.CompoundButton.Checked)
> setter de propriedade ou [`Toggle()`](xref:Android.Widget.CompoundButton.Toggle)
> ProcessOnStatus...

*Partes desta página são modificações com base no trabalho criado e compartilhado pelo projeto de software livre do Android e usadas de acordo com os termos descritos na licença de* [*atribuição
Creative Commons 2,5*](https://creativecommons.org/licenses/by/2.5/). 
