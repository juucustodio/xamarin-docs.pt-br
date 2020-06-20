---
title: Noções básicas de bitmap no SkiaSharp
description: Este artigo explica como carregar bitmaps no SkiaSharp de várias fontes e exibi-los em Xamarin.Forms aplicativos e demonstra isso com o código de exemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 32C95DFF-9065-42D7-966C-D3DBD16906B3
author: davidbritch
ms.author: dabritch
ms.date: 07/17/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1e4c170f818dc62640b1cd72ec3b70f48d227d93
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84137729"
---
# <a name="bitmap-basics-in-skiasharp"></a>Noções básicas de bitmap no SkiaSharp

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Carregue bitmaps de várias fontes e exiba-os._

O suporte de bitmaps no SkiaSharp é bastante extenso. Este artigo aborda apenas as noções básicas de &mdash; como carregar bitmaps e como exibi-los:

![](bitmaps-images/basicbitmaps-small.png "The display of two bitmaps")

Uma exploração muito mais profunda de bitmaps pode ser encontrada na seção [bitmaps do SkiaSharp](../bitmaps/index.md).

Um bitmap SkiaSharp é um objeto do tipo [`SKBitmap`](xref:SkiaSharp.SKBitmap) . Há várias maneiras de criar um bitmap, mas este artigo se restringe ao [`SKBitmap.Decode`](xref:SkiaSharp.SKBitmap.Decode(System.IO.Stream)) método, que carrega o bitmap de um objeto .NET `Stream` .

A página **bitmaps básicos** no programa **SkiaSharpFormsDemos** demonstra como carregar bitmaps de três fontes diferentes:

- Da Internet
- De um recurso inserido no executável
- Da biblioteca de fotos do usuário

Três `SKBitmap` objetos para essas três fontes são definidos como campos na [`BasicBitmapsPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/BasicBitmapsPage.cs) classe:

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

## <a name="loading-a-bitmap-from-the-web"></a>Carregando um bitmap da Web

Para carregar um bitmap com base em uma URL, você pode usar a [`HttpClient`](/dotnet/api/system.net.http.httpclient?view=netstandard-2.0) classe. Você deve instanciar apenas uma instância do `HttpClient` e reutilizá-la, portanto, armazená-la como um campo:

```csharp
HttpClient httpClient = new HttpClient();
```

Ao usar `HttpClient` o com aplicativos Ios e Android, você desejará definir as propriedades do projeto, conforme descrito nos documentos em **[TLS (segurança da camada de transporte) 1,2](~/cross-platform/app-fundamentals/transport-layer-security.md)**.

Como é mais conveniente usar o `await` operador com `HttpClient` , o código não pode ser executado no `BasicBitmapsPage` Construtor. Em vez disso, faz parte da `OnAppearing` substituição. A URL aponta para uma área no site da Xamarin com alguns bitmaps de exemplo. Um pacote no site da Web permite acrescentar uma especificação para redimensionar o bitmap para uma largura específica:

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

            webBitmap = SKBitmap.Decode(memStream);
            canvasView.InvalidateSurface();
        };
    }
    catch
    {
    }
}
```

O sistema operacional Android gera uma exceção ao usar o `Stream` retornado de `GetStreamAsync` no `SKBitmap.Decode` método porque ele está executando uma operação demorada em um thread principal. Por esse motivo, o conteúdo do arquivo de bitmap é copiado para um `MemoryStream` objeto usando `CopyToAsync` .

O `SKBitmap.Decode` método estático é responsável por decodificar arquivos de bitmap. Ele funciona com formatos de bitmap JPEG, PNG e GIF e armazena os resultados em um formato SkiaSharp interno. Neste ponto, o `SKCanvasView` precisa ser invalidado para permitir que o `PaintSurface` manipulador atualize a exibição.

## <a name="loading-a-bitmap-resource"></a>Carregando um recurso de bitmap

Em termos de código, a abordagem mais fácil para carregar bitmaps está incluindo um recurso de bitmap diretamente em seu aplicativo. O programa **SkiaSharpFormsDemos** inclui uma pasta chamada **Media** contendo vários arquivos de bitmap, incluindo um chamado **monkey.png**. Para bitmaps armazenados como recursos de programa, você deve usar a caixa de diálogo **Propriedades** para dar ao arquivo uma **ação de compilação** do **recurso incorporado**!

Cada recurso inserido tem uma *ID de recurso* que consiste no nome do projeto, na pasta e no nome do arquivo, todos conectados por pontos: **SkiaSharpFormsDemos.Media.monkey.png**. Você pode obter acesso a esse recurso especificando essa ID de recurso como um argumento para o [`GetManifestResourceStream`](xref:System.Reflection.Assembly.GetManifestResourceStream(System.String)) método da [`Assembly`](xref:System.Reflection.Assembly) classe:

```csharp
string resourceID = "SkiaSharpFormsDemos.Media.monkey.png";
Assembly assembly = GetType().GetTypeInfo().Assembly;

using (Stream stream = assembly.GetManifestResourceStream(resourceID))
{
    resourceBitmap = SKBitmap.Decode(stream);
}
```

Esse `Stream` objeto pode ser passado diretamente para o `SKBitmap.Decode` método.

## <a name="loading-a-bitmap-from-the-photo-library"></a>Carregando um bitmap da biblioteca de fotos

Também é possível que o usuário carregue uma foto da biblioteca de imagens do dispositivo. Esse recurso não é fornecido por Xamarin.Forms si só. O trabalho requer um serviço de dependência, como aquele descrito no artigo que [seleciona uma foto da biblioteca de imagens](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md).

O arquivo **IPhotoLibrary.cs** no projeto **SkiaSharpFormsDemos** e os três arquivos **PhotoLibrary.cs** nos projetos da plataforma foram adaptados do artigo. Além disso, o arquivo **MainActivity.cs** do Android foi modificado conforme descrito no artigo, e o projeto do IOS recebeu permissão para acessar a biblioteca de fotos com duas linhas na parte inferior do arquivo **info. plist** .

O `BasicBitmapsPage` construtor adiciona um `TapGestureRecognizer` ao para `SKCanvasView` ser notificado de toques. No recebimento de um toque, o `Tapped` manipulador obtém acesso ao serviço de dependência do seletor de imagem e às chamadas `PickPhotoAsync` . Se um `Stream` objeto for retornado, ele será passado para o `SKBitmap.Decode` método:

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

Observe que o `Tapped` manipulador também chama o `InvalidateSurface` método do `SKCanvasView` objeto. Isso gera uma nova chamada para o `PaintSurface` manipulador.

## <a name="displaying-the-bitmaps"></a>Exibindo os bitmaps

O `PaintSurface` manipulador precisa exibir três bitmaps. O manipulador pressupõe que o telefone está no modo retrato e divide a tela verticalmente em três partes iguais.

O primeiro bitmap é exibido com o método mais simples [`DrawBitmap`](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,System.Single,System.Single,SkiaSharp.SKPaint)) . Tudo o que você precisa especificar são as coordenadas X e Y em que o canto superior esquerdo do bitmap deve ser posicionado:

