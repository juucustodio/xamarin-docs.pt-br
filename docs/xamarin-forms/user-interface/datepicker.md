---
title: Xamarin.Forms DatePicker
description: O DatePicker é uma Xamarin.Forms exibição que permite ao usuário selecionar uma data. Este artigo explica como consumir um DatePicker em um Xamarin.Forms aplicativo.
ms.prod: xamarin
ms.assetid: 68E8EF8A-42E7-4939-8ABE-64D060E609D9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/04/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: fed80f0954613a6bff49d0611d56549405c78333
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91564089"
---
# <a name="no-locxamarinforms-datepicker"></a>Xamarin.Forms DatePicker

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-datepicker)

_Uma Xamarin.Forms exibição que permite ao usuário selecionar uma data._

O Xamarin.Forms [`DatePicker`](xref:Xamarin.Forms.DatePicker) invoca o controle seletor de data da plataforma e permite que o usuário selecione uma data. `DatePicker` define oito Propriedades:

- [`MinimumDate`](xref:Xamarin.Forms.DatePicker.MinimumDate) do tipo [`DateTime`](xref:System.DateTime) , que usa como padrão o primeiro dia do ano de 1900.
- [`MaximumDate`](xref:Xamarin.Forms.DatePicker.MaximumDate) do tipo `DateTime` , que usa como padrão o último dia do ano 2100.
- [`Date`](xref:Xamarin.Forms.DatePicker.Date) do tipo `DateTime` , a data selecionada, que usa como padrão o valor [`DateTime.Today`](xref:System.DateTime.Today) .
- [`Format`](xref:Xamarin.Forms.DatePicker.Format) do tipo `string` , uma cadeia de caracteres de formatação .NET [padrão](/dotnet/standard/base-types/standard-date-and-time-format-strings/) ou [personalizada](/dotnet/standard/base-types/custom-date-and-time-format-strings/) , que usa como padrão "D", o padrão de data por extenso.
- [`TextColor`](xref:Xamarin.Forms.DatePicker.TextColor) do tipo [`Color`](xref:Xamarin.Forms.Color) , a cor usada para exibir a data selecionada, para a qual o padrão será [`Color.Default`](xref:Xamarin.Forms.Color.Default) .
- [`FontAttributes`](xref:Xamarin.Forms.DatePicker.FontAttributes) do tipo [`FontAttributes`](xref:Xamarin.Forms.FontAttributes) , que usa como padrão [`FontAtributes.None`](xref:Xamarin.Forms.FontAttributes.None) .
- [`FontFamily`](xref:Xamarin.Forms.DatePicker.FontFamily) do tipo `string` , que usa como padrão `null` .
- [`FontSize`](xref:Xamarin.Forms.DatePicker.FontSize) do tipo `double` , que usa como padrão-1,0.
- `CharacterSpacing`, do tipo `double` , é o espaçamento entre os caracteres do `DatePicker` texto.

O `DatePicker` dispara um [`DateSelected`](xref:Xamarin.Forms.DatePicker.DateSelected) evento quando o usuário seleciona uma data.

> [!WARNING]
> Ao definir `MinimumDate` e `MaximumDate` , verifique se `MinimumDate` é sempre menor ou igual a `MaximumDate` . Caso contrário, `DatePicker` gerará uma exceção.

Internamente, o `DatePicker` garante que `Date` esteja entre `MinimumDate` e `MaximumDate` , inclusive. Se `MinimumDate` ou `MaximumDate` for definido para que `Date` não esteja entre eles, `DatePicker` o ajustará o valor de `Date` .

