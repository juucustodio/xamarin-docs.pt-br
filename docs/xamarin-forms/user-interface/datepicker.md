---
title: DatePicker Xamarin. Forms
description: O DatePicker é um modo de exibição Xamarin. Forms que permite ao usuário selecionar uma data. Este artigo explica como consumir um DatePicker em um aplicativo Xamarin. Forms.
ms.prod: xamarin
ms.assetid: 68E8EF8A-42E7-4939-8ABE-64D060E609D9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/04/2018
ms.openlocfilehash: 15d4159d89a463c0335d9c91b24b55151c91de8c
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72695909"
---
# <a name="xamarinforms-datepicker"></a>DatePicker Xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-datepicker)

_Uma exibição do Xamarin. Forms que permite ao usuário selecionar uma data._

O [`DatePicker`](xref:Xamarin.Forms.DatePicker) Xamarin. Forms invoca o controle de seletor de data da plataforma e permite que o usuário selecione uma data. `DatePicker` define oito Propriedades:

- [`MinimumDate`](xref:Xamarin.Forms.DatePicker.MinimumDate) do tipo [`DateTime`](xref:System.DateTime), que usa como padrão o primeiro dia do ano 1900.
- [`MaximumDate`](xref:Xamarin.Forms.DatePicker.MaximumDate) do tipo `DateTime`, que usa como padrão o último dia do ano 2100.
- [`Date`](xref:Xamarin.Forms.DatePicker.Date) do tipo `DateTime`, a data selecionada, que usa como padrão o valor [`DateTime.Today`](xref:System.DateTime.Today).
- [`Format`](xref:Xamarin.Forms.DatePicker.Format) do tipo `string`, uma cadeia de caracteres de formatação .NET [padrão](/dotnet/standard/base-types/standard-date-and-time-format-strings/) ou [personalizada](/dotnet/standard/base-types/custom-date-and-time-format-strings/) , cujo padrão é "D", o padrão de data por extenso.
- [`TextColor`](xref:Xamarin.Forms.DatePicker.TextColor) do tipo [`Color`](xref:Xamarin.Forms.Color), a cor usada para exibir a data selecionada, cujo padrão é [`Color.Default`](xref:Xamarin.Forms.Color.Default).
- [`FontAttributes`](xref:Xamarin.Forms.DatePicker.FontAttributes) do tipo [`FontAttributes`](xref:Xamarin.Forms.FontAttributes), cujo padrão é [`FontAtributes.None`](xref:Xamarin.Forms.FontAttributes.None).
- [`FontFamily`](xref:Xamarin.Forms.DatePicker.FontFamily) do tipo `string`, cujo padrão é `null`.
- [`FontSize`](xref:Xamarin.Forms.DatePicker.FontSize) do tipo `double`, que usa como padrão-1,0.
- `CharacterSpacing`, do tipo `double`, é o espaçamento entre os caracteres do texto do `DatePicker`.

O `DatePicker` aciona um evento de [`DateSelected`](xref:Xamarin.Forms.DatePicker.DateSelected) quando o usuário seleciona uma data.

> [!WARNING]
> Ao definir `MinimumDate` e `MaximumDate`, certifique-se de que `MinimumDate` seja sempre menor ou igual a `MaximumDate`. Caso contrário, `DatePicker` gerará uma exceção.

Internamente, a `DatePicker` garante que `Date` esteja entre `MinimumDate` e `MaximumDate`, inclusive. Se `MinimumDate` ou `MaximumDate` estiver definido para que `Date` não esteja entre eles, `DatePicker` ajustará o valor de `Date`.

