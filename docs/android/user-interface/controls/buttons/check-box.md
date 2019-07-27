---
title: CheckBox
ms.prod: xamarin
ms.assetid: A884AF10-D5EA-72CA-2301-B80CEC7FFBE7
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: f6f594d86cab8b1173ee9f67402862e1ec2890b2
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68510368"
---
# <a name="checkbox"></a>CheckBox

Nesta seção, você criará uma caixa de seleção para selecionar itens, usando o[`CheckBox`](xref:Android.Widget.CheckBox)
Widget. Quando a caixa de seleção é pressionada, uma mensagem do sistema indica o estado atual da caixa de seleção.

Abra o arquivo Resources **/layout/Main. axml** e [`CheckBox`](xref:Android.Widget.CheckBox) adicione o elemento ( [`LinearLayout`](xref:Android.Widget.LinearLayout)dentro do):

```xml
<CheckBox android:id="@+id/checkbox"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="check it out" />
```

Para fazer algo quando o estado for alterado, adicione o seguinte código ao final do[`OnCreate()`](xref:Android.App.Activity.OnCreate*)
forma

```csharp
CheckBox checkbox = FindViewById<CheckBox>(Resource.Id.checkbox);

checkbox.Click += (o, e) => {
    if (checkbox.Checked)
        Toast.MakeText (this, "Selected", ToastLength.Short).Show ();
    else
        Toast.MakeText (this, "Not selected", ToastLength.Short).Show ();
};
```

Isso captura a[`CheckBox`](xref:Android.Widget.CheckBox)
o elemento do layout, em seguida, manipula o evento Click, que define a ação a ser feita quando a caixa de seleção é clicada. Quando clicado, o[`Checked`](xref:Android.Widget.CompoundButton.Checked)
a propriedade é chamada para verificar o novo estado da caixa de seleção. Se tiver sido marcada, um[`Toast`](xref:Android.Widget.Toast)
exibe a mensagem "Selected", caso contrário, exibe "não selecionado". Dos[`CheckBox`](xref:Android.Widget.CheckBox)
manipula suas próprias alterações de estado, portanto, você só precisa consultar o estado atual.

Execute-o.

> [!TIP]
> Se você precisar alterar o estado por conta própria (por exemplo, ao carregar [`CheckBoxPreference`](xref:Android.Preferences.CheckBoxPreference)um salvo, use o[`Checked`](xref:Android.Widget.CompoundButton.Checked)
> setter de propriedade ou[`Toggle()`](xref:Android.Widget.CompoundButton.Toggle)
> método.

*Partes desta página são modificações com base no trabalho criado e compartilhado pelo projeto de software livre do Android e usadas de acordo com os termos descritos na licença de atribuição do*
[*Creative Commons 2,5*](http://creativecommons.org/licenses/by/2.5/).
