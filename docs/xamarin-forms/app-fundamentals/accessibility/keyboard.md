---
title: Acessibilidade do teclado
description: Em vez de usar a sequência de tabulação padrão, às vezes é necessário ajustar a acessibilidade da interface do usuário especificando a sequência de tabulação com uma combinação das propriedades TabIndex e IsTapStop.
ms.prod: xamarin
ms.assetid: 8be8f498-558a-4894-a01f-91a0d3ef927e
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/09/2019
ms.openlocfilehash: ba29e57008bfea420562d88ab1612c09f4460e85
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68651068"
---
# <a name="keyboard-accessibility-in-xamarinforms"></a>Acessibilidade do teclado no Xamarin.Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-accessibility)

Os usuários que utilizam leitores de tela ou que tenham problemas de mobilidade podem ter dificuldade para usar aplicativos que não fornecem acesso apropriado por teclado. Os aplicativos Xamarin.Forms aceitam a especificação de uma ordem de tabulação a fim de melhorar a usabilidade e a acessibilidade. Especificar uma ordem de tabulação para os controles habilita a navegação por teclado, prepara as páginas do aplicativo para receber as entradas em uma ordem específica e possibilita aos leitores de tela a leitura de elementos focalizáveis para o usuário.

Por padrão, a ordem de tabulação dos controles é a mesma ordem em que eles são listados no XAML ou são adicionados de maneira programática a uma coleção filho. Essa é a ordem em que os controles serão navegados usando um teclado e lidos pelos leitores de tela, e geralmente essa ordem padrão é a melhor. No entanto, a ordem padrão nem sempre é o mesmo que a ordem esperada, conforme mostrado no exemplo de código XAML a seguir:

```xaml
<Grid>
    <Grid.ColumnDefinitions>
        <ColumnDefinition Width="0.5*" />
        <ColumnDefinition Width="0.5*" />
    </Grid.ColumnDefinitions>
    <Grid.RowDefinitions>
        <RowDefinition Height="Auto" />
        <RowDefinition Height="Auto" />
        <RowDefinition Height="Auto" />
    </Grid.RowDefinitions>
    <Label Text="You"
           HorizontalOptions="Center" />
    <Label Grid.Column="1"
           Text="Manager"
           HorizontalOptions="Center" />
    <Entry Grid.Row="1"
           Placeholder="Enter forename" />
    <Entry Grid.Column="1"
           Grid.Row="1"
           Placeholder="Enter forename" />
    <Entry Grid.Row="2"
           Placeholder="Enter surname" />
    <Entry Grid.Column="1"
           Grid.Row="2"
           Placeholder="Enter surname" />
</Grid>
```

A captura de tela a seguir mostra a ordem de tabulação padrão para este exemplo de código:

![](keyboard-images/default-tab-order.png "Ordem de tabulação baseada em linha padrão")

A ordem de tabulação aqui é baseada nas linhas e é a ordem em que os controles são listados no XAML. Portanto, pressionar a tecla Tab navega pelas instâncias de [`Entry`](xref:Xamarin.Forms.Entry) do primeiro nome, seguidas pelas instâncias de `Entry` do sobrenome. No entanto, uma experiência mais intuitiva seria usar a navegação com tabulação por colunas, de modo que pressionar a tecla Tab navegaria pelos pares de primeiro nome e sobrenome. Isso pode ser feito especificando a ordem de tabulação dos controles de entrada.

> [!NOTE]
> Na Plataforma Universal do Windows, é possível definir atalhos de teclado que fornecem uma maneira intuitiva para os usuários navegarem e interagirem rapidamente com a interface do usuário visível do aplicativo usando um teclado em vez do mouse ou de uma tela de toque. Para obter mais informações, confira [Configurando chaves de acesso de VisualElement](~/xamarin-forms/platform/windows/visualelement-access-keys.md).

## <a name="setting-the-tab-order"></a>Configurando a ordem de tabulação

A propriedade `VisualElement.TabIndex` é usada para indicar a ordem na qual as instâncias de [`VisualElement`](xref:Xamarin.Forms.VisualElement) recebem foco quando o usuário navega pelos controles pressionando a tecla Tab. O valor padrão da propriedade é 0 e ela pode ser definida como qualquer valor de `int`.

As seguintes regras se aplicam ao usar a ordem de tabulação padrão ou ao definir a propriedade `TabIndex`:

