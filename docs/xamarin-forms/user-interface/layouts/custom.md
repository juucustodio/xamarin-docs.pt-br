---
title: Criar um layout personalizado no Xamarin. Forms
description: Este artigo explica como escrever uma classe de layout personalizado e demonstra uma classe de WrapLayout sensíveis à orientação que organiza seus filhos horizontalmente pela página e, em seguida, ajusta a exibição dos filhos subsequentes em linhas adicionais.
ms.prod: xamarin
ms.assetid: B0CFDB59-14E5-49E9-965A-3DCCEDAC2E31
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/29/2017
ms.openlocfilehash: 0828d780ed075a6e3b18ba5020f5908fb8c06189
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70292583"
---
# <a name="create-a-custom-layout-in-xamarinforms"></a>Criar um layout personalizado no Xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-customlayout-wraplayout)

_Xamarin. Forms define quatro classes de layout – StackLayout, AbsoluteLayout, RelativeLayout e grade, e cada organiza seus filhos de uma maneira diferente. No entanto, às vezes, é necessário para organizar o conteúdo da página usando um layout que não são fornecido pelo xamarin. Forms. Este artigo explica como escrever uma classe de layout personalizado e demonstra uma classe de WrapLayout sensíveis à orientação que organiza seus filhos horizontalmente pela página e, em seguida, ajusta a exibição dos filhos subsequentes em linhas adicionais._

No xamarin. Forms, todas as classes de layout derivam de [ `Layout<T>` ](xref:Xamarin.Forms.Layout`1) classe e restringir o tipo genérico para [ `View` ](xref:Xamarin.Forms.View) e seus tipos derivados. Por sua vez, o `Layout<T>` classe deriva de [ `Layout` ](xref:Xamarin.Forms.Layout) classe, que fornece o mecanismo para posicionamento e o filho de dimensionamento elementos.

Cada elemento visual é responsável por determinar seu próprio tamanho preferencial, o que é conhecido como o *solicitado* tamanho. [`Page`](xref:Xamarin.Forms.Page), [ `Layout` ](xref:Xamarin.Forms.Layout), e [ `Layout<View>` ](xref:Xamarin.Forms.Layout`1) tipos derivados são responsáveis por determinar o local e o tamanho do seu filho ou filhos, em relação a mesmos. Portanto, o layout envolve uma relação pai-filho, no qual o pai determina qual deve ser o tamanho de seus filhos, mas tentará acomodar o tamanho solicitado do filho.

Uma compreensão detalhada dos ciclos de layout e a invalidação da xamarin. Forms é necessária para criar um layout personalizado. Esses ciclos agora serão discutidos.

## <a name="layout"></a>Layout

Layout começa na parte superior da árvore visual com uma página, e ele passa todas as ramificações da árvore visual para abranger todos os elementos visuais em uma página. Elementos que são pais de outros elementos são responsáveis por redimensionar e posicionar seus filhos em relação a mesmos.

O [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) classe define um [ `Measure` ](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags)) o método que mede um elemento para operações de layout, e um [ `Layout` ](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle)) método Especifica a área retangular que o elemento será renderizado no. Quando um aplicativo é iniciado e a primeira página for exibida, um *ciclo de layout* primeiro consistindo de `Measure` chamadas e, em seguida `Layout` chama, inicia no [ `Page` ](xref:Xamarin.Forms.Page) objeto:

1. Durante o ciclo de layout, cada elemento pai é responsável por chamar o `Measure` método em seus filhos.
1. Após os filhos terem sido medidos, todos os elementos pai é responsável por chamar o `Layout` método em seus filhos.

Esse ciclo garante que cada elemento visual na página recebe chamadas para o `Measure` e `Layout` métodos. O processo é mostrado no diagrama a seguir:

![](custom-images/layout-cycle.png "Ciclo de Layout do xamarin. Forms")

