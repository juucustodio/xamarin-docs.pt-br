---
title: Seletor de data
description: Selecionando datas do calendário usando DatePickerDialog e DialogFragment
ms.prod: xamarin
ms.assetid: F2BCD8D4-8957-EA53-C5A8-6BB603ADB47B
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 01/22/2018
ms.openlocfilehash: b54a0795dee0bd7a02b419da497f9a1b3f3ee7ff
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029213"
---
# <a name="android-date-picker"></a>Seletor de data do Android

## <a name="overview"></a>Visão Geral

Há ocasiões em que um usuário deve inserir dados em um aplicativo Android. Para ajudá-lo, a estrutura do Android fornece o [`DatePicker`](xref:Android.Widget.DatePicker) widget e o [`DatePickerDialog`](xref:Android.App.DatePickerDialog) . O `DatePicker` permite que os usuários selecionem o ano, o mês e o dia em uma interface consistente entre dispositivos e aplicativos. O `DatePickerDialog` é uma classe auxiliar que encapsula a `DatePicker` em uma caixa de diálogo.

Os aplicativos Android modernos devem exibir o `DatePickerDialog` em um [`DialogFragment`](xref:Android.App.DialogFragment). Isso permitirá que um aplicativo exiba o DatePicker como uma caixa de diálogo pop-up ou inserido em uma atividade. Além disso, o `DialogFragment` gerenciará o ciclo de vida e a exibição da caixa de diálogo, reduzindo a quantidade de código que deve ser implementada.

Este guia demonstrará como usar o `DatePickerDialog`, encapsulado em um `DialogFragment`. O aplicativo de exemplo exibirá o `DatePickerDialog` como uma caixa de diálogo modal quando o usuário clicar em um botão em uma atividade. Quando a data for definida pelo usuário, uma `TextView` será atualizada com a data que foi selecionada.

