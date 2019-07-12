---
title: CheckBox
ms.prod: xamarin
ms.assetid: A884AF10-D5EA-72CA-2301-B80CEC7FFBE7
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 9f2fd10e5cfe28206d323b2769517c2584919232
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67829719"
---
# <a name="checkbox"></a>CheckBox

Nesta seção, você aprenderá a criar uma caixa de seleção para a seleção de itens, usando o [`CheckBox`](https://developer.xamarin.com/api/type/Android.Widget.CheckBox)
widget. Quando a caixa de seleção é pressionada, uma mensagem de notificação do sistema indicará o estado atual da caixa de seleção.

Abra o **Resources/layout/Main.axml** arquivo e adicione o [ `CheckBox` ](https://developer.xamarin.com/api/type/Android.Widget.CheckBox/) elemento (dentro a [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout)):

```xml
<CheckBox android:id="@+id/checkbox"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="check it out" />
```

Para fazer algo quando o estado é alterado, adicione o seguinte código ao final das [`OnCreate()`](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/Android.OS.PersistableBundle)
método:

```csharp
CheckBox checkbox = FindViewById<CheckBox>(Resource.Id.checkbox);

checkbox.Click += (o, e) => {
    if (checkbox.Checked)
        Toast.MakeText (this, "Selected", ToastLength.Short).Show ();
    else
        Toast.MakeText (this, "Not selected", ToastLength.Short).Show ();
};
```

Isso captura o [`CheckBox`](https://developer.xamarin.com/api/type/Android.Widget.CheckBox/)
elemento do layout, em seguida, manipula o evento de clique, que define a ação a ser feita quando a caixa de seleção é clicada. Quando clicado, o [`Checked`](https://developer.xamarin.com/api/property/Android.Widget.CompoundButton.Checked/)
propriedade é chamada para verificar o novo estado da caixa de seleção. Se foram verificado, em seguida, um [`Toast`](https://developer.xamarin.com/api/type/Android.Widget.Toast/)
Exibe a mensagem "Selecionados", caso contrário, que ele exibe "Não selecionados". O [`CheckBox`](https://developer.xamarin.com/api/type/Android.Widget.CheckBox/)
lida com suas próprias alterações de estado, portanto, você precisará consultar o estado atual.

Executá-lo.

> [!TIP]
> Se você precisar alterar o estado por conta própria (por exemplo, quando carregar um salvo [ `CheckBoxPreference` ](https://developer.xamarin.com/api/type/Android.Preferences.CheckBoxPreference), use o [`Checked`](https://developer.xamarin.com/api/property/Android.Widget.CompoundButton.Checked)
> setter de propriedade ou [`Toggle()`](https://developer.xamarin.com/api/member/Android.Widget.CompoundButton.Toggle)
> método.

*Partes desta página são modificações com base no trabalho criado e compartilhado por Android Open Source Project e usadas de acordo com os termos descritos na*
[*2.5 atribuição de licença da Creative Commons* ](http://creativecommons.org/licenses/by/2.5/).
