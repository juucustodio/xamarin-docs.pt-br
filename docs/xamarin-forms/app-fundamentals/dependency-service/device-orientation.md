---
title: Verificando a orientação do dispositivo
description: Este artigo explica como usar a classe do xamarin. Forms DependencyService para acessar a orientação do dispositivo de código compartilhado.
ms.prod: xamarin
ms.assetid: 5F60975F-47DB-4361-B97C-2290D6F77D2F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/09/2016
ms.openlocfilehash: 52b82033cbd6fe0e1a44f5729c815074852230bf
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50115404"
---
# <a name="checking-device-orientation"></a>Verificando a orientação do dispositivo

Este artigo vai guiá-lo para usar [ `DependencyService` ](xref:Xamarin.Forms.DependencyService) para verificar a orientação do dispositivo de código compartilhado usando as APIs nativas em cada plataforma. Este passo a passo se baseia no existente `DeviceOrientation` plug-in por Ali Özgür. Consulte a [repositório GitHub](https://github.com/aliozgur/Xamarin.Plugins/tree/master/DeviceOrientation) para obter mais informações.

- **[Criando a Interface](#Creating_the_Interface)**  &ndash; entender como a interface é criado em código compartilhado.
- **[Implementação do iOS](#iOS_Implementation)**  &ndash; Saiba como implementar a interface em código nativo para iOS.
- **[Implementação do Android](#Android_Implementation)**  &ndash; Saiba como implementar a interface em código nativo para Android.
- **[Implementação de UWP](#WindowsImplementation)**  &ndash; Saiba como implementar a interface em código nativo para Universal Windows Platform (UWP).
- **[Implementando em código compartilhado](#Implementing_in_Shared_Code)**  &ndash; Saiba como usar `DependencyService` para chamar a implementação nativa a partir do código compartilhado.

O aplicativo usando `DependencyService` terá a seguinte estrutura:

![](device-orientation-images/orientation-diagram.png "Estrutura de aplicativo do DependencyService")

> [!NOTE]
> É possível detectar se o dispositivo está na orientação retrato ou paisagem em código compartilhado, conforme demonstrado [orientação do dispositivo](~/xamarin-forms/user-interface/layouts/device-orientation.md#Reacting_to_Changes_in_Orientation). O método descrito neste artigo usa recursos nativos para obter mais informações sobre a orientação, incluindo se o dispositivo está de cabeça para baixo.

<a name="Creating_the_Interface" />

## <a name="creating-the-interface"></a>Criando a Interface

Primeiro, crie uma interface no código compartilhado que expresse a funcionalidade que você planeja implementar. Neste exemplo, a interface contém um único método:

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

Codificação em relação a essa interface no código compartilhado permitirá que o aplicativo xamarin. Forms acessar a orientação do dispositivo APIs em cada plataforma.

> [!NOTE]
> Classes que implementam a interface devem ter um construtor sem parâmetros para trabalhar com o `DependencyService`.

<a name="iOS_Implementation" />

## <a name="ios-implementation"></a>Implementação do iOS

A Interface deve ser implementada em cada projeto de aplicativo específico da plataforma. Observe que a classe tem um construtor sem parâmetros, de modo que o `DependencyService` pode criar novas instâncias:

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

Por fim, adicione isso `[assembly]` atributo acima da classe (e fora de todos os namespaces que foram definidos), incluindo quaisquer `using` instruções:

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

Adicione isso `[assembly]` atributo acima da classe (e fora de todos os namespaces que foram definidos), incluindo quaisquer `using` instruções:

```csharp
using DependencyServiceSample.Droid; //enables registration outside of namespace
using Android.Hardware;

[assembly: Xamarin.Forms.Dependency (typeof (DeviceOrientationImplementation))]
namespace DependencyServiceSample.Droid {
    ...
```

Este atributo registra a classe como uma implementação do `IDeviceOrientaiton` Interface, o que significa que `DependencyService.Get<IDeviceOrientation>` podem ser usados em código compartilhado pode criar uma instância dele.

<a name="WindowsImplementation" />

## <a name="universal-windows-platform-implementation"></a>Implementação da plataforma universal do Windows

O código a seguir implementa o `IDeviceOrientation` interface na plataforma Universal do Windows:

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

Adicione a `[assembly]` atributo acima da classe (e fora de todos os namespaces que foram definidos), incluindo quaisquer `using` instruções:

```csharp
using DependencyServiceSample.WindowsPhone; //enables registration outside of namespace

[assembly: Dependency(typeof(DeviceOrientationImplementation))]
namespace DependencyServiceSample.WindowsPhone {
    ...
```

Este atributo registra a classe como uma implementação do `DeviceOrientationImplementation` Interface, o que significa que `DependencyService.Get<IDeviceOrientation>` podem ser usados em código compartilhado pode criar uma instância dele.

<a name="Implementing_in_Shared_Code" />

## <a name="implementing-in-shared-code"></a>Implementando em código compartilhado

Agora podemos escrever e testar o código compartilhado que acessa o `IDeviceOrientation` interface. Esta página simple inclui um botão que atualiza seu próprio texto com base na orientação do dispositivo. Ele usa o `DependencyService` para obter uma instância das `IDeviceOrientation` interface &ndash; em tempo de execução essa instância será a implementação específica da plataforma que tem acesso completo para o SDK nativo:

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

Executando esse aplicativo no iOS, Android ou as plataformas Windows e pressionando o botão resultará no texto do botão Atualizar com a orientação do dispositivo.

![](device-orientation-images/orientation.png "Exemplo de orientação do dispositivo")


## <a name="related-links"></a>Links relacionados

- [Usando o DependencyService (amostra)](https://developer.xamarin.com/samples/UsingDependencyService)
- [DependencyService (amostra)](https://developer.xamarin.com/samples/DependencyService/DependencyServiceSample/)
- [Amostras do Xamarin.Forms](https://github.com/xamarin/xamarin-forms-samples)
