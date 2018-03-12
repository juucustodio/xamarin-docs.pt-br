---
title: Seletor
description: Este guia aborda criando e trabalhando com processos de escolhas em um aplicativo xamarin.
ms.topic: article
ms.prod: xamarin
ms.assetid: A2369EFC-285A-44DD-9E80-EC65BC3DF041
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 08/02/2017
ms.openlocfilehash: 402b17ddbb28fb8896ad0f158fe8dbcd17689f40
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="picker"></a>Seletor

O controle de seletor exibe o controle de 'roda como' que contém uma lista de rolagem de valores com o valor selecionado está sendo realçada. Os usuários gire a roda para selecionar a opção que desejarem.

Um usuário específico caso para selecionadores-a para definir a data e / ou hora. Para fornecer esse Apple criou uma subclasse personalizada de UIPickerView chamado UIDatePicker.

O artigo aborda a implementação e usando o [seletor](#picker) e [seletor de data](#datepicker) controles.

<a name="picker">

## <a name="picker"></a>Seletor

### <a name="implementing-a-picker"></a>Implementando um seletor

Um seletor é implementado por criando um novo [`UIPickerView`](https://developer.xamarin.com/api/type/UIKit.UIPickerView/):

```csharp
UIPickerView pickerView = new UIPickerView(
                            new CGRect(
                                UIScreen.MainScreen.Bounds.X-UIScreen.MainScreen.Bounds.Width, UIScreen.MainScreen.Bounds.Height -230, 
                                UIScreen.MainScreen.Bounds.Width, 
                                180));
```


### <a name="pickers-and-storyboards"></a>Selecionadores e Storyboards

Se você estiver usando o Designer do iOS para criar sua interface do usuário, o seletor de pode ser adicionado ao seu layout da caixa de ferramentas:

![](picker-images/image1.png)


### <a name="working-with-picker"></a>Trabalhando com o seletor

Depois que você criou um seletor, se em código ou por meio de storyboards, você precisará atribuir um _modelo_ a ela para que você possa passar e interagir com dados.

```csharp
public override void ViewDidLoad()
{
    base.ViewDidLoad();

    var pickerModel = new PeopleModel(personLabel);

    personPicker.Model = pickerModel;
}
```

O código a seguir mostra um exemplo de um modelo:

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

Primeiro, você precisará passar alguns dados para fornecer opções diferentes para um usuário selecionar. Quando possível tentar manter essa lista tão curta quanto possível, ou se necessário tentar usar mais de um 'discar' (chamado *componentes*):

![Seletor de dois componentes](picker-images/image3.png)

Para definir o número de componentes, substituir o `GetComponentCount` método: 

```csharp
public override nint GetComponentCount(UIPickerView pickerView)
{
    return 2;
}
```

O valor de retorno equivale ao número de ligações que terá seu seletor.

### <a name="customizing-appearance"></a>Personalizando a aparência
 
A aparência do `UIPickerView` pode ser personalizado usando o `UIPickerView.UIPickerViewAppearance` classe ou por substituir o `UIPickerViewModel.GetView` e `UIPickerViewModel.GetRowHeight` métodos no `UIPickerViewModel`.


<a name="datepicker">

## <a name="date-picker"></a>Seletor de data

### <a name="implementing-a-date-picker"></a>Implementando um seletor de data

Um seletor de data é implementado por criando um novo [ `UIDatePickerView` ](https://developer.xamarin.com/api/type/UIKit.UIDatePicker/):

```csharp
UIPickerView pickerView = new UIPickerView(
                            new CGRect(
                                UIScreen.MainScreen.Bounds.X-UIScreen.MainScreen.Bounds.Width, UIScreen.MainScreen.Bounds.Height -230, 
                                UIScreen.MainScreen.Bounds.Width, 
                                180));
```


### <a name="date-pickers-and-storyboards"></a>Data selecionadores e Storyboards

Se você estiver usando o Designer do iOS para criar sua interface do usuário, o **seletor de data** podem ser adicionados ao seu layout da caixa de ferramentas. As seguintes propriedades podem ser ajustadas de painel de propriedades:

![](picker-images/image2.png)

* **Modo** – modo a data e hora. Isso pode ser a data, hora, data e hora ou um temporizador de contagem regressiva. 
* **Localidade** – a localidade do seletor de data. Escolha **padrão** para definir como o padrão do sistema ou definido como qualquer localidade específica.
* **Intervalo de** – mostra o incremento em que as opções de relógio serão exibidas.
* **Data, a data mínima, a data máxima** – define as restrições e exibirá o seletor de data inicial para as datas podem ser selecionadas.

### <a name="configuring-the-datepicker"></a>Configurando o selecionador de data

Você pode limitar o intervalo de datas que um usuário pode selecionar usando o `MinimumDate` e `MaximumDate` propriedades. O trecho de código a seguir mostra um exemplo de como definir o intervalo entre 60 anos atrás e hoje:

```csharp
var calendar = new NSCalendar(NSCalendarType.Gregorian);
var currentDate = NSDate.Now;
var components = new NSDateComponents();

components.Year = -60;

NSDate minDate = calendar.DateByAddingComponents(components, NSDate.Now, NSCalendarOptions.None);

datePickerView.MinimumDate = minDate;
datePickerView.MaximumDate = NSDate.Now;
```

Como alternativa, você também pode usar os controles do .NET para definir o intervalo de datas de mínimo e máximo. Por exemplo:

```csharp
DatePicker.MinimumDate = (NSDate)DateTime.Today.AddDays (-7);
DatePicker.MaximumDate = (NSDate)DateTime.Today.AddDays (7);
```

Você também pode definir o `MinuteInterval` propriedade para definir o intervalo no qual o seletor exibirá minutos. O trecho de código a seguir pode ser usado para definir o seletor de minutos para ser definida em intervalos de 10.

```csharp
datePickerView.MinuteInterval = 10;
```

Há quatro modos de seletor de data pode ser definido como usando o [ `UIDatePicker.Mode` ](https://developer.xamarin.com/api/property/UIKit.UIDatePicker.Mode/) propriedade. A lista a seguir mostra um exemplo de cada um deles e como implementá-la:

#### <a name="time"></a>Hora

O modo de tempo exibe a hora com um seletor de hora e minuto e uma designação de AM ou PM opcional. Ele é definido com o `UIDatePickerMode.Time` propriedade. Por exemplo:

```csharp
datePickerView.Mode = UIDatePickerMode.Time;
```

A imagem a seguir ilustra um exemplo desse modo de DatePicker:

![](picker-images/image8.png)



#### <a name="date"></a>Date

O modo de data exibe a data com um seletor de ano, mês e dia. Ele é definido com o `UIDatePickerMode.Date` propriedade. Por exemplo:

```csharp
datePickerView.Mode = UIDatePickerMode.Date;
```

A imagem a seguir ilustra um exemplo de como essa DatePicker:

![](picker-images/image7.png)

A ordem dos seletores de depende da localidade da a `UIDatePicker`. Por padrão, isso será definido para o padrão do sistema. A imagem acima mostra o layout dos seletores no `en_US` localidade, mas ao alterá-lo para um dia | Mês | Layout de ano, você pode usar o código a seguir para definir a localidade:

```csharp
datePickerView.Locale = NSLocale.FromLocaleIdentifier("en_GB");
```

![](picker-images/image9.png)


#### <a name="date-and-time"></a>Data e Hora

O modo de data e hora exibe uma shortend da data, o tempo em horas e minutos e um dependings opcional de designação de AM ou PM em se for usado um relógio de 12 ou 24 horas. Ele é definido com o `UIDatePickerMode.DateAndTime` propriedade. Por exemplo:

```csharp
datePickerView.Mode = UIDatePickerMode.DateAndTime;
```

A imagem a seguir ilustra um exemplo de como essa DatePicker:

![](picker-images/image6.png)

Assim como acontece com [data](#Date), a ordem dos seletores e o uso de 12 ou 24 horas dependem a localidade da a `UIDatePicker`.

#### <a name="countdown-timer"></a>Timer de contagem regressiva

O modo de timer de contagem regressiva exibe a hora e os valores de minutos. Ele é definido com o `UIDatePickerMode.CountDownTimer` propriedade. Por exemplo:

```csharp
datePickerView.Mode = UIDatePickerMode.CountDownTimer;
```

A imagem a seguir ilustra um exemplo de como essa DatePicker:

![](picker-images/image5.png)

Você pode usar o `CountDownDuration` propriedade a ser exibido o valor, o seletor de data de contagem regressiva de captura. Por exemplo, para adicionar o valor de contagem regressiva para a data atual, você pode usar o código a seguir:

```csharp
var currentTime = NSDate.Now;
var countDownTimerTime = datePickerView.CountDownDuration;
var finishCountdown = currentTime.AddSeconds(countDownTimerTime);

dateLabel.Text = "Alarm set for:" + coundownTimeformat.ToString(finishCountdown);
```

#### <a name="formatting"></a>Formatação 

Os valores dos modos DateAndTime, data e hora podem ser capturados usando o `Date` propriedade o UIDatePicker (por exemplo: `datePickerView.Date`), que é do tipo NSDate. Para formatar essa data em algo mais legível, use [ `NSDateFormatter` ](https://developer.xamarin.com/api/type/Foundation.NSDateFormatter/). Os exemplos a seguir mostram como usar algumas das propriedades disponíveis nessa classe.

O `DateFormat` é definido como uma cadeia de caracteres para representar como a data deve ser exibida:

```csharp
NSDateFormatter dateFormat = new NSDateFormatter();
dateFormat.DateFormat = "yyyy-MM-dd";
```

O `TimeStyle` propriedade define um `NSDateFormatterStyle`:

```csharp
NSDateFormatter timeFormat = new NSDateFormatter();
timeFormat.TimeStyle = NSDateFormatterStyle.Short;
```

Os campos para `NSDateFormatterStyle` exibidos da seguinte maneira:

![](picker-images/timestyle.png)

O `DateStyle` propriedade define um `NSDateFormatterStyle`:

```csharp
NSDateFormatter dateTimeformat = new NSDateFormatter();
dateTimeformat.DateStyle = NSDateFormatterStyle.Long;
```

Os campos para `NSDateFormatterStyle` exibidos da seguinte maneira:

![](picker-images/datestyle.png)

Em seguida, você pode extrair o NSDate formatado como uma cadeia de caracteres usando o código a seguir:

```csharp
dateLabel.Text = dateTimeformat.ToString(datePickerView.Date);
```

## <a name="related-links"></a>Links relacionados

- [PickerControl (exemplo)](https://developer.xamarin.com/samples/monotouch/PickerControl/)
