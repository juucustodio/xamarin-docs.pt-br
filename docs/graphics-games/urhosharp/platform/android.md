---
title: Suporte do Android do UrhoSharp
description: Este documento descreve a configuração específica do Android e as informações relacionadas ao recurso de UrhoSharp. Em particular, ele aborda as arquiteturas com suporte, como criar um projeto, configurando e iniciando Urho e personalizadas a incorporação de Urho.
ms.prod: xamarin
ms.assetid: 8409BD81-B1A6-4F5D-AE11-6BBD3F7C6327
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: e7371fa85fd5955e9a0fd285adb32844001821b3
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61285162"
---
# <a name="urhosharp-android-support"></a>Suporte do Android do UrhoSharp

_Recursos e configuração específica do android_

Embora Urho é uma biblioteca de classes portátil e permite que a mesma API a ser usado em toda a plataforma de vários para sua lógica do jogo, você ainda precisará inicializar Urho em seu driver específico da plataforma, em alguns casos, você vai querer tirar proveito dos recursos específicos da plataforma .

Nas páginas a seguir, suponha que `MyGame` é uma subclasse do `Application` classe.

## <a name="architectures"></a>Arquiteturas

**Arquiteturas com suporte**: x86, armeabi, armeabi-v7a

## <a name="create-a-project"></a>Criar um projeto

Criar um projeto do Android e adicione o pacote NuGet de UrhoSharp.

Adicionar dados que contém seus ativos para o **ativos** diretório e verifique se todos os arquivos têm **AndroidAsset** como o **Build Action**.

![Configuração de projeto](android-images/image-3.png "adicionar dados que contém os ativos para o diretório de ativos")

## <a name="configure-and-launching-urho"></a>Configurar e iniciar Urho

Adicionar usando as instruções para o `Urho` e `Urho.Android` namespaces e, em seguida, adicione este código para inicializar Urho, bem como a inicialização do seu aplicativo.

A maneira mais simples para executar um jogo, conforme implementado na classe MyGame é chamar

```csharp
UrhoSurface.RunInActivity<MyGame>();
```

Isso abrirá uma atividade de tela inteira com o jogo como um conteúdo.

## <a name="custom-embedding-of-urho"></a>Incorporação personalizado de Urho

Você pode Alternativamente a ter Urho assuma a tela do aplicativo inteiro, e para usá-lo como um componente do seu aplicativo, você pode criar um `SurfaceView` por meio de:

```csharp
var surface = UrhoSurface.CreateSurface<MyGame>(activity)
```

Você também precisará encaminhar alguns eventos sua atividade para UrhoSharp, por exemplo:

```csharp
protected override void OnPause()
{
    UrhoSurface.OnPause();
    base.OnPause();
}
```

Você deve fazer o mesmo: `OnResume`, `OnPause`, `OnLowMemory`, `OnDestroy`, `DispatchKeyEvent` e `OnWindowFocusChanged`.

Isso mostra uma atividade típica que inicia o jogo:

```csharp
[Activity(Label = "MyUrhoApp", MainLauncher = true,
    Icon = "@drawable/icon", Theme = "@android:style/Theme.NoTitleBar.Fullscreen",
    ConfigurationChanges = ConfigChanges.KeyboardHidden | ConfigChanges.Orientation,
    ScreenOrientation = ScreenOrientation.Portrait)]
public class MainActivity : Activity
{
    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);
        var mLayout = new AbsoluteLayout(this);
        var surface = UrhoSurface.CreateSurface<MyUrhoApp>(this);
        mLayout.AddView(surface);
        SetContentView(mLayout);
    }

    protected override void OnResume()
    {
        UrhoSurface.OnResume();
        base.OnResume();
    }

    protected override void OnPause()
    {
        UrhoSurface.OnPause();
        base.OnPause();
    }

    public override void OnLowMemory()
    {
        UrhoSurface.OnLowMemory();
        base.OnLowMemory();
    }

    protected override void OnDestroy()
    {
        UrhoSurface.OnDestroy();
        base.OnDestroy();
    }

    public override bool DispatchKeyEvent(KeyEvent e)
    {
        if (!UrhoSurface.DispatchKeyEvent(e))
            return false;
        return base.DispatchKeyEvent(e);
    }

    public override void OnWindowFocusChanged(bool hasFocus)
    {
        UrhoSurface.OnWindowFocusChanged(hasFocus);
        base.OnWindowFocusChanged(hasFocus);
    }
}
```

