---
title: Escrevendo aplicativos responsivos
ms.prod: xamarin
ms.assetid: 452DF940-6331-55F0-D130-002822BBED55
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/15/2018
ms.openlocfilehash: e3f7d788e71718f4ca1336b7906cf3d63bf07f32
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68509205"
---
# <a name="writing-responsive-applications"></a>Escrevendo aplicativos responsivos

Uma das chaves para manter uma GUI responsiva é realizar tarefas de longa execução em um thread em segundo plano para que a GUI não seja bloqueada. Digamos que desejamos calcular um valor a ser exibido para o usuário, mas esse valor leva 5 segundos para calcular:

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

Isso funcionará, mas o aplicativo será "interrompido" por 5 segundos enquanto o valor é calculado. Durante esse tempo, o aplicativo não responderá a nenhuma interação do usuário. Para contornar isso, queremos fazer nossos cálculos em um thread em segundo plano:

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

Agora, calculamos o valor em um thread em segundo plano para que nossa GUI permaneça responsiva durante o cálculo. No entanto, quando o cálculo é feito, nosso aplicativo falha, deixando isso no log:

```shell
E/mono    (11207): EXCEPTION handling: Android.Util.AndroidRuntimeException: Exception of type 'Android.Util.AndroidRuntimeException' was thrown.
E/mono    (11207):
E/mono    (11207): Unhandled Exception: Android.Util.AndroidRuntimeException: Exception of type 'Android.Util.AndroidRuntimeException' was thrown.
E/mono    (11207):   at Android.Runtime.JNIEnv.CallVoidMethod (IntPtr jobject, IntPtr jmethod, Android.Runtime.JValue[] parms)
E/mono    (11207):   at Android.Widget.TextView.set_Text (IEnumerable`1 value)
E/mono    (11207):   at MonoDroidDebugging.Activity1.SlowMethod ()
```

Isso ocorre porque você deve atualizar a GUI do thread de GUI. Nosso código atualiza a GUI do thread ThreadPool, fazendo com que o aplicativo falhe. Precisamos calcular nosso valor no thread em segundo plano, mas, em seguida, fazer nossa atualização no thread de GUI, que é manipulado com [Activity. RunOnUIThread](xref:Android.App.Activity.RunOnUiThread*):

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

Esse código funciona conforme o esperado. Essa GUI permanece responsiva e é atualizada corretamente quando o cálculo é comple.

Observe que essa técnica não é usada apenas para calcular um valor caro. Ele pode ser usado para qualquer tarefa de execução longa que pode ser feita em segundo plano, como uma chamada de serviço Web ou o download de dados da Internet.