> [!NOTE]
> Observe que os ciclos de layout também podem ocorrer em um subconjunto da árvore visual se algo for alterado para afetar o layout. Isso inclui itens que estão sendo adicionados ou removidos da coleção como em uma [ `StackLayout` ](xref:Xamarin.Forms.StackLayout), uma alteração no [ `IsVisible` ](xref:Xamarin.Forms.VisualElement.IsVisible) propriedade de um elemento ou uma alteração no tamanho de um elemento.

Cada classe de xamarin. Forms que tem um `Content` ou um `Children` propriedade tem um substituível [ `LayoutChildren` ](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) método. Classes de layout personalizados que derivam de [ `Layout<View>` ](xref:Xamarin.Forms.Layout`1) deve substituir esse método e certifique-se de que o [ `Measure` ](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags)) e [ `Layout` ](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle)) são métodos chamado em todos os filhos do elemento, para fornecer o layout personalizado desejado.

Além disso, cada classe que deriva de [ `Layout` ](xref:Xamarin.Forms.Layout) ou [ `Layout<View>` ](xref:Xamarin.Forms.Layout`1) deve substituir o [ `OnMeasure` ](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) método, que é sempre que uma classe de layout Determina o tamanho que ele precisa estar fazendo chamadas para o [ `Measure` ](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags)) métodos de seus filhos.

> [!NOTE]
> Elementos determinam seu tamanho com base no *restrições*, que indicam a quantidade de espaço está disponível para um elemento dentro do pai do elemento. Restrições passada para o [ `Measure` ](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags)) e [ `OnMeasure` ](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) métodos podem variar de 0 a `Double.PositiveInfinity`. É um elemento *restrita*, ou *totalmente restrita*, quando ele recebe uma chamada para seu [ `Measure` ](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags)) método com argumentos não infinita - o elemento é restrita para um tamanho específico. É um elemento *irrestrita*, ou *parcialmente restrita*, quando ele recebe uma chamada para seu `Measure` método com pelo menos um argumento igual a `Double.PositiveInfinity` – a restrição de infinita pode ser pensada como indicando o dimensionamento automático.

## <a name="invalidation"></a>Invalidação

Invalidação de é o processo pelo qual uma alteração em um elemento em uma página dispara um novo ciclo de layout. Elementos são considerados inválidos quando eles não terão mais o tamanho correto ou a posição. Por exemplo, se o [ `FontSize` ](xref:Xamarin.Forms.Button.FontSize) propriedade de uma [ `Button` ](xref:Xamarin.Forms.Button) alterações, o `Button` será considerada inválida porque ela não terá mais o tamanho correto. Redimensionando o `Button` , em seguida, pode ter um efeito dominó das alterações no layout através do restante de uma página.

Elementos invalidam a mesmos, invocando o [ `InvalidateMeasure` ](xref:Xamarin.Forms.VisualElement.InvalidateMeasure) método, geralmente quando uma propriedade do elemento é alterada que pode resultar em um novo tamanho do elemento. Este método aciona o [ `MeasureInvalidated` ](xref:Xamarin.Forms.VisualElement.MeasureInvalidated) evento, que lida com o pai do elemento para disparar um novo ciclo de layout.

O [ `Layout` ](xref:Xamarin.Forms.Layout) classe define um manipulador para o [ `MeasureInvalidated` ](xref:Xamarin.Forms.VisualElement.MeasureInvalidated) evento em todos os filhos adicionados ao seu `Content` propriedade ou `Children` coleção e desanexa o manipulador quando o filho é removido. Portanto, todos os elementos na árvore visual que tem filhos é alertado sempre que um de seus filhos altera o tamanho. O diagrama a seguir ilustra como uma alteração no tamanho de um elemento na árvore visual pode causar alterações ripple acima na árvore:

![](custom-images/invalidation.png "Invalidação na árvore Visual")

