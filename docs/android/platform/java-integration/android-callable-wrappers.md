---
title: Callable Wrappers do Android
ms.prod: xamarin
ms.assetid: C33E15FA-1E2B-819A-C656-CA588D611492
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/15/2018
ms.openlocfilehash: 7edbdaa5a690a641523cb5baad7909ed01992aa5
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50121809"
---
# <a name="android-callable-wrappers"></a>Callable Wrappers do Android

Callable Wrappers do Android (ACWs) são necessárias sempre que o tempo de execução do Android invoca código gerenciado. Esses wrappers são necessárias porque não há nenhuma maneira de registrar as classes com arte (o tempo de execução Android) em tempo de execução. (Especificamente, o [JNI DefineClass() função](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html#wp15986) não é compatível com o tempo de execução do Android.} Callable Wrappers do Android, portanto, fazer a falta de suporte ao registro de tipo de tempo de execução. 

*Sempre que* código do Android precisa executar uma `virtual` ou método que é de interface `overridden` ou implementados em código gerenciado, xamarin. Android deve fornecer um proxy de Java para que esse método é expedido para o tipo gerenciado apropriado. Esses tipos de proxy do Java são código Java que tem a classe base "mesmo" e a lista de interface de Java como o tipo gerenciado, os mesmos construtores de implementação e declarar qualquer classe base substituído e métodos de interface. 

Callable wrappers do Android são gerados pelo **monodroid.exe** de programa durante a [processo de compilação](~/android/deploy-test/building-apps/build-process.md): eles são gerados para todos os tipos que herdam (direta ou indiretamente) [ Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/). 



## <a name="android-callable-wrapper-naming"></a>Nomenclatura do Android Callable Wrapper

Nomes de pacote para Callable Wrappers do Android se baseiam os MD5SUM do nome qualificado pelo assembly do tipo que está sendo exportado. Essa técnica de nomenclatura torna possível para o mesmo nome de tipo totalmente qualificado ser disponibilizado por assemblies diferentes sem introduzir um erro de empacotamento. 

Devido a esse esquema de nomenclatura MD5SUM, você não pode acessar diretamente seus tipos por nome. Por exemplo, a seguinte `adb` comando não funcionará porque o nome do tipo `my.ActivityType` não é gerado por padrão: 

```shell
adb shell am start -n My.Package.Name/my.ActivityType
```

Além disso, você poderá ver erros, como o seguinte se você tentar fazer referência a um tipo por nome:

```shell
java.lang.ClassNotFoundException: Didn't find class "com.company.app.MainActivity"
on path: DexPathList[[zip file "/data/app/com.company.App-1.apk"] ...
```

Se você *fazer* exigem acesso aos tipos por nome, você pode declarar um nome para esse tipo em uma declaração de atributo. Por exemplo, aqui está o código que declara uma atividade com o nome totalmente qualificado `My.ActivityType`:

```csharp
namespace My {
    [Activity]
    public partial class ActivityType : Activity {
        /* ... */
    }
}
```

O `ActivityAttribute.Name` propriedade pode ser definida para declarar explicitamente o nome dessa atividade: 

```csharp
namespace My {
    [Activity(Name="my.ActivityType")]
    public partial class ActivityType : Activity {
        /* ... */
    }
}
```

Depois que a configuração dessa propriedade é adicionada, `my.ActivityType` pode ser acessado pelo nome do código externo e de `adb` scripts. O `Name` atributo pode ser definido para muitos tipos diferentes incluindo `Activity`, `Application`, `Service`, `BroadcastReceiver`, e `ContentProvider`: 

-   [ActivityAttribute.Name](https://developer.xamarin.com/api/property/Android.App.ActivityAttribute.Name/)
-   [ApplicationAttribute.Name](https://developer.xamarin.com/api/property/Android.App.ApplicationAttribute.Name/)
-   [ServiceAttribute.Name](https://developer.xamarin.com/api/property/Android.App.ServiceAttribute.Name/)
-   [BroadcastReceiverAttribute.Name](https://developer.xamarin.com/api/property/Android.Content.BroadcastReceiverAttribute.Name/)
-   [ContentProviderAttribute.Name](https://developer.xamarin.com/api/property/Android.Content.ContentProviderAttribute.Name/)

Com base em MD5SUM ACW nomenclatura foi introduzido no xamarin. Android 5.0. Para obter mais informações sobre a nomeação de atributo, consulte [RegisterAttribute](https://developer.xamarin.com/api/type/Android.Runtime.RegisterAttribute/). 



## <a name="implementing-interfaces"></a>Implementando interfaces

Há momentos em que talvez você precise implementar uma interface Android, tal como [Android.Content.IComponentCallbacks](https://developer.xamarin.com/api/type/Android.Content.IComponentCallbacks/). Uma vez que todas as classes do Android e interface estendem o [Android.Runtime.IJavaObject](https://developer.xamarin.com/api/type/Android.Runtime.IJavaObject/) surge do interface, a pergunta: como podemos implementar `IJavaObject`? 

A pergunta foi respondida acima: o motivo pelo qual todos os tipos de Android precisam implementar `IJavaObject` é para que o xamarin. Android tem um callable wrapper do Android para fornecer ao Android, ou seja, um proxy de Java para o tipo fornecido. Uma vez que **monodroid.exe** procura apenas `Java.Lang.Object` subclasses, e `Java.Lang.Object` implementa `IJavaObject,` a resposta será óbvia: subclasse `Java.Lang.Object`: 

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


## <a name="implementation-details"></a>Detalhes de implementação

*O restante desta página fornece detalhes de implementação, sujeito a alterações sem aviso prévio* (e é apresentada aqui apenas porque os desenvolvedores ficarão curiosos sobre o que está acontecendo). 

Por exemplo, considerando o seguinte C# código-fonte:

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

O **mandroid.exe** programa irá gerar o Android Callable Wrapper a seguir: 

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

Observe que a classe base é preservada, e `native` declarações de método são fornecidas para cada método que é substituído no código gerenciado. 
