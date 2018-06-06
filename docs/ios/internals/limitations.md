---
title: Limitações do xamarin
description: Este documento descreve as limitações do xamarin, discutindo genéricos, genéricas subclasses de NSObjects, P/Invokes em objetos genéricos e muito mais.
ms.prod: xamarin
ms.assetid: 5AC28F21-4567-278C-7F63-9C2142C6E06A
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 04/09/2018
ms.openlocfilehash: 8eb2cd5a749beab6f089479f5992fe3fbc16dd0a
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34786223"
---
# <a name="limitations-of-xamarinios"></a>Limitações do xamarin

Como os aplicativos no iPhone usando xamarin são compilados para código estático, não é possível usar quaisquer recursos que exigem a geração de código em tempo de execução.

Estas são as limitações de xamarin em comparação comparadas a área de trabalho Mono:

 <a name="Limited_Generics_Support" />


## <a name="limited-generics-support"></a>Suporte limitado genéricos

Ao contrário do .NET/tradicional Mono, no iPhone estaticamente compilação do código antecipadamente em vez de ser compilado sob demanda por um compilador JIT.

Do mono [AOT completo](http://www.mono-project.com/docs/advanced/aot/#full-aot) tecnologia tem algumas limitações em relação ao genéricos, eles são causados porque nem todos os possíveis instanciação genérica pode ser determinada com antecedência no tempo de compilação. Isso não é um problema para tempos de execução .NET ou Mono regulares, como o código sempre é compilado em tempo de execução usando a apenas em compilador de tempo. Mas isso representa um desafio para um compilador estático como xamarin.

Alguns dos problemas comuns que os desenvolvedores, incluem:

 <a name="Generic_Subclasses_of_NSObjects_are_limited" />


### <a name="generic-subclasses-of-nsobjects-are-limited"></a>As Subclasses de NSObjects genérico são limitadas

Xamarin atualmente tem suporte limitado para criação genéricas subclasses da classe NSObject, como não há suporte para métodos genéricos. A partir de 7.2.1, usar subclasses genéricas de NSObjects é possível, como este:

```csharp
class Foo<T> : UIView {
    [..]
}
```

> [!NOTE]
> Enquanto genéricas subclasses de NSObjects são possíveis, há algumas limitações. Leitura de [genéricas subclasses de NSObject](~/ios/internals/api-design/nsobject-generics.md) documento para obter mais informações



### <a name="pinvokes-in-generic-types"></a>P/invoca em tipos genéricos

Não há suporte para P/Invokes nas classes genéricas:

```csharp
class GenericType<T> {
    [DllImport ("System")]
    public static extern int getpid ();
}
```

 <a name="Property.SetInfo_on_a_Nullable_Type_is_not_supported" />


### <a name="propertysetinfo-on-a-nullable-type-is-not-supported"></a>Não há suporte para Property.SetInfo em um tipo anulável

Usando Property.SetInfo da reflexão para definir o valor em um Nullable&lt;T&gt; não é suportada atualmente.

 <a name="Value_types_as_Dictionary_Keys" />


### <a name="value-types-as-dictionary-keys"></a>Tipos de valor como chaves de dicionário

Usando um tipo de valor como um dicionário&lt;TKey, TValue&gt; chave é um problema, como padrão o construtor de dicionário tenta usar EqualityComparer&lt;TKey&gt;. O padrão. EqualityComparer&lt;TKey&gt;. Padrão, por sua vez, tenta usar reflexão para criar uma instância de um novo tipo que implementa o IEqualityComparer&lt;TKey&gt; interface.

Isso funciona para tipos de referência (como a reflexão + criar um novo tipo de etapa será ignorado), mas o valor de tipos de falhas e o que grave rapidamente em vez disso, quando você tentar usá-lo no dispositivo.

 **Solução alternativa**: implementar manualmente a [IEqualityComparer&lt;TKey&gt; ](https://developer.xamarin.com/api/type/System.Collections.Generic.IEqualityComparer%601/) em um novo tipo de interface e fornecer uma instância desse tipo de [dicionário&lt;TKey, TValue&gt; ](https://developer.xamarin.com/api/type/System.Collections.Generic.Dictionary%3CTKey,TValue%3E/) [(IEqualityComparer&lt;TKey&gt;)](https://developer.xamarin.com/api/type/System.Collections.Generic.IEqualityComparer%601/) construtor.


 <a name="No_Dynamic_Code_Generation" />


## <a name="no-dynamic-code-generation"></a>Não há geração de código dinâmico

Desde que o kernel do iPhone impede que um aplicativo gerar código dinamicamente Mono no iPhone não dá suporte a nenhuma forma de geração de código dinâmico. Elas incluem:

-  O Emit não está disponível.
-  Não há suporte para System.Runtime.Remoting.
-  Não há suporte para a criação de tipos dinamicamente (não Type. GetType ("MyType ' 1")), embora procurar tipos existentes (Type. GetType ("System. String"), por exemplo, funciona bem). 
-  Inversas retornos de chamada devem ser registrados com o tempo de execução em tempo de compilação.


 
 <a name="System.Reflection.Emit" />


### <a name="systemreflectionemit"></a>System.Reflection.Emit

A falta de System. Reflection. **Emitir** significa que nenhum código que depende de geração de código de tempo de execução funcionará. Isso inclui coisas como:

-  O Dynamic Language Runtime.
-  Qualquer idioma criado sobre o Dynamic Language Runtime.
-  TransparentProxy do comunicação remota ou qualquer outra coisa que faria com que o tempo de execução gerar código dinamicamente. 


 **Importante:** não confunda **Reflection. Emit** com **reflexão**. Reflection. Emit é sobre como gerar código dinamicamente e ter esse código em JIT e compiladas em código nativo. Devido a limitações no iPhone (nenhuma compilação JIT) isso não é suportado.

Mas funciona bem, a API de reflexão inteira, incluindo Type. GetType ("someClass"), listando métodos, listando propriedades, atributos e valores de busca.

### <a name="using-delegates-to-call-native-functions"></a>Usando delegados para chamar funções nativas

Para chamar uma função nativa por meio de um representante do c#, a declaração de delegado deve ser decorada com um dos seguintes atributos:

- [UnmanagedFunctionPointerAttribute](https://developer.xamarin.com/api/type/System.Runtime.InteropServices.UnmanagedFunctionPointerAttribute/) (preferencial, pois ela é compatível com o padrão de .NET 1.1 + e de plataforma cruzada)
- [MonoNativeFunctionWrapperAttribute](https://developer.xamarin.com/api/type/ObjCRuntime.MonoNativeFunctionWrapperAttribute)

Falha ao fornecer um desses atributos resultará em um erro em tempo de execução, como:

```
System.ExecutionEngineException: Attempting to JIT compile method '(wrapper managed-to-native) YourClass/YourDelegate:wrapper_aot_native(object,intptr,intptr)' while running in aot-only mode.
```
 
 <a name="Reverse_Callbacks" />


### <a name="reverse-callbacks"></a>Reverter os retornos de chamada

É possível passar instâncias de delegado do c# para código não gerenciado em vez de um ponteiro de função em Mono padrão. O tempo de execução normalmente seria transformar os ponteiros de função em uma conversão pequena que permite que o código não gerenciado chamar código gerenciado.

Em Mono essas pontes são implementados por Just-in-Time compilador. Quando usa o compilador antecipada de tempo exigido por iPhone existem duas limitações importantes neste ponto:

-  Você deve sinalizar todos os seus métodos de retorno de chamada com o [MonoPInvokeCallbackAttribute](https://developer.xamarin.com/api/type/ObjCRuntime.MonoPInvokeCallbackAttribute) 
-  Os métodos precisam ser métodos estáticos, não há suporte para a instância métodos. 
 
<a name="No_Remoting" />

## <a name="no-remoting"></a>Nenhuma comunicação remota

A pilha de comunicação remota não está disponível em xamarin.


 <a name="Runtime_Disabled_Features" />


## <a name="runtime-disabled-features"></a>Tempo de execução desabilitado recursos

Os seguintes recursos foram desabilitados no iOS do Mono tempo de execução:

-  Criador de Perfil
-  Reflection. Emit
-  Funcionalidade de Reflection.Emit.Save
-  Associações de COM
-  O mecanismo JIT
-  Verificador de metadados (como não há nenhuma JIT)


 <a name=".NET_API_Limitations" />


## <a name="net-api-limitations"></a>Limitações da API .NET

A API .NET exposto é um subconjunto do framework completo como não tudo o que está disponível no iOS. Consulte as perguntas Frequentes para um [lista de assemblies com suporte no momento](~/cross-platform/internals/available-assemblies.md).



Em particular, o perfil de API usado por xamarin não inclui o System. Configuration, portanto, não é possível usar arquivos XML externos para configurar o comportamento de tempo de execução.
