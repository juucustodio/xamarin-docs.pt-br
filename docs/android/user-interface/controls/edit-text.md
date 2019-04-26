---
title: Editar texto
description: Como usar o widget EditText para aceitar a entrada do usuário.
ms.prod: xamarin
ms.assetid: E513BCBC-438E-15E8-B83A-4B768A8E8B32
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/09/2018
ms.openlocfilehash: d42cec1ee0939bead9ede83a042f5b6cbb5298cd
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61153533"
---
# <a name="edit-text"></a>Editar texto

Nesta seção, você aprenderá a usar o [EditText](https://developer.xamarin.com/api/type/Android.Widget.EditText/) widget para criar um campo de texto de entrada do usuário. Depois que foi digitado no campo de texto a **Enter** chave exibirá o texto em uma mensagem de notificação do sistema.

Abra **Resources/layout/activity_main.axml** e adicione o [EditText](https://developer.xamarin.com/api/type/Android.Widget.EditText/) elemento a um recipiente de layout. O exemplo a seguir **activity_main.axml** tem um `EditText` que tenha sido adicionado a um `LinearLayout`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <EditText
        android:id="@+id/edittext"
        android:layout_width="match_parent"
        android:imeOptions="actionGo"
        android:inputType="text"
        android:layout_height="wrap_content" />
</LinearLayout>
```

Neste exemplo de código, o `EditText` atributo `android:imeOptions` é definido como `actionGo`. Essa configuração altera o padrão [feito](https://developer.android.com/reference/android/view/inputmethod/EditorInfo#IME_ACTION_DONE) ação para o [Ir](https://developer.android.com/reference/android/view/inputmethod/EditorInfo#IME_ACTION_GO) ação, de modo que tocar o **Enter** gatilhos da chave o `KeyPress` manipulador de entrada.
(Normalmente, `actionGo` é usado para que o **Enter** chave leva o usuário para o destino de uma URL que é digitado.)

Para lidar com entrada de texto do usuário, adicione o seguinte código ao final dos [OnCreate](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/) método na **MainActivity.cs**:

```csharp
EditText edittext = FindViewById<EditText>(Resource.Id.edittext);
edittext.KeyPress += (object sender, View.KeyEventArgs e) => {
    e.Handled = false;
    if (e.Event.Action == KeyEventActions.Down && e.KeyCode == Keycode.Enter) 
    {
        Toast.MakeText(this, edittext.Text, ToastLength.Short).Show();
        e.Handled = true;
    }
};
```

Além disso, adicione o seguinte `using` instrução na parte superior do **MainActivity.cs** se ele não ainda estiver presente:

```csharp
using Android.Views;
```

Este exemplo de código infla a [EditText](https://developer.xamarin.com/api/type/Android.Widget.EditText/) elemento do layout e adiciona uma [KeyPress](https://developer.xamarin.com/api/event/Android.Views.View.KeyPress/) manipulador que define a ação a ser feita quando uma tecla é pressionada enquanto o widget tem o foco. Nesse caso, o método é definido para escutar o **Enter** chave (quando tocado) e, em seguida, pop-up uma [Toast](https://developer.xamarin.com/api/type/Android.Widget.Toast/) mensagem com o texto que foi inserido. Observe que o [Handled](https://developer.xamarin.com/api/property/Android.Views.View+KeyEventArgs.Handled/) propriedade deve ser sempre `true` se o evento foi tratado. Isso é necessário para impedir que o evento por propagação up (o que resulta em um retorno de carro no campo de texto).

Execute o aplicativo e digite algum texto no campo de texto. Quando você pressiona o **Enter** chave, a notificação do sistema será exibida conforme mostrado à direita:

[![Exemplos de como inserir texto em EditText](edit-text-images/edit-text-sml.png)](edit-text-images/edit-text.png#lightbox)

*Partes desta página são modificações com base no trabalho criado e* [ *compartilhados por Android Open Source Project* ](http://code.google.com/policies.html) *e usadas de acordo com os termos descritos no* [ *Licença de atribuição creative Commons 2.5* ](http://creativecommons.org/licenses/by/2.5/) *. Este tutorial se baseia a* [ *tutorial Android coisas de forma* ](https://developer.android.com/resources/tutorials/views/hello-formstuff.html) *.*


## <a name="related-links"></a>Links relacionados

- [EditTextSample](https://developer.xamarin.com/samples/monodroid/UserInterface/EditTextSample/)
