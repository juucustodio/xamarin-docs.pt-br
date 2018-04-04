---
title: iOS 7 visão geral sobre a Interface do usuário
description: iOS 7 apresenta uma grande quantidade de alterações na interface do usuário. Este artigo destaca algumas das alterações maiores, a aparência visual de controles e APIs que dão suporte ao novo design.
ms.prod: xamarin
ms.assetid: FADCEA7C-8968-42A1-9E9E-F4BBAB7BCF2C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 3f5ea8abd41e718f9ac947c5acb290dffe400ddd
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="ios-7-user-interface-overview"></a>iOS 7 visão geral sobre a Interface do usuário

_iOS 7 apresenta uma grande quantidade de alterações na interface do usuário. Este artigo destaca algumas das alterações maiores, a aparência visual de controles e APIs que dão suporte ao novo design._

iOS 7 se concentra em conteúdo sobre chrome. Elementos de interface do usuário no iOS 7 ênfase chrome, removendo atributos como bordas estranhas, barras de status e barras de navegação, que reduzem a quantidade de espaço usado por modos de exibição de conteúdo. No iOS 7, o conteúdo foi projetado para usar a tela inteira.

iOS 7 apresenta várias outras alterações: cor é usada para distinguir elementos de interface do usuário, em vez de atributos como bordas de botão. Muitos elementos, como barras de navegação e barras de status, são agora borrado e transparente ou transparente, com conteúdo levando a área abaixo deles. Esses modos de exibição de conteúdo renderizam através das barras borradas, transmitir a sensação de profundidade na interface do usuário.

Este artigo aborda várias alterações a elementos de interface do usuário no iOS que 7, bem como várias APIs relacionados para o novo design de interface do usuário.

## <a name="view-and-control-changes"></a>Modo de exibição e controle de alterações

Todas as exibições em UIKit em conformidade com a nova aparência do iOS 7. Esta seção destaca algumas das alterações para essas exibições, bem como as APIs relacionadas que foram alterados para oferecer suporte a nova interface do usuário.

### <a name="uibutton"></a>UIButton

Botões criados a partir de `UIButton` classe agora está sem borda, com nenhum plano de fundo por padrão, conforme mostrado abaixo:

 ![](ios7-ui-images/button.png "Exemplo UIButton")

O `UIButtonType.RoundedRect` estilo foi preterido. Se usado no iOS 7, `UIButtonType.RoundedRect` resultará em `UIButtonType.System` que está sendo usado, o que produz o estilo do botão padrão com nenhum plano de fundo ou bordas visíveis, como mostrado acima.

### <a name="uibarbuttonitem"></a>UIBarButtonItem

Semelhante ao `UIButton`, botões da barra também são sem borda, padronizando para o novo `UIBarButtonItemStyle.Plain` estilo mostrado abaixo:

 ![](ios7-ui-images/barbuttonplain.png "Sample UIBarButtonItem")

Além disso, o `UIBarButtonItemStyle.Bordered` estilo foi preterido. Configuração `UIBarButtonItemStyle.Bordered` iOS 7 resultará no `UIBarButtonItemStyle.Plain` estilo que está sendo usado.

O `UIBarButtonItemStyle.Done` estilo não foi preterido. No entanto, ele também criará um botão sem borda, apenas com um estilo de texto em negrito, conforme mostrado:

 ![](ios7-ui-images/barbuttondone.png "Exemplo UIBarButtonItem no estilo concluído")

### <a name="uialertview"></a>UIAlertView

Além da alteração de estilo para a nova iOS 7 aparência, modos de exibição de alertas não dão suporte à personalização por meio do modo de exibição secundário. Embora `UIAlertView` herda de `UIView`, chamar `AddSubview` em um `UIAlertView` não tem nenhum efeito. Por exemplo, considere o seguinte código:

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

