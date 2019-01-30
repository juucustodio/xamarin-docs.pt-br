---
title: Controle de seletor no xamarin. IOS
description: Este documento descreve como projetar e trabalhar com controles de seletor em um aplicativo xamarin. IOS. Ele aborda como implementar um seletor de no código e no Designer do iOS.
ms.prod: xamarin
ms.assetid: A2369EFC-285A-44DD-9E80-EC65BC3DF041
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 08/14/2018
ms.openlocfilehash: 525ddf3c8cfc457738099c3afbb162fd3fb9239b
ms.sourcegitcommit: a1a58afea68912c79d16a3f64de9a0c1feb2aeb4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55233569"
---
# <a name="picker-control-in-xamarinios"></a>Controle de seletor no xamarin. IOS

Um [ `UIPickerView` ](xref:UIKit.UIPickerView) torna possível escolher um valor de uma lista por componentes individuais de uma interface semelhante a roda de rolagem.

Seletores costumam ser usados para selecionar uma data e hora; A Apple fornece o [`UIDatePicker`](xref:UIKit.UIDatePicker)
classe para essa finalidade.

O artigo descreve como implementar e usar o `UIPickerView` e `UIDatePicker` controles.

## <a name="uipickerview"></a>UIPickerView

### <a name="implementing-a-picker"></a>Implementando um seletor

Implementar um seletor de instanciar um novo `UIPickerView`:

```csharp
UIPickerView pickerView = new UIPickerView(
    new CGRect(
        UIScreen.MainScreen.Bounds.X - UIScreen.MainScreen.Bounds.Width, 
        UIScreen.MainScreen.Bounds.Height - 230,
        UIScreen.MainScreen.Bounds.Width,
        180
    )
);
```

### <a name="pickers-and-storyboards"></a>Seletores e storyboards

Para criar um seletor na **Designer do iOS**, arraste um **seletor de exibição** do **caixa de ferramentas** à superfície de design.

![Arraste um seletor de exibição para a superfície de design](picker-images/image1.png "arrastar um seletor de exibição para a superfície de design")

### <a name="working-with-a-picker-control"></a>Trabalhando com um controle seletor

Usa um seletor de um _modelo_ para interagir com dados:

```csharp
public override void ViewDidLoad()
{
    base.ViewDidLoad();
    var pickerModel = new PeopleModel(personLabel);
    personPicker.Model = pickerModel;
}
```

O [ `UIPickerViewModel` ](xref:UIKit.UIPickerViewModel) classe base implementa duas interfaces, [`IUIPickerDataSource`](xref:UIKit.IUIPickerViewDataSource)
e [ `IUIPickerViewDelegate` ](xref:UIKit.IUIPickerViewDelegate), que declarar vários métodos que especificam os dados do seletor e como ele lida com a interação:

```csharp
public class PeopleModel : UIPickerViewModel
{
    public string[] names = new string[] {
            "Amy Burns",
            "Kevin Mullins",
            "Craig Dunn",
            "Joel Martinez",
            "Charles Petzold",
            "David Britch",
            "Mark McLemore",
            "Tom Opegenorth",
            "Joseph Hill",
            "Miguel De Icaza"
        };

    private UILabel personLabel;

    public PeopleModel(UILabel personLabel)
    {
        this.personLabel = personLabel;
    }

    public override nint GetComponentCount(UIPickerView pickerView)
    {
        return 2;
    }

    public override nint GetRowsInComponent(UIPickerView pickerView, nint component)
    {
        return names.Length;
    }

    public override string GetTitle(UIPickerView pickerView, nint row, nint component)
    {
        if (component == 0)
            return names[row];
        else
            return row.ToString();
    }

    public override void Selected(UIPickerView pickerView, nint row, nint component)
    {
        personLabel.Text = $"This person is: {names[pickerView.SelectedRowInComponent(0)]},\n they are number {pickerView.SelectedRowInComponent(1)}";
    }

    public override nfloat GetComponentWidth(UIPickerView picker, nint component)
    {
        if (component == 0)
            return 240f;
        else
            return 40f;
    }

    public override nfloat GetRowHeight(UIPickerView picker, nint component)
    {
        return 40f;
    }
```

Um seletor pode ter várias colunas, ou _componentes_. Componentes de particionem um seletor em várias seções, permitindo a seleção de dados mais fácil e mais específico:

![Seletor de dois componentes](picker-images/image3.png "seletor com dois componentes")

Para especificar o número de componentes em um seletor, use o [`GetComponentCount`](xref:UIKit.UIPickerViewModel.GetComponentCount(UIKit.UIPickerView)) 
método.

### <a name="customizing-a-pickers-appearance"></a>Personalizando a aparência do seletor

