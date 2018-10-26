---
title: Modo de tabela do xamarin. Forms
description: Este artigo explica como usar a classe de modo de tabela do xamarin. Forms para apresentar os menus de rolagem, configurações e formulários de entrada em aplicativos.
ms.prod: xamarin
ms.assetid: D1619D19-A74F-40DF-8E53-B1B7DFF7A3FB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/04/2018
ms.openlocfilehash: b8e851e735fa39d015e22ce511c39ad825bc97c9
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50119977"
---
# <a name="xamarinforms-tableview"></a>Modo de tabela do xamarin. Forms

[Modo de tabela](xref:Xamarin.Forms.TableView) é um modo de exibição de lista rolável de dados ou opções em que existem linhas que não compartilham o mesmo modelo. Diferentemente [ListView](~/xamarin-forms/user-interface/listview/index.md), modo de tabela não tem o conceito de um `ItemsSource`, portanto, os itens devem ser adicionados como filhos manualmente.

Este guia é composto das seções a seguir:

- **[Casos de uso](#Use_Cases)**  &ndash; quando usar o modo de tabela em vez de ListView ou uma exibição personalizada.
- **[Estrutura do modo de tabela](#TableView_Structure)**  &ndash; a hierarquia de modos de exibição que é necessária em um modo de tabela.
- **[Aparência do modo de tabela](#TableView_Appearance)**  &ndash; as opções de personalização para o modo de tabela.
- **[Células internos](#Built-In_Cells)**  &ndash; opções de célula internas, incluindo [EntryCell](#EntryCell) e [SwitchCell](#SwitchCell).
- **[Células personalizadas](#Custom_Cells)**  &ndash; como fazer seus próprios células personalizadas.

![](tableview-images/tableview-all-sml.png "Exemplo de modo de tabela")

<a name="Use_Cases" />

## <a name="use-cases"></a>Casos de uso

Modo de tabela é útil quando:

- apresentar uma lista de configurações,
- coleta de dados em um formulário, ou
- mostrando dados que são apresentados diferente da linha a linha (por exemplo, números, porcentagens e imagens).

Modo de tabela lida com a rolagem e definir o layout de linhas nas seções atraentes, uma necessidade comum para os cenários acima. O `TableView` usa o controle de exibição equivalente quando estiverem disponíveis, criar uma aparência nativa para cada plataforma de base de cada plataforma.

<a name="TableView_Structure" />

## <a name="tableview-structure"></a>Estrutura do modo de tabela

Elementos em um `TableView` são organizados em seções. Na raiz do `TableView` é o `TableRoot`, que é o pai de um ou mais `TableSections`:

```csharp
Content = new TableView {
    Root = new TableRoot {
        new TableSection...
    },
    Intent = TableIntent.Settings
};
```

Cada `TableSection` consiste em um título e um ou mais ViewCells. Aqui, vemos o `TableSection`do `Title` propriedade definida como *"Anel"* no construtor:

```csharp
var section = new TableSection ("Ring") { //TableSection constructor takes title as an optional parameter
    new SwitchCell {Text = "New Voice Mail"},
    new SwitchCell {Text = "New Mail", On = true}
};
```

Para realizar o mesmo layout como acima no XAML:

```xaml
<TableView Intent="Settings">
    <TableRoot>
        <TableSection Title="Ring">
            <SwitchCell Text="New Voice Mail" />
      <SwitchCell Text="New Mail" On="true" />
        </TableSection>
    </TableRoot>
</TableView>
```

<a name="TableView_Appearance" />

## <a name="tableview-appearance"></a>Aparência do modo de tabela

Modo de tabela expõe os `Intent` propriedade, que é uma enumeração das seguintes opções:

- **Dados** &ndash; para uso ao exibir entradas de dados. Observe que [ListView](~/xamarin-forms/user-interface/listview/index.md) pode ser uma opção melhor para listas de dados de rolagem.
- **Formulário** &ndash; para uso quando o modo de tabela está agindo como um formulário.
- **Menu** &ndash; para uso ao apresentar um menu das seleções.
- **As configurações** &ndash; para uso ao exibir uma lista de definições de configuração.

O `TableIntent` escolhida podem afetar como o `TableView` aparece em cada plataforma. Mesmo que haja não limpará as diferenças, é uma prática recomendada para selecionar o `TableIntent` que mais se aproxima como você pretende usar a tabela.

<a name="Built-In_Cells" />

## <a name="built-in-cells"></a>Células internas

Xamarin. Forms é fornecido com células internas para coletar e exibir informações. Embora o ListView e o modo de tabela podem usar todas as células mesmas, é mais relevantes para um cenário de modo de tabela a seguir:

- **SwitchCell** &ndash; para apresentar e capturar um estado de verdadeiro/falso, juntamente com um rótulo de texto.
- **EntryCell** &ndash; para apresentar e capturando texto.

Ver [aparência de célula do ListView](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md) para obter uma descrição detalhada dos [TextCell](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#TextCell) e [ImageCell](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#ImageCell).

<a name="switchcell" />

### <a name="switchcell"></a>SwitchCell
[`SwitchCell`](xref:Xamarin.Forms.SwitchCell) é o controle a ser usado para apresentar e capturar um ligado/desligado ou `true` / `false` estado.

SwitchCells tiver uma linha de texto a ser editado e uma propriedade liga/desliga. Ambas essas propriedades são associáveis.

- `Text` &ndash; texto a ser exibido ao lado do comutador.
- `On` &ndash; Se a opção é exibida como em ou desativado.

Observe que o `SwitchCell` expõe o `OnChanged` evento, permitindo que você responda às alterações no estado da célula.

![](tableview-images/switch-cell.png "Exemplo de SwitchCell")

<a name="entrycell" />

### <a name="entrycell"></a>EntryCell
[`EntryCell`](xref:Xamarin.Forms.EntryCell) é útil quando você precisa exibir dados de texto que o usuário pode editar. `EntryCell`s oferecem as seguintes propriedades que podem ser personalizadas:

- `Keyboard` &ndash; O teclado para exibir durante a edição. Há opções para coisas como valores numéricos, email, números de telefone, etc. [Consulte os documentos de API](xref:Xamarin.Forms.Keyboard).
- `Label` &ndash; O texto de rótulo a ser exibida à direita do campo de entrada de texto.
- `LabelColor` &ndash; A cor do texto do rótulo.
- `Placeholder` &ndash; Texto a ser exibido no campo de entrada quando ele é nulo ou vazio. Esse texto desaparece quando a entrada de texto começa.
- `Text` &ndash; O texto no campo de entrada.
- `HorizontalTextAlignment` &ndash; O alinhamento horizontal do texto. Pode ser centro, esquerdo ou direito alinhado. [Consulte os documentos de API](xref:Xamarin.Forms.TextAlignment).

Observe que `EntryCell` expõe o `Completed` evento, que é disparado quando o usuário acessa 'concluído' no teclado durante a edição de texto.

![](tableview-images/entry-cell.png "Exemplo de EntryCell")

<a name="Custom_Cells" />

## <a name="custom-cells"></a>Células personalizadas
Quando as células internas não forem suficientes, células personalizadas podem ser usadas para apresentar e capturar dados da maneira que faça sentido para seu aplicativo. Por exemplo, você talvez queira apresentar um controle deslizante para permitir ao usuário escolher a opacidade de uma imagem.

Todas as células personalizadas devem derivar de [ `ViewCell` ](xref:Xamarin.Forms.ViewCell), a mesma classe base que todos os internos célula tipos de uso.

Este é um exemplo de uma célula personalizada:

![](tableview-images/custom-cell.png "Exemplo de célula personalizado")

### <a name="xaml"></a>XAML
O XAML para criar o layout acima está abaixo:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="DemoTableView.TablePage" Title="TableView">
    <ContentPage.Content>
        <TableView Intent="Settings">
            <TableRoot>
                <TableSection Title="Getting Started">
                    <ViewCell>
                        <StackLayout Orientation="Horizontal">
                            <Image Source="bulb.png" />
                            <Label Text="left"
                              TextColor="#f35e20" />
                            <Label Text="right"
                              HorizontalOptions="EndAndExpand"
                              TextColor="#503026" />
                        </StackLayout>
                    </ViewCell>
                </TableSection>
            </TableRoot>
        </TableView>
    </ContentPage.Content>
</ContentPage>

```

O XAML acima está fazendo muito. Vamos dividi-lo:

- O elemento raiz sob o `TableView` é o `TableRoot`.
- Há um `TableSection` imediatamente sob o `TableRoot`.
- O `ViewCell` é definido diretamente sob a seção. Diferentemente `ListView`, `TableView` não requer que personalizado (ou qualquer) as células são definidas em um `ItemTemplate`.
- Um StackLayout é usado para gerenciar o layout da célula personalizada. Qualquer layout pode ser usado aqui.

### <a name="cnum"></a>C&num;

Porque `TableView` funciona com dados estáticos ou dados que são alterados manualmente, ele não tem o conceito de um modelo de item. Em vez disso, as células personalizadas podem ser criadas manualmente e colocar na tabela. Observe que a técnica de criar um personalizado de célula que herda de `ViewCell`, em seguida, adicioná-lo para o `TableView` como você faria uma célula interna, também há suporte para.
Aqui está o código c# para realizar o layout acima:

```csharp
var table = new TableView();
table.Intent = TableIntent.Settings;
var layout = new StackLayout() { Orientation = StackOrientation.Horizontal };
layout.Children.Add (new Image() {Source = "bulb.png"});
layout.Children.Add (new Label() {
    Text = "left",
    TextColor = Color.FromHex("#f35e20"),
    VerticalOptions = LayoutOptions.Center
});
layout.Children.Add (new Label () {
    Text = "right",
    TextColor = Color.FromHex ("#503026"),
    VerticalOptions = LayoutOptions.Center,
    HorizontalOptions = LayoutOptions.EndAndExpand
});
table.Root = new TableRoot () {
    new TableSection("Getting Started") {
        new ViewCell() {View = layout}
    }
};

Content = table;
```

O c# acima está fazendo muito. Vamos dividi-lo:

- O elemento raiz sob o `TableView` é o `TableRoot`.
- Há um `TableSection` imediatamente sob o `TableRoot`.
- O `ViewCell` é definido diretamente sob a seção. Diferentemente `ListView`, `TableView` não requer que personalizado (ou qualquer) as células são definidas em um `ItemTemplate`.
- Um StackLayout é usado para gerenciar o layout da célula personalizada. Qualquer layout pode ser usado aqui.

Observe que uma classe para a célula personalizada nunca é definida. Em vez disso, o `ViewCell`da propriedade de exibição é definida para uma determinada instância de `ViewCell`.

## <a name="row-height"></a>Altura da linha

O [ `TableView` ](xref:Xamarin.Forms.TableView) classe tem duas propriedades que podem ser usadas para alterar a altura da linha de células:

- [`RowHeight`](xref:Xamarin.Forms.TableView.RowHeight) – Define a altura de cada linha para um `int`.
- [`HasUnevenRows`](xref:Xamarin.Forms.TableView.HasUnevenRows) – linhas têm diferentes alturas se definido como `true`. Observe que, ao definir essa propriedade como `true`, as alturas das linhas automaticamente serão calculadas e aplicadas pelo xamarin. Forms.

Quando a altura do conteúdo em uma célula em uma [ `TableView` ](xref:Xamarin.Forms.TableView) for alterado, a linha de altura é atualizada implicitamente no Android e Universal Windows Platform (UWP). No entanto, no iOS ele deve ser forçado a atualizar definindo a [ `HasUnevenRows` ](xref:Xamarin.Forms.TableView.HasUnevenRows) propriedade `true` e chamando o [ `Cell.ForceUpdateSize` ](xref:Xamarin.Forms.Cell.ForceUpdateSize) método.

A exemplo XAML a seguir mostra uma [ `TableView` ](xref:Xamarin.Forms.TableView) que contém um [ `ViewCell` ](xref:Xamarin.Forms.ViewCell):

```xaml
<ContentPage ...>
    <TableView ...
               HasUnevenRows="true">
        <TableRoot>
            ...
            <TableSection ...>
                ...
                <ViewCell x:Name="_viewCell"
                          Tapped="OnViewCellTapped">
                    <Grid Margin="15,0">
                        <Grid.RowDefinitions>
                            <RowDefinition Height="Auto" />
                            <RowDefinition Height="Auto" />
                        </Grid.RowDefinitions>
                        <Label Text="Tap this cell." />
                        <Label x:Name="_target"
                               Grid.Row="1"
                               Text="The cell has changed size."
                               IsVisible="false" />
                    </Grid>
                </ViewCell>
            </TableSection>
        </TableRoot>
    </TableView>
</ContentPage>
```

Quando o [ `ViewCell` ](xref:Xamarin.Forms.ViewCell) é tocado, o `OnViewCellTapped` manipulador de eventos é executado:

```csharp
void OnViewCellTapped(object sender, EventArgs e)
{
    _target.IsVisible = !_target.IsVisible;
    _viewCell.ForceUpdateSize();
}
```

O `OnViewCellTapped` manipulador de eventos mostra ou oculta a segunda [ `Label` ](xref:Xamarin.Forms.Label) no [ `ViewCell` ](xref:Xamarin.Forms.ViewCell)e atualiza explicitamente o tamanho da célula, chamando o [ `Cell.ForceUpdateSize` ](xref:Xamarin.Forms.Cell.ForceUpdateSize) método.

As capturas de tela a seguir mostram a célula antes da que está sendo tocado após:

![](tableview-images/cell-beforeresize.png "ViewCell antes que está sendo redimensionada")

Capturas de tela as seguir mostram a célula depois sendo tocado após:

![](tableview-images/cell-afterresize.png "ViewCell depois que está sendo redimensionada")

> [!IMPORTANT]
> Há uma grande possibilidade de degradação do desempenho se esse recurso está sendo usado em excesso.

## <a name="related-links"></a>Links relacionados

- [Modo de tabela (amostra)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/TableView)