[![captura de tela do botão data de escolha seguido pela caixa de diálogo Seletor de data](date-picker-images/image-01-sml.png)](date-picker-images/image-01.png#lightbox)

## <a name="requirements"></a>Requisitos

O aplicativo de exemplo para este guia tem como alvo o Android 4,1 (nível de API
16) ou superior, mas é aplicável ao Android 3,0 (API nível 11 ou superior). É possível dar suporte a versões mais antigas do Android com a adição da biblioteca de suporte do Android v4 ao projeto e algumas alterações de código.

## <a name="using-the-datepicker"></a>Usando o DatePicker

Este exemplo irá estender `DialogFragment`. A subclasse hospedará e exibirá uma `DatePickerDialog`:

![Caixa de diálogo closeup do seletor de data](date-picker-images/image-02.png)

Quando o usuário seleciona uma data e clica no botão **OK** , o `DatePickerDialog` chamará o método [`IOnDateSetListener.OnDateSet`](xref:Android.App.DatePickerDialog.IOnDateSetListener.OnDateSet*).
Essa interface é implementada pelo `DialogFragment`de hospedagem. Se o usuário clicar no botão **Cancelar** , então o fragmento e a caixa de diálogo serão descartados.

Há várias maneiras pelas quais o `DialogFragment` pode retornar a data selecionada para a atividade de hospedagem:

1. **Invocar um método ou definir uma propriedade** &ndash; a atividade pode fornecer uma propriedade ou método especificamente para definir esse valor.

2. **Gerar um evento** &ndash; o `DialogFragment` pode definir um evento que será gerado quando `OnDateSet` for invocado.

3. **Use um `Action`** &ndash; o `DialogFragment` pode invocar um `Action<DateTime>` para exibir a data na atividade. A atividade fornecerá o `Action<DateTime` ao instanciar o `DialogFragment`. Este exemplo usará a terceira técnica e exigirá que a atividade forneça um `Action<DateTime>` ao `DialogFragment`.

### <a name="extending-dialogfragment"></a>Estendendo DialogFragment

A primeira etapa na exibição de uma `DatePickerDialog` é a subclasse `DialogFragment` e fazer com que ela implemente a interface `IOnDateSetListener`:

```csharp
public class DatePickerFragment : DialogFragment, 
                                  DatePickerDialog.IOnDateSetListener
{
    // TAG can be any string of your choice.
    public static readonly string TAG = "X:" + typeof (DatePickerFragment).Name.ToUpper();
    
    // Initialize this value to prevent NullReferenceExceptions.
    Action<DateTime> _dateSelectedHandler = delegate { };
    
    public static DatePickerFragment NewInstance(Action<DateTime> onDateSelected)
    {
        DatePickerFragment frag = new DatePickerFragment();
        frag._dateSelectedHandler = onDateSelected;
        return frag;
    }
    
    public override Dialog OnCreateDialog(Bundle savedInstanceState)
    {
        DateTime currently = DateTime.Now;
        DatePickerDialog dialog = new DatePickerDialog(Activity, 
                                                       this, 
                                                       currently.Year, 
                                                       currently.Month - 1,
                                                       currently.Day);
        return dialog;
    }
    
    public void OnDateSet(DatePicker view, int year, int monthOfYear, int dayOfMonth)
    {
        // Note: monthOfYear is a value between 0 and 11, not 1 and 12!
        DateTime selectedDate = new DateTime(year, monthOfYear + 1, dayOfMonth);
        Log.Debug(TAG, selectedDate.ToLongDateString());
        _dateSelectedHandler(selectedDate);
    }
}
```

O método `NewInstance` é invocado para criar uma instância de um novo `DatePickerFragment`. Esse método usa um `Action<DateTime>` que será invocado quando o usuário clicar no botão **OK** na `DatePickerDialog`.

Quando o fragmento for exibido, o Android chamará o método `OnCreateDialog`. Esse método criará um novo objeto `DatePickerDialog` e o inicializará com a data atual e o objeto de retorno de chamada (que é a instância atual do `DatePickerFragment`).

> [!NOTE]
> Lembre-se de que o valor do mês em que `IOnDateSetListener.OnDateSet` é invocado está no intervalo de 0 a 11, e não de 1 a 12. O dia do mês estará no intervalo de 1 a 31 (dependendo de qual mês foi selecionado).

### <a name="showing-the-datepickerfragment"></a>Mostrando o DatePickerFragment

Agora que o `DialogFragment` foi implementado, esta seção examinará como usar o fragmento em uma atividade. No aplicativo de exemplo que acompanha este guia, a atividade criará uma instância do `DialogFragment` usando o método `NewInstance` Factory e, em seguida, o exibirá como Invoke `DialogFragment.Show`. Como parte da instanciação da `DialogFragment`, a atividade passa um `Action<DateTime>`, que exibirá a data em uma `TextView` que é hospedada pela atividade:

```csharp
[Activity(Label = "@string/app_name", MainLauncher = true, Icon = "@drawable/icon")]
public class MainActivity : Activity
{
    TextView _dateDisplay;
    Button _dateSelectButton;

    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);
        SetContentView(Resource.Layout.Main);

        _dateDisplay = FindViewById<TextView>(Resource.Id.date_display);
        _dateSelectButton = FindViewById<Button>(Resource.Id.date_select_button);
        _dateSelectButton.Click += DateSelect_OnClick;
    }

    void DateSelect_OnClick(object sender, EventArgs eventArgs)
    {
        DatePickerFragment frag = DatePickerFragment.NewInstance(delegate(DateTime time)
                                                                 {
                                                                     _dateDisplay.Text = time.ToLongDateString();
                                                                 });
        frag.Show(FragmentManager, DatePickerFragment.TAG);
    }
}
```

## <a name="summary"></a>Resumo

Este exemplo abordou como exibir um widget de `DatePicker` como uma caixa de diálogo modal pop-up como parte de uma atividade do Android. Ele forneceu uma implementação de exemplo de DialogFragment e abordou a interface `IOnDateSetListener`. Este exemplo também demonstrou como o DialogFragment pode interagir com a atividade do host para exibir a data selecionada.

## <a name="related-links"></a>Links relacionados

- [DialogFragment](xref:Android.App.DialogFragment)
- [DatePicker](xref:Android.Widget.DatePicker)
- [DatePickerDialog](xref:Android.App.DatePickerDialog)
- [DatePickerDialog.IOnDateSetListener](xref:Android.App.DatePickerDialog.IOnDateSetListener)
- [Selecione uma data](https://github.com/xamarin/recipes/tree/master/Recipes/android/controls/datepicker/select_a_date)
