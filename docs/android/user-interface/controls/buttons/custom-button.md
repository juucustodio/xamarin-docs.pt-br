---
title: Botão personalizado
ms.prod: xamarin
ms.assetid: C523D41E-5855-248D-079D-6B12B74B7617
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: e6fc3fe4c3cb89d74188557615f58cc8e34f5991
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="custom-button"></a>Botão personalizado

Nesta seção, você criará um botão com uma imagem personalizada em vez de texto, usando o [ `Button` ](https://developer.xamarin.com/api/type/Android.Widget.Button/) widget e um arquivo XML que define três imagens diferentes a ser usado para os estados de botão diferente. Quando o botão é pressionado, uma mensagem curta será exibida.

Clique com botão direito e baixar as três imagens abaixo, copie-os para o **recursos/drawable** diretório do projeto. Esses serão usados para os estados de botão diferente.

 [![Um ícone verde Android para o estado normal](custom-button-images/android-normal.png)](custom-button-images/android-normal.png#lightbox) [ ![ícone laranja Android para o estado de determinados](custom-button-images/android-focused.png)](custom-button-images/android-focused.png#lightbox) [ ![ícone amarelo Android para o estado pressionado](custom-button-images/android-pressed.png)](custom-button-images/android-pressed.png#lightbox)

Criar um novo arquivo no **recursos/drawable** diretório denominado **android_button.xml**. Insira o seguinte XML:

```xml
<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:drawable="@drawable/android_pressed"
          android:state_pressed="true" />
    <item android:drawable="@drawable/android_focused"
          android:state_focused="true" />
    <item android:drawable="@drawable/android_normal" />
</selector>
```

Isso define um único recurso drawable, que altera sua imagem com base no estado atual do botão. A primeira `<item>` define **android_pressed.png** como a imagem quando o botão é pressionado (ela foi ativada); o segundo `<item>` define **android_focused.png** como a imagem quando o botão destina-se (quando o botão é realçado usando o trackball ou direcional); e o terceiro `<item>` define **android_normal.png** como a imagem para o estado normal (quando pressionado nem focalizado). Esse arquivo XML agora representa um único recurso drawable e quando referenciada por uma [ `Button` ](https://developer.xamarin.com/api/type/Android.Widget.Button/) para seu plano de fundo, a imagem exibida será alterada com base em um destes três estados.


> [!NOTE]
> A ordem dos `<item>` elementos é importante. Quando nesse drawable é referenciada, o `<item>`s são percorridos para determinar qual é apropriado para o estado atual do botão.
> Como a imagem "normal" seja o última, é só aplicada quando as condições `android:state_pressed` e `android:state_focused` ambos avaliaram false.

Abra o **Resources/layout/Main.axml** e adicione o [ `Button` ](https://developer.xamarin.com/api/type/Android.Widget.Button/) elemento:

```xml
<Button
        android:id="@+id/button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:padding="10dp"
        android:background="@drawable/android_button" />
```

O `android:background` atributo especifica o recurso drawable a ser usado para o plano de fundo do botão (que, quando salvo em **Resources/drawable/android.xml**, é referido como `@drawable/android`). Isso substitui a imagem de plano de fundo normal usada para botões em todo o sistema. Para drawable alterar sua imagem de acordo com o estado do botão, a imagem deve ser aplicada ao plano de fundo.

Para fazer com que o botão fazer algo quando pressionado, adicione o seguinte código no final do [ `OnCreate()` ](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/Android.OS.PersistableBundle/) método:

```csharp
Button button = FindViewById<Button>(Resource.Id.button);

button.Click += (o, e) => {
    Toast.MakeText (this, "Beep Boop", ToastLength.Short).Show ();
};
```

Isso captura o [ `Button` ](https://developer.xamarin.com/api/type/Android.Widget.Button/) do layout, em seguida, adiciona um [ `Toast` ](https://developer.xamarin.com/api/type/Android.Widget.Toast/) mensagem a ser exibida quando o [ `Button` ](https://developer.xamarin.com/api/type/Android.Widget.Button/) é clicado.

Agora execute o aplicativo.


*Partes desta página são modificações com base no trabalho criado e compartilhado pelo Android Abrir projeto de origem e usada de acordo com condições descritas no*
[*Creative Commons 2.5 atribuição de licença* ](http://creativecommons.org/licenses/by/2.5/).
