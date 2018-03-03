---
title: IOS UrhoSharp e tvOS suporte
description: "iOS e tvOS configuração específica e recursos"
ms.topic: article
ms.prod: xamarin
ms.assetid: 7B06567E-E789-4EA1-A2A9-F3B2212EDD23
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.openlocfilehash: 9cf779b23ed830c07af0100152a44d6c3c4e317b
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="urhosharp-ios-and-tvos-support"></a>IOS UrhoSharp e tvOS suporte

_iOS e tvOS configuração específica e recursos_

Embora Urho é uma biblioteca de classes portátil, e permite que a mesma API a ser usado na plataforma de vários para seu jogo lógica, você ainda precisa inicializar Urho no driver específico da plataforma e, em alguns casos, convém tirar proveito dos recursos específicos de plataforma .

Nas páginas a seguir, suponha que `MyGame` é um sublcass do `Application` classe.

# <a name="ios-and-tvos"></a>iOS e tvOS

**Suporte a arquiteturas:** armv7, arm64, i386

# <a name="creating-a-project"></a>Criando um projeto

Criar um projeto do iOS e, em seguida, adicionar dados ao diretório de recursos e verifique se todos os arquivos **BundleResource** como o **ação de compilação**.

![Configuração de projeto](ios-images/image-4.png "adicionar dados ao diretório de recursos")

# <a name="configuring-and-launching-urho"></a>Configurando e iniciando Urho

Adicionar usando as instruções para o `Urho` e `Urho.iOS` namespaces e, em seguida, adicione este código para inicializar Urho, bem como a inicialização do seu aplicativo:

```csharp
new MyGame().Run();
```

Observe que, como a espera de iOS `FinishedLaunching` para concluir, você deve enfileirar a chamada para `Run()` para ser executado depois que o método é concluído, este é um idioma comuns:

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

É importante que você desabilite otimizações PNG porque o otimizador PNG iOS padrão irá gerar imagens Urho não pode consumir atualmente corretamente

# <a name="custom-embedding-of-urho"></a>Inserindo personalizado de Urho

Você pode opcionalmente a ter Urho assumir a tela do aplicativo inteiro e para usá-lo como um componente do seu aplicativo, você pode criar um `UrhoSurface` que é um `UIView` que você pode inserir em seu aplicativo existente.

Este é o que você precisa fazer:

```csharp
var view = new UrhoSurface () {
    Frame = new CGRect (100,100,200,200),
    BackgroundColor = UIColor.Red
}
window.AddSubview (view);
```

Isso irá hospedar sua classe Urho, portanto, em seguida, você deve fazer:

```csharp
new MyGame().Run ();
```

