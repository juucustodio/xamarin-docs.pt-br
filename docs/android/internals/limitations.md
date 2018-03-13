---
title: "Limitações"
ms.topic: article
ms.prod: xamarin
ms.assetid: F953F9B4-3596-8B3A-A8E4-8219B5B9F7CA
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/15/2018
ms.openlocfilehash: 1b970432d7cd5b6a84b8af72ab616493f3cd36a7
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="limitations"></a>Limitações

Como aplicativos no Android exigem a geração de tipos do Java proxy durante o processo de compilação, não é possível gerar todo o código em tempo de execução.

Estas são as limitações de xamarin em comparação comparadas a área de trabalho Mono:


## <a name="limited-dynamic-language-support"></a>Suporte de linguagem dinâmica limitado

 [Callable wrappers do Android](~/android/platform/java-integration/android-callable-wrappers.md) são necessárias a qualquer momento em que o tempo de execução Android precisa chamar código gerenciado. Callable wrappers do Android são gerados em tempo de compilação, com base na análise estática de IL. O resultado desse: você *não é possível* usar linguagens dinâmicas (IronPython, IronRuby, etc.) em qualquer cenário onde subclassificação dos tipos de Java é necessária (incluindo subclassificação) indireta, pois não há nenhuma maneira de extrair esses tipos dinâmicos em tempo de compilação para gerar os Android callable wrappers do necessário.


## <a name="limited-java-generation-support"></a>Suporte à geração de Java limitado

