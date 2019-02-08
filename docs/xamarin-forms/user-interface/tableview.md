---
title: Modo de tabela do xamarin. Forms
description: Este artigo explica como usar a classe de modo de tabela do xamarin. Forms para apresentar os menus de rolagem, configurações e formulários de entrada em aplicativos.
ms.prod: xamarin
ms.assetid: D1619D19-A74F-40DF-8E53-B1B7DFF7A3FB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/14/2018
ms.openlocfilehash: fda8c08cdbdab01f5d68b7f349d5f28f31316290
ms.sourcegitcommit: 93c9fe61eb2cdfa530960b4253eb85161894c882
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55831996"
---
# <a name="xamarinforms-tableview"></a>Modo de tabela do xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/TableView)

[`TableView`](xref:Xamarin.Forms.TableView) é um modo de exibição de lista rolável de dados ou opções em que existem linhas que não compartilham o mesmo modelo. Diferentemente [ListView](~/xamarin-forms/user-interface/listview/index.md), `TableView` não tem o conceito de um `ItemsSource`, portanto, os itens devem ser adicionados como filhos.

![](tableview-images/tableview-all-sml.png "Exemplo de modo de tabela")

<a name="Use_Cases" />

## <a name="use-cases"></a>Casos de uso

[`TableView`](xref:Xamarin.Forms.TableView) é útil quando:

- apresentar uma lista de configurações,
- coleta de dados em um formulário, ou
- mostrando dados que são apresentados diferente da linha a linha (por exemplo, números, porcentagens e imagens).

[`TableView`](xref:Xamarin.Forms.TableView) manipula a rolagem e layout de linhas nas seções atraentes, uma necessidade comum para os cenários acima. O `TableView` usa o controle de exibição equivalente quando estiverem disponíveis, criar uma aparência nativa para cada plataforma de base de cada plataforma.

<a name="TableView_Structure" />

## <a name="structure"></a>Estrutura

Elementos em uma [ `TableView` ](xref:Xamarin.Forms.TableView) são organizados em seções. Na raiz do `TableView` é o [ `TableRoot` ](xref:Xamarin.Forms.TableRoot), que é o pai de um ou mais [ `TableSection` ](xref:Xamarin.Forms.TableSection) instâncias. Cada [ `TableSection` ](xref:Xamarin.Forms.TableSection) consiste em um título e um ou mais [ `ViewCell` ](xref:Xamarin.Forms.ViewCell) instâncias:

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

O código c# equivalente é:

```csharp
Content = new TableView
{
    Root = new TableRoot
    {
        new TableSection("Ring")
        {
          // TableSection constructor takes title as an optional parameter
          new SwitchCell { Text = "New Voice Mail" },
          new SwitchCell { Text = "New Mail", On = true }
        }
    },
    Intent = TableIntent.Settings
};
```

<a name="TableView_Appearance" />

## <a name="appearance"></a>Aparência

[`TableView`](xref:Xamarin.Forms.TableView) expõe o [ `Intent` ](xref:Xamarin.Forms.TableView.Intent) propriedade, que pode ser definida para qualquer um dos [ `TableIntent` ](xref:Xamarin.Forms.TableIntent) membros de enumeração:

- `Data` – para uso ao exibir entradas de dados. Observe que [ListView](~/xamarin-forms/user-interface/listview/index.md) pode ser uma opção melhor para listas de dados de rolagem.
- `Form` – para uso quando o modo de tabela está agindo como um formulário.
- `Menu` – para uso ao apresentar um menu das seleções.
- `Settings` – para uso ao exibir uma lista de definições de configuração.

O [ `TableIntent` ](xref:Xamarin.Forms.TableIntent) valor escolhido pode afetar como o [ `TableView` ](xref:Xamarin.Forms.TableView) aparece em cada plataforma. Mesmo que haja não limpará as diferenças, é uma prática recomendada para selecionar o `TableIntent` que mais se aproxima como você pretende usar a tabela.

Além disso, a cor do texto exibida para cada [ `TableSection` ](xref:Xamarin.Forms.TableSection) pode ser alterado definindo o `TextColor` propriedade a um [ `Color` ](xref:Xamarin.Forms.Color).

<a name="Built-In_Cells" />

## <a name="built-in-cells"></a>Células internas

Xamarin. Forms é fornecido com células internas para coletar e exibir informações. Embora [ `ListView` ](xref:Xamarin.Forms.ListView) e [ `TableView` ](xref:Xamarin.Forms.TableView) pode usar todas as células mesmas, [ `SwitchCell` ](xref:Xamarin.Forms.SwitchCell) e [ `EntryCell` ](xref:Xamarin.Forms.EntryCell)são mais relevantes para um `TableView` cenário.

