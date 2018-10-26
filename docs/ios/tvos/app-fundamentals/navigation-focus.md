---
title: Trabalhando com foco no Xamarin e navegação do tvOS
description: Este artigo aborda o conceito de foco e como ele é usado para apresentar e tratar a navegação dentro de um aplicativo xamarin. tvos.
ms.prod: xamarin
ms.assetid: DD72E95F-AE9B-47D2-B132-5FA5FBD8026E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 1cfa51b8e5434480d7d15fbf23d78f8b8735f16a
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50112579"
---
# <a name="working-with-tvos-navigation-and-focus-in-xamarin"></a>Trabalhando com foco no Xamarin e navegação do tvOS

_Este artigo aborda o conceito de foco e como ele é usado para apresentar e tratar a navegação dentro de um aplicativo xamarin. tvos._


Este artigo aborda o conceito de [foco](#Focus-and-Selection) e como ela é usada para manipular [navegação](#Navigation) na Interface do usuário de um aplicativo xamarin. tvos. Vamos examinar como o controles de navegação do tvOS internos usar foco, o realce e a seleção para fornecer uma navegação de Interface de usuário do seu aplicativo tvos.

[![](navigation-focus-images/intro01.png "Navegação de Interface do usuário de aplicativos de tvOS")](navigation-focus-images/intro01.png#lightbox)

Em seguida, vamos dar uma olhada em como o foco pode ser usado com [Parallax](#Focus-and-Parallax) e *imagens em camadas* fornecer pistas visuais para o estado atual de navegação para o usuário final.

Por fim, vamos examinar trabalhando com [foco](#Working-with-Focus), [atualizações de foco](#Working-with-Focus-Updates), [guias foco](#Working-with-Focus-Guides), [foco nas coleções](#Working-with-Focus-in-Collections) e [ Habilitação da Parallax](#Enabling-Parallax) nos modos de exibição de imagem em seus aplicativos xamarin. tvos.

<a name="Navigation" />

## <a name="navigation"></a>Navegação

Usuários de seu aplicativo tvos não interagirá com sua interface diretamente como com o iOS onde eles tap imagens na tela do dispositivo, mas indiretamente em toda a sala usando o [Siri remoto](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote). Você precisará ter isso em mente ao projetar a Interface do usuário do seu aplicativo para que ele flua naturalmente, ainda que mantém o usuário tenha a experiência de Apple TV.

Um aplicativo tvOS bem-sucedida implementa navegação de uma forma perfeita com suporte para a finalidade do aplicativo e a estrutura dos dados que ele apresenta sem chamar a atenção para a navegação em si. Projete sua navegação para que ela se parece natural e familiar sem dominar a Interface do usuário ou o foco para fora o conteúdo e a experiência do usuário de aplicativos de desenho.

[![](navigation-focus-images/nav01.png "O aplicativo de configurações do tvOS")](navigation-focus-images/nav01.png#lightbox)

Embora o uso de uma Apple TV, o usuário normalmente navega por meio de um conjunto gráfico empilhado de telas, cada apresentação de um determinado conjunto de conteúdo. Por sua vez, cada nova tela pode levar a uma ou mais telas de conteúdo usando controles de interface do usuário padrão, como subpropriedades [botões](~/ios/tvos/user-interface/buttons.md), [barras de guia](~/ios/tvos/user-interface/tab-bars.md), tabelas, [exibições de coleção](~/ios/tvos/user-interface/collection-views.md) ou [ Dividir modos de exibição](~/ios/tvos/user-interface/split-views.md).

Com cada nova tela de dados, o usuário navegar mais profunda para essa pilha de telas. Usando o **Menu** botão no controle Siri remoto, eles poderão navegar para trás por meio da pilha para retornar a uma tela anterior ou o Menu principal.

A Apple sugere tendo o seguinte em mente ao projetar a navegação para seu aplicativo tvOS:

- **Layout de sua navegação para fazer rapidamente encontrar conteúdo e fácil** -usuários deseja acessar o conteúdo dentro de seu aplicativo no menor número de toques, clica e passa o dedo quanto possível. Simplifique sua navegação e organizar o conteúdo para os números mínimo de telas.
- **Criar um fluido Interface usando o Touch** -Certifique-se de que um usuário pode se mover entre _focalizável itens_ com muito atrito usando o menor número de possíveis de gestos.
- **Design com foco em mente** -uma vez que o usuário está interagindo com o conteúdo dentro de uma sala, eles precisam mover o foco para um item de Interface do usuário antes de interagir com ele usando o Siri remoto. Os usuários receberão frustrados com seu aplicativo se ele requer muitos gestos para atingir suas metas.
- **Fornecer com versões anteriores a navegação por meio do botão de Menu** – para criar uma experiência fácil e familiar, permitir que os usuários para navegar para trás usando o Siri Remote **Menu** botão. Pressionar o **Menu** botão deve sempre retornar à tela anterior ou retornar ao Menu de principal do aplicativo. No nível superior do aplicativo, pressionar o **Menu** botão deve retornar para a tela inicial para Apple TV.
- **Normalmente Evite exibindo um botão Voltar** – porque pressionando o **Menu** botão no Siri remoto navega para trás pela pilha da tela, evitar a exibição de um controle extra que duplica a esse comportamento. Uma exceção a essa regra é para a compra de telas ou telas com ações destrutivas (como excluindo conteúdo) em que um **Cancelar** botão também deve ser exibido.
- **Mostrar grandes coleções em uma única tela, em vez de muitos** -o Siri remoto foi projetado para tornar movendo por meio de uma grande coleção de conteúdo rápida e fácil usando gestos. Se seu aplicativo funciona com uma grande coleção de itens focalizável, considere a mantê-los em uma única tela, em vez de dividi-los em várias telas que exigem mais navegação na parte do usuário.
- **Use controles padrão para navegação** -novamente, para criar uma experiência de usuário fácil e familiar, sempre que possível, usar interno `UIKit` controles como controles de página, barras de guia, controles segmentados, modos de exibição de tabela, exibições de coleção e divisão Modos de exibição para a navegação do seu aplicativo. Uma vez que o usuário já estiver familiarizado com esses elementos, eles poderão intuitivamente navegar de seu aplicativo.
- **Favorecer a navegação de conteúdo Horizontal** -devido à natureza da Apple TV, o dedo para esquerda para a direita no Siri remoto é mais natural do que para cima e para baixo. Considere esta opção durante a criação de layouts de conteúdo para seu aplicativo.

<a name="Focus-and-Selection" />

## <a name="focus-and-selection"></a>Foco e a seleção

Na Apple TV, uma imagem, o botão ou outro elemento de interface do usuário é considerado _em foco_ quando ele é o destino do painel de navegação atual.

[![](navigation-focus-images/focus01.png "Exemplo de foco e a seleção")](navigation-focus-images/focus01.png#lightbox)

Ao contrário de dispositivos iOS em que o usuário diretamente está interagindo com elementos na tela de toque do dispositivo, os usuários interagem com os elementos de tvOS do salão usando Siri remoto. Para apresentar e lidar com essa interação do usuário, a Apple TV usa um _foco_ com base em modelo.

Usando gestos e botão pressiona na [Siri remoto](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote), o usuário move o foco de um elemento de interface do usuário para outro. Depois que o foco mudou para o elemento desejado, o usuário clica para selecionar o conteúdo ou ativar a ação representada por esse elemento.

Como as alterações de foco, animações sutis e efeitos (como o efeito da Parallax em imagens) são usados para identificar claramente o item de Interface do usuário que tem foco no momento.

Apple tem as sugestões a seguir para trabalhar com o foco e a seleção:

- **Usar controles de interface do usuário internos para efeitos de animação** – usando `UIKit` e a API de foco na Interface do usuário, o modelo de foco será aplicada automaticamente a animação padrão e efeitos visuais para seus elementos de interface do usuário. Isso faz com que seu aplicativo se sentir nativo e familiar para os usuários da plataforma Apple TV e possibilita a movimentação intuitiva e flexível entre os itens de controle.
- **Move o foco em direções esperado** -na Apple TV, quase todos os elementos usa manipulação indireta. Por exemplo, o usuário usa o Siri remoto para mover o foco e o sistema automaticamente rola para a interface para manter o item focalizado atualmente visíveis. Se seu aplicativo implementa esse tipo de interação, certifique-se de que o foco move na direção do gesto do usuário. Portanto, se o usuário passa o dedo em foco o Siri remoto deve mover para a direita (que pode fazer com que a tela para rolar para a esquerda). A única exceção a essa regra é itens de tela inteira que usam manipulação direta (onde passar o dedo para cima move o elemento para cima).
- **Certifique-se de que o Item focalizado é Obvious** -como seus usuários estão interagindo com seus elementos de interface do usuário à distância, é essencial que o item focalizado atualmente se destaca. Normalmente isso pode ser manipulado automaticamente por interno `UIKit` elementos. Para controles personalizados, use recursos como o tamanho do item ou de sombra para mostrar o foco.
- **Usar Parallax para tornar voltada para itens responsivos** - pequeno, gestos circulares em Siri remoto resultam em movimento suave e em tempo real do item focalizado. Isso [efeito Parallax](#Focus-and-Parallax) está incorporada `UIKit` _imagens em camadas_ dar ao usuário um senso de conexão para o item focalizado.
- **Criar itens de controle de tamanho apropriado** -itens grandes com um amplo espaçamento são mais fáceis de selecionar e navegar que itens menores.
- **Criar o elemento de interface do usuário para ser boa o concentradas ou Unfocused** -normalmente Apple TV representa o Item focalizado aumentando seu tamanho. Certifique-se de que elementos de interface do usuário do seus aplicativos ótimo em qualquer tamanho de apresentação e, se necessário, fornecem ativos para elementos de tamanhos maiores.
- **Representar foco alterações com fluidez** -Use animação para desaparecer perfeitamente entre itens **focalizado** e **Unfocused** estado para manter as transições de ser brusca.
- **Não exibir um Cursor** -os usuários esperam navegar da interface do usuário do seu aplicativo usando o foco e não ao mover um cursor em torno da tela. A Interface do usuário sempre deve usar o modelo de foco para apresentar uma experiência de usuário consistente.

<a name="Working-with-Focus" />

### <a name="working-with-focus"></a>Trabalhando com foco

Pode haver ocasiões em que você deseja criar um controle personalizado que pode se tornar um Item de controle. Se então substituir a `CanBecomeFocused` propriedade e retorno `true`, caso contrário retorne `false`. Por exemplo:

```csharp
public class myView : UIView
{
    public override bool CanBecomeFocused {
        get {return true;}
    }
}
```

A qualquer momento, você pode usar o `Focused` propriedade de um `UIKit` controle para ver se o item atual. Se `true` o item de interface do usuário atualmente tem o foco, caso contrário não existir. Por exemplo:

```csharp
// Is my view in focus?
if (myView.Focused) {
    // Do something
    ...
}
```

Enquanto você diretamente não é possível mover o foco para outro elemento de interface do usuário por meio de código, você pode especificar qual elemento de interface do usuário primeiro obtém foco quando uma tela é carregada, definindo sua `PreferredFocusedView` propriedade para `true`. Por exemplo:

```csharp
// Make the play button the starting focus item
playButton.PreferredFocusedView = true;
```

<a name="Working-with-Focus-Updates" />

### <a name="working-with-focus-updates"></a>Trabalhando com as atualizações de foco 

Quando o usuário faz com que o foco mudar de um elemento de interface do usuário para outro (por exemplo, em resposta a um gesto no Siri remoto) uma _evento de atualização de foco_ será enviado para o item de perder o foco e o item que recebeu o foco.

Para elementos personalizados que herdam `UIView` ou `UIViewController`, você pode substituir os vários métodos para trabalhar com o evento de atualização de foco:

- **DidUpdateFocus** -esse método será chamado sempre que o modo de exibição ganha ou perde o foco.
- **ShouldUpdateFocus** -Use esse método para definir onde o foco é permitido mover.

Para solicitar que o mecanismo de foco move o foco volta para o `PreferredFocusedView` elemento de interface do usuário, chamada de `SetNeedsUpdateFocus` método do controlador de exibição.

> [!IMPORTANT]
> Chamar `SetNeedsUpdateFocus` terá efeito apenas se o controlador de exibição que está sendo chamado contra contém a exibição que tem foco no momento.




<a name="Working-with-Focus-Guides" />

### <a name="working-with-focus-guides"></a>Trabalhando com guias de foco

O mecanismo de foco incorporados tvOS é excelente para manipulação movimentações entre os itens que se enquadram em uma grade horizontal e vertical. Normalmente, você precisa fazer nada mais do que adicionar que os elementos de interface do usuário para o design da interface e o mecanismo de foco manipulará automaticamente a movimentação entre esses elementos sem a intervenção do desenvolvedor.

No entanto, pode haver vezes, devido às necessidades de seu design de interface do usuário, quando não se enquadram em uma grade horizontal e vertical de elementos de interface do usuário e podem estar inacessíveis por estarem diagonais uns aos outros. Isso acontece porque o mecanismo de foco foi projetado para lidar com simples para cima, para baixo, esquerda e direita movimentação entre apenas os itens de interface do usuário.

Execute o seguinte layout de interface do usuário para obter um exemplo:

 [![](navigation-focus-images/guide01.png "Trabalhar com guias de foco de exemplo")](navigation-focus-images/guide01.png#lightbox)
 
Porque o **mais informações** botão não estiver em uma grade horizontal e vertical com o **comprar** botão, ele seria inacessível ao usuário. No entanto, isso pode ser facilmente corrigido usando um _guia de foco_ para fornecer dicas de movimentação para o mecanismo de foco. 

Um guia de foco (`UIFocusGuide`) expõe uma área não visíveis do modo de exibição como Focusable para o mecanismo de foco, permitindo assim que o foco para ser redirecionado para outro modo de exibição.

Portanto, para resolver o exemplo acima, o código a seguir poderia ser adicionado ao controlador de exibição para criar um guia de foco entre a **mais informações** e **comprar** botões:

```csharp
public UIFocusGuide FocusGuide = new UIFocusGuide ();
...

public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Add Focus Guide to layout
    View.AddLayoutGuide (FocusGuide);

    // Define Focus Guide that will allow the user to move
    // between the More Info and Buy buttons.
    FocusGuide.LeftAnchor.ConstraintEqualTo (BuyButton.LeftAnchor).Active = true;
    FocusGuide.TopAnchor.ConstraintEqualTo (MoreInfoButton.TopAnchor).Active = true;
    FocusGuide.WidthAnchor.ConstraintEqualTo (BuyButton.WidthAnchor).Active = true;
    FocusGuide.HeightAnchor.ConstraintEqualTo (MoreInfoButton.HeightAnchor).Active = true;
}
```

Primeiro, um novo `UIFocusGuide` é criado e adicionado à coleção de guia do Layout do modo de exibição usando o `AddLayoutGuide` método.

Em seguida, o guia de foco superior, esquerda, largura e Altura âncoras são ajustadas relativo a **de mais informações** e **comprar** botões para posicioná-la entre eles. Consulte:

[![](navigation-focus-images/guide02.png "Guia de foco de exemplo")](navigation-focus-images/guide02.png#lightbox)

Também é importante observar que as novas restrições estão sendo ativadas conforme eles são criados definindo sua `Active` propriedade para `true`:

```csharp
FocusGuide.LeftAnchor.ConstraintEqualTo (...).Active = true;
```

Com o novo guia de foco estabelecido e adicionado para a exibição, o controlador de exibição `DidUpdateFocus` método pode ser substituído e o código a seguir é adicionado ao mover entre o **de mais informações** e **comprar** botões:

```csharp
public override void DidUpdateFocus (UIFocusUpdateContext context, UIFocusAnimationCoordinator coordinator)
{
    base.DidUpdateFocus (context, coordinator);

    // Get next focusable item from context
    var nextFocusableItem = context.NextFocusedView;

    // Anything to process?
    if (nextFocusableItem == null) return;

    // Decide the next focusable item based on the current
    // item with focus
    if (nextFocusableItem == MoreInfoButton) {
        // Move from the More Info to Buy button
        FocusGuide.PreferredFocusedView = BuyButton;
    } else if (nextFocusableItem == BuyButton) {
        // Move from the Buy to the More Info button
        FocusGuide.PreferredFocusedView = MoreInfoButton;
    } else {
        // No valid move
        FocusGuide.PreferredFocusedView = null;
    }
}
```

Primeiro, esse get de código a `NextFocusedView` do `UIFocusUpdateContext` que foi passado (`context`). Se essa exibição é `null`, não é necessário nenhum processamento e o método foi encerrado.

Em seguida, o `nextFocusableItem` é avaliada. Se ele corresponder a qualquer um de **mais informações** ou **comprar** botões, o foco é enviado para o botão oposto usando o guia de foco `PreferredFocusedView` propriedade. Por exemplo:

```csharp
// Move from the More Info to Buy button
FocusGuide.PreferredFocusedView = BuyButton;
```

No caso em que nenhum botão é a origem da mudança de foco, o `PreferredFocusedView` propriedade é limpa:

```csharp
// No valid move
FocusGuide.PreferredFocusedView = null;
```

<a name="Working-with-Focus-in-Collections" />

### <a name="working-with-focus-in-collections"></a>Trabalhando com foco em coleções

Ao decidir se um item individual pode ser focalizável em uma `UICollectionView` ou um `UITableView`, você vai substituir os métodos da `UICollectionViewDelegate` ou `UITableViewDelegate` , respectivamente. Por exemplo:

```csharp
public class CardHandDelegate : UICollectionViewDelegateFlowLayout
{
    ...
    public override bool CanFocusItem (UICollectionView collectionView, NSIndexPath indexPath)
    {
        if (indexPath == null) {
            return false;
        } else {
            var controller = collectionView as CardHandViewController;
            return !controller.Hand [indexPath.Row].IsFaceDown;
        }
    }
}
```

O `CanFocusItem` retorn `true` se o item atual pode estar em foco, caso contrário, retorna `false`.

Se você quiser que um `UICollectionView` ou um `UITableView` para lembrar e restaura o foco para o último item quando ele perde e recupera o foco, defina o `RemembersLastFocusedIndexPath` propriedade `true`.

<a name="Focus-and-Parallax" />

## <a name="focus-and-parallax"></a>Foco e Parallax

Como mencionado acima, um elemento de Interface do usuário é considerado _em foco_ quando ele é o item atual durante um evento de navegação. Geralmente trata de um item em foco, seu tamanho é aumentado ligeiramente e sua visualmente elevação usando uma sombra. 

Se o usuário faz um gesto lento, circular no Siri remoto, o Item com foco será sway em tempo real em resposta a esse movimento. Enquanto o sway ocorre, um trabalho iluminado é aplicado à sua imagem fazendo com que a superfície parecem se destacam. Após um determinado período de inatividade, qualquer conteúdo fora de foco escurece e o item focalizado aumentará ainda maior. 

Esses efeitos trabalham juntos para fornecer uma conexão mental entre o conteúdo na tela da TV e o usuário interagir com a Apple TV de sofá.

Na Apple TV, esse efeito Parallax é usado em todo o sistema para transmitir um senso de profundidade 3D e dynamics para itens em foco. tvOS usa uma série de transparente, [imagens em camadas](~/ios/tvos/app-fundamentals/icons-images.md#Layered-Images) que ele se move e dimensiona dinamicamente para criar esse efeito.

Imagens em camadas são necessárias para ícone do aplicativo tvOS e tem suporte para conteúdo dinâmico da prateleira superior. Embora não seja necessário, a Apple sugere enfaticamente implementar imagens em camadas em outros itens focalizável na interface de usuário do seu aplicativo.

### <a name="enabling-parallax"></a>Habilitação da Parallax

O `UIImageView` controle (ou qualquer controle que herda de `UIImageView`) automaticamente dá suporte ao efeito Parallax. Por padrão, esse suporte está desabilitado, habilitá-la, use o seguinte código:

```csharp
myImageView.AdjustsImageWhenAncestorFocused = true;
```

Com essa propriedade definida como `true`, o modo de exibição de imagem automaticamente terá o efeito da Parallax quando ele é selecionado pelo usuário e em foco.

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo abordou o conceito de foco e como ele é usado para tratar a navegação na Interface do usuário de um aplicativo xamarin. tvos. Ele examinar como o controles de navegação do tvOS internos usar foco, o realce e a seleção para fornecer uma navegação. Em seguida, ele examinou como o foco pode ser usado com Parallax e imagens em camadas para fornecer pistas visuais para o estado atual de navegação para o usuário final. Por fim, ele examinou a trabalhar com foco, as atualizações de foco, foco nas coleções e habilitando Parallax.




## <a name="related-links"></a>Links relacionados

- [Amostras do tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [Guias de Interface humana do tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guia de programação de aplicativo para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