Todas as oito propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que elas podem ser estilizadas e as propriedades podem ser destinos de associações de dados. A `Date` propriedade tem um modo de associação padrão de [`BindingMode.TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) , o que significa que ele pode ser um destino de uma associação de dados em um aplicativo que usa a arquitetura [MVVM (Model-View-ViewModel)](~/xamarin-forms/enterprise-application-patterns/mvvm.md) .

## <a name="initializing-the-datetime-properties"></a>Inicializando as propriedades DateTime

No código, você pode inicializar as `MinimumDate` `MaximumDate` Propriedades, e `Date` para valores do tipo `DateTime` :

```csharp
DatePicker datePicker = new DatePicker
{
    MinimumDate = new DateTime(2018, 1, 1),
    MaximumDate = new DateTime(2018, 12, 31),
    Date = new DateTime(2018, 6, 21)
};
```

Quando um `DateTime` valor é especificado em XAML, o analisador XAML usa o `DateTime.Parse` método com um `CultureInfo.InvariantCulture` argumento para converter a cadeia de caracteres em um `DateTime` valor. As datas devem ser especificadas em um formato preciso: meses de dois dígitos, dias de dois dígitos e anos de quatro dígitos separados por barras:

```xaml
<DatePicker MinimumDate="01/01/2018"
            MaximumDate="12/31/2018"
            Date="06/21/2018" />
```

Se a `BindingContext` propriedade de `DatePicker` for definida como uma instância de um ViewModel que contém propriedades do tipo `DateTime` chamado `MinDate` , `MaxDate` e `SelectedDate` (por exemplo), você poderá instanciar o `DatePicker` seguinte:

```xaml
<DatePicker MinimumDate="{Binding MinDate}"
            MaximumDate="{Binding MaxDate}"
            Date="{Binding SelectedDate}" />
```

Neste exemplo, todas as três propriedades são inicializadas para as propriedades correspondentes no ViewModel. Como a `Date` propriedade tem um modo de associação de `TwoWay` , qualquer nova data que o usuário seleciona é refletida automaticamente no ViewModel.

Se o `DatePicker` não contiver uma associação em sua `Date` propriedade, um aplicativo deverá anexar um manipulador ao `DateSelected` evento para ser informado quando o usuário selecionar uma nova data.

Para obter informações sobre como definir propriedades de fonte, consulte [fontes](~/xamarin-forms/user-interface/text/fonts.md).

## <a name="datepicker-and-layout"></a>DatePicker e layout

É possível usar uma opção de layout horizontal irrestrito, como `Center` , `Start` ou `End` com `DatePicker` :

```xaml
<DatePicker ···
            HorizontalOptions="Center"
            ··· />
```

No entanto, isso não é recomendado. Dependendo da configuração da `Format` propriedade, as datas selecionadas podem exigir diferentes larguras de exibição. Por exemplo, a cadeia de caracteres de formato "D" faz com que as `DateTime` datas de exibição sejam exibidas em um formato longo e "quarta-feira, 12 de setembro de 2018" requer uma largura de exibição maior do que "sexta-feira, 4 de maio de 2018". Dependendo da plataforma, essa diferença pode fazer com que a `DateTime` exibição altere a largura no layout ou que a exibição seja truncada.

> [!TIP]
> É melhor usar a `HorizontalOptions` configuração padrão de `Fill` com `DatePicker` e não usar uma largura de `Auto` quando colocar `DatePicker` em uma `Grid` célula.

## <a name="datepicker-in-an-application"></a>DatePicker em um aplicativo

O exemplo [**DaysBetweenDates**](/samples/xamarin/xamarin-forms-samples/userinterface-datepicker) inclui dois `DatePicker` modos de exibição em sua página. Eles podem ser usados para selecionar duas datas e o programa calcula o número de dias entre essas datas. O programa não altera as configurações das `MinimumDate` Propriedades e `MaximumDate` , portanto, as duas datas devem estar entre 1900 e 2100.

Este é o arquivo XAML:

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DaysBetweenDates"
             x:Class="DaysBetweenDates.MainPage">
    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS" Value="0, 20, 0, 0" />
        </OnPlatform>
    </ContentPage.Padding>

    <StackLayout Margin="10">
        <Label Text="Days Between Dates"
               Style="{DynamicResource TitleStyle}"
               Margin="0, 20"
               HorizontalTextAlignment="Center" />

        <Label Text="Start Date:" />

        <DatePicker x:Name="startDatePicker"
                    Format="D"
                    Margin="30, 0, 0, 30"
                    DateSelected="OnDateSelected" />

        <Label Text="End Date:" />

        <DatePicker x:Name="endDatePicker"
                    MinimumDate="{Binding Source={x:Reference startDatePicker},
                                          Path=Date}"
                    Format="D"
                    Margin="30, 0, 0, 30"
                    DateSelected="OnDateSelected" />

        <StackLayout Orientation="Horizontal"
                     Margin="0, 0, 0, 30">
            <Label Text="Include both days in total: "
                   VerticalOptions="Center" />
            <Switch x:Name="includeSwitch"
                    Toggled="OnSwitchToggled" />
        </StackLayout>

        <Label x:Name="resultLabel"
               FontAttributes="Bold"
               HorizontalTextAlignment="Center" />

    </StackLayout>
</ContentPage>
```

Cada uma `DatePicker` é atribuída a uma `Format` propriedade de "D" para um formato de data longa. Observe também que o `endDatePicker` objeto tem uma associação que tem como destino sua `MinimumDate` propriedade. A origem da associação é a `Date` propriedade selecionada do `startDatePicker` objeto. Isso garante que a data de término seja sempre posterior ou igual à data de início. Além dos dois `DatePicker` objetos, a `Switch` é rotulada "incluir ambos os dias no total".

As duas `DatePicker` exibições têm manipuladores anexados ao `DateSelected` evento e o `Switch` tem um manipulador anexado ao seu `Toggled` evento. Esses manipuladores de eventos estão no arquivo code-behind e disparam um novo cálculo dos dias entre as duas datas:

```csharp
public partial class MainPage : ContentPage
{
    public MainPage()
    {
        InitializeComponent();
    }

    void OnDateSelected(object sender, DateChangedEventArgs args)
    {
        Recalculate();
    }

    void OnSwitchToggled(object sender, ToggledEventArgs args)
    {
        Recalculate();
    }

    void Recalculate()
    {
        TimeSpan timeSpan = endDatePicker.Date - startDatePicker.Date +
            (includeSwitch.IsToggled ? TimeSpan.FromDays(1) : TimeSpan.Zero);

        resultLabel.Text = String.Format("{0} day{1} between dates",
                                            timeSpan.Days, timeSpan.Days == 1 ? "" : "s");
    }
}
```

Quando o exemplo é executado pela primeira vez, ambos os `DatePicker` modos de exibição são inicializados para a data de hoje. A captura de tela a seguir mostra o programa em execução no iOS e Android:

[![Dias entre as datas de início](datepicker-images/DaysBetweenDatesStart.png "Dias entre as datas de início")](datepicker-images/DaysBetweenDatesStart-Large.png#lightbox "Dias entre as datas de início")

Tocar em qualquer um dos `DatePicker` monitores invoca o seletor de data da plataforma. As plataformas implementam esse seletor de data de maneiras muito diferentes, mas cada abordagem é familiar para os usuários dessa plataforma:

[![Dias entre as datas Select](datepicker-images/DaysBetweenDatesSelect.png "Dias entre as datas Select")](datepicker-images/DaysBetweenDatesSelect-Large.png#lightbox "Dias entre as datas Select")

> [!TIP]
> No Android, a `DatePicker` caixa de diálogo pode ser personalizada substituindo o `CreateDatePickerDialog` método em um renderizador personalizado. Isso permite, por exemplo, botões adicionais a serem adicionados à caixa de diálogo.

Depois que duas datas forem selecionadas, o aplicativo exibirá o número de dias entre essas datas:

[![Dias entre os resultados de datas](datepicker-images/DaysBetweenDatesResult.png "Dias entre os resultados de datas")](datepicker-images/DaysBetweenDatesResult-Large.png#lightbox "Dias entre os resultados de datas")

## <a name="related-links"></a>Links relacionados

- [Exemplo de DaysBetweenDates](/samples/xamarin/xamarin-forms-samples/userinterface-datepicker)
- [API DatePicker](xref:Xamarin.Forms.DatePicker)