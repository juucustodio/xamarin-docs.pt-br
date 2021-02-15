---
title: Desempenho de ListView
description: Embora ListView seja uma exibição poderosa para a exibição de dados, ele tem algumas limitações. Este artigo explica como garantir um ótimo desempenho com um Xamarin.Forms ListView em um aplicativo.
ms.prod: xamarin
ms.assetid: 1B085639-652C-4862-86EB-5D55D32B9395
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/11/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 98f91ec614df08997e7cbf23530086f541500b5d
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93375467"
---
# <a name="listview-performance"></a>Desempenho de ListView

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/workingwithlistviewnative)

Ao escrever aplicativos móveis, o desempenho é importante. Os usuários passaram a esperar uma rolagem suave e tempos de carregamento rápido. A falha ao atender às expectativas dos seus usuários custará as classificações na loja de aplicativos ou, no caso de um aplicativo de linha de negócios, custará o tempo e o dinheiro da sua organização.

O Xamarin.Forms [`ListView`](xref:Xamarin.Forms.ListView) é uma exibição avançada para a exibição de dados, mas tem algumas limitações. O desempenho da rolagem pode ser afetado ao usar células personalizadas, especialmente quando elas contêm hierarquias de exibição profundamente aninhadas ou usam determinados layouts que exigem medição complexa. Felizmente, há técnicas que você pode usar para evitar o mau desempenho.

## <a name="caching-strategy"></a>Estratégia de cache

ListViews são frequentemente usados para exibir muito mais dados do que se ajustam à tela. Por exemplo, um aplicativo de música pode ter uma biblioteca de músicas com milhares de entradas. A criação de um item para cada entrada desperdiçaria uma memória valiosa e funcionaria inadequadamente. Criar e destruir linhas constantemente exigiria que o aplicativo criasse uma instância e limpe os objetos constantemente, o que também teria um desempenho ruim.

Para conservar a memória, os [`ListView`](xref:Xamarin.Forms.ListView) equivalentes nativos para cada plataforma têm recursos internos para reutilizar linhas. Somente as células visíveis na tela são carregadas na memória e o **conteúdo** é carregado em células existentes. Esse padrão impede que o aplicativo instancie milhares de objetos, economizando tempo e memória.

Xamarin.Forms permite a [`ListView`](xref:Xamarin.Forms.ListView) reutilização de célula por meio da [`ListViewCachingStrategy`](xref:Xamarin.Forms.ListViewCachingStrategy) enumeração, que tem os seguintes valores:

```csharp
public enum ListViewCachingStrategy
{
    RetainElement,   // the default value
    RecycleElement,
    RecycleElementAndDataTemplate
}
```

> [!NOTE]
> O Plataforma Universal do Windows (UWP) ignora a [`RetainElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement) estratégia de cache, pois ela sempre usa o Caching para melhorar o desempenho. Portanto, por padrão, ele se comporta como se a [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) estratégia de cache fosse aplicada.

### <a name="retainelement"></a>Retain

A [`RetainElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement) estratégia de cache especifica que o [`ListView`](xref:Xamarin.Forms.ListView) irá gerar uma célula para cada item na lista e é o comportamento padrão `ListView` . Ele deve ser usado nas seguintes circunstâncias:

- Cada célula tem um grande número de associações (20-30 +).
- O modelo de célula é alterado com frequência.
- O teste revela que a `RecycleElement` estratégia de cache resulta em uma velocidade de execução reduzida.

