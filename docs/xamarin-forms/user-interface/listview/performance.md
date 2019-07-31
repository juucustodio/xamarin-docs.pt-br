---
title: Desempenho de ListView
description: Embora o ListView é um modo de exibição avançado para exibir os dados, ele tem algumas limitações. Este artigo explica como garantir um ótimo desempenho com um ListView do xamarin. Forms em um aplicativo.
ms.prod: xamarin
ms.assetid: 1B085639-652C-4862-86EB-5D55D32B9395
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/11/2017
ms.openlocfilehash: 4a0a7a4db4b0ca982a162ec3a0b67dc729af0ed2
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68655937"
---
# <a name="listview-performance"></a>Desempenho de ListView

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistviewnative)

Ao escrever aplicativos móveis, o desempenho é importante. Os usuários já conhecem rolagem suave e tempos de carregamento rápido. Com falha atender às expectativas dos usuários custam avaliações na loja de aplicativos ou no caso de um aplicativo de linha de negócios, custo organização tempo e dinheiro.

Embora [ `ListView` ](xref:Xamarin.Forms.ListView) é um modo eficiente para exibir os dados, ele tem algumas limitações. Desempenho de rolagem pode ser afetado ao usar células personalizadas, especialmente quando eles contenham hierarquias de exibição profundamente aninhadas ou usam determinados layouts que exigem muita de medição. Felizmente, existem técnicas que você pode usar para evitar um desempenho ruim.

<a name="cachingstrategy" />

## <a name="caching-strategy"></a>Estratégia de cache

ListViews geralmente são usados para exibir dados muito mais do que pode caber na tela. Considere um aplicativo de música, por exemplo. Uma biblioteca de músicas pode ter milhares de entradas. A abordagem simple, o que seria criar uma linha para cada música, teria um desempenho ruim. Essa abordagem desperdiça memória valiosa e pode reduzir a rolagem para um rastreamento. Outra abordagem é criar e destruir linhas como dados são colocados na exibição. Isso exige a instanciação de constante e a limpeza de objetos de exibição, que pode ser muito lento.

Para conservar memória, nativo [ `ListView` ](xref:Xamarin.Forms.ListView) equivalentes para cada plataforma tem recursos internos para reutilização de linhas. Apenas as células visíveis na tela são carregadas na memória e o **conteúdo** é carregado em células existentes. Isso impede que o aplicativo precisar criar uma instância de milhares de objetos, economizando tempo e memória.

Xamarin. Forms permite [ `ListView` ](xref:Xamarin.Forms.ListView) célula usar novamente por meio de [ `ListViewCachingStrategy` ](xref:Xamarin.Forms.ListViewCachingStrategy) enumeração, que tem os seguintes valores:

```csharp
public enum ListViewCachingStrategy
{
  RetainElement,   // the default value
  RecycleElement,
  RecycleElementAndDataTemplate
}
```

> [!NOTE]
> A Universal Windows Platform (UWP) ignora a [ `RetainElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement) estratégia, de cache porque ele sempre usa o cache para melhorar o desempenho. Portanto, por padrão se comporta como se o [ `RecycleElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) estratégia de cache é aplicada.

### <a name="retainelement"></a>RetainElement

O [ `RetainElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement) estratégia de cache Especifica que o [ `ListView` ](xref:Xamarin.Forms.ListView) gerará uma célula para cada item na lista, e é o padrão `ListView` comportamento. Geralmente, ele deve ser usado nas seguintes circunstâncias:

- Quando cada célula tem um grande número de associações (20-30 ou mais).
- Quando o modelo de célula é alterado com frequência.
- Quando testar revela que o `RecycleElement` armazenar em cache os resultados de estratégia em uma velocidade de execução reduzida.

É importante reconhecer as consequências do [ `RetainElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement) estratégia de cache ao trabalhar com células personalizadas. Qualquer código de inicialização de célula precisará executar para a criação de cada célula, que pode ser várias vezes por segundo. Nessa circunstância, técnicas de layout que eram um problemas em uma página, como uso de vários aninhados [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) instâncias, se tornar afunilamentos de desempenho quando elas são configuradas e destruída em tempo real conforme o usuário rolar.

<a name="recycleelement" />

### <a name="recycleelement"></a>RecycleElement

