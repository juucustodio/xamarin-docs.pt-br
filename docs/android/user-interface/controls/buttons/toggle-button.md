---
title: ToggleButton
ms.prod: xamarin
ms.assetid: 9ADA8FCF-63ED-897A-DD56-D7D86535A92C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: a22d274feb5539164663ac0c48e5a84bdf5d2c66
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67830223"
---
# <a name="togglebutton"></a>ToggleButton

Nesta seção, você criará um botão usado especificamente para alternância entre dois estados, usando o [ `ToggleButton` ](https://developer.xamarin.com/api/type/Android.Widget.ToggleButton/) widget. Este widget é uma excelente alternativa para botões de opção se você tiver dois estados simples que são mutuamente exclusivos ("on" e "desativado", por exemplo). Android 4.0 (API nível 14) introduziu uma alternativa para o botão de alternância, conhecido como um [ `Switch` ](https://developer.xamarin.com/api/type/Android.Widget.Switch/).

Um exemplo de uma **ToggleButton** pode ser visto no par de imagens, à esquerda, enquanto o par de mão direita das imagens apresenta um exemplo de uma **Switch**:

![Exemplos de Switches e ToggleButtons dentro e fora dos Estados](toggle-button-images/togglebutton-switch.png)  

Controle que usa um aplicativo é uma questão de estilo. Ambos os widgets são funcionalmente equivalentes.

Abra o **Resources/layout/Main.axml** arquivo e adicione o [ `ToggleButton` ](https://developer.xamarin.com/api/type/Android.Widget.ToggleButton/) elemento (dentro a [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)):

Para fazer algo quando o estado é alterado, adicione o seguinte código ao final das [`OnCreate()`](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/Android.OS.PersistableBundle)
método:

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

Isso captura a [ `ToggleButton` ](https://developer.xamarin.com/api/type/Android.Widget.ToggleButton/) elemento do layout e manipula o evento de clique, que define a ação a ser executada quando o botão é clicado. Neste exemplo, o método verifica se o novo estado do botão, em seguida, mostra uma [ `Toast` ](https://developer.xamarin.com/api/type/Android.Widget.Toast/) mensagem que indica o estado atual.

Observe que o [ `ToggleButton` ](https://developer.xamarin.com/api/type/Android.Widget.ToggleButton/) identificadores de seu próprio estado alterar entre marcados e desmarcados, para que você acabou de fazer a que ele é.

Execute o aplicativo.


> [!TIP]
> Se você precisar alterar o estado por conta própria (por exemplo, quando carregar um salvo [ `CheckBoxPreference` ](https://developer.xamarin.com/api/type/Android.Preferences.CheckBoxPreference/)), use o [`Checked`](https://developer.xamarin.com/api/property/Android.Widget.CompoundButton.Checked/)
> setter de propriedade ou [`Toggle()`](https://developer.xamarin.com/api/member/Android.Widget.CompoundButton.Toggle/)
> método.


## <a name="related-links"></a>Links relacionados

- [ToggleButton](https://developer.android.com/reference/android/widget/ToggleButton.html)
- [Switch](https://developer.android.com/reference/android/widget/Switch.html)
