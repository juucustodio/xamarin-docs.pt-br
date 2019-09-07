---
title: Seletor de tempo
description: Selecionando uma hora usando TimePickerDialog e DialogFragment
ms.prod: xamarin
ms.assetid: EB4E8206-E8AD-9F04-AC1C-82AC9364A9DD
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 1f55e5d97058d07926233e8b116663e6674a441c
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70764951"
---
# <a name="android-time-picker"></a>Seletor de tempo do Android

Para fornecer uma maneira para o usuário selecionar uma hora, você pode usar o [timeseparar](xref:Android.Widget.TimePicker). Aplicativos Android normalmente usam `TimePicker` com [TimePickerDialog](xref:Android.App.TimePickerDialog) para selecionar um valor &ndash; de tempo isso ajuda a garantir uma interface consistente entre dispositivos e aplicativos. `TimePicker`permite que os usuários selecionem a hora do dia no modo AM/PM de 24 horas ou 12 horas.
`TimePickerDialog`é uma classe auxiliar que encapsula o `TimePicker` em uma caixa de diálogo.

[![Captura de tela de exemplo da caixa de diálogo Seletor de tempo em ação](time-picker-images/01-example-screen-sml.png)](time-picker-images/01-example-screen.png#lightbox)

## <a name="overview"></a>Visão geral

Os aplicativos Android modernos exibem o `TimePickerDialog` em um [DialogFragment](xref:Android.App.DialogFragment). Isso possibilita que um aplicativo exiba o como uma caixa `TimePicker` de diálogo pop-up ou incorpore-o em uma atividade. Além disso, o `DialogFragment` gerencia o ciclo de vida e a exibição da caixa de diálogo, reduzindo a quantidade de código que deve ser implementada.

Este guia demonstra como usar o `TimePickerDialog`, encapsulado em um. `DialogFragment` O aplicativo de exemplo exibe `TimePickerDialog` o como uma caixa de diálogo modal quando o usuário clica em um botão em uma atividade. Quando a hora é definida pelo usuário, a caixa de diálogo é encerrada e um manipulador `TextView` atualiza um na tela de atividade com a hora selecionada.

## <a name="requirements"></a>Requisitos

O aplicativo de exemplo para este guia tem como alvo o Android 4,1 (nível de API
16) ou superior, mas pode ser usado com Android 3,0 (API nível 11 ou superior). É possível dar suporte a versões mais antigas do Android com a adição da biblioteca de suporte do Android v4 ao projeto e algumas alterações de código.

## <a name="using-the-timepicker"></a>Usando o timeseparar

Este exemplo estende `DialogFragment`; a implementação de subclasse de `DialogFragment` (chamada `TimePickerFragment` abaixo) hospeda e exibe um `TimePickerDialog`. Quando o aplicativo de exemplo é iniciado pela primeira vez, ele exibe um botão de `TextView` seleção de **tempo** acima de a que será usado para exibir a hora selecionada:

[![Tela inicial do aplicativo de exemplo](time-picker-images/02-initial-app-screen-sml.png)](time-picker-images/02-initial-app-screen.png#lightbox)

Quando você clica no botão **selecionar hora** , o aplicativo de exemplo inicia `TimePickerDialog` o como mostrado nesta captura de tela:

[![Captura de tela da caixa de diálogo Seletor de tempo padrão exibida pelo aplicativo](time-picker-images/03-am-pm-time-dialog-sml.png)](time-picker-images/03-am-pm-time-dialog.png#lightbox)

No, selecionar um horário e clicar no botão **OK** faz com que `TimePickerDialog` o chame o método [IOnTimeSetListener. ontimeset.](xref:Android.App.TimePickerDialog.IOnTimeSetListener.OnTimeSet*) `TimePickerDialog`
Essa interface é implementada pela hospedagem `DialogFragment` (`TimePickerFragment`, descrita abaixo). Clicar no botão **Cancelar** faz com que o fragmento e a caixa de diálogo sejam ignorados.

`DialogFragment`Retorna o tempo selecionado para a atividade de Hospedagem de uma das três maneiras:

1. **Invocando um método ou definindo uma propriedade** &ndash; A atividade pode fornecer uma propriedade ou método especificamente para definir esse valor.

2. **Gerando um evento** O pode definir um evento que será gerado quando `OnTimeSet` o for invocado. `DialogFragment` &ndash;

3. **Usar um `Action`**  &ndash; podeinvocar`Action<DateTime>` um para exibir a hora na atividade. `DialogFragment` A atividade fornecerá o `Action<DateTime` ao instanciar o `DialogFragment`.

Este exemplo usará a terceira técnica, que requer que a atividade forneça um `Action<DateTime>` manipulador para o `DialogFragment`.

## <a name="start-an-app-project"></a>Iniciar um projeto de aplicativo

Inicie um novo projeto Android chamado **TimePickerDemo** (se você não estiver familiarizado com a criação de projetos Xamarin. Android, consulte [Olá, Android](~/android/get-started/hello-android/hello-android-quickstart.md) para saber como criar um novo projeto).

Edite **Resources/layout/Main. axml** e substitua seu conteúdo pelo XML a seguir:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:layout_gravity="center_horizontal"
    android:padding="16dp">
    <Button
        android:id="@+id/select_button"
        android:paddingLeft="24dp"
        android:paddingRight="24dp"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="PICK TIME"
        android:textSize="20dp" />
    <TextView
        android:id="@+id/time_display"
        android:layout_height="wrap_content"
        android:layout_width="match_parent"
        android:paddingTop="22dp"
        android:text="Picked time will be displayed here"
        android:textSize="24dp" />
</LinearLayout>
```

Esse é um [LinearLayout](xref:Android.Widget.LinearLayout) básico com um [TextView](xref:Android.Widget.TextView) que exibe a hora e um [botão](xref:Android.Widget.Button) que abre o `TimePickerDialog`. Observe que esse layout usa cadeias de caracteres e dimensões embutidas em código para tornar o aplicativo mais simples e &ndash; mais fácil de entender um aplicativo de produção normalmente usa recursos para esses valores (como pode ser visto no exemplo de código [DatePicker](https://github.com/xamarin/recipes/blob/master/Recipes/android/controls/datepicker/select_a_date/Resources/layout/Main.axml) ).

Edite **MainActivity.cs** e substitua seu conteúdo pelo código a seguir:

```csharp
using Android.App;
using Android.Widget;
using Android.OS;
using System;
using Android.Util;
using Android.Text.Format;

namespace TimePickerDemo
{
    [Activity(Label = "TimePickerDemo", MainLauncher = true, Icon = "@drawable/icon")]
    public class MainActivity : Activity
    {
        TextView timeDisplay;
        Button timeSelectButton;

        protected override void OnCreate(Bundle bundle)
        {
            base.OnCreate(bundle);
            SetContentView(Resource.Layout.Main);
            timeDisplay = FindViewById<TextView>(Resource.Id.time_display);
            timeSelectButton = FindViewById<Button>(Resource.Id.select_button);
        }
    }
}
```

Ao compilar e executar este exemplo, você deverá ver uma tela inicial semelhante à captura de tela a seguir:

[![Tela inicial do aplicativo](time-picker-images/02-initial-app-screen-sml.png)](time-picker-images/02-initial-app-screen.png#lightbox)

Clicar no botão de **seleção de tempo** não faz `DialogFragment` nada porque o ainda não foi implementado para `TimePicker`exibir o.
A próxima etapa é criar isso `DialogFragment`.

## <a name="extending-dialogfragment"></a>Estendendo DialogFragment

Para estender `DialogFragment` para uso com `TimePicker`o, é necessário criar uma subclasse derivada de `DialogFragment` e implementa `TimePickerDialog.IOnTimeSetListener`. Adicione a seguinte classe a **MainActivity.cs**:

```csharp
public class TimePickerFragment : DialogFragment, TimePickerDialog.IOnTimeSetListener
{
    public static readonly string TAG = "MyTimePickerFragment";
    Action<DateTime> timeSelectedHandler = delegate { };

    public static TimePickerFragment NewInstance(Action<DateTime> onTimeSelected)
    {
        TimePickerFragment frag = new TimePickerFragment();
        frag.timeSelectedHandler = onTimeSelected;
        return frag;
    }

    public override Dialog OnCreateDialog (Bundle savedInstanceState)
    {
        DateTime currentTime = DateTime.Now;
        bool is24HourFormat = DateFormat.Is24HourFormat(Activity);
        TimePickerDialog dialog = new TimePickerDialog
            (Activity, this, currentTime.Hour, currentTime.Minute, is24HourFormat);
        return dialog;
    }

    public void OnTimeSet(TimePicker view, int hourOfDay, int minute)
    {
        DateTime currentTime = DateTime.Now;
        DateTime selectedTime = new DateTime(currentTime.Year, currentTime.Month, currentTime.Day, hourOfDay, minute, 0);
        Log.Debug(TAG, selectedTime.ToLongTimeString());
        timeSelectedHandler (selectedTime);
    }
}
```

Essa `TimePickerFragment` classe é dividida em partes menores e explicada na próxima seção.

### <a name="dialogfragment-implementation"></a>Implementação de DialogFragment

`TimePickerFragment`implementa vários métodos: um método de fábrica, um método de instanciação de caixa `OnTimeSet` de diálogo e o `TimePickerDialog.IOnTimeSetListener`método de manipulador exigido pelo.

- `TimePickerFragment`é uma subclasse de `DialogFragment`. Ele também implementa a `TimePickerDialog.IOnTimeSetListener` interface (ou seja, fornece o método necessário `OnTimeSet` ):

    ```csharp
    public class TimePickerFragment : DialogFragment, TimePickerDialog.IOnTimeSetListener
    ```

- `TAG`é inicializado para fins de log (*MyTimePickerFragment* pode ser alterado para qualquer cadeia de caracteres que você queira usar). A `timeSelectedHandler` ação é inicializada para um delegado vazio para evitar exceções de referência nulas:

    ```csharp
    public static readonly string TAG = "MyTimePickerFragment";
    Action<DateTime> timeSelectedHandler = delegate { };
    ```

- O `NewInstance` método de fábrica é chamado para criar uma `TimePickerFragment`instância de um novo. Esse método usa um `Action<DateTime>` manipulador que é invocado quando o usuário clica no botão **OK** no `TimePickerDialog`:

    ```csharp
    public static TimePickerFragment NewInstance(Action<DateTime> onTimeSelected)
    {
        TimePickerFragment frag = new TimePickerFragment();
        frag.timeSelectedHandler = onTimeSelected;
        return frag;
    }
    ```

- Quando o fragmento deve ser exibido, o Android chama o `DialogFragment` método [OnCreateDialog](xref:Android.App.DialogFragment.OnCreateDialog*).
    Esse método cria um novo `TimePickerDialog` objeto e o inicializa com a atividade, o objeto de retorno de chamada (que é a instância `TimePickerFragment`atual do) e a hora atual:

    ```csharp
    public override Dialog OnCreateDialog (Bundle savedInstanceState)
    {
        DateTime currentTime = DateTime.Now;
        bool is24HourFormat = DateFormat.Is24HourFormat(Activity);
        TimePickerDialog dialog = new TimePickerDialog
            (Activity, this, currentTime.Hour, currentTime.Minute, is24HourFormat);
        return dialog;
    }
    ```

- Quando o usuário altera a configuração de hora na `TimePicker` caixa de diálogo `OnTimeSet` , o método é invocado. `OnTimeSet`Cria um `DateTime` objeto usando a data e as mesclagens atuais na hora (hora e minuto) selecionadas pelo usuário:

    ```csharp
    public void OnTimeSet(TimePicker view, int hourOfDay, int minute)
    {
        DateTime currentTime = DateTime.Now;
        DateTime selectedTime = new DateTime(currentTime.Year, currentTime.Month, currentTime.Day, hourOfDay, minute, 0);
    ```

- Esse `DateTime` objeto é passado para o `timeSelectedHandler` que é registrado com o `TimePickerFragment` objeto no momento da criação. `OnTimeSet`invoca esse manipulador para atualizar a exibição do tempo da atividade para a hora selecionada (esse manipulador é implementado na próxima seção):

    ```csharp
    timeSelectedHandler (selectedTime);
    ```

## <a name="displaying-the-timepickerfragment"></a>Exibindo o TimePickerFragment

Agora que o `DialogFragment` foi implementado, é hora de instanciar o `DialogFragment` usando o método `NewInstance` de fábrica e exibi-lo invocando [DialogFragment. show](xref:Android.App.DialogFragment.Show*):

Adicione o seguinte método a `MainActivity`:

```csharp
void TimeSelectOnClick (object sender, EventArgs eventArgs)
{
    TimePickerFragment frag = TimePickerFragment.NewInstance (
        delegate (DateTime time)
        {
            timeDisplay.Text = time.ToShortTimeString();
        });

    frag.Show(FragmentManager, TimePickerFragment.TAG);
}
```

Depois `TimeSelectOnClick` de instanciar `TimePickerFragment`um, ele cria e passa um delegado para um método anônimo que atualiza a exibição do tempo da atividade com o valor de tempo passado. Por fim, ele inicia `TimePicker` o fragmento de diálogo `DialogFragment.Show`(via) para `TimePicker` exibir o para o usuário.

No final do `OnCreate` método, adicione a seguinte linha para anexar o manipulador de eventos ao botão **selecionar tempo** que inicia a caixa de diálogo:

```csharp
timeSelectButton.Click += TimeSelectOnClick;
```

Quando o botão de **escolha de hora** for `TimeSelectOnClick` clicado, será chamado para `TimePicker` exibir o fragmento de diálogo para o usuário.

## <a name="try-it"></a>Experimente!

Compile e execute o aplicativo. Quando você clica no botão **selecionar hora** , o `TimePickerDialog` é exibido no formato de hora padrão para a atividade (nesse caso, o modo AM/PM de 12 horas):

[![A caixa de diálogo de tempo é exibida no modo AM/PM](time-picker-images/03-am-pm-time-dialog-sml.png)](time-picker-images/03-am-pm-time-dialog.png#lightbox)
   
Quando você clica em **OK** na `TimePicker` caixa de diálogo, o manipulador `TextView` atualiza a atividade com a hora escolhida e, em seguida, sai:

[![A hora de a/M é exibida na atividade TextView](time-picker-images/04-after-time-dialog-sml.png)](time-picker-images/04-after-time-dialog.png#lightbox)

Em seguida, adicione a seguinte linha de código `OnCreateDialog` para imediatamente `is24HourFormat` após ser declarada e inicializada:

```csharp
is24HourFormat = true;
```

Essa alteração força o sinalizador passado para o `TimePickerDialog` Construtor `true` para que o modo de 24 horas seja usado em vez do formato de hora da atividade de hospedagem. Quando você criar e executar o aplicativo novamente, clique no botão **selecionar hora** , a `TimePicker` caixa de diálogo será exibida no formato de 24 horas:

[![Caixa de diálogo timeseparar no formato de 24 horas](time-picker-images/05-24hr-time-dialog-sml.png)](time-picker-images/05-24hr-time-dialog.png#lightbox)

Como o manipulador chama [DateTime. ToShortTimeString](xref:System.DateTime.ToShortDateString*) para imprimir a hora para a atividade `TextView`, a hora ainda é impressa no formato AM/PM padrão de 12 horas.

## <a name="summary"></a>Resumo

Este artigo explicou como exibir um `TimePicker` widget como uma caixa de diálogo modal Popup de uma atividade do Android. Ele fornecia uma `DialogFragment` implementação de exemplo e `IOnTimeSetListener` discutia a interface. Este exemplo também demonstrou como o `DialogFragment` pode interagir com a atividade do host para exibir a hora selecionada.

## <a name="related-links"></a>Links relacionados

- [DialogFragment](xref:Android.App.DialogFragment)
- [TimePicker](xref:Android.Widget.TimePicker)
- [TimePickerDialog](xref:Android.App.TimePickerDialog)
- [TimePickerDialog.IOnTimeSetListener](xref:Android.App.TimePickerDialog.IOnTimeSetListener)
- [TimePickerDemo (exemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/userinterface-timepickerdemo)
