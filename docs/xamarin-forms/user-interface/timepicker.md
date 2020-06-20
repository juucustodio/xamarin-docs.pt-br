---
title: Xamarin.FormsTimePicker
description: O timeseparar é uma Xamarin.Forms exibição que permite ao usuário selecionar uma hora. Este artigo explica como consumir um seletor de em um Xamarin.Forms aplicativo.
ms.prod: xamarin
ms.assetid: 2E99FB23-B82D-4EB4-AFB3-5002E736E7B2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/16/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 7c2e6b97f2207ebb6543fb6a720cd430331f989b
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84138223"
---
# <a name="xamarinforms-timepicker"></a>Xamarin.FormsTimePicker

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-timepicker)

_Uma Xamarin.Forms exibição que permite ao usuário selecionar uma hora._

O Xamarin.Forms [`TimePicker`](xref:Xamarin.Forms.TimePicker) invoca o controle de seletor de tempo da plataforma e permite que o usuário selecione uma hora. `TimePicker` define as propriedades a seguir:

- [`Time`](xref:Xamarin.Forms.TimePicker.Time)do tipo `TimeSpan` , a hora selecionada, cujo padrão é `TimeSpan` 0. O `TimeSpan` tipo indica uma duração de tempo desde a meia-noite.
- [`Format`](xref:Xamarin.Forms.TimePicker.Format)do tipo `string` , uma cadeia de caracteres de formatação .NET [padrão](/dotnet/standard/base-types/standard-date-and-time-format-strings/) ou [personalizada](/dotnet/standard/base-types/custom-date-and-time-format-strings/) , cujo padrão é "t", o padrão de tempo curto.
- [`TextColor`](xref:Xamarin.Forms.TimePicker.TextColor)do tipo [`Color`](xref:Xamarin.Forms.Color) , a cor usada para exibir a hora selecionada, para a qual o padrão será [`Color.Default`](xref:Xamarin.Forms.Color.Default) .
- [`FontAttributes`](xref:Xamarin.Forms.TimePicker.FontAttributes)do tipo [`FontAttributes`](xref:Xamarin.Forms.FontAttributes) , que usa como padrão [`FontAtributes.None`](xref:Xamarin.Forms.FontAttributes.None) .
- [`FontFamily`](xref:Xamarin.Forms.TimePicker.FontFamily)do tipo `string` , que usa como padrão `null` .
- [`FontSize`](xref:Xamarin.Forms.TimePicker.FontSize)do tipo `double` , que usa como padrão-1,0.
- `CharacterSpacing`, do tipo `double` , é o espaçamento entre os caracteres do `TimePicker` texto.

Todas essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que elas podem ser estilizadas e as propriedades podem ser destinos de associações de dados. A [`Time`](xref:Xamarin.Forms.TimePicker.Time) propriedade tem um modo de associação padrão de [`BindingMode.TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) , o que significa que ele pode ser um destino de uma associação de dados em um aplicativo que usa a arquitetura [MVVM (Model-View-ViewModel)](~/xamarin-forms/enterprise-application-patterns/mvvm.md) .

O [`TimePicker`](xref:Xamarin.Forms.TimePicker) não inclui um evento para indicar um novo [`Time`](xref:Xamarin.Forms.TimePicker.Time) valor selecionado. Se precisar ser notificado disso, você poderá adicionar um manipulador para o [`PropertyChanged`](xref:Xamarin.Forms.BindableObject.PropertyChanged) evento.

## <a name="initializing-the-time-property"></a>Inicializando a propriedade time

No código, você pode inicializar a [`Time`](xref:Xamarin.Forms.TimePicker.Time) propriedade para um valor do tipo `TimeSpan` :

```csharp
TimePicker timePicker = new TimePicker
{
  Time = new TimeSpan(4, 15, 26) // Time set to "04:15:26"
};
```

Quando a [`Time`](xref:Xamarin.Forms.TimePicker.Time) propriedade é especificada em XAML, o valor é convertido em a `TimeSpan` e validado para garantir que o número de milissegundos seja maior ou igual a 0, e que o número de horas seja menor que 24. Os componentes de tempo devem ser separados por dois-pontos:

```xaml
<TimePicker Time="4:15:26" />
```

Se a [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) propriedade de [`TimePicker`](xref:Xamarin.Forms.TimePicker) for definida como uma instância de um ViewModel que contém uma propriedade do tipo `TimeSpan` chamado `SelectedTime` (por exemplo,), você poderá instanciar o `TimePicker` seguinte:

```xaml
<TimePicker Time="{Binding SelectedTime}" />
```

Neste exemplo, a [`Time`](xref:Xamarin.Forms.TimePicker.Time) propriedade é inicializada para a `SelectedTime` propriedade no ViewModel. Como a `Time` propriedade tem um modo de associação de [`TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) , qualquer novo horário que o usuário seleciona é automaticamente propagado para o ViewModel.

Se o [`TimePicker`](xref:Xamarin.Forms.TimePicker) não contiver uma associação em sua [`Time`](xref:Xamarin.Forms.TimePicker.Time) propriedade, um aplicativo deverá anexar um manipulador ao [`PropertyChanged`](xref:Xamarin.Forms.BindableObject.PropertyChanged) evento para ser informado quando o usuário selecionar uma nova hora.

Para obter informações sobre como definir propriedades de fonte, consulte [fontes](~/xamarin-forms/user-interface/text/fonts.md).

