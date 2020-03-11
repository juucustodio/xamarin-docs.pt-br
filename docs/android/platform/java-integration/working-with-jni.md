---
title: Trabalhando com JNI e Xamarin. Android
description: O Xamarin. Android permite a gravação de C# aplicativos Android com o em vez do Java. Vários assemblies são fornecidos com o Xamarin. Android, que fornecem associações para bibliotecas Java, incluindo mono. Android. dll e mono. Android. GoogleMaps. dll. No entanto, as associações não são fornecidas para todas as bibliotecas Java possíveis e as associações que são fornecidas não podem associar todos os tipos e membros de Java. Para usar membros e tipos de Java não associados, a interface nativa do Java (JNI) pode ser usada. Este artigo ilustra como usar o JNI para interagir com tipos e membros de Java de aplicativos Xamarin. Android.
ms.prod: xamarin
ms.assetid: A417DEE9-7B7B-4E35-A79C-284739E3838E
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/09/2018
ms.openlocfilehash: 0fa717a775ff2f1ace9e248a8afde8d373e8a1f8
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2020
ms.locfileid: "78292384"
---
# <a name="working-with-jni-and-xamarinandroid"></a>Trabalhando com JNI e Xamarin. Android

_O Xamarin. Android permite a gravação de C# aplicativos Android com o em vez do Java. Vários assemblies são fornecidos com o Xamarin. Android, que fornecem associações para bibliotecas Java, incluindo mono. Android. dll e mono. Android. GoogleMaps. dll. No entanto, as associações não são fornecidas para todas as bibliotecas Java possíveis e as associações que são fornecidas não podem associar todos os tipos e membros de Java. Para usar membros e tipos de Java não associados, a interface nativa do Java (JNI) pode ser usada. Este artigo ilustra como usar o JNI para interagir com tipos e membros de Java de aplicativos Xamarin. Android._

## <a name="overview"></a>Visão geral

Nem sempre é necessário ou pode criar um wrapper resgatável gerenciado (MCW) para invocar o código Java. Em muitos casos, o JNI "embutido" é perfeitamente aceitável e útil para uso único de membros Java não associados. Geralmente, é mais simples usar JNI para invocar um único método em uma classe Java do que gerar uma associação. jar inteira.

O Xamarin. Android fornece o `Mono.Android.dll` assembly, que fornece uma associação para a biblioteca de `android.jar` do Android. Tipos e membros que não estão presentes em `Mono.Android.dll` e tipos não presentes em `android.jar` podem ser usados pela associação manual deles. Para associar tipos e membros de Java, use a **interface nativa do Java** (**JNI**) para Pesquisar tipos, ler e gravar campos e invocar métodos.

A API JNI no Xamarin. Android é conceitualmente muito semelhante à API de `System.Reflection` no .NET: ela possibilita que você procure tipos e membros por nome, ler e gravar valores de campo, métodos de invocação e muito mais. Você pode usar JNI e o atributo personalizado `Android.Runtime.RegisterAttribute` para declarar métodos virtuais que podem ser associados para dar suporte à substituição. Você pode associar interfaces para que elas possam ser implementadas C#no.

Este documento explica:

- Como o JNI se refere a tipos.
- Como Pesquisar, ler e gravar campos.
- Como Pesquisar e invocar métodos.
- Como expor métodos virtuais para permitir a substituição de código gerenciado.
- Como expor interfaces.

## <a name="requirements"></a>Requisitos

JNI, como exposto pelo [namespace Android. Runtime. JNIEnv](xref:Android.Runtime.JNIEnv), está disponível em todas as versões do Xamarin. Android.
Para associar tipos e interfaces Java, você deve usar o Xamarin. Android 4,0 ou posterior.

## <a name="managed-callable-wrappers"></a>Wrappers callable gerenciados

Um **wrapper resgatável gerenciado** (**MCW**) é uma *Associação* para uma classe ou interface java que encapsula toda a maquina JNI para que o código do C# cliente não precise se preocupar com a complexidade subjacente do JNI. A maioria das `Mono.Android.dll` consiste em wrappers callable gerenciados.

Os wrappers callable gerenciados têm duas finalidades:

1. Encapsular o uso do JNI para que o código do cliente não precise saber sobre a complexidade subjacente.
1. Possibilitar tipos de Java de subclasse e implementar interfaces Java.

A primeira finalidade é apenas para conveniência e encapsulamento de complexidade para que os consumidores tenham um conjunto simples e gerenciado de classes para usar. Isso requer o uso dos vários membros do [JNIEnv](xref:Android.Runtime.JNIEnv) , conforme descrito posteriormente neste artigo. Tenha em mente que os wrappers chamáveis gerenciados não são estritamente necessários &ndash; uso "embutido" JNI é perfeitamente aceitável e é útil para uso único de membros Java não associados. A subclasse e a implementação de interface exigem o uso de wrappers callable gerenciados.

## <a name="android-callable-wrappers"></a>Callable Wrappers do Android

