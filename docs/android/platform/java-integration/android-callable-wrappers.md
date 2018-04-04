---
title: Callable Wrappers do Android
ms.prod: xamarin
ms.assetid: C33E15FA-1E2B-819A-C656-CA588D611492
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/15/2018
ms.openlocfilehash: bb15c7f3a36cc7f1ed235d92e343bbae67a718b8
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="android-callable-wrappers"></a>Callable Wrappers do Android

Android Callable Wrappers (ACWs) são necessárias sempre que o tempo de execução Android invoca código gerenciado. Esses wrappers serão necessárias porque não é possível registrar classes com arte (o tempo de execução Android) em tempo de execução. (Especificamente, o [função JNI DefineClass()](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html#wp15986) não é suportada pelo tempo de execução do Android.} Callable Wrappers do Android, portanto, fazer para a falta de suporte de registro de tipo de tempo de execução. 

*Sempre* código do Android precisa executar uma `virtual` ou método de interface `overridden` ou implementados em código gerenciado, xamarin deve fornecer um proxy do Java para que esse método é enviado para o tipo gerenciado apropriado. Esses tipos de proxy de Java são o código de Java que tem a classe base "mesmo" e a lista de interfaces de Java como o tipo gerenciado, Implementando os construtores mesmo e declarando uma classe base substituída e métodos de interface. 

Callable wrappers do Android são gerados pelo **monodroid.exe** programa durante o [do processo de compilação](~/android/deploy-test/building-apps/build-process.md): eles são gerados para todos os tipos que herdam (direta ou indiretamente) [ Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/). 



## <a name="android-callable-wrapper-naming"></a>Android Callable Wrapper de nomenclatura

Nomes de pacote para Android Callable Wrappers baseiam-se no MD5SUM do nome do assembly qualificado do tipo que está sendo exportado. Essa técnica de nomenclatura torna possível para o mesmo nome de tipo totalmente qualificado ser disponibilizado por assemblies diferentes sem introduzir um erro de empacotamento. 

Devido a esse esquema de nomenclatura de MD5SUM, você não pode acessar diretamente os tipos por nome. Por exemplo, a seguinte `adb` comando não funcionará porque o nome do tipo `my.ActivityType` não é gerado por padrão: 

```shell
adb shell am start -n My.Package.Name/my.ActivityType
```

Além disso, você pode ver erros, como o seguinte se você tentar fazer referência a um tipo por nome:

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

Depois que a configuração dessa propriedade é adicionada, `my.ActivityType` pode ser acessada pelo nome do código externo e de `adb` scripts. O `Name` atributo pode ser definido para muitos tipos diferentes incluindo `Activity`, `Application`, `Service`, `BroadcastReceiver`, e `ContentProvider`: 

-   [ActivityAttribute.Name](https://developer.xamarin.com/api/property/Android.App.ActivityAttribute.Name/)
-   [ApplicationAttribute.Name](https://developer.xamarin.com/api/property/Android.App.ApplicationAttribute.Name/)
-   [ServiceAttribute.Name](https://developer.xamarin.com/api/property/Android.App.ServiceAttribute.Name/)
-   [BroadcastReceiverAttribute.Name](https://developer.xamarin.com/api/property/Android.Content.BroadcastReceiverAttribute.Name/)
-   [ContentProviderAttribute.Name](https://developer.xamarin.com/api/property/Android.Content.ContentProviderAttribute.Name/)

Com base em MD5SUM ACW nomenclatura foi introduzido no xamarin 5.0. Para obter mais informações sobre a nomeação de atributo, consulte [RegisterAttribute](https://developer.xamarin.com/api/type/Android.Runtime.RegisterAttribute/). 



## <a name="implementing-interfaces"></a>Implementando interfaces

Há ocasiões em que talvez você precise implementar uma interface Android, tal como [Android.Content.IComponentCallbacks](https://developer.xamarin.com/api/type/Android.Content.IComponentCallbacks/). Desde que todas as classes Android e interface estendem o [Android.Runtime.IJavaObject](https://developer.xamarin.com/api/type/Android.Runtime.IJavaObject/) interface, a pergunta surge: como podemos implementar `IJavaObject`? 

A pergunta foi respondida acima: o motivo pelo qual todos os tipos de Android precisam implementar `IJavaObject` é para xamarin tem um callable wrapper do Android para fornecer a Android, ou seja, um proxy do Java para o tipo fornecido. Como **monodroid.exe** procura somente `Java.Lang.Object` subclasses e `Java.Lang.Object` implementa `IJavaObject,` a resposta é óbvia: subclasse `Java.Lang.Object`: 

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

*O restante desta página fornece detalhes de implementação, sujeito a alterações sem aviso prévio* (e são apresentados aqui somente por desenvolvedores será curiosos sobre o que está acontecendo). 

Por exemplo, dada a fonte c# a seguir:

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
