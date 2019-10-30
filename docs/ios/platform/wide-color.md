---
title: Cor larga no Xamarin. iOS
description: Este documento discute toda a cor e como ela pode ser usada em um aplicativo Xamarin. iOS ou Xamarin. Mac.
ms.prod: xamarin
ms.assetid: 576E978A-F182-489A-83E4-D8CDC6890B24
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: e7240a271de1f0199c2c9fc045f5c95745eb98c5
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031254"
---
# <a name="wide-color-in-xamarinios"></a>Cor larga no Xamarin. iOS

_Este artigo aborda toda a cor e como ela pode ser usada em um aplicativo Xamarin. iOS ou Xamarin. Mac._

o iOS 10 e o macOS Sierra aprimoram o suporte para formatos de pixel de intervalo estendido e espaços de cores de ampla gama em todo o sistema, incluindo estruturas como gráficos principais, imagem principal, metal e AVFoundation. O suporte para dispositivos com monitores largos de cores é mais facilitou, fornecendo esse comportamento em toda a pilha gráfica.

## <a name="asset-catalogs"></a>Catálogos de ativos

### <a name="supporting-wide-color-with-asset-catalogs"></a>Suporte a cores largos com catálogos de ativos

No iOS 10 e macOS Sierra, a Apple expandiu os catálogos de ativos usados para incluir e categorizar o conteúdo da imagem estática no pacote do aplicativo para dar suporte a cores amplas.

O uso de catálogos de ativos fornece os seguintes benefícios para um aplicativo:

- Eles fornecem a melhor opção de implantação para ativos de imagem estática.
- Eles dão suporte à correção automática de cores.
- Eles dão suporte à otimização automática de formato de pixel.
- Eles dão suporte à divisão de aplicativos e à Finamento de aplicativos, o que garante que apenas o conteúdo relevante seja entregue ao dispositivo do usuário final.

A Apple fez os seguintes aprimoramentos aos catálogos de ativos para obter suporte a cores largos:

- Eles dão suporte ao conteúdo de origem de 16 bits (por canal de cores).
- Eles dão suporte à catalogação de conteúdo por gama de exibições. O conteúdo pode ser marcado para o sRGB ou para exibir as cores P3.

O desenvolvedor tem três opções para dar suporte ao conteúdo de cores largos em seus aplicativos:

1. **Não fazer nada** -como o conteúdo de cores largos só deve ser usado em situações em que o aplicativo precise exibir cores vivas (onde eles aprimorarão a experiência do usuário), o conteúdo fora desse requisito deverá ser deixado como está. Ele continuará a ser renderizado corretamente em todas as situações de hardware.
2. **Atualizar conteúdo existente para exibir P3** -isso exige que o desenvolvedor substitua o conteúdo da imagem existente em seu catálogo de ativos por um novo arquivo atualizado no formato P3 e marque-o como tal no editor de ativos. No momento da compilação, uma imagem derivada do sRGB será gerada a partir desses ativos.
3. **Fornecer conteúdo de ativo otimizado** -nessa situação, o desenvolvedor fornecerá um sRGB de 8 bits e uma visão de exibição P3 de 16 bits de cada imagem no catálogo de ativos (corretamente marcado no editor de ativos).

### <a name="asset-catalog-deployment"></a>Implantação do catálogo de ativos

O seguinte ocorrerá quando o desenvolvedor enviar um aplicativo para a loja de aplicativos com catálogos de ativos que incluem conteúdo de imagem de cor ampla:

- Quando o aplicativo for implantado no usuário final, a fatia do aplicativo garantirá que apenas a variante de conteúdo apropriada seja entregue ao dispositivo do usuário.
- No dispositivo que não oferece suporte a cores amplas, não há nenhum custo de carga para incluir o conteúdo de cores largos, pois ele nunca é enviado para o dispositivo.
- `NSImage` no macOS Sierra (e posterior) selecionará automaticamente a melhor representação de conteúdo para a exibição do hardware.
- O conteúdo exibido será atualizado automaticamente quando ou se as características de exibição de hardware dos dispositivos forem alteradas.

### <a name="asset-catalog-storage"></a>Armazenamento do catálogo de ativos

