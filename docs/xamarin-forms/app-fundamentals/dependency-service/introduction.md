---
title: Introdução ao DependencyService do Xamarin.Forms
description: Este artigo explica como usar a classe do DependencyService do Xamarin.Forms para invocar a funcionalidade de plataforma nativa.
ms.prod: xamarin
ms.assetid: 5d019604-4f6f-4932-9b26-1fce3b4d88f8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/12/2019
ms.openlocfilehash: b27b4b0c3c5662c6cc1c2c151dd9ebe1523da3a4
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "71198519"
---
# <a name="xamarinforms-dependencyservice-introduction"></a>Introdução ao DependencyService do Xamarin.Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/dependencyservice/)

A classe [`DependencyService`](xref:Xamarin.Forms.DependencyService) é um localizador de serviço que permite que os aplicativos Xamarin.Forms invoquem a funcionalidade de plataforma nativa de um código compartilhado.

O processo para usar o [`DependencyService`](xref:Xamarin.Forms.DependencyService) para invocar a funcionalidade de plataforma nativa é:

1. Crie uma interface para a funcionalidade de plataforma nativa, em código compartilhado. Para obter mais informações, confira [Criar uma interface](#create-an-interface).
1. Implemente a interface nos projetos de plataforma necessários. Para obter mais informações, confira [Implementar a interface em cada plataforma](#implement-the-interface-on-each-platform).
1. Registrar as implementações de plataforma no [`DependencyService`](xref:Xamarin.Forms.DependencyService). Isso permite que o Xamarin.Forms localize as implementações de plataforma em runtime. Para obter mais informações, confira [Registrar as implementações de plataforma](#register-the-platform-implementations).
1. Resolva as implementações de plataforma de um código compartilhado e as invoque. Para obter mais informações, confira [Resolver as implementações de plataforma](#resolve-the-platform-implementations).

O diagrama a seguir mostra como a funcionalidade de plataforma nativa é invocada em um aplicativo Xamarin.Forms:

![Visão geral do local do serviço usando a classe DependencyService do Xamarin. Forms](introduction-images/dependency-service.png "Local do serviço DependencyService")

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

Após a implementação da interface em cada projeto de plataforma, as implementações de plataforma deverão ser registradas no [`DependencyService`](xref:Xamarin.Forms.DependencyService), de modo que o Xamarin.Forms possa localizá-las em tempo de execução. Isso normalmente é executado com o [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute), o que indica que o tipo especificado fornece uma implementação da interface.

O exemplo a seguir mostra o uso do [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) para registrar a implementação do iOS da interface `IDeviceOrientationService`:

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

Neste exemplo, o [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) registra o `DeviceOrientationService` com o [`DependencyService`](xref:Xamarin.Forms.DependencyService). Da mesma forma, as implementações da interface `IDeviceOrientationService` em outras plataformas devem ser registradas com o [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute).

Para obter mais informações sobre como registrar as implementações de plataforma com o [`DependencyService`](xref:Xamarin.Forms.DependencyService), confira [Registro e resolução de DependencyService do Xamarin.Forms](registration-and-resolution.md).

## <a name="resolve-the-platform-implementations"></a>Resolver as implementações da plataforma

Após o registro das implementações de plataforma com o [`DependencyService`](xref:Xamarin.Forms.DependencyService), as implementações deverão ser resolvidas antes de serem invocadas. Isso normalmente é feito no código compartilhado, usando o método [`DependencyService.Get<T>`](xref:Xamarin.Forms.DependencyService.Get*).

O código a seguir mostra um exemplo de chamada ao método [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) para resolver a interface `IDeviceOrientationService` e, em seguida, invocação de seu método `GetOrientation`:

```csharp
IDeviceOrientationService service = DependencyService.Get<IDeviceOrientationService>();
DeviceOrientation orientation = service.GetOrientation();
```

Como alternativa, esse código pode ser condensado em uma única linha:

```csharp
DeviceOrientation orientation = DependencyService.Get<IDeviceOrientationService>().GetOrientation();
```

Para obter mais informações sobre como resolver as implementações de plataforma com o [`DependencyService`](xref:Xamarin.Forms.DependencyService), confira [Registro e resolução de DependencyService do Xamarin.Forms](registration-and-resolution.md).

## <a name="related-links"></a>Links relacionados

- [Demonstrações de DependencyService (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/dependencyservice/)
- [Registro e resolução de DependencyService do Xamarin.Forms](registration-and-resolution.md)
