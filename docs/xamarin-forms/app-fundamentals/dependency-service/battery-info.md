---
title: Verificando o Status da bateria
description: Este artigo explica como usar a classe do xamarin. Forms DependencyService para acessar informações sobre a bateria nativamente para cada plataforma.
ms.prod: xamarin
ms.assetid: CF1C5A73-84ED-407D-BDC5-EB1D83D2D3DB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/09/2016
ms.openlocfilehash: cbb4a01ac2c6d933fe40a0b3c2571d1fe3ce75c0
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998383"
---
# <a name="checking-battery-status"></a>Verificando o Status da bateria

Este artigo orienta a criação de um aplicativo que verifica o status da bateria. Este artigo se baseia o plug-in de bateria por James Montemagno. Para obter mais informações, consulte o [repositório GitHub](https://github.com/jamesmontemagno/Xamarin.Plugins/tree/master/Battery).

Porque o xamarin. Forms não inclui a funcionalidade para verificar o status da bateria atual, esse aplicativo precisará usar [ `DependencyService` ](xref:Xamarin.Forms.DependencyService) para tirar proveito de APIs nativas.  Este artigo abordará as etapas a seguir para usar `DependencyService`:

- **[Criando a Interface](#Creating_the_Interface)**  &ndash; entender como a interface é criada em código compartilhado.
- **[Implementação do iOS](#iOS_Implementation)**  &ndash; Saiba como implementar a interface em código nativo para iOS.
- **[Implementação do Android](#Android_Implementation)**  &ndash; Saiba como implementar a interface em código nativo para Android.
- **[Implementação da plataforma Windows universal](#UWPImplementation)**  &ndash; Saiba como implementar a interface em código nativo para Universal Windows Platform (UWP).
- **[Implementando em código compartilhado](#Implementing_in_Shared_Code)**  &ndash; Saiba como usar `DependencyService` para chamar a implementação nativa a partir do código compartilhado.

Quando concluído, o aplicativo usando `DependencyService` terá a seguinte estrutura:

![](battery-info-images/battery-diagram.png "Estrutura de aplicativo do DependencyService")

<a name="Creating_the_Interface" />

## <a name="creating-the-interface"></a>Criando a Interface

Primeiro, crie uma interface no código compartilhado que expresse a funcionalidade desejada. No caso de uma bateria de verificação de aplicativo, as informações relevantes são a porcentagem de bateria restante, se o dispositivo está carregando ou não e como o dispositivo está recebendo energia:

```csharp
namespace DependencyServiceSample
{
  public enum BatteryStatus
  {
    Charging,
    Discharging,
    Full,
    NotCharging,
    Unknown
  }

  public enum PowerSource
  {
    Battery,
    Ac,
    Usb,
    Wireless,
    Other
  }

  public interface IBattery
  {
    int RemainingChargePercent { get; }
    BatteryStatus Status { get; }
    PowerSource PowerSource { get; }
  }
}
```

Codificação em relação a essa interface no código compartilhado permitirá que o aplicativo xamarin. Forms acessar as APIs de gerenciamento de energia em cada plataforma.

> [!NOTE]
> Classes que implementam a Interface devem ter um construtor sem parâmetros para trabalhar com o `DependencyService`. Construtores não podem ser definidos pelas interfaces.

<a name="iOS_Implementation" />

## <a name="ios-implementation"></a>Implementação do iOS

O `IBattery` interface deve ser implementada em cada projeto de aplicativo específico da plataforma. A implementação do iOS usará nativo [ `UIDevice` ](https://developer.xamarin.com/api/type/UIKit.UIDevice/) APIs para acessar as informações da bateria. Observe que a classe a seguir tem um construtor sem parâmetros, de modo que o `DependencyService` pode criar novas instâncias:

```csharp
using UIKit;
using Foundation;
using DependencyServiceSample.iOS;

namespace DependencyServiceSample.iOS
{
  public class BatteryImplementation : IBattery
  {
    public BatteryImplementation()
    {
      UIDevice.CurrentDevice.BatteryMonitoringEnabled = true;
    }

    public int RemainingChargePercent
    {
      get
      {
        return (int)(UIDevice.CurrentDevice.BatteryLevel * 100F);
      }
    }

    public BatteryStatus Status
    {
      get
      {
        switch (UIDevice.CurrentDevice.BatteryState)
        {
          case UIDeviceBatteryState.Charging:
            return BatteryStatus.Charging;
          case UIDeviceBatteryState.Full:
            return BatteryStatus.Full;
          case UIDeviceBatteryState.Unplugged:
            return BatteryStatus.Discharging;
          default:
            return BatteryStatus.Unknown;
        }
      }
    }

    public PowerSource PowerSource
    {
      get
      {
        switch (UIDevice.CurrentDevice.BatteryState)
        {
          case UIDeviceBatteryState.Charging:
            return PowerSource.Ac;
          case UIDeviceBatteryState.Full:
            return PowerSource.Ac;
          case UIDeviceBatteryState.Unplugged:
            return PowerSource.Battery;
          default:
            return PowerSource.Other;
        }
      }
    }
  }
}
```

Por fim, adicione isso `[assembly]` atributo acima da classe (e fora de todos os namespaces que foram definidos), incluindo quaisquer `using` instruções:

```csharp
using UIKit;
using Foundation;
using DependencyServiceSample.iOS;//necessary for registration outside of namespace

[assembly: Xamarin.Forms.Dependency (typeof (BatteryImplementation))]
namespace DependencyServiceSample.iOS
{
  public class BatteryImplementation : IBattery {
    ...
```

Este atributo registra a classe como uma implementação do `IBattery` interface, o que significa que `DependencyService.Get<IBattery>` pode ser usado para criar uma instância no código compartilhado:

<a name="Android_Implementation" />

## <a name="android-implementation"></a>Implementação do Android

A implementação do Android usa o [ `Android.OS.BatteryManager` ](https://developer.xamarin.com/api/type/Android.OS.BatteryManager/) API. Essa implementação é mais complexa do que a versão do iOS, que exigem verificações para lidar com a falta de permissões da bateria:

```csharp
using System;
using Android;
using Android.Content;
using Android.App;
using Android.OS;
using BatteryStatus = Android.OS.BatteryStatus;
using DependencyServiceSample.Droid;

namespace DependencyServiceSample.Droid
{
  public class BatteryImplementation : IBattery
  {
    private BatteryBroadcastReceiver batteryReceiver;
    public BatteryImplementation() { }

    public int RemainingChargePercent
    {
      get
      {
        try
        {
          using (var filter = new IntentFilter(Intent.ActionBatteryChanged))
          {
            using (var battery = Application.Context.RegisterReceiver(null, filter))
            {
              var level = battery.GetIntExtra(BatteryManager.ExtraLevel, -1);
              var scale = battery.GetIntExtra(BatteryManager.ExtraScale, -1);

              return (int)Math.Floor(level * 100D / scale);
            }
          }
        }
        catch
        {
          System.Diagnostics.Debug.WriteLine ("Ensure you have android.permission.BATTERY_STATS");
          throw;
        }

      }
    }

    public DependencyServiceSample.BatteryStatus Status
    {
      get
      {
        try
        {
          using (var filter = new IntentFilter(Intent.ActionBatteryChanged))
          {
            using (var battery = Application.Context.RegisterReceiver(null, filter))
            {
              int status = battery.GetIntExtra(BatteryManager.ExtraStatus, -1);
              var isCharging = status == (int)BatteryStatus.Charging || status == (int)BatteryStatus.Full;

              var chargePlug = battery.GetIntExtra(BatteryManager.ExtraPlugged, -1);
              var usbCharge = chargePlug == (int)BatteryPlugged.Usb;
              var acCharge = chargePlug == (int)BatteryPlugged.Ac;
              bool wirelessCharge = false;
              wirelessCharge = chargePlug == (int)BatteryPlugged.Wireless;

              isCharging = (usbCharge || acCharge || wirelessCharge);
              if (isCharging)
                return DependencyServiceSample.BatteryStatus.Charging;

              switch(status)
              {
                case (int)BatteryStatus.Charging:
                  return DependencyServiceSample.BatteryStatus.Charging;
                case (int)BatteryStatus.Discharging:
                  return DependencyServiceSample.BatteryStatus.Discharging;
                case (int)BatteryStatus.Full:
                  return DependencyServiceSample.BatteryStatus.Full;
                case (int)BatteryStatus.NotCharging:
                  return DependencyServiceSample.BatteryStatus.NotCharging;
                default:
                  return DependencyServiceSample.BatteryStatus.Unknown;
              }
            }
          }
        }
        catch
        {
          System.Diagnostics.Debug.WriteLine ("Ensure you have android.permission.BATTERY_STATS");
          throw;
        }
      }
    }

    public PowerSource PowerSource
    {
      get
      {
        try
        {
          using (var filter = new IntentFilter(Intent.ActionBatteryChanged))
          {
            using (var battery = Application.Context.RegisterReceiver(null, filter))
            {
              int status = battery.GetIntExtra(BatteryManager.ExtraStatus, -1);
              var isCharging = status == (int)BatteryStatus.Charging || status == (int)BatteryStatus.Full;

              var chargePlug = battery.GetIntExtra(BatteryManager.ExtraPlugged, -1);
              var usbCharge = chargePlug == (int)BatteryPlugged.Usb;
              var acCharge = chargePlug == (int)BatteryPlugged.Ac;

              bool wirelessCharge = false;
              wirelessCharge = chargePlug == (int)BatteryPlugged.Wireless;

              isCharging = (usbCharge || acCharge || wirelessCharge);

              if (!isCharging)
                return DependencyServiceSample.PowerSource.Battery;
              else if (usbCharge)
                return DependencyServiceSample.PowerSource.Usb;
              else if (acCharge)
                return DependencyServiceSample.PowerSource.Ac;
              else if (wirelessCharge)
                return DependencyServiceSample.PowerSource.Wireless;
              else
                return DependencyServiceSample.PowerSource.Other;
            }
          }
        }
        catch
        {
          System.Diagnostics.Debug.WriteLine ("Ensure you have android.permission.BATTERY_STATS");
          throw;
        }
      }
    }
  }
}
```

Adicione isso `[assembly]` atributo acima da classe (e fora de todos os namespaces que foram definidos), incluindo quaisquer `using` instruções:

```csharp
...
using BatteryStatus = Android.OS.BatteryStatus;
using DependencyServiceSample.Droid; //enables registration outside of namespace

[assembly: Xamarin.Forms.Dependency (typeof (BatteryImplementation))]
namespace DependencyServiceSample.Droid
{
  public class BatteryImplementation : IBattery {
    ...
```

Este atributo registra a classe como uma implementação do `IBattery` interface, o que significa que `DependencyService.Get<IBattery>` podem ser usados em código compartilhado pode criar uma instância dele.

<a name="UWPImplementation" />

## <a name="universal-windows-platform-implementation"></a>Implementação da plataforma universal do Windows

A implementação de UWP usa o `Windows.Devices.Power` APIs para obter informações sobre o status da bateria:

```csharp
using DependencyServiceSample.UWP;
using Xamarin.Forms;

[assembly: Dependency(typeof(BatteryImplementation))]
namespace DependencyServiceSample.UWP
{
    public class BatteryImplementation : IBattery
    {
        private BatteryStatus status = BatteryStatus.Unknown;
        Windows.Devices.Power.Battery battery;

        public BatteryImplementation()
        {
        }

        private Windows.Devices.Power.Battery DefaultBattery
        {
            get
            {
                return battery ?? (battery = Windows.Devices.Power.Battery.AggregateBattery);
            }
        }

        public int RemainingChargePercent
        {
            get
            {
                var finalReport = DefaultBattery.GetReport();
                var finalPercent = -1;

                if (finalReport.RemainingCapacityInMilliwattHours.HasValue && finalReport.FullChargeCapacityInMilliwattHours.HasValue)
                {
                    finalPercent = (int)((finalReport.RemainingCapacityInMilliwattHours.Value /
                        (double)finalReport.FullChargeCapacityInMilliwattHours.Value) * 100);
                }
                return finalPercent;
            }
        }

        public BatteryStatus Status
        {
            get
            {
                var report = DefaultBattery.GetReport();
                var percentage = RemainingChargePercent;

                if (percentage >= 1.0)
                {
                    status = BatteryStatus.Full;
                }
                else if (percentage < 0)
                {
                    status = BatteryStatus.Unknown;
                }
                else
                {
                    switch (report.Status)
                    {
                        case Windows.System.Power.BatteryStatus.Charging:
                            status = BatteryStatus.Charging;
                            break;
                        case Windows.System.Power.BatteryStatus.Discharging:
                            status = BatteryStatus.Discharging;
                            break;
                        case Windows.System.Power.BatteryStatus.Idle:
                            status = BatteryStatus.NotCharging;
                            break;
                        case Windows.System.Power.BatteryStatus.NotPresent:
                            status = BatteryStatus.Unknown;
                            break;
                    }
                }
                return status;
            }
        }

        public PowerSource PowerSource
        {
            get
            {
                if (status == BatteryStatus.Full || status == BatteryStatus.Charging)
                {
                    return PowerSource.Ac;
                }
                return PowerSource.Battery;
            }
        }
    }
}
```

O `[assembly]` atributo acima da declaração de namespace registra a classe como uma implementação do `IBattery` interface, o que significa que `DependencyService.Get<IBattery>` pode ser usado no código compartilhado para criar uma instância dele.

<a name="Implementing_in_Shared_Code" />

## <a name="implementing-in-shared-code"></a>Implementando em código compartilhado

Agora que a interface foi implementada para cada plataforma, o aplicativo de compartilhado pode ser escrito para tirar proveito dela. O aplicativo será composto de uma página com um botão que, quando tocado atualizações seu texto com o status da bateria atual. Ele usa o `DependencyService` para obter uma instância de `IBattery` interface. Em tempo de execução, essa instância será a implementação específica da plataforma que tem acesso completo para o SDK nativo.

```csharp
public MainPage ()
{
    var button = new Button {
        Text = "Click for battery info",
        VerticalOptions = LayoutOptions.CenterAndExpand,
        HorizontalOptions = LayoutOptions.CenterAndExpand,
    };
    button.Clicked += (sender, e) => {
        var bat = DependencyService.Get<IBattery>();

        switch (bat.PowerSource){
          case PowerSource.Battery:
            button.Text = "Battery - ";
            break;
          case PowerSource.Ac:
            button.Text = "AC - ";
            break;
          case PowerSource.Usb:
            button.Text = "USB - ";
            break;
          case PowerSource.Wireless:
            button.Text = "Wireless - ";
            break;
          case PowerSource.Other:
          default:
            button.Text = "Other - ";
            break;
        }
        switch (bat.Status){
          case BatteryStatus.Charging:
            button.Text += "Charging";
            break;
          case BatteryStatus.Discharging:
            button.Text += "Discharging";
            break;
          case BatteryStatus.NotCharging:
            button.Text += "Not Charging";
            break;
          case BatteryStatus.Full:
            button.Text += "Full";
            break;
          case BatteryStatus.Unknown:
          default:
            button.Text += "Unknown";
            break;
        }
    };
    Content = button;
}
```

Executando esse aplicativo no iOS, Android, ou UWP e pressionando o botão resultará no texto do botão sendo atualizada para refletir o status atual de energia do dispositivo.

![](battery-info-images/battery.png "Exemplo de Status da bateria")


## <a name="related-links"></a>Links relacionados

- [DependencyService (amostra)](https://developer.xamarin.com/samples/DependencyService)
- [Usando o DependencyService (amostra)](https://developer.xamarin.com/samples/UsingDependencyService/)
- [Amostras do Xamarin.Forms](https://github.com/xamarin/xamarin-forms-samples)
