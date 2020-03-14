---
title: Desempenho de ListView
description: Embora o ListView é um modo de exibição avançado para exibir os dados, ele tem algumas limitações. Este artigo explica como garantir um ótimo desempenho com um ListView do xamarin. Forms em um aplicativo.
ms.prod: xamarin
ms.assetid: 1B085639-652C-4862-86EB-5D55D32B9395
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/11/2017
ms.openlocfilehash: ed920db129d2af203046a648c0069580f99a295e
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79305335"
---
# <a name="listview-performance"></a>Desempenho de ListView

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistviewnative)

Ao escrever aplicativos móveis, o desempenho é importante. Os usuários já conhecem rolagem suave e tempos de carregamento rápido. Com falha atender às expectativas dos usuários custam avaliações na loja de aplicativos ou no caso de um aplicativo de linha de negócios, custo organização tempo e dinheiro.

A [`ListView`](xref:Xamarin.Forms.ListView) do Xamarin. Forms é uma exibição poderosa para a exibição de dados, mas tem algumas limitações. O desempenho da rolagem pode ser afetado ao usar células personalizadas, especialmente quando elas contêm hierarquias de exibição profundamente aninhadas ou usam determinados layouts que exigem medição complexa. Felizmente, existem técnicas que você pode usar para evitar um desempenho ruim.

## <a name="caching-strategy"></a>Estratégia de cache

ListViews são frequentemente usados para exibir muito mais dados do que se ajustam à tela. Por exemplo, um aplicativo de música pode ter uma biblioteca de músicas com milhares de entradas. A criação de um item para cada entrada desperdiçaria uma memória valiosa e funcionaria inadequadamente. Criar e destruir linhas constantemente exigiria que o aplicativo criasse uma instância e limpe os objetos constantemente, o que também teria um desempenho ruim.

Para conservar a memória, os equivalentes [`ListView`](xref:Xamarin.Forms.ListView) nativos para cada plataforma têm recursos internos para reutilizar linhas. Somente as células visíveis na tela são carregadas na memória e o **conteúdo** é carregado em células existentes. Esse padrão impede que o aplicativo instancie milhares de objetos, economizando tempo e memória.

O Xamarin. Forms permite a reutilização de célula [`ListView`](xref:Xamarin.Forms.ListView) por meio da enumeração [`ListViewCachingStrategy`](xref:Xamarin.Forms.ListViewCachingStrategy) , que tem os seguintes valores:

```csharp
public enum ListViewCachingStrategy
{
    RetainElement,   // the default value
    RecycleElement,
    RecycleElementAndDataTemplate
}
```

> [!NOTE]
> O Plataforma Universal do Windows (UWP) ignora a estratégia de cache de [`RetainElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement) , pois ela sempre usa o Caching para melhorar o desempenho. Portanto, por padrão, ele se comporta como se a estratégia de cache de [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) for aplicada.

### <a name="retainelement"></a>RetainElement

A estratégia de cache [`RetainElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement) especifica que o [`ListView`](xref:Xamarin.Forms.ListView) irá gerar uma célula para cada item na lista e é o comportamento de `ListView` padrão. Ele deve ser usado nas seguintes circunstâncias:

- Cada célula tem um grande número de associações (20-30 +).
- O modelo de célula é alterado com frequência.
- O teste revela que a estratégia de cache `RecycleElement` resulta em uma velocidade de execução reduzida.

