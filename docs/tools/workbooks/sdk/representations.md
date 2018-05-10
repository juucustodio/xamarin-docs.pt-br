---
title: Representações em pastas de trabalho do Xamarin
ms.prod: xamarin
ms.assetid: 5C7A60E3-1427-47C9-A022-720F25ECB031
author: topgenorth
ms.author: toopge
ms.date: 03/30/2017
ms.openlocfilehash: 90c3522fda0541021e10c97ce27cc92b5db37902
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/09/2018
---
# <a name="representations-in-xamarin-workbooks"></a>Representações em pastas de trabalho do Xamarin

## <a name="representations"></a>representações

Dentro de uma sessão de pasta de trabalho ou Inspetor, código que é executado e gera um resultado (por exemplo, um método que retornar um valor ou o resultado de uma expressão) é processado por meio do pipeline de representação no agente. Todos os objetos, com exceção dos tipos primitivos como inteiros, serão refletidos para gerar gráficos interativos membro e passará por um processo para fornecer representações alternativas que o cliente pode renderizar com mais detalhes. Objetos de qualquer tamanho e a profundidade com segurança têm suporte (inclusive ciclos e enumeráveis infinito) devido à comunicação remota e reflexão lento e interativo.

Pastas de trabalho do Xamarin fornece alguns tipos comuns a todos os agentes e os clientes que permitem a renderização avançada de resultados. [`Color`][xir-color] é um exemplo de tipo, onde o agente é responsável por converter de exemplo no iOS, `CGColor` ou `UIColor` objetos em um `Xamarin.Interactive.Representations.Color` objeto.

Além das representações comuns, a integração do SDK fornece APIs para serialização representações personalizadas no agente e renderização representações no cliente.

## <a name="external-representations"></a>Representações externas

[`Xamarin.Interactive.IAgent.RepresentationManager`][repman] fornece a capacidade de registrar um [ `RepresentationProvider` ] [ repp], que uma integração deve implementar para converter de um objeto arbitrário para um formulário desconhecido para processar. Essas formas desconhecidas devem implementar o [ `ISerializableObject` ] [ serobj] interface.

Implementando o `ISerializableObject` interface adiciona um método de serialização que precisamente controla como os objetos são serializados. O `Serialize` método espera que um desenvolvedor exatamente especificará quais propriedades devem ser serializadas, e qual será o nome final. Observando o `Person` do objeto em nosso [`KitchenSink` exemplo] [exemplo], podemos ver como isso funciona:

```csharp
public sealed class Person : ISerializableObject
{
  public string Name { get; }

  // Rest of the code is omitted…

  void ISerializableObject.Serialize (ObjectSerializer serializer)
    => serializer.Property (nameof (Name), Name);
}
```

Se quiséssemos fornecer um superconjunto ou um subconjunto das propriedades do objeto original, é possível fazer isso com `Serialize`. Por exemplo, podemos pode fazer algo parecido para fornecer um pré-calculadas `Age` propriedade `Person`:

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
> APIs que produzem `ISerializableObject` objetos diretamente não precisam ser manipulados por um `RepresentationProvider`. Se o objeto que você deseja exibir for **não** um `ISerializableObject`, você deve tratar encapsulá-lo no seu `RepresentationProvider`.

### <a name="rendering-a-representation"></a>Uma representação de renderização

Processadores são implementados em JavaScript e terá acesso a uma versão de JavaScript do objeto representado por meio de `ISerializableObject`. A cópia de JavaScript também terá um `$type` propriedade que indica o nome do tipo .NET de cadeia de caracteres.

É recomendável usar TypeScript para código de integração de cliente, que é compilado naturalmente de baunilha JavaScript. De qualquer forma, o SDK fornece [tipificação] [ typings] que pode ser referenciado diretamente pela TypeScript ou simplesmente chamado manualmente se escrevendo baunilha JavaScript é preferencial.

O ponto de integração principal para renderização é `xamarin.interactive.RendererRegistry`:

```js
xamarin.interactive.RendererRegistry.registerRenderer(
  function (source) {
    if (source.$type === "SampleExternalIntegration.Person")
      return new PersonRenderer;
    return undefined;
  }
);
```

Aqui, `PersonRenderer` implementa o `Renderer` interface. Consulte o [tipificação] [ typings] para obter mais detalhes.

[typings]: https://github.com/xamarin/Workbooks/blob/master/SDK/typings/xamarin-interactive.d.ts
[xir-color]: https://developer.xamarin.com/api/type/Xamarin.Interactive.Representations.Color/
[repman]: https://developer.xamarin.com/api/type/Xamarin.Interactive.Representations.IRepresentationManager/
[repp]: https://developer.xamarin.com/api/type/Xamarin.Interactive.Representations.RepresentationProvider/
[serobj]: https://developer.xamarin.com/api/type/Xamarin.Interactive.Serialization.ISerializableObject/
