---
title: watchOS controles de imagem em Xamarin
description: Este documento descreve como usar os controles de imagem em um aplicativo de watchOS criado com o Xamarin. Ele aborda o controle WKInterfaceImage, o método SetImage, adicionar imagens a uma extensão de observação, animações e muito mais.
ms.prod: xamarin
ms.assetid: B741C207-3427-46F3-9C90-A52BF8933FA4
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: eb58c587f737a5991a21f0efe9964353a8ab0399
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34791245"
---
# <a name="watchos-image-controls-in-xamarin"></a>watchOS controles de imagem em Xamarin

watchOS fornece um [ `WKInterfaceImage` ](https://developer.xamarin.com/api/type/WatchKit.WKInterfaceImage/) controle para exibir imagens e animações simples. Alguns controles também podem ter uma imagem de plano de fundo (por exemplo, botões, grupos e controladores de interface).

![](image-images/image-walkway.png "Imagem mostrando a Apple Watch") ![ ] (image-images/image-animation.png "Apple Watch com animação simples")
<!-- watch image courtesy of http://infinitapps.com/bezel/ -->

Use imagens de catálogo de ativos para adicionar imagens a aplicativos de Kit de inspeção.
Somente **@2x** versões serão necessárias, já que todos os Assista dispositivos têm exibe Retina.

![](image-images/asset-universal-sml.png "Somente 2 x versões serão necessárias, já que todos os Assista dispositivos têm exibe Retina")

É uma boa prática para garantir que as imagens se tiverem o tamanho correto para a exibição de inspeção. *Evite* usando imagens incorretamente dimensionadas (aqueles especialmente grandes) e dimensionamento para exibi-los no relógio.

Você pode usar os tamanhos de Kit de inspeção (38mm e 42mm) em uma imagem do catálogo de ativos para especificar imagens diferentes para cada tamanho de exibição.

![](image-images/asset-watch-sml.png "Você pode usar os tamanhos de inspecionar Kit 38mm e mm 42 em uma imagem do catálogo de ativos para especificar imagens diferentes para cada tamanho de exibição")


## <a name="images-on-the-watch"></a>Imagens de inspeção

A maneira mais eficiente para exibir imagens é *incluí-los no projeto de aplicativo de inspeção* e exibi-los usando o `SetImage(string imageName)` método.

Por exemplo, o [WatchKitCatalog](https://developer.xamarin.com/samples/WatchKitCatalog/) exemplo tem um número de imagens adicionadas a um catálogo de ativos no projeto de aplicativo de inspeção:

![](image-images/asset-whale-sml.png "O exemplo WatchKitCatalog tem um número de imagens adicionadas a um catálogo de ativos no projeto de aplicativo de inspeção")

Esses podem ser com eficiência carregados e exibidos no watch usando `SetImage` com o parâmetro de nome de cadeia de caracteres:

```csharp
myImageControl.SetImage("Whale");
myOtherImageControl.SetImage("Worry");
```

### <a name="background-images"></a>Imagens de plano de fundo

A mesma lógica se aplica a `SetBackgroundImage (string imageName)` no `Button`, `Group`, e `InterfaceController` classes. Melhor desempenho é obtido por armazenar as imagens no próprio aplicativo inspeção.


## <a name="images-in-the-watch-extension"></a>Imagens na extensão de inspeção

Além de carregamento de imagens que são armazenadas no próprio aplicativo watch, você pode enviar imagens do pacote de extensão para o aplicativo de inspeção para exibição (ou você pode fazer o download de imagens de um local remoto e exiba as).

Para carregar imagens da extensão de observação, crie `UIImage` instâncias e, em seguida, chame `SetImage` com o `UIImage` objeto.

Por exemplo, o [WatchKitCatalog](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/) exemplo tem uma imagem chamada **Bumblebee** no projeto de extensão de inspeção:

![](image-images/asset-bumblebee-sml.png "O exemplo WatchKitCatalog tem uma imagem chamada Bumblebee no projeto de extensão de inspeção")

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

O [WatchKitCatalog](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/) exemplo tem uma série de imagens numeradas no projeto de aplicativo de observação com o **barramento** prefixo:

![](image-images/asset-bus-animation-sml.png "O exemplo WatchKitCatalog tem uma série de imagens numeradas no projeto de aplicativo de observação com o prefixo de barramento")

Para exibir essas imagens como uma animação, primeiro carregue a imagem usando `SetImage` com o nome de prefixo e, em seguida, chamada `StartAnimating`:

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
> aplicativos watchOS 3 executado totalmente no dispositivo. As informações a seguir são watchOS 1 apenas para aplicativos.

Se o aplicativo usa repetidamente uma imagem que é armazenada na extensão (ou foi baixada), é possível armazenar em cache a imagem no armazenamento do relógio, para aumentar o desempenho para exibições subsequentes.

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


### <a name="managing-the-cache"></a>Gerenciar o Cache

O cache de aproximadamente 20 MB de tamanho. Ele é mantido entre as reinicializações de aplicativo e quando ele está cheia, é sua responsabilidade para limpar arquivos usando `RemoveCachedImage` ou `RemoveAllCachedImages` métodos no `WKInterfaceDevice.CurrentDevice` objeto.



## <a name="related-links"></a>Links relacionados

- [WatchKitCatalog (exemplo)](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [Doc de imagem da Apple](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/Images.html)
