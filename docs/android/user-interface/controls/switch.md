---
title: Opção Xamarin. Android
description: Como usar o widget switch em um aplicativo Xamarin. Android
ms.prod: xamarin
ms.assetid: 6E1F3324-EC41-454A-AEC0-0208813C7E50
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/29/2018
ms.openlocfilehash: 73becb5e4424854c9be6cdc3554f6cf93507b9a9
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029132"
---
# <a name="xamarinandroid-switch"></a>Opção Xamarin. Android

O widget `Switch` (mostrado abaixo) permite que um usuário alterne entre dois Estados, como ativado ou desativado. O valor padrão de `Switch` é OFF. O widget é mostrado abaixo nos Estados de ligar e desligar:

[![capturas de tela de um widget de comutador nos Estados desligado e em](switch-images/16-switch-onoff.png)](switch-images/16-switch-onoff.png#lightbox)

## <a name="creating-a-switch"></a>Criando uma opção

Para criar uma opção, basta declarar um elemento `Switch` em XML da seguinte maneira:

```xml
<Switch android:layout_width="wrap_content"
        android:layout_height="wrap_content" />
```

Isso cria um comutador básico, conforme mostrado abaixo:

[![captura de tela do aplicativo de demonstração exibindo um interruptor no estado desligado](switch-images/07-switch.png)](switch-images/07-switch.png#lightbox)

## <a name="changing-default-values"></a>Alterando valores padrão

O texto que o controle exibe para os Estados ligado e desligado e o valor padrão é configurável. Por exemplo, para tornar a opção padrão para ativado e ler não/sim em vez de OFF/ON, podemos definir os atributos `checked`, `textOn`e `textOff` no XML a seguir.

```xml
<Switch android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:checked="true"
        android:textOn="YES"
        android:textOff="NO" />
```

## <a name="providing-a-title"></a>Fornecendo um título

O widget de `Switch` também dá suporte à inclusão de um rótulo de texto, definindo o atributo `text` da seguinte maneira:

```xml
<Switch android:text="Is Xamarin.Android great?"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:checked="true"
        android:textOn="YES"
        android:textOff="NO" />
```

Essa marcação produz a seguinte captura de tela no tempo de execução:

[![captura de tela do aplicativo de demonstração com texto que precede horizontalmente o widget de alternância](switch-images/08-switch.png)](switch-images/08-switch.png#lightbox)

Quando um valor de `Switch`é alterado, ele gera um evento de `CheckedChange`.
Por exemplo, no código a seguir, capturamos esse evento e apresentamos um widget `Toast` com uma mensagem baseada no valor de `isChecked` de `Switch`, que é passado para o manipulador de eventos como parte do argumento `CompoundButton.CheckedChangeEventArg`.

```csharp
Switch s = FindViewById<Switch> (Resource.Id.monitored_switch);
           
s.CheckedChange += delegate(object sender, CompoundButton.CheckedChangeEventArgs e) {
    var toast = Toast.MakeText (this, "Your answer is " +
        (e.IsChecked ?  "correct" : "incorrect"), ToastLength.Short);
    toast.Show ();
};
```

## <a name="related-links"></a>Links relacionados

- [SwitchDemo (exemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/switchdemo)
- [Tutorial de layout de guia](~/android/user-interface/layouts/tab-layout/index.md)
