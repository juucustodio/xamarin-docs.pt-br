---
title: Trabalhar com JNI
description: Xamarin. Android permite escrever aplicativos Android dentro do C# em vez de Java. Vários assemblies são fornecidos com o xamarin. Android que fornecem associações para bibliotecas de Java, incluindo Mono.Android.dll e Mono.Android.GoogleMaps.dll. No entanto, as associações não são fornecidas para cada biblioteca Java possíveis e as associações que são fornecidas não podem associar cada tipo de Java e membro. Para usar membros e tipos Java não associado, o Java JNI (Interface nativa) podem ser usados. Este artigo ilustra como usar JNI para interagir com tipos Java e os membros de aplicativos xamarin. Android.
ms.prod: xamarin
ms.assetid: A417DEE9-7B7B-4E35-A79C-284739E3838E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: aa2e2ac96b37bc781f2e4a3778ea0aaf970649ec
ms.sourcegitcommit: 58d8bbc19ead3eb535fb8248710d93ba0892e05d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67674614"
---
# <a name="working-with-jni"></a>Trabalhar com JNI

_Xamarin. Android permite escrever aplicativos Android dentro do C# em vez de Java. Vários assemblies são fornecidos com o xamarin. Android que fornecem associações para bibliotecas de Java, incluindo Mono.Android.dll e Mono.Android.GoogleMaps.dll. No entanto, as associações não são fornecidas para cada biblioteca Java possíveis e as associações que são fornecidas não podem associar cada tipo de Java e membro. Para usar membros e tipos Java não associado, o Java JNI (Interface nativa) podem ser usados. Este artigo ilustra como usar JNI para interagir com tipos Java e os membros de aplicativos xamarin. Android._


## <a name="overview"></a>Visão geral

Nem sempre é possível criar um Managed Callable Wrapper (MCW) para invocar código Java ou necessário. Em muitos casos, "embutido" JNI é perfeitamente aceitável e útil para uso individual de membros não associados de Java. Geralmente é mais simples usar JNI para invocar um método único em uma classe Java que ao gerar uma associação. jar inteira.

Xamarin. Android fornece as `Mono.Android.dll` assembly, que fornece uma associação para do Android `android.jar` biblioteca. Tipos e membros não apresentam dentro `Mono.Android.dll` e tipos não está presente no `android.jar` pode ser usado vinculando-os manualmente. Para associar os membros e tipos de Java, você deve usar o **Interface nativa Java** (**JNI**) para pesquisar tipos, ler e gravar os campos e invocar métodos.

A API de JNI no xamarin. Android é conceitualmente muito semelhante ao `System.Reflection` API no .NET: ele possibilita pesquisar tipos e membros por nome, ler e gravar valores de campo, invocar métodos e muito mais. Você pode usar JNI e o `Android.Runtime.RegisterAttribute` atributo personalizado para declarar métodos virtuais que podem ser vinculados para dar suporte à substituição. Você pode associar as interfaces para que eles podem ser implementados em C#.

Este documento explica:

-  Como JNI refere-se aos tipos.
-  Como pesquisar, ler e gravar os campos.
-  Como pesquisar e invocar métodos.
-  Como expor métodos virtuais para permitir a substituição de código gerenciado.
-  Como expor interfaces.



## <a name="requirements"></a>Requisitos