```csharp
public void DrawBitmap (SKBitmap bitmap, Single x, Single y, SKPaint paint = null)
```

Embora um `SKPaint` parâmetro seja definido, ele tem um valor padrão de `null` e você pode ignorá-lo. Os pixels do bitmap são simplesmente transferidos para os pixels da superfície de exibição com um mapeamento de um para um. Você verá um aplicativo para esse `SKPaint` argumento na próxima seção sobre transparência de [**SkiaSharp**](transparency.md).

Um programa pode obter as dimensões de pixel de um bitmap com [`Width`](xref:SkiaSharp.SKBitmap.Width) as [`Height`](xref:SkiaSharp.SKBitmap.Height) Propriedades e. Essas propriedades permitem que o programa calcule coordenadas para posicionar o bitmap no centro do terceiro superior da tela:

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

Os outros dois bitmaps são exibidos com uma versão do [`DrawBitmap`](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKRect,SkiaSharp.SKPaint)) com um `SKRect` parâmetro:

```csharp
public void DrawBitmap (SKBitmap bitmap, SKRect dest, SKPaint paint = null)
```

Uma terceira versão do [`DrawBitmap`](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKRect,SkiaSharp.SKRect,SkiaSharp.SKPaint)) tem dois `SKRect` argumentos para especificar um subconjunto retangular do bitmap a ser exibido, mas essa versão não é usada neste artigo.

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

O bitmap é ampliado para as dimensões do retângulo, motivo pelo qual o macaco é alongado horizontalmente nessas capturas de tela:

[![](bitmaps-images/basicbitmaps-small.png "A triple screenshot of the Basic Bitmaps page")](bitmaps-images/basicbitmaps-large.png#lightbox "A triple screenshot of the Basic Bitmaps page")

A terceira imagem &mdash; que você só pode ver se executa o programa e carrega uma foto de sua própria biblioteca de imagens &mdash; também é exibida dentro de um retângulo, mas a posição e o tamanho do retângulo são ajustados para manter a taxa de proporção do bitmap. Esse cálculo é um pouco mais envolvido porque requer o cálculo de um fator de dimensionamento com base no tamanho do bitmap e no retângulo de destino, e centraliza o retângulo nessa área:

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

Se nenhum bitmap ainda tiver sido carregado da biblioteca de imagens, o `else` bloco exibirá algum texto para solicitar que o usuário toque na tela.

Você pode exibir bitmaps com vários graus de transparência e o próximo artigo sobre [**transparência de SkiaSharp**](transparency.md) descreve como.

## <a name="related-links"></a>Links relacionados

- [APIs do SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
- [Escolhendo uma foto da biblioteca de imagens](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md)
