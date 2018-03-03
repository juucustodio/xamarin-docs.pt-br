---
title: Suporte a Mac UrhoSharp
description: "Recursos e a configuração específica do Mac"
ms.topic: article
ms.prod: xamarin
ms.assetid: 95FFBD36-14E9-4C17-B1E8-9A04E81E824D
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.openlocfilehash: cdaca45c3132abf3f52d407d4ce59c680248fce7
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="urhosharp-mac-support"></a>Suporte a Mac UrhoSharp

_Recursos e a configuração específica do Mac_

Embora Urho é uma biblioteca de classes portátil, e permite que a mesma API a ser usado na plataforma de vários para seu jogo lógica, você ainda precisa inicializar Urho no driver específico da plataforma e, em alguns casos, convém tirar proveito dos recursos específicos de plataforma .

Nas páginas a seguir, suponha que `MyGame` é uma subclasse do `Application` classe.

# <a name="macos-x"></a>MacOS X

**Suporte a arquiteturas:** x86/x86-64 de 32 e 64 bits.

# <a name="creating-a-project"></a>Criando um projeto

Criar um projeto de console, o Urho NuGet de referência e, em seguida, certifique-se de que você pode localizar os ativos (os diretórios que contém o diretório de dados).

```csharp
DesktopUrhoInitializer.AssetsDirectory = "../Assets";
new MyGame().Run();
```

## <a name="example"></a>Exemplo

[Exemplo completo](https://github.com/xamarin/urho-samples/tree/master/FeatureSamples/Cocoa)


