---
title: Wrappers que podem ser chamados pelo Android para Xamarin. Android
ms.prod: xamarin
ms.assetid: C33E15FA-1E2B-819A-C656-CA588D611492
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/15/2018
ms.openlocfilehash: b55cffc19eec5ae95a0a0aba8053bdaaa49e7747
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70761474"
---
# <a name="android-callable-wrappers-for-xamarinandroid"></a>Wrappers que podem ser chamados pelo Android para Xamarin. Android

Os ACWs (Android callable wrappers) são necessários sempre que o tempo de execução do Android invoca o código gerenciado. Esses wrappers são necessários porque não há como registrar classes com arte (o tempo de execução do Android) em tempo de execução. (Especificamente, a [função JNI DefineClass ()](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html#wp15986) não é suportada pelo tempo de execução do Android.} Os wrappers que podem ser chamados pelo Android, portanto, constituem a falta de suporte ao registro de tipo de tempo de execução. 

*Toda vez* O código do Android precisa executar `virtual` um método de interface ou `overridden` que seja ou implementado em código gerenciado, o Xamarin. Android deve fornecer um proxy Java para que esse método seja expedido para o tipo gerenciado apropriado. Esses tipos de proxy Java são código Java que tem a "mesma" classe base e a lista de interface java como o tipo gerenciado, implementando os mesmos construtores e declarando qualquer classe base substituída e métodos de interface. 

Os wrappers que podem ser chamados pelo Android são gerados pelo programa **monodroid. exe** durante o [processo de compilação](~/android/deploy-test/building-apps/build-process.md): eles são gerados para todos os tipos que (direta ou indiretamente) herdam [Java. lang. Object](xref:Java.Lang.Object). 

## <a name="android-callable-wrapper-naming"></a>Nomenclatura do wrapper callable do Android

Os nomes de pacote para wrappers que podem ser chamados pelo Android são baseados no MD5SUM do nome qualificado pelo assembly do tipo que está sendo exportado. Essa técnica de nomenclatura possibilita que o mesmo nome de tipo totalmente qualificado seja disponibilizado por diferentes assemblies sem introduzir um erro de empacotamento. 

Devido a esse esquema de nomenclatura MD5SUM, você não pode acessar diretamente seus tipos por nome. Por exemplo, o comando `adb` a seguir não funcionará porque o nome `my.ActivityType` do tipo não é gerado por padrão: 

```shell
adb shell am start -n My.Package.Name/my.ActivityType
```

Além disso, você poderá ver erros como o seguinte se tentar fazer referência a um tipo por nome:

```shell
java.lang.ClassNotFoundException: Didn't find class "com.company.app.MainActivity"
on path: DexPathList[[zip file "/data/app/com.company.App-1.apk"] ...
```

Se você *precisar de* acesso a tipos por nome, poderá declarar um nome para esse tipo em uma declaração de atributo. Por exemplo, aqui está o código que declara uma atividade com o nome `My.ActivityType`totalmente qualificado:

```csharp
namespace My {
    [Activity]
    public partial class ActivityType : Activity {
        /* ... */
    }
}
```

A `ActivityAttribute.Name` propriedade pode ser definida para declarar explicitamente o nome dessa atividade: 

```csharp
namespace My {
    [Activity(Name="my.ActivityType")]
    public partial class ActivityType : Activity {
        /* ... */
    }
}
```

Depois que essa configuração de propriedade é `my.ActivityType` adicionada, pode ser acessada pelo nome de `adb` código externo e de scripts. O `Name` atributo pode ser definido para muitos tipos diferentes, `Activity`incluindo `Application`, `Service` `BroadcastReceiver`,, e `ContentProvider`: 

- [ActivityAttribute.Name](xref:Android.App.ActivityAttribute.Name)
- [ApplicationAttribute.Name](xref:Android.App.ApplicationAttribute.Name)
- [ServiceAttribute.Name](xref:Android.App.ServiceAttribute.Name)
- [BroadcastReceiverAttribute.Name](xref:Android.Content.BroadcastReceiverAttribute.Name)
- [ContentProviderAttribute.Name](xref:Android.Content.ContentProviderAttribute.Name)

A nomenclatura de ACW baseada em MD5SUM foi introduzida no Xamarin. Android 5,0. Para obter mais informações sobre a nomenclatura de atributo, consulte [RegisterAttribute](xref:Android.Runtime.RegisterAttribute). 

## <a name="implementing-interfaces"></a>Implementando interfaces

Há ocasiões em que talvez seja necessário implementar uma interface do Android, como [Android. Content. IComponentCallbacks](xref:Android.Content.IComponentCallbacks). Como todas as classes e interfaces do Android estendem a interface [Android. Runtime. IJavaObject](xref:Android.Runtime.IJavaObject) , a questão surge: como `IJavaObject`implementamos? 

A pergunta foi respondida acima: o motivo pelo qual todos os tipos `IJavaObject` Android precisam ser implementados é que o Xamarin. Android tem um wrapper callable Android para fornecer ao Android, ou seja, um proxy Java para o tipo fornecido. Como **monodroid. exe** só procura por `Java.Lang.Object` subclasses e `Java.Lang.Object` implementa `IJavaObject,` a resposta é óbvia: subclasse `Java.Lang.Object`: 

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

*O restante desta página fornece detalhes de implementação sujeitos a alterações sem aviso prévio* (e é apresentado aqui somente porque os desenvolvedores estarão curiosos sobre o que está acontecendo). 

Por exemplo, considerando a seguinte C# fonte:

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

O programa **mandroid. exe** irá gerar o seguinte wrapper do Android callable: 

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

Observe que a classe base é preservada e `native` declarações de método são fornecidas para cada método que é substituído no código gerenciado. 