O [ `RecycleElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) estratégia de cache Especifica que o [ `ListView` ](xref:Xamarin.Forms.ListView) tenta minimizar sua velocidade de execução e o volume de memória por meio da reciclagem de células da lista. Esse modo não oferece sempre uma melhoria de desempenho e teste deve ser executado para determinar todos os aprimoramentos. No entanto, ele geralmente é a opção preferencial e deve ser usado nas seguintes circunstâncias:

- Quando cada célula tem um pequeno número moderado de associações.
- Quando cada célula [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) define todos os dados da célula.
- Quando cada célula é basicamente semelhante, com o modelo de célula inalterável.

Durante a virtualização, a célula terá seu contexto de associação atualizado e, portanto, se um aplicativo usa esse modo deve garantir que as atualizações de contexto de associação são manipuladas corretamente. Todos os dados sobre a célula devem ser provenientes do contexto de associação ou poderão ocorrer erros de consistência. Isso pode ser feito usando vinculação de dados para exibir dados da célula. Como alternativa, os dados da célula devem ser definidos no `OnBindingContextChanged` substituir, em vez de no construtor da célula personalizada, conforme demonstrado no exemplo de código a seguir:

```csharp
public class CustomCell : ViewCell
{
  Image image = null;

  public CustomCell ()
  {
    image = new Image();
    View = image;
  }

  protected override void OnBindingContextChanged ()
  {
    base.OnBindingContextChanged ();

    var item = BindingContext as ImageItem;
    if (item != null) {
      image.Source = item.ImageUrl;
    }
  }
}
```

Para obter mais informações, consulte [alterações de contexto de associação](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#binding-context-changes).

No iOS e Android, se células usam renderizadores personalizados, eles devem garantir que a notificação de alteração de propriedade é implementada corretamente. Quando as células são reutilizadas seus valores de propriedade serão alterado quando o contexto de associação é atualizado ao de uma célula disponível, com `PropertyChanged` eventos sendo gerados. Para obter mais informações, consulte [personalizar uma ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md).

#### <a name="recycleelement-with-a-datatemplateselector"></a>RecycleElement com um DataTemplateSelector

Quando um [ `ListView` ](xref:Xamarin.Forms.ListView) usa um [ `DataTemplateSelector` ](xref:Xamarin.Forms.DataTemplateSelector) para selecionar um [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate), a [ `RecycleElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) de cache estratégia não armazena em cache `DataTemplate`s. Em vez disso, um `DataTemplate` é selecionado para cada item de dados na lista.

> [!NOTE]
> O [ `RecycleElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) estratégia de cache tem um pré-requisito, introduzido no xamarin. Forms 2.4, que, quando um [ `DataTemplateSelector` ](xref:Xamarin.Forms.DataTemplateSelector) é solicitado a selecionar um [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)cada `DataTemplate` deve retornar o mesmo [ `ViewCell` ](xref:Xamarin.Forms.ViewCell) tipo. Por exemplo, dada uma [ `ListView` ](xref:Xamarin.Forms.ListView) com um `DataTemplateSelector` que pode retornar `MyDataTemplateA` (onde `MyDataTemplateA` retorna um `ViewCell` do tipo `MyViewCellA`), ou `MyDataTemplateB` (onde `MyDataTemplateB`retorna um `ViewCell` do tipo `MyViewCellB`), quando `MyDataTemplateA` será retornada a ele deve retornar `MyViewCellA` ou uma exceção será gerada.

### <a name="recycleelementanddatatemplate"></a>RecycleElementAndDataTemplate

O [ `RecycleElementAndDataTemplate` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElementAndDataTemplate) estratégia de cache baseia-se a [ `RecycleElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) estratégia de cache, além disso, garantindo que, quando um [ `ListView` ](xref:Xamarin.Forms.ListView) usa um [ `DataTemplateSelector` ](xref:Xamarin.Forms.DataTemplateSelector) para selecionar um [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate), `DataTemplate`s são armazenados em cache pelo tipo de item na lista. Portanto, `DataTemplate`s são selecionados de uma vez por tipo de item, em vez de uma vez por instância do item.

> [!NOTE]
> O [ `RecycleElementAndDataTemplate` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElementAndDataTemplate) estratégia de cache tem um pré-requisito que o `DataTemplate`s retornado pelo [ `DataTemplateSelector` ](xref:Xamarin.Forms.DataTemplateSelector) deve usar o [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate.%23ctor(System.Type)) construtor que usa um `Type`.

### <a name="setting-the-caching-strategy"></a>Definindo a estratégia de cache

O [ `ListViewCachingStrategy` ](xref:Xamarin.Forms.ListViewCachingStrategy) valor de enumeração é especificado com um [ `ListView` ](xref:Xamarin.Forms.ListView) sobrecarga de construtor, conforme mostrado no exemplo de código a seguir:

```csharp
var listView = new ListView(ListViewCachingStrategy.RecycleElement);
```

No XAML, defina o `CachingStrategy` atributo conforme mostrado no código a seguir:

```xaml
<ListView CachingStrategy="RecycleElement">
    <ListView.ItemTemplate>
        <DataTemplate>
            <ViewCell>
              ...
            </ViewCell>
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

Isso tem o mesmo efeito que definir o argumento de estratégia de cache do construtor no C#; Observe que não há nenhuma `CachingStrategy` propriedade `ListView`.

#### <a name="setting-the-caching-strategy-in-a-subclassed-listview"></a>Definir a estratégia de cache em uma subclasse ListView

Definindo o `CachingStrategy` atributo do XAML em uma subclasse [ `ListView` ](xref:Xamarin.Forms.ListView) não produzirá o comportamento desejado, porque não há nenhum `CachingStrategy` propriedade `ListView`. Além disso, se [XAMLC](~/xamarin-forms/xaml/xamlc.md) estiver habilitado, a seguinte mensagem de erro será produzida: **Nenhuma propriedade, propriedade vinculável ou evento encontrado para ' CachingStrategy '**

A solução para esse problema é para especificar um construtor na subclasse [ `ListView` ](xref:Xamarin.Forms.ListView) que aceita um [ `ListViewCachingStrategy` ](xref:Xamarin.Forms.ListViewCachingStrategy) parâmetro e o passa para a classe base:

```csharp
public class CustomListView : ListView
{
  public CustomListView (ListViewCachingStrategy strategy) : base (strategy)
  {
  }
  ...
}
```

Em seguida, a [ `ListViewCachingStrategy` ](xref:Xamarin.Forms.ListViewCachingStrategy) valor de enumeração pode ser especificado na XAML usando o `x:Arguments` sintaxe:

```xaml
<local:CustomListView>
  <x:Arguments>
    <ListViewCachingStrategy>RecycleElement</ListViewCachingStrategy>
  </x:Arguments>
</local:CustomListView>
```

<a name="improving-performance" />

## <a name="improving-listview-performance"></a>Melhorando o desempenho de ListView

Há muitas técnicas para melhorar o desempenho de um `ListView`:

-  Associar o `ItemsSource` propriedade para um `IList<T>` coleção em vez de um `IEnumerable<T>` coleção, pois `IEnumerable<T>` coleções não dão suporte a acesso aleatório.
-  Use as células internas (como `TextCell`  /  `SwitchCell` ), em vez de `ViewCell` sempre que você pode.
-  Use menos elementos. Por exemplo, considere usar um único `FormattedString` rótulo em vez de vários rótulos.
-  Substitua os `ListView` com um `TableView` ao exibir dados não homogêneos – ou seja, os dados de diferentes tipos.
-  Limite o uso de [ `Cell.ForceUpdateSize` ](xref:Xamarin.Forms.Cell.ForceUpdateSize) método. Se o uso excessivo, ele prejudicará o desempenho.
-  No Android, evite definir um `ListView`da visibilidade de separador de linha ou a cor, depois que ele foi instanciado, já que resulta em uma penalidade de desempenho grande.
-  Evite alterar o layout da célula com base nas [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext). Isso resulta em grandes custos de inicialização e de layout.
-  Evite hierarquias de layout profundamente aninhadas. Use `AbsoluteLayout` ou `Grid` para ajudar a reduzir o aninhamento.
-  Evite específico `LayoutOptions` diferente de `Fill` (preenchimento é o mais barato de computação).
-  Evite colocar uma `ListView` dentro de um `ScrollView` pelos seguintes motivos:
    - O `ListView` implementa seu próprio rolagem.
    - O `ListView` não receberão qualquer gestos, como eles serão manipulados pelo pai `ScrollView`.
    - O `ListView` pode apresentar um cabeçalho personalizado e um rodapé que rola com os elementos da lista, oferecendo potencialmente a funcionalidade que o `ScrollView` foi usado para. Para obter mais informações, consulte [cabeçalhos e rodapés](~/xamarin-forms/user-interface/listview/customizing-list-appearance.md#Headers_and_Footers).
-  Considere um renderizador personalizado se você precisar de um design muito específico e complexo, apresentado em suas células.

`AbsoluteLayout` tem o potencial para executar layouts sem uma chamada única medida. Isso torna muito poderoso para desempenho. Se `AbsoluteLayout` não pode ser usado, considere [ `RelativeLayout` ](xref:Xamarin.Forms.RelativeLayout). Se usando `RelativeLayout`, passar as restrições diretamente será consideravelmente mais rápido do que usar a API de expressão. Isso ocorre porque a API de expressão usa JIT e no iOS a árvore tem deve ser interpretado, que é mais lento. A expressão de API é adequada para layouts de página em que ele apenas necessário layout inicial e a rotação, mas em `ListView`, onde ele é executado constantemente durante a rolagem, ele reduz o desempenho.

Criar um renderizador personalizado para um [ `ListView` ](xref:Xamarin.Forms.ListView) ou suas células é uma abordagem para reduzir o efeito de cálculos de layout no desempenho de rolagem. Para obter mais informações, consulte [Personalizando um ListView](~/xamarin-forms/app-fundamentals/custom-renderer/listview.md) e [personalizar uma ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md).


## <a name="related-links"></a>Links relacionados

- [Exibição de renderizador personalizado (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistviewnative)
- [ViewCell de renderizador personalizado (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-viewcell)
- [ListViewCachingStrategy](xref:Xamarin.Forms.ListViewCachingStrategy)