JNI, exposta por meio de [Android.Runtime.JNIEnv namespace](https://developer.xamarin.com/api/type/Android.Runtime.JNIEnv/), está disponível em todas as versões do xamarin. Android.
Para associar as interfaces e tipos de Java, você deve usar o xamarin. Android 4.0 ou posterior.


## <a name="managed-callable-wrappers"></a>Callable Wrappers gerenciados

Um **gerenciados Callable Wrapper** (**MCW**) é um *associação* para uma classe de Java ou interface que resume todos os o maquinário JNI assim que o cliente C# código não precisa ser se preocupe sobre a complexidade subjacente de JNI. A maioria dos `Mono.Android.dll` consiste de callable wrappers gerenciados.

Callable wrappers do gerenciado têm duas finalidades:

1.  Encapsule o uso JNI para que o código do cliente não precisa saber sobre a complexidade subjacente.
1.  Possibilitam na subclasse tipos Java e implementar interfaces Java.

A primeira finalidade é puramente para conveniência e encapsulamento de complexidade para que os consumidores têm um conjunto de classes para uso simple e gerenciado. Isso requer o uso dos vários [JNIEnv](https://developer.xamarin.com/api/type/Android.Runtime.JNIEnv/) os membros, conforme descrito posteriormente neste artigo. Tenha em mente que callable wrappers gerenciados não são estritamente necessárias &ndash; "embutido" usar JNI é perfeitamente aceitável e é útil para uso individual de membros não associados de Java. Implementação classing inferior e a interface exige o uso de callable wrappers gerenciados.



## <a name="android-callable-wrappers"></a>Callable Wrappers do Android

Callable wrappers do Android (ACW) são necessárias sempre que o tempo de execução do Android (arte) precisa chamar código gerenciado; Esses wrappers são necessárias porque não há nenhuma maneira de registrar as classes com arte no tempo de execução.
(Especificamente, o [DefineClass](http://docs.oracle.com/javase/6/docs/technotes/guides/jni/spec/functions.html#wp15986) não há suporte para a função JNI pelo tempo de execução do Android. Callable wrappers do Android, portanto, certifique-se a falta de suporte ao registro de tipo de tempo de execução.)

Sempre que precisa executar uma máquina virtual ou de método que é substituído ou implementado em código gerenciado de interface de código do Android, xamarin. Android deve fornecer um proxy de Java para que esse método é expedido para o tipo gerenciado apropriado. Esses tipos de proxy do Java são código Java que têm a classe base "mesmo" e a lista de interface de Java como o tipo gerenciado, os mesmos construtores de implementação e declarar qualquer classe base substituído e métodos de interface.

Callable wrappers do Android são gerados pelo **monodroid.exe** de programa durante a [processo de compilação](~/android/deploy-test/building-apps/build-process.md)e são gerados para todos os tipos que herdam (direta ou indiretamente) [ Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/).



### <a name="implementing-interfaces"></a>Implementando interfaces

Há momentos em que talvez você precise implementar uma interface Android, (como [Android.Content.IComponentCallbacks](https://developer.xamarin.com/api/type/Android.Content.IComponentCallbacks/)).

Todas as interfaces e classes Android estendem a [Android.Runtime.IJavaObject](https://developer.xamarin.com/api/type/Android.Runtime.IJavaObject/) interface; portanto, todos os tipos de Android devem implementar `IJavaObject`.
Xamarin. Android tira proveito desse fato &ndash; usa `IJavaObject` para fornecer o Android com um proxy de Java (um wrapper que pode ser chamado Android) para o tipo de dado gerenciado. Porque **monodroid.exe** procura apenas `Java.Lang.Object` subclasses (que deve implementar `IJavaObject`), criando subclasses `Java.Lang.Object` nos fornece uma maneira de implementar interfaces em código gerenciado. Por exemplo:

```csharp
class MyComponentCallbacks : Java.Lang.Object, Android.Content.IComponentCallbacks {
    public void OnConfigurationChanged (Android.Content.Res.Configuration newConfig) {
        // implementation goes here...
    }
    public void OnLowMemory () {
        // implementation goes here...
    }
}
```


### <a name="implementation-details"></a>Detalhes da implementação

*O restante deste artigo fornece detalhes de implementação, sujeito a alterações sem aviso prévio* (e é apresentada aqui apenas porque os desenvolvedores podem estar curiosos sobre o que está acontecendo nos bastidores).

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

public class HelloAndroid extends android.app.Activity {
    static final String __md_methods;
    static {
        __md_methods =
            "n_onCreate:(Landroid/os/Bundle;)V:GetOnCreate_Landroid_os_Bundle_Handler\n" +
            "";
        mono.android.Runtime.register (
                "Mono.Samples.HelloWorld.HelloAndroid, HelloWorld, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null",
                HelloAndroid.class,
                __md_methods);
    }

    public HelloAndroid ()
    {
        super ();
        if (getClass () == HelloAndroid.class)
            mono.android.TypeManager.Activate (
                "Mono.Samples.HelloWorld.HelloAndroid, HelloWorld, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null",
                "", this, new java.lang.Object[] { });
    }

    @Override
    public void onCreate (android.os.Bundle p0)
    {
        n_onCreate (p0);
    }

    private native void n_onCreate (android.os.Bundle p0);
}
```

Observe que a classe base é preservada e declarações de métodos nativos são fornecidas para cada método que é substituído no código gerenciado.



### <a name="exportattribute-and-exportfieldattribute"></a>ExportAttribute e ExportFieldAttribute

Normalmente, o xamarin. Android gera automaticamente o código Java que compõe o ACW; Essa geração baseia-se nos nomes de classe e método quando uma classe derivada de uma classe Java e substitui os métodos Java existentes. No entanto, em alguns cenários, a geração de código não é adequada, conforme descrito a seguir:

-   Android dá suporte a nomes de ação em atributos XML de layout, por exemplo o [android: onClick](https://developer.xamarin.com/api/member/Android.Views.View+IOnClickListener.OnClick/p/Android.Views.View/) atributo XML. Quando for especificado, a instância do modo de exibição inflada tenta pesquisar o método de Java.

-   O [java.io.Serializable](https://developer.android.com/reference/java/io/Serializable.html) interface requer `readObject` e `writeObject` métodos. Desde que eles não são membros dessa interface, nossa implementação gerenciada correspondente não expõe esses métodos para o código Java.

-   O [android.os.Parcelable](https://developer.xamarin.com/api/type/Android.Os.Parcelable/) interface espera que uma classe de implementação deve ter um campo estático `CREATOR` do tipo `Parcelable.Creator`. O código Java gerado requer algum campo explícito. Com nosso cenário padrão, não há nenhuma maneira de campo de saída no código Java a partir do código gerenciado.


Como a geração de código oferece uma solução para gerar métodos arbitrários de Java com nomes arbitrários, começando com o xamarin. Android 4.2, o [ExportAttribute](https://developer.xamarin.com/api/type/Java.Interop.ExportAttribute/) e [ExportFieldAttribute](https://developer.xamarin.com/api/type/Java.Interop.ExportFieldAttribute/) foram introduzido para oferecer uma solução para os cenários acima. Os dois atributos residem no `Java.Interop` namespace:

-   `ExportAttribute` &ndash; Especifica um nome de método e seus tipos de exceção esperada (para dar explícita "lança", em Java). Quando ele é usado em um método, o método será "Exportar" um método de Java que gera um código de expedição para a invocação de JNI correspondente para o método gerenciado. Isso pode ser usado com `android:onClick` e `java.io.Serializable`.

-   `ExportFieldAttribute` &ndash; Especifica um nome de campo. Ele reside em um método que funciona como um inicializador de campo. Isso pode ser usado com `android.os.Parcelable`.

O [ExportAttribute](https://developer.xamarin.com/samples/monodroid/ExportAttribute/) projeto de exemplo ilustra como usar esses atributos.


#### <a name="troubleshooting-exportattribute-and-exportfieldattribute"></a>Solução de problemas ExportAttribute e ExportFieldAttribute

-   Empacotamento falhar devido à ausência **Mono.Android.Export.dll** &ndash; se você usou `ExportAttribute` ou `ExportFieldAttribute` alguns métodos no seu código ou as bibliotecas dependentes, você precisa adicionar  **Mono.Android.Export.dll**. Esse assembly é isolado para dar suporte ao código de retorno de chamada do Java. Ela é separada da **Mono.Android.dll** enquanto adiciona adicionais de tamanho para o aplicativo.

-   No build de versão `MissingMethodException` ocorre para os métodos de exportação &ndash; na versão de compilação, `MissingMethodException` ocorre para os métodos de exportação. (Esse problema é corrigido na versão mais recente do xamarin. Android).



### <a name="exportparameterattribute"></a>ExportParameterAttribute

`ExportAttribute` e `ExportFieldAttribute` fornecem funcionalidade que pode usar o código de tempo de execução de Java. Esse código de tempo de execução acessa o código gerenciado por meio dos métodos JNI gerados orientados por esses atributos. Como resultado, não há nenhum método Java existente que associa o método gerenciado; Portanto, o método de Java é gerado a partir de uma assinatura do método gerenciado.

No entanto, neste caso, não é totalmente determinante. Notavelmente, isso é verdadeiro em algumas avançados mapeamentos entre tipos gerenciados e tipos de Java, como:

-  InputStream
-  OutputStream
-  XmlPullParser
-  XmlResourceParser

Quando os tipos, como eles são necessários para os métodos exportados, o `ExportParameterAttribute` deve ser usado para fornecer o parâmetro correspondente ou retornar um tipo de valor explicitamente.



### <a name="annotation-attribute"></a>Atributo de anotação

No xamarin. Android 4.2, convertemos `IAnnotation` tipos de implementação em atributos (System. Attribute) e suporte adicionado para geração de anotação wrappers de Java.

Isso significa que as seguintes alterações direcionais:

-   Gera o gerador de associação `Java.Lang.DeprecatedAttribute` partir `java.Lang.Deprecated` (enquanto ele deve ser `[Obsolete]` em código gerenciado).

-   Isso não significa que `Java.Lang.Deprecated` classe desaparecerá. Esses objetos baseados em Java ainda serviria como objetos usual de Java (se existir tal uso). Haverá `Deprecated` e `DeprecatedAttribute` classes.

-   O `Java.Lang.DeprecatedAttribute` classe é marcada como `[Annotation]` . Quando há um atributo personalizado que é herdado desta `[Annotation]` atributo, tarefa do msbuild irá gerar uma anotação de Java para o atributo personalizado (@Deprecated) no Wrapper que pode ser chamado Android (ACW).

-   Anotações pudesse ser geradas em classes, métodos e exportado campos (que é um método em código gerenciado).

Se a classe recipiente (a própria classe anotada, ou a classe que contém os membros anotados) não estiver registrada, toda a origem de classe Java não é gerada, incluindo anotações. Para métodos, você pode especificar o `ExportAttribute` para obter o método gerado explicitamente e anotado. Além disso, ele não é um recurso para "Gerar" uma definição de classe de anotação de Java. Em outras palavras, se você definir um atributo personalizado gerenciado para um determinado tipo de anotação, você precisará adicionar outra biblioteca. jar que contém a classe de anotação correspondente do Java. Adicionando um arquivo de código-fonte Java que define o tipo de anotação não é suficiente. O compilador Java não funciona da mesma forma que **apt**.

Além das seguintes limitações se aplicam:

-   Esse processo de conversão não considera `@Target` anotação até o momento em que o tipo de anotação.

-   Atributos em uma propriedade não funciona. Use atributos para a propriedade getter ou setter.



## <a name="class-binding"></a>Classe de associação

Uma classe de associação significa gravar um invólucro gerenciado que pode ser chamado para simplificar a invocação do tipo Java subjacente.

Associando métodos abstratos e virtuais para permitir a substituição de C# requer o xamarin. Android 4.0. No entanto, qualquer versão do xamarin. Android pode vincular métodos não virtuais, métodos estáticos ou métodos virtuais sem suporte a substituições.

Uma associação normalmente contém os seguintes itens:

-  Um [JNI identificador para o tipo de Java que está sendo associado](#_Looking_up_Java_Types).

-  [Campo de JNI IDs e as propriedades para cada campo associado](#_Instance_Fields).

-  [IDs de método JNI e métodos para cada um associado método](#_Instance_Methods).

-  Se for necessário classing inferior, o tipo precisa ter uma [RegisterAttribute](https://developer.xamarin.com/api/type/Android.Runtime.RegisterAttribute/) atributo personalizado na declaração de tipo com [RegisterAttribute.DoNotGenerateAcw](https://developer.xamarin.com/api/property/Android.Runtime.RegisterAttribute.DoNotGenerateAcw/) definido como `true`.



### <a name="declaring-type-handle"></a>Identificador de tipo de declaração

Os métodos de pesquisa de campo e método exigem uma referência de objeto referindo-se ao seu tipo declarativo. Por convenção, isso é mantido em um `class_ref` campo:

```csharp
static IntPtr class_ref = JNIEnv.FindClass(CLASS);
```

Consulte a [referências de tipo de JNI](#_JNI_Type_References) seção para obter detalhes sobre o `CLASS` token.


### <a name="binding-fields"></a>Campos de associação

Campos de Java são expostos como C# propriedades, por exemplo, o campo de Java [java.lang.System.in](https://developer.android.com/reference/java/lang/System.html#in) está vinculado como o C# propriedade [Java.Lang.JavaSystem.In](https://developer.xamarin.com/api/property/Java.Lang.JavaSystem.In/).
Além disso, como JNI faz distinção entre os campos estáticos e campos de instância, diferentes métodos de ser usado ao implementar as propriedades.

Associação de campo envolve três conjuntos de métodos:

1.  O *obter a id do campo* método. O *obter a id do campo* método é responsável por retornar um campo de lidar com que o *obter valor do campo* e *definir valor do campo* usarão métodos. Obter a id de campo exige saber a declaração de tipo, o nome do campo e o [assinatura de tipo JNI](#JNI_Type_Signatures) do campo.

1.  O *obter o valor do campo* métodos. Esses métodos exigem o identificador de campo e serão responsáveis por ler o valor do campo de Java.
    O método usado depende do tipo do campo.

1.  O *definir o valor do campo* métodos. Esses métodos exigem o identificador de campo e são responsáveis por gravar o valor do campo dentro do Java. O método usado depende do tipo do campo.


 [Campos estáticos](#_Static_Fields) usar o [JNIEnv.GetStaticFieldID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticMethodID/), `JNIEnv.GetStatic*Field`, e [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/) métodos.

 [Campos de instância](#_Instance_Fields) usar o [JNIEnv.GetFieldID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetFieldID/), `JNIEnv.Get*Field`, e [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/) métodos.

Por exemplo, a propriedade estática `JavaSystem.In` podem ser implementados como:

```csharp
static IntPtr in_jfieldID;
public static System.IO.Stream In
{
    get {
        if (in_jfieldId == IntPtr.Zero)
            in_jfieldId = JNIEnv.GetStaticFieldID (class_ref, "in", "Ljava/io/InputStream;");
        IntPtr __ret = JNIEnv.GetStaticObjectField (class_ref, in_jfieldId);
        return InputStreamInvoker.FromJniHandle (__ret, JniHandleOwnership.TransferLocalRef);
    }
}
```

Observação: Estamos usando [InputStreamInvoker.FromJniHandle](https://developer.xamarin.com/api/member/Android.Runtime.InputStreamInvoker.FromJniHandle/(System.IntPtr%2cAndroid.Runtime.JniHandleOwnership)) para converter a referência JNI em um `System.IO.Stream` estiver usando a instância e podemos `JniHandleOwnership.TransferLocalRef` porque [JNIEnv.GetStaticObjectField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticObjectField/) retorna um referência de local.

Muitas da [Android.Runtime](https://developer.xamarin.com/api/namespace/Android.Runtime/) tipos têm `FromJniHandle` métodos que converterão um JNI de referência para o tipo desejado.



### <a name="method-binding"></a>Associação de método

Métodos Java são expostos como C# métodos e como C# propriedades. Por exemplo, o método Java [java.lang.Runtime.runFinalizersOnExit](https://developer.android.com/reference/java/lang/Runtime.html#runFinalizersOnExit(boolean)) método está vinculado como o [Java.Lang.Runtime.RunFinalizersOnExit](https://developer.xamarin.com/api/member/Java.Lang.Runtime.RunFinalizersOnExit/) método e o [java.lang.Object.getClass ](https://developer.android.com/reference/java/lang/Object.html#getClass) método está vinculado como o [Java.Lang.Object.Class](https://developer.xamarin.com/api/property/Java.Lang.Object.Class/) propriedade.

Invocação de método é um processo em duas etapas:

1.  O *obter a id do método* para o método a ser invocado. O *obter a id do método* método é responsável por retornar um identificador de método que usarão os métodos de invocação de método. Como obter o identificador de método exige saber a declaração de tipo, o nome do método e o [assinatura de tipo JNI](#JNI_Type_Signatures) do método.

1.  Invoque o método.

Assim como ocorre com campos, os métodos a serem usados para obter o identificador de método e invocar o método diferem entre os métodos de instância e métodos estáticos.

[Métodos estáticos](#_Static_Methods_1) usar [JNIEnv.GetStaticMethodID()](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticMethodID/) para procurar a id do método e, em seguida, usar o `JNIEnv.CallStatic*Method` família de métodos de invocação.

[Métodos de instância](#_Instance_Methods) usar [JNIEnv.GetMethodID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetMethodID/) para procurar a id do método e usar os `JNIEnv.Call*Method` e `JNIEnv.CallNonvirtual*Method` famílias dos métodos de invocação.

A associação do método é potencialmente mais do que apenas a invocação de método. Associação de método também inclui permitindo que um método a ser substituído (para os métodos abstratos e não-final) ou implementado (para métodos de interface). O [que dão suporte a herança, Interfaces](#_Supporting_Inheritance,_Interfaces_1) seção aborda as complexidades de dar suporte a métodos virtuais e os métodos de interface.

<a name="_Static_Methods_1" />

#### <a name="static-methods"></a>Métodos estáticos

Um método estático de associação envolve o uso `JNIEnv.GetStaticMethodID` para obter um identificador de método, em seguida, usando o apropriado `JNIEnv.CallStatic*Method` método, dependendo do tipo de retorno do método. A seguir está um exemplo de uma associação para o [Runtime.getRuntime](https://developer.android.com/reference/java/lang/Runtime.html#getRuntime()) método:

```csharp
static IntPtr id_getRuntime;

[Register ("getRuntime", "()Ljava/lang/Runtime;", "")]
public static Java.Lang.Runtime GetRuntime ()
{
    if (id_getRuntime == IntPtr.Zero)
        id_getRuntime = JNIEnv.GetStaticMethodID (class_ref,
                "getRuntime", "()Ljava/lang/Runtime;");

    return Java.Lang.Object.GetObject<Java.Lang.Runtime> (
            JNIEnv.CallStaticObjectMethod  (class_ref, id_getRuntime),
            JniHandleOwnership.TransferLocalRef);
}
```

Observe que podemos armazenar o identificador do método em um campo estático, `id_getRuntime`. Isso é uma otimização de desempenho, para que o identificador do método não precisa ser pesquisados em cada invocação. Não é necessário para armazenar em cache o identificador do método dessa maneira. Depois de obter o identificador do método, [JNIEnv.CallStaticObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticObjectMethod/) é usado para invocar o método. `JNIEnv.CallStaticObjectMethod` Retorna um `IntPtr` que contém o identificador da instância Java retornada.
[Java.Lang.Object.GetObject&lt;T&gt;(IntPtr, JniHandleOwnership)](https://developer.xamarin.com/api/member/Java.Lang.Object.GetObject%7BT%7D/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) é usada para converter o identificador de Java em uma instância de objeto com rigidez de tipos.



#### <a name="non-virtual-instance-method-binding"></a>Associação de método de instância não virtual

Associação de um `final` método de instância ou um método de instância que não requer a substituição, envolve o uso `JNIEnv.GetMethodID` para obter um identificador de método, em seguida, usando o apropriado `JNIEnv.Call*Method` método, dependendo do tipo de retorno do método. A seguir está um exemplo de uma associação para o `Object.Class` propriedade:

```csharp
static IntPtr id_getClass;
public Java.Lang.Class Class {
    get {
        if (id_getClass == IntPtr.Zero)
            id_getClass = JNIEnv.GetMethodID (class_ref, "getClass", "()Ljava/lang/Class;");
        return Java.Lang.Object.GetObject<Java.Lang.Class> (
                JNIEnv.CallObjectMethod (Handle, id_getClass),
                JniHandleOwnership.TransferLocalRef);
    }
}
```

Observe que podemos armazenar o identificador do método em um campo estático, `id_getClass`.
Isso é uma otimização de desempenho, para que o identificador do método não precisa ser pesquisados em cada invocação. Não é necessário para armazenar em cache o identificador do método dessa maneira. Depois de obter o identificador do método, [JNIEnv.CallStaticObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticObjectMethod/) é usado para invocar o método. `JNIEnv.CallStaticObjectMethod` Retorna um `IntPtr` que contém o identificador da instância Java retornada.
[Java.Lang.Object.GetObject&lt;T&gt;(IntPtr, JniHandleOwnership)](https://developer.xamarin.com/api/member/Java.Lang.Object.GetObject%7BT%7D/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) é usada para converter o identificador de Java em uma instância de objeto com rigidez de tipos.


### <a name="binding-constructors"></a>Construtores de associação

Construtores são métodos Java com o nome `"<init>"`. Assim como ocorre com métodos de instância de Java, `JNIEnv.GetMethodID` é usado para pesquisar o identificador de construtor. Ao contrário dos métodos de Java, o [JNIEnv.NewObject](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.NewObject/) métodos são usados para invocar o identificador do método de construtor. O valor de retorno `JNIEnv.NewObject` é uma referência de local de JNI:


```csharp
int value = 42;
IntPtr class_ref    = JNIEnv.FindClass ("java/lang/Integer");
IntPtr id_ctor_I    = JNIEnv.GetMethodID (class_ref, "<init>", "(I)V");
IntPtr lrefInstance = JNIEnv.NewObject (class_ref, id_ctor_I, new JValue (value));
// Dispose of lrefInstance, class_ref…
```

Normalmente uma associação de classe será subclasse [Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/).
Quando subclasses `Java.Lang.Object`, um adicionais semântico entra: uma `Java.Lang.Object` instância mantém uma referência global a uma instância de Java por meio de `Java.Lang.Object.Handle` propriedade.

1.  O `Java.Lang.Object` construtor padrão alocará uma instância de Java.

1.  Se o tipo tem um `RegisterAttribute` , e `RegisterAttribute.DoNotGenerateAcw` é `true` , em seguida, uma instância da `RegisterAttribute.Name` tipo é criado por meio de seu construtor padrão.

1.  Caso contrário, o [Callable Wrapper do Android](~/android/platform/java-integration/android-callable-wrappers.md) (ACW) correspondente ao `this.GetType` é instanciado via seu construtor padrão. Callable Wrappers do Android são gerados durante a criação de pacote para cada `Java.Lang.Object` subclasse para o qual `RegisterAttribute.DoNotGenerateAcw` não está definido como `true`.

Para associações de tipos que são não classe, esse é o esperado semântica: Criando uma instância de um `Mono.Samples.HelloWorld.HelloAndroid` C# instância deve construir um Java `mono.samples.helloworld.HelloAndroid` instância que é um Android Callable Wrapper gerado.

Para associações de classe, isso pode ser o comportamento correto, se o tipo de Java contém um construtor padrão e/ou nenhum outro construtor precisa ser invocado. Caso contrário, deve ser fornecido um construtor que executa as seguintes ações:

1.  Invocar o [Java.Lang.Object (IntPtr, JniHandleOwnership)](https://developer.xamarin.com/api/constructor/Java.Lang.Object.Object/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) em vez do padrão `Java.Lang.Object` construtor. Isso é necessário para evitar a criação de uma nova instância de Java.

1.  Verifique o valor da [Java.Lang.Object.Handle](https://developer.xamarin.com/api/property/Java.Lang.Object.Handle/) antes de criar todas as instâncias Java. O `Object.Handle` propriedade terá um valor diferente de `IntPtr.Zero` se um Android Callable Wrapper foi construído no código Java e a associação de classe está sendo construída para conter a instância criada do Android Callable Wrapper. Por exemplo, quando o Android cria uma `mono.samples.helloworld.HelloAndroid` instância, o Android Callable Wrapper será criado primeiro e o Java `HelloAndroid` construtor criará uma instância do correspondente `Mono.Samples.HelloWorld.HelloAndroid` tipo, com o `Object.Handle` sendo de propriedade definido como a instância de Java antes da execução do construtor.

1.  Se o tipo de tempo de execução atual não é o mesmo que a declaração de tipo e, em seguida, uma instância do Wrapper que pode ser chamado Android correspondente deve ser criado e use [Object.SetHandle](https://developer.xamarin.com/api/member/Java.Lang.Object.SetHandle/(System.IntPtr%2cAndroid.Runtime.JniHandleOwnership)) para armazenar o identificador retornado por [ JNIEnv.CreateInstance](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CreateInstance/).

1.  Se o tipo de tempo de execução atual é o mesmo que o tipo de declaração, em seguida, invocar o construtor de Java e use [Object.SetHandle](https://developer.xamarin.com/api/member/Java.Lang.Object.SetHandle/(System.IntPtr%2cAndroid.Runtime.JniHandleOwnership)) para armazenar o identificador retornado por `JNIEnv.NewInstance` .


Por exemplo, considere a [java.lang.Integer(int)](https://developer.android.com/reference/java/lang/Integer.html#Integer(int)) construtor. Isso será associado como:

```csharp
// Cache the constructor's method handle for later use
static IntPtr id_ctor_I;

// Need [Register] for subclassing
// RegisterAttribute.Name is always ".ctor"
// RegisterAttribute.Signature is tye JNI type signature of constructor
// RegisterAttribute.Connector is ignored; use ""
[Register (".ctor", "(I)V", "")]
public Integer (int value)
    // 1. Prevent Object default constructor execution
    : base (IntPtr.Zero, JniHandleOwnership.DoNotTransfer)
{
    // 2. Don't allocate Java instance if already allocated
    if (Handle != IntPtr.Zero)
        return;

    // 3. Derived type? Create Android Callable Wrapper
    if (GetType () != typeof (Integer)) {
        SetHandle (
                Android.Runtime.JNIEnv.CreateInstance (GetType (), "(I)V", new JValue (value)),
                JniHandleOwnership.TransferLocalRef);
        return;
    }

    // 4. Declaring type: lookup &amp; cache method id...
    if (id_ctor_I == IntPtr.Zero)
        id_ctor_I = JNIEnv.GetMethodID (class_ref, "<init>", "(I)V");
    // ...then create the Java instance and store
    SetHandle (
            JNIEnv.NewObject (class_ref, id_ctor_I, new JValue (value)),
            JniHandleOwnership.TransferLocalRef);
}
```

O [JNIEnv.CreateInstance](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CreateInstance/) métodos são auxiliares para realizar uma `JNIEnv.FindClass`, `JNIEnv.GetMethodID`, `JNIEnv.NewObject`, e `JNIEnv.DeleteGlobalReference` no valor retornado de `JNIEnv.FindClass`. Consulte a próxima seção para obter detalhes.

<a name="_Supporting_Inheritance,_Interfaces_1" />

### <a name="supporting-inheritance-interfaces"></a>Suporte a herança, Interfaces

Subclasses de um tipo de Java ou implementar uma interface Java requer a geração de [Callable Wrappers do Android](~/android/platform/java-integration/android-callable-wrappers.md) (ACWs) que são gerados para cada `Java.Lang.Object` subclasse durante o processo de empacotamento. Geração de ACW é controlada por meio de [Android.Runtime.RegisterAttribute](https://developer.xamarin.com/api/type/Android.Runtime.RegisterAttribute/) atributo personalizado.

Para C# tipos, o `[Register]` construtor de atributo personalizado requer um argumento: o [JNI simplificado a referência de tipo](#_Simplified_Type_References_1) para Java correspondente de tipo. Isso permite fornecer nomes diferentes entre Java e C#.

Antes de xamarin. Android 4.0, o `[Register]` atributo personalizado não estava disponível para tipos de Java "alias" existentes. Isso ocorre porque o processo de geração de ACW geraria ACWs para cada `Java.Lang.Object` subclasse encontrado.

Xamarin. Android 4.0 introduzida o [RegisterAttribute.DoNotGenerateAcw](https://developer.xamarin.com/api/property/Android.Runtime.RegisterAttribute.DoNotGenerateAcw/) propriedade. Esta propriedade instrui o processo de geração ACW *ignorar* tipo anotado, permitindo que a declaração do novo Managed Callable Wrappers que não resultará em ACWs sendo gerados no momento da criação de pacote. Isso permite que tipos Java existentes de associação. Por exemplo, considere a seguinte classe de Java simple, `Adder`, que contém um método, `add`, que adiciona para números inteiros e retorna o resultado:

```java
package mono.android.test;
public class Adder {
    public int add (int a, int b) {
        return a + b;
    }
}
```

O `Adder` tipo pode ser associado como:

```csharp
[Register ("mono/android/test/Adder", DoNotGenerateAcw=true)]
public partial class Adder : Java.Lang.Object {
    static IntPtr class_ref = JNIEnv.FindClass ( "mono/android/test/Adder");

    public Adder ()
    {
    }

    public Adder (IntPtr handle, JniHandleOwnership transfer)
        : base (handle, transfer)
    {
    }
}
partial class ManagedAdder : Adder {
}
```

Aqui, o `Adder` C# tipo de *aliases* o `Adder` tipo de Java. O `[Register]` atributo é usado para especificar o nome JNI do `mono.android.test.Adder` tipo Java e o `DoNotGenerateAcw` propriedade é usada para inibir a geração de ACW. Isso resultará na geração de um ACW para o `ManagedAdder` de tipo, que corretamente subclasses de `mono.android.test.Adder` tipo. Se o `RegisterAttribute.DoNotGenerateAcw` propriedade não tivesse sido usada e, em seguida, o processo de build do xamarin. Android teria gerado um novo `mono.android.test.Adder` tipo Java. Isso resultaria em erros de compilação, como o `mono.android.test.Adder` tipo deve estar presente duas vezes, em dois arquivos separados.



### <a name="binding-virtual-methods"></a>Associando métodos virtuais

`ManagedAdder` as subclasses de Java `Adder` tipo, mas ele não é particularmente interessante: o C# `Adder` tipo não define quaisquer métodos virtuais, então `ManagedAdder` não é possível substituir qualquer coisa.

Associação `virtual` métodos para permitir a substituição por subclasses requer várias coisas que precisam ser feitas a que se enquadram em duas categorias a seguir:

1.  **Associação de método**

1.  **Método de registro**


#### <a name="method-binding"></a>Associação de método

Um método de vinculação requer a adição de dois membros de suporte para o C# `Adder` definição: `ThresholdType`, e `ThresholdClass`.

##### <a name="thresholdtype"></a>ThresholdType

O `ThresholdType` propriedade retorna o tipo atual da associação:

```csharp
partial class Adder {
    protected override System.Type ThresholdType {
        get {
            return typeof (Adder);
        }
    }
}
```

`ThresholdType` é usado em associação de método para determinar quando ele deve fazer expedição de método não virtual contra não virtual. Ele sempre deve retornar um `System.Type` instância que corresponde à declaração C# tipo.

##### <a name="thresholdclass"></a>ThresholdClass

O `ThresholdClass` propriedade retorna a referência de classe JNI para o tipo associado:

```csharp
partial class Adder {
    protected override IntPtr ThresholdClass {
        get {
            return class_ref;
        }
    }
}
```

`ThresholdClass` é usado em associação de método ao invocar métodos não virtuais.

#### <a name="binding-implementation"></a>Implementação da associação

A implementação de associação do método é responsável pela invocação de tempo de execução do método Java. Ele também contém um `[Register]` declaração de atributo personalizado que é parte do registro do método e será discutida na seção de registro do método:

```csharp
[Register ("add", "(II)I", "GetAddHandler")]
    public virtual int Add (int a, int b)
    {
        if (id_add == IntPtr.Zero)
            id_add = JNIEnv.GetMethodID (class_ref, "add", "(II)I");
        if (GetType () == ThresholdType)
            return JNIEnv.CallIntMethod (Handle, id_add, new JValue (a), new JValue (b));
        return JNIEnv.CallNonvirtualIntMethod (Handle, ThresholdClass, id_add, new JValue (a), new JValue (b));
    }
}
```

O `id_add` campo contém o identificador de método para o método de Java para invocar. O `id_add` valor é obtido de `JNIEnv.GetMethodID`, que requer a declaração de classe (`class_ref`), o nome do método Java (`"add"`) e a assinatura JNI do método (`"(II)I"`).

Depois de obter o identificador de método, `GetType` é comparado ao `ThresholdType` para determinar se a expedição virtual ou não virtual é necessária. Expedição virtual é necessário quando `GetType` corresponde à `ThresholdType`, como `Handle` podem se referir a uma subclasse alocada em Java, que substitui o método.

Quando `GetType` não corresponde à `ThresholdType`, `Adder` tenha sido derivado (por exemplo, `ManagedAdder`) e o `Adder.Add` implementação será invocada apenas se a subclasse chamado `base.Add`. Esse é o caso de despacho não virtual, que é onde `ThresholdClass` chega. `ThresholdClass` Especifica a classe de Java que fornecerá a implementação do método a ser invocado.



#### <a name="method-registration"></a>Método de registro

Vamos supor que temos atualizada `ManagedAdder` definição que substitui o `Adder.Add` método:

```csharp
partial class ManagedAdder : Adder {
    public override int Add (int a, int b) {
        return (a*2) + (b*2);
    }
}
```

Lembre-se de que `Adder.Add` tinha um `[Register]` atributo personalizado:

```csharp
[Register ("add", "(II)I", "GetAddHandler")]
```

O `[Register]` construtor de atributo personalizado aceita três valores:

1.  O nome do método Java, `"add"` nesse caso.

1.  A assinatura de tipo de JNI do método, `"(II)I"` nesse caso.

1.  O *método conector* , `GetAddHandler` nesse caso.
    Métodos de conector serão discutidos mais tarde.


Os dois primeiros parâmetros permitem que o processo de geração de ACW gerar uma declaração de método para substituir o método. O ACW resultante deverá conter alguns do código a seguir:

```csharp
public class ManagedAdder extends mono.android.test.Adder {
    static final String __md_methods;
    static {
        __md_methods = "n_add:(II)I:GetAddHandler\n" +
            "";
        mono.android.Runtime.register (...);
    }
    @Override
    public int add (int p0, int p1) {
        return n_add (p0, p1);
    }
    private native int n_add (int p0, int p1);
    // ...
}
```

Observe que um `@Override` método for declarado, que delega para um `n_`-prefixado método o mesmo nome. Isso certifique-se de que quando o código Java invoca `ManagedAdder.add`, `ManagedAdder.n_add` será invocado, que permitirá a substituição de C# `ManagedAdder.Add` método a ser executado.

Portanto, a pergunta mais importante: como é `ManagedAdder.n_add` associado ao `ManagedAdder.Add`?

Java `native` métodos são registrados com o tempo de execução do Java (o tempo de execução Android) por meio de [JNI RegisterNatives função](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html#wp17734).
`RegisterNatives` aceita uma matriz de estruturas que contém o nome do método Java, a assinatura de tipo de JNI e um ponteiro de função para invocar segue [JNI convenção de chamada](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/design.html#wp715).
O ponteiro de função deve ser uma função que usa dois argumentos de ponteiro, seguidos de parâmetros do método. O Java `ManagedAdder.n_add` método deve ser implementado por meio de uma função que tem o seguinte protótipo de C:

```csharp
int FunctionName(JNIEnv *env, jobject this, int a, int b)
```

Xamarin. Android não expõe um `RegisterNatives` método. Em vez disso, o ACW e o MCW juntos fornecem as informações necessárias para invocar `RegisterNatives`: o ACW contém o nome do método e a assinatura de tipo JNI, a única coisa que falta é um ponteiro de função para ligar.

É aí que o *método conector* chega. O terceiro `[Register]` parâmetro de atributo personalizado é o nome de um método definido no tipo registrado ou de uma classe base do tipo registrado que não aceita parâmetros e retorna um `System.Delegate`. Retornado `System.Delegate` por sua vez se refere a um método que tem a assinatura de função JNI correta. Por fim, o delegado que retorna o método de conector *deve* ter raiz para que o GC não coletá-los, como o delegado está sendo oferecido para Java.

```csharp
#pragma warning disable 0169
static Delegate cb_add;
// This method must match the third parameter of the [Register]
// custom attribute, must be static, must return System.Delegate,
// and must accept no parameters.
static Delegate GetAddHandler ()
{
    if (cb_add == null)
        cb_add = JNINativeWrapper.CreateDelegate ((Func<IntPtr, IntPtr, int, int, int>) n_Add);
    return cb_add;
}
// This method is registered with JNI.
static int n_Add (IntPtr jnienv, IntPtr lrefThis, int a, int b)
{
    Adder __this = Java.Lang.Object.GetObject<Adder>(lrefThis, JniHandleOwnership.DoNotTransfer);
    return __this.Add (a, b);
}
#pragma warning restore 0169
```

O `GetAddHandler` método cria uma `Func<IntPtr, IntPtr, int, int,
int>` delegado que se refere à `n_Add` método, em seguida, invoca [JNINativeWrapper.CreateDelegate](https://developer.xamarin.com/api/member/Android.Runtime.JNINativeWrapper.CreateDelegate/).
`JNINativeWrapper.CreateDelegate` encapsula o método fornecido em um bloco try/catch, para que qualquer exceção sem tratamento é tratadas e resultará em gerando o [AndroidEvent.UnhandledExceptionRaiser](https://developer.xamarin.com/api/event/Android.Runtime.AndroidEnvironment.UnhandledExceptionRaiser/) eventos. O delegado resultante é armazenado no estático `cb_add` variável, de modo que o GC não liberará o delegado.

Por fim, o `n_Add` método é responsável por marshaling dos parâmetros JNI para os tipos gerenciados correspondentes, em seguida, chame o método de delegação de.

Observação: Sempre use `JniHandleOwnership.DoNotTransfer` ao obter uma MCW ao longo de uma instância de Java. Tratá-las como uma referência local (e, portanto, chamando `JNIEnv.DeleteLocalRef`) será interrompido gerenciado –&gt; Java -&gt; managed stack transições.



### <a name="complete-adder-binding"></a>Associação da Adder completa

Gerenciada a completa de associação para o `mono.android.tests.Adder` é do tipo:

```csharp
[Register ("mono/android/test/Adder", DoNotGenerateAcw=true)]
public class Adder : Java.Lang.Object {

    static IntPtr class_ref = JNIEnv.FindClass ("mono/android/test/Adder");

    public Adder ()
    {
    }

    public Adder (IntPtr handle, JniHandleOwnership transfer)
        : base (handle, transfer)
    {
    }

    protected override Type ThresholdType {
        get {return typeof (Adder);}
    }

    protected override IntPtr ThresholdClass {
        get {return class_ref;}
    }

#region Add
    static IntPtr id_add;

    [Register ("add", "(II)I", "GetAddHandler")]
    public virtual int Add (int a, int b)
    {
        if (id_add == IntPtr.Zero)
            id_add = JNIEnv.GetMethodID (class_ref, "add", "(II)I");
        if (GetType () == ThresholdType)
            return JNIEnv.CallIntMethod (Handle, id_add, new JValue (a), new JValue (b));
        return JNIEnv.CallNonvirtualIntMethod (Handle, ThresholdClass, id_add, new JValue (a), new JValue (b));
    }

#pragma warning disable 0169
    static Delegate cb_add;
    static Delegate GetAddHandler ()
    {
        if (cb_add == null)
            cb_add = JNINativeWrapper.CreateDelegate ((Func<IntPtr, IntPtr, int, int, int>) n_Add);
        return cb_add;
    }

    static int n_Add (IntPtr jnienv, IntPtr lrefThis, int a, int b)
    {
        Adder __this = Java.Lang.Object.GetObject<Adder>(lrefThis, JniHandleOwnership.DoNotTransfer);
        return __this.Add (a, b);
    }
#pragma warning restore 0169
#endregion
}
```



### <a name="restrictions"></a>Restrições

Ao escrever um tipo que corresponde a critérios a seguir:

1.  Subclasses `Java.Lang.Object`

1.  Tem um `[Register]` atributo personalizado

1.  `RegisterAttribute.DoNotGenerateAcw` é `true`


Em seguida, para o tipo de interação de GC *não deve* ter todos os campos que podem se referir a um `Java.Lang.Object` ou `Java.Lang.Object` subclasse em tempo de execução. Por exemplo, os campos do tipo `System.Object` e qualquer tipo de interface não são permitidos. Tipos que não podem referenciar `Java.Lang.Object` instâncias são permitidas, tais como `System.String` e `List<int>`. Essa restrição é impedir que a coleção de objetos prematuro pelo GC.

Se o tipo deve conter um campo de instância pode se referir a um `Java.Lang.Object` da instância, em seguida, o tipo de campo deve ser `System.WeakReference` ou `GCHandle`.



## <a name="binding-abstract-methods"></a>Métodos abstratos de associação

Associação `abstract` métodos é basicamente idêntico à associação de métodos virtuais. Há somente duas diferenças:

1.  O método abstrato é abstrato. Ela ainda mantém o `[Register]` atributo e o registro do método associado, apenas o método de associação é movido para o `Invoker` tipo.

1.  Um não - `abstract` `Invoker` tipo é criado que herda do tipo abstrato. O `Invoker` tipo deve substituir abstrato de todos os métodos declarados na classe base e a implementação substituída é a implementação do método de associação, embora o caso de despacho não virtual pode ser ignorado.


Por exemplo, suponha que os itens acima `mono.android.test.Adder.add` método foram `abstract`. O C# associação seria alterado para que `Adder.Add` foram abstrata e um novo `AdderInvoker` tipo seria definido que implementados `Adder.Add`:

```csharp
partial class Adder {
    [Register ("add", "(II)I", "GetAddHandler")]
    public abstract int Add (int a, int b);

    // The Method Registration machinery is identical to the
    // virtual method case...
}

partial class AdderInvoker : Adder {
    public AdderInvoker (IntPtr handle, JniHandleOwnership transfer)
        : base (handle, transfer)
    {
    }

    static IntPtr id_add;
    public override int Add (int a, int b)
    {
        if (id_add == IntPtr.Zero)
            id_add = JNIEnv.GetMethodID (class_ref, "add", "(II)I");
        return JNIEnv.CallIntMethod (Handle, id_add, new JValue (a), new JValue (b));
    }
}
```

O `Invoker` tipo só é necessário para obter referências JNI para instâncias criadas para Java.


## <a name="binding-interfaces"></a>Interfaces de associação

Interfaces de associação é conceitualmente semelhante à associação de classes que contêm métodos virtuais, mas muitas das especificações diferem de maneiras sutis (e não tão sutis). Considere o seguinte [declaração de interface Java](https://github.com/xamarin/monodroid-samples/blob/master/SanityTests/Adder.java#L14):

```csharp
public interface Progress {
    void onAdd(int[] values, int currentIndex, int currentSum);
}
```

Ligações de interface têm duas partes: o C# definição de interface e uma definição do chamador para a interface.



### <a name="interface-definition"></a>Definição de interface

O C# definição de interface deve atender os requisitos a seguir:

-   A definição de interface deve ter um `[Register]` atributo personalizado.

-   A definição de interface deve estender o `IJavaObject interface`.
    Se isso não impedirá que ACWs herdem a interface Java.

-   Cada método de interface deve conter um `[Register]` atributo especificando o nome do método Java correspondente, a assinatura JNI e o método de conector.

-   O método do conector também deve especificar o tipo que o método do conector pode estar localizado em.

Ao associar `abstract` e `virtual` métodos, o método de conector serão pesquisados dentro da hierarquia de herança do tipo que está sendo registrado. As interfaces não podem ter nenhum método que contém os corpos, portanto, isso não funcionar, portanto, o requisito de que ser especificado um tipo que indica onde se encontra o método de conector. O tipo é especificado na cadeia de caracteres de método conector, depois de dois-pontos `':'`, e deve ser o nome de tipo qualificado do assembly do tipo que contém o chamador.

Declarações de método de interface são o método Java correspondente usando uma tradução *compatíveis* tipos. Para tipos de builtin Java, os tipos compatíveis são correspondentes C# tipos de, por exemplo, Java `int` é C# `int`. Para tipos de referência, o tipo compatível é um tipo que pode fornecer um identificador JNI do tipo Java apropriado.

Os membros de interface não serão invocados diretamente pelo Java &ndash; invocação será ser mediada por meio do tipo Invoker &ndash; para alguma quantidade de flexibilidade é permitida.

A interface de progresso do Java pode ser [declarado em C# como](https://github.com/xamarin/monodroid-samples/blob/master/SanityTests/ManagedAdder.cs#L83):

```csharp
[Register ("mono/android/test/Adder$Progress", DoNotGenerateAcw=true)]
public interface IAdderProgress : IJavaObject {
    [Register ("onAdd", "([III)V",
            "GetOnAddHandler:Mono.Samples.SanityTests.IAdderProgressInvoker, SanityTests, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null")]
    void OnAdd (JavaArray<int> values, int currentIndex, int currentSum);
}
```

Observe que no acima que podemos mapear Java `int[]` parâmetro para um [JavaArray&lt;int&gt;](https://developer.xamarin.com/api/type/Android.Runtime.JavaArray%601/).
Isso não é necessário: estamos pôde associado para um C# `int[]`, ou um `IList<int>`, ou algo mais inteiramente. Qualquer tipo for escolhido, o `Invoker` precisa ser capaz de convertê-lo em um Java `int[]` tipo de invocação.


### <a name="invoker-definition"></a>Definição do chamador

O `Invoker` definição de tipo deve herdar `Java.Lang.Object`, implementar a interface apropriada e fornecer todos os métodos de conexão que referenciada na definição de interface. Não há mais uma sugestão que difere de uma associação de classe: o `class_ref` IDs de campo e método devem ser membros de instância, os membros não estáticos.

O motivo para preferindo membros de instância tem a ver com `JNIEnv.GetMethodID` comportamento em tempo de execução do Android. (Isso pode ser também o comportamento do Java; ele ainda não foi testado). `JNIEnv.GetMethodID` retorna null ao procurar por um método que vem de uma interface implementada e não na interface declarada. Considere a [java.util.SortedMap&lt;K, V&gt; ](https://developer.android.com/reference/java/util/SortedMap.html) interface Java, que implementa o [java.util.Map&lt;K, V&gt; ](https://developer.android.com/reference/java/util/Map.html) interface. O mapa fornece uma [desmarque](https://developer.android.com/reference/java/util/Map.html#clear()) método, portanto, um aparentemente razoável `Invoker` definição para SortedMap seria:

```csharp
// Fails at runtime. DO NOT FOLLOW
partial class ISortedMapInvoker : Java.Lang.Object, ISortedMap {
    static IntPtr class_ref = JNIEnv.FindClass ("java/util/SortedMap");
    static IntPtr id_clear;
    public void Clear()
    {
        if (id_clear == IntPtr.Zero)
            id_clear = JNIEnv.GetMethodID(class_ref, "clear", "()V");
        JNIEnv.CallVoidMethod(Handle, id_clear);
    }
     // ...
}
```

Acima falhará porque `JNIEnv.GetMethodID` retornará `null` ao procurar o `Map.clear` método por meio de `SortedMap` instância da classe.

Há duas soluções para isso: acompanhar a interface que cada método é proveniente e ter um `class_ref` para cada interface, ou manter tudo como membros de instância e executar a pesquisa de método do tipo de classe mais derivada, não o tipo de interface. O último é feito na **Mono.Android.dll**.

A definição do invocador tem seis seções: o construtor, o `Dispose` método, o `ThresholdType` e `ThresholdClass` membros, o `GetObject` método, a implementação do método de interface e a implementação do método de conector.



#### <a name="constructor"></a>Construtor

O construtor precisa pesquisar a classe de tempo de execução da instância que está sendo invocada e armazenar a classe de tempo de execução na instância `class_ref` campo:

```csharp
partial class IAdderProgressInvoker {
    IntPtr class_ref;
    public IAdderProgressInvoker (IntPtr handle, JniHandleOwnership transfer)
        : base (handle, transfer)
    {
        IntPtr lref = JNIEnv.GetObjectClass (Handle);
        class_ref   = JNIEnv.NewGlobalRef (lref);
        JNIEnv.DeleteLocalRef (lref);
    }
}
```

Observação: O `Handle` propriedade deve ser usada dentro do corpo de construtor e não o `handle` parâmetro, como no Android v4.0 o `handle` parâmetro pode ser inválido após o construtor base termina a execução.


#### <a name="dispose-method"></a>Método Dispose

O `Dispose` método precisa liberar a referência global alocada no construtor:

```csharp
partial class IAdderProgressInvoker {
    protected override void Dispose (bool disposing)
    {
        if (this.class_ref != IntPtr.Zero)
            JNIEnv.DeleteGlobalRef (this.class_ref);
        this.class_ref = IntPtr.Zero;
        base.Dispose (disposing);
    }
}
```


#### <a name="thresholdtype-and-thresholdclass"></a>ThresholdType e ThresholdClass

O `ThresholdType` e `ThresholdClass` membros são idênticos para o que é encontrado em uma associação de classe:

```csharp
partial class IAdderProgressInvoker {
    protected override Type ThresholdType {
        get {
            return typeof (IAdderProgressInvoker);
        }
    }
    protected override IntPtr ThresholdClass {
        get {
            return class_ref;
        }
    }
}
```


#### <a name="getobject-method"></a>Método GetObject

Um estático `GetObject` método é necessário para suportar [Extensions.JavaCast&lt;T&gt;()](https://developer.xamarin.com/api/member/Android.Runtime.Extensions.JavaCast%7BTResult%7D/p/Android.Runtime.IJavaObject/):

```csharp
partial class IAdderProgressInvoker {
    public static IAdderProgress GetObject (IntPtr handle, JniHandleOwnership transfer)
    {
        return new IAdderProgressInvoker (handle, transfer);
    }
}
```


#### <a name="interface-methods"></a>Métodos de interface

Cada método da interface precisa ter uma implementação, que invoca o método Java correspondente por meio de JNI:

```csharp
partial class IAdderProgressInvoker {
    IntPtr id_onAdd;
    public void OnAdd (JavaArray<int> values, int currentIndex, int currentSum)
    {
        if (id_onAdd == IntPtr.Zero)
            id_onAdd = JNIEnv.GetMethodID (class_ref, "onAdd", "([III)V");
        JNIEnv.CallVoidMethod (Handle, id_onAdd, new JValue (JNIEnv.ToJniHandle (values)), new JValue (currentIndex), new JValue (currentSum));
    }
}
```



#### <a name="connector-methods"></a>Métodos de conector

Os métodos de conector e a infraestrutura de suporte são responsáveis por marshaling dos parâmetros JNI apropriado C# tipos. O Java `int[]` parâmetro será passado como um JNI `jintArray`, que é um `IntPtr` em C#. O `IntPtr` deve ser empacotado para uma `JavaArray<int>` para dar suporte à invocação de C# interface:

```csharp
partial class IAdderProgressInvoker {
    static Delegate cb_onAdd;
    static Delegate GetOnAddHandler ()
    {
        if (cb_onAdd == null)
            cb_onAdd = JNINativeWrapper.CreateDelegate ((Action<IntPtr, IntPtr, IntPtr, int, int>) n_OnAdd);
        return cb_onAdd;
    }

    static void n_OnAdd (IntPtr jnienv, IntPtr lrefThis, IntPtr values, int currentIndex, int currentSum)
    {
        IAdderProgress __this = Java.Lang.Object.GetObject<IAdderProgress>(lrefThis, JniHandleOwnership.DoNotTransfer);
        using (var _values = new JavaArray<int>(values, JniHandleOwnership.DoNotTransfer)) {
            __this.OnAdd (_values, currentIndex, currentSum);
        }
    }
}
```

Se `int[]` seria preferível `JavaList<int>`, em seguida, [JNIEnv.GetArray()](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetArray/(System.IntPtr%2cAndroid.Runtime.JniHandleOwnership%2cSystem.Type)) poderia ser usado em vez disso:

```csharp
int[] _values = (int[]) JNIEnv.GetArray(values, JniHandleOwnership.DoNotTransfer, typeof (int));
```

No entanto, observe que `JNIEnv.GetArray` copia toda a matriz entre VMs, portanto, para matrizes grandes, isso pode resultar em muita pressão de GC adicionado.


### <a name="complete-invoker-definition"></a>Concluir a definição do chamador

O [concluir a definição de IAdderProgressInvoker](https://github.com/xamarin/monodroid-samples/blob/master/SanityTests/ManagedAdder.cs#L88):

```csharp
class IAdderProgressInvoker : Java.Lang.Object, IAdderProgress {

    IntPtr class_ref;

    public IAdderProgressInvoker (IntPtr handle, JniHandleOwnership transfer)
        : base (handle, transfer)
    {
        IntPtr lref = JNIEnv.GetObjectClass (Handle);
        class_ref = JNIEnv.NewGlobalRef (lref);
        JNIEnv.DeleteLocalRef (lref);
    }

    protected override void Dispose (bool disposing)
    {
        if (this.class_ref != IntPtr.Zero)
            JNIEnv.DeleteGlobalRef (this.class_ref);
        this.class_ref = IntPtr.Zero;
        base.Dispose (disposing);
    }

    protected override Type ThresholdType {
        get {return typeof (IAdderProgressInvoker);}
    }

    protected override IntPtr ThresholdClass {
        get {return class_ref;}
    }

    public static IAdderProgress GetObject (IntPtr handle, JniHandleOwnership transfer)
    {
        return new IAdderProgressInvoker (handle, transfer);
    }

#region OnAdd
    IntPtr id_onAdd;
    public void OnAdd (JavaArray<int> values, int currentIndex, int currentSum)
    {
        if (id_onAdd == IntPtr.Zero)
            id_onAdd = JNIEnv.GetMethodID (class_ref, "onAdd",
                    "([III)V");
        JNIEnv.CallVoidMethod (Handle, id_onAdd,
                new JValue (JNIEnv.ToJniHandle (values)),
                new JValue (currentIndex),
new JValue (currentSum));
    }

#pragma warning disable 0169
    static Delegate cb_onAdd;
    static Delegate GetOnAddHandler ()
    {
        if (cb_onAdd == null)
            cb_onAdd = JNINativeWrapper.CreateDelegate ((Action<IntPtr, IntPtr, IntPtr, int, int>) n_OnAdd);
        return cb_onAdd;
    }

    static void n_OnAdd (IntPtr jnienv, IntPtr lrefThis, IntPtr values, int currentIndex, int currentSum)
    {
        IAdderProgress __this = Java.Lang.Object.GetObject<IAdderProgress>(lrefThis, JniHandleOwnership.DoNotTransfer);
        using (var _values = new JavaArray<int>(values, JniHandleOwnership.DoNotTransfer)) {
            __this.OnAdd (_values, currentIndex, currentSum);
        }
    }
#pragma warning restore 0169
#endregion
}
```



## <a name="jni-object-references"></a>Referências de objeto JNI

Muitos métodos JNIEnv retornam *JNI* *referências de objeto*, que são semelhante às `GCHandle`s. JNI fornece três tipos diferentes de referências de objeto: referências locais, referências de globais e referências fracas e globais. Todos os três são representados como `System.IntPtr`, *mas* (de acordo com a seção tipos de função de JNI) nem todos os `IntPtr`s retornado de `JNIEnv` métodos são referências. Por exemplo, [JNIEnv.GetMethodID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetMethodID/) retorna um `IntPtr`, mas ele não retorna uma referência de objeto, ele retorna um `jmethodID`. Consulte a [documentação da função JNI](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html) para obter detalhes.

Referências locais são criadas pela *a maioria dos* métodos de criação de referência.
Android permite apenas um número limitado de referências locais existir em um determinado momento, geralmente 512. Referências locais podem ser excluídas por meio [JNIEnv.DeleteLocalRef](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.DeleteLocalRef/).
Ao contrário de JNI, nem todos fazer referência a métodos de JNIEnv quais referências de objeto de retorno retornam referências locais. [JNIEnv.FindClass](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.FindClass/) retorna um *global* referência. É altamente recomendável que você exclua referências locais mais rápido possível, possivelmente criando uma [Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/) em torno do objeto e especificando `JniHandleOwnership.TransferLocalRef` para o [Java.Lang.Object (IntPtr Identificador de transferência JniHandleOwnership)](https://developer.xamarin.com/api/constructor/Java.Lang.Object.Object/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) construtor.

Referências globais são criadas pelo [JNIEnv.NewGlobalRef](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.NewGlobalRef/) e [JNIEnv.FindClass](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.FindClass/).
Eles podem ser destruídos com [JNIEnv.DeleteGlobalRef](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.DeleteGlobalRef/).
Emuladores têm um limite de 2.000 referências pendentes globais, enquanto os dispositivos de hardware têm um limite de cerca de 52.000 referências globais.

Referências fracas e globais estão disponíveis somente no Android v2.2 (Froyo) e versões posteriores. Referências fracas e globais podem ser excluídas com [JNIEnv.DeleteWeakGlobalRef](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.DeleteWeakGlobalRef/(System.IntPtr)).


### <a name="dealing-with-jni-local-references"></a>Lidando com referências de Local de JNI

O [JNIEnv.GetObjectField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetObjectField/), [JNIEnv.GetStaticObjectField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticObjectField/), [JNIEnv.CallObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallObjectMethod/), [JNIEnv.CallNonvirtualObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualObjectMethod/)e [JNIEnv.CallStaticObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticObjectMethod/) métodos retornam um `IntPtr` que contém uma referência de local de JNI para um objeto de Java, ou `IntPtr.Zero` se Java retornado `null`. Devido ao número limitado de referências locais que podem estar pendentes no depois (512 entradas), é desejável para garantir que as referências são excluídos de forma oportuna. Há três maneiras referências locais podem ser tratadas: explicitamente excluí-los, criando uma `Java.Lang.Object` instância para mantê-las e usando `Java.Lang.Object.GetObject<T>()` para criar um wrapper gerenciado que pode ser chamado ao redor deles.



### <a name="explicitly-deleting-local-references"></a>Excluindo explicitamente referências locais

[JNIEnv.DeleteLocalRef](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.DeleteLocalRef/) é usado para excluir referências locais. Depois que a referência de local tiver sido excluída, ele não pode ser usado, portanto, tome cuidado para garantir que `JNIEnv.DeleteLocalRef` é a última coisa feita com a referência de local.

```csharp
IntPtr lref = JNIEnv.CallObjectMethod(instance, methodID);
try {
    // Do something with `lref`
}
finally {
    JNIEnv.DeleteLocalRef (lref);
}
```



### <a name="wrapping-with-javalangobject"></a>Encapsular com Java.Lang.Object

`Java.Lang.Object` Fornece um [Java.Lang.Object (identificador IntPtr, transferência de JniHandleOwnership)](https://developer.xamarin.com/api/constructor/Java.Lang.Object.Object/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) construtor que pode ser usada para encapsular uma referência de JNI existente. O [JniHandleOwnership](https://developer.xamarin.com/api/type/Android.Runtime.JniHandleOwnership/) parâmetro determina como o `IntPtr` parâmetro deve ser tratado:

-   [JniHandleOwnership.DoNotTransfer](https://developer.xamarin.com/api/field/Android.Runtime.JniHandleOwnership.DoNotTransfer/) &ndash; criado `Java.Lang.Object` instância criará uma nova referência global dos `handle` parâmetro, e `handle` é alterado.
    O chamador é responsável por liberar `handle` , se necessário.

-   [JniHandleOwnership.TransferLocalRef](https://developer.xamarin.com/api/field/Android.Runtime.JniHandleOwnership.TransferLocalRef/) &ndash; criado `Java.Lang.Object` instância criará uma nova referência global dos `handle` parâmetro, e `handle` é excluída com [JNIEnv.DeleteLocalRef ](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.DeleteLocalRef/) . O chamador não deve liberar `handle` e não deve usar `handle` depois que o construtor termina a execução.

-   [JniHandleOwnership.TransferGlobalRef](https://developer.xamarin.com/api/field/Android.Runtime.JniHandleOwnership.TransferLocalRef/) &ndash; criado `Java.Lang.Object` instância será assumir a propriedade do `handle` parâmetro. O chamador deve liberar não `handle` .


Uma vez que os métodos de invocação de método JNI retornam local refs, `JniHandleOwnership.TransferLocalRef` seriam usados normalmente:

```csharp
IntPtr lref = JNIEnv.CallObjectMethod(instance, methodID);
var value = new Java.Lang.Object (lref, JniHandleOwnership.TransferLocalRef);
```

A referência global criada não será liberada até que o `Java.Lang.Object` instância é coletado como lixo. Se você conseguir, descarte da instância irá liberar a referência global, acelerando as coletas de lixo:

```csharp
IntPtr lref = JNIEnv.CallObjectMethod(instance, methodID);
using (var value = new Java.Lang.Object (lref, JniHandleOwnership.TransferLocalRef)) {
    // use value ...
}
```



### <a name="using-javalangobjectgetobjectlttgt"></a>Using Java.Lang.Object.GetObject&lt;T&gt;()

`Java.Lang.Object` Fornece um [Java.Lang.Object.GetObject&lt;T&gt;(identificador IntPtr, transferência de JniHandleOwnership)](https://developer.xamarin.com/api/member/Java.Lang.Object.GetObject%7BT%7D/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) método que pode ser usado para criar um wrapper gerenciado que pode ser chamado do tipo especificado.

O tipo `T` deve atender aos seguintes requisitos:

1.  `T` deve ser um tipo de referência.

1.  `T` deve implementar o `IJavaObject` interface.

1.  Se `T` não é uma classe abstrata ou interface, em seguida, `T` deve fornecer um construtor com os tipos de parâmetro `(IntPtr,
    JniHandleOwnership)` .

1.  Se `T` é uma classe abstrata ou uma interface, lá *deve* ser um *invoker* disponíveis para `T` . Um chamador é um tipo não abstrato herdado `T` ou implementa `T` , e tem o mesmo nome que `T` com um sufixo do chamador. Por exemplo, se T é a interface `Java.Lang.IRunnable` , em seguida, o tipo `Java.Lang.IRunnableInvoker` deve existir e deve conter o necessária `(IntPtr,
    JniHandleOwnership)` construtor.


Uma vez que os métodos de invocação de método JNI retornam local refs, `JniHandleOwnership.TransferLocalRef` seriam usados normalmente:

```csharp
IntPtr lrefString = JNIEnv.CallObjectMethod(instance, methodID);
Java.Lang.String value = Java.Lang.Object.GetObject<Java.Lang.String>( lrefString, JniHandleOwnership.TransferLocalRef);
```

<a name="_Looking_up_Java_Types" />

## <a name="looking-up-java-types"></a>Pesquisar tipos de Java

Para pesquisar um campo ou método em JNI, o tipo declarativo do campo ou do método deve ser pesquisado primeiro. O [Android.Runtime.JNIEnv.FindClass(string)](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.FindClass/(System.String)) método é usado para pesquisar tipos Java. O parâmetro de cadeia de caracteres é o *simplificado de referência de tipo* ou o *referência de tipo completo* para o tipo de Java. Consulte a [seção de referências de tipo de JNI](#_JNI_Type_References) para obter detalhes sobre referências de tipo completo e simplificada.

Observação: Ao contrário de todas as outras `JNIEnv` método que retorna instâncias de objeto, `FindClass` retorna uma referência global, não uma referência local.

<a name="_Instance_Fields" />

## <a name="instance-fields"></a>Campos de instância

Campos são manipulados por meio de *IDs do campo*. IDs do campo são obtidas por meio [JNIEnv.GetFieldID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetFieldID/), que requer que a classe que o campo é definido no, o nome do campo e o [assinatura de tipo de JNI](#JNI_Type_Signatures) do campo.

IDs do campo não precisa ser liberado e são válidos, desde que o tipo Java correspondente é carregado. (Android não atualmente suporta o descarregamento de classe.)

Há dois conjuntos de métodos para manipular os campos de instância: uma para ler os campos de instância e outro para a gravação de campos de instância. Todos os conjuntos de métodos requerem uma ID de campo ler ou gravar o valor do campo.


### <a name="reading-instance-field-values"></a>Ler valores de campo de instância

O conjunto de métodos para ler valores de campo de instância segue o padrão de nomenclatura:

```csharp
* JNIEnv.Get*Field(IntPtr instance, IntPtr fieldID);
```
onde `*` é o tipo do campo:

-   [JNIEnv.GetObjectField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetObjectField/) &ndash; ler o valor de qualquer campo de instância que não é um tipo interno, como `java.lang.Object` , matrizes e tipos de interface. O valor retornado é uma referência de local de JNI.

-   [JNIEnv.GetBooleanField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetBooleanField/) &ndash; ler o valor de `bool` campos de instância.

-   [JNIEnv.GetByteField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetByteField/) &ndash; ler o valor de `sbyte` campos de instância.

-   [JNIEnv.GetCharField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetCharField/) &ndash; ler o valor de `char` campos de instância.

-   [JNIEnv.GetShortField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetShortField/) &ndash; ler o valor de `short` campos de instância.

-   [JNIEnv.GetIntField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetIntField/) &ndash; ler o valor de `int` campos de instância.

-   [JNIEnv.GetLongField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetLongField/) &ndash; ler o valor de `long` campos de instância.

-   [JNIEnv.GetFloatField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetFloatField/) &ndash; ler o valor de `float` campos de instância.

-   [JNIEnv.GetDoubleField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetDoubleField/) &ndash; ler o valor de `double` campos de instância.





### <a name="writing-instance-field-values"></a>Gravando valores de campo de instância

O conjunto de métodos para gravar valores de campo de instância segue o padrão de nomenclatura:

```csharp
JNIEnv.SetField(IntPtr instance, IntPtr fieldID, Type value);
```

em que *tipo* é o tipo do campo:

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.IntPtr)) &ndash; gravar o valor de qualquer campo que não é um tipo interno, como `java.lang.Object` , matrizes e tipos de interface. O `IntPtr` valor pode ser uma referência de local de JNI, referência global de JNI, referência de global fraca JNI, ou `IntPtr.Zero` (para `null` ).

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.Boolean)) &ndash; gravar o valor de `bool` campos de instância.

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.SByte)) &ndash; gravar o valor de `sbyte` campos de instância.

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.Char)) &ndash; gravar o valor de `char` campos de instância.

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.Int16)) &ndash; gravar o valor de `short` campos de instância.

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.Int32)) &ndash; gravar o valor de `int` campos de instância.

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.Int64)) &ndash; gravar o valor de `long` campos de instância.

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.Single)) &ndash; gravar o valor de `float` campos de instância.

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.Double)) &ndash; gravar o valor de `double` campos de instância.


<a name="_Static_Fields" />

## <a name="static-fields"></a>Campos estáticos

Campos estáticos são manipulados por meio *IDs do campo*. IDs do campo são obtidas por meio [JNIEnv.GetStaticFieldID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticFieldID/), que requer que a classe que o campo é definido no, o nome do campo e o [assinatura de tipo de JNI](#JNI_Type_Signatures) do campo.

IDs do campo não precisa ser liberado e são válidos, desde que o tipo Java correspondente é carregado. (Android não atualmente suporta o descarregamento de classe.)

Há dois conjuntos de métodos para manipular os campos estáticos: um para a leitura de campos de instância e outro para a gravação de campos de instância. Todos os conjuntos de métodos requerem uma ID de campo ler ou gravar o valor do campo.


### <a name="reading-static-field-values"></a>Leitura de valores de campo estático

O conjunto de métodos para ler valores de campo estático segue o padrão de nomenclatura:

```csharp
* JNIEnv.GetStatic*Field(IntPtr class, IntPtr fieldID);
```

onde `*` é o tipo do campo:

-   [JNIEnv.GetStaticObjectField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticObjectField/) &ndash; ler o valor de qualquer campo estático que não é um tipo interno, como `java.lang.Object` , matrizes e tipos de interface. O valor retornado é uma referência de local de JNI.

-   [JNIEnv.GetStaticBooleanField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticBooleanField/) &ndash; ler o valor de `bool` campos estáticos.

-   [JNIEnv.GetStaticByteField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticByteField/) &ndash; ler o valor de `sbyte` campos estáticos.

-   [JNIEnv.GetStaticCharField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticCharField/) &ndash; ler o valor de `char` campos estáticos.

-   [JNIEnv.GetStaticShortField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticShortField/) &ndash; ler o valor de `short` campos estáticos.

-   [JNIEnv.GetStaticLongField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticLongField/) &ndash; ler o valor de `long` campos estáticos.

-   [JNIEnv.GetStaticFloatField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticFloatField/) &ndash; ler o valor de `float` campos estáticos.

-   [JNIEnv.GetStaticDoubleField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticDoubleField/) &ndash; ler o valor de `double` campos estáticos.



### <a name="writing-static-field-values"></a>Gravando valores de campo estático

O conjunto de métodos para gravar valores de campo estático segue o padrão de nomenclatura:

```csharp
JNIEnv.SetStaticField(IntPtr class, IntPtr fieldID, Type value);
```

em que *tipo* é o tipo do campo:

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.IntPtr)) &ndash; gravar o valor de qualquer campo estático que não é um tipo interno, como `java.lang.Object` , matrizes e tipos de interface. O `IntPtr` valor pode ser uma referência de local de JNI, referência global de JNI, referência de global fraca JNI, ou `IntPtr.Zero` (para `null` ).

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.Boolean)) &ndash; gravar o valor de `bool` campos estáticos.

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.SByte)) &ndash; gravar o valor de `sbyte` campos estáticos.

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.Char)) &ndash; gravar o valor de `char` campos estáticos.

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.Int16)) &ndash; gravar o valor de `short` campos estáticos.

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.Int32)) &ndash; gravar o valor de `int` campos estáticos.

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.Int64)) &ndash; gravar o valor de `long` campos estáticos.

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.Single)) &ndash; gravar o valor de `float` campos estáticos.

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.Double)) &ndash; gravar o valor de `double` campos estáticos.


<a name="_Instance_Methods" />

## <a name="instance-methods"></a>Métodos de instância

Métodos de instância são invocados por meio *método IDs*. Método IDs são obtidas por meio [JNIEnv.GetMethodID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetMethodID/), que requer que o tipo que o método é definido no, o nome do método e o [assinatura de tipo de JNI](#JNI_Type_Signatures) do método.

IDs de método não precisa ser liberado e são válidos, desde que o tipo Java correspondente é carregado. (Android não atualmente suporta o descarregamento de classe.)

Há dois conjuntos de métodos para invocar métodos: um para invocar métodos virtualmente e outro para invocar métodos não virtualmente. Os dois conjuntos de métodos requerem uma ID de método invocar o método e invocação não virtual também requer que você especifique qual implementação de classe deve ser invocada.

Métodos de interface só podem ser pesquisados dentro do tipo declarativo; métodos que vêm de interfaces herdadas/estendido não podem ser pesquisados. Consulte as Interfaces de associação posterior / implementação do chamador de seção para obter mais detalhes.

Qualquer método declarado na classe ou qualquer classe base ou interface implementada pode ser pesquisado.


### <a name="virtual-method-invocation"></a>Invocação de método virtual

O conjunto de métodos para chamar métodos praticamente segue o padrão de nomenclatura:

```csharp
* JNIEnv.Call*Method( IntPtr instance, IntPtr methodID, params JValue[] args );
```

onde `*` é o tipo de retorno do método.

-   [JNIEnv.CallObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallObjectMethod/) &ndash; invocar um método que retorna um tipo não builtin, tais como `java.lang.Object` , matrizes e interfaces. O valor retornado é uma referência de local de JNI.

-   [JNIEnv.CallBooleanMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallBooleanMethod/) &ndash; invocar um método que retorna um `bool` valor.

-   [JNIEnv.CallByteMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallByteMethod/) &ndash; invocar um método que retorna um `sbyte` valor.

-   [JNIEnv.CallCharMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallCharMethod/) &ndash; invocar um método que retorna um `char` valor.

-   [JNIEnv.CallShortMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallShortMethod/) &ndash; invocar um método que retorna um `short` valor.

-   [JNIEnv.CallLongMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallLongMethod/) &ndash; invocar um método que retorna um `long` valor.

-   [JNIEnv.CallFloatMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallFloatMethod/) &ndash; invocar um método que retorna um `float` valor.

-   [JNIEnv.CallDoubleMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallDoubleMethod/) &ndash; invocar um método que retorna um `double` valor.



### <a name="non-virtual-method-invocation"></a>Invocação de método não virtual

O conjunto de métodos para chamar métodos sem praticamente segue o padrão de nomenclatura:

```csharp
* JNIEnv.CallNonvirtual*Method( IntPtr instance, IntPtr class, IntPtr methodID, params JValue[] args );
```

onde `*` é o tipo de retorno do método. Invocação de método não virtual normalmente é usada para invocar o método de base de um método virtual.

-   [JNIEnv.CallNonvirtualObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualObjectMethod/) &ndash; não são praticamente invocar um método que retorna um tipo não builtin, tais como `java.lang.Object` , matrizes e interfaces. O valor retornado é uma referência de local de JNI.

-   [JNIEnv.CallNonvirtualBooleanMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualBooleanMethod/) &ndash; não são praticamente invocar um método que retorna um `bool` valor.

-   [JNIEnv.CallNonvirtualByteMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualByteMethod/) &ndash; não são praticamente invocar um método que retorna um `sbyte` valor.

-   [JNIEnv.CallNonvirtualCharMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualCharMethod/) &ndash; não são praticamente invocar um método que retorna um `char` valor.

-   [JNIEnv.CallNonvirtualShortMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualShortMethod/) &ndash; não são praticamente invocar um método que retorna um `short` valor.

-   [JNIEnv.CallNonvirtualLongMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualLongMethod/) &ndash; não são praticamente invocar um método que retorna um `long` valor.

-   [JNIEnv.CallNonvirtualFloatMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualFloatMethod/) &ndash; não são praticamente invocar um método que retorna um `float` valor.

-   [JNIEnv.CallNonvirtualDoubleMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualDoubleMethod/) &ndash; não são praticamente invocar um método que retorna um `double` valor.


<a name="_Static_Methods" />

## <a name="static-methods"></a>Métodos estáticos

Métodos estáticos são invocados por meio *método IDs*. Método IDs são obtidas por meio [JNIEnv.GetStaticMethodID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticMethodID/), que requer que o tipo que o método é definido no, o nome do método e o [assinatura de tipo de JNI](#JNI_Type_Signatures) do método.

IDs de método não precisa ser liberado e são válidos, desde que o tipo Java correspondente é carregado. (Android não atualmente suporta o descarregamento de classe.)



### <a name="static-method-invocation"></a>Invocação de método estático

O conjunto de métodos para chamar métodos praticamente segue o padrão de nomenclatura:

```csharp
* JNIEnv.CallStatic*Method( IntPtr class, IntPtr methodID, params JValue[] args );
```

onde `*` é o tipo de retorno do método.

-   [JNIEnv.CallStaticObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticObjectMethod/) &ndash; chamar um método estático que retorna um tipo não builtin, tais como `java.lang.Object` , matrizes e interfaces. O valor retornado é uma referência de local de JNI.

-   [JNIEnv.CallStaticBooleanMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticBooleanMethod/) &ndash; chamar um método estático que retorna um `bool` valor.

-   [JNIEnv.CallStaticByteMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticByteMethod/) &ndash; chamar um método estático que retorna um `sbyte` valor.

-   [JNIEnv.CallStaticCharMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticCharMethod/) &ndash; chamar um método estático que retorna um `char` valor.

-   [JNIEnv.CallStaticShortMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticShortMethod/) &ndash; chamar um método estático que retorna um `short` valor.

-   [JNIEnv.CallStaticLongMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallLongMethod/) &ndash; chamar um método estático que retorna um `long` valor.

-   [JNIEnv.CallStaticFloatMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticFloatMethod/) &ndash; chamar um método estático que retorna um `float` valor.

-   [JNIEnv.CallStaticDoubleMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticDoubleMethod/) &ndash; chamar um método estático que retorna um `double` valor.


<a name="JNI_Type_Signatures" />

## <a name="jni-type-signatures"></a>Assinaturas de tipo JNI

[Assinaturas de tipo de JNI](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/types.html#wp16432) estão [referências de tipo de JNI](#_JNI_Type_References) (referências de tipo no entanto, não simplificado), com exceção dos métodos. Com métodos, a assinatura de tipo de JNI é um parêntese de abertura `'('`, seguido pelas referências de tipo para todos o tipos concatenados (com nenhuma vírgula a separação ou qualquer outra coisa), seguido por um parêntese de fechamento de parâmetro `')'`, seguido pela referência de tipo de JNI do tipo de retorno do método.

Por exemplo, considerando o método Java:

```java
long f(int n, String s, int[] array);
```

A assinatura de tipo JNI seria:

```csharp
(ILjava/lang/String;[I)J
```

Em geral, é *fortemente* recomendável usar o `javap` comando para determinar as assinaturas JNI. Por exemplo, a assinatura de tipo de JNI do [java.lang.Thread.State.valueOf(String)](https://developer.android.com/reference/java/lang/Thread.State.html#valueOf(java.lang.String)) método é "(Ljava/lang/cadeia de caracteres;) Ljava/lang/Thread$ estado;", enquanto a JNI tipo de assinatura do [ java.lang.Thread.State.values](https://developer.android.com/reference/java/lang/Thread.State.html#values) método é "() [lang/Ljava/Thread$ estado;". Fique atento para a ponto e vírgula à direita; Esses *são* faz parte da assinatura de tipo de JNI.

<a name="_JNI_Type_References" />

## <a name="jni-type-references"></a>Referências de tipo JNI

Referências de tipo JNI são diferentes das referências de tipo Java. Você não pode usar nomes de tipo totalmente qualificados do Java, como `java.lang.String` com JNI, você deve usar as variações de JNI `"java/lang/String"` ou `"Ljava/lang/String;"`, dependendo do contexto; consulte abaixo para obter detalhes.
Há quatro tipos de referências de tipo JNI:

-  **built-in**
-  **simplified**
-  **type**
-  **array**


### <a name="built-in-type-references"></a>Referências de tipo interno

Referências de tipo internos são um único caractere usado para referenciar tipos de valor internos. O mapeamento é da seguinte maneira:

-  `"B"` para `sbyte` .
-  `"S"` para `short` .
-  `"I"` para `int` .
-  `"J"` para `long` .
-  `"F"` para `float` .
-  `"D"` para `double` .
-  `"C"` para `char` .
-  `"Z"` para `bool` .
-  `"V"` para `void` tipos de retorno do método.


<a name="_Simplified_Type_References_1" />

### <a name="simplified-type-references"></a>Referências de tipo simplificada

Referências de tipo simplificada só podem ser usadas na [JNIEnv.FindClass(string)](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.FindClass/(System.String)).
Há duas maneiras para derivar uma referência de tipo simplificado:

1.  Um nome totalmente qualificado de Java, substitua cada `'.'` dentro do nome do pacote e antes do nome de tipo com `'/'` e cada `'.'` dentro de um nome de tipo com `'$'` .

1.  Ler a saída do `'unzip -l android.jar | grep JavaName'` .


Uma das duas resultará no tipo de Java [java.lang.Thread.State](https://developer.android.com/reference/java/lang/Thread.State.html) que é mapeado para a referência de tipo simplificada `java/lang/Thread$State`.


### <a name="type-references"></a>Referências de tipo

Uma referência de tipo é uma referência de tipo interno ou uma referência de tipo simplificada com um `'L'` prefixo e um `';'` sufixo. Para o tipo de Java [lang](https://developer.android.com/reference/java/lang/String.html), é a referência de tipo simplificada `"java/lang/String"`, enquanto que a referência de tipo é `"Ljava/lang/String;"`.

Referências de tipo são usadas com referências de tipo de matriz e com assinaturas de JNI.

Uma outra maneira de obter uma referência de tipo está lendo a saída de `'javap -s -classpath android.jar fully.qualified.Java.Name'`.
Dependendo do tipo envolvida, você pode usar uma declaração de construtor ou método retorna o tipo para determinar o nome JNI. Por exemplo:

```shell
$ javap -classpath android.jar -s java.lang.Thread.State
Compiled from "Thread.java"
```

```java
public final class java.lang.Thread$State extends java.lang.Enum{
public static final java.lang.Thread$State NEW;
  Signature: Ljava/lang/Thread$State;
public static final java.lang.Thread$State RUNNABLE;
  Signature: Ljava/lang/Thread$State;
public static final java.lang.Thread$State BLOCKED;
  Signature: Ljava/lang/Thread$State;
public static final java.lang.Thread$State WAITING;
  Signature: Ljava/lang/Thread$State;
public static final java.lang.Thread$State TIMED_WAITING;
  Signature: Ljava/lang/Thread$State;
public static final java.lang.Thread$State TERMINATED;
  Signature: Ljava/lang/Thread$State;
public static java.lang.Thread$State[] values();
  Signature: ()[Ljava/lang/Thread$State;
public static java.lang.Thread$State valueOf(java.lang.String);
  Signature: (Ljava/lang/String;)Ljava/lang/Thread$State;
static {};
  Signature: ()V
}
```

`Thread.State` é um tipo de enumeração de Java, então é possível usar a assinatura do `valueOf` método para determinar que a referência de tipo é o estado de $ Ljava/lang/Thread;.



### <a name="array-type-references"></a>Referências de tipo de matriz

Referências de tipo de matriz são `'['` prefixado para uma referência de tipo JNI.
Referências de tipo simplificado não podem ser usadas ao especificar matrizes.

Por exemplo, `int[]` está `"[I"`, `int[][]` é `"[[I"`, e `java.lang.Object[]` é `"[Ljava/lang/Object;"`.



## <a name="java-generics-and-type-erasure"></a>Eliminação de tipo e genéricos de Java

*A maioria dos* do tempo, como visto por meio de JNI, genéricos de Java *não existem*.
Há algumas "dobras", mas esses dobras estão em como o Java interage com os genéricos, não com como JNI procura e invoca membros genéricos.

Não há nenhuma diferença entre um tipo genérico ou membro e um tipo não genérico ou membro ao interagir por meio de JNI. Por exemplo, o tipo genérico [java.lang.Class&lt;T&gt; ](https://developer.android.com/reference/java/lang/Class.html) também é o tipo genérico "bruto" `java.lang.Class`, ambos com a mesma referência de tipo simplificada, `"java/lang/Class"`.


## <a name="java-native-interface-support"></a>Suporte de Interface nativa do Java

[Android.Runtime.JNIEnv](https://developer.xamarin.com/api/type/Android.Runtime.JNIEnv/) é o wrapper gerenciado para o Java JNI (Interface nativa). JNI funções são declaradas dentro de [especificação de Interface nativa Java](http://download.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html), embora os métodos foram alterados para remover o explícita `JNIEnv*` parâmetro e `IntPtr` é usado em vez de `jobject`, `jclass`, `jmethodID`, etc. Por exemplo, considere a [JNI NewObject função](http://download.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html#wp4517):

```csharp
jobject NewObjectA(JNIEnv *env, jclass clazz, jmethodID methodID, jvalue *args);
```

Isso é exposto como o [JNIEnv.NewObject](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.NewObject/p/System.IntPtr/System.IntPtr/Android.Runtime.JValue[]/) método:

```csharp
public static IntPtr NewObject(IntPtr clazz, IntPtr jmethod, params JValue[] parms);
```

Convertendo entre as duas chamadas é razoavelmente simples. Em C, você teria:

```c
jobject CreateMapActivity(JNIEnv *env)
{
    jclass    Map_Class   = (*env)->FindClass(env, "mono/samples/googlemaps/MyMapActivity");
    jmethodID Map_defCtor = (*env)->GetMethodID (env, Map_Class, "<init>", "()V");
    jobject   instance    = (*env)->NewObject (env, Map_Class, Map_defCtor);

    return instance;
}
```

O C# equivalente seria:

```csharp
IntPtr CreateMapActivity()
{
    IntPtr Map_Class   = JNIEnv.FindClass ("mono/samples/googlemaps/MyMapActivity");
    IntPtr Map_defCtor = JNIEnv.GetMethodID (Map_Class, "<init>", "()V");
    IntPtr instance    = JNIEnv.NewObject (Map_Class, Map_defCtor);

    return instance;
}
```

Quando você tiver uma instância de objeto Java mantida em um IntPtr, provavelmente você desejará fazer algo com ele. Você pode usar como métodos JNIEnv [ <span class="external">JNIEnv.CallVoidMethod()</span> ](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallVoidMethod/p/System.IntPtr/System.IntPtr/Android.Runtime.JValue[]/) para fazer isso, mas se já houver um análogo de C# wrapper, você vai querer construir um wrapper sobre a referência JNI. Você pode fazer isso por meio de [Extensions.JavaCast <t>()</t> ](https://developer.xamarin.com/api/member/Android.Runtime.Extensions.JavaCast%7BTResult%7D/p/Android.Runtime.IJavaObject/) método de extensão:

```csharp
IntPtr lrefActivity = CreateMapActivity();

// imagine that Activity were instead an interface or abstract type...
Activity mapActivity = new Java.Lang.Object(lrefActivity, JniHandleOwnership.TransferLocalRef)
    .JavaCast<Activity>();
```

Você também pode usar o [Java.Lang.Object.GetObject <t>()</t> ](https://developer.xamarin.com/api/member/Java.Lang.Object.GetObject%7BT%7D/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) método:

```csharp
IntPtr lrefActivity = CreateMapActivity();

// imagine that Activity were instead an interface or abstract type...
Activity mapActivity = Java.Lang.Object.GetObject<Activity>(lrefActivity, JniHandleOwnership.TransferLocalRef);
```

Além disso, todas as funções JNI foram modificadas, removendo o `JNIEnv*` parâmetro presente em cada função JNI.


## <a name="summary"></a>Resumo

Lidar diretamente com JNI é uma experiência terrível que deve ser evitada a todo custo. Infelizmente, nem sempre é podem ser evitado; Espero que este guia fornecerá alguma ajuda quando você atinge os casos de Java não acoplados com Mono para Android.


## <a name="related-links"></a>Links relacionados

- [SanityTests (amostra)](https://developer.xamarin.com/samples/SanityTests/)
- [Especificação de Interface nativa do Java](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/jniTOC.html)
- [Funções de Interface nativa do Java](http://download.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html)
