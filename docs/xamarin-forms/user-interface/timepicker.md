---
title: Xamarin. Forms TimePicker
description: O TimPicker é um modo de exibição do xamarin. Forms que permite que o usuário selecione uma hora. Este artigo explica como consumir um TimePicker em um aplicativo xamarin. Forms.
ms.prod: xamarin
ms.assetid: 2E99FB23-B82D-4EB4-AFB3-5002E736E7B2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/16/2018
ms.openlocfilehash: 9a534c39d514fec9a0de4bc810f33c972453baa2
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50111463"
---
# <a name="xamarinforms-timepicker"></a>Xamarin. Forms TimePicker

_Uma exibição do xamarin. Forms que permite que o usuário selecione uma hora._

O xamarin. Forms [ `TimePicker` ](xref:Xamarin.Forms.TimePicker) invoca o controle de seletor de tempo da plataforma e permite que o usuário selecione uma hora. `TimePicker` Define as propriedades a seguir:

- [`Time`](xref:Xamarin.Forms.TimePicker.Time) do tipo `TimeSpan`, o tempo selecionado, que assume como padrão um `TimeSpan` igual a 0. O `TimeSpan` tipo indica uma duração de tempo desde a meia-noite.
- [`Format`](xref:Xamarin.Forms.TimePicker.Format) do tipo `string`, um [padrão](/dotnet/standard/base-types/standard-date-and-time-format-strings/) ou [personalizado](/dotnet/standard/base-types/custom-date-and-time-format-strings/) .NET formatação de cadeia de caracteres, cujo padrão é "t", o padrão de hora abreviada.
- [`TextColor`](xref:Xamarin.Forms.TimePicker.TextColor) do tipo [ `Color` ](xref:Xamarin.Forms.Color), a cor usada para exibir o tempo selecionado, cujo padrão é [ `Color.Default` ](xref:Xamarin.Forms.Color.Default).
- [`FontAttributes`](xref:Xamarin.Forms.TimePicker.FontAttributes) do tipo [ `FontAttributes` ](xref:Xamarin.Forms.FontAttributes), cujo padrão é [ `FontAtributes.None` ](xref:Xamarin.Forms.FontAttributes.None).
- [`FontFamily`](xref:Xamarin.Forms.TimePicker.FontFamily) do tipo `string`, cujo padrão é `null`.
- [`FontSize`](xref:Xamarin.Forms.TimePicker.FontSize) do tipo `double`, cujo padrão é de -1,0.

