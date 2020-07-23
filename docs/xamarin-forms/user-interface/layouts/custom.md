---
title: Criar um layout personalizado emXamarin.Forms
description: Este artigo explica como escrever uma classe de layout personalizada e demonstra uma classe WrapLayout sensível à orientação que organiza seus filhos horizontalmente na página e, em seguida, encapsula a exibição de filhos subsequentes em linhas adicionais.
ms.prod: xamarin
ms.assetid: B0CFDB59-14E5-49E9-965A-3DCCEDAC2E31
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/29/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b3063a644a48a8796b03b1a6acedbbcbfc7acbf7
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86934258"
---
# <a name="create-a-custom-layout-in-xamarinforms"></a>Criar um layout personalizado emXamarin.Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-customlayout-wraplayout)

_Xamarin.Formsdefine cinco classes de layout – StackLayout, AbsoluteLayout, RelativeLayout, Grid e FlexLayout, e cada uma organiza seus filhos de forma diferente. No entanto, às vezes é necessário organizar o conteúdo da página usando um layout não fornecido pelo Xamarin.Forms . Este artigo explica como escrever uma classe de layout personalizada e demonstra uma classe WrapLayout sensível à orientação que organiza seus filhos horizontalmente na página e, em seguida, encapsula a exibição de filhos subsequentes em linhas adicionais._

No Xamarin.Forms , todas as classes de layout derivam da [`Layout<T>`](xref:Xamarin.Forms.Layout`1) classe e restringem o tipo genérico para [`View`](xref:Xamarin.Forms.View) e seus tipos derivados. Por sua vez, a `Layout<T>` classe deriva da [`Layout`](xref:Xamarin.Forms.Layout) classe, que fornece o mecanismo de posicionamento e dimensionamento de elementos filho.

Cada elemento visual é responsável por determinar seu próprio tamanho preferencial, que é conhecido como o tamanho *solicitado* . [`Page`](xref:Xamarin.Forms.Page)[`Layout`](xref:Xamarin.Forms.Layout) [`Layout<View>`](xref:Xamarin.Forms.Layout`1) os tipos derivados,, e são responsáveis por determinar o local e o tamanho de seus filhos, ou filhos, em relação a si mesmos. Portanto, o layout envolve uma relação pai-filho, em que o pai determina qual deve ser o tamanho de seus filhos, mas tentará acomodar o tamanho solicitado do filho.

É necessário um entendimento completo dos Xamarin.Forms ciclos de layout e invalidação para criar um layout personalizado. Esses ciclos agora serão discutidos.

## <a name="layout"></a>Layout

O layout começa na parte superior da árvore visual com uma página e prossegue por todas as ramificações da árvore visual para abranger todos os elementos visuais em uma página. Elementos que são pais para outros elementos são responsáveis por dimensionar e posicionar seus filhos em relação a si mesmos.

A [`VisualElement`](xref:Xamarin.Forms.VisualElement) classe define um [ `Measure` ] (xref: Xamarin.Forms . Visualelement. Measure (System. Double, System. Double, Xamarin.Forms . MeasureFlags)) que mede um elemento para operações de layout e um [ `Layout` ] (xref: Xamarin.Forms . Visualelement. layout ( Xamarin.Forms . Rectangle)) que especifica a área retangular em que o elemento será renderizado. Quando um aplicativo é iniciado e a primeira página é exibida, um *ciclo de layout* que consiste em primeiro de `Measure` chamadas e, em seguida, `Layout` chama, começa no [`Page`](xref:Xamarin.Forms.Page) objeto:

1. Durante o ciclo de layout, cada elemento pai é responsável por chamar o `Measure` método em seus filhos.
1. Depois que os filhos são medidos, cada elemento pai é responsável por chamar o `Layout` método em seus filhos.

Esse ciclo garante que todos os elementos visuais na página recebam chamadas para `Measure` os `Layout` métodos e. O processo é mostrado no diagrama a seguir:

![Xamarin.FormsCiclo de layout](custom-images/layout-cycle.png)

> [!NOTE]
> Observe que os ciclos de layout também podem ocorrer em um subconjunto da árvore visual se algo mudar para afetar o layout. Isso inclui itens sendo adicionados ou removidos de uma coleção, como em um [`StackLayout`](xref:Xamarin.Forms.StackLayout) , uma alteração na [`IsVisible`](xref:Xamarin.Forms.VisualElement.IsVisible) propriedade de um elemento ou uma alteração no tamanho de um elemento.

