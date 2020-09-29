---
title: Visão geral da interface do usuário do iOS 7
description: o iOS 7 apresenta uma grande quantidade de alterações na interface do usuário. Este artigo destaca algumas das alterações maiores, na aparência visual dos controles e nas APIs que dão suporte ao novo design.
ms.prod: xamarin
ms.assetid: FADCEA7C-8968-42A1-9E9E-F4BBAB7BCF2C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 724d6ab8d39a5c0054302685c5f9f4ba0f3d9908
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91431732"
---
# <a name="ios-7-user-interface-overview"></a>Visão geral da interface do usuário do iOS 7

_o iOS 7 apresenta uma grande quantidade de alterações na interface do usuário. Este artigo destaca algumas das alterações maiores, na aparência visual dos controles e nas APIs que dão suporte ao novo design._

o iOS 7 se concentra no conteúdo em relação ao Chrome. Elementos da interface do usuário no Chrome de destaque do iOS 7 removendo atributos como bordas estranhas, barras de status e barras de navegação, o que reduz a quantidade de espaço da tela usada pelas exibições de conteúdo. No iOS 7, o conteúdo é projetado para usar a tela inteira.

o iOS 7 apresenta várias outras alterações: a cor é usada para distinguir elementos da interface do usuário, no lugar de atributos, como bordas de botão. Muitos elementos, como barras de navegação e barras de status, agora são desfocados e translúcidas ou transparentes, com exibições de conteúdo que ocupam área abaixo deles. Essas exibições de conteúdo são renderizadas pelas barras desfocadas, transmitindo uma sensação de profundidade na interface do usuário.

Este artigo aborda várias das alterações nos elementos da interface do usuário no iOS 7, bem como várias APIs relacionadas ao novo design de interface do usuário.

## <a name="view-and-control-changes"></a>Exibir e controlar alterações

Todas as exibições no UIKit estão em conformidade com a nova aparência do iOS 7. Esta seção destaca algumas das alterações feitas nessas exibições, bem como as APIs relacionadas que foram alteradas para dar suporte à nova interface do usuário.

### <a name="uibutton"></a>UIButton

Os botões criados da `UIButton` classe agora são sem borda, sem nenhum plano de fundo por padrão, conforme mostrado abaixo:

 ![UIButton de exemplo](ios7-ui-images/button.png)

O `UIButtonType.RoundedRect` estilo foi preterido. Se usado no iOS 7, `UIButtonType.RoundedRect` resultará em `UIButtonType.System` uso, que produz o estilo de botão padrão sem bordas de plano de fundo ou visíveis, como mostrado acima.

### <a name="uibarbuttonitem"></a>UIBarButtonItem

Semelhante a `UIButton` , os botões de barra também são sem bordas, o que usa como padrão o novo `UIBarButtonItemStyle.Plain` estilo mostrado abaixo:

 ![UIBarButtonItem de exemplo](ios7-ui-images/barbuttonplain.png)

Além disso, o `UIBarButtonItemStyle.Bordered` estilo foi preterido. `UIBarButtonItemStyle.Bordered`A configuração no Ios 7 fará com `UIBarButtonItemStyle.Plain` que o estilo seja usado.

O `UIBarButtonItemStyle.Done` estilo não foi preterido. No entanto, ele também criará um botão sem borda, apenas com um estilo de texto em negrito, como mostrado:

 ![Exemplo de UIBarButtonItem no estilo concluído](ios7-ui-images/barbuttondone.png)

### <a name="uialertview"></a>UIAlertView

Além da alteração de estilo para a nova aparência do iOS 7, as exibições de alertas não oferecem mais suporte à personalização por meio de subexibição. Embora `UIAlertView` herde de `UIView` , chamar `AddSubview` em um `UIAlertView` não tem nenhum efeito. Por exemplo, considere o seguinte código:

```csharp
UIBarButtonItem button = new UIBarButtonItem ("Bar Button", UIBarButtonItemStyle.Plain, (s,e) =>
{
    UIAlertView alert = new UIAlertView ("Title", "Message", null, "Cancel", "OK");

    alert.AddSubview (new UIView () {
        Frame = new CGRect(50, 50,100, 100),
        BackgroundColor = UIColor.Green
    });

    alert.Show ();
});
```

Isso produz uma exibição de alerta padrão, com a subexibição ignorada, conforme mostrado abaixo:

 ![UIAlertView de exemplo](ios7-ui-images/alert.png)

 Observação: UIAlertView foi preterido no iOS 8. Exiba a receita do [controlador de alerta](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/alertcontroller) em usando uma exibição de alerta no Ios 8 e superior.