Todas essas propriedades têm o respaldo [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que eles podem ser estilizados e as propriedades podem ser alvos de vinculações de dados. O [ `Time` ](xref:Xamarin.Forms.TimePicker.Time) propriedade tem um modo de associação padrão de [ `BindingMode.TwoWay` ](xref:Xamarin.Forms.BindingMode.TwoWay), que significa que ele pode ser um destino de associação de dados em um aplicativo que usa o [ Model-View-ViewModel (MVVM)](~/xamarin-forms/enterprise-application-patterns/mvvm.md) arquitetura.

O [ `TimePicker` ](xref:Xamarin.Forms.TimePicker) não inclui um evento para indicar um novo selecionado [ `Time` ](xref:Xamarin.Forms.TimePicker.Time) valor. Se você precisar ser notificado sobre isso, você pode adicionar um manipulador para o [ `PropertyChanged` ](xref:Xamarin.Forms.BindableObject.PropertyChanged) eventos.

## <a name="initializing-the-time-property"></a>Inicializando a propriedade de tempo

No código, você pode inicializar o [ `Time` ](xref:Xamarin.Forms.TimePicker.Time) propriedade para um valor do tipo `TimeSpan`:

```csharp
TimePicker timePicker = new TimePicker
{
  Time = new TimeSpan(4, 15, 26) // Time set to "04:15:26"
};
```

Quando o [ `Time` ](xref:Xamarin.Forms.TimePicker.Time) propriedade é especificada no XAML, o valor é convertido em um `TimeSpan` e validadas para garantir que o número de milissegundos é maior que ou igual a 0 e o número de horas é menor que 24. Os componentes de tempo devem ser separados por dois-pontos:

```xaml
<TimePicker Time="4:15:26" />
```

Se o [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) propriedade do [ `TimePicker` ](xref:Xamarin.Forms.TimePicker) é definido como uma instância de um ViewModel que contém uma propriedade de tipo `TimeSpan` chamado `SelectedTime` (por exemplo), você pode criar uma instância o `TimePicker` semelhante a esta:

```xaml
<TimePicker Time="{Binding SelectedTime}" />
```

Neste exemplo, o [ `Time` ](xref:Xamarin.Forms.TimePicker.Time) propriedade é inicializada para o `SelectedTime` propriedade no ViewModel. Porque o `Time` propriedade tem um modo de associação da [ `TwoWay` ](xref:Xamarin.Forms.BindingMode.TwoWay), qualquer novo tempo em que o usuário selecionar será propagado automaticamente para o ViewModel.

Se o [ `TimePicker` ](xref:Xamarin.Forms.TimePicker) não contém uma associação em seu [ `Time` ](xref:Xamarin.Forms.TimePicker.Time) propriedade, um aplicativo deve anexar um manipulador para o [ `PropertyChanged` ](xref:Xamarin.Forms.BindableObject.PropertyChanged) eventos para ser informado quando o usuário seleciona um novo tempo.

Para obter informações sobre como definir propriedades de fonte, consulte [fontes](~/xamarin-forms/user-interface/text/fonts.md).

## <a name="timepicker-and-layout"></a>TimePicker e layout

É possível usar uma opção de layout horizontal irrestrito, como `Center`, `Start`, ou `End` com [ `TimePicker` ](xref:Xamarin.Forms.TimePicker):

```xaml
<TimePicker ···
            HorizontalOptions="Center"
            ··· />
```

No entanto, isso não é recomendado. Dependendo da configuração do [ `Format` ](xref:Xamarin.Forms.TimePicker.Format) propriedade, selecionada vezes podem exigir que as larguras de exibição diferentes. Por exemplo, a cadeia de caracteres de formato "T" faz com que o [ `TimePicker` ](xref:Xamarin.Forms.TimePicker) exibir para exibir os tempos de em um formato longo e ": 15:26 4AM" requer uma maior largura de exibição do formato de hora abreviada ("t") da ": 15 4AM". Dependendo da plataforma, essa diferença pode causar a `TimePicker` exibição para alterar a largura de layout, ou para a exibição a ser truncado.

> [!TIP]
> É melhor usar o padrão `HorizontalOptions` configuração do `Fill` com [ `TimePicker` ](xref:Xamarin.Forms.TimePicker)e não deve usar uma largura de `Auto` ao colocar `TimePicker` em um `Grid` célula.

## <a name="timepicker-in-an-application"></a>TimePicker em um aplicativo

O [ **SetTimer** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/TimePicker/) exemplo inclui [ `TimePicker` ](xref:Xamarin.Forms.TimePicker), [ `Entry` ](xref:Xamarin.Forms.Entry), e [ `Switch` ](xref:Xamarin.Forms.Switch) modos de exibição em sua página. O `TimePicker` pode ser usado para selecionar uma hora, e quando de tempo que ocorre uma caixa de diálogo de alerta é exibida que lembra o usuário do texto na `Entry`, fornecido o `Switch` é ativado. Aqui está o arquivo XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:SetTimer"
             x:Class="SetTimer.MainPage">
    <StackLayout>
        ...
        <Entry x:Name="_entry"
               Placeholder="Enter event to be reminded of" />
        <Label Text="Select the time below to be reminded at." />
        <TimePicker x:Name="_timePicker"
                    Time="11:00:00"
                    Format="T"
                    PropertyChanged="OnTimePickerPropertyChanged" />
        <StackLayout Orientation="Horizontal">
            <Label Text="Enable timer:" />
            <Switch x:Name="_switch"
                    HorizontalOptions="EndAndExpand"
                    Toggled="OnSwitchToggled" />
        </StackLayout>
    </StackLayout>
</ContentPage>
```

O [ `Entry` ](xref:Xamarin.Forms.Entry) permite que você insira o texto de lembrete que será exibido quando ocorre o tempo selecionado. O [ `TimePicker` ](xref:Xamarin.Forms.TimePicker) é atribuído um [ `Format` ](xref:Xamarin.Forms.TimePicker.Format) propriedade de "T" para o formato de hora longa. Ele tem um manipulador de eventos anexado à [ `PropertyChanged` ](xref:Xamarin.Forms.BindableObject.PropertyChanged) eventos e o [ `Switch` ](xref:Xamarin.Forms.Switch) tem um manipulador anexado ao seu [ `Toggled` ](xref:Xamarin.Forms.Switch.Toggled) eventos. Esses manipuladores de eventos estão no arquivo code-behind e chamada o `SetTriggerTime` método:

```csharp
public partial class MainPage : ContentPage
{
    DateTime _triggerTime;

    public MainPage()
    {
        InitializeComponent();

        Device.StartTimer(TimeSpan.FromSeconds(1), OnTimerTick);
    }

    bool OnTimerTick()
    {
        if (_switch.IsToggled && DateTime.Now >= _triggerTime)
        {
            _switch.IsToggled = false;
            DisplayAlert("Timer Alert", "The '" + _entry.Text + "' timer has elapsed", "OK");
        }
        return true;
    }

    void OnTimePickerPropertyChanged(object sender, PropertyChangedEventArgs args)
    {
        if (args.PropertyName == "Time")
        {
            SetTriggerTime();
        }
    }

    void OnSwitchToggled(object sender, ToggledEventArgs args)
    {
        SetTriggerTime();
    }

    void SetTriggerTime()
    {
        if (_switch.IsToggled)
        {
            _triggerTime = DateTime.Today + _timePicker.Time;
            if (_triggerTime < DateTime.Now)
            {
                _triggerTime += TimeSpan.FromDays(1);
            }
        }
    }
}
```

O `SetTriggerTime` método calcula um tempo de timer com base no `DateTime.Today` o valor da propriedade e o `TimeSpan` valor retornado da [ `TimePicker` ](xref:Xamarin.Forms.TimePicker). Isso é necessário porque o `DateTime.Today` propriedade retorna um `DateTime` que indica a data atual, mas com um tempo de meia-noite. Se já tiver passado tempo o temporizador hoje em dia, em seguida, supõe-se para ser amanhã.

Os tiques do temporizador cada segundo, executando o `OnTimerTick` método que verifica se o [ `Switch` ](xref:Xamarin.Forms.Switch) é sobre como e se a hora atual é maior que ou igual ao tempo de temporizador. Quando ocorre o tempo de timer, o [ `DisplayAlert` ](xref:Xamarin.Forms.Page.DisplayAlert*) método apresenta uma caixa de diálogo de alerta para o usuário como um lembrete.

Quando o exemplo é executado pela primeira vez, o [ `TimePicker` ](xref:Xamarin.Forms.TimePicker) exibição é inicializada como às 11h. Tocar o `TimePicker` invoca o seletor de tempo de plataforma. As três plataformas implementam o seletor de hora em duas maneiras diferentes, mas cada abordagem é familiar aos usuários da plataforma:

[![Selecione hora](timepicker-images/timepicker-open.png "selecione hora")](timepicker-images/timepicker-open-large.png#lightbox "selecione hora")

> [!TIP]
> No Android, o [ `TimePicker` ](xref:Xamarin.Forms.TimePicker) caixa de diálogo pode ser personalizada substituindo o `CreateTimePickerDialog` método em um renderizador personalizado. Isso permite, por exemplo, botões adicionais a serem adicionados à caixa de diálogo.

Depois de selecionar uma hora, o tempo selecionado é exibido na [ `TimePicker` ](xref:Xamarin.Forms.TimePicker):

[![Tempo selecionado](timepicker-images/timepicker-selected.png "tempo selecionado")](timepicker-images/timepicker-selected-large.png#lightbox "tempo selecionado")

Desde que o [ `Switch` ](xref:Xamarin.Forms.Switch) seja alternado para a posição ligada, o aplicativo exibe uma caixa de diálogo alerta lembrar o usuário do texto no [ `Entry` ](xref:Xamarin.Forms.Entry) quando ocorre o tempo selecionado:

[![Pop-up de temporizador](timepicker-images/timer-test.png "pop-up do temporizador")](timepicker-images/timer-test-large.png#lightbox "pop-up do Timer")

Assim que a caixa de diálogo de alerta é exibida, o [ `Switch` ](xref:Xamarin.Forms.Switch) seja alternado para a posição off.

## <a name="related-links"></a>Links relacionados

- [Exemplo de SetTimer](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/TimePicker/)
- [TimePicker API](xref:Xamarin.Forms.TimePicker)
