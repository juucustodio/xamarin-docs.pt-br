---
title: Salvando bitmaps de SkiaSharp em arquivos
description: Explore os vários formatos de arquivo com suporte pelo SkiaSharp para salvar os bitmaps na biblioteca de fotos do usuário.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 2D696CB6-B31B-42BC-8D3B-11D63B1E7D9C
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: 876594440c28fb0f30e0438f2ef02ae7fe89040c
ms.sourcegitcommit: 7eed80186e23e6aff3ddbbf7ce5cd1fa20af1365
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/11/2018
ms.locfileid: "51526501"
---
# <a name="saving-skiasharp-bitmaps-to-files"></a>Salvando bitmaps de SkiaSharp em arquivos

Depois que um aplicativo de SkiaSharp criou ou modificou um bitmap, o aplicativo poderá salvar o bitmap para a biblioteca de fotos do usuário:

![Salvando Bitmaps](saving-images/SavingSample.png "salvando Bitmaps")

Essa tarefa envolve duas etapas:

- Convertendo o bitmap de SkiaSharp para dados em um formato de arquivo específico, como JPEG ou PNG.
- Salvando o resultado para a biblioteca de fotos usando o código específico da plataforma.

## <a name="file-formats-and-codecs"></a>Codecs e formatos de arquivo

A maioria do arquivo de bitmap populares de hoje formata usam a compactação para reduzir o espaço de armazenamento. As duas categorias amplas de técnicas de compactação são chamadas _perdas_ e _sem perdas_. Estes termos de indicam se o algoritmo de compactação resulta na perda de dados.

O formato com perdas mais popular foi desenvolvido por Joint Photographic Experts Group e é chamado de JPEG. O algoritmo de compactação de JPEG analisa a imagem usando uma ferramenta de matemática chamada a _transformação de co-seno separada_e tenta remover os dados que não são cruciais para preservar a fidelidade visual da imagem. O grau de compactação pode ser controlado com uma configuração geralmente denominada _qualidade_. Configurações de qualidade superiores resultam em arquivos maiores.

Por outro lado, um algoritmo de compactação sem perdas analisa a imagem para repetição e padrões de pixels que podem ser codificados de forma que reduz os dados, mas não resulta na perda de todas as informações. Os dados de bitmap original possam ser restaurados totalmente a partir do arquivo compactado. O formato de arquivo primário de compactados sem perda em uso hoje é elementos gráficos PNG (Portable Network).

Em geral, JPEG é usado para fotografias, enquanto PNG é usado para imagens que foram geradas manualmente ou de forma algorítmica. Qualquer algoritmo de compactação sem perdas que reduz o tamanho de alguns arquivos necessariamente deve aumentar o tamanho de outras pessoas. Felizmente, esse aumento no tamanho geralmente ocorre somente para dados que contém muitas informações aleatórias (ou aleatórias).

A compactação de algoritmos são complexos o suficiente para justificar a dois termos que descrevem os processos de compactação e descompactação:

- _decodificar_ &mdash; ler um formato de arquivo de bitmap e descompactá-los
- _codificar_ &mdash; compactar o bitmap e gravar em um formato de arquivo de bitmap

O [ `SKBitmap` ](xref:SkiaSharp.SKBitmap) classe contém vários métodos chamados `Decode` que criam um `SKBitmap` de uma fonte compactada. É tudo que é necessário fornecer um nome de arquivo, fluxo ou matriz de bytes. O decodificador pode determinar o formato de arquivo e entregá-la para a função de decodificação interna adequada.

