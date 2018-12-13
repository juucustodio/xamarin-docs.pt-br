---
title: Verificando o status da bateria
description: Este artigo explica como usar a classe DependencyService do Xamarin.Forms para acessar informações da bateria de modo nativo para cada plataforma.
ms.prod: xamarin
ms.assetid: CF1C5A73-84ED-407D-BDC5-EB1D83D2D3DB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/09/2016
ms.openlocfilehash: cbb4a01ac2c6d933fe40a0b3c2571d1fe3ce75c0
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998383"
---
# <a name="checking-battery-status"></a>Verificando o status da bateria

Este artigo descreve como criar um aplicativo que verifica o status da bateria. Este artigo se baseia o plug-in Battery de James Montemagno. Para obter mais informações, confira o [Repositório do GitHub](https://github.com/jamesmontemagno/Xamarin.Plugins/tree/master/Battery).

Como o Xamarin.Forms não inclui a funcionalidade para verificar o status atual da bateria, esse aplicativo precisará usar [`DependencyService`](xref:Xamarin.Forms.DependencyService) para tirar proveito de APIs nativas.  Este artigo abordará as etapas a seguir para usar `DependencyService`:

- **[Criando a Interface](#Creating_the_Interface)** &ndash; compreenda como a interface é criada em código compartilhado.
- **[Implementação de iOS](#iOS_Implementation)** &ndash; saiba como implementar a interface em código nativo para iOS.
- **[Implementação de Android](#Android_Implementation)** &ndash; saiba como implementar a interface em código nativo para Android.
- **[Implementação da Plataforma Universal do Windows](#UWPImplementation)** &ndash; saiba como implementar a interface em código nativo para a UWP (Plataforma Universal do Windows).
- **[Implementação em código compartilhado](#Implementing_in_Shared_Code)** &ndash; saiba como usar `DependencyService` para fazer chamadas na implementação nativa usando código compartilhado.

Quando concluído, o aplicativo que usa `DependencyService` terá a seguinte estrutura:

![](battery-info-images/battery-diagram.png "Estrutura de aplicativo de DependencyService")

<a name="Creating_the_Interface" />

## <a name="creating-the-interface"></a>Criando a Interface

Primeiro, crie no código compartilhado uma interface que expressa a funcionalidade desejada. No caso de um aplicativo de verificação de bateria, as informações relevantes são o percentual de bateria restante, se o dispositivo está sendo carregado ou não e como o dispositivo está recebendo energia:

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

A codificação nessa interface no código compartilhado permitirá que o aplicativo Xamarin.Forms acesse as APIs de gerenciamento de energia em cada plataforma.

> [!NOTE]
> Classes que implementam a interface devem ter um construtor sem parâmetros para trabalhar com `DependencyService`. Construtores não podem ser definidos pelas interfaces.

<a name="iOS_Implementation" />

## <a name="ios-implementation"></a>Implementação de iOS

A interface `IBattery` deve ser implementada em cada projeto de aplicativo específico da plataforma. A implementação do iOS usará as APIs de [`UIDevice`](https://developer.xamarin.com/api/type/UIKit.UIDevice/) nativas para acessar as informações da bateria. Observe que a classe a seguir tem um construtor sem parâmetros, de modo que `DependencyService` pode criar novas instâncias:

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

Por fim, adicione este atributo `[assembly]` acima da classe (e fora dos namespaces definidos), incluindo as instruções `using`:

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

Esse atributo registra a classe como uma implementação da Interface de `IBattery`, o que significa que `DependencyService.Get<IBattery>` pode ser usado em código compartilhado para criar uma instância dele:

<a name="Android_Implementation" />

## <a name="android-implementation"></a>Implementação de Android

A implementação do Android usa a API [`Android.OS.BatteryManager`](https://developer.xamarin.com/api/type/Android.OS.BatteryManager/). Essa implementação é mais complexa do que a versão do iOS, exigindo verificações para lidar com a falta de permissões da bateria:

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

Adicione este atributo `[assembly]` acima da classe (e fora dos namespaces definidos), incluindo as instruções `using`:

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

Esse atributo registra a classe como uma implementação da Interface de `IBattery`, o que significa que `DependencyService.Get<IBattery>` pode ser usado no código compartilhado para criar uma instância dele.

<a name="UWPImplementation" />

## <a name="universal-windows-platform-implementation"></a>Implementação na Plataforma Universal do Windows

A implementação da UWP usa as APIs de `Windows.Devices.Power` para obter informações sobre o status da bateria:

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

O atributo `[assembly]` acima da declaração de namespace registra a classe como uma implementação da interface de `IBattery`, o que significa que `DependencyService.Get<IBattery>` pode ser usado em código compartilhado para criar uma instância dele.

<a name="Implementing_in_Shared_Code" />

## <a name="implementing-in-shared-code"></a>Implementação em código compartilhado

Agora que a interface foi implementada para cada plataforma, o aplicativo compartilhado pode ser escrito de forma a tirar proveito dela. O aplicativo será composto por uma página com um botão que, quando tocado, atualiza seu texto com o status da bateria atual. Ele usa o `DependencyService` para obter uma instância da interface `IBattery`. Em tempo de execução, essa instância será a implementação específica da plataforma com acesso completo ao SDK nativo.

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

Executar esse aplicativo no iOS, no Android ou na UWP e pressionar o botão fará com que o texto do botão seja atualizado para refletir o status de alimentação atual do dispositivo.

![](battery-info-images/battery.png "Exemplo de status da bateria")


## <a name="related-links"></a>Links relacionados

- [DependencyService (amostra)](https://developer.xamarin.com/samples/DependencyService)
- [Usando o DependencyService (amostra)](https://developer.xamarin.com/samples/UsingDependencyService/)
- [Amostras do Xamarin.Forms](https://github.com/xamarin/xamarin-forms-samples)
