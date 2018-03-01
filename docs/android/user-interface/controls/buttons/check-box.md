---
title: CheckBox
ms.topic: article
ms.prod: xamarin
ms.assetid: A884AF10-D5EA-72CA-2301-B80CEC7FFBE7
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 78b32a90661c4fde279314aed5c62854b497980a
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="checkbox"></a>CheckBox

Nesta seção, você criará uma caixa de seleção para selecionar itens, usando o [ `CheckBox` ](https://developer.xamarin.com/api/type/Android.Widget.CheckBox) widget. Quando a caixa de seleção é pressionada, uma mensagem de notificação do sistema indicará o estado atual da caixa de seleção.

Abra o **Resources/layout/Main.axml** e adicione o [ `CheckBox` ](https://developer.xamarin.com/api/type/Android.Widget.CheckBox/) elemento (dentro de [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout)):

```xml
<CheckBox android:id="@+id/checkbox"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="check it out" />
```

Para fazer algo quando o estado é alterado, adicione o seguinte código ao final do [ `OnCreate()` ](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/Android.OS.PersistableBundle) método:

```csharp
CheckBox checkbox = FindViewById<CheckBox>(Resource.Id.checkbox);

checkbox.Click += (o, e) => {
    if (checkbox.Checked)
        Toast.MakeText (this, "Selected", ToastLength.Short).Show ();
    else
        Toast.MakeText (this, "Not selected", ToastLength.Short).Show ();
};
```

Isso captura o [ `CheckBox` ](https://developer.xamarin.com/api/type/Android.Widget.CheckBox/) elemento do layout, em seguida, manipula o evento de clique, que define a ação a ser feita quando a caixa de seleção é clicada. Quando clicado, o [ `Checked` ](https://developer.xamarin.com/api/property/Android.Widget.CompoundButton.Checked/) propriedade é chamada para verificar o estado novo da caixa de seleção. Se tiver sido verificada, uma [ `Toast` ](https://developer.xamarin.com/api/type/Android.Widget.Toast/) exibe a mensagem "Selecionados", caso contrário, ele exibe "Não selecionado". O [ `CheckBox` ](https://developer.xamarin.com/api/type/Android.Widget.CheckBox/) gerencia suas próprias alterações de estado, portanto você precisa consultar o estado atual.

Executá-lo.

**Dica:** se você precisar alterar o estado por conta própria (como quando carregar um salvo [ `CheckBoxPreference` ](https://developer.xamarin.com/api/type/Android.Preferences.CheckBoxPreference), use o [ `Checked` ](https://developer.xamarin.com/api/property/Android.Widget.CompoundButton.Checked) setter de propriedade ou [ `Toggle()` ](https://developer.xamarin.com/api/member/Android.Widget.CompoundButton.Toggle) método.

*Partes desta página são modificações com base no trabalho criado e compartilhado pelo Android Abrir projeto de origem e usada de acordo com condições descritas no*
[*Creative Commons 2.5 atribuição de licença* ](http://creativecommons.org/licenses/by/2.5/).
