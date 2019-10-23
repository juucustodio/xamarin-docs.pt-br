---
title: Timeseparador do Xamarin. Forms
description: O timeseparar é um modo de exibição Xamarin. Forms que permite ao usuário selecionar uma hora. Este artigo explica como consumir um seletor de em um aplicativo Xamarin. Forms.
ms.prod: xamarin
ms.assetid: 2E99FB23-B82D-4EB4-AFB3-5002E736E7B2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/16/2018
ms.openlocfilehash: aae0791199b0e3042a3c619fcb11e7b877f52012
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72695910"
---
# <a name="xamarinforms-timepicker"></a>Timeseparador do Xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-timepicker)

_Uma exibição do Xamarin. Forms que permite ao usuário selecionar uma hora._

O [`TimePicker`](xref:Xamarin.Forms.TimePicker) Xamarin. Forms invoca o controle de seletor de tempo da plataforma e permite que o usuário selecione uma hora. `TimePicker` define as seguintes propriedades:

- [`Time`](xref:Xamarin.Forms.TimePicker.Time) do tipo `TimeSpan`, a hora selecionada, cujo padrão é um `TimeSpan` de 0. O tipo de `TimeSpan` indica uma duração de tempo desde a meia-noite.
- [`Format`](xref:Xamarin.Forms.TimePicker.Format) do tipo `string`, uma cadeia de caracteres de formatação .NET [padrão](/dotnet/standard/base-types/standard-date-and-time-format-strings/) ou [personalizada](/dotnet/standard/base-types/custom-date-and-time-format-strings/) , cujo padrão é "t", o padrão de tempo curto.
- [`TextColor`](xref:Xamarin.Forms.TimePicker.TextColor) do tipo [`Color`](xref:Xamarin.Forms.Color), a cor usada para exibir a hora selecionada, cujo padrão é [`Color.Default`](xref:Xamarin.Forms.Color.Default).
- [`FontAttributes`](xref:Xamarin.Forms.TimePicker.FontAttributes) do tipo [`FontAttributes`](xref:Xamarin.Forms.FontAttributes), cujo padrão é [`FontAtributes.None`](xref:Xamarin.Forms.FontAttributes.None).
- [`FontFamily`](xref:Xamarin.Forms.TimePicker.FontFamily) do tipo `string`, cujo padrão é `null`.
- [`FontSize`](xref:Xamarin.Forms.TimePicker.FontSize) do tipo `double`, que usa como padrão-1,0.
- `CharacterSpacing`, do tipo `double`, é o espaçamento entre os caracteres do texto do `TimePicker`.

Todas essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que elas podem ser estilizadas e as propriedades podem ser destinos de associações de dados. A propriedade [`Time`](xref:Xamarin.Forms.TimePicker.Time) tem um modo de associação padrão de [`BindingMode.TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay), o que significa que ele pode ser um destino de uma associação de dados em um aplicativo que usa a arquitetura [MVVM (Model-View-ViewModel)](~/xamarin-forms/enterprise-application-patterns/mvvm.md) .

O [`TimePicker`](xref:Xamarin.Forms.TimePicker) não inclui um evento para indicar um novo valor de [`Time`](xref:Xamarin.Forms.TimePicker.Time) selecionado. Se precisar ser notificado disso, você poderá adicionar um manipulador para o evento [`PropertyChanged`](xref:Xamarin.Forms.BindableObject.PropertyChanged) .

## <a name="initializing-the-time-property"></a>Inicializando a propriedade time

No código, você pode inicializar a propriedade [`Time`](xref:Xamarin.Forms.TimePicker.Time) com um valor do tipo `TimeSpan`:

```csharp
TimePicker timePicker = new TimePicker
{
  Time = new TimeSpan(4, 15, 26) // Time set to "04:15:26"
};
```

Quando a propriedade [`Time`](xref:Xamarin.Forms.TimePicker.Time) é especificada em XAML, o valor é convertido em um `TimeSpan` e validado para garantir que o número de milissegundos seja maior ou igual a 0, e que o número de horas seja menor que 24. Os componentes de tempo devem ser separados por dois-pontos:

```xaml
<TimePicker Time="4:15:26" />
```

Se a propriedade [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de [`TimePicker`](xref:Xamarin.Forms.TimePicker) for definida como uma instância de um ViewModel que contém uma propriedade do tipo `TimeSpan` chamada `SelectedTime` (por exemplo), você poderá instanciar o `TimePicker` da seguinte maneira:

```xaml
<TimePicker Time="{Binding SelectedTime}" />
```

Neste exemplo, a propriedade [`Time`](xref:Xamarin.Forms.TimePicker.Time) é inicializada para a propriedade `SelectedTime` no ViewModel. Como a propriedade `Time` tem um modo de associação de [`TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay), qualquer novo horário que o usuário seleciona é automaticamente propagado para o ViewModel.

Se o [`TimePicker`](xref:Xamarin.Forms.TimePicker) não contiver uma associação em sua propriedade [`Time`](xref:Xamarin.Forms.TimePicker.Time) , um aplicativo deverá anexar um manipulador ao evento [`PropertyChanged`](xref:Xamarin.Forms.BindableObject.PropertyChanged) para ser informado quando o usuário selecionar uma nova hora.

Para obter informações sobre como definir propriedades de fonte, consulte [fontes](~/xamarin-forms/user-interface/text/fonts.md).