Todas as oito propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que elas podem ser estilizadas e as propriedades podem ser destinos de associações de dados. A propriedade `Date` tem um modo de associação padrão de [`BindingMode.TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay), o que significa que ele pode ser um destino de uma associação de dados em um aplicativo que usa a arquitetura [MVVM (Model-View-ViewModel)](~/xamarin-forms/enterprise-application-patterns/mvvm.md) .

## <a name="initializing-the-datetime-properties"></a>Inicializando as propriedades DateTime

No código, você pode inicializar as propriedades `MinimumDate`, `MaximumDate` e `Date` para os valores do tipo `DateTime`:

```csharp
DatePicker datePicker = new DatePicker
{
    MinimumDate = new DateTime(2018, 1, 1),
    MaximumDate = new DateTime(2018, 12, 31),
    Date = new DateTime(2018, 6, 21)
};
```

Quando um valor de `DateTime` é especificado em XAML, o analisador XAML usa o método `DateTime.Parse` com um argumento `CultureInfo.InvariantCulture` para converter a cadeia de caracteres em um valor de `DateTime`. As datas devem ser especificadas em um formato preciso: meses de dois dígitos, dias de dois dígitos e anos de quatro dígitos separados por barras:

```xaml
<DatePicker MinimumDate="01/01/2018"
            MaximumDate="12/31/2018"
            Date="06/21/2018" />
```

Se a propriedade `BindingContext` de `DatePicker` for definida como uma instância de um ViewModel que contém propriedades do tipo `DateTime` chamado `MinDate`, `MaxDate` e `SelectedDate` (por exemplo), você poderá instanciar o `DatePicker` como este :

```xaml
<DatePicker MinimumDate="{Binding MinDate}"
            MaximumDate="{Binding MaxDate}"
            Date="{Binding SelectedDate}" />
```

Neste exemplo, todas as três propriedades são inicializadas para as propriedades correspondentes no ViewModel. Como a propriedade `Date` tem um modo de associação de `TwoWay`, qualquer nova data que o usuário seleciona é refletida automaticamente no ViewModel.

Se o `DatePicker` não contiver uma associação em sua propriedade `Date`, um aplicativo deverá anexar um manipulador ao evento `DateSelected` para ser informado quando o usuário selecionar uma nova data.

Para obter informações sobre como definir propriedades de fonte, consulte [fontes](~/xamarin-forms/user-interface/text/fonts.md).

## <a name="datepicker-and-layout"></a>DatePicker e layout

É possível usar uma opção de layout horizontal irrestrito, como `Center`, `Start` ou `End` com `DatePicker`:

```xaml
<DatePicker ···
            HorizontalOptions="Center"
            ··· />
```

No entanto, isso não é recomendado. Dependendo da configuração da propriedade `Format`, as datas selecionadas podem exigir diferentes larguras de exibição. Por exemplo, a cadeia de caracteres de formato "D" faz `DateTime` exibir datas em um formato longo e "quarta-feira, 12 de setembro de 2018" requer uma largura de exibição maior que "sexta-feira, 4 de maio de 2018". Dependendo da plataforma, essa diferença pode fazer com que a exibição de `DateTime` altere a largura no layout ou que a exibição seja truncada.

> [!TIP]
> É melhor usar a configuração de `HorizontalOptions` padrão de `Fill` com `DatePicker`, e não usar uma largura de `Auto` ao colocar `DatePicker` em uma célula `Grid`.

## <a name="datepicker-in-an-application"></a>DatePicker em um aplicativo

O exemplo [**DaysBetweenDates**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-datepicker) inclui duas exibições de `DatePicker` em sua página. Eles podem ser usados para selecionar duas datas e o programa calcula o número de dias entre essas datas. O programa não altera as configurações das propriedades `MinimumDate` e `MaximumDate`, portanto, as duas datas devem estar entre 1900 e 2100.

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

Cada `DatePicker` é atribuída a uma propriedade de `Format` de "D" para um formato de data por extenso. Observe também que o objeto `endDatePicker` tem uma associação que tem como destino sua propriedade `MinimumDate`. A origem da associação é a propriedade `Date` selecionada do objeto `startDatePicker`. Isso garante que a data de término seja sempre posterior ou igual à data de início. Além dos dois objetos `DatePicker`, um `Switch` é rotulado "incluir ambos os dias no total".

Os dois `DatePicker` modos de exibição têm manipuladores anexados ao evento `DateSelected`, e o `Switch` tem um manipulador anexado ao seu evento de `Toggled`. Esses manipuladores de eventos estão no arquivo code-behind e disparam um novo cálculo dos dias entre as duas datas:

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

Quando o exemplo é executado pela primeira vez, ambos os modos de exibição `DatePicker` são inicializados para a data de hoje. A captura de tela a seguir mostra o programa em execução no iOS, no Android e no Plataforma Universal do Windows:

[![Dias entre as datas de início](datepicker-images/DaysBetweenDatesStart.png "Dias entre as datas de início")](datepicker-images/DaysBetweenDatesStart-Large.png#lightbox "Dias entre as datas de início")

Tocar em uma das `DatePicker` exibe invoca o seletor de data da plataforma. As plataformas implementam esse seletor de data de maneiras muito diferentes, mas cada abordagem é familiar para os usuários dessa plataforma:

[![Dias entre as datas Select](datepicker-images/DaysBetweenDatesSelect.png "Dias entre as datas Select")](datepicker-images/DaysBetweenDatesSelect-Large.png#lightbox "Dias entre as datas Select")

> [!TIP]
> No Android, a caixa de diálogo `DatePicker` pode ser personalizada substituindo o método `CreateDatePickerDialog` em um renderizador personalizado. Isso permite, por exemplo, botões adicionais a serem adicionados à caixa de diálogo.

Depois que duas datas forem selecionadas, o aplicativo exibirá o número de dias entre essas datas:

[![Dias entre os resultados de datas](datepicker-images/DaysBetweenDatesResult.png "Dias entre os resultados de datas")](datepicker-images/DaysBetweenDatesResult-Large.png#lightbox "Dias entre os resultados de datas")

## <a name="related-links"></a>Links relacionados

- [Exemplo de DaysBetweenDates](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-datepicker)
- [API DatePicker](xref:Xamarin.Forms.DatePicker)
