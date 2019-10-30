---
title: CheckBox
ms.prod: xamarin
ms.assetid: A884AF10-D5EA-72CA-2301-B80CEC7FFBE7
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/06/2018
ms.openlocfilehash: 06908ad8993eb6d47476006b23865fa1c7fe694f
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029338"
---
# <a name="checkbox"></a>CheckBox

Nesta seção, você criará uma caixa de seleção para selecionar itens, usando o widget [`CheckBox`](xref:Android.Widget.CheckBox) . Quando a caixa de seleção é pressionada, uma mensagem do sistema indica o estado atual da caixa de seleção.

Abra o arquivo **Resources/layout/Main. axml** e adicione o elemento [`CheckBox`](xref:Android.Widget.CheckBox) (dentro do [`LinearLayout`](xref:Android.Widget.LinearLayout)):

```xml
<CheckBox android:id="@+id/checkbox"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="check it out" />
```

Para fazer algo quando o estado for alterado, adicione o seguinte código ao final do método de [`OnCreate()`](xref:Android.App.Activity.OnCreate*) :

```csharp
CheckBox checkbox = FindViewById<CheckBox>(Resource.Id.checkbox);

checkbox.Click += (o, e) => {
    if (checkbox.Checked)
        Toast.MakeText (this, "Selected", ToastLength.Short).Show ();
    else
        Toast.MakeText (this, "Not selected", ToastLength.Short).Show ();
};
```

Isso captura o elemento [`CheckBox`](xref:Android.Widget.CheckBox) do layout e, em seguida, manipula o evento de clique, que define a ação a ser feita quando a caixa de seleção é clicada. Quando clicado, a propriedade [`Checked`](xref:Android.Widget.CompoundButton.Checked) é chamada para verificar o novo estado da caixa de seleção. Se tiver sido marcada, um [`Toast`](xref:Android.Widget.Toast) exibirá a mensagem "selecionado", caso contrário, ele exibirá "não selecionado". O [`CheckBox`](xref:Android.Widget.CheckBox) manipula suas próprias alterações de estado, portanto, você só precisa consultar o estado atual.

Execute-o.

> [!TIP]
> Se precisar alterar o estado por conta própria (por exemplo, ao carregar um [`CheckBoxPreference`](xref:Android.Preferences.CheckBoxPreference)salvo, use o método setter ou [`Toggle()`](xref:Android.Widget.CompoundButton.Toggle) da propriedade [`Checked`](xref:Android.Widget.CompoundButton.Checked) .

*Partes desta página são modificações com base no trabalho criado e compartilhado pelo projeto de software livre do Android e usadas de acordo com os termos descritos na licença de atribuição do* [*Creative Commons 2,5*](https://creativecommons.org/licenses/by/2.5/).
