---
title: Exibições de coleção no Xamarin. iOS
description: Exibições de coleção permitem que o conteúdo seja exibido usando layouts arbitrários. Eles permitem criar facilmente layouts de grade prontos para uso, além de oferecer suporte a layouts personalizados.
ms.prod: xamarin
ms.assetid: F4B85F25-0CB5-4FEA-A3B5-D22FCDC81AE4
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: 871020ff1f69edc20b582806a584dfd51982d336
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69528602"
---
# <a name="collection-views-in-xamarinios"></a>Exibições de coleção no Xamarin. iOS

_Exibições de coleção permitem que o conteúdo seja exibido usando layouts arbitrários. Eles permitem criar facilmente layouts de grade prontos para uso, além de oferecer suporte a layouts personalizados._

Exibições de coleção, disponíveis `UICollectionView` na classe, são um novo conceito no Ios 6 que introduz a apresentação de vários itens na tela usando layouts. Os padrões para fornecer dados para um `UICollectionView` para criar itens e interagir com esses itens seguem os mesmos padrões de delegação e fonte de dados comumente usados no desenvolvimento do Ios.

No entanto, as exibições de coleção funcionam com um subsistema de `UICollectionView` layout que é independente do próprio. Portanto, simplesmente fornecer um layout diferente pode alterar facilmente a apresentação de uma exibição de coleção.

o IOS fornece uma classe de `UICollectionViewFlowLayout` layout chamada que permite que layouts baseados em linha, como uma grade, sejam criados sem trabalho adicional. Além disso, também é possível criar layouts personalizados que permitem qualquer apresentação que você possa imaginar.

## <a name="uicollectionview-basics"></a>Noções básicas do UICollectionView

A `UICollectionView` classe é composta por três itens diferentes:

- **Células** – exibições controladas por dados para cada item
- **Exibições complementares** – exibições controladas por dados associadas a uma seção.
- **Exibições de decoração** – exibições não controladas por dados criadas por um layout

## <a name="cells"></a>Células

