---
title: Visão geral da interface do usuário do iOS 7
description: o iOS 7 apresenta uma grande quantidade de alterações na interface do usuário. Este artigo destaca algumas das alterações maiores, na aparência visual dos controles e nas APIs que dão suporte ao novo design.
ms.prod: xamarin
ms.assetid: FADCEA7C-8968-42A1-9E9E-F4BBAB7BCF2C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 4731be58c1fadae0bba6768570ecfd181b071dd2
ms.sourcegitcommit: ce4670de51e24116a944c778ee64585bd0aae0e1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2020
ms.locfileid: "79088936"
---
# <a name="ios-7-user-interface-overview"></a>Visão geral da interface do usuário do iOS 7

_o iOS 7 apresenta uma grande quantidade de alterações na interface do usuário. Este artigo destaca algumas das alterações maiores, na aparência visual dos controles e nas APIs que dão suporte ao novo design._

o iOS 7 se concentra no conteúdo em relação ao Chrome. Elementos da interface do usuário no Chrome de destaque do iOS 7 removendo atributos como bordas estranhas, barras de status e barras de navegação, o que reduz a quantidade de espaço da tela usada pelas exibições de conteúdo. No iOS 7, o conteúdo é projetado para usar a tela inteira.

o iOS 7 apresenta várias outras alterações: a cor é usada para distinguir elementos da interface do usuário, no lugar de atributos, como bordas de botão. Muitos elementos, como barras de navegação e barras de status, agora são desfocados e translúcidas ou transparentes, com exibições de conteúdo que ocupam área abaixo deles. Essas exibições de conteúdo são renderizadas pelas barras desfocadas, transmitindo uma sensação de profundidade na interface do usuário.

Este artigo aborda várias das alterações nos elementos da interface do usuário no iOS 7, bem como várias APIs relacionadas ao novo design de interface do usuário.

## <a name="view-and-control-changes"></a>Exibir e controlar alterações

Todas as exibições no UIKit estão em conformidade com a nova aparência do iOS 7. Esta seção destaca algumas das alterações feitas nessas exibições, bem como as APIs relacionadas que foram alteradas para dar suporte à nova interface do usuário.

### <a name="uibutton"></a>UIButton

Os botões criados na classe `UIButton` agora são sem borda, sem nenhum plano de fundo por padrão, conforme mostrado abaixo:

 ![](ios7-ui-images/button.png "Sample UIButton")

O estilo de `UIButtonType.RoundedRect` foi preterido. Se usado no iOS 7, `UIButtonType.RoundedRect` resultará na `UIButtonType.System` usada, que produz o estilo de botão padrão sem bordas de plano de fundo ou visíveis, como mostrado acima.

### <a name="uibarbuttonitem"></a>UIBarButtonItem

Semelhante à `UIButton`, os botões de barra também são sem borda, o que usa como padrão o novo estilo de `UIBarButtonItemStyle.Plain` mostrado abaixo:

 ![](ios7-ui-images/barbuttonplain.png "Sample UIBarButtonItem")

Além disso, o estilo de `UIBarButtonItemStyle.Bordered` foi preterido. A configuração `UIBarButtonItemStyle.Bordered` no iOS 7 fará com que o estilo de `UIBarButtonItemStyle.Plain` usado.

O estilo de `UIBarButtonItemStyle.Done` não foi preterido. No entanto, ele também criará um botão sem borda, apenas com um estilo de texto em negrito, como mostrado:

 ![](ios7-ui-images/barbuttondone.png "Sample UIBarButtonItem in the Done style")

### <a name="uialertview"></a>UIAlertView

Além da alteração de estilo para a nova aparência do iOS 7, as exibições de alertas não oferecem mais suporte à personalização por meio de subexibição. Embora `UIAlertView` herde de `UIView`, a chamada `AddSubview` em um `UIAlertView` não tem efeito. Por exemplo, considere o seguinte código:

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

 ![](ios7-ui-images/alert.png "Sample UIAlertView")

 Observação: UIAlertView foi preterido no iOS 8. Exiba a receita do [controlador de alerta](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/alertcontroller) em usando uma exibição de alerta no Ios 8 e superior.

### <a name="uisegmentedcontrol"></a>UISegmentedControl