Isso gera uma exibição de alerta padrão, com ele é ignorado, conforme mostrado abaixo:

 ![](ios7-ui-images/alert.png "Exemplo UIAlertView")
 
 Observação: UIAlertView foi preterido no iOS 8. Exibição de [controlador alerta](https://developer.xamarin.com/recipes/ios/standard_controls/alertcontroller/) receita usando uma exibição de alerta no iOS 8 e posterior.

### <a name="uisegmentedcontrol"></a>UISegmentedControl

Controles segmentados no iOS 7 são transparentes e dar suporte a cor do matiz. A cor do matiz é usada para a cor do texto e borda. Quando um segmento é selecionado, a cor é trocada entre o plano de fundo e texto, com a cor do matiz usada para realçar o segmento selecionado, conforme mostrado abaixo:

 ![](ios7-ui-images/segmentedcontrol.png "Exemplo UISegmentedControl")

Além disso, o `UISegmentedControlStyle` foi preterido no iOS 7.

### <a name="picker-views"></a>Modos de exibição do seletor

A API de exibições de seletor é praticamente inalterada; No entanto, as diretrizes de design de 7 de iOS agora estão no estado exibições seletor devem ser apresentadas embutido em vez de como entradas exibições animado de parte inferior da tela ou por meio de um novo controlador de colocados na pilha de um controlador de navegação, como iOS anterior versões. Isso pode ser visto no aplicativo de calendário do sistema:

 ![](ios7-ui-images/inlinepicker.png "Isso pode ser visto no aplicativo de calendário do sistema")

### <a name="uisearchdisplaycontroller"></a>UISearchDisplayController

A barra de pesquisa é exibida no painel de navegação da barra quando o `UISearchDisplayController.DisplaysSearchBarInNavigationBar` estiver definida como true. Quando definido como falso - padrão - barra de navegação é ocultada quando o controlador de pesquisa é exibido.

Captura de tela a seguir mostra a barra de pesquisa dentro de um `UISearchDisplayController`:

 ![](ios7-ui-images/searchbar.png "Exemplo UISearchDisplayController")

### <a name="uitableview"></a>UITableView

As APIs em torno de `UITableView` são principalmente inalterado; no entanto, o estilo foi alterado significativamente de acordo com o novo design de interface do usuário. A hierarquia de exibição interna também é um pouco diferente. Essa alteração não afetará a maioria dos aplicativos, mas é algo a serem consideradas.

#### <a name="grouped-table-style"></a>Estilo de tabela agrupados

O estilo agrupado alterado atualizou com o conteúdo agora estendendo até as bordas da tela, conforme mostrado abaixo:

 ![](ios7-ui-images/table1.png "Estilo de tabela agrupados de exemplo")

#### <a name="separatorinset"></a>SeparatorInset

Separadores de linha podem ser recuados definindo o `UITableVIewCell.SeparatorInset` propriedade. Por exemplo, o código a seguir deve ser usado para as células da borda esquerda do recuo:

```csharp
cell.SeparatorInset = new UIEdgeInsets (0, 50, 0, 0);
```

Isso gera a exibição de tabela com as células recuadas conforme mostrado abaixo:

 ![](ios7-ui-images/separatorinset.png "Exemplo UITableView SeparatorInset")

#### <a name="table-button-styles"></a>Estilos de botão de tabela

Os vários botões usados nos modos de exibição de tabela todas foram alterados. Captura de tela a seguir apresenta uma exibição de tabela no modo de edição:

 ![](ios7-ui-images/table2.png "Esta captura de tela apresenta uma exibição de tabela no modo de edição")

### <a name="additional-control-changes"></a>Alterações adicionais de controle

Outros controles UIKit foram alterados, incluindo controles deslizantes, comutadores e steppers. Essas alterações são puramente visuais. Para obter mais informações, consulte da Apple [guia de transição de interface do usuário do iOS 7](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/TransitionGuide/index.html).

## <a name="general-user-interface-changes"></a>Alterações gerais da Interface de usuário

Além das alterações em UIKit, iOS 7 apresenta uma variedade de alterações visuais na interface do usuário, incluindo:

-  Conteúdo de tela inteira
-  Aparência da barra
-  Cor do matiz

<a name="fullscreen" />

### <a name="full-screen-content"></a>Conteúdo de tela inteira

iOS 7 foi projetado para permitir que aplicativos tirar proveito da tela inteira. Exibir controladores agora aparecem sobrepostos por uma barra de status e a barra de navegação - se houver - em vez de que aparecem abaixo das barras de status e de navegação.

Como preparar seu aplicativo para iOS 7, você pode realinhar sub-visualizações visualmente usando *Interface construtor* ou *Xamarin iOS Designer*. Você também pode usar uma das novas APIs para ajudar a manipular programaticamente o conteúdo de tela inteira. Essas APIs são apresentadas abaixo.

#### <a name="toplayoutguide-and-bottomlayoutguide"></a>TopLayoutGuide e BottomLayoutGuide

 `TopLayoutGuide` e `BottomLayoutGuide` servir como uma referência para onde os modos de exibição devem começar ou terminar, para que o conteúdo não é sobreposto por um transparente `UIKit` barra, como no exemplo a seguir:

 [![](ios7-ui-images/clipped.png "Conteúdo de exemplo não sobreposto por uma barra UIKit transparente")](ios7-ui-images/clipped.png#lightbox)

Essas APIs pode ser usadas para calcular o deslocamento de um modo de exibição da parte superior ou inferior da tela e ajustar adequadamente o posicionamento do conteúdo:

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

Consulte o [ImageViewer](https://developer.xamarin.com/samples/mobile/iOS7-ui-updates) para um exemplo de funcionamento.

O valor de deslocamento é gerado dinamicamente depois que o modo de exibição foi adicionado à hierarquia, tentativa de ler `TopLayoutGuide` e `BottomLayoutGuide` valores em `ViewDidLoad` retornará 0. Calcular o valor após a exibição carregada - por exemplo, no `ViewDidLayoutSubviews`.

> [!IMPORTANT]
> `TopLayoutGuide` e `BottomLayoutGuide` são preteridos no iOS 11 em favor do novo layout de área de segurança. Apple declararam que é compatível com a versão iOS anterior ao iOS 11 usando a área de segurança. Para obter mais informações, consulte o [atualizar seu aplicativo para iOS 11](~/ios/platform/introduction-to-ios11/updating-your-app/visual-design.md#fullscreen) guia.

#### <a name="edgesforextendedlayout"></a>EdgesForExtendedLayout

Essa API Especifica quais bordas de um modo de exibição devem ser estendidas para tela inteira, independentemente de barra translucency. No iOS 7, barras de navegação e barras de ferramentas são exibidos em camadas sobre o modo de exibição do controlador - diferentemente no iOS anterior versões, em que eles não ocupam o mesmo espaço. O aplicativo de fotos do iOS 7 ilustra o padrão `UIViewController.EdgesForExtendedLayout` valor, `UIRectEdge.All`. Essa configuração preenche todos os quatro bordas na exibição de conteúdo, criando o efeito de sobreposição e tela cheia:

 [![](ios7-ui-images/photos.png "Exemplo EdgesForExtendedLayout")](ios7-ui-images/photos.png#lightbox)

Tocar a imagem remove as barras e mostra a imagem em tela inteira:

 [![](ios7-ui-images/photos2.png "EdgesForExtendedLayout com as barras removido")](ios7-ui-images/photos2.png#lightbox)

Como o conteúdo de tela inteira é o padrão, os aplicativos configurados para iOS 6 terá parte da exibição recortada, como a captura de tela abaixo:

 [![](ios7-ui-images/clipped.png "Aplicativos configurados para iOS 6 terá parte da exibição recortada, como esta captura de tela")](ios7-ui-images/clipped.png#lightbox)

Modificando o `UIViewController.EdgesForExtendedLayout` propriedade ajusta esse comportamento. É possível especificar que o modo de exibição não preencher qualquer Bordas, nossa visão evitará a exibição de conteúdo no espaço ocupado pela navegação ou barras de ferramentas (em cada orientação):

```csharp
if (UIDevice.CurrentDevice.CheckSystemVersion (7, 0)) { 
    this.EdgesForExtendedLayout = UIRectEdge.None;
}
```

Em nosso aplicativo, veremos que o modo de exibição é reposicionado novamente, para que a imagem inteira fique visível:

 [![](ios7-ui-images/good.png "Exemplo com a imagem inteira visível")](ios7-ui-images/good.png#lightbox)

Observe que, enquanto os efeitos da `TopLayoutGuide/BottomLayoutGuide` e `EdgesForExtendedLayout` APIs são semelhantes, eles devem preencher objetivos diferentes. Alterando o `EdgesForExtendedLayout` configuração padrão pode corrigir recortadas exibições em aplicativos criados para iOS 6, mas um design de BOM iOS 7 deve respeitar a estética de tela inteira e fornecer uma experiência de exibição, contando completo tela `TopLayoutGuide` e `BottomLayoutGuide`para posicionar o conteúdo que tenha devem ser manipulados em um local à vontade para o usuário corretamente.

Consulte o [ImageViewer](https://developer.xamarin.com/samples/mobile/iOS7-ui-updates) para um exemplo de funcionamento.

### <a name="status-and-navigation-bars"></a>Status e barras de navegação

A barra de status e barras de navegação são renderizadas com transparência. Barras de status são transparentes, enquanto as barras de ferramentas e barras de navegação são transparente e borrado para transmitir a sensação de profundidade na interface do usuário. Captura de tela a seguir mostra essa obscurecendo e a transparência, onde a cor de plano de fundo azul da exibição de coleção mostra o status e a navegação de barras, dando-lhes uma aparência azul clara por meio de:

 ![](ios7-ui-images/transparent-navbar.png "Status de exemplo e barra obscurecendo navegação")

#### <a name="status-bar-styles"></a>Estilos de barra de status

Juntamente com obscurecendo e transparência, primeiro plano de uma barra de status pode ser claro ou escuro (escuro sendo o padrão). O estilo de barra de status pode ser definido do controlador de exibição. Um controlador de exibição também pode definir se a barra de status é ocultada ou exibida.

Por exemplo, o código a seguir substitui o `PreferredStatusBarStyle` método de um controlador de exibição para fazer com que a barra de status exibir um clara do primeiro plano:

```csharp
public override UIStatusBarStyle PreferredStatusBarStyle ()
{
    return UIStatusBarStyle.LightContent;
}
```

Isso faz com que a barra de status apareçam como abaixo:

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

### <a name="tint-color"></a>Cor do matiz

Agora, os botões são exibidos como texto sem cromo. A cor do texto pode ser controlada usando o novo `TintColor` propriedade `UIView`. Definindo o `TintColor` aplica-se a cor para a hierarquia de toda a exibição para o modo de exibição que define a ele. Para aplicar um `TintColor`ao longo de um aplicativo, defina-o no `Window`. Você também pode detectar quando a cor do matiz alterado por meio de `UIView.TintColorDidChange` método.

Por exemplo, a captura de tela a seguir mostra o efeito de alterar a cor do matiz na exibição de um controlador navegação para roxo:

 ![](ios7-ui-images/tint-color.png "Cor da tonalidade roxo em uma exibição de controladores de navegação")

A cor do matiz pode ser aplicada a imagens bem quando o `RenderingMode` é definido como `UIImageRenderingMode.AlwaysTemplate`.

> [!IMPORTANT]
> Cor do matiz não pode ser definida usando `UIAppearance`.


### <a name="dynamic-type"></a>Tipo dinâmico

No iOS 7, o usuário pode especificar o tamanho do texto nas configurações do sistema. Com o tipo dinâmico, a fonte é ajustada dinamicamente aparência independentemente do tamanho. `UIFont.PreferredFontForTextStyle` deve ser usado para obter uma fonte que é otimizada para o tamanho controlado pelo usuário.

## <a name="summary"></a>Resumo

Este artigo aborda as alterações a elementos de interface do usuário no iOS 7. Ele examina várias das alterações feitas aos controles no UIKit, realce alterações visual e modos de exibição, bem como alterações para as APIs relacionadas. Finalmente, ele apresenta novas APIs para trabalhar com o conteúdo de tela inteira, o novo suporte de cor do matiz e o tipo dinâmico.

## <a name="related-links"></a>Links relacionados

- [ImageViewer (exemplo)](https://developer.xamarin.com/samples/monotouch/iOS7-ui-updates)
