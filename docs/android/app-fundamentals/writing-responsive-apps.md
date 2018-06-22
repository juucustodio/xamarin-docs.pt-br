---
title: Gravando aplicativos responsivos
ms.prod: xamarin
ms.assetid: 452DF940-6331-55F0-D130-002822BBED55
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/15/2018
ms.openlocfilehash: b8c113b67b3fbfa57ca86c72e11ddeb0e4e1a9ab
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
ms.locfileid: "30763495"
---
# <a name="writing-responsive-applications"></a>Gravando aplicativos responsivos

Uma das chaves para manter uma GUI de resposta é fazer tarefas de longa execução em um thread em segundo plano para a interface gráfica do usuário não sejam bloqueados. Vamos dizer que desejamos calcular um valor a ser exibido para o usuário, mas esse valor tem 5 segundos para calcular:

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

Isso funcionará, mas o aplicativo será "congelar" por 5 segundos enquanto o valor é calculado. Durante esse tempo, o aplicativo não responder a qualquer interação do usuário. Para contornar esse problema, queremos fazer os cálculos em um thread em segundo plano:

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

Agora podemos calcular o valor em um thread em segundo plano para que nossa interface gráfica do usuário permanece responsivo durante o cálculo. No entanto, quando o cálculo é feito, nosso aplicativo falha, deixe esse no log:

```shell
E/mono    (11207): EXCEPTION handling: Android.Util.AndroidRuntimeException: Exception of type 'Android.Util.AndroidRuntimeException' was thrown.
E/mono    (11207):
E/mono    (11207): Unhandled Exception: Android.Util.AndroidRuntimeException: Exception of type 'Android.Util.AndroidRuntimeException' was thrown.
E/mono    (11207):   at Android.Runtime.JNIEnv.CallVoidMethod (IntPtr jobject, IntPtr jmethod, Android.Runtime.JValue[] parms)
E/mono    (11207):   at Android.Widget.TextView.set_Text (IEnumerable`1 value)
E/mono    (11207):   at MonoDroidDebugging.Activity1.SlowMethod ()
```

Isso ocorre porque você deve atualizar a interface gráfica do usuário do thread de interface gráfica do usuário. Nosso código atualiza a GUI do thread ThreadPool, fazendo com que o aplicativo falhe. Precisamos para calcular o nosso valor no thread em segundo plano, mas, em seguida, fazer nossa atualização no thread da interface gráfica do usuário, que é tratado com [Activity.RunOnUIThread](https://developer.xamarin.com/api/member/Android.App.Activity.RunOnUiThread/(System.Action)):

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

Esse código funciona conforme o esperado. Essa interface gráfica do usuário continua respondendo e é atualizada corretamente quando o cálculo é complexos.

Observe que essa técnica não é usada apenas para calcular um valor caro. Ele pode ser usado para qualquer tarefa de execução demorada que pode ser feita em segundo plano, como uma chamada de serviço web ou baixar dados da internet.