## <a name="timepicker-and-layout"></a>Timeseparar e layout

É possível usar uma opção de layout horizontal irrestrito, como `Center` , `Start` ou `End` com [`TimePicker`](xref:Xamarin.Forms.TimePicker) :

```xaml
<TimePicker ···
            HorizontalOptions="Center"
            ··· />
```

No entanto, isso não é recomendado. Dependendo da configuração da [`Format`](xref:Xamarin.Forms.TimePicker.Format) propriedade, os horários selecionados podem exigir diferentes larguras de exibição. Por exemplo, a cadeia de caracteres de formato "T" faz com que a [`TimePicker`](xref:Xamarin.Forms.TimePicker) exibição exiba os horários em um formato longo, e "4:15:26 am" requer uma largura de exibição maior do que o formato de hora abreviada ("T") de "4:15 AM". Dependendo da plataforma, essa diferença pode fazer com que a `TimePicker` exibição altere a largura no layout ou que a exibição seja truncada.

> [!TIP]
> É melhor usar a `HorizontalOptions` configuração padrão de `Fill` com [`TimePicker`](xref:Xamarin.Forms.TimePicker) e não usar uma largura de `Auto` quando colocar `TimePicker` em uma `Grid` célula.

## <a name="timepicker-in-an-application"></a>Timeseparar em um aplicativo

O exemplo [**SetTimer**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-timepicker) inclui [`TimePicker`](xref:Xamarin.Forms.TimePicker) [`Entry`](xref:Xamarin.Forms.Entry) [`Switch`](xref:Xamarin.Forms.Switch) exibições, e em sua página. O `TimePicker` pode ser usado para selecionar uma hora e quando ocorrer uma caixa de diálogo de alerta que lembra o usuário do texto no `Entry` , desde que o `Switch` seja alternado. Este é o arquivo XAML:

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

O [`Entry`](xref:Xamarin.Forms.Entry) permite inserir o texto do lembrete que será exibido quando ocorrer o tempo selecionado. O [`TimePicker`](xref:Xamarin.Forms.TimePicker) é atribuído a uma [`Format`](xref:Xamarin.Forms.TimePicker.Format) propriedade de "T" para o formato de hora longa. Ele tem um manipulador de eventos anexado ao [`PropertyChanged`](xref:Xamarin.Forms.BindableObject.PropertyChanged) evento e o [`Switch`](xref:Xamarin.Forms.Switch) tem um manipulador anexado ao seu [`Toggled`](xref:Xamarin.Forms.Switch.Toggled) evento. Esses manipuladores de eventos estão no arquivo code-behind e chamam o `SetTriggerTime` método:

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

O `SetTriggerTime` método calcula uma hora do temporizador com base no `DateTime.Today` valor da propriedade e o `TimeSpan` valor retornado do [`TimePicker`](xref:Xamarin.Forms.TimePicker) . Isso é necessário porque a `DateTime.Today` propriedade retorna um `DateTime` que indica a data atual, mas com uma hora de meia-noite. Se o tempo do temporizador já passou hoje, supõe-se que seja amanhã.

O temporizador é marcado a cada segundo, executando o `OnTimerTick` método que verifica se o [`Switch`](xref:Xamarin.Forms.Switch) está ligado e se a hora atual é maior ou igual à hora do temporizador. Quando ocorre a hora do temporizador, o [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) método apresenta uma caixa de diálogo de alerta ao usuário como um lembrete.

Quando o exemplo é executado pela primeira vez, a [`TimePicker`](xref:Xamarin.Forms.TimePicker) exibição é inicializada como 11h. Tocar em `TimePicker` invoca o seletor de tempo de plataforma. As plataformas implementam o seletor de tempo de maneiras muito diferentes, mas cada abordagem é familiar para os usuários dessa plataforma:

[![Selecionar hora](timepicker-images/timepicker-open.png "Selecionar hora")](timepicker-images/timepicker-open-large.png#lightbox "Selecionar hora")

> [!TIP]
> No Android, a [`TimePicker`](xref:Xamarin.Forms.TimePicker) caixa de diálogo pode ser personalizada substituindo o `CreateTimePickerDialog` método em um renderizador personalizado. Isso permite, por exemplo, botões adicionais a serem adicionados à caixa de diálogo.

Depois de selecionar uma hora, a hora selecionada é exibida no [`TimePicker`](xref:Xamarin.Forms.TimePicker) :

[![Hora selecionada](timepicker-images/timepicker-selected.png "Hora selecionada")](timepicker-images/timepicker-selected-large.png#lightbox "Hora selecionada")

Desde que o [`Switch`](xref:Xamarin.Forms.Switch) seja alternado para a posição ligado, o aplicativo exibe uma caixa de diálogo de alerta lembrando o usuário do texto em [`Entry`](xref:Xamarin.Forms.Entry) quando ocorre a hora selecionada:

[![Pop-up de temporizador](timepicker-images/timer-test.png "Pop-up de temporizador")](timepicker-images/timer-test-large.png#lightbox "Pop-up de temporizador")

Assim que a caixa de diálogo de alerta for exibida, o [`Switch`](xref:Xamarin.Forms.Switch) será alternado para a posição desativado.

## <a name="related-links"></a>Links relacionados

- [Exemplo de SetTimer](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-timepicker)
- [API timeseparar](xref:Xamarin.Forms.TimePicker)
