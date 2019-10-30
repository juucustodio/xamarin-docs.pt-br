---
title: ToggleButton
ms.prod: xamarin
ms.assetid: 9ADA8FCF-63ED-897A-DD56-D7D86535A92C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/06/2018
ms.openlocfilehash: fe444d255beb9c08b4b5bcf5de36a8740e503b55
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029289"
---
# <a name="togglebutton"></a>ToggleButton

Nesta seção, você criará um botão usado especificamente para alternar entre dois Estados, usando o widget [`ToggleButton`](xref:Android.Widget.ToggleButton) . Esse widget é uma excelente alternativa para botões de opção se você tiver dois Estados simples mutuamente exclusivos ("on" e "off", por exemplo). O Android 4,0 (API nível 14) introduziu uma alternativa ao botão de alternância conhecido como um [`Switch`](xref:Android.Widget.Switch).

Um exemplo de um **ToggleButton** pode ser visto no par de imagens à esquerda, enquanto o par de imagens à direita apresenta um exemplo de um **interruptor**:

![Exemplos de comutadores e ToggleButtons nos Estados ligado e desligado](toggle-button-images/togglebutton-switch.png)  

O controle que um aplicativo usa é uma questão de estilo. Ambos os widgets são funcionalmente equivalentes.

Abra o arquivo **Resources/layout/Main. axml** e adicione o elemento [`ToggleButton`](xref:Android.Widget.ToggleButton) (dentro do [`LinearLayout`](xref:Android.Widget.LinearLayout)):

Para fazer algo quando o estado for alterado, adicione o seguinte código ao final da [`OnCreate()`](xref:Android.App.Activity.OnCreate*)
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

Isso captura o elemento [`ToggleButton`](xref:Android.Widget.ToggleButton) do layout e manipula o evento de clique, que define a ação a ser executada quando o botão é clicado. Neste exemplo, o método verifica o novo estado do botão e, em seguida, mostra um [`Toast`](xref:Android.Widget.Toast) mensagem que indica o estado atual.

Observe que a [`ToggleButton`](xref:Android.Widget.ToggleButton) manipula sua própria alteração de estado entre marcada e desmarcada, portanto, você apenas pergunta qual é.

Execute o aplicativo.

> [!TIP]
> Se você precisar alterar o estado por conta própria (por exemplo, ao carregar um [`CheckBoxPreference`](xref:Android.Preferences.CheckBoxPreference)salvo), use o [`Checked`](xref:Android.Widget.CompoundButton.Checked)
> setter de propriedade ou [`Toggle()`](xref:Android.Widget.CompoundButton.Toggle)
> método.

## <a name="related-links"></a>Links relacionados

- [Alternância](https://developer.android.com/reference/android/widget/ToggleButton.html)
- [Switch](https://developer.android.com/reference/android/widget/Switch.html)
