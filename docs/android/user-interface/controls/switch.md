---
title: Alternar
description: Como usar o widget de comutador em um aplicativo xamarin. Android
ms.prod: xamarin
ms.assetid: 6E1F3324-EC41-454A-AEC0-0208813C7E50
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/29/2018
ms.openlocfilehash: e3bcce48a675a9ba3d1d41f93babc7fcb26448c8
ms.sourcegitcommit: 081a2d094774c6f75437d28b71d22607e33aae71
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2018
ms.locfileid: "37403270"
---
# <a name="switch"></a>Alternar

O `Switch` widget (mostrada abaixo) permite que o usuário alterne entre dois estados, como em ou desativado. O `Switch` valor padrão é OFF. Em seus estados ON e OFF, o widget é mostrado abaixo:

[![Capturas de tela de um widget de comutador e ativar Estados](switch-images/16-switch-onoff.png)](switch-images/16-switch-onoff.png#lightbox)


## <a name="creating-a-switch"></a>Criar um comutador

Para criar um comutador, simplesmente declare um `Switch` elemento no XML da seguinte maneira:

```xml
<Switch android:layout_width="wrap_content"
        android:layout_height="wrap_content" />
```

Isso cria um comutador básico, conforme mostrado abaixo:

[![Captura de tela do aplicativo de demonstração, exibindo um comutador no estado desligado](switch-images/07-switch.png)](switch-images/07-switch.png#lightbox)


## <a name="changing-default-values"></a>Alterando os valores padrão

O texto que o controle exibe para o ON e OFF estados e o valor padrão são configuráveis. Por exemplo, para tornar a opção ON por padrão e Sim/não de leitura em vez de ligar/desligar, podemos definir o `checked`, `textOn`, e `textOff` atributos no XML a seguir.

```xml
<Switch android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:checked="true"
        android:textOn="YES"
        android:textOff="NO" />
```



## <a name="providing-a-title"></a>Fornecer um título

O `Switch` widget também dá suporte a incluindo um rótulo de texto, definindo o `text` atributo da seguinte maneira:

```xml
<Switch android:text="Is Xamarin.Android great?"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:checked="true"
        android:textOn="YES"
        android:textOff="NO" />
```

Essa marcação produz a seguinte captura de tela em tempo de execução:

[![Captura de tela do aplicativo de demonstração com o texto horizontalmente antecede o widget de Switch](switch-images/08-switch.png)](switch-images/08-switch.png#lightbox)

Quando um `Switch`do valor for alterado, ele gera um `CheckedChange` eventos.
Por exemplo, no código a seguir, podemos capturar esse evento e apresentar uma `Toast` widget com uma mensagem com base no `isChecked` valor de `Switch`, que é passado para o manipulador de eventos como parte do `CompoundButton.CheckedChangeEventArg` argumento.

```csharp
Switch s = FindViewById<Switch> (Resource.Id.monitored_switch);
           
s.CheckedChange += delegate(object sender, CompoundButton.CheckedChangeEventArgs e) {
    var toast = Toast.MakeText (this, "Your answer is " +
        (e.IsChecked ?  "correct" : "incorrect"), ToastLength.Short);
    toast.Show ();
};
```


## <a name="related-links"></a>Links relacionados

- [SwitchDemo (amostra)](https://developer.xamarin.com/samples/monodroid/SwitchDemo/)
- [Tutorial do guia Layout](~/android/user-interface/layouts/tab-layout/index.md)
