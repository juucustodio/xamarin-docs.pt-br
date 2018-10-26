---
title: Trabalhando com o watchOS tamanhos de tela no Xamarin
description: Este documento descreve como trabalhar com vários tamanhos de tela de watchOS. Ele discute o Designer de Interface de watchOS simulador, watchOS e recursos de imagem.
ms.prod: xamarin
ms.assetid: 840DF939-2F59-4ABA-87D8-92AAC8A92BC4
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: b2f4cc71c1993e51ed55b51edd7c50d393e60873
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50117493"
---
# <a name="working-with-watchos-screen-sizes-in-xamarin"></a>Trabalhando com o watchOS tamanhos de tela no Xamarin

Apple Watch está disponível em dois tamanhos de tela:

- **38mm**
  - pixels lógicos de 136 x 170 (pixels físicos de 340 x 272)

- **42mm**
  - 156 x 195 pixels lógicos (312 x 390 pixels físicos).

Tamanho da tela deve ser levar em consideração ao projetar e testar seus aplicativos.

## <a name="watchos-interface-designer"></a>Designer de Interface de watchOS

Assista a controladores de interface em por padrão, o Visual Studio para Mac Designer exibirá **Any do Apple Watch**.

![](screen-sizes-images/screen-any-sml.png "Os controladores de interface do Designer exibe Assista em qualquer de Apple Watch")

Use o menu de tamanho para editar e visualizar seu storyboard em qualquer um dos tamanhos de tela disponíveis: **38mm** ou **42mm**:

![](screen-sizes-images/screen-menu-sml.png "Selecionando o tamanho de 38mm ou 42mm")

O tamanho de tela maior, às vezes, processará o conteúdo que seria truncados/oculto na tela menor.
Certifique-se de testar em ambos os tamanhos.


### <a name="interface-design"></a>Design de interface

Seu aplicativo deve exibir o mesmo conteúdo na tela, independentemente do tamanho e deve expandir ou recolher elementos conforme apropriado. No Visual Studio para Mac Designer, no Inspetor de atributo, você deve usar **relativo ao contêiner** ou **tamanho para caber o conteúdo** em preferência tamanhos fixos.

![](screen-sizes-images/sizeattributepanel-sml.png "Usar relativo ao contêiner ou tamanho para caber o conteúdo em preferência tamanhos fixos")

Porque a tela de inspeção está circundada por uma tampa preta, fornecer o preenchimento ao redor de sua interface não é recomendado. Permitir que elementos tocar a borda da tela e permitir que o painel frontal formam uma borda natural em torno do aplicativo.


## <a name="watchos-simulator"></a>Simulador do watchOS

Quando testes no simulador, você podem alternar facilmente entre os tamanhos de tela de dois usando o **Hardware > dispositivo** menu.

![](screen-sizes-images/simulator.png "O simulador pode alternar entre os tamanhos de tela de dois usando o menu de dispositivo de Hardware")


## <a name="image-resources"></a>Recursos de imagem

Se um único ativo não boa aparência em tamanhos diferentes, você deve usar vários ativos de imagem. Catálogos de ativos de imagem permitem bitmaps separados a ser especificado para cada tamanho:

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

Leia mais sobre como usar o [controle de imagem](~/ios/watchos/user-interface/image.md).



## <a name="related-links"></a>Links relacionados

- [Introdução ao watchOS 3](~/ios/watchos/platform/introduction-to-watchos3/index.md)
