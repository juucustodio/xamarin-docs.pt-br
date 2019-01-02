---
title: Verificando a orientação do dispositivo
description: Este artigo explica como usar a classe DependencyService do Xamarin.Forms para acessar a orientação do dispositivo usando código compartilhado.
ms.prod: xamarin
ms.assetid: 5F60975F-47DB-4361-B97C-2290D6F77D2F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/09/2016
ms.openlocfilehash: a63d0d4c90bff36e55d98b731217133fe5958c1f
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2018
ms.locfileid: "53051465"
---
# <a name="checking-device-orientation"></a>Verificando a orientação do dispositivo

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://developer.xamarin.com/samples/UsingDependencyService)

Este artigo mostra como usar [`DependencyService`](xref:Xamarin.Forms.DependencyService) para verificar a orientação do dispositivo do código compartilhado usando as APIs nativas de cada plataforma. Este passo a passo se baseia no plug-in `DeviceOrientation` de Ali Özgür. Confira o [Repositório do GitHub](https://github.com/aliozgur/Xamarin.Plugins/tree/master/DeviceOrientation) para obter mais informações.

- **[Criando a Interface](#Creating_the_Interface)** &ndash; compreenda como a interface é criada em código compartilhado.
- **[Implementação de iOS](#iOS_Implementation)** &ndash; saiba como implementar a interface em código nativo para iOS.
- **[Implementação de Android](#Android_Implementation)** &ndash; saiba como implementar a interface em código nativo para Android.
- **[Implementação de UWP](#WindowsImplementation)** &ndash; saiba como implementar a interface em código nativo para a UWP (Plataforma Universal do Windows).
- **[Implementação em código compartilhado](#Implementing_in_Shared_Code)** &ndash; saiba como usar `DependencyService` para fazer chamadas na implementação nativa usando código compartilhado.

O aplicativo que usa `DependencyService` terá a seguinte estrutura:

![](device-orientation-images/orientation-diagram.png "Estrutura de aplicativo de DependencyService")

> [!NOTE]
> É possível detectar se o dispositivo está com orientação de retrato ou paisagem no código compartilhado, conforme demonstrado em [Orientação do Dispositivo](~/xamarin-forms/user-interface/layouts/device-orientation.md#Reacting_to_Changes_in_Orientation). O método descrito neste artigo usa recursos nativos para obter mais informações sobre a orientação, incluindo se o dispositivo está de cabeça para baixo.

<a name="Creating_the_Interface" />

## <a name="creating-the-interface"></a>Criando a Interface

Primeiro, crie no código compartilhado uma interface que expressa a funcionalidade que você planeja implementar. Neste exemplo, a interface contém um único método:

```csharp
namespace DependencyServiceSample.Abstractions
{
    public enum DeviceOrientations
    {
        Undefined,
        Landscape,
        Portrait
    }

    public interface IDeviceOrientation
    {
        DeviceOrientations GetOrientation();
    }
}
```

A codificação nessa interface no código compartilhado permitirá que o aplicativo Xamarin.Forms acesse as APIs de orientação do dispositivo em cada plataforma.

> [!NOTE]
> Classes que implementam a interface devem ter um construtor sem parâmetros para trabalhar com `DependencyService`.

<a name="iOS_Implementation" />

## <a name="ios-implementation"></a>Implementação de iOS

A Interface deve ser implementada em cada projeto de aplicativo específico da plataforma. Observe que a classe tem um construtor sem parâmetros, de modo que `DependencyService` pode criar novas instâncias:

```csharp
using UIKit;
using Foundation;

namespace DependencyServiceSample.iOS
{
    public class DeviceOrientationImplementation : IDeviceOrientation
    {
        public DeviceOrientationImplementation(){ }

        public DeviceOrientations GetOrientation()
        {
            var currentOrientation = UIApplication.SharedApplication.StatusBarOrientation;
            bool isPortrait = currentOrientation == UIInterfaceOrientation.Portrait
                || currentOrientation == UIInterfaceOrientation.PortraitUpsideDown;

            return isPortrait ? DeviceOrientations.Portrait: DeviceOrientations.Landscape;
        }
    }
}
```

Por fim, adicione este atributo `[assembly]` acima da classe (e fora dos namespaces definidos), incluindo as instruções `using`:

```csharp
using UIKit;
using Foundation;
using DependencyServiceSample.iOS; //enables registration outside of namespace

[assembly: Xamarin.Forms.Dependency (typeof (DeviceOrientationImplementation))]
namespace DependencyServiceSample.iOS {
    ...
```

Esse atributo registra a classe como uma implementação da Interface de `IDeviceOrientation`, o que significa que `DependencyService.Get<IDeviceOrientation>` pode ser usado no código compartilhado para criar uma instância dele.

<a name="Android_Implementation" />

## <a name="android-implementation"></a>Implementação de Android

O código a seguir implementa `IDeviceOrientation` no Android:

```csharp
using DependencyServiceSample.Droid;
using Android.Hardware;

namespace DependencyServiceSample.Droid
{
    public class DeviceOrientationImplementation : IDeviceOrientation
    {
        public DeviceOrientationImplementation() { }

        public static void Init() { }

        public DeviceOrientations GetOrientation()
        {
            IWindowManager windowManager = Android.App.Application.Context.GetSystemService(Context.WindowService).JavaCast<IWindowManager>();

            var rotation = windowManager.DefaultDisplay.Rotation;
            bool isLandscape = rotation == SurfaceOrientation.Rotation90 || rotation == SurfaceOrientation.Rotation270;
            return isLandscape ? DeviceOrientations.Landscape : DeviceOrientations.Portrait;
        }
    }
}
```

Adicione este atributo `[assembly]` acima da classe (e fora dos namespaces definidos), incluindo as instruções `using`:

```csharp
using DependencyServiceSample.Droid; //enables registration outside of namespace
using Android.Hardware;

[assembly: Xamarin.Forms.Dependency (typeof (DeviceOrientationImplementation))]
namespace DependencyServiceSample.Droid {
    ...
```

Esse atributo registra a classe como uma implementação da Interface de `IDeviceOrientaiton`, o que significa que `DependencyService.Get<IDeviceOrientation>` pode ser usado no código compartilhado para criar uma instância dele.

<a name="WindowsImplementation" />

## <a name="universal-windows-platform-implementation"></a>Implementação na Plataforma Universal do Windows

O código a seguir implementa a interface `IDeviceOrientation` na Plataforma Universal do Windows:

```csharp
namespace DependencyServiceSample.WindowsPhone
{
    public class DeviceOrientationImplementation : IDeviceOrientation
    {
        public DeviceOrientationImplementation() { }

        public DeviceOrientations GetOrientation()
        {
            var orientation = Windows.UI.ViewManagement.ApplicationView.GetForCurrentView().Orientation;
            if (orientation == Windows.UI.ViewManagement.ApplicationViewOrientation.Landscape) {
                return DeviceOrientations.Landscape;
            }
            else {
                return DeviceOrientations.Portrait;
            }
        }
    }
}
```

Adicione o atributo `[assembly]` acima da classe (e fora dos namespaces definidos), incluindo as instruções `using`:

```csharp
using DependencyServiceSample.WindowsPhone; //enables registration outside of namespace

[assembly: Dependency(typeof(DeviceOrientationImplementation))]
namespace DependencyServiceSample.WindowsPhone {
    ...
```

Esse atributo registra a classe como uma implementação da Interface de `DeviceOrientationImplementation`, o que significa que `DependencyService.Get<IDeviceOrientation>` pode ser usado no código compartilhado para criar uma instância dele.

<a name="Implementing_in_Shared_Code" />

## <a name="implementing-in-shared-code"></a>Implementação em código compartilhado

Agora, podemos escrever e testar o código compartilhado que acessa a interface `IDeviceOrientation`. Esta página simples inclui um botão que atualiza seu próprio texto com base na orientação do dispositivo. Ela usa `DependencyService` para obter uma instância da interface &ndash; de `IDeviceOrientation` em tempo de execução. Essa instância será a implementação específica da plataforma com acesso completo ao SDK nativo:

```csharp
public MainPage ()
{
    var orient = new Button {
        Text = "Get Orientation",
        VerticalOptions = LayoutOptions.CenterAndExpand,
        HorizontalOptions = LayoutOptions.CenterAndExpand,
    };
    orient.Clicked += (sender, e) => {
       var orientation = DependencyService.Get<IDeviceOrientation>().GetOrientation();
       switch(orientation){
           case DeviceOrientations.Undefined:
                orient.Text = "Undefined";
                break;
           case DeviceOrientations.Landscape:
                orient.Text = "Landscape";
                break;
           case DeviceOrientations.Portrait:
                orient.Text = "Portrait";
                break;
       }
    };
    Content = orient;
}
```

Executar esse aplicativo no iOS, no Android ou na plataformas Windows e pressionar o botão fará com que o texto do botão seja atualizado com a orientação do dispositivo.

![](device-orientation-images/orientation.png "Exemplo de orientação do dispositivo")


## <a name="related-links"></a>Links relacionados

- [Usando o DependencyService (amostra)](https://developer.xamarin.com/samples/UsingDependencyService)
- [DependencyService (amostra)](https://developer.xamarin.com/samples/DependencyService/DependencyServiceSample/)
- [Amostras do Xamarin.Forms](https://github.com/xamarin/xamarin-forms-samples)
