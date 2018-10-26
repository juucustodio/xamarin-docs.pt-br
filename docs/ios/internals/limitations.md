---
title: Limitações do xamarin. IOS
description: Este documento descreve as limitações do xamarin. IOS, discutindo os elementos genéricos, subclasses genéricas de NSObjects, P/Invokes em objetos genéricos e muito mais.
ms.prod: xamarin
ms.assetid: 5AC28F21-4567-278C-7F63-9C2142C6E06A
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 04/09/2018
ms.openlocfilehash: 1ccbea1921b4e0c4189182696c8679d041eea60b
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50113021"
---
# <a name="limitations-of-xamarinios"></a>Limitações do xamarin. IOS

Uma vez que os aplicativos no iPhone usando xamarin. IOS são compilados para código estático, não é possível usar todos os recursos que exigem a geração de código em tempo de execução.

Estas são as limitações de xamarin. IOS em comparação comparadas a área de trabalho Mono:

 <a name="Limited_Generics_Support" />


## <a name="limited-generics-support"></a>Suporte limitado de genéricos

Ao contrário do Mono/.NET tradicional, o código no iPhone é compilado estaticamente antecipadamente em vez de que está sendo compilado sob demanda por um compilador JIT.

Mono [AOT completo](http://www.mono-project.com/docs/advanced/aot/#full-aot) tecnologia tem algumas limitações em relação aos genéricos, esses erros são causados porque nem todos os possíveis instanciação genérica pode ser determinada com antecedência no tempo de compilação. Isso não é um problema para tempos de execução regulares do .NET ou Mono, como sempre, o código é compilado em tempo de execução usando o Just in compilador do tempo. Mas isso apresenta um desafio para um compilador estático, como xamarin. IOS.

Alguns dos problemas comuns que os desenvolvedores encontram, incluem:

 <a name="Generic_Subclasses_of_NSObjects_are_limited" />


### <a name="generic-subclasses-of-nsobjects-are-limited"></a>Subclasses genéricas de NSObjects são limitados

Xamarin. IOS atualmente tem suporte limitado para a criação de subclasses genéricas de NSObject classe, como não há suporte para métodos genéricos. A partir de 7.2.1, usar subclasses genéricas de NSObjects é possível, como este:

```csharp
class Foo<T> : UIView {
    [..]
}
```

> [!NOTE]
> Embora subclasses genéricas de NSObjects sejam possíveis, há algumas limitações. Leia as [subclasses genéricas de NSObject](~/ios/internals/api-design/nsobject-generics.md) documento para obter mais informações



### <a name="pinvokes-in-generic-types"></a>P/Invokes em tipos genéricos

Não há suporte para P/Invokes em classes genéricas:

```csharp
class GenericType<T> {
    [DllImport ("System")]
    public static extern int getpid ();
}
```

 <a name="Property.SetInfo_on_a_Nullable_Type_is_not_supported" />


### <a name="propertysetinfo-on-a-nullable-type-is-not-supported"></a>Não há suporte para Property.SetInfo em um tipo que permite valor nulo

Uso Property.SetInfo da reflexão para definir o valor em uma propriedade Nullable&lt;T&gt; não é suportada atualmente.

 <a name="Value_types_as_Dictionary_Keys" />


### <a name="value-types-as-dictionary-keys"></a>Tipos de valor como chaves de dicionário

Usando um tipo de valor como um dicionário&lt;TKey, TValue&gt; chave é problemático, como padrão o construtor de dicionário tenta usar EqualityComparer&lt;TKey&gt;. Por padrão. EqualityComparer&lt;TKey&gt;. Padrão, em seguida, tenta usar a reflexão para instanciar um novo tipo que implementa o IEqualityComparer&lt;TKey&gt; interface.

Isso funciona para tipos de referência (como a reflexão + criar um novo tipo de etapa será ignorado), mas, por valor de tipos de falhas e irá consumir rapidamente em vez disso, quando você tentar usá-lo no dispositivo.

 **Solução alternativa**: implementar manualmente a [IEqualityComparer&lt;TKey&gt; ](xref:System.Collections.Generic.IEqualityComparer`1) em um novo tipo de interface e fornecer uma instância desse tipo para o [dicionário&lt;TKey, TValue&gt; ](xref:System.Collections.Generic.Dictionary`2) [(IEqualityComparer&lt;TKey&gt;)](xref:System.Collections.Generic.IEqualityComparer`1) construtor.


 <a name="No_Dynamic_Code_Generation" />


## <a name="no-dynamic-code-generation"></a>Não há geração de código dinâmico

Uma vez que o kernel do iPhone impede que um aplicativo de geração de código dinamicamente Mono no iPhone não dá suporte a nenhuma forma de geração de código dinâmico. Elas incluem:

-  O Emit não está disponível.
-  Não há suporte para Remoting.
-  Não há suporte para a criação de tipos dinamicamente (nenhum Type. GetType ("MyType" 1")), embora procurando tipos existentes (Type. GetType ("System. String"), por exemplo, é simplesmente muito bom). 
-  Retornos de chamada inversos devem ser registrados com o tempo de execução em tempo de compilação.


 
 <a name="System.Reflection.Emit" />


### <a name="systemreflectionemit"></a>System.Reflection.Emit

A falta de System. Reflection. **Emitir** significa que nenhum código que depende de geração de código de tempo de execução funcionará. Isso inclui coisas como:

-  O tempo de execução de linguagem dinâmica.
-  Qualquer idioma criado com base em Dynamic Language Runtime.
-  TransparentProxy do comunicação remota ou qualquer outra coisa que faria com que o tempo de execução gerar código dinamicamente. 


 **Importante:** não confunda **Reflection. Emit** com **reflexão**. Reflection. Emit é sobre como gerar código dinamicamente e ter esse código compilado em JIT e compiladas em código nativo. Devido a limitações no iPhone (nenhuma compilação JIT) isso não é suportado.

Mas a API de reflexão inteira, incluindo Type. GetType ("someClass"), listando os métodos, propriedades, a listagem busca de atributos e valores funciona muito bem.

### <a name="using-delegates-to-call-native-functions"></a>Usando delegados para chamar funções nativas

Para chamar uma função nativa por meio de um representante do c#, a declaração do delegado deve ser decorada com um dos seguintes atributos:

- [UnmanagedFunctionPointerAttribute](xref:System.Runtime.InteropServices.UnmanagedFunctionPointerAttribute) (de preferência, uma vez que ele é compatível com o .NET Standard 1.1 + e de plataforma cruzada)
- [MonoNativeFunctionWrapperAttribute](https://developer.xamarin.com/api/type/ObjCRuntime.MonoNativeFunctionWrapperAttribute)

Deixar de fornecer um desses atributos resultará em um erro de tempo de execução, como:

```
System.ExecutionEngineException: Attempting to JIT compile method '(wrapper managed-to-native) YourClass/YourDelegate:wrapper_aot_native(object,intptr,intptr)' while running in aot-only mode.
```
 
 <a name="Reverse_Callbacks" />


### <a name="reverse-callbacks"></a>Inverter os retornos de chamada

Em Mono padrão, é possível passar instâncias de delegado do c# para código não gerenciado no lugar de um ponteiro de função. O tempo de execução normalmente seria transformar esses ponteiros de função em uma conversão pequeno que permite que o código não gerenciado retornar a chamada para código gerenciado.

Em Mono essas pontes são implementados por Just-in-Time compilador. Quando usar o compilador ahead of time exigido por iPhone existem duas limitações importantes neste momento:

-  Você deve sinalizar todos os seus métodos de retorno de chamada com o [MonoPInvokeCallbackAttribute](https://developer.xamarin.com/api/type/ObjCRuntime.MonoPInvokeCallbackAttribute) 
-  Os métodos precisam ser métodos estáticos, não há suporte por exemplo métodos. 
 
<a name="No_Remoting" />

## <a name="no-remoting"></a>Nenhuma comunicação remota

A pilha de comunicação remota não está disponível no xamarin. IOS.


 <a name="Runtime_Disabled_Features" />


## <a name="runtime-disabled-features"></a>Recursos do tempo de execução desabilitado

Os seguintes recursos foram desabilitados no iOS do Mono Runtime:

-  Criador de Perfil
-  Reflection. Emit
-  Funcionalidade de Reflection.Emit.Save
-  Associações de COM
-  O mecanismo JIT
-  Verificador de metadados (já que não há nenhum JIT)


 <a name=".NET_API_Limitations" />


## <a name="net-api-limitations"></a>Limitações da API .NET

A API .NET exposta é um subconjunto da estrutura completa, pois nem tudo está disponível no iOS. Consulte as perguntas Frequentes para um [lista de assemblies com suporte no momento](~/cross-platform/internals/available-assemblies.md).



Em particular, o perfil de API usado pelo xamarin. IOS não inclui o System. Configuration, portanto, não é possível usar arquivos XML externos para configurar o comportamento do tempo de execução.
