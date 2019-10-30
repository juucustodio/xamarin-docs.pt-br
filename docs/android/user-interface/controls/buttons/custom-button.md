---
title: Botão personalizado
ms.prod: xamarin
ms.assetid: C523D41E-5855-248D-079D-6B12B74B7617
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/06/2018
ms.openlocfilehash: d85c67cf18c61af04cf12bfab58a5b516d380f62
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029363"
---
# <a name="custom-button"></a>Botão personalizado

Nesta seção, você criará um botão com uma imagem personalizada em vez de texto, usando o widget [`Button`](xref:Android.Widget.Button) e um arquivo XML que define três imagens diferentes para usar nos Estados de botão diferentes. Quando o botão for pressionado, uma mensagem curta será exibida.

Clique com o botão direito do mouse e baixe as três imagens abaixo e copie-as para o diretório de **recursos/empates** do seu projeto. Eles serão usados para os diferentes Estados de botão.

 [![ícone do Android verde para o estado normal](custom-button-images/android-normal.png)](custom-button-images/android-normal.png#lightbox) [![ícone do Android em laranja para o estado focalizado](custom-button-images/android-focused.png)](custom-button-images/android-focused.png#lightbox) [![ícone do Android amarelo para o estado pressionado](custom-button-images/android-pressed.png)](custom-button-images/android-pressed.png#lightbox)

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

Isso define um único recurso que poderá ser desenhado, que alterará sua imagem com base no estado atual do botão. O primeiro `<item>` define **android_pressed. png** como a imagem quando o botão é pressionado (ele foi ativado); a segunda `<item>` define **android_focused. png** como a imagem quando o botão está focalizado (quando o botão é realçado usando o trackball ou o pad direcional); e o terceiro `<item>` define **android_normal. png** como a imagem para o estado normal (quando não pressionada nem focada). Esse arquivo XML agora representa um único recurso de desenho e, quando referenciado por um [`Button`](xref:Android.Widget.Button) para seu plano de fundo, a imagem exibida será alterada com base nesses três Estados.

> [!NOTE]
> A ordem dos elementos de `<item>` é importante. Quando esse empate é referenciado, os `<item>`s são percorridos em ordem para determinar qual deles é apropriado para o estado atual do botão.
> Como a imagem "normal" é por último, ela só é aplicada quando as condições `android:state_pressed` e `android:state_focused` ter ambos avaliados como falso.

Abra o arquivo **Resources/layout/Main. axml** e adicione o elemento [`Button`](xref:Android.Widget.Button) :

```xml
<Button
        android:id="@+id/button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:padding="10dp"
        android:background="@drawable/android_button" />
```

O atributo `android:background` especifica o recurso desenhável a ser usado para o plano de fundo do botão (que, quando salvo em **recursos/desenhável/Android. xml**, é referenciado como `@drawable/android`). Isso substitui a imagem de plano de fundo normal usada para botões em todo o sistema. Para que o Draw possa alterar sua imagem com base no estado do botão, a imagem deve ser aplicada ao plano de fundo.

Para fazer com que o botão faça algo quando pressionado, adicione o seguinte código ao final da [`OnCreate()`](xref:Android.App.Activity.OnCreate*)
forma

```csharp
Button button = FindViewById<Button>(Resource.Id.button);

button.Click += (o, e) => {
    Toast.MakeText (this, "Beep Boop", ToastLength.Short).Show ();
};
```

Isso captura a [`Button`](xref:Android.Widget.Button) do layout e adiciona uma mensagem de [`Toast`](xref:Android.Widget.Toast) a ser exibida quando o [`Button`](xref:Android.Widget.Button) é clicado.

Agora, execute o aplicativo.

*Partes desta página são modificações com base no trabalho criado e compartilhado pelo projeto de software livre do Android e usadas de acordo com os termos descritos na licença de* [*atribuição
Creative Commons 2,5*](https://creativecommons.org/licenses/by/2.5/).
