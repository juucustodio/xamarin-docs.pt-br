---
title: TableView
description: "Apresente menus de rolagem, configurações e formulários de entrada."
ms.topic: article
ms.prod: xamarin
ms.assetid: D1619D19-A74F-40DF-8E53-B1B7DFF7A3FB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: 2dff3ca311341ffe1e30145ad436820b715973b0
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2018
---
# <a name="tableview"></a>TableView

[Modo de tabela](https://developer.xamarin.com/api/type/Xamarin.Forms.TableView/) é um modo de exibição para exibir listas roláveis de dados ou opções onde há linhas que não compartilham o mesmo modelo. Ao contrário de [ListView](~/xamarin-forms/user-interface/listview/index.md), modo de tabela não tem o conceito de um `ItemsSource`, portanto, itens devem ser adicionados como filhos manualmente.

Este guia é composto das seguintes seções:

- **[Casos de uso](#Use_Cases)**  &ndash; quando usar o modo de tabela em vez de ListView ou uma exibição personalizada.
- **[Estrutura do modo de tabela](#TableView_Structure)**  &ndash; a hierarquia dos modos de exibição que é necessária em um modo de tabela.
- **[Aparência do modo de tabela](#TableView_Appearance)**  &ndash; as opções de personalização para o modo de tabela.
- **[Células internas](#Built-In_Cells)**  &ndash; opções de célula internas, incluindo [EntryCell](#EntryCell) e [SwitchCell](#SwitchCell).
- **[Células personalizadas](#Custom_Cells)**  &ndash; como criar suas próprias células personalizadas.

![](tableview-images/tableview-all-sml.png "Exemplo de modo de tabela")

<a name="Use_Cases" />

## <a name="use-cases"></a>Casos de uso

Modo de tabela é útil quando:

- apresentar uma lista de configurações
- coleta de dados em um formulário, ou
- mostrando os dados apresentados diferente da linha a linha (por exemplo, números, porcentagens e imagens).

Modo de tabela manipula rolagem e a disposição de linhas nas seções atraentes, uma necessidade comum para os cenários acima. O `TableView` usa o controle de exibição equivalente quando disponível, a criação de uma aparência nativa para cada plataforma de base de cada plataforma.

<a name="TableView_Structure" />

## <a name="tableview-structure"></a>Estrutura do modo de tabela

Elementos em um `TableView` são organizados em seções. Na raiz do `TableView` é o `TableRoot`, que é pai de um ou mais `TableSections`:

```csharp
Content = new TableView {
    Root = new TableRoot {
        new TableSection...
    },
    Intent = TableIntent.Settings
};
```

Cada `TableSection` consiste em um título e um ou mais ViewCells. Aqui, podemos ver o `TableSection`do `Title` propriedade definida como *"Anel"* no construtor:

```csharp
var section = new TableSection ("Ring") { //TableSection constructor takes title as an optional parameter
    new SwitchCell {Text = "New Voice Mail"},
    new SwitchCell {Text = "New Mail", On = true}
};
```

Para realizar o mesmo layout como acima em XAML:

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

- **Dados** &ndash; para uso ao exibir entradas de dados. Observe que [ListView](~/xamarin-forms/user-interface/listview/index.md) pode ser uma opção melhor para rolar a lista de dados.
- **Formulário** &ndash; para uso quando o modo de tabela está agindo como um formulário.
- **Menu** &ndash; para uso ao apresentar um menu das seleções.
- **Configurações de** &ndash; para uso ao exibir uma lista de definições de configuração.

O `TableIntent` você escolher podem afetar como o `TableView` aparece em cada plataforma. Mesmo se houver diferenças não fica claro, é uma prática recomendada para selecionar o `TableIntent` que mais se aproxima como você pretende usar a tabela.

<a name="Built-In_Cells" />

## <a name="built-in-cells"></a>Células internas

Xamarin. Forms vem com células internas para coletar e exibir informações. Embora ListView e o modo de tabela podem usar todas as células mesmo, é mais relevantes para um cenário de modo de tabela a seguir:

- **SwitchCell** &ndash; para apresentar e capturar o estado de verdadeiro/falso, junto com um rótulo de texto.
- **EntryCell** &ndash; para apresentar e capturar o texto.

Consulte [ListView célula aparência](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md) para uma descrição detalhada do [TextCell](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#TextCell) e [ImageCell](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#ImageCell).

<a name="switchcell" />

### <a name="switchcell"></a>SwitchCell
[`SwitchCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.SwitchCell/) é o controle a ser usado para apresentar e capturar um ativado/desativado ou `true` / `false` estado.

SwitchCells tiver uma linha de texto para editar e uma propriedade de ativar/desativar. Essas duas propriedades são associáveis.

- `Text` &ndash; texto a ser exibido ao lado do comutador.
- `On` &ndash; Se a opção é exibida como ativado ou desativado.

Observe que o `SwitchCell` expõe o `OnChanged` eventos, permitindo que você responda a alterações no estado da célula.

![](tableview-images/switch-cell.png "Exemplo de SwitchCell")

<a name="entrycell" />

### <a name="entrycell"></a>EntryCell
[`EntryCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.EntryCell/) é útil quando você precisa exibir dados de texto que o usuário pode editar. `EntryCell`s oferecem as seguintes propriedades que podem ser personalizadas:

- `Keyboard` &ndash; O teclado para exibir durante a edição. Há opções para elementos como valores numéricos, email, números de telefone, etc. [Consulte a documentação da API](http://developer.xamarin.com/api/type/Xamarin.Forms.Keyboard/).
- `Label` &ndash; O texto do rótulo exibido à direita do campo de entrada de texto.
- `LabelColor` &ndash; A cor do texto do rótulo.
- `Placeholder` &ndash; Texto a ser exibido no campo de entrada quando ele é nulo ou vazio. Esse texto desaparece quando começa a entrada de texto.
- `Text` &ndash; O texto no campo de entrada.
- `HorizontalTextAlignment` &ndash; O alinhamento horizontal do texto. Pode ser centro, esquerdo ou direito alinhado. [Consulte a documentação da API](http://developer.xamarin.com/api/type/Xamarin.Forms.TextAlignment/).

Observe que `EntryCell` expõe o `Completed` evento, que é disparado quando o usuário pressiona 'done' no teclado ao editar texto.

![](tableview-images/entry-cell.png "Exemplo de EntryCell")

<a name="Custom_Cells" />

## <a name="custom-cells"></a>Células personalizadas
Quando as células internas não são suficientes, células personalizadas podem ser usadas para apresentar e capturar dados da maneira que faça sentido para seu aplicativo. Por exemplo, você talvez queira apresentar um controle deslizante para permitir que um usuário escolha a opacidade de uma imagem.

Todas as células personalizadas devem derivar de [ `ViewCell` ](http://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/), a mesma classe base que todos os a célula interna tipos de uso.

Este é um exemplo de uma célula personalizado:

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
- Há um `TableSection` imediatamente abaixo do `TableRoot`.
- O `ViewCell` é definido diretamente sob a seção. Ao contrário de `ListView`, `TableView` não exige que personalizados (ou qualquer um) as células são definidas em um `ItemTemplate`.
- Um StackLayout é usado para gerenciar o layout da célula personalizado. Qualquer layout pode ser usada aqui.

### <a name="cnum"></a>C&num;

Porque `TableView` funciona com dados estáticos ou dados que são alterados manualmente, não tem o conceito de um modelo de item. Em vez disso, células personalizadas podem ser criadas manualmente e coloque a tabela. Observe que a técnica de criação de um personalizado célula que herda de `ViewCell`, em seguida, adicioná-lo para o `TableView` como você faria uma célula interna, também há suporte para.
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

C# acima está fazendo muito. Vamos dividi-lo:

- O elemento raiz sob o `TableView` é o `TableRoot`.
- Há um `TableSection` imediatamente abaixo do `TableRoot`.
- O `ViewCell` é definido diretamente sob a seção. Ao contrário de `ListView`, `TableView` não exige que personalizados (ou qualquer um) as células são definidas em um `ItemTemplate`.
- Um StackLayout é usado para gerenciar o layout da célula personalizado. Qualquer layout pode ser usada aqui.

Observe que nunca é definida uma classe para a célula personalizada. Em vez disso, o `ViewCell`da propriedade de exibição está definida para uma determinada instância de `ViewCell`.



## <a name="related-links"></a>Links relacionados

- [Modo de tabela (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/TableView)