Os controles segmentados no iOS 7 são transparentes e dão suporte à cor de tonalidade. A cor da tonalidade é usada para a cor do texto e da borda. Quando um segmento é selecionado, a cor é trocada entre o plano de fundo e o texto, com a cor de tonalidade usada para realçar o segmento selecionado, como mostrado abaixo:

 ![](ios7-ui-images/segmentedcontrol.png "Sample UISegmentedControl")

Além disso, o `UISegmentedControlStyle` foi preterido no iOS 7.

### <a name="picker-views"></a>Exibições do seletor

A API para exibições do seletor está amplamente inalterada; no entanto, as diretrizes de design do iOS 7 agora as exibições do seletor de estado devem ser apresentadas em linha, e não como modos de exibição de entrada animados na parte inferior da tela ou por um novo controlador enviado para a pilha de um controlador de navegação, como nas versões anteriores do iOS. Isso pode ser visto no aplicativo de calendário do sistema:

 ![](ios7-ui-images/inlinepicker.png "This can be seen in the system calendar app")

### <a name="uisearchdisplaycontroller"></a>UISearchDisplayController

A barra de pesquisa agora é mostrada dentro da barra de navegação quando a propriedade `UISearchDisplayController.DisplaysSearchBarInNavigationBar` é definida como true. Quando definido como false-o padrão-a barra de navegação é ocultada quando o controlador de pesquisa é exibido.

A captura de tela a seguir mostra a barra de pesquisa dentro de um `UISearchDisplayController`:

 ![](ios7-ui-images/searchbar.png "Sample UISearchDisplayController")

### <a name="uitableview"></a>UITableView

As APIs em todo `UITableView` são principalmente inalteradas; no entanto, o estilo mudou drasticamente para estar de acordo com o novo design da interface do usuário. A hierarquia de exibição interna também é um pouco diferente. Essa alteração não afetará a maioria dos aplicativos, mas é algo que você deve estar atento.

#### <a name="grouped-table-style"></a>Estilo de tabela agrupada

O estilo agrupado alterado foi atualizado, com o conteúdo agora estendido para as bordas da tela, conforme mostrado abaixo:

 ![](ios7-ui-images/table1.png "Sample Grouped Table Style")

#### <a name="separatorinset"></a>SeparatorInset

Os separadores de linha agora podem ser recuados definindo a propriedade `UITableVIewCell.SeparatorInset`. Por exemplo, o código a seguir seria usado para recuar as células da borda esquerda:

```csharp
cell.SeparatorInset = new UIEdgeInsets (0, 50, 0, 0);
```

Isso produz na exibição de tabela com células recuadas, conforme mostrado abaixo:

 ![](ios7-ui-images/separatorinset.png "Sample UITableView SeparatorInset")

#### <a name="table-button-styles"></a>Estilos de botão de tabela

Os vários botões usados em exibições de tabela foram alterados. A captura de tela a seguir apresenta uma exibição de tabela no modo de edição:

 ![](ios7-ui-images/table2.png "This screenshot presents a table view in editing mode")

### <a name="additional-control-changes"></a>Alterações de controle adicionais

Outros controles UIKit também foram alterados, incluindo controles deslizantes, interruptores e percorridos. Essas alterações são puramente visuais. Para obter mais informações, consulte o [Guia de transição da interface do usuário do IOS 7](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/TransitionGuide/index.html)da Apple.

## <a name="general-user-interface-changes"></a>Alterações gerais da interface do usuário

Além das alterações no UIKit, o iOS 7 apresenta uma variedade de alterações visuais na interface do usuário, incluindo:

- Conteúdo de tela inteira
- Aparência da barra
- Cor da tonalidade

<a name="fullscreen" />

### <a name="full-screen-content"></a>Conteúdo de tela inteira

o iOS 7 foi projetado para permitir que os aplicativos tirem proveito de toda a tela. Os controladores de exibição agora aparecem sobrepostos por uma barra de status e barra de navegação-se houver um, em vez de aparecer abaixo do status e das barras de navegação.

