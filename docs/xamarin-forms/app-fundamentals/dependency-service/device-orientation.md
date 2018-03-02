---
title: "Verificando a orientação do dispositivo"
description: "Use DependencyService para orientação do dispositivo de acesso de código compartilhado"
ms.topic: article
ms.prod: xamarin
ms.assetid: 5F60975F-47DB-4361-B97C-2290D6F77D2F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/09/2016
ms.openlocfilehash: ee91f0ebdc07f03831ae95a4b8ae6f85c3eb549e
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2018
---
# <a name="checking-device-orientation"></a>Verificando a orientação do dispositivo

Este artigo vai guiá-lo para usar [ `DependencyService` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DependencyService/) para verificar a orientação do dispositivo de código compartilhado com as APIs nativas em cada plataforma. Este passo a passo se baseia existente `DeviceOrientation` plug-in por Ali Özgür. Consulte o [repositório GitHub](https://github.com/aliozgur/Xamarin.Plugins/tree/master/DeviceOrientation) para obter mais informações.

- **[Criar a Interface](#Creating_the_Interface)**  &ndash; entender como a interface é criado em código compartilhado.
- **[iOS implementação](#iOS_Implementation)**  &ndash; saber como implementar a interface em código nativo para iOS.
- **[Implementação do Android](#Android_Implementation)**  &ndash; saber como implementar a interface em código nativo para o Android.
- **[Implementação do Windows](#WindowsImplementation)**  &ndash; saber como implementar a interface em código nativo para Windows Phone e o Windows UWP (plataforma Universal).
- **[Implementando em código compartilhado](#Implementing_in_Shared_Code)**  &ndash; aprender a usar `DependencyService` para chamar a implementação nativa de código compartilhado.

O aplicativo usando `DependencyService` terá a seguinte estrutura:

![](device-orientation-images/orientation-diagram.png "Estrutura de aplicativo DependencyService")

> [!NOTE]
> **Observação:** é possível detectar se o dispositivo estiver na orientação retrato ou paisagem no código compartilhado, conforme demonstrado no [dispositivo Orientation]/guides/xamarin-forms/user-interface/layouts/device-orientation/#changes-in-orientation) . O método descrito neste artigo usa recursos nativos para obter mais informações sobre a orientação, incluindo se o dispositivo é de cabeça para baixo.

<a name="Creating_the_Interface" />

## <a name="creating-the-interface"></a>Criando a Interface

Primeiro, crie uma interface no código compartilhado que expressa a funcionalidade que você pretende implementar. Neste exemplo, a interface contém um único método:

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

Codificando a essa interface no código compartilhado permitirá que o aplicativo xamarin. Forms acessar a orientação do dispositivo APIs em cada plataforma.

> [!NOTE]
> **Observação**: Classes que implementam a interface devem ter um construtor sem parâmetros para trabalhar com o `DependencyService`.

<a name="iOS_Implementation" />

## <a name="ios-implementation"></a>Implementação do iOS

A Interface deve ser implementada em cada projeto de aplicativo específico da plataforma. Observe que a classe tem um construtor sem parâmetros para que o `DependencyService` pode criar novas instâncias:

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

Finalmente, adicione este `[assembly]` atributo acima da classe (e fora de todos os namespaces que foram definidos), incluindo quaisquer `using` instruções:

```csharp
using UIKit;
using Foundation;
using DependencyServiceSample.iOS; //enables registration outside of namespace

[assembly: Xamarin.Forms.Dependency (typeof (DeviceOrientationImplementation))]
namespace DependencyServiceSample.iOS {
    ...
```

Este atributo registra a classe como uma implementação do `IDeviceOrientation` Interface, o que significa que `DependencyService.Get<IDeviceOrientation>` pode ser usado no código compartilhado para criar uma instância dele.

<a name="Android_Implementation" />

## <a name="android-implementation"></a>Implementação do Android

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

Adicione esse `[assembly]` atributo acima da classe (e fora de todos os namespaces que foram definidos), incluindo quaisquer `using` instruções:

```csharp
using DependencyServiceSample.Droid; //enables registration outside of namespace
using Android.Hardware;

[assembly: Xamarin.Forms.Dependency (typeof (DeviceOrientationImplementation))]
namespace DependencyServiceSample.Droid {
    ...
```

Este atributo registra a classe como uma implementação do `IDeviceOrientaiton` Interface, o que significa que `DependencyService.Get<IDeviceOrientation>` pode ser usado no código compartilhado pode criar uma instância dela.

<a name="WindowsImplementation" />

## <a name="windows-phone-and-universal-windows-platform-implementation"></a>Windows Phone e a implementação da plataforma Universal do Windows

O código a seguir implementa a `IDeviceOrientation` interface no Windows Phone e a plataforma Universal do Windows:

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

Adicionar o `[assembly]` atributo acima da classe (e fora de todos os namespaces que foram definidos), incluindo quaisquer `using` instruções:

```csharp
using DependencyServiceSample.WindowsPhone; //enables registration outside of namespace

[assembly: Dependency(typeof(DeviceOrientationImplementation))]
namespace DependencyServiceSample.WindowsPhone {
    ...
```

Este atributo registra a classe como uma implementação do `DeviceOrientationImplementation` Interface, o que significa que `DependencyService.Get<IDeviceOrientation>` pode ser usado no código compartilhado pode criar uma instância dela.

<a name="Implementing_in_Shared_Code" />

## <a name="implementing-in-shared-code"></a>Implementando em código compartilhado

Agora podemos escrever e testar o código compartilhado que acessa o `IDeviceOrientation` interface. Esta página simple inclui um botão que atualize seu próprio texto com base na orientação do dispositivo. Ele usa o `DependencyService` para obter uma instância do `IDeviceOrientation` interface &ndash; em tempo de execução, esta instância será a implementação específica de plataforma que tenha acesso completo para o SDK nativo:

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

Executar este aplicativo no iOS, Android ou plataformas Windows e pressionando o botão resultará no texto do botão de atualização com a orientação do dispositivo.

![](device-orientation-images/orientation.png "Exemplo de orientação do dispositivo")


## <a name="related-links"></a>Links relacionados

- [Usando DependencyService (exemplo)](https://developer.xamarin.com/samples/UsingDependencyService)
- [DependencyService (exemplo)](https://developer.xamarin.com/samples/DependencyService/DependencyServiceSample/)
- [Exemplos de xamarin. Forms](https://github.com/xamarin/xamarin-forms-samples)