Cada Xamarin.Forms classe que tem uma `Content` ou uma `Children` propriedade tem um método substituível [`LayoutChildren`](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) . As classes de layout personalizadas que derivam de [`Layout<View>`](xref:Xamarin.Forms.Layout`1) devem substituir esse método e garantir que o [ `Measure` ] (xref: Xamarin.Forms . Visualelement. Measure (System. Double, System. Double, Xamarin.Forms . MeasureFlags)) e [ `Layout` ] (xref: Xamarin.Forms . Visualelement. layout ( Xamarin.Forms . Rectangle)) os métodos são chamados em todos os filhos do elemento, para fornecer o layout personalizado desejado.

Além disso, todas as classes que derivam de [`Layout`](xref:Xamarin.Forms.Layout) ou [`Layout<View>`](xref:Xamarin.Forms.Layout`1) devem substituir o [`OnMeasure`](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) método, que é onde uma classe de layout determina o tamanho que precisa ser fazendo chamadas para o [ `Measure` ] (xref: Xamarin.Forms . Visualelement. Measure (System. Double, System. Double, Xamarin.Forms . MeasureFlags)) métodos de seus filhos.

> [!NOTE]
> Os elementos determinam seu tamanho com base nas *restrições*, o que indica a quantidade de espaço disponível para um elemento dentro do pai do elemento. Restrições passadas para [ `Measure` ] (xref: Xamarin.Forms . Visualelement. Measure (System. Double, System. Double, Xamarin.Forms . MeasureFlags)) e os [`OnMeasure`](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) métodos podem variar de 0 a `Double.PositiveInfinity` . Um elemento é *restrito*, ou *totalmente restrito*, quando recebe uma chamada para seu [ `Measure` ] (xref: Xamarin.Forms . Visualelement. Measure (System. Double, System. Double, Xamarin.Forms . MeasureFlags)) método com argumentos não infinitos-o elemento é restrito a um tamanho específico. Um elemento é *irrestrito*, ou *parcialmente restrito*, quando recebe uma chamada para seu `Measure` método com pelo menos um argumento igual a `Double.PositiveInfinity` – a restrição infinita pode ser considerada como indicando o dimensionamento automático.

## <a name="invalidation"></a>Invalidação

Invalidação é o processo pelo qual uma alteração em um elemento em uma página dispara um novo ciclo de layout. Os elementos são considerados inválidos quando não têm mais o tamanho ou a posição corretos. Por exemplo, se a [`FontSize`](xref:Xamarin.Forms.Button.FontSize) propriedade de uma [`Button`](xref:Xamarin.Forms.Button) alteração for alterada, o `Button` será considerado inválido porque não terá mais o tamanho correto. O redimensionamento `Button` pode então ter um efeito ondulado de alterações no layout por meio do restante de uma página.

Os elementos invalidam a si próprios invocando o [`InvalidateMeasure`](xref:Xamarin.Forms.VisualElement.InvalidateMeasure) método, geralmente quando uma propriedade do elemento é alterada, o que pode resultar em um novo tamanho do elemento. Esse método dispara o [`MeasureInvalidated`](xref:Xamarin.Forms.VisualElement.MeasureInvalidated) evento, que os identificadores pai do elemento para disparar um novo ciclo de layout.

A [`Layout`](xref:Xamarin.Forms.Layout) classe define um manipulador para o [`MeasureInvalidated`](xref:Xamarin.Forms.VisualElement.MeasureInvalidated) evento em cada filho adicionado à sua `Content` propriedade ou `Children` coleção e desanexa o manipulador quando o filho é removido. Portanto, todos os elementos na árvore visual que têm filhos são alertados sempre que um de seus filhos altera o tamanho. O diagrama a seguir ilustra como uma alteração no tamanho de um elemento na árvore visual pode causar alterações que propagam a árvore:

![Invalidação na árvore visual](custom-images/invalidation.png)

No entanto, a `Layout` classe tenta restringir o impacto de uma alteração no tamanho de um filho no layout de uma página. Se o layout for de tamanho restrito, uma alteração de tamanho filho não afetará nada maior do que o layout pai na árvore visual. No entanto, geralmente uma alteração no tamanho de um layout afeta como o layout organiza seus filhos. Portanto, qualquer alteração no tamanho do layout iniciará um ciclo de layout para o layout e o layout receberá chamadas para seus [`OnMeasure`](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) métodos e [`LayoutChildren`](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) .

