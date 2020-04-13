---
title: Trabalhando com JNI e Xamarin.Android
description: Xamarin.Android permite escrever aplicativos Android com C# em vez de Java. Vários conjuntos são fornecidos com Xamarin.Android que fornece vinculações para bibliotecas Java, incluindo Mono.Android.dll e Mono.Android.GoogleMaps.dll. No entanto, as vinculações não são fornecidas para todas as bibliotecas Java possíveis, e as vinculações fornecidas podem não vincular todos os tipos e membros java. Para usar tipos e membros Java não vinculados, a Interface Nativa Java (JNI) pode ser usada. Este artigo ilustra como usar o JNI para interagir com tipos java e membros de aplicativos Xamarin.Android.
ms.prod: xamarin
ms.assetid: A417DEE9-7B7B-4E35-A79C-284739E3838E
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/09/2018
ms.openlocfilehash: 0fa717a775ff2f1ace9e248a8afde8d373e8a1f8
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "78292384"
---
# <a name="working-with-jni-and-xamarinandroid"></a>Trabalhando com JNI e Xamarin.Android

_Xamarin.Android permite escrever aplicativos Android com C# em vez de Java. Vários conjuntos são fornecidos com Xamarin.Android que fornece vinculações para bibliotecas Java, incluindo Mono.Android.dll e Mono.Android.GoogleMaps.dll. No entanto, as vinculações não são fornecidas para todas as bibliotecas Java possíveis, e as vinculações fornecidas podem não vincular todos os tipos e membros java. Para usar tipos e membros Java não vinculados, a Interface Nativa Java (JNI) pode ser usada. Este artigo ilustra como usar o JNI para interagir com tipos java e membros de aplicativos Xamarin.Android._

## <a name="overview"></a>Visão geral

Nem sempre é necessário ou possível criar um Wrapper Callable Gerenciado (MCW) para invocar o código Java. Em muitos casos, jni "inline" é perfeitamente aceitável e útil para o uso único de membros Java não vinculados. Muitas vezes é mais simples usar jni para invocar um único método em uma classe Java do que gerar uma ligação .jar inteira.

Xamarin.Android fornece `Mono.Android.dll` o conjunto, que fornece `android.jar` uma vinculação para a biblioteca do Android. Tipos e membros `Mono.Android.dll` não presentes dentro `android.jar` e tipos não presentes dentro podem ser usados ligando-os manualmente. Para vincular tipos e membros java, você usa a **Interface Nativa Java** **(JNI)** para procurar tipos, ler e escrever campos e invocar métodos.

A API JNI no Xamarin.Android é `System.Reflection` conceitualmente muito semelhante à API em .NET: torna possível que você procure tipos e membros por nome, leia e escreva valores de campo, invoque métodos e muito mais. Você pode usar jni e o atributo `Android.Runtime.RegisterAttribute` personalizado para declarar métodos virtuais que podem ser obrigados a suportar sobre-substituição. Você pode vincular interfaces para que elas possam ser implementadas em C#.

Este documento explica:

- Como jni se refere a tipos.
- Como procurar, ler e escrever campos.
- Como procurar e invocar métodos.
- Como expor métodos virtuais para permitir a substituição do código gerenciado.
- Como expor interfaces.

## <a name="requirements"></a>Requisitos

JNI, conforme exposto através do [namespace Android.Runtime.JNIEnv,](xref:Android.Runtime.JNIEnv)está disponível em todas as versões do Xamarin.Android.
Para ligar tipos e interfaces Java, você deve usar Xamarin.Android 4.0 ou posterior.

## <a name="managed-callable-wrappers"></a>Wrappers callable gerenciados

Um **Wrapper Callable Gerenciado** (**MCW)** é uma *vinculação* para uma classe java ou interface que encerra todas as máquinas JNI para que o código C# do cliente não precise se preocupar com a complexidade subjacente do JNI. A `Mono.Android.dll` maioria consiste em invólucros caláveis gerenciados.

Os invólucros de chamada gerenciados servem a dois propósitos:

1. Encapsular o uso do JNI para que o código do cliente não precise saber sobre a complexidade subjacente.
1. Torne possível tipos de Java de subclasse e implemente interfaces Java.

O primeiro propósito é puramente por conveniência e encapsulamento de complexidade para que os consumidores tenham um conjunto simples e gerenciado de classes para usar. Isso requer o uso dos vários membros [da JNIEnv,](xref:Android.Runtime.JNIEnv) conforme descrito posteriormente neste artigo. Tenha em mente que os invólucros &ndash; callable gerenciados não são estritamente necessários o uso "inline" jni é perfeitamente aceitável e é útil para o uso único de membros Java não vinculados. A subclassificação e implementação de interface requer o uso de invólucros callable gerenciados.

## <a name="android-callable-wrappers"></a>Callable Wrappers do Android

