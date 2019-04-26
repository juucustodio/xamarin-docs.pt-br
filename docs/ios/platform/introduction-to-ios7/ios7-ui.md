---
title: Visão geral da interface do usuário do iOS 7
description: iOS 7 introduz uma grande quantidade de alterações na interface do usuário. Este artigo destaca algumas das maiores alterações, na aparência visual de controles e APIs que suportam o novo design.
ms.prod: xamarin
ms.assetid: FADCEA7C-8968-42A1-9E9E-F4BBAB7BCF2C
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 132265c27e1d1ba3b8f3fc8db10d7b3cfa746197
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61038910"
---
# <a name="ios-7-user-interface-overview"></a>Visão geral da interface do usuário do iOS 7

_iOS 7 introduz uma grande quantidade de alterações na interface do usuário. Este artigo destaca algumas das maiores alterações, na aparência visual de controles e APIs que suportam o novo design._

iOS 7 se concentra em conteúdo sobre o cromo. Elementos de interface do usuário no iOS 7 ênfase chrome, removendo atributos como bordas estranhas, barras de status e as barras de navegação, que reduz a quantidade de espaço usado por modos de exibição de conteúdo da tela. No iOS 7, o conteúdo foi projetado para usar a tela inteira.

iOS 7 apresenta várias outras alterações: cor é usada para distinguir elementos de interface do usuário, em vez de atributos, como as bordas do botão. Vários elementos, como barras de navegação e barras de status, agora estão desfocadas e transparente ou transparentes, com visualizações de conteúdo levando a área abaixo deles. Esses modos de exibição de conteúdo é renderizado através das barras desfocadas, transmitir uma sensação de profundidade na interface do usuário.

Este artigo aborda várias das alterações a elementos de interface do usuário no iOS que 7, bem como várias APIs relacionadas para o novo design de interface do usuário.

## <a name="view-and-control-changes"></a>Modo de exibição e controle de alterações

Todas as exibições no UIKit em conformidade com a nova aparência do iOS 7. Esta seção destaca algumas das alterações para esses modos de exibição, bem como as APIs relacionadas que foram alterados para oferecer suporte a nova interface do usuário.

### <a name="uibutton"></a>UIButton

Botões criados a partir de `UIButton` classe agora estão sem borda, com nenhum plano de fundo, por padrão, conforme mostrado abaixo:

 ![](ios7-ui-images/button.png "Exemplo UIButton")

O `UIButtonType.RoundedRect` estilo foi preterido. Se usado no iOS 7, `UIButtonType.RoundedRect` resultará em `UIButtonType.System` que está sendo usado, o que produz o estilo do botão padrão sem plano de fundo ou bordas visíveis, conforme mostrado acima.

### <a name="uibarbuttonitem"></a>UIBarButtonItem

Semelhante ao `UIButton`, botões da barra também estão sem borda, padronizando para o novo `UIBarButtonItemStyle.Plain` estilo mostrado abaixo:

 ![](ios7-ui-images/barbuttonplain.png "Exemplo UIBarButtonItem")

Além disso, o `UIBarButtonItemStyle.Bordered` estilo foi preterido. Definindo `UIBarButtonItemStyle.Bordered` no iOS 7 resultará na `UIBarButtonItemStyle.Plain` de estilo que está sendo usado.

O `UIBarButtonItemStyle.Done` estilo não foi preterido. No entanto, ele também criará um botão sem borda, apenas com um estilo de texto em negrito, conforme mostrado:

 ![](ios7-ui-images/barbuttondone.png "Exemplo UIBarButtonItem no estilo Done")

### <a name="uialertview"></a>UIAlertView

A alteração de estilo para a iOS 7 aparência nova e, além de modos de exibição de alerta não suportam personalização por meio do modo de exibição secundário. Embora `UIAlertView` herda `UIView`, chamar `AddSubview` em um `UIAlertView` não tem nenhum efeito. Por exemplo, considere o seguinte código:

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

Isso produz uma exibição de alerta padrão, com o modo de exibição secundário que está sendo ignorado, conforme mostrado abaixo:

 ![](ios7-ui-images/alert.png "Exemplo UIAlertView")
 
 Observação: UIAlertView foi preterido no iOS 8. Modo de exibição de [controlador de alerta](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/alertcontroller) receita sobre como usar uma exibição de alerta no iOS 8 e versões posteriores.

