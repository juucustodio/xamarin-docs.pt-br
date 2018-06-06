---
title: Exibições de coleção em xamarin
description: Exibições de coleção permitem que o conteúdo a ser exibido usando layouts arbitrários. Eles permitem criar facilmente layouts de grade fora da caixa, e também dar suporte a layouts personalizados.
ms.prod: xamarin
ms.assetid: F4B85F25-0CB5-4FEA-A3B5-D22FCDC81AE4
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/20/2017
ms.openlocfilehash: b9ba2f885364084d6bee67c460b4831c00c7ae55
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34790621"
---
# <a name="collection-views-in-xamarinios"></a>Exibições de coleção em xamarin

_Exibições de coleção permitem que o conteúdo a ser exibido usando layouts arbitrários. Eles permitem criar facilmente layouts de grade fora da caixa, e também dar suporte a layouts personalizados._

Exibições de coleção, disponíveis no `UICollectionView` da classe, são um novo conceito no iOS 6 que apresentam apresentar vários itens na tela usando layouts. Os padrões para fornecer dados para um `UICollectionView` para criar itens e interagir com os seguinte itens a delegação mesmo e fonte de dados padrões usados no desenvolvimento do iOS.

No entanto, as exibições de coleção funcionam com um subsistema de layout que seja independente do `UICollectionView` em si. Portanto, basta fornecer um layout diferente pode alterar facilmente a apresentação de uma exibição de coleção.

iOS fornece uma classe de layout chamada `UICollectionViewFlowLayout` que permite que a linha de layouts, como uma grade a ser criado sem trabalho adicional. Além disso, layouts personalizados também podem ser criados permitir qualquer apresentação que você pode imaginar.

## <a name="uicollectionview-basics"></a>Noções básicas de UICollectionView

O `UICollectionView` classe é composta por três itens diferentes:

-  **Células** – exibições controladas por dados para cada item
-  **Modos de exibição suplementares** – exibições controladas por dados associadas a uma seção.
-  **Exibições de decoração** – controlada por exibições criadas por um layout de dados não

## <a name="cells"></a>Células