Os invólucros callable (ACW) do Android são necessários sempre que o tempo de execução do Android (ART) precisa invocar código gerenciado; esses invólucros são necessários porque não há como registrar classes com ART em tempo de execução.
(Especificamente, a função [DefineClass](https://docs.oracle.com/javase/6/docs/technotes/guides/jni/spec/functions.html#wp15986) JNI não é suportada pelo tempo de execução do Android. Os invólucros callable do Android compõem assim a falta de suporte de registro do tipo de tempo de execução.)

Sempre que o código Android precisar executar um método virtual ou de interface que seja substituído ou implementado em código gerenciado, o Xamarin.Android deve fornecer um proxy Java para que este método seja despachado para o tipo gerenciado apropriado. Esses tipos de proxy Java são código Java que têm a classe base "mesmo" e a lista de interface Java como o tipo gerenciado, implementando os mesmos construtores e declarando quaisquer métodos de classe base e interface substituídos.

Os invólucros callable do Android são gerados pelo programa **monodroid.exe** durante o [processo de compilação,](~/android/deploy-test/building-apps/build-process.md)e são gerados para todos os tipos que (direta ou indiretamente) herdam [Java.Lang.Object](xref:Java.Lang.Object).

### <a name="implementing-interfaces"></a>Implementando interfaces

Há momentos em que você pode precisar implementar uma interface Android (como [Android.Content.IComponentCallbacks).](xref:Android.Content.IComponentCallbacks)

Todas as classes e interfaces do Android estendem a interface [Android.Runtime.IJavaObject;](xref:Android.Runtime.IJavaObject) portanto, todos os tipos `IJavaObject`de Android devem implementar .
Xamarin.Android aproveita este &ndash; fato `IJavaObject` que usa para fornecer ao Android um proxy Java (um invólucro callable android) para o tipo gerenciado dado. Como **o monodroid.exe** só procura `Java.Lang.Object` subclasses (que devem ser implementadas), `IJavaObject`a subclasse nos `Java.Lang.Object` fornece uma maneira de implementar interfaces em código gerenciado. Por exemplo:

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

*O restante deste artigo fornece detalhes de implementação sujeitos a alterações sem aviso prévio* (e é apresentado aqui apenas porque os desenvolvedores podem estar curiosos sobre o que está acontecendo sob o capô).

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

Observe que a classe base está preservada, e as declarações de método nativo são fornecidas para cada método que é substituído dentro do código gerenciado.

### <a name="exportattribute-and-exportfieldattribute"></a>Atributo de exportação e atributo de campo de exportação

Normalmente, o Xamarin.Android gera automaticamente o código Java que compreende o ACW; esta geração é baseada nos nomes de classe e método quando uma classe deriva de uma classe Java e substitui os métodos Java existentes. No entanto, em alguns cenários, a geração de código não é adequada, como descrito abaixo:

- O Android suporta nomes de ação em atributos XML de layout, por exemplo, o atributo [Android:onClick](xref:Android.Views.View.IOnClickListener.OnClick*) XML. Quando é especificado, a instância de exibição inflada tenta procurar o método Java.

- A interface [java.io.Serializable](https://developer.android.com/reference/java/io/Serializable.html) requer `readObject` e `writeObject` métodos. Como eles não são membros desta interface, nossa implementação gerenciada correspondente não expõe esses métodos ao código Java.

- A interface [android.os.Parcelable](xref:Android.OS.Parcelable) espera que uma classe `CREATOR` de `Parcelable.Creator`implementação deve ter um campo estático do tipo . O código Java gerado requer algum campo explícito. Com nosso cenário padrão, não há como produzir campo em código Java a partir de código gerenciado.

Como a geração de código não fornece uma solução para gerar métodos Java arbitrários com nomes arbitrários, começando pelo Xamarin.Android 4.2, o [ExportAttribute](xref:Java.Interop.ExportAttribute) e [o ExportFieldAttribute](xref:Java.Interop.ExportFieldAttribute) foram introduzidos para oferecer uma solução para os cenários acima. Ambos os atributos residem no `Java.Interop` namespace:

- `ExportAttribute`&ndash; especifica um nome do método e seus tipos de exceção esperados (para dar "lançamentos" explícitos em Java). Quando é usado em um método, o método "exportará" um método Java que gera um código de expedição para a invocação JNI correspondente ao método gerenciado. Isso pode ser `android:onClick` `java.io.Serializable`usado com e .

- `ExportFieldAttribute`&ndash; especifica um nome de campo. Ele reside em um método que funciona como um inicializador de campo. Isso pode ser `android.os.Parcelable`usado com .

#### <a name="troubleshooting-exportattribute-and-exportfieldattribute"></a>Solução de problemas ExportAttribute e ExportFieldAttribute

- A embalagem falha devido à falta de **Mono.Android.Export.dll** &ndash; se você usou `ExportAttribute` ou `ExportFieldAttribute` em alguns métodos em seu código ou bibliotecas dependentes, você tem que adicionar **Mono.Android.Export.dll**. Este conjunto está isolado para suportar o código de retorno de chamada de Java. Ele é separado do **Mono.Android.dll,** pois adiciona tamanho adicional ao aplicativo.

- Na compilação `MissingMethodException` Release, ocorre &ndash; para métodos de exportação na compilação de lançamento, `MissingMethodException` ocorre para métodos de exportação. (Este problema é corrigido na versão mais recente do Xamarin.Android.)

### <a name="exportparameterattribute"></a>Atributo de parâmetro de exportação

`ExportAttribute`e `ExportFieldAttribute` fornecer funcionalidade que o código de tempo de execução Java pode usar. Este código de tempo de execução acessa o código gerenciado através dos métodos JNI gerados impulsionados por esses atributos. Como resultado, não há nenhum método Java existente que o método gerenciado se vincule; portanto, o método Java é gerado a partir de uma assinatura de método gerenciado.

No entanto, este caso não é totalmente determinante. Mais notavelmente, isso é verdade em alguns mapeamentos avançados entre tipos gerenciados e tipos Java, tais como:

- InputStream
- Outputstream
- XmlPullParser
- XmlResourceParser

Quando tipos como estes são necessários `ExportParameterAttribute` para métodos exportados, devem ser usados para dar explicitamente o parâmetro correspondente ou o valor de retorno de um tipo.

### <a name="annotation-attribute"></a>Atributo de anotação

No Xamarin.Android 4.2, `IAnnotation` convertemos os tipos de implementação em atributos (System.Attribute), e adicionamos suporte para geração de anotação em invólucros Java.

Isso significa as seguintes alterações direcionais:

- O gerador `Java.Lang.DeprecatedAttribute` de `java.Lang.Deprecated` ligação gera `[Obsolete]` a partir de (enquanto ele deve estar em código gerenciado).

- Isso não significa que `Java.Lang.Deprecated` a classe existente desaparecerá. Esses objetos baseados em Java ainda podem ser usados como objetos Java usuais (se tal uso existir). Haverá `Deprecated` aulas. `DeprecatedAttribute`

- A `Java.Lang.DeprecatedAttribute` classe está `[Annotation]` marcada como . Quando há um atributo personalizado que `[Annotation]` é herdado desse atributo, a tarefa msbuild gerará uma anotação Java para esse atributo personalizado (@Deprecated) no Android Callable Wrapper (ACW).

- Anotações poderiam ser geradas em classes, métodos e campos exportados (que é um método em código gerenciado).

Se a classe que contém (a própria classe anotada, ou a classe que contém os membros anotados) não for registrada, toda a fonte da classe Java não será gerada, incluindo anotações. Para métodos, você `ExportAttribute` pode especificar o para obter o método explicitamente gerado e anotado. Além disso, não é um recurso para "gerar" uma definição de classe de anotação Java. Em outras palavras, se você definir um atributo gerenciado personalizado para uma determinada anotação, você terá que adicionar outra biblioteca .jar que contenha a classe de anotação Java correspondente. Adicionar um arquivo de origem Java que define o tipo de anotação não é suficiente. O compilador Java não funciona da mesma forma **que o apto**.

Além disso, as seguintes limitações se aplicam:

- Este processo de `@Target` conversão não considera a anotação no tipo de anotação até agora.

- Atributos em uma propriedade não funciona. Use atributos para obter propriedade ou setter em vez disso.

## <a name="class-binding"></a>Vinculação de classe

Vincular uma classe significa escrever um invólucro callable gerenciado para simplificar a invocação do tipo Java subjacente.

Vincular métodos virtuais e abstratos para permitir a substituição de C# requer Xamarin.Android 4.0. No entanto, qualquer versão do Xamarin.Android pode vincular métodos não virtuais, métodos estáticos ou métodos virtuais sem suportar substituições.

Uma vinculação normalmente contém os seguintes itens:

- Uma [alça JNI para o tipo Java sendo vinculado](#_Looking_up_Java_Types).

- [IDs de campo JNI e propriedades para cada campo vinculado](#_Instance_Fields).

- [IDs e métodos do método JNI para cada método vinculado](#_Instance_Methods).

- Se a subclassificação for necessária, o tipo precisa ter um atributo personalizado [RegisterAttribute](xref:Android.Runtime.RegisterAttribute) na `true`declaração de tipo com [RegisterAttribute.DoNotGenerateAcw](xref:Android.Runtime.RegisterAttribute.DoNotGenerateAcw) definido como .

### <a name="declaring-type-handle"></a>Declarando a alça do tipo

Os métodos de análise de campo e método requerem uma referência de objeto referente ao seu tipo declaração. Por convenção, isso é `class_ref` realizado em um campo:

```csharp
static IntPtr class_ref = JNIEnv.FindClass(CLASS);
```

Consulte a seção [REFERÊNCIAs do tipo JNI](#_JNI_Type_References) para obter detalhes sobre o `CLASS` token.

### <a name="binding-fields"></a>Campos de Ligação

Os campos Java são expostos como propriedades C#, por exemplo, o campo Java [java.lang.System.in](https://developer.android.com/reference/java/lang/System.html#in) está vinculado à propriedade C# [Java.Lang.JavaSystem.In](xref:Java.Lang.JavaSystem.In).
Além disso, uma vez que jni distingue entre campos estáticos e campos de instância, diferentes métodos serão usados na implementação das propriedades.

A ligação de campo envolve três conjuntos de métodos:

1. O método *de id get field.* O método *get field id* é responsável por devolver uma alça de campo que o *valor de campo get* e os métodos de valor de campo *definidos* usarão. A obtenção do id de campo requer conhecer o tipo de declaração, o nome do campo e a assinatura do [tipo JNI](#JNI_Type_Signatures) do campo.

1. Os métodos *de valor de campo get.* Esses métodos exigem a alça de campo e são responsáveis pela leitura do valor do campo a partir de Java.
    O método de uso depende do tipo do campo.

1. Os métodos *de valor de campo definidos.* Esses métodos exigem a alça de campo e são responsáveis por escrever o valor do campo dentro de Java. O método de uso depende do tipo do campo.

[Os campos estáticos](#_Static_Fields) usam os métodos `JNIEnv.GetStatic*Field` [JNIEnv.GetStaticFieldID](xref:Android.Runtime.JNIEnv.GetStaticMethodID*)e [JNIEnv.SetStaticField.](xref:Android.Runtime.JNIEnv.SetStaticField*)

 [Os campos de instância](#_Instance_Fields) usam os métodos `JNIEnv.Get*Field` [JNIEnv.GetFieldID](xref:Android.Runtime.JNIEnv.GetFieldID*)e [JNIEnv.SetField.](xref:Android.Runtime.JNIEnv.SetField*)

Por exemplo, a `JavaSystem.In` propriedade estática pode ser implementada como:

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

Nota: Estamos usando [o InputStreamInvoker.FromJniHandle](xref:Android.Runtime.InputStreamInvoker.FromJniHandle*) para converter `System.IO.Stream` a referência JNI `JniHandleOwnership.TransferLocalRef` em uma instância, e estamos usando porque [JNIEnv.GetStaticObjectField](xref:Android.Runtime.JNIEnv.GetStaticObjectField*) retorna uma referência local.

Muitos dos tipos [Android.Runtime](xref:Android.Runtime) têm `FromJniHandle` métodos que converterão uma referência JNI no tipo desejado.

### <a name="method-binding"></a>Vinculação do método

Os métodos Java são expostos como métodos C# e como propriedades C#. Por exemplo, o método Java [java.lang.runtime.runFinalizersOnExit](https://developer.android.com/reference/java/lang/Runtime.html#runFinalizersOnExit(boolean)) está vinculado como o método [Java.Lang.Runtime.RunFinalizersOnExit,](xref:Java.Lang.Runtime.RunFinalizersOnExit*) e o método [java.lang.Object.getClass](https://developer.android.com/reference/java/lang/Object.html#getClass) está vinculado à propriedade [Java.Lang.Object.Class.](xref:Java.Lang.Object.Class)

A invocação do método é um processo de duas etapas:

1. O *método get id* para o método de invocar. O método *get method id* é responsável por retornar um método que os métodos de invocação do método usarão. A obtenção do id do método requer conhecer o tipo de declaração, o nome do método e a assinatura do [tipo JNI](#JNI_Type_Signatures) do método.

1. Invoque o método.

Assim como nos campos, os métodos a serem utilizados para obter o id do método e invocar o método diferem entre métodos estáticos e métodos de instância.

[Os métodos estáticos](#_Static_Methods_1) usam [JNIEnv.GetStaticMethodID()](xref:Android.Runtime.JNIEnv.GetStaticMethodID*) para procurar `JNIEnv.CallStatic*Method` o id do método e usar a família de métodos para invocação.

[Os métodos de instância](#_Instance_Methods) usam [JNIEnv.GetMethodID](xref:Android.Runtime.JNIEnv.GetMethodID*) para procurar `JNIEnv.Call*Method` `JNIEnv.CallNonvirtual*Method` o id do método e usar as famílias e as famílias dos métodos para invocação.

A vinculação do método é potencialmente mais do que apenas invocação do método. A vinculação do método também inclui permitir que um método seja substituído (para métodos abstratos e não finais) ou implementado (para métodos de interface). A [seção Herança de Suporte, Interfaces](#_Supporting_Inheritance,_Interfaces_1) abrange as complexidades de suporte a métodos virtuais e métodos de interface.

<a name="_Static_Methods_1" />

#### <a name="static-methods"></a>Métodos estáticos

Vincular um método `JNIEnv.GetStaticMethodID` estático envolve o uso de `JNIEnv.CallStatic*Method` uma alça de método e, em seguida, usar o método apropriado, dependendo do tipo de retorno do método. A seguir, um exemplo de uma vinculação para o método [Runtime.getRuntime:](https://developer.android.com/reference/java/lang/Runtime.html#getRuntime())

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

Observe que armazenamos a alça do `id_getRuntime`método em um campo estático, . Trata-se de uma otimização de desempenho, de modo que o cabo do método não precisa ser examinado em cada invocação. Não é necessário cache do manuseio do método desta forma. Uma vez que a alça do método é obtida, [JNIEnv.CallStaticObjectMethod](xref:Android.Runtime.JNIEnv.CallStaticObjectMethod*) é usado para invocar o método. `JNIEnv.CallStaticObjectMethod`retorna `IntPtr` um que contém a alça da instância Java retornada.
[Java.Lang.Object.GetObject&lt;&gt;T (IntPtr, JniHandleOwnership)](xref:Java.Lang.Object.GetObject*) é usado para converter a alça Java em uma instância de objeto fortemente digitada.

#### <a name="non-virtual-instance-method-binding"></a>Vinculação do método de instância não virtual

Vincular `final` um método de instância, ou um método de `JNIEnv.GetMethodID` instância que não exija substituição, `JNIEnv.Call*Method` envolve o uso para obter um manipulador de método, em seguida, usando o método apropriado, dependendo do tipo de retorno do método. O seguinte é um exemplo `Object.Class` de uma vinculação para a propriedade:

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

Observe que armazenamos a alça do `id_getClass`método em um campo estático, .
Trata-se de uma otimização de desempenho, de modo que o cabo do método não precisa ser examinado em cada invocação. Não é necessário cache do manuseio do método desta forma. Uma vez que a alça do método é obtida, [JNIEnv.CallStaticObjectMethod](xref:Android.Runtime.JNIEnv.CallStaticObjectMethod*) é usado para invocar o método. `JNIEnv.CallStaticObjectMethod`retorna `IntPtr` um que contém a alça da instância Java retornada.
[Java.Lang.Object.GetObject&lt;&gt;T (IntPtr, JniHandleOwnership)](xref:Java.Lang.Object.GetObject*) é usado para converter a alça Java em uma instância de objeto fortemente digitada.

### <a name="binding-constructors"></a>Construtores de Ligação

Construtores são métodos Java `"<init>"`com o nome . Assim como nos métodos `JNIEnv.GetMethodID` de ocorrência java, é usado para procurar a alça do construtor. Ao contrário dos métodos Java, os métodos [JNIEnv.NewObject](xref:Android.Runtime.JNIEnv.NewObject*) são usados para invocar o cabo do método do construtor. O valor `JNIEnv.NewObject` de retorno é uma referência local jni:

```csharp
int value = 42;
IntPtr class_ref    = JNIEnv.FindClass ("java/lang/Integer");
IntPtr id_ctor_I    = JNIEnv.GetMethodID (class_ref, "<init>", "(I)V");
IntPtr lrefInstance = JNIEnv.NewObject (class_ref, id_ctor_I, new JValue (value));
// Dispose of lrefInstance, class_ref…
```

Normalmente, uma vinculação de classe irá subclasse [Java.Lang.Object](xref:Java.Lang.Object).
Ao `Java.Lang.Object`subclassificar, um semântico adicional `Java.Lang.Object` entra em jogo: uma instância `Java.Lang.Object.Handle` mantém uma referência global a uma instância Java através da propriedade.

1. O `Java.Lang.Object` construtor padrão alocará uma instância Java.

1. Se o tipo `RegisterAttribute` tiver `RegisterAttribute.DoNotGenerateAcw` `true` um , e `RegisterAttribute.Name` for , então uma instância do tipo será criada através de seu construtor padrão.

1. Caso contrário, o [Android Callable Wrapper](~/android/platform/java-integration/android-callable-wrappers.md) `this.GetType` (ACW) correspondente é instanciado através de seu construtor padrão. Os Wrappers callable do Android `Java.Lang.Object` são gerados `RegisterAttribute.DoNotGenerateAcw` durante a `true`criação do pacote para cada subclasse para a qual não está definido como .

Para tipos que não são vinculações de classe, este é `Mono.Samples.HelloWorld.HelloAndroid` o semântico `mono.samples.helloworld.HelloAndroid` esperado: a instanciação de uma instância C# deve construir uma instância Java que é um Wrapper Callable do Android gerado.

Para vinculações de classe, este pode ser o comportamento correto se o tipo Java contiver um construtor padrão e/ou nenhum outro construtor precisar ser invocado. Caso contrário, deve ser fornecido um construtor que execute as seguintes ações:

1. Invocando o [Java.Lang.Object (IntPtr, JniHandleOwnership)](xref:Java.Lang.Object#ctor*) em vez do construtor padrão. `Java.Lang.Object` Isso é necessário para evitar a criação de uma nova instância Java.

1. Verifique o valor de [Java.Lang.Object.Handle](xref:Java.Lang.Object.Handle) antes de criar quaisquer instâncias Java. A `Object.Handle` propriedade terá um `IntPtr.Zero` valor diferente se um Android Callable Wrapper foi construído em código Java, e a vinculação de classe está sendo construída para conter a instância criada do Android Callable Wrapper. Por exemplo, quando `mono.samples.helloworld.HelloAndroid` o Android cria uma instância, o Android Callable `HelloAndroid` Wrapper será criado primeiro `Mono.Samples.HelloWorld.HelloAndroid` , e `Object.Handle` o construtor Java criará uma instância do tipo correspondente, com a propriedade sendo definida para a instância Java antes da execução do construtor.

1. Se o tipo de tempo de execução atual não for o mesmo que o tipo de declaração, então uma instância do Wrapper callable do Android correspondente deve ser criada e usar [Object.SetHandle](xref:Java.Lang.Object.SetHandle*) para armazenar a alça retornada por [JNIEnv.CreateInstance](xref:Android.Runtime.JNIEnv.CreateInstance*).

1. Se o tipo de tempo de execução atual for o mesmo que o tipo de declaração, `JNIEnv.NewInstance` então invoque o construtor Java e use [Object.SetHandle](xref:Java.Lang.Object.SetHandle*) para armazenar a alça devolvida por .

Por exemplo, considere o construtor [java.lang.Integer(int).](https://developer.android.com/reference/java/lang/Integer.html#Integer(int)) Isso é vinculado como:

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

Os métodos [JNIEnv.CreateInstance](xref:Android.Runtime.JNIEnv.CreateInstance*) são auxiliares `JNIEnv.GetMethodID` `JNIEnv.NewObject`para `JNIEnv.DeleteGlobalReference` executar a `JNIEnv.FindClass`, `JNIEnv.FindClass`, e sobre o valor retornado de . Consulte a próxima seção para obter detalhes.

<a name="_Supporting_Inheritance,_Interfaces_1" />

### <a name="supporting-inheritance-interfaces"></a>Suporte à herança, interfaces

A subclassificação de um tipo Java ou a implementação de uma interface Java requer a `Java.Lang.Object` geração de ACWs [(Callable Wrappers) android](~/android/platform/java-integration/android-callable-wrappers.md) que são gerados para cada subclasse durante o processo de embalagem. A geração ACW é controlada através do atributo personalizado [Android.Runtime.RegisterAttribute.](xref:Android.Runtime.RegisterAttribute)

Para os tipos `[Register]` C#, o construtor de atributos personalizado requer um argumento: a [referência de tipo simplificada JNI](#_Simplified_Type_References_1) para o tipo Java correspondente. Isso permite fornecer diferentes nomes entre Java e C#.

Antes do Xamarin.Android 4.0, o atributo `[Register]` personalizado não estava disponível para os tipos Java existentes de "alias". Isso porque o processo de geração ACW geraria ACWs para cada `Java.Lang.Object` subclasse encontrada.

Xamarin.Android 4.0 introduziu a propriedade [RegisterAttribute.DoNotGenerateAcw.](xref:Android.Runtime.RegisterAttribute.DoNotGenerateAcw) Esta propriedade instrui o processo de geração ACW a *pular* o tipo anotado, permitindo a declaração de novos Wrappers Callable Gerenciados que não resultarão na geração de ACWs no momento da criação do pacote. Isso permite vincular os tipos Java existentes. Por exemplo, considere a seguinte `Adder`classe Java simples, que contém um método, `add`que adiciona aos inteiros e retorna o resultado:

```java
package mono.android.test;
public class Adder {
    public int add (int a, int b) {
        return a + b;
    }
}
```

O `Adder` tipo pode ser vinculado como:

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

Aqui, `Adder` o tipo C# `Adder` *alias* do tipo Java. O `[Register]` atributo é usado para especificar o nome JNI do tipo `mono.android.test.Adder` Java, e a `DoNotGenerateAcw` propriedade é usada para inibir a geração ACW. Isso resultará na geração de um `ManagedAdder` ACW para o `mono.android.test.Adder` tipo, que subclasses adequadamente do tipo. Se `RegisterAttribute.DoNotGenerateAcw` a propriedade não tivesse sido usada, então o processo de `mono.android.test.Adder` compilação xamarin.android teria gerado um novo tipo Java. Isso resultaria em erros de `mono.android.test.Adder` compilação, já que o tipo estaria presente duas vezes, em dois arquivos separados.

### <a name="binding-virtual-methods"></a>Métodos virtuais de vinculação

`ManagedAdder`subclasses do `Adder` tipo Java, mas não é particularmente `Adder` interessante: o tipo C# não `ManagedAdder` define nenhum método virtual, então não pode substituir nada.

Os `virtual` métodos de vinculação para permitir a substituição por subclasses exigem várias coisas que precisam ser feitas que se enquadram nas duas categorias a seguir:

1. **Vinculação do método**

1. **Registro de Métodos**

#### <a name="method-binding"></a>Vinculação do método

Uma vinculação de método requer a adição `ThresholdType`de `ThresholdClass`dois membros de suporte à definição C#: `Adder` , e .

##### <a name="thresholdtype"></a>ThresholdType

A `ThresholdType` propriedade retorna o tipo atual da vinculação:

```csharp
partial class Adder {
    protected override System.Type ThresholdType {
        get {
            return typeof (Adder);
        }
    }
}
```

`ThresholdType`é usado no Método Vinculação para determinar quando deve executar o despacho de método virtual vs. não virtual. Ele deve sempre `System.Type` retornar uma instância que corresponda ao tipo C# declarando.

##### <a name="thresholdclass"></a>ThresholdClass

A `ThresholdClass` propriedade retorna a referência de classe JNI para o tipo vinculado:

```csharp
partial class Adder {
    protected override IntPtr ThresholdClass {
        get {
            return class_ref;
        }
    }
}
```

`ThresholdClass`é usado no Método De Vinculação ao invocar métodos não virtuais.

#### <a name="binding-implementation"></a>Implementação de vinculação

A implementação de vinculação do método é responsável pela invocação em tempo de execução do método Java. Ele também `[Register]` contém uma declaração de atributo personalizado que faz parte do registro do método e será discutida na seção Registro de Métodos:

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

O `id_add` campo contém o método ID para o método Java para invocar. O `id_add` valor é `JNIEnv.GetMethodID`obtido a partir de`class_ref`, que requer`"add"`a classe declarativa ( ),`"(II)I"`o nome do método Java ( ), e a assinatura JNI do método ( ).

Uma vez que o ID do método é obtido, `GetType` é comparado para `ThresholdType` determinar se o envio virtual ou não virtual é necessário. O despacho virtual `GetType` `ThresholdType`é `Handle` necessário quando as correspondências, como pode se referir a uma subclasse alocada em Java que substitui o método.

Quando `GetType` `ThresholdType`não corresponder, `Adder` foi subclassificada `ManagedAdder`(por exemplo), e `Adder.Add` a implementação só será invocada `base.Add`se a subclasse for invocada . Este é o caso de despacho `ThresholdClass` não virtual, que é onde entra. `ThresholdClass`especifica qual classe Java fornecerá a implementação do método para invocar.

#### <a name="method-registration"></a>Registro de Métodos

Suponha que `ManagedAdder` temos uma `Adder.Add` definição atualizada que substitui o método:

```csharp
partial class ManagedAdder : Adder {
    public override int Add (int a, int b) {
        return (a*2) + (b*2);
    }
}
```

Lembre-se `Adder.Add` `[Register]` que tinha um atributo personalizado:

```csharp
[Register ("add", "(II)I", "GetAddHandler")]
```

O `[Register]` construtor de atributos personalizado aceita três valores:

1. O nome do método `"add"` Java, neste caso.

1. A assinatura tipo JNI `"(II)I"` do método, neste caso.

1. O *método conector,* `GetAddHandler` neste caso.
    Os métodos do conector serão discutidos posteriormente.

Os dois primeiros parâmetros permitem que o processo de geração aCW gere uma declaração de método para substituir o método. O ACW resultante conteria alguns dos seguintes códigos:

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

Observe que `@Override` um método é declarado, `n_`que delega a um método prefixado de mesmo nome. Isso garante que quando `ManagedAdder.add`o `ManagedAdder.n_add` código Java for invocado, será `ManagedAdder.Add` invocado, o que permitirá que o método C# principal seja executado.

Assim, a pergunta mais `ManagedAdder.n_add` importante: como `ManagedAdder.Add`está ligado a ?

Os `native` métodos Java são registrados com o tempo de execução java (o tempo de execução do Android) através da [função JNI RegisterNatives](https://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html#wp17734).
`RegisterNatives`leva uma matriz de estruturas contendo o nome do método Java, o TIPO DE ASSINATURA JNI e um ponteiro de função para invocar que segue [a convenção de chamada JNI](https://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/design.html#wp715).
O ponteiro de função deve ser uma função que leva dois argumentos de ponteiro seguidos pelos parâmetros do método. O `ManagedAdder.n_add` método Java deve ser implementado através de uma função que tenha o seguinte protótipo C:

```csharp
int FunctionName(JNIEnv *env, jobject this, int a, int b)
```

Xamarin.Android não expõe `RegisterNatives` um método. Em vez disso, o ACW e o `RegisterNatives`MCW juntos fornecem as informações necessárias para invocar : o ACW contém o nome do método e a assinatura do tipo JNI, a única coisa que falta é um ponteiro de função para conectar.

É aqui que entra o *método do conector.* O `[Register]` terceiro parâmetro de atributo personalizado é o nome de um método definido no tipo registrado ou `System.Delegate`uma classe base do tipo registrado que não aceita parâmetros e retorna a . O retornado, `System.Delegate` por sua vez, refere-se a um método que tem a assinatura de função JNI correta. Finalmente, o delegado que o método conector retorna *deve* ser enraizado para que o GC não o colete, pois o delegado está sendo fornecido ao Java.

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

O `GetAddHandler` método `Func<IntPtr, IntPtr, int, int,
int>` cria um delegado `n_Add` que se refere ao método e, em seguida, invoca [JNINativeWrapper.CreateDelegate](xref:Android.Runtime.JNINativeWrapper.CreateDelegate*).
`JNINativeWrapper.CreateDelegate`envolve o método fornecido em um bloco de tentativa/captura, de modo que quaisquer exceções não manuseadas sejam tratadas e resultarão em elevar o evento [AndroidEvent.UnhandledExceptionRaiser.](xref:Android.Runtime.AndroidEnvironment.UnhandledExceptionRaiser) O delegado resultante é armazenado `cb_add` na variável estática para que o GC não liberte o delegado.

Finalmente, `n_Add` o método é responsável por empacotar os parâmetros JNI para os tipos gerenciados correspondentes e, em seguida, delegar a chamada do método.

Nota: `JniHandleOwnership.DoNotTransfer` Use sempre ao obter um MCW sobre uma instância Java. Tratá-los como uma referência `JNIEnv.DeleteLocalRef`local (e,&gt; portanto,&gt; chamar ) vai quebrar as transições de pilha gerenciadas - Java - gerenciadas.

### <a name="complete-adder-binding"></a>Ligação completa de adder

A vinculação gerenciada `mono.android.tests.Adder` completa para o tipo é:

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

Ao escrever um tipo que corresponda aos seguintes critérios:

1. Subclasses`Java.Lang.Object`

1. Tem `[Register]` um atributo personalizado

1. `RegisterAttribute.DoNotGenerateAcw` é `true`

Em seguida, para a interação GC, o `Java.Lang.Object` tipo `Java.Lang.Object` *não deve* ter quaisquer campos que possam se referir a uma ou subclasse em tempo de execução. Por exemplo, campos `System.Object` de tipo e qualquer tipo de interface não são permitidos. Tipos que não `Java.Lang.Object` podem se referir `System.String` a `List<int>`instâncias são permitidos, tais como e . Esta restrição é para evitar a coleta prematura de objetos pelo GC.

Se o tipo deve conter um campo `Java.Lang.Object` de instância que pode `System.WeakReference` `GCHandle`se referir a uma instância, então o tipo de campo deve ser ou .

## <a name="binding-abstract-methods"></a>Métodos abstratos de vinculação

Os `abstract` métodos de vinculação são em grande parte idênticos aos métodos virtuais de ligação. Há apenas duas diferenças:

1. O método abstrato é abstrato. Ele ainda retém o atributo `[Register]` e o Registro do `Invoker` Método associado, o Método de Vinculação é apenas movido para o tipo.

1. Um não-tipo `abstract` `Invoker` é criado que subclasses do tipo abstrato. O `Invoker` tipo deve substituir todos os métodos abstratos declarados na classe base, e a implementação substituída é a implementação de Vinculação de Métodos, embora o caso de despacho não virtual possa ser ignorado.

Por exemplo, suponha que o método acima `mono.android.test.Adder.add` foi `abstract`. A vinculação C# mudaria `Adder.Add` de modo que `AdderInvoker` fosse abstrata, `Adder.Add`e um novo tipo seria definido que implementado:

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

O `Invoker` tipo só é necessário ao obter referências JNI para instâncias criadas por Java.

## <a name="binding-interfaces"></a>Interfaces de vinculação

Interfaces vinculantes são conceitualmente semelhantes às classes vinculantes que contêm métodos virtuais, mas muitas das especificidades diferem de maneiras sutis (e não tão sutis). Considere a seguinte [declaração de interface Java:](https://github.com/xamarin/monodroid-samples/blob/master/SanityTests/Adder.java#L14)

```csharp
public interface Progress {
    void onAdd(int[] values, int currentIndex, int currentSum);
}
```

As ligações de interface têm duas partes: a definição de interface C# e uma definição de Invocador para a interface.

### <a name="interface-definition"></a>Definição de interface

A definição de interface C# deve cumprir os seguintes requisitos:

- A definição da `[Register]` interface deve ter um atributo personalizado.

- A definição de `IJavaObject interface`interface deve estender o .
    Se não o fizer, os ACWs herdam da interface Java.

- Cada método de `[Register]` interface deve conter um atributo especificando o nome do método Java correspondente, a assinatura JNI e o método conector.

- O método conector também deve especificar o tipo em que o método conector pode ser localizado.

Quando `abstract` vincular `virtual` e métodos, o método conector seria pesquisado dentro da hierarquia de herança do tipo registrado. As interfaces não podem ter métodos que contenham corpos, portanto isso não funciona, portanto, a exigência de que um tipo seja especificado indicando onde o método conector está localizado. O tipo é especificado dentro da seqüência do método conector, após um cólon `':'`, e deve ser o nome do tipo qualificado de montagem do tipo que contém o invocador.

As declarações do método de interface são uma tradução do método Java correspondente usando tipos *compatíveis.* Para os tipos de builtin Java, os tipos compatíveis `int` são os `int`tipos C# correspondentes, por exemplo, Java é C# . Para tipos de referência, o tipo compatível é um tipo que pode fornecer uma alça JNI do tipo Java apropriado.

Os membros da interface não serão &ndash; diretamente invocados pela invocação &ndash; Java serão mediados através do tipo Invoker, de modo que alguma quantidade de flexibilidade é permitida.

A interface Java Progress pode ser [declarada em C# como:](https://github.com/xamarin/monodroid-samples/blob/master/SanityTests/ManagedAdder.cs#L83)

```csharp
[Register ("mono/android/test/Adder$Progress", DoNotGenerateAcw=true)]
public interface IAdderProgress : IJavaObject {
    [Register ("onAdd", "([III)V",
            "GetOnAddHandler:Mono.Samples.SanityTests.IAdderProgressInvoker, SanityTests, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null")]
    void OnAdd (JavaArray<int> values, int currentIndex, int currentSum);
}
```

Observe no acima que mapeamos o parâmetro Java `int[]` para um [int&lt;&gt;JavaArray](xref:Android.Runtime.JavaArray`1).
Isso não é necessário: poderíamos tê-lo `int[]`ligado a `IList<int>`um C# , ou um , ou algo totalmente mais. Seja qual for `Invoker` o tipo escolhido, ele precisa `int[]` ser capaz de traduzi-lo em um tipo Java para invocação.

### <a name="invoker-definition"></a>Definição do Invocador

A `Invoker` definição de `Java.Lang.Object`tipo deve herdar, implementar a interface apropriada e fornecer todos os métodos de conexão referenciados na definição da interface. Há mais uma sugestão que difere de `class_ref` uma vinculação de classe: os IDs de campo e método devem ser membros de instância, não membros estáticos.

A razão para preferir membros de `JNIEnv.GetMethodID` instância tem a ver com o comportamento no tempo de execução do Android. (Este pode ser o comportamento java também; ele não foi testado.) `JNIEnv.GetMethodID` retorna nulo ao procurar um método que vem de uma interface implementada e não da interface declarada. Considere a interface [java.util.SortedMap&lt;K,&gt; V](https://developer.android.com/reference/java/util/SortedMap.html) Java, que implementa a interface [java.util.Map&lt;K, V.&gt; ](https://developer.android.com/reference/java/util/Map.html) O mapa fornece um método [claro,](https://developer.android.com/reference/java/util/Map.html#clear()) assim, uma definição aparentemente razoável `Invoker` para SortedMap seria:

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

O acima `JNIEnv.GetMethodID` falhará `null` porque retornará `Map.clear` ao `SortedMap` procurar o método através da instância de classe.

Existem duas soluções para isso: rastrear de qual `class_ref` interface cada método vem, e ter uma para cada interface, ou manter tudo como membros de instância e executar a busca do método no tipo de classe mais derivada, não no tipo de interface. Este último é feito em **Mono.Android.dll**.

A definição do Invocador tem seis seções: `ThresholdType` o `ThresholdClass` construtor, `GetObject` o `Dispose` método, os membros, o método, a implementação do método de interface e a implementação do método de conector.

#### <a name="constructor"></a>Construtor

O construtor precisa procurar a classe de tempo de execução da instância que `class_ref` está sendo invocada e armazenar a classe de tempo de execução no campo de instância:

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

Nota: `Handle` A propriedade deve ser usada dentro `handle` do corpo do construtor, e `handle` não o parâmetro, pois no Android v4.0 o parâmetro pode ser inválido após o construtor base terminar a execução.

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

Os `ThresholdType` `ThresholdClass` membros e membros são idênticos ao que é encontrado em uma vinculação de classe:

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

Um `GetObject` método estático é necessário para suportar [extensões.JavaCast&lt;&gt;T ()](xref:Android.Runtime.Extensions.JavaCast*):

```csharp
partial class IAdderProgressInvoker {
    public static IAdderProgress GetObject (IntPtr handle, JniHandleOwnership transfer)
    {
        return new IAdderProgressInvoker (handle, transfer);
    }
}
```

#### <a name="interface-methods"></a>Métodos de interface

Cada método da interface precisa ter uma implementação, que invoca o método Java correspondente através de JNI:

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

#### <a name="connector-methods"></a>Métodos do conector

Os métodos de conector e a infra-estrutura de suporte são responsáveis por empacotar os parâmetros JNI para tipos C# apropriados. O `int[]` parâmetro Java será passado como `jintArray`um JNI , que é um `IntPtr` dentro c#. O `IntPtr` deve ser empajado para a `JavaArray<int>` fim de apoiar a invocação da interface C#:

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

Se `int[]` seria preferível, `JavaList<int>`então [JNIEnv.GetArray()](xref:Android.Runtime.JNIEnv.GetArray*) poderia ser usado em vez disso:

```csharp
int[] _values = (int[]) JNIEnv.GetArray(values, JniHandleOwnership.DoNotTransfer, typeof (int));
```

Note, no `JNIEnv.GetArray` entanto, que copia toda a matriz entre VMs, então para grandes matrizes isso pode resultar em muita pressão gc adicionada.

### <a name="complete-invoker-definition"></a>Definição completa do Invocador

A [definição completa de IAdderProgressInvoker:](https://github.com/xamarin/monodroid-samples/blob/master/SanityTests/ManagedAdder.cs#L88)

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

Muitos métodos JNIEnv retornam *referências de objetos* `GCHandle` *JNI,* que são semelhantes a s. O JNI fornece três tipos diferentes de referências de objetos: referências locais, referências globais e referências globais fracas. Todos os três `System.IntPtr`são representados como , *mas* (de `IntPtr`acordo com `JNIEnv` a seção TIPOS de função JNI) nem todos os s retornados dos métodos são referências. Por exemplo, [JNIEnv.GetMethodID](xref:Android.Runtime.JNIEnv.GetMethodID*) retorna um `IntPtr`, mas não retorna uma `jmethodID`referência de objeto, ele retorna um . Consulte a documentação da [função JNI](https://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html) para obter detalhes.

As referências locais são criadas pela *maioria* dos métodos de criação de referência.
O Android só permite que um número limitado de referências locais existam a qualquer momento, geralmente 512. As referências locais podem ser excluídas via [JNIEnv.DeleteLocalRef](xref:Android.Runtime.JNIEnv.DeleteLocalRef*).
Ao contrário do JNI, nem todos os métodos de referência JNIEnv que retornam referências de objeto retornam referências locais; [JNIEnv.FindClass](xref:Android.Runtime.JNIEnv.FindClass*) retorna uma referência *global.* É fortemente recomendável que você exclua referências locais o mais rápido possível, possivelmente construindo um `JniHandleOwnership.TransferLocalRef` [Java.Lang.Object](xref:Java.Lang.Object) em torno do objeto e especificando para o construtor [Java.Lang.Object (alça IntPtr, transferência JniHandleOwnership).](xref:Java.Lang.Object#ctor*)

As referências globais são criadas por [JNIEnv.NewGlobalRef](xref:Android.Runtime.JNIEnv.NewGlobalRef*) e [JNIEnv.FindClass](xref:Android.Runtime.JNIEnv.FindClass*).
Eles podem ser destruídos com [JNIEnv.DeleteGlobalRef](xref:Android.Runtime.JNIEnv.DeleteGlobalRef*).
Os emuladores têm um limite de 2.000 referências globais pendentes, enquanto os dispositivos de hardware têm um limite de cerca de 52.000 referências globais.

Referências globais fracas só estão disponíveis no Android v2.2 (Froyo) e posteriormente. Referências globais fracas podem ser excluídas com [JNIEnv.DeleteWeakGlobalRef](xref:Android.Runtime.JNIEnv.DeleteWeakGlobalRef*).

### <a name="dealing-with-jni-local-references"></a>Lidando com referências locais jni

Os métodos [JNIEnv.GetObjectField,](xref:Android.Runtime.JNIEnv.GetObjectField*) [JNIEnv.GetStaticObjectField,](xref:Android.Runtime.JNIEnv.GetStaticObjectField*) [JNIEnv.CallObjectMethod,](xref:Android.Runtime.JNIEnv.CallObjectMethod*) [JNIEnv.CallNonvirtualObjectMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualObjectMethod*) e [JNIEnv.CallStaticObjectMethod](xref:Android.Runtime.JNIEnv.CallStaticObjectMethod*) retornam um `IntPtr` que contém `IntPtr.Zero` uma referência `null`local jni a um objeto Java, ou se Java retornou . Devido ao número limitado de referências locais que podem ser pendentes de uma só vez (512 entradas), é desejável garantir que as referências sejam excluídas em tempo hábil. Há três maneiras pelas quais as referências locais podem ser `Java.Lang.Object` tratadas: excluindo-as explicitamente, criando uma instância para segurá-las e usando `Java.Lang.Object.GetObject<T>()` para criar um invólucro callable gerenciado ao seu redor.

### <a name="explicitly-deleting-local-references"></a>Excluindo explicitamente referências locais

[JNIEnv.DeleteLocalRef](xref:Android.Runtime.JNIEnv.DeleteLocalRef*) é usado para excluir referências locais. Uma vez excluída a referência local, ela não pode mais ser usada, `JNIEnv.DeleteLocalRef` por isso deve-se tomar cuidado para garantir que essa seja a última coisa feita com a referência local.

```csharp
IntPtr lref = JNIEnv.CallObjectMethod(instance, methodID);
try {
    // Do something with `lref`
}
finally {
    JNIEnv.DeleteLocalRef (lref);
}
```

### <a name="wrapping-with-javalangobject"></a>Embrulhando com Java.Lang.Object

`Java.Lang.Object`fornece um construtor [Java.Lang.Object (alça IntPtr, transferência JniHandleOwnership)](xref:Java.Lang.Object#ctor*) que pode ser usado para envolver uma referência JNI de saída. O parâmetro [JniHandleOwnership](xref:Android.Runtime.JniHandleOwnership) determina `IntPtr` como o parâmetro deve ser tratado:

- [JniHandleOwnership.DoNotTransfer](xref:Android.Runtime.JniHandleOwnership.DoNotTransfer) &ndash; A `Java.Lang.Object` instância criada criará uma `handle` nova referência `handle` global a partir do parâmetro e não será alterada.
    O interlocutor é responsável `handle` pela libertação, se necessário.

- [JniHandleOwnership.TransferLocalRef](xref:Android.Runtime.JniHandleOwnership.TransferLocalRef) &ndash; A `Java.Lang.Object` instância criada criará uma `handle` nova referência `handle` global a partir do parâmetro e será excluída com [JNIEnv.DeleteLocalRef](xref:Android.Runtime.JNIEnv.DeleteLocalRef*) . O chamador `handle` não deve ser `handle` livre e não deve usar após a execução do construtor.

- [JniHandleOwnership.TransferGlobalRef](xref:Android.Runtime.JniHandleOwnership.TransferLocalRef) &ndash; A `Java.Lang.Object` instância criada assumirá `handle` a propriedade do parâmetro. O interlocutor não `handle` deve liberar.

Uma vez que os métodos de invocação do método JNI retornam os árbitros locais, `JniHandleOwnership.TransferLocalRef` normalmente seriam utilizados:

```csharp
IntPtr lref = JNIEnv.CallObjectMethod(instance, methodID);
var value = new Java.Lang.Object (lref, JniHandleOwnership.TransferLocalRef);
```

A referência global criada não será `Java.Lang.Object` liberada até que a instância seja coletada. Se você for capaz, a eliminação da instância liberará a referência global, acelerando as coletas de lixo:

```csharp
IntPtr lref = JNIEnv.CallObjectMethod(instance, methodID);
using (var value = new Java.Lang.Object (lref, JniHandleOwnership.TransferLocalRef)) {
    // use value ...
}
```

### <a name="using-javalangobjectgetobjectlttgt"></a>Usando&lt;Java.Lang.Object.GetObject&gt;T ()

`Java.Lang.Object`fornece um método [Java.Lang.Object.GetObject&lt;&gt;T (intPtr, transferência JniHandleOwnership)](xref:Java.Lang.Object.GetObject*) que pode ser usado para criar um invólucro callable gerenciado do tipo especificado.

O `T` tipo deve cumprir os seguintes requisitos:

1. `T`deve ser um tipo de referência.

1. `T`deve implementar `IJavaObject` a interface.

1. Se `T` não for uma classe `T` ou interface abstrata, então deve `(IntPtr,
    JniHandleOwnership)` fornecer a um construtor os tipos de parâmetros .

1. Se `T` for uma classe abstrata ou uma interface, `T` *deve* haver um *invocador* disponível para . Um invocador é um tipo não `T` abstrato que herda ou implementa, `T` e tem o mesmo nome de `T` um sufixo Invoker. Por exemplo, se T `Java.Lang.IRunnable` é a `Java.Lang.IRunnableInvoker` interface, então o `(IntPtr,
    JniHandleOwnership)` tipo deve existir e deve conter o construtor necessário.

Uma vez que os métodos de invocação do método JNI retornam os árbitros locais, `JniHandleOwnership.TransferLocalRef` normalmente seriam utilizados:

```csharp
IntPtr lrefString = JNIEnv.CallObjectMethod(instance, methodID);
Java.Lang.String value = Java.Lang.Object.GetObject<Java.Lang.String>( lrefString, JniHandleOwnership.TransferLocalRef);
```

<a name="_Looking_up_Java_Types" />

## <a name="looking-up-java-types"></a>Procurando os tipos java

Para procurar um campo ou método no JNI, o tipo de declaração para o campo ou método deve ser analisado primeiro. O método [Android.Runtime.JNIEnv.FindClass(string)](xref:Android.Runtime.JNIEnv.FindClass*)é usado para procurar tipos java. O parâmetro string é a *referência de tipo simplificada* ou a *referência completa* do tipo para o tipo Java. Consulte a [seção REFERÊNCIAs do tipo JNI](#_JNI_Type_References) para obter detalhes sobre referências de tipo simplificadas e completas.

Nota: Ao `JNIEnv` contrário de qualquer `FindClass` outro método que retorna instâncias de objeto, retorna uma referência global, não uma referência local.

<a name="_Instance_Fields" />

## <a name="instance-fields"></a>Campos de instância

Os campos são manipulados através *de ids de campo.* Os IDs de campo são obtidos via [JNIEnv.GetFieldID](xref:Android.Runtime.JNIEnv.GetFieldID*), que requer a classe em que o campo é definido, o nome do campo e a [Assinatura do Tipo JNI](#JNI_Type_Signatures) do campo.

Os IDs de campo não precisam ser liberados e são válidos desde que o tipo Java correspondente seja carregado. (O Android não suporta atualmente o descarregamento de classe.)

Existem dois conjuntos de métodos para manipular campos de instância: um para campos de instância de leitura e outro para campos de instância de escrita. Todos os conjuntos de métodos requerem um ID de campo para ler ou escrever o valor de campo.

### <a name="reading-instance-field-values"></a>Valores de campo de instância de leitura

O conjunto de métodos para leitura de valores de campo de instância segue o padrão de nomeação:

```csharp
* JNIEnv.Get*Field(IntPtr instance, IntPtr fieldID);
```

onde `*` está o tipo de campo:

- [JNIEnv.GetObjectField](xref:Android.Runtime.JNIEnv.GetObjectField*) &ndash; Leia o valor de qualquer campo de instância que `java.lang.Object` não seja um tipo de embutido, como , matrizes e tipos de interface. O valor devolvido é uma referência local jni.

- [JNIEnv.GetBooleanField](xref:Android.Runtime.JNIEnv.GetBooleanField*) &ndash; Leia o `bool` valor dos campos de instância.

- [JNIEnv.GetByteField](xref:Android.Runtime.JNIEnv.GetByteField*) &ndash; Leia o `sbyte` valor dos campos de instância.

- [JNIEnv.GetCharField](xref:Android.Runtime.JNIEnv.GetCharField*) &ndash; Leia o `char` valor dos campos de instância.

- [JNIEnv.GetShortField](xref:Android.Runtime.JNIEnv.GetShortField*) &ndash; Leia o `short` valor dos campos de instância.

- [JNIEnv.GetIntField](xref:Android.Runtime.JNIEnv.GetIntField*) &ndash; Leia o `int` valor dos campos de instância.

- [JNIEnv.GetLongField](xref:Android.Runtime.JNIEnv.GetLongField*) &ndash; Leia o `long` valor dos campos de instância.

- [JNIEnv.GetFloatField](xref:Android.Runtime.JNIEnv.GetFloatField*) &ndash; Leia o `float` valor dos campos de instância.

- [JNIEnv.GetDoubleField](xref:Android.Runtime.JNIEnv.GetDoubleField*) &ndash; Leia o `double` valor dos campos de instância.

### <a name="writing-instance-field-values"></a>Valores de campo de instância de escrita

O conjunto de métodos para escrever valores de campo de instância segue o padrão de nomeação:

```csharp
JNIEnv.SetField(IntPtr instance, IntPtr fieldID, Type value);
```

onde *tipo* é o tipo do campo:

- [JNIEnv.SetField](xref:Android.Runtime.JNIEnv.SetField*) &ndash; ) Escreva o valor de qualquer campo que não `java.lang.Object` seja um tipo de embutido, como , matrizes e tipos de interface. O `IntPtr` valor pode ser uma referência local JNI, referência global `IntPtr.Zero` JNI, referência global fraca JNI, ou (para `null` ).

- [JNIEnv.SetField](xref:Android.Runtime.JNIEnv.SetField*) &ndash; ) Escreva `bool` o valor dos campos de instância.

- [JNIEnv.SetField](xref:Android.Runtime.JNIEnv.SetField*) &ndash; ) Escreva `sbyte` o valor dos campos de instância.

- [JNIEnv.SetField](xref:Android.Runtime.JNIEnv.SetField*) &ndash; ) Escreva `char` o valor dos campos de instância.

- [JNIEnv.SetField](xref:Android.Runtime.JNIEnv.SetField*) &ndash; ) Escreva `short` o valor dos campos de instância.

- [JNIEnv.SetField](xref:Android.Runtime.JNIEnv.SetField*) &ndash; ) Escreva `int` o valor dos campos de instância.

- [JNIEnv.SetField](xref:Android.Runtime.JNIEnv.SetField*) &ndash; ) Escreva `long` o valor dos campos de instância.

- [JNIEnv.SetField](xref:Android.Runtime.JNIEnv.SetField*) &ndash; ) Escreva `float` o valor dos campos de instância.

- [JNIEnv.SetField](xref:Android.Runtime.JNIEnv.SetField*) &ndash; ) Escreva `double` o valor dos campos de instância.

<a name="_Static_Fields" />

## <a name="static-fields"></a>Campos estáticos

Os campos estáticos são manipulados através *de IDs de campo*. Os IDs de campo são obtidos via [JNIEnv.GetStaticFieldID](xref:Android.Runtime.JNIEnv.GetStaticFieldID*), que requer a classe em que o campo é definido, o nome do campo e a Assinatura do [Tipo JNI](#JNI_Type_Signatures) do campo.

Os IDs de campo não precisam ser liberados e são válidos desde que o tipo Java correspondente seja carregado. (O Android não suporta atualmente o descarregamento de classe.)

Existem dois conjuntos de métodos para manipular campos estáticos: um para leitura de campos de instância e outro para campos de exemplo de escrita. Todos os conjuntos de métodos requerem um ID de campo para ler ou escrever o valor de campo.

### <a name="reading-static-field-values"></a>Leitura de valores de campo estáticos

O conjunto de métodos para leitura de valores de campo estáticos segue o padrão de nomeação:

```csharp
* JNIEnv.GetStatic*Field(IntPtr class, IntPtr fieldID);
```

onde `*` está o tipo de campo:

- [JNIEnv.GetStaticObjectField](xref:Android.Runtime.JNIEnv.GetStaticObjectField*) &ndash; Leia o valor de qualquer campo estático que `java.lang.Object` não seja um tipo de embutido, como, matrizes e tipos de interface. O valor devolvido é uma referência local jni.

- [JNIEnv.GetStaticBooleanField](xref:Android.Runtime.JNIEnv.GetStaticBooleanField*) &ndash; Leia o `bool` valor dos campos estáticos.

- [JNIEnv.GetStaticByteField](xref:Android.Runtime.JNIEnv.GetStaticByteField*) &ndash; Leia o `sbyte` valor dos campos estáticos.

- [JNIEnv.GetStaticCharField](xref:Android.Runtime.JNIEnv.GetStaticCharField*) &ndash; Leia o `char` valor dos campos estáticos.

- [JNIEnv.GetStaticShortField](xref:Android.Runtime.JNIEnv.GetStaticShortField*) &ndash; Leia o `short` valor dos campos estáticos.

- [JNIEnv.GetStaticLongField](xref:Android.Runtime.JNIEnv.GetStaticLongField*) &ndash; Leia o `long` valor dos campos estáticos.

- [JNIEnv.GetStaticFloatField](xref:Android.Runtime.JNIEnv.GetStaticFloatField*) &ndash; Leia o `float` valor dos campos estáticos.

- [JNIEnv.GetStaticDoubleField](xref:Android.Runtime.JNIEnv.GetStaticDoubleField*) &ndash; Leia o `double` valor dos campos estáticos.

### <a name="writing-static-field-values"></a>Escrevendo valores de campo estáticos

O conjunto de métodos para escrever valores de campo estáticos segue o padrão de nomeação:

```csharp
JNIEnv.SetStaticField(IntPtr class, IntPtr fieldID, Type value);
```

onde *tipo* é o tipo do campo:

- [JNIEnv.SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*) &ndash; ) Escreva o valor de qualquer campo estático que `java.lang.Object` não seja um tipo de embutido, como, matrizes e tipos de interface. O `IntPtr` valor pode ser uma referência local JNI, referência global `IntPtr.Zero` JNI, referência global fraca JNI, ou (para `null` ).

- [JNIEnv.SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*) &ndash; ) Escreva `bool` o valor dos campos estáticos.

- [JNIEnv.SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*) &ndash; ) Escreva `sbyte` o valor dos campos estáticos.

- [JNIEnv.SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*) &ndash; ) Escreva `char` o valor dos campos estáticos.

- [JNIEnv.SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*) &ndash; ) Escreva `short` o valor dos campos estáticos.

- [JNIEnv.SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*) &ndash; ) Escreva `int` o valor dos campos estáticos.

- [JNIEnv.SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*) &ndash; ) Escreva `long` o valor dos campos estáticos.

- [JNIEnv.SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*) &ndash; ) Escreva `float` o valor dos campos estáticos.

- [JNIEnv.SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*) &ndash; ) Escreva `double` o valor dos campos estáticos.

<a name="_Instance_Methods" />

## <a name="instance-methods"></a>Métodos de ocorrência

Os métodos de instância são invocados através *de IDs de método*. Os IDs do método são obtidos via [JNIEnv.GetMethodID](xref:Android.Runtime.JNIEnv.GetMethodID*), que requer o tipo em que o método é definido, o nome do método e a [Assinatura do Tipo JNI](#JNI_Type_Signatures) do método.

Os IDs do método não precisam ser liberados e são válidos desde que o tipo Java correspondente seja carregado. (O Android não suporta atualmente o descarregamento de classe.)

Existem dois conjuntos de métodos para invocar métodos: um para invocar métodos virtualmente, e outro para invocar métodos não virtualmente. Ambos os conjuntos de métodos exigem um ID de método para invocar o método, e a invocação não virtual também exige que você especifique qual implementação de classe deve ser invocada.

Os métodos de interface só podem ser olhados dentro do tipo declaração; métodos provenientes de interfaces estendidas/herdadas não podem ser examinados. Consulte a seção Interfaces de Vinculação / Implementação de Invocadores posteriores para obter mais detalhes.

Qualquer método declarado na classe ou em qualquer classe base ou interface implementada pode ser examinado.

### <a name="virtual-method-invocation"></a>Invocação do Método Virtual

O conjunto de métodos para invocar métodos segue virtualmente o padrão de nomeação:

```csharp
* JNIEnv.Call*Method( IntPtr instance, IntPtr methodID, params JValue[] args );
```

onde `*` está o tipo de retorno do método.

- [JNIEnv.CallObjectMethod](xref:Android.Runtime.JNIEnv.CallObjectMethod*) &ndash; Invocar um método que retorna um tipo `java.lang.Object` não incorporado, como, matrizes e interfaces. O valor devolvido é uma referência local jni.

- [JNIEnv.CallBooleanMethod](xref:Android.Runtime.JNIEnv.CallBooleanMethod*) &ndash; Invocar um método `bool` que retorna um valor.

- [JNIEnv.CallByteMethod](xref:Android.Runtime.JNIEnv.CallByteMethod*) &ndash; Invocar um método `sbyte` que retorna um valor.

- [JNIEnv.CallCharMethod](xref:Android.Runtime.JNIEnv.CallCharMethod*) &ndash; Invocar um método `char` que retorna um valor.

- [JNIEnv.CallShortMethod](xref:Android.Runtime.JNIEnv.CallShortMethod*) &ndash; Invocar um método `short` que retorna um valor.

- [JNIEnv.CallLongMethod](xref:Android.Runtime.JNIEnv.CallLongMethod*) &ndash; Invocar um método `long` que retorna um valor.

- [JNIEnv.CallFloatMethod](xref:Android.Runtime.JNIEnv.CallFloatMethod*) &ndash; Invocar um método `float` que retorna um valor.

- [JNIEnv.CallDoubleMethod](xref:Android.Runtime.JNIEnv.CallDoubleMethod*) &ndash; Invocar um método `double` que retorna um valor.

### <a name="non-virtual-method-invocation"></a>Invocação de métodos não virtuais

O conjunto de métodos para invocar métodos não virtualmente segue o padrão de nomeação:

```csharp
* JNIEnv.CallNonvirtual*Method( IntPtr instance, IntPtr class, IntPtr methodID, params JValue[] args );
```

onde `*` está o tipo de retorno do método. A invocação de métodos não virtuais é geralmente usada para invocar o método base de um método virtual.

- [JNIEnv.CallNonvirtualObjectMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualObjectMethod*) &ndash; Não invoca virtualmente um método que retorna um `java.lang.Object` tipo não incorporado, como, matrizes e interfaces. O valor devolvido é uma referência local jni.

- [JNIEnv.CallNonvirtualBooleanMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualBooleanMethod*) &ndash; Não invoca virtualmente um `bool` método que retorna um valor.

- [JNIEnv.CallNonvirtualByteMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualByteMethod*) &ndash; Não invoca virtualmente um `sbyte` método que retorna um valor.

- [JNIEnv.CallNonvirtualCharMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualCharMethod*) &ndash; Não invoca virtualmente um `char` método que retorna um valor.

- [JNIEnv.CallNonvirtualShortMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualShortMethod*) &ndash; Não invoca virtualmente um `short` método que retorna um valor.

- [JNIEnv.CallNonvirtualLongMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualLongMethod*) &ndash; Não invoca virtualmente um `long` método que retorna um valor.

- [JNIEnv.CallNonvirtualFloatMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualFloatMethod*) &ndash; Não invoca virtualmente um `float` método que retorna um valor.

- [JNIEnv.CallNonvirtualDoubleMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualDoubleMethod*) &ndash; Não invoca virtualmente um `double` método que retorna um valor.

<a name="_Static_Methods" />

## <a name="static-methods"></a>Métodos estáticos

Os métodos estáticos são invocados através *de IDs de método*. Os IDs do método são obtidos via [JNIEnv.GetStaticMethodID](xref:Android.Runtime.JNIEnv.GetStaticMethodID*), que requer o tipo em que o método é definido, o nome do método e a Assinatura do [Tipo JNI](#JNI_Type_Signatures) do método.

Os IDs do método não precisam ser liberados e são válidos desde que o tipo Java correspondente seja carregado. (O Android não suporta atualmente o descarregamento de classe.)

### <a name="static-method-invocation"></a>Invocação do Método Estático

O conjunto de métodos para invocar métodos segue virtualmente o padrão de nomeação:

```csharp
* JNIEnv.CallStatic*Method( IntPtr class, IntPtr methodID, params JValue[] args );
```

onde `*` está o tipo de retorno do método.

- [JNIEnv.CallStaticObjectMethod](xref:Android.Runtime.JNIEnv.CallStaticObjectMethod*) &ndash; Invocar um método estático que retorna um `java.lang.Object` tipo não construído, como, matrizes e interfaces. O valor devolvido é uma referência local jni.

- [JNIEnv.CallStaticBooleanMethod](xref:Android.Runtime.JNIEnv.CallStaticBooleanMethod*) &ndash; Invocar um método `bool` estático que retorna um valor.

- [JNIEnv.CallStaticByteMethod](xref:Android.Runtime.JNIEnv.CallStaticByteMethod*) &ndash; Invocar um método `sbyte` estático que retorna um valor.

- [JNIEnv.CallStaticCharMethod](xref:Android.Runtime.JNIEnv.CallStaticCharMethod*) &ndash; Invocar um método `char` estático que retorna um valor.

- [JNIEnv.CallStaticShortMethod](xref:Android.Runtime.JNIEnv.CallStaticShortMethod*) &ndash; Invocar um método `short` estático que retorna um valor.

- [JNIEnv.CallStaticLongMethod](xref:Android.Runtime.JNIEnv.CallLongMethod*) &ndash; Invocar um método `long` estático que retorna um valor.

- [JNIEnv.CallStaticFloatMethod](xref:Android.Runtime.JNIEnv.CallStaticFloatMethod*) &ndash; Invocar um método `float` estático que retorna um valor.

- [JNIEnv.CallStaticDoubleMethod](xref:Android.Runtime.JNIEnv.CallStaticDoubleMethod*) &ndash; Invocar um método `double` estático que retorna um valor.

<a name="JNI_Type_Signatures" />

## <a name="jni-type-signatures"></a>Assinaturas do tipo JNI

[As assinaturas do tipo JNI](https://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/types.html#wp16432) são [referências do tipo JNI](#_JNI_Type_References) (embora não sejam referências de tipo simplificadas), exceto para métodos. Com métodos, a Assinatura do Tipo JNI `'('`é um parêntese aberto, seguido pelas referências de tipo para todos os tipos de parâmetros concatenados juntos (sem comores separados ou qualquer outra coisa), seguido por um parêntese `')'`de fechamento, seguido pela referência do tipo JNI do tipo de retorno do método.

Por exemplo, dado o método Java:

```java
long f(int n, String s, int[] array);
```

A assinatura do tipo JNI seria:

```csharp
(ILjava/lang/String;[I)J
```

Em geral, é *fortemente* recomendável `javap` usar o comando para determinar assinaturas JNI. Por exemplo, a assinatura do tipo JNI do método [java.lang.Thread.State.valueOf(String)](https://developer.android.com/reference/java/lang/Thread.State.html#valueOf(java.lang.String)) é "(Ljava/lang/String;)Ljava/lang/Thread$State;", enquanto a assinatura do tipo JNI do método [java.lang.Thread.State.values](https://developer.android.com/reference/java/lang/Thread.State.html#values) é "()[Ljava/lang/Thread$State;". Cuidado com os ponto e vírgula que se arrastam; esses *são* parte da assinatura tipo JNI.

<a name="_JNI_Type_References" />

## <a name="jni-type-references"></a>Referências do tipo JNI

As referências do tipo JNI são diferentes das referências do tipo Java. Você não pode usar nomes `java.lang.String` de tipo Java totalmente qualificados, como `"java/lang/String"` `"Ljava/lang/String;"`com JNI, você deve usar as variações JNI ou , dependendo do contexto; veja abaixo para mais detalhes.
Existem quatro tipos de referências do tipo JNI:

- **embutido**
- **Simplificado**
- **type**
- **Matriz**

### <a name="built-in-type-references"></a>Referências de tipo incorporadas

As referências de tipo incorporadas são um único caractere, usado para referenciar tipos de valor incorporados. O mapeamento é o seguinte:

- `"B"`para `sbyte` .
- `"S"`para `short` .
- `"I"`para `int` .
- `"J"`para `long` .
- `"F"`para `float` .
- `"D"`para `double` .
- `"C"`para `char` .
- `"Z"`para `bool` .
- `"V"`para `void` tipos de retorno de método.

<a name="_Simplified_Type_References_1" />

### <a name="simplified-type-references"></a>Referências de tipo simplificadas

Referências de tipo simplificadas só podem ser usadas em [JNIEnv.FindClass(string)](xref:Android.Runtime.JNIEnv.FindClass*)).
Existem duas maneiras de obter uma referência de tipo simplificada:

1. A partir de um nome `'.'` Java totalmente qualificado, substitua `'/'` cada nome `'.'` do pacote `'$'` e antes do nome do tipo com , e cada um dentro de um nome de tipo com .

1. Leia a `'unzip -l android.jar | grep JavaName'` saída de .

Qualquer um dos dois resultará no tipo Java [java.lang.Thread.State](https://developer.android.com/reference/java/lang/Thread.State.html) sendo `java/lang/Thread$State`mapeado para a referência de tipo simplificada .

### <a name="type-references"></a>Referências de tipo

Uma referência de tipo é uma referência de tipo `'L'` incorporada ou `';'` uma referência de tipo simplificada com um prefixo e um sufixo. Para o tipo Java [java.lang.String,](https://developer.android.com/reference/java/lang/String.html)a `"java/lang/String"`referência de tipo `"Ljava/lang/String;"`simplificada é , enquanto a referência de tipo é .

As referências de tipo são usadas com referências de tipo Array e com assinaturas JNI.

Uma maneira adicional de obter uma referência `'javap -s -classpath android.jar fully.qualified.Java.Name'`de tipo é lendo a saída de .
Dependendo do tipo envolvido, você pode usar uma declaração de construtor ou tipo de retorno do método para determinar o nome JNI. Por exemplo:

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

`Thread.State`é um tipo de enum Java, então `valueOf` podemos usar a Assinatura do método para determinar que a referência do tipo é Ljava/lang/Thread$State;.

### <a name="array-type-references"></a>Referências do tipo de matriz

As referências `'['` do tipo de matriz são prefixadas a uma referência do tipo JNI.
As referências de tipo simplificadas não podem ser usadas ao especificar matrizes.

`int[]` Por exemplo, `"[I"` `int[][]` é `"[[I"`, `java.lang.Object[]` `"[Ljava/lang/Object;"`é , e é .

## <a name="java-generics-and-type-erasure"></a>Java Genéricos e Apagamento de Tipo

*Na maioria das* vezes, como visto através do JNI, os genéricos Java *não existem.*
Existem algumas "rugas", mas essas rugas estão em como Java interage com genéricos, não com a forma como jni olha para cima e invoca membros genéricos.

Não há diferença entre um tipo ou membro genérico e um tipo ou membro não genérico ao interagir através do JNI. Por exemplo, o tipo genérico [&lt;java.lang.Class T&gt; ](https://developer.android.com/reference/java/lang/Class.html) também é o tipo `java.lang.Class`genérico "cru", `"java/lang/Class"`ambos com a mesma referência de tipo simplificado, .

## <a name="java-native-interface-support"></a>Suporte à interface nativa java

[Android.Runtime.JNIEnv](xref:Android.Runtime.JNIEnv) é um invólucro gerenciado para a Interface Nativa Jave (JNI). As funções JNI são declaradas dentro da [Especificação de Interface Nativa Java,](https://download.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html) `IntPtr` embora os `jobject`métodos tenham sido alterados para remover o parâmetro explícito `jclass` `JNIEnv*` e são usados em vez de , , `jmethodID`etc. Por exemplo, considere a [função JNI NewObject:](https://download.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html#wp4517)

```csharp
jobject NewObjectA(JNIEnv *env, jclass clazz, jmethodID methodID, jvalue *args);
```

Isso é exposto como o método [JNIEnv.NewObject:](xref:Android.Runtime.JNIEnv.NewObject*)

```csharp
public static IntPtr NewObject(IntPtr clazz, IntPtr jmethod, params JValue[] parms);
```

Traduzir entre as duas chamadas é razoavelmente simples. Em C você teria:

```c
jobject CreateMapActivity(JNIEnv *env)
{
    jclass    Map_Class   = (*env)->FindClass(env, "mono/samples/googlemaps/MyMapActivity");
    jmethodID Map_defCtor = (*env)->GetMethodID (env, Map_Class, "<init>", "()V");
    jobject   instance    = (*env)->NewObject (env, Map_Class, Map_defCtor);

    return instance;
}
```

O equivalente C# seria:

```csharp
IntPtr CreateMapActivity()
{
    IntPtr Map_Class   = JNIEnv.FindClass ("mono/samples/googlemaps/MyMapActivity");
    IntPtr Map_defCtor = JNIEnv.GetMethodID (Map_Class, "<init>", "()V");
    IntPtr instance    = JNIEnv.NewObject (Map_Class, Map_defCtor);

    return instance;
}
```

Uma vez que você tenha uma instância java object realizada em um IntPtr, você provavelmente vai querer fazer algo com ele. Você pode usar métodos JNIEnv como [JNIEnv.CallVoidMethod()](xref:Android.Runtime.JNIEnv.CallVoidMethod*) para fazê-lo, mas se já houver um invólucro C# analógico, então você vai querer construir um invólucro sobre a referência JNI. Você pode fazê-lo através do método de extensão [extensionista Extensions.JavaCast\<T>:](xref:Android.Runtime.Extensions.JavaCast*)

```csharp
IntPtr lrefActivity = CreateMapActivity();

// imagine that Activity were instead an interface or abstract type...
Activity mapActivity = new Java.Lang.Object(lrefActivity, JniHandleOwnership.TransferLocalRef)
    .JavaCast<Activity>();
```

Você também pode usar o método [\<Java.Lang.Object.GetObject T>:](xref:Java.Lang.Object.GetObject*)

```csharp
IntPtr lrefActivity = CreateMapActivity();

// imagine that Activity were instead an interface or abstract type...
Activity mapActivity = Java.Lang.Object.GetObject<Activity>(lrefActivity, JniHandleOwnership.TransferLocalRef);
```

Além disso, todas as funções JNI foram `JNIEnv*` modificadas removendo o parâmetro presente em cada função JNI.

## <a name="summary"></a>Resumo

Lidar diretamente com o JNI é uma experiência terrível que deve ser evitada a todo custo. Infelizmente, nem sempre é evitável; espero que este guia forneça alguma assistência quando você acertar os casos Java desvinculados com Mono para Android.

## <a name="related-links"></a>Links relacionados

- [Especificação de interface nativa de Java](https://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/jniTOC.html)
- [Funções de interface nativa de Java](https://download.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html)
