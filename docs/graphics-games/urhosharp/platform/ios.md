---
title: UrhoSharp iOS e tvOS suporte
description: Este documento aborda o iOS e tvOS suporte UrhoSharp. Ele descreve como criar um projeto, configurar e iniciar Urho e executar uma inserção personalizada de Urho.
ms.prod: xamarin
ms.assetid: 7B06567E-E789-4EA1-A2A9-F3B2212EDD23
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: 3efdbfcfdd670525dbf3198deb17c4631a889c56
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832467"
---
# <a name="urhosharp-ios-and-tvos-support"></a>Suporte a iOS e tvOS UrhoSharp

Embora Urho é uma biblioteca de classes portátil e permite que a mesma API a ser usado em toda a plataforma de vários para sua lógica do jogo, você ainda precisará inicializar Urho em seu driver específico da plataforma, em alguns casos, você vai querer tirar proveito dos recursos específicos da plataforma .

Nas páginas a seguir, suponha que `MyGame` é um sublcass do `Application` classe.

## <a name="ios-and-tvos"></a>iOS e tvOS

**Arquiteturas com suporte:** armv7, arm64, i386

## <a name="creating-a-project"></a>Criar um projeto

Criar um projeto do iOS e, em seguida, adicionar dados ao diretório de recursos e certifique-se de tem todos os arquivos **BundleResource** como o **Build Action**.

![Configuração de projeto](ios-images/image-4.png "adicionar dados ao diretório de recursos")

## <a name="configuring-and-launching-urho"></a>Configurando e iniciando Urho

Adicionar usando as instruções para o `Urho` e `Urho.iOS` namespaces e, em seguida, adicione este código para inicializar Urho, bem como a inicialização do seu aplicativo:

```csharp
new MyGame().Run();
```

Observe que, como a espera do iOS `FinishedLaunching` para concluir, você deve enfileirar a chamada para `Run()` para executar após a conclusão do método, isso é uma linguagem comum:

```csharp
public override bool FinishedLaunching(UIApplication app, NSDictionary options)
{
    LaunchGame();
    return true;
}

async void LaunchGame()
{
    await Task.Yield();
    new SamplyGame().Run();
}
```

É importante que você desabilite otimizações de PNG, porque o otimizador PNG de iOS padrão irá gerar imagens Urho não pode consumir atualmente corretamente

## <a name="custom-embedding-of-urho"></a>Incorporação personalizado de Urho

Você pode Alternativamente a ter Urho assuma a tela do aplicativo inteiro, e para usá-lo como um componente do seu aplicativo, você pode criar uma `UrhoSurface` que é um `UIView` que pode ser inserida em seu aplicativo existente.

Este é o que você precisaria fazer:

```csharp
var view = new UrhoSurface () {
    Frame = new CGRect (100,100,200,200),
    BackgroundColor = UIColor.Red
}
window.AddSubview (view);
```

Isso irá hospedar sua classe Urho, portanto, em seguida, você deveria fazer:

```csharp
new MyGame().Run ();
```
