---
title: Suporte a Mac UrhoSharp
description: Este documento aborda o suporte ao macOS UrhoSharp. Ele descreve como criar um projeto e fornece um link para um código de exemplo.
ms.prod: xamarin
ms.assetid: 95FFBD36-14E9-4C17-B1E8-9A04E81E824D
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: 6d0a048020284319682c1bee0f9a1d7f9af00977
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61386358"
---
# <a name="urhosharp-mac-support"></a>Suporte a Mac UrhoSharp

_Recursos e configuração específicas do Mac_

Embora Urho é uma biblioteca de classes portátil e permite que a mesma API a ser usado em toda a plataforma de vários para sua lógica do jogo, você ainda precisará inicializar Urho em seu driver específico da plataforma, em alguns casos, você vai querer tirar proveito dos recursos específicos da plataforma .

Nas páginas a seguir, suponha que `MyGame` é uma subclasse do `Application` classe.

## <a name="macos"></a>macOS

**Arquiteturas com suporte:** x86/x86-64 para 32 bits e 64 bits.

## <a name="creating-a-project"></a>Criando um projeto

Criar um projeto de console, o Urho NuGet de referência e, em seguida, certifique-se de que você pode localizar os ativos (os diretórios que contém o diretório de dados).

```csharp
DesktopUrhoInitializer.AssetsDirectory = "../Assets";
new MyGame().Run();
```

## <a name="example"></a>Exemplo

[Exemplo completo](https://github.com/xamarin/urho-samples/tree/master/FeatureSamples/Cocoa)


