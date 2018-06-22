---
title: Vinculação no Android
ms.prod: xamarin
ms.assetid: 3528E195-AA74-90AF-B5F3-3B65FB4F0BB8
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/30/2018
ms.openlocfilehash: bcc9617553be425ab17050a1a6fb034f6d7f596d
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
ms.locfileid: "30767577"
---
# <a name="linking-on-android"></a>Vinculação no Android

Aplicativos Xamarin.Android usam um *vinculador* para reduzir o tamanho do aplicativo. O vinculador utiliza uma análise estática do seu aplicativo para determinar quais assemblies, tipos e membros são realmente usados. O vinculador, em seguida, se comporta como um *coletor de lixo*, procurando continuamente assemblies, tipos e membros que são referenciados, até todo o fechamento desses elementos ser encontrado. Tudo fora esse fechamento é *descartado*.

Por exemplo, a amostra [Hello, Android](https://developer.xamarin.com/samples/HelloM4A/):

|Configuração|Tamanho 1.2.0|Tamanho 4.0.1|
|---|---|---|
|Versão sem vinculação:|14,0 MB|16,0 MB|
|Versão com vinculação:|4,2 MB|2,9 MB|

A vinculação resulta em um pacote que tem 30% do tamanho do pacote original (desvinculado) na 1.2.0 e 18% do pacote desvinculado na 4.0.1.



## <a name="control"></a>Controle

A vinculação se baseia em *análise estática*. Consequentemente, tudo o que depende do ambiente de tempo de execução não será detectado:

```csharp
// To play along at home, Example must be in a different assembly from MyActivity.
public class Example {
    // Compiler provides default constructor...
}

[Activity (Label="Linker Example", MainLauncher=true)]
public class MyActivity {
    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        // Will this work?
        var o = Activator.CreateInstance (typeof (ExampleLibrary.Example));
    }
}
```


### <a name="linker-behavior"></a>Comportamento do vinculador

O mecanismo principal para controlar o vinculador é a lista suspensa **Comportamento do vinculador** (*Vinculação* no Visual Studio) dentro da caixa de diálogo **Opções de Projeto**. Há três opções:

1.  **Não Vincular** (*Nenhum* no Visual Studio)
1.  **Vincular os Assemblies do SDK** (*Somente os Assemblies do SDK*)
1.  **Vincular Todos os Assemblies** (*Assemblies de SDK e de Usuário*)


A opção **Não Vincular** desliga o vinculador; o exemplo de tamanho de aplicativo "Versão sem Vinculação" acima usava esse comportamento. Isso é útil para solucionar problemas de falhas de tempo de execução, para ver se o vinculador é responsável. Essa configuração geralmente não é recomendada para builds de produção.

A opção **Vincular Assemblies de SDK** vincula apenas [assemblies que vêm com o Xamarin.Android](~/cross-platform/internals/available-assemblies.md).
Todos os outros assemblies (tais como o seu código) não estão vinculados.

A opção **Vincular Todos os Assemblies** vincula todos os assemblies, o que significa que seu código também pode ser removido se não houver nenhuma referência estática.

O exemplo acima funcionará com as opções *Não Vincular* e *Vincular Assemblies de SDK* e falhará com o comportamento *Vincular Todos os Assemblies*, gerando o erro a seguir:

```shell
E/mono    (17755): [0xafd4d440:] EXCEPTION handling: System.MissingMethodException: Default constructor not found for type ExampleLibrary.Example.
I/MonoDroid(17755): UNHANDLED EXCEPTION: System.MissingMethodException: Default constructor not found for type ExampleLibrary.Example.
I/MonoDroid(17755): at System.Activator.CreateInstance (System.Type,bool) <0x00180>
I/MonoDroid(17755): at System.Activator.CreateInstance (System.Type) <0x00017>
I/MonoDroid(17755): at LinkerScratch2.Activity1.OnCreate (Android.OS.Bundle) <0x00027>
I/MonoDroid(17755): at Android.App.Activity.n_OnCreate_Landroid_os_Bundle_ (intptr,intptr,intptr) <0x00057>
I/MonoDroid(17755): at (wrapper dynamic-method) object.95bb4fbe-bef8-4e5b-8e99-ca83a5d7a124 (intptr,intptr,intptr) <0x00033>
E/mono    (17755): [0xafd4d440:] EXCEPTION handling: System.MissingMethodException: Default constructor not found for type ExampleLibrary.Example.
E/mono    (17755):
E/mono    (17755): Unhandled Exception: System.MissingMethodException: Default constructor not found for type ExampleLibrary.Example.
E/mono    (17755):   at System.Activator.CreateInstance (System.Type type, Boolean nonPublic) [0x00000] in <filename unknown>:0
E/mono    (17755):   at System.Activator.CreateInstance (System.Type type) [0x00000] in <filename unknown>:0
E/mono    (17755):   at LinkerScratch2.Activity1.OnCreate (Android.OS.Bundle bundle) [0x00000] in <filename unknown>:0
E/mono    (17755):   at Android.App.Activity.n_OnCreate_Landroid_os_Bundle_ (IntPtr jnienv, IntPtr native__this, IntPtr native_savedInstanceState) [0x00000] in <filename unknown>:0
E/mono    (17755):   at (wrapper dynamic-method) object:95bb4fbe-bef8-4e5b-8e99-ca83a5d7a124 (intptr,intptr,intptr)
```


### <a name="preserving-code"></a>Preservação do código

Às vezes, o vinculador removerá código que você deseja preservar. Por exemplo:

-   Talvez exista código que você chame dinamicamente via `System.Reflection.MemberInfo.Invoke`.

-   Se você cria uma instância de tipos dinamicamente, convém preservar o construtor padrão de seus tipos.

-   Se você usar a serialização de XML, você talvez queira preservar as propriedades de seus tipos.

Nesses casos, você pode usar o atributo [Android.Runtime.Preserve](https://developer.xamarin.com/api/type/Android.Runtime.PreserveAttribute/). Cada membro que não está vinculado estaticamente pelo aplicativo está sujeito a ser removido, então esse atributo pode ser usado para marcar membros que não estão referenciados estaticamente, mas ainda são necessários para seu aplicativo. Você pode aplicar esse atributo a todos os membros de um tipo ou ao tipo propriamente dito.

No exemplo a seguir, esse atributo é usado para preservar o construtor da classe `Example`:

```csharp
public class Example
{
    [Android.Runtime.Preserve]
    public Example ()
    {
    }
}
```

Se você quiser preservar o tipo inteiro, poderá usar a seguinte sintaxe de atributo:

```csharp
[Android.Runtime.Preserve (AllMembers = true)]
```

Por exemplo, no fragmento de código a seguir, toda a classe `Example` é preservada para a serialização de XML:

```csharp
[Android.Runtime.Preserve (AllMembers = true)]
class Example
{
    // Compiler provides default constructor...
}
```

Às vezes você deseja preservar a certos membros, mas somente se o tipo recipiente foi preservado. Nesses casos, use a seguinte sintaxe de atributo:

```csharp
[Android.Runtime.Preserve (Conditional = true)]
```

Se você não desejar executar uma dependência em bibliotecas do Xamarin &ndash; por exemplo, você está criando uma PCL (biblioteca de classes portátil) de plataforma cruzada &ndash; você ainda pode usar o atributo `Android.Runtime.Preserve`. Para fazer isso, declare uma classe `PreserveAttribute` dentro do namespace `Android.Runtime` deste modo:

```csharp
namespace Android.Runtime
{
    public sealed class PreserveAttribute : System.Attribute
    {
        public bool AllMembers;
        public bool Conditional;
    }
}
```

Nos exemplos acima, o atributo `Preserve` é declarado no namespace `Android.Runtime`; no entanto, você pode usar o atributo `Preserve` em qualquer namespace porque o vinculador procura esse atributo pelo nome do tipo.



### <a name="falseflag"></a>falseflag

Se o atributo [Preserve] não pode ser usado, geralmente é útil fornecer um bloco de código para que o vinculador acredite que o tipo é usado, impedindo simultaneamente que o bloco de código seja executado em tempo de execução. Para usar essa técnica, poderíamos fazer:

```csharp
[Activity (Label="Linker Example", MainLauncher=true)]
class MyActivity {

#pragma warning disable 0219, 0649
    static bool falseflag = false;
    static MyActivity ()
    {
        if (falseflag) {
            var ignore = new Example ();
        }
    }
#pragma warning restore 0219, 0649

    // ...
}
```



### <a name="linkskip"></a>linkskip

É possível especificar que um conjunto de assemblies fornecidos pelo usuário não sejam vinculados, permitindo simultaneamente que outros assemblies de usuário sejam ignorados com o comportamento *Vincular Assemblies de SDK* usando a [Propriedade AndroidLinkSkip do MSBuild](~/android/deploy-test/building-apps/build-process.md):

```xml
<PropertyGroup>
    <AndroidLinkSkip>Assembly1;Assembly2</AndroidLinkSkip>
</PropertyGroup>
```


### <a name="linkdescription"></a>LinkDescription

A [`@(LinkDescription)`](~/android/deploy-test/building-apps/build-process.md)
**Ação de build** pode ser usada em arquivos que podem conter um [arquivo de configuração de vinculador personalizado](~/cross-platform/deploy-test/linker.md).
arquivo. Arquivos de configuração de vinculador personalizados podem ser necessários para preservar membros `internal` ou `private` que precisam ser preservados.



### <a name="custom-attributes"></a>Atributos personalizados

Quando um assembly é vinculado, os seguintes tipos de atributo personalizados serão removidos de todos os membros:

-  System.ObsoleteAttribute
-  System.MonoDocumentationNoteAttribute
-  System.MonoExtensionAttribute
-  System.MonoInternalNoteAttribute
-  System.MonoLimitationAttribute
-  System.MonoNotSupportedAttribute
-  System.MonoTODOAttribute
-  System.Xml.MonoFIXAttribute


Quando um assembly é vinculado, os seguintes tipos de atributo personalizados serão removidos de todos os builds de versão:

-  System.Diagnostics.DebuggableAttribute
-  System.Diagnostics.DebuggerBrowsableAttribute
-  System.Diagnostics.DebuggerDisplayAttribute
-  System.Diagnostics.DebuggerHiddenAttribute
-  System.Diagnostics.DebuggerNonUserCodeAttribute
-  System.Diagnostics.DebuggerStepperBoundaryAttribute
-  System.Diagnostics.DebuggerStepThroughAttribute
-  System.Diagnostics.DebuggerTypeProxyAttribute
-  System.Diagnostics.DebuggerVisualizerAttribute


## <a name="related-links"></a>Links relacionados

- [Configuração personalizada do vinculador](~/cross-platform/deploy-test/linker.md)
- [Vinculação no iOS](~/ios/deploy-test/linker.md)