### <a name="uisegmentedcontrol"></a>UISegmentedControl

Os controles segmentados no iOS 7 são transparentes e dão suporte à cor de tonalidade. A cor da tonalidade é usada para a cor do texto e da borda. Quando um segmento é selecionado, a cor é trocada entre o plano de fundo e o texto, com a cor de tonalidade usada para realçar o segmento selecionado, como mostrado abaixo:

 ![UISegmentedControl de exemplo](ios7-ui-images/segmentedcontrol.png)

Além disso, o `UISegmentedControlStyle` foi preterido no Ios 7.

### <a name="picker-views"></a>Exibições do seletor

A API para exibições do seletor está amplamente inalterada; no entanto, as diretrizes de design do iOS 7 agora as exibições do seletor de estado devem ser apresentadas em linha, e não como modos de exibição de entrada animados na parte inferior da tela ou por um novo controlador enviado para a pilha de um controlador de navegação, como nas versões anteriores do iOS. Isso pode ser visto no aplicativo de calendário do sistema:

 ![Isso pode ser visto no aplicativo de calendário do sistema](ios7-ui-images/inlinepicker.png)

### <a name="uisearchdisplaycontroller"></a>UISearchDisplayController

A barra de pesquisa agora é mostrada dentro da barra de navegação quando a `UISearchDisplayController.DisplaysSearchBarInNavigationBar` propriedade é definida como true. Quando definido como false-o padrão-a barra de navegação é ocultada quando o controlador de pesquisa é exibido.

A captura de tela a seguir mostra a barra de pesquisa em um `UISearchDisplayController` :

 ![UISearchDisplayController de exemplo](ios7-ui-images/searchbar.png)

### <a name="uitableview"></a>UITableView

As APIs `UITableView` são, principalmente, inalteradas; no entanto, o estilo mudou drasticamente para estar em conformidade com o novo design da interface do usuário. A hierarquia de exibição interna também é um pouco diferente. Essa alteração não afetará a maioria dos aplicativos, mas é algo que você deve estar atento.

#### <a name="grouped-table-style"></a>Estilo de tabela agrupada

O estilo agrupado alterado foi atualizado, com o conteúdo agora estendido para as bordas da tela, conforme mostrado abaixo:

 ![Exemplo de estilo de tabela agrupada](ios7-ui-images/table1.png)

#### <a name="separatorinset"></a>SeparatorInset

Os separadores de linha agora podem ser recuados definindo a `UITableVIewCell.SeparatorInset` propriedade. Por exemplo, o código a seguir seria usado para recuar as células da borda esquerda:

```csharp
cell.SeparatorInset = new UIEdgeInsets (0, 50, 0, 0);
```

Isso produz na exibição de tabela com células recuadas, conforme mostrado abaixo:

 ![UITableView SeparatorInset de amostra](ios7-ui-images/separatorinset.png)

#### <a name="table-button-styles"></a>Estilos de botão de tabela

Os vários botões usados em exibições de tabela foram alterados. A captura de tela a seguir apresenta uma exibição de tabela no modo de edição:

 ![Esta captura de tela apresenta uma exibição de tabela no modo de edição](ios7-ui-images/table2.png)

### <a name="additional-control-changes"></a>Alterações de controle adicionais

Outros controles UIKit também foram alterados, incluindo controles deslizantes, interruptores e percorridos. Essas alterações são puramente visuais. Para obter mais informações, consulte o [Guia de transição da interface do usuário do IOS 7](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/TransitionGuide/index.html)da Apple.

## <a name="general-user-interface-changes"></a>Alterações gerais da interface do usuário

Além das alterações no UIKit, o iOS 7 apresenta uma variedade de alterações visuais na interface do usuário, incluindo:

- Conteúdo de tela inteira
- Aparência da barra
- Cor da tonalidade

<a name="fullscreen"></a>

### <a name="full-screen-content"></a>Conteúdo de tela inteira

o iOS 7 foi projetado para permitir que os aplicativos tirem proveito de toda a tela. Os controladores de exibição agora aparecem sobrepostos por uma barra de status e barra de navegação-se houver um, em vez de aparecer abaixo do status e das barras de navegação.

Ao preparar seu aplicativo para o iOS 7, você pode realinhar as subexibições visualmente usando *interface Builder* ou o *Xamarin Ios designer*. Você também pode usar uma das novas APIs para ajudar a lidar com o conteúdo de tela inteira de forma programática. Essas APIs são introduzidas abaixo.

