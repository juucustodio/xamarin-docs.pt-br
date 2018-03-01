---
title: "Atualizações de Design Visual"
description: Explorando os novos recursos do iOS 11
ms.topic: article
ms.prod: xamarin
ms.assetid: 7C300B94-0FAF-492E-A326-877419A1824B
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/13/2016
ms.openlocfilehash: 2904a7da73f5bf6e8960f65239d1f8dc52ab1aba
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="visual-design-updates"></a>Atualizações de Design Visual

_Explorando os novos recursos do iOS 11_

No iOS 11, a Apple introduziu novas alterações visual, incluindo atualizações para a barra de navegação, barra de pesquisa e modos de exibição de tabela. Além de melhorias foram feitas para permitir mais flexibilidade em conteúdo de tela inteira e de margens. Essas alterações são abordadas neste guia.

## <a name="uikit"></a>UIKit

Barras de UIKit foram adaptadas no iOS 11 para torná-los mais acessível para usuários finais.

Uma alteração é uma nova tela de HUD aparece quando pressionamentos longa de um usuário em uma barra de item. Para habilitar isso, defina o `largeContentSizeImage` propriedade `UIBarItem` e adicionar uma imagem maior por meio de um [catálogo de ativos](~/ios/app-fundamentals/images-icons/displaying-an-image.md):

```csharp
barItem.LargeContentSizeImage = UIImage.FromBundle("AccessibleImage");
```

### <a name="navigation-bar"></a>Barra de navegação
iOS 11 introduziu novas funcionalidades para facilitar a leitura de títulos da barra de navegação. Aplicativos podem exibir esse título maior atribuindo o `PrefersLargeTitles` propriedade como true:

```csharp
NavigationController.NavigationBar.PrefersLargeTitles = true;
```

Títulos de maior de configuração em seu aplicativo torna _todos os_ títulos de barras de navegação em seu aplicativo pareçam maiores, conforme ilustrado na captura de tela abaixo:

![Título da navegação grande](visual-design-images/image7.png)

Para controlar quando um grande título é exibido em uma barra de navegação, defina o `LargeTitleDisplayMode` no item de navegação para `Always`, `Never`, ou `Automatic`.

### <a name="search-controller"></a>Controlador de pesquisa

iOS 11 tornou mais fácil de adicionar um controlador de pesquisa diretamente à barra de navegação. Depois de criar um controlador de pesquisa, adicione-o para a barra de navegação com o `SearchController` propriedade:

```csharp
NavigationItem.SearchController = searchController;
```

[![Título da navegação grandes com barra de pesquisa](visual-design-images/image8-sml.png)](visual-design-images/image8-sml.png)

Dependendo da funcionalidade de seu aplicativo, você pode querer ou não a barra de pesquisa para ocultar quando um usuário rolar pela lista. Você poderá ajustar isso usando o `HidesSearchBarWhenScrolling` propriedade.

## <a name="margins"></a>Margens

Apple criou uma nova propriedade – `directionalLayoutMargins` – que pode ser usado para definir o espaço entre os modos de exibição e sub-visualizações. Use `directionalLayoutMargins` com `leading` ou `trailing` inserções. Independentemente se o sistema é um idioma da esquerda para a direita ou da direita para esquerda, o espaçamento em seu aplicativo está definido adequadamente pelo iOS.

No iOS 10 e antes de todas as exibições tinham um tamanho de margem mínima para que eles seriam se alinham. iOS 11 introduziu a opção para substituir esse usando `ViewRespectsSystemMinimumLayoutMargins`. Por exemplo, definir essa propriedade como false permite ajustar seu inserções de borda para zero:

```csharp
ViewRespectsSystemMinimumLayoutMargins = false;
View.LayoutMargins = UIEdgeInsets.Zero;
```
![Margem da imagem mostrando com zero embutida no ios 11](visual-design-images/image9.png)

<a name="fullscreen" />

## <a name="full-screen-content"></a>Conteúdo de tela inteira

iOS 7 [introduzido](~/ios/platform/introduction-to-ios7/ios7-ui.md#fullscreen) `topLayoutGuide` e `bottomLayoutGuide` como uma forma de restringir seus modos de exibição para que eles não são ocultos por barras UIKit e em uma área visível da tela. Eles foram substituídos no iOS 11 em favor do _área segura_.

A área de segurança é uma nova maneira de pensar sobre o espaço visível do seu aplicativo e como as restrições são adicionadas entre uma exibição e um super. Por exemplo, considere a imagem a seguir:

[![Área segura vs superior e guia de layout da parte inferior](visual-design-images/image10-sml.png)](visual-design-images/image10.png)

Anteriormente, se tiver adicionado um modo de exibição e quisesse que ele fique visível na área de verde acima, você poderia restringi-la para o _inferior_ do `TopLayoutGuide` e o _superior_ do `BottomLayoutGuide`. No iOS 11, você faria em vez disso, restringi-lo para o _superior_ e _inferior_ da área de segurança. Segue um exemplo:

```csharp
var safeGuide = View.SafeAreaLayoutGuide;
imageView.TopAnchor.ConstraintEqualTo(safeGuide.TopAnchor).Active = true;
safeGuide.BottomAnchor.ConstraintEqualTo(imageView.BottomAnchor).Active = true;
```

## <a name="table-view"></a>Modo de exibição de tabela

O UITableView teve um número de alterações pequenas, mas significativas, no iOS 11.

Por padrão, cabeçalhos, rodapés e células são agora automaticamente dimensionadas com base em seu conteúdo. Para recusar a esse conjunto de comportamento de dimensionamento automático de `EstimatedRowHeight`, `EstimatedSectionHeaderHeight`, ou `EstimatedSectionFooterHeight` como zero.

No entanto, em algumas circunstâncias (por exemplo, ao adicionar UITableViewController no iOS Designer ou ao usar Storboards existente no construtor de Interface) talvez seja necessário habilitar manualmente o dimensionamento automático de células. Para fazer isso, certifique-se de ter definido as propriedades a seguir no modo de tabela para células, cabeçalhos e rodapés, respectivamente:

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

iOS 11 expandiu a funcionalidade de ações de linha. `UISwipeActionsConfiguration` foi introduzido para definir um conjunto de ações que deve ocorrer quando o usuário passa em ambas as direções em uma linha em uma exibição de tabela. Esse comportamento é semelhante ao que o mail. App nativo. Para obter mais informações, consulte o [ações de linha](~/ios/user-interface/controls/tables/row-action.md) guia.

Modos de exibição de tabela têm suporte para arrastar e soltar no iOS 11. Para obter mais informações, consulte o [arrastar e soltar](~/ios/platform/introduction-to-ios11/drag-and-drop.md#uitableview) guia.


## <a name="related-links"></a>Links relacionados

- [O que há de novo no iOS 11 (Apple)](https://developer.apple.com/ios/)
- [Página de produto atualizados App Store (Apple)](https://developer.apple.com/app-store/product-page/)
- [Atualizar seu aplicativo para iOS 11 (WWDC) (vídeo)](https://developer.apple.com/videos/play/wwdc2017/204/)
