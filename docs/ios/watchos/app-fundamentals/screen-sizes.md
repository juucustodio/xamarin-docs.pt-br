---
title: Trabalhando com tamanhos de tela watchOS no Xamarin
description: Este documento descreve como trabalhar com vários tamanhos de tela watchOS. Ele aborda o watchOS interface designer, o simulador de watchOS e os recursos de imagem.
ms.prod: xamarin
ms.assetid: 840DF939-2F59-4ABA-87D8-92AAC8A92BC4
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: aeaa1bb1273bc062e0ac76eaa09722827f15797f
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028400"
---
# <a name="working-with-watchos-screen-sizes-in-xamarin"></a>Trabalhando com tamanhos de tela watchOS no Xamarin

Apple Watch está disponível em dois tamanhos de tela:

- **38 mm**
  - 136 x 170 pixels lógicos (272 x 340 pixels físicos)

- **42 mm**
  - 156 x 195 pixels lógicos (312 x 390 pixels físicos).

Você deve levar em conta o tamanho da tela ao projetar e testar seus aplicativos.

## <a name="watchos-interface-designer"></a>Designer de interface watchOS

Por padrão, o designer de Visual Studio para Mac exibirá os controladores de interface de inspeção em **qualquer Apple Watch**.

![](screen-sizes-images/screen-any-sml.png "The Designer displays watch interface controllers at Any Apple Watch")

Use o menu tamanho para editar e visualizar o storyboard em qualquer um dos tamanhos de tela disponíveis: **38 mm** ou **42 mm**:

![](screen-sizes-images/screen-menu-sml.png "Selecting the 38mm or 42mm size")

Às vezes, o tamanho de tela maior renderizará o conteúdo que seria truncado/oculto na tela menor.
Certifique-se de testar ambos os tamanhos.

### <a name="interface-design"></a>Design de interface

Seu aplicativo deve exibir o mesmo conteúdo na tela, independentemente do tamanho, e deve expandir ou contratar elementos conforme apropriado. No designer de Visual Studio para Mac, no Inspetor de atributo, você deve usar **relativo ao contêiner** ou **tamanho para ajustar o conteúdo** em preferência a tamanhos fixos.

![](screen-sizes-images/sizeattributepanel-sml.png "Use Relative to Container or Size to Fit Content in preference to fixed sizes")

Como a tela Watch está circundada por uma tampa preta, não é recomendável fornecer preenchimento em direção à sua interface. Permita que os elementos entrem na borda da tela e deixe o painel formar uma borda natural em torno do aplicativo.

## <a name="watchos-simulator"></a>Simulador de watchOS

Ao testar o simulador, você pode alternar facilmente entre os dois tamanhos de tela usando o menu de **dispositivo de > de hardware** .

![](screen-sizes-images/simulator.png "The simulator can switch between the two screen sizes using the Hardware Device menu")

## <a name="image-resources"></a>Recursos de imagem

Você deve usar vários ativos de imagem se um único ativo não parecer bom em tamanhos diferentes. Os catálogos de ativos de imagem permitem que bitmaps separados sejam especificados para cada tamanho:

![](screen-sizes-images/images-xcassets.png "Image asset catalog editor")

```csharp
// specify the asset name, the correct size will automatically be loaded
staticImage.SetImage(UIImage.FromBundle("Walkway"));
```

Como alternativa, use o código para determinar o tamanho da tela e carregar diferentes imagens completamente:

```csharp
bool large = WKInterfaceDevice.CurrentDevice.ScreenBounds.Size.Width > 136.0;
// Load image depending on screen size
using (var image = UIImage.FromBundle (large ? "42mm-Walkway" : "38mm-Walkway"))
{
   myImage.SetImage (image);

}
```

Leia mais sobre como usar o [controle imagem](~/ios/watchos/user-interface/image.md).

## <a name="related-links"></a>Links relacionados

- [Introdução ao watchOS 3](~/ios/watchos/platform/introduction-to-watchos3/index.md)
