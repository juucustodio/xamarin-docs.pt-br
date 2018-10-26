---
title: Atualizações de Design visual no iOS 11
description: Este documento descreve o design visual atualizações apresentado no iOS 11. Ele aborda as alterações nas barras de navegação, controladores de pesquisa, margens, conteúdo de tela inteira e modos de exibição de tabela.
ms.prod: xamarin
ms.assetid: 7C300B94-0FAF-492E-A326-877419A1824B
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/13/2016
ms.openlocfilehash: c6351f2c25f8e31181c761aea1b471315a8a05e8
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50114503"
---
# <a name="visual-design-updates-in-ios-11"></a>Atualizações de Design visual no iOS 11

No iOS 11, a Apple introduziu novas alterações visual, incluindo atualizações para a barra de navegação, barra de pesquisa e modos de exibição de tabela. Além de melhorias foram feitas para permitir mais flexibilidade sobre as margens e o conteúdo de tela inteira. Essas alterações são abordadas neste guia. 

Para obter informações específicas sobre o desenvolvimento para iPhone X, assista da Apple [projetar para iPhone X](https://developer.apple.com/videos/play/fall2017/801/) vídeo.

## <a name="uikit"></a>UIKit

Barras de UIKit, foram adaptadas no iOS 11 para torná-los mais acessível para usuários finais.

Uma alteração é uma nova exibição HUD que aparece quando pressionamentos longa de um usuário em uma barra de item. Para habilitar isso, defina as `largeContentSizeImage` propriedade na `UIBarItem` e adicione uma imagem maior por meio de um [catálogo de ativos](~/ios/app-fundamentals/images-icons/displaying-an-image.md):

```csharp
barItem.LargeContentSizeImage = UIImage.FromBundle("AccessibleImage");
```

### <a name="navigation-bar"></a>Barra de navegação
iOS 11 introduziu novas funcionalidades para facilitar a leitura de títulos da barra de navegação. Os aplicativos podem exibir esse título maior por meio da atribuição de `PrefersLargeTitles` propriedade como true:

```csharp
NavigationController.NavigationBar.PrefersLargeTitles = true;
```

Títulos maiores de configuração em seu aplicativo faz _todos os_ títulos de barras de navegação em seu aplicativo pareçam maiores, conforme ilustrado na seguinte captura de tela:

![Título da navegação grande](visual-design-images/image7.png)

Para controlar quando um título grande for exibido em uma barra de navegação, defina as `LargeTitleDisplayMode` no item de navegação para `Always`, `Never`, ou `Automatic`.

### <a name="search-controller"></a>Controlador de pesquisa

iOS 11 fez mais fácil de adicionar um controlador de pesquisa diretamente à barra de navegação. Depois de criar um controlador de pesquisa, adicioná-lo à sua barra de navegação com o `SearchController` propriedade:

```csharp
NavigationItem.SearchController = searchController;
```

[![Título da navegação grandes com a barra de pesquisa](visual-design-images/image8-sml.png)](visual-design-images/image8-sml.png#lightbox)

Dependendo da funcionalidade do seu aplicativo, você pode querer ou não a barra de pesquisa para ocultar quando um usuário percorre uma lista. Você pode ajustar isso usando o `HidesSearchBarWhenScrolling` propriedade.

## <a name="margins"></a>Margens

A Apple criou uma nova propriedade – `directionalLayoutMargins` – que pode ser usado para definir o espaço entre as exibições e subexibições. Use `directionalLayoutMargins` com `leading` ou `trailing` inserções. Independentemente se o sistema é um idioma da esquerda para a direita ou da direita para esquerda, o espaçamento em seu aplicativo está definido corretamente pelo iOS.

No iOS 10 e antes de todas as exibições tinham um tamanho de margem mínima à qual eles seriam se alinham. iOS 11 introduziu a opção para substituir isso usando `ViewRespectsSystemMinimumLayoutMargins`. Por exemplo, definir essa propriedade como false permite que você ajuste seu inserções de borda como zero:

```csharp
ViewRespectsSystemMinimumLayoutMargins = false;
View.LayoutMargins = UIEdgeInsets.Zero;
```
![Margem da imagem que mostra com zero baixo-relevo no ios 11](visual-design-images/image9.png)

<a name="fullscreen" />

## <a name="full-screen-content"></a>Conteúdo de tela inteira

iOS 7 [introduzidos](~/ios/platform/introduction-to-ios7/ios7-ui.md#fullscreen) `topLayoutGuide` e `bottomLayoutGuide` como uma maneira de restringir seus modos de exibição para que eles não estiverem ocultas por barras de UIKit e estão em uma área visível da tela. Elas foram substituídas no iOS 11 em favor do _área segura_.

Área de segurança é uma nova maneira de pensar sobre o espaço visível do seu aplicativo e como as restrições são adicionadas entre um modo de exibição e uma exibição super. Por exemplo, considere a imagem a seguir:

[![Área segura vs superior e o guia do layout inferior](visual-design-images/image10-sml.png)](visual-design-images/image10.png#lightbox)

Anteriormente, se você tivesse adicionado a um modo de exibição e queria que ele fique visível na área de verde acima, você deve restringir-o para o _inferior_ da `TopLayoutGuide` e o _superior_ do `BottomLayoutGuide`. No iOS 11, você faria em vez disso, restringi-lo para o _superior_ e o _inferior_ da área de segurança. Segue um exemplo:

```csharp
var safeGuide = View.SafeAreaLayoutGuide;
imageView.TopAnchor.ConstraintEqualTo(safeGuide.TopAnchor).Active = true;
safeGuide.BottomAnchor.ConstraintEqualTo(imageView.BottomAnchor).Active = true;
```

## <a name="table-view"></a>Exibição de tabela

O UITableView teve um número de alterações pequenas, mas significativas, no iOS 11.

Por padrão, cabeçalhos, rodapés e células são agora automaticamente dimensionadas com base em seu conteúdo. Para recusar esse conjunto de comportamento de dimensionamento automático a `EstimatedRowHeight`, `EstimatedSectionHeaderHeight`, ou `EstimatedSectionFooterHeight` como zero.

No entanto, em algumas circunstâncias (por exemplo, quando adicionar UITableViewController no iOS Designer ou ao usar Storboards existente no Interface Builder) talvez seja necessário habilitar manualmente o dimensionamento automático de células. Para fazer isso, certifique-se de que você definiu as seguintes propriedades na exibição de tabela de células, cabeçalhos e rodapés, respectivamente:

```csharp
// Cells
TableView.RowHeight = UITableView.AutomaticDimension;
TableView.EstimatedRowHeight = UITableView.AutomaticDimension;

// Header
TableView.SectionHeaderHeight = UITableView.AutomaticDimension;
TableView.EstimatedSectionHeaderHeight = 40f;

//Footer
TableView.SectionFooterHeight = UITableView.AutomaticDimension;
TableView.EstimatedSectionFooterHeight = 40f;

```

iOS 11 expandiu a funcionalidade de ações de linha. `UISwipeActionsConfiguration` foi introduzido para definir um conjunto de ações que deve ocorrer quando o usuário passa o dedo em qualquer direção, em uma linha em uma exibição de tabela. Esse comportamento é semelhante do Mail nativo. Para obter mais informações, consulte o [ações de linha](~/ios/user-interface/controls/tables/row-action.md) guia.

Modos de exibição de tabela têm suporte para arrastar e soltar no iOS 11. Para obter mais informações, consulte o [arrastar e soltar](~/ios/platform/introduction-to-ios11/drag-and-drop.md#uitableview) guia.


## <a name="related-links"></a>Links relacionados

- [O que há de novo no iOS 11 (Apple)](https://developer.apple.com/ios/)
- [Página de Store de aplicativo atualizado do produto (Apple)](https://developer.apple.com/app-store/product-page/)
- [Design para iPhone X (Apple) (vídeo)](https://developer.apple.com/videos/play/fall2017/801/)
- [Atualizar seu aplicativo para iOS 11 (WWDC) (vídeo)](https://developer.apple.com/videos/play/wwdc2017/204/)

