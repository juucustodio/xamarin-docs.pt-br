---
title: Noções básicas de bitmap no SkiaSharp
description: Este artigo explica como carregar bitmaps no SkiaSharp de várias fontes e exibi-los em aplicativos Xamarin. Forms e demonstra isso com o código de exemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 32C95DFF-9065-42D7-966C-D3DBD16906B3
author: davidbritch
ms.author: dabritch
ms.date: 07/17/2018
ms.openlocfilehash: 4aa73e1cba3f970396e5a52679372a160f47f7af
ms.sourcegitcommit: 3bf02ecac9a8855779e07eb1e7e27abb9fc38934
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/29/2019
ms.locfileid: "74658262"
---
# <a name="bitmap-basics-in-skiasharp"></a>Noções básicas de bitmap no SkiaSharp

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Carregue bitmaps de várias fontes e exiba-os._

O suporte de bitmaps no SkiaSharp é bastante extenso. Este artigo aborda apenas as noções básicas &mdash; como carregar bitmaps e como exibi-los:

![](bitmaps-images/basicbitmaps-small.png "The display of two bitmaps")

Uma exploração muito mais profunda de bitmaps pode ser encontrada na seção [bitmaps do SkiaSharp](../bitmaps/index.md).

Um bitmap SkiaSharp é um objeto do tipo [`SKBitmap`](xref:SkiaSharp.SKBitmap). Há várias maneiras de criar um bitmap, mas este artigo se restringe ao método [`SKBitmap.Decode`](xref:SkiaSharp.SKBitmap.Decode(System.IO.Stream)) , que carrega o bitmap de um objeto .net `Stream`.

A página **bitmaps básicos** no programa **SkiaSharpFormsDemos** demonstra como carregar bitmaps de três fontes diferentes:

- Da Internet
- De um recurso inserido no executável
- Da biblioteca de fotos do usuário

