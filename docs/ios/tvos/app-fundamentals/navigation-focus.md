---
title: "Trabalhando com foco e navegação"
description: "Este artigo aborda o conceito de foco e como ele é usado para apresentar e tratar navegação dentro de um aplicativo Xamarin.tvOS."
ms.topic: article
ms.prod: xamarin
ms.assetid: DD72E95F-AE9B-47D2-B132-5FA5FBD8026E
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 489f3bd43ff4515000127ac29de197435493d5a9
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="working-with-navigation-and-focus"></a>Trabalhando com foco e navegação

_Este artigo aborda o conceito de foco e como ele é usado para apresentar e tratar navegação dentro de um aplicativo Xamarin.tvOS._


Este artigo aborda o conceito de [foco](#Focus-and-Selection) e como ele é usado para tratar [navegação](#Navigation) na Interface de usuário do aplicativo um Xamarin.tvOS. Vamos examinar como os controles de navegação de tvOS internos usar foco, realce e seleção para fornecer a navegação de Interface de usuário do seu aplicativo Xamarin.tvOS.

[![](navigation-focus-images/intro01.png "aplicativos tvOS navegação da Interface de usuário")](navigation-focus-images/intro01.png#lightbox)

Em seguida, vamos dar uma olhada em como o foco pode ser usado com [Parallax](#Focus-and-Parallax) e *imagens em camadas* para fornecer dicas visuais para o estado atual de navegação para o usuário final.

Por fim, vamos examinar trabalhando com [foco](#Working-with-Focus), [foco atualizações](#Working-with-Focus-Updates), [foco guias](#Working-with-Focus-Guides), [foco nas coleções](#Working-with-Focus-in-Collections) e [ Habilitação da Parallax](#Enabling-Parallax) nos modos de exibição de imagem em seus aplicativos Xamarin.tvOS.

<a name="Navigation" />

## <a name="navigation"></a>Navegação

Os usuários do seu aplicativo Xamarin.tvOS não interagirá com sua interface diretamente como com iOS onde eles tocar imagens na tela do dispositivo, mas indiretamente de entre o espaço usando o [Siri remoto](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote). Você precisará ter isso em mente ao projetar a Interface do usuário do aplicativo para que flui naturalmente, mas mantém o usuário tenha a experiência de Apple TV.

Um aplicativo tvOS bem-sucedida implementa a navegação de forma perfeita com suporte para a finalidade do aplicativo e a estrutura dos dados que apresenta sem chamar a atenção para a navegação em si. Crie a navegação para que ele parece natural e familiar sem Dominando a Interface do usuário ou o desenho de foco de conteúdo e a experiência do usuário de aplicativos.

[![](navigation-focus-images/nav01.png "O aplicativo de configurações de tvOS")](navigation-focus-images/nav01.png#lightbox)

Enquanto usar um Apple TV, o usuário normalmente navega por um conjunto de empilhados de telas, cada uma apresentação de um determinado conjunto de conteúdo. Por sua vez, cada nova tela pode levar a uma ou mais sub telas de conteúdo usando controles de interface do usuário padrão, como [botões](~/ios/tvos/user-interface/buttons.md), [guia barras](~/ios/tvos/user-interface/tab-bars.md), tabelas, [exibições de coleção](~/ios/tvos/user-interface/collection-views.md) ou [ Dividir exibições](~/ios/tvos/user-interface/split-views.md).

Com cada nova tela de dados, o usuário navegar mais profunda para esta pilha de telas. Usando o **Menu** botão remoto Siri, podem navegar para trás por meio da pilha para retornar a uma tela anterior ou o Menu principal.

Apple sugere com o seguinte em mente ao projetar a navegação para seu aplicativo tvOS:

- **Layout a navegação para tornar rápido Localizar conteúdo e fácil** -desejado de usuários para acessar o conteúdo dentro de seu aplicativo no menor número de toques, clica e mostra quanto possível. Simplificar a navegação e organizar o conteúdo para os números mínimo das telas.
- **Criar um fluxo Interface usando Touch** -Certifique-se de que um usuário pode mover entre _itens Focusable é_ com mínima fricção usando o menor número possível de gestos.
- **Design com foco em mente** -desde que o usuário está interagindo com o conteúdo dentro de uma sala, eles precisam move o foco para um item de Interface do usuário antes de interagir com ele usando o controle remoto de Siri. Os usuários obterão frustrados com seu aplicativo se ele requer muitos gestos para atingir suas metas.
- **Fornecer com versões anteriores a navegação por meio do botão de Menu** - para criar uma experiência mais fácil e familiar, permitem aos usuários navegar usando versões anteriores de Siri Remote **Menu** botão. Pressionar o **Menu** botão deve sempre retornar para a tela anterior ou retornar ao Menu de principal do aplicativo. No nível superior do aplicativo, pressionando a **Menu** botão deve retornar à tela de início para Apple TV.
- **Normalmente Evite exibindo um botão Voltar** – porque pressionando o **Menu** botão no controle remoto Siri navega para trás pela pilha da tela, evite a exibição de um controle extra que duplica esse comportamento. Uma exceção a essa regra é para comprar telas ou telas com ações (como a exclusão de conteúdo) em que um **Cancelar** botão deve ser exibido também.
- **Mostrar coleções grandes em uma única tela, em vez de muitos** -o Siri remoto foi projetado para tornar movendo por meio de uma grande coleção de conteúdo rápida e fácil usando gestos. Se o aplicativo funciona com uma grande coleção de itens de controle, considere a possibilidade de mantê-los em uma única tela, em vez de dividi-los em várias telas que exigem mais navegação na parte do usuário.
- **Use controles padrão para navegação** -novamente, para criar uma experiência de usuário fácil e familiar, sempre que possível, use interno `UIKit` controles como controles de página, guia barras, controles segmentado, modos de exibição de tabela, exibições de coleção e divisão Modos de exibição de navegação do aplicativo. Desde que o usuário já estiver familiarizado com esses elementos, eles poderão intuitivamente navegue seu aplicativo.
- **Favorecer a navegação de conteúdo Horizontal** -devido à natureza da Apple TV, passar o dedo esquerda para a direita no controle remoto Siri é mais natural do que para cima e para baixo. Considere esta opção ao criar layouts de conteúdo para seu aplicativo.

<a name="Focus-and-Selection" />

## <a name="focus-and-selection"></a>Foco e seleção

No Apple TV, uma imagem, o botão ou outro elemento de interface do usuário é considerado como _em foco_ quando ela é o destino da navegação atual.

[![](navigation-focus-images/focus01.png "Exemplo de foco e seleção")](navigation-focus-images/focus01.png#lightbox)

Ao contrário de dispositivos iOS onde o usuário diretamente está interagindo com elementos na tela de toque do dispositivo, os usuários interagem com elementos tvOS de dentro de uma sala usando Siri remoto. Para apresentar e lidar com essa interação do usuário, a Apple TV usa um _foco_ com base em modelo.

Usar o botão e gestos pressiona no [Siri remoto](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote), o usuário move o foco de um elemento de interface do usuário para outro. Depois que o foco mudou para o elemento desejado, o usuário clica para selecionar o conteúdo ou ativar a ação representada pelo elemento.

Como as alterações de foco, animações sutis e efeitos (como o efeito da Parallax em imagens) são usados para identificar claramente o item de Interface do usuário que tem o foco no momento.

Apple tem as seguintes sugestões para trabalhar com foco e seleção:

- **Use controles de interface do usuário interna para efeitos de movimento** - usando `UIKit` e a API de foco na Interface do usuário, o modelo de foco será aplicada automaticamente o movimento padrão e efeitos visuais para os elementos de interface do usuário. Isso faz com que seu aplicativo se sentir nativo e familiar para os usuários da plataforma Apple TV e permite movimentação intuitiva e flexível entre os itens de controle.
- **Move o foco em direções esperado** -no Apple TV, quase todo elemento usa manipulação indireta. Por exemplo, o usuário usa remoto Siri para mover o foco e o sistema automaticamente rola a interface para manter o item focalizado atualmente visíveis. Se seu aplicativo implementa esse tipo de interação, certifique-se de que o foco move na direção de gesto do usuário. Portanto, se o usuário passa o dedo em foco Siri remoto deve mover para a direita (o que pode fazer com que a tela para rolar para a esquerda). A única exceção a essa regra é os itens de tela cheia que usam a manipulação direta (onde passar o dedo para cima move o elemento para cima).
- **Certifique-se de que o Item de foco é Obvious** -como seus usuários estão interagindo com os elementos de interface do usuário à distância, é fundamental que o item focalizado atualmente se destaca. Normalmente isso será tratado automaticamente por interno `UIKit` elementos. Para controles personalizados, use recursos como o tamanho do item ou sombra para mostrar o foco.
- **Use da Parallax para tornar focalizado itens responsivo** - pequeno, circulares gestos em remoto Siri resultam em movimento suave, em tempo real do item focalizado atualmente. Isso [efeito da Parallax](#Focus-and-Parallax) está incorporada `UIKit` _imagens em camadas_ para dar ao usuário uma noção da conexão para o item focalizado.
- **Criar itens de controle de tamanho apropriado** -itens grandes com um amplo espaçamento são mais fáceis de selecionar e navegar de itens menores.
- **Criar o elemento de interface do usuário para pesquisar bom o foco ou Unfocused** -normalmente Apple TV representa o Item focalizado aumentando seu tamanho. Certifique-se de que elementos de interface do usuário do aplicativos uma aparência excelentes qualquer tamanho de apresentação e, se necessário, fornecem ativos para elementos de tamanhos maior.
- **Representar foco alterações fluidez** -usar animação para desaparecer suave entre itens **focalizada** e **Unfocused** estado para manter as transições sejam chocantes.
- **Não exibir um Cursor** -os usuários esperam navegar da interface do usuário do seu aplicativo usando o foco e não movendo um cursor pela tela. Sua Interface de usuário sempre deverá usar o modelo de foco para apresentar uma experiência de usuário consistente.

<a name="Working-with-Focus" />

### <a name="working-with-focus"></a>Trabalhando com foco

Pode haver momentos em que você deseja criar um controle personalizado que pode se tornar um Item de controle. Se então substituir o `CanBecomeFocused` propriedade e retornar `true`, caso contrário retorne `false`. Por exemplo:

```csharp
public class myView : UIView
{
    public override bool CanBecomeFocused {
        get {return true;}
    }
}
```

A qualquer momento, você pode usar o `Focused` propriedade de um `UIKit` controle para ver se o item atual. Se `true` o item de interface do usuário tem o foco no momento, ou não. Por exemplo:

```csharp
// Is my view in focus?
if (myView.Focused) {
    // Do something
    ...
}
```

Enquanto você diretamente não é possível mover o foco para outro elemento de interface do usuário por meio de código, você pode especificar qual elemento de interface do usuário primeiro recebe foco quando uma tela é carregada, definindo seu `PreferredFocusedView` propriedade `true`. Por exemplo:

```csharp
// Make the play button the starting focus item
playButton.PreferredFocusedView = true;
```

<a name="Working-with-Focus-Updates" />

### <a name="working-with-focus-updates"></a>Trabalhando com atualizações de foco 

Quando o usuário faz com que o foco mudar de um elemento de interface do usuário para outro (por exemplo, em resposta a um gesto no controle remoto Siri) um _evento de atualização de foco_ serão enviadas para o item perdendo o foco e o item ganhando o foco.

Para elementos personalizados que herdam de `UIView` ou `UIViewController`, você pode substituir os vários métodos para trabalhar com o evento de atualização de foco:

- **DidUpdateFocus** -este método será chamado sempre que o modo de exibição recebe ou perde o foco.
- **ShouldUpdateFocus** -Use esse método para definir onde o foco é permitido mover.

Para solicitar que o mecanismo de foco move o foco volta para o `PreferredFocusedView` elemento de interface do usuário, chamada de `SetNeedsUpdateFocus` método do controlador de exibição.

> [!IMPORTANT]
> **Observação:** chamando `SetNeedsUpdateFocus` somente terá efeito se o controlador de exibição que ele está sendo chamado em contém a exibição que tem o foco no momento.




<a name="Working-with-Focus-Guides" />

### <a name="working-with-focus-guides"></a>Trabalhando com guias de foco

O mecanismo de foco incorporados tvOS é excelente para tratamento de movimentações entre os itens que se encaixam em uma grade horizontal e vertical. Normalmente, você precisa fazer nada mais do que adicionar que os elementos de interface do usuário para seu design de interface e o mecanismo de foco manipula automaticamente a movimentação entre esses elementos sem intervenção do desenvolvedor.

No entanto, pode haver vezes, devido das necessidades do seu design de interface do usuário, quando os elementos de interface do usuário não se encaixam em uma grade horizontal e vertical e podem estar inacessíveis por estarem diagonais uns aos outros. Isso acontece porque o mecanismo de foco foi projetado para lidar com simples para cima, para baixo, para a esquerda e direita movimentação entre apenas os itens de interface do usuário.

Execute o seguinte layout de interface do usuário para obter um exemplo:

 [![](navigation-focus-images/guide01.png "Trabalhando com exemplo de guias de foco")](navigation-focus-images/guide01.png#lightbox)
 
Porque o **informações** botão não estiver em uma grade horizontal e vertical com o **comprar** botão ele seria inacessível para o usuário. No entanto, isso pode ser facilmente corrigido usando um _guia de foco_ para fornecer dicas de movimentação para o mecanismo de foco. 

Um guia de foco (`UIFocusGuide`) expõe uma área não visíveis do modo de exibição como Focusable ao mecanismo de foco, permitindo que o foco para ser redirecionado para outro modo de exibição.

Assim, para resolver o exemplo acima, o código a seguir pode ser adicionado ao controlador de exibição para criar um guia de foco entre o **informações** e **comprar** botões:

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

Primeiro, um novo `UIFocusGuide` é criado e adicionado à coleção de guia de Layout do modo de exibição usando o `AddLayoutGuide` método.

Em seguida, o guia de foco superior, esquerda, largura e Altura âncoras são ajustadas relativo a **mais informações sobre** e **comprar** botões para posicioná-la entre eles. Consulte:

[![](navigation-focus-images/guide02.png "Guia de foco de exemplo")](navigation-focus-images/guide02.png#lightbox)

Também é importante observar que as novas restrições estão sendo ativadas conforme eles são criados definindo suas `Active` propriedade `true`:

```csharp
FocusGuide.LeftAnchor.ConstraintEqualTo (...).Active = true;
```

Com a nova guia de foco estabelecida e adicionado à exibição, o controlador de exibição `DidUpdateFocus` método pode ser substituído e adicionado o código a seguir para mover entre o **informações** e **comprar** botões:

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

Primeiro, este get de código a `NextFocusedView` do `UIFocusUpdateContext` que foi passado em (`context`). Se este modo de exibição `null`, não é necessário nenhum processamento e o método foi encerrado.

Em seguida, o `nextFocusableItem` é avaliada. Se ele corresponde a qualquer um de **mais informações sobre** ou **comprar** botões, o foco é enviado ao botão oposto usando o guia de foco `PreferredFocusedView` propriedade. Por exemplo:

```csharp
// Move from the More Info to Buy button
FocusGuide.PreferredFocusedView = BuyButton;
```

Se nenhum botão é a origem do turno foco, o `PreferredFocusedView` propriedade é limpo:

```csharp
// No valid move
FocusGuide.PreferredFocusedView = null;
```

<a name="Working-with-Focus-in-Collections" />

### <a name="working-with-focus-in-collections"></a>Trabalhando com foco em coleções

Ao decidir se ou não um item individual pode ser Focusable é em um `UICollectionView` ou um `UITableView`, você vai substituir os métodos do `UICollectionViewDelegate` ou `UITableViewDelegate` respectivamente. Por exemplo:

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

O `CanFocusItem` método `true` se o item atual pode ser em foco, caso contrário ele retornará `false`.

Se você quiser uma `UICollectionView` ou um `UITableView` para lembrar e restaurar foco para o último item quando ela perde e recupera o foco, defina o `RemembersLastFocusedIndexPath` propriedade `true`.

<a name="Focus-and-Parallax" />

## <a name="focus-and-parallax"></a>Foco e Parallax

Como mencionado acima, um elemento de Interface do usuário é considerado como _em foco_ quando ele é o item atual durante um evento de navegação. Normalmente um item entra em foco, seu tamanho é aumentado um pouco e ele visualmente elevado usando uma sombra. 

Se o usuário fizer um gesto lento, circular no controle remoto Siri, o Item focalizado será sway em tempo real em resposta a essa movimentação. Como o sway ocorre, um sheen iluminado é aplicada à sua imagem de fazer com que a superfície de aparecer ser exibido. Depois de uma determinada quantidade de inatividade, qualquer conteúdo fora de foco escurece e o item focalizada aumentará ainda maior. 

Esses efeitos trabalham juntos para fornecer uma ligeira conexão entre o conteúdo na tela de TV e o usuário interagir com o Apple TV de sofá.

No Apple TV, o efeito da Parallax é usado em todo o sistema para transmitir uma noção da profundidade 3D e dynamics para itens em foco. tvOS usa uma série de transparente, [imagens em camadas](~/ios/tvos/app-fundamentals/icons-images.md#Layered-Images) que ele move e pode ser dimensionado dinamicamente para criar esse efeito.

Imagens em camadas são necessárias para o ícone do aplicativo tvOS e tem suporte para conteúdo dinâmico de validade da parte superior. Embora não seja necessário, a Apple recomenda implementar imagens em camadas em quaisquer outros itens de controle na interface de usuário do seu aplicativo.

### <a name="enabling-parallax"></a>Habilitação da Parallax

O `UIImageView` controle (ou qualquer controle que herda de `UIImageView`) suporta automaticamente o efeito da Parallax. Por padrão, esse suporte está desabilitado, habilitá-lo, use o seguinte código:

```csharp
myImageView.AdjustsImageWhenAncestorFocused = true;
```

Com essa propriedade definida como `true`, o modo de exibição de imagem automaticamente terá o efeito da Parallax quando ele é selecionado pelo usuário e em foco.

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo abordou o conceito de foco e como ele é usado para controlar a navegação na Interface do usuário do aplicativo um Xamarin.tvOS. Ele examinar como os controles de navegação de tvOS internos usar foco, realce e seleção para fornecer uma navegação. Em seguida, ele examinamos como foco pode ser usado com Parallax e imagens em camadas para fornecer dicas visuais para o estado atual de navegação para o usuário final. Finalmente, ele examinado trabalhar com foco, atualizações de foco, foco nas coleções e habilitando Parallax.




## <a name="related-links"></a>Links relacionados

- [Amostras do tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS guias de Interface Humana](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guia de programação de aplicativo para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
