---
title: Xamarin. Android versus desktop-diferenças no tempo de execução do mono
ms.prod: xamarin
ms.assetid: F953F9B4-3596-8B3A-A8E4-8219B5B9F7CA
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/25/2018
ms.openlocfilehash: 8fe0e3a9adedb161c527ccdf6d6c3a7cd06a1d86
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027844"
---
# <a name="limitations"></a>Limitações

Como os aplicativos no Android exigem a geração de tipos de proxy Java durante o processo de compilação, não é possível gerar todo o código em tempo de execução.

Essas são as limitações do Xamarin. Android em comparação com o mono da área de trabalho:

## <a name="limited-dynamic-language-support"></a>Suporte limitado a linguagens dinâmicas

 Os [wrappers](~/android/platform/java-integration/android-callable-wrappers.md) que podem ser chamados pelo Android são necessários sempre que o tempo de execução do Android precisa invocar o código gerenciado. Os wrappers que podem ser chamados pelo Android são gerados no momento da compilação, com base na análise estática de IL. O resultado final disso: você *não pode* usar linguagens dinâmicas (IronPython, IronRuby, etc.) em qualquer cenário em que a subclasse de tipos Java é necessária (incluindo a subclasse indireta), pois não há como extrair esses tipos dinâmicos no momento da compilação para gerar os wrappers que podem ser chamados pelo Android necessários.

## <a name="limited-java-generation-support"></a>Suporte à geração de Java limitado

Os [wrappers](~/android/platform/java-integration/android-callable-wrappers.md) que podem ser chamados pelo Android precisam ser gerados para que o código Java chame código gerenciado. *Por padrão, os*wrappers que podem ser chamados pelo Android conterão apenas (determinados) construtores declarados e métodos que substituem um método Java Virtual (ou seja, ele tem [`RegisterAttribute`](xref:Android.Runtime.RegisterAttribute)) ou implementam um método de interface java (a interface da mesma forma tem `Attribute`).
  
Antes da versão 4,1, nenhum método adicional poderia ser declarado. Com a versão 4,1, [os atributos personalizados `Export` e `ExportField` podem ser usados para declarar os métodos e os campos do Java no wrapper que pode ser chamado pelo Android](~/android/platform/java-integration/working-with-jni.md).

### <a name="missing-constructors"></a>Construtores ausentes

Os construtores permanecem complicados, a menos que [`ExportAttribute`](xref:Java.Interop.ExportAttribute) seja usado. O algoritmo para gerar construtores de wrapper callable Android é que um Construtor Java será emitido se:

1. Há um mapeamento de Java para todos os tipos de parâmetro
2. A classe base declara o mesmo Construtor &ndash; isso é necessário porque o wrapper que pode ser chamado pelo Android *deve* invocar o construtor de classe base correspondente; nenhum argumento padrão pode ser usado (já que não há uma maneira fácil de determinar quais valores devem ser usados em Java).

Por exemplo, considere a seguinte classe:

```csharp
[Service]
class MyIntentService : IntentService {
    public MyIntentService (): base ("value")
    {
    }
}
```

Embora isso pareça perfeitamente lógico, o wrapper do Android callable *no Builds* não conterá um construtor padrão. Consequentemente, se você tentar iniciar esse serviço (por exemplo, [`Context.StartService`](xref:Android.Content.Context.StartService*), ele falhará:

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

A solução alternativa é declarar um construtor padrão, adorna-lo com a `ExportAttribute`e definir o [`ExportAttribute.SuperStringArgument`](xref:Java.Interop.ExportAttribute.SuperArgumentsString): 

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

### <a name="generic-c-classes"></a>Classes C# genéricas

As C# classes genéricas são apenas parcialmente suportadas. As seguintes limitações existem:

- Tipos genéricos não podem usar `[Export]` ou `[ExportField`]. Tentar fazer isso irá gerar um erro de `XA4207`.

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

- Métodos genéricos não podem usar `[Export]` ou `[ExportField]`:

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

- `[ExportField]` não pode ser usado em métodos que retornam `void`:

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

- Instâncias de tipos genéricos _não devem_ ser criadas a partir do código Java.
    Eles só podem ser criados com segurança a partir de código gerenciado:

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

## <a name="partial-java-generics-support"></a>Suporte a Java genéricos parcial

O suporte à vinculação de genéricos Java é limitado. Particularmente, os membros em uma classe de instância genérica que é derivada de outra classe genérica (não instanciada) são deixados expostos como Java. lang. Object. Por exemplo, o método [Android. Content. intuito. GetParcelableExtra](xref:Android.Content.Intent.GetParcelableExtra*) retorna Java. lang. Object. Isso ocorre devido a genéricos Java apagados.
Temos algumas classes que não aplicam essa limitação, mas elas são ajustadas manualmente.

## <a name="related-links"></a>Links relacionados

- [Callable Wrappers do Android](~/android/platform/java-integration/android-callable-wrappers.md)
- [Trabalhando com JNI](~/android/platform/java-integration/working-with-jni.md)
- [ExportAttribute](xref:Java.Interop.ExportAttribute)
- [Ampliação](xref:Java.Interop.ExportAttribute.SuperArgumentsString)
- [Registrattribute](xref:Android.Runtime.RegisterAttribute)
