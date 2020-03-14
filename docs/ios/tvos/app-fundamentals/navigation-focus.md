---
title: Trabalhando com navegação e foco do tvOS no Xamarin
description: Este artigo aborda o conceito de foco e como ele é usado para apresentar e tratar a navegação dentro de um aplicativo Xamarin. tvOS.
ms.prod: xamarin
ms.assetid: DD72E95F-AE9B-47D2-B132-5FA5FBD8026E
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 3c754acc3502d7aa2c47264e734187ffe060c029
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79304719"
---
# <a name="working-with-tvos-navigation-and-focus-in-xamarin"></a>Trabalhando com navegação e foco do tvOS no Xamarin

_Este artigo aborda o conceito de foco e como ele é usado para apresentar e tratar a navegação dentro de um aplicativo Xamarin. tvOS._

Este artigo aborda o conceito de [foco](#Focus-and-Selection) e como ele é usado para manipular a [navegação](#Navigation) em uma interface do usuário do aplicativo Xamarin. tvOS. Vamos examinar como os controles de navegação internos do tvOS usam o foco, o realce e a seleção para fornecer a navegação da interface do usuário do seu aplicativo Xamarin. tvOS.

[![](navigation-focus-images/intro01.png "tvOS apps User Interface Navigation")](navigation-focus-images/intro01.png#lightbox)

Em seguida, vamos dar uma olhada em como o foco pode ser usado com [da Parallax](#Focus-and-Parallax) e *imagens em camadas* para fornecer pistas visuais para o estado de navegação atual para o usuário final.

Por fim, veremos como trabalhar com o [foco](#Working-with-Focus), [as atualizações de foco](#Working-with-Focus-Updates), os guias de [foco](#Working-with-Focus-Guides), o [foco em coleções](#Working-with-Focus-in-Collections) e a [habilitação de da Parallax](#enabling-parallax) em exibições de imagem em seus aplicativos Xamarin. tvOS.

<a name="Navigation" />

## <a name="navigation"></a>Navegação

Os usuários do seu aplicativo Xamarin. tvOS não serão interagindo com sua interface diretamente como com iOS, em que eles tocam imagens na tela do dispositivo, mas indiretamente de toda a sala usando o [Siri remoto](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote). Você precisará ter isso em mente ao criar a interface do usuário do aplicativo para que ele flua naturalmente, mas que mantenha o usuário imersos na experiência da Apple TV.

Um aplicativo tvOS bem-sucedido implementa a navegação de forma a dar suporte uniforme à finalidade do aplicativo e à estrutura dos dados que ele apresenta sem chamar atenção para a navegação em si. Projete sua navegação para que ela se sinta natural e familiar sem ter dominado a interface do usuário ou desenhar o foco fora do conteúdo e da experiência do usuário dos aplicativos.

[![](navigation-focus-images/nav01.png "The tvOS settings app")](navigation-focus-images/nav01.png#lightbox)

Ao usar uma Apple TV, o usuário normalmente navega por um conjunto empilhado de telas, cada uma apresentando um determinado conjunto de conteúdo. Por sua vez, cada nova tela pode levar a uma ou mais subtelas de conteúdo usando controles padrão da interface do usuário, como [botões](~/ios/tvos/user-interface/buttons.md), [barras de guias](~/ios/tvos/user-interface/tab-bars.md), tabelas, [exibições de coleção](~/ios/tvos/user-interface/collection-views.md) ou [exibições divididas](~/ios/tvos/user-interface/split-views.md).

Com cada nova tela de dados, o usuário navega mais profundamente nessa pilha de telas. Usando o botão de **menu** no Siri remoto, eles podem navegar para trás pela pilha para retornar a uma tela anterior ou menu principal.

A Apple sugere o seguinte em mente ao criar a navegação para seu aplicativo tvOS:

- **Faça o layout de sua navegação para tornar a localização de conteúdo rápida e fácil** -os usuários desejam acessar o conteúdo em seu aplicativo com o menor número de toques, cliques e passes o mais possível. Simplifique sua navegação e organize o conteúdo para o número mínimo de telas.
- **Crie uma interface fluida usando o Touch** -certifique-se de que um usuário pode se mover entre _itens com foco_ com o mínimo de conflito usando o menor número de gestos possíveis.
- **Design com foco em mente** -como o usuário está interagindo com conteúdo pela sala, eles precisam mover o foco para um item de interface do usuário antes de interagir com ele usando o Siri remoto. Os usuários ficarão frustrados com seu aplicativo se exigirem muitos gestos para que atinjam suas metas.
- **Fornecer navegação para trás por meio do botão de menu** – para criar uma experiência fácil e familiar, permita que os usuários naveguem para trás usando o botão de **menu** do Siri Remote. Pressionar o botão de **menu** sempre deve retornar à tela anterior ou retornar ao menu principal do aplicativo. No nível superior do aplicativo, pressionar o botão de **menu** deve retornar para a tela inicial da Apple TV.
- **Normalmente, evite exibir um botão voltar** – porque pressionar o botão de **menu** no Siri remoto navega para trás pela pilha de tela, evite exibir um controle extra que duplique esse comportamento. Uma exceção a essa regra é para telas de compra ou telas com ações destrutivas (como excluir conteúdo) em que um botão de **cancelamento** também deve ser exibido.
- **Mostre grandes coleções em uma única tela, em vez de muitos** – o Siri Remote foi projetado para fazer a movimentação por uma grande coleção de conteúdo de forma rápida e fácil usando gestos. Se seu aplicativo funciona com uma grande coleção de itens focados, considere mantê-los em uma única tela, em vez de dividi-los em várias telas que exigem mais navegação na parte do usuário.
- **Use controles padrão para navegação** novamente, para criar uma experiência do usuário fácil e familiar, sempre que possível, use controles internos de `UIKit`, como controles de página, barras de guias, controles segmentados, exibições de tabela, exibições de coleção e exibições divididas para a navegação do aplicativo. Como o usuário já está familiarizado com esses elementos, eles poderão, de forma intuitiva, navegar no seu aplicativo.
- **Favorecer a navegação horizontal de conteúdo** -por causa da natureza da Apple TV, passar da esquerda para a direita na Siri remota é mais natural do que para cima e para baixo. Considere essa opção ao criar layouts de conteúdo para seu aplicativo.

<a name="Focus-and-Selection" />

## <a name="focus-and-selection"></a>Foco e seleção

Na Apple TV, uma imagem, um botão ou outro elemento de interface do usuário é considerado _em foco_ quando é o destino da navegação atual.

[![](navigation-focus-images/focus01.png "Focus and Selection example")](navigation-focus-images/focus01.png#lightbox)

Ao contrário dos dispositivos iOS em que o usuário está interagindo diretamente com elementos na tela Touch do dispositivo, os usuários interagem com elementos tvOS de toda a sala usando o Siri remoto. Para apresentar e lidar com essa interação do usuário, a Apple TV usa um modelo baseado em _foco_ .

Usando gestos e pressionamentos de botão no [Siri remoto](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote), o usuário move o foco de um elemento de interface do usuário para outro. Depois de o foco ter sido deslocado para o elemento desejado, o usuário clica para selecionar o conteúdo ou ativar a ação representada por esse elemento.

Conforme as alterações de foco, animações e efeitos sutis (como o efeito de da Parallax em imagens) são usadas para identificar claramente o item de interface do usuário que tem foco no momento.

A Apple tem as seguintes sugestões para trabalhar com o foco e a seleção:

- **Usar controles de interface do usuário internos para efeitos de movimento** – usando `UIKit` e a API de foco em sua interface do usuário, o modelo de foco aplicará automaticamente os efeitos visuais e o movimento padrão aos seus elementos de interface de usuário. Isso faz com que seu aplicativo se sinta nativo e familiar para os usuários da plataforma Apple TV e permite uma movimentação fluida e intuitiva entre os itens com foco.
- **Mover o foco em direções esperadas** – na Apple TV, quase todos os elementos usam a manipulação indireta. Por exemplo, o usuário usa o Siri Remote para mover o foco e o sistema rola automaticamente a interface para manter o item atualmente focalizado. Se seu aplicativo implementar esse tipo de interação, certifique-se de que o foco se mova na direção do gesto do usuário. Portanto, se o usuário passar o dedo para a direita no Siri, o foco remoto deverá ser movido para a direita (o que pode fazer com que a tela role para a esquerda). A única exceção a essa regra são os itens de tela inteira que usam a manipulação direta (onde o dedo para cima move o elemento para cima).
- **Verifique se o item focado é óbvio** – como os usuários estão interagindo com os elementos da interface do usuário do Afar, é essencial que o item atualmente focalizado se destaque. Normalmente, isso será manipulado automaticamente por elementos de `UIKit` internos. Para controles personalizados, use recursos como tamanho do item ou sombra para mostrar o foco.
- **Use da Parallax para fazer com que itens focados respondam** a pequenos gestos circulares no resultado remoto Siri em AdaBoost, movimento em tempo real do item focado. Esse [efeito de da Parallax](#Focus-and-Parallax) é criado em `UIKit` _imagens em camadas_ para dar ao usuário uma noção de conexão com o item focado.
- **Criar itens com foco do tamanho apropriado** -itens grandes com espaçamento amplo são mais fáceis de selecionar e navegar do que itens menores.
- **Criar elemento de interface do usuário para parecer bom focado ou sem foco** – normalmente, a Apple TV representa o item focado aumentando seu tamanho. Verifique se os elementos da interface do usuário de seus aplicativos parecem ótimos em qualquer tamanho de apresentação e, se necessário, forneça ativos para elementos de tamanho maiores.
- **Represente as alterações de foco** de forma fluida-use a animação para esmaecer suavemente entre um estado de itens **focalizado** e sem **foco** para manter as transições de dissonante.
- **Não exibir um cursor** -os usuários esperam navegar pela interface do usuário do aplicativo usando o foco e não movendo um cursor ao lado da tela. A interface do usuário sempre deve usar o modelo de foco para apresentar uma experiência de usuário consistente.

<a name="Working-with-Focus" />

### <a name="working-with-focus"></a>Trabalhando com foco

Pode haver ocasiões em que você deseja criar um controle personalizado que possa se tornar um item focado. Nesse caso, substitua a propriedade `CanBecomeFocused` e retorne `true`, caso contrário, retorna `false`. Por exemplo:

```csharp
public class myView : UIView
{
    public override bool CanBecomeFocused {
        get {return true;}
    }
}
```

A qualquer momento, você pode usar a propriedade `Focused` de um controle de `UIKit` para ver se ele é o item atual. Se `true` o item da interface do usuário tiver foco no momento, caso contrário, não. Por exemplo:

```csharp
// Is my view in focus?
if (myView.Focused) {
    // Do something
    ...
}
```

Embora não seja possível mover o foco diretamente para outro elemento de interface do usuário por meio de código, você pode especificar qual elemento de interface do usuário primeiro obtém o foco quando uma tela é carregada definindo sua propriedade `PreferredFocusedView` como `true`. Por exemplo:

```csharp
// Make the play button the starting focus item
playButton.PreferredFocusedView = true;
```

<a name="Working-with-Focus-Updates" />

### <a name="working-with-focus-updates"></a>Trabalhando com atualizações de foco 

Quando o usuário faz com que o foco se desloque de um elemento de interface do usuário para outro (por exemplo, em resposta a um gesto no Siri remoto), um _evento de atualização de foco_ será enviado para o item que está perdendo o foco e o item ganha foco.

Para elementos personalizados que herdam de `UIView` ou `UIViewController`, você pode substituir vários métodos para trabalhar com o evento de atualização de foco:

- **DidUpdateFocus** -este método será chamado sempre que a exibição ganha ou perde o foco.
- **ShouldUpdateFocus** – Use esse método para definir onde o foco pode ser movido.

Para solicitar que o mecanismo de foco mova o foco de volta para o elemento de interface do usuário `PreferredFocusedView`, chame o método `SetNeedsUpdateFocus` do controlador de exibição.

> [!IMPORTANT]
> Chamar `SetNeedsUpdateFocus` só terá efeito se o controlador de exibição ao qual ele está sendo chamado contiver a exibição que tem o foco no momento.

<a name="Working-with-Focus-Guides" />

### <a name="working-with-focus-guides"></a>Trabalhando com guias de foco

O mecanismo de foco interno do tvOS é ótimo na manipulação de movimentos entre itens que se enquadram em uma grade horizontal e vertical. Normalmente, você não precisa fazer nada mais do que adicionar os elementos da interface do usuário ao seu design de interface e o mecanismo de foco tratará automaticamente o movimento entre esses elementos sem a intervenção do desenvolvedor.

No entanto, pode haver ocasiões, devido à necessidades de seu design de interface do usuário, quando os elementos da interface do usuário não se enquadram em uma grade horizontal e vertical e podem estar inacessíveis porque são diagonais entre si. Isso acontece porque o mecanismo de foco foi projetado para lidar com o movimento simples, para baixo, para a esquerda e para a direita entre os itens da interface do usuário.

Veja o seguinte layout de interface do usuário para obter um exemplo:

 [![](navigation-focus-images/guide01.png "Working with Focus Guides example")](navigation-focus-images/guide01.png#lightbox)

Como o botão **mais informações** não se enquadra em uma grade horizontal e vertical com o botão **comprar** , ele estaria inacessível ao usuário. No entanto, isso pode ser facilmente corrigido usando um _Guia de foco_ para fornecer dicas de movimentação para o mecanismo de foco. 

Um guia de foco (`UIFocusGuide`) expõe uma área não visível da exibição como de foco para o mecanismo de foco, permitindo que o foco seja redirecionado para outra exibição.

Portanto, para resolver o exemplo fornecido acima, o código a seguir pode ser adicionado ao controlador de exibição para criar um guia de foco entre os botões **mais informações** e **comprar** :

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

Primeiro, um novo `UIFocusGuide` é criado e adicionado à coleção de guias de layout do modo de exibição usando o método `AddLayoutGuide`.

Em seguida, as âncoras superior, esquerda, largura e altura do guia de foco são ajustadas em relação aos botões **mais informações** e **comprar** para posicioná-la entre elas. Consulte:

[![](navigation-focus-images/guide02.png "Example Focus Guide")](navigation-focus-images/guide02.png#lightbox)

Também é importante observar que as novas restrições estão sendo ativadas à medida que são criadas definindo sua propriedade `Active` como `true`:

```csharp
FocusGuide.LeftAnchor.ConstraintEqualTo (...).Active = true;
```

Com o novo guia de foco estabelecido e adicionado à exibição, o método de `DidUpdateFocus` do controlador de exibição pode ser substituído e o código a seguir foi adicionado para se mover entre os botões **mais informações** e **comprar** :

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

Primeiro, esse código obtém a `NextFocusedView` do `UIFocusUpdateContext` que foi passado (`context`). Se essa exibição for `null`, nenhum processamento é necessário e o método foi encerrado.

Em seguida, o `nextFocusableItem` é avaliado. Se ele corresponder aos botões **mais informações** ou **comprar** , o foco será enviado para o botão oposto usando a propriedade `PreferredFocusedView` do guia de foco. Por exemplo:

```csharp
// Move from the More Info to Buy button
FocusGuide.PreferredFocusedView = BuyButton;
```

No caso de nenhum botão ser a origem do deslocamento de foco, a propriedade `PreferredFocusedView` é desmarcada:

```csharp
// No valid move
FocusGuide.PreferredFocusedView = null;
```

<a name="Working-with-Focus-in-Collections" />

### <a name="working-with-focus-in-collections"></a>Trabalhando com foco em coleções

Ao decidir se um item individual pode ou não ser focado em um `UICollectionView` ou em um `UITableView`, você substituirá os métodos do `UICollectionViewDelegate` ou `UITableViewDelegate`, respectivamente. Por exemplo:

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

O método `CanFocusItem` retornará `true` se o item atual puder estar em foco, caso contrário retornará `false`.

Se você quiser uma `UICollectionView` ou uma `UITableView` para lembrar e restaurar o foco para o último item quando ele perder e recuperar o foco, defina a propriedade `RemembersLastFocusedIndexPath` como `true`.

<a name="Focus-and-Parallax" />

## <a name="focus-and-parallax"></a>Foco e da Parallax

Como mencionado acima, um elemento de interface do usuário é considerado _em foco_ quando é o item atual durante um evento de navegação. Normalmente, como um item entra em foco, seu tamanho é ligeiramente aumentado e é visualmente privilegiado usando uma sombra. 

Se o usuário fizer um gesto circular e lento no Siri remoto, o item focalizado irá Sway em tempo real em resposta a esse movimento. À medida que o Sway ocorre, um Sheen iluminado é aplicado à sua imagem, fazendo com que a superfície pareça brilhar. Após uma determinada quantidade de inatividade, qualquer conteúdo fora do foco será esmaecido e o item focado aumentará ainda mais. 

Esses efeitos funcionam em conjunto para fornecer uma conexão mental entre o conteúdo na tela da TV e o usuário que interage com a Apple TV do sofá.

Na Apple TV, esse efeito da Parallax é usado em todo o sistema para transmitir uma noção de profundidade 3D e dinâmica a itens em foco. o tvOS usa uma série de imagens transparentes e em [camadas](~/ios/tvos/app-fundamentals/icons-images.md#Layered-Images) que são movidas e dimensionadas dinamicamente para criar esse efeito.

As imagens em camadas são necessárias para o ícone do aplicativo tvOS e têm suporte para o conteúdo de prateleira superior dinâmico. Embora não seja necessário, a Apple sugere enfaticamente a implementação de imagens em camadas em qualquer outro item com foco na interface do usuário do seu aplicativo.

### <a name="enabling-parallax"></a>Habilitando da Parallax

O controle de `UIImageView` (ou qualquer controle que herda de `UIImageView`) oferece suporte automaticamente ao efeito de da Parallax. Por padrão, esse suporte está desabilitado, para habilitá-lo, use o seguinte código:

```csharp
myImageView.AdjustsImageWhenAncestorFocused = true;
```

Com essa propriedade definida como `true`, o modo de exibição de imagem automaticamente obterá o efeito de da Parallax quando for selecionado pelo usuário e em foco.

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo abordou o conceito de foco e como ele é usado para manipular a navegação em uma interface do usuário do aplicativo Xamarin. tvOS. Ele examina como os controles de navegação internos do tvOS usam foco, realce e seleção para fornecer navegação. Em seguida, vimos como o foco pode ser usado com da Parallax e imagens em camadas para fornecer pistas visuais para o estado de navegação atual para o usuário final. Por fim, ele examinou o trabalho com o foco, as atualizações de foco, o foco em coleções e a habilitação de da Parallax.

## <a name="related-links"></a>Links Relacionados

- [Amostras do tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guias de interface humana do tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guia de programação de aplicativo para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