Três objetos `SKBitmap` para essas três fontes são definidos como campos na classe [`BasicBitmapsPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/BasicBitmapsPage.cs) :

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

Para carregar um bitmap com base em uma URL, você pode usar a classe [`HttpClient`](/dotnet/api/system.net.http.httpclient?view=netstandard-2.0) . Você deve instanciar apenas uma instância do `HttpClient` e reutilizá-la, portanto, armazene-a como um campo:

```csharp
HttpClient httpClient = new HttpClient();
```

Ao usar o `HttpClient` com aplicativos iOS e Android, você desejará definir as propriedades do projeto conforme descrito nos documentos em **[TLS (segurança da camada de transporte) 1,2](~/cross-platform/app-fundamentals/transport-layer-security.md)** .

Como é mais conveniente usar o operador de `await` com `HttpClient`, o código não pode ser executado no construtor de `BasicBitmapsPage`. Em vez disso, ele faz parte da substituição de `OnAppearing`. A URL aponta para uma área no site da Xamarin com alguns bitmaps de exemplo. Um pacote no site da Web permite acrescentar uma especificação para redimensionar o bitmap para uma largura específica:

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

O sistema operacional Android gera uma exceção ao usar o `Stream` retornado de `GetStreamAsync` no método `SKBitmap.Decode` porque ele está executando uma operação demorada em um thread principal. Por esse motivo, o conteúdo do arquivo de bitmap é copiado para um objeto `MemoryStream` usando `CopyToAsync`.

O método de `SKBitmap.Decode` estático é responsável por decodificar arquivos de bitmap. Ele funciona com formatos de bitmap JPEG, PNG e GIF e armazena os resultados em um formato SkiaSharp interno. Neste ponto, o `SKCanvasView` precisa ser invalidado para permitir que o manipulador de `PaintSurface` atualize a exibição.

## <a name="loading-a-bitmap-resource"></a>Carregando um recurso de bitmap

Em termos de código, a abordagem mais fácil para carregar bitmaps está incluindo um recurso de bitmap diretamente em seu aplicativo. O programa **SkiaSharpFormsDemos** inclui uma pasta chamada **Media** contendo vários arquivos de bitmap, incluindo um **macaco nomeado. png**. Para bitmaps armazenados como recursos de programa, você deve usar a caixa de diálogo **Propriedades** para dar ao arquivo uma **ação de compilação** do **recurso incorporado**!

Cada recurso inserido tem uma *ID de recurso* que consiste no nome do projeto, na pasta e no nome do arquivo, todos conectados por pontos: **SkiaSharpFormsDemos. Media. macaco. png**. Você pode obter acesso a esse recurso especificando essa ID de recurso como um argumento para o método [`GetManifestResourceStream`](xref:System.Reflection.Assembly.GetManifestResourceStream(System.String)) da classe [`Assembly`](xref:System.Reflection.Assembly) :

```csharp
string resourceID = "SkiaSharpFormsDemos.Media.monkey.png";
Assembly assembly = GetType().GetTypeInfo().Assembly;

using (Stream stream = assembly.GetManifestResourceStream(resourceID))
{
    resourceBitmap = SKBitmap.Decode(stream);
}
```

Esse objeto `Stream` pode ser passado diretamente para o método `SKBitmap.Decode`.

## <a name="loading-a-bitmap-from-the-photo-library"></a>Carregando um bitmap da biblioteca de fotos

Também é possível que o usuário carregue uma foto da biblioteca de imagens do dispositivo. Esse recurso não é fornecido pelo Xamarin. Forms em si. O trabalho requer um serviço de dependência, como aquele descrito no artigo que [seleciona uma foto da biblioteca de imagens](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md).

O arquivo **IPhotoLibrary.cs** no projeto **SkiaSharpFormsDemos** e os três arquivos **PhotoLibrary.cs** nos projetos da plataforma foram adaptados do artigo. Além disso, o arquivo **MainActivity.cs** do Android foi modificado conforme descrito no artigo, e o projeto do IOS recebeu permissão para acessar a biblioteca de fotos com duas linhas na parte inferior do arquivo **info. plist** .

O Construtor `BasicBitmapsPage` adiciona uma `TapGestureRecognizer` à `SKCanvasView` para ser notificado de toques. No recebimento de um toque, o manipulador de `Tapped` obtém acesso ao serviço de dependência de seletor de imagem e chama `PickPhotoAsync`. Se um objeto `Stream` for retornado, ele será passado para o método `SKBitmap.Decode`:

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

Observe que o manipulador de `Tapped` também chama o método `InvalidateSurface` do objeto `SKCanvasView`. Isso gera uma nova chamada para o manipulador de `PaintSurface`.

## <a name="displaying-the-bitmaps"></a>Exibindo os bitmaps

O manipulador de `PaintSurface` precisa exibir três bitmaps. O manipulador pressupõe que o telefone está no modo retrato e divide a tela verticalmente em três partes iguais.

O primeiro bitmap é exibido com o método de [`DrawBitmap`](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,System.Single,System.Single,SkiaSharp.SKPaint)) mais simples. Tudo o que você precisa especificar são as coordenadas X e Y em que o canto superior esquerdo do bitmap deve ser posicionado:

```csharp
public void DrawBitmap (SKBitmap bitmap, Single x, Single y, SKPaint paint = null)
```

Embora um parâmetro `SKPaint` seja definido, ele tem um valor padrão de `null` e você pode ignorá-lo. Os pixels do bitmap são simplesmente transferidos para os pixels da superfície de exibição com um mapeamento de um para um. Você verá um aplicativo para este `SKPaint` argumento na próxima seção sobre transparência de [**SkiaSharp**](transparency.md).

Um programa pode obter as dimensões de pixel de um bitmap com as propriedades [`Width`](xref:SkiaSharp.SKBitmap.Width) e [`Height`](xref:SkiaSharp.SKBitmap.Height) . Essas propriedades permitem que o programa calcule coordenadas para posicionar o bitmap no centro do terceiro superior da tela:

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

Os outros dois bitmaps são exibidos com uma versão do [`DrawBitmap`](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKRect,SkiaSharp.SKPaint)) com um parâmetro `SKRect`:

```csharp
public void DrawBitmap (SKBitmap bitmap, SKRect dest, SKPaint paint = null)
```

Uma terceira versão do [`DrawBitmap`](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKRect,SkiaSharp.SKRect,SkiaSharp.SKPaint)) tem dois argumentos de `SKRect` para especificar um subconjunto retangular do bitmap a ser exibido, mas essa versão não é usada neste artigo.

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

A terceira imagem &mdash; que você só poderá ver se executar o programa e carregar uma foto de sua própria biblioteca de imagens &mdash; também será exibida dentro de um retângulo, mas a posição e o tamanho do retângulo serão ajustados para manter a taxa de proporção do bitmap. Esse cálculo é um pouco mais envolvido porque requer o cálculo de um fator de dimensionamento com base no tamanho do bitmap e no retângulo de destino, e centraliza o retângulo nessa área:

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

Se nenhum bitmap ainda tiver sido carregado da biblioteca de imagens, o bloco de `else` exibirá algum texto para solicitar que o usuário toque na tela.

Você pode exibir bitmaps com vários graus de transparência e o próximo artigo sobre [**transparência de SkiaSharp**](transparency.md) descreve como.

## <a name="related-links"></a>Links Relacionados

- [APIs do SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
- [Escolhendo uma foto da biblioteca de imagens](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md)