- Instâncias de [`VisualElement`](xref:Xamarin.Forms.VisualElement) com um `TabIndex` igual a 0 são adicionadas à ordem de tabulação com base em sua ordem de declaração em coleções XAML ou filho.
- Instâncias de [`VisualElement`](xref:Xamarin.Forms.VisualElement) com um `TabIndex` maior que 0 são adicionadas à ordem de tabulação com base no valor de `TabIndex`.
- Instâncias de [`VisualElement`](xref:Xamarin.Forms.VisualElement) com um `TabIndex` menor que 0 são adicionadas à ordem de tabulação e aparecem antes de qualquer valor igual a zero.
- Conflitos em um `TabIndex` são resolvidos pela ordem de declaração.

Após definir uma ordem de tabulação, pressionar a tecla Tab passará o foco pelos controles na ordem crescente de `TabIndex`, voltando para o início quando o controle final é atingido.

O exemplo de XAML a seguir mostra a propriedade `TabIndex` definida nos controles de entrada para permitir a navegação com tabulação baseada na coluna:

```xaml
<Grid>
    <Grid.ColumnDefinitions>
        <ColumnDefinition Width="0.5*" />
        <ColumnDefinition Width="0.5*" />
    </Grid.ColumnDefinitions>
    <Grid.RowDefinitions>
        <RowDefinition Height="Auto" />
        <RowDefinition Height="Auto" />
        <RowDefinition Height="Auto" />
    </Grid.RowDefinitions>
    <Label Text="You"
           HorizontalOptions="Center" />
    <Label Grid.Column="1"
           Text="Manager"
           HorizontalOptions="Center" />
    <Entry Grid.Row="1"
           Placeholder="Enter forename"
           TabIndex="1" />
    <Entry Grid.Column="1"
           Grid.Row="1"
           Placeholder="Enter forename"
           TabIndex="3" />
    <Entry Grid.Row="2"
           Placeholder="Enter surname"
           TabIndex="2" />
    <Entry Grid.Column="1"
           Grid.Row="2"
           Placeholder="Enter surname"
           TabIndex="4" />
</Grid>
```

A captura de tela a seguir mostra a ordem de tabulação para este exemplo de código:

![](keyboard-images/correct-tab-order.png "Ordem de tabulação baseada em colunas")

A ordem de tabulação aqui é baseada em colunas. Portanto, pressionar a tecla Tab navega pelos pares de [`Entry`](xref:Xamarin.Forms.Entry) de primeiro nome e sobrenome.

> [!IMPORTANT]
> Os leitores de tela no iOS e no Android respeitarão a `TabIndex` de um [`VisualElement`](xref:Xamarin.Forms.VisualElement) ao ler os elementos acessíveis na tela.

## <a name="excluding-controls-from-the-tab-order"></a>Excluindo controles da ordem de tabulação

Além de definir a ordem de tabulação dos controles, pode ser necessário excluir os controles da ordem de tabulação. Uma maneira de conseguir isso é definir a propriedade [`IsEnabled`](xref:Xamarin.Forms.VisualElement) dos controles como `false`, pois controles desabilitados são excluídos da ordem de tabulação.

No entanto, pode ser necessário excluir controles da ordem de tabulação, mesmo quando eles não estão desabilitados. Isso pode ser feito usando a propriedade `VisualElement.IsTapStop`, que indica se um [`VisualElement`](xref:Xamarin.Forms.VisualElement) está incluído na navegação por tabulação. Seu valor padrão é `true`, e quando o valor é `false` o controle é ignorado pela infraestrutura da navegação por tabulação, independentemente de um `TabIndex` estar definido.

## <a name="supported-controls"></a>Controles com suporte

As propriedades `TabIndex` e `IsTapStop` têm suporte nos seguintes controles, que aceitam entradas com teclado em uma ou mais plataformas:

- [`Button`](xref:Xamarin.Forms.Button)
- [`DatePicker`](xref:Xamarin.Forms.DatePicker)
- [`Editor`](xref:Xamarin.Forms.Editor)
- [`Entry`](xref:Xamarin.Forms.Entry)
- [`NavigationPage`](xref:Xamarin.Forms.NavigationPage)
- [`Picker`](xref:Xamarin.Forms.Picker)
- [`ProgressBar`](xref:Xamarin.Forms.ProgressBar)
- [`SearchBar`](xref:Xamarin.Forms.SearchBar)
- [`Slider`](xref:Xamarin.Forms.Slider)
- [`Stepper`](xref:Xamarin.Forms.Stepper)
- [`Switch`](xref:Xamarin.Forms.Switch)
- [`TabbedPage`](xref:Xamarin.Forms.TabbedPage)
- [`TimePicker`](xref:Xamarin.Forms.TimePicker)

> [!NOTE]
> Esses controles não são focalizáveis pela tabulação em todas as plataformas.

## <a name="related-links"></a>Links relacionados

- [Acessibilidade (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-accessibility)