Além disso, o [ `SKCodec` ](xref:SkiaSharp.SKCodec) classe tem dois métodos chamados `Create` que pode criar um `SKCodec` do objeto de uma fonte compactada e permitir que um aplicativo para obter mais envolvido no processo de decodificação. (O `SKCodec` classe é mostrada no artigo [ **animando Bitmaps de SkiaSharp** ](animating.md#gif-animation) juntamente com um arquivo GIF animado de decodificação.)

Quando estiver codificando um bitmap, mais informações são necessárias: O codificador deve saber o formato de arquivo específico que o aplicativo deseja usar (JPEG ou PNG ou alguma outra coisa). Se um formato com perdas for desejado, a codificação também deve saber o nível de qualidade desejado. 

O `SKBitmap` classe define um [ `Encode` ](xref:SkiaSharp.SKBitmap.Encode(SkiaSharp.SKWStream,SkiaSharp.SKEncodedImageFormat,System.Int32)) método com a seguinte sintaxe:

```csharp
public Boolean Encode (SKWStream dst, SKEncodedImageFormat format, Int32 quality)
```

Esse método é descrito mais detalhadamente em breve. O bitmap codificado é gravado em um fluxo gravável. (O 'W' em `SKWStream` significa "gravável".) O segundo e terceiro argumentos especificam o formato de arquivo e (para formatos com perdas) a qualidade desejada, variando de 0 a 100.

Além disso, o [ `SKImage` ](xref:SkiaSharp.SKImage) e [ `SKPixmap` ](xref:SkiaSharp.SKPixmap) classes também definem `Encode` métodos que são um pouco mais versáteis e que talvez você prefira. Você pode criar facilmente uma `SKImage` do objeto de um `SKBitmap` objeto estático de uso [ `SKImage.FromBitmap` ](xref:SkiaSharp.SKImage.FromBitmap(SkiaSharp.SKBitmap)) método. Você pode obter um `SKPixmap` do objeto de um `SKBitmap` do objeto usando o [ `PeekPixels` ](xref:SkiaSharp.SKBitmap.PeekPixels) método.

Um dos [ `Encode` ](xref:SkiaSharp.SKImage.Encode) métodos definidos pelo `SKImage` não tem parâmetros e salva automaticamente em um formato PNG. Esse método sem parâmetros é muito fácil de usar.

## <a name="platform-specific-code-for-saving-bitmap-files"></a>Código específico da plataforma para salvar arquivos de bitmap

Quando você codifica uma `SKBitmap` formato de objeto em um arquivo específico, geralmente, você obterá um objeto de fluxo de algum tipo, ou uma matriz de dados. Alguns da `Encode` métodos (incluindo aquele sem parâmetros definido por `SKImage`) retornar um [ `SKData` ](xref:SkiaSharp.SKData) objeto, que pode ser convertido em uma matriz de bytes usando o [ `ToArray` ](xref:SkiaSharp.SKData.ToArray) método. Esses dados, em seguida, devem ser salvo em um arquivo. 

Salvar em um arquivo no armazenamento local do aplicativo é muito fácil, porque você pode usar o padrão `System.IO` classes e métodos para essa tarefa. Essa técnica é demonstrada no artigo [ **animando Bitmaps de SkiaSharp** ](animating.md#bitmap-animation) juntamente com a animação de uma série de bitmaps do conjunto de Mandelbrot.

Se você quiser que o arquivo a ser compartilhado por outros aplicativos, ele deve ser salvo para a biblioteca de fotos do usuário. Esta tarefa requer código específico da plataforma e o uso do xamarin. Forms [ `DependencyService` ](xref:Xamarin.Forms.DependencyService).

O **SkiaSharpFormsDemo** do projeto na [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) aplicativo define uma `IPhotoLibrary` interface usada com o `DependencyService` classe. Isso define a sintaxe de um `SavePhotoAsync` método:

```csharp
public interface IPhotoLibrary
{
    Task<Stream> PickPhotoAsync();

    Task<bool> SavePhotoAsync(byte[] data, string folder, string filename);
}
```

Essa interface também define o `PickPhotoAsync` método, que é usado para abrir o seletor de arquivos específicos da plataforma para biblioteca de fotos do dispositivo.

Para `SavePhotoAsync`, o primeiro argumento é uma matriz de bytes que contém o bitmap já codificado em um formato de arquivo específico, como JPEG ou PNG. É possível que um aplicativo talvez queira isolar todos os bitmaps que ele cria para uma determinada pasta, que é especificada no parâmetro de Avançar, seguido pelo nome do arquivo. O método retorna um valor booliano que indica êxito ou não.

Aqui está como `SavePhotoAsync` é implementada nas três plataformas:

### <a name="the-ios-implementation"></a>A implementação do iOS

A implementação do iOS dos `SavePhotoAsync` usa o [ `SaveToPhotosAlbum` ](https://developer.xamarin.com/api/member/UIKit.UIImage.SaveToPhotosAlbum/) método de `UIImage`:

```csharp
public class PhotoLibrary : IPhotoLibrary
{
    ···
    public Task<bool> SavePhotoAsync(byte[] data, string folder, string filename)
    {
        NSData nsData = NSData.FromArray(data);
        UIImage image = new UIImage(nsData);
        TaskCompletionSource<bool> taskCompletionSource = new TaskCompletionSource<bool>();

        image.SaveToPhotosAlbum((UIImage img, NSError error) =>
        {
            taskCompletionSource.SetResult(error == null);
        });

        return taskCompletionSource.Task;
    }
}
```

Infelizmente, não há nenhuma maneira de especificar um nome de arquivo ou pasta para a imagem. 

O **Info. plist** arquivo no projeto do iOS requer uma chave que indica que ele adiciona imagens para a biblioteca de fotos:

```xml
<key>NSPhotoLibraryAddUsageDescription</key>
<string>SkiaSharp Forms Demos adds images to your photo library</string>
```

Atenção! A chave de permissão para simplesmente acessar a biblioteca de fotos é muito semelhante, mas não o mesmo:

```xml
<key>NSPhotoLibraryUsageDescription</key>
<string>SkiaSharp Forms Demos accesses your photo library</string>
```

### <a name="the-android-implementation"></a>A implementação do Android

A implementação de Android da `SavePhotoAsync` primeiro verifica se o `folder` argumento é `null` ou uma cadeia de caracteres vazia. Nesse caso, o bitmap é salvo no diretório raiz da biblioteca de fotos. Caso contrário, a pasta é obtida, e se ela não existir, ele será criado:

```csharp
public class PhotoLibrary : IPhotoLibrary
{
    ···
    public async Task<bool> SavePhotoAsync(byte[] data, string folder, string filename)
    {
        try
        {
            File picturesDirectory = Environment.GetExternalStoragePublicDirectory(Environment.DirectoryPictures);
            File folderDirectory = picturesDirectory;

            if (!string.IsNullOrEmpty(folder))
            {
                folderDirectory = new File(picturesDirectory, folder);
                folderDirectory.Mkdirs();
            }

            using (File bitmapFile = new File(folderDirectory, filename))
            {
                bitmapFile.CreateNewFile();

                using (FileOutputStream outputStream = new FileOutputStream(bitmapFile))
                {
                    await outputStream.WriteAsync(data);
                }

                // Make sure it shows up in the Photos gallery promptly.
                MediaScannerConnection.ScanFile(MainActivity.Instance,
                                                new string[] { bitmapFile.Path },
                                                new string[] { "image/png", "image/jpeg" }, null);
            }
        }
        catch
        {
            return false;
        }

        return true;
    }
}
```

A chamada para `MediaScannerConnection.ScanFile` não é estritamente necessária, mas se você estiver testando seu programa, imediatamente, marcando a biblioteca de fotos, ajuda muito, atualizando a exibição da biblioteca da Galeria.

O **androidmanifest. XML** arquivo exige a seguinte marca de permissão:

```xml
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

### <a name="the-uwp-implementation"></a>A implementação de UWP

A implementação de UWP do `SavePhotoAsync` é muito semelhantes em estrutura à implementação do Android:

```csharp
public class PhotoLibrary : IPhotoLibrary
{
    ···
    public async Task<bool> SavePhotoAsync(byte[] data, string folder, string filename)
    {
        StorageFolder picturesDirectory = KnownFolders.PicturesLibrary;
        StorageFolder folderDirectory = picturesDirectory;

        // Get the folder or create it if necessary
        if (!string.IsNullOrEmpty(folder))
        {
            try
            {
                folderDirectory = await picturesDirectory.GetFolderAsync(folder);
            }
            catch
            { }

            if (folderDirectory == null)
            {
                try
                {
                    folderDirectory = await picturesDirectory.CreateFolderAsync(folder);
                }
                catch
                {
                    return false;
                }
            }
        }

        try
        {
            // Create the file.
            StorageFile storageFile = await folderDirectory.CreateFileAsync(filename,
                                                CreationCollisionOption.GenerateUniqueName);

            // Convert byte[] to Windows buffer and write it out.
            IBuffer buffer = WindowsRuntimeBuffer.Create(data, 0, data.Length, data.Length);
            await FileIO.WriteBufferAsync(storageFile, buffer);
        }
        catch
        {
            return false;
        }

        return true;
    }
}
```

O **funcionalidades** seção o **Package. appxmanifest** arquivo requer **biblioteca de imagens**.

## <a name="exploring-the-image-formats"></a>Explorando os formatos de imagem

Aqui está o [ `Encode` ](xref:SkiaSharp.SKBitmap.Encode(SkiaSharp.SKWStream,SkiaSharp.SKEncodedImageFormat,System.Int32)) método `SKImage` novamente:

```csharp
public Boolean Encode (SKWStream dst, SKEncodedImageFormat format, Int32 quality)
```

[`SKEncodedImageFormat`](xref:SkiaSharp.SKEncodedImageFormat) é uma enumeração com membros que se referem a onze formatos de arquivo de bitmap, alguns dos quais são bastante obscuras:

- `Astc` &mdash; Compactação de textura escalonável adaptável
- `Bmp` &mdash; Bitmap do Windows
- `Dng` &mdash; Negativo Digital da Adobe
- `Gif` &mdash; Formát GIF
- `Ico` &mdash; Imagens de ícone do Windows
- `Jpeg` &mdash; Joint Photographic Experts Group
- `Ktx` &mdash; Formato de textura Khronos para OpenGL
- `Pkm` &mdash; Pokémon salvar arquivo
- `Png` &mdash; Portable Network Graphics
- `Wbmp` &mdash; Formato de Bitmap de protocolo de aplicativo sem fio (1 bit por pixel)
- `Webp` &mdash; Formato do Google WebP

Como você verá em breve, apenas três desses formatos de arquivo (`Jpeg`, `Png`, e `Webp`), na verdade, são compatíveis com SkiaSharp.

Para salvar uma `SKBitmap` objeto nomeado `bitmap` à biblioteca de fotos do usuário, você também precisa de um membro do `SKEncodedImageFormat` enumeração denominada `imageFormat` e (para formatos com perdas) um número inteiro `quality` variável. Você pode usar o código a seguir para salvar esse bitmap em um arquivo com o nome `filename` no `folder` pasta:

```csharp
using (MemoryStream memStream = new MemoryStream())
using (SKManagedWStream wstream = new SKManagedWStream(memStream))
{
    bitmap.Encode(wstream, imageFormat, quality);
    byte[] data = memStream.ToArray();

    // Check the data array for content!

    bool success = await DependencyService.Get<IPhotoLibrary>().SavePhotoAsync(data, folder, filename);

    // Check return value for success!
}
```

O `SKManagedWStream` deriva de classe `SKWStream` (que significa "fluxo gravável"). O `Encode` método grava o arquivo de bitmap codificado no fluxo. Consultem os comentários no código algum erro, talvez seja necessário executar a verificação. 

O **salvar arquivo de formatos** página na [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) aplicativo usa um código semelhante para permitir que você pode experimentar com a economia de um bitmap em vários formatos.

O arquivo XAML contém um `SKCanvasView` que exibe um bitmap, enquanto o restante da página contém tudo o que o aplicativo precisa chamar o `Encode` método `SKBitmap`. Ele tem um `Picker` para um membro do `SKEncodedImageFormat` enumeração, um `Slider` para o argumento de qualidade para formatos de bitmap com perdas, dois `Entry` modos de exibição para um nome de nome de arquivo e pasta e um `Button` para salvar o arquivo.

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaforms="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Bitmaps.SaveFileFormatsPage"
             Title="Save Bitmap Formats">

    <StackLayout Margin="10">
        <skiaforms:SKCanvasView PaintSurface="OnCanvasViewPaintSurface"
                                VerticalOptions="FillAndExpand" />

        <Picker x:Name="formatPicker"
                Title="image format"
                SelectedIndexChanged="OnFormatPickerChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKEncodedImageFormat}">
                    <x:Static Member="skia:SKEncodedImageFormat.Astc" />
                    <x:Static Member="skia:SKEncodedImageFormat.Bmp" />
                    <x:Static Member="skia:SKEncodedImageFormat.Dng" />
                    <x:Static Member="skia:SKEncodedImageFormat.Gif" />
                    <x:Static Member="skia:SKEncodedImageFormat.Ico" />
                    <x:Static Member="skia:SKEncodedImageFormat.Jpeg" />
                    <x:Static Member="skia:SKEncodedImageFormat.Ktx" />
                    <x:Static Member="skia:SKEncodedImageFormat.Pkm" />
                    <x:Static Member="skia:SKEncodedImageFormat.Png" />
                    <x:Static Member="skia:SKEncodedImageFormat.Wbmp" />
                    <x:Static Member="skia:SKEncodedImageFormat.Webp" />
                </x:Array>
            </Picker.ItemsSource>
        </Picker>

        <Slider x:Name="qualitySlider"
                Maximum="100"
                Value="50" />

        <Label Text="{Binding Source={x:Reference qualitySlider},
                              Path=Value,
                              StringFormat='Quality = {0:F0}'}"
               HorizontalTextAlignment="Center" />

        <StackLayout Orientation="Horizontal">
            <Label Text="Folder Name: "
                   VerticalOptions="Center" />

            <Entry x:Name="folderNameEntry"
                   Text="SaveFileFormats"
                   HorizontalOptions="FillAndExpand" />
        </StackLayout>

        <StackLayout Orientation="Horizontal">
            <Label Text="File Name: "
                   VerticalOptions="Center" />

            <Entry x:Name="fileNameEntry"
                   Text="Sample.xxx"
                   HorizontalOptions="FillAndExpand" />
        </StackLayout>

        <Button Text="Save" 
                Clicked="OnButtonClicked">
            <Button.Triggers>
                <DataTrigger TargetType="Button"
                             Binding="{Binding Source={x:Reference formatPicker},
                                               Path=SelectedIndex}"
                             Value="-1">
                    <Setter Property="IsEnabled" Value="False" />
                </DataTrigger>

                <DataTrigger TargetType="Button"
                             Binding="{Binding Source={x:Reference fileNameEntry},
                                               Path=Text.Length}"
                             Value="0">
                    <Setter Property="IsEnabled" Value="False" />
                </DataTrigger>
            </Button.Triggers>
        </Button>

        <Label x:Name="statusLabel"
               Text="OK"
               Margin="10, 0" />
    </StackLayout>
</ContentPage>
```

O arquivo code-behind carrega um recurso de bitmap e usa o `SKCanvasView` para exibi-lo. Que nunca bitmap alterações. O `SelectedIndexChanged` manipulador para o `Picker` modifica o nome do arquivo com uma extensão que é o mesmo que o membro de enumeração:

```csharp
public partial class SaveFileFormatsPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(typeof(SaveFileFormatsPage),
        "SkiaSharpFormsDemos.Media.MonkeyFace.png");

    public SaveFileFormatsPage ()
    {
        InitializeComponent ();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        args.Surface.Canvas.DrawBitmap(bitmap, args.Info.Rect, BitmapStretch.Uniform);
    }

    void OnFormatPickerChanged(object sender, EventArgs args)
    {
        if (formatPicker.SelectedIndex != -1)
        {
            SKEncodedImageFormat imageFormat = (SKEncodedImageFormat)formatPicker.SelectedItem;
            fileNameEntry.Text = Path.ChangeExtension(fileNameEntry.Text, imageFormat.ToString());
            statusLabel.Text = "OK";
        }
    }

    async void OnButtonClicked(object sender, EventArgs args)
    {
        SKEncodedImageFormat imageFormat = (SKEncodedImageFormat)formatPicker.SelectedItem;
        int quality = (int)qualitySlider.Value;

        using (MemoryStream memStream = new MemoryStream())
        using (SKManagedWStream wstream = new SKManagedWStream(memStream))
        {
            bitmap.Encode(wstream, imageFormat, quality);
            byte[] data = memStream.ToArray();

            if (data == null)
            {
                statusLabel.Text = "Encode returned null";
            }
            else if (data.Length == 0)
            {
                statusLabel.Text = "Encode returned empty array";
            }
            else
            {
                bool success = await DependencyService.Get<IPhotoLibrary>().
                    SavePhotoAsync(data, folderNameEntry.Text, fileNameEntry.Text);

                if (!success)
                {
                    statusLabel.Text = "SavePhotoAsync return false";
                }
                else
                {
                    statusLabel.Text = "Success!";
                }
            }
        }
    }
}
```

O `Clicked` manipulador para o `Button` funciona todos o real. Ele obtém dois argumentos para `Encode` do `Picker` e `Slider`e, em seguida, usa o código mostrado anteriormente para criar um `SKManagedWStream` para o `Encode` método. Os dois `Entry` exibições fornecerem nomes de arquivo e pasta para o `SavePhotoAsync` método.

A maioria desse método é dedicado para lidar com problemas ou erros. Se `Encode` cria uma matriz vazia, isso significa que o formato de arquivo específico não tem suporte. Se `SavePhotoAsync` retorna `false`, em seguida, o arquivo não foi salvo com êxito. 

Aqui está o programa em execução nas três plataformas:

[![Salvar formatos de arquivo](saving-images/SaveFileFormats.png "salvar formatos de arquivo")](saving-images/SaveFileFormats-Large.png#lightbox)

Nessa captura de tela mostra somente três formatos que têm suporte nessas plataformas:

- JPEG
- PNG
- WebP

Para todos os outros formatos, o `Encode` método grava nada no fluxo e a matriz de bytes resultante está vazia.

O bitmap que o **salvar formatos de arquivo** poupa página é 600 pixels quadrados. Com 4 bytes por pixel, que é um total de bytes 1,440,000 na memória. A tabela a seguir mostra o tamanho do arquivo para várias combinações de formato de arquivo e de qualidade:

|Formatar|Qualidade|Tamanho|
|------|------:|---:|
| PNG | N/D | K 492 |
| JPEG | 0 | K 2,95 |
|      | 50 | 22.1 K |
|      | 100 | K 206 |
| WebP | 0 | K 2,71 |
|      | 50 | 11.9 K |
|      | 100 | K 101 |

Você pode experimentar com diversas configurações de qualidade e examinar os resultados.

## <a name="saving-finger-paint-art"></a>Salvando finger-paint arte

Um uso comum de um bitmap é em programas, de desenho em que ele funcione conforme o algo chamado um _bitmap sombra_. Todo o desenho é mantido no bitmap, que, em seguida, é exibido pelo programa. O bitmap também é útil para salvar o desenho.

O [ **pintura a dedo em SkiaSharp** ](../paths/finger-paint.md) artigo demonstrou como usar o toque de controle para implementar um programa para pintura com dedo primitivo. O programa de suporte a uma única cor e largura do traço apenas uma, mas ele mantido o desenho inteiro em uma coleção de `SKPath` objetos.

O **pintura a dedo com salve** página na [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) exemplo também mantém o desenho inteiro em uma coleção de `SKPath` objetos, mas ele também renderiza o desenho no bitmap, que pode salvar sua biblioteca de fotos.

Grande parte desse programa é semelhante ao original **pintura a dedo** programa. Uma melhoria é que o arquivo XAML agora instancia botões rotulados **clara** e **salvar**:

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             xmlns:tt="clr-namespace:TouchTracking"
             x:Class="SkiaSharpFormsDemos.Bitmaps.FingerPaintSavePage"
             Title="Finger Paint Save">

    <StackLayout>
        <Grid BackgroundColor="White"
              VerticalOptions="FillAndExpand">
            <skia:SKCanvasView x:Name="canvasView"
                               PaintSurface="OnCanvasViewPaintSurface" />
            <Grid.Effects>
                <tt:TouchEffect Capture="True"
                                TouchAction="OnTouchEffectAction" />
            </Grid.Effects>
        </Grid>

        <Grid>
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="*" />
                <ColumnDefinition Width="*" />
            </Grid.ColumnDefinitions>
        </Grid>

        <Button Text="Clear"
                Grid.Row="0"
                Margin="50, 5"
                Clicked="OnClearButtonClicked" />

        <Button Text="Save"
                Grid.Row="1"
                Margin="50, 5"
                Clicked="OnSaveButtonClicked" />

    </StackLayout>
</ContentPage>
```

O arquivo code-behind mantém um campo do tipo `SKBitmap` chamado `saveBitmap`. Esse bitmap é criado ou recriado no `PaintSurface` manipulador sempre que o tamanho da exibição da superfície alterações. Se o bitmap precisa ser recriado, o conteúdo do bitmap existente é copiado para o novo bitmap, para que tudo o que é mantido, independentemente de como a superfície de exibição muda de tamanho:

```csharp
public partial class FingerPaintSavePage : ContentPage
{
    ···
    SKBitmap saveBitmap;

    public FingerPaintSavePage ()
    {
        InitializeComponent ();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        // Create bitmap the size of the display surface
        if (saveBitmap == null)
        {
            saveBitmap = new SKBitmap(info.Width, info.Height);
        }
        // Or create new bitmap for a new size of display surface
        else if (saveBitmap.Width < info.Width || saveBitmap.Height < info.Height)
        {
            SKBitmap newBitmap = new SKBitmap(Math.Max(saveBitmap.Width, info.Width),
                                              Math.Max(saveBitmap.Height, info.Height));

            using (SKCanvas newCanvas = new SKCanvas(newBitmap))
            {
                newCanvas.Clear();
                newCanvas.DrawBitmap(saveBitmap, 0, 0);
            }

            saveBitmap = newBitmap;
        }

        // Render the bitmap
        canvas.Clear();
        canvas.DrawBitmap(saveBitmap, 0, 0);
    }
    ···
}
```

O desenho feito o `PaintSurface` manipulador ocorre no final e consista exclusivamente o bitmap de renderização.

O processamento de toque é semelhante ao programa anterior. O programa mantém duas coleções, `inProgressPaths` e `completedPaths`, que contêm tudo o que o usuário desenhou desde a última vez em que a exibição foi limpo. Para cada evento de toque, o `OnTouchEffectAction` chamadas do manipulador `UpdateBitmap`:

```csharp
public partial class FingerPaintSavePage : ContentPage
{
    Dictionary<long, SKPath> inProgressPaths = new Dictionary<long, SKPath>();
    List<SKPath> completedPaths = new List<SKPath>();

    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Blue,
        StrokeWidth = 10,
        StrokeCap = SKStrokeCap.Round,
        StrokeJoin = SKStrokeJoin.Round
    };
    ···
    void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        switch (args.Type)
        {
            case TouchActionType.Pressed:
                if (!inProgressPaths.ContainsKey(args.Id))
                {
                    SKPath path = new SKPath();
                    path.MoveTo(ConvertToPixel(args.Location));
                    inProgressPaths.Add(args.Id, path);
                    UpdateBitmap();
                }
                break;

            case TouchActionType.Moved:
                if (inProgressPaths.ContainsKey(args.Id))
                {
                    SKPath path = inProgressPaths[args.Id];
                    path.LineTo(ConvertToPixel(args.Location));
                    UpdateBitmap();
                }
                break;

            case TouchActionType.Released:
                if (inProgressPaths.ContainsKey(args.Id))
                {
                    completedPaths.Add(inProgressPaths[args.Id]);
                    inProgressPaths.Remove(args.Id);
                    UpdateBitmap();
                }
                break;

            case TouchActionType.Cancelled:
                if (inProgressPaths.ContainsKey(args.Id))
                {
                    inProgressPaths.Remove(args.Id);
                    UpdateBitmap();
                }
                break;
        }
    }

    SKPoint ConvertToPixel(Point pt)
    {
        return new SKPoint((float)(canvasView.CanvasSize.Width * pt.X / canvasView.Width),
                            (float)(canvasView.CanvasSize.Height * pt.Y / canvasView.Height));
    }

    void UpdateBitmap()
    {
        using (SKCanvas saveBitmapCanvas = new SKCanvas(saveBitmap))
        {
            saveBitmapCanvas.Clear();

            foreach (SKPath path in completedPaths)
            {
                saveBitmapCanvas.DrawPath(path, paint);
            }

            foreach (SKPath path in inProgressPaths.Values)
            {
                saveBitmapCanvas.DrawPath(path, paint);
            }
        }

        canvasView.InvalidateSurface();
    }
    ···
}
```

O `UpdateBitmap` método redesenha `saveBitmap` criando um novo `SKCanvas`, limpá-la e, em seguida, renderizar todos os caminhos no bitmap. Ele conclui invalidando `canvasView` , de modo que o bitmap pode ser desenhado na tela.

Aqui estão os manipuladores para os dois botões. O **clara** botão limpa ambas as coleções de caminho, as atualizações `saveBitmap` (que resulta em Limpar o bitmap) e invalida a `SKCanvasView`:

```csharp
public partial class FingerPaintSavePage : ContentPage
{
    ···
    void OnClearButtonClicked(object sender, EventArgs args)
    {
        completedPaths.Clear();
        inProgressPaths.Clear();
        UpdateBitmap();
        canvasView.InvalidateSurface();
    }

    async void OnSaveButtonClicked(object sender, EventArgs args)
    {
        using (SKImage image = SKImage.FromBitmap(saveBitmap))
        {
            SKData data = image.Encode();
            DateTime dt = DateTime.Now;
            string filename = String.Format("FingerPaint-{0:D4}{1:D2}{2:D2}-{3:D2}{4:D2}{5:D2}{6:D3}.png",
                                            dt.Year, dt.Month, dt.Day, dt.Hour, dt.Minute, dt.Second, dt.Millisecond);

            IPhotoLibrary photoLibrary = DependencyService.Get<IPhotoLibrary>();
            bool result = await photoLibrary.SavePhotoAsync(data.ToArray(), "FingerPaint", filename);

            if (!result)
            {
                await DisplayAlert("FingerPaint", "Artwork could not be saved. Sorry!", "OK");
            }
        }
    }
}
```

O **salve** manipulador do botão usa o simplificada [ `Encode` ](xref:SkiaSharp.SKImage.Encode) método de `SKImage`. Este método codifica usando o formato PNG. O `SKImage` objeto é criado com base no `saveBitmap`e o `SKData` objeto contém o arquivo PNG codificado. 

O `ToArray` método de `SKData` obtém uma matriz de bytes. Esse é o que é passado para o `SavePhotoAsync` método, juntamente com um nome de pasta fixo e um nome de arquivo exclusivo construído a partir de data e hora atuais.

Aqui está o programa em ação:

[![Finger pintura de salvamento](saving-images/FingerPaintSave.png "Finger Paint salvar")](saving-images/FingerPaintSave-Large.png#lightbox)

Uma técnica muito semelhante é usada na [ **SpinPaint** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/SpinPaint/) exemplo. Isso também é um programa para pintura com dedo, exceto pelo fato do usuário pinta em um disco de rotação que, em seguida, reproduza os designs em seus quatro quadrantes. A cor das alterações de pintura a dedo, como o disco está girando:

[![Rotação Paint](saving-images/SpinPaint.png "girar tinta")](saving-images/SpinPaint-Large.png#lightbox)

O **salve** botão de `SpinPaint` classe é semelhante ao **pintura a dedo** em que ele salva a imagem em um nome de pasta fixa (**SpainPaint**) e um nome de arquivo construído a partir de a data e hora.

## <a name="related-links"></a>Links relacionados

- [APIs de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (amostra)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
- [SpinPaint (amostra)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/SpinPaint/)
