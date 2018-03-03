---
title: Criar um Layout personalizado
description: "Xamarin. Forms define quatro classes de layout – StackLayout, AbsoluteLayout, RelativeLayout e grade, e cada organiza seus filhos de forma diferente. No entanto, às vezes, é necessário organizar o conteúdo de página usando um layout que não foi fornecido pelo xamarin. Forms. Este artigo explica como escrever uma classe de layout personalizado e demonstra uma classe de WrapLayout sensíveis à orientação que organiza os seus filhos horizontalmente na página e, em seguida, ajusta a exibição de filhos subsequentes para linhas adicionais."
ms.topic: article
ms.prod: xamarin
ms.assetid: B0CFDB59-14E5-49E9-965A-3DCCEDAC2E31
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/29/2017
ms.openlocfilehash: 4c7bf5f2c867faef7d9baf8d511393dbe2d129a4
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="creating-a-custom-layout"></a>Criar um Layout personalizado

_Xamarin. Forms define quatro classes de layout – StackLayout, AbsoluteLayout, RelativeLayout e grade, e cada organiza seus filhos de forma diferente. No entanto, às vezes, é necessário organizar o conteúdo de página usando um layout que não foi fornecido pelo xamarin. Forms. Este artigo explica como escrever uma classe de layout personalizado e demonstra uma classe de WrapLayout sensíveis à orientação que organiza os seus filhos horizontalmente na página e, em seguida, ajusta a exibição de filhos subsequentes para linhas adicionais._

## <a name="overview"></a>Visão geral

No xamarin. Forms, todas as classes de layout derivar o [ `Layout<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout%3CT%3E/) de classe e a restrição de tipo genérico para [ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/) e seus tipos derivados. Por sua vez, o `Layout<T>` classe deriva o [ `Layout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout/) classe, que fornece o mecanismo para posicionamento e filho de dimensionamento elementos.

Cada elemento visual é responsável por determinar seu próprio tamanho preferencial, o que é conhecido como o *solicitado* tamanho. [`Page`](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/), [ `Layout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout/), e [ `Layout<View>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout%3CT%3E/) tipos derivados são responsáveis por determinar o local e o tamanho de seu filho ou filho, em relação a mesmos. Portanto, o layout envolve uma relação pai-filho, em que o pai determina qual deve ser o tamanho de seus filhos, mas tentará acomodar o tamanho solicitado do filho.

Um entendimento completo dos ciclos de layout e invalidação da xamarin. Forms é necessária para criar um layout personalizado. Esses ciclos agora serão discutidos.

## <a name="layout"></a>Layout

Layout começa na parte superior da árvore visual de uma página, e ele passa todas as ramificações da árvore visual para abranger todos os elementos visuais em uma página. Elementos que são pais de outros elementos são responsáveis por dimensionamento e posicionamento de seus filhos em relação a mesmos.

