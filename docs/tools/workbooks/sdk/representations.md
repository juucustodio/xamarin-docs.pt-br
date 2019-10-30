---
title: Representações em Xamarin Workbooks
description: Este documento descreve o pipeline de representação de Xamarin Workbooks, que permite a renderização de resultados avançados para qualquer código que retorna um valor.
ms.prod: xamarin
ms.assetid: 5C7A60E3-1427-47C9-A022-720F25ECB031
author: davidortinau
ms.author: daortin
ms.date: 03/30/2017
ms.openlocfilehash: c1afba6943faa03ee07a3ec70624f668748041cb
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73018591"
---
# <a name="representations-in-xamarin-workbooks"></a>Representações em Xamarin Workbooks

## <a name="representations"></a>Representações

Em uma pasta de trabalho ou sessão de Inspetor, o código que é executado e produz um resultado (por exemplo, um método que retorna um valor ou o resultado de uma expressão) é processado por meio do pipeline de representação no agente. Todos os objetos, com exceção de primitivos como inteiros, serão refletidos para produzir grafos de membro interativos e passarão por um processo para fornecer representações alternativas que o cliente pode renderizar de forma mais rica. Os objetos de qualquer tamanho e profundidade têm suporte seguro (incluindo ciclos e enumeráveis infinitos) devido à reflexão e à comunicação remota lentas e interativas.

Xamarin Workbooks fornece alguns tipos comuns a todos os agentes e clientes que permitem a renderização avançada de resultados. `Color` é um exemplo desse tipo, em que, por exemplo, no iOS, o agente é responsável por converter `CGColor` ou `UIColor` objetos em um objeto `Xamarin.Interactive.Representations.Color`.

Além de representações comuns, o SDK de integração fornece APIs para serializar representações personalizadas no agente e representações de renderização no cliente.

## <a name="external-representations"></a>Representações externas

`Xamarin.Interactive.IAgent.RepresentationManager` fornece a capacidade de registrar um `RepresentationProvider`, que uma integração deve implementar para converter de um objeto arbitrário para um formulário independente para renderização. Esses formulários independentes devem implementar a interface `ISerializableObject`.

A implementação da interface `ISerializableObject` adiciona um método Serialize que controla precisamente como os objetos são serializados. O método `Serialize` espera que um desenvolvedor Especifique exatamente quais propriedades devem ser serializadas e qual será o nome final. Observando o objeto `Person` em nosso [exemplo de`KitchenSink`] [amostra], podemos ver como isso funciona:

```csharp
public sealed class Person : ISerializableObject
{
  public string Name { get; }

  // Rest of the code is omitted…

  void ISerializableObject.Serialize (ObjectSerializer serializer)
    => serializer.Property (nameof (Name), Name);
}
```

Se quiséssemos fornecer um superconjunto ou subconjunto de propriedades do objeto original, podemos fazer isso com `Serialize`. Por exemplo, podemos fazer algo assim para fornecer uma propriedade de `Age` previamente computada em `Person`:

```csharp
public sealed class Person : ISerializableObject
{
  public string Name { get; set; }
  public DateTime DateOfBirth { get; set; }

  // <snip>

  void ISerializableObject.Serialize (ObjectSerializer serializer)
  {
    serializer.Property (nameof (Name), Name);
    serializer.Property (nameof (DateOfBirth), DateOfBirth);

    // Let's pre-compute an Age property that's the person's age in years,
    // so we don't have to compute it in the renderer.
    var age = (DateTime.MinValue + (DateTime.Now - DateOfBirth)).Year - 1;
    serializer.Property ("Age", age)
  }
}
```

> [!NOTE]
> As APIs que geram `ISerializableObject` objetos diretamente não precisam ser manipuladas por um `RepresentationProvider`. Se o objeto que você deseja exibir **não** for um `ISerializableObject`, você desejará manipular o encapsulamento em seu `RepresentationProvider`.

### <a name="rendering-a-representation"></a>Renderizando uma representação

Os renderizadores são implementados em JavaScript e terão acesso a uma versão JavaScript do objeto representado por meio de `ISerializableObject`. A cópia JavaScript também terá uma propriedade de cadeia de caracteres `$type` que indica o nome do tipo .NET.

É recomendável usar o TypeScript para o código de integração do cliente, que é, naturalmente, compilado no JavaScript da baunilha. De qualquer forma, o SDK fornece [digitações][typings] que podem ser referenciadas diretamente pelo TypeScript ou simplesmente referenciadas manualmente se for preferível escrever JavaScript de baunilha.

O principal ponto de integração para renderização é `xamarin.interactive.RendererRegistry`:

```js
xamarin.interactive.RendererRegistry.registerRenderer(
  function (source) {
    if (source.$type === "SampleExternalIntegration.Person")
      return new PersonRenderer;
    return undefined;
  }
);
```

Aqui, `PersonRenderer` implementa a interface `Renderer`. Consulte as [digitações][typings] para obter mais detalhes.

[typings]: https://github.com/xamarin/Workbooks/blob/master/SDK/typings/xamarin-interactive.d.ts