Células são objetos que representam um único item no conjunto de dados que está sendo apresentado pelo modo de exibição de coleção. Cada célula é uma instância da `UICollectionViewCell` classe, que é composta de três exibições diferentes, conforme mostrado na figura abaixo:

 [![](uicollectionview-images/01-uicollectionviewcell.png "Cada célula é composta por três exibições diferentes, como mostrado aqui")](uicollectionview-images/01-uicollectionviewcell.png#lightbox)

A `UICollectionViewCell` classe tem as seguintes propriedades para cada uma dessas exibições:

- `ContentView`– Essa exibição contém o conteúdo que a célula apresenta. Ele é renderizado na ordem z superior na tela.
- `SelectedBackgroundView`– As células têm suporte interno para seleção. Essa exibição é usada para indicar visualmente que uma célula está selecionada. Ele é processado logo abaixo de `ContentView` quando uma célula é selecionada.
- `BackgroundView`– As células também podem exibir um plano de fundo, que é `BackgroundView` apresentado pelo. Essa exibição é renderizada sob `SelectedBackgroundView` o.


Ao definir o `ContentView` que for menor do que o `BackgroundView` e `SelectedBackgroundView`o, o `BackgroundView` pode ser usado para estruturar visualmente o conteúdo, `SelectedBackgroundView` enquanto o será exibido quando uma célula for selecionada, como mostrado abaixo:

 [![](uicollectionview-images/02-cells.png "Os elementos de célula diferentes")](uicollectionview-images/02-cells.png#lightbox)

As células na captura de tela acima são criadas herdando de `UICollectionViewCell` e definindo as `ContentView`propriedades `SelectedBackgroundView` , `BackgroundView` e, respectivamente, conforme mostrado no código a seguir:

```csharp
public class AnimalCell : UICollectionViewCell
{
        UIImageView imageView;

        [Export ("initWithFrame:")]
        public AnimalCell (CGRect frame) : base (frame)
        {
            BackgroundView = new UIView{BackgroundColor = UIColor.Orange};

            SelectedBackgroundView = new UIView{BackgroundColor = UIColor.Green};

            ContentView.Layer.BorderColor = UIColor.LightGray.CGColor;
            ContentView.Layer.BorderWidth = 2.0f;
            ContentView.BackgroundColor = UIColor.White;
            ContentView.Transform = CGAffineTransform.MakeScale (0.8f, 0.8f);

            imageView = new UIImageView (UIImage.FromBundle ("placeholder.png"));
            imageView.Center = ContentView.Center;
            imageView.Transform = CGAffineTransform.MakeScale (0.7f, 0.7f);

            ContentView.AddSubview (imageView);
        }

        public UIImage Image {
            set {
                imageView.Image = value;
            }
        }
}
```

 <a name="Supplementary_Views" />


## <a name="supplementary-views"></a>Exibições suplementares

Exibições complementares são exibições que apresentam informações associadas a cada seção `UICollectionView`de um. Como as células, exibições suplementares são controladas por dados. Quando as células apresentam os dados de item de uma fonte de dados, exibições complementares apresentam os dados da seção, como as categorias de livro em uma Bookshelf ou o gênero de música em uma biblioteca de músicas.

Por exemplo, uma exibição suplementar poderia ser usada para apresentar um cabeçalho para uma determinada seção, conforme mostrado na figura abaixo:

 [![](uicollectionview-images/02a-supplementary-view.png "Uma exibição suplementar usada para apresentar um cabeçalho para uma determinada seção, como mostrado aqui")](uicollectionview-images/02a-supplementary-view.png#lightbox)

Para usar uma exibição suplementar, primeiro ele precisa ser registrado no `ViewDidLoad` método:

```csharp
CollectionView.RegisterClassForSupplementaryView (typeof(Header), UICollectionElementKindSection.Header, headerId);
```

Em seguida, o modo de exibição precisa ser retornado `GetViewForSupplementaryElement`usando, criado `DequeueReusableSupplementaryView`usando e herda de `UICollectionReusableView`. O trecho de código a seguir produzirá o SupplementaryView mostrado na captura de tela acima:

```csharp
public override UICollectionReusableView GetViewForSupplementaryElement (UICollectionView collectionView, NSString elementKind, NSIndexPath indexPath)
        {
            var headerView = (Header)collectionView.DequeueReusableSupplementaryView (elementKind, headerId, indexPath);
            headerView.Text = "Supplementary View";
            return headerView;
        }

```

Exibições complementares são mais genéricas do que apenas cabeçalhos e rodapés.
Eles podem ser posicionados em qualquer lugar na exibição de coleção e podem ser compostos de quaisquer exibições, tornando sua aparência totalmente personalizável.

 <a name="Decoration_Views" />


## <a name="decoration-views"></a>Exibições de decoração

Exibições de decoração são puramente exibições visuais que podem ser `UICollectionView`exibidas em um. Ao contrário de células e exibições complementares, elas não são controladas por dados. Eles são sempre criados dentro da subclasse de um layout e, subsequentemente, podem ser alterados como o layout do conteúdo. Por exemplo, uma exibição de decoração poderia ser usada para apresentar uma exibição em segundo plano que rola com o conteúdo `UICollectionView`no, conforme mostrado abaixo:

 [![](uicollectionview-images/02c-decoration-view.png "Exibição de decoração com um plano de fundo vermelho")](uicollectionview-images/02c-decoration-view.png#lightbox)

 O trecho de código abaixo altera o plano de fundo para vermelho `CircleLayout` na classe Samples:

 ```csharp
 public class MyDecorationView : UICollectionReusableView
    {
        [Export ("initWithFrame:")]
        public MyDecorationView (CGRect frame) : base (frame)
        {
            BackgroundColor = UIColor.Red;
        }
    }
 ```


## <a name="data-source"></a>fonte de dados

Assim como acontece com outras partes do IOS, `UITableView` como `MKMapView`e `UICollectionView` , obtém seus dados de uma *fonte de dados*, que é exposta no Xamarin. Ios **`UICollectionViewDataSource`** por meio da classe. Essa classe é responsável por fornecer conteúdo para o `UICollectionView` , como:

- **Células** – retornadas `GetCell` do método.
- **Exibições complementares** – retornadas do `GetViewForSupplementaryElement` método.
- **Número de seções** – retornadas `NumberOfSections` do método. O padrão é 1 se não for implementado.
- **Número de itens por seção** – retornados do `GetItemsCount` método.

### <a name="uicollectionviewcontroller"></a>UICollectionViewController
Para sua conveniência, `UICollectionViewController` a classe está disponível. Isso é configurado automaticamente para ser o delegado, que é discutido na próxima seção e fonte de dados para sua `UICollectionView` exibição.

Assim como `UITableView`acontece com `UICollectionView` , a classe chamará apenas sua fonte de dados para obter células para itens que estão na tela.
As células que rolam para fora da tela são colocadas em uma fila para reutilização, como ilustra a imagem a seguir:

 [![](uicollectionview-images/03-cell-reuse.png "As células que rolam para fora na tela são colocadas em uma fila para reutilização, conforme mostrado aqui")](uicollectionview-images/03-cell-reuse.png#lightbox)

A reutilização de célula foi `UICollectionView` simplificada com e `UITableView`. Você não precisa mais criar uma célula diretamente na fonte de dados se uma não estiver disponível na fila de reutilização, pois as células são registradas com o sistema. Se uma célula não estiver disponível ao fazer a chamada para retirar da fila a célula da fila de reutilização, o iOS a criará automaticamente com base no tipo ou NIB que foi registrado.
A mesma técnica também está disponível para exibições suplementares.

Por exemplo, considere o código a seguir que registra `AnimalCell` a classe:

```csharp
static NSString animalCellId = new NSString ("AnimalCell");
CollectionView.RegisterClassForCell (typeof(AnimalCell), animalCellId);
```

Quando um `UICollectionView` precisa de uma célula porque seu item está na tela, o `UICollectionView` chama o método da `GetCell` fonte de dados. Assim como isso funciona com UITableView, esse método é responsável por configurar uma célula dos dados de backup, que seria uma `AnimalCell` classe nesse caso.

O código a seguir mostra uma implementação `GetCell` do que retorna `AnimalCell` uma instância do:

```csharp
public override UICollectionViewCell GetCell (UICollectionView collectionView, Foundation.NSIndexPath indexPath)
{
        var animalCell = (AnimalCell)collectionView.DequeueReusableCell (animalCellId, indexPath);

        var animal = animals [indexPath.Row];

        animalCell.Image = animal.Image;

        return animalCell;
}
```

A chamada para `DequeReusableCell` é onde a célula será desenfileirada da fila de reutilização ou, se uma célula não estiver disponível na fila, criada com base no tipo registrado na chamada para. `CollectionView.RegisterClassForCell`

Nesse caso, ao registrar a `AnimalCell` classe, o Ios criará um novo `AnimalCell` internamente e o retornará quando for feita uma chamada para remover a fila de uma célula, após a configuração da imagem contida na classe animal e retornada para exibição para a `UICollectionView`.

 <a name="Delegate" />


### <a name="delegate"></a>delegado

A `UICollectionView` classe usa um delegado de tipo `UICollectionViewDelegate` para dar suporte à `UICollectionView`interação com conteúdo no. Isso permite o controle de:

- **Seleção de célula** – determina se uma célula está selecionada.
- **Realce de célula** – determina se uma célula está sendo tocada no momento.
- **Menus de célula** – menu exibido para uma célula em resposta a um gesto de pressionar longo.


Assim como acontece com a fonte de `UICollectionViewController` dados, o é configurado por padrão como o delegado `UICollectionView`para o.

 <a name="Cell_HighLighting" />


#### <a name="cell-highlighting"></a>Realce de célula

Quando uma célula é pressionada, a célula faz a transição para um estado realçado e não é selecionada até que o usuário Levante o dedo da célula. Isso permite uma alteração temporária na aparência da célula antes de ser realmente selecionada. Na seleção, a célula `SelectedBackgroundView` é exibida. A figura a seguir mostra o estado realçado antes da ocorrência da seleção:

 [![](uicollectionview-images/04-cell-highlight.png "Esta figura mostra o estado realçado logo antes que a seleção ocorra")](uicollectionview-images/04-cell-highlight.png#lightbox)

Para implementar o realce, `ItemHighlighted` os `ItemUnhighlighted` métodos e do `UICollectionViewDelegate` podem ser usados. Por exemplo, o código a seguir aplicará um plano de fundo `ContentView` amarelo do quando a célula for realçada e um plano de fundo branco quando não realçado, conforme mostrado na imagem acima:

```csharp
public override void ItemHighlighted (UICollectionView collectionView, NSIndexPath indexPath)
{
        var cell = collectionView.CellForItem(indexPath);
        cell.ContentView.BackgroundColor = UIColor.Yellow;
}

public override void ItemUnhighlighted (UICollectionView collectionView, NSIndexPath indexPath)
{
        var cell = collectionView.CellForItem(indexPath);
        cell.ContentView.BackgroundColor = UIColor.White;
}
```

 <a name="Disabling_Selection" />


#### <a name="disabling-selection"></a>Desabilitar seleção

A seleção é habilitada por `UICollectionView`padrão no. Para desabilitar a seleção, `ShouldHighlightItem` substitua e retorne FALSE, conforme mostrado abaixo:

```csharp
public override bool ShouldHighlightItem (UICollectionView collectionView, NSIndexPath indexPath)
{
        return false;
}
```

Quando o realce estiver desabilitado, o processo de seleção de uma célula também será desabilitado. Além disso, também há um `ShouldSelectItem` método que controla a seleção diretamente, embora `ShouldHighlightItem` se seja implementado e retorna false `ShouldSelectItem` , não é chamado.

 `ShouldSelectItem`permite a ativação ou desativação da seleção de item por item, quando `ShouldHighlightItem` não está implementado. Ele também permite o realce sem seleção, `ShouldHighlightItem` se for implementado e retorna true, `ShouldSelectItem` enquanto retorna false.

 <a name="Cell_Menus" />


#### <a name="cell-menus"></a>Menus de célula

Cada célula em um `UICollectionView` é capaz de mostrar um menu que permite recortar, copiar e colar, opcionalmente, com suporte. Para criar um menu Editar em uma célula:

1. Substituir `ShouldShowMenu` e retornar true se o item deve mostrar um menu.
1. Substitua `CanPerformAction` e retorne verdadeiro para cada ação que o item puder executar, que será qualquer um dos comandos Recortar, copiar ou colar.
1. Substitua `PerformAction` para executar a edição e a cópia da operação de colagem.


A captura de tela a seguir mostra o menu quando uma célula é longa pressionada:

 [![](uicollectionview-images/04a-menu.png "Esta captura de tela mostra o menu quando uma célula é longa pressionada")](uicollectionview-images/04a-menu.png#lightbox)

 <a name="Layout" />


## <a name="layout"></a>Layout

`UICollectionView`dá suporte a um sistema de layout que permite que o posicionamento de todos os seus elementos, células, exibições suplementares e exibições de `UICollectionView` decoração seja gerenciado independentemente do próprio.
Usando o sistema de layout, um aplicativo pode dar suporte a layouts como a grade que vimos neste artigo, além de fornecer layouts personalizados.

 <a name="Layout_Basics" />


### <a name="layout-basics"></a>Noções básicas de layout

Layouts em um `UICollectionView` são definidos em uma classe que herda de `UICollectionViewLayout`. A implementação de layout é responsável pela criação dos atributos de layout para cada item `UICollectionView`no. Há duas maneiras de criar um layout:

- Use o interno `UICollectionViewFlowLayout` .
- Forneça um layout personalizado herdando de `UICollectionViewLayout` .


 <a name="Flow_Layout" />


### <a name="flow-layout"></a>Layout de fluxo

A `UICollectionViewFlowLayout` classe fornece um layout baseado em linha adequado para organizar o conteúdo em uma grade de células como vimos.

Para usar um layout de fluxo:

- Criar uma instância do `UICollectionViewFlowLayout` :


```csharp
var layout = new UICollectionViewFlowLayout ();
```

- Passe a instância para o construtor do `UICollectionView` :


```csharp
simpleCollectionViewController = new SimpleCollectionViewController (layout);
```

Isso é tudo o que é necessário para o layout de conteúdo em uma grade. Além disso, quando a orientação é alterada `UICollectionViewFlowLayout` , os identificadores reorganizam o conteúdo adequadamente, como mostrado abaixo:

 [![](uicollectionview-images/05-layout-orientation.png "Exemplo de alterações de orientação")](uicollectionview-images/05-layout-orientation.png#lightbox)

 <a name="Section_Inset" />


#### <a name="section-inset"></a>Inserção de seção

Para fornecer algum espaço em volta `UIContentView`do, os layouts `SectionInset` têm uma propriedade `UIEdgeInsets`do tipo. Por exemplo, o código a seguir fornece um buffer de 50 pixels em volta de cada `UIContentView` seção do quando apresentado por `UICollectionViewFlowLayout`um:

```csharp
var layout = new UICollectionViewFlowLayout ();
layout.SectionInset = new UIEdgeInsets (50,50,50,50);
```

Isso resulta em espaçamento em volta da seção, como mostrado abaixo:

 [![](uicollectionview-images/06-sectioninset.png "Espaçamento em volta da seção, como mostrado aqui")](uicollectionview-images/06-sectioninset.png#lightbox)

 <a name="Subclassing_UICollectionViewFlowLayout" />


#### <a name="subclassing-uicollectionviewflowlayout"></a>UICollectionViewFlowLayout de subclasse

Na edição para usar `UICollectionViewFlowLayout` diretamente, ele também pode ser subclasse para personalizar ainda mais o layout do conteúdo ao longo de uma linha. Por exemplo, isso pode ser usado para criar um layout que não encapsula as células em uma grade, mas, em vez disso, cria uma única linha com um efeito de rolagem horizontal, como mostrado abaixo:

 [![](uicollectionview-images/07-line-layout.png "Uma única linha com um efeito de rolagem horizontal")](uicollectionview-images/07-line-layout.png#lightbox)

Para implementar isso, a subclasse `UICollectionViewFlowLayout` requer:

- Inicializar todas as propriedades de layout que se aplicam ao próprio layout ou a todos os itens no layout no construtor.
- Substituindo `ShouldInvalidateLayoutForBoundsChange` , retornando true para que, quando os `UICollectionView` limites das alterações, o layout das células seja recalculado. Isso é usado nesse caso, verifique se o código da transformação aplicada à célula centermost será aplicado durante a rolagem.
- Substituindo `TargetContentOffset` para fazer com que a célula centermost se encaixe no `UICollectionView` centro do as paradas de rolagem.
- Substituindo `LayoutAttributesForElementsInRect` para retornar uma matriz `UICollectionViewLayoutAttributes` de. Cada `UICollectionViewLayoutAttribute` contém informações sobre como fazer o layout de um item específico, incluindo propriedades como `Center` , `Size` `ZIndex` e `Transform3D` .


O código a seguir mostra uma implementação desse tipo:

```csharp
using System;
using CoreGraphics;
using Foundation;
using UIKit;
using CoreGraphics;
using CoreAnimation;

namespace SimpleCollectionView
{
    public class LineLayout : UICollectionViewFlowLayout
    {
        public const float ITEM_SIZE = 200.0f;
        public const int ACTIVE_DISTANCE = 200;
        public const float ZOOM_FACTOR = 0.3f;

        public LineLayout ()
        {
            ItemSize = new CGSize (ITEM_SIZE, ITEM_SIZE);
            ScrollDirection = UICollectionViewScrollDirection.Horizontal;
            SectionInset = new UIEdgeInsets (400,0,400,0);
            MinimumLineSpacing = 50.0f;
        }

        public override bool ShouldInvalidateLayoutForBoundsChange (CGRect newBounds)
        {
            return true;
        }

        public override UICollectionViewLayoutAttributes[] LayoutAttributesForElementsInRect (CGRect rect)
        {
            var array = base.LayoutAttributesForElementsInRect (rect);
            var visibleRect = new CGRect (CollectionView.ContentOffset, CollectionView.Bounds.Size);

            foreach (var attributes in array) {
                if (attributes.Frame.IntersectsWith (rect)) {
                    float distance = (float)(visibleRect.GetMidX () - attributes.Center.X);
                    float normalizedDistance = distance / ACTIVE_DISTANCE;
                    if (Math.Abs (distance) < ACTIVE_DISTANCE) {
                        float zoom = 1 + ZOOM_FACTOR * (1 - Math.Abs (normalizedDistance));
                        attributes.Transform3D = CATransform3D.MakeScale (zoom, zoom, 1.0f);
                        attributes.ZIndex = 1;
                    }
                }
            }
            return array;
        }

        public override CGPoint TargetContentOffset (CGPoint proposedContentOffset, CGPoint scrollingVelocity)
        {
            float offSetAdjustment = float.MaxValue;
            float horizontalCenter = (float)(proposedContentOffset.X + (this.CollectionView.Bounds.Size.Width / 2.0));
            CGRect targetRect = new CGRect (proposedContentOffset.X, 0.0f, this.CollectionView.Bounds.Size.Width, this.CollectionView.Bounds.Size.Height);
            var array = base.LayoutAttributesForElementsInRect (targetRect);
            foreach (var layoutAttributes in array) {
                float itemHorizontalCenter = (float)layoutAttributes.Center.X;
                if (Math.Abs (itemHorizontalCenter - horizontalCenter) < Math.Abs (offSetAdjustment)) {
                    offSetAdjustment = itemHorizontalCenter - horizontalCenter;
                }
            }
            return new CGPoint (proposedContentOffset.X + offSetAdjustment, proposedContentOffset.Y);
        }

    }
}
```

 <a name="Custom_Layout" />


### <a name="custom-layout"></a>Layout personalizado

Além de usar `UICollectionViewFlowLayout`, os layouts também podem ser totalmente personalizados pela herança diretamente do `UICollectionViewLayout`.

Os principais métodos a serem substituídos são:

- `PrepareLayout`– Usado para executar cálculos geométricos iniciais que serão usados em todo o processo de layout.
- `CollectionViewContentSize`– Retorna o tamanho da área usada para exibir o conteúdo.
- `LayoutAttributesForElementsInRect`– Assim como com o exemplo de UICollectionViewFlowLayout mostrado anteriormente, esse método é usado para fornecer informações `UICollectionView` para o quanto ao layout de cada item. No entanto, `UICollectionViewFlowLayout` ao contrário do, ao criar um layout personalizado, você pode posicionar itens, no entanto, escolher.


Por exemplo, o mesmo conteúdo pode ser apresentado em um layout circular, conforme mostrado abaixo:

 [![](uicollectionview-images/08-circle-layout.png "Um layout personalizado circular, como mostrado aqui")](uicollectionview-images/08-circle-layout.png#lightbox)

O poderoso elemento sobre layouts é que, para alterar do layout de grade, para um layout de rolagem horizontal, e subsequentemente para esse layout circular, só `UICollectionView` é necessário que a classe de layout fornecida seja alterada. Nada no `UICollectionView`, seu delegado ou código da fonte de dados é alterado de forma alguma.


## <a name="changes-in-ios-9"></a>Alterações no iOS 9


No Ios 9, o modo de exibição`UICollectionView`de coleção () agora dá suporte à reordenação de itens prontos para uso, adicionando um novo reconhecedor de gestos padrão e vários novos métodos de suporte.

Usando esses novos métodos, você pode implementar facilmente arrastar para reordenar em seu modo de exibição de coleção e ter a opção de personalizar a aparência dos itens durante qualquer estágio do processo de reordenação.

[![](uicollectionview-images/intro01.png "Um exemplo do processo de reordenação")](uicollectionview-images/intro01.png#lightbox)

Neste artigo, vamos dar uma olhada na implementação de arrastar para reordenar em um aplicativo Xamarin. iOS, bem como algumas das outras alterações que o iOS 9 fez no controle de modo de exibição de coleção:

- [Reordenação fácil de itens](#Easy-Reordering-of-Items)
    - [Exemplo de reordenação simples](#Simple-Reordering-Example)
    - [Usando um reconhecedor de gestor personalizado](#Using-a-Custom-Gesture-Recognizer)
    - [Layouts e reordenação personalizados](#Custom-Layouts-and-Reording)
- [Alterações de exibição de coleção](#collection-view-changes)

<a name="Easy-Reordering-of-Items" />

## <a name="reordering-of-items"></a>Reordenação de itens

Como mencionado acima, uma das alterações mais significativas no modo de exibição de coleção no iOS 9 foi a adição de uma funcionalidade fácil de arrastar para reordenar pronta para uso.

No iOS 9, a maneira mais rápida de adicionar reordenação a uma exibição de coleção é usar um `UICollectionViewController`.
O controlador de exibição de coleção agora `InstallsStandardGestureForInteractiveMovement` tem uma propriedade, que adiciona um reconhecedor de *gestos* padrão que oferece suporte ao arrastar para reordenar itens na coleção.
Como o valor padrão é `true`, você só precisa implementar o `MoveItem` método da `UICollectionViewDataSource` classe para dar suporte a "arrastar para reordenar". Por exemplo:

```csharp
public override void MoveItem (UICollectionView collectionView, NSIndexPath sourceIndexPath, NSIndexPath destinationIndexPath)
{
    // Reorder our list of items
    ...
}
```
<a name="Simple-Reordering-Example" />

### <a name="simple-reordering-example"></a>Exemplo de reordenação simples

Como um exemplo rápido, inicie um novo projeto Xamarin. iOS e edite o arquivo **Main. Storyboard** . Arraste um `UICollectionViewController` para a superfície de design:

[![](uicollectionview-images/quick01.png "Adicionando um UICollectionViewController")](uicollectionview-images/quick01.png#lightbox)

Selecione o modo de exibição de coleção (pode ser mais fácil fazer isso na estrutura de tópicos do documento). Na guia layout da Painel de Propriedades, defina os seguintes tamanhos, conforme ilustrado na captura de tela abaixo:

- **Tamanho da célula**: Largura – 60 | Altura – 60
- **Tamanho do cabeçalho**: Largura – 0 | Altura – 0
- **Tamanho do rodapé**: Largura – 0 | Altura – 0
- **Espaçamento mínimo**: Para as células – 8 | Para linhas – 8
- **Indefinições de seção**: Top – 16 | Inferior – 16 | Esquerda – 16 | Direita – 16

[![](uicollectionview-images/quick04.png "Definir os tamanhos de exibição de coleção")](uicollectionview-images/quick04.png#lightbox)

Em seguida, edite a célula padrão:
    - Alterar a cor do plano de fundo para azul
    - Adicionar um rótulo para atuar como o título da célula
    - Definir o identificador de reutilização para a **célula**

[![](uicollectionview-images/quick02.png "Editar a célula padrão")](uicollectionview-images/quick02.png#lightbox)

Adicione restrições para manter o rótulo centralizado dentro da célula à medida que ele muda de tamanho:

No **painel de propriedades** para o _CollectionViewCell_ e defina a **classe** como `TextCollectionViewCell`:

[![](uicollectionview-images/quick05.png "Definir a classe como TextCollectionViewCell")](uicollectionview-images/quick05.png#lightbox)

Defina a **exibição** reutilizável da `Cell`coleção como:

[![](uicollectionview-images/quick06.png "Definir a exibição reutilizável da coleção como célula")](uicollectionview-images/quick06.png#lightbox)

Por fim, selecione o rótulo e nomeie `TextLabel`-o:

[![](uicollectionview-images/quick07.png "rótulo de nome TextLabel")](uicollectionview-images/quick07.png#lightbox)

Edite `TextCollectionViewCell` a classe e adicione as seguintes propriedades:

```csharp
using System;
using Foundation;
using UIKit;

namespace CollectionView
{
    public partial class TextCollectionViewCell : UICollectionViewCell
    {
        #region Computed Properties
        public string Title {
            get { return TextLabel.Text; }
            set { TextLabel.Text = value; }
        }
        #endregion

        #region Constructors
        public TextCollectionViewCell (IntPtr handle) : base (handle)
        {
        }
        #endregion
    }
}
```

Aqui, `Text` a propriedade do rótulo é exposta como o título da célula, para que possa ser definida a partir do código.

Adicione uma nova C# classe ao projeto e chame- `WaterfallCollectionSource`a. Edite o arquivo e faça com que ele se pareça com o seguinte:

```csharp
using System;
using Foundation;
using UIKit;
using System.Collections.Generic;

namespace CollectionView
{
    public class WaterfallCollectionSource : UICollectionViewDataSource
    {
        #region Computed Properties
        public WaterfallCollectionView CollectionView { get; set;}
        public List<int> Numbers { get; set; } = new List<int> ();
        #endregion

        #region Constructors
        public WaterfallCollectionSource (WaterfallCollectionView collectionView)
        {
            // Initialize
            CollectionView = collectionView;

            // Init numbers collection
            for (int n = 0; n < 100; ++n) {
                Numbers.Add (n);
            }
        }
        #endregion

        #region Override Methods
        public override nint NumberOfSections (UICollectionView collectionView) {
            // We only have one section
            return 1;
        }

        public override nint GetItemsCount (UICollectionView collectionView, nint section) {
            // Return the number of items
            return Numbers.Count;
        }

        public override UICollectionViewCell GetCell (UICollectionView collectionView, NSIndexPath indexPath)
        {
            // Get a reusable cell and set {~~it's~>its~~} title from the item
            var cell = collectionView.DequeueReusableCell ("Cell", indexPath) as TextCollectionViewCell;
            cell.Title = Numbers [(int)indexPath.Item].ToString();

            return cell;
        }

        public override bool CanMoveItem (UICollectionView collectionView, NSIndexPath indexPath) {
            // We can always move items
            return true;
        }

        public override void MoveItem (UICollectionView collectionView, NSIndexPath sourceIndexPath, NSIndexPath destinationIndexPath)
        {
            // Reorder our list of items
            var item = Numbers [(int)sourceIndexPath.Item];
            Numbers.RemoveAt ((int)sourceIndexPath.Item);
            Numbers.Insert ((int)destinationIndexPath.Item, item);
        }
        #endregion
    }
}
```

Essa classe será a fonte de dados para nossa exibição de coleção e fornecerá as informações para cada célula na coleção.
Observe que o `MoveItem` método é implementado para permitir que os itens na coleção sejam arrastados reordenados.

Adicione outra classe C# nova ao projeto e chame- `WaterfallCollectionDelegate`a. Edite esse arquivo e faça com que ele fique semelhante ao seguinte:

```csharp
using System;
using Foundation;
using UIKit;
using System.Collections.Generic;

namespace CollectionView
{
    public class WaterfallCollectionDelegate : UICollectionViewDelegate
    {
        #region Computed Properties
        public WaterfallCollectionView CollectionView { get; set;}
        #endregion

        #region Constructors
        public WaterfallCollectionDelegate (WaterfallCollectionView collectionView)
        {

            // Initialize
            CollectionView = collectionView;

        }
        #endregion

        #region Overrides Methods
        public override bool ShouldHighlightItem (UICollectionView collectionView, NSIndexPath indexPath) {
            // Always allow for highlighting
            return true;
        }

        public override void ItemHighlighted (UICollectionView collectionView, NSIndexPath indexPath)
        {
            // Get cell and change to green background
            var cell = collectionView.CellForItem(indexPath);
            cell.ContentView.BackgroundColor = UIColor.FromRGB(183,208,57);
        }

        public override void ItemUnhighlighted (UICollectionView collectionView, NSIndexPath indexPath)
        {
            // Get cell and return to blue background
            var cell = collectionView.CellForItem(indexPath);
            cell.ContentView.BackgroundColor = UIColor.FromRGB(164,205,255);
        }
        #endregion
    }
}
```

Isso atuará como o delegado para nossa exibição de coleção. Os métodos foram substituídos para realçar uma célula enquanto o usuário interage com ela no modo de exibição de coleção.

Adicione uma última C# classe ao projeto e chame- `WaterfallCollectionView`a. Edite esse arquivo e faça com que ele fique semelhante ao seguinte:

```csharp
using System;
using UIKit;
using System.Collections.Generic;
using Foundation;

namespace CollectionView
{
    [Register("WaterfallCollectionView")]
    public class WaterfallCollectionView : UICollectionView
    {

        #region Constructors
        public WaterfallCollectionView (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();

            // Initialize
            DataSource = new WaterfallCollectionSource(this);
            Delegate = new WaterfallCollectionDelegate(this);

        }
        #endregion
    }
}
```

Observe que `DataSource` e `Delegate` que criamos acima são definidos quando o modo de exibição de coleção é construído a partir de seu Storyboard (ou arquivo **. xib** ).

Edite o arquivo **Main. Storyboard** novamente e selecione o modo de exibição de coleção e alterne para as **Propriedades**. Defina a **classe** como a classe `WaterfallCollectionView` personalizada que definimos acima:

Salve as alterações feitas na interface do usuário e execute o aplicativo.
Se o usuário selecionar um item da lista e arrastá-lo para um novo local, os outros itens serão animados automaticamente à medida que forem movidos para fora do caminho do item.
Quando o usuário soltar o item em um novo local, ele ficará nesse local. Por exemplo:

[![](uicollectionview-images/intro01.png "Um exemplo de como arrastar um item para um novo local")](uicollectionview-images/intro01.png#lightbox)

<a name="Using-a-Custom-Gesture-Recognizer" />

### <a name="using-a-custom-gesture-recognizer"></a>Usando um reconhecedor de gestor personalizado

Nos casos em que você não pode `UICollectionViewController` usar um e deve usar `UIViewController`um regular, ou se desejar obter mais controle sobre o gesto de arrastar e soltar, você pode criar seu próprio reconhecedor de gestos personalizado e adicioná-lo à exibição de coleção quando o modo de exibição for carregado. Por exemplo:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Create a custom gesture recognizer
    var longPressGesture = new UILongPressGestureRecognizer ((gesture) => {

        // Take action based on state
        switch(gesture.State) {
        case UIGestureRecognizerState.Began:
            var selectedIndexPath = CollectionView.IndexPathForItemAtPoint(gesture.LocationInView(View));
            if (selectedIndexPath !=null) {
                CollectionView.BeginInteractiveMovementForItem(selectedIndexPath);
            }
            break;
        case UIGestureRecognizerState.Changed:
            CollectionView.UpdateInteractiveMovementTargetPosition(gesture.LocationInView(View));
            break;
        case UIGestureRecognizerState.Ended:
            CollectionView.EndInteractiveMovement();
            break;
        default:
            CollectionView.CancelInteractiveMovement();
            break;
        }

    });

    // Add the custom recognizer to the collection view
    CollectionView.AddGestureRecognizer(longPressGesture);
}
```

Aqui estamos usando vários novos métodos adicionados à exibição de coleção para implementar e controlar a operação de arrastar:

- `BeginInteractiveMovementForItem`-Marca o início de uma operação de movimentação.
- `UpdateInteractiveMovementTargetPosition`-É enviado à medida que o local do item é atualizado.
- `EndInteractiveMovement`-Marca o final de uma movimentação de item.
- `CancelInteractiveMovement`-Marca o usuário Cancelando a operação de movimentação.

Quando o aplicativo for executado, a operação de arrastar funcionará exatamente como o reconhecedor de gestos de arrastar padrão que vem com o modo de exibição de coleção.

<a name="Custom-Layouts-and-Reording" />

### <a name="custom-layouts-and-reordering"></a>Layouts e reordenação personalizados

No iOS 9, vários novos métodos foram adicionados para trabalhar com layouts de arrastar para reordenação e personalizados em uma exibição de coleção. Para explorar esse recurso, vamos adicionar um layout personalizado à coleção.

Primeiro, adicione uma nova C# classe chamada `WaterfallCollectionLayout` ao projeto. Edite-o e faça com que ele se pareça com o seguinte:

```csharp
using System;
using Foundation;
using UIKit;
using System.Collections.Generic;
using CoreGraphics;

namespace CollectionView
{
    [Register("WaterfallCollectionLayout")]
    public class WaterfallCollectionLayout : UICollectionViewLayout
    {
        #region Private Variables
        private int columnCount = 2;
        private nfloat minimumColumnSpacing = 10;
        private nfloat minimumInterItemSpacing = 10;
        private nfloat headerHeight = 0.0f;
        private nfloat footerHeight = 0.0f;
        private UIEdgeInsets sectionInset = new UIEdgeInsets(0, 0, 0, 0);
        private WaterfallCollectionRenderDirection itemRenderDirection = WaterfallCollectionRenderDirection.ShortestFirst;
        private Dictionary<nint,UICollectionViewLayoutAttributes> headersAttributes = new Dictionary<nint, UICollectionViewLayoutAttributes>();
        private Dictionary<nint,UICollectionViewLayoutAttributes> footersAttributes = new Dictionary<nint, UICollectionViewLayoutAttributes>();
        private List<CGRect> unionRects = new List<CGRect>();
        private List<nfloat> columnHeights = new List<nfloat>();
        private List<UICollectionViewLayoutAttributes> allItemAttributes = new List<UICollectionViewLayoutAttributes>();
        private List<List<UICollectionViewLayoutAttributes>> sectionItemAttributes = new List<List<UICollectionViewLayoutAttributes>>();
        private nfloat unionSize = 20;
        #endregion

        #region Computed Properties
        [Export("ColumnCount")]
        public int ColumnCount {
            get { return columnCount; }
            set {
                WillChangeValue ("ColumnCount");
                columnCount = value;
                DidChangeValue ("ColumnCount");

                InvalidateLayout ();
            }
        }

        [Export("MinimumColumnSpacing")]
        public nfloat MinimumColumnSpacing {
            get { return minimumColumnSpacing; }
            set {
                WillChangeValue ("MinimumColumnSpacing");
                minimumColumnSpacing = value;
                DidChangeValue ("MinimumColumnSpacing");

                InvalidateLayout ();
            }
        }

        [Export("MinimumInterItemSpacing")]
        public nfloat MinimumInterItemSpacing {
            get { return minimumInterItemSpacing; }
            set {
                WillChangeValue ("MinimumInterItemSpacing");
                minimumInterItemSpacing = value;
                DidChangeValue ("MinimumInterItemSpacing");

                InvalidateLayout ();
            }
        }

        [Export("HeaderHeight")]
        public nfloat HeaderHeight {
            get { return headerHeight; }
            set {
                WillChangeValue ("HeaderHeight");
                headerHeight = value;
                DidChangeValue ("HeaderHeight");

                InvalidateLayout ();
            }
        }

        [Export("FooterHeight")]
        public nfloat FooterHeight {
            get { return footerHeight; }
            set {
                WillChangeValue ("FooterHeight");
                footerHeight = value;
                DidChangeValue ("FooterHeight");

                InvalidateLayout ();
            }
        }

        [Export("SectionInset")]
        public UIEdgeInsets SectionInset {
            get { return sectionInset; }
            set {
                WillChangeValue ("SectionInset");
                sectionInset = value;
                DidChangeValue ("SectionInset");

                InvalidateLayout ();
            }
        }

        [Export("ItemRenderDirection")]
        public WaterfallCollectionRenderDirection ItemRenderDirection {
            get { return itemRenderDirection; }
            set {
                WillChangeValue ("ItemRenderDirection");
                itemRenderDirection = value;
                DidChangeValue ("ItemRenderDirection");

                InvalidateLayout ();
            }
        }
        #endregion

        #region Constructors
        public WaterfallCollectionLayout ()
        {
        }

        public WaterfallCollectionLayout(NSCoder coder) : base(coder) {

        }
        #endregion

        #region Public Methods
        public nfloat ItemWidthInSectionAtIndex(int section) {

            var width = CollectionView.Bounds.Width - SectionInset.Left - SectionInset.Right;
            return (nfloat)Math.Floor ((width - ((ColumnCount - 1) * MinimumColumnSpacing)) / ColumnCount);
        }
        #endregion

        #region Override Methods
        public override void PrepareLayout ()
        {
            base.PrepareLayout ();

            // Get the number of sections
            var numberofSections = CollectionView.NumberOfSections();
            if (numberofSections == 0)
                return;

            // Reset collections
            headersAttributes.Clear ();
            footersAttributes.Clear ();
            unionRects.Clear ();
            columnHeights.Clear ();
            allItemAttributes.Clear ();
            sectionItemAttributes.Clear ();

            // Initialize column heights
            for (int n = 0; n < ColumnCount; n++) {
                columnHeights.Add ((nfloat)0);
            }

            // Process all sections
            nfloat top = 0.0f;
            var attributes = new UICollectionViewLayoutAttributes ();
            var columnIndex = 0;
            for (nint section = 0; section < numberofSections; ++section) {
                // Calculate section specific metrics
                var minimumInterItemSpacing = (MinimumInterItemSpacingForSection == null) ? MinimumColumnSpacing :
                    MinimumInterItemSpacingForSection (CollectionView, this, section);

                // Calculate widths
                var width = CollectionView.Bounds.Width - SectionInset.Left - SectionInset.Right;
                var itemWidth = (nfloat)Math.Floor ((width - ((ColumnCount - 1) * MinimumColumnSpacing)) / ColumnCount);

                // Calculate section header
                var heightHeader = (HeightForHeader == null) ? HeaderHeight :
                    HeightForHeader (CollectionView, this, section);

                if (heightHeader > 0) {
                    attributes = UICollectionViewLayoutAttributes.CreateForSupplementaryView (UICollectionElementKindSection.Header, NSIndexPath.FromRowSection (0, section));
                    attributes.Frame = new CGRect (0, top, CollectionView.Bounds.Width, heightHeader);
                    headersAttributes.Add (section, attributes);
                    allItemAttributes.Add (attributes);

                    top = attributes.Frame.GetMaxY ();
                }

                top += SectionInset.Top;
                for (int n = 0; n < ColumnCount; n++) {
                    columnHeights [n] = top;
                }

                // Calculate Section Items
                var itemCount = CollectionView.NumberOfItemsInSection(section);
                List<UICollectionViewLayoutAttributes> itemAttributes = new List<UICollectionViewLayoutAttributes> ();

                for (nint n = 0; n < itemCount; n++) {
                    var indexPath = NSIndexPath.FromRowSection (n, section);
                    columnIndex = NextColumnIndexForItem (n);
                    var xOffset = SectionInset.Left + (itemWidth + MinimumColumnSpacing) * (nfloat)columnIndex;
                    var yOffset = columnHeights [columnIndex];
                    var itemSize = (SizeForItem == null) ? new CGSize (0, 0) : SizeForItem (CollectionView, this, indexPath);
                    nfloat itemHeight = 0.0f;

                    if (itemSize.Height > 0.0f && itemSize.Width > 0.0f) {
                        itemHeight = (nfloat)Math.Floor (itemSize.Height * itemWidth / itemSize.Width);
                    }

                    attributes = UICollectionViewLayoutAttributes.CreateForCell (indexPath);
                    attributes.Frame = new CGRect (xOffset, yOffset, itemWidth, itemHeight);
                    itemAttributes.Add (attributes);
                    allItemAttributes.Add (attributes);
                    columnHeights [columnIndex] = attributes.Frame.GetMaxY () + MinimumInterItemSpacing;
                }
                sectionItemAttributes.Add (itemAttributes);

                // Calculate Section Footer
                nfloat footerHeight = 0.0f;
                columnIndex = LongestColumnIndex();
                top = columnHeights [columnIndex] - MinimumInterItemSpacing + SectionInset.Bottom;
                footerHeight = (HeightForFooter == null) ? FooterHeight : HeightForFooter(CollectionView, this, section);

                if (footerHeight > 0) {
                    attributes = UICollectionViewLayoutAttributes.CreateForSupplementaryView (UICollectionElementKindSection.Footer, NSIndexPath.FromRowSection (0, section));
                    attributes.Frame = new CGRect (0, top, CollectionView.Bounds.Width, footerHeight);
                    footersAttributes.Add (section, attributes);
                    allItemAttributes.Add (attributes);
                    top = attributes.Frame.GetMaxY ();
                }

                for (int n = 0; n < ColumnCount; n++) {
                    columnHeights [n] = top;
                }
            }

            var i =0;
            var attrs = allItemAttributes.Count;
            while(i < attrs) {
                var rect1 = allItemAttributes [i].Frame;
                i = (int)Math.Min (i + unionSize, attrs) - 1;
                var rect2 = allItemAttributes [i].Frame;
                unionRects.Add (CGRect.Union (rect1, rect2));
                i++;
            }

        }

        public override CGSize CollectionViewContentSize {
            get {
                if (CollectionView.NumberOfSections () == 0) {
                    return new CGSize (0, 0);
                }

                var contentSize = CollectionView.Bounds.Size;
                contentSize.Height = columnHeights [0];
                return contentSize;
            }
        }

        public override UICollectionViewLayoutAttributes LayoutAttributesForItem (NSIndexPath indexPath)
        {
            if (indexPath.Section >= sectionItemAttributes.Count) {
                return null;
            }

            if (indexPath.Item >= sectionItemAttributes [indexPath.Section].Count) {
                return null;
            }

            var list = sectionItemAttributes [indexPath.Section];
            return list [(int)indexPath.Item];
        }

        public override UICollectionViewLayoutAttributes LayoutAttributesForSupplementaryView (NSString kind, NSIndexPath indexPath)
        {
            var attributes = new UICollectionViewLayoutAttributes ();

            switch (kind) {
            case "header":
                attributes = headersAttributes [indexPath.Section];
                break;
            case "footer":
                attributes = footersAttributes [indexPath.Section];
                break;
            }

            return attributes;
        }

        public override UICollectionViewLayoutAttributes[] LayoutAttributesForElementsInRect (CGRect rect)
        {
            var begin = 0;
            var end = unionRects.Count;
            List<UICollectionViewLayoutAttributes> attrs = new List<UICollectionViewLayoutAttributes> ();


            for (int i = 0; i < end; i++) {
                if (rect.IntersectsWith(unionRects[i])) {
                    begin = i * (int)unionSize;
                }
            }

            for (int i = end - 1; i >= 0; i--) {
                if (rect.IntersectsWith (unionRects [i])) {
                    end = (int)Math.Min ((i + 1) * (int)unionSize, allItemAttributes.Count);
                    break;
                }
            }

            for (int i = begin; i < end; i++) {
                var attr = allItemAttributes [i];
                if (rect.IntersectsWith (attr.Frame)) {
                    attrs.Add (attr);
                }
            }

            return attrs.ToArray();
        }

        public override bool ShouldInvalidateLayoutForBoundsChange (CGRect newBounds)
        {
            var oldBounds = CollectionView.Bounds;
            return (newBounds.Width != oldBounds.Width);
        }
        #endregion

        #region Private Methods
        private int ShortestColumnIndex() {
            var index = 0;
            var shortestHeight = nfloat.MaxValue;
            var n = 0;

            // Scan each column for the shortest height
            foreach (nfloat height in columnHeights) {
                if (height < shortestHeight) {
                    shortestHeight = height;
                    index = n;
                }
                ++n;
            }

            return index;
        }

        private int LongestColumnIndex() {
            var index = 0;
            var longestHeight = nfloat.MinValue;
            var n = 0;

            // Scan each column for the shortest height
            foreach (nfloat height in columnHeights) {
                if (height > longestHeight) {
                    longestHeight = height;
                    index = n;
                }
                ++n;
            }

            return index;
        }

        private int NextColumnIndexForItem(nint item) {
            var index = 0;

            switch (ItemRenderDirection) {
            case WaterfallCollectionRenderDirection.ShortestFirst:
                index = ShortestColumnIndex ();
                break;
            case WaterfallCollectionRenderDirection.LeftToRight:
                index = ColumnCount;
                break;
            case WaterfallCollectionRenderDirection.RightToLeft:
                index = (ColumnCount - 1) - ((int)item / ColumnCount);
                break;
            }

            return index;
        }
        #endregion

        #region Events
        public delegate CGSize WaterfallCollectionSizeDelegate(UICollectionView collectionView, WaterfallCollectionLayout layout, NSIndexPath indexPath);
        public delegate nfloat WaterfallCollectionFloatDelegate(UICollectionView collectionView, WaterfallCollectionLayout layout, nint section);
        public delegate UIEdgeInsets WaterfallCollectionEdgeInsetsDelegate(UICollectionView collectionView, WaterfallCollectionLayout layout, nint section);

        public event WaterfallCollectionSizeDelegate SizeForItem;
        public event WaterfallCollectionFloatDelegate HeightForHeader;
        public event WaterfallCollectionFloatDelegate HeightForFooter;
        public event WaterfallCollectionEdgeInsetsDelegate InsetForSection;
        public event WaterfallCollectionFloatDelegate MinimumInterItemSpacingForSection;
        #endregion
    }
}
```

Essa classe pode ser usada para fornecer um layout de tipo de duas colunas e de cascata personalizada para o modo de exibição de coleção.
O código usa codificação de chave-valor (por `WillChangeValue` meio `DidChangeValue` dos métodos e) para fornecer vinculação de dados para nossas propriedades computadas nessa classe.

Em seguida, edite o `WaterfallCollectionSource` e faça as seguintes alterações e inclusões:

```csharp
private Random rnd = new Random();
...

public List<nfloat> Heights { get; set; } = new List<nfloat> ();
...

public WaterfallCollectionSource (WaterfallCollectionView collectionView)
{
    // Initialize
    CollectionView = collectionView;

    // Init numbers collection
    for (int n = 0; n < 100; ++n) {
        Numbers.Add (n);
        Heights.Add (rnd.Next (0, 100) + 40.0f);
    }
}
```

Isso criará uma altura aleatória para cada um dos itens que serão exibidos na lista.

Em seguida, edite a `WaterfallCollectionView` classe e adicione a seguinte propriedade auxiliar:

```csharp
public WaterfallCollectionSource Source {
    get { return (WaterfallCollectionSource)DataSource; }
}
```

Isso facilitará a obtenção de nossa fonte de dados (e as alturas de item) do layout personalizado.

Por fim, edite o controlador de exibição e adicione o seguinte código:

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    var waterfallLayout = new WaterfallCollectionLayout ();

    // Wireup events
    waterfallLayout.SizeForItem += (collectionView, layout, indexPath) => {
        var collection = collectionView as WaterfallCollectionView;
        return new CGSize((View.Bounds.Width-40)/3,collection.Source.Heights[(int)indexPath.Item]);
    };

    // Attach the custom layout to the collection
    CollectionView.SetCollectionViewLayout(waterfallLayout, false);
}
```

Isso cria uma instância do nosso layout personalizado, define o evento para fornecer o tamanho de cada item e anexa o novo layout à nossa exibição de coleção.

Se executarmos o aplicativo Xamarin. iOS novamente, o modo de exibição de coleção agora será semelhante ao seguinte:

[![](uicollectionview-images/custom01.png "A exibição de coleção agora terá esta aparência")](uicollectionview-images/custom01.png#lightbox)

Ainda podemos arrastar para reordenar os itens como antes, mas os itens agora mudarão de tamanho para se ajustarem ao seu novo local quando forem descartados.

## <a name="collection-view-changes"></a>Alterações de exibição de coleção

Nas seções a seguir, examinaremos detalhadamente as alterações feitas em cada classe da exibição de coleção pelo iOS 9.

### <a name="uicollectionview"></a>UICollectionView

As seguintes alterações ou adições foram feitas `UICollectionView` na classe para IOS 9:

- `BeginInteractiveMovementForItem`– Marca o início de uma operação de arrastar.
- `CancelInteractiveMovement`– Informa ao modo de exibição de coleção que o usuário cancelou uma operação de arrastar.
- `EndInteractiveMovement`– Informa ao modo de exibição de coleção que o usuário concluiu uma operação de arrastar.
- `GetIndexPathsForVisibleSupplementaryElements`– Retorna o `indexPath` de um cabeçalho ou rodapé em uma seção de exibição de coleção.
- `GetSupplementaryView`– Retorna o cabeçalho ou o rodapé fornecido.
- `GetVisibleSupplementaryViews`– Retorna uma lista de todos os cabeçalhos e rodapés visíveis.
- `UpdateInteractiveMovementTargetPosition`– Informa o modo de exibição de coleção que o usuário moveu ou está movendo um item durante uma operação de arrastar.

### <a name="uicollectionviewcontroller"></a>UICollectionViewController

As seguintes alterações ou adições foram feitas `UICollectionViewController` na classe no Ios 9:

- `InstallsStandardGestureForInteractiveMovement`– Se `true` o novo reconhecedor de gestos que dá suporte automaticamente ao arrastar para a reordenação, será usado.
- `CanMoveItem`– Informa o modo de exibição de coleção se um determinado item puder ser arrastado reordenado.
- `GetTargetContentOffset`– Usado para obter o deslocamento de um determinado item de exibição de coleção.
- `GetTargetIndexPathForMove`– Obtém o `indexPath` de um determinado item para uma operação de arrastar.
- `MoveItem`– Move a ordem de um determinado item na lista.


### <a name="uicollectionviewdatasource"></a>UICollectionViewDataSource

As seguintes alterações ou adições foram feitas `UICollectionViewDataSource` na classe no Ios 9:

- `CanMoveItem`– Informa o modo de exibição de coleção se um determinado item puder ser arrastado reordenado.
- `MoveItem`– Move a ordem de um determinado item na lista.

### <a name="uicollectionviewdelegate"></a>UICollectionViewDelegate

As seguintes alterações ou adições foram feitas `UICollectionViewDelegate` na classe no Ios 9:

- `GetTargetContentOffset`– Usado para obter o deslocamento de um determinado item de exibição de coleção.
- `GetTargetIndexPathForMove`– Obtém o `indexPath` de um determinado item para uma operação de arrastar.

### <a name="uicollectionviewflowlayout"></a>UICollectionViewFlowLayout

As seguintes alterações ou adições foram feitas `UICollectionViewFlowLayout` na classe no Ios 9:

- `SectionFootersPinToVisibleBounds`– Une os rodapés de seção aos limites de exibição de coleção visíveis.
- `SectionHeadersPinToVisibleBounds`– Une os cabeçalhos de seção aos limites de exibição de coleção visíveis.

### <a name="uicollectionviewlayout"></a>UICollectionViewLayout

As seguintes alterações ou adições foram feitas `UICollectionViewLayout` na classe no Ios 9:

- `GetInvalidationContextForEndingInteractiveMovementOfItems`– Retorna o contexto de invalidação no final de uma operação de arrastar quando o usuário termina ou cancela o recurso.
- `GetInvalidationContextForInteractivelyMovingItems`– Retorna o contexto Invalidation no início de uma operação de arrastar.
- `GetLayoutAttributesForInteractivelyMovingItem`– Obtém os atributos de layout de um determinado item ao arrastar um item.
- `GetTargetIndexPathForInteractivelyMovingItem`– Retorna o `indexPath` do item que está no ponto determinado ao arrastar um item.

### <a name="uicollectionviewlayoutattributes"></a>UICollectionViewLayoutAttributes

As seguintes alterações ou adições foram feitas `UICollectionViewLayoutAttributes` na classe no Ios 9:

- `CollisionBoundingPath`– Retorna o caminho de colisão de dois itens durante uma operação de arrastar.
- `CollisionBoundsType`– Retorna o tipo de colisão (como a `UIDynamicItemCollisionBoundsType`) que ocorreu durante uma operação de arrastar.

### <a name="uicollectionviewlayoutinvalidationcontext"></a>UICollectionViewLayoutInvalidationContext

As seguintes alterações ou adições foram feitas `UICollectionViewLayoutInvalidationContext` na classe no Ios 9:

- `InteractiveMovementTarget`– Retorna o item de destino de uma operação de arrastar.
- `PreviousIndexPathsForInteractivelyMovingItems`– Retorna o `indexPaths` de outros itens envolvidos em uma operação de arrastar para reordenar.
- `TargetIndexPathsForInteractivelyMovingItems`– Retorna o `indexPaths` dos itens que serão reordenados como resultado de uma operação de arrastar para reordenar.

### <a name="uicollectionviewsource"></a>UICollectionViewSource

As seguintes alterações ou adições foram feitas `UICollectionViewSource` na classe no Ios 9:

- `CanMoveItem`– Informa o modo de exibição de coleção se um determinado item puder ser arrastado reordenado.
- `GetTargetContentOffset`– Retorna os deslocamentos de itens que serão movidos por meio de uma operação de arrastar para reordenar.
- `GetTargetIndexPathForMove`– Retorna o `indexPath` de um item que será movido durante uma operação de arrastar para reordenar.
- `MoveItem`– Move a ordem de um determinado item na lista.

## <a name="summary"></a>Resumo

Este artigo abordou as alterações nas exibições de coleção no iOS 9 e descreveu como implementá-las no Xamarin. iOS.
Ele abordou a implementação de uma ação simples de arrastar para reordenar em uma exibição de coleção; usando um reconhecedor de gestor personalizado com arrastar para reordenar; e como o recurso de arrastar para reordenação afeta um layout de exibição de coleção personalizado.

## <a name="related-links"></a>Links relacionados

- [Amostras do iOS 9](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS9)
- [Exemplo de exibição de coleção](https://docs.microsoft.com/samples/xamarin/ios-samples/ios9-collectionview)
- [SimpleCollectionView (exemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/simplecollectionview)
- [Eventos, protocolos e delegados](~/ios/app-fundamentals/delegates-protocols-and-events.md)
- [Trabalhando com tabelas e células](~/ios/user-interface/controls/tables/index.md)