O [ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/) classe define um [ `Measure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Measure/p/System.Double/System.Double/Xamarin.Forms.MeasureFlags/) método que mede um elemento para operações de layout, e um [ `Layout` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Layout/p/Xamarin.Forms.Rectangle/) método Especifica a área retangular que o elemento será renderizado em. Quando um aplicativo é iniciado e a primeira página é exibida, um *ciclo de layout* primeiro consistindo de `Measure` chamadas e, em seguida, `Layout` chama, inicia no [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) objeto:

1. Durante o ciclo de layout, cada elemento pai é responsável por chamar o `Measure` método em seus filhos.
1. Depois que os filhos foram calculados, cada elemento pai é responsável por chamar o `Layout` método em seus filhos.

Esse ciclo garante que cada elemento visual na página receba chamadas para o `Measure` e `Layout` métodos. O processo é mostrado no diagrama a seguir:

![](custom-images/layout-cycle.png "Ciclo de Layout xamarin. Forms")

> [!NOTE]
> Observe que os ciclos de layout também podem ocorrer em um subconjunto da árvore visual se algo muda para afetar o layout. Isso inclui itens que está sendo adicionado ou removido de uma coleção, como em um [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/), uma alteração no [ `IsVisible` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.IsVisible/) propriedade de um elemento ou uma alteração no tamanho de um elemento.

Cada classe de xamarin. Forms que tem um `Content` ou um `Children` propriedade tem um substituível [ `LayoutChildren` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.LayoutChildren/p/System.Double/System.Double/System.Double/System.Double/) método. Classes de layout personalizado que derivam de [ `Layout<View>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout%3CT%3E/) deve substituir este método e certifique-se de que o [ `Measure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Measure/p/System.Double/System.Double/Xamarin.Forms.MeasureFlags/) e [ `Layout` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Layout/p/Xamarin.Forms.Rectangle/) métodos são chamado em filhos de todos os do elemento, para fornecer o layout personalizado desejado.

Além disso, cada classe que deriva de [ `Layout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout/) ou [ `Layout<View>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout%3CT%3E/) deve substituir o [ `OnMeasure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.OnMeasure/p/System.Double/System.Double/) método, que é quando uma classe de layout Determina o tamanho que ele precisa ser fazendo chamadas para o [ `Measure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Measure/p/System.Double/System.Double/Xamarin.Forms.MeasureFlags/) métodos de seus filhos.

> [!NOTE]
> Elementos determinam seu tamanho com base em *restrições*, que indicam a quantidade de espaço está disponível para um elemento dentro do pai do elemento. Restrições passada para o [ `Measure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Measure/p/System.Double/System.Double/Xamarin.Forms.MeasureFlags/) e [ `OnMeasure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.OnMeasure/p/System.Double/System.Double/) métodos podem variar de 0 a `Double.PositiveInfinity`. É um elemento *restrita*, ou *totalmente restrita*, quando ele recebe uma chamada para seu [ `Measure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Measure/p/System.Double/System.Double/Xamarin.Forms.MeasureFlags/) método com argumentos não infinita - o elemento é restrita para um tamanho específico. É um elemento *irrestrita*, ou *parcialmente restrita*, quando ele recebe uma chamada para seu `Measure` método com pelo menos um argumento igual a `Double.PositiveInfinity` – a restrição infinita pode ser considerado indicando o dimensionamento automático.

## <a name="invalidation"></a>Invalidação

Invalidação é o processo pelo qual uma alteração em um elemento em uma página dispara um novo ciclo de layout. Elementos são considerados inválidos quando eles não tem mais o tamanho correto ou a posição. Por exemplo, se o [ `FontSize` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.FontSize/) propriedade de um [ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) alterações, o `Button` é considerado inválido porque ele não terá mais o tamanho correto. Redimensionando o `Button` , em seguida, pode ter um efeito de ondulação de alterações no layout com o restante de uma página.