No entanto, o `Layout` classe tenta restringir o impacto de uma alteração no tamanho de um filho no layout de uma página. Se o layout é restringido de tamanho, em seguida, uma alteração de tamanho de filho não afeta nada maior do que o layout do pai na árvore visual. No entanto, geralmente uma alteração no tamanho de um layout afeta como o layout organiza seus filhos. Portanto, qualquer alteração no tamanho de um layout iniciará um ciclo de layout para o layout e o layout receberá chamadas para seus [ `OnMeasure` ](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) e [ `LayoutChildren` ](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) métodos.

O [ `Layout` ](xref:Xamarin.Forms.Layout) classe define também uma [ `InvalidateLayout` ](xref:Xamarin.Forms.Layout.InvalidateLayout) método que tem uma finalidade similar do [ `InvalidateMeasure` ](xref:Xamarin.Forms.VisualElement.InvalidateMeasure) método. O `InvalidateLayout` método deve ser chamado sempre que for feita uma alteração que afeta como o layout posiciona e dimensiona seus filhos. Por exemplo, o `Layout` classe invoca o `InvalidateLayout` método sempre que um filho é adicionado ou removido de um layout.

O [ `InvalidateLayout` ](xref:Xamarin.Forms.Layout.InvalidateLayout) pode ser substituído para implementar um cache para minimizar repetitivas invocações da [ `Measure` ](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags)) métodos de filhos do layout. Substituindo o `InvalidateLayout` método fornecerá uma notificação de quando os filhos são adicionados ou removidos do layout. Da mesma forma, o [ `OnChildMeasureInvalidated` ](xref:Xamarin.Forms.Layout.OnChildMeasureInvalidated) método pode ser substituído para fornecer uma notificação quando um dos filhos do layout muda de tamanho. Para ambas as substituições de método, um layout personalizado deve responder ao limpar o cache. Para obter mais informações, consulte [Calculando e armazenando dados](#caching).

## <a name="create-a-custom-layout"></a>Criar um layout personalizado

O processo para criar um layout personalizado é da seguinte maneira:

1. Crie uma classe que derive da classe `Layout<View>`. Para obter mais informações, consulte [criando um WrapLayout](#creating).
1. [*opcional*] adicionar propriedades, apoiadas por propriedades vinculáveis, para todos os parâmetros que devem ser definidas na classe de layout. Para obter mais informações, consulte [Adicionando propriedades apoiado por propriedades vinculáveis](#adding_properties).
1. Substituir a [ `OnMeasure` ](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) método para invocar o [ `Measure` ](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags)) método em todo o layout filhos e retornar um tamanho solicitado para o layout. Para obter mais informações, consulte [substituindo o método OnMeasure](#onmeasure).
1. Substituir o [ `LayoutChildren` ](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) método para invocar o [ `Layout` ](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle)) filhos do todos os o layout método. Falha ao invocar o [ `Layout` ](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle)) método em cada filho em um layout resultará na filho nunca receber um tamanho correto ou posição e, portanto, o filho não se tornará visível na página. Para obter mais informações, consulte [substituindo o método LayoutChildren](#layoutchildren).

    > [!NOTE]
    > Ao enumerar os filhos na [ `OnMeasure` ](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) e [ `LayoutChildren` ](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) substituições, ignore qualquer filho cujo [ `IsVisible` ](xref:Xamarin.Forms.VisualElement.IsVisible) estiver definida como `false`. Isso garantirá que o layout personalizado não deixar espaço para os filhos invisíveis.

1. [*opcional*] substituir a [ `InvalidateLayout` ](xref:Xamarin.Forms.Layout.InvalidateLayout) método a ser notificado quando os filhos são adicionados ou removidos do layout. Para obter mais informações, consulte [substituindo o método InvalidateLayout](#invalidatelayout).
1. [*opcional*] substituir a [ `OnChildMeasureInvalidated` ](xref:Xamarin.Forms.Layout.OnChildMeasureInvalidated) método a ser notificado quando um dos filhos do layout muda de tamanho. Para obter mais informações, consulte [substituindo o método OnChildMeasureInvalidated](#onchildmeasureinvalidated).

> [!NOTE]
> Observe que o [ `OnMeasure` ](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) substituição não será invocada se o tamanho do layout é controlado pelo seu pai, em vez de seus filhos. No entanto, a substituição será chamada se uma ou ambas as restrições são infinitas ou se a classe de layout tem não padrão [ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions) ou [ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions) valores de propriedade. Por esse motivo, o [ `LayoutChildren` ](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) substituição não pode contar com tamanhos de filho obtidos durante a [ `OnMeasure` ](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) chamada de método. Em vez disso, `LayoutChildren` deve invocar o [ `Measure` ](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags)) método nos filhos do layout, antes de invocar o [ `Layout` ](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle)) método. Como alternativa, o tamanho dos filhos obtido na `OnMeasure` substituição pode ser armazenados em cache para evitar mais tarde `Measure` invocações no `LayoutChildren` substituição, mas a classe de layout será preciso saber quando os tamanhos precisam ser obtido novamente. Para obter mais informações, consulte [Calculando e armazenando dados de Layout](#caching).

A classe de layout, em seguida, pode ser consumida por adicioná-lo para um [ `Page` ](xref:Xamarin.Forms.Page)e pela adição de filhos no layout. Para obter mais informações, consulte [consumindo o WrapLayout](#consuming).

<a name="creating" />

### <a name="create-a-wraplayout"></a>Criar um WrapLayout

O aplicativo de exemplo demonstra uma orientação diferencia `WrapLayout` classe que organiza seus filhos horizontalmente pela página e, em seguida, ajusta a exibição dos filhos subsequentes em linhas adicionais.

O `WrapLayout` classe aloca a mesma quantidade de espaço para cada filho, conhecido como o *tamanho de célula*, com base no tamanho máximo dos filhos. Menor do que o tamanho da célula pode ser posicionado dentro da célula de filhos com base em suas [ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions) e [ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions) valores de propriedade.

O `WrapLayout` definição de classe é mostrada no exemplo de código a seguir:

```csharp
public class WrapLayout : Layout<View>
{
  Dictionary<Size, LayoutData> layoutDataCache = new Dictionary<Size, LayoutData>();
  ...
}
```

<a name="caching" />

#### <a name="calculate-and-cache-layout-data"></a>Calcular e armazenar em cache os dados de layout

O `LayoutData` estrutura armazena dados sobre uma coleção de filhos em um número de propriedades:

- `VisibleChildCount` – o número de filhos que são visíveis no layout.
- `CellSize` – o tamanho máximo de todos os filhos, ajustado para o tamanho do layout.
- `Rows` – o número de linhas.
- `Columns` – o número de colunas.

O `layoutDataCache` campo é usado para armazenar vários `LayoutData` valores. Quando o aplicativo for iniciado, dois `LayoutData` objetos serão armazenados em cache para o `layoutDataCache` dicionário para a orientação atual – uma para os argumentos de restrição para o `OnMeasure` substituição e outro para o `width` e `height` argumentos para o `LayoutChildren` substituir. Para girar o dispositivo em orientação paisagem, o `OnMeasure` substituir e o `LayoutChildren` substituição será novamente invocada, que resulta em dois outro `LayoutData` objetos a serem armazenados em cache no dicionário. No entanto, ao retornar o dispositivo para a orientação retrato, não há cálculos adicionais são necessários porque o `layoutDataCache` já tem os dados necessários.

O seguinte exemplo de código mostra a `GetLayoutData` método, que calcula as propriedades do `LayoutData` estruturados com base em um determinado tamanho:

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

- Ele determina se um calculado `LayoutData` valor já está no cache e retorna-o se ele estiver disponível.
- Caso contrário, ele enumera todos os filhos, invocando o `Measure` método em cada filho com uma largura infinita e a altura e determina o tamanho máximo de filho.
- Desde que haja pelo menos um filho visível, ele calcula o número de linhas e colunas necessárias e, em seguida, calcula um tamanho de célula para os filhos com base nas dimensões do `WrapLayout`. Observe que o tamanho da célula é geralmente um pouco mais amplo do que o tamanho máximo de filhos, mas que também pode ser menor se o `WrapLayout` não é grande o suficiente para a mais ampla filho ou tall suficiente para o filho mais alto.
- Ele armazena o novo `LayoutData` valor no cache.

<a name="adding_properties" />

#### <a name="add-properties-backed-by-bindable-properties"></a>Adicionar propriedades com suporte pelas propriedades vinculáveis

O `WrapLayout` classe define `ColumnSpacing` e `RowSpacing` propriedades, cujos valores são usados para separar as linhas e colunas no layout, e que é apoiado por propriedades associáveis. As propriedades vinculáveis são mostradas no exemplo de código a seguir:

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

Invoca o manipulador de propriedade alterada de cada propriedade associável a `InvalidateLayout` substituição do método para disparar um novo layout passar o `WrapLayout`. Para obter mais informações, consulte [substituindo o método InvalidateLayout](#invalidatelayout) e [substituindo o método OnChildMeasureInvalidated](#onchildmeasureinvalidated).

<a name="onmeasure" />

#### <a name="override-the-onmeasure-method"></a>Substituir o método onmeasure

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

Invoca a substituição de `GetLayoutData` método e construções de um `SizeRequest` objeto dos dados retornados, levando em consideração também a `RowSpacing` e `ColumnSpacing` valores de propriedade. Para obter mais informações sobre o `GetLayoutData` método, consulte [Calculando e armazenando dados](#caching).

> [!IMPORTANT]
> O [ `Measure` ](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags)) e [ `OnMeasure` ](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) métodos nunca devem solicitar uma dimensão de infinita, retornando um [ `SizeRequest` ](xref:Xamarin.Forms.SizeRequest) valor com uma propriedade definida como `Double.PositiveInfinity`. No entanto, pelo menos um dos argumentos de restrição `OnMeasure` pode ser `Double.PositiveInfinity`.

<a name="layoutchildren" />

#### <a name="override-the-layoutchildren-method"></a>Substituir o método LayoutChildren

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

A substituição começa com uma chamada para o `GetLayoutData` método e, em seguida, enumera todos os filhos para dimensionar e posicioná-los dentro da célula de cada filho. Isso é feito invocando o [ `LayoutChildIntoBoundingRegion` ](xref:Xamarin.Forms.Layout.LayoutChildIntoBoundingRegion(Xamarin.Forms.VisualElement,Xamarin.Forms.Rectangle)) método, que é usado para posicionar um filho dentro de um retângulo com base em seu [ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions) e [ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions)valores de propriedade. Isso é equivalente a fazer uma chamada para o filho [ `Layout` ](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle)) método.

> [!NOTE]
> Observe que o retângulo transmitido para o `LayoutChildIntoBoundingRegion` método inclui toda a área em que o filho pode residir.

Para obter mais informações sobre o `GetLayoutData` método, consulte [Calculando e armazenando dados](#caching).

<a name="invalidatelayout" />

#### <a name="overridethe-invalidatelayout-method"></a>Método Overridethe InvalidateLayout

O [ `InvalidateLayout` ](xref:Xamarin.Forms.Layout.InvalidateLayout) substituição é invocada quando filhos são adicionados ou removidos do layout, ou quando um do `WrapLayout` alterações de propriedades de valor, conforme mostrado no exemplo de código a seguir:

```csharp
protected override void InvalidateLayout()
{
  base.InvalidateLayout();
  layoutInfoCache.Clear();
}
```

A substituição invalida o layout e descarta todas as informações de layout em cache.

> [!NOTE]
> Para parar o [ `Layout` ](xref:Xamarin.Forms.Layout) classe invocando o [ `InvalidateLayout` ](xref:Xamarin.Forms.Layout.InvalidateLayout) substituição do método sempre que um filho é adicionado ou removido de um layout, o [ `ShouldInvalidateOnChildAdded` ](xref:Xamarin.Forms.Layout.ShouldInvalidateOnChildAdded(Xamarin.Forms.View)) e [ `ShouldInvalidateOnChildRemoved` ](xref:Xamarin.Forms.Layout.ShouldInvalidateOnChildRemoved(Xamarin.Forms.View)) métodos e retorno `false`. A classe de layout, em seguida, pode implementar um processo personalizado quando filhos são adicionados ou removidos.

<a name="onchildmeasureinvalidated" />

#### <a name="override-the-onchildmeasureinvalidated-method"></a>Substituir o método OnChildMeasureInvalidated

O [ `OnChildMeasureInvalidated` ](xref:Xamarin.Forms.Layout.OnChildMeasureInvalidated) substituição é invocada quando um dos filhos do layout muda de tamanho e é mostrado no exemplo de código a seguir:

```csharp
protected override void OnChildMeasureInvalidated()
{
  base.OnChildMeasureInvalidated();
  layoutInfoCache.Clear();
}
```

A substituição invalida o layout filho e descarta todas as informações de layout em cache.

<a name="consuming" />

### <a name="consume-the-wraplayout"></a>Consumir o WrapLayout

O `WrapLayout` classe pode ser consumido, colocando-o em um [ `Page` ](xref:Xamarin.Forms.Page) tipo derivado, conforme demonstrado no exemplo de código XAML a seguir:

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

Filhos, em seguida, podem ser adicionados para o `WrapLayout` conforme necessário. O seguinte exemplo de código mostra [ `Image` ](xref:Xamarin.Forms.Image) elementos que estão sendo adicionados ao `WrapLayout`:

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

Quando a página que contém o `WrapLayout` aparecer, de forma assíncrona, o aplicativo de exemplo acessa um arquivo JSON remoto que contém uma lista de fotos, cria um [ `Image` ](xref:Xamarin.Forms.Image) elemento para cada foto e adiciona-o para o `WrapLayout`. Isso resulta na exibição mostrada nas capturas de tela seguir:

![](custom-images/portait-screenshots.png "Capturas de tela do exemplo aplicativo retrato")

As capturas de tela a seguir mostram o `WrapLayout` depois é girada para a orientação paisagem:

![](custom-images/landscape-ios.png "Captura de tela de cenário de aplicativo do iOS de exemplo")
![](custom-images/landscape-android.png "tela de paisagem de aplicativo Android do exemplo")
![](custom-images/landscape-uwp.png " Captura de tela do exemplo UWP aplicativo paisagem")

O número de colunas em cada linha depende do tamanho da foto, a largura da tela e o número de pixels por unidade independente de dispositivo. O [ `Image` ](xref:Xamarin.Forms.Image) elementos de forma assíncrona carregar as fotos e, portanto, o `WrapLayout` classe receberá chamadas frequentes para seus [ `LayoutChildren` ](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) método conforme cada `Image` elemento recebe um novo tamanho com base na foto carregada.

## <a name="related-links"></a>Links relacionados

- [WrapLayout (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-customlayout-wraplayout)
- [Layouts personalizados](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter26.md)
- [Criar Layouts personalizados no xamarin. Forms (vídeo)](https://evolve.xamarin.com/session/56e20f83bad314273ca4d81c)
- [T\<> de layout](xref:Xamarin.Forms.Layout`1)
- [Layout](xref:Xamarin.Forms.Layout)
- [VisualElement](xref:Xamarin.Forms.VisualElement)
