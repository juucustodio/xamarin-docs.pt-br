---
title: Botão personalizado
ms.prod: xamarin
ms.assetid: C523D41E-5855-248D-079D-6B12B74B7617
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: ecb745f2f50b5aa0e22e331a4def0be9d8f86aa5
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68510390"
---
# <a name="custom-button"></a>Botão personalizado

Nesta seção, você criará um botão com uma imagem personalizada em vez de texto, usando o [`Button`](xref:Android.Widget.Button) widget e um arquivo XML que define três imagens diferentes a serem usadas para os Estados de botão diferentes. Quando o botão for pressionado, uma mensagem curta será exibida.

Clique com o botão direito do mouse e baixe as três imagens abaixo e copie-as para o diretório de **recursos/empates** do seu projeto. Eles serão usados para os diferentes Estados de botão.

 [ ![](custom-button-images/android-focused.png)](custom-button-images/android-focused.png#lightbox) [ ÍconeverdedoAndroidparaoestadonormalíconedoAndroidemlaranjaparaoestadoprioritárioícone![do](custom-button-images/android-normal.png)](custom-button-images/android-normal.png#lightbox) [ ![Android amarelo para o estado pressionado](custom-button-images/android-pressed.png)](custom-button-images/android-pressed.png#lightbox)

Crie um novo arquivo no diretório de **recursos/empates** chamado **android_button. xml**. Insira o seguinte XML:

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

Isso define um único recurso que poderá ser desenhado, que alterará sua imagem com base no estado atual do botão. O primeiro `<item>` define **android_pressed. png** como a imagem quando o botão é pressionado (ele foi ativado); o segundo `<item>` define **android_focused. png** como a imagem quando o botão está focalizado (quando o botão é realçado usando o trackball ou o pad direcional); e o terceiro `<item>` define **android_normal. png** como a imagem para o estado normal (quando não pressionada nem focada). Esse arquivo XML agora representa um único recurso de desenho e, quando referenciado [`Button`](xref:Android.Widget.Button) por um para seu plano de fundo, a imagem exibida será alterada com base nesses três Estados.


> [!NOTE]
> A ordem dos `<item>` elementos é importante. Quando esse empate é referenciado, os `<item>`s são percorridos em ordem para determinar qual deles é apropriado para o estado atual do botão.
> Como a imagem "normal" é por último, ela só é aplicada quando as `android:state_pressed` condições `android:state_focused` e ambos são avaliados como falso.

Abra o arquivo Resources **/layout/Main. axml** e [`Button`](xref:Android.Widget.Button) adicione o elemento:

```xml
<Button
        android:id="@+id/button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:padding="10dp"
        android:background="@drawable/android_button" />
```

O `android:background` atributo especifica o recurso que deve ser desenhado a ser usado para o plano de fundo do botão (que, quando salvo em **recursos/desenhável/Android. xml**, é referenciado como `@drawable/android`). Isso substitui a imagem de plano de fundo normal usada para botões em todo o sistema. Para que o Draw possa alterar sua imagem com base no estado do botão, a imagem deve ser aplicada ao plano de fundo.

Para fazer com que o botão faça algo quando pressionado, adicione o seguinte código ao final do[`OnCreate()`](xref:Android.App.Activity.OnCreate*)
forma

```csharp
Button button = FindViewById<Button>(Resource.Id.button);

button.Click += (o, e) => {
    Toast.MakeText (this, "Beep Boop", ToastLength.Short).Show ();
};
```

Isso captura o [`Button`](xref:Android.Widget.Button) do layout e, em seguida, adiciona [`Toast`](xref:Android.Widget.Toast) uma mensagem a ser exibida quando [`Button`](xref:Android.Widget.Button) o é clicado.

Agora, execute o aplicativo.


*Partes desta página são modificações com base no trabalho criado e compartilhado pelo projeto de software livre do Android e usadas de acordo com os termos descritos na licença de atribuição do*
[*Creative Commons 2,5*](http://creativecommons.org/licenses/by/2.5/).
