---
title: Desempenho de ListView
description: Certifique-se de excelente desempenho com seu aplicativo com base em ListView.
ms.topic: article
ms.prod: xamarin
ms.assetid: 1B085639-652C-4862-86EB-5D55D32B9395
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/11/2017
ms.openlocfilehash: 2acaef5fd42b867e88fb9b81d401ea752480124a
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2018
---
# <a name="listview-performance"></a>Desempenho de ListView

Ao escrever aplicativos móveis, o desempenho é importante. Os usuários já conhecem rolagem suave e tempos de carregamento rápido. Falha atender às expectativas dos usuários custará classificações no armazenamento do aplicativo ou no caso de um aplicativo de linha de negócios, custo organização tempo e dinheiro.

Embora [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) é um modo eficiente para exibir dados, ele tem algumas limitações. Rolar o desempenho poderá ser afetado ao usar células personalizadas, especialmente quando eles contenham hierarquias da exibição profundamente aninhadas ou usam determinados layouts que exigem muita medida. Felizmente, existem técnicas que você pode usar para evitar um baixo desempenho.

Os seguintes tópicos são abordados neste artigo:

- **[Estratégia de cache](#cachingstrategy)**
- **[Melhorando o desempenho de ListView](#improving-performance)**

<a name="cachingstrategy" />

## <a name="caching-strategy"></a>Estratégia de cache

ListViews geralmente são usados para exibir muito mais dados do que pode caber na tela. Considere um aplicativo de música, por exemplo. Uma biblioteca de músicas pode ter milhares de entradas. A abordagem simple, o que seria criar uma linha para cada música, teria um baixo desempenho. Essa abordagem desperdiça memória e pode reduzir a rolagem para um rastreamento. Outra abordagem é criar e destruir linhas como rolados para a exibição de dados. Isso exige a instanciação constante e limpeza de objetos de exibição, que pode ser muito lento.

Para conservar memória, nativa [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) equivalentes para cada plataforma tem recursos internos para reutilizar em linhas. Apenas as células visíveis na tela são carregadas na memória e o **conteúdo** é carregado em células existentes. Isso evita a necessidade de criar uma instância de milhares de objetos, economizando tempo e memória do aplicativo.

Xamarin. Forms permite [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) célula usar novamente por meio de [ `ListViewCachingStrategy` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListViewCachingStrategy/) enumeração, que tem os seguintes valores:

```csharp
public enum ListViewCachingStrategy
{
  RetainElement,   // the default value
  RecycleElement,
  RecycleElementAndDataTemplate
}
```

> [!NOTE]
> **Observação**: O Windows UWP (plataforma Universal) ignora o [ `RetainElement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RetainElement/) cache estratégia, pois ele sempre usa o cache para melhorar o desempenho. Portanto, por padrão ele se comporta como se o [ `RecycleElement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RecycleElement/) estratégia de cache é aplicada.

### <a name="retainelement"></a>RetainElement

O [ `RetainElement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RetainElement/) estratégia de cache Especifica que o [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) irá gerar uma célula para cada item na lista, e é o padrão `ListView` comportamento. Geralmente deve ser usada nas seguintes circunstâncias:

- Quando cada célula tem um grande número de associações (20-30 +).
- Quando o modelo de célula alterada com frequência.
- Quando testar revela que o `RecycleElement` armazenar em cache os resultados de estratégia em uma velocidade de execução reduzida.

É importante reconhecer as consequências do [ `RetainElement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RetainElement/) estratégia de cache ao trabalhar com células personalizadas. Qualquer código de inicialização de célula precisará executar para a criação de cada célula, que pode ser várias vezes por segundo. Nesse caso, as técnicas de layout que foram bem em uma página, como usando vários aninhados [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) instâncias, se tornar afunilamentos de desempenho quando são instalação e destruídos em tempo real como o usuário rolar.

<a name="recycleelement" />

### <a name="recycleelement"></a>RecycleElement

O [ `RecycleElement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RecycleElement/) estratégia de cache Especifica que o [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) tentará reduzir sua velocidade de volume e execução de memória por reciclagem células da lista. Esse modo não oferece sempre uma melhoria de desempenho e teste deve ser executada para determinar todos os aprimoramentos. No entanto, ele geralmente é a opção preferida e deve ser usado nas seguintes circunstâncias:

- Quando cada célula tem um pequeno número moderado de associações.
- Quando cada célula [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) define todos os dados da célula.
- Quando cada célula é muito semelhante, com o modelo de célula inalterado.

Durante a virtualização, a célula terá seu contexto de associação atualizado e então se um aplicativo usa esse modo deve garantir que atualizações do contexto de associação são tratadas corretamente. Todos os dados sobre a célula devem vir do contexto de associação ou poderão ocorrer erros de consistência. Isso pode ser feito por meio de associação de dados para exibir dados da célula. Como alternativa, os dados da célula devem ser definidos `OnBindingContextChanged` substituir, em vez de no construtor da célula personalizado, como demonstrado no exemplo de código a seguir:

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

No iOS e Android, se as células usam processadores personalizados, eles devem garantir que notificação de alteração de propriedade é implementada corretamente. Quando as células são reutilizadas seus valores de propriedade serão alterado quando o contexto de associação é atualizado para que uma célula disponível, com `PropertyChanged` eventos que está sendo gerados. Para obter mais informações, consulte [Personalizando uma ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md).

#### <a name="recycleelement-with-a-datatemplateselector"></a>RecycleElement com um DataTemplateSelector

Quando um [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) usa um [ `DataTemplateSelector` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplateSelector/) para selecionar um [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/), o [ `RecycleElement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RecycleElement/) cache estratégia de cache não `DataTemplate`s. Em vez disso, um `DataTemplate` é selecionado para cada item de dados na lista.

> [!NOTE]
> **Observação**: O [ `RecycleElement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RecycleElement/) estratégia de cache tem um pré-requisito, introduzido no xamarin. Forms 2.4, que, quando um [ `DataTemplateSelector` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplateSelector/) é solicitado a selecionar um [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) cada `DataTemplate` deve retornar o mesmo [ `ViewCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/) tipo. Por exemplo, dada uma [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) com um `DataTemplateSelector` que pode retornar `MyDataTemplateA` (onde `MyDataTemplateA` retorna um `ViewCell` do tipo `MyViewCellA`), ou `MyDataTemplateB` (onde `MyDataTemplateB`retorna um `ViewCell` do tipo `MyViewCellB`), quando `MyDataTemplateA` é retornado deve retornar `MyViewCellA` ou uma exceção será lançada.

### <a name="recycleelementanddatatemplate"></a>RecycleElementAndDataTemplate

O [ `RecycleElementAndDataTemplate` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RecycleElementAndDataTemplate/) estratégia de cache se baseia o [ `RecycleElement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RecycleElement/) estratégia de cache, além disso, garantindo que, quando um [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) usa um [ `DataTemplateSelector` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplateSelector/) para selecionar um [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/), `DataTemplate`s são armazenados em cache pelo tipo de item na lista. Portanto, `DataTemplate`s serão selecionadas uma vez por tipo de item, em vez de uma vez por instância do item.

> [!NOTE]
> **Observação**: O [ `RecycleElementAndDataTemplate` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RecycleElementAndDataTemplate/) estratégia de cache tem um pré-requisito que o `DataTemplate`s retornado pelo [ `DataTemplateSelector` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplateSelector/) devem usar o [ `DataTemplate` ](https://developer.xamarin.com/api/constructor/Xamarin.Forms.DataTemplate.DataTemplate/p/System.Type/) construtor que usa um `Type`.

### <a name="setting-the-caching-strategy"></a>Definir a estratégia de cache

O [ `ListViewCachingStrategy` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListViewCachingStrategy/) valor de enumeração é especificado com um [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) sobrecarga de construtor, conforme mostrado no exemplo de código a seguir:

```csharp
var listView = new ListView(ListViewCachingStrategy.RecycleElement);
```

Em XAML, definir o `CachingStrategy` atributo conforme mostrado no código a seguir:

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

Isso tem o mesmo efeito que definir o argumento de estratégia de cache do construtor no c#; Observe que não há nenhum `CachingStrategy` propriedade `ListView`.

#### <a name="setting-the-caching-strategy-in-a-subclassed-listview"></a>Definir a estratégia de cache em uma subclasse ListView

Definindo o `CachingStrategy` atributo do XAML em uma subclasse [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) não produzirá o comportamento desejado, porque não há nenhum `CachingStrategy` propriedade `ListView`. Além disso, se [XAMLC](~/xamarin-forms/xaml/xamlc.md) é habilitada, a seguinte mensagem de erro será produzida: **nenhuma propriedade, a propriedade associável ou o evento encontrado para 'CachingStrategy'**

A solução para esse problema é para especificar um construtor na subclasse [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) que aceita um [ `ListViewCachingStrategy` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListViewCachingStrategy/) parâmetro e o transmite para a classe base:

```csharp
public class CustomListView : ListView
{
  public CustomListView (ListViewCachingStrategy strategy) : base (strategy)
  {
  }
  ...
}
```

Em seguida, o [ `ListViewCachingStrategy` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListViewCachingStrategy/) o valor de enumeração pode ser especificado do XAML usando o `x:Arguments` sintaxe:

```xaml
<local:CustomListView>
  <x:Arguments>
    <ListViewCachingStrategy>RecycleElement</ListViewCachingStrategy>
  </x:Arguments>
</local:CustomListView>
```

<a name="improving-performance" />

## <a name="improving-listview-performance"></a>Melhorando o desempenho de ListView

Há várias técnicas para melhorar o desempenho de um `ListView`:

-  Associar o `ItemsSource` propriedade para um `IList<T>` coleção em vez de um `IEnumerable<T>` coleção porque `IEnumerable<T>` coleções não oferecem suporte a acesso aleatório.
-  Use as células internas (como `TextCell`  /  `SwitchCell` ) em vez de `ViewCell` sempre que você pode.
-  Use menos elementos. Por exemplo, considere usar um único `FormattedString` rótulo em vez de vários rótulos.
-  Substitua o `ListView` com um `TableView` ao exibir dados não homogêneos – ou seja, os dados de diferentes tipos.
-  Limite o uso de [ `Cell.ForceUpdateSize` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Cell.ForceUpdateSize()/) método. Se o uso excessivo, ele prejudicará o desempenho.
-  No Android, evite definir um `ListView`da visibilidade do separador de linha ou a cor, depois de instanciado, já que resulta em uma penalidade de desempenho grande.
-  Evite alterar o layout de célula com base no [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/). Isso resulta em grandes custos de layout e de inicialização.
-  Evite hierarquias de layout profundamente aninhadas. Use `AbsoluteLayout` ou `Grid` para ajudar a reduzir o aninhamento.
-  Evite específico `LayoutOptions` que `Fill` (preenchimento é cheapest de computação).
-  Evite colocar um `ListView` dentro de um `ScrollView` pelos seguintes motivos:
  - O `ListView` implementa seu próprio rolagem.
  - O `ListView` não receberão qualquer gestos, como eles serão manipulados pelo pai `ScrollView`.
  - O `ListView` pode apresentar um cabeçalho personalizado e um rodapé que percorre os elementos da lista, potencialmente oferece a funcionalidade que o `ScrollView` foi usado. Para obter mais informações, consulte [cabeçalhos e rodapés](~/xamarin-forms/user-interface/listview/customizing-list-appearance.md#Headers_and_Footers).
-  Considere um renderizador personalizado se você precisar de um design muito específico, complexo apresentado nas células.

`AbsoluteLayout` tem o potencial de executar layouts sem uma chamada única medida. Isso facilita muito poderoso para desempenho. Se `AbsoluteLayout` não pode ser usado, considere [ `RelativeLayout` ](http://developer.xamarin.com/api/type/Xamarin.Forms.RelativeLayout/). Se usar `RelativeLayout`, passar restrições diretamente será consideravelmente mais rápido do que usar a API de expressão. Isso ocorre porque a API de expressão usa JIT e no iOS árvore tem deve ser interpretado, que é mais lento. A expressão de API é adequada para layouts de página onde ele necessário apenas para layout inicial e a rotação, mas em `ListView`, onde ela é executada constantemente durante a rolagem, ele reduz o desempenho.

Criando um renderizador personalizado para um [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) ou suas células é uma abordagem para reduzir o efeito de cálculos de layout no desempenho de rolagem. Para obter mais informações, consulte [Personalizando uma ListView](~/xamarin-forms/app-fundamentals/custom-renderer/listview.md) e [Personalizando uma ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md).


## <a name="related-links"></a>Links relacionados

- [Exibição personalizada do processador (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithListviewNative/)
- [ViewCell de renderizador personalizado (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/viewcell/)
- [ListViewCachingStrategy](https://developer.xamarin.com/api/type/Xamarin.Forms.ListViewCachingStrategy/)
