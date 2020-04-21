---
title: Wrappers callable android para Xamarin.Android
ms.prod: xamarin
ms.assetid: C33E15FA-1E2B-819A-C656-CA588D611492
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/15/2018
ms.openlocfilehash: ef2f8e0375786ba7b627fdf75545cbb48318c1aa
ms.sourcegitcommit: 854798de42566750d9c70b6d0539b7ee73ff6ddc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2020
ms.locfileid: "81646597"
---
# <a name="android-callable-wrappers-for-xamarinandroid"></a>Wrappers callable android para Xamarin.Android

Os ACWs (ACWs) do Android Callable São necessários sempre que o tempo de execução do Android invoca código gerenciado. Esses invólucros são necessários porque não há como registrar classes com ART (o tempo de execução do Android) em tempo de execução. (Especificamente, a [função JNI DefineClass()](https://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html#wp15986) não é suportada pelo tempo de execução do Android.} Os Wrappers callable do Android compõem assim a falta de suporte de registro do tipo de tempo de execução. 

*Toda vez* O código Android `virtual` precisa executar um `overridden` método ou interface que seja ou implementado em código gerenciado, o Xamarin.Android deve fornecer um proxy Java para que este método seja enviado para o tipo gerenciado apropriado. Esses tipos de proxy Java são código Java que tem a classe base "mesmo" e a lista de interface Java como o tipo gerenciado, implementando os mesmos construtores e declarando quaisquer métodos de classe base e interface substituídos. 

Os invólucros callable do Android são gerados pelo programa **monodroid.exe** durante o [processo de compilação](~/android/deploy-test/building-apps/build-process.md): eles são gerados para todos os tipos que (direta ou indiretamente) herdam [Java.Lang.Object](xref:Java.Lang.Object). 

## <a name="android-callable-wrapper-naming"></a>Nomeação do invólucro callable do Android

Os nomes dos pacotes para Android Callable Wrappers são baseados no MD5SUM do nome qualificado para montagem do tipo que está sendo exportado. Esta técnica de nomeação permite que o mesmo nome de tipo totalmente qualificado seja disponibilizado por diferentes conjuntos sem introduzir um erro de embalagem. 

Por causa deste esquema de nomeação MD5SUM, você não pode acessar diretamente seus tipos pelo nome. Por exemplo, `adb` o seguinte comando não `my.ActivityType` funcionará porque o nome do tipo não é gerado por padrão: 

```shell
adb shell am start -n My.Package.Name/my.ActivityType
```

Além disso, você pode ver erros como o seguinte se você tentar referenciar um tipo por nome:

```shell
java.lang.ClassNotFoundException: Didn't find class "com.company.app.MainActivity"
on path: DexPathList[[zip file "/data/app/com.company.App-1.apk"] ...
```

Se *você* precisar de acesso a tipos por nome, você pode declarar um nome para esse tipo em uma declaração de atributo. Por exemplo, aqui está o código que declara `My.ActivityType`uma atividade com o nome totalmente qualificado:

```csharp
namespace My {
    [Activity]
    public partial class ActivityType : Activity {
        /* ... */
    }
}
```

A `ActivityAttribute.Name` propriedade pode ser definida para declarar explicitamente o nome desta atividade: 

```csharp
namespace My {
    [Activity(Name="my.ActivityType")]
    public partial class ActivityType : Activity {
        /* ... */
    }
}
```

Depois que essa configuração de propriedade for adicionada, `my.ActivityType` `adb` pode ser acessada pelo nome a partir de código externo e de scripts. O `Name` atributo pode ser definido `Activity` `Application`para `Service` `BroadcastReceiver`muitos `ContentProvider`tipos diferentes, incluindo , , e : 

- [ActivityAttribute.Name](xref:Android.App.ActivityAttribute.Name)
- [ApplicationAttribute.Name](xref:Android.App.ApplicationAttribute.Name)
- [ServiceAttribute.Name](xref:Android.App.ServiceAttribute.Name)
- [BroadcastReceiverAttribute.Name](xref:Android.Content.BroadcastReceiverAttribute.Name)
- [ContentProviderAttribute.Name](xref:Android.Content.ContentProviderAttribute.Name)

A nomenclatura ACW baseada em MD5SUM foi introduzida no Xamarin.Android 5.0. Para obter mais informações sobre a nomeação de atributos, consulte [RegisterAttribute](xref:Android.Runtime.RegisterAttribute). 

## <a name="implementing-interfaces"></a>Implementando interfaces

Há momentos em que você pode precisar implementar uma interface Android, como [Android.Content.IComponentCallbacks](xref:Android.Content.IComponentCallbacks). Como todas as classes e interface do Android estendem a interface [Android.Runtime.IJavaObject,](xref:Android.Runtime.IJavaObject) surge a pergunta: como implementamos `IJavaObject`? 

A pergunta foi respondida acima: a razão `IJavaObject` pela qual todos os tipos de Android precisam implementar é para que o Xamarin.Android tenha um invólucro callable android para fornecer ao Android, ou seja, um proxy Java para o tipo dado. Uma **vez que monodroid.exe** só `Java.Lang.Object` procura `IJavaObject` `Java.Lang.Object` `Java.Lang.Object` subclasses e implementos, a resposta é óbvia: subclasse : 

```csharp
class MyComponentCallbacks : Java.Lang.Object, Android.Content.IComponentCallbacks {

    public void OnConfigurationChanged (Android.Content.Res.Configuration newConfig)
    {
        // implementation goes here...
    } 

    public void OnLowMemory ()
    {
        // implementation goes here...
    }
}
```

## <a name="implementation-details"></a>Detalhes da implementação

*O restante desta página fornece detalhes de implementação sujeitos a alterações sem aviso prévio* (e é apresentado aqui apenas porque os desenvolvedores ficarão curiosos sobre o que está acontecendo). 

Por exemplo, dada a seguinte fonte C#:

```csharp
using System;
using Android.App;
using Android.OS;

namespace Mono.Samples.HelloWorld
{
    public class HelloAndroid : Activity
    {
        protected override void OnCreate (Bundle savedInstanceState)
        {
            base.OnCreate (savedInstanceState);
            SetContentView (R.layout.main);
        }
    }
}
```

O programa **mandroid.exe** irá gerar o seguinte Android Callable Wrapper: 

```java
package mono.samples.helloWorld;

public class HelloAndroid
    extends android.app.Activity
{
    static final String __md_methods;
    static {
        __md_methods = "n_onCreate:(Landroid/os/Bundle;)V:GetOnCreate_Landroid_os_Bundle_Handler\n" + "";
        mono.android.Runtime.register (
            "Mono.Samples.HelloWorld.HelloAndroid, HelloWorld, Version=1.0.0.0, 
            Culture=neutral, PublicKeyToken=null", HelloAndroid.class, __md_methods);
    }

    public HelloAndroid ()
    {
        super ();
        if (getClass () == HelloAndroid.class)
            mono.android.TypeManager.Activate (
                "Mono.Samples.HelloWorld.HelloAndroid, HelloWorld, Version=1.0.0.0, 
                Culture=neutral, PublicKeyToken=null", "", this, new java.lang.Object[] {  });
    }

    @Override
    public void onCreate (android.os.Bundle p0)
    {
        n_onCreate (p0);
    }

    private native void n_onCreate (android.os.Bundle p0);
}
```

Observe que a classe base `native` está preservada, e as declarações de método são fornecidas para cada método que é substituído dentro do código gerenciado. 
