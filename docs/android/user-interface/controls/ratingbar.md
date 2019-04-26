---
title: RatingBar
description: Como adicionar um widget RatingBar para uma atividade do Android.
ms.prod: xamarin
ms.assetid: d7a1f9bb-926d-4f93-9e8e-0fa933e330e7
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/29/2018
ms.openlocfilehash: 97d2a126be70e210d2e8f4ebf4d7a25ff8777a02
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60945430"
---
# <a name="ratingbar"></a>RatingBar

Um RatingBar é um widget de interface do usuário que exibe uma classificação de uma a cinco estrelas. O usuário pode selecionar uma classificação clicando em uma estrela nesta seção, você criará um widget que permite que o usuário forneça uma classificação, com o [ `RatingBar` ](https://developer.xamarin.com/api/type/Android.Widget.RatingBar/) widget.

![Exemplo de um RatingBar](ratingbar-images/01-ratingbar.png)


## <a name="creating-a-ratingbar"></a>Criando um RatingBar

1. Abra o **Resource/layout/Main.axml** arquivo e adicione o [`RatingBar`](https://developer.xamarin.com/api/type/Android.Widget.RatingBar/)
   elemento (dentro de [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)):

    ```xml
    <RatingBar android:id="@+id/ratingbar"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:numStars="5"
            android:stepSize="1.0"/>
    ```
   O `android:numStars` atributo define quantas estrelas para exibir a barra de classificação. O `android:stepSize` atributo define a granularidade de cada estrela (por exemplo, um valor de `0.5` permitiria que as classificações de estrelas metade).

2. Para fazer algo quando uma nova classificação foi definida, adicione o seguinte código ao final das [`OnCreate()`](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/Android.OS.PersistableBundle)
   método:

    ```csharp
    RatingBar ratingbar = FindViewById<RatingBar>(Resource.Id.ratingbar);

    ratingbar.RatingBarChange += (o, e) => {
            Toast.MakeText(this, "New Rating: " + ratingbar.Rating.ToString (), ToastLength.Short).Show ();
    };
    ```

    Isso captura a [ `RatingBar` ](https://developer.xamarin.com/api/type/Android.Widget.RatingBar/) widget do layout com [ `FindViewById` ](https://developer.xamarin.com/api/member/Android.App.Activity.FindViewById/) e, em seguida, define um método de evento, em seguida, define a ação a ser executada quando o usuário define uma classificação. Nesse caso, uma simples [ `Toast` ](https://developer.xamarin.com/api/type/Android.Widget.Toast/) a nova classificação de mensagem é exibida.

3.  Execute o aplicativo.

