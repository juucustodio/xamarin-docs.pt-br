---
title: Suporte a Mac UrhoSharp
description: "Programa de instalação específicas do Mac e recursos UrhoSharp."
ms.topic: article
ms.prod: xamarin
ms.assetid: 95FFBD36-14E9-4C17-B1E8-9A04E81E824D
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.openlocfilehash: fff96a19d5f5286f2c9483407fcaaab6d15ff2b5
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="urhosharp-mac-support"></a>Suporte a Mac UrhoSharp

_Recursos e a configuração específica do Mac_

Embora Urho é uma biblioteca de classes portátil, e permite que a mesma API a ser usado na plataforma de vários para seu jogo lógica, você ainda precisa inicializar Urho no driver específico da plataforma e, em alguns casos, convém tirar proveito dos recursos específicos de plataforma .

Nas páginas a seguir, suponha que `MyGame` é uma subclasse do `Application` classe.

## <a name="macos"></a>macOS

**Suporte a arquiteturas:** x86/x86-64 de 32 e 64 bits.

## <a name="creating-a-project"></a>Criando um projeto

Criar um projeto de console, o Urho NuGet de referência e, em seguida, certifique-se de que você pode localizar os ativos (os diretórios que contém o diretório de dados).

```csharp
DesktopUrhoInitializer.AssetsDirectory = "../Assets";
new MyGame().Run();
```

## <a name="example"></a>Exemplo

[Exemplo completo](https://github.com/xamarin/urho-samples/tree/master/FeatureSamples/Cocoa)


