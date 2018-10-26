---
title: Navegação por teclado
description: Em vez de usar a sequência de guia padrão, às vezes, é necessário ajustar a interface do usuário, especificando a sequência de guia com uma combinação das propriedades TabIndex e IsTapStop.
ms.prod: xamarin
ms.assetid: 8be8f498-558a-4894-a01f-91a0d3ef927e
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/15/2018
ms.openlocfilehash: f703dff56d2947c35a9bc76e0eb909bfe9023bac
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50130832"
---
# <a name="keyboard-navigation-in-xamarinforms"></a>Navegação por teclado no xamarin. Forms

Alguns usuários podem ter dificuldade em usar os aplicativos que não fornecem acesso de teclado apropriado. Especificar uma ordem de tabulação para controles permite a navegação de teclado e prepara as páginas de aplicativo para receber a entrada em uma ordem específica.

Por padrão, a ordem de tabulação de controles é a mesma ordem em que eles são listados no XAML ou por meio de programação são adicionados a uma coleção de filhos. Essa ordem é a ordem em que os controles serão ser acessados por meio com um teclado e geralmente esta ordem padrão é a melhor ordem. No entanto, a ordem padrão não é sempre o mesmo que a ordem esperada, conforme mostrado no exemplo de código XAML a seguir:

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

Captura de tela a seguir mostra a ordem de tabulação padrão para este exemplo de código:

![](keyboard-images/default-tab-order.png "Ordem de tabulação em linha padrão")

A ordem de tabulação é baseado em linha e é a ordem em que os controles são listados no XAML. Portanto, pressionando a tecla Tab navega por meio de forename [ `Entry` ](xref:Xamarin.Forms.Entry) instâncias, seguidas por sobrenome `Entry` instâncias. No entanto, uma experiência mais intuitiva seria usar uma navegação por guias de coluna, primeiro, para que pressionar a tecla Tab navega por meio de pares de forename sobrenome. Isso pode ser feito especificando a ordem de tabulação dos controles de entrada.

> [!NOTE]
> Na plataforma Universal do Windows, os atalhos de teclado podem ser definidos que fornecem uma maneira intuitiva para os usuários a navegar rapidamente e interagir com a interface do usuário visível do aplicativo por meio de um teclado em vez de por meio de um mouse ou touch. Para obter mais informações, consulte [chaves de acesso da configuração VisualElement](~/xamarin-forms/platform/platform-specifics/consuming/windows.md#visualelement-accesskeys).

## <a name="setting-the-tab-order"></a>Definindo a ordem de tabulação

O `VisualElement.TabIndex` propriedade é usada para indicar a ordem na qual [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) instâncias recebem o foco quando o usuário navega por meio de controles pressionando a tecla Tab. O valor padrão da propriedade é 0, e ele pode ser definido como qualquer `int` valor.

As seguintes regras se aplicam quando usando a ordem de tabulação padrão, ou definindo o `TabIndex` propriedade:

 - [`VisualElement`](xref:Xamarin.Forms.VisualElement) instâncias com um `TabIndex` igual a 0 são adicionados à ordem de tabulação com base em sua ordem de declaração em coleções XAML ou filho.
 - [`VisualElement`](xref:Xamarin.Forms.VisualElement) instâncias com um `TabIndex` com base em maior que 0 são adicionados à ordem de tabulação em seus `TabIndex` valor.
 - [`VisualElement`](xref:Xamarin.Forms.VisualElement) instâncias com um `TabIndex` menores que 0 são adicionados à ordem de tabulação e aparecer antes de qualquer valor igual a zero.
 - Está em conflito em um `TabIndex` são resolvidos por ordem de declaração.

Depois de definir uma ordem de tabulação, pressionando a tecla Tab passará o foco por meio de controles em crescente `TabIndex` ordem, voltando para o início, quando é atingido o final do controle.

A exemplo XAML a seguir mostra o `TabIndex` propriedade definida em controles de entrada para permitir a navegação do guia da primeira coluna:

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

Captura de tela a seguir mostra a ordem de tabulação para este exemplo de código:

![](keyboard-images/correct-tab-order.png "Ordem de tabulação baseado em coluna")

A ordem de tabulação é baseado em coluna. Portanto, pressionando a tecla Tab navega pelo sobrenome forename [ `Entry` ](xref:Xamarin.Forms.Entry) pares.

## <a name="excluding-controls-from-the-tab-order"></a>Excluindo controles da ordem de tabulação

Além de definir a ordem de tabulação dos controles, ele pode ser necessário excluir os controles da ordem de tabulação. Uma maneira de conseguir isso é, definindo o [ `IsEnabled` ](xref:Xamarin.Forms.VisualElement) propriedade dos controles para `false`, pois controles desabilitados são excluídos da ordem de tabulação.

No entanto, pode ser necessário excluir os controles da ordem de tabulação, mesmo quando eles não estão desabilitados. Isso pode ser obtido com o `VisualElement.IsTapStop` propriedade, que indica se um [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) está incluído na navegação pela tecla tab. Seu valor padrão é `true`, e quando seu valor é `false` o controle será ignorado pela infraestrutura do guia de navegação, independentemente se um `TabIndex` está definido.

## <a name="supported-controls"></a>Controles com suporte

O `TabIndex` e `IsTapStop` propriedades têm suporte nos seguintes controles, que aceitam entrada de teclado em uma ou mais plataformas:

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
> Cada um desses controles não for focalizável em todas as plataformas de guia.

## <a name="related-links"></a>Links relacionados

- [Acessibilidade (amostra)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Accessibility/)
