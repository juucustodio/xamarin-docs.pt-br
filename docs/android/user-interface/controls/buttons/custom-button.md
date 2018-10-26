---
title: Botão personalizado
ms.prod: xamarin
ms.assetid: C523D41E-5855-248D-079D-6B12B74B7617
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: b5ccefa1eb7e659584c1c82481bbd4473a3a8abc
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50122849"
---
# <a name="custom-button"></a>Botão personalizado

Nesta seção, você criará um botão com uma imagem personalizada em vez de texto, usando o [ `Button` ](https://developer.xamarin.com/api/type/Android.Widget.Button/) widget e um arquivo XML que define três diferentes imagens a ser usado para os estados do botão diferente. Quando o botão é pressionado, uma mensagem curta será exibida.

Com o botão direito e baixar as três imagens abaixo e, em seguida, copiá-los para o **recursos/drawable** do seu projeto. Eles serão usados para os estados do botão diferente.

 [![Ícone verde Android para o estado normal](custom-button-images/android-normal.png)](custom-button-images/android-normal.png#lightbox) [ ![ícone laranja Android para o estado de foco](custom-button-images/android-focused.png)](custom-button-images/android-focused.png#lightbox) [ ![ícone amarelo Android para o estado pressionado](custom-button-images/android-pressed.png)](custom-button-images/android-pressed.png#lightbox)

Criar um novo arquivo na **recursos/drawable** diretório chamado **android_button.xml**. Insira o seguinte XML:

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

Isso define um único recurso desenhável, que vai alterar sua imagem com base no estado atual do botão. A primeira `<item>` define **android_pressed.png** como a imagem quando o botão é pressionado (ela foi ativada); o segundo `<item>` define **android_focused.png** como a imagem quando o botão destina-se (quando o botão está realçado usando o trackball ou direcional); e o terceiro `<item>` define **android_normal.png** como a imagem para o estado normal (quando pressionado nem focalizado). Esse arquivo XML agora representa um único recurso desenhável e quando referenciado por uma [ `Button` ](https://developer.xamarin.com/api/type/Android.Widget.Button/) para seu plano de fundo, a imagem exibida será alterada com base em um destes três estados.


> [!NOTE]
> A ordem dos `<item>` elementos é importante. Quando nesse desenháveis é referenciada, o `<item>`s são percorridos em ordem para determinar qual delas é apropriado para o estado atual do botão.
> Como a imagem "normal" é o último, é apenas aplicada quando as condições `android:state_pressed` e `android:state_focused` ambos avaliou false.

Abra o **Resources/layout/Main.axml** arquivo e adicione o [ `Button` ](https://developer.xamarin.com/api/type/Android.Widget.Button/) elemento:

```xml
<Button
        android:id="@+id/button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:padding="10dp"
        android:background="@drawable/android_button" />
```

O `android:background` atributo especifica o recurso desenhável a ser usado para o plano de fundo do botão (que, quando salvo no **Resources/drawable/android.xml**, é referenciado como `@drawable/android`). Isso substitui a imagem normal do plano de fundo usada nos botões em todo o sistema. Em ordem para desenháveis alterar sua imagem de acordo com o estado do botão, a imagem deve ser aplicada ao plano de fundo.

Para fazer com que o botão fazer algo quando pressionado, adicione o seguinte código no final das [`OnCreate()`](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/Android.OS.PersistableBundle/)
método:

```csharp
Button button = FindViewById<Button>(Resource.Id.button);

button.Click += (o, e) => {
    Toast.MakeText (this, "Beep Boop", ToastLength.Short).Show ();
};
```

Isso captura a [ `Button` ](https://developer.xamarin.com/api/type/Android.Widget.Button/) do layout, em seguida, adiciona um [ `Toast` ](https://developer.xamarin.com/api/type/Android.Widget.Toast/) mensagem a ser exibida quando o [ `Button` ](https://developer.xamarin.com/api/type/Android.Widget.Button/) é clicado.

Agora execute o aplicativo.


*Partes desta página são modificações com base no trabalho criado e compartilhado por Android Open Source Project e usadas de acordo com os termos descritos na*
[*2.5 atribuição de licença da Creative Commons* ](http://creativecommons.org/licenses/by/2.5/).
