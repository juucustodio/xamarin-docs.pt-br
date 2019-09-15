---
title: CheckBox
ms.prod: xamarin
ms.assetid: A884AF10-D5EA-72CA-2301-B80CEC7FFBE7
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: b947217706fc8ef7ce7945bf4c88349f4367ffcd
ms.sourcegitcommit: cf56d2bae34dc0f8e94c2d3d28d5f460d59807bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/13/2019
ms.locfileid: "70985670"
---
# <a name="checkbox"></a>CheckBox

Nesta seção, você criará uma caixa de seleção para selecionar itens, usando [`CheckBox`](xref:Android.Widget.CheckBox) o widget. Quando a caixa de seleção é pressionada, uma mensagem do sistema indica o estado atual da caixa de seleção.

Abra o arquivo **Resources/layout/Main. axml** e [`CheckBox`](xref:Android.Widget.CheckBox) adicione o elemento ( [`LinearLayout`](xref:Android.Widget.LinearLayout)dentro do):

```xml
<CheckBox android:id="@+id/checkbox"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="check it out" />
```

Para fazer algo quando o estado for alterado, adicione o seguinte código ao final do [`OnCreate()`](xref:Android.App.Activity.OnCreate*) método:

```csharp
CheckBox checkbox = FindViewById<CheckBox>(Resource.Id.checkbox);

checkbox.Click += (o, e) => {
    if (checkbox.Checked)
        Toast.MakeText (this, "Selected", ToastLength.Short).Show ();
    else
        Toast.MakeText (this, "Not selected", ToastLength.Short).Show ();
};
```

Isso captura o [`CheckBox`](xref:Android.Widget.CheckBox) elemento do layout e manipula o evento de clique, que define a ação a ser feita quando a caixa de seleção é clicada. Quando clicado, [`Checked`](xref:Android.Widget.CompoundButton.Checked) a propriedade é chamada para verificar o novo estado da caixa de seleção. Se ele tiver sido marcado, um [`Toast`](xref:Android.Widget.Toast) exibirá a mensagem "selecionado", caso contrário, ele exibirá "não selecionado". O [`CheckBox`](xref:Android.Widget.CheckBox) manipula suas próprias alterações de estado, portanto, você só precisa consultar o estado atual.

Execute-o.

> [!TIP]
> Se você precisar alterar o estado por conta própria (por exemplo, ao carregar [`CheckBoxPreference`](xref:Android.Preferences.CheckBoxPreference)um salvo, [`Checked`](xref:Android.Widget.CompoundButton.Checked) use o método [`Toggle()`](xref:Android.Widget.CompoundButton.Toggle) ou setter de propriedade.

*Partes desta página são modificações com base no trabalho criado e compartilhado pelo projeto de software livre do Android e usadas de acordo com os termos descritos no* [*Licença de atribuição do Creative Commons 2,5*](http://creativecommons.org/licenses/by/2.5/).
