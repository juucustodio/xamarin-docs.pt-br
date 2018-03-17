---
title: Usando o selecionador de data
description: "Um modo de exibição xamarin. Forms permite que o usuário selecione uma data"
ms.topic: article
ms.prod: xamarin
ms.assetid: 68E8EF8A-42E7-4939-8ABE-64D060E609D9
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 03/12/2018
ms.openlocfilehash: 5c214fe4124b900ea63399b97084d1ce0e181d4a
ms.sourcegitcommit: 5fc1c4d17cd9c755604092cf7ff038a6358f8646
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/17/2018
---
# <a name="using-datepicker"></a>Usando o selecionador de data

_Um modo de exibição xamarin. Forms permite que o usuário selecione uma data_

O xamarin. Forms [ `DatePicker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DatePicker/) invoca o controle de seletor de data da plataforma e permite que o usuário selecione uma data. `DatePicker` define cinco propriedades:

- [`MinimumDate`](https://developer.xamarin.com/api/property/Xamarin.Forms.DatePicker.MinimumDate/) tipo [ `DateTime` ](https://developer.xamarin.com/api/type/System.DateTime/), que assume como padrão o primeiro dia do ano de 1900.
- [`MaximumDate`](https://developer.xamarin.com/api/property/Xamarin.Forms.DatePicker.MaximumDate/) tipo `DateTime`, o padrão é o último dia do ano 2100.
- [`Date`](https://developer.xamarin.com/api/property/Xamarin.Forms.DatePicker.Date/) tipo `DateTime`, a data selecionada, o valor padrão é [ `DateTime.Today` ](https://developer.xamarin.com/api/property/System.DateTime.Today/).
- [`Format`](https://developer.xamarin.com/api/property/Xamarin.Forms.DatePicker.Format/) tipo `string`, um [padrão](/dotnet/standard/base-types/standard-date-and-time-format-strings/) ou [personalizado](/dotnet/standard/base-types/custom-date-and-time-format-strings/) .NET formatação de cadeia de caracteres, cujo padrão é "D", o long data padrão.
- [`TextColor`](https://developer.xamarin.com/api/property/Xamarin.Forms.DatePicker.TextColor/) tipo [ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/), a cor usada para exibir a data selecionada, cujo padrão é [ `Color.Default` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.Default/).

O `DatePicker` dispara uma [ `DateSelected` ](https://developer.xamarin.com/api/event/Xamarin.Forms.DatePicker.DateSelected/) evento quando o usuário seleciona uma data.

> [!WARNING]
> Ao definir `MinimumDate` e `MaximumDate`, certifique-se de que `MinimumDate` sempre é menor ou igual a `MaximumDate`. Caso contrário, `DatePicker` gerará uma exceção.

Internamente, o `DatePicker` garante que `Date` entre `MinimumDate` e `MaximumDate`, inclusive. Se `MinimumDate` ou `MaximumDate` está definido para que `Date` não está entre eles, `DatePicker` ajustará o valor de `Date`.

Todas as cinco propriedades têm o respaldo [ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/) objetos, o que significa que eles podem ser denominados, e as propriedades podem ser alvos de associações de dados. O `Date` propriedade tem um modo de associação padrão de [ `BindingMode.TwoWay` ](https://developer.xamarin.com/api/field/Xamarin.Forms.BindingMode.TwoWay/), que significa que ele pode ser um destino de uma associação de dados em um aplicativo que usa o [Model-View-ViewModel (MVVM)](~/xamarin-forms/enterprise-application-patterns/mvvm.md) arquitetura.

## <a name="initializing-the-datetime-properties"></a>Inicializando as propriedades de data e hora

No código, você pode inicializar o `MinimumDate`, `MaximumDate`, e `Date` propriedades para valores do tipo `DateTime`:

```csharp
DatePicker datePicker = new DatePicker
{
    MinimumDate = new DateTime(2018, 1, 1),
    MaximumDate = new DateTime(2018, 12, 31),
    Date = new DateTime(2018, 6, 21)
};
```

Quando um `DateTime` valor é especificado em XAML, o analisador XAML usa o `DateTime.Parse` método com um `CultureInfo.InvariantCulture` argumento para converter a cadeia de caracteres para um `DateTime` valor. As datas devem ser especificadas em um formato preciso: dois dígitos meses, dias de dois dígitos e anos de quatro dígitos, separados por barras:

```xaml
<DatePicker MinimumDate="01/01/2018"
            MaximumDate="12/31/2018"
            Date="06/21/2018" />
```

Se o `BindingContext` propriedade de `DatePicker` é definido como uma instância de um ViewModel que contém as propriedades do tipo `DateTime` chamado `MinDate`, `MaxDate`, e `SelectedDate` (por exemplo), você pode instanciar o `DatePicker` assim :

```xaml
<DatePicker MinimumDate="{Binding MinDate}"
            MaximumDate="{Binding MaxDate}"
            Date="{Binding SelectedDate}" />
```

Neste exemplo, todas as três propriedades são inicializadas para as propriedades correspondentes no ViewModel. Porque o `Date` propriedade possui um modo de associação do `TwoWay`, qualquer nova data em que o usuário seleciona automaticamente é refletido no ViewModel.

Se o `DatePicker` não tem uma associação no seu `Date` propriedade, um aplicativo deve anexar um manipulador para o `DateSelected` evento para ser informado quando o usuário seleciona uma nova data.

## <a name="datepicker-and-layout"></a>DatePicker e layout

É possível usar uma opção de layout horizontal irrestrita como `Center`, `Start`, ou `End` com `DatePicker`:

```xaml
<DatePicker ··· 
            HorizontalOptions="Center" 
            ··· />
```

No entanto, isso não é recomendado. Dependendo da configuração do `Format` propriedade selecionada datas podem exigir larguras de exibição diferente. Por exemplo, faz com que a cadeia de caracteres de formato "D" `DateTime` exibir datas em um formato longo e "Quarta-feira, 12 de setembro de 2018" requer uma maior largura de exibição que "Sexta-feira, 4 de maio de 2018." Dependendo da plataforma, essa diferença pode causar o `DateTime` exibição para alterar a largura de layout, ou para a exibição a ser truncado.

> [!TIP]
> É melhor usar o padrão `HorizontalOptions` de `Fill` com `DatePicker`e não para usar uma largura de `Auto` ao colocar `DatePicker` em um `Grid` célula.

## <a name="datepicker-in-an-application"></a>DatePicker em um aplicativo

O [ **DaysBetweenDates** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/DatePicker) exemplo inclui dois `DatePicker` exibições em sua página. Eles podem ser usados para selecionar duas datas, e o programa calcula o número de dias entre essas datas. O programa não altera as configurações de `MinimumDate` e `MaximumDate` propriedades, para que as duas datas entre 1900 e 2100.

Aqui está o arquivo XAML:

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

Cada `DatePicker` é atribuído um `Format` propriedade de "D" para um formato de data por extenso. Observe também que o `endDatePicker` objeto possui uma associação que tem como alvo seus `MinimumDate` propriedade. A fonte da associação é selecionado `Date` propriedade o `startDatePicker` objeto. Isso garante que a data de término é sempre posterior ou igual à data de início. Além dos dois `DatePicker` objetos, um `Switch` é rotulado como "Incluir os dois dias no total". 

Os dois `DatePicker` exibições têm manipuladores anexados para o `DateSelected` evento e o `Switch` tem um manipulador anexado ao seu `Toggled` eventos. Esses manipuladores de eventos no arquivo code-behind e disparam um novo cálculo de dias entre as duas datas:

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

Quando o exemplo é primeiro executado, ambos `DatePicker` modos de exibição são inicializados a data de hoje. Captura de tela a seguir mostra o programa em execução no iOS, Android e a plataforma Universal do Windows:

[![Dias entre as datas de início](datepicker-images/DaysBetweenDatesStart.png "dias entre as datas de início")](datepicker-images/DaysBetweenDatesStart-Large.png#lightbox "dias entre as datas de início")

Ao tocar em qualquer uma da `DatePicker` exibe invoca o seletor de data de plataforma. As três plataformas implementam este seletor de data de maneiras muito diferentes, mas cada abordagem é familiar aos usuários de plataforma:

[![Selecione de dias entre datas](datepicker-images/DaysBetweenDatesSelect.png "Selecione dias entre datas")](datepicker-images/DaysBetweenDatesSelect-Large.png#lightbox "Selecione dias entre datas")

Depois de duas datas forem selecionadas, o aplicativo exibe o número de dias entre essas datas:

[![Dias entre datas resultado](datepicker-images/DaysBetweenDatesResult.png "dias entre datas resultado")](datepicker-images/DaysBetweenDatesResult-Large.png#lightbox "dias entre as datas de resultados")

## <a name="related-links"></a>Links relacionados

- [Exemplo de DaysBetweenDates](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/DatePicker)
- [API de DatePicker](https://developer.xamarin.com/api/type/Xamarin.Forms.DatePicker/)
