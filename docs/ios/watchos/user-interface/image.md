---
title: Controles de imagem no Xamarin do watchOS
description: Este documento descreve como usar controles de imagem em um aplicativo do watchOS criado com o Xamarin. Ele aborda o controle WKInterfaceImage, o método SetImage, adicionar imagens a uma extensão de inspeção, animações e muito mais.
ms.prod: xamarin
ms.assetid: B741C207-3427-46F3-9C90-A52BF8933FA4
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 6a2b8c99156963ae167aecd29a618d0feeffbdc7
ms.sourcegitcommit: 2713f2c1d74e3582704c3d0ca65b6651119ed489
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/15/2019
ms.locfileid: "56321123"
---
# <a name="watchos-image-controls-in-xamarin"></a>Controles de imagem no Xamarin do watchOS

watchOS fornece um [`WKInterfaceImage`](xref:WatchKit.WKInterfaceImage) controle para exibir imagens e animações simples. Alguns controles também podem ter uma imagem de plano de fundo (como botões, grupos e controladores de interface).

![](image-images/image-walkway.png "Imagem mostrando a Apple Watch") ![](image-images/image-animation.png "Apple Watch com animação simples")
<!-- watch image courtesy of http://infinitapps.com/bezel/ -->

Use imagens de catálogo de ativos para adicionar imagens a aplicativos de Kit de inspeção.
Somente **@2x** versões são obrigatórias, já que todos Assista dispositivos têm exibe Retina.

![](image-images/asset-universal-sml.png "Versões de x apenas 2 são necessárias, desde que todos Assista dispositivos têm exibe Retina")

É uma boa prática para garantir que as imagens em si são o tamanho correto para a exibição da inspeção. *Evite* usando imagens dimensionadas incorretamente (aqueles especialmente grandes) e dimensionamento para exibi-los no relógio.

Você pode usar os tamanhos do Kit de inspeção (38mm e 42mm) em uma imagem do catálogo de ativos para especificar imagens diferentes para cada tamanho de exibição.

![](image-images/asset-watch-sml.png "Você pode usar os tamanhos de inspeção Kit 38mm e 42mm em uma imagem do catálogo de ativos para especificar imagens diferentes para cada tamanho de tela")


## <a name="images-on-the-watch"></a>Imagens no Watch

É a maneira mais eficiente para exibir imagens *incluí-los no projeto do aplicativo watch* e exibi-las usando o `SetImage(string imageName)` método.

Por exemplo, o [WatchKitCatalog](https://developer.xamarin.com/samples/WatchKitCatalog/) exemplo tem um número de imagens adicionadas a um catálogo de ativos no projeto do aplicativo watch:

![](image-images/asset-whale-sml.png "O exemplo WatchKitCatalog tem um número de imagens adicionadas a um catálogo de ativos no projeto do aplicativo de inspeção")

Eles podem ser com eficiência carregados e exibidos no watch usando `SetImage` com o parâmetro de nome de cadeia de caracteres:

```csharp
myImageControl.SetImage("Whale");
myOtherImageControl.SetImage("Worry");
```

### <a name="background-images"></a>Imagens de plano de fundo

A mesma lógica aplica-se para o `SetBackgroundImage (string imageName)` sobre o `Button`, `Group`, e `InterfaceController` classes. Melhor desempenho é obtido ao armazenar as imagens no próprio aplicativo watch.


## <a name="images-in-the-watch-extension"></a>Imagens na extensão de inspeção

Além de carregar as imagens armazenadas no próprio aplicativo watch, você pode enviar imagens do pacote de extensão para o aplicativo de inspeção para exibição (ou você poderia fazer o download de imagens de um local remoto e exibi-las).

Para carregar imagens da extensão de inspeção, crie `UIImage` instâncias e, em seguida, chame `SetImage` com o `UIImage` objeto.

Por exemplo, o [WatchKitCatalog](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/) exemplo possui uma imagem chamada **Bumblebee** no projeto de extensão de inspeção:

![](image-images/asset-bumblebee-sml.png "O exemplo de WatchKitCatalog tem uma imagem chamada Bumblebee no projeto de extensão de inspeção")

O código a seguir resultará em:

- a imagem que está sendo carregada na memória, e
- exibido no relógio.

```csharp
using (var image = UIImage.FromBundle ("Bumblebee")) {
    myImageControl.SetImage (image);
}
```


## <a name="animations"></a>Animations

Para animar um conjunto de imagens, eles devem sempre começam com o mesmo prefixo e ter um sufixo numérico.

O [WatchKitCatalog](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/) exemplo tem uma série de imagens numeradas no projeto do aplicativo watch com o **barramento** prefixo:

![](image-images/asset-bus-animation-sml.png "O exemplo de WatchKitCatalog tem uma série de imagens numeradas no projeto do aplicativo watch com o prefixo de barramento")

Para exibir essas imagens como uma animação, primeiro carregue a imagem usando `SetImage` com o nome do prefixo e, em seguida, chamada `StartAnimating`:

```csharp
animatedImage.SetImage ("Bus");
animatedImage.StartAnimating ();
```

Chamar `StopAnimating` no controle de imagem para interromper o loop de animação:

```csharp
animatedImage.StopAnimating ();
```


<a name="cache" />

## <a name="appendix-caching-images-watchos-1"></a>Apêndice: Cache de imagens (watchOS 1)

> [!IMPORTANT]
> aplicativos watchOS 3 executado totalmente no dispositivo. As informações a seguir destina-se somente a aplicativos watchOS 1.

Se o aplicativo usa repetidamente uma imagem que é armazenada na extensão (ou tiver sido baixada), é possível armazenar em cache a imagem no armazenamento do watch, para aumentar o desempenho para exibições subsequentes.

Use o `WKInterfaceDevice`s `AddCachedImage` método para transferir a imagem para o relógio e, em seguida, usar `SetImage` com o parâmetro de nome de imagem como uma cadeia de caracteres para exibi-lo:

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

Você pode consultar o conteúdo do cache de imagem no código usando `WKInterfaceDevice.CurrentDevice.WeakCachedImages`.


### <a name="managing-the-cache"></a>Gerenciamento do Cache

O cache de cerca de 20 MB de tamanho. Ele é mantido entre as reinicializações de aplicativo, e quando ele está cheia é sua responsabilidade para limpar arquivos usando `RemoveCachedImage` ou `RemoveAllCachedImages` métodos no `WKInterfaceDevice.CurrentDevice` objeto.



## <a name="related-links"></a>Links relacionados

- [WatchKitCatalog (amostra)](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [Documento de imagem da Apple](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/Images.html)
