---
title: Noções básicas de bitmap no SkiaSharp
description: Este artigo explica como carregar bitmaps em SkiaSharp de várias fontes e exibi-los nos aplicativos xamarin. Forms e demonstra isso com o código de exemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 32C95DFF-9065-42D7-966C-D3DBD16906B3
author: davidbritch
ms.author: dabritch
ms.date: 07/17/2018
ms.openlocfilehash: f43779fd0a61bd3ad04f3f7445faa6517fb9c989
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68645893"
---
# <a name="bitmap-basics-in-skiasharp"></a>Noções básicas de bitmap no SkiaSharp

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Carregar bitmaps de várias fontes e exibi-los._

O suporte de bitmaps em SkiaSharp é muito extenso. Este artigo aborda apenas as Noções básicas &mdash; como carregar bitmaps e como exibi-los:

![](bitmaps-images/bitmapssample.png "A exibição de dois bitmaps")

Uma exploração muito mais profunda de bitmaps pode ser encontrada na seção [SkiaSharp Bitmaps](../bitmaps/index.md).

Um bitmap de SkiaSharp é um objeto do tipo [ `SKBitmap` ](xref:SkiaSharp.SKBitmap). Há várias maneiras para criar um bitmap, mas este artigo restringe-se para o [ `SKBitmap.Decode` ](xref:SkiaSharp.SKBitmap.Decode(System.IO.Stream)) método, que carrega o bitmap de um .NET `Stream` objeto.

O **Bitmaps básica** página na **SkiaSharpFormsDemos** programa demonstra como carregar bitmaps de três fontes diferentes:

- Na Internet
- De um recurso incorporado no executável
- Da biblioteca de fotos do usuário