Para personalizar a aparência de um seletor, use o [`UIPickerView.UIPickerViewAppearance`](https://developer.xamarin.com/api/type/UIKit.UIPickerView+UIPickerViewAppearance/)
classe ou substituir os [ `GetView` ](xref:UIKit.UIPickerViewModel.GetView(UIKit.UIPickerView,System.nint,System.nint,UIKit.UIView)) e [ `GetRowHeight` ](xref:UIKit.UIPickerViewModel.GetRowHeight(UIKit.UIPickerView,System.nint)) métodos no `UIPickerViewModel`.

## <a name="uidatepicker"></a>UIDatePicker

### <a name="implementing-a-date-picker"></a>Implementando um seletor de data

Implementar um seletor de data instanciando um `UIDatePicker`:

```csharp
UIPickerView pickerView = new UIPickerView(
    new CGRect(
        UIScreen.MainScreen.Bounds.X - UIScreen.MainScreen.Bounds.Width,
        UIScreen.MainScreen.Bounds.Height - 230,
        UIScreen.MainScreen.Bounds.Width,
        180
     )
);
```

### <a name="date-pickers-and-storyboards"></a>Selecionadores de data e storyboards

Para criar um seletor de data na **Designer do iOS**, arraste um **selecionador de data** do **caixa de ferramentas** à superfície de design.

![Arraste um seletor de data para a superfície de design](picker-images/image2.png "arraste um seletor de data para a superfície de design")

### <a name="date-picker-properties"></a>Propriedades de selecionador de data

#### <a name="minimum-and-maximum-date"></a>Data de máximo e mínimo

[`MinimumDate`](xref:UIKit.UIDatePicker.MinimumDate) e [ `MaximumDate` ](xref:UIKit.UIDatePicker.MaximumDate) limitar o intervalo de datas, disponíveis no selecionador de data. Por exemplo, o código a seguir restringe um selecionador de data para os anos de sessenta levam para o momento presente:

```csharp
var calendar = new NSCalendar(NSCalendarType.Gregorian);
var currentDate = NSDate.Now;
var components = new NSDateComponents();
components.Year = -60;
NSDate minDate = calendar.DateByAddingComponents(components, NSDate.Now, NSCalendarOptions.None);
datePickerView.MinimumDate = minDate;
datePickerView.MaximumDate = NSDate.Now;
```

> [!TIP]
> É possível converter explicitamente um `DateTime` para um `NSDate`:
> ```csharp
> DatePicker.MinimumDate = (NSDate)DateTime.Today.AddDays (-7);
> DatePicker.MaximumDate = (NSDate)DateTime.Today.AddDays (7);
> ```

#### <a name="minute-interval"></a>Intervalo de minutos

O [ `MinuteInterval` ](xref:UIKit.UIDatePicker.MinuteInterval) propriedade define o intervalo no qual o seletor de exibirá minutos:

```csharp
datePickerView.MinuteInterval = 10;
```

#### <a name="mode"></a>Modo

Suportam a quatro selecionadores [modos](xref:UIKit.UIDatePickerMode)descrito abaixo:

##### <a name="uidatepickermodetime"></a>UIDatePickerMode.Time

`UIDatePickerMode.Time` Exibe a hora com um seletor de hora e minuto e uma designação de AM ou PM opcional:

```csharp
datePickerView.Mode = UIDatePickerMode.Time;
```

![UIDatePickerMode.Time](picker-images/image8.png "UIDatePickerMode.Time")

##### <a name="uidatepickermodedate"></a>UIDatePickerMode.Date

`UIDatePickerMode.Date` Exibe a data com um seletor de ano, mês e dia:

```csharp
datePickerView.Mode = UIDatePickerMode.Date;
```

![UIDatePickerMode.Date](picker-images/image7.png "UIDatePickerMode.Date")

A ordem dos seletores depende da localidade do seletor de data, que, por padrão, usa a localidade do sistema. A imagem acima mostra o layout dos seletores no `en_US` localidade, mas a seguir altera a ordem dia | Mês | Ano:

```csharp
datePickerView.Locale = NSLocale.FromLocaleIdentifier("en_GB");
```

![Dia | Mês | Ano](picker-images/image9.png "dia | Mês | Ano")

##### <a name="uidatepickermodedateandtime"></a>UIDatePickerMode.DateAndTime

`UIDatePickerMode.DateAndTime` Exibe uma exibição abreviada de data, o tempo em horas e minutos e uma designação de AM ou PM opcional (dependendo do formato de 12 ou 24 horas é usado):

```csharp
datePickerView.Mode = UIDatePickerMode.DateAndTime;
```

![UIDatePickerMode.DateAndTime](picker-images/image6.png "UIDatePickerMode.DateAndTime")

Assim como acontece com [ `UIDatePickerMode.Date` ](#uidatepickermodedate), a ordem dos seletores e o uso de um relógio de 12 ou 24 horas dependem da localidade do seletor de data.

> [!TIP]
> Use o `Date` propriedade para capturar o valor de um seletor de data no modo `UIDatePickerMode.Time`, `UIDatePickerMode.Date`, ou `UIDatePickerMode.DateAndTime`. Esse valor é armazenado como um `NSDate`.

##### <a name="uidatepickermodecountdowntimer"></a>UIDatePickerMode.CountDownTimer

`UIDatePickerMode.CountDownTimer` Exibe os valores de hora e minuto:

```csharp
datePickerView.Mode = UIDatePickerMode.CountDownTimer;
```

!["UIDatePickerMode.CountDownTimer"](picker-images/image5.png "UIDatePickerMode.CountDownTimer")

O `CountDownDuration` propriedade captura o valor de um seletor de data no `UIDatePickerMode.CountDownTimer` modo. Por exemplo, para adicionar o valor de contagem regressiva para a data atual:

```csharp
var currentTime = NSDate.Now;
var countDownTimerTime = datePickerView.CountDownDuration;
var finishCountdown = currentTime.AddSeconds(countDownTimerTime);

dateLabel.Text = "Alarm set for:" + coundownTimeformat.ToString(finishCountdown);
```

#### <a name="nsdateformatter"></a>NSDateFormatter

Para formatar uma `NSDate`, use uma [ `NSDateFormatter` ](xref:Foundation.NSDateFormatter).

Para usar um `NSDateFormatter`, chame seu [ `ToString` ](xref:Foundation.NSDateFormatter.ToString(Foundation.NSDate)) método. Por exemplo:

```csharp
var date = NSDate.Now;
var formatter = new NSDateFormatter();
formatter.DateStyle = NSDateFormatterStyle.Full;
formatter.TimeStyle = NSDateFormatterStyle.Full;
var formattedDate = formatter.ToString(d);
// Tuesday, August 14, 2018 at 11:20:42 PM Mountain Daylight Time
```

##### <a name="dateformat"></a>DateFormat

O [ `DateFormat` ](xref:Foundation.NSDateFormatter.DateFormat) propriedade (uma cadeia de caracteres) de um `NSDateFormatter` permite uma especificação de formato de data personalizável:

```csharp
NSDateFormatter dateFormat = new NSDateFormatter();
dateFormat.DateFormat = "yyyy-MM-dd";
```

##### <a name="timestyle"></a>TimeStyle

O [ `TimeStyle` ](xref:Foundation.NSDateFormatter.TimeStyle) propriedade (uma [ `NSDateFormatterStyle` ](xref:Foundation.NSDateFormatterStyle) de um `NSDateFormatter` Especifica a formatação de hora com base em predeterminada estilos:

```csharp
NSDateFormatter timeFormat = new NSDateFormatter();
timeFormat.TimeStyle = NSDateFormatterStyle.Short;
```

Vários `NSDateFormatterStyle` valores vezes são exibidos da seguinte maneira:

- `NSDateFormatterStyle.Full`: Horário de verão do Leste 7:46:00 PM
- `NSDateFormatterStyle.Long`: 7:47:00 PM EDT
- `NSDateFormatterStyle.Medium`: 47: 7:00 PM
- `NSDateFormatterSytle.Short`: 7:47 PM

##### <a name="datestyle"></a>DateStyle

O [ `DateStyle` ](xref:Foundation.NSDateFormatter.DateStyle) propriedade (uma `NSDateFormatterStyle`) de um `NSDateFormatter` Especifica a formatação de datas com base em predeterminada estilos:

```csharp
NSDateFormatter dateTimeformat = new NSDateFormatter();
dateTimeformat.DateStyle = NSDateFormatterStyle.Long;
```

Vários `NSDateFormatterStyle` valores exibem datas da seguinte maneira:

- `NSDateFormatterStyle.Full`: Quarta-feira, 2 de agosto de 2017 às 7:48 horas
- `NSDateFormatterStyle.Long`: 2 de agosto de 2017 em 7:49 PM
- `NSDateFormatterStyle.Medium`: 2 de agosto de 2017, 7:49 PM
- `NSDateFormatterStyle.Short`: 2/8/17, 7:50 PM

> [!NOTE]
> `DateFormat` e `DateStyle` / `TimeStyle` fornecem diferentes maneiras de especificar a formatação de data e hora. Mais recentemente definir propriedades determinam que o formatador de data de saída do.

## <a name="related-links"></a>Links relacionados

- [PickerControl (amostra)](https://developer.xamarin.com/samples/monotouch/PickerControl/)