É importante reconhecer as consequências da [`RetainElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement) estratégia de cache ao trabalhar com células personalizadas. Qualquer código de inicialização de célula precisará ser executado para cada criação de célula, que pode ser várias vezes por segundo. Nessa circunstância, as técnicas de layout que eram bem em uma página, como o uso de várias instâncias aninhadas [`StackLayout`](xref:Xamarin.Forms.StackLayout) , se tornam gargalos de desempenho quando são configuradas e destruídas em tempo real à medida que o usuário rola.

### <a name="recycleelement"></a>Reciclar

A [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) estratégia de cache especifica que o [`ListView`](xref:Xamarin.Forms.ListView) tentará minimizar seu volume de memória e velocidade de execução por meio da reciclagem de células da lista. Esse modo nem sempre oferece uma melhoria no desempenho, e os testes devem ser realizados para determinar qualquer melhoria. No entanto, é a escolha preferida e deve ser usada nas seguintes circunstâncias:

- Cada célula tem um número pequeno a moderado de associações.
- Cada célula [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) define todos os dados da célula.
- Cada célula é basicamente semelhante, com o modelo de célula inalterado.

Durante a virtualização, a célula terá seu contexto de associação atualizado e, portanto, se um aplicativo usar esse modo, ele deverá garantir que as atualizações de contexto de associação sejam tratadas adequadamente. Todos os dados sobre a célula devem vir do contexto de associação ou erros de consistência podem ocorrer. Esse problema pode ser evitado usando a vinculação de dados para exibir dados de célula. Como alternativa, os dados da célula devem ser definidos na `OnBindingContextChanged` substituição, em vez de no construtor da célula Personalizada, conforme demonstrado no exemplo de código a seguir:

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

No iOS e no Android, se as células usarem renderizadores personalizados, eles deverão garantir que a notificação de alteração de propriedade seja implementada corretamente. Quando as células forem reutilizadas, seus valores de propriedade serão alterados quando o contexto de associação for atualizado para o de uma célula disponível, com `PropertyChanged` eventos sendo gerados. Para obter mais informações, consulte [Personalizando um ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md).

#### <a name="recycleelement-with-a-datatemplateselector"></a>Reciclar com um DataTemplateSelector

Quando um [`ListView`](xref:Xamarin.Forms.ListView) usa um [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) para selecionar um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) , a [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) estratégia de cache não armazena em cache os `DataTemplate` s. Em vez disso, um `DataTemplate` é selecionado para cada item de dados na lista.

> [!NOTE]
> A [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) estratégia de cache tem um pré-requisito, introduzido em Xamarin.Forms 2,4, que quando um [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) é solicitado a selecionar um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) que cada um `DataTemplate` deve retornar ao mesmo [`ViewCell`](xref:Xamarin.Forms.ViewCell) tipo. Por exemplo, considerando um [`ListView`](xref:Xamarin.Forms.ListView) com um `DataTemplateSelector` que pode retornar `MyDataTemplateA` (em que `MyDataTemplateA` retorna um `ViewCell` do tipo `MyViewCellA` ) ou `MyDataTemplateB` (onde `MyDataTemplateB` retorna um `ViewCell` do tipo `MyViewCellB` ), quando `MyDataTemplateA` é retornado, ele deve retornar `MyViewCellA` ou uma exceção será gerada.

### <a name="recycleelementanddatatemplate"></a>RecycleElementAndDataTemplate

A [`RecycleElementAndDataTemplate`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElementAndDataTemplate) estratégia de cache baseia- [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) se na estratégia de cache, garantindo, além disso, que quando um [`ListView`](xref:Xamarin.Forms.ListView) usa um [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) para selecionar um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) , `DataTemplate` os s são armazenados em cache pelo tipo de item na lista. Portanto, os `DataTemplate` s são selecionados uma vez por tipo de item, em vez de uma instância por item.

> [!NOTE]
> A [`RecycleElementAndDataTemplate`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElementAndDataTemplate) estratégia de cache tem um pré-requisito que os `DataTemplate` s retornados pelo [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) devem usar o [`DataTemplate`](xref:Xamarin.Forms.DataTemplate.%23ctor(System.Type)) Construtor que usa um `Type` .

### <a name="set-the-caching-strategy"></a>Definir a estratégia de cache

O [`ListViewCachingStrategy`](xref:Xamarin.Forms.ListViewCachingStrategy) valor de enumeração é especificado com uma [`ListView`](xref:Xamarin.Forms.ListView) sobrecarga de construtor, conforme mostrado no exemplo de código a seguir:

```csharp
var listView = new ListView(ListViewCachingStrategy.RecycleElement);
```

Em XAML, defina o `CachingStrategy` atributo conforme mostrado no XAML abaixo:

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

Esse método tem o mesmo efeito que definir o argumento de estratégia de cache no Construtor em C#.

#### <a name="set-the-caching-strategy-in-a-subclassed-listview"></a>Definir a estratégia de cache em um ListView em uma subclasse

Definir o `CachingStrategy` atributo do XAML em uma subclasse [`ListView`](xref:Xamarin.Forms.ListView) não produzirá o comportamento desejado, pois não há nenhuma `CachingStrategy` propriedade em `ListView` . Além disso, se [XAMLC](~/xamarin-forms/xaml/xamlc.md) estiver habilitado, a seguinte mensagem de erro será produzida: **nenhuma propriedade, propriedade vinculável ou evento encontrado para ' CachingStrategy '**

A solução para esse problema é especificar um construtor na subclasse [`ListView`](xref:Xamarin.Forms.ListView) que aceita um [`ListViewCachingStrategy`](xref:Xamarin.Forms.ListViewCachingStrategy) parâmetro e o transmite para a classe base:

```csharp
public class CustomListView : ListView
{
    public CustomListView (ListViewCachingStrategy strategy) : base (strategy)
    {
    }
    ...
}
```

Em seguida, o [`ListViewCachingStrategy`](xref:Xamarin.Forms.ListViewCachingStrategy) valor de enumeração pode ser especificado a partir de XAML usando a `x:Arguments` sintaxe:

```xaml
<local:CustomListView>
    <x:Arguments>
        <ListViewCachingStrategy>RecycleElement</ListViewCachingStrategy>
    </x:Arguments>
</local:CustomListView>
```

## <a name="listview-performance-suggestions"></a>Sugestões de desempenho de ListView

Há muitas técnicas para melhorar o desempenho de um `ListView` . As sugestões a seguir podem melhorar o desempenho do seu ListView

- Associe a `ItemsSource` Propriedade a uma `IList<T>` coleção em vez de uma `IEnumerable<T>` coleção, porque as `IEnumerable<T>` coleções não dão suporte ao acesso aleatório.
- Use as células internas (como `TextCell`  /  `SwitchCell` ) em vez de `ViewCell` sempre que possível.
- Use menos elementos. Por exemplo, considere usar um único `FormattedString` rótulo em vez de vários rótulos.
- Substitua `ListView` por um `TableView` ao exibir dados não homogêneos – ou seja, dados de tipos diferentes.
- Limite o uso do [`Cell.ForceUpdateSize`](xref:Xamarin.Forms.Cell.ForceUpdateSize) método. Se for usado, ele diminuirá o desempenho.
- No Android, evite configurar a `ListView` visibilidade ou a cor de um separador de linhas após ter sido instanciada, pois resulta em uma grande penalidade de desempenho.
- Evite alterar o layout de célula com base no [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) . A alteração do layout gera custos grandes de medição e inicialização.
- Evite hierarquias de layout profundamente aninhadas. Use  `AbsoluteLayout` ou  `Grid` para ajudar a reduzir o aninhamento.
- Evite outros específicos que não sejam `LayoutOptions`  `Fill` ( `Fill` o mais barato de computar).
- Evite colocar um `ListView` por dentro de um pelos `ScrollView` seguintes motivos:
  - O `ListView` implementa sua própria rolagem.
  - O `ListView` não receberá nenhum gesto, pois eles serão manipulados pelo pai `ScrollView` .
  - O `ListView` pode apresentar um cabeçalho e um rodapé personalizados que rolam com os elementos da lista, potencialmente oferecendo a funcionalidade `ScrollView` usada para o. Para obter mais informações, consulte [cabeçalhos e rodapés](~/xamarin-forms/user-interface/listview/customizing-list-appearance.md#headers-and-footers).
- Considere um renderizador personalizado se você precisar de um design específico e complexo apresentado em suas células.

`AbsoluteLayout` tem o potencial de executar layouts sem uma única chamada de medida, tornando-o altamente funcional. Se `AbsoluteLayout` não puder ser usado, considere [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) . Se estiver usando `RelativeLayout` , passar restrições diretamente será consideravelmente mais rápido do que usar a API de expressão. Esse método é mais rápido porque a API de expressão usa JIT e no iOS a árvore precisa ser interpretada, o que é mais lento. A API de expressão é adequada para layouts de página, onde ele só é necessário no layout inicial e na rotação, mas no `ListView` , onde ele é executado constantemente durante a rolagem, ele afeta o desempenho.

Criar um renderizador personalizado para uma [`ListView`](xref:Xamarin.Forms.ListView) ou suas células é uma abordagem para reduzir o efeito de cálculos de layout no desempenho de rolagem. Para obter mais informações, consulte [Personalizando um ListView](~/xamarin-forms/app-fundamentals/custom-renderer/listview.md) e [Personalizando um ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md).

## <a name="related-links"></a>Links relacionados

- [Exibição do renderizador personalizado (exemplo)](/samples/xamarin/xamarin-forms-samples/workingwithlistviewnative)
- [ViewCell de processador personalizado (exemplo)](/samples/xamarin/xamarin-forms-samples/customrenderers-viewcell)
- [ListViewCachingStrategy](xref:Xamarin.Forms.ListViewCachingStrategy)