## <a name="timepicker-and-layout"></a>Timeseparar e layout

É possível usar uma opção de layout horizontal irrestrito, como `Center`, `Start` ou `End` com [`TimePicker`](xref:Xamarin.Forms.TimePicker):

```xaml
<TimePicker ···
            HorizontalOptions="Center"
            ··· />
```

No entanto, isso não é recomendado. Dependendo da configuração da propriedade [`Format`](xref:Xamarin.Forms.TimePicker.Format) , os horários selecionados podem exigir diferentes larguras de exibição. Por exemplo, a cadeia de caracteres de formato "T" faz com que a exibição de [`TimePicker`](xref:Xamarin.Forms.TimePicker) exiba os horários em um formato longo, e "4:15:26 am" requer uma largura de exibição maior do que o formato de hora abreviada ("T") de "4:15 AM". Dependendo da plataforma, essa diferença pode fazer com que a exibição de `TimePicker` altere a largura no layout ou que a exibição seja truncada.

> [!TIP]
> É melhor usar a configuração de `HorizontalOptions` padrão de `Fill` com [`TimePicker`](xref:Xamarin.Forms.TimePicker), e não usar uma largura de `Auto` ao colocar `TimePicker` em uma célula `Grid`.

## <a name="timepicker-in-an-application"></a>Timeseparar em um aplicativo

O exemplo [**SetTimer**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-timepicker) inclui as exibições [`TimePicker`](xref:Xamarin.Forms.TimePicker), [`Entry`](xref:Xamarin.Forms.Entry)e [`Switch`](xref:Xamarin.Forms.Switch) em sua página. A `TimePicker` pode ser usada para selecionar uma hora e, quando ocorrer uma caixa de diálogo de alerta que lembra o usuário do texto na `Entry`, desde que a `Switch` seja ativada. Este é o arquivo XAML:

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

O [`Entry`](xref:Xamarin.Forms.Entry) permite inserir o texto do lembrete que será exibido quando ocorrer a hora selecionada. A [`TimePicker`](xref:Xamarin.Forms.TimePicker) é atribuída a uma propriedade [`Format`](xref:Xamarin.Forms.TimePicker.Format) de "t" para o formato de hora longa. Ele tem um manipulador de eventos anexado ao evento [`PropertyChanged`](xref:Xamarin.Forms.BindableObject.PropertyChanged) e o [`Switch`](xref:Xamarin.Forms.Switch) tem um manipulador anexado ao seu evento [`Toggled`](xref:Xamarin.Forms.Switch.Toggled) . Esses manipuladores de eventos estão no arquivo code-behind e chamam o método `SetTriggerTime`:

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

O método `SetTriggerTime` calcula uma hora do temporizador com base no valor da propriedade `DateTime.Today` e o valor `TimeSpan` retornado da [`TimePicker`](xref:Xamarin.Forms.TimePicker). Isso é necessário porque a propriedade `DateTime.Today` retorna um `DateTime` indicando a data atual, mas com uma hora de meia-noite. Se o tempo do temporizador já passou hoje, supõe-se que seja amanhã.

O temporizador é marcado a cada segundo, executando o método `OnTimerTick` que verifica se o [`Switch`](xref:Xamarin.Forms.Switch) está ativado e se a hora atual é maior ou igual à hora do temporizador. Quando ocorre a hora do temporizador, o método [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) apresenta uma caixa de diálogo de alerta ao usuário como um lembrete.

Quando o exemplo é executado pela primeira vez, a exibição [`TimePicker`](xref:Xamarin.Forms.TimePicker) é inicializada para 11h. Tocar no `TimePicker` invoca o seletor de tempo de plataforma. As plataformas implementam o seletor de tempo de maneiras muito diferentes, mas cada abordagem é familiar para os usuários dessa plataforma:

[![Selecionar hora](timepicker-images/timepicker-open.png "Selecionar hora")](timepicker-images/timepicker-open-large.png#lightbox "Selecionar hora")

> [!TIP]
> No Android, a caixa de diálogo [`TimePicker`](xref:Xamarin.Forms.TimePicker) pode ser personalizada substituindo o método `CreateTimePickerDialog` em um renderizador personalizado. Isso permite, por exemplo, botões adicionais a serem adicionados à caixa de diálogo.

Depois de selecionar uma hora, a hora selecionada é exibida no [`TimePicker`](xref:Xamarin.Forms.TimePicker):

[![Hora selecionada](timepicker-images/timepicker-selected.png "Hora selecionada")](timepicker-images/timepicker-selected-large.png#lightbox "Hora selecionada")

Desde que a [`Switch`](xref:Xamarin.Forms.Switch) seja alternada para a posição ligado, o aplicativo exibe uma caixa de diálogo de alerta lembrando o usuário do texto no [`Entry`](xref:Xamarin.Forms.Entry) quando ocorre a hora selecionada:

[![Pop-up de temporizador](timepicker-images/timer-test.png "Pop-up de temporizador")](timepicker-images/timer-test-large.png#lightbox "Pop-up de temporizador")

Assim que a caixa de diálogo de alerta for exibida, a [`Switch`](xref:Xamarin.Forms.Switch) será alternada para a posição desativado.

## <a name="related-links"></a>Links relacionados

- [Exemplo de SetTimer](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-timepicker)
- [API timeseparar](xref:Xamarin.Forms.TimePicker)
