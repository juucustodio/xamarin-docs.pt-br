---
title: Limitações do Xamarin. iOS
description: Este documento descreve as limitações do Xamarin. iOS, discutindo genéricos, subclasses genéricas de NSObjects, P/Invokes em objetos genéricos e muito mais.
ms.prod: xamarin
ms.assetid: 5AC28F21-4567-278C-7F63-9C2142C6E06A
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/09/2018
ms.openlocfilehash: 003ea31c765bd2610e93e0f85fe995606d55022f
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84567392"
---
# <a name="limitations-of-xamarinios"></a>Limitações do Xamarin. iOS

Como os aplicativos que usam o Xamarin. iOS são compilados no código estático, não é possível usar quaisquer instalações que exijam a geração de código em tempo de execução.

Essas são as limitações do Xamarin. iOS em comparação com o mono da área de trabalho:

 <a name="Limited_Generics_Support"></a>

## <a name="limited-generics-support"></a>Suporte a genéricos limitados

Ao contrário do mono/. NET tradicional, o código no iPhone é compilado estaticamente antecipadamente em vez de ser compilado sob demanda por um compilador JIT.

A tecnologia [AOT completa](https://www.mono-project.com/docs/advanced/aot/#full-aot) do mono tem algumas limitações em relação aos genéricos, elas são causadas porque nem todas as instanciações genéricas possíveis podem ser determinadas na frente no tempo de compilação. Isso não é um problema para tempos de execução regulares do .NET ou do mono, pois o código é sempre compilado em tempo de execução usando o compilador just in time. Mas isso representa um desafio para um compilador estático como o Xamarin. iOS.

Alguns dos problemas comuns que os desenvolvedores executam, incluem:

 <a name="Generic_Subclasses_of_NSObjects_are_limited"></a>

### <a name="generic-subclasses-of-nsobjects-are-limited"></a>As subclasses genéricas de NSObjects são limitadas

O Xamarin. iOS atualmente tem suporte limitado para criar subclasses genéricas da classe NSObject, como não há suporte para métodos genéricos. A partir de 7.2.1, é possível usar subclasses genéricas de NSObjects, como esta:

```csharp
class Foo<T> : UIView {
    [..]
}
```

> [!NOTE]
> Embora as subclasses genéricas de NSObjects sejam possíveis, há algumas limitações. Ler as [subclasses genéricas do documento NSObject](~/ios/internals/api-design/nsobject-generics.md) para obter mais informações

 <a name="No_Dynamic_Code_Generation"></a>

## <a name="no-dynamic-code-generation"></a>Nenhuma geração de código dinâmico

Como o kernel do iOS impede que um aplicativo gere código dinamicamente, o Xamarin. iOS não oferece suporte a nenhuma forma de geração de código dinâmico. Elas incluem:

- O System. Reflection. Emit não está disponível.
- Não há suporte para System. Runtime. Remoting.
- Não há suporte para a criação dinâmica de tipos (sem Type. GetType ("com MyType ' 1")), embora a pesquisa de tipos existentes (Type. GetType ("System. String"), por exemplo, funcione bem).
- Retornos de chamada invertidos devem ser registrados com o tempo de execução no momento da compilação.

 <a name="System.Reflection.Emit"></a>

### <a name="systemreflectionemit"></a>System.Reflection.Emit

A falta de System. Reflection. **Emissão** significa que nenhum código que depende da geração de código de tempo de execução funcionará. Isso inclui itens como:

- O tempo de execução de linguagem dinâmica.
- Quaisquer linguagens criadas com base no tempo de execução de linguagem dinâmica.
- TransparentProxy de comunicação remota ou qualquer outra coisa que cause o tempo de execução para gerar código dinamicamente.

  > [!IMPORTANT]
  > Não confunda **Reflection. Emit** com **reflexão**. Reflection. Emit é sobre a geração dinâmica de código e o código compilado em JIT e compilado para código nativo. Devido às limitações no iOS (sem compilação JIT), não há suporte para isso.

Mas a API de reflexão inteira, incluindo Type. GetType ("someClass"), listagem de métodos, propriedades de listagem, busca de atributos e valores funciona bem.

### <a name="using-delegates-to-call-native-functions"></a>Usando delegados para chamar funções nativas

Para chamar uma função nativa por meio de um delegado C#, a declaração do delegado deve ser decorada com um dos seguintes atributos:

- [UnmanagedFunctionPointerAttribute](xref:System.Runtime.InteropServices.UnmanagedFunctionPointerAttribute) (preferencial, pois é multiplataforma e compatível com .net Standard 1.1 +)
- [MonoNativeFunctionWrapperAttribute](xref:ObjCRuntime.MonoNativeFunctionWrapperAttribute)

A falha ao fornecer um desses atributos resultará em um erro de tempo de execução, como:

```
System.ExecutionEngineException: Attempting to JIT compile method '(wrapper managed-to-native) YourClass/YourDelegate:wrapper_aot_native(object,intptr,intptr)' while running in aot-only mode.
```

 <a name="Reverse_Callbacks"></a>

### <a name="reverse-callbacks"></a>Retornos de chamada reverso

Em mono padrão, é possível passar instâncias de delegado em C# para código não gerenciado no lugar de um ponteiro de função. Normalmente, o tempo de execução transforma esses ponteiros de função em uma conversão pequena que permite que um código não gerenciado chame de volta para código gerenciado.

No mono, essas pontes são implementadas pelo compilador just-in-time. Ao usar o compilador antes do tempo exigido pelo iPhone, há duas limitações importantes neste ponto:

- Você deve sinalizar todos os seus métodos de retorno de chamada com o [MonoPInvokeCallbackAttribute](xref:ObjCRuntime.MonoPInvokeCallbackAttribute)
- Os métodos precisam ser métodos estáticos, não há suporte para métodos de instância.

<a name="No_Remoting"></a>

## <a name="no-remoting"></a>Sem comunicação remota

A pilha de comunicação remota não está disponível no Xamarin. iOS.

 <a name="Runtime_Disabled_Features"></a>

## <a name="runtime-disabled-features"></a>Recursos desabilitados para tempo de execução

Os recursos a seguir foram desabilitados no tempo de execução do iOS do mono:

- Criador de perfil
- Reflection. Emit
- Funcionalidade Reflection. Emit. Save
- Associações COM
- O mecanismo JIT
- Verificador de metadados (já que não há JIT)

 <a name=".NET_API_Limitations"></a>

## <a name="net-api-limitations"></a>Limitações da API .NET

A API do .NET exposta é um subconjunto da estrutura completa, já que tudo está disponível no iOS. Consulte as perguntas frequentes para obter uma [lista de assemblies com suporte no momento](~/cross-platform/internals/available-assemblies.md).

Em particular, o perfil de API usado pelo Xamarin. iOS não inclui System. Configuration, portanto, não é possível usar arquivos XML externos para configurar o comportamento do tempo de execução.
