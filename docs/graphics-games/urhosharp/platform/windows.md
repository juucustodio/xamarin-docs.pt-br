---
title: Suporte do Windows do UrhoSharp
description: Este documento aborda o suporte ao Windows UrhoSharp. Ele descreve como criar um projeto, configurar e iniciar Urho, integrar com o WPF e integrar com UWP.
ms.prod: xamarin
ms.assetid: A4F36014-AE4E-4F07-A1AC-F264AAA68ACF
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: 471029375d8a61a6c48d94a66d7836807e0da22f
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61386300"
---
# <a name="urhosharp-windows-support"></a>Suporte do Windows do UrhoSharp

Embora Urho é uma biblioteca de classes portátil e permite que a mesma API a ser usado em toda a plataforma de vários para sua lógica do jogo, você ainda precisará inicializar Urho em seu driver específico da plataforma, em alguns casos, você vai querer tirar proveito dos recursos específicos da plataforma .

Nas páginas a seguir, suponha que `MyGame` é uma subclasse do `Application` classe.

**Arquiteturas com suporte:** apenas de Windows 64 bits.

Você pode ver exemplos completos que mostram como usar isso em nosso [exemplos](https://github.com/xamarin/urho-samples/tree/master/FeatureSamples)

## <a name="standalone-project"></a>Projeto autônomo

### <a name="creating-a-project"></a>Criando um projeto

Criar um projeto de Console, o Urho NuGet de referência e, em seguida, certifique-se de que você pode localizar os ativos (os diretórios que contém o diretório de dados).

### <a name="configuring-and-launching-urho"></a>Configurando e iniciando Urho

Para iniciar seu aplicativo, faça o seguinte:

```csharp
DesktopUrhoInitializer.AssetsDirectory = "../Assets";
new MyGame().Run();
```

### <a name="example"></a>Exemplo

[Exemplo completo](https://github.com/xamarin/urho-samples/tree/master/FeatureSamples/Desktop)

## <a name="integrated-with-wpf"></a>Integrado com o WPF

### <a name="creating-a-project"></a>Criando um projeto

Criar um projeto WPF, o Urho NuGet de referência e, em seguida, certifique-se de que você pode localizar os ativos (os diretórios que contém o diretório de dados).

### <a name="configuring-and-launching-urho-from-wpf"></a>Configurando e iniciando Urho do WPF

Criar uma subclasse de `Window` e configurar seus ativos como este:

```csharp
    public partial class MainWindow : Window
    {
        Application currentApplication;

        public MainWindow()
        {
            InitializeComponent();
            DesktopUrhoInitializer.AssetsDirectory = @"../../Assets";
            Loaded += (s,e) => RunGame (new MyGame ());
        }

        async void RunGame(MyGame game)
        {
            var urhoSurface = new Panel { Dock = DockStyle.Fill };
            WindowsFormsHost.Child = urhoSurface;
            WindowsFormsHost.Focus();
            urhoSurface.Focus();
            await Task.Yield();
            var appOptions = new ApplicationOptions(assetsFolder: "Data")
                {
                    ExternalWindow = RunInSdlWindow.IsChecked.Value ? IntPtr.Zero : urhoSurface.Handle,
                    LimitFps = false, //true means "limit to 200fps"
                };
            currentApplication = Urho.Application.CreateInstance(value.Type, appOptions);
            currentApplication.Run();
        }
    }
```

### <a name="example"></a>Exemplo

[Exemplo completo](https://github.com/xamarin/urho-samples/tree/master/FeatureSamples/WPF)

## <a name="integrated-with-uwp"></a>Integrado com a UWP

### <a name="creating-a-project"></a>Criando um projeto

Crie um projeto UWP, o Urho NuGet de referência e, em seguida, certifique-se de que você pode localizar os ativos (os diretórios que contém o diretório de dados).

### <a name="configuring-and-launching-urho-from-uwp"></a>Configurando e iniciando Urho da UWP

Criar uma subclasse de `Window` e configurar seus ativos como este:

```csharp
{
            InitializeComponent();
            GameTypes = typeof(Sample).GetTypeInfo().Assembly.GetTypes()
                .Where(t => t.GetTypeInfo().IsSubclassOf(typeof(Application)) && t != typeof(Sample))
                .Select((t, i) => new TypeInfo(t, $"{i + 1}. {t.Name}", ""))
                .ToArray();
            DataContext = this;
            Loaded += (s, e) => RunGame (new MyGame ());
        }

        public void RunGame(TypeInfo value)
        {
            //at this moment, UWP supports assets only in pak files (see PackageTool)
            currentApplication = UrhoSurface.Run(value.Type, "Data.pak");
        }
    }
```

### <a name="example"></a>Exemplo

[Exemplo completo](https://github.com/xamarin/urho-samples/tree/master/FeatureSamples/UWP)

## <a name="integrated-with-windowsforms"></a>Integrado ao Windows. Forms

### <a name="creating-a-project"></a>Criando um projeto

Criar um projeto do Windows. Forms, o Urho NuGet de referência e, em seguida, certifique-se de que você pode localizar os ativos (os diretórios que contém o diretório de dados).

### <a name="configuring-and-launching-urho-from-windowsforms"></a>Configurando e iniciando Urho do Windows. Forms

Inicie o Urho no seu formulário, consulte [exemplo completo](https://github.com/xamarin/urho-samples/blob/master/FeatureSamples/WinForms/SamplesForm.cs)
