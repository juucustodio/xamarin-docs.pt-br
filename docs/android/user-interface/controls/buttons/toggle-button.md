---
title: ToggleButton
ms.prod: xamarin
ms.assetid: 9ADA8FCF-63ED-897A-DD56-D7D86535A92C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 56372bb643cab545529d6a4a89c804471f3344bc
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70762454"
---
# <a name="togglebutton"></a>ToggleButton

Nesta seção, você criará um botão usado especificamente para alternar entre dois Estados, usando o [`ToggleButton`](xref:Android.Widget.ToggleButton) widget. Esse widget é uma excelente alternativa para botões de opção se você tiver dois Estados simples mutuamente exclusivos ("on" e "off", por exemplo). O Android 4,0 (API nível 14) introduziu uma alternativa ao botão de alternância conhecido [`Switch`](xref:Android.Widget.Switch)como um.

Um exemplo de um **ToggleButton** pode ser visto no par de imagens à esquerda, enquanto o par de imagens à direita apresenta um exemplo de um **interruptor**:

![Exemplos de comutadores e ToggleButtons nos Estados ligado e desligado](toggle-button-images/togglebutton-switch.png)  

O controle que um aplicativo usa é uma questão de estilo. Ambos os widgets são funcionalmente equivalentes.

Abra o arquivo **Resources/layout/Main. axml** e [`ToggleButton`](xref:Android.Widget.ToggleButton) adicione o elemento ( [`LinearLayout`](xref:Android.Widget.LinearLayout)dentro do):

Para fazer algo quando o estado for alterado, adicione o seguinte código ao final do[`OnCreate()`](xref:Android.App.Activity.OnCreate*)
forma

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

Isso captura o [`ToggleButton`](xref:Android.Widget.ToggleButton) elemento do layout e manipula o evento de clique, que define a ação a ser executada quando o botão é clicado. Neste exemplo, o método verifica o novo estado do botão e, em seguida, mostra [`Toast`](xref:Android.Widget.Toast) uma mensagem que indica o estado atual.

Observe que o [`ToggleButton`](xref:Android.Widget.ToggleButton) manipula sua própria alteração de estado entre marcada e desmarcada, portanto, você apenas pergunta qual é.

Execute o aplicativo.

> [!TIP]
> Se você precisar alterar o estado por conta própria (por exemplo, ao carregar [`CheckBoxPreference`](xref:Android.Preferences.CheckBoxPreference)um salvo), use o[`Checked`](xref:Android.Widget.CompoundButton.Checked)
> setter de propriedade ou[`Toggle()`](xref:Android.Widget.CompoundButton.Toggle)
> método.

## <a name="related-links"></a>Links relacionados

- [ToggleButton](https://developer.android.com/reference/android/widget/ToggleButton.html)
- [Switch](https://developer.android.com/reference/android/widget/Switch.html)
