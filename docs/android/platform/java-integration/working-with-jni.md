---
title: Trabalhando com JNI
description: "Xamarin permite escrever aplicativos do Android no c# em vez de Java. Vários assemblies são fornecidos com o xamarin que fornecem associações para bibliotecas de Java, incluindo Mono.Android.dll e Mono.Android.GoogleMaps.dll. No entanto, associações não são fornecidas para cada biblioteca Java possíveis e as associações que são fornecidas não podem associar cada tipo de Java e membro. Para usar tipos Java e os membros não associado, a Interface nativa de Java (JNI) pode ser usado. Este artigo ilustra como usar JNI para interagir com tipos de Java e membros de aplicativos xamarin."
ms.topic: article
ms.prod: xamarin
ms.assetid: A417DEE9-7B7B-4E35-A79C-284739E3838E
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/15/2018
ms.openlocfilehash: d1c441de089a84c93c251588115abecb19816868
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="working-with-jni"></a>Trabalhando com JNI

_Xamarin permite escrever aplicativos do Android no c# em vez de Java. Vários assemblies são fornecidos com o xamarin que fornecem associações para bibliotecas de Java, incluindo Mono.Android.dll e Mono.Android.GoogleMaps.dll. No entanto, associações não são fornecidas para cada biblioteca Java possíveis e as associações que são fornecidas não podem associar cada tipo de Java e membro. Para usar tipos Java e os membros não associado, a Interface nativa de Java (JNI) pode ser usado. Este artigo ilustra como usar JNI para interagir com tipos de Java e membros de aplicativos xamarin._

<a name="_Overview" />

## <a name="overview"></a>Visão geral

Nem sempre é possível criar um gerenciado Callable Wrapper (MCW) para invocar o código Java ou necessário. Em muitos casos, "inline" JNI é perfeitamente aceitável e são úteis para uso individual de membros de Java não associados. Geralmente é mais simples usar JNI para invocar um único método em uma classe Java que ao gerar uma associação. jar inteira.

Xamarin fornece o `Mono.Android.dll` assembly, que fornece uma associação para do Android `android.jar` biblioteca. Tipos e membros não está presente no `Mono.Android.dll` e tipos não está presente no `android.jar` podem ser usados por associá-los manualmente. Para associar Java tipos e membros, você deve usar o **Interface nativa do Java** (**JNI**) para pesquisar tipos, ler, gravar campos e chamar métodos.

A API de JNI no xamarin é conceitualmente muito semelhante do `System.Reflection` API no .NET: ele possibilita pesquisar tipos e membros por nome, ler e gravar valores de campo, invocar métodos e muito mais. Você pode usar JNI e `Android.Runtime.RegisterAttribute` atributo personalizado para declarar os métodos virtuais que podem ser vinculados para dar suporte a substituição. Você pode associar interfaces para que eles possam ser implementados em c#.

Este documento explica:

-  Como JNI refere-se aos tipos.
-  Como pesquisa, leitura e gravação campos.
-  Como pesquisar e chamar métodos.
-  Como expor métodos virtuais para permitir a substituição de código gerenciado.
-  Como expor interfaces.


<a name="_Requirements" />

## <a name="requirements"></a>Requisitos

