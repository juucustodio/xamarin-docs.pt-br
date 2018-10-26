---
title: Escrevendo aplicativos responsivos
ms.prod: xamarin
ms.assetid: 452DF940-6331-55F0-D130-002822BBED55
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/15/2018
ms.openlocfilehash: a1642c4cbb790cf09d2a31e629408afc61d5b7ab
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50121770"
---
# <a name="writing-responsive-applications"></a>Escrevendo aplicativos responsivos

Uma das chaves para manter uma GUI responsiva é fazer tarefas de longa execução em um thread em segundo plano para que a interface gráfica do usuário não seja bloqueada. Digamos que queremos calcular um valor a ser exibido para o usuário, mas esse valor leva de 5 segundos para calcular:

```csharp
public class ThreadDemo : Activity
{
    TextView textview;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        // Create a new TextView and set it as our view
        textview = new TextView (this);
        textview.Text = "Working..";

        SetContentView (textview);

        SlowMethod ();
    }

    private void SlowMethod ()
    {
        Thread.Sleep (5000);
        textview.Text = "Method Complete";
    }
}
```

Isso funcionará, mas o aplicativo será "congelar" por 5 segundos enquanto o valor é calculado. Durante esse tempo, o aplicativo não responder a qualquer interação do usuário. Para contornar isso, queremos fazer nossos cálculos em um thread em segundo plano:

```csharp
public class ThreadDemo : Activity
{
    TextView textview;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        // Create a new TextView and set it as our view
        textview = new TextView (this);
        textview.Text = "Working..";

        SetContentView (textview);

        ThreadPool.QueueUserWorkItem (o => SlowMethod ());
    }

    private void SlowMethod ()
    {
        Thread.Sleep (5000);
        textview.Text = "Method Complete";
    }
}
```

Agora, podemos calcular o valor em um thread em segundo plano para que nossa interface gráfica do usuário permaneça responsivo durante o cálculo. No entanto, quando o cálculo é concluído, nosso aplicativo falha, deixe esse no log:

```shell
E/mono    (11207): EXCEPTION handling: Android.Util.AndroidRuntimeException: Exception of type 'Android.Util.AndroidRuntimeException' was thrown.
E/mono    (11207):
E/mono    (11207): Unhandled Exception: Android.Util.AndroidRuntimeException: Exception of type 'Android.Util.AndroidRuntimeException' was thrown.
E/mono    (11207):   at Android.Runtime.JNIEnv.CallVoidMethod (IntPtr jobject, IntPtr jmethod, Android.Runtime.JValue[] parms)
E/mono    (11207):   at Android.Widget.TextView.set_Text (IEnumerable`1 value)
E/mono    (11207):   at MonoDroidDebugging.Activity1.SlowMethod ()
```

Isso ocorre porque você deve atualizar a GUI do thread de GUI. Nosso código atualiza a GUI do thread do ThreadPool, causando a falha no aplicativo. Precisamos calcular nosso valor no thread em segundo plano, mas, em seguida, fazer nossa atualização no thread de GUI, que é tratado com [Activity.RunOnUIThread](https://developer.xamarin.com/api/member/Android.App.Activity.RunOnUiThread/(System.Action)):

```csharp
public class ThreadDemo : Activity
{
    TextView textview;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        // Create a new TextView and set it as our view
        textview = new TextView (this);
        textview.Text = "Working..";

        SetContentView (textview);

        ThreadPool.QueueUserWorkItem (o => SlowMethod ());
    }

    private void SlowMethod ()
    {
        Thread.Sleep (5000);
        RunOnUiThread (() => textview.Text = "Method Complete");
    }
}
```

Esse código funciona conforme o esperado. Essa interface gráfica do usuário permanece responsiva e é atualizada corretamente depois que o cálculo é comple.

Observe que essa técnica não é usada apenas para calcular um valor de caro. Ele pode ser usado para qualquer tarefa de longa execução que pode ser feita em segundo plano, como uma chamada de serviço web ou baixar dados de internet.