Elementos invalidam próprios invocando o [ `InvalidateMeasure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.InvalidateMeasure()/) método, geralmente quando uma propriedade do elemento de alterações que pode resultar em um novo tamanho do elemento. Este método aciona o [ `MeasureInvalidated` ](https://developer.xamarin.com/api/event/Xamarin.Forms.VisualElement.MeasureInvalidated/) evento, que controla o pai do elemento para disparar um novo ciclo de layout.

O [ `Layout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout/) classe define um manipulador para o [ `MeasureInvalidated` ](https://developer.xamarin.com/api/event/Xamarin.Forms.VisualElement.MeasureInvalidated/) eventos em todos os filhos adicionados ao seu `Content` propriedade ou `Children` coleção e desanexa o manipulador quando o filho é removido. Portanto, cada elemento na árvore visual que possui filhos é alertado sempre que um de seus filhos altera o tamanho. O diagrama a seguir ilustra como uma alteração no tamanho de um elemento na árvore visual pode causar alterações de ondulação a árvore:

![](custom-images/invalidation.png "Invalidação na árvore Visual")

No entanto, a `Layout` classe tenta restringir o impacto de uma alteração no tamanho do filho no layout de uma página. Se o layout é restringido de tamanho, em seguida, uma alteração no tamanho do filho não afeta qualquer coisa maior do que o layout do pai na árvore visual. No entanto, geralmente uma alteração no tamanho de um layout afeta como o layout organiza os seus filhos. Portanto, qualquer alteração no tamanho do layout iniciará um ciclo de layout para o layout e o layout receberá chamadas para seus [ `OnMeasure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.OnMeasure/p/System.Double/System.Double/) e [ `LayoutChildren` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.LayoutChildren/p/System.Double/System.Double/System.Double/System.Double/) métodos.

O [ `Layout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout/) classe também define uma [ `InvalidateLayout` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.InvalidateLayout()/) método que tem uma finalidade semelhante para o [ `InvalidateMeasure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.InvalidateMeasure()/) método. O `InvalidateLayout` método deve ser chamado sempre que uma alteração é feita que afeta como o layout de posições e tamanhos de seus filhos. Por exemplo, o `Layout` classe invoca o `InvalidateLayout` método sempre que um filho é adicionado ou removido de um layout.

O [ `InvalidateLayout` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.InvalidateLayout()/) pode ser substituído para implementar um cache para minimizar as chamadas repetitivas do [ `Measure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Measure/p/System.Double/System.Double/Xamarin.Forms.MeasureFlags/) métodos de filhos do layout. Substituindo o `InvalidateLayout` método fornecerá uma notificação de quando os filhos são adicionados ou removidos do layout. Da mesma forma, o [ `OnChildMeasureInvalidated` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.OnChildMeasureInvalidated()/) método pode ser substituído para fornecer uma notificação quando um dos filhos do layout altera o tamanho. Para ambas as substituições de método, um layout personalizado deve responder limpando o cache. Para obter mais informações, consulte [Calculando e cache de dados](#caching).

## <a name="creating-a-custom-layout"></a>Criar um Layout personalizado

O processo para criar um layout personalizado é o seguinte:

1. Crie uma classe que derive da classe `Layout<View>`. Para obter mais informações, consulte [criando um WrapLayout](#creating).
1. [*opcional*] adicionar propriedades, com o apoio de propriedades vinculáveis, todos os parâmetros que devem ser definidas na classe de layout. Para obter mais informações, consulte [Adicionando propriedades com o apoio de propriedades vinculáveis](#adding_properties).
1. Substituir o [ `OnMeasure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.OnMeasure/p/System.Double/System.Double/) método a invocar o [ `Measure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Measure/p/System.Double/System.Double/Xamarin.Forms.MeasureFlags/) método em todo o layout filhos e retornar um tamanho solicitado para o layout. Para obter mais informações, consulte [substituindo o método OnMeasure](#onmeasure).
1. Substituir o [ `LayoutChildren` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.LayoutChildren/p/System.Double/System.Double/System.Double/System.Double/) método a invocar o [ `Layout` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Layout/p/Xamarin.Forms.Rectangle/) filhos do todos os o layout método. Falha ao invocar o [ `Layout` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Layout/p/Xamarin.Forms.Rectangle/) método em cada filho em um layout resultará no filho nunca receber um tamanho correto ou posição e, portanto, o filho não ficará visível na página. Para obter mais informações, consulte [substituindo o método LayoutChildren](#layoutchildren).

  > [!NOTE]
>  Ao enumerar os filhos no [ `OnMeasure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.OnMeasure/p/System.Double/System.Double/) e [ `LayoutChildren` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.LayoutChildren/p/System.Double/System.Double/System.Double/System.Double/) substituições, ignorar qualquer filho cujo [ `IsVisible` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.IsVisible/) está definida como `false`. Isso irá garantir que o layout personalizado não deixam espaço para filhos invisíveis.

1. [*opcional*] substituir o [ `InvalidateLayout` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.InvalidateLayout()/) método a ser notificado quando filhos são adicionados ou removidos do layout. Para obter mais informações, consulte [substituindo o método InvalidateLayout](#invalidatelayout).
1. [*opcional*] substituir o [ `OnChildMeasureInvalidated` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.OnChildMeasureInvalidated()/) método a ser notificado quando um dos filhos do layout altera o tamanho. Para obter mais informações, consulte [substituindo o método OnChildMeasureInvalidated](#onchildmeasureinvalidated).

> [!NOTE]
> Observe que o [ `OnMeasure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.OnMeasure/p/System.Double/System.Double/) substituição não ser invocada se o tamanho do layout é controlado pelo seu pai, em vez de seus filhos. No entanto, a substituição será chamada se uma ou ambas as restrições são infinitas ou se a classe de layout não-padrão [ `HorizontalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/) ou [ `VerticalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/) valores de propriedade. Por esse motivo, o [ `LayoutChildren` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.LayoutChildren/p/System.Double/System.Double/System.Double/System.Double/) substituição não pode confiar em tamanhos de filho obtidos durante a [ `OnMeasure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.OnMeasure/p/System.Double/System.Double/) chamada de método. Em vez disso, `LayoutChildren` deve chamar o [ `Measure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Measure/p/System.Double/System.Double/Xamarin.Forms.MeasureFlags/) método nos filhos do layout, antes de chamar o [ `Layout` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Layout/p/Xamarin.Forms.Rectangle/) método. Como alternativa, o tamanho dos filhos são obtidos no `OnMeasure` substituição pode ser armazenados em cache para evitar posteriormente `Measure` invocações no `LayoutChildren` substituição, mas a classe de layout precisará saber quando os tamanhos precisam ser obtidas. Para obter mais informações, consulte [Calculando e cache de dados de Layout](#caching).

A classe de layout pode ser consumida por adicioná-lo para um [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)e adicionando filhos no layout. Para obter mais informações, consulte [consumindo o WrapLayout](#consuming).

<a name="creating" />

### <a name="creating-a-wraplayout"></a>Criando um WrapLayout

O aplicativo de exemplo demonstra uma orientação diferencia `WrapLayout` classe organiza seus filhos horizontalmente na página e, em seguida, ajusta a exibição de filhos subsequentes para linhas adicionais.

O `WrapLayout` classe aloca a mesma quantidade de espaço para cada filho, conhecido como o *tamanho da célula*, com base no tamanho máximo dos filhos. Filhos menores do que o tamanho da célula pode ser posicionado dentro da célula com base em seus [ `HorizontalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/) e [ `VerticalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/) valores de propriedade.

O `WrapLayout` definição de classe é mostrada no exemplo de código a seguir:

```csharp
public class WrapLayout : Layout<View>
{
  Dictionary<Size, LayoutData> layoutDataCache = new Dictionary<Size, LayoutData>();
  ...
}
```

<a name="caching" />

#### <a name="calculating-and-caching-layout-data"></a>Calcular e armazenar em cache dados de Layout

O `LayoutData` estrutura armazena dados sobre uma coleção de filhos em um número de propriedades:

- `VisibleChildCount` – o número de filhos que são visíveis no layout.
- `CellSize` – o tamanho máximo de todos os filhos, ajustada para o tamanho do layout.
- `Rows` – o número de linhas.
- `Columns` – o número de colunas.

O `layoutDataCache` campo é usado para armazenar vários `LayoutData` valores. Quando o aplicativo for iniciado, dois `LayoutData` objetos serão armazenados em cache para o `layoutDataCache` dicionário para a orientação atual – uma para os argumentos de restrição para o `OnMeasure` substituição e uma para o `width` e `height` argumentos para o `LayoutChildren` substituir. Ao girar o dispositivo na orientação paisagem, o `OnMeasure` substituir e `LayoutChildren` substituição será novamente invocada, que resultará em dois outro `LayoutData` objetos sejam armazenados em cache no dicionário. No entanto, ao retornar o dispositivo para a orientação retrato, não há cálculos adicionais são necessários porque o `layoutDataCache` já tem os dados necessários.

O seguinte exemplo de código mostra o `GetLayoutData` método, que calcula as propriedades do `LayoutData` estruturados com base em um determinado tamanho:

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

- Determina se um calculado `LayoutData` valor já está no cache e retorna-o se ele estiver disponível.
- Caso contrário, ele enumera através de todos os filhos, chamar o `Measure` método em cada filho com um infinita largura e altura e determina o tamanho máximo de filhos.
- Desde que haja pelo menos um filho visível, ela calcula o número de linhas e colunas necessárias e, em seguida, calcula um tamanho de célula para os filhos com base nas dimensões do `WrapLayout`. Observe que o tamanho da célula é geralmente um pouco maior do que o tamanho máximo de filhos, mas que também pode ser menor se o `WrapLayout` não é grande o suficiente para a mais ampla filho ou altura suficiente para o filho mais alto.
- Ele armazena a nova `LayoutData` valor no cache.

<a name="adding_properties" />

#### <a name="adding-properties-backed-by-bindable-properties"></a>Adicionando propriedades com o apoio de propriedades vinculáveis

O `WrapLayout` classe define `ColumnSpacing` e `RowSpacing` propriedades, cujos valores são usados para separar as linhas e colunas no layout e que é apoiado por propriedades vinculáveis. As propriedades vinculáveis são mostradas no exemplo de código a seguir:

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

Invoca o manipulador de propriedade alterada de cada propriedade associável a `InvalidateLayout` substituição de método para disparar um novo layout de passar o `WrapLayout`. Para obter mais informações, consulte [substituindo o método InvalidateLayout](#invalidatelayout) e [substituindo o método OnChildMeasureInvalidated](#onchildmeasureinvalidated).

<a name="onmeasure" />

#### <a name="overriding-the-onmeasure-method"></a>Substituindo o método OnMeasure

O `OnMeasure` substituição é mostrada no exemplo de código a seguir:

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

Invoca a substituição de `GetLayoutData` método e construções um `SizeRequest` objeto de dados retornados, enquanto também levando em consideração o `RowSpacing` e `ColumnSpacing` valores de propriedade. Para obter mais informações sobre o `GetLayoutData` método, consulte [Calculando e cache de dados](#caching).

> [!IMPORTANT]
> O [ `Measure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Measure/p/System.Double/System.Double/Xamarin.Forms.MeasureFlags/) e [ `OnMeasure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.OnMeasure/p/System.Double/System.Double/) métodos nunca devem solicitar uma dimensão de infinita, retornando um [ `SizeRequest` ](https://developer.xamarin.com/api/type/Xamarin.Forms.SizeRequest/) valor com uma propriedade definida como `Double.PositiveInfinity`. No entanto, pelo menos um dos argumentos de restrição para `OnMeasure` pode ser `Double.PositiveInfinity`.

<a name="layoutchildren" />

#### <a name="overriding-the-layoutchildren-method"></a>Substituindo o método LayoutChildren

O `LayoutChildren` substituição é mostrada no exemplo de código a seguir:

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

A substituição começa com uma chamada para o `GetLayoutData` método e, em seguida, enumera todos os filhos de tamanho e posicioná-los dentro da célula de cada filho. Isso é feito chamando a [ `LayoutChildIntoBoundingRegion` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.LayoutChildIntoBoundingRegion/p/Xamarin.Forms.VisualElement/Xamarin.Forms.Rectangle/) método, que é usado para posicionar um filho dentro de um retângulo com base em seu [ `HorizontalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/) e [ `VerticalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/)valores de propriedade. Isso é equivalente ao fazer uma chamada para o filho [ `Layout` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Layout/p/Xamarin.Forms.Rectangle/) método.

> [!NOTE]
> Observe que o retângulo é passado para o `LayoutChildIntoBoundingRegion` método inclui toda a área na qual o filho pode residir.

Para obter mais informações sobre o `GetLayoutData` método, consulte [Calculando e cache de dados](#caching).

<a name="invalidatelayout" />

#### <a name="overriding-the-invalidatelayout-method"></a>Substituindo o método InvalidateLayout

O [ `InvalidateLayout` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.InvalidateLayout()/) substituição é invocada quando filhos são adicionados ou removidos do layout, ou quando um do `WrapLayout` alterações de propriedades de valor, conforme mostrado no exemplo de código a seguir:

```csharp
protected override void InvalidateLayout()
{
  base.InvalidateLayout();
  layoutInfoCache.Clear();
}
```

A substituição invalida o layout e descarta todas as informações de layout em cache.

> [!NOTE]
> Para interromper o [ `Layout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout/) classe invocando o [ `InvalidateLayout` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.InvalidateLayout()/) método sempre que um filho é adicionado ou removido de um layout, substituir o [ `ShouldInvalidateOnChildAdded` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.ShouldInvalidateOnChildAdded/p/Xamarin.Forms.View/) e [ `ShouldInvalidateOnChildRemoved` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.ShouldInvalidateOnChildRemoved/p/Xamarin.Forms.View/) métodos e retornar `false`. A classe de layout, em seguida, pode implementar um processo personalizado quando filhos são adicionados ou removidos.

<a name="onchildmeasureinvalidated" />

#### <a name="overriding-the-onchildmeasureinvalidated-method"></a>Substituindo o método OnChildMeasureInvalidated

O [ `OnChildMeasureInvalidated` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.OnChildMeasureInvalidated()/) substituição é chamada quando um dos filhos do layout altera o tamanho e é mostrado no exemplo de código a seguir:

```csharp
protected override void OnChildMeasureInvalidated()
{
  base.OnChildMeasureInvalidated();
  layoutInfoCache.Clear();
}
```

A substituição invalida o layout do filho e descarta todas as informações de layout em cache.

<a name="consuming" />

### <a name="consuming-the-wraplayout"></a>Consumindo o WrapLayout

O `WrapLayout` classe pode ser consumida, colocando-o em um [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) tipo derivado, conforme demonstrado no exemplo de código XAML a seguir:

```xaml
<ContentPage ... xmlns:local="clr-namespace:ImageWrapLayout">
    <ScrollView Margin="0,20,0,20">
        <local:WrapLayout x:Name="wrapLayout" />
    </ScrollView>
</ContentPage>
```

O código c# equivalente é mostrado abaixo:

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

Filhos podem ser adicionados para o `WrapLayout` conforme necessário. O seguinte exemplo de código mostra [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) elementos que estão sendo adicionados para o `WrapLayout`:

```csharp
protected override async void OnAppearing()
{
  base.OnAppearing();

  var images = await GetImageListAsync();
  foreach (var photo in images.Photos)
  {
    var image = new Image
    {
      Source = ImageSource.FromUri(new Uri(photo + string.Format("?width={0}&height={0}&mode=max", Device.RuntimePlatform == Device.UWP ? 120 : 240)))
    };
    wrapLayout.Children.Add(image);
  }
}

async Task<ImageList> GetImageListAsync()
{
  var requestUri = "https://docs.xamarin.com/demo/stock.json";
  using (var client = new HttpClient())
  {
    var result = await client.GetStringAsync(requestUri);
    return JsonConvert.DeserializeObject<ImageList>(result);
  }
}
```

Quando a página que contém o `WrapLayout` aparece, de forma assíncrona, o aplicativo de exemplo acessa um arquivo remoto de JSON que contém uma lista de fotos, cria um [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) elemento para cada foto e o adiciona à `WrapLayout`. Isso resulta na exibição mostrada nas capturas de tela seguir:

![](custom-images/portait-screenshots.png "Capturas de tela do exemplo aplicativo retrato")

O capturas de tela a seguir mostram o `WrapLayout` depois é girada para a orientação paisagem:

![](custom-images/landscape-ios.png "Captura de tela de cenário de aplicativo do iOS de exemplo")
![](custom-images/landscape-android.png "tela de cenário de aplicativo Android exemplo") 
 ![ ] (custom-images/landscape-uwp.png " Captura de tela do exemplo UWP aplicativo paisagem")

O número de colunas em cada linha depende do tamanho da foto, a largura da tela e o número de pixels por unidade independente de dispositivo. O [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) elementos de forma assíncrona carregar fotos e, portanto, o `WrapLayout` classe receberá chamadas frequentes ao seu [ `LayoutChildren` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.LayoutChildren/p/System.Double/System.Double/System.Double/System.Double/) método como cada `Image` elemento recebe um novo tamanho com base na foto carregada.

## <a name="summary"></a>Resumo

Este artigo explicou como escrever uma classe de layout personalizado e demonstrou uma orientação diferencia `WrapLayout` classe organiza seus filhos horizontalmente na página e, em seguida, ajusta a exibição de filhos subsequentes para linhas adicionais.


## <a name="related-links"></a>Links relacionados

- [WrapLayout (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/CustomLayout/WrapLayout/)
- [Layouts personalizados](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter26.md)
- [Criar Layouts personalizados no xamarin. Forms (vídeo)](https://evolve.xamarin.com/session/56e20f83bad314273ca4d81c)
- [Layout<T>](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout%3CT%3E/)
- [Layout](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout/)
- [VisualElement](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/)