As células são objetos que representam um único item no conjunto de dados que está sendo apresentado pela exibição de coleção. Cada célula é uma ocorrência da `UICollectionViewCell` classe, que é composta de três modos de exibição diferentes, conforme mostrado na figura a seguir:

 [![](uicollectionview-images/01-uicollectionviewcell.png "Cada célula é composta de três modos de exibição diferentes, conforme mostrado aqui")](uicollectionview-images/01-uicollectionviewcell.png#lightbox)

O `UICollectionViewCell` classe tem as seguintes propriedades para cada um desses modos de exibição:

-   `ContentView` – Este modo de exibição contém o conteúdo que apresenta a célula. Ela é processada na ordem z superior na tela.
-   `SelectedBackgroundView` – Células têm suporte interno para seleção. Essa exibição é usada para indicar visualmente se uma célula está selecionada. Ele é renderizado logo abaixo do `ContentView` quando uma célula estiver selecionada.
-   `BackgroundView` – Células também podem exibir um plano de fundo, que é apresentado pelo `BackgroundView` . Essa exibição é renderizada sob o `SelectedBackgroundView` .


Definindo o `ContentView` , para que seja menor do que o `BackgroundView` e `SelectedBackgroundView`, o `BackgroundView` pode ser usado para quadro visualmente o conteúdo, enquanto o `SelectedBackgroundView` será exibido quando uma célula é selecionada, conforme mostrado abaixo:

 [![](uicollectionview-images/02-cells.png "Os elementos de célula diferente")](uicollectionview-images/02-cells.png#lightbox)

As células na captura de tela acima são criadas por herança de `UICollectionViewCell` e configuração de `ContentView`, `SelectedBackgroundView` e `BackgroundView` propriedades, respectivamente, conforme mostrado no código a seguir:

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


## <a name="supplementary-views"></a>Modos de exibição suplementares

Exibições suplementares são exibições que apresentam informações associadas a cada seção de um `UICollectionView`. Como as células, modos de exibição suplementares são controladas por dados. Quando células apresentam os dados de item de uma fonte de dados, exibições suplementares apresentar os dados de seção, como as categorias do catálogo na estante ou gênero de música em uma biblioteca de música.

Por exemplo, uma exibição suplementares pode ser usada para apresentar um cabeçalho de uma seção específica, como mostrado na figura a seguir:

 [![](uicollectionview-images/02a-supplementary-view.png "Um modo de exibição suplementares usado para apresentar um cabeçalho de uma seção específica, conforme mostrado aqui")](uicollectionview-images/02a-supplementary-view.png#lightbox)

Para usar um modo de exibição suplementares, ela precisa primeiro ser registrado no `ViewDidLoad` método:

```csharp
CollectionView.RegisterClassForSupplementaryView (typeof(Header), UICollectionElementKindSection.Header, headerId);
```

Em seguida, a exibição precisa ser retornadas usando `GetViewForSupplementaryElement`, criado usando `DequeueReusableSupplementaryView`e herda do `UICollectionReusableView`. O trecho de código a seguir produzirá o SupplementaryView mostrado na captura de tela acima:

```csharp
public override UICollectionReusableView GetViewForSupplementaryElement (UICollectionView collectionView, NSString elementKind, NSIndexPath indexPath)
        {
            var headerView = (Header)collectionView.DequeueReusableSupplementaryView (elementKind, headerId, indexPath);
            headerView.Text = "Supplementary View";
            return headerView;
        }

```

Modos de exibição suplementares são mais genéricos que apenas os cabeçalhos e rodapés.
Eles podem ser posicionados em qualquer lugar na exibição de coleção e podem ser compostos por todas as exibições, tornando a aparência totalmente personalizável.

 <a name="Decoration_Views" />


## <a name="decoration-views"></a>Exibições de decoração

Decoração exibições são puramente visual que pode ser exibido em um `UICollectionView`. Diferentemente de células e modos de exibição adicionais, eles não são controlada por dados. Eles sempre são criados dentro de subclasse do layout e subsequentemente podem alterar como o layout do conteúdo. Por exemplo, uma exibição de decoração pode ser usada para apresentar um modo de exibição do plano de fundo rola com o conteúdo a `UICollectionView`, conforme mostrado abaixo:

 [![](uicollectionview-images/02c-decoration-view.png "Exibição de decoração com um plano de fundo vermelho")](uicollectionview-images/02c-decoration-view.png#lightbox)

 O trecho de código a seguir altera o plano de fundo para vermelho nas amostras `CircleLayout` classe:

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

Assim como acontece com outras partes do iOS, como `UITableView` e `MKMapView`, `UICollectionView` obtém seus dados de um *fonte de dados*, que é exposto no xamarin por meio de **`UICollectionViewDataSource`** classe. Essa classe é responsável por fornecer conteúdo para o `UICollectionView` como:

-  **Células** – retornado de `GetCell` método.
-  **Modos de exibição suplementares** – retornado de `GetViewForSupplementaryElement` método.
-  **Número de seções** – retornado de `NumberOfSections` método. O padrão é 1 se não implementado.
-  **Número de itens por seção** – retornado de `GetItemsCount` método.

### <a name="uicollectionviewcontroller"></a>UICollectionViewController
Para sua conveniência, o `UICollectionViewController` classe está disponível. Isso é configurado automaticamente para ser tanto o delegado, que é abordado na próxima seção, e a fonte de dados do seu `UICollectionView` exibição.

Assim como acontece com `UITableView`, o `UICollectionView` classe chamará somente a fonte de dados para obter as células para itens que estão na tela.
Células que aparecem na tela são colocadas em uma fila para reutilização, como mostra a imagem a seguir:

 [![](uicollectionview-images/03-cell-reuse.png "As células que aparecem na tela são colocadas em uma fila para reutilização como mostrado aqui")](uicollectionview-images/03-cell-reuse.png#lightbox)

Reutilização de célula foi simplificada com `UICollectionView` e `UITableView`. Você não precisa criar uma célula diretamente na fonte de dados se um não estiver disponível na fila de reutilização, como as células são registradas com o sistema. Se uma célula não está disponível ao fazer a chamada para a eliminação da fila a célula da fila de reutilização, iOS criará automaticamente com base no tipo ou nib que foi registrado.
A mesma técnica também está disponível para modos de exibição adicionais.

Por exemplo, considere o seguinte código que registra o `AnimalCell` classe:

```csharp
static NSString animalCellId = new NSString ("AnimalCell");
CollectionView.RegisterClassForCell (typeof(AnimalCell), animalCellId);
```

Quando um `UICollectionView` precisa de uma célula porque o item estiver na tela, o `UICollectionView` chama sua fonte de dados `GetCell` método. Semelhante à maneira como isso funciona com UITableView, esse método é responsável por configurar uma célula de dados do backup, o que seriam um `AnimalCell` classe nesse caso.

O código a seguir mostra uma implementação de `GetCell` que retorna um `AnimalCell` instância:

```csharp
public override UICollectionViewCell GetCell (UICollectionView collectionView, Foundation.NSIndexPath indexPath)
{
        var animalCell = (AnimalCell)collectionView.DequeueReusableCell (animalCellId, indexPath);

        var animal = animals [indexPath.Row];

        animalCell.Image = animal.Image;

        return animalCell;
}
```

A chamada para `DequeReusableCell` é onde a célula ou eliminação enfileirada da fila de reutilização ou, se uma célula não está disponível na fila, criada com base no tipo de registrado na chamada para `CollectionView.RegisterClassForCell`.

Nesse caso, registrando o `AnimalCell` classe iOS criará um novo `AnimalCell` internamente e retorná-lo quando é feita uma chamada para a eliminação da fila de uma célula, após o qual ele está configurado com a imagem contidas na classe animal e retornados para exibição para o `UICollectionView`.

 <a name="Delegate" />


### <a name="delegate"></a>delegado

O `UICollectionView` classe usa um delegate do tipo `UICollectionViewDelegate` para dar suporte à interação com o conteúdo a `UICollectionView`. Isso permite o controle de:

-  **Seleção de célula** – determina se uma célula está selecionada.
-  **Realce de célula** – determina se uma célula no momento está sendo alterada.
-  **Menus de célula** – Menu exibido para uma célula em resposta a um gesto pressione longo.


Assim como acontece com a fonte de dados, o `UICollectionViewController` é configurado por padrão para ser o representante para a `UICollectionView`.

 <a name="Cell_HighLighting" />


#### <a name="cell-highlighting"></a>Realce de célula

Quando uma célula é pressionada, as transições de célula em um estado realçado, e ele não está selecionado até que o usuário levante o dedo da célula. Isso permite que uma alteração temporária a aparência da célula antes de realmente estiver selecionada. Após a seleção, a célula `SelectedBackgroundView` é exibido. A figura abaixo mostra o estado realçado antes que a seleção ocorre:

 [![](uicollectionview-images/04-cell-highlight.png "Esta figura mostra o estado realçado antes de ocorre a seleção")](uicollectionview-images/04-cell-highlight.png#lightbox)

Para implementar o realce, o `ItemHighlighted` e `ItemUnhighlighted` métodos do `UICollectionViewDelegate` pode ser usado. Por exemplo, o código a seguir serão aplicadas a um plano de fundo amarelo a `ContentView` quando a célula é realçada e um plano de fundo branco quando não realçado, conforme mostrado na imagem acima:

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

Seleção é habilitada por padrão em `UICollectionView`. Para desativar a seleção, substituir `ShouldHighlightItem` e retornar false, conforme mostrado abaixo:

```csharp
public override bool ShouldHighlightItem (UICollectionView collectionView, NSIndexPath indexPath)
{
        return false;
}
```

Quando o realce é desativado, o processo de selecionar uma célula também seja desabilitado. Além disso, há também uma `ShouldSelectItem` método que controla a seleção diretamente, embora se `ShouldHighlightItem` é implementado e retornará false, `ShouldSelectItem` não for chamado.

 `ShouldSelectItem` permite a seleção ser ativada ou desativada em uma base de item a item, quando `ShouldHighlightItem` não está implementado. Ele também permite que realce sem seleção, se `ShouldHighlightItem` é implementado e retorna true, ao `ShouldSelectItem` retorna false.

 <a name="Cell_Menus" />


#### <a name="cell-menus"></a>Menus de célula

Cada célula em uma `UICollectionView` é capaz de mostrar um menu que permite recortar, copiar e colar, opcionalmente, ser suportados. Para criar um menu Editar em uma célula:

1.  Substituir `ShouldShowMenu` e retorna true se o item deve mostrar um menu.
1.  Substituir `CanPerformAction` e retornar true para todas as ações que pode executar o item, que será qualquer de recortar, copiar ou colar.
1.  Substituir `PerformAction` para executar a edição, copie da operação de colagem.


Captura de tela a seguir mostra o menu quando uma célula é pressionada longa:

 [![](uicollectionview-images/04a-menu.png "Esta captura de tela mostra o menu quando uma célula longa é pressionada")](uicollectionview-images/04a-menu.png#lightbox)

 <a name="Layout" />


## <a name="layout"></a>Layout

`UICollectionView` oferece suporte a um sistema de layout que permite que o posicionamento de todos os seus elementos, células, suplementares exibições e exibições de decoração, para ser gerenciado independentemente do `UICollectionView` em si.
Usando o sistema de layout, um aplicativo pode dar suporte a layouts, como a grade é visto neste artigo, bem como fornecer layouts personalizados.

 <a name="Layout_Basics" />


### <a name="layout-basics"></a>Noções básicas de layout

Layouts em uma `UICollectionView` são definidos em uma classe que herda de `UICollectionViewLayout`. A implementação de layout é responsável por criar os atributos de layout para cada item de `UICollectionView`. Há duas maneiras de criar um layout:

-  Usar interno `UICollectionViewFlowLayout` .
-  Forneça um layout personalizado herdando de `UICollectionViewLayout` .


 <a name="Flow_Layout" />


### <a name="flow-layout"></a>Layout de fluxo

O `UICollectionViewFlowLayout` classe fornece um layout que é adequado para organizar o conteúdo em uma grade de células como vimos baseada em linha.

Para usar um layout de fluxo:

-  Criar uma instância de `UICollectionViewFlowLayout` :


```csharp
var layout = new UICollectionViewFlowLayout ();
```

-  Passe a instância para o construtor de `UICollectionView` :


```csharp
simpleCollectionViewController = new SimpleCollectionViewController (layout);
```

Isso é tudo o que é necessário para o layout do conteúdo em uma grade. Além disso, quando a orientação muda, o `UICollectionViewFlowLayout` manipula reorganizar o conteúdo corretamente, conforme mostrado abaixo:

 [![](uicollectionview-images/05-layout-orientation.png "Exemplo das alterações de orientação")](uicollectionview-images/05-layout-orientation.png#lightbox)

 <a name="Section_Inset" />


#### <a name="section-inset"></a>Inserção de seção

Para fornecer espaço em torno de `UIContentView`, layouts têm um `SectionInset` propriedade do tipo `UIEdgeInsets`. Por exemplo, o código a seguir fornece um buffer de 50 pixels em torno de cada seção de `UIContentView` quando disposto por uma `UICollectionViewFlowLayout`:

```csharp
var layout = new UICollectionViewFlowLayout ();
layout.SectionInset = new UIEdgeInsets (50,50,50,50);
```

Isso resulta em espaçamento ao redor da seção conforme mostrado abaixo:

 [![](uicollectionview-images/06-sectioninset.png "Espaçamento ao redor da seção, como mostrado aqui")](uicollectionview-images/06-sectioninset.png#lightbox)

 <a name="Subclassing_UICollectionViewFlowLayout" />


#### <a name="subclassing-uicollectionviewflowlayout"></a>Subclasses UICollectionViewFlowLayout

Na edição usando `UICollectionViewFlowLayout` diretamente, ele também pode ter subclasses para personalizar o layout do conteúdo ao longo da linha. Por exemplo, isso pode ser usado para criar um layout que não encapsula as células em uma grade, mas em vez disso, cria uma única linha com um efeito de rolagem horizontal, conforme mostrado abaixo:

 [![](uicollectionview-images/07-line-layout.png "Uma única linha com um efeito de rolagem horizontal")](uicollectionview-images/07-line-layout.png#lightbox)

Para implementar isso subclassificação `UICollectionViewFlowLayout` requer:

-  Inicializando as propriedades de layout que se aplicam ao layout do próprio ou todos os itens de layout no construtor.
-  Substituindo `ShouldInvalidateLayoutForBoundsChange` , retornando true isso que, quando os limites do `UICollectionView` alterações, o layout das células serão recalculadas. Isso é usado neste caso Certifique-se o código de transformação aplicada para a célula centermost será aplicada durante a rolagem.
-  Substituindo `TargetContentOffset` para tornar o centermost célula snap para o centro do `UICollectionView` como rolagem será interrompido.
-  Substituindo `LayoutAttributesForElementsInRect` para retornar uma matriz de `UICollectionViewLayoutAttributes` . Cada `UICollectionViewLayoutAttribute` contém informações sobre como o layout do item específico, incluindo propriedades, como seu `Center` , `Size` , `ZIndex` e `Transform3D` .


O código a seguir mostra essa implementação:

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

Além de usar `UICollectionViewFlowLayout`, layouts também podem ser totalmente personalizados herdando diretamente da `UICollectionViewLayout`.

Os métodos principais para substituir são:

-   `PrepareLayout` – Usado para executar cálculos geométricos inicias que serão utilizados durante o processo de layout.
-   `CollectionViewContentSize` -Retorna o tamanho da área usada para exibir o conteúdo.
-   `LayoutAttributesForElementsInRect` – Como com o exemplo UICollectionViewFlowLayout mostrado anteriormente, esse método é usado para fornecer informações para o `UICollectionView` sobre como layout de cada item. No entanto, diferentemente de `UICollectionViewFlowLayout` , ao criar um layout personalizado, é possível posicionar itens, no entanto, você escolher.


Por exemplo, o mesmo conteúdo pode ser apresentado em um layout circular conforme mostrado abaixo:

 [![](uicollectionview-images/08-circle-layout.png "Um circular personalizado layout conforme mostrado aqui")](uicollectionview-images/08-circle-layout.png#lightbox)

A coisa avançada sobre layouts é que, para alterar do layout de grade, para um layout de rolagem horizontal e subsequentemente a este layout circular requer somente a classe de layout fornecida para o `UICollectionView` ser alterado. Nada a `UICollectionView`, seu representante ou dados de origem alterações de código em todos os.


## <a name="changes-in-ios-9"></a>Alterações no iOS 9


No iOS 9, a exibição de coleção (`UICollectionView`) agora oferece suporte a arrasta a reordenação de itens para fora da caixa, adicionando um novo reconhecedor de gestos padrão e vários novos métodos de suporte.

Usando esses novos métodos, você pode implementar facilmente arrastar para reordenar na exibição de coleção e tem a opção de personalizar a aparência de itens durante qualquer estágio do processo de reordenação.

[![](uicollectionview-images/intro01.png "Um exemplo do processo de reordenação")](uicollectionview-images/intro01.png#lightbox)

Neste artigo, vamos dar uma olhada na implementação de arrastar para reordenar em um aplicativo xamarin, bem como algumas das alterações feitas para o controle de exibição de coleção iOS 9:

- [Fácil de reorganização de itens](#Easy-Reordering-of-Items)
    - [Exemplo simples de reordenação](#Simple-Reordering-Example)
    - [Usando um reconhecedor de gestos personalizados](#Using-a-Custom-Gesture-Recognizer)
    - [Layouts personalizados e reordenação](#Custom-Layouts-and-Reording)
- [Alterações de exibição de coleção](#Collection-View-Changes)

<a name="Easy-Reordering-of-Items" />

## <a name="reordering-of-items"></a>Reorganização de itens

Como mencionado acima, uma das alterações mais significativas para o modo de exibição de coleção no iOS 9 foi a adição de funcionalidade de arrastar para reordenar fácil fora da caixa.

No iOS 9, a maneira mais rápida para adicionar a reordenação para uma exibição de coleção é usar um `UICollectionViewController`.
O controlador de exibição de coleção agora tem um `InstallsStandardGestureForInteractiveMovement` propriedade, que adiciona um padrão *reconhecedor de gestos* que dá suporte a arrastamento para reorganizar os itens na coleção.
Como o valor padrão é `true`, você só precisa implementar o `MoveItem` método o `UICollectionViewDataSource` classe para dar suporte a arrastar para reordenar. Por exemplo:

```csharp
public override void MoveItem (UICollectionView collectionView, NSIndexPath sourceIndexPath, NSIndexPath destinationIndexPath)
{
    // Reorder our list of items
    ...
}
```
<a name="Simple-Reordering-Example" />

### <a name="simple-reordering-example"></a>Exemplo simples de reordenação

Como um exemplo rápido, iniciar um novo projeto de xamarin e editar o **Main.storyboard** arquivo. Arraste um `UICollectionViewController` na superfície de design:

[![](uicollectionview-images/quick01.png "Adicionando um UICollectionViewController")](uicollectionview-images/quick01.png#lightbox)

Selecione o modo de exibição de coleção (talvez seja mais fácil de fazer isso da estrutura de tópicos do documento). Na guia layout da área de propriedades, defina os seguintes tamanhos, conforme ilustrado na captura de tela abaixo:

- **Tamanho da célula**: largura – 60 | Altura – 60
- **O tamanho do cabeçalho**: largura – 0 | Altura – 0
- **Tamanho do rodapé**: largura – 0 | Altura – 0
- **Espaçamento mínimo**: para células – 8 | Para linhas – 8
- **Seção inserções**: superior – 16 | Baixo – 16 | Esquerda – 16 | Direita – 16

[![](uicollectionview-images/quick04.png "Defina os tamanhos de exibição de coleção")](uicollectionview-images/quick04.png#lightbox)

Em seguida, edite o padrão de célula:
    - Alterar sua cor de plano de fundo azul
    - Adicionar um rótulo para atuar como o título da célula
    - Definir o identificador de reutilização **célula**

[![](uicollectionview-images/quick02.png "Editar a célula padrão")](uicollectionview-images/quick02.png#lightbox)

Adicione restrições para manter o rótulo centralizado dentro da célula como seu tamanho é alterado:

No **propriedade preenchimento** para o _CollectionViewCell_ e defina o **classe** para `TextCollectionViewCell`:

[![](uicollectionview-images/quick05.png "Definir a classe como TextCollectionViewCell")](uicollectionview-images/quick05.png#lightbox)

Definir o **exibição de coleção reutilizável** para `Cell`:

[![](uicollectionview-images/quick06.png "Definir a exibição de coleção reutilizável a célula")](uicollectionview-images/quick06.png#lightbox)

Por fim, selecione o rótulo e nomeie-o `TextLabel`:

[![](uicollectionview-images/quick07.png "rótulo de nome TextLabel")](uicollectionview-images/quick07.png#lightbox)

Editar o `TextCollectionViewCell` classe e adicione as seguintes propriedades.:

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

Aqui o `Text` propriedade do rótulo é exposta como o título da célula, portanto ele pode ser definido no código.

Adicionar uma nova classe do c# para o projeto e chamá-la `WaterfallCollectionSource`. Edite o arquivo e torná-lo a seguinte aparência:

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

Essa classe será a fonte de dados para nosso exibição de coleção e forneça as informações para cada célula na coleção.
Observe que o `MoveItem` método é implementado para permitir a arraste itens da coleção a ser reordenada.

Adicione outra nova classe c# ao projeto e chamá-la `WaterfallCollectionDelegate`. Editar esse arquivo e torná-lo a seguinte aparência:

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

Isso irá atuar como o representante para nosso exibição de coleção. Métodos foram substituídos para realçar uma célula, conforme o usuário interage com ele no modo de exibição de coleção.

Adicionar uma última c# classe ao projeto e chamá-la `WaterfallCollectionView`. Editar esse arquivo e torná-lo a seguinte aparência:

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

Observe que `DataSource` e `Delegate` que criamos acima são definidos quando o modo de exibição de coleção é construído a partir de seu storyboard (ou **.xib** arquivo).

Editar o **Main.storyboard** novamente e selecione o modo de exibição de coleção e alterne para o **propriedades**. Definir o **classe** para personalizado `WaterfallCollectionView` classe são definidas acima:

Salvar as alterações feitas na interface do usuário e executar o aplicativo.
Se o usuário seleciona um item da lista e arrasta-o para um novo local, os outros itens serão animar automaticamente conforme se move do caminho do item.
Quando o usuário solta o item em um novo local, ele será fique nesse local. Por exemplo:

[![](uicollectionview-images/intro01.png "Um exemplo de arrastar um item para um novo local")](uicollectionview-images/intro01.png#lightbox)

<a name="Using-a-Custom-Gesture-Recognizer" />

### <a name="using-a-custom-gesture-recognizer"></a>Usando um reconhecedor de gestos personalizados

Em casos onde você não pode usar um `UICollectionViewController` e deve usar uma expressão `UIViewController`, ou se você quiser mais controle sobre o gesto de arrastar e soltar, você pode criar seus próprios reconhecedor de gestos personalizados e adicioná-lo para o modo de exibição de coleção quando o modo de exibição é carregado. Por exemplo:

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

Aqui, estamos usando vários novos métodos adicionados à exibição de coleção para implementar e controlar a operação de arrastar:

 - `BeginInteractiveMovementForItem` -Marca o início de uma operação de movimentação.
 - `UpdateInteractiveMovementTargetPosition` -É enviado como o local do item é atualizado.
 - `EndInteractiveMovement` -Marca o fim de mover um item.
 - `CancelInteractiveMovement` -Marca o usuário cancelar a operação de movimentação.

Quando o aplicativo é executado, a operação de arrastar funciona exatamente como o padrão arraste reconhecedor de gestos que vem com o modo de exibição de coleção.

<a name="Custom-Layouts-and-Reording" />

### <a name="custom-layouts-and-reordering"></a>Layouts personalizados e reordenação

No iOS 9, vários métodos novos foram adicionados para trabalhar com layouts personalizados e arrastar para reordenar em uma exibição de coleção. Para explorar esse recurso, vamos adicionar um layout personalizado à coleção.

Primeiro, adicione uma nova classe c# chamada `WaterfallCollectionLayout` ao projeto. Editá-lo e torná-lo a seguinte aparência:

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

Isso pode ser usado a classe para fornecer um personalizada de duas colunas, o layout do tipo de cascata à exibição da coleção.
O código usa a codificação de chave-valor (por meio de `WillChangeValue` e `DidChangeValue` métodos) para fornecer a associação de dados para nosso computadas propriedades dessa classe.

Em seguida, edite o `WaterfallCollectionSource` e faça as alterações e adições a seguir:

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

Em seguida, edite o `WaterfallCollectionView` classe e adicione a seguinte propriedade auxiliar:

```csharp
public WaterfallCollectionSource Source {
    get { return (WaterfallCollectionSource)DataSource; }
}
```

Isso tornará mais fácil de obter na nossa fonte de dados (e a altura do item), do layout personalizado.

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

Isso cria uma instância do nosso layout personalizado, define o evento para fornecer o tamanho de cada item e anexa o novo layout para nosso exibição de coleção.

Se executarmos o aplicativo xamarin novamente, o modo de exibição de coleção agora se parecerá com o seguinte:

[![](uicollectionview-images/custom01.png "O modo de exibição de coleção será agora assim")](uicollectionview-images/custom01.png#lightbox)

Podemos itens ainda arrastar para reordenar como antes, mas os itens agora serão alterado de tamanho para o novo local quando eles são descartados.

## <a name="collection-view-changes"></a>Alterações de exibição de coleção

Nas seções a seguir, vamos dar uma olhada detalhada as alterações feitas em cada classe no modo de exibição de coleção por iOS 9.

### <a name="uicollectionview"></a>UICollectionView

As adições ou alterações a seguir foram feitas para o `UICollectionView` classe para iOS 9:

 - `BeginInteractiveMovementForItem` – Marca o início de uma operação de arrastar.
 - `CancelInteractiveMovement` – Informa a coleção de modo que o usuário cancelou a operação de arrastar.
 - `EndInteractiveMovement` – Informa a coleção de modo que o usuário concluiu uma operação de arrastar.
 - `GetIndexPathsForVisibleSupplementaryElements` -Retorna o `indexPath` de um cabeçalho ou rodapé de página em uma seção de exibição de coleção.
 - `GetSupplementaryView` – Retorna determinado cabeçalho ou rodapé.
 - `GetVisibleSupplementaryViews` -Retorna uma lista de todos os visível cabeçalhos e rodapés.
 - `UpdateInteractiveMovementTargetPosition` – Informa a coleção de modo que o usuário foi movido ou está movendo, um item durante uma operação de arrastar.

### <a name="uicollectionviewcontroller"></a>UICollectionViewController

As adições ou alterações a seguir foram feitas para o `UICollectionViewController` classe no iOS 9:

 - `InstallsStandardGestureForInteractiveMovement` – Se `true` o reconhecedor de gestos novo que automaticamente dá suporte a arrastar para reordenar será usado.
 - `CanMoveItem` – Informa se um determinado item pode ser reordenada de arrastar o modo de exibição de coleção.
 - `GetTargetContentOffset` – Usado para obter o deslocamento de um item de exibição de determinada coleção.
 - `GetTargetIndexPathForMove` – Obtém o `indexPath` de um determinado item para uma operação de arrastar.
 - `MoveItem` – Move a ordem de um determinado item na lista.


### <a name="uicollectionviewdatasource"></a>UICollectionViewDataSource

As adições ou alterações a seguir foram feitas para o `UICollectionViewDataSource` classe no iOS 9:

 - `CanMoveItem` – Informa se um determinado item pode ser reordenada de arrastar o modo de exibição de coleção.
 - `MoveItem` – Move a ordem de um determinado item na lista.

### <a name="uicollectionviewdelegate"></a>UICollectionViewDelegate

As adições ou alterações a seguir foram feitas para o `UICollectionViewDelegate` classe no iOS 9:

 - `GetTargetContentOffset` – Usado para obter o deslocamento de um item de exibição de determinada coleção.
 - `GetTargetIndexPathForMove` – Obtém o `indexPath` de um determinado item para uma operação de arrastar.

### <a name="uicollectionviewflowlayout"></a>UICollectionViewFlowLayout

As adições ou alterações a seguir foram feitas para o `UICollectionViewFlowLayout` classe no iOS 9:

 - `SectionFootersPinToVisibleBounds` – Prende os rodapés da seção para os limites de exibição de coleção visível.
 - `SectionHeadersPinToVisibleBounds` – Prende os cabeçalhos de seção para os limites de exibição de coleção visível.

### <a name="uicollectionviewlayout"></a>UICollectionViewLayout

As adições ou alterações a seguir foram feitas para o `UICollectionViewLayout` classe no iOS 9:

 - `GetInvalidationContextForEndingInteractiveMovementOfItems` -Retorna o contexto de invalidação do final de uma operação de arrastar quando o usuário concluir a operação de arrastar ou cancele.
 - `GetInvalidationContextForInteractivelyMovingItems` -Retorna o contexto de invalidação no início de uma operação de arrastar.
 - `GetLayoutAttributesForInteractivelyMovingItem` – Obtém os atributos de Layout para um determinado item ao arrastar um item.
 - `GetTargetIndexPathForInteractivelyMovingItem` -Retorna o `indexPath` do item que é dado momento ao arrastar um item.

### <a name="uicollectionviewlayoutattributes"></a>UICollectionViewLayoutAttributes

As adições ou alterações a seguir foram feitas para o `UICollectionViewLayoutAttributes` classe no iOS 9:

 - `CollisionBoundingPath` -Retorna o caminho de colisão de dois itens durante uma operação de arrastar.
 - `CollisionBoundsType` -Retorna o tipo de colisão (como uma `UIDynamicItemCollisionBoundsType`) que ocorreu durante uma operação de arrastar.

### <a name="uicollectionviewlayoutinvalidationcontext"></a>UICollectionViewLayoutInvalidationContext

As adições ou alterações a seguir foram feitas para o `UICollectionViewLayoutInvalidationContext` classe no iOS 9:

 - `InteractiveMovementTarget` -Retorna o item de destino de uma operação de arrastar.
 - `PreviousIndexPathsForInteractivelyMovingItems` -Retorna o `indexPaths` de outros itens envolvidos em um arrastar para reordenar a operação.
 - `TargetIndexPathsForInteractivelyMovingItems` -Retorna o `indexPaths` de itens que serão reordenados como resultado de uma operação de arrastar para reordenar.

### <a name="uicollectionviewsource"></a>UICollectionViewSource

As adições ou alterações a seguir foram feitas para o `UICollectionViewSource` classe no iOS 9:

 - `CanMoveItem` – Informa se um determinado item pode ser reordenada de arrastar o modo de exibição de coleção.
 - `GetTargetContentOffset` -Retorna os deslocamentos de itens que serão movidos por meio de uma operação de arrastar para reordenar.
 - `GetTargetIndexPathForMove` -Retorna o `indexPath` de um item que será movido durante uma operação de arrastar para reordenar.
 - `MoveItem` – Move a ordem de um determinado item na lista.

## <a name="summary"></a>Resumo

Este artigo tem abordadas as alterações às exibições de coleção no iOS 9 e descritos como implementá-los no xamarin.
Ele trata a implementação de uma ação de arrastar para reordenar simple em uma exibição de coleção; usando um reconhecedor de gestos personalizados com arrastar para reordenar; e como arrastar para reordenar afeta um layout de exibição de coleção personalizados.

## <a name="related-links"></a>Links relacionados

- [Exemplos do iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [Exemplo de modo de exibição de coleção](https://developer.xamarin.com/samples/monotouch/ios9/CollectionView/)
- [SimpleCollectionView (exemplo)](https://developer.xamarin.com/samples/SimpleCollectionView/)
- [Eventos, protocolos e delegados](~/ios/app-fundamentals/delegates-protocols-and-events.md)
- [Trabalhando com tabelas e células](~/ios/user-interface/controls/tables/index.md)
