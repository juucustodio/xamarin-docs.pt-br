---
title: Xamarin.Forms TableView
description: Este artigo explica como usar a Xamarin.Forms classe Tableview para apresentar menus de rolagem, configurações e formulários de entrada em aplicativos.
ms.prod: xamarin
ms.assetid: D1619D19-A74F-40DF-8E53-B1B7DFF7A3FB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/25/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: afb2c7c5c82a7ce530846c266d231b893bbbf39d
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93371217"
---
# <a name="no-locxamarinforms-tableview"></a>Xamarin.Forms TableView

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/userinterface-tableview)

[`TableView`](xref:Xamarin.Forms.TableView) é uma exibição para exibir listas roláveis de dados ou opções em que há linhas que não compartilham o mesmo modelo. Ao contrário de [ListView](~/xamarin-forms/user-interface/listview/index.md), não `TableView` tem o conceito de um `ItemsSource` , portanto, os itens devem ser adicionados manualmente como filhos.

![Exemplo de TableView](tableview-images/tableview-all-sml.png)

## <a name="use-cases"></a>Casos de uso

[`TableView`](xref:Xamarin.Forms.TableView) é útil quando:

- Apresentando uma lista de configurações,
- coletando dados em um formulário ou
- mostrando os dados que são apresentados de forma diferente da linha para a linha (por exemplo, números, percentuais e imagens).

[`TableView`](xref:Xamarin.Forms.TableView) lida com a rolagem e o layout de linhas em seções atrativas, uma necessidade comum para os cenários acima. O `TableView` controle usa a exibição equivalente subjacente de cada plataforma quando disponível, criando uma aparência nativa para cada plataforma.

## <a name="structure"></a>Estrutura

Os elementos em um [`TableView`](xref:Xamarin.Forms.TableView) são organizados em seções. Na raiz de `TableView` é o [`TableRoot`](xref:Xamarin.Forms.TableRoot) , que é o pai de uma ou mais [`TableSection`](xref:Xamarin.Forms.TableSection) instâncias. Cada [`TableSection`](xref:Xamarin.Forms.TableSection) um consiste em um cabeçalho e uma ou mais [`ViewCell`](xref:Xamarin.Forms.ViewCell) instâncias:

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

Este é o código C# equivalente:

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

## <a name="appearance"></a>Aparência

[`TableView`](xref:Xamarin.Forms.TableView) expõe a [`Intent`](xref:Xamarin.Forms.TableView.Intent) propriedade, que pode ser definida como qualquer um dos [`TableIntent`](xref:Xamarin.Forms.TableIntent) membros da enumeração:

- `Data` – para uso ao exibir entradas de dados. Observe que [ListView](~/xamarin-forms/user-interface/listview/index.md) pode ser uma opção melhor para rolar listas de dados.
- `Form` – para uso quando o TableView está agindo como um formulário.
- `Menu` – para uso ao apresentar um menu de seleções.
- `Settings` – para uso ao exibir uma lista de definições de configuração.

O [`TableIntent`](xref:Xamarin.Forms.TableIntent) valor escolhido pode afetar o modo como [`TableView`](xref:Xamarin.Forms.TableView) aparece em cada plataforma. Mesmo que não haja diferenças claras, é uma prática recomendada selecionar o `TableIntent` que mais se aproximará de como você pretende usar a tabela.

Além disso, a cor do texto exibido para cada [`TableSection`](xref:Xamarin.Forms.TableSection) pode ser alterada definindo a `TextColor` propriedade como a [`Color`](xref:Xamarin.Forms.Color) .

## <a name="built-in-cells"></a>Células internas

Xamarin.Forms o vem com células internas para coletar e exibir informações. Embora [`ListView`](xref:Xamarin.Forms.ListView) e [`TableView`](xref:Xamarin.Forms.TableView) possa usar todas as mesmas células, [`SwitchCell`](xref:Xamarin.Forms.SwitchCell) e [`EntryCell`](xref:Xamarin.Forms.EntryCell) são as mais relevantes para um `TableView` cenário.

