---
title: Controles de imagem watchOS no Xamarin
description: Este documento descreve como usar controles de imagem em um aplicativo watchOS criado com o Xamarin. Ele aborda o controle WKInterfaceImage, o método SetImage, adicionando imagens a uma extensão de inspeção, animações e muito mais.
ms.prod: xamarin
ms.assetid: B741C207-3427-46F3-9C90-A52BF8933FA4
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 3fd119828a953c002c7d66f248bf26b413018ae4
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86939692"
---
# <a name="watchos-image-controls-in-xamarin"></a>Controles de imagem watchOS no Xamarin

o watchOS fornece um [`WKInterfaceImage`](xref:WatchKit.WKInterfaceImage) controle para exibir imagens e animações simples. Alguns controles também podem ter uma imagem de plano de fundo (como botões, grupos e controladores de interface).

![Apple Watch mostrando ](image-images/image-walkway.png) ![ Apple Watch de imagem com animação simples](image-images/image-animation.png)
<!-- watch image courtesy of http://infinitapps.com/bezel/ -->

Use imagens do catálogo de ativos para adicionar imagens a aplicativos do kit de inspeção.
Somente **@2x** as versões são necessárias, pois todos os dispositivos de inspeção têm telas de retina.

![Somente as versões 2x são necessárias, já que todos os dispositivos de inspeção têm telas de retina](image-images/asset-universal-sml.png)

É uma boa prática garantir que as próprias imagens sejam o tamanho correto para a exibição de inspeção. *Evite* usar imagens de tamanho incorreto (especialmente grandes) e dimensionar para exibi-las no relógio.

Você pode usar os tamanhos do kit de inspeção (38 mm e 42 mm) em uma imagem de catálogo de ativos para especificar imagens diferentes para cada tamanho de exibição.

![Você pode usar os tamanhos do kit de inspeção 38 mm e 42 mm em uma imagem de catálogo de ativos para especificar imagens diferentes para cada tamanho de exibição](image-images/asset-watch-sml.png)

## <a name="images-on-the-watch"></a>Imagens na inspeção

A maneira mais eficiente de exibir imagens é *incluí-las no projeto de aplicativo de inspeção* e exibi-las usando o `SetImage(string imageName)` método.

Por exemplo, o exemplo [WatchKitCatalog](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog/) tem um número de imagens adicionadas a um catálogo de ativos no projeto de aplicativo Watch:

![O exemplo de WatchKitCatalog tem um número de imagens adicionadas a um catálogo de ativos no projeto de aplicativo de inspeção](image-images/asset-whale-sml.png)

Eles podem ser carregados e exibidos com eficiência no Watch usando `SetImage` com o parâmetro de nome da cadeia de caracteres:

```csharp
myImageControl.SetImage("Whale");
myOtherImageControl.SetImage("Worry");
```

### <a name="background-images"></a>Imagens de plano de fundo

A mesma lógica se aplica ao `SetBackgroundImage (string imageName)` nas `Button` classes, `Group` e `InterfaceController` . O melhor desempenho é obtido armazenando as imagens no próprio aplicativo Watch.

## <a name="images-in-the-watch-extension"></a>Imagens na extensão de inspeção

Além de carregar imagens armazenadas no próprio aplicativo Watch, você pode enviar imagens do pacote de extensão para o aplicativo Watch para exibição (ou pode baixar imagens de um local remoto e exibi-las).

Para carregar imagens da extensão Watch, crie `UIImage` instâncias e, em seguida, chame `SetImage` com o `UIImage` objeto.

Por exemplo, o exemplo [WatchKitCatalog](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog) tem uma imagem chamada **Bumblebee** no projeto de extensão Watch:

![O exemplo WatchKitCatalog tem uma imagem chamada Bumblebee no projeto de extensão Watch](image-images/asset-bumblebee-sml.png)

O código a seguir resultará em:

- a imagem que está sendo carregada na memória e
- exibido na inspeção.

```csharp
using (var image = UIImage.FromBundle ("Bumblebee")) {
    myImageControl.SetImage (image);
}
```

## <a name="animations"></a>Animações

Para animar um conjunto de imagens, todas elas devem começar com o mesmo prefixo e ter um sufixo numérico.

O exemplo de [WatchKitCatalog](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog) tem uma série de imagens numeradas no projeto de aplicativo de inspeção com o prefixo de **barramento** :

![O exemplo WatchKitCatalog tem uma série de imagens numeradas no projeto de aplicativo de inspeção com o prefixo de barramento](image-images/asset-bus-animation-sml.png)

Para exibir essas imagens como uma animação, primeiro carregue a imagem usando `SetImage` com o nome do prefixo e, em seguida, chame `StartAnimating` :

```csharp
animatedImage.SetImage ("Bus");
animatedImage.StartAnimating ();
```

Chame `StopAnimating` no controle de imagem para interromper o loop de animação:

```csharp
animatedImage.StopAnimating ();
```

<a name="cache"></a>

## <a name="appendix-caching-images-watchos-1"></a>Apêndice: imagens de cache (watchOS 1)

> [!IMPORTANT]
> os aplicativos watchOS 3 são executados inteiramente no dispositivo. As informações a seguir são apenas para aplicativos watchOS 1.

Se o aplicativo usar repetidamente uma imagem armazenada na extensão (ou tiver sido baixada), será possível armazenar em cache a imagem no armazenamento da inspeção, para aumentar o desempenho para exibições subsequentes.

Use o `WKInterfaceDevice` `AddCachedImage` método s para transferir a imagem para a inspeção e, em seguida, use `SetImage` com o parâmetro nome da imagem como uma cadeia de caracteres para exibi-la:

```csharp
var device = WKInterfaceDevice.CurrentDevice;
using (var image = UIImage.FromBundle ("Bumblebee")) {
    if (!device.AddCachedImage (image, "Bumblebee")) {
            Console.WriteLine ("Image cache full.");
        } else {
            cachedImage.SetImage ("Bumblebee");
        }
    }
}
```

Você pode consultar o conteúdo do cache de imagem no código usando `WKInterfaceDevice.CurrentDevice.WeakCachedImages` .

### <a name="managing-the-cache"></a>Gerenciando o cache

O cache cerca de 20 MB de tamanho. Ele é mantido entre as reinicializações do aplicativo e, quando ele é preenchido, é sua responsabilidade limpar os arquivos `RemoveCachedImage` usando `RemoveAllCachedImages` os métodos ou no `WKInterfaceDevice.CurrentDevice` objeto.

## <a name="related-links"></a>Links Relacionados

- [WatchKitCatalog (exemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [Documento de imagem da Apple](https://developer.apple.com/documentation/watchkit/wkinterfaceimage)