[Android Callable Wrappers](~/android/platform/java-integration/android-callable-wrappers.md) precisa ser gerado para código de Java chamar código gerenciado. *Por padrão*, callable wrappers do Android conterá apenas (determinados) declarados construtores e métodos que substituir um método virtual de Java (ou seja, ele tem [ `RegisterAttribute` ](https://developer.xamarin.com/api/type/Android.Runtime.RegisterAttribute/)) ou implementar um (de método de interface de Java da mesma forma tem uma interface `Attribute`).
  
Antes da versão 4.1, nenhum outro método adicional pode ser declarado. Com a versão 4.1, [o `Export` e `ExportField` atributos personalizados podem ser usados para declarar os métodos de Java e campos dentro do Android Callable Wrapper](~/android/platform/java-integration/working-with-jni.md).

### <a name="missing-constructors"></a>Construtores ausentes

Construtores permanecem complicados, a menos que [ `ExportAttribute` ](https://developer.xamarin.com/api/type/Java.Interop.ExportAttribute) é usado. O algoritmo para a geração de construtores de wrapper callable Android é que um construtor Java será emitido se:

1. Há um mapeamento de Java para todos os tipos de parâmetro
2. A classe base declara o mesmo construtor &ndash; isso é necessário porque o callable wrapper do Android *deve* chamar o construtor da classe base correspondente; podem ser usados sem argumentos padrão (pois não há nenhuma maneira fácil de determinar quais valores devem ser usados em Java).

Por exemplo, considere a seguinte classe:

```csharp
[Service]
class MyIntentService : IntentService {
    public MyIntentService (): base ("value")
    {
    }
}
```

Embora esse parece perfeitamente lógico, o Android callable wrapper do resultante *nas compilações de lançamento* não contém um construtor padrão. Consequentemente, se você tentar iniciar o serviço (por exemplo, [ `Context.StartService` ](https://developer.xamarin.com/api/member/Android.Content.Context.StartService/p/Android.Content.Intent/)), ele falhará:

```shell
E/AndroidRuntime(31766): FATAL EXCEPTION: main
E/AndroidRuntime(31766): java.lang.RuntimeException: Unable to instantiate service example.MyIntentService: java.lang.InstantiationException: can't instantiate class example.MyIntentService; no empty constructor
E/AndroidRuntime(31766):        at android.app.ActivityThread.handleCreateService(ActivityThread.java:2347)
E/AndroidRuntime(31766):        at android.app.ActivityThread.access$1600(ActivityThread.java:130)
E/AndroidRuntime(31766):        at android.app.ActivityThread$H.handleMessage(ActivityThread.java:1277)
E/AndroidRuntime(31766):        at android.os.Handler.dispatchMessage(Handler.java:99)
E/AndroidRuntime(31766):        at android.os.Looper.loop(Looper.java:137)
E/AndroidRuntime(31766):        at android.app.ActivityThread.main(ActivityThread.java:4745)
E/AndroidRuntime(31766):        at java.lang.reflect.Method.invokeNative(Native Method)
E/AndroidRuntime(31766):        at java.lang.reflect.Method.invoke(Method.java:511)
E/AndroidRuntime(31766):        at com.android.internal.os.ZygoteInit$MethodAndArgsCaller.run(ZygoteInit.java:786)
E/AndroidRuntime(31766):        at com.android.internal.os.ZygoteInit.main(ZygoteInit.java:553)
E/AndroidRuntime(31766):        at dalvik.system.NativeStart.main(Native Method)
E/AndroidRuntime(31766): Caused by: java.lang.InstantiationException: can't instantiate class example.MyIntentService; no empty constructor
E/AndroidRuntime(31766):        at java.lang.Class.newInstanceImpl(Native Method)
E/AndroidRuntime(31766):        at java.lang.Class.newInstance(Class.java:1319)
E/AndroidRuntime(31766):        at android.app.ActivityThread.handleCreateService(ActivityThread.java:2344)
E/AndroidRuntime(31766):        ... 10 more
```

A solução alternativa é declarar um construtor padrão, Adornar com o `ExportAttribute`e defina o [ `ExportAttribute.SuperStringArgument` ](https://developer.xamarin.com/api/property/Java.Interop.ExportAttribute.SuperArgumentsString/): 

```csharp
[Service]
class MyIntentService : IntentService {
    [Export (SuperArgumentsString = "\"value\"")]
    public MyIntentService (): base("value")
    {
    }

    // ...
}
```


### <a name="generic-c-classes"></a>Classes genéricas c#

Classes genéricas c# são suportadas apenas parcialmente. Existem as seguintes limitações:


-   Tipos genéricos não podem usar `[Export]` ou `[ExportField`]. Tentar fazer isso irá gerar um `XA4207` erro.

    ```csharp
    public abstract class Parcelable<T> : Java.Lang.Object, IParcelable
    {
        // Invalid; generates XA4207
        [ExportField ("CREATOR")]
        public static IParcelableCreator CreateCreator ()
        {
            ...
    }
    ```

-   Métodos genéricos não podem usar `[Export]` ou `[ExportField]`:

    ```csharp
    public class Example : Java.Lang.Object
    {
        
        // Invalid; generates XA4207
        [Export]
        public static void Method<T>(T value)
        {
            ...
        }
    }
    ```

-   `[ExportField]` não pode ser usado em métodos que retornam `void`:

    ```csharp
    public class Example : Java.Lang.Object
    {
        // Invalid; generates XA4208
        [ExportField ("CREATOR")]
        public static void CreateSomething ()
        {
        }
    }
    ```

-   Instâncias de tipos genéricos _não_ ser criado a partir do código Java.
    Eles podem ser criados somente com segurança de código gerenciado:

    ```csharp
    [Activity (Label="Die!", MainLauncher=true)]
    public class BadGenericActivity<T> : Activity
    {
        protected override void OnCreate (Bundle bundle)
        {
            base.OnCreate (bundle);
        }
    }
    ```


## <a name="partial-java-generics-support"></a>Suporte a genéricos Java parcial

O suporte a genéricos de associação de Java é limitado. Particularmente, os membros em uma classe de instância genérico que é derivada de outra classe genérica (não instanciado) são deixados expostos como Java.Lang.Object. Por exemplo, [Android.Content.Intent.GetParcelableExtra](https://developer.xamarin.com/api/member/Android.Content.Intent.GetParcelableExtra/p/System.String/) método retorna Java.Lang.Object. Isso ocorre devido a apagados genéricos de Java.
Temos algumas classes que não se aplicam a essa limitação, mas eles são ajustados manualmente.


## <a name="related-links"></a>Links relacionados

- [Callable Wrappers do Android](~/android/platform/java-integration/android-callable-wrappers.md)
- [Trabalhando com JNI](~/android/platform/java-integration/working-with-jni.md)
- [ExportAttribute](https://developer.xamarin.com/api/type/Java.Interop.ExportAttribute/)
- [SuperString](https://developer.xamarin.com/api/property/Java.Interop.ExportAttribute.SuperArgumentsString/)
- [RegisterAttribute](https://developer.xamarin.com/api/type/Android.Runtime.RegisterAttribute/)