JNI, como exposto por meio de [Android.Runtime.JNIEnv namespace](https://developer.xamarin.com/api/type/Android.Runtime.JNIEnv/), está disponível em cada versão do xamarin.
Para associar as interfaces e tipos de Java, você deve usar o xamarin 4.0 ou posterior.

<a name="_Managed_Callable_Wrappers" />

## <a name="managed-callable-wrappers"></a>Callable Wrappers gerenciados

Um **gerenciados Callable Wrapper** (**MCW**) é um *associação* para uma classe Java ou interface que encapsula a todas as máquinas JNI para que o código de cliente c# não precisa se preocupar sobre a complexidade subjacente de JNI. A maioria das `Mono.Android.dll` consiste encapsuladores que pode ser chamadas.

Callable wrappers do gerenciado têm duas finalidades:

1.  Encapsule o uso JNI para que o código do cliente não precisa saber sobre a complexidade subjacente.
1.  Possibilitam a classe sub tipos Java e implementar interfaces Java.

A finalidade da primeira é apenas para conveniência e ao encapsulamento de complexidade para que os consumidores têm um conjunto de classes para uso simple e gerenciado. Isso requer o uso de diversos [JNIEnv](https://developer.xamarin.com/api/type/Android.Runtime.JNIEnv/) membros conforme descrito posteriormente neste artigo. Tenha em mente que callable wrappers gerenciados não são estritamente necessárias &ndash; "inline" usar JNI é perfeitamente aceitável e é útil para uso individual de membros de Java não associados. Implementação classing abaixo e interface requer o uso de encapsuladores que pode ser chamadas.


<a name="_Android_Callable_Wrappers" />

## <a name="android-callable-wrappers"></a>Callable Wrappers do Android

Android callable wrappers do (ACW) são necessárias sempre que o tempo de execução do Android (arte) precisa chamar código gerenciado; Esses wrappers serão necessárias porque não é possível registrar classes com arte em tempo de execução.
(Especificamente, o [DefineClass](http://docs.oracle.com/javase/6/docs/technotes/guides/jni/spec/functions.html#wp15986) função JNI não é suportada pelo tempo de execução do Android. Callable wrappers do Android, portanto, compõem a falta de suporte de registro de tipo de tempo de execução.)

Sempre que precisa executar uma máquina virtual ou método que é substituído ou implementado em código gerenciado de interface de código do Android, xamarin deve fornecer um proxy do Java para que esse método obtém enviado para o tipo gerenciado apropriado. Esses tipos de proxy do Java são o código Java que têm a classe base "mesmo" e a lista de interfaces de Java como o tipo gerenciado, Implementando os construtores mesmo e declarando uma classe base substituída e métodos de interface.

Callable wrappers do Android são gerados pelo **monodroid.exe** programa durante o [do processo de compilação](~/android/deploy-test/building-apps/build-process.md)e são gerados para todos os tipos que herdam (direta ou indiretamente) [ Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/).


<a name="_Implementing_Interfaces" />

### <a name="implementing-interfaces"></a>Implementando interfaces

Há ocasiões em que talvez você precise implementar uma interface Android, (como [Android.Content.IComponentCallbacks](https://developer.xamarin.com/api/type/Android.Content.IComponentCallbacks/)).

Todas as interfaces e classes Android estendem o [Android.Runtime.IJavaObject](https://developer.xamarin.com/api/type/Android.Runtime.IJavaObject/) interface; portanto, todos os tipos de Android devem implementar `IJavaObject`.
Xamarin aproveita desse fato &ndash; usa `IJavaObject` para fornecer Android com um proxy de Java (um wrapper que pode ser chamado Android) para o tipo de dado gerenciado. Porque **monodroid.exe** procura somente `Java.Lang.Object` subclasses (que deve implementar `IJavaObject`), subclasses `Java.Lang.Object` nos fornece uma maneira de implementar interfaces em código gerenciado. Por exemplo:

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

<a name="_Implementation_Details" />

### <a name="implementation-details"></a>Detalhes de implementação

*O restante deste artigo fornece detalhes de implementação, sujeito a alterações sem aviso prévio* (e são apresentados aqui somente por desenvolvedores podem estar curiosos sobre o que está acontecendo nos bastidores).

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


<a name="_ExportAttribute_and_ExportFieldAttribute" />

### <a name="exportattribute-and-exportfieldattribute"></a>ExportAttribute e ExportFieldAttribute

Normalmente, o xamarin gera automaticamente o código de Java que compreende ACW; Essa geração baseia-se nos nomes de classe e método quando uma classe derivada de uma classe Java e substitui os métodos de Java existentes. No entanto, em alguns cenários, a geração de código não é adequada, como descrito abaixo:

-   Ação de suporte do Android nomes em atributos de layout de xml, por exemplo o [android: onClick](https://developer.xamarin.com/api/member/Android.Views.View+IOnClickListener.OnClick/p/Android.Views.View/) atributo XML. Quando for especificado, a instância de exibição inflated tente pesquisar o método de Java.

-   O [Java.IO. Serializable](http://developer.android.com/reference/java/io/Serializable.html) interface requer `readObject` e `writeObject` métodos. Desde que eles não são membros desta interface, nossa implementação gerenciada correspondente não expõe esses métodos para o código de Java.

-   O [android.os.Parcelable](https://developer.xamarin.com/api/type/Android.Os.Parcelable/) interface espera que uma classe de implementação deve ter um campo estático `CREATOR` do tipo `Parcelable.Creator`. O código Java gerado requer alguns campos explícito. Com nosso cenário padrão, não há nenhuma maneira de campo de saída no código Java a partir do código gerenciado.


Como a geração de código não fornece uma solução para gerar métodos arbitrários de Java com nomes arbitrários, começando com o xamarin 4.2, o [ExportAttribute](https://developer.xamarin.com/api/type/Java.Interop.ExportAttribute/) e [ExportFieldAttribute](https://developer.xamarin.com/api/type/Java.Interop.ExportFieldAttribute/) foram introduzido para oferecer uma solução para os cenários acima. Ambos os atributos residem no `Java.Interop` namespace:

-   `ExportAttribute` &ndash; Especifica um nome de método e seus tipos de exceção esperada (para dar explícito "gera", em Java). Quando ele é usado em um método, o método "exportará" um método de Java que gera um código de distribuição para a invocação de JNI correspondente para o método gerenciado. Isso pode ser usado com `android:onClick` e `java.io.Serializable`.

-   `ExportFieldAttribute` &ndash; Especifica um nome de campo. Ela reside em um método que funciona como um inicializador de campo. Isso pode ser usado com `android.os.Parcelable`.

O [ExportAttribute](https://developer.xamarin.com/samples/monodroid/ExportAttribute/) projeto de exemplo ilustra como usar esses atributos.

<a name="_Troubleshooting_ExportAttribute_and_ExportFieldAttribute" />

#### <a name="troubleshooting-exportattribute-and-exportfieldattribute"></a>Solucionando problemas de ExportAttribute e ExportFieldAttribute

-   Empacotamento falhar devido à ausência de **Mono.Android.Export.dll** &ndash; se você usou `ExportAttribute` ou `ExportFieldAttribute` em alguns métodos no seu código ou bibliotecas dependentes, você precisará adicionar  **Mono.Android.Export.dll**. Esse assembly é isolado para dar suporte a código de retorno de chamada do Java. Ele é separado de **Mono.Android.dll** enquanto adiciona adicionais de tamanho para o aplicativo.

-   Na versão de compilação, `MissingMethodException` ocorre para métodos de exportação &ndash; na versão de compilação, `MissingMethodException` ocorre para métodos de exportação. (Esse problema é corrigido na versão mais recente do xamarin.)


<a name="_ExportParameterAttribute" />

### <a name="exportparameterattribute"></a>ExportParameterAttribute

`ExportAttribute` e `ExportFieldAttribute` fornecem funcionalidade que pode usar o código de tempo de execução de Java. Esse código de tempo de execução acessa o código gerenciado por meio dos métodos JNI gerados orientados por esses atributos. Como resultado, não há nenhum método de Java existente que associa o método gerenciado; Portanto, o método de Java é gerado de uma assinatura de método gerenciado.

No entanto, nesse caso, não é totalmente determinante. Notadamente, isso é verdadeiro em algumas avançados mapeamentos entre tipos gerenciados e tipos de Java, como:

-  InputStream
-  OutputStream
-  XmlPullParser
-  XmlResourceParser

Quando tipos de como eles são necessários para métodos exportados, o `ExportParameterAttribute` deve ser usada para fornecer o parâmetro correspondente explicitamente ou um tipo de valor de retorno.

 <a name="_Annotation_Attribute" />


### <a name="annotation-attribute"></a>Atributo de anotação

Xamarin 4.2, convertemos `IAnnotation` tipos de implementação em atributos (System. Attribute) e suporte adicionado para geração de anotação wrappers de Java.

Isso significa que as seguintes alterações direcionais:

-   O gerador de associação gera `Java.Lang.DeprecatedAttribute` de `java.Lang.Deprecated` (embora deve ser `[Obsolete]` em código gerenciado).

-   Isso não significa que existente `Java.Lang.Deprecated` classe desaparecerão. Esses objetos baseados em Java podem ser usados ainda como objetos de Java normais (se existir tal uso). Haverá `Deprecated` e `DeprecatedAttribute` classes.

-   O `Java.Lang.DeprecatedAttribute` classe está marcada como `[Annotation]` . Quando há um atributo personalizado que é herdado disso `[Annotation]` atributo, tarefa msbuild irá gerar uma anotação de Java para o atributo personalizado (@Deprecated) no Wrapper pode ser chamado Android (ACW).

-   As anotações podem ser geradas para classes, métodos e exportados campos (que é um método no código gerenciado).

Se a classe continente (a própria classe anotada, ou a classe que contém os membros anotados) não estiver registrada, toda a origem de classe Java não é gerada, incluindo anotações. Para métodos, você pode especificar o `ExportAttribute` para obter o método gerado e anotado explicitamente. Além disso, não é um recurso para uma definição de classe de anotação Java "Gerar". Em outras palavras, se você definir um atributo personalizado de gerenciado para um determinado tipo de anotação, você precisará adicionar outra biblioteca. jar que contém a classe de anotação Java correspondente. Adicionar um arquivo de origem Java que define o tipo de anotação não é suficiente. O compilador Java não funciona da mesma maneira como **apt**.

Além disso, aplicar as seguintes limitações:

-   Esse processo de conversão não considera `@Target` anotação até o momento em que o tipo de anotação.

-   Atributos para uma propriedade não funciona. Use atributos para a propriedade getter ou setter.


<a name="_Class_Binding" />

## <a name="class-binding"></a>Associação de classe

Associação de uma classe significa escrevendo um callable wrapper gerenciado para simplificar a invocação do tipo Java subjacente.

Métodos abstratos e virtuais para permitir a substituição do c# de associação requer xamarin 4.0. No entanto, qualquer versão do xamarin pode associar métodos virtuais, métodos estáticos ou métodos não virtuais sem suporte para substituições.

Uma associação normalmente contém os seguintes itens:

-  Um [JNI identificador para o tipo de Java que está sendo associado](#_Looking_up_Java_Types).

-  [Campo de JNI IDs e as propriedades para cada campo associado](#_Instance_Fields).

-  [IDs de método JNI e os métodos para cada um associado método](#_Instance_Methods).

-  Se for necessário classing abaixo, o tipo deve ter um [RegisterAttribute](https://developer.xamarin.com/api/type/Android.Runtime.RegisterAttribute/) atributo personalizado na declaração de tipo com [RegisterAttribute.DoNotGenerateAcw](https://developer.xamarin.com/api/property/Android.Runtime.RegisterAttribute.DoNotGenerateAcw/) definido como `true`.


<a name="_Declaring_Type_Handle" />

### <a name="declaring-type-handle"></a>Identificador de tipo declarativo

Os métodos de pesquisa de campo e método exigem uma referência de objeto referindo-se ao seu tipo declarativo. Por convenção, isso é mantido em um `class_ref` campo:

```csharp
static IntPtr class_ref = JNIEnv.FindClass(CLASS);
```

Consulte o [referências de tipo de JNI](#_JNI_Type_References) seção para obter detalhes sobre o `CLASS` token.

<a name="_Binding_Fields" />

### <a name="binding-fields"></a>Campos de associação

Campos de Java são expostos como propriedades do c#, por exemplo, o campo de Java [java.lang.System.in](http://developer.android.com/reference/java/lang/System.html#in) associados como a propriedade c# [Java.Lang.JavaSystem.In](https://developer.xamarin.com/api/property/Java.Lang.JavaSystem.In/).
Além disso, como JNI faz distinção entre campos estáticos e campos de instância, diferentes métodos de ser usado ao implementar as propriedades.

Associação de campo envolve três conjuntos de métodos:

1.  O *obter id do campo* método. O *obter id do campo* método é responsável por retornar um campo de lidar com isso o *obter o valor do campo* e *definir valor do campo* métodos usará. Como obter a id de campo requer conhecimento de declaração de tipo, o nome do campo e o [assinatura de tipo JNI](#_JNI_Type_Signatures) do campo.

1.  O *obter o valor do campo* métodos. Esses métodos exigem o identificador de campo e serão responsáveis por ler o valor do campo do Java.
    O método a ser usado depende do tipo do campo.

1.  O *definir valor do campo* métodos. Esses métodos exigem o identificador de campo e serão responsáveis por gravar o valor do campo em Java. O método a ser usado depende do tipo do campo.


 [Campos estáticos](#_Static_Fields) usar o [JNIEnv.GetStaticFieldID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticMethodID/), `JNIEnv.GetStatic*Field`, e [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/) métodos.

 [Campos de instância](#_Instance_Fields) usar o [JNIEnv.GetFieldID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetFieldID/), `JNIEnv.Get*Field`, e [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/) métodos.

Por exemplo, a propriedade estática `JavaSystem.In` podem ser implementadas como:

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

Observação: Estamos usando [InputStreamInvoker.FromJniHandle](https://developer.xamarin.com/api/member/Android.Runtime.InputStreamInvoker.FromJniHandle/(System.IntPtr%2cAndroid.Runtime.JniHandleOwnership)) para converter a referência JNI em um `System.IO.Stream` instância e está usando `JniHandleOwnership.TransferLocalRef` porque [JNIEnv.GetStaticObjectField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticObjectField/) Retorna uma referência local.

Muitas do [Android.Runtime](https://developer.xamarin.com/api/namespace/Android.Runtime/) tipos têm `FromJniHandle` referência de métodos que converterá um JNI para o tipo desejado.

 <a name="_Method_Binding" />


### <a name="method-binding"></a>Vinculação de método

Métodos de Java são expostos como c# métodos e propriedades do c#. Por exemplo, o método Java [java.lang.Runtime.runFinalizersOnExit](http://developer.android.com/reference/java/lang/Runtime.html#runFinalizersOnExit(boolean)) método está vinculado como o [Java.Lang.Runtime.RunFinalizersOnExit](https://developer.xamarin.com/api/member/Java.Lang.Runtime.RunFinalizersOnExit/) método e o [java.lang.Object.getClass ](http://developer.android.com/reference/java/lang/Object.html#getClass) método está vinculado como o [Java.Lang.Object.Class](https://developer.xamarin.com/api/property/Java.Lang.Object.Class/) propriedade.

Invocação de método é um processo de duas etapas:

1.  O *obter id do método* para o método a ser invocado. O *obter id do método* método é responsável por retornar um identificador do método que irá usar os métodos de invocação de método. Como obter a identificação de método requer conhecimento de declaração de tipo, o nome do método e o [assinatura de tipo JNI](#_JNI_Type_Signatures) do método.

1.  Invoque o método.

Assim como ocorre com campos, os métodos para usar para obter a identificação do método e invocar o método diferem entre os métodos de instância e métodos estáticos.

[Métodos estáticos](#_Static_Methods_1) usar [JNIEnv.GetStaticMethodID()](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticMethodID/) para pesquisar a identificação do método e usar o `JNIEnv.CallStatic*Method` família de métodos para invocação.

[Métodos de instância](#_Instance_Methods) usar [JNIEnv.GetMethodID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetMethodID/) para pesquisar a identificação do método e usar o `JNIEnv.Call*Method` e `JNIEnv.CallNonvirtual*Method` famílias de métodos para invocação.

Vinculação de método é potencialmente mais do que apenas a invocação de método. Vinculação de método também inclui permitindo que um método a ser substituído (para métodos abstract e não-final) ou implementado (para métodos de interface). O [dando suporte a herança, Interfaces](#_Supporting_Inheritance,_Interfaces_1) seção aborda as complexidades de dar suporte a métodos de interface e métodos virtuais.

 <a name="_Static_Methods" />


#### <a name="static-methods"></a>Métodos estáticos

Um método estático de associação envolve o uso do `JNIEnv.GetStaticMethodID` para obter um identificador de método, em seguida, usando as `JNIEnv.CallStatic*Method` método, dependendo do tipo de retorno do método. A seguir está um exemplo de uma associação para o [Runtime.getRuntime](http://developer.android.com/reference/java/lang/Runtime.html#getRuntime()) método:

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

Observe que, armazenamos o identificador do método em um campo estático, `id_getRuntime`. Isso é uma otimização de desempenho, para que o identificador do método não precisa ser pesquisado em cada chamada. Não é necessário para armazenar em cache o identificador do método dessa maneira. Depois de obter o identificador do método, [JNIEnv.CallStaticObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticObjectMethod/) é usado para chamar o método. `JNIEnv.CallStaticObjectMethod` Retorna um `IntPtr` que contém o identificador da instância Java retornado.
[Java.Lang.Object.GetObject&lt;T&gt;(IntPtr, JniHandleOwnership)](https://developer.xamarin.com/api/member/Java.Lang.Object.GetObject%7BT%7D/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) é usado para converter o identificador de Java em uma instância do objeto fortemente tipado.

 <a name="_Non-virtual_Instance_Method_Binding" />


#### <a name="non-virtual-instance-method-binding"></a>Vinculação de método de instância não virtual

Associando um `final` método de instância ou um método de instância que não requer substituição, envolve o uso do `JNIEnv.GetMethodID` para obter um identificador de método, em seguida, usando as `JNIEnv.Call*Method` método, dependendo do tipo de retorno do método. A seguir está um exemplo de uma associação para o `Object.Class` propriedade:

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

Observe que, armazenamos o identificador do método em um campo estático, `id_getClass`.
Isso é uma otimização de desempenho, para que o identificador do método não precisa ser pesquisado em cada chamada. Não é necessário para armazenar em cache o identificador do método dessa maneira. Depois de obter o identificador do método, [JNIEnv.CallStaticObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticObjectMethod/) é usado para chamar o método. `JNIEnv.CallStaticObjectMethod` Retorna um `IntPtr` que contém o identificador da instância Java retornado.
[Java.Lang.Object.GetObject&lt;T&gt;(IntPtr, JniHandleOwnership)](https://developer.xamarin.com/api/member/Java.Lang.Object.GetObject%7BT%7D/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) é usado para converter o identificador de Java em uma instância do objeto fortemente tipado.

<a name="_Binding_Constructors" />

### <a name="binding-constructors"></a>Construtores de associação

Construtores são os métodos de Java com o nome `"<init>"`. Assim como ocorre com métodos de instância de Java `JNIEnv.GetMethodID` é usado para pesquisar o identificador de construtor. Ao contrário dos métodos de Java, o [JNIEnv.NewObject](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.NewObject/) métodos são usados para chamar o identificador do método de construtor. O valor de retorno de `JNIEnv.NewObject` é uma referência de local de JNI:


```csharp
int value = 42;
IntPtr class_ref    = JNIEnv.FindClass ("java/lang/Integer");
IntPtr id_ctor_I    = JNIEnv.GetMethodID (class_ref, "<init>", "(I)V");
IntPtr lrefInstance = JNIEnv.NewObject (class_ref, id_ctor_I, new JValue (value));
// Dispose of lrefInstance, class_ref…
```

Normalmente uma associação de classe será subclasse [Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/).
Quando subclasses `Java.Lang.Object`, um adicionais semântica entra: um `Java.Lang.Object` instância mantém uma referência global para uma instância de Java por meio de `Java.Lang.Object.Handle` propriedade.

1.  O `Java.Lang.Object` construtor padrão alocará uma instância de Java.

1.  Se o tipo tiver um `RegisterAttribute` , e `RegisterAttribute.DoNotGenerateAcw` é `true` , em seguida, uma instância do `RegisterAttribute.Name` tipo é criado por meio de seu construtor padrão.

1.  Caso contrário, o [Android Callable Wrapper](~/android/platform/java-integration/android-callable-wrappers.md) (ACW) correspondente ao `this.GetType` é instanciado por meio de seu construtor padrão. Android Callable Wrappers gerados durante a criação de pacote para cada `Java.Lang.Object` subclasse para o qual `RegisterAttribute.DoNotGenerateAcw` não está definido como `true`.

Para tipos de classe não associações, esse é o esperado semântica: Criando um `Mono.Samples.HelloWorld.HelloAndroid` c# instância deve construir um Java `mono.samples.helloworld.HelloAndroid` instância que é um Android Callable Wrapper gerado.

Para associações de classe, isso pode ser o comportamento correto, se o tipo de Java contém um construtor padrão e/ou nenhum outro construtor deve ser invocado. Caso contrário, deve ser fornecido um construtor que executa as seguintes ações:

1.  Invocar o [Java.Lang.Object (IntPtr, JniHandleOwnership)](https://developer.xamarin.com/api/constructor/Java.Lang.Object.Object/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) em vez do padrão `Java.Lang.Object` construtor. Isso é necessário para evitar a criação de uma nova instância de Java.

1.  Verifique o valor da [Java.Lang.Object.Handle](https://developer.xamarin.com/api/property/Java.Lang.Object.Handle/) antes de criar quaisquer instâncias de Java. O `Object.Handle` propriedade terá um valor diferente de `IntPtr.Zero` se um Callable Wrapper Android foi construído no código Java e a associação de classe está sendo criada para conter a instância criada do Android Callable Wrapper. Por exemplo, quando o Android cria um `mono.samples.helloworld.HelloAndroid` instância, o Android Callable Wrapper será criado primeiro e o Java `HelloAndroid` construtor criará uma instância do `Mono.Samples.HelloWorld.HelloAndroid` tipo, com o `Object.Handle` propriedade sendo definido para a instância de Java antes da execução do construtor.

1.  Se o tipo de tempo de execução atual não é o mesmo que a declaração de tipo, em seguida, uma instância do Android Callable Wrapper correspondente deve ser criado e use [Object.SetHandle](https://developer.xamarin.com/api/member/Java.Lang.Object.SetHandle/(System.IntPtr%2cAndroid.Runtime.JniHandleOwnership)) para armazenar o identificador retornado por [ JNIEnv.CreateInstance](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CreateInstance/).

1.  Se o tipo de tempo de execução atual é o mesmo que o tipo de declaração, em seguida, chamar o construtor de Java e use [Object.SetHandle](https://developer.xamarin.com/api/member/Java.Lang.Object.SetHandle/(System.IntPtr%2cAndroid.Runtime.JniHandleOwnership)) para armazenar o identificador retornado por `JNIEnv.NewInstance` .


Por exemplo, considere o [java.lang.Integer(int)](http://developer.android.com/reference/java/lang/Integer.html#Integer(int)) construtor. Isso é vinculado como:

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

 <a name="_Supporting_Inheritance,_Interfaces" />


### <a name="supporting-inheritance-interfaces"></a>O suporte a herança, Interfaces

Subclasses de um tipo de Java ou implementar uma interface Java requer a geração de [Android Callable Wrappers](~/android/platform/java-integration/android-callable-wrappers.md) (ACWs) que são gerados para cada `Java.Lang.Object` subclasse durante o processo de empacotamento. Geração de ACW é controlada por meio de [Android.Runtime.RegisterAttribute](https://developer.xamarin.com/api/type/Android.Runtime.RegisterAttribute/) atributo personalizado.

Para tipos c#, o `[Register]` construtor de atributo personalizado requer um argumento: o [JNI simplificado referência de tipo](#_Simplified_Type_References_1) para o Java correspondente de tipo. Isso permite fornecer nomes diferentes entre Java e c#.

Antes de xamarin 4.0, o `[Register]` atributo personalizado não estava disponível para tipos de Java "alias" existentes. Isso ocorre porque o processo de geração de ACW produziria ACWs para cada `Java.Lang.Object` subclasse encontrado.

Xamarin 4.0 introduzida o [RegisterAttribute.DoNotGenerateAcw](https://developer.xamarin.com/api/property/Android.Runtime.RegisterAttribute.DoNotGenerateAcw/) propriedade. Esta propriedade instrui o processo de geração ACW *ignorar* o tipo anotado, permitindo que a declaração do novo gerenciados Callable Wrappers que não resultará em ACWs sendo gerados no momento da criação de pacote. Isso permite que os tipos de Java existentes da associação. Por exemplo, considere a seguinte classe Java simple, `Adder`, que contém um método, `add`, que adiciona a inteiros e retorna o resultado:

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

Aqui, o `Adder` tipo c# *aliases* o `Adder` tipo Java. O `[Register]` atributo é usado para especificar o nome JNI do `mono.android.test.Adder` tipo Java e o `DoNotGenerateAcw` propriedade é usada para inibir geração ACW. Isso resulta na geração de um ACW para o `ManagedAdder` tipo, que corretamente subclasses de `mono.android.test.Adder` tipo. Se o `RegisterAttribute.DoNotGenerateAcw` propriedade não tivesse sido usada, em seguida, o processo de build xamarin teria gerado um novo `mono.android.test.Adder` tipo Java. Isso resulta em erros de compilação, como o `mono.android.test.Adder` tipo estaria presente duas vezes em dois arquivos separados.

 <a name="_Binding_Virtual_Methods" />


### <a name="binding-virtual-methods"></a>Associação de métodos virtuais

`ManagedAdder` as subclasses de Java `Adder` tipo, mas ele não é especialmente interessante: c# `Adder` tipo não define qualquer métodos virtuais, portanto `ManagedAdder` não pode substituir qualquer coisa.

Associação `virtual` métodos para permitir a substituição por subclasses requer várias coisas que precisam ser feitas a que se enquadram em duas categorias a seguir:

1.  **Vinculação de método**

1.  **Método de registro**

<a name="_Method_Binding" />

#### <a name="method-binding"></a>Vinculação de método

Uma associação de método requer a adição de dois membros de suporte para o c# `Adder` definição: `ThresholdType`, e `ThresholdClass`.

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

`ThresholdType` é usado em associação de método para determinar quando deve executar virtual versus despacho de método não virtual. Ele sempre deve retornar um `System.Type` instância que corresponde ao tipo c# declarativo.

##### <a name="thresholdclass"></a>ThresholdClass

O `ThresholdClass` propriedade retorna a referência de classe JNI para o tipo de limite:

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

#### <a name="binding-implementation"></a>Implementação de associação

A implementação de associação do método é responsável por invocação de tempo de execução do método Java. Ele também contém um `[Register]` declaração de atributo personalizado que faz parte do registro de método e será abordada na seção do registro do método:

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

O `id_add` campo contém a ID do método para o método de Java para chamar. O `id_add` valor é obtido do `JNIEnv.GetMethodID`, que requer a declaração de classe (`class_ref`), o nome do método Java (`"add"`) e a assinatura do método de JNI (`"(II)I"`).

Depois de obter a identificação do método, `GetType` é comparado ao `ThresholdType` para determinar se a expedição de máquina virtual ou não é necessária. Expedição virtual é necessária quando `GetType` corresponde `ThresholdType`, como `Handle` podem se referir a uma subclasse alocada Java que substitui o método.

Quando `GetType` não corresponde `ThresholdType`, `Adder` tenha sido derivado (, por exemplo `ManagedAdder`) e o `Adder.Add` implementação só será chamada se a subclasse chamado `base.Add`. Esse é o caso de expedição de máquina virtual, que é onde `ThresholdClass` entram em ação. `ThresholdClass` Especifica qual classe Java fornecerá a implementação do método a ser invocado.

 <a name="_Method_Registration" />


#### <a name="method-registration"></a>Método de registro

Suponha que você tenha atualizada `ManagedAdder` definição que substitui o `Adder.Add` método:

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
    Métodos de conector serão abordados posteriormente.


Os dois primeiros parâmetros permitem que o processo de geração de ACW gerar uma declaração de método para substituir o método. O ACW resultante conterá alguns o código a seguir:

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

Observe que uma `@Override` método for declarado, que delega para um `n_`-prefixados método o mesmo nome. Isso certifique-se de que quando o código Java invoca `ManagedAdder.add`, `ManagedAdder.n_add` será invocado, que permitirá a substituição c# `ManagedAdder.Add` método a ser executado.

Portanto, a questão mais importante: como é `ManagedAdder.n_add` conectado à `ManagedAdder.Add`?

Java `native` métodos são registrados com o tempo de execução Java (o tempo de execução Android) por meio de [RegisterNatives JNI função](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html#wp17734).
`RegisterNatives` usa uma matriz de estruturas que contém o nome do método Java, a assinatura de tipo JNI e um ponteiro de função para chamar segue [JNI convenção de chamada](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/design.html#wp715).
O ponteiro de função deve ser uma função que leva dois argumentos de ponteiro seguidos pelos parâmetros de método. O Java `ManagedAdder.n_add` método deve ser implementado por meio de uma função que tem o seguinte protótipo de C:

```csharp
int FunctionName(JNIEnv *env, jobject this, int a, int b)
```

Xamarin não expõe um `RegisterNatives` método. Em vez disso, o ACW e o MCW juntos fornecem as informações necessárias para invocar `RegisterNatives`: o ACW contém o nome do método e a assinatura de tipo JNI, a única coisa ausente é um ponteiro de função para ligar.

Isso é onde o *método conector* entram em ação. O terceiro `[Register]` parâmetro de atributo personalizado é o nome de um método definido no tipo registrado ou uma classe base do tipo registrado que não aceita parâmetros e retorna um `System.Delegate`. Retornado `System.Delegate` por sua vez se refere a um método que tem a assinatura de função JNI correta. Por fim, o delegado que retorna o método conector *deve* ter raiz para que o GC não coletá-los, como o representante que está sendo fornecido para Java.

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

O `GetAddHandler` método cria um `Func<IntPtr, IntPtr, int, int,
int>` delegado que refere-se para o `n_Add` , em seguida, invoca um método, [JNINativeWrapper.CreateDelegate](https://developer.xamarin.com/api/member/Android.Runtime.JNINativeWrapper.CreateDelegate/).
`JNINativeWrapper.CreateDelegate` encapsula o método fornecido em um bloco try/catch, para que todas as exceções sem tratamento são tratadas e resultarão em gerando o [AndroidEvent.UnhandledExceptionRaiser](https://developer.xamarin.com/api/event/Android.Runtime.AndroidEnvironment.UnhandledExceptionRaiser/) eventos. O representante resultante é armazenado no estático `cb_add` variável para que o GC não liberará o delegado.

Por fim, o `n_Add` método é responsável por marshaling de parâmetros JNI para os tipos gerenciados correspondentes, em seguida, chame o método de delegação.

Observação: Use sempre `JniHandleOwnership.DoNotTransfer` ao obter um MCW por uma instância de Java. Tratá-los como uma referência local (e, portanto, chamando `JNIEnv.DeleteLocalRef`) será interrompido gerenciado -&gt; Java -&gt; pilha transições gerenciadas.

 <a name="_Complete_Adder_Binding" />


### <a name="complete-adder-binding"></a>Concluir Adicionador de associação

Completo gerenciados associação para o `mono.android.tests.Adder` é do tipo:

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

 <a name="_Restrictions" />


### <a name="restrictions"></a>Restrições

Ao escrever um tipo que corresponde aos seguintes critérios:

1.  Subclasses `Java.Lang.Object`

1.  Tem um `[Register]` atributo personalizado

1.  `RegisterAttribute.DoNotGenerateAcw` é `true`


Em seguida, para o tipo de interação de GC *não* ter todos os campos que podem se referir a um `Java.Lang.Object` ou `Java.Lang.Object` subclasse em tempo de execução. Por exemplo, os campos do tipo `System.Object` e qualquer tipo de interface não são permitidos. Tipos que não é possível fazer referência a `Java.Lang.Object` instâncias são permitidas, tais como `System.String` e `List<int>`. Essa restrição é para evitar a coleta de objeto prematuro pelo GC.

Se o tipo deve conter um campo de instância pode se referir a um `Java.Lang.Object` instância, em seguida, o tipo de campo deve ser `System.WeakReference` ou `GCHandle`.


 <a name="_Binding_Abstract_Methods" />

## <a name="binding-abstract-methods"></a>Métodos abstratos de associação

Associação `abstract` métodos é praticamente idêntica à associação de métodos virtuais. Há apenas duas diferenças:

1.  O método abstract é abstrato. Ela ainda mantém o `[Register]` atributo e o registro de método associado, apenas o método de associação é movido para o `Invoker` tipo.

1.  Não `abstract` `Invoker` tipo é criado que herda do tipo abstrato. O `Invoker` tipo deve substituir os métodos abstratos todos os declarado na classe base, e a implementação substituída é a implementação do método de associação, embora o caso de despacho não virtual pode ser ignorado.


Por exemplo, suponha que o acima `mono.android.test.Adder.add` método `abstract`. Altere a associação do c# para que `Adder.Add` foram abstract e um novo `AdderInvoker` tipo seria definido que implementado `Adder.Add`:

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

 <a name="_Binding_Interfaces" />

## <a name="binding-interfaces"></a>Interfaces de associação

Interfaces de associação é conceitualmente semelhante à associação de classes que contêm métodos virtuais, mas muitas das especificações diferem de maneiras sutis (e não tão sutis). Considere o seguinte [declaração de interface de Java](https://github.com/xamarin/monodroid-samples/blob/master/SanityTests/Adder.java#L14):

```csharp
public interface Progress {
    void onAdd(int[] values, int currentIndex, int currentSum);
}
```

Associações de interface têm duas partes: a definição de interface c# e uma definição de chamador para a interface.


<a name="_Interface_Definition" />

### <a name="interface-definition"></a>Definição de interface

A definição da interface c# deve atender aos seguintes requisitos:

-   A definição de interface deve ter um `[Register]` atributo personalizado.

-   A definição de interface deve estender o `IJavaObject interface`.
    Se isso não impedirá que ACWs de herdar da interface Java.

-   Cada método de interface deve conter um `[Register]` atributo especificando o nome do método Java correspondente, a assinatura JNI e o método de conector.

-   O método de conector também deve especificar o tipo que o método do conector pode estar localizado em.

Ao associar `abstract` e `virtual` métodos, o método do conector deve ser pesquisados dentro da hierarquia de herança do tipo que está sendo registrado. Interfaces não podem ter nenhum método que contém o corpo, para que isso não funcionar, portanto, o requisito de que seja especificado um tipo que indica onde se encontra o método de conector. O tipo é especificado na cadeia de caracteres de método conector, depois de dois-pontos `':'`, e deve ser o nome de tipo qualificado de assembly do tipo que contém o chamador.

Declarações de método de interface são uma tradução do correspondente método Java usando *compatível* tipos. Para tipos internos de Java, os tipos compatíveis são os c# tipos de, por exemplo, Java `int` for c# `int`. Para tipos de referência, o tipo compatível é um tipo que pode fornecer um identificador JNI do tipo apropriado de Java.

Os membros de interface não serão invocados diretamente em Java &ndash; invocação será seja mediada por tipo de chamador &ndash; para alguma quantidade de flexibilidade é permitida.

A interface de andamento do Java pode ser [declarado em c# como](https://github.com/xamarin/monodroid-samples/blob/master/SanityTests/ManagedAdder.cs#L83):

```csharp
[Register ("mono/android/test/Adder$Progress", DoNotGenerateAcw=true)]
public interface IAdderProgress : IJavaObject {
    [Register ("onAdd", "([III)V",
            "GetOnAddHandler:Mono.Samples.SanityTests.IAdderProgressInvoker, SanityTests, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null")]
    void OnAdd (JavaArray<int> values, int currentIndex, int currentSum);
}
```

Observe as opções acima que nós de mapa de Java `int[]` parâmetro para um [JavaArray&lt;int&gt;](https://developer.xamarin.com/api/type/Android.Runtime.JavaArray%601/).
Isso não é necessário: nós foi ele foi associado a um c# `int[]`, ou um `IList<int>`, ou algo completamente. O tipo é escolhido, o `Invoker` precisa ser capaz de traduzi-lo em um Java `int[]` tipo de invocação.

<a name="_Invoker_Definition" />

### <a name="invoker-definition"></a>Definição de chamador

O `Invoker` definição de tipo deve herdar `Java.Lang.Object`, implementar a interface adequada e fornecer todos os métodos de conexão referenciados na definição de interface. Há uma sugestão mais que difere de uma associação de classe: o `class_ref` IDs de campo e de método devem ser membros de instância, não membros estáticos.

O motivo para preferindo membros de instância tem a ver com `JNIEnv.GetMethodID` comportamento em tempo de execução do Android. (Isso pode ser o comportamento do Java também; ele não foi testado). `JNIEnv.GetMethodID` retorna null ao procurar um método que vêm de uma interface implementada e não a interface declarada. Considere o [java.util.SortedMap&lt;K, V&gt; ](http://developer.android.com/reference/java/util/SortedMap.html) interface Java, que implementa o [java.util.Map&lt;K, V&gt; ](http://developer.android.com/reference/java/util/Map.html) interface. O mapa fornece um [limpar](http://developer.android.com/reference/java/util/Map.html#clear()) método, portanto, um aparentemente razoável `Invoker` para SortedMap poderia ser:

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

Há duas soluções para isso: controlar qual interface proveniente de cada método e ter um `class_ref` para cada interface, ou mantenha tudo como membros de instância e executar a pesquisa de método no tipo de classe mais derivado, e não do tipo de interface. O segundo é feito em **Mono.Android.dll**.

A definição de chamador tem seis seções: o construtor de `Dispose` método, o `ThresholdType` e `ThresholdClass` membros, o `GetObject` método, a implementação do método de interface e a implementação do método de conector.

 <a name="_Constructor" />


#### <a name="constructor"></a>Construtor

O construtor deve pesquisar a classe de tempo de execução da instância que está sendo invocada e armazenar a classe de tempo de execução na instância `class_ref` campo:

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

Observação: O `Handle` propriedade deve ser usada dentro do corpo de construtor e não o `handle` parâmetro, como na v 4.0 Android a `handle` parâmetro pode ser inválido após o construtor base conclui a execução.

<a name="_Dispose_Method" />

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

<a name="_ThresholdType_and_ThresholdClass" />

#### <a name="thresholdtype-and-thresholdclass"></a>ThresholdType e ThresholdClass

O `ThresholdType` e `ThresholdClass` membros são idênticos às que se encontra em uma associação de classe:

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

<a name="_GetObject_Method" />

#### <a name="getobject-method"></a>Método GetObject

Static `GetObject` método é necessário para suportar [Extensions.JavaCast&lt;T&gt;()](https://developer.xamarin.com/api/member/Android.Runtime.Extensions.JavaCast%7BTResult%7D/p/Android.Runtime.IJavaObject/):

```csharp
partial class IAdderProgressInvoker {
    public static IAdderProgress GetObject (IntPtr handle, JniHandleOwnership transfer)
    {
        return new IAdderProgressInvoker (handle, transfer);
    }
}
```

<a name="_Interface_Methods" />

#### <a name="interface-methods"></a>Métodos de interface

Cada método da interface precisa ter uma implementação, que chama o método correspondente de Java por meio de JNI:

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

 <a name="_Connector_Methods" />


#### <a name="connector-methods"></a>Métodos de conector

Os métodos de conector e a infraestrutura de suporte são responsáveis por marshaling os parâmetros JNI para tipos c# apropriados. O Java `int[]` parâmetro será passado como um JNI `jintArray`, que é um `IntPtr` em c#. O `IntPtr` deve ser empacotado para um `JavaArray<int>` para oferecer suporte ao invocar a interface c#:

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

Se `int[]` seria preferencial sobre `JavaList<int>`, em seguida, [JNIEnv.GetArray()](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetArray/(System.IntPtr%2cAndroid.Runtime.JniHandleOwnership%2cSystem.Type)) pode ser usado em vez disso:

```csharp
int[] _values = (int[]) JNIEnv.GetArray(values, JniHandleOwnership.DoNotTransfer, typeof (int));
```

No entanto, observe que `JNIEnv.GetArray` copia a matriz inteira entre VMs, portanto para matrizes grandes, isso pode resultar em muita pressão de GC adicionado.

<a name="_Complete_Invoker_Definition" />

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

 <a name="_JNI_Object_References" />


## <a name="jni-object-references"></a>Referências de objeto JNI

Muitos métodos JNIEnv retornam *JNI* *referências de objeto*, que são semelhante às `GCHandle`s. JNI fornece três tipos diferentes de referências de objeto: referências locais, referências globais e referências fracas e global. Todos os três são representados como `System.IntPtr`, *mas* (de acordo com a seção de tipos de função de JNI) nem todos os `IntPtr`s retornado de `JNIEnv` métodos são referências. Por exemplo, [JNIEnv.GetMethodID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetMethodID/) retorna um `IntPtr`, mas ele não retorna uma referência de objeto, ele retorna um `jmethodID`. Consulte o [documentação de função JNI](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html) para obter detalhes.

Referências locais são criadas por *mais* métodos de criação de referência.
Android permite apenas um número limitado de referências de locais existir a qualquer momento determinado, geralmente 512. Referências de locais podem ser excluídas por meio de [JNIEnv.DeleteLocalRef](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.DeleteLocalRef/).
Ao contrário de JNI, nem todos os referenciam métodos JNIEnv quais referências de objeto de retorno retornam referências de locais; [JNIEnv.FindClass](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.FindClass/) retorna um *global* referência. É altamente recomendável que você exclua referências locais mais rápido possível, possivelmente, criando um [Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/) em torno do objeto e especificando `JniHandleOwnership.TransferLocalRef` para o [Java.Lang.Object (IntPtr Identificador de transferência JniHandleOwnership)](https://developer.xamarin.com/api/constructor/Java.Lang.Object.Object/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) construtor.

Referências globais são criadas por [JNIEnv.NewGlobalRef](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.NewGlobalRef/) e [JNIEnv.FindClass](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.FindClass/).
Eles podem ser destruídos com [JNIEnv.DeleteGlobalRef](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.DeleteGlobalRef/).
Emuladores têm um limite de 2.000 referências global pendentes, enquanto os dispositivos de hardware têm um limite de aproximadamente 52.000 referências global.

Referências fracas e globais estão disponíveis somente no Android v. 2.2 (Froyo) e posterior. Referências fracas e globais podem ser excluídas com [JNIEnv.DeleteWeakGlobalRef](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.DeleteWeakGlobalRef/(System.IntPtr)).

<a name="_Dealing_With_JNI_Local_References" />

### <a name="dealing-with-jni-local-references"></a>Lidando com referências de Local de JNI

O [JNIEnv.GetObjectField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetObjectField/), [JNIEnv.GetStaticObjectField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticObjectField/), [JNIEnv.CallObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallObjectMethod/), [JNIEnv.CallNonvirtualObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualObjectMethod/)e [JNIEnv.CallStaticObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticObjectMethod/) métodos retornam um `IntPtr` que contém uma referência de local de JNI para um objeto de Java, ou `IntPtr.Zero` se Java retornado `null`. Devido ao número limitado de referências de locais que pode estar pendente na depois (512 entradas), que é desejável para garantir que as referências são excluídos de maneira oportuna. Referências de locais podem ser tratadas de três maneiras: explicitamente excluí-los, criando um `Java.Lang.Object` instância para mantê-los e usar `Java.Lang.Object.GetObject<T>()` para criar um wrapper gerenciado que pode ser chamado ao redor deles.

 <a name="_Explicitly_Deleting_Local_References" />


### <a name="explicitly-deleting-local-references"></a>Excluindo explicitamente referências locais

[JNIEnv.DeleteLocalRef](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.DeleteLocalRef/) é usado para excluir referências de locais. Depois que a referência local tiver sido excluída, ele não pode ser usado, portanto deve tomar cuidado para garantir que `JNIEnv.DeleteLocalRef` é a última coisa feita com a referência de local.

```csharp
IntPtr lref = JNIEnv.CallObjectMethod(instance, methodID);
try {
    // Do something with `lref`
}
finally {
    JNIEnv.DeleteLocalRef (lref);
}
```

 <a name="_Wrapping_with_Java.Lang.Object" />


### <a name="wrapping-with-javalangobject"></a>Encapsulamento com Java.Lang.Object

`Java.Lang.Object` Fornece um [Java.Lang.Object (identificador de IntPtr, transferência de JniHandleOwnership)](https://developer.xamarin.com/api/constructor/Java.Lang.Object.Object/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) construtor que pode ser usado para incluir uma referência JNI existente. O [JniHandleOwnership](https://developer.xamarin.com/api/type/Android.Runtime.JniHandleOwnership/) parâmetro determina como o `IntPtr` deve ser tratado de parâmetro:

-   [JniHandleOwnership.DoNotTransfer](https://developer.xamarin.com/api/field/Android.Runtime.JniHandleOwnership.DoNotTransfer/) &ndash; criados `Java.Lang.Object` instância criará uma nova referência global do `handle` parâmetro, e `handle` é alterada.
    O chamador é responsável por liberando `handle` , se necessário.

-   [JniHandleOwnership.TransferLocalRef](https://developer.xamarin.com/api/field/Android.Runtime.JniHandleOwnership.TransferLocalRef/) &ndash; criados `Java.Lang.Object` instância criará uma nova referência global do `handle` parâmetro, e `handle` é excluída com [JNIEnv.DeleteLocalRef ](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.DeleteLocalRef/) . O chamador não deve liberar `handle` e não devem usar `handle` depois que o construtor conclui a execução.

-   [JniHandleOwnership.TransferGlobalRef](https://developer.xamarin.com/api/field/Android.Runtime.JniHandleOwnership.TransferLocalRef/) &ndash; criados `Java.Lang.Object` instância será assumir a propriedade do `handle` parâmetro. O chamador não deve liberar `handle` .


Como os métodos de invocação de método JNI retornam referências de locais `JniHandleOwnership.TransferLocalRef` seriam usados normalmente:

```csharp
IntPtr lref = JNIEnv.CallObjectMethod(instance, methodID);
var value = new Java.Lang.Object (lref, JniHandleOwnership.TransferLocalRef);
```

A referência global criada não será liberada até que o `Java.Lang.Object` instância é coletado como lixo. Se você conseguir, descarte da instância irá liberar a referência global, acelerando a coletas de lixo:

```csharp
IntPtr lref = JNIEnv.CallObjectMethod(instance, methodID);
using (var value = new Java.Lang.Object (lref, JniHandleOwnership.TransferLocalRef)) {
    // use value ...
}
```

 <a name="_Using_Java.Lang.Object.GetObject&lt;T&gt;()" />


### <a name="using-javalangobjectgetobjectlttgt"></a>Using Java.Lang.Object.GetObject&lt;T&gt;()

`Java.Lang.Object` Fornece um [Java.Lang.Object.GetObject&lt;T&gt;(identificador de IntPtr, transferência de JniHandleOwnership)](https://developer.xamarin.com/api/member/Java.Lang.Object.GetObject%7BT%7D/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) método que pode ser usado para criar um wrapper gerenciado que pode ser chamado do tipo especificado.

O tipo `T` devem ser atendidos os seguintes requisitos:

1.  `T` deve ser um tipo de referência.

1.  `T` deve implementar o `IJavaObject` interface.

1.  Se `T` não é uma classe abstract ou interface, em seguida, `T` deve fornecer um construtor com os tipos de parâmetro `(IntPtr,
    JniHandleOwnership)` .

1.  Se `T` é uma classe abstrata ou uma interface, há *deve* ser um *chamador* disponível para `T` . Um chamador é um tipo não abstrato herdado `T` ou implementa `T` , e tem o mesmo nome como `T` com um sufixo do chamador. Por exemplo, se T é a interface `Java.Lang.IRunnable` , em seguida, o tipo `Java.Lang.IRunnableInvoker` deve existir e deve conter o necessária `(IntPtr,
    JniHandleOwnership)` construtor.


Como os métodos de invocação de método JNI retornam referências de locais `JniHandleOwnership.TransferLocalRef` seriam usados normalmente:

```csharp
IntPtr lrefString = JNIEnv.CallObjectMethod(instance, methodID);
Java.Lang.String value = Java.Lang.Object.GetObject<Java.Lang.String>( lrefString, JniHandleOwnership.TransferLocalRef);
```

<a name="_Looking_up_Java_Types" />

## <a name="looking-up-java-types"></a>Pesquisar tipos de Java

Para pesquisar um campo ou método JNI, o tipo de declaração para o campo ou método deve ser pesquisado primeiro. O [Android.Runtime.JNIEnv.FindClass(string)](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.FindClass/(System.String)) método é usado para pesquisar tipos de Java. O parâmetro de cadeia de caracteres é o *simplificado de referência de tipo* ou *referência de tipo completo* para o tipo de Java. Consulte o [seção referências de tipo de JNI](#_JNI_Type_References) para obter detalhes sobre referências de tipo simplificado e completo.

Observação: ao contrário de todas as outras `JNIEnv` método que retorna instâncias de objeto, `FindClass` retorna uma referência global, não uma referência local.

<a name="_Instance_Fields" />

## <a name="instance-fields"></a>Campos de instância

Campos são manipulados por meio de *campo IDs*. IDs de campo são obtidos por meio de [JNIEnv.GetFieldID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetFieldID/), que requer que a classe que o campo está definido no, o nome do campo e o [assinatura de tipo de JNI](#_JNI_Type_Signatures) do campo.

IDs de campo não precisam ser liberado e são válidos como o tipo Java correspondente é carregado. (Android não atualmente suporta o descarregamento de classe.)

Há dois conjuntos de métodos para manipular os campos de instância: um para a leitura de campos de instância e outro para a gravação de campos de instância. Todos os conjuntos de métodos requerem uma ID de campo ler ou gravar o valor do campo.

<a name="_Reading_Instance_Field_Values" />

### <a name="reading-instance-field-values"></a>Valores de campo de instância de leitura

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



 <a name="_Writing_Instance_Field_Values" />


### <a name="writing-instance-field-values"></a>Gravando valores de campo de instância

O conjunto de métodos para gravar valores de campo de instância segue o padrão de nomenclatura:

```csharp
JNIEnv.SetField(IntPtr instance, IntPtr fieldID, Type value);
```

onde *tipo* é o tipo do campo:

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.IntPtr)) &ndash; gravar o valor de qualquer campo que não é um tipo interno, como `java.lang.Object` , matrizes e tipos de interface. O `IntPtr` valor pode ser uma referência de local de JNI, referência global JNI, referência de global de JNI fraca, ou `IntPtr.Zero` (para `null` ).

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

Campos estáticos são manipulados por meio de *campo IDs*. IDs de campo são obtidos por meio de [JNIEnv.GetStaticFieldID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticFieldID/), que requer que a classe que o campo está definido no, o nome do campo e o [assinatura de tipo de JNI](#JNI%20Type%20Signatures) do campo.

IDs de campo não precisam ser liberado e são válidos como o tipo Java correspondente é carregado. (Android não atualmente suporta o descarregamento de classe.)

Há dois conjuntos de métodos para manipular campos estáticos: uma para ler os campos de instância e outro para a gravação de campos de instância. Todos os conjuntos de métodos requerem uma ID de campo ler ou gravar o valor do campo.

<a name="_Reading_Static_Field_Values" />

### <a name="reading-static-field-values"></a>Ler os valores de campo estático

O conjunto de métodos para ler valores do campo estático segue o padrão de nomenclatura:

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


<a name="_Writing_Static_Field_Values" />

### <a name="writing-static-field-values"></a>Gravando valores de campo estático

O conjunto de métodos para gravar valores de campo estático segue o padrão de nomenclatura:

```csharp
JNIEnv.SetStaticField(IntPtr class, IntPtr fieldID, Type value);
```

onde *tipo* é o tipo do campo:

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.IntPtr)) &ndash; gravar o valor de qualquer campo estático que não é um tipo interno, como `java.lang.Object` , matrizes e tipos de interface. O `IntPtr` valor pode ser uma referência de local de JNI, referência global JNI, referência de global de JNI fraca, ou `IntPtr.Zero` (para `null` ).

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

Métodos de instância são invocados por meio de *método IDs*. Método IDs são obtidas por meio de [JNIEnv.GetMethodID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetMethodID/), que requer que o tipo que o método é definido no, o nome do método e o [assinatura de tipo de JNI](#_JNI_Type_Signatures) do método.

Método IDs não precisam ser liberado e são válidos como o tipo Java correspondente é carregado. (Android não atualmente suporta o descarregamento de classe.)

Há dois conjuntos de métodos para chamar métodos: uma para invocar métodos virtualmente e outra para invocar métodos não virtualmente. Os dois conjuntos de métodos requerem uma ID do método invocar o método e não virtual invocação também requer que você especifique qual implementação de classe deve ser chamada.

Métodos de interface só podem ser pesquisados no tipo de declaração; métodos que vêm de interfaces de estendida/herdado não podem ser pesquisados. Consulte as Interfaces de associação posterior / implementação de chamador de seção para obter mais detalhes.

Qualquer método declarado na classe ou qualquer classe base ou interface implementada pode ser pesquisado.

<a name="_Virtual_Method_Invocation" />

### <a name="virtual-method-invocation"></a>Invocação de método virtual

O conjunto de métodos para chamar métodos praticamente segue o padrão de nomenclatura:

```csharp
* JNIEnv.Call*Method( IntPtr instance, IntPtr methodID, params JValue[] args );
```

onde `*` é o tipo de retorno do método.

-   [JNIEnv.CallObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallObjectMethod/) &ndash; invocar um método que retorna um tipo interno não, como `java.lang.Object` , matrizes e interfaces. O valor retornado é uma referência de local de JNI.

-   [JNIEnv.CallBooleanMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallBooleanMethod/) &ndash; invocar um método que retorna um `bool` valor.

-   [JNIEnv.CallByteMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallByteMethod/) &ndash; invocar um método que retorna um `sbyte` valor.

-   [JNIEnv.CallCharMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallCharMethod/) &ndash; invocar um método que retorna um `char` valor.

-   [JNIEnv.CallShortMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallShortMethod/) &ndash; invocar um método que retorna um `short` valor.

-   [JNIEnv.CallLongMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallLongMethod/) &ndash; invocar um método que retorna um `long` valor.

-   [JNIEnv.CallFloatMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallFloatMethod/) &ndash; invocar um método que retorna um `float` valor.

-   [JNIEnv.CallDoubleMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallDoubleMethod/) &ndash; invocar um método que retorna um `double` valor.


<a name="_Non-virtual_Method_Invocation" />

### <a name="non-virtual-method-invocation"></a>Invocação de método não virtual

O conjunto de métodos para chamar métodos não praticamente segue o padrão de nomenclatura:

```csharp
* JNIEnv.CallNonvirtual*Method( IntPtr instance, IntPtr class, IntPtr methodID, params JValue[] args );
```

onde `*` é o tipo de retorno do método. Invocação de método não virtual normalmente é usada para chamar o método de base de um método virtual.

-   [JNIEnv.CallNonvirtualObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualObjectMethod/) &ndash; não praticamente invocar um método que retorna um tipo interno não, como `java.lang.Object` , matrizes e interfaces. O valor retornado é uma referência de local de JNI.

-   [JNIEnv.CallNonvirtualBooleanMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualBooleanMethod/) &ndash; não praticamente invocar um método que retorna um `bool` valor.

-   [JNIEnv.CallNonvirtualByteMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualByteMethod/) &ndash; não praticamente invocar um método que retorna um `sbyte` valor.

-   [JNIEnv.CallNonvirtualCharMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualCharMethod/) &ndash; não praticamente invocar um método que retorna um `char` valor.

-   [JNIEnv.CallNonvirtualShortMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualShortMethod/) &ndash; não praticamente invocar um método que retorna um `short` valor.

-   [JNIEnv.CallNonvirtualLongMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualLongMethod/) &ndash; não praticamente invocar um método que retorna um `long` valor.

-   [JNIEnv.CallNonvirtualFloatMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualFloatMethod/) &ndash; não praticamente invocar um método que retorna um `float` valor.

-   [JNIEnv.CallNonvirtualDoubleMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualDoubleMethod/) &ndash; não praticamente invocar um método que retorna um `double` valor.


 <a name="_Static_Methods" />

## <a name="static-methods"></a>Métodos estáticos

Métodos estáticos são invocados por meio de *método IDs*. Método IDs são obtidas por meio de [JNIEnv.GetStaticMethodID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticMethodID/), que requer que o tipo que o método é definido no, o nome do método e o [assinatura de tipo de JNI](#JNI%20Type%20Signatures) do método.

Método IDs não precisam ser liberado e são válidos como o tipo Java correspondente é carregado. (Android não atualmente suporta o descarregamento de classe.)

 <a name="_Static_Method_Invocation" />


### <a name="static-method-invocation"></a>Invocação de método estático

O conjunto de métodos para chamar métodos praticamente segue o padrão de nomenclatura:

```csharp
* JNIEnv.CallStatic*Method( IntPtr class, IntPtr methodID, params JValue[] args );
```

onde `*` é o tipo de retorno do método.

-   [JNIEnv.CallStaticObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticObjectMethod/) &ndash; invocar um método estático que retorna um tipo interno não, como `java.lang.Object` , matrizes e interfaces. O valor retornado é uma referência de local de JNI.

-   [JNIEnv.CallStaticBooleanMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticBooleanMethod/) &ndash; invocar um método estático que retorna um `bool` valor.

-   [JNIEnv.CallStaticByteMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticByteMethod/) &ndash; invocar um método estático que retorna um `sbyte` valor.

-   [JNIEnv.CallStaticCharMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticCharMethod/) &ndash; invocar um método estático que retorna um `char` valor.

-   [JNIEnv.CallStaticShortMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticShortMethod/) &ndash; invocar um método estático que retorna um `short` valor.

-   [JNIEnv.CallStaticLongMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallLongMethod/) &ndash; invocar um método estático que retorna um `long` valor.

-   [JNIEnv.CallStaticFloatMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticFloatMethod/) &ndash; invocar um método estático que retorna um `float` valor.

-   [JNIEnv.CallStaticDoubleMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticDoubleMethod/) &ndash; invocar um método estático que retorna um `double` valor.


 <a name="_JNI_Type_Signatures" />


## <a name="jni-type-signatures"></a>Assinaturas de tipo JNI

[Assinaturas de tipo de JNI](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/types.html#wp16432) são [referências de tipo de JNI](#_JNI_Type_References) (referências de tipo Embora não simplificado), exceto os métodos. Com métodos, a assinatura de tipo de JNI é um parêntese de abertura `'('`, seguido as referências de tipo para todos o tipos concatenados (com nenhuma vírgula separar ou qualquer outra coisa), seguido por um parêntese de fechamento de parâmetro `')'`, seguido pela referência de tipo de JNI do tipo de retorno do método.

Por exemplo, considerando o método Java:

```java
long f(int n, String s, int[] array);
```

A assinatura de tipo JNI seria:

```csharp
(ILjava/lang/String;[I)J
```

Em geral, é *fortemente* recomendável usar o `javap` comando para determinar a assinaturas JNI. Por exemplo, a assinatura de tipo de JNI do [java.lang.Thread.State.valueOf(String)](http://developer.android.com/reference/java/lang/Thread.State.html#valueOf(java.lang.String)) método é "(Ljava/lang/String;) Ljava/lang/Thread$ estado;", enquanto o JNI tipo de assinatura do [ java.lang.Thread.State.values](http://developer.android.com/reference/java/lang/Thread.State.html#values) método é "() [Ljava/lang/Thread$ estado;". Fique atento para a ponto e vírgula à direita; Essas *são* faz parte da assinatura de tipo JNI.

<a name="_JNI_Type_References" />

## <a name="jni-type-references"></a>Referências de tipo JNI

Referências de tipo JNI são diferentes das referências de tipo de Java. Você não pode usar nomes de tipo totalmente qualificados do Java como `java.lang.String` com JNI, você deve usar as variações de JNI `"java/lang/String"` ou `"Ljava/lang/String;"`, dependendo do contexto; consulte abaixo para obter detalhes.
Há quatro tipos de referências de tipo JNI:

-  **built-in**
-  **simplified**
-  **type**
-  **array**


 <a name="_Built-in_Type_References" />


### <a name="built-in-type-references"></a>Referências de tipo interno

Referências de tipo interno é um único caractere, usado para fazer referência a tipos de valor internos. O mapeamento é o seguinte:

-  `"B"` para `sbyte` .
-  `"S"` para `short` .
-  `"I"` para `int` .
-  `"J"` para `long` .
-  `"F"` para `float` .
-  `"D"` para `double` .
-  `"C"` para `char` .
-  `"Z"` para `bool` .
-  `"V"` para `void` tipos de retorno do método.


 <a name="_Simplified_Type_References" />


### <a name="simplified-type-references"></a>Referências de tipo simplificada

Referências de tipo simplificado só podem ser usadas em [JNIEnv.FindClass(string)](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.FindClass/(System.String)).
Há duas maneiras de obter uma referência de tipo simplificada:

1.  Um nome totalmente qualificado de Java, substitua cada `'.'` dentro do nome do pacote e antes do nome de tipo com `'/'` e cada `'.'` dentro de um nome de tipo com `'$'` .

1.  Ler a saída de `'unzip -l android.jar | grep JavaName'` .


Um dos dois resultará no tipo de Java [java.lang.Thread.State](http://developer.android.com/reference/java/lang/Thread.State.html) que é mapeado para a referência de tipo simplificado `java/lang/Thread$State`.

<a name="_Type_References" />

### <a name="type-references"></a>Referências de tipo

Uma referência de tipo é uma referência de tipo interno ou uma referência de tipo simplificada com um `'L'` prefixo e um `';'` sufixo. Para o tipo de Java [Java](http://developer.android.com/reference/java/lang/String.html), a referência de tipo simplificada é `"java/lang/String"`, enquanto a referência de tipo é `"Ljava/lang/String;"`.

Referências de tipo são usadas com referências de tipo de matriz e com assinaturas de JNI.

Uma outra maneira de obter uma referência de tipo está lendo a saída de `'javap -s -classpath android.jar fully.qualified.Java.Name'`.
Dependendo do tipo envolvidos, você pode usar uma declaração de construtor ou tipo para determinar o nome JNI de retorno do método. Por exemplo:

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

`Thread.State` é um tipo de enumeração de Java, para que possa usar a assinatura do `valueOf` método para determinar a referência de tipo é Ljava/lang/Thread$ estado;.


<a name="_Array_Type_References" />

### <a name="array-type-references"></a>Referências de tipo de matriz

Referências de tipo de matriz são `'['` o prefixo para uma referência de tipo JNI.
Referências de tipo simplificado não podem ser usadas ao especificar matrizes.

Por exemplo, `int[]` é `"[I"`, `int[][]` é `"[[I"`, e `java.lang.Object[]` é `"[Ljava/lang/Object;"`.

<a name="_Java_Generics_and_Type_Erasure" />


## <a name="java-generics-and-type-erasure"></a>Genéricos de Java e eliminação de tipo

*A maioria dos* de tempo, como visto pelo JNI, genéricos Java *não existem*.
Há alguns "dobras", mas as dobras em como o Java interage com genéricos, não com como JNI procura e invoca membros genéricos.

Não há nenhuma diferença entre um tipo genérico ou membro e um tipo não genérico ou membro ao interagir por meio de JNI. Por exemplo, o tipo genérico [java.lang.Class&lt;T&gt; ](http://developer.android.com/reference/java/lang/Class.html) também é o tipo genérico "bruto" `java.lang.Class`, ambos com a mesma referência de tipo simplificado, `"java/lang/Class"`.

<a name="Java_Native_Interface_Support" />

## <a name="java-native-interface-support"></a>Suporte a Interface nativa Java

[Android.Runtime.JNIEnv](https://developer.xamarin.com/api/type/Android.Runtime.JNIEnv/) é o wrapper gerenciado para Java nativo Interface (JNI). Funções de JNI são declaradas dentro a [especificação de Interface de Java nativo](http://download.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html), embora os métodos foram alterados para remover o explícita `JNIEnv*` parâmetro e `IntPtr` é usado em vez de `jobject`, `jclass`, `jmethodID`, etc. Por exemplo, considere o [NewObject JNI função](http://download.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html#wp4517):

```csharp
jobject NewObjectA(JNIEnv *env, jclass clazz, jmethodID methodID, jvalue *args);
```

Isso é exposto como o [JNIEnv.NewObject](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.NewObject/p/System.IntPtr/System.IntPtr/Android.Runtime.JValue[]/) método:

```csharp
public static IntPtr NewObject(IntPtr clazz, IntPtr jmethod, params JValue[] parms);
```

Convertendo entre as duas chamadas é razoavelmente simples. Em C, você deve ter:

```c
jobject CreateMapActivity(JNIEnv *env)
{
    jclass    Map_Class   = (*env)->FindClass(env, "mono/samples/googlemaps/MyMapActivity");
    jmethodID Map_defCtor = (*env)->GetMethodID (env, Map_Class, "<init>", "()V");
    jobject   instance    = (*env)->NewObject (env, Map_Class, Map_defCtor);

    return instance;
}
```

O equivalente do c# seria:

```csharp
IntPtr CreateMapActivity()
{
    IntPtr Map_Class   = JNIEnv.FindClass ("mono/samples/googlemaps/MyMapActivity");
    IntPtr Map_defCtor = JNIEnv.GetMethodID (Map_Class, "<init>", "()V");
    IntPtr instance    = JNIEnv.NewObject (Map_Class, Map_defCtor);

    return instance;
}
```

Depois que você tiver uma instância de objeto Java mantida em um IntPtr, você provavelmente desejará fazer algo com ele. Você pode usar métodos de JNIEnv como [ <span class="external">JNIEnv.CallVoidMethod()</span> ](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallVoidMethod/p/System.IntPtr/System.IntPtr/Android.Runtime.JValue[]/) para fazer isso, mas se já houver um wrapper c# analógico, em seguida, você vai querer construir um wrapper sobre a referência JNI. Você pode fazer para por meio de [Extensions.JavaCast <t>()</t> ](https://developer.xamarin.com/api/member/Android.Runtime.Extensions.JavaCast%7BTResult%7D/p/Android.Runtime.IJavaObject/) método de extensão:

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

<a name="_Summary" />

## <a name="summary"></a>Resumo

Lidar diretamente com JNI é uma experiência terrível que deve ser evitada a todo custo. Infelizmente, nem sempre é evitáveis; Esperamos que este guia fornecerá alguma ajuda ao atingir os casos de Java desassociados com Mono para Android.


## <a name="related-links"></a>Links relacionados

- [SanityTests (exemplo)](https://developer.xamarin.com/samples/SanityTests/)
- [Especificação de Interface nativa de Java](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/jniTOC.html)
- [Funções de Interface nativa de Java](http://download.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html)
