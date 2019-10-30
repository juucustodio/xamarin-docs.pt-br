---
title: Controle do seletor no Xamarin. iOS
description: Este documento descreve como projetar e trabalhar com controles de seletor em um aplicativo Xamarin. iOS. Ele aborda como implementar um seletor no código e no designer do iOS.
ms.prod: xamarin
ms.assetid: A2369EFC-285A-44DD-9E80-EC65BC3DF041
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 08/14/2018
ms.openlocfilehash: ac96363378e91c60956d28352535733c7e954e6a
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73021992"
---
# <a name="picker-control-in-xamarinios"></a>Controle do seletor no Xamarin. iOS

Uma [`UIPickerView`](xref:UIKit.UIPickerView) torna possível escolher um valor de uma lista, rolando componentes individuais de uma interface do tipo roda.

Os seletores são usados frequentemente para selecionar uma data e hora; A Apple fornece o [`UIDatePicker`](xref:UIKit.UIDatePicker)
classe para essa finalidade.

O artigo descreve como implementar e usar os controles `UIPickerView` e `UIDatePicker`.

## <a name="uipickerview"></a>UIPickerView

### <a name="implementing-a-picker"></a>Implementando um seletor

Implemente um seletor instanciando um novo `UIPickerView`:

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

Para criar um seletor no **designer do IOS**, arraste um **modo de exibição de seletor** da **caixa de ferramentas** para a superfície de design.

![Arraste uma exibição de seletor para a superfície de design](picker-images/image1.png "Arraste uma exibição de seletor para a superfície de design")

### <a name="working-with-a-picker-control"></a>Trabalhando com um controle seletor

Um seletor usa um _modelo_ para interagir com os dados:

```csharp
public override void ViewDidLoad()
{
    base.ViewDidLoad();
    var pickerModel = new PeopleModel(personLabel);
    personPicker.Model = pickerModel;
}
```

A classe base [`UIPickerViewModel`](xref:UIKit.UIPickerViewModel) implementa duas interfaces, [`IUIPickerDataSource`](xref:UIKit.IUIPickerViewDataSource)
e [`IUIPickerViewDelegate`](xref:UIKit.IUIPickerViewDelegate), que declaram vários métodos que especificam os dados de um seletor e como ele manipula a interação:

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

Um seletor pode ter várias colunas ou _componentes_. Componentes particionam um seletor em várias seções, permitindo uma seleção de dados mais fácil e mais específica:

![Seletor com dois componentes](picker-images/image3.png "Seletor com dois componentes")

Para especificar o número de componentes em um seletor, use o [`GetComponentCount`](xref:UIKit.UIPickerViewModel.GetComponentCount(UIKit.UIPickerView)) 
método.

### <a name="customizing-a-pickers-appearance"></a>Personalizando a aparência de um seletor

Para personalizar a aparência de um seletor, use o [`UIPickerView.UIPickerViewAppearance`](xref:UIKit.UIPickerView.UIPickerViewAppearance)
ou substitua os métodos [`GetView`](xref:UIKit.UIPickerViewModel.GetView(UIKit.UIPickerView,System.nint,System.nint,UIKit.UIView)) e [`GetRowHeight`](xref:UIKit.UIPickerViewModel.GetRowHeight(UIKit.UIPickerView,System.nint)) no `UIPickerViewModel`.

## <a name="uidatepicker"></a>UIDatePicker

### <a name="implementing-a-date-picker"></a>Implementando um seletor de data

Implemente um seletor de data instanciando um `UIDatePicker`:

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

### <a name="date-pickers-and-storyboards"></a>Seletores de data e storyboards

Para criar um seletor de data no **designer do IOS**, arraste um **seletor de data** da caixa de **ferramentas** para a superfície de design.

![Arrastar um seletor de data para a superfície de design](picker-images/image2.png "Arrastar um seletor de data para a superfície de design")

### <a name="date-picker-properties"></a>Propriedades do seletor de data

#### <a name="minimum-and-maximum-date"></a>Data mínima e máxima

[`MinimumDate`](xref:UIKit.UIDatePicker.MinimumDate) e [`MaximumDate`](xref:UIKit.UIDatePicker.MaximumDate) limitar o intervalo de datas disponíveis no seletor de data. Por exemplo, o código a seguir restringe um seletor de data para os 60 anos que levam até o momento atual:

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
> É possível converter explicitamente um `DateTime` em um `NSDate`:
>
> ```csharp
> DatePicker.MinimumDate = (NSDate)DateTime.Today.AddDays (-7);
> DatePicker.MaximumDate = (NSDate)DateTime.Today.AddDays (7);
> ```

#### <a name="minute-interval"></a>Intervalo de minutos

A propriedade [`MinuteInterval`](xref:UIKit.UIDatePicker.MinuteInterval) define o intervalo no qual o seletor exibirá minutos:

```csharp
datePickerView.MinuteInterval = 10;
```

#### <a name="mode"></a>Modo

Os seletores de data dão suporte a quatro [modos](xref:UIKit.UIDatePickerMode), descritos abaixo:

##### <a name="uidatepickermodetime"></a>UIDatePickerMode. time

`UIDatePickerMode.Time` exibe a hora com um seletor de hora e minuto e uma designação opcional AM ou PM:

```csharp
datePickerView.Mode = UIDatePickerMode.Time;
```

![UIDatePickerMode. time](picker-images/image8.png "UIDatePickerMode. time")

##### <a name="uidatepickermodedate"></a>UIDatePickerMode. Date

`UIDatePickerMode.Date` exibe a data com um seletor de mês, dia e ano:

```csharp
datePickerView.Mode = UIDatePickerMode.Date;
```