#### <a name="toplayoutguide-and-bottomlayoutguide"></a>TopLayoutGuide e BottomLayoutGuide

 `TopLayoutGuide` e `BottomLayoutGuide` serve como uma referência para onde as exibições devem começar ou terminar, para que o conteúdo não seja sobreposto por uma `UIKit` barra translúcida, como no exemplo a seguir:

 [![Conteúdo de exemplo não sobreposto por uma barra UIKit translúcida](ios7-ui-images/clipped.png)](ios7-ui-images/clipped.png#lightbox)

Essas APIs podem ser usadas para calcular o deslocamento de uma exibição na parte superior ou inferior da tela e ajustar o posicionamento do conteúdo de acordo:

```csharp
public override void ViewDidLayoutSubviews ()
{
    base.ViewDidLayoutSubviews ();

    if (UIDevice.CurrentDevice.CheckSystemVersion (7, 0)) { 
        nfloat displacement_y = this.TopLayoutGuide.Length;

        //load subviews with displacement
    }

}
```

Podemos usar o valor calculado acima para definir o `ImageView` deslocamento de nossa parte superior da tela, para que toda a imagem fique visível:

 [![Exemplo de substituição de ImageViews na parte superior da tela](ios7-ui-images/good2.png)](ios7-ui-images/good2.png#lightbox)

Consulte o [ImageViewer](/samples/xamarin/ios-samples/ios7-ui-updates/) para obter um exemplo funcional.

O valor de deslocamento é gerado dinamicamente depois que a exibição é adicionada à hierarquia, portanto, a tentativa de ler `TopLayoutGuide` e `BottomLayoutGuide` valores em `ViewDidLoad` retornará 0. Calcule o valor depois que o modo de exibição tiver sido carregado-por exemplo, no `ViewDidLayoutSubviews` .

> [!IMPORTANT]
> `TopLayoutGuide` e `BottomLayoutGuide` são preteridos no Ios 11 em favor do novo layout de área segura. A Apple afirmou que o uso da área segura é compatível com a versão do iOS anterior ao iOS 11. Para obter mais informações, consulte o guia [atualizando seu aplicativo para IOS 11](~/ios/platform/introduction-to-ios11/updating-your-app/visual-design.md#fullscreen) .

#### <a name="edgesforextendedlayout"></a>EdgesForExtendedLayout

Essa API especifica quais bordas de uma exibição devem ser estendidas para a tela inteira, independentemente da barra translucency. No iOS 7, barras de navegação e barras de ferramentas aparecem em camadas acima da exibição do controlador – ao contrário das versões anteriores do iOS, onde não ocupam o mesmo espaço. O aplicativo de fotos do iOS 7 ilustra o `UIViewController.EdgesForExtendedLayout` valor padrão, `UIRectEdge.All` . Essa configuração preenche todas as quatro bordas na exibição com conteúdo, criando o efeito de sobreposição e de tela inteira:

 [![EdgesForExtendedLayout de exemplo](ios7-ui-images/photos.png)](ios7-ui-images/photos.png#lightbox)

Tocar na imagem remove as barras e mostra a imagem em tela inteira:

 [![EdgesForExtendedLayout com as barras removidas](ios7-ui-images/photos2.png)](ios7-ui-images/photos2.png#lightbox)

Como o conteúdo de tela inteira é o padrão, os aplicativos configurados para iOS 6 terão parte da exibição recortada, como na captura de tela abaixo:

 [![Os aplicativos configurados para iOS 6 terão parte da exibição recortada, como nesta captura de tela](ios7-ui-images/clipped.png)](ios7-ui-images/clipped.png#lightbox)

A modificação da `UIViewController.EdgesForExtendedLayout` propriedade é ajustada para esse comportamento. Podemos especificar que a exibição não preenche nenhuma borda, portanto, nossa exibição evitará exibir o conteúdo no espaço ocupado por navegação ou barras de ferramentas (a cada orientação):

```csharp
if (UIDevice.CurrentDevice.CheckSystemVersion (7, 0)) { 
    this.EdgesForExtendedLayout = UIRectEdge.None;
}
```

Em nosso aplicativo, veremos que a exibição é reposicionada novamente, de modo que toda a imagem é visível:

 [![Exemplo com imagem inteira visível](ios7-ui-images/good.png)](ios7-ui-images/good.png#lightbox)

Observe que, embora os efeitos das `TopLayoutGuide/BottomLayoutGuide` `EdgesForExtendedLayout` APIs e sejam semelhantes, eles destinam-se a preencher metas diferentes. A alteração da `EdgesForExtendedLayout` configuração do padrão pode corrigir exibições recortadas em aplicativos criados para IOS 6, mas um bom design do IOS 7 deve respeitar a estética de tela inteira e fornecer uma experiência de exibição de tela inteira, contando com `TopLayoutGuide` e `BottomLayoutGuide` posicionar corretamente o conteúdo que pretende ser manipulado em um local confortável para o usuário.

Consulte o [ImageViewer](/samples/xamarin/ios-samples/ios7-ui-updates/) para obter um exemplo funcional.

### <a name="status-and-navigation-bars"></a>Status e barras de navegação

A barra de status e as barras de navegação são renderizadas com transparência. As barras de status são transparentes, enquanto as barras de ferramentas e as barras de navegação são translúcidas e desfocadas para transmitir a sensação de profundidade na interface do usuário. A captura de tela a seguir mostra esse desfoque e transparência, em que a cor da tela de fundo azul da exibição de coleção mostra o status e as barras de navegação, dando a eles uma aparência azul clara:

 ![Status de exemplo e desfoque de barra de navegação](ios7-ui-images/transparent-navbar.png)

#### <a name="status-bar-styles"></a>Estilos da barra de status

Junto com o desfoque e a transparência, o primeiro plano de uma barra de status pode ser claro ou escuro (escuro sendo o padrão). O estilo da barra de status pode ser definido no controlador de exibição. Um controlador de exibição também pode definir se a barra de status está oculta ou exibida.

Por exemplo, o código a seguir substitui o `PreferredStatusBarStyle` método de um controlador de exibição para fazer com que a barra de status exiba um primeiro plano de luz:

```csharp
public override UIStatusBarStyle PreferredStatusBarStyle ()
{
    return UIStatusBarStyle.LightContent;
}
```

Isso faz com que a barra de status apareça como abaixo:

 ![Barra de status de exemplo](ios7-ui-images/light-status-bar.png)

Para ocultar a barra de status do código do controlador de exibição, substitua `PrefersStatusBarHidden` , conforme mostrado abaixo:

```csharp
public override bool PrefersStatusBarHidden ()
{
    return true;
}
```

Isso oculta a barra de status:

 ![Barra de status oculta](ios7-ui-images/status-bar-hidden.png)

### <a name="tint-color"></a>Cor da tonalidade

Agora, os botões são exibidos como texto sem cromo. A cor do texto pode ser controlada usando a nova `TintColor` propriedade em `UIView` . A configuração `TintColor` aplica a cor à hierarquia de exibição inteira para a exibição que a define. Para aplicar um `TintColor` aplicativo inteiro, defina-o no `Window` . Você também pode detectar quando a cor da tonalidade é alterada por meio do `UIView.TintColorDidChange` método.

Por exemplo, a captura de tela a seguir mostra o efeito de alterar a cor de tonalidade em uma exibição do controlador de navegação para roxo:

 ![Cor de tonalidade roxa em uma exibição de controladores de navegação](ios7-ui-images/tint-color.png)

A cor da tonalidade também pode ser aplicada a imagens quando o `RenderingMode` é definido como `UIImageRenderingMode.AlwaysTemplate` .

> [!IMPORTANT]
> A cor da tonalidade não pode ser definida usando `UIAppearance` .

### <a name="dynamic-type"></a>Tipo dinâmico

No iOS 7, o usuário pode especificar o tamanho do texto nas configurações do sistema. Com o tipo dinâmico, a fonte é ajustada dinamicamente para parecer bom, independentemente do tamanho. `UIFont.PreferredFontForTextStyle` deve ser usado para obter uma fonte otimizada para o tamanho controlado pelo usuário.

## <a name="summary"></a>Resumo

Este artigo aborda as alterações nos elementos da interface do usuário no iOS 7. Ele examina várias das alterações feitas nas exibições e nos controles no UIKit, destacando as alterações visuais, bem como as alterações nas APIs relacionadas. Por fim, ele introduz novas APIs para trabalhar com conteúdo de tela inteira, novo suporte a cores de tonalidade e tipo dinâmico.

## <a name="related-links"></a>Links Relacionados

- [ImageViewer (exemplo)](/samples/xamarin/ios-samples/ios7-ui-updates)