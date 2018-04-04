---
title: ToggleButton
ms.prod: xamarin
ms.assetid: 9ADA8FCF-63ED-897A-DD56-D7D86535A92C
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 8323c456a97a033e19374a4bd3ea7468ecafa608
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="togglebutton"></a>ToggleButton

Nesta seção, você criará um botão usado especificamente para alternância entre dois estados, usando o [ `ToggleButton` ](https://developer.xamarin.com/api/type/Android.Widget.ToggleButton/) widget. Esse widget é uma excelente alternativa para botões de opção se você tiver dois estados simples que são mutuamente exclusivos ("on" e "desativado", por exemplo). Android 4.0 (API nível 14) introduziu uma alternativa para o botão de alternância, conhecido como um [ `Switch` ](https://developer.xamarin.com/api/type/Android.Widget.Switch/).

Um exemplo de um **ToggleButton** podem ser vistos no par esquerdo das imagens, enquanto o par de direito de imagens apresenta um exemplo de um **Switch**:

![Exemplos de comutadores e ToggleButtons no logon e logoff Estados](toggle-button-images/togglebutton-switch.png)  

O controle de um aplicativo usa é uma questão de estilo. Ambos os widgets são funcionalmente equivalentes.

Abra o **Resources/layout/Main.axml** e adicione o [ `ToggleButton` ](https://developer.xamarin.com/api/type/Android.Widget.ToggleButton/) elemento (dentro de [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)):

Para fazer algo quando o estado é alterado, adicione o seguinte código ao final do [ `OnCreate()` ](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/Android.OS.PersistableBundle) método:

```csharp
ToggleButton togglebutton = FindViewById<ToggleButton>(Resource.Id.togglebutton);

togglebutton.Click += (o, e) => {
    // Perform action on clicks
    if (togglebutton.Checked)
        Toast.MakeText(this, "Checked", ToastLength.Short).Show ();
    else
        Toast.MakeText(this, "Not checked", ToastLength.Short).Show ();
};
```

Isso captura o [ `ToggleButton` ](https://developer.xamarin.com/api/type/Android.Widget.ToggleButton/) elemento do layout e manipula o evento de clique, que define a ação a ser executada quando o botão é clicado. Neste exemplo, o método verifica o novo estado do botão e mostra um [ `Toast` ](https://developer.xamarin.com/api/type/Android.Widget.Toast/) mensagem que indica o estado atual.

Observe que o [ `ToggleButton` ](https://developer.xamarin.com/api/type/Android.Widget.ToggleButton/) identificadores de seu próprio estado alterar entre marcados e desmarcados, para que você peça que ele é apenas.

Execute o aplicativo.


**Dica:** se você precisar alterar o estado por conta própria (como quando carregar um salvo [ `CheckBoxPreference` ](https://developer.xamarin.com/api/type/Android.Preferences.CheckBoxPreference/)), use o [ `Checked` ](https://developer.xamarin.com/api/property/Android.Widget.CompoundButton.Checked/) setter de propriedade ou [ `Toggle()` ](https://developer.xamarin.com/api/member/Android.Widget.CompoundButton.Toggle/) método.


## <a name="related-links"></a>Links relacionados

- [ToggleButton](http://developer.android.com/reference/android/widget/ToggleButton.html)
- [Switch](http://developer.android.com/reference/android/widget/Switch.html)
