---
title: Editar texto
ms.prod: xamarin
ms.assetid: E513BCBC-438E-15E8-B83A-4B768A8E8B32
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: d6be8ae1587742a8c2a37b22b2da3701187dde2a
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
ms.locfileid: "30774597"
---
# <a name="edit-text"></a>Editar texto

Nesta seção, você criará um campo de texto de entrada do usuário, usando o [EditText](https://developer.xamarin.com/api/type/Android.Widget.EditText/) widget. Depois que foi digitado no campo de texto, a tecla "Enter" exibirá o texto em uma mensagem de notificação do sistema.

Abra o <code>Resources\layout\main.xml</code> e adicione o [EditText](https://developer.xamarin.com/api/type/Android.Widget.EditText/) elemento (dentro de [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)):

```xml
<pre><code class=" syntax brush-C#">&lt;EditText
    android:id="@+id/edittext"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"/&gt;</code></pre>
```

Para fazer algo com o texto que o usuário digita, adicione o seguinte código ao final do [OnCreate](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/) método:

```csharp
EditText edittext = FindViewById<EditText>(Resource.Id.edittext);
edittext.KeyPress += (object sender, View.KeyEventArgs e) => {
 e.Handled = false;
 if (e.Event.Action == KeyEventActions.Down && e.KeyCode == Keycode.Enter) {
  Toast.MakeText (this, edittext.Text, ToastLength.Short).Show ();
  e.Handled = true;
         }
};
```

Isso captura o [ `EditText` ](https://developer.xamarin.com/api/type/Android.Widget.EditText/) elemento do layout e define uma [ `KeyPress` ](https://developer.xamarin.com/api/event/Android.Views.View.KeyPress/) manipulador, que define a ação a ser feita quando uma tecla é pressionada enquanto o widget tem foco. Nesse caso, o método está definido para escutar a tecla Enter (quando pressionado) e, em seguida, pop-up uma [ `Toast` ](https://developer.xamarin.com/api/type/Android.Widget.Toast/) mensagem com o texto que foi digitado. O [ `Handled` ](https://developer.xamarin.com/api/property/Android.Views.View+KeyEventArgs.Handled/) propriedade deve ser sempre `true` se o evento foi tratado, para que o evento não bolha-up (que pode resultar em um retorno de carro no campo de texto).

Execute o aplicativo.

*Partes desta página são modificações com base no trabalho criado e* [ *compartilhado pelo projeto de código-fonte aberto Android* ](http://code.google.com/policies.html) *e usada de acordo com condições descritas no* [ *Creative Commons 2.5 atribuição licença* ](http://creativecommons.org/licenses/by/2.5/) *. Este tutorial se baseia o* [ *tutorial Android coisas de forma* ](http://developer.android.com/resources/tutorials/views/hello-formstuff.html) *.*



## <a name="related-links"></a>Links relacionados

- [EditTextSample](https://developer.xamarin.com/samples/monodroid/UserInterface/EditTextSample/)