Consulte [aparência da célula ListView](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md) para obter uma descrição detalhada de [textcell](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#textcell) e [ImageCell](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#imagecell).

### <a name="switchcell"></a>SwitchCell

[`SwitchCell`](xref:Xamarin.Forms.SwitchCell)é o controle a ser usado para apresentar e capturar um Estado ou ligado `true` / `false` . Ele define as seguintes propriedades:

- `Text` – texto a ser exibido ao lado da opção.
- `On` – se a opção é exibida como ativada ou desativada.
- `OnColor` – o [`Color`](xref:Xamarin.Forms.Color) da mudança quando ele está na posição ligado.

Todas essas propriedades são vinculáveis.

[`SwitchCell`](xref:Xamarin.Forms.SwitchCell) também expõe o `OnChanged` evento, permitindo que você responda às alterações no estado da célula.

![Exemplo de SwitchCell](tableview-images/switch-cell.png)

### <a name="entrycell"></a>EntryCell

[`EntryCell`](xref:Xamarin.Forms.EntryCell) é útil quando você precisa exibir dados de texto que o usuário pode editar. Ele define as seguintes propriedades:

- `Keyboard` – O teclado a ser exibido durante a edição. Há opções para itens como valores numéricos, email, números de telefone, etc. [consulte os documentos da API](xref:Xamarin.Forms.Keyboard).
- `Label` – O texto do rótulo a ser exibido à esquerda do campo de entrada de texto.
- `LabelColor` – A cor do texto do rótulo.
- `Placeholder` – Texto a ser exibido no campo de entrada quando ele é nulo ou vazio. Esse texto desaparece quando a entrada de texto começa.
- `Text` – O texto no campo de entrada.
- `HorizontalTextAlignment` – O alinhamento horizontal do texto. Os valores são centralizado, esquerdo ou alinhado à direita. [Consulte os documentos da API](xref:Xamarin.Forms.TextAlignment).
- `VerticalTextAlignment` – O alinhamento vertical do texto. Os valores são `Start` , `Center` ou `End` .

[`EntryCell`](xref:Xamarin.Forms.EntryCell) também expõe o `Completed` evento, que é disparado quando o usuário pressiona o botão "Done" no teclado ao editar o texto.

![Exemplo de EntryCell](tableview-images/entry-cell.png)

## <a name="custom-cells"></a>Células personalizadas

Quando as células internas não são suficientes, as células personalizadas podem ser usadas para apresentar e capturar dados da maneira que faz sentido para seu aplicativo. Por exemplo, talvez você queira apresentar um controle deslizante para permitir que um usuário escolha a opacidade de uma imagem.

Todas as células personalizadas devem derivar de [`ViewCell`](xref:Xamarin.Forms.ViewCell) , a mesma classe base que todos os tipos de célula internos usam.

Este é um exemplo de uma célula Personalizada:

![Exemplo de célula Personalizada](tableview-images/custom-cell.png)

O exemplo a seguir mostra o XAML usado para criar o [`TableView`](xref:Xamarin.Forms.TableView) nas capturas de tela acima:

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

Este é o código C# equivalente:

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

O elemento raiz sob o [`TableView`](xref:Xamarin.Forms.TableView) é o [`TableRoot`](xref:Xamarin.Forms.TableRoot) , e há um [`TableSection`](xref:Xamarin.Forms.TableSection) logo abaixo de `TableRoot` . O [`ViewCell`](xref:Xamarin.Forms.ViewCell) é definido diretamente sob o `TableSection` , e um [`StackLayout`](xref:Xamarin.Forms.StackLayout) é usado para gerenciar o layout da célula Personalizada, embora qualquer layout possa ser usado aqui.

> [!NOTE]
> Ao [`ListView`](xref:Xamarin.Forms.ListView) contrário [`TableView`](xref:Xamarin.Forms.TableView) do, o não exige que as células personalizadas (ou nenhuma) sejam definidas em um `ItemTemplate` .

## <a name="row-height"></a>Altura da linha

A [`TableView`](xref:Xamarin.Forms.TableView) classe tem duas propriedades que podem ser usadas para alterar a altura da linha das células:

- [`RowHeight`](xref:Xamarin.Forms.TableView.RowHeight) – define a altura de cada linha como um `int` .
- [`HasUnevenRows`](xref:Xamarin.Forms.TableView.HasUnevenRows) – as linhas têm alturas variáveis se definidas como `true` . Observe que ao definir essa propriedade como `true` , as alturas de linha serão automaticamente calculadas e aplicadas pelo Xamarin.Forms .

Quando a altura do conteúdo em uma célula em um [`TableView`](xref:Xamarin.Forms.TableView) é alterada, a altura da linha é atualizada implicitamente no Android e no plataforma universal do Windows (UWP). No entanto, no iOS, ele deve ser forçado a atualizar definindo a [`HasUnevenRows`](xref:Xamarin.Forms.TableView.HasUnevenRows) propriedade como `true` e chamando o [`Cell.ForceUpdateSize`](xref:Xamarin.Forms.Cell.ForceUpdateSize) método.

O exemplo de XAML a seguir mostra um [`TableView`](xref:Xamarin.Forms.TableView) que contém um [`ViewCell`](xref:Xamarin.Forms.ViewCell) :

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

Quando o [`ViewCell`](xref:Xamarin.Forms.ViewCell) é tocado, o `OnViewCellTapped` manipulador de eventos é executado:

```csharp
void OnViewCellTapped(object sender, EventArgs e)
{
    _target.IsVisible = !_target.IsVisible;
    _viewCell.ForceUpdateSize();
}
```

O `OnViewCellTapped` manipulador de eventos mostra ou oculta o segundo [`Label`](xref:Xamarin.Forms.Label) no [`ViewCell`](xref:Xamarin.Forms.ViewCell) e atualiza explicitamente o tamanho da célula chamando o [`Cell.ForceUpdateSize`](xref:Xamarin.Forms.Cell.ForceUpdateSize) método.

As capturas de tela a seguir mostram a célula antes de serem tocadas sobre:

![ViewCell antes de ser redimensionado](tableview-images/cell-beforeresize.png)

As capturas de tela a seguir mostram a célula depois de serem tocadas:

![ViewCell depois de ser redimensionado](tableview-images/cell-afterresize.png)

> [!IMPORTANT]
> Há uma grande possibilidade de degradação do desempenho se esse recurso estiver superutilizado.

## <a name="related-links"></a>Links relacionados

- [TableView (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-tableview)