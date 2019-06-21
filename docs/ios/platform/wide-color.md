---
title: Cores amplas no xamarin. IOS
description: Este documento discute as cores de largura e como ele pode ser usado em um aplicativo xamarin. IOS ou xamarin. Mac.
ms.prod: xamarin
ms.assetid: 576E978A-F182-489A-83E4-D8CDC6890B24
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 523aa1a97e1c536b5afbdb66c52f605382fa338d
ms.sourcegitcommit: a153623a69b5cb125f672df8007838afa32e9edf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67268799"
---
# <a name="wide-color-in-xamarinios"></a>Cores amplas no xamarin. IOS

_Este artigo aborda as cores de largura e como ele pode ser usado em um aplicativo xamarin. IOS ou xamarin. Mac._

iOS 10 e o macOS Sierra aprimora o suporte para formatos de pixel de intervalo estendido e espaços de cores de toda a gama de cores em todo o sistema, incluindo estruturas como elementos gráficos principais, imagem principal, Metal e AVFoundation. Suporte para dispositivos com telas de cores adicional é facilitado por fornecendo esse comportamento em toda a pilha inteira de gráficos.

## <a name="asset-catalogs"></a>Catálogos de ativos

### <a name="supporting-wide-color-with-asset-catalogs"></a>Suporte a cores com catálogos de ativos

No iOS 10 e no macOS Sierra, Apple expandiu os catálogos de ativo usado para incluir e categorizar o conteúdo de imagem estática no pacote do aplicativo para dar suporte a cores de largura.

Usando catálogos de ativos oferecem os seguintes benefícios para um aplicativo:

- Eles fornecem a melhor opção de implantação para ativos de imagem estática.
- Eles oferecem suporte a correção de cor automática.
- Eles dão suporte a otimização de formato de pixel automáticas.
- Eles oferecem suporte a divisão do aplicativo e diminuir o aplicativo que garante que somente o conteúdo que é relevante get entregue ao dispositivo do usuário final.

Apple tornou os seguintes aprimoramentos para catálogos de ativos para suporte a cores de largura:

- Eles oferecem suporte a conteúdo de origem de 16 bits (por canal de cor).
- Eles oferecem suporte a conteúdo de catalogação por gama de exibição. Conteúdo pode ser marcado para o monitor P3 gamas ou sRGB.

O desenvolvedor tem três opções para dar suporte a conteúdo de cores em seus aplicativos:

1. **Não faça nada** -uma vez que o conteúdo de cores amplas só deve ser usado em situações em que o aplicativo precisa para exibir cores vivas (onde eles serão aprimorar a experiência do usuário), o conteúdo fora esse requisito deve ser deixado como-está. Ele continuará a ser renderizado corretamente em todas as situações de hardware.
2. **Atualizar o conteúdo existente para o monitor P3** -isso exige que o desenvolvedor substitua o conteúdo de imagem existente em seu catálogo de ativos com um arquivo novo e atualizado no formato P3 e marcá-lo como tal no Editor ativo. No momento da compilação, uma imagem de derivativos sRGB será gerada a partir desses ativos.
3. **Fornecer conteúdo de ativos com otimização de** -nessa situação, o desenvolvedor fornecerá um sRGB de 8 bits e uma visão de monitor P3 de 16 bits de cada imagem no catálogo de ativos (corretamente marcados no editor ativo).

### <a name="asset-catalog-deployment"></a>Implantação do catálogo de ativos

O seguinte ocorrerá quando o desenvolvedor envia um aplicativo para a Store do aplicativo com catálogos de ativos que incluem o conteúdo da imagem de cores:

- Quando o aplicativo é implantado para o usuário final, aplicativo de divisão garantirá que apenas a variante de conteúdo apropriada é entregue ao dispositivo do usuário.
- No dispositivo que não dão suporte a cores amplas, não há nenhum custo de carga para incluir o conteúdo de cores, pois ele nunca é enviado ao dispositivo.
- `NSImage` no macOS Sierra (e posterior) selecionará automaticamente a melhor representação de conteúdo para exibição do hardware.
- O conteúdo exibido será atualizado automaticamente quando ou se o hardware de dispositivos exibir características de alteração.

### <a name="asset-catalog-storage"></a>Armazenamento de catálogo de ativos

Armazenamento de catálogo ativo tem as propriedades e as implicações para um aplicativo a seguir:

- No momento da compilação, o Apple tenta otimizar o armazenamento do conteúdo de imagem por meio de conversões de imagem de alta qualidade.
- 16 bits são usados por um canal de cor para o conteúdo de cores.
- Compactação de conteúdo de imagem dependente é usada para reduzir os tamanhos de conteúdo do produto. Compressões "com perdas" novos foram adicionados para otimizar ainda mais os tamanhos de conteúdo.

## <a name="rendering-off-screen-images-in-app"></a>Renderizando imagens fora da tela no aplicativo

Com base no tipo de aplicativo que está sendo criado, ele pode permitir que o usuário incluir o conteúdo da imagem que coletaram da internet ou criar conteúdo de imagem diretamente dentro do aplicativo (como um vetor de aplicativo de desenho por exemplo).

Em ambos os casos, o aplicativo pode processar as imagens necessárias fora da tela ampla cor usando os recursos aprimorados adicionados para iOS e macOS.

### <a name="drawing-wide-color-in-ios"></a>Cores amplas de desenho no iOS

Antes de discutir como corretamente desenhar uma imagem de cores de largura no iOS 10, vejamos o código de desenho de iOS comuns seguintes:

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

Há problemas com o código padrão que precisam ser abordadas _antes de_ pode ser usado para desenhar uma imagem de cores. O `UIGraphics.BeginImageContext (size)` método usado para iniciar o desenho da imagem de iOS tem as seguintes limitações:

- Ele não é possível criar os contextos de imagem com mais de 8 bits por canal de cor.
- Ele não pode representar cores em que o espaço de cores sRGB de intervalo estendido.
- Ele não tem a capacidade de fornecer uma interface para criar os contextos em um espaço de cor não sRGB devido a rotinas C baixo nível que está sendo chamadas em segundo plano.

Para lidar com essas limitações e desenhar uma imagem de cores de largura no iOS 10, use o código a seguir:

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

O novo `UIGraphicsImageRenderer` classe cria um novo contexto de imagem que é capaz de lidar com o espaço de cores sRGB de intervalo estendido e tem os seguintes recursos:

- Ele é totalmente gerenciado pelo padrão de cor.
- Ele oferece suporte para o espaço de cores sRGB de intervalo estendido por padrão.
- -Lo de maneira inteligente decide se ele deve renderizar na sRGB ou sRGB de intervalo estendido o espaço de cores com base nos recursos do dispositivo iOS que o aplicativo está sendo executado.
- Ele totalmente e automaticamente gerencia o contexto de imagem (`CGContext`) tempo de vida para que o desenvolvedor não precisa se preocupar sobre como chamar begin e end comandos de contexto.
- Ele é compatível com o `UIGraphics.GetCurrentContext()` método.

O `CreateImage` método da `UIGraphicsImageRenderer` classe é chamado para criar uma imagem de cores e passado com o contexto de imagem para desenhar em um manipulador de conclusão. Tudo o desenho é feito dentro este manipulador de conclusão da seguinte maneira:

- O `UIColor.FromDisplayP3` método cria uma nova cor vermelha totalmente saturada no espaço de cor P3 exibição ampla variedade e ele é usado para desenhar na primeira metade do retângulo. 
- A segunda metade do retângulo é desenhado no sRGB normal totalmente saturado cor vermelha para comparação.

### <a name="drawing-wide-color-in-macos"></a>Cores amplas de desenho no macOS

O `NSImage` classe foi expandido no macOS Sierra para dar suporte ao desenho de imagens coloridas ampla. Por exemplo:

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

## <a name="rendering-on-screen-images-in-app"></a>Na tela de renderização de imagens no aplicativo

Para renderizar imagens com cores ampla na tela, o processo funciona de forma semelhante ao desenho de uma imagem de fora da tela de cores amplas para macOS e iOS apresentada acima.

### <a name="rendering-on-screen-in-ios"></a>Na tela de renderização no iOS

Quando o aplicativo precisa renderizar uma imagem de cores na tela no iOS, substituir os `Draw` método da `UIView` em questão como de costume. Por exemplo:

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

Como o iOS 10 com o `UIGraphicsImageRenderer` classe mostrada acima, de maneira inteligente decide se ele deve renderizar na sRGB ou sRGB de intervalo estendido o espaço de cores com base nos recursos do dispositivo iOS que o aplicativo está sendo executado quando o `Draw` método é chamado. Além disso, o `UIImageView` tem sido cor gerenciado desde o iOS 9.3.

Se o aplicativo precisa saber como o processamento está sendo feito em um `UIView` ou `UIViewController`, ele pode verificar o novo `DisplayGamut` propriedade o `UITraitCollection` classe. Esse valor será uma `UIDisplayGamut` enum das seguintes opções:

- P3
- Srgb
- Não especificado

Se o aplicativo deseja controle qual espaço de cores é usado para desenhar uma imagem, pode usar um novo `ContentsFormat` propriedade do `CALayer` para especificar o espaço de cor desejado. Esse valor pode ser um `CAContentsFormat` enum das seguintes opções:

- Gray8Uint
- Rgba16Float
- Rgba8Uint

### <a name="rendering-on-screen-in-macos"></a>Na tela de renderização no macOS

Quando o aplicativo precisa renderizar uma imagem de cores na tela no macOS, substituir os `DrawRect` método da `NSView` em questão como de costume. Por exemplo:

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

Novamente, ela de maneira inteligente decide se ele deve renderizar na sRGB ou sRGB de intervalo estendido o espaço de cores com base nos recursos de hardware Mac que o aplicativo está sendo executado quando o `DrawRect` método é chamado.

Se o aplicativo deseja controle qual espaço de cores é usado para desenhar uma imagem, pode usar um novo `DepthLimit` propriedade do `NSWindow` classe para especificar o espaço de cor desejado. Esse valor pode ser um `NSWindowDepth` enum das seguintes opções:

- OneHundredTwentyEightBitRgb
- SixtyfourBitRgb
- TwentyfourBitRgb