É importante reconhecer as consequências da estratégia de cache [`RetainElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement) ao trabalhar com células personalizadas. Qualquer código de inicialização de célula precisará executar para a criação de cada célula, que pode ser várias vezes por segundo. Nessa circunstância, as técnicas de layout que eram bem em uma página, como o uso de várias instâncias de [`StackLayout`](xref:Xamarin.Forms.StackLayout) aninhadas, se tornam gargalos de desempenho quando são configuradas e destruídas em tempo real à medida que o usuário rola.

### <a name="recycleelement"></a>RecycleElement

A estratégia de cache [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) especifica que o [`ListView`](xref:Xamarin.Forms.ListView) tentará minimizar seu volume de memória e velocidade de execução por meio da reciclagem de células da lista. Esse modo nem sempre oferece uma melhoria no desempenho, e os testes devem ser realizados para determinar qualquer melhoria. No entanto, é a escolha preferida e deve ser usada nas seguintes circunstâncias:

- Cada célula tem um número pequeno a moderado de associações.
- A [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de cada célula define todos os dados da célula.
- Cada célula é basicamente semelhante, com o modelo de célula inalterado.

Durante a virtualização, a célula terá seu contexto de associação atualizado e, portanto, se um aplicativo usa esse modo deve garantir que as atualizações de contexto de associação são manipuladas corretamente. Todos os dados sobre a célula devem ser provenientes do contexto de associação ou poderão ocorrer erros de consistência. Esse problema pode ser evitado usando a vinculação de dados para exibir dados de célula. Como alternativa, os dados da célula devem ser definidos na substituição de `OnBindingContextChanged`, em vez de no construtor da célula Personalizada, conforme demonstrado no exemplo de código a seguir:

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

Para obter mais informações, consulte [vinculação de alterações de contexto](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#binding-context-changes).

No iOS e Android, se células usam renderizadores personalizados, eles devem garantir que a notificação de alteração de propriedade é implementada corretamente. Quando as células forem reutilizadas, seus valores de propriedade serão alterados quando o contexto de associação for atualizado para o de uma célula disponível, com `PropertyChanged` eventos sendo gerados. Para obter mais informações, consulte [Personalizando um ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md).

#### <a name="recycleelement-with-a-datatemplateselector"></a>RecycleElement com um DataTemplateSelector

Quando um [`ListView`](xref:Xamarin.Forms.ListView) usa uma [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) para selecionar uma [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), a estratégia de cache [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) não armazena em cache `DataTemplate`s. Em vez disso, um `DataTemplate` é selecionado para cada item de dados na lista.

> [!NOTE]
> A estratégia de cache [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) tem um pré-requisito, introduzido no Xamarin. Forms 2,4, que quando um [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) é solicitado a selecionar uma [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) que cada `DataTemplate` deve retornar o mesmo tipo de [`ViewCell`](xref:Xamarin.Forms.ViewCell) . Por exemplo, considerando um [`ListView`](xref:Xamarin.Forms.ListView) com um `DataTemplateSelector` que pode retornar `MyDataTemplateA` (onde `MyDataTemplateA` retorna um `ViewCell` do tipo `MyViewCellA`) ou `MyDataTemplateB` (em que `MyDataTemplateB` retorna um `ViewCell` do tipo `MyViewCellB`), quando `MyDataTemplateA` é retornado, ele deve retornar `MyViewCellA` ou uma exceção será gerada.

### <a name="recycleelementanddatatemplate"></a>RecycleElementAndDataTemplate

A estratégia de cache de [`RecycleElementAndDataTemplate`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElementAndDataTemplate) baseia-se na estratégia de cache de [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) , garantindo, além disso, que quando uma [`ListView`](xref:Xamarin.Forms.ListView) usa um [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) para selecionar uma [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), `DataTemplate`s são armazenadas em cache pelo tipo de item na lista. Portanto, `DataTemplate`s são selecionados uma vez por tipo de item, em vez de uma instância por item.

> [!NOTE]
> A estratégia de cache de [`RecycleElementAndDataTemplate`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElementAndDataTemplate) tem um pré-requisito que os `DataTemplate`retornados pelo [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) devem usar o construtor [`DataTemplate`](xref:Xamarin.Forms.DataTemplate.%23ctor(System.Type)) que usa um `Type`.

### <a name="set-the-caching-strategy"></a>Definir a estratégia de cache

O valor de enumeração de [`ListViewCachingStrategy`](xref:Xamarin.Forms.ListViewCachingStrategy) é especificado com uma sobrecarga de construtor de [`ListView`](xref:Xamarin.Forms.ListView) , conforme mostrado no exemplo de código a seguir:

```csharp
var listView = new ListView(ListViewCachingStrategy.RecycleElement);
```

Em XAML, defina o atributo `CachingStrategy` conforme mostrado no XAML abaixo:

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

Esse método tem o mesmo efeito que definir o argumento de estratégia de cache no construtor C#no.

#### <a name="set-the-caching-strategy-in-a-subclassed-listview"></a>Definir a estratégia de cache em um ListView em uma subclasse

Definir o atributo `CachingStrategy` do XAML em um [`ListView`](xref:Xamarin.Forms.ListView) de subclasse não produzirá o comportamento desejado, pois não há nenhuma propriedade `CachingStrategy` no `ListView`. Além disso, se [XAMLC](~/xamarin-forms/xaml/xamlc.md) estiver habilitado, a seguinte mensagem de erro será produzida: **nenhuma propriedade, propriedade vinculável ou evento encontrado para ' CachingStrategy '**

A solução para esse problema é especificar um construtor no [`ListView`](xref:Xamarin.Forms.ListView) de subclasse que aceita um parâmetro [`ListViewCachingStrategy`](xref:Xamarin.Forms.ListViewCachingStrategy) e o transmite para a classe base:

```csharp
public class CustomListView : ListView
{
    public CustomListView (ListViewCachingStrategy strategy) : base (strategy)
    {
    }
    ...
}
```

Em seguida, o valor de enumeração [`ListViewCachingStrategy`](xref:Xamarin.Forms.ListViewCachingStrategy) pode ser especificado a partir de XAML usando a sintaxe `x:Arguments`:

```xaml
<local:CustomListView>
    <x:Arguments>
        <ListViewCachingStrategy>RecycleElement</ListViewCachingStrategy>
    </x:Arguments>
</local:CustomListView>
```

## <a name="listview-performance-suggestions"></a>Sugestões de desempenho de ListView

Há muitas técnicas para melhorar o desempenho de um `ListView`. As sugestões a seguir podem melhorar o desempenho do seu ListView

- Associe a propriedade `ItemsSource` a uma coleção de `IList<T>` em vez de uma coleção de `IEnumerable<T>`, porque as coleções de `IEnumerable<T>` não dão suporte ao acesso aleatório.
- Use as células internas (como `TextCell` / `SwitchCell`) em vez de `ViewCell` sempre que possível.
- Use menos elementos. Por exemplo, considere usar um único rótulo de `FormattedString` em vez de vários rótulos.
- Substitua o `ListView` por uma `TableView` ao exibir dados não homogêneos – ou seja, dados de tipos diferentes.
- Limite o uso do método [`Cell.ForceUpdateSize`](xref:Xamarin.Forms.Cell.ForceUpdateSize) . Se o uso excessivo, ele prejudicará o desempenho.
- No Android, evite definir a visibilidade ou a cor do separador de linha de um `ListView`depois de ter sido instanciada, pois ela resulta em uma grande penalidade de desempenho.
- Evite alterar o layout de célula com base no [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext). A alteração do layout gera custos grandes de medição e inicialização.
- Evite hierarquias de layout profundamente aninhadas. Use `AbsoluteLayout` ou `Grid` para ajudar a reduzir o aninhamento.
- Evite `LayoutOptions` específicos diferentes de `Fill` (`Fill` é o mais barato de calcular).
- Evite colocar um `ListView` dentro de um `ScrollView` pelos seguintes motivos:
  - O `ListView` implementa sua própria rolagem.
  - O `ListView` não receberá nenhum gesto, pois eles serão manipulados pelo `ScrollView`pai.
  - O `ListView` pode apresentar um cabeçalho e um rodapé personalizados que rolam com os elementos da lista, potencialmente oferecendo a funcionalidade para a qual o `ScrollView` foi usado. Para obter mais informações, consulte [cabeçalhos e rodapés](~/xamarin-forms/user-interface/listview/customizing-list-appearance.md#headers-and-footers).
- Considere um renderizador personalizado se você precisar de um design específico e complexo apresentado em suas células.

`AbsoluteLayout` tem o potencial de executar layouts sem uma única chamada de medida, tornando-o altamente funcional. Se `AbsoluteLayout` não puder ser usado, considere [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout). Se estiver usando `RelativeLayout`, passar restrições diretamente será consideravelmente mais rápido do que usar a API de expressão. Esse método é mais rápido porque a API de expressão usa JIT e no iOS a árvore precisa ser interpretada, o que é mais lento. A API de expressão é adequada para layouts de página, onde ele só é necessário no layout inicial e na rotação, mas em `ListView`, onde é executado constantemente durante a rolagem, ele afeta o desempenho.

Criar um renderizador personalizado para uma [`ListView`](xref:Xamarin.Forms.ListView) ou suas células é uma abordagem para reduzir o efeito de cálculos de layout no desempenho de rolagem. Para obter mais informações, consulte [Personalizando um ListView](~/xamarin-forms/app-fundamentals/custom-renderer/listview.md) e [Personalizando um ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md).

## <a name="related-links"></a>Links relacionados

- [Exibição do renderizador personalizado (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistviewnative)
- [ViewCell de processador personalizado (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-viewcell)
- [ListViewCachingStrategy](xref:Xamarin.Forms.ListViewCachingStrategy)
