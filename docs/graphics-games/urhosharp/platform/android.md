---
title: Suporte do UrhoSharp Android
description: Este documento descreve a configuração específica do Android e informações relacionadas ao recurso UrhoSharp. Em particular, ele discute as arquiteturas com suporte, como criar um projeto, configurando e iniciando Urho e personalizadas a incorporação de Urho.
ms.prod: xamarin
ms.assetid: 8409BD81-B1A6-4F5D-AE11-6BBD3F7C6327
author: charlespetzold
ms.author: chape
ms.date: 03/29/2017
ms.openlocfilehash: 6e489f52712989b5f94fa52d5ec6f22a13ce6252
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34783775"
---
# <a name="urhosharp-android-support"></a>Suporte do UrhoSharp Android

_Recursos e configuração específica do android_

Embora Urho é uma biblioteca de classes portátil, e permite que a mesma API a ser usado na plataforma de vários para seu jogo lógica, você ainda precisa inicializar Urho no driver específico da plataforma e, em alguns casos, convém tirar proveito dos recursos específicos de plataforma .

Nas páginas a seguir, suponha que `MyGame` é uma subclasse do `Application` classe.

## <a name="architectures"></a>Arquiteturas

**Suporte a arquiteturas**: x86, armeabi, armeabi v7a

## <a name="create-a-project"></a>Criar um projeto

Criar um projeto Android e adicionar o pacote UrhoSharp NuGet.

Adicionar dados que contém seus ativos para o **ativos** directory e verifique se todos os arquivos têm **AndroidAsset** como o **ação de compilação**.

![Configuração de projeto](android-images/image-3.png "adicionar dados que contém os ativos para o diretório de ativos")

## <a name="configure-and-launching-urho"></a>Configurar e iniciar Urho

Adicionar usando as instruções para o `Urho` e `Urho.Android` namespaces e, em seguida, adicione este código para inicializar Urho, bem como a inicialização do seu aplicativo.

A maneira mais simples para executar um jogo, conforme implementado na classe MyGame é chamar

```csharp
UrhoSurface.RunInActivity<MyGame>();
```

Isso abrirá uma atividade de tela inteira como um conteúdo com o jogo.

## <a name="custom-embedding-of-urho"></a>Inserindo personalizado de Urho

Você pode opcionalmente a ter Urho assumir a tela do aplicativo inteiro e para usá-lo como um componente do seu aplicativo, você pode criar um `SurfaceView` por meio de:

```csharp
var surface = UrhoSurface.CreateSurface<MyGame>(activity)
```

Você também precisará encaminhar eventos alguns sua atividade para UrhoSharp, por exemplo:

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

