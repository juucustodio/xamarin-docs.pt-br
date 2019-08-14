---
title: Layouts no Xamarin.Forms
description: Xamarin.Forms tem vários layouts e recursos para organizar o conteúdo na tela, e este artigo explica-los.
ms.prod: xamarin
ms.assetid: 65030DA3-C7C1-4A02-B478-811073C39139
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 12/18/2018
ms.openlocfilehash: 7d63dafbb17a3757f5d15f65e977b4b0cb8bc44a
ms.sourcegitcommit: 41a029c69925e3a9d2de883751ebfd649e8747cd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68980872"
---
# <a name="layouts-in-xamarinforms"></a>Layouts no Xamarin.Forms

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)

Xamarin.Forms tem vários layouts e recursos para organizar o conteúdo na tela.

> [!VIDEO https://youtube.com/embed/4HlLjTZQzjM]

**Vídeo de layouts do Xamarin. Forms**

Cada controle de layout é descrito abaixo, bem como detalhes sobre como lidar com alterações de orientação de tela:

* **[StackLayout](stack-layout.md)** – usado para organizar exibições lineares, tanto horizontal quanto verticalmente. Modos de exibição em um StackLayout podem ser alinhados ao centro, esquerda ou direita do layout.
* **[AbsoluteLayout](absolute-layout.md)** – usado para organizar as exibições definindo coordenadas & tamanho em termos de valores absolutos ou taxas. AbsoluteLayout pode ser usado para a camada de modos de exibição, bem como ancorá-los para a esquerda, direita ou centralizado.
* **[RelativeLayout](relative-layout.md)** – usado para organizar as exibições definindo restrições relativas às dimensões de seu pai & posição.
* **[Grade](grid.md)** – usada para organizar as exibições em uma grade. Linhas e colunas podem ser especificadas em termos de valores absolutos ou taxas.
* **[FlexLayout](flex-layout.md)** – usado para organizar as exibições horizontal ou verticalmente com o encapsulamento.
* **[Quadro](frame.md)** -usado para encapsular uma exibição com uma borda, ou quadro, que pode ser configurada com cores, arrastar sombra e muito mais.
* **[ScrollView](scroll-view.md)** – usado para fornecer rolagem quando uma exibição não se ajusta totalmente dentro dos limites da tela.
* **[Layoutoptions](layout-options.md)** – defina alinhamento e expansão para uma exibição, em relação ao seu pai.
* **[Transparência de entrada](#input_transparency)** – especifica se um elemento recebe entrada.
* **[Margin e Padding](margin-and-padding.md)** – demonstra como controlar o comportamento do layout quando um elemento é renderizado na interface do usuário.
* **[Orientação do dispositivo](device-orientation.md)** – explica como lidar com as alterações de orientação do dispositivo.
* **[Layout em dispositivos Tablet e desktop](tablet.md)** – mostra como otimizar para telas maiores em cada plataforma.
* **[Layouts vinculáveis](bindable-layouts.md)** – habilite as classes de layout para gerar seu conteúdo ligando a uma coleção de itens.
* **[Criando um layout personalizado](custom.md)** – explica como criar uma classe de layout personalizada.
* **[Compactação de layout](layout-compression.md)** – remove o layout especificado da árvore visual em uma tentativa de melhorar o desempenho de renderização de página.

Controles de plataforma também podem ser usados diretamente no Xamarin.Forms layouts com [ **incorporação nativa** ](~/xamarin-forms/platform/native-views/index.md) (novo no Xamarin.Forms 2.2), e você pode [ **criar layouts personalizados** ](custom.md) para atender às necessidades específicas.

O gráfico a seguir visualiza os controles de layout:

[![](images/layouts-sml.png "Xamarin. Forms Layouts")](images/layouts.png#lightbox "Layouts do xamarin. Forms")

## <a name="choosing-the-right-layout"></a>Escolher o Layout Certo

Os layouts que você escolher para seu aplicativo podem ajudar ou prejudicar como você está criando um aplicativo Xamarin.Forms atraente e utilizável. Dedicar algum tempo para considerar como funciona cada layout pode ajudá-lo a escrever código de interface do usuário mais limpo e escalonável. Uma tela pode ter uma combinação de layouts diferentes para alcançar um design específico.

### <a name="stacklayoutstack-layoutmd"></a>[StackLayout](stack-layout.md)

O `StackLayout` é usado para exibir os modos de exibição ao longo de uma linha horizontal ou vertical. Posição e tamanho do layout são determinados com base em um modo de exibição `HeightRequest`, `WidthRequest`, `HorizontalOptions` e `VerticalOptions`. `StackLayout` geralmente é usado como o layout de base, organizando outros layouts na tela.

Para obter um exemplo de quando o `StackLayout` seria uma boa opção, considere um aplicativo que precisa para exibir um botão e um rótulo, com o rótulo alinhado à esquerda e o botão alinhado à direita.

```xaml
<StackLayout Orientation="Horizontal">
  <Label HorizontalOptions="StartAndExpand" Text="Label" />
  <Button HorizontalOptions="End" Text="Button" />
</StackLayout>
```

### <a name="flexlayoutflex-layoutmd"></a>[FlexLayout](flex-layout.md)

O `FlexLayout` é semelhante ao `StackLayout` em que ele exibe os modos de exibição filho horizontal ou verticalmente:

```xaml
<FlexLayout Direction="Column"
            AlignItems="Center"
            JustifyContent="SpaceEvenly">

    <Label Text="FlexLayout in Action" />
    <Button Text="Button" />
    <Label Text="Another Label" />
</FlexLayout>
```

No entanto, se houver muitos filhos para caber em uma única linha ou coluna, `FlexLayout` também é capaz de encapsular essas exibições. `FlexLayout` se baseia no módulo de Layout de caixa flexível CSS e tem muitas das mesmas opções internas para posicionar e alinhar seus filhos.

### <a name="absolutelayoutabsolute-layoutmd"></a>[AbsoluteLayout](absolute-layout.md)

O `AbsoluteLayout` é usado para exibir os modos de exibição, com tamanho e posição que está sendo especificado como valores explícitos ou relativo ao tamanho do layout. Diferentemente `StackLayout` e `Grid`, `AbsoluteLayout` permite que o filho exibições se sobrepõem. Diferentemente `RelativeLayout`, `AbsoluteLayout` não permite que você colocar elementos fora da tela.

Para obter um exemplo de quando `AbsoluteLayout` seria uma boa opção, considere um aplicativo que precisa para apresentar as coleções de objetos como pilhas. Isso é geralmente visto ao apresentar álbuns de fotos ou músicas. O código a seguir fornece a aparência de uma pilha, com elementos girados para dica sobre o conteúdo da pilha:

No XAML:

```xaml
<AbsoluteLayout Padding="15">
  <Image AbsoluteLayout.LayoutFlags="PositionProportional" AbsoluteLayout.LayoutBounds="0.5, 0, 100, 100" Rotation="30"
    Source="bottom.png" />
  <Image AbsoluteLayout.LayoutFlags="PositionProportional" AbsoluteLayout.LayoutBounds="0.5, 0, 100, 100" Rotation="60"
    Source="middle.png" />
  <Image AbsoluteLayout.LayoutFlags="PositionProportional" AbsoluteLayout.LayoutBounds="0.5, 0, 100, 100"
    Source="cover.png" />
</AbsoluteLayout>
```

Observe os seguintes aspectos de código acima:

- Cada `Image` é exibida na mesma posição (no meio do espaço horizontal)
- O `Padding` será considerado pelo `AbsoluteLayout`, ao contrário `RelativeLayout`, que vão ignorá-la.
- `AbsoluteLayout.LayoutFlags` Especifica como os limites de layout serão interpretados. Nesse caso, `PositionProportional`, significa que as coordenadas será uma proporção do tamanho do layout, enquanto o tamanho será interpretado como um tamanho de explícito.
- `AbsoluteLayout.Layoutbounds` Especifica a posição horizontal, vertical posição, largura e altura nessa ordem.

### <a name="relativelayoutrelative-layoutmd"></a>[RelativeLayout](relative-layout.md)

O `RelativeLayout` é usado para exibir os modos de exibição, com tamanho e posição especificada como valores relativos aos valores de layout ou de outro modo de exibição. Não é necessário correspondê-lo corresponde o valor no modo de exibição relacionado valores relativos. Por exemplo, é possível definir um modo de exibição `Width` propriedade seja proporcional para outra exibição `X` propriedade.

RelativeLayout pode ser usado para criar interfaces do usuário que são dimensionados proporcionalmente de tamanhos de dispositivo. O XAML a seguir implementa um design com as caixas nos cantos mais altas, com um flagpole com sinalizador no Centro de:

```xaml
<RelativeLayout HorizontalOptions="FillAndExpand" VerticalOptions="FillAndExpand">
  <BoxView Color="Blue" HeightRequest="50" WidthRequest="50"
    RelativeLayout.XConstraint= "{ConstraintExpression Type=RelativeToParent, Property=Width, Factor = 0}"
    RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor = 0}" />
  <BoxView Color="Red" HeightRequest="50" WidthRequest="50"
    RelativeLayout.XConstraint= "{ConstraintExpression Type=RelativeToParent, Property=Width, Factor = .9}"
    RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor = 0}" />
  <BoxView Color="Gray" WidthRequest="15" x:Name="pole"
    RelativeLayout.HeightConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=.75}"
    RelativeLayout.XConstraint= "{ConstraintExpression Type=RelativeToParent, Property=Width, Factor = .45}"
    RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor = .25}" />
  <BoxView Color="Green"
    RelativeLayout.HeightConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=.10, Constant=10}"
    RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent,Property=Width, Factor=.2, Constant=20}"
    RelativeLayout.XConstraint= "{ConstraintExpression Type=RelativeToView, ElementName=pole, Property=X, Constant=15}"
    RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToView, ElementName=pole, Property=Y, Constant=0}" />
</RelativeLayout>
```

Observe os seguintes aspectos de código acima:

- Tamanhos e posições são especificados como restrições.
- O flagpole é chamado para que o sinalizador (verde da caixa) posição pode ser definida em relação a flagpole.
- As expressões de restrição tem `Factor` e `Constant` propriedades, que podem ser usadas para definir os tamanhos e posições como múltiplos (ou frações) das propriedades de outros objetos, além de uma constante. Constantes podem ser negativos.

### <a name="gridgridmd"></a>[Grade](grid.md)

O `Grid` é usado para exibir elementos em linhas e colunas. Observe que a grade não é uma tabela, para que ele não tem o conceito de células, linhas de cabeçalho e rodapé ou bordas entre linhas e colunas. Em geral, a grade não é adequado para exibir dados tabulares. Para esse uso, considere uma [ListView](~/xamarin-forms/user-interface/listview/index.md) ou [modo de tabela](~/xamarin-forms/user-interface/tableview.md).

Para obter um exemplo de quando um `Grid` é o layout da direita para usar, considere uma entrada numérica para uma calculadora. Uma entrada numérica para uma calculadora pode consistir em quatro linhas e três colunas, cada um com um botão. O código a seguir implementa esse design:

```xaml
<Grid>
  <Grid.RowDefinitions>
    <RowDefinition Height="*" />
    <RowDefinition Height="*" />
    <RowDefinition Height="*" />
    <RowDefinition Height="*" />
  </Grid.RowDefinitions>

  <Grid.ColumnDefinitions>
    <ColumnDefinition Width="*" />
    <ColumnDefinition Width="*" />
    <ColumnDefinition Width="*" />
  </Grid.ColumnDefinitions>
  <Button Text="1" Grid.Row="0" Grid.Column="0" />
  <Button Text="2" Grid.Row="0" Grid.Column="1" />
  <Button Text="3" Grid.Row="0" Grid.Column="2" />
  <Button Text="4" Grid.Row="1" Grid.Column="0" />
  <Button Text="5" Grid.Row="1" Grid.Column="1" />
  <Button Text="6" Grid.Row="1" Grid.Column="2" />
  <Button Text="7" Grid.Row="2" Grid.Column="0" />
  <Button Text="8" Grid.Row="2" Grid.Column="1" />
  <Button Text="9" Grid.Row="2" Grid.Column="2" />
  <Button Text="0" Grid.Row="3" Grid.Column="1" />
  <Button Text="&lt;-" Grid.Row="3" Grid.Column="2" />
</Grid>
```

Observe os seguintes aspectos de código acima:

- Grades e colunas são especificadas explicitamente, não é inferido do conteúdo.
- `Height` e `Width` valores podem ser definidos como estrelas, o que significa que a grade definirá esses valores para preencher o espaço disponível.
- Posição de cada botão é especificada por `Grid.Row`  &  `Grid.Column` propriedades.

### <a name="frameframemd"></a>[Quadro](frame.md)

Um `Frame` layout é usado para criar uma borda personalizável em torno `View`de filho. Um `Frame` pode ser usado para colocar uma borda em torno de um controle como `Button` ou `Label` ou pode encapsular outros layouts para criar objetos de interface do usuário mais complexos, como um cartão.

### <a name="layoutoptionslayout-optionsmd"></a>[LayoutOptions](layout-options.md)

O [ `LayoutOptions` ](xref:Xamarin.Forms.LayoutOptions) estrutura pode ser usada para definir o alinhamento e expansão para uma exibição, relativo ao seu pai.

### <a name="margin-and-paddingmargin-and-paddingmd"></a>[Margem e preenchimento](margin-and-padding.md)

O [ `Margin` ](xref:Xamarin.Forms.View.Margin) e [ `Padding` ](xref:Xamarin.Forms.Layout.Padding) propriedades controlam o comportamento de layout quando um elemento é renderizado na interface do usuário.

<a name="input_transparency" />

### <a name="input-transparency"></a>Entrada de transparência

Cada elemento tem um [ `InputTransparent` ](xref:Xamarin.Forms.VisualElement.InputTransparent) propriedade que é usada para definir se o elemento recebe entrada. Seu valor padrão é `false`, garantindo que o elemento recebe entrada.

Quando essa propriedade é definida em uma classe de contêiner, como uma classe de layout, as transferências de seu valor para os elementos filho. Portanto, definir a [ `InputTransparent` ](xref:Xamarin.Forms.VisualElement.InputTransparent) propriedade `true` em um layout de classe resultará em elementos do layout do não recebendo entrada.

### <a name="device-orientationdevice-orientationmd"></a>[Orientação do dispositivo](device-orientation.md)

Xamarin. Forms e seus layouts internos são capazes de lidar com alterações na orientação do dispositivo. Considere quais orientações seu aplicativo dará suporte, bem como você fará uso de espaço fornecido nos modos retrato e paisagem.

### <a name="layout-for-tablet-and-desktop-appstabletmd"></a>[Layout para aplicativos de Tablet e área de trabalho](tablet.md)

iOS, Android e plataforma Universal do Windows todas as maiores tamanhos de tela de suporte em dispositivos do tablet (bem como laptops e desktops para Windows). Xamarin. Forms permite que você otimize seu aplicativo para telas maiores, como detectar o tipo de dispositivo e a ajustar o layout de página ou usando uma página totalmente diferente completamente para telas maiores.

### <a name="bindable-layoutsbindable-layoutsmd"></a>[Layouts Associáveis](bindable-layouts.md)

A `BindableLayout` classe habilita qualquer classe [`Layout<T>`](xref:Xamarin.Forms.Layout`1) de layout que deriva da classe para gerar seu conteúdo ligando-se a uma coleção de itens, com a opção de definir a aparência de cada item com um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate).

### <a name="creating-a-custom-layoutcustommd"></a>[Criar um layout personalizado](custom.md)

Xamarin. Forms define quatro classes de layout - [ `StackLayout` ](xref:Xamarin.Forms.StackLayout), [ `AbsoluteLayout` ](xref:Xamarin.Forms.AbsoluteLayout), [ `RelativeLayout` ](xref:Xamarin.Forms.RelativeLayout), e [ `Grid` ](xref:Xamarin.Forms.Grid), e cada organiza seus filhos de uma maneira diferente. No entanto, às vezes, seu necessário para organizar o conteúdo da página usando um layout não fornecidos pelo xamarin. Forms. Este artigo explica como escrever uma classe de layout personalizado e demonstra uma orientação diferencia `WrapLayout` classe que organiza seus filhos horizontalmente pela página e, em seguida, ajusta a exibição dos filhos subsequentes em linhas adicionais.

### <a name="layout-compressionlayout-compressionmd"></a>[Compactação de Layout](layout-compression.md)

Compactação de layout remove os layouts especificados da árvore visual em uma tentativa de melhorar o desempenho de renderização da página. O benefício de desempenho que isso oferece varia dependendo da complexidade de uma página, da versão do sistema operacional que está sendo usado e do dispositivo no qual o aplicativo está sendo executado. No entanto, os maiores ganhos de desempenho serão observados em versões mais antigas.

## <a name="making-your-choice"></a>Fazendo sua escolha

Lembre-se de que na maioria dos casos, mais de uma opção de layout pode ser usada para implementar o design desejado. Quando há várias opções válidas, considere a abordagem que será a maneira mais fácil para a sua situação.
A maioria dos projetos não podem ser realizados com apenas um layout, portanto nest layouts como necessário para criar designs mais complexos.

## <a name="related-links"></a>Links relacionados

- [Diretrizes de Interface humana da Apple](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/MobileHIG)
- [Site de Design do Android](https://developer.android.com/design/index.html)
- [Layout (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)
- [Exemplo de BusinessTumble (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-businesstumble)