Três `SKBitmap` objetos para essas três fontes são definidos como campos na [ `BasicBitmapsPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/BasicBitmapsPage.cs) classe:

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

Para carregar um bitmap com base em uma URL, você pode usar o [ `HttpClient` ](/dotnet/api/system.net.http.httpclient?view=netstandard-2.0) classe. Você deve instanciar apenas uma instância de `HttpClient` e reutilizá-lo, então, armazená-lo como um campo:

```csharp
HttpClient httpClient = new HttpClient();
```

Ao usar `HttpClient` com aplicativos iOS e Android, você vai querer definir propriedades do projeto, conforme descrito nos documentos  **[Transport Layer Security (TLS) 1.2](~/cross-platform/app-fundamentals/transport-layer-security.md)** .

Porque é mais conveniente usar o `await` operador com `HttpClient`, o código não pode ser executado no `BasicBitmapsPage` construtor. Em vez disso, ele faz parte do `OnAppearing` substituir. Aqui, a URL aponta para uma área no site do Xamarin com alguns exemplos de bitmaps. Um pacote no site da web permite acrescentar uma especificação para redimensionar o bitmap para uma largura específica:


```csharp
protected override async void OnAppearing()
{
    base.OnAppearing();

    // Load web bitmap.
    string url = "https://developer.xamarin.com/demo/IMG_3256.JPG?width=480";

    try
    {
        using (Stream stream = await httpClient.GetStreamAsync(url))
        using (MemoryStream memStream = new MemoryStream())
        {
            await stream.CopyToAsync(memStream);
            memStream.Seek(0, SeekOrigin.Begin);

            webBitmap = SKBitmap.Decode(stream);
            canvasView.InvalidateSurface();
        };
    }
    catch
    {
    }
}
```

O sistema operacional Android gera uma exceção ao usar o `Stream` retornado de `GetStreamAsync` no `SKBitmap.Decode` método porque ele está executando uma operação demorada em um thread principal. Por esse motivo, o conteúdo do arquivo de bitmap é copiado para um `MemoryStream` do objeto usando `CopyToAsync`.

Estático `SKBitmap.Decode` método é responsável pela decodificação de arquivos de bitmap. Ele funciona com formatos de bitmap GIF, PNG e JPEG e armazena os resultados em um formato de SkiaSharp interno. Neste ponto, o `SKCanvasView` precisa ser invalidada para permitir que o `PaintSurface` manipulador para atualizar a exibição. 

## <a name="loading-a-bitmap-resource"></a>Carregar um recurso de Bitmap

Em termos de código, a abordagem mais fácil para carregar os bitmaps está incluindo um recurso de bitmap diretamente em seu aplicativo. O **SkiaSharpFormsDemos** programa inclui uma pasta chamada **mídia** que contém vários arquivos, incluindo um denominado de bitmap **monkey.png**. Para bitmaps armazenados como recursos do programa, você deve usar o **propriedades** caixa de diálogo dê ao arquivo um **Build Action** dos **Embedded Resource**!

Cada recurso inserido tem uma *ID de recurso* que consiste no nome do projeto, na pasta e no nome do arquivo, todos conectados por pontos: **SkiaSharpFormsDemos.Media.monkey.png**. Você pode obter acesso a esse recurso com a especificação desse recurso ID como um argumento para o [ `GetManifestResourceStream` ](xref:System.Reflection.Assembly.GetManifestResourceStream(System.String)) método da [ `Assembly` ](xref:System.Reflection.Assembly) classe:

```csharp
string resourceID = "SkiaSharpFormsDemos.Media.monkey.png";
Assembly assembly = GetType().GetTypeInfo().Assembly;

using (Stream stream = assembly.GetManifestResourceStream(resourceID))
{
    resourceBitmap = SKBitmap.Decode(stream);
}
```

Isso `Stream` objeto pode ser passado diretamente para o `SKBitmap.Decode` método.

## <a name="loading-a-bitmap-from-the-photo-library"></a>Carregar um Bitmap da biblioteca de fotos

Também é possível que o usuário carregar uma foto da biblioteca de imagens do dispositivo. Esse recurso não é fornecido pelo xamarin. Forms em si. O trabalho exigir um serviço de dependência, como descrito no artigo [escolhendo uma foto na biblioteca de imagens](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md).

O **IPhotoLibrary.cs** arquivo na **SkiaSharpFormsDemos** projeto e os três **PhotoLibrary.cs** arquivos em projetos de plataforma foram adaptados do artigo. Além disso, o Android **MainActivity.cs** arquivo foi modificado conforme descrito no artigo e o projeto do iOS tem permissão para acessar a biblioteca de fotos com duas linhas ao final de **Info. plist**  arquivo.

O `BasicBitmapsPage` construtor adiciona um `TapGestureRecognizer` para o `SKCanvasView` para ser notificado de toques. Após o recebimento de um toque, o `Tapped` manipulador obtém acesso ao serviço de dependência do seletor de imagem e chamadas `PickPhotoAsync`. Se um `Stream` objeto é retornado e, em seguida, ele é passado para o `SKBitmap.Decode` método:

```csharp
// Add tap gesture recognizer
TapGestureRecognizer tapRecognizer = new TapGestureRecognizer();
tapRecognizer.Tapped += async (sender, args) =>
{
    // Load bitmap from photo library
    IPhotoLibrary photoLibrary = DependencyService.Get<IPhotoLibrary>();

    using (Stream stream = await photoLibrary.PickPhotoAsync())
    {
        if (stream != null)
        {
            libraryBitmap = SKBitmap.Decode(stream);
            canvasView.InvalidateSurface();
        }
    }
};
canvasView.GestureRecognizers.Add(tapRecognizer);
```

Observe que o `Tapped` também chama um manipulador a `InvalidateSurface` método o `SKCanvasView` objeto. Isso gera uma nova chamada para o `PaintSurface` manipulador.

## <a name="displaying-the-bitmaps"></a>Exibindo os Bitmaps

O `PaintSurface` manipulador precisa exibir três bitmaps. O manipulador pressupõe que o telefone está no modo retrato e divide a tela verticalmente em três partes iguais.

O bitmap primeiro é exibido com a forma mais simples [ `DrawBitmap` ](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,System.Single,System.Single,SkiaSharp.SKPaint)) método. Você só precisa especificar são as coordenadas X e Y em que o canto superior esquerdo do bitmap é posicionado:

```csharp
public void DrawBitmap (SKBitmap bitmap, Single x, Single y, SKPaint paint = null)
```

Embora uma `SKPaint` parâmetro é definido, ele tem um valor padrão de `null` e você pode ignorá-lo. Os pixels do bitmap simplesmente são transferidos para os pixels da superfície de exibição com um mapeamento individual. Você verá um aplicativo para este `SKPaint` argumento na próxima seção sobre [ **SkiaSharp transparência**](transparency.md).

Um programa pode obter as dimensões de pixel de um bitmap com o [ `Width` ](xref:SkiaSharp.SKBitmap.Width) e [ `Height` ](xref:SkiaSharp.SKBitmap.Height) propriedades. Essas propriedades permitem que o programa calcular as coordenadas para posicionar o bitmap no centro do terceiro superior da tela:

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

Os outros dois bitmaps são exibidos com uma versão do [ `DrawBitmap` ](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKRect,SkiaSharp.SKPaint)) com um `SKRect` parâmetro:

```csharp
public void DrawBitmap (SKBitmap bitmap, SKRect dest, SKPaint paint = null)
```

Uma terceira versão do [ `DrawBitmap` ](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKRect,SkiaSharp.SKRect,SkiaSharp.SKPaint)) tem duas `SKRect` argumentos para especificar um subconjunto retangular do bitmap para exibição, mas essa versão não é usado neste artigo.

Aqui está o código para exibir o bitmap carregado de um bitmap do recurso inserido:

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

O bitmap é esticado para as dimensões do retângulo, por isso, o monkey horizontalmente é alongado nessas capturas de tela:

[![](bitmaps-images/basicbitmaps-small.png "Uma captura de tela tripla da página básica Bitmaps")](bitmaps-images/basicbitmaps-large.png#lightbox "uma tripla captura de tela da página básica Bitmaps")

A terceira imagem &mdash; que você pode ver apenas se você executar o programa e carregar uma foto da sua própria biblioteca de imagens &mdash; também é exibido dentro de um retângulo, mas o retângulo posição e tamanho são ajustados para manter a taxa de proporção do bitmap. Esse cálculo é um pouco mais complicado porque ele requer o cálculo de um fator com base no tamanho do retângulo de destino e o bitmap de escala e centralizando o retângulo nessa área:

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

Se ainda tiver sido carregado sem bitmap da biblioteca de imagens, em seguida, a `else` bloco exibirá algum texto para solicitar que o usuário tocar a tela.

Você pode exibir os bitmaps com vários graus de transparência e o próximo artigo sobre [ **SkiaSharp transparência** ](transparency.md) descreve como.

## <a name="related-links"></a>Links relacionados

- [APIs de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
- [Escolhendo uma foto na biblioteca de imagens](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md)
