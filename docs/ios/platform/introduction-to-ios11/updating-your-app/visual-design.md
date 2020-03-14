---
title: Atualizações de Design Visual no iOS 11
description: Este documento descreve as atualizações de Design Visual introduzidas no iOS 11. Ele aborda as alterações nas barras de navegação, controladores de pesquisa, margens, conteúdo de tela inteira e exibições de tabela.
ms.prod: xamarin
ms.assetid: 7C300B94-0FAF-492E-A326-877419A1824B
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/13/2016
ms.openlocfilehash: e5a61af4cd8a09df3ffddb74658f646aa8edfa1f
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79304754"
---
# <a name="visual-design-updates-in-ios-11"></a>Atualizações de Design Visual no iOS 11

No iOS 11, a Apple introduziu novas alterações visuais, incluindo atualizações na barra de navegação, na barra de pesquisa e nos modos de exibição de tabela. Além disso, foram feitas melhorias para permitir mais flexibilidade em relação às margens e ao conteúdo de tela inteira. Essas alterações são abordadas neste guia. 

Para obter informações especificamente sobre como projetar para iPhone X, Assista [à criação de vídeo da Apple para iPhone x](https://developer.apple.com/videos/play/fall2017/801/) .

## <a name="uikit"></a>UIKit

As barras de UIKit foram adaptadas no iOS 11 para torná-las mais acessíveis para os usuários finais.

Uma dessas alterações é uma nova exibição de HUD que aparece quando um usuário se pressiona em um item de barra. Para habilitar isso, defina a propriedade `largeContentSizeImage` em `UIBarItem` e adicione uma imagem maior por meio de um [Catálogo de ativos](~/ios/app-fundamentals/images-icons/displaying-an-image.md):

```csharp
barItem.LargeContentSizeImage = UIImage.FromBundle("AccessibleImage");
```

### <a name="navigation-bar"></a>Barra de navegação
o iOS 11 introduziu uma nova funcionalidade para facilitar a leitura dos títulos das barras de navegação. Os aplicativos podem exibir esse título maior atribuindo a propriedade `PrefersLargeTitles` como true:

```csharp
NavigationController.NavigationBar.PrefersLargeTitles = true;
```

Definir títulos maiores em seu aplicativo faz com que _todos os_ títulos das barras de navegação em seu aplicativo pareçam maiores, conforme ilustrado na seguinte captura de tela:

![Título de navegação grande](visual-design-images/image7.png)

Para controlar quando um título grande é exibido em uma barra de navegação, defina o `LargeTitleDisplayMode` no item de navegação como `Always`, `Never`ou `Automatic`.

### <a name="search-controller"></a>Controlador de pesquisa

o iOS 11 facilitou a adição de um controlador de pesquisa diretamente à barra de navegação. Depois de criar um controlador de pesquisa, adicione-o à sua barra de navegação com a propriedade `SearchController`:

```csharp
NavigationItem.SearchController = searchController;
```

[![título de navegação grande com barra de pesquisa](visual-design-images/image8-sml.png)](visual-design-images/image8-sml.png#lightbox)

Dependendo da funcionalidade do seu aplicativo, você pode ou não desejar que a barra de pesquisa seja ocultada quando um usuário rolar por uma lista. Você pode ajustar isso usando a propriedade `HidesSearchBarWhenScrolling`.

## <a name="margins"></a>{1&gt;Margens&lt;1}

A Apple criou uma nova propriedade – `directionalLayoutMargins` – que pode ser usada para definir o espaço entre exibições e subexibições. Use `directionalLayoutMargins` com `leading` ou `trailing` indefinições. Independentemente de o sistema ser um idioma da esquerda para a direita ou da direita para a esquerda, o espaçamento em seu aplicativo é definido adequadamente pelo iOS.

No iOS 10 e anterior, todas as exibições tinham um tamanho de margem mínimo para o qual elas se alinhariam. o iOS 11 introduziu a opção para substituir isso usando `ViewRespectsSystemMinimumLayoutMargins`. Por exemplo, definir essa propriedade como false permite que você ajuste suas indefinições de borda para zero:

```csharp
ViewRespectsSystemMinimumLayoutMargins = false;
View.LayoutMargins = UIEdgeInsets.Zero;
```

![Imagem mostrando margem com inserção zero no Ios 11](visual-design-images/image9.png)

<a name="fullscreen" />

## <a name="full-screen-content"></a>Conteúdo de tela inteira

o iOS 7 [introduziu](~/ios/platform/introduction-to-ios7/ios7-ui.md#fullscreen) `topLayoutGuide` e `bottomLayoutGuide` como uma forma de restringir suas exibições para que elas não fiquem ocultas por barras de UIKit e estejam em uma área visível da tela. Eles foram preteridos no iOS 11 em favor da _área de segurança_.

A área segura é uma nova maneira de pensar sobre o espaço visível do seu aplicativo e como as restrições são adicionadas entre uma exibição e uma superexibição. Por exemplo, considere a imagem a seguir:

[Guia de layout da área de ![segura versus superior e inferior](visual-design-images/image10-sml.png)](visual-design-images/image10.png#lightbox)

Anteriormente, se você adicionou uma exibição e queria que ela estivesse visível na área verde acima, você a restringiria na _parte inferior_ da `TopLayoutGuide` e na _parte superior_ da `BottomLayoutGuide`. No iOS 11, você seria restringi-lo na _parte superior_ e na _parte inferior_ da área de segurança. Há um exemplo a seguir:

```csharp
var safeGuide = View.SafeAreaLayoutGuide;
imageView.TopAnchor.ConstraintEqualTo(safeGuide.TopAnchor).Active = true;
safeGuide.BottomAnchor.ConstraintEqualTo(imageView.BottomAnchor).Active = true;
```

## <a name="table-view"></a>Exibição de tabela

O UITableView tinha uma série de alterações pequenas, mas significativas, no iOS 11.

Por padrão, os cabeçalhos, os rodapés e as células agora são dimensionados automaticamente com base no seu conteúdo. Para recusar esse comportamento de dimensionamento automático, defina o `EstimatedRowHeight`, `EstimatedSectionHeaderHeight`ou `EstimatedSectionFooterHeight` como zero.

No entanto, em algumas circunstâncias (por exemplo, ao adicionar UITableViewController no designer do iOS ou ao usar o Storboards existente no Interface Builder), talvez seja necessário habilitar manualmente as células de dimensionamento automático. Para fazer isso, verifique se você definiu as seguintes propriedades no modo de exibição de tabela para células, cabeçalhos e rodapés, respectivamente:

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

o iOS 11 expandiu a funcionalidade das ações de linha. `UISwipeActionsConfiguration` foi introduzido para definir um conjunto de ações que devem ocorrer quando o usuário passa em uma das direções em uma linha em uma exibição de tabela. Esse comportamento é semelhante ao do mail. app nativo. Para obter mais informações, consulte o guia de [ações de linha](~/ios/user-interface/controls/tables/row-action.md) .

As exibições de tabela têm suporte para arrastar e soltar no iOS 11. Para obter mais informações, consulte a guia de [arrastar e soltar](~/ios/platform/introduction-to-ios11/drag-and-drop.md#uitableview) .

## <a name="related-links"></a>Links Relacionados

- [O que há de novo no iOS 11 (Apple)](https://developer.apple.com/ios/)
- [Página de produto da App Store atualizada (Apple)](https://developer.apple.com/app-store/product-page/)
- [Projetando para iPhone X (Apple) (vídeo)](https://developer.apple.com/videos/play/fall2017/801/)
- [Atualizando seu aplicativo para iOS 11 (WWDC) (vídeo)](https://developer.apple.com/videos/play/wwdc2017/204/)
