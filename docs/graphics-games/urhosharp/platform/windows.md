---
title: Suporte do Windows UrhoSharp
description: "Recursos e a configuração específica do Windows"
ms.topic: article
ms.prod: xamarin
ms.assetid: A4F36014-AE4E-4F07-A1AC-F264AAA68ACF
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.openlocfilehash: ec4fec01aa27e522aeec6df09041f14b1c3db080
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="urhosharp-windows-support"></a>Suporte do Windows UrhoSharp

_Recursos e a configuração específica do Windows_

Embora Urho é uma biblioteca de classes portátil, e permite que a mesma API a ser usado na plataforma de vários para seu jogo lógica, você ainda precisa inicializar Urho no driver específico da plataforma e, em alguns casos, convém tirar proveito dos recursos específicos de plataforma .

Nas páginas a seguir, suponha que `MyGame` é uma subclasse do `Application` classe.

**Suporte a arquiteturas:** apenas 64 bits Windows.

Você pode ver exemplos completos mostrando como usar isso em nosso [exemplos](https://github.com/xamarin/urho-samples/tree/master/FeatureSamples)

# <a name="standalone-project"></a>Projeto autônomo

## <a name="creating-a-project"></a>Criando um projeto

Criar um projeto de Console, o Urho NuGet de referência e, em seguida, certifique-se de que você pode localizar os ativos (os diretórios que contém o diretório de dados).

## <a name="configuring-and-launching-urho"></a>Configurando e iniciando Urho

Para iniciar seu aplicativo, faça o seguinte:

```csharp
DesktopUrhoInitializer.AssetsDirectory = "../Assets";
new MyGame().Run();
```
## <a name="example"></a>Exemplo

[Exemplo completo](https://github.com/xamarin/urho-samples/tree/master/FeatureSamples/Desktop)

# <a name="integrated-with-wpf"></a>Integrado com WPF

## <a name="creating-a-project"></a>Criando um projeto

Criar um projeto WPF, o Urho NuGet de referência e, em seguida, certifique-se de que você pode localizar os ativos (os diretórios que contém o diretório de dados).

## <a name="configuring-and-launching-urho-from-wpf"></a>Configurando e iniciando Urho do WPF

Criar uma subclasse de `Window` e configurar seus ativos assim:

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

## <a name="example"></a>Exemplo

[Exemplo completo](https://github.com/xamarin/urho-samples/tree/master/FeatureSamples/WPF)

# <a name="integrated-with-uwp"></a>Integrado com UWP

## <a name="creating-a-project"></a>Criando um projeto

Criar um projeto UWP, fazer referência a Urho NuGet e, em seguida, certifique-se de que você pode localizar os ativos (os diretórios que contém o diretório de dados).

## <a name="configuring-and-launching-urho-from-uwp"></a>Configurando e iniciando Urho de UWP

Criar uma subclasse de `Window` e configurar seus ativos assim:

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

## <a name="example"></a>Exemplo

[Exemplo completo](https://github.com/xamarin/urho-samples/tree/master/FeatureSamples/UWP)

# <a name="integrated-with-windowsforms"></a>Integrado ao Windows. Forms

## <a name="creating-a-project"></a>Criando um projeto

Criar um projeto do Windows. Forms, o Urho NuGet de referência e, em seguida, certifique-se de que você pode localizar os ativos (os diretórios que contém o diretório de dados).

## <a name="configuring-and-launching-urho-from-windowsforms"></a>Configurando e iniciando Urho do Windows. Forms

Iniciar Urho do formulário, consulte [exemplo completo](https://github.com/xamarin/urho-samples/blob/master/FeatureSamples/WinForms/SamplesForm.cs)

