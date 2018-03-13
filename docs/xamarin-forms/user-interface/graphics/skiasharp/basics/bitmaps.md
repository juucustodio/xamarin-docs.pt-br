---
title: "Noções básicas de bitmap"
description: "Carregue os bitmaps de várias fontes e exibi-los."
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 32C95DFF-9065-42D7-966C-D3DBD16906B3
author: charlespetzold
ms.author: chape
ms.date: 04/03/2017
ms.openlocfilehash: facdcb6231b77714d6f5d0c7d496db12f75b1d08
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2018
---
# <a name="bitmap-basics"></a>Noções básicas de bitmap

_Carregue os bitmaps de várias fontes e exibi-los._

O suporte de bitmaps em SkiaSharp é muito extenso. Este artigo aborda apenas as Noções básicas de & #x 2014; como carregar bitmaps e como exibi-los:

![](bitmaps-images/bitmapssample.png "A exibição de dois bitmaps")

Um bitmap SkiaSharp é um objeto do tipo [ `SKBitmap` ](https://developer.xamarin.com/api/type/SkiaSharp.SKBitmap/). Há muitas maneiras de criar um bitmap, mas este artigo restringe-se para o [ `SKBitmap.Decode` ](https://developer.xamarin.com/api/member/SkiaSharp.SKBitmap.Decode/p/SkiaSharp.SKStream/) método, que carrega o bitmap de um [ `SKStream` ](https://developer.xamarin.com/api/type/SkiaSharp.SKStream/) objeto que faz referência a um arquivo de bitmap. É conveniente usar o [ `SKManagedStream` ](https://developer.xamarin.com/api/type/SkiaSharp.SKManagedStream/) classe que deriva de `SKStream` porque ele tem um construtor que aceite um .NET [ `Stream` ](https://developer.xamarin.com/api/type/System.IO.Stream/) objeto.

O **Bitmaps básico** página o **SkiaSharpFormsDemos** programa demonstra como carregar bitmaps de três fontes diferentes:

- Na Internet
- De um recurso incorporado no executável
- Da biblioteca de fotos do usuário

Três `SKBitmap` objetos essas três fontes são definidos como campos de [ `BasicBitmapsPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Basics/BasicBitmapsPage.cs) classe:

```csharp
public class BasicBitmapsPage : ContentPage
{
    SKCanvasView canvasView;
    SKBitmap webBitmap;
    SKBitmap resourceBitmap;
    SKBitmap libraryBitmap;

    public BasicBitmapsPage()
    {
        Title = "Basic Bitmaps";

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
        ...
    }
    ...
}
```

## <a name="loading-a-bitmap-from-the-web"></a>Carregar um Bitmap da Web

Para carregar um bitmap com base em uma URL, você pode usar o [ `WebRequest` ](https://developer.xamarin.com/api/type/System.Net.WebRequest/) classe, conforme mostrado no código a seguir executado no `BasicBitmapsPage` construtor. Aqui, a URL aponta para uma área no site da web Xamarin com alguns exemplos de bitmaps. Um pacote no site da web permite anexar uma especificação para redimensionar o bitmap para uma largura específica:

```csharp
Uri uri = new Uri("http://developer.xamarin.com/demo/IMG_3256.JPG?width=480");
WebRequest request = WebRequest.Create(uri);
request.BeginGetResponse((IAsyncResult arg) =>
{
    try
    {
        using (Stream stream = request.EndGetResponse(arg).GetResponseStream())
        using (MemoryStream memStream = new MemoryStream())
        {
            stream.CopyTo(memStream);
            memStream.Seek(0, SeekOrigin.Begin);

            using (SKManagedStream skStream = new SKManagedStream(memStream))
            {
                webBitmap = SKBitmap.Decode(skStream);
            }
        }
    }
    catch
    {
    }

    Device.BeginInvokeOnMainThread(() => canvasView.InvalidateSurface());

}, null);
```

Quando o bitmap foi baixado com êxito, o método de retorno de chamada passada para o `BeginGetResponse` execuções do método. O `EndGetResponse` chamada deve estar em um `try` bloquear no caso de erro. O `Stream` obtido do objeto `GetResponseStream` não é adequado em algumas plataformas, então o conteúdo de bitmap é copiado para um `MemoryStream` objeto. Neste ponto, o `SKManagedStream` objeto pode ser criado. Agora faz referência ao arquivo de bitmap, que é provavelmente um arquivo JPEG ou PNG. O `SKBitmap.Decode` método decodifica o arquivo de bitmap e armazena os resultados em um formato SkiaSharp interno.

O método de retorno de chamada transmitido para `BeginGetResponse` executa depois que o construtor concluiu a execução, o que significa que o `SKCanvasView` precisa ser invalidados para permitir que o `PaintSurface` manipulador para atualizar a exibição. No entanto, o `BeginGetResponse` retorno de chamada é executado em um thread secundário de execução, portanto, é necessário usar `Device.BeginInvokeOnMainThread` para executar o `InvalidateSurface` método no thread de interface do usuário.

## <a name="loading-a-bitmap-resource"></a>Carregar um recurso de Bitmap

Em termos de código, a abordagem mais fácil para carregar os bitmaps incluem um recurso de bitmap diretamente em seu aplicativo. O **SkiaSharpFormsDemos** programa inclui uma pasta denominada **mídia** que contém um arquivo de bitmap chamado **monkey.png**. No **propriedades** caixa de diálogo para esse arquivo, você deve fornecer um arquivo um **ação de compilação** de **recurso incorporado**!

Cada recurso inserido tem um *ID de recurso* que consiste no nome do projeto, a pasta e o nome do arquivo, todos os conectados por períodos: **SkiaSharpFormsDemos.Media.monkey.png**. Você pode obter acesso a este recurso, especificando o recurso ID como um argumento para o [ `GetManifestResourceStream` ](https://developer.xamarin.com/api/member/System.Reflection.Assembly.GetManifestResourceStream/p/System.String/) método o [ `Assembly` ](https://developer.xamarin.com/api/type/System.Reflection.Assembly/) classe:

```csharp
string resourceID = "SkiaSharpFormsDemos.Media.monkey.png";
Assembly assembly = GetType().GetTypeInfo().Assembly;

using (Stream stream = assembly.GetManifestResourceStream(resourceID))
using (SKManagedStream skStream = new SKManagedStream(stream))
{
    resourceBitmap = SKBitmap.Decode(skStream);
}
```

Isso `Stream` objeto pode ser convertido diretamente em um `SKManagedStream` objeto.

## <a name="loading-a-bitmap-from-the-photo-library"></a>Carregar um Bitmap da biblioteca de fotos

Também é possível que o usuário carregar uma foto da biblioteca de imagens do dispositivo. Este recurso não é fornecido pelo xamarin. Forms em si. O trabalho exigir uma dependência de serviço, como descrito no artigo [uma foto de separação da biblioteca de imagens](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md).

O **IPicturePicker.cs** de arquivo e os três **PicturePickerImplementation.cs** arquivos de artigo foram copiados para vários projetos do **SkiaSharpFormsDemos**solução e recebe novos nomes de namespace. Além disso, o Android **MainActivity.cs** arquivo tiver sido modificado, conforme descrito no artigo e o projeto iOS tem permissão para acessar a biblioteca de fotos com duas linhas na parte inferior do **Info. plist**  arquivo.

O `BasicBitmapsPage` construtor adiciona um `TapGestureRecognizer` para o `SKCanvasView` para ser notificado de toques. Ao receber um toque, o `Tapped` manipulador obtém acesso ao serviço de dependência do seletor de imagem e chamadas `GetImageStreamAsync`. Se um `Stream` objeto é retornado, em seguida, o conteúdo é copiado em um `MemoryStream`, conforme solicitado pelo algumas das plataformas. O restante do código é semelhante às duas outras técnicas:

```csharp
// Add tap gesture recognizer
TapGestureRecognizer tapRecognizer = new TapGestureRecognizer();
tapRecognizer.Tapped += async (sender, args) =>
{
    // Load bitmap from photo library
    IPicturePicker picturePicker = DependencyService.Get<IPicturePicker>();

    using (Stream stream = await picturePicker.GetImageStreamAsync())
    {
        if (stream != null)
        {
            using (MemoryStream memStream = new MemoryStream())
            {
                stream.CopyTo(memStream);
                memStream.Seek(0, SeekOrigin.Begin);

                using (SKManagedStream skStream = new SKManagedStream(memStream))
                {
                    libraryBitmap = SKBitmap.Decode(skStream);
                }
            }
            canvasView.InvalidateSurface();
        }
    }
};
canvasView.GestureRecognizers.Add(tapRecognizer);
```

Observe que o `Tapped` chamadas do manipulador de `InvalidateSurface` método do `SKCanvasView` objeto. Isso gera uma nova chamada para o `PaintSurface` manipulador.

## <a name="displaying-the-bitmaps"></a>Exibindo os Bitmaps

O `PaintSurface` manipulador precisa exibir três bitmaps. O manipulador pressupõe que o telefone está no modo retrato e a tela se divide verticalmente em três partes iguais.

O bitmap primeiro é exibido com mais simples [ `DrawBitmap` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawBitmap/p/SkiaSharp.SKBitmap/System.Single/System.Single/SkiaSharp.SKPaint/) método. Você precisa especificar são as coordenadas X e Y, onde o canto superior esquerdo do bitmap deve ser posicionado:

```csharp
public void DrawBitmap (SKBitmap bitmap, Single x, Single y, SKPaint paint = null)
```

Embora um `SKPaint` parâmetro for definido, ele tem um valor padrão de `null` e você pode ignorá-lo. Os pixels do bitmap simplesmente são transferidos para os pixels da superfície de exibição com um mapeamento a um.

Um programa pode obter as dimensões de pixel de um bitmap com o [ `Width` ](https://developer.xamarin.com/api/property/SkiaSharp.SKBitmap.Width/) e [ `Height` ](https://developer.xamarin.com/api/property/SkiaSharp.SKBitmap.Height/) propriedades. Essas propriedades permitem que o programa calcular as coordenadas para posicionar o bitmap no centro da terceira superior da tela:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    if (webBitmap != null)
    {
        float x = (info.Width - webBitmap.Width) / 2;
        float y = (info.Height / 3 - webBitmap.Height) / 2;
        canvas.DrawBitmap(webBitmap, x, y);
    }
    ...
}
```

Os outros dois bitmaps são exibidos com uma versão do [ `DrawBitmap` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawBitmap/p/SkiaSharp.SKBitmap/SkiaSharp.SKRect/SkiaSharp.SKPaint/) com um `SKRect` parâmetro:

```csharp
public void DrawBitmap (SKBitmap bitmap, SKRect dest, SKPaint paint = null)
```

Uma terceira versão do [ `DrawBitmap` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawBitmap/p/SkiaSharp.SKBitmap/SkiaSharp.SKRect/SkiaSharp.SKRect/SkiaSharp.SKPaint/) tem duas `SKRect` argumentos para especificar um subconjunto retangular do bitmap para exibição, mas essa versão não é usado neste artigo.

Aqui está o código para exibir o bitmap carregado de um bitmap de recurso inserido:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    if (resourceBitmap != null)
    {
        canvas.DrawBitmap(resourceBitmap,
            new SKRect(0, info.Height / 3, info.Width, 2 * info.Height / 3));
    }
    ...
}
```

O bitmap é ampliado para as dimensões do retângulo, o motivo pelo qual o monkey é ampliado horizontalmente nessas capturas de tela:

[![](bitmaps-images/basicbitmaps-small.png "Uma captura de tela tripla da página básica Bitmaps")](bitmaps-images/basicbitmaps-large.png#lightbox "uma tripla captura de tela da página básica Bitmaps")

A terceira imagem & #x 2014; que você pode ver somente se você executar o programa e carregar uma foto de sua própria biblioteca de imagens & #x 2014; também é exibido dentro de um retângulo, mas o retângulo posição e tamanho são ajustados para manter a taxa de proporção do bitmap. Esse cálculo é um pouco mais envolvido porque ele requer o cálculo de um fator com base no tamanho do bitmap e o retângulo de destino de escala e centralizar o retângulo nessa área:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    if (libraryBitmap != null)
    {
        float scale = Math.Min((float)info.Width / libraryBitmap.Width,
                               info.Height / 3f / libraryBitmap.Height);

        float left = (info.Width - scale * libraryBitmap.Width) / 2;
        float top = (info.Height / 3 - scale * libraryBitmap.Height) / 2;
        float right = left + scale * libraryBitmap.Width;
        float bottom = top + scale * libraryBitmap.Height;
        SKRect rect = new SKRect(left, top, right, bottom);
        rect.Offset(0, 2 * info.Height / 3);

        canvas.DrawBitmap(libraryBitmap, rect);
    }
    else
    {
        using (SKPaint paint = new SKPaint())
        {
            paint.Color = SKColors.Blue;
            paint.TextAlign = SKTextAlign.Center;
            paint.TextSize = 48;

            canvas.DrawText("Tap to load bitmap",
                info.Width / 2, 5 * info.Height / 6, paint);
        }
    }
}
```

Se nenhum bitmap ainda tenha sido carregado da biblioteca de imagens, em seguida, o `else` bloco exibe algum texto para solicitar ao usuário para toque na tela.


## <a name="related-links"></a>Links relacionados

- [APIs de SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (sample)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/SkiaSharpFormsDemos/)
- [Escolhendo uma foto na biblioteca de imagens](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md)
