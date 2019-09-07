---
title: Editar texto
description: Como usar o widget EditText para aceitar a entrada do usuário.
ms.prod: xamarin
ms.assetid: E513BCBC-438E-15E8-B83A-4B768A8E8B32
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/09/2018
ms.openlocfilehash: e8ffe337e1f5c74bc348b9600a466f1232f40b0b
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70758842"
---
# <a name="xamarinandroid-edit-text"></a>Editar texto do Xamarin. Android

Nesta seção, você usará o widget [EDITTEXT](xref:Android.Widget.EditText) para criar um campo de texto para entrada do usuário. Depois que o texto tiver sido inserido no campo, a tecla **Enter** exibirá o texto em uma mensagem do sistema.

Abra **Resources/layout/activity_main. axml** e adicione o elemento [EDITTEXT](xref:Android.Widget.EditText) a um layout de contenção. O exemplo a seguir **activity_main. axml** tem `EditText` um que foi adicionado a um `LinearLayout`:

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

Neste exemplo de código, o `EditText` atributo `android:imeOptions` é definido como `actionGo`. Essa configuração altera a ação padrão [feita](https://developer.android.com/reference/android/view/inputmethod/EditorInfo#IME_ACTION_DONE) para a ação [go](https://developer.android.com/reference/android/view/inputmethod/EditorInfo#IME_ACTION_GO) para que tocar a tecla **Enter** dispare `KeyPress` o manipulador de entrada.
(Normalmente, `actionGo` é usado para que a tecla **Enter** leve o usuário para o destino de uma URL que é digitada em.)

Para manipular a entrada de texto do usuário, adicione o seguinte código ao final do método [OnCreate](xref:Android.App.Activity.OnCreate*) em **MainActivity.cs**:

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

Além disso, adicione a seguinte `using` instrução à parte superior de **MainActivity.cs** se ela ainda não estiver presente:

```csharp
using Android.Views;
```

Este exemplo de código inplana o elemento [EDITTEXT](xref:Android.Widget.EditText) do layout e adiciona um manipulador de [pressionamento](xref:Android.Views.View.KeyPress) de teclas que define a ação a ser feita quando uma tecla é pressionada enquanto o widget tem foco. Nesse caso, o método é definido para escutar a tecla **Enter** (quando tocado) e, em seguida, exibir uma mensagem do [sistema](xref:Android.Widget.Toast) com o texto que foi inserido. Observe que a propriedade [Handled](xref:Android.Views.View.KeyEventArgs.Handled) sempre deve ser `true` se o evento tiver sido manipulado. Isso é necessário para impedir que o evento seja bolha (o que resultaria em um retorno de carro no campo de texto).

Execute o aplicativo e insira algum texto no campo de texto. Quando você pressionar a tecla **Enter** , o sistema de notificação será exibido conforme mostrado à direita:

[![Exemplos de inserção de texto em EditText](edit-text-images/edit-text-sml.png)](edit-text-images/edit-text.png#lightbox)

*Partes desta página são modificações com base no trabalho criado e* [*compartilhado pelo projeto de código aberto do Android*](http://code.google.com/policies.html) *e usado de acordo com os termos descritos no* [*Licença de atribuição do Creative Commons 2,5*](http://creativecommons.org/licenses/by/2.5/) *. Este tutorial se baseia no tutorial* de [*coisas de formulário do Android*](https://developer.android.com/resources/tutorials/views/hello-formstuff.html) *.*

## <a name="related-links"></a>Links relacionados

- [EditTextSample](https://docs.microsoft.com/samples/xamarin/monodroid-samples/userinterface-edittextsample)
