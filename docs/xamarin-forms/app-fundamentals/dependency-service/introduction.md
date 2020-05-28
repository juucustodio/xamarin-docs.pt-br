---
title: Xamarin.FormsIntrodução ao DependencyService
description: Este artigo explica como usar a Xamarin.Forms classe DependencyService para invocar a funcionalidade da plataforma nativa.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f4d43a0c9c4878733d65b170c27e744b397aa4d0
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84138353"
---
# <a name="xamarinforms-dependencyservice-introduction"></a>Xamarin.FormsIntrodução ao DependencyService

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/dependencyservice/)

A [`DependencyService`](xref:Xamarin.Forms.DependencyService) classe é um localizador de serviço que permite que Xamarin.Forms os aplicativos invoquem a funcionalidade de plataforma nativa do código compartilhado.

O processo para usar o [`DependencyService`](xref:Xamarin.Forms.DependencyService) para invocar a funcionalidade da plataforma nativa é:

1. Crie uma interface para a funcionalidade de plataforma nativa, em código compartilhado. Para obter mais informações, confira [Criar uma interface](#create-an-interface).
1. Implemente a interface nos projetos de plataforma necessários. Para obter mais informações, confira [Implementar a interface em cada plataforma](#implement-the-interface-on-each-platform).
1. Registre as implementações de plataforma com o [`DependencyService`](xref:Xamarin.Forms.DependencyService) . Isso permite que Xamarin.Forms o localize as implementações de plataforma em tempo de execução. Para obter mais informações, confira [Registrar as implementações de plataforma](#register-the-platform-implementations).
1. Resolva as implementações de plataforma de um código compartilhado e as invoque. Para obter mais informações, confira [Resolver as implementações de plataforma](#resolve-the-platform-implementations).

O diagrama a seguir mostra como a funcionalidade da plataforma nativa é invocada em um Xamarin.Forms aplicativo:

![Visão geral do local do serviço usando a Xamarin.Forms classe DependencyService](introduction-images/dependency-service.png "Local do serviço DependencyService")

## <a name="create-an-interface"></a>Criar uma interface

A primeira etapa em ser capaz de invocar a funcionalidade de plataforma nativa de um código compartilhado é criar uma interface que defina a API para interagir com a funcionalidade de plataforma nativa. Essa interface deve ser colocada em seu projeto de código compartilhado.

O exemplo a seguir mostra uma interface para uma API que pode ser usada para recuperar a orientação de um dispositivo:

```csharp
public interface IDeviceOrientationService
{
    DeviceOrientation GetOrientation();
}
```

## <a name="implement-the-interface-on-each-platform"></a>Implementar a interface em cada plataforma

Depois de criar a interface que define a API para interagir com a funcionalidade de plataforma nativa, a interface deverá ser implementada em cada projeto de plataforma.

### <a name="ios"></a>iOS

O exemplo de código a seguir mostra a implementação da interface `IDeviceOrientationService` no iOS:

```csharp
namespace DependencyServiceDemos.iOS
{
    public class DeviceOrientationService : IDeviceOrientationService
    {
        public DeviceOrientation GetOrientation()
        {
            UIInterfaceOrientation orientation = UIApplication.SharedApplication.StatusBarOrientation;

            bool isPortrait = orientation == UIInterfaceOrientation.Portrait ||
                orientation == UIInterfaceOrientation.PortraitUpsideDown;
            return isPortrait ? DeviceOrientation.Portrait : DeviceOrientation.Landscape;
        }
    }
}
```

### <a name="android"></a>Android

O exemplo de código a seguir mostra a implementação da interface `IDeviceOrientationService` no Android:

```csharp
namespace DependencyServiceDemos.Droid
{
    public class DeviceOrientationService : IDeviceOrientationService
    {
        public DeviceOrientation GetOrientation()
        {
            IWindowManager windowManager = Android.App.Application.Context.GetSystemService(Context.WindowService).JavaCast<IWindowManager>();

            SurfaceOrientation orientation = windowManager.DefaultDisplay.Rotation;
            bool isLandscape = orientation == SurfaceOrientation.Rotation90 ||
                orientation == SurfaceOrientation.Rotation270;
            return isLandscape ? DeviceOrientation.Landscape : DeviceOrientation.Portrait;
        }
    }
}
```

### <a name="universal-windows-platform"></a>Plataforma Universal do Windows

O exemplo de código a seguir mostra a implementação da interface `IDeviceOrientationService` na UWP (Plataforma Universal do Windows):

```csharp
namespace DependencyServiceDemos.UWP
{
    public class DeviceOrientationService : IDeviceOrientationService
    {
        public DeviceOrientation GetOrientation()
        {
            ApplicationViewOrientation orientation = ApplicationView.GetForCurrentView().Orientation;
            return orientation == ApplicationViewOrientation.Landscape ? DeviceOrientation.Landscape : DeviceOrientation.Portrait;
        }
    }
}
```

## <a name="register-the-platform-implementations"></a>Registrar as implementações de plataforma

Depois de implementar a interface em cada projeto de plataforma, as implementações de plataforma devem ser registradas com o [`DependencyService`](xref:Xamarin.Forms.DependencyService) , para que Xamarin.Forms possam localizá-las em tempo de execução. Normalmente, isso é executado com o [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) , que indica que o tipo especificado fornece uma implementação da interface.

O exemplo a seguir mostra como usar o [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) para registrar a implementação do IOS da `IDeviceOrientationService` interface:

```csharp
using Xamarin.Forms;

[assembly: Dependency(typeof(DependencyServiceDemos.iOS.DeviceOrientationService))]
namespace DependencyServiceDemos.iOS
{
    public class DeviceOrientationService : IDeviceOrientationService
    {
        public DeviceOrientation GetOrientation()
        {
            ...
        }
    }
}
```

Neste exemplo, o [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) registra o `DeviceOrientationService` com o [`DependencyService`](xref:Xamarin.Forms.DependencyService) . Da mesma forma, as implementações da `IDeviceOrientationService` interface em outras plataformas devem ser registradas com o [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) .

Para obter mais informações sobre como registrar implementações de plataforma com o [`DependencyService`](xref:Xamarin.Forms.DependencyService) , consulte [ Xamarin.Forms registro e resolução do DependencyService](registration-and-resolution.md).

## <a name="resolve-the-platform-implementations"></a>Resolver as implementações da plataforma

Após o registro das implementações de plataforma com o [`DependencyService`](xref:Xamarin.Forms.DependencyService) , as implementações devem ser resolvidas antes de serem invocadas. Normalmente, isso é executado em código compartilhado usando o [`DependencyService.Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) método.

O código a seguir mostra um exemplo de como chamar o [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) método para resolver a `IDeviceOrientationService` interface e, em seguida, invocar seu `GetOrientation` método:

```csharp
IDeviceOrientationService service = DependencyService.Get<IDeviceOrientationService>();
DeviceOrientation orientation = service.GetOrientation();
```

Como alternativa, esse código pode ser condensado em uma única linha:

```csharp
DeviceOrientation orientation = DependencyService.Get<IDeviceOrientationService>().GetOrientation();
```

Para obter mais informações sobre como resolver implementações de plataforma com o [`DependencyService`](xref:Xamarin.Forms.DependencyService) , consulte [ Xamarin.Forms registro e resolução do DependencyService](registration-and-resolution.md).

## <a name="related-links"></a>Links relacionados

- [Demonstrações de DependencyService (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/dependencyservice/)
- [Xamarin.FormsRegistro e resolução do DependencyService](registration-and-resolution.md)
