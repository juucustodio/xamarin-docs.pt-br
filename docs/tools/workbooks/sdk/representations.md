---
title: Representações em Xamarin Workbooks
description: Este documento descreve o pipeline de representação de pastas de trabalho do Xamarin, que permite o processamento de resultados avançados para qualquer código que retorna um valor.
ms.prod: xamarin
ms.assetid: 5C7A60E3-1427-47C9-A022-720F25ECB031
author: lobrien
ms.author: laobri
ms.date: 03/30/2017
ms.openlocfilehash: d9aafbe13e06875b6577a4d2308e419932fd1589
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50103706"
---
# <a name="representations-in-xamarin-workbooks"></a>Representações em Xamarin Workbooks

## <a name="representations"></a>Representações

Dentro de uma pasta de trabalho ou o Inspetor de sessão, código que é executado e produz um resultado (por exemplo, um método que retorna um valor ou o resultado de uma expressão) é processado pelo pipeline de representação no agente. Todos os objetos, com exceção dos primitivos como inteiros, serão refletidos para gerar grafos de membro interativo e passarão por um processo para fornecer representações alternativas que o cliente pode renderizar mais avançada. Objetos de qualquer tamanho e profundidade têm suporte com segurança (incluindo ciclos e enumeráveis infinito) devido à comunicação remota e reflexão lenta e interativa.

Xamarin Workbooks fornece alguns tipos comuns a todos os agentes e os clientes que permitem a avançados de renderização dos resultados. [`Color`][xir-color] é um exemplo desse tipo, onde por exemplo, no iOS, o agente é responsável por converter `CGColor` ou `UIColor` objetos em um `Xamarin.Interactive.Representations.Color` objeto.

Além de representações comuns, a integração do SDK do fornece APIs para serialização representações personalizadas no agente e renderização representações no cliente.

## <a name="external-representations"></a>Representações externas

[`Xamarin.Interactive.IAgent.RepresentationManager`][repman] fornece a capacidade de registrar um [ `RepresentationProvider` ] [ repp], que uma integração deve implementar para converter de um objeto arbitrário para um formulário independente para processar. Esses formulários independentes devem implementar o [ `ISerializableObject` ] [ serobj] interface.

Implementando o `ISerializableObject` interface adiciona um método de serialização que precisamente controla como os objetos são serializados. O `Serialize` método espera que um desenvolvedor exatamente especificará quais propriedades devem ser serializadas, e qual será o nome final. Examinando os `Person` do objeto em nossa [`KitchenSink` exemplo] [exemplo], podemos ver como isso funciona:

```csharp
public sealed class Person : ISerializableObject
{
  public string Name { get; }

  // Rest of the code is omitted…

  void ISerializableObject.Serialize (ObjectSerializer serializer)
    => serializer.Property (nameof (Name), Name);
}
```

Se quiséssemos fornecer um superconjunto ou subconjunto de propriedades do objeto original, é possível fazer isso com `Serialize`. Por exemplo, podemos pode fazer algo assim para fornecer um pré-calculado `Age` propriedade `Person`:

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
> As APIs que produzem `ISerializableObject` objetos diretamente não precisam ser tratadas por um `RepresentationProvider`. Se o objeto que você deseja exibir for **não** um `ISerializableObject`, você vai querer manipular encapsulá-lo no seu `RepresentationProvider`.

### <a name="rendering-a-representation"></a>Uma representação de renderização

Os renderizadores são implementados em JavaScript e terá acesso a uma versão do JavaScript do objeto representado por meio de `ISerializableObject`. A cópia de JavaScript também terá uma `$type` propriedade string que indica o nome do tipo .NET.

É recomendável usar o TypeScript para o código de integração de cliente, que obviamente é compilado em JavaScript comum. De qualquer forma, o SDK fornece [digitações] [ typings] que podem ser referenciados diretamente pelo TypeScript ou chamado simplesmente manualmente se escrevendo baunilha JavaScript é preferencial.

O ponto de integração principal para a renderização é `xamarin.interactive.RendererRegistry`:

```js
xamarin.interactive.RendererRegistry.registerRenderer(
  function (source) {
    if (source.$type === "SampleExternalIntegration.Person")
      return new PersonRenderer;
    return undefined;
  }
);
```

Aqui, `PersonRenderer` implementa o `Renderer` interface. Consulte a [digitações] [ typings] para obter mais detalhes.

[typings]: https://github.com/xamarin/Workbooks/blob/master/SDK/typings/xamarin-interactive.d.ts
[xir-color]: https://developer.xamarin.com/api/type/Xamarin.Interactive.Representations.Color/
[repman]: https://developer.xamarin.com/api/type/Xamarin.Interactive.Representations.IRepresentationManager/
[repp]: https://developer.xamarin.com/api/type/Xamarin.Interactive.Representations.RepresentationProvider/
[serobj]: https://developer.xamarin.com/api/type/Xamarin.Interactive.Serialization.ISerializableObject/