### <a name="uisegmentedcontrol"></a>UISegmentedControl

Controles segmentados no iOS 7 são transparentes e dar suporte a cor da tonalidade. A cor da tonalidade é usada para a cor da borda e texto. Quando um segmento é selecionado, a cor é trocada entre o plano de fundo e texto, com a cor de tonalidade usada para realçar o segmento selecionado, conforme mostrado abaixo:

 ![](ios7-ui-images/segmentedcontrol.png "Exemplo UISegmentedControl")

Além disso, o `UISegmentedControlStyle` foi preterido no iOS 7.

### <a name="picker-views"></a>Modos de exibição do seletor

A API de modos de exibição do seletor é basicamente inalterada; No entanto, as diretrizes de design de 7 de iOS agora estão no estado exibições seletor devem ser apresentadas embutido em vez de como exibições de entrada é animado de parte inferior da tela ou por meio de um novo controlador de enviada por push na pilha do controlador de navegação, como no iOS anterior versões. Isso pode ser visto no aplicativo de calendário do sistema:

 ![](ios7-ui-images/inlinepicker.png "Isso pode ser visto no aplicativo de calendário do sistema")

### <a name="uisearchdisplaycontroller"></a>UISearchDisplayController

A barra de pesquisa agora é mostrada entre a navegação da barra quando o `UISearchDisplayController.DisplaysSearchBarInNavigationBar` estiver definida como true. Quando definido como falso - padrão - barra de navegação é ocultada quando o controlador de pesquisa é exibido.

Captura de tela a seguir mostra a barra de pesquisa dentro de um `UISearchDisplayController`:

 ![](ios7-ui-images/searchbar.png "Exemplo UISearchDisplayController")

### <a name="uitableview"></a>UITableView

As APIs em torno de `UITableView` são basicamente inalterado; no entanto, o estilo foi alterado significativamente de acordo com o novo design de interface do usuário. A hierarquia de exibição interna também é um pouco diferente. Essa alteração não afetará a maioria dos aplicativos, mas é algo a serem consideradas.

#### <a name="grouped-table-style"></a>Estilo de tabela agrupada

O estilo agrupado alterado foi atualizado, com o conteúdo agora estendendo às bordas da tela, conforme mostrado abaixo:

 ![](ios7-ui-images/table1.png "Estilo de tabela agrupada de exemplo")

#### <a name="separatorinset"></a>SeparatorInset

Separadores de linha agora podem ser recuados, definindo o `UITableVIewCell.SeparatorInset` propriedade. Por exemplo, o código a seguir poderia ser usado para as células da borda esquerda do recuo:

```csharp
cell.SeparatorInset = new UIEdgeInsets (0, 50, 0, 0);
```

Isso produz o modo de exibição de tabela com células recuadas conforme mostrado abaixo:

 ![](ios7-ui-images/separatorinset.png "Exemplo UITableView SeparatorInset")

#### <a name="table-button-styles"></a>Estilos de botão de tabela

Os vários botões usados em exibições de tabela foram alterados. Captura de tela a seguir apresenta uma exibição de tabela no modo de edição:

 ![](ios7-ui-images/table2.png "Esta captura de tela apresenta uma exibição de tabela no modo de edição")

### <a name="additional-control-changes"></a>Alterações de controle adicional

Outros controles UIKit foram alteradas, inclusive controles deslizantes, comutadores e steppers. Essas alterações são puramente visuais. Para obter mais informações, consulte da Apple [guia de transição de interface do usuário do iOS 7](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/TransitionGuide/index.html).

## <a name="general-user-interface-changes"></a>Alterações gerais da Interface do usuário

Além das alterações no UIKit, iOS 7 apresenta uma variedade de alterações visuais na interface do usuário, incluindo:

-  Conteúdo de tela inteira
-  Aparência da barra
-  Cor da tonalidade

<a name="fullscreen" />

### <a name="full-screen-content"></a>Conteúdo de tela inteira

iOS 7 foi projetado para permitir que os aplicativos aproveitar toda a tela. Controladores de exibição agora aparecem sobrepostas por uma barra de status e a barra de navegação - se houver - em vez de que aparecem abaixo das barras de status e a navegação.

Como preparar seu aplicativo para iOS 7, você pode refletir os subexibições usam visualmente *Interface Builder* ou o *Designer do iOS Xamarin*. Você também pode usar uma das novas APIs para ajudar a manipular programaticamente o conteúdo de tela inteira. Essas APIs são apresentadas abaixo.