Ver [aparência de célula do ListView](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md) para obter uma descrição detalhada dos [TextCell](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#TextCell) e [ImageCell](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#ImageCell).

<a name="switchcell" />

### <a name="switchcell"></a>SwitchCell

[`SwitchCell`](xref:Xamarin.Forms.SwitchCell) é o controle a ser usado para apresentar e capturar um ligado/desligado ou `true` / `false` estado. Ele define as propriedades a seguir:

- `Text` – texto a ser exibido ao lado do comutador.
- `On` – Se a opção é exibida como em ou desativado.
- `OnColor` – o [ `Color` ](xref:Xamarin.Forms.Color) do comutador quando ele está em posição de ligado.

Todas essas propriedades são associáveis.

[`SwitchCell`](xref:Xamarin.Forms.SwitchCell) também expõe o `OnChanged` evento, permitindo que você responda às alterações no estado da célula.

![](tableview-images/switch-cell.png "Exemplo de SwitchCell")

<a name="entrycell" />

### <a name="entrycell"></a>EntryCell

[`EntryCell`](xref:Xamarin.Forms.EntryCell) é útil quando você precisa exibir dados de texto que o usuário pode editar. Ele define as propriedades a seguir:

- `Keyboard` – O teclado para exibir durante a edição. Há opções para coisas como valores numéricos, email, números de telefone, etc. [Consulte os documentos de API](xref:Xamarin.Forms.Keyboard).
- `Label` – O texto de rótulo a ser exibida à direita do campo de entrada de texto.
- `LabelColor` – A cor do texto do rótulo.
- `Placeholder` – Texto exibido no campo de entrada quando ele é nulo ou vazio. Esse texto desaparece quando a entrada de texto começa.
- `Text` – O texto no campo de entrada.
- `HorizontalTextAlignment` – O alinhamento horizontal do texto. Pode ser centro, esquerdo ou direito alinhado. [Consulte os documentos de API](xref:Xamarin.Forms.TextAlignment).

[`EntryCell`](xref:Xamarin.Forms.EntryCell) também expõe o `Completed` evento, que é disparado quando o usuário pressiona o botão 'concluído' no teclado durante a edição de texto.

![](tableview-images/entry-cell.png "Exemplo de EntryCell")

<a name="Custom_Cells" />

## <a name="custom-cells"></a>Células personalizadas

Quando as células internas não forem suficientes, células personalizadas podem ser usadas para apresentar e capturar dados da maneira que faça sentido para seu aplicativo. Por exemplo, você talvez queira apresentar um controle deslizante para permitir ao usuário escolher a opacidade de uma imagem.

Todas as células personalizadas devem derivar de [ `ViewCell` ](xref:Xamarin.Forms.ViewCell), a mesma classe base que todos os internos célula tipos de uso.

Este é um exemplo de uma célula personalizada:

![](tableview-images/custom-cell.png "Exemplo de célula personalizado")

O exemplo a seguir mostra o XAML usado para criar o [ `TableView` ](xref:Xamarin.Forms.TableView) nas capturas de tela acima:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DemoTableView.TablePage"
             Title="TableView">
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
</ContentPage>
```

O código c# equivalente é:

```csharp
var table = new TableView();
table.Intent = TableIntent.Settings;
var layout = new StackLayout() { Orientation = StackOrientation.Horizontal };
layout.Children.Add (new Image() { Source = "bulb.png"});
layout.Children.Add (new Label()
{
    Text = "left",
    TextColor = Color.FromHex("#f35e20"),
    VerticalOptions = LayoutOptions.Center
});
layout.Children.Add (new Label ()
{
    Text = "right",
    TextColor = Color.FromHex ("#503026"),
    VerticalOptions = LayoutOptions.Center,
    HorizontalOptions = LayoutOptions.EndAndExpand
});
table.Root = new TableRoot ()
{
    new TableSection("Getting Started")
    {
        new ViewCell() {View = layout}
    }
};
Content = table;
```

O elemento raiz sob o [ `TableView` ](xref:Xamarin.Forms.TableView) é o [ `TableRoot` ](xref:Xamarin.Forms.TableRoot)e há um [ `TableSection` ](xref:Xamarin.Forms.TableSection) imediatamente abaixo o `TableRoot`. O [ `ViewCell` ](xref:Xamarin.Forms.ViewCell) é definido diretamente sob o `TableSection`e um [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) é usado para gerenciar o layout da célula personalizada, embora qualquer layout pode ser usado aqui.

> [!NOTE]
> Diferentemente [ `ListView` ](xref:Xamarin.Forms.ListView), [ `TableView` ](xref:Xamarin.Forms.TableView) não exige que personalizado (ou qualquer) as células são definidas em um `ItemTemplate`.

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
