---
title: Trabalhando com watchOS tamanhos de tela de Xamarin
description: Este documento descreve como trabalhar com vários tamanhos de tela de watchOS. Ele aborda a Interface do Designer, o simulador, watchOS watchOS e recursos de imagem.
ms.prod: xamarin
ms.assetid: 840DF939-2F59-4ABA-87D8-92AAC8A92BC4
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 30866c70879950acd8f43fd5880b1b24ba127fa4
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34790705"
---
# <a name="working-with-watchos-screen-sizes-in-xamarin"></a>Trabalhando com watchOS tamanhos de tela de Xamarin

Apple Watch está disponível em dois tamanhos de tela:

- **38mm**
  - pixels lógicos de 136 x 170 (272 x 340 pixels físicos)

- **42mm**
  - 156 x 195 pixels lógicos (312 x 390 pixels físicos).

Tamanho da tela deve ser levar em consideração ao projetar e testar seus aplicativos.

## <a name="watchos-interface-designer"></a>watchOS Interface de Designer

Por padrão, o Visual Studio para Mac Designer exibirá Assista a controladores de interface de **qualquer do Apple Watch**.

![](screen-sizes-images/screen-any-sml.png "Os controladores de interface do Designer exibe observar em qualquer de Apple Watch")

Use o menu de tamanho para editar e visualizar o storyboard a qualquer um dos tamanhos de tela disponíveis: **38mm** ou **42mm**:

![](screen-sizes-images/screen-menu-sml.png "Selecionar o tamanho 38mm ou mm 42")

O tamanho de tela maior, às vezes, processará o conteúdo que possa ser truncado/oculto na tela menor.
Certifique-se de testar em ambos os tamanhos.


### <a name="interface-design"></a>Design de interface

Seu aplicativo deve exibir o mesmo conteúdo na tela, independentemente do tamanho e deve expandir ou recolher elementos conforme apropriado. No Visual Studio para Mac Designer, no Inspetor de atributo, você deve usar **relativo ao contêiner** ou **dimensionar para ajustar conteúdo** em vez de tamanhos fixos.

![](screen-sizes-images/sizeattributepanel-sml.png "Use relativo ao contêiner ou dimensionar para ajustar conteúdo em vez de tamanhos fixos")

Como a tela de inspeção está circundada por uma tampa preta, não é recomendável fornecer preenchimento ao redor de sua interface. Permitir que elementos tocar a borda da tela e permitem que o painel frontal formam uma borda natural em torno do aplicativo.


## <a name="watchos-simulator"></a>watchOS simulador

Quando testar no simulador, você pode alternar facilmente entre os tamanhos de tela de dois usando o **Hardware > dispositivo** menu.

![](screen-sizes-images/simulator.png "O simulador pode alternar entre os tamanhos de tela de dois usando o menu de dispositivo de Hardware")


## <a name="image-resources"></a>Recursos de imagem

Se um único ativo não tenham uma boa aparência em tamanhos diferentes, você deve usar vários ativos de imagem. Catálogos de ativos de imagem permite que bitmaps separado deve ser especificado para cada tamanho de:

![](screen-sizes-images/images-xcassets.png "Editor de catálogo de ativos de imagem")

```csharp
// specify the asset name, the correct size will automatically be loaded
staticImage.SetImage(UIImage.FromBundle("Walkway"));
```

Como alternativa, use o código para determinar o tamanho da tela e carregar imagens diferentes completamente:

```csharp
bool large = WKInterfaceDevice.CurrentDevice.ScreenBounds.Size.Width > 136.0;
// Load image depending on screen size
using (var image = UIImage.FromBundle (large ? "42mm-Walkway" : "38mm-Walkway"))
{
   myImage.SetImage (image);

}
```

Ler mais sobre como usar o [controle imagem](~/ios/watchos/user-interface/image.md).



## <a name="related-links"></a>Links relacionados

- [Introdução ao watchOS 3](~/ios/watchos/platform/introduction-to-watchos3/index.md)