#### <a name="toplayoutguide-and-bottomlayoutguide"></a>TopLayoutGuide e BottomLayoutGuide

 `TopLayoutGuide` e `BottomLayoutGuide` servir como uma referência para onde os modos de exibição devem começar ou terminar, para que o conteúdo não é sobreposto por uma translúcido `UIKit` barra, como no exemplo a seguir:

 [![](ios7-ui-images/clipped.png "Conteúdo de exemplo não sobreposto por uma barra de UIKit translúcida")](ios7-ui-images/clipped.png#lightbox)

Essas APIs pode ser usados para calcular o deslocamento do modo de exibição da parte superior ou inferior da tela e ajustar adequadamente o posicionamento do conteúdo:

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

Podemos usar o valor calculado acima para definir nossos `ImageView`do deslocamento da parte superior da tela, para que a imagem inteira fique visível:

 [![](ios7-ui-images/good2.png "Deslocamento de ImageViews de exemplo da parte superior da tela")](ios7-ui-images/good2.png#lightbox)

Consulte a [ImageViewer](https://developer.xamarin.com/samples/mobile/iOS7-ui-updates) para um exemplo de funcionamento.

O valor de deslocamento é gerado dinamicamente após a exibição foi adicionada à hierarquia, portanto, ao tentar ler `TopLayoutGuide` e `BottomLayoutGuide` os valores em `ViewDidLoad` retornará 0. Calcular o valor depois que o modo de exibição tiver carregado - por exemplo, no `ViewDidLayoutSubviews`.

> [!IMPORTANT]
> `TopLayoutGuide` e `BottomLayoutGuide` são preteridos no iOS 11 em favor do novo layout de área de segurança. A Apple declarou que o uso de área de segurança é compatível com o iOS versão anterior ao iOS 11. Para obter mais informações, consulte o [atualizando seu aplicativo para iOS 11](~/ios/platform/introduction-to-ios11/updating-your-app/visual-design.md#fullscreen) guia.

#### <a name="edgesforextendedlayout"></a>EdgesForExtendedLayout

Essa API Especifica quais bordas de um modo de exibição devem ser estendidas para tela inteira, independentemente da barra translucência. No iOS 7, barras de navegação e barras de ferramentas são exibidas sobre o modo de exibição do controlador - diferentemente no iOS anterior versões, em que eles não ocupam o mesmo espaço. O aplicativo de fotos do iOS 7 ilustra o padrão `UIViewController.EdgesForExtendedLayout` valor, `UIRectEdge.All`. Essa configuração preenche todas as quatro bordas no modo de exibição com o conteúdo, criando o efeito de sobreposição e tela cheia:

 [![](ios7-ui-images/photos.png "Exemplo EdgesForExtendedLayout")](ios7-ui-images/photos.png#lightbox)

Tocar na imagem remove as barras e mostra a imagem em tela inteira:

 [![](ios7-ui-images/photos2.png "EdgesForExtendedLayout com as barras removido")](ios7-ui-images/photos2.png#lightbox)

Como o conteúdo de tela inteira é o padrão, os aplicativos configurados para o iOS 6 terá parte da exibição recortada, como na captura de tela abaixo:

 [![](ios7-ui-images/clipped.png "Aplicativos configurados para o iOS 6 terá parte da exibição recortada, como na captura de tela")](ios7-ui-images/clipped.png#lightbox)

Modificando o `UIViewController.EdgesForExtendedLayout` ajusta de propriedade para esse comportamento. Podemos especificar que o modo de exibição não preencham qualquer Bordas, portanto, nossa exibição evitará a exibir o conteúdo em que o espaço ocupado pela navegação ou barras de ferramentas (em cada orientação):

```csharp
if (UIDevice.CurrentDevice.CheckSystemVersion (7, 0)) { 
    this.EdgesForExtendedLayout = UIRectEdge.None;
}
```

Em nosso aplicativo, veremos o modo de exibição é reposicionado novamente, para que a imagem inteira fique visível:

 [![](ios7-ui-images/good.png "Exemplo com a imagem inteira visível")](ios7-ui-images/good.png#lightbox)

Observe que, enquanto os efeitos do `TopLayoutGuide/BottomLayoutGuide` e `EdgesForExtendedLayout` APIs são semelhantes, eles devem preencher metas diferentes. Alterando a `EdgesForExtendedLayout` configuração padrão pode corrigir recortados modos de exibição em aplicativos criados para o iOS 6, mas um design de BOM iOS 7 deve honrar a estética de tela inteira e fornecer uma experiência de exibição, confiar em completo tela `TopLayoutGuide` e `BottomLayoutGuide`para posicionar corretamente o conteúdo que foi projetado para ser manipulados em um lugar à vontade para o usuário.

Consulte a [ImageViewer](https://developer.xamarin.com/samples/mobile/iOS7-ui-updates) para um exemplo de funcionamento.

### <a name="status-and-navigation-bars"></a>Status e barras de navegação

A barra de status e as barras de navegação são renderizadas com transparência. Barras de status são transparentes, enquanto as barras de ferramentas e barras de navegação são translúcido e desfocada para transmitir a sensação de profundidade na interface do usuário. Captura de tela a seguir mostra esse desfoque e transparência, onde a cor do plano de fundo azul da exibição de coleção mostra por meio de barras o status e a navegação, dando-lhes uma aparência azul clara:

 ![](ios7-ui-images/transparent-navbar.png "Exemplo de Status e barra Desfoque de navegação")

#### <a name="status-bar-styles"></a>Estilos de barra de status

Além de desfoque e transparência, o primeiro plano de uma barra de status pode ser claro ou escuro (escuro sendo o padrão). Pode ser definido no estilo de barra de status do controlador de exibição. Um controlador de exibição também pode definir se a barra de status é ocultada ou exibida.

Por exemplo, o código a seguir substitui o `PreferredStatusBarStyle` método de um controlador de exibição para fazer com que a barra de status exiba um primeiro plano claro:

```csharp
public override UIStatusBarStyle PreferredStatusBarStyle ()
{
    return UIStatusBarStyle.LightContent;
}
```

Isso faz com que a barra de status aparecem conforme mostrado abaixo:

 ![](ios7-ui-images/light-status-bar.png "Exemplo de barra de Status")

Para ocultar a barra de status do código do controlador de exibição, substituir `PrefersStatusBarHidden`, conforme mostrado abaixo:

```csharp
public override bool PrefersStatusBarHidden ()
{
    return true;
}
```

Isso oculta a barra de status:

 ![](ios7-ui-images/status-bar-hidden.png "Barra de status oculta")

### <a name="tint-color"></a>Cor da tonalidade

Agora, os botões são exibidos como texto sem cromo. A cor do texto pode ser controlada usando o novo `TintColor` propriedade `UIView`. Definindo o `TintColor` aplica-se a cor para a hierarquia de exibição inteiro para o modo de exibição que a define. Para aplicar uma `TintColor`ao longo de um aplicativo, defina-o sobre o `Window`. Você também pode detectar quando a cor da tonalidade é alterado por meio de `UIView.TintColorDidChange` método.

Por exemplo, a captura de tela a seguir mostra o efeito de alterar a cor da tonalidade na exibição do controlador de navegação para roxo:

 ![](ios7-ui-images/tint-color.png "Cor da tonalidade roxo em uma exibição de controladores de navegação")

A cor da tonalidade pode ser aplicada a imagens também quando o `RenderingMode` é definido como `UIImageRenderingMode.AlwaysTemplate`.

> [!IMPORTANT]
> Cor da tonalidade não pode ser definida usando `UIAppearance`.


### <a name="dynamic-type"></a>Tipo dinâmico

No iOS 7, o usuário pode especificar o tamanho do texto nas configurações do sistema. Com o tipo dinâmico, a fonte é ajustada dinamicamente aparência, independentemente do tamanho. `UIFont.PreferredFontForTextStyle` deve ser usado para obter uma fonte que é otimizada para o tamanho controlado pelo usuário.

## <a name="summary"></a>Resumo

Este artigo aborda as alterações em elementos de interface do usuário no iOS 7. Ele examina várias das alterações feitas nos modos de exibição e controles no UIKit, realce de ambas as alterações de visual, bem como alterações em APIs relacionadas. Por fim, ele introduz novas APIs para trabalhar com conteúdo de tela inteira, o novo suporte de cor de tonalidade e o tipo dinâmico.

## <a name="related-links"></a>Links relacionados

- [ImageViewer (amostra)](https://developer.xamarin.com/samples/monotouch/iOS7-ui-updates)