A [`Layout`](xref:Xamarin.Forms.Layout) classe também define um [`InvalidateLayout`](xref:Xamarin.Forms.Layout.InvalidateLayout) método que tem uma finalidade semelhante ao [`InvalidateMeasure`](xref:Xamarin.Forms.VisualElement.InvalidateMeasure) método. O `InvalidateLayout` método deve ser invocado sempre que for feita uma alteração que afeta como o layout posiciona e dimensiona seus filhos. Por exemplo, a `Layout` classe invoca o `InvalidateLayout` método sempre que um filho é adicionado ou removido de um layout.

O [`InvalidateLayout`](xref:Xamarin.Forms.Layout.InvalidateLayout) pode ser substituído para implementar um cache para minimizar invocações repetitivas do [ `Measure` ] (xref: Xamarin.Forms . Visualelement. Measure (System. Double, System. Double, Xamarin.Forms . MeasureFlags)) métodos de filhos do layout. Substituir o `InvalidateLayout` método fornecerá uma notificação de quando os filhos são adicionados ou removidos do layout. Da mesma forma, o [`OnChildMeasureInvalidated`](xref:Xamarin.Forms.Layout.OnChildMeasureInvalidated) método pode ser substituído para fornecer uma notificação quando um dos filhos do layout muda de tamanho. Para as duas substituições de método, um layout personalizado deve responder limpando o cache. Para obter mais informações, consulte [Calculate and cache layout data](#calculate-and-cache-layout-data).

## <a name="create-a-custom-layout"></a>Criar um layout personalizado

O processo para criar um layout personalizado é o seguinte:

1. Crie uma classe que derive da classe `Layout<View>`. Para obter mais informações, consulte [criar um WrapLayout](#create-a-wraplayout).
1. [*opcional*] Adicione Propriedades, apoiadas por propriedades vinculáveis, para quaisquer parâmetros que devem ser definidos na classe layout. Para obter mais informações, consulte [Adicionar propriedades apoiadas por propriedades vinculáveis](#add-properties-backed-by-bindable-properties).
1. Substitua o [`OnMeasure`](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) método para invocar [ `Measure` ] (xref: Xamarin.Forms . Visualelement. Measure (System. Double, System. Double, Xamarin.Forms . MeasureFlags)) em todos os filhos do layout e retorna um tamanho solicitado para o layout. Para obter mais informações, consulte [substituir o método onmeasure](#override-the-onmeasure-method).
1. Substitua o [`LayoutChildren`](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) método para invocar [ `Layout` ] (xref: Xamarin.Forms . Visualelement. layout ( Xamarin.Forms . Rectangle)) do método em todos os filhos do layout. Falha ao invocar [ `Layout` ] (xref: Xamarin.Forms . Visualelement. layout ( Xamarin.Forms . Rectangle)) o método em cada filho em um layout resultará no filho nunca recebendo um tamanho ou posição correto e, portanto, o filho não ficará visível na página. Para obter mais informações, consulte [substituir o método LayoutChildren](#override-the-layoutchildren-method).

    > [!NOTE]
    > Ao enumerar filhos nas [`OnMeasure`](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) [`LayoutChildren`](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) substituições e, ignore qualquer filho cuja [`IsVisible`](xref:Xamarin.Forms.VisualElement.IsVisible) Propriedade esteja definida como `false` . Isso garantirá que o layout personalizado não deixará espaço para filhos invisíveis.

1. [*opcional*] Substitua o [`InvalidateLayout`](xref:Xamarin.Forms.Layout.InvalidateLayout) método a ser notificado quando os filhos forem adicionados ou removidos do layout. Para obter mais informações, consulte [substituir o método InvalidateLayout](#override-the-invalidatelayout-method).
1. [*opcional*] Substitua o [`OnChildMeasureInvalidated`](xref:Xamarin.Forms.Layout.OnChildMeasureInvalidated) método a ser notificado quando um dos filhos do layout mudar de tamanho. Para obter mais informações, consulte [substituir o método OnChildMeasureInvalidated](#override-the-onchildmeasureinvalidated-method).

> [!NOTE]
> Observe que a [`OnMeasure`](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) substituição não será invocada se o tamanho do layout for regido por seu pai, em vez de seus filhos. No entanto, a substituição será invocada se uma ou ambas as restrições forem infinitas ou se a classe de layout tiver [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) valores de propriedade ou não padrão [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) . Por esse motivo, a [`LayoutChildren`](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) substituição não pode depender de tamanhos filho obtidos durante a [`OnMeasure`](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) chamada do método. Em vez disso, `LayoutChildren` deve invocar [ `Measure` ] (xref: Xamarin.Forms . Visualelement. Measure (System. Double, System. Double, Xamarin.Forms . MeasureFlags)) para o método nos filhos do layout, antes de invocar [ `Layout` ] (xref: Xamarin.Forms . Visualelement. layout ( Xamarin.Forms . Rectangle)). Como alternativa, o tamanho dos filhos obtidos na `OnMeasure` substituição pode ser armazenado em cache para evitar `Measure` invocações posteriores na `LayoutChildren` substituição, mas a classe de layout precisará saber quando os tamanhos precisam ser obtidos novamente. Para obter mais informações, consulte [Calculate and cache layout data](#calculate-and-cache-layout-data).

A classe de layout pode ser consumida adicionando-a a um [`Page`](xref:Xamarin.Forms.Page) e adicionando filhos ao layout. Para obter mais informações, consulte [consumir o WrapLayout](#consume-the-wraplayout).

### <a name="create-a-wraplayout"></a>Criar um WrapLayout

O aplicativo de exemplo demonstra uma classe sensível à orientação `WrapLayout` que organiza seus filhos horizontalmente na página e, em seguida, encapsula a exibição de filhos subsequentes em linhas adicionais.

A `WrapLayout` classe aloca a mesma quantidade de espaço para cada filho, conhecida como o tamanho da *célula*, com base no tamanho máximo dos filhos. Filhos menores que o tamanho da célula podem ser posicionados dentro da célula com base em seus [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) valores de propriedade e.

A `WrapLayout` definição de classe é mostrada no exemplo de código a seguir:

```csharp
public class WrapLayout : Layout<View>
{
  Dictionary<Size, LayoutData> layoutDataCache = new Dictionary<Size, LayoutData>();
  ...
}
```

#### <a name="calculate-and-cache-layout-data"></a>Calcular e armazenar em cache os dados de layout

A `LayoutData` estrutura armazena dados sobre uma coleção de filhos em várias propriedades:

- `VisibleChildCount`– o número de filhos que são visíveis no layout.
- `CellSize`– o tamanho máximo de todos os filhos, ajustados para o tamanho do layout.
- `Rows`– o número de linhas.
- `Columns`– o número de colunas.

O `layoutDataCache` campo é usado para armazenar vários `LayoutData` valores. Quando o aplicativo for iniciado, dois `LayoutData` objetos serão armazenados em cache no `layoutDataCache` dicionário para a orientação atual – um para os argumentos de restrição para a `OnMeasure` substituição e um para os `width` `height` argumentos e para a `LayoutChildren` substituição. Ao girar o dispositivo para a orientação paisagem, a `OnMeasure` substituição e a `LayoutChildren` substituição serão invocadas novamente, o que fará com que outros dois `LayoutData` objetos sejam armazenados em cache no dicionário. No entanto, ao retornar o dispositivo para a orientação retrato, nenhum cálculo adicional é necessário porque o `layoutDataCache` já tem os dados necessários.

O exemplo de código a seguir mostra o `GetLayoutData` método, que calcula as propriedades do `LayoutData` estruturado com base em um tamanho específico:

```csharp
LayoutData GetLayoutData(double width, double height)
{
  Size size = new Size(width, height);

  // Check if cached information is available.
  if (layoutDataCache.ContainsKey(size))
  {
    return layoutDataCache[size];
  }

  int visibleChildCount = 0;
  Size maxChildSize = new Size();
  int rows = 0;
  int columns = 0;
  LayoutData layoutData = new LayoutData();

  // Enumerate through all the children.
  foreach (View child in Children)
  {
    // Skip invisible children.
    if (!child.IsVisible)
      continue;

    // Count the visible children.
    visibleChildCount++;

    // Get the child's requested size.
    SizeRequest childSizeRequest = child.Measure(Double.PositiveInfinity, Double.PositiveInfinity);

    // Accumulate the maximum child size.
    maxChildSize.Width = Math.Max(maxChildSize.Width, childSizeRequest.Request.Width);
    maxChildSize.Height = Math.Max(maxChildSize.Height, childSizeRequest.Request.Height);
  }

  if (visibleChildCount != 0)
  {
    // Calculate the number of rows and columns.
    if (Double.IsPositiveInfinity(width))
    {
      columns = visibleChildCount;
      rows = 1;
    }
    else
    {
      columns = (int)((width + ColumnSpacing) / (maxChildSize.Width + ColumnSpacing));
      columns = Math.Max(1, columns);
      rows = (visibleChildCount + columns - 1) / columns;
    }

    // Now maximize the cell size based on the layout size.
    Size cellSize = new Size();

    if (Double.IsPositiveInfinity(width))
      cellSize.Width = maxChildSize.Width;
    else
      cellSize.Width = (width - ColumnSpacing * (columns - 1)) / columns;

    if (Double.IsPositiveInfinity(height))
      cellSize.Height = maxChildSize.Height;
    else
      cellSize.Height = (height - RowSpacing * (rows - 1)) / rows;

    layoutData = new LayoutData(visibleChildCount, cellSize, rows, columns);
  }

  layoutDataCache.Add(size, layoutData);
  return layoutData;
}
```

O `GetLayoutData` método executa as seguintes operações:

- Ele determina se um `LayoutData` valor calculado já está no cache e o retorna se ele estiver disponível.
- Caso contrário, ele enumerará todos os filhos, invocando o `Measure` método em cada filho com uma largura e altura infinitas e determinará o tamanho máximo do filho.
- Desde que haja pelo menos um filho visível, ele calcula o número de linhas e colunas necessárias e, em seguida, calcula um tamanho de célula para os filhos com base nas dimensões do `WrapLayout` . Observe que o tamanho da célula é geralmente um pouco mais largo do que o tamanho do filho máximo, mas isso também pode ser menor se o `WrapLayout` não for grande o suficiente para o filho mais largo ou o suficiente para o filho mais alto.
- Ele armazena o novo `LayoutData` valor no cache.

#### <a name="add-properties-backed-by-bindable-properties"></a>Adicionar propriedades com suporte pelas propriedades vinculáveis

A `WrapLayout` classe define `ColumnSpacing` e `RowSpacing` Propriedades, cujos valores são usados para separar as linhas e colunas no layout e que são apoiadas pelas propriedades vinculáveis. As propriedades vinculáveis são mostradas no exemplo de código a seguir:

```csharp
public static readonly BindableProperty ColumnSpacingProperty = BindableProperty.Create(
  "ColumnSpacing",
  typeof(double),
  typeof(WrapLayout),
  5.0,
  propertyChanged: (bindable, oldvalue, newvalue) =>
  {
    ((WrapLayout)bindable).InvalidateLayout();
  });

public static readonly BindableProperty RowSpacingProperty = BindableProperty.Create(
  "RowSpacing",
  typeof(double),
  typeof(WrapLayout),
  5.0,
  propertyChanged: (bindable, oldvalue, newvalue) =>
  {
    ((WrapLayout)bindable).InvalidateLayout();
  });
```

O manipulador de propriedade alterada de cada propriedade vinculável invoca a `InvalidateLayout` substituição do método para disparar uma nova passagem de layout no `WrapLayout` . Para obter mais informações, consulte [substituir o método InvalidateLayout](#override-the-invalidatelayout-method) e [substituir o método OnChildMeasureInvalidated](#override-the-onchildmeasureinvalidated-method).

#### <a name="override-the-onmeasure-method"></a>Substituir o método onmeasure

A `OnMeasure` substituição é mostrada no exemplo de código a seguir:

```csharp
protected override SizeRequest OnMeasure(double widthConstraint, double heightConstraint)
{
  LayoutData layoutData = GetLayoutData(widthConstraint, heightConstraint);
  if (layoutData.VisibleChildCount == 0)
  {
    return new SizeRequest();
  }

  Size totalSize = new Size(layoutData.CellSize.Width * layoutData.Columns + ColumnSpacing * (layoutData.Columns - 1),
                layoutData.CellSize.Height * layoutData.Rows + RowSpacing * (layoutData.Rows - 1));
  return new SizeRequest(totalSize);
}
```

A substituição invoca o `GetLayoutData` método e constrói um `SizeRequest` objeto a partir dos dados retornados, enquanto também leva em conta os `RowSpacing` valores de `ColumnSpacing` propriedade e. Para obter mais informações sobre o `GetLayoutData` método, consulte [Calculate and cache layout data](#calculate-and-cache-layout-data).

> [!IMPORTANT]
> O [ `Measure` ] (xref: Xamarin.Forms . Visualelement. Measure (System. Double, System. Double, Xamarin.Forms . MeasureFlags)) e os [`OnMeasure`](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) métodos nunca devem solicitar uma dimensão infinita retornando um [`SizeRequest`](xref:Xamarin.Forms.SizeRequest) valor com uma propriedade definida como `Double.PositiveInfinity` . No entanto, pelo menos um dos argumentos de restrição `OnMeasure` pode ser `Double.PositiveInfinity` .

#### <a name="override-the-layoutchildren-method"></a>Substituir o método LayoutChildren

A `LayoutChildren` substituição é mostrada no exemplo de código a seguir:

```csharp
protected override void LayoutChildren(double x, double y, double width, double height)
{
  LayoutData layoutData = GetLayoutData(width, height);

  if (layoutData.VisibleChildCount == 0)
  {
    return;
  }

  double xChild = x;
  double yChild = y;
  int row = 0;
  int column = 0;

  foreach (View child in Children)
  {
    if (!child.IsVisible)
    {
      continue;
    }

    LayoutChildIntoBoundingRegion(child, new Rectangle(new Point(xChild, yChild), layoutData.CellSize));
    if (++column == layoutData.Columns)
    {
      column = 0;
      row++;
      xChild = x;
      yChild += RowSpacing + layoutData.CellSize.Height;
    }
    else
    {
      xChild += ColumnSpacing + layoutData.CellSize.Width;
    }
  }
}
```

A substituição começa com uma chamada para o `GetLayoutData` método e, em seguida, enumera todos os filhos para dimensioná-los e posicioná-los na célula de cada filho. Isso é obtido invocando-se o [ `LayoutChildIntoBoundingRegion` ] (xref: Xamarin.Forms . Layout. LayoutChildIntoBoundingRegion ( Xamarin.Forms . Visualelement, Xamarin.Forms . Rectangle)), que é usado para posicionar um filho dentro de um retângulo com base em seus [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) valores de propriedade e. Isso é equivalente a fazer uma chamada para o [] do filho `Layout` (xref: Xamarin.Forms . Visualelement. layout ( Xamarin.Forms . Rectangle)).

> [!NOTE]
> Observe que o retângulo passado para o `LayoutChildIntoBoundingRegion` método inclui a área inteira na qual o filho pode residir.

Para obter mais informações sobre o `GetLayoutData` método, consulte [Calculate and cache layout data](#calculate-and-cache-layout-data).

#### <a name="override-the-invalidatelayout-method"></a>Substituir o método InvalidateLayout

A [`InvalidateLayout`](xref:Xamarin.Forms.Layout.InvalidateLayout) substituição é invocada quando os filhos são adicionados ou removidos do layout, ou quando uma das `WrapLayout` Propriedades altera o valor, conforme mostrado no exemplo de código a seguir:

```csharp
protected override void InvalidateLayout()
{
  base.InvalidateLayout();
  layoutInfoCache.Clear();
}
```

A substituição invalida o layout e descarta todas as informações de layout armazenadas em cache.

> [!NOTE]
> Para interromper a [`Layout`](xref:Xamarin.Forms.Layout) classe [`InvalidateLayout`](xref:Xamarin.Forms.Layout.InvalidateLayout) que está chamando o método sempre que um filho é adicionado ou removido de um layout, substitua o [ `ShouldInvalidateOnChildAdded` ] (xref: Xamarin.Forms . Layout. ShouldInvalidateOnChildAdded ( Xamarin.Forms . View)) e [ `ShouldInvalidateOnChildRemoved` ] (xref: Xamarin.Forms . Layout. ShouldInvalidateOnChildRemoved ( Xamarin.Forms . View)) e Return `false` . A classe de layout pode então implementar um processo personalizado quando os filhos são adicionados ou removidos.

#### <a name="override-the-onchildmeasureinvalidated-method"></a>Substituir o método OnChildMeasureInvalidated

A [`OnChildMeasureInvalidated`](xref:Xamarin.Forms.Layout.OnChildMeasureInvalidated) substituição é invocada quando um dos filhos do layout muda de tamanho e é mostrado no exemplo de código a seguir:

```csharp
protected override void OnChildMeasureInvalidated()
{
  base.OnChildMeasureInvalidated();
  layoutInfoCache.Clear();
}
```

A substituição invalida o layout filho e descarta todas as informações de layout em cache.

### <a name="consume-the-wraplayout"></a>Consumir o WrapLayout

A `WrapLayout` classe pode ser consumida colocando-a em um [`Page`](xref:Xamarin.Forms.Page) tipo derivado, conforme demonstrado no exemplo de código XAML a seguir:

```xaml
<ContentPage ... xmlns:local="clr-namespace:ImageWrapLayout">
    <ScrollView Margin="0,20,0,20">
        <local:WrapLayout x:Name="wrapLayout" />
    </ScrollView>
</ContentPage>
```

O código C# equivalente é mostrado abaixo:

```csharp
public class ImageWrapLayoutPageCS : ContentPage
{
  WrapLayout wrapLayout;

  public ImageWrapLayoutPageCS()
  {
    wrapLayout = new WrapLayout();

    Content = new ScrollView
    {
      Margin = new Thickness(0, 20, 0, 20),
      Content = wrapLayout
    };
  }
  ...
}
```

Os filhos podem ser adicionados ao `WrapLayout` conforme necessário. O exemplo de código a seguir mostra os [`Image`](xref:Xamarin.Forms.Image) elementos que estão sendo adicionados ao `WrapLayout` :

```csharp
protected override async void OnAppearing()
{
    base.OnAppearing();

    var images = await GetImageListAsync();
    if (images != null)
    {
        foreach (var photo in images.Photos)
        {
            var image = new Image
            {
                Source = ImageSource.FromUri(new Uri(photo))
            };
            wrapLayout.Children.Add(image);
        }
    }
}

async Task<ImageList> GetImageListAsync()
{
    try
    {
        string requestUri = "https://raw.githubusercontent.com/xamarin/docs-archive/master/Images/stock/small/stock.json";
        string result = await _client.GetStringAsync(requestUri);
        return JsonConvert.DeserializeObject<ImageList>(result);
    }
    catch (Exception ex)
    {
        Debug.WriteLine($"\tERROR: {ex.Message}");
    }

    return null;
}
```

Quando a página que contém o `WrapLayout` é exibida, o aplicativo de exemplo acessa de forma assíncrona um arquivo JSON remoto que contém uma lista de fotos, cria um [`Image`](xref:Xamarin.Forms.Image) elemento para cada foto e a adiciona ao `WrapLayout` . Isso resulta na aparência mostrada nas capturas de tela seguir:

![Capturas de tela retrato do aplicativo de exemplo](custom-images/portait-screenshots.png)

As capturas de tela a seguir mostram o `WrapLayout` depois que ele foi girado para orientação paisagem:

![Exemplo de captura de tela do cenário do aplicativo iOS exemplo de captura de tela exemplo de instantâneo de cenário do ](custom-images/landscape-ios.png)
 ![ ](custom-images/landscape-android.png)
 ![ aplicativo UWP](custom-images/landscape-uwp.png)

O número de colunas em cada linha depende do tamanho da foto, da largura da tela e do número de pixels por unidade independente de dispositivo. Os [`Image`](xref:Xamarin.Forms.Image) elementos carregam as fotos de forma assíncrona e, portanto, a `WrapLayout` classe receberá chamadas frequentes para seu [`LayoutChildren`](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) método, pois cada `Image` elemento recebe um novo tamanho com base na foto carregada.

## <a name="related-links"></a>Links relacionados

- [WrapLayout (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-customlayout-wraplayout)
- [Layouts personalizados](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter26.md)
- [Criando layouts personalizados no Xamarin.Forms (vídeo)](https://www.youtube.com/watch?v=sxjOqNZFhKU)
- [Layout\<T>](xref:Xamarin.Forms.Layout`1)
- [Layout](xref:Xamarin.Forms.Layout)
- [Visualelement](xref:Xamarin.Forms.VisualElement)