Ao preparar seu aplicativo para o iOS 7, você pode realinhar as subexibições visualmente usando *interface Builder* ou o *Xamarin Ios designer*. Você também pode usar uma das novas APIs para ajudar a lidar com o conteúdo de tela inteira de forma programática. Essas APIs são introduzidas abaixo.

#### <a name="toplayoutguide-and-bottomlayoutguide"></a>TopLayoutGuide e BottomLayoutGuide

 `TopLayoutGuide` e `BottomLayoutGuide` servem como uma referência para onde as exibições devem começar ou terminar, para que o conteúdo não seja sobreposto por uma barra de `UIKit` translúcida, como no exemplo a seguir:

 [![](ios7-ui-images/clipped.png "Sample content not overlapped by a translucent UIKit bar")](ios7-ui-images/clipped.png#lightbox)

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

Podemos usar o valor calculado acima para definir o deslocamento de nosso `ImageView`na parte superior da tela, para que toda a imagem fique visível:

 [![](ios7-ui-images/good2.png "Example ImageViews displacement from the top of the screen")](ios7-ui-images/good2.png#lightbox)

Consulte o [ImageViewer](https://docs.microsoft.com/samples/xamarin/ios-samples/ios7-ui-updates/) para obter um exemplo funcional.

O valor de deslocamento é gerado dinamicamente após a exibição ser adicionada à hierarquia, portanto, tentar ler `TopLayoutGuide` e `BottomLayoutGuide` valores em `ViewDidLoad` retornará 0. Calcule o valor depois que o modo de exibição tiver sido carregado-por exemplo, na `ViewDidLayoutSubviews`.

> [!IMPORTANT]
> `TopLayoutGuide` e `BottomLayoutGuide` são preteridos no iOS 11 em favor do novo layout de área segura. A Apple afirmou que o uso da área segura é compatível com a versão do iOS anterior ao iOS 11. Para obter mais informações, consulte o guia [atualizando seu aplicativo para IOS 11](~/ios/platform/introduction-to-ios11/updating-your-app/visual-design.md#fullscreen) .

#### <a name="edgesforextendedlayout"></a>EdgesForExtendedLayout

Essa API especifica quais bordas de uma exibição devem ser estendidas para a tela inteira, independentemente da barra translucency. No iOS 7, barras de navegação e barras de ferramentas aparecem em camadas acima da exibição do controlador – ao contrário das versões anteriores do iOS, onde não ocupam o mesmo espaço. O aplicativo de fotos do iOS 7 ilustra o valor de `UIViewController.EdgesForExtendedLayout` padrão `UIRectEdge.All`. Essa configuração preenche todas as quatro bordas na exibição com conteúdo, criando o efeito de sobreposição e de tela inteira:

 [![](ios7-ui-images/photos.png "Sample EdgesForExtendedLayout")](ios7-ui-images/photos.png#lightbox)

Tocar na imagem remove as barras e mostra a imagem em tela inteira:

 [![](ios7-ui-images/photos2.png "EdgesForExtendedLayout with the bars removed")](ios7-ui-images/photos2.png#lightbox)

Como o conteúdo de tela inteira é o padrão, os aplicativos configurados para iOS 6 terão parte da exibição recortada, como na captura de tela abaixo:

 [![](ios7-ui-images/clipped.png "Apps configured for iOS 6 will have part of the view clipped, as in this screenshot")](ios7-ui-images/clipped.png#lightbox)

A modificação da propriedade `UIViewController.EdgesForExtendedLayout` é ajustada para esse comportamento. Podemos especificar que a exibição não preenche nenhuma borda, portanto, nossa exibição evitará exibir o conteúdo no espaço ocupado por navegação ou barras de ferramentas (a cada orientação):

```csharp
if (UIDevice.CurrentDevice.CheckSystemVersion (7, 0)) { 
    this.EdgesForExtendedLayout = UIRectEdge.None;
}
```

Em nosso aplicativo, veremos que a exibição é reposicionada novamente, de modo que toda a imagem é visível:

 [![](ios7-ui-images/good.png "Example with whole image visible")](ios7-ui-images/good.png#lightbox)

Observe que, embora os efeitos das APIs `TopLayoutGuide/BottomLayoutGuide` e `EdgesForExtendedLayout` sejam semelhantes, eles destinam-se a preencher metas diferentes. Alterar a configuração de `EdgesForExtendedLayout` do padrão pode corrigir exibições recortadas em aplicativos criados para iOS 6, mas um bom design do iOS 7 deve respeitar a estética de tela inteira e fornecer uma experiência de exibição de tela inteira, contando com `TopLayoutGuide` e `BottomLayoutGuide` para posicionar corretamente o conteúdo que pretende ser manipulado em um local confortável para o usuário.

Consulte o [ImageViewer](https://docs.microsoft.com/samples/xamarin/ios-samples/ios7-ui-updates/) para obter um exemplo funcional.

### <a name="status-and-navigation-bars"></a>Status e barras de navegação

A barra de status e as barras de navegação são renderizadas com transparência. As barras de status são transparentes, enquanto as barras de ferramentas e as barras de navegação são translúcidas e desfocadas para transmitir a sensação de profundidade na interface do usuário. A captura de tela a seguir mostra esse desfoque e transparência, em que a cor da tela de fundo azul da exibição de coleção mostra o status e as barras de navegação, dando a eles uma aparência azul clara:

 ![](ios7-ui-images/transparent-navbar.png "Sample Status and Navigation Bar blurring")

#### <a name="status-bar-styles"></a>Estilos da barra de status

Junto com o desfoque e a transparência, o primeiro plano de uma barra de status pode ser claro ou escuro (escuro sendo o padrão). O estilo da barra de status pode ser definido no controlador de exibição. Um controlador de exibição também pode definir se a barra de status está oculta ou exibida.

Por exemplo, o código a seguir substitui o método `PreferredStatusBarStyle` de um controlador de exibição para fazer com que a barra de status exiba um primeiro plano de luz:

```csharp
public override UIStatusBarStyle PreferredStatusBarStyle ()
{
    return UIStatusBarStyle.LightContent;
}
```

Isso faz com que a barra de status apareça como abaixo:

 ![](ios7-ui-images/light-status-bar.png "Sample Status Bar")

Para ocultar a barra de status do código do controlador de exibição, substitua `PrefersStatusBarHidden`, conforme mostrado abaixo:

```csharp
public override bool PrefersStatusBarHidden ()
{
    return true;
}
```

Isso oculta a barra de status:

 ![](ios7-ui-images/status-bar-hidden.png "Status Bar hidden")

### <a name="tint-color"></a>Cor da tonalidade

Agora, os botões são exibidos como texto sem cromo. A cor do texto pode ser controlada usando a nova propriedade `TintColor` em `UIView`. Definir a `TintColor` aplica a cor à hierarquia de exibição inteira para a exibição que a define. Para aplicar um `TintColor`em um aplicativo, defina-o no `Window`. Você também pode detectar quando a cor da tonalidade é alterada por meio do método `UIView.TintColorDidChange`.

Por exemplo, a captura de tela a seguir mostra o efeito de alterar a cor de tonalidade em uma exibição do controlador de navegação para roxo:

 ![](ios7-ui-images/tint-color.png "Purple tint color on a navigation controllers view")

A cor da tonalidade também pode ser aplicada a imagens quando a `RenderingMode` é definida como `UIImageRenderingMode.AlwaysTemplate`.

> [!IMPORTANT]
> A cor da tonalidade não pode ser definida usando `UIAppearance`.

### <a name="dynamic-type"></a>Tipo dinâmico

No iOS 7, o usuário pode especificar o tamanho do texto nas configurações do sistema. Com o tipo dinâmico, a fonte é ajustada dinamicamente para parecer bom, independentemente do tamanho. `UIFont.PreferredFontForTextStyle` deve ser usado para obter uma fonte otimizada para o tamanho controlado pelo usuário.

## <a name="summary"></a>Resumo

Este artigo aborda as alterações nos elementos da interface do usuário no iOS 7. Ele examina várias das alterações feitas nas exibições e nos controles no UIKit, destacando as alterações visuais, bem como as alterações nas APIs relacionadas. Por fim, ele introduz novas APIs para trabalhar com conteúdo de tela inteira, novo suporte a cores de tonalidade e tipo dinâmico.

## <a name="related-links"></a>Links Relacionados

- [ImageViewer (exemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios7-ui-updates)
