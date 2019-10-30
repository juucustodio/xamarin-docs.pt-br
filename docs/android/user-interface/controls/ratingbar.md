---
title: RatingBar Xamarin. Android
description: Como adicionar um widget RatingBar a uma atividade do Android.
ms.prod: xamarin
ms.assetid: d7a1f9bb-926d-4f93-9e8e-0fa933e330e7
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/29/2018
ms.openlocfilehash: 529fecb4e24e83ef7b783815843e132347d99262
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029149"
---
# <a name="xamarinandroid-ratingbar"></a>RatingBar Xamarin. Android

Um RatingBar é um widget de interface do usuário que exibe uma classificação de uma a cinco estrelas. O usuário pode selecionar uma classificação por gravação em uma estrela nesta seção, você criará um widget que permite ao usuário fornecer uma classificação, com o widget [`RatingBar`](xref:Android.Widget.RatingBar) .

![Exemplo de um RatingBar](ratingbar-images/01-ratingbar.png)

## <a name="creating-a-ratingbar"></a>Criando um RatingBar

1. Abra o arquivo **Resource/layout/Main. axml** e adicione o [`RatingBar`](xref:Android.Widget.RatingBar)
   elemento (dentro do [`LinearLayout`](xref:Android.Widget.LinearLayout)):

   ```xml
   <RatingBar android:id="@+id/ratingbar"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:numStars="5"
            android:stepSize="1.0"/>
   ```

   O atributo `android:numStars` define a quantidade de estrelas a serem exibidas para a barra de classificação. O atributo `android:stepSize` define a granularidade de cada estrela (por exemplo, um valor de `0.5` permitiria classificações de meia estrela).

2. Para fazer algo quando uma nova classificação tiver sido definida, adicione o seguinte código ao final da [`OnCreate()`](xref:Android.App.Activity.OnCreate*)
   forma

    ```csharp
    RatingBar ratingbar = FindViewById<RatingBar>(Resource.Id.ratingbar);

    ratingbar.RatingBarChange += (o, e) => {
            Toast.MakeText(this, "New Rating: " + ratingbar.Rating.ToString (), ToastLength.Short).Show ();
    };
    ```

    Isso captura o widget de [`RatingBar`](xref:Android.Widget.RatingBar) do layout com [`FindViewById`](xref:Android.App.Activity.FindViewById*) e, em seguida, define um método de evento, em seguida, define a ação a ser executada quando o usuário define uma classificação. Nesse caso, uma mensagem [`Toast`](xref:Android.Widget.Toast) simples exibe a nova classificação.

3. Execute o aplicativo.