ACW (Android callable wrappers) são necessários sempre que o tempo de execução do Android (arte) precisa invocar código gerenciado; Esses wrappers são necessários porque não há como registrar classes com arte em tempo de execução.
(Especificamente, a função [DefineClass](https://docs.oracle.com/javase/6/docs/technotes/guides/jni/spec/functions.html#wp15986) JNI não é suportada pelo tempo de execução do Android. Os wrappers que podem ser chamados pelo Android, portanto, constituem a falta de suporte ao registro de tipo de tempo de execução.)

Sempre que o código do Android precisa executar um método virtual ou de interface que é substituído ou implementado em código gerenciado, o Xamarin. Android deve fornecer um proxy Java para que esse método seja expedido para o tipo gerenciado apropriado. Esses tipos de proxy Java são código Java que têm a classe base "mesma" e a lista de interfaces Java como o tipo gerenciado, implementando os mesmos construtores e declarando qualquer classe base substituída e métodos de interface.

Os wrappers que podem ser chamados pelo Android são gerados pelo programa **monodroid. exe** durante o [processo de compilação](~/android/deploy-test/building-apps/build-process.md)e são gerados para todos os tipos que (direta ou indiretamente) herdam [Java. lang. Object](xref:Java.Lang.Object).

### <a name="implementing-interfaces"></a>Implementando interfaces

Há ocasiões em que talvez seja necessário implementar uma interface do Android, (como [Android. Content. IComponentCallbacks](xref:Android.Content.IComponentCallbacks)).

Todas as classes e interfaces do Android estendem a interface [Android. Runtime. IJavaObject](xref:Android.Runtime.IJavaObject) ; Portanto, todos os tipos Android devem implementar `IJavaObject`.
O Xamarin. Android aproveita esse fato &ndash; ele usa `IJavaObject` para fornecer ao Android um proxy Java (um wrapper callable Android) para o tipo gerenciado fornecido. Como **monodroid. exe** só procura `Java.Lang.Object` subclasses (que devem implementar `IJavaObject`), a subclasse `Java.Lang.Object` nos fornece uma maneira de implementar interfaces em código gerenciado. Por exemplo:

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

*O restante deste artigo fornece detalhes de implementação sujeitos a alterações sem aviso prévio* (e é apresentado aqui somente porque os desenvolvedores podem estar curiosos sobre o que está acontecendo nos bastidores).

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

Observe que a classe base é preservada e as declarações de método nativo são fornecidas para cada método que é substituído no código gerenciado.

### <a name="exportattribute-and-exportfieldattribute"></a>ExportAttribute e ExportFieldAttribute

Normalmente, o Xamarin. Android gera automaticamente o código Java que compreende o ACW; Essa geração se baseia nos nomes de classe e método quando uma classe deriva de uma classe Java e substitui os métodos Java existentes. No entanto, em alguns cenários, a geração de código não é adequada, conforme descrito abaixo:

- O Android dá suporte a nomes de ação em atributos XML de layout, por exemplo, o atributo XML [Android: OnClick](xref:Android.Views.View.IOnClickListener.OnClick*) . Quando especificado, a instância de exibição inplana tenta pesquisar o método Java.

- A interface [Java. IO. Serializable](https://developer.android.com/reference/java/io/Serializable.html) requer `readObject` e `writeObject` métodos. Como não são membros dessa interface, nossa implementação gerenciada correspondente não expõe esses métodos ao código Java.

- A interface [Android. os.](xref:Android.OS.Parcelable) configurable espera que uma classe de implementação deva ter um campo estático `CREATOR` do tipo `Parcelable.Creator`. O código Java gerado requer algum campo explícito. Com nosso cenário padrão, não há nenhuma maneira de produzir o campo no código Java a partir do código gerenciado.

Como a geração de código não fornece uma solução para gerar métodos Java arbitrários com nomes arbitrários, começando com o Xamarin. Android 4,2, o [ExportAttribute](xref:Java.Interop.ExportAttribute) e o [ExportFieldAttribute](xref:Java.Interop.ExportFieldAttribute) foram introduzidos para oferecer uma solução para os cenários acima. Ambos os atributos residem no namespace `Java.Interop`:

- `ExportAttribute` &ndash; especifica um nome de método e seus tipos de exceção esperados (para fornecer "throws" explícitos em Java). Quando ele é usado em um método, o método "exporta" um método Java que gera um código de expedição para a invocação JNI correspondente ao método gerenciado. Isso pode ser usado com `android:onClick` e `java.io.Serializable`.

- `ExportFieldAttribute` &ndash; especifica um nome de campo. Ele reside em um método que funciona como um inicializador de campo. Isso pode ser usado com `android.os.Parcelable`.

#### <a name="troubleshooting-exportattribute-and-exportfieldattribute"></a>Solução de problemas de ExportAttribute e ExportFieldAttribute

- Falha no empacotamento devido à falta de &ndash; **mono. Android. Export. dll** se você usou `ExportAttribute` ou `ExportFieldAttribute` em alguns métodos em seu código ou em bibliotecas dependentes, será necessário adicionar **mono. Android. Export. dll**. Este assembly é isolado para dar suporte ao código de retorno de chamada do Java. Ele é separado de **mono. Android. dll** , pois adiciona tamanho adicional ao aplicativo.

- Na compilação da versão, `MissingMethodException` ocorre para métodos de exportação &ndash; na compilação da versão, `MissingMethodException` ocorre para métodos de exportação. (Esse problema é corrigido na versão mais recente do Xamarin. Android.)

### <a name="exportparameterattribute"></a>ExportParameterAttribute

`ExportAttribute` e `ExportFieldAttribute` fornecem funcionalidade que o código de tempo de execução Java pode usar. Esse código de tempo de execução acessa código gerenciado por meio dos métodos JNI gerados orientados por esses atributos. Como resultado, não há nenhum método Java existente associado ao método gerenciado; Portanto, o método Java é gerado a partir de uma assinatura de método gerenciado.

No entanto, esse caso não é totalmente determinante. Mais notavelmente, isso é verdadeiro em alguns mapeamentos avançados entre tipos gerenciados e tipos de Java, como:

- InputStream
- OutputStream
- XmlPullParser
- XmlResourceParser

Quando tipos como esses são necessários para métodos exportados, o `ExportParameterAttribute` deve ser usado para conceder explicitamente ao parâmetro correspondente ou ao valor de retorno um tipo.

### <a name="annotation-attribute"></a>Atributo de anotação

No Xamarin. Android 4,2, convertemos `IAnnotation` tipos de implementação em atributos (System. Attribute) e adicionamos suporte para geração de anotação em wrappers Java.

Isso significa as seguintes alterações direcionais:

- O gerador de associação gera `Java.Lang.DeprecatedAttribute` de `java.Lang.Deprecated` (embora ele deva ser `[Obsolete]` em código gerenciado).

- Isso não significa que a classe de `Java.Lang.Deprecated` existente será desapareceda. Esses objetos baseados em Java ainda podem ser usados como objetos Java usuais (se esse uso existir). Haverá `Deprecated` e `DeprecatedAttribute` classes.

- A classe `Java.Lang.DeprecatedAttribute` está marcada como `[Annotation]`. Quando há um atributo personalizado que é herdado deste `[Annotation]` atributo, a tarefa do MSBuild gerará uma anotação Java para esse atributo personalizado (@Deprecated) no wrapper do Android callable (ACW).

- As anotações podem ser geradas em classes, métodos e campos exportados (que é um método em código gerenciado).

Se a classe que a contém (a própria classe anotada ou a classe que contém os membros anotados) não estiver registrada, toda a origem da classe Java não será gerada, incluindo anotações. Para métodos, você pode especificar o `ExportAttribute` para obter o método explicitamente gerado e anotado. Além disso, não é um recurso para "gerar" uma definição de classe de anotação Java. Em outras palavras, se você definir um atributo gerenciado personalizado para uma determinada anotação, terá que adicionar outra biblioteca. jar que contenha a classe de anotação Java correspondente. A adição de um arquivo de origem Java que define o tipo de anotação não é suficiente. O compilador Java não funciona da mesma maneira que a **apt**.

Além disso, as seguintes limitações se aplicam:

- Esse processo de conversão não considera `@Target` anotação no tipo de anotação até o momento.

- Os atributos em uma propriedade não funcionam. Em vez disso, use atributos para property getter ou setter.

## <a name="class-binding"></a>Associação de classe

A associação de uma classe significa gravar um wrapper chamável gerenciado para simplificar a invocação do tipo Java subjacente.

Associar métodos virtuais e abstratos para permitir a C# substituição de requer Xamarin. Android 4,0. No entanto, qualquer versão do Xamarin. Android pode associar métodos não virtuais, métodos estáticos ou métodos virtuais sem suporte a substituições.

Uma associação normalmente contém os seguintes itens:

- Um [identificador JNI para o tipo Java que está sendo associado](#_Looking_up_Java_Types).

- [IDs e propriedades de campo JNI para cada campo associado](#_Instance_Fields).

- [Métodos e IDs de método JNI para cada método associado](#_Instance_Methods).

- Se a subclasse for necessária, o tipo precisará ter um atributo personalizado [registrattribute](xref:Android.Runtime.RegisterAttribute) na declaração de tipo com [RegisterAttribute. DoNotGenerateAcw](xref:Android.Runtime.RegisterAttribute.DoNotGenerateAcw) definido como `true`.

### <a name="declaring-type-handle"></a>Declarando identificador de tipo

Os métodos de pesquisa de campo e método exigem uma referência de objeto referindo-se ao tipo declarativo. Por convenção, isso é mantido em um campo de `class_ref`:

```csharp
static IntPtr class_ref = JNIEnv.FindClass(CLASS);
```

Consulte a seção [referências de tipo JNI](#_JNI_Type_References) para obter detalhes sobre o token de `CLASS`.

### <a name="binding-fields"></a>Campos de associação

Os campos Java são expostos C# como propriedades, por exemplo, o campo Java [Java.lang.System.in](https://developer.android.com/reference/java/lang/System.html#in) está associado C# como a propriedade [Java.lang.JavaSystem.in](xref:Java.Lang.JavaSystem.In).
Além disso, como JNI distingue entre campos estáticos e campos de instância, métodos diferentes são usados ao implementar as propriedades.

A associação de campo envolve três conjuntos de métodos:

1. O método *Get Field ID* . O método *Get Field ID* é responsável por retornar um identificador de campo que os métodos *Get Field Value* e *set Field Value* usarão. A obtenção da ID do campo requer conhecer o tipo declarativo, o nome do campo e a [assinatura do tipo JNI](#JNI_Type_Signatures) do campo.

1. Os métodos *obter valor do campo* . Esses métodos exigem o identificador de campo e são responsáveis por ler o valor do campo do Java.
    O método a ser usado depende do tipo do campo.

1. Os métodos do *valor do campo Set* . Esses métodos exigem o identificador de campo e são responsáveis por gravar o valor do campo em Java. O método a ser usado depende do tipo do campo.

Os [campos estáticos](#_Static_Fields) usam os métodos [JNIEnv. GetStaticFieldID](xref:Android.Runtime.JNIEnv.GetStaticMethodID*), `JNIEnv.GetStatic*Field`e [JNIEnv. setestáticofield](xref:Android.Runtime.JNIEnv.SetStaticField*) .

 Os [campos de instância](#_Instance_Fields) usam os métodos [JNIEnv. getfieldid](xref:Android.Runtime.JNIEnv.GetFieldID*), `JNIEnv.Get*Field`e [JNIEnv. SetField](xref:Android.Runtime.JNIEnv.SetField*) .

Por exemplo, a propriedade estática `JavaSystem.In` pode ser implementada como:

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

Observação: estamos usando [InputStreamInvoker. FromJniHandle](xref:Android.Runtime.InputStreamInvoker.FromJniHandle*) para converter a referência de JNI em uma instância de `System.IO.Stream`, e estamos usando `JniHandleOwnership.TransferLocalRef` porque [JNIEnv. GetStaticObjectField](xref:Android.Runtime.JNIEnv.GetStaticObjectField*) retorna uma referência local.

Muitos dos tipos [Android. Runtime](xref:Android.Runtime) têm `FromJniHandle` métodos que converterão uma referência de JNI no tipo desejado.

### <a name="method-binding"></a>Associação de método

Os métodos Java são expostos C# como métodos e C# como propriedades. Por exemplo, o método Java Java [. lang. Runtime. runfinalizersOnExit](https://developer.android.com/reference/java/lang/Runtime.html#runFinalizersOnExit(boolean)) é associado como o método [Java. lang. Runtime. runfinalizersOnExit](xref:Java.Lang.Runtime.RunFinalizersOnExit*) e o método [Java. lang. Object. GetClass](https://developer.android.com/reference/java/lang/Object.html#getClass) é associado como a propriedade [Java. lang. Object. Class](xref:Java.Lang.Object.Class) .

A invocação de método é um processo de duas etapas:

1. A *ID do método Get* para o método a ser invocado. O método *Get Method ID* é responsável por retornar um identificador de método que será usado pelos métodos de invocação de método. A obtenção da ID do método requer conhecer o tipo declarativo, o nome do método e a [assinatura do tipo JNI](#JNI_Type_Signatures) do método.

1. Invoque o método.

Assim como acontece com os campos, os métodos a serem usados para obter a ID do método e invocar o método diferem entre métodos estáticos e métodos de instância.

Os [métodos estáticos](#_Static_Methods_1) usam [JNIEnv. GetStaticMethodID ()](xref:Android.Runtime.JNIEnv.GetStaticMethodID*) para pesquisar a ID do método e usam a família de métodos `JNIEnv.CallStatic*Method` para invocação.

Os [métodos de instância](#_Instance_Methods) usam [JNIEnv. getmethodid](xref:Android.Runtime.JNIEnv.GetMethodID*) para pesquisar a ID do método e usam as famílias `JNIEnv.Call*Method` e `JNIEnv.CallNonvirtual*Method` de métodos para invocação.

A associação de método é potencialmente mais do que apenas invocação de método. A associação de método também inclui permitir que um método seja substituído (para métodos abstratos e não finais) ou implementado (para métodos de interface). A seção [herança de suporte, interfaces](#_Supporting_Inheritance,_Interfaces_1) aborda as complexidades do suporte de métodos virtuais e métodos de interface.

<a name="_Static_Methods_1" />

#### <a name="static-methods"></a>Métodos estáticos

A associação de um método estático envolve o uso de `JNIEnv.GetStaticMethodID` para obter um identificador de método e, em seguida, usar o método `JNIEnv.CallStatic*Method` apropriado, dependendo do tipo de retorno do método. Veja a seguir um exemplo de uma associação para o método [Runtime. GetRuntime](https://developer.android.com/reference/java/lang/Runtime.html#getRuntime()) :

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

Observe que armazenamos o identificador de método em um campo estático, `id_getRuntime`. Essa é uma otimização de desempenho, para que o identificador de método não precise ser pesquisado em todas as invocações. Não é necessário armazenar em cache o identificador de método dessa maneira. Depois que o identificador do método é obtido, [JNIEnv. CallStaticObjectMethod](xref:Android.Runtime.JNIEnv.CallStaticObjectMethod*) é usado para invocar o método. `JNIEnv.CallStaticObjectMethod` retorna um `IntPtr` que contém o identificador da instância Java retornada.
[Java. lang. Object. GetObject&lt;t&gt;(IntPtr, JniHandleOwnership)](xref:Java.Lang.Object.GetObject*) é usado para converter o identificador Java em uma instância de objeto com rigidez de tipos.

#### <a name="non-virtual-instance-method-binding"></a>Associação de método de instância não virtual

Associar um método de instância `final` ou um método de instância que não requer substituição envolve usar `JNIEnv.GetMethodID` para obter um identificador de método e, em seguida, usar o método `JNIEnv.Call*Method` apropriado, dependendo do tipo de retorno do método. Veja a seguir um exemplo de uma associação para a propriedade `Object.Class`:

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

Observe que armazenamos o identificador de método em um campo estático, `id_getClass`.
Essa é uma otimização de desempenho, para que o identificador de método não precise ser pesquisado em todas as invocações. Não é necessário armazenar em cache o identificador de método dessa maneira. Depois que o identificador do método é obtido, [JNIEnv. CallStaticObjectMethod](xref:Android.Runtime.JNIEnv.CallStaticObjectMethod*) é usado para invocar o método. `JNIEnv.CallStaticObjectMethod` retorna um `IntPtr` que contém o identificador da instância Java retornada.
[Java. lang. Object. GetObject&lt;t&gt;(IntPtr, JniHandleOwnership)](xref:Java.Lang.Object.GetObject*) é usado para converter o identificador Java em uma instância de objeto com rigidez de tipos.

### <a name="binding-constructors"></a>Construtores de associação

Os construtores são métodos Java com o nome `"<init>"`. Assim como ocorre com os métodos de instância Java, `JNIEnv.GetMethodID` é usado para pesquisar o identificador do construtor. Ao contrário dos métodos Java, os métodos [JNIEnv. NewObject](xref:Android.Runtime.JNIEnv.NewObject*) são usados para invocar o identificador de método do construtor. O valor de retorno de `JNIEnv.NewObject` é uma referência local JNI:

```csharp
int value = 42;
IntPtr class_ref    = JNIEnv.FindClass ("java/lang/Integer");
IntPtr id_ctor_I    = JNIEnv.GetMethodID (class_ref, "<init>", "(I)V");
IntPtr lrefInstance = JNIEnv.NewObject (class_ref, id_ctor_I, new JValue (value));
// Dispose of lrefInstance, class_ref…
```

Normalmente, uma associação de classe será a subclasse de [Java. lang. Object](xref:Java.Lang.Object).
Ao `Java.Lang.Object`de subclasses, uma semântica adicional entra em cena: uma instância de `Java.Lang.Object` mantém uma referência global a uma instância Java por meio da propriedade `Java.Lang.Object.Handle`.

1. O construtor padrão `Java.Lang.Object` alocará uma instância Java.

1. Se o tipo tiver um `RegisterAttribute` e `RegisterAttribute.DoNotGenerateAcw` for `true`, uma instância do tipo `RegisterAttribute.Name` será criada por meio de seu construtor padrão.

1. Caso contrário, o ACW ( [Android callable wrapper](~/android/platform/java-integration/android-callable-wrappers.md) ) correspondente a `this.GetType` é instanciado por meio de seu construtor padrão. Os wrappers que podem ser chamados pelo Android são gerados durante a criação do pacote para cada subclasse de `Java.Lang.Object` para a qual `RegisterAttribute.DoNotGenerateAcw` não está definido como `true`.

Para tipos que não são associações de classe, essa é a semântica esperada: instanciar uma C# instância de `Mono.Samples.HelloWorld.HelloAndroid` deve construir uma instância de `mono.samples.helloworld.HelloAndroid` Java que é um wrapper que pode ser chamado pelo Android.

Para associações de classe, esse pode ser o comportamento correto se o tipo Java contiver um construtor padrão e/ou nenhum outro Construtor precisar ser invocado. Caso contrário, um construtor deve ser fornecido, o que executa as seguintes ações:

1. Invocar o [Java. lang. Object (IntPtr, JniHandleOwnership)](xref:Java.Lang.Object#ctor*) em vez do construtor de `Java.Lang.Object` padrão. Isso é necessário para evitar a criação de uma nova instância Java.

1. Verifique o valor de [Java. lang. Object. Handle](xref:Java.Lang.Object.Handle) antes de criar qualquer instância do Java. A propriedade `Object.Handle` terá um valor diferente de `IntPtr.Zero` se um wrapper que pode ser chamado pelo Android tiver sido construído no código Java e a associação de classe estiver sendo construída para conter a instância criada do Android callable wrapper. Por exemplo, quando o Android cria uma instância de `mono.samples.helloworld.HelloAndroid`, o Android callable wrapper será criado primeiro e o Construtor Java `HelloAndroid` criará uma instância do tipo de `Mono.Samples.HelloWorld.HelloAndroid` correspondente, com a propriedade `Object.Handle` sendo definida como a instância Java antes da execução do construtor.

1. Se o tipo de tempo de execução atual não for o mesmo que o tipo declarativo, uma instância do Android callable wrapper correspondente deverá ser criada e usará [Object. SetHandle](xref:Java.Lang.Object.SetHandle*) para armazenar o identificador retornado por [JNIEnv. CreateInstance](xref:Android.Runtime.JNIEnv.CreateInstance*).

1. Se o tipo de tempo de execução atual for o mesmo que o tipo declarativo, invoque o Construtor Java e use [Object. SetHandle](xref:Java.Lang.Object.SetHandle*) para armazenar o identificador retornado por `JNIEnv.NewInstance`.

Por exemplo, considere o construtor [Java. lang. Integer (int)](https://developer.android.com/reference/java/lang/Integer.html#Integer(int)) . Isso é associado como:

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

Os métodos [JNIEnv. CreateInstance](xref:Android.Runtime.JNIEnv.CreateInstance*) são auxiliares para executar um `JNIEnv.FindClass`, `JNIEnv.GetMethodID`, `JNIEnv.NewObject`e `JNIEnv.DeleteGlobalReference` no valor retornado de `JNIEnv.FindClass`. Consulte a próxima seção para obter detalhes.

<a name="_Supporting_Inheritance,_Interfaces_1" />

### <a name="supporting-inheritance-interfaces"></a>Suporte à herança, interfaces

A subclasse de um tipo Java ou a implementação de uma interface java requer a geração de ACWs ( [Android callable wrappers](~/android/platform/java-integration/android-callable-wrappers.md) ) que são gerados para cada subclasse de `Java.Lang.Object` durante o processo de empacotamento. A geração de ACW é controlada por meio do atributo personalizado [Android. Runtime. RegisterAttribute](xref:Android.Runtime.RegisterAttribute) .

Para C# tipos, o construtor de atributo personalizado `[Register]` requer um argumento: a [referência de tipo simplificada JNI](#_Simplified_Type_References_1) para o tipo Java correspondente. Isso permite fornecer nomes diferentes entre Java e C#.

Antes do Xamarin. Android 4,0, o atributo personalizado `[Register]` não estava disponível para "alias" de tipos Java existentes. Isso ocorre porque o processo de geração de ACW geraria ACWs para cada subclasse de `Java.Lang.Object` encontrada.

O Xamarin. Android 4,0 introduziu a propriedade [RegisterAttribute. DoNotGenerateAcw](xref:Android.Runtime.RegisterAttribute.DoNotGenerateAcw) . Essa propriedade instrui o processo de geração de ACW a *ignorar* o tipo anotado, permitindo a declaração de novos wrappers chamáveis gerenciados que não resultarão na geração de ACWs no momento da criação do pacote. Isso permite a associação de tipos Java existentes. Por exemplo, considere a seguinte classe Java simples, `Adder`, que contém um método, `add`, que adiciona a inteiros e retorna o resultado:

```java
package mono.android.test;
public class Adder {
    public int add (int a, int b) {
        return a + b;
    }
}
```

O tipo de `Adder` pode ser associado como:

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

Aqui, o tipo C# de `Adder` *alias* do tipo de `Adder` Java. O atributo `[Register]` é usado para especificar o nome JNI do tipo Java `mono.android.test.Adder` e a propriedade `DoNotGenerateAcw` é usada para inibir a geração de ACW. Isso resultará na geração de um ACW para o tipo de `ManagedAdder`, que subclasseia adequadamente o tipo de `mono.android.test.Adder`. Se a propriedade `RegisterAttribute.DoNotGenerateAcw` não tiver sido usada, o processo de compilação do Xamarin. Android teria gerado um novo tipo de Java `mono.android.test.Adder`. Isso resultaria em erros de compilação, pois o tipo de `mono.android.test.Adder` seria apresentado duas vezes, em dois arquivos separados.

### <a name="binding-virtual-methods"></a>Métodos virtuais de associação

`ManagedAdder` subclasses do tipo de `Adder` Java, mas não é particularmente interessante: C# o tipo de `Adder` não define nenhum método virtual, portanto, `ManagedAdder` não pode substituir nada.

A associação de métodos `virtual` para permitir a substituição por subclasses requer várias coisas que precisam ser feitas, que se enquadram nas duas categorias a seguir:

1. **Associação de método**

1. **Registro do método**

#### <a name="method-binding"></a>Associação de método

Uma associação de método requer a adição de dois membros de suporte C# à definição de `Adder`: `ThresholdType`e `ThresholdClass`.

##### <a name="thresholdtype"></a>ThresholdType

A propriedade `ThresholdType` retorna o tipo atual da associação:

```csharp
partial class Adder {
    protected override System.Type ThresholdType {
        get {
            return typeof (Adder);
        }
    }
}
```

`ThresholdType` é usado na associação de método para determinar quando ele deve executar a expedição de método virtual versus não virtual. Ele sempre deve retornar uma instância de `System.Type` que corresponde ao tipo C# declarativo.

##### <a name="thresholdclass"></a>ThresholdClass

A propriedade `ThresholdClass` retorna a referência de classe JNI para o tipo associado:

```csharp
partial class Adder {
    protected override IntPtr ThresholdClass {
        get {
            return class_ref;
        }
    }
}
```

`ThresholdClass` é usado na associação de método ao invocar métodos não virtuais.

#### <a name="binding-implementation"></a>Implementação de associação

A implementação de associação de método é responsável pela invocação de tempo de execução do método Java. Ele também contém uma declaração de atributo personalizado `[Register]` que faz parte do registro do método e será discutido na seção de registro do método:

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

O campo `id_add` contém a ID do método do método Java a ser invocado. O valor de `id_add` é obtido de `JNIEnv.GetMethodID`, que requer a classe de declaração (`class_ref`), o nome do método Java (`"add"`) e a assinatura JNI do método (`"(II)I"`).

Depois que a ID do método é obtida, o `GetType` é comparado com `ThresholdType` para determinar se um despacho virtual ou não virtual é necessário. O despacho virtual é necessário quando `GetType` corresponde a `ThresholdType`, já que `Handle` pode se referir a uma subclasse alocada em Java que substitui o método.

Quando `GetType` não corresponde `ThresholdType`, `Adder` tem sido subclasse (por exemplo, por `ManagedAdder`) e a implementação de `Adder.Add` só será invocada se a subclasse invocada `base.Add`. Esse é o caso de expedição não virtual, que é onde `ThresholdClass` entra. `ThresholdClass` especifica qual classe Java fornecerá a implementação do método a ser invocado.

#### <a name="method-registration"></a>Registro do método

Suponha que tenhamos uma definição de `ManagedAdder` atualizada que substitui o método `Adder.Add`:

```csharp
partial class ManagedAdder : Adder {
    public override int Add (int a, int b) {
        return (a*2) + (b*2);
    }
}
```

Lembre-se de que `Adder.Add` tinha um atributo personalizado `[Register]`:

```csharp
[Register ("add", "(II)I", "GetAddHandler")]
```

O construtor de atributo personalizado `[Register]` aceita três valores:

1. O nome do método Java, `"add"` nesse caso.

1. A assinatura do tipo JNI do método, `"(II)I"` nesse caso.

1. O *método de conector* , `GetAddHandler` nesse caso.
    Os métodos de conector serão discutidos posteriormente.

Os dois primeiros parâmetros permitem que o processo de geração de ACW gere uma declaração de método para substituir o método. O ACW resultante conterá alguns dos seguintes códigos:

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

Observe que um método `@Override` é declarado, que delega um método prefixado `n_`de mesmo nome. Isso garante que, quando o código Java invoca `ManagedAdder.add`, `ManagedAdder.n_add` será invocado, o que permitirá C# que o método de substituição `ManagedAdder.Add` seja executado.

Portanto, a pergunta mais importante: como `ManagedAdder.n_add` é conectada ao `ManagedAdder.Add`?

Os métodos de `native` Java são registrados com o tempo de execução do Java (o tempo de execução do Android) por meio da [função JNI RegisterNatives](https://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html#wp17734).
`RegisterNatives` usa uma matriz de estruturas que contém o nome do método Java, a assinatura de tipo JNI e um ponteiro de função para invocar isso segue a [Convenção de chamada JNI](https://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/design.html#wp715).
O ponteiro de função deve ser uma função que usa dois argumentos de ponteiro seguidos pelos parâmetros do método. O método de `ManagedAdder.n_add` Java deve ser implementado por meio de uma função que tem o seguinte protótipo C:

```csharp
int FunctionName(JNIEnv *env, jobject this, int a, int b)
```

O Xamarin. Android não expõe um método `RegisterNatives`. Em vez disso, a ACW e a MCW juntas fornecem as informações necessárias para invocar `RegisterNatives`: o ACW contém o nome do método e a assinatura de tipo JNI, a única coisa ausente é um ponteiro de função para conectar.

É aí que entra o *método de conector* . O terceiro parâmetro de atributo personalizado `[Register]` é o nome de um método definido no tipo registrado ou uma classe base do tipo registrado que não aceita parâmetros e retorna um `System.Delegate`. O `System.Delegate` retornado, por sua vez, se refere a um método que tem a assinatura de função JNI correta. Por fim, o delegado que o método de conector retorna *deve* ter raiz para que o GC não o colete, pois o delegado está sendo fornecido para Java.

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

O método `GetAddHandler` cria um delegado de `Func<IntPtr, IntPtr, int, int,
int>` que se refere ao método `n_Add` e, em seguida, invoca [JNINativeWrapper. CreateDelegate](xref:Android.Runtime.JNINativeWrapper.CreateDelegate*).
`JNINativeWrapper.CreateDelegate` encapsula o método fornecido em um bloco try/catch, de forma que quaisquer exceções sem tratamento sejam tratadas e resultará na geração do evento [AndroidEvent. UnhandledExceptionRaiser](xref:Android.Runtime.AndroidEnvironment.UnhandledExceptionRaiser) . O delegado resultante é armazenado na variável de `cb_add` estática para que o GC não libere o delegado.

Por fim, o método `n_Add` é responsável por realizar o marshaling dos parâmetros JNI para os tipos gerenciados correspondentes e, em seguida, delegar a chamada do método.

Observação: sempre use `JniHandleOwnership.DoNotTransfer` ao obter uma MCW em uma instância Java. Tratá-los como uma referência local (e, portanto, chamar `JNIEnv.DeleteLocalRef`) interromperá as transições de pilha gerenciadas&gt; Java&gt;.

### <a name="complete-adder-binding"></a>Concluir Associação de adicionador

A associação gerenciada completa para o tipo de `mono.android.tests.Adder` é:

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

Ao gravar um tipo que corresponde aos seguintes critérios:

1. Subclasses `Java.Lang.Object`

1. Tem um `[Register]` atributo personalizado

1. `RegisterAttribute.DoNotGenerateAcw` é `true`

Em seguida, para a interação do GC, o tipo *não deve* ter nenhum campo que possa se referir a uma subclasse `Java.Lang.Object` ou `Java.Lang.Object` em tempo de execução. Por exemplo, os campos do tipo `System.Object` e qualquer tipo de interface não são permitidos. Tipos que não podem se referir a instâncias de `Java.Lang.Object` são permitidos, como `System.String` e `List<int>`. Essa restrição é para evitar a coleta de objetos prematuro pelo GC.

Se o tipo deve conter um campo de instância que possa fazer referência a uma instância de `Java.Lang.Object`, o tipo de campo deve ser `System.WeakReference` ou `GCHandle`.

## <a name="binding-abstract-methods"></a>Métodos abstratos de associação

Os métodos de associação `abstract` são amplamente idênticos aos métodos virtuais de associação. Há apenas duas diferenças:

1. O método abstract é abstract. Ele ainda retém o atributo `[Register]` e o registro do método associado, a associação de método é movida apenas para o tipo de `Invoker`.

1. Um tipo de `Invoker` não `abstract` é criado e cria uma subclasse do tipo abstrato. O tipo de `Invoker` deve substituir todos os métodos abstratos declarados na classe base, e a implementação substituída é a implementação de associação de método, embora o caso de expedição não virtual possa ser ignorado.

Por exemplo, suponha que o método de `mono.android.test.Adder.add` acima fosse `abstract`. A C# Associação seria alterada para que `Adder.Add` fossem abstratas e um novo tipo de `AdderInvoker` fosse definido, que implementou `Adder.Add`:

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

O tipo de `Invoker` só é necessário ao obter referências de JNI para instâncias criadas por Java.

## <a name="binding-interfaces"></a>Interfaces de associação

As interfaces de associação são conceitualmente semelhantes às classes de associação que contêm métodos virtuais, mas muitas das especificações diferem de maneiras sutis (e não tão sutis). Considere a seguinte [declaração de interface java](https://github.com/xamarin/monodroid-samples/blob/master/SanityTests/Adder.java#L14):

```csharp
public interface Progress {
    void onAdd(int[] values, int currentIndex, int currentSum);
}
```

As associações de interface têm duas partes: C# a definição de interface e uma definição de chamador para a interface.

### <a name="interface-definition"></a>Definição da interface

A C# definição de interface deve atender aos seguintes requisitos:

- A definição de interface deve ter um `[Register]` atributo personalizado.

- A definição de interface deve estender o `IJavaObject interface`.
    Deixar de fazer isso impedirá que o ACWs herde da interface java.

- Cada método de interface deve conter um atributo `[Register]` especificando o nome do método Java correspondente, a assinatura JNI e o método de conector.

- O método de conector também deve especificar o tipo no qual o método de conector pode estar localizado.

Ao associar os métodos `abstract` e `virtual`, o método de conector seria pesquisado dentro da hierarquia de herança do tipo que está sendo registrado. Interfaces não podem ter métodos que contenham corpos, portanto, isso não funciona, portanto, o requisito é que um tipo seja especificado indicando onde o método de conector está localizado. O tipo é especificado dentro da cadeia de caracteres do método do conector, depois de dois-pontos `':'`e deve ser o nome do tipo qualificado do assembly do tipo que contém o chamador.

As declarações de método de interface são uma tradução do método Java correspondente usando tipos *compatíveis* . Para tipos internos de Java, os tipos compatíveis são os C# tipos correspondentes, por exemplo, `int` C# Java é `int`. Para tipos de referência, o tipo compatível é um tipo que pode fornecer um identificador JNI do tipo Java apropriado.

Os membros da interface não serão invocados diretamente pelo Java &ndash; invocação será mediada por meio do tipo de chamador &ndash;, portanto, uma quantidade de flexibilidade é permitida.

A interface de progresso de Java pode ser [declarada C# em como](https://github.com/xamarin/monodroid-samples/blob/master/SanityTests/ManagedAdder.cs#L83):

```csharp
[Register ("mono/android/test/Adder$Progress", DoNotGenerateAcw=true)]
public interface IAdderProgress : IJavaObject {
    [Register ("onAdd", "([III)V",
            "GetOnAddHandler:Mono.Samples.SanityTests.IAdderProgressInvoker, SanityTests, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null")]
    void OnAdd (JavaArray<int> values, int currentIndex, int currentSum);
}
```

Observe que, no acima, mapeamos o parâmetro Java `int[]` para um [JavaArray&lt;int&gt;](xref:Android.Runtime.JavaArray`1).
Isso não é necessário: poderíamos tê-lo associado a C# uma `int[]`, ou uma `IList<int>`ou outra coisa inteiramente. Qualquer que seja o tipo escolhido, o `Invoker` precisa ser capaz de convertê-lo em um tipo de `int[]` Java para invocação.

### <a name="invoker-definition"></a>Definição do chamador

A definição de tipo de `Invoker` deve herdar `Java.Lang.Object`, implementar a interface apropriada e fornecer todos os métodos de conexão referenciados na definição de interface. Há mais uma sugestão que difere de uma associação de classe: o campo `class_ref` e as IDs de método devem ser membros de instância, não membros estáticos.

O motivo para os membros de instância de preferência tem a ver com `JNIEnv.GetMethodID` comportamento no tempo de execução do Android. (Isso também pode ser o comportamento do Java; ele não foi testado.) `JNIEnv.GetMethodID` retorna NULL ao pesquisar um método proveniente de uma interface implementada e não da interface declarada. Considere a interface java [. util. SortedMap&lt;k, v&gt;](https://developer.android.com/reference/java/util/SortedMap.html) Java, que implementa a interface [Java. util. map&lt;k, v&gt;](https://developer.android.com/reference/java/util/Map.html) . O MAP fornece um método [claro](https://developer.android.com/reference/java/util/Map.html#clear()) , portanto, uma definição de `Invoker` aparentemente razoável para SortedMap seria:

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

A seguir falhará porque `JNIEnv.GetMethodID` retornará `null` ao pesquisar o método `Map.clear` por meio da instância da classe `SortedMap`.

Há duas soluções para isso: acompanhe a interface de cada método e tenha um `class_ref` para cada interface ou mantenha tudo como membros de instância e execute a pesquisa de método no tipo de classe mais derivado, não no tipo de interface. O último é feito em **mono. Android. dll**.

A definição do chamador tem seis seções: o construtor, o método `Dispose`, os membros `ThresholdType` e `ThresholdClass`, o método `GetObject`, a implementação do método de interface e a implementação do método de conector.

#### <a name="constructor"></a>Construtor

O Construtor precisa pesquisar a classe de tempo de execução da instância que está sendo invocada e armazenar a classe de tempo de execução na instância `class_ref` campo:

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

Observação: a propriedade `Handle` deve ser usada no corpo do construtor e não no parâmetro `handle`, como no Android v 4.0, o parâmetro `handle` pode ser inválido depois que o construtor base concluir a execução.

#### <a name="dispose-method"></a>Método Dispose

O método `Dispose` precisa liberar a referência global alocada no construtor:

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

#### <a name="thresholdtype-and-thresholdclass"></a>Thresholdtype e ThresholdClass

Os membros `ThresholdType` e `ThresholdClass` são idênticos ao que é encontrado em uma associação de classe:

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

Um método de `GetObject` estático é necessário para dar suporte a [extensões. JavaCast&lt;t&gt;()](xref:Android.Runtime.Extensions.JavaCast*):

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

Os métodos de conector e a infraestrutura de suporte são responsáveis pelo marshaling dos parâmetros C# JNI para os tipos apropriados. O parâmetro Java `int[]` será passado como um `jintArray`JNI, que é um `IntPtr` no C#. O `IntPtr` deve ser empacotado para um `JavaArray<int>` a fim de dar suporte à invocação da C# interface:

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

Se `int[]` for preferencial sobre `JavaList<int>`, [JNIEnv. GetArray ()](xref:Android.Runtime.JNIEnv.GetArray*) poderia ser usado em vez disso:

```csharp
int[] _values = (int[]) JNIEnv.GetArray(values, JniHandleOwnership.DoNotTransfer, typeof (int));
```

Observe, no entanto, que `JNIEnv.GetArray` copia toda a matriz entre VMs, para que, para matrizes grandes, isso possa resultar em muitas pressões de GC adicionadas.

### <a name="complete-invoker-definition"></a>Concluir definição do chamador

A [definição de IAdderProgressInvoker completa](https://github.com/xamarin/monodroid-samples/blob/master/SanityTests/ManagedAdder.cs#L88):

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

Muitos métodos JNIEnv retornam *referências de objeto* *JNI* , que são semelhantes a `GCHandle`s. O JNI fornece três tipos diferentes de referências de objeto: referências locais, referências globais e referências globais fracas. Todos os três são representados como `System.IntPtr`, *mas* (de acordo com a seção tipos de função JNI) nem todos os `IntPtr`retornados dos métodos `JNIEnv` são referências. Por exemplo, [JNIEnv. Getmethodid](xref:Android.Runtime.JNIEnv.GetMethodID*) retorna um `IntPtr`, mas não retorna uma referência de objeto, ele retorna um `jmethodID`. Consulte a [documentação da função JNI](https://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html) para obter detalhes.

Referências locais são criadas pela *maioria dos* métodos de criação de referência.
O Android permite que apenas um número limitado de referências locais existam em um determinado momento, geralmente 512. As referências locais podem ser excluídas por meio de [JNIEnv. DeleteLocalRef](xref:Android.Runtime.JNIEnv.DeleteLocalRef*).
Ao contrário de JNI, nem todos os métodos JNIEnv de referência que retornam referências de objeto retornam referências locais; [JNIEnv. FindClass](xref:Android.Runtime.JNIEnv.FindClass*) retorna uma referência *global* . É altamente recomendável que você exclua referências locais da maneira mais rápida possível, possivelmente criando um [Java. lang. Object](xref:Java.Lang.Object) ao lado do objeto e especificando `JniHandleOwnership.TransferLocalRef` para o construtor [Java. lang. Object (identificador IntPtr, JniHandleOwnership Transfer)](xref:Java.Lang.Object#ctor*) .

As referências globais são criadas por [JNIEnv. NewGlobalRef](xref:Android.Runtime.JNIEnv.NewGlobalRef*) e [JNIEnv. FindClass](xref:Android.Runtime.JNIEnv.FindClass*).
Eles podem ser destruídos com [JNIEnv. DeleteGlobalRef](xref:Android.Runtime.JNIEnv.DeleteGlobalRef*).
Os emuladores têm um limite de 2.000 referências globais pendentes, enquanto os dispositivos de hardware têm um limite de aproximadamente 52.000 referências globais.

Referências globais fracas só estão disponíveis no Android v 2.2 (Froyo) e posterior. Referências globais fracas podem ser excluídas com [JNIEnv. DeleteWeakGlobalRef](xref:Android.Runtime.JNIEnv.DeleteWeakGlobalRef*).

### <a name="dealing-with-jni-local-references"></a>Lidando com referências locais do JNI

Os métodos [JNIEnv. Getobjectfield](xref:Android.Runtime.JNIEnv.GetObjectField*), [JNIEnv. GetStaticObjectField](xref:Android.Runtime.JNIEnv.GetStaticObjectField*), [JNIEnv. CallObjectMethod](xref:Android.Runtime.JNIEnv.CallObjectMethod*), [JNIEnv. CallNonvirtualObjectMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualObjectMethod*) e [JNIEnv. CallStaticObjectMethod](xref:Android.Runtime.JNIEnv.CallStaticObjectMethod*) retornam um `IntPtr` que contém uma referência local de JNI para um objeto Java, ou `IntPtr.Zero` se Java retornou `null`. Devido ao número limitado de referências locais que podem ser pendentes de uma vez (512 entradas), é desejável garantir que as referências sejam excluídas em tempo hábil. Há três maneiras pelas quais as referências locais podem ser tratadas: excluindo-as explicitamente, criando uma instância de `Java.Lang.Object` para contê-las e usando `Java.Lang.Object.GetObject<T>()` para criar um wrapper chamável gerenciado ao seu respeito.

### <a name="explicitly-deleting-local-references"></a>Excluindo explicitamente referências locais

[JNIEnv. DeleteLocalRef](xref:Android.Runtime.JNIEnv.DeleteLocalRef*) é usado para excluir referências locais. Depois que a referência local tiver sido excluída, ela não poderá mais ser usada, portanto, tenha cuidado para garantir que `JNIEnv.DeleteLocalRef` seja a última ação feita com a referência local.

```csharp
IntPtr lref = JNIEnv.CallObjectMethod(instance, methodID);
try {
    // Do something with `lref`
}
finally {
    JNIEnv.DeleteLocalRef (lref);
}
```

### <a name="wrapping-with-javalangobject"></a>Encapsulamento com Java. lang. Object

`Java.Lang.Object` fornece um construtor [Java. lang. Object (alça IntPtr, JniHandleOwnership Transfer)](xref:Java.Lang.Object#ctor*) , que pode ser usado para encapsular uma referência de JNI saindo. O parâmetro [JniHandleOwnership](xref:Android.Runtime.JniHandleOwnership) determina como o parâmetro de `IntPtr` deve ser tratado:

- [JniHandleOwnership. DoNotTransfer](xref:Android.Runtime.JniHandleOwnership.DoNotTransfer) &ndash; a instância de `Java.Lang.Object` criada criará uma nova referência global do parâmetro `handle` e `handle` será inalterada.
    O chamador é responsável por liberar `handle`, se necessário.

- [JniHandleOwnership. TransferLocalRef](xref:Android.Runtime.JniHandleOwnership.TransferLocalRef) &ndash; a instância de `Java.Lang.Object` criada criará uma nova referência global do parâmetro `handle` e `handle` será excluída com [JNIEnv. DeleteLocalRef](xref:Android.Runtime.JNIEnv.DeleteLocalRef*) . O chamador não deve liberar `handle` e não deve usar `handle` depois que o Construtor concluir a execução.

- [JniHandleOwnership. TransferGlobalRef](xref:Android.Runtime.JniHandleOwnership.TransferLocalRef) &ndash; a instância de `Java.Lang.Object` criada assumirá a propriedade do parâmetro `handle`. O chamador não deve liberar `handle`.

Como os métodos de invocação do método JNI retornam referências locais, `JniHandleOwnership.TransferLocalRef` normalmente seriam usadas:

```csharp
IntPtr lref = JNIEnv.CallObjectMethod(instance, methodID);
var value = new Java.Lang.Object (lref, JniHandleOwnership.TransferLocalRef);
```

A referência global criada não será liberada até que a instância de `Java.Lang.Object` seja coletada pelo lixo. Se for possível, a alienação da instância liberará a referência global, acelerando as coletas de lixo:

```csharp
IntPtr lref = JNIEnv.CallObjectMethod(instance, methodID);
using (var value = new Java.Lang.Object (lref, JniHandleOwnership.TransferLocalRef)) {
    // use value ...
}
```

### <a name="using-javalangobjectgetobjectlttgt"></a>Usando Java. lang. Object. GetObject&lt;T&gt;()

`Java.Lang.Object` fornece um método [Java. lang. Object. GetObject&lt;t&gt;(identificador IntPtr, transferência JniHandleOwnership)](xref:Java.Lang.Object.GetObject*) que pode ser usado para criar um wrapper callable gerenciado do tipo especificado.

O tipo `T` deve atender aos seguintes requisitos:

1. `T` deve ser um tipo de referência.

1. `T` deve implementar a interface `IJavaObject`.

1. Se `T` não for uma classe ou interface abstrata, `T` deverá fornecer um construtor com os tipos de parâmetro `(IntPtr,
    JniHandleOwnership)`.

1. Se `T` for uma classe abstrata ou uma interface, *deverá* haver um *chamador* disponível para `T`. Um chamador é um tipo não abstrato que herda `T` ou implementa `T` e tem o mesmo nome que `T` com um sufixo de chamador. Por exemplo, se T for a interface `Java.Lang.IRunnable`, o tipo `Java.Lang.IRunnableInvoker` deverá existir e deverá conter o construtor de `(IntPtr,
    JniHandleOwnership)` necessário.

Como os métodos de invocação do método JNI retornam referências locais, `JniHandleOwnership.TransferLocalRef` normalmente seriam usadas:

```csharp
IntPtr lrefString = JNIEnv.CallObjectMethod(instance, methodID);
Java.Lang.String value = Java.Lang.Object.GetObject<Java.Lang.String>( lrefString, JniHandleOwnership.TransferLocalRef);
```

<a name="_Looking_up_Java_Types" />

## <a name="looking-up-java-types"></a>Pesquisando tipos de Java

Para pesquisar um campo ou método em JNI, o tipo declarativo do campo ou do método deve ser pesquisado primeiro. O método [Android. Runtime. JNIEnv. FindClass (String)](xref:Android.Runtime.JNIEnv.FindClass*)) é usado para Pesquisar tipos Java. O parâmetro de cadeia de caracteres é a *referência de tipo simplificada* ou a *referência de tipo completo* para o tipo Java. Consulte a [seção referências de tipo JNI](#_JNI_Type_References) para obter detalhes sobre as referências de tipo simplificado e completo.

Observação: ao contrário de cada outro método de `JNIEnv` que retorna instâncias de objeto, `FindClass` retorna uma referência global, não uma referência local.

<a name="_Instance_Fields" />

## <a name="instance-fields"></a>Campos de instância

Os campos são manipulados por meio de *IDs de campo*. As IDs de campo são obtidas por meio de [JNIEnv. Getfieldid](xref:Android.Runtime.JNIEnv.GetFieldID*), que requer a classe em que o campo é definido, o nome do campo e a [assinatura de tipo JNI](#JNI_Type_Signatures) do campo.

As IDs de campo não precisam ser liberadas e são válidas, desde que o tipo Java correspondente seja carregado. (Atualmente, o Android não dá suporte ao descarregamento de classes.)

Há dois conjuntos de métodos para manipular campos de instância: um para ler campos de instância e outro para escrever campos de instância. Todos os conjuntos de métodos exigem uma ID de campo para ler ou gravar o valor do campo.

### <a name="reading-instance-field-values"></a>Lendo valores de campo de instância

O conjunto de métodos para ler valores de campo de instância segue o padrão de nomenclatura:

```csharp
* JNIEnv.Get*Field(IntPtr instance, IntPtr fieldID);
```

onde `*` é o tipo do campo:

- [JNIEnv. Getobjectfield](xref:Android.Runtime.JNIEnv.GetObjectField*) &ndash; ler o valor de qualquer campo de instância que não seja um tipo Builtin, como `java.lang.Object`, matrizes e tipos de interface. O valor retornado é uma referência local JNI.

- [JNIEnv. Getboolianfield](xref:Android.Runtime.JNIEnv.GetBooleanField*) &ndash; ler o valor de `bool` campos de instância.

- [JNIEnv. Getbytefield](xref:Android.Runtime.JNIEnv.GetByteField*) &ndash; ler o valor de `sbyte` campos de instância.

- [JNIEnv. Getcharfield](xref:Android.Runtime.JNIEnv.GetCharField*) &ndash; ler o valor de `char` campos de instância.

- [JNIEnv. Getcurtafield](xref:Android.Runtime.JNIEnv.GetShortField*) &ndash; ler o valor de `short` campos de instância.

- [JNIEnv. GetIntField](xref:Android.Runtime.JNIEnv.GetIntField*) &ndash; ler o valor de `int` campos de instância.

- [JNIEnv. Getlongafield](xref:Android.Runtime.JNIEnv.GetLongField*) &ndash; ler o valor de `long` campos de instância.

- [JNIEnv. Getfloatfield](xref:Android.Runtime.JNIEnv.GetFloatField*) &ndash; ler o valor de `float` campos de instância.

- [JNIEnv. Getdoublefield](xref:Android.Runtime.JNIEnv.GetDoubleField*) &ndash; ler o valor de `double` campos de instância.

### <a name="writing-instance-field-values"></a>Gravando valores de campo de instância

O conjunto de métodos para escrever valores de campo de instância segue o padrão de nomenclatura:

```csharp
JNIEnv.SetField(IntPtr instance, IntPtr fieldID, Type value);
```

em que *tipo* é o tipo do campo:

- [JNIEnv. SetField](xref:Android.Runtime.JNIEnv.SetField*)) &ndash; gravar o valor de qualquer campo que não seja um tipo interno, como `java.lang.Object`, matrizes e tipos de interface. O valor de `IntPtr` pode ser uma referência local JNI, referência global JNI, JNI referência global fraca ou `IntPtr.Zero` (para `null`).

- [JNIEnv. SetField](xref:Android.Runtime.JNIEnv.SetField*)) &ndash; gravar o valor de `bool` campos de instância.

- [JNIEnv. SetField](xref:Android.Runtime.JNIEnv.SetField*)) &ndash; gravar o valor de `sbyte` campos de instância.

- [JNIEnv. SetField](xref:Android.Runtime.JNIEnv.SetField*)) &ndash; gravar o valor de `char` campos de instância.

- [JNIEnv. SetField](xref:Android.Runtime.JNIEnv.SetField*)) &ndash; gravar o valor de `short` campos de instância.

- [JNIEnv. SetField](xref:Android.Runtime.JNIEnv.SetField*)) &ndash; gravar o valor de `int` campos de instância.

- [JNIEnv. SetField](xref:Android.Runtime.JNIEnv.SetField*)) &ndash; gravar o valor de `long` campos de instância.

- [JNIEnv. SetField](xref:Android.Runtime.JNIEnv.SetField*)) &ndash; gravar o valor de `float` campos de instância.

- [JNIEnv. SetField](xref:Android.Runtime.JNIEnv.SetField*)) &ndash; gravar o valor de `double` campos de instância.

<a name="_Static_Fields" />

## <a name="static-fields"></a>Campos estáticos

Os campos estáticos são manipulados por meio de *IDs de campo*. As IDs de campo são obtidas por meio de [JNIEnv. GetStaticFieldID](xref:Android.Runtime.JNIEnv.GetStaticFieldID*), que requer a classe em que o campo é definido, o nome do campo e a [assinatura do tipo JNI](#JNI_Type_Signatures) do campo.

As IDs de campo não precisam ser liberadas e são válidas, desde que o tipo Java correspondente seja carregado. (Atualmente, o Android não dá suporte ao descarregamento de classes.)

Há dois conjuntos de métodos para manipular campos estáticos: um para ler campos de instância e outro para escrever campos de instância. Todos os conjuntos de métodos exigem uma ID de campo para ler ou gravar o valor do campo.

### <a name="reading-static-field-values"></a>Lendo valores de campo estático

O conjunto de métodos para ler valores de campo estático segue o padrão de nomenclatura:

```csharp
* JNIEnv.GetStatic*Field(IntPtr class, IntPtr fieldID);
```

onde `*` é o tipo do campo:

- [JNIEnv. GetStaticObjectField](xref:Android.Runtime.JNIEnv.GetStaticObjectField*) &ndash; ler o valor de qualquer campo estático que não seja um tipo Builtin, como `java.lang.Object`, matrizes e tipos de interface. O valor retornado é uma referência local JNI.

- [JNIEnv. GetStaticBooleanField](xref:Android.Runtime.JNIEnv.GetStaticBooleanField*) &ndash; ler o valor de `bool` campos estáticos.

- [JNIEnv. GetStaticByteField](xref:Android.Runtime.JNIEnv.GetStaticByteField*) &ndash; ler o valor de `sbyte` campos estáticos.

- [JNIEnv. GetStaticCharField](xref:Android.Runtime.JNIEnv.GetStaticCharField*) &ndash; ler o valor de `char` campos estáticos.

- [JNIEnv. GetStaticShortField](xref:Android.Runtime.JNIEnv.GetStaticShortField*) &ndash; ler o valor de `short` campos estáticos.

- [JNIEnv. GetStaticLongField](xref:Android.Runtime.JNIEnv.GetStaticLongField*) &ndash; ler o valor de `long` campos estáticos.

- [JNIEnv. GetStaticFloatField](xref:Android.Runtime.JNIEnv.GetStaticFloatField*) &ndash; ler o valor de `float` campos estáticos.

- [JNIEnv. GetStaticDoubleField](xref:Android.Runtime.JNIEnv.GetStaticDoubleField*) &ndash; ler o valor de `double` campos estáticos.

### <a name="writing-static-field-values"></a>Gravando valores de campo estático

O conjunto de métodos para escrever valores de campo estático segue o padrão de nomenclatura:

```csharp
JNIEnv.SetStaticField(IntPtr class, IntPtr fieldID, Type value);
```

em que *tipo* é o tipo do campo:

- [JNIEnv. SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*)) &ndash; gravar o valor de qualquer campo estático que não seja um tipo Builtin, como `java.lang.Object`, matrizes e tipos de interface. O valor de `IntPtr` pode ser uma referência local JNI, referência global JNI, JNI referência global fraca ou `IntPtr.Zero` (para `null`).

- [JNIEnv. SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*)) &ndash; gravar o valor de `bool` campos estáticos.

- [JNIEnv. SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*)) &ndash; gravar o valor de `sbyte` campos estáticos.

- [JNIEnv. SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*)) &ndash; gravar o valor de `char` campos estáticos.

- [JNIEnv. SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*)) &ndash; gravar o valor de `short` campos estáticos.

- [JNIEnv. SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*)) &ndash; gravar o valor de `int` campos estáticos.

- [JNIEnv. SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*)) &ndash; gravar o valor de `long` campos estáticos.

- [JNIEnv. SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*)) &ndash; gravar o valor de `float` campos estáticos.

- [JNIEnv. SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*)) &ndash; gravar o valor de `double` campos estáticos.

<a name="_Instance_Methods" />

## <a name="instance-methods"></a>Métodos de instância

Os métodos de instância são invocados por meio de *IDs de método*. As IDs de método são obtidas por meio de [JNIEnv. Getmethodid](xref:Android.Runtime.JNIEnv.GetMethodID*), que requer o tipo em que o método é definido, o nome do método e a [assinatura de tipo JNI](#JNI_Type_Signatures) do método.

As IDs de método não precisam ser liberadas e são válidas, desde que o tipo Java correspondente seja carregado. (Atualmente, o Android não dá suporte ao descarregamento de classes.)

Há dois conjuntos de métodos para invocar métodos: um para invocar métodos virtualmente e outro para invocar métodos que não são praticamente. Os dois conjuntos de métodos exigem uma ID de método para invocar o método, e a invocação não virtual também requer que você especifique qual implementação de classe deve ser invocada.

Métodos de interface só podem ser pesquisados dentro do tipo declarativo; métodos que vêm de interfaces estendidas/herdadas não podem ser pesquisados. Consulte a seção implementação de interfaces/chamador de associação posterior para obter mais detalhes.

Qualquer método declarado na classe ou qualquer classe base ou interface implementada pode ser pesquisada.

### <a name="virtual-method-invocation"></a>Invocação de método virtual

O conjunto de métodos para invocar métodos praticamente segue o padrão de nomenclatura:

```csharp
* JNIEnv.Call*Method( IntPtr instance, IntPtr methodID, params JValue[] args );
```

onde `*` é o tipo de retorno do método.

- [JNIEnv. CallObjectMethod](xref:Android.Runtime.JNIEnv.CallObjectMethod*) &ndash; invocar um método que retorna um tipo não Builtin, como `java.lang.Object`, matrizes e interfaces. O valor retornado é uma referência local JNI.

- [JNIEnv. CallBooleanMethod](xref:Android.Runtime.JNIEnv.CallBooleanMethod*) &ndash; invocar um método que retorna um valor de `bool`.

- [JNIEnv. CallByteMethod](xref:Android.Runtime.JNIEnv.CallByteMethod*) &ndash; invocar um método que retorna um valor de `sbyte`.

- [JNIEnv. CallCharMethod](xref:Android.Runtime.JNIEnv.CallCharMethod*) &ndash; invocar um método que retorna um valor de `char`.

- [JNIEnv. CallShortMethod](xref:Android.Runtime.JNIEnv.CallShortMethod*) &ndash; invocar um método que retorna um valor de `short`.

- [JNIEnv. CallLongMethod](xref:Android.Runtime.JNIEnv.CallLongMethod*) &ndash; invocar um método que retorna um valor de `long`.

- [JNIEnv. CallFloatMethod](xref:Android.Runtime.JNIEnv.CallFloatMethod*) &ndash; invocar um método que retorna um valor de `float`.

- [JNIEnv. CallDoubleMethod](xref:Android.Runtime.JNIEnv.CallDoubleMethod*) &ndash; invocar um método que retorna um valor de `double`.

### <a name="non-virtual-method-invocation"></a>Invocação de método não virtual

O conjunto de métodos para invocar métodos não segue praticamente o padrão de nomenclatura:

```csharp
* JNIEnv.CallNonvirtual*Method( IntPtr instance, IntPtr class, IntPtr methodID, params JValue[] args );
```

onde `*` é o tipo de retorno do método. A invocação de método não virtual geralmente é usada para invocar o método base de um método virtual.

- [JNIEnv. CallNonvirtualObjectMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualObjectMethod*) &ndash; não praticamente invoca um método que retorna um tipo não Builtin, como `java.lang.Object`, matrizes e interfaces. O valor retornado é uma referência local JNI.

- [JNIEnv. CallNonvirtualBooleanMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualBooleanMethod*) &ndash; não praticamente invoca um método que retorna um valor de `bool`.

- [JNIEnv. CallNonvirtualByteMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualByteMethod*) &ndash; não praticamente invoca um método que retorna um valor de `sbyte`.

- [JNIEnv. CallNonvirtualCharMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualCharMethod*) &ndash; não praticamente invoca um método que retorna um valor de `char`.

- [JNIEnv. CallNonvirtualShortMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualShortMethod*) &ndash; não praticamente invoca um método que retorna um valor de `short`.

- [JNIEnv. CallNonvirtualLongMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualLongMethod*) &ndash; não praticamente invoca um método que retorna um valor de `long`.

- [JNIEnv. CallNonvirtualFloatMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualFloatMethod*) &ndash; não praticamente invoca um método que retorna um valor de `float`.

- [JNIEnv. CallNonvirtualDoubleMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualDoubleMethod*) &ndash; não praticamente invoca um método que retorna um valor de `double`.

<a name="_Static_Methods" />

## <a name="static-methods"></a>Métodos estáticos

Os métodos estáticos são invocados por meio de *IDs de método*. As IDs de método são obtidas por meio de [JNIEnv. GetStaticMethodID](xref:Android.Runtime.JNIEnv.GetStaticMethodID*), que requer o tipo em que o método é definido, o nome do método e a [assinatura de tipo JNI](#JNI_Type_Signatures) do método.

As IDs de método não precisam ser liberadas e são válidas, desde que o tipo Java correspondente seja carregado. (Atualmente, o Android não dá suporte ao descarregamento de classes.)

### <a name="static-method-invocation"></a>Invocação de método estático

O conjunto de métodos para invocar métodos praticamente segue o padrão de nomenclatura:

```csharp
* JNIEnv.CallStatic*Method( IntPtr class, IntPtr methodID, params JValue[] args );
```

onde `*` é o tipo de retorno do método.

- [JNIEnv. CallStaticObjectMethod](xref:Android.Runtime.JNIEnv.CallStaticObjectMethod*) &ndash; invocar um método estático que retorna um tipo não Builtin, como `java.lang.Object`, matrizes e interfaces. O valor retornado é uma referência local JNI.

- [JNIEnv. CallStaticBooleanMethod](xref:Android.Runtime.JNIEnv.CallStaticBooleanMethod*) &ndash; invocar um método estático que retorna um valor de `bool`.

- [JNIEnv. CallStaticByteMethod](xref:Android.Runtime.JNIEnv.CallStaticByteMethod*) &ndash; invocar um método estático que retorna um valor de `sbyte`.

- [JNIEnv. CallStaticCharMethod](xref:Android.Runtime.JNIEnv.CallStaticCharMethod*) &ndash; invocar um método estático que retorna um valor de `char`.

- [JNIEnv. CallStaticShortMethod](xref:Android.Runtime.JNIEnv.CallStaticShortMethod*) &ndash; invocar um método estático que retorna um valor de `short`.

- [JNIEnv. CallStaticLongMethod](xref:Android.Runtime.JNIEnv.CallLongMethod*) &ndash; invocar um método estático que retorna um valor de `long`.

- [JNIEnv. CallStaticFloatMethod](xref:Android.Runtime.JNIEnv.CallStaticFloatMethod*) &ndash; invocar um método estático que retorna um valor de `float`.

- [JNIEnv. CallStaticDoubleMethod](xref:Android.Runtime.JNIEnv.CallStaticDoubleMethod*) &ndash; invocar um método estático que retorna um valor de `double`.

<a name="JNI_Type_Signatures" />

## <a name="jni-type-signatures"></a>Assinaturas do tipo JNI

As [assinaturas do tipo JNI](https://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/types.html#wp16432) são [referências de tipo JNI](#_JNI_Type_References) (embora não sejam simplificadas referências de tipo), exceto para métodos. Com métodos, a assinatura de tipo JNI é um parêntese aberto `'('`, seguido pelas referências de tipo para todos os tipos de parâmetro concatenados (sem vírgulas separadoras ou qualquer outra coisa), seguidos de um parêntese de fechamento `')'`, seguido pela referência de tipo JNI do tipo de retorno do método.

Por exemplo, considerando o método Java:

```java
long f(int n, String s, int[] array);
```

A assinatura do tipo JNI seria:

```csharp
(ILjava/lang/String;[I)J
```

Em geral, é *altamente* recomendável usar o comando `javap` para determinar as assinaturas do JNI. Por exemplo, a assinatura de tipo JNI do método [Java. lang. thread. State. valueOf (String)](https://developer.android.com/reference/java/lang/Thread.State.html#valueOf(java.lang.String)) é "(Ljava/Lang/String;) Ljava/Lang/thread $ State;", enquanto a assinatura de tipo JNI do método [Java. lang. thread. State. Values](https://developer.android.com/reference/java/lang/Thread.State.html#values) é "() [Ljava/Lang/thread $ State;". Fique atento aos pontos-e-vírgulas à direita; Eles *fazem* parte da assinatura de tipo JNI.

<a name="_JNI_Type_References" />

## <a name="jni-type-references"></a>Referências de tipo JNI

As referências de tipo JNI são diferentes das referências de tipo Java. Você não pode usar nomes de tipo Java totalmente qualificados, como `java.lang.String` com JNI, em vez disso, você deve usar as variações de JNI `"java/lang/String"` ou `"Ljava/lang/String;"`, dependendo do contexto; consulte abaixo para obter detalhes.
Há quatro tipos de referências de tipo JNI:

- **interno**
- **simplifica**
- **tipo**
- **array**

### <a name="built-in-type-references"></a>Referências de tipo interno

Referências de tipo interno são um único caractere, usado para referenciar tipos de valor internos. O mapeamento é o seguinte:

- `"B"` para `sbyte`.
- `"S"` para `short`.
- `"I"` para `int`.
- `"J"` para `long`.
- `"F"` para `float`.
- `"D"` para `double`.
- `"C"` para `char`.
- `"Z"` para `bool`.
- `"V"` para tipos de retorno de método `void`.

<a name="_Simplified_Type_References_1" />

### <a name="simplified-type-references"></a>Referências de tipo simplificadas

As referências de tipo simplificadas só podem ser usadas em [JNIEnv. FindClass (String)](xref:Android.Runtime.JNIEnv.FindClass*)).
Há duas maneiras de derivar uma referência de tipo simplificada:

1. A partir de um nome Java totalmente qualificado, substitua cada `'.'` no nome do pacote e antes do nome do tipo com `'/'`, e a cada `'.'` em um nome de tipo com `'$'`.

1. Leia a saída de `'unzip -l android.jar | grep JavaName'`.

Qualquer um dos dois resultará no tipo Java [Java. lang. thread. State](https://developer.android.com/reference/java/lang/Thread.State.html) que está sendo mapeado para a referência de tipo simplificada `java/lang/Thread$State`.

### <a name="type-references"></a>Referências de tipo

Uma referência de tipo é uma referência de tipo interna ou uma referência de tipo simplificada com um prefixo de `'L'` e um sufixo de `';'`. Para o tipo Java [Java. lang. String](https://developer.android.com/reference/java/lang/String.html), a referência de tipo simplificada é `"java/lang/String"`, enquanto a referência de tipo é `"Ljava/lang/String;"`.

Referências de tipo são usadas com referências de tipo de matriz e com assinaturas JNI.

Uma maneira adicional de obter uma referência de tipo é lendo a saída de `'javap -s -classpath android.jar fully.qualified.Java.Name'`.
Dependendo do tipo envolvido, você pode usar uma declaração de construtor ou um tipo de retorno de método para determinar o nome do JNI. Por exemplo:

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

`Thread.State` é um tipo de enumeração Java, portanto, podemos usar a assinatura do método `valueOf` para determinar que a referência de tipo seja Ljava/Lang/thread $ State;.

### <a name="array-type-references"></a>Referências de tipo de matriz

Referências de tipo de matriz são `'['` prefixadas para uma referência de tipo JNI.
Referências de tipo simplificadas não podem ser usadas ao especificar matrizes.

Por exemplo, `int[]` é `"[I"`, `int[][]` é `"[[I"`e `java.lang.Object[]` é `"[Ljava/lang/Object;"`.

## <a name="java-generics-and-type-erasure"></a>Genéricos e tipos de apagamento Java

Na *maioria* das vezes, como visto por meio de JNI, os genéricos Java não *existem*.
Há algumas "rugas", mas essas rugas estão em como o Java interage com os genéricos, e não com a aparência de JNI e invoca Membros genéricos.

Não há nenhuma diferença entre um tipo genérico ou membro e um tipo não genérico ou membro ao interagir com JNI. Por exemplo, o tipo genérico [Java. lang. Class&lt;t&gt;](https://developer.android.com/reference/java/lang/Class.html) também é o tipo genérico "raw" `java.lang.Class`, ambos com a mesma referência de tipo simplificada, `"java/lang/Class"`.

## <a name="java-native-interface-support"></a>Suporte de interface nativa Java

[Android. Runtime. JNIEnv](xref:Android.Runtime.JNIEnv) é um wrapper gerenciado para a JNI (interface nativa Jave). As funções JNI são declaradas dentro da [especificação de interface nativa do Java](https://download.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html), embora os métodos tenham sido alterados para remover o parâmetro `JNIEnv*` explícito e `IntPtr` é usado em vez de `jobject`, `jclass`, `jmethodID`, etc. Por exemplo, considere a [função JNI NewObject](https://download.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html#wp4517):

```csharp
jobject NewObjectA(JNIEnv *env, jclass clazz, jmethodID methodID, jvalue *args);
```

Isso é exposto como o método [JNIEnv. NewObject](xref:Android.Runtime.JNIEnv.NewObject*) :

```csharp
public static IntPtr NewObject(IntPtr clazz, IntPtr jmethod, params JValue[] parms);
```

A tradução entre as duas chamadas é razoavelmente simples. Em C, você teria:

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

Depois de ter uma instância do objeto Java mantida em um IntPtr, você provavelmente desejará fazer algo com ele. Você pode usar métodos JNIEnv como [JNIEnv. CallVoidMethod ()](xref:Android.Runtime.JNIEnv.CallVoidMethod*) para fazer isso, mas se já houver um wrapper análogo C# , você desejará construir um wrapper na referência de JNI. Você pode fazer isso por meio do método [Extensions. JavaCast\<t >](xref:Android.Runtime.Extensions.JavaCast*) extensão:

```csharp
IntPtr lrefActivity = CreateMapActivity();

// imagine that Activity were instead an interface or abstract type...
Activity mapActivity = new Java.Lang.Object(lrefActivity, JniHandleOwnership.TransferLocalRef)
    .JavaCast<Activity>();
```

Você também pode usar o método [Java. lang. Object. GetObject\<t >](xref:Java.Lang.Object.GetObject*) :

```csharp
IntPtr lrefActivity = CreateMapActivity();

// imagine that Activity were instead an interface or abstract type...
Activity mapActivity = Java.Lang.Object.GetObject<Activity>(lrefActivity, JniHandleOwnership.TransferLocalRef);
```

Além disso, todas as funções JNI foram modificadas removendo o parâmetro `JNIEnv*` presente em cada função JNI.

## <a name="summary"></a>Resumo

Lidar diretamente com o JNI é uma experiência terrível que deve ser evitada em todos os custos. Infelizmente, nem sempre é podem ser evitados; Felizmente, este guia fornecerá assistência quando você atingir os casos Java desvinculados com mono para Android.

## <a name="related-links"></a>Links relacionados

- [Especificação de interface nativa do Java](https://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/jniTOC.html)
- [Funções de interface nativa Java](https://download.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html)
