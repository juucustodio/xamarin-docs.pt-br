---
title: Seletor de tempo
description: Selecionar uma hora usando TimePickerDialog e DialogFragment
ms.prod: xamarin
ms.assetid: EB4E8206-E8AD-9F04-AC1C-82AC9364A9DD
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: c4261e3dccaccc4c88afe9c1033fb16b730fea6e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
ms.locfileid: "30769891"
---
# <a name="time-picker"></a>Seletor de tempo

Para fornecer uma maneira para que o usuário selecione uma hora, você pode usar [TimePicker](https://developer.xamarin.com/api/type/Android.Widget.TimePicker/). Aplicativos do Android normalmente usam `TimePicker` com [TimePickerDialog](https://developer.xamarin.com/api/type/Android.App.TimePickerDialog/) para selecionar um valor de hora &ndash; ajuda a garantir uma interface consistente entre dispositivos e aplicativos. `TimePicker` permite que os usuários selecionem a hora do dia no modo de AM/PM de 12 horas ou 24 horas.
`TimePickerDialog` é uma classe auxiliar que encapsula o `TimePicker` em uma caixa de diálogo.

[![Captura de tela de exemplo da caixa de diálogo Seletor de tempo em ação](time-picker-images/01-example-screen-sml.png)](time-picker-images/01-example-screen.png#lightbox)

## <a name="overview"></a>Visão geral

Exibem os aplicativos modernos Android a `TimePickerDialog` em uma [DialogFragment](https://developer.xamarin.com/api/type/Android.App.DialogFragment/). Isso torna possível para um aplicativo para exibir o `TimePicker` como uma caixa de diálogo pop-up ou incorporá-lo em uma atividade. Além disso, o `DialogFragment` gerencia o ciclo de vida e a exibição da caixa de diálogo, reduzindo a quantidade de código que deve ser implementada.

Este guia demonstra como usar o `TimePickerDialog`, encapsulado em um `DialogFragment`. O aplicativo de exemplo exibe o `TimePickerDialog` como uma caixa de diálogo restrita quando o usuário clica em um botão em uma atividade. Quando a hora é definida pelo usuário, a caixa de diálogo será encerrado e atualiza um manipulador de um `TextView` na tela de atividade com a hora em que foi selecionada.

## <a name="requirements"></a>Requisitos

O aplicativo de exemplo para este guia tem como alvo o Android 4.1 (API nível
16) ou superior, mas pode ser usado com Android 3.0 (API nível 11 ou superior). É possível dar suporte a versões mais antigas do Android com a adição de v4 a biblioteca de suporte Android para o projeto e algumas alterações de código.

## <a name="using-the-timepicker"></a>Usando o TimePicker

Este exemplo estende `DialogFragment`; a implementação de subclasse de `DialogFragment` (chamado `TimePickerFragment` abaixo) hospeda e exibe um `TimePickerDialog`. Quando o aplicativo de exemplo é iniciado pela primeira vez, ele exibe um **PICK tempo** acima do botão um `TextView` que será usado para exibir o tempo selecionado:

[![Tela do aplicativo de exemplo inicial](time-picker-images/02-initial-app-screen-sml.png)](time-picker-images/02-initial-app-screen.png#lightbox)

Quando você clica o **PICK tempo** botão, o aplicativo de exemplo iniciar o `TimePickerDialog` como visto nesta captura de tela:

[![Captura de tela da caixa de diálogo Seletor de tempo padrão exibida pelo aplicativo](time-picker-images/03-am-pm-time-dialog-sml.png)](time-picker-images/03-am-pm-time-dialog.png#lightbox)

No `TimePickerDialog`, selecionando um tempo e clicando o **Okey** botão faz com que o `TimePickerDialog` para invocar o método [IOnTimeSetListener.OnTimeSet](https://developer.xamarin.com/api/member/Android.App.TimePickerDialog+IOnTimeSetListener.OnTimeSet/p/Android.Widget.TimePicker/System.Int32/System.Int32/System.Int32/).
Essa interface é implementada pelo hospeda `DialogFragment` (`TimePickerFragment`, descrito abaixo). Clique o **Cancelar** botão faz com que o fragmento e a caixa de diálogo a ser descartado.

`DialogFragment` Retorna o tempo selecionado para a hospedagem Actvity em uma das três maneiras:

1. **Invocando um método ou definindo a propriedade** &ndash; a atividade pode fornecer uma propriedade ou método especificamente para definir esse valor.

2. **Gerar um evento** &ndash; o `DialogFragment` pode definir um evento que será gerado quando `OnTimeSet` é invocado.

3. **Usando um `Action`**  &ndash; o `DialogFragment` pode invocar um `Action<DateTime>` para exibir a hora em que a atividade. A atividade fornecerá o `Action<DateTime` ao instanciar o `DialogFragment`. 

Este exemplo usará a terceira técnica, que exige que a fonte de atividade um `Action<DateTime>` manipulador para o `DialogFragment`.



## <a name="start-an-app-project"></a>Iniciar um projeto de aplicativo

Iniciar um novo projeto Android chamado **TimePickerDemo** (se você não estiver familiarizado com a criação de projetos de xamarin, consulte [Hello, Android](~/android/get-started/hello-android/hello-android-quickstart.md) para aprender a criar um novo projeto).

Editar **Resources/layout/Main.axml** e substitua o seu conteúdo com o seguinte XML:

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

Este é um basic [LinearLayout](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/) com um [TextView](https://developer.xamarin.com/api/type/Android.Widget.TextView/) que exibe a hora e um [botão](https://developer.xamarin.com/api/type/Android.Widget.Button/) que abre o `TimePickerDialog`. Observe que este layout usa cadeias de caracteres codificadas e as dimensões para tornar o aplicativo mais simples e mais fácil de entender &ndash; um aplicativo de produção normalmente usa recursos para esses valores (como pode ser visto no [DatePicker](https://github.com/xamarin/recipes/blob/master/android/controls/datepicker/select_a_date/Resources/layout/Main.axml) exemplo de código).

Editar **MainActivity.cs** e substitua o seu conteúdo com o código a seguir:

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

Quando você compilar e executar este exemplo, você verá uma tela inicial semelhante à captura de tela a seguir:

[![Tela inicial do aplicativo](time-picker-images/02-initial-app-screen-sml.png)](time-picker-images/02-initial-app-screen.png#lightbox)

Clicando o **PICK tempo** botão não faz nada porque o `DialogFragment` ainda não foi implementado para exibir o `TimePicker`.
A próxima etapa é criar isso `DialogFragment`.



## <a name="extending-dialogfragment"></a>Estendendo DialogFragment

Para estender `DialogFragment` para uso com `TimePicker`, é necessário criar uma subclasse que é derivada de `DialogFragment` e implementa `TimePickerDialog.IOnTimeSetListener`. Adicione a seguinte classe para **MainActivity.cs**:

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

Isso `TimePickerFragment` classe é dividido em partes menores e explicada na próxima seção.


### <a name="dialogfragment-implementation"></a>Implementação de DialogFragment

`TimePickerFragment` implementa vários métodos: um método de fábrica, um método de instanciação da caixa de diálogo e o `OnTimeSet` exigido pelo método do manipulador `TimePickerDialog.IOnTimeSetListener`.

-   `TimePickerFragment` é uma subclasse de `DialogFragment`. Ele também implementa o `TimePickerDialog.IOnTimeSetListener` interface (ou seja, ele fornece necessários `OnTimeSet` método):

    ```csharp
    public class TimePickerFragment : DialogFragment, TimePickerDialog.IOnTimeSetListener
    ```

-   `TAG` é inicializado para fins de registro (*MyTimePickerFragment* pode ser alterado a qualquer cadeia de caracteres que você deseja usar). O `timeSelectedHandler` ação é inicializada com um delegado vazio para evitar exceções de referência nula:

    ```csharp
    public static readonly string TAG = "MyTimePickerFragment";
    Action<DateTime> timeSelectedHandler = delegate { };
    ```

-   O `NewInstance` método de fábrica é chamado para instanciar um novo `TimePickerFragment`. Esse método usa um `Action<DateTime>` manipulador que é invocado quando o usuário clica o **Okey** no botão de `TimePickerDialog`:

    ```csharp
    public static TimePickerFragment NewInstance(Action<DateTime> onTimeSelected)
    {
        TimePickerFragment frag = new TimePickerFragment();
        frag.timeSelectedHandler = onTimeSelected;
        return frag;
    }
    ```

-   Quando o fragmento é exibido, Android chama o `DialogFragment` método [OnCreateDialog](https://developer.xamarin.com/api/member/Android.App.DialogFragment.OnCreateDialog/p/Android.OS.Bundle/). 
    Esse método cria um novo `TimePickerDialog` do objeto e a inicializa com a atividade, o objeto de retorno de chamada (que é a instância atual do `TimePickerFragment`) e a hora atual:

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

-   Quando o usuário altera a configuração de tempo no `TimePicker` caixa de diálogo, o `OnTimeSet` método é invocado. `OnTimeSet` cria um `DateTime` objeto usando a data atual e a mescla no tempo (hora e minuto) selecionada pelo usuário:

    ```csharp
    public void OnTimeSet(TimePicker view, int hourOfDay, int minute)
    {
        DateTime currentTime = DateTime.Now;
        DateTime selectedTime = new DateTime(currentTime.Year, currentTime.Month, currentTime.Day, hourOfDay, minute, 0);
    ```


-   Isso `DateTime` objeto é passado para o `timeSelectedHandler` que está registrado com o `TimePickerFragment` objeto no momento da criação. `OnTimeSet` invoca a esse manipulador para atualizar a exibição de hora da atividade para o tempo selecionado (esse manipulador é implementado na próxima seção):

    ```csharp
    timeSelectedHandler (selectedTime);
    ```



## <a name="displaying-the-timepickerfragment"></a>Exibindo o TimePickerFragment

Agora que o `DialogFragment` tiver sido implementado, é hora de criar uma instância a `DialogFragment` usando o `NewInstance` método de fábrica e exibi-lo ao chamar [DialogFragment.Show](https://developer.xamarin.com/api/member/Android.App.DialogFragment.Show/p/Android.App.FragmentManager/System.String/):

Adicione o seguinte método para `MainActivity`:

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

Depois de `TimeSelectOnClick` instancia um `TimePickerFragment`, ele cria e passa um representante de um método anônimo que atualiza a exibição de hora da atividade com o valor de tempo no passado. Finalmente, ele inicia o `TimePicker` fragmento da caixa de diálogo (por meio de `DialogFragment.Show`) para exibir o `TimePicker` para o usuário.

No final do `OnCreate` método, adicione a linha a seguir para anexar o manipulador de eventos para o **PICK tempo** botão que inicia a caixa de diálogo:

```csharp
timeSelectButton.Click += TimeSelectOnClick;
```

Quando o **PICK tempo** botão é clicado, `TimeSelectOnClick` será chamado para exibir o `TimePicker` fragmento da caixa de diálogo para o usuário.



## <a name="try-it"></a>Experimente!

Compile e execute o aplicativo. Quando você clica o **PICK tempo** botão, o `TimePickerDialog` é exibido no formato de hora padrão para a atividade (no modo de AM/PM neste caso, 12 horas):

[![Caixa de diálogo de hora é exibida no modo de AM/PM](time-picker-images/03-am-pm-time-dialog-sml.png)](time-picker-images/03-am-pm-time-dialog.png#lightbox)
   
Quando você clica em **Okey** no `TimePicker` caixa de diálogo, o manipulador de atualizações da atividade `TextView` com o tempo escolhido e, em seguida, fecha:

[![Hora da / M é exibida em TextView a atividade](time-picker-images/04-after-time-dialog-sml.png)](time-picker-images/04-after-time-dialog.png#lightbox)

Em seguida, adicione a seguinte linha de código para `OnCreateDialog` imediatamente após `is24HourFormat` é declarada e inicializado:

```csharp
is24HourFormat = true;
```

Essa alteração força o sinalizador passado para o `TimePickerDialog` construtor a ser `true` para que o modo de 24 horas é usado em vez do formato de hora da atividade de hospedagem. Quando você compilar e executa o aplicativo novamente, clique no **PICK tempo** botão, o `TimePicker` caixa de diálogo será exibida no formato de 24 horas:

[![Caixa de diálogo TimePicker no formato de 24 horas](time-picker-images/05-24hr-time-dialog-sml.png)](time-picker-images/05-24hr-time-dialog.png#lightbox)

Porque chama o manipulador [DateTime.ToShortTimeString](https://msdn.microsoft.com/en-us/library/system.datetime.toshortdatestring%28v=vs.110%29.aspx) para imprimir o tempo para a atividade `TextView`, o tempo ainda é impressa no formato padrão 12 horas AM/PM.



## <a name="summary"></a>Resumo

Este artigo explicou como exibir um `TimePicker` widget como uma caixa de diálogo modal pop-up de uma atividade Android. Ele fornecido um exemplo `DialogFragment` implementação e discutido a `IOnTimeSetListener` interface. Este exemplo também demonstrou como o `DialogFragment` pode interagir com o host de atividade para exibir o tempo selecionado.


## <a name="related-links"></a>Links relacionados

- [DialogFragment](https://developer.xamarin.com/api/type/Android.App.DialogFragment/)
- [TimePicker](https://developer.xamarin.com/api/type/Android.Widget.TimePicker/)
- [TimePickerDialog](https://developer.xamarin.com/api/type/Android.App.TimePickerDialog/)
- [TimePickerDialog.IOnTimeSetListener](https://developer.xamarin.com/api/type/Android.App.TimePickerDialog+IOnTimeSetListener/)
- [TimePickerDemo (sample)](https://developer.xamarin.com/samples/monodroid/UserInterface/TimePickerDemo)
