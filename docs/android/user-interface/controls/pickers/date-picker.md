---
title: Seletor de data
description: Seleção de datas do calendário usando o DatePickerDialog e DialogFragment
ms.prod: xamarin
ms.assetid: F2BCD8D4-8957-EA53-C5A8-6BB603ADB47B
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 01/22/2018
ms.openlocfilehash: 8f4e6318d904efb2f77c36732fc6519699f72ac9
ms.sourcegitcommit: b56b3f906d2c05a3f1be219ef41be8b79e519b8e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2018
ms.locfileid: "39241130"
---
# <a name="date-picker"></a>Seletor de data

## <a name="overview"></a>Visão geral

Há ocasiões em que, quando um usuário deve inserir dados em um aplicativo do Android. Para ajudar com isso, a estrutura do Android fornece as [ `DatePicker` ](https://developer.xamarin.com/api/type/Android.Widget.DatePicker/) widget e o [ `DatePickerDialog` ](https://developer.xamarin.com/api/type/Android.App.DatePickerDialog/) . O `DatePicker` permite que os usuários selecionem o ano, mês e dia em uma interface consistente entre dispositivos e aplicativos. O `DatePickerDialog` é uma classe auxiliar que encapsula o `DatePicker` em uma caixa de diálogo.

Aplicativos Android modernos devem exibir o `DatePickerDialog` em um [ `DialogFragment` ](https://developer.xamarin.com/api/type/Android.App.DialogFragment/). Isso permitirá que um aplicativo exibir o DatePicker como uma caixa de diálogo pop-up ou inserida em uma atividade. Além disso, o `DialogFragment` irá gerenciar o ciclo de vida e a exibição da caixa de diálogo, reduzindo a quantidade de código que deve ser implementado.

Este guia demonstra como usar o `DatePickerDialog`encapsulada em um `DialogFragment`. O aplicativo de exemplo exibirá o `DatePickerDialog` como uma caixa de diálogo modal quando o usuário clica em um botão em uma atividade. Quando a data é definida pelo usuário, um `TextView` será atualizado com a data em que foi selecionada.

[![Botão de captura de tela de escolher data seguido de caixa de diálogo do seletor de data](date-picker-images/image-01-sml.png)](date-picker-images/image-01.png#lightbox)

## <a name="requirements"></a>Requisitos

O aplicativo de exemplo para este guia tem como alvo o Android 4.1 (nível da API
16) ou superior, mas é aplicável a 3.0 Android (API nível 11 ou superior). É possível oferecer suporte a versões mais antigas do Android com a adição de v4 a biblioteca do Android dão suporte ao projeto e algumas alterações de código.

## <a name="using-the-datepicker"></a>Usando o DatePicker

Este exemplo estenderá `DialogFragment`. A subclasse hospedará e exibir um `DatePickerDialog`:

![Caixa de diálogo Close-up de seletor de data](date-picker-images/image-02.png)

Quando o usuário seleciona uma data e clica o **Okey** botão, o `DatePickerDialog` chamará o método [ `IOnDateSetListener.OnDateSet` ](https://developer.xamarin.com/api/member/Android.App.DatePickerDialog+IOnDateSetListener.OnDateSet/p/Android.Widget.DatePicker/System.Int32/System.Int32/System.Int32/).
Essa interface é implementada pelo hospeda `DialogFragment`. Se o usuário clica o **Cancelar** botão, em seguida, o fragmento e a caixa de diálogo serão descartar a mesmos.

Há várias maneiras de `DialogFragment` pode retornar a data selecionada para a atividade de hospedagem:

1. **Invocar um método ou definir uma propriedade** &ndash; a atividade pode fornecer uma propriedade ou método especificamente para definir esse valor.

2. **Gerar um evento** &ndash; as `DialogFragment` pode definir um evento que será acionado quando `OnDateSet` é invocado.

3. **Use uma `Action`**  &ndash; o `DialogFragment` pode invocar um `Action<DateTime>` para exibir a data em que a atividade. A atividade fornecerá a `Action<DateTime` ao instanciar o `DialogFragment`. Este exemplo usará a terceira técnica e exigir que a atividade forneça uma `Action<DateTime>` para o `DialogFragment`.



### <a name="extending-dialogfragment"></a>Estendendo DialogFragment

A primeira etapa na exibição de um `DatePickerDialog` é a subclasse `DialogFragment` e fazê-lo a implementar o `IOnDateSetListener` interface:

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

O `NewInstance` método é chamado para instanciar um novo `DatePickerFragment`. Esse método usa um `Action<DateTime>` que será chamado quando o usuário clica no **Okey** botão o `DatePickerDialog`.

Quando o fragmento deve ser exibido, o Android chamará o método `OnCreateDialog`. Esse método criará um novo `DatePickerDialog` do objeto e inicializá-lo com a data atual e o objeto de retorno de chamada (que é a instância atual do `DatePickerFragment`).


> [!NOTE]
> Lembre-se que o valor do mês quando `IOnDateSetListener.OnDateSet` é invocado está no intervalo de 0 a 11 e não de 1 a 12. O dia do mês será no intervalo de 1 a 31 (dependendo de qual mês foi selecionado).



### <a name="showing-the-datepickerfragment"></a>Mostrando o DatePickerFragment

Agora que o `DialogFragment` tiver sido implementado, esta seção examinará como usar o fragmento em uma atividade. No aplicativo de exemplo que acompanha este guia, a atividade instanciará o `DialogFragment` usando o `NewInstance` método de fábrica e exibição, em seguida, ele invoque `DialogFragment.Show`. Como parte de criar uma instância de `DialogFragment`, passa a atividade de um `Action<DateTime>`, que exibirá a data em um `TextView` que é hospedado pela atividade:

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

Este exemplo discutido como exibir um `DatePicker` widget como uma caixa de diálogo modal pop-up como parte de uma atividade do Android. Ele fornecido um exemplo de implementação de DialogFragment e discutido o `IOnDateSetListener` interface. Este exemplo também demonstrou como o DialogFragment pode interagir com o host de atividade para exibir a data selecionada.


## <a name="related-links"></a>Links relacionados

- [DialogFragment](https://developer.xamarin.com/api/type/Android.App.DialogFragment/)
- [DatePicker](https://developer.xamarin.com/api/type/Android.Widget.DatePicker/)
- [DatePickerDialog](https://developer.xamarin.com/api/type/Android.App.DatePickerDialog/)
- [DatePickerDialog.IOnDateSetListener](https://developer.xamarin.com/api/type/Android.App.DatePickerDialog+IOnDateSetListener/)
- [Selecione uma data](https://github.com/xamarin/recipes/tree/master/Recipes/android/controls/datepicker/select_a_date)