![UIDatePickerMode. Date](picker-images/image7.png "UIDatePickerMode. Date")

A ordem dos seletores depende da localidade do seletor de data, que por padrão usa a localidade do sistema. A imagem acima mostra o layout dos seletores na localidade `en_US`, mas as seguintes alterações são feitas no pedido para o dia | Mês | Year

```csharp
datePickerView.Locale = NSLocale.FromLocaleIdentifier("en_GB");
```

![Dia | Mês | Year](picker-images/image9.png "Dia | Mês | Year")

##### <a name="uidatepickermodedateandtime"></a>UIDatePickerMode. DateAndTime

`UIDatePickerMode.DateAndTime` exibe uma exibição reduzida da data, o tempo em horas e minutos e uma designação de AM ou PM opcional (dependendo se um relógio de 12 ou 24 horas é usado):

```csharp
datePickerView.Mode = UIDatePickerMode.DateAndTime;
```

![UIDatePickerMode. DateAndTime](picker-images/image6.png "UIDatePickerMode. DateAndTime")

Assim como ocorre com [`UIDatePickerMode.Date`](#uidatepickermodedate), a ordem dos seletores e o uso de um relógio de 12 ou 24 horas depende da localidade do seletor de data.

> [!TIP]
> Use a propriedade `Date` para capturar o valor de um seletor de data no modo `UIDatePickerMode.Time`, `UIDatePickerMode.Date` ou `UIDatePickerMode.DateAndTime`. Esse valor é armazenado como um `NSDate`.

##### <a name="uidatepickermodecountdowntimer"></a>UIDatePickerMode.CountDownTimer

`UIDatePickerMode.CountDownTimer` exibe valores de hora e minuto:

```csharp
datePickerView.Mode = UIDatePickerMode.CountDownTimer;
```

!["UIDatePickerMode.CountDownTimer"](picker-images/image5.png "UIDatePickerMode.CountDownTimer")

A propriedade `CountDownDuration` captura o valor de um seletor de data no modo `UIDatePickerMode.CountDownTimer`. Por exemplo, para adicionar o valor de contagem regressiva à data atual:

```csharp
var currentTime = NSDate.Now;
var countDownTimerTime = datePickerView.CountDownDuration;
var finishCountdown = currentTime.AddSeconds(countDownTimerTime);

dateLabel.Text = "Alarm set for:" + coundownTimeformat.ToString(finishCountdown);
```

#### <a name="nsdateformatter"></a>NSDateFormatter

Para formatar um `NSDate`, use um [`NSDateFormatter`](xref:Foundation.NSDateFormatter).

Para usar um `NSDateFormatter`, chame seu método [`ToString`](xref:Foundation.NSDateFormatter.ToString(Foundation.NSDate)) . Por exemplo:

```csharp
var date = NSDate.Now;
var formatter = new NSDateFormatter();
formatter.DateStyle = NSDateFormatterStyle.Full;
formatter.TimeStyle = NSDateFormatterStyle.Full;
var formattedDate = formatter.ToString(d);
// Tuesday, August 14, 2018 at 11:20:42 PM Mountain Daylight Time
```

##### <a name="dateformat"></a>DateFormat

A propriedade [`DateFormat`](xref:Foundation.NSDateFormatter.DateFormat) (uma cadeia de caracteres) de um `NSDateFormatter` permite uma especificação de formato de data personalizável:

```csharp
NSDateFormatter dateFormat = new NSDateFormatter();
dateFormat.DateFormat = "yyyy-MM-dd";
```

##### <a name="timestyle"></a>Timestyle

A propriedade [`TimeStyle`](xref:Foundation.NSDateFormatter.TimeStyle) (uma [`NSDateFormatterStyle`](xref:Foundation.NSDateFormatterStyle) de um `NSDateFormatter` especifica a formatação de tempo com base em estilos predeterminados:

```csharp
NSDateFormatter timeFormat = new NSDateFormatter();
timeFormat.TimeStyle = NSDateFormatterStyle.Short;
```

Vários valores de `NSDateFormatterStyle` exibem horas da seguinte maneira:

- `NSDateFormatterStyle.Full`: horário de Verão do leste 7:46:00 PM
- `NSDateFormatterStyle.Long`: EDT do 7:47:00 PM
- `NSDateFormatterStyle.Medium`:7:47:00 PM
- `NSDateFormatterSytle.Short`:7:47 PM

##### <a name="datestyle"></a>Datastyle

A propriedade [`DateStyle`](xref:Foundation.NSDateFormatter.DateStyle) (um `NSDateFormatterStyle`) de um `NSDateFormatter` especifica a formatação de data com base em estilos predeterminados:

```csharp
NSDateFormatter dateTimeformat = new NSDateFormatter();
dateTimeformat.DateStyle = NSDateFormatterStyle.Long;
```

Vários valores de `NSDateFormatterStyle` exibem datas da seguinte maneira:

- `NSDateFormatterStyle.Full`: quarta-feira, 2 de agosto de 2017 às 7:48 PM
- `NSDateFormatterStyle.Long`:2 de agosto de 2017 às 7:49 PM
- `NSDateFormatterStyle.Medium`:2 de agosto de 2017, 7:49 PM
- `NSDateFormatterStyle.Short`:8/2/17, 7:50 PM

> [!NOTE]
> `DateFormat` e `DateStyle` / `TimeStyle` fornecem diferentes maneiras de especificar a formatação de data e hora. As propriedades definidas mais recentemente determinam a saída do formatador de data.

## <a name="related-links"></a>Links relacionados

- [PickerControl (exemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/pickercontrol)