O armazenamento do catálogo de ativos tem as seguintes propriedades e implicações para um aplicativo:

- No momento da compilação, a Apple tenta otimizar o armazenamento do conteúdo da imagem por meio de conversões de imagem de alta qualidade.
- 16 bits são usados por canal de cor para o conteúdo de cores largos.
- A compactação de imagem dependente de conteúdo é usada para reduzir os tamanhos de conteúdo de entrega. Novas compactação "com perdas" foram adicionadas para otimizar ainda mais os tamanhos de conteúdo.

## <a name="rendering-off-screen-images-in-app"></a>Renderizando imagens fora da tela no aplicativo

Com base no tipo de aplicativo que está sendo criado, ele pode permitir que o usuário inclua conteúdo de imagem coletado da Internet ou Crie conteúdo de imagem diretamente dentro do aplicativo (como um aplicativo de desenho vetorial, por exemplo).

Em ambos os casos, o aplicativo pode renderizar as imagens necessárias fora da tela na cor larga usando recursos avançados adicionados ao iOS e ao macOS.

### <a name="drawing-wide-color-in-ios"></a>Desenho de cores largos no iOS

Antes de discutir como desenhar corretamente uma imagem em larga cor no iOS 10, examine o seguinte código de desenho do iOS comum:

```csharp
public UIImage DrawWideColorImage ()
{
    var size = new CGSize (250, 250);
    UIGraphics.BeginImageContext (size);

    ...

    UIGraphics.EndImageContext ();
    return image = UIGraphics.GetImageFromCurrentImageContext ();
    }
```

Há problemas com o código padrão que precisará ser resolvido _antes_ que ele possa ser usado para desenhar uma imagem de cor ampla. O método de `UIGraphics.BeginImageContext (size)` usado para iniciar o desenho de imagem iOS tem as seguintes limitações:

- Ele não pode criar contextos de imagem com mais de 8 bits por canal de cor.
- Ele não pode representar cores no espaço de cores sRGB de intervalo estendido.
- Ele não tem a capacidade de fornecer uma interface para criar contextos em um espaço de cores não sRGB devido às rotinas de baixo nível C sendo chamadas em segundo plano.

Para lidar com essas limitações e desenhar uma imagem em larga cor no iOS 10, use o código a seguir em vez disso:

```csharp
public UIImage DrawWideColorImage ()
{
    var size = new CGSize (250, 250);
    var render = new UIGraphicsImageRenderer (size);

    var image = render.CreateImage ((UIGraphicsImageRendererContext context) => {
        var bounds = context.Format.Bounds;
        CGRect slice;
        CGRect remainder;
        bounds.Divide (bounds.Width / 2, CGRectEdge.MinXEdge, out slice, out remainder);

        var redP3 = UIColor.FromDisplayP3 (1.0F, 0.0F, 0.0F, 1.0F);
        redP3.SetColor ();
        context.FillRect (slice);

        var redRGB = UIColor.Red;
        redRGB.SetColor ();
        context.FillRect (remainder);
    });

    // Return results
    return image;
}
```

A nova classe `UIGraphicsImageRenderer` cria um novo contexto de imagem que é capaz de manipular o espaço de cores sRGB de intervalo estendido e tem os seguintes recursos:

- Por padrão, ele é totalmente gerenciado por cores.
- Ele dá suporte ao espaço de cores sRGB de intervalo estendido por padrão.
- Ele decide de forma inteligente se deve ser renderizado no espaço de cores sRGB ou de intervalo estendido sRGB com base nos recursos do dispositivo iOS em que o aplicativo está sendo executado.
- Ele gerencia de forma completa e automática o tempo de vida do contexto de imagem (`CGContext`) para que o desenvolvedor não precise se preocupar em chamar comandos de contexto Begin e end.
- Ele é compatível com o método `UIGraphics.GetCurrentContext()`.

O método `CreateImage` da classe `UIGraphicsImageRenderer` é chamado para criar uma imagem de cor larga e passou um manipulador de conclusão com o contexto de imagem para o qual desenhar. Todo o desenho é feito dentro desse manipulador de conclusão da seguinte maneira:

- O método `UIColor.FromDisplayP3` cria uma nova cor vermelha totalmente saturada no espaço de cor de uma grande gama de cores e é usado para desenhar a primeira metade do retângulo. 
- A segunda metade do retângulo é desenhada na cor vermelha totalmente saturada do sRGB para comparação.

### <a name="drawing-wide-color-in-macos"></a>Desenho de cores largos no macOS

A classe `NSImage` foi expandida em macOS Sierra para dar suporte ao desenho de imagens de cores largos. Por exemplo:

```csharp
var size = CGSize(250,250);
var wideColorImage = new NSImage(size, false, (drawRect) =>{
    var rects = drawRect.Divide(drawRect.Size.Width/2,CGRect.MinXEdge);

    var color = new NSColor(1.0, 0.0, 0.0, 1.0).Set();
    var path = new NSBezierPath(rects.Slice).Fill();

    color = new NSColor(1.0, 0.0, 0.0, 1.0).Set();
    path = new NSBezierPath(rects.Remainder).Fill();

    // Return modified
    return true;
});
```

## <a name="rendering-on-screen-images-in-app"></a>Renderizando imagens na tela no aplicativo

Para renderizar imagens de cores amplas na tela, o processo funciona de forma semelhante a desenhar uma imagem de cor de toda a tela para macOS e iOS apresentados acima.

### <a name="rendering-on-screen-in-ios"></a>Renderização na tela no iOS

Quando o aplicativo precisar renderizar uma imagem em uma cor larga na tela no iOS, substitua o método `Draw` do `UIView` em questão como de costume. Por exemplo:

```csharp
using System;
using UIKit;
using CoreGraphics;

namespace MonkeyTalk
{
    public class MonkeyView : UIView
    {
        public MonkeyView ()
        {
        }

        public override void Draw (CGRect rect)
        {
            base.Draw (rect);

            // Draw the image to screen
        ...
        }
    }
}
```

Como o iOS 10 faz com a classe `UIGraphicsImageRenderer` mostrada acima, ele decide de forma inteligente se deve renderizar o espaço de cores sRGB ou do intervalo estendido com base nos recursos do dispositivo iOS em que o aplicativo está sendo executado quando o método `Draw` é chamado. Além disso, o `UIImageView` tem sido gerenciado por cores desde o iOS 9,3 também.

Se o aplicativo precisar saber como a renderização está sendo feita em um `UIView` ou `UIViewController`, ela poderá verificar a nova propriedade `DisplayGamut` da classe `UITraitCollection`. Esse valor será um `UIDisplayGamut` enumeração do seguinte:

- P3
- SRGB
- Não especificado

Se o aplicativo quiser controlar qual espaço de cores é usado para desenhar uma imagem, ele poderá usar uma nova propriedade `ContentsFormat` da `CALayer` para especificar o espaço de cores desejado. Esse valor pode ser uma `CAContentsFormat` enumeração do seguinte:

- Gray8Uint
- Rgba16Float
- Rgba8Uint

### <a name="rendering-on-screen-in-macos"></a>Renderização na tela no macOS

Quando o aplicativo precisa renderizar uma imagem em cores largas na tela no macOS, substitua o método `DrawRect` do `NSView` em questão como de costume. Por exemplo:

```csharp
using System;
using AppKit;
using CoreGraphics;
using CoreAnimation;

namespace MonkeyTalkMac
{
    public class MonkeyView : NSView
    {
        public MonkeyView ()
        {
        }

        public override void DrawRect (CGRect dirtyRect)
        {
            base.DrawRect (dirtyRect);

            // Draw graphics into the view
            ...
        }
    }
}
```

Novamente, ele decide de forma inteligente se deve ser renderizado no espaço de cores sRGB ou de intervalo estendido sRGB com base nos recursos do hardware do Mac em que o aplicativo está sendo executado quando o método `DrawRect` é chamado.

Se o aplicativo quiser controlar qual espaço de cores é usado para desenhar uma imagem, ele poderá usar uma nova propriedade `DepthLimit` da classe `NSWindow` para especificar o espaço de cores desejado. Esse valor pode ser uma `NSWindowDepth` enumeração do seguinte:

- OneHundredTwentyEightBitRgb
- SixtyfourBitRgb
- TwentyfourBitRgb
