---
title: ''
description: ''
ms.prod: ''
ms.technology: ''
ms.assetid: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 01f4fcf1953658af44d2a8996913860a3b605abf
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84138652"
---
# <a name="saving-skiasharp-bitmaps-to-files"></a>Salvando bitmaps SkiaSharp em arquivos

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Depois que um aplicativo SkiaSharp criou ou modificou um bitmap, o aplicativo pode querer salvar o bitmap na biblioteca de fotos do usuário:

![Salvando bitmaps](saving-images/SavingSample.png "Salvando bitmaps")

Esta tarefa abrange duas etapas:

- Converter o bitmap SkiaSharp em dados em um formato de arquivo específico, como JPEG ou PNG.
- Salvar o resultado na biblioteca de fotos usando código específico da plataforma.

## <a name="file-formats-and-codecs"></a>Formatos de arquivo e codecs

A maioria dos formatos de arquivo de bitmap populares de hoje usa compactação para reduzir o espaço de armazenamento. As duas categorias amplas de técnicas de compactação são chamadas de _perda_ e _sem perdas_. Esses termos indicam se o algoritmo de compactação resulta ou não na perda de dados.

O formato de perda mais popular foi desenvolvido pelo grupo de especialistas geográficos conjuntos e é chamado de JPEG. O algoritmo de compactação JPEG analisa a imagem usando uma ferramenta matemática chamada de _transformação de cosseno discreta_e tenta remover dados que não são cruciais para preservar a fidelidade visual da imagem. O grau de compactação pode ser controlado com uma configuração geralmente conhecida como _qualidade_. As configurações de qualidade superior resultam em arquivos maiores.

Por outro lado, um algoritmo de compactação sem perdas analisa a imagem para repetição e padrões de pixels que podem ser codificados de uma maneira que reduza os dados, mas não resulta na perda de qualquer informação. Os dados de bitmap originais podem ser restaurados inteiramente do arquivo compactado. O formato de arquivo compactado sem perdas primário em uso hoje é o PNG (Portable Network Graphics).

Geralmente, o JPEG é usado para fotografias, enquanto o PNG é usado para imagens que foram geradas manualmente ou forma algorítmica. Qualquer algoritmo de compactação sem perdas que reduz o tamanho de alguns arquivos deve necessariamente aumentar o tamanho de outros. Felizmente, esse aumento de tamanho geralmente ocorre apenas para dados que contêm muitas informações aleatórias (ou aparentemente aleatórias).

Os algoritmos de compactação são complexos o suficiente para garantir dois termos que descrevem os processos de compactação e descompactação:

- _decodificar_ &mdash; ler um formato de arquivo de bitmap e descompactá-lo
- _codificar_ &mdash; compactar o bitmap e gravar em um formato de arquivo de bitmap

A [`SKBitmap`](xref:SkiaSharp.SKBitmap) classe contém vários métodos chamados `Decode` que criam um `SKBitmap` de uma fonte compactada. Tudo o que é necessário é fornecer um nome de arquivo, fluxo ou matriz de bytes. O decodificador pode determinar o formato do arquivo e entregá-lo à função de decodificação interna apropriada.

Além disso, a [`SKCodec`](xref:SkiaSharp.SKCodec) classe tem dois métodos chamados `Create` que podem criar um `SKCodec` objeto de uma fonte compactada e permitir que um aplicativo fique mais envolvido no processo de decodificação. (A `SKCodec` classe é mostrada no artigo [**animação de bitmaps SkiaSharp**](animating.md#gif-animation) em conexão com a decodificação de um arquivo GIF animado.)

Ao codificar um bitmap, mais informações são necessárias: o codificador deve saber o formato de arquivo específico que o aplicativo deseja usar (JPEG ou PNG ou algo mais). Se um formato de perda for desejado, a codificação também deverá saber o nível de qualidade desejado.

A `SKBitmap` classe define um [`Encode`](xref:SkiaSharp.SKBitmap.Encode(SkiaSharp.SKWStream,SkiaSharp.SKEncodedImageFormat,System.Int32)) método com a seguinte sintaxe:

```csharp
public Boolean Encode (SKWStream dst, SKEncodedImageFormat format, Int32 quality)
```

Esse método é descrito em mais detalhes em breve. O bitmap codificado é gravado em um fluxo gravável. (O "W" no `SKWStream` representa "gravável".) O segundo e o terceiro argumentos especificam o formato de arquivo e (para formatos de perda) a qualidade desejada, variando de 0 a 100.

Além disso, as [`SKImage`](xref:SkiaSharp.SKImage) [`SKPixmap`](xref:SkiaSharp.SKPixmap) classes e também definem `Encode` métodos que são um pouco mais versáteis e que você pode preferir. Você pode criar facilmente um `SKImage` objeto de um `SKBitmap` objeto usando o [`SKImage.FromBitmap`](xref:SkiaSharp.SKImage.FromBitmap(SkiaSharp.SKBitmap)) método estático. Você pode obter um `SKPixmap` objeto de um `SKBitmap` objeto usando o [`PeekPixels`](xref:SkiaSharp.SKBitmap.PeekPixels) método.

Um dos [`Encode`](xref:SkiaSharp.SKImage.Encode) métodos definidos por `SKImage` não tem parâmetros e é salvo automaticamente em um formato png. Esse método sem parâmetros é muito fácil de usar.

## <a name="platform-specific-code-for-saving-bitmap-files"></a>Código específico da plataforma para salvar arquivos de bitmap

Quando você codifica um `SKBitmap` objeto em um formato de arquivo específico, em geral você será deixado com um objeto de fluxo de algum tipo ou uma matriz de dados. Alguns dos `Encode` métodos (incluindo aquele sem parâmetros definidos por `SKImage` ) retornam um [`SKData`](xref:SkiaSharp.SKData) objeto, que pode ser convertido em uma matriz de bytes usando o [`ToArray`](xref:SkiaSharp.SKData.ToArray) método. Esses dados devem ser salvos em um arquivo.

Salvar em um arquivo no armazenamento local do aplicativo é muito fácil, pois você pode usar `System.IO` classes e métodos padrão para essa tarefa. Essa técnica é demonstrada no artigo [**animação de bitmaps SkiaSharp**](animating.md#bitmap-animation) em conexão com a animação de uma série de bitmaps do conjunto Mandelbrot.

Se você quiser que o arquivo seja compartilhado por outros aplicativos, ele deverá ser salvo na biblioteca de fotos do usuário. Essa tarefa requer o código específico da plataforma e o uso do Xamarin.Forms [`DependencyService`](xref:Xamarin.Forms.DependencyService) .

O projeto **SkiaSharpFormsDemo** no aplicativo [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) define uma `IPhotoLibrary` interface usada com a `DependencyService` classe. Isso define a sintaxe de um `SavePhotoAsync` método:

```csharp
public interface IPhotoLibrary
{
    Task<Stream> PickPhotoAsync();

    Task<bool> SavePhotoAsync(byte[] data, string folder, string filename);
}
```

Essa interface também define o `PickPhotoAsync` método, que é usado para abrir o seletor de arquivos específico da plataforma para a biblioteca de fotos do dispositivo.

Para `SavePhotoAsync` , o primeiro argumento é uma matriz de bytes que contém o bitmap já codificado em um formato de arquivo específico, como JPEG ou png. É possível que um aplicativo queira isolar todos os bitmaps que ele cria em uma determinada pasta, que é especificada no próximo parâmetro, seguida pelo nome do arquivo. O método retorna um valor booleano que indica êxito ou não.

As seções a seguir discutem como o `SavePhotoAsync` é implementado em cada plataforma.

### <a name="the-ios-implementation"></a>A implementação do iOS

A implementação do iOS do `SavePhotoAsync` usa o [`SaveToPhotosAlbum`](xref:UIKit.UIImage.SaveToPhotosAlbum*) método de `UIImage` :

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

Infelizmente, não há como especificar um nome de arquivo ou pasta para a imagem.

O arquivo **info. plist** no projeto do IOS requer uma chave indicando que ele adiciona imagens à biblioteca de fotos:

```xml
<key>NSPhotoLibraryAddUsageDescription</key>
<string>SkiaSharp Forms Demos adds images to your photo library</string>
```

Fique atento! A chave de permissão para simplesmente acessar a biblioteca de fotos é muito semelhante, mas não a mesma:

```xml
<key>NSPhotoLibraryUsageDescription</key>
<string>SkiaSharp Forms Demos accesses your photo library</string>
```

### <a name="the-android-implementation"></a>A implementação do Android

A implementação do Android do `SavePhotoAsync` primeiro verifica se o `folder` argumento é `null` ou uma cadeia de caracteres vazia. Nesse caso, o bitmap é salvo no diretório raiz da biblioteca de fotos. Caso contrário, a pasta será obtida e, se ela não existir, ela será criada:

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

A chamada para `MediaScannerConnection.ScanFile` não é estritamente necessária, mas se você estiver testando seu programa verificando imediatamente a biblioteca de fotos, ela ajuda muito atualizando a exibição da Galeria de bibliotecas.

O arquivo **AndroidManifest. xml** requer a seguinte marca de permissão:

```xml
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

### <a name="the-uwp-implementation"></a>A implementação de UWP

A implementação de UWP do `SavePhotoAsync` é muito semelhante na estrutura para a implementação do Android:

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

A seção de **recursos** do arquivo **Package. Appxmanifest** requer a **biblioteca de imagens**.

## <a name="exploring-the-image-formats"></a>Explorando os formatos de imagem

Este é o [`Encode`](xref:SkiaSharp.SKBitmap.Encode(SkiaSharp.SKWStream,SkiaSharp.SKEncodedImageFormat,System.Int32)) método de `SKImage` novamente:

```csharp
public Boolean Encode (SKWStream dst, SKEncodedImageFormat format, Int32 quality)
```

[`SKEncodedImageFormat`](xref:SkiaSharp.SKEncodedImageFormat)é uma enumeração com membros que se referem a onze formatos de arquivo de bitmap, alguns dos quais são bastante obscuros:

- `Astc`&mdash;Compactação de textura escalonável adaptável
- `Bmp`&mdash;Bitmap do Windows
- `Dng`&mdash;Adobe Digital negativo
- `Gif`&mdash;Graphics Interchange Format
- `Ico`&mdash;Imagens de ícone do Windows
- `Jpeg`&mdash;Grupo de especialistas do conjunto fotográfico
- `Ktx`&mdash;Formato de textura Khronos para OpenGL
- `Pkm`&mdash;Pokémon salvar arquivo
- `Png`&mdash;Gráfico de rede portátil
- `Wbmp`&mdash;Formato de bitmap do protocolo de aplicativo sem fio (1 bit por pixel)
- `Webp`&mdash;Formato do Google WebP

Como você verá em breve, apenas três desses formatos de arquivo ( `Jpeg` , `Png` e `Webp` ) são, na verdade, compatíveis com o SkiaSharp.

Para salvar um `SKBitmap` objeto chamado na `bitmap` biblioteca de fotos do usuário, você também precisa de um membro da `SKEncodedImageFormat` enumeração denominada `imageFormat` e (para formatos com perdas) uma `quality` variável de inteiro. Você pode usar o código a seguir para salvar esse bitmap em um arquivo com o nome `filename` na `folder` pasta:

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

A `SKManagedWStream` classe deriva de `SKWStream` (que significa "fluxo gravável"). O `Encode` método grava o arquivo de bitmap codificado nesse fluxo. Os comentários nesse código referem-se a alguma verificação de erro que talvez seja necessário executar.

A página **salvar formatos de arquivo** no aplicativo [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) usa um código semelhante para permitir que você experimente salvar um bitmap nos vários formatos.

O arquivo XAML contém um `SKCanvasView` que exibe um bitmap, enquanto o restante da página contém tudo o que o aplicativo precisa para chamar o `Encode` método `SKBitmap` . Ele tem um `Picker` para um membro da `SKEncodedImageFormat` enumeração, um `Slider` para o argumento de qualidade para formatos de bitmap com perdas, duas `Entry` exibições para um nome de arquivo e pasta e um `Button` para salvar o arquivo.

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

O arquivo code-behind carrega um recurso de bitmap e usa o `SKCanvasView` para exibi-lo. Esse bitmap nunca é alterado. O `SelectedIndexChanged` manipulador para o `Picker` modifica o nome de arquivo com uma extensão que é igual ao membro de enumeração:

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

O `Clicked` manipulador para o `Button` faz todo o trabalho real. Ele obtém dois argumentos para `Encode` de `Picker` e `Slider` , em seguida, usa o código mostrado anteriormente para criar um `SKManagedWStream` para o `Encode` método. Os dois `Entry` modos de exibição fornecerão nomes de arquivo e pasta para o `SavePhotoAsync` método.

A maior parte desse método é dedicada ao tratamento de problemas ou erros. Se `Encode` o criar uma matriz vazia, significa que não há suporte para o formato de arquivo específico. Se `SavePhotoAsync` retornar `false` , o arquivo não foi salvo com êxito.

Este é o programa em execução:

[![Salvar formatos de arquivo](saving-images/SaveFileFormats.png "Salvar formatos de arquivo")](saving-images/SaveFileFormats-Large.png#lightbox)

Essa captura de tela mostra os três formatos que têm suporte nessas plataformas:

- JPEG
- PNG
- WebP

Para todos os outros formatos, o `Encode` método não grava nada no fluxo e a matriz de bytes resultante está vazia.

O bitmap que a página **salvar formatos de arquivo** salva é 600-pixels quadrado. Com 4 bytes por pixel, esse é um total de 1.440.000 bytes na memória. A tabela a seguir mostra o tamanho do arquivo para várias combinações de formato de arquivo e qualidade:

|Formatar|Qualidade|Tamanho|
|---
Título: Descrição: MS. Prod: MS. Technology: MS. AssetID: autor: MS. Author: MS. Date: no-loc:
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

---|---Título: Descrição: MS. Prod: MS. Technology: MS. AssetID: autor: MS. Author: MS. Date: no-loc:
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

---:|---:| | PNG | N/A | 492K | | JPEG | 0 | 2.95 k | |      | 50 | 22.1 k | |      | 100 | 206K | | WebP | 0 | 2.71 k | |      | 50 | 11.9 k | |      | 100 | 101K |

Você pode experimentar várias configurações de qualidade e examinar os resultados.

## <a name="saving-finger-paint-art"></a>Salvando o dedo-pintura

Um uso comum de um bitmap está em programas de desenho, onde ele funciona como algo chamado de _bitmap de sombra_. Todo o desenho é retido no bitmap, que é exibido pelo programa. O bitmap também é útil para salvar o desenho.

O artigo [**pintura do dedo no SkiaSharp**](../paths/finger-paint.md) demonstrou como usar o controle de toque para implementar um programa primitivo de pintura a dedos. O programa tem suporte apenas para uma cor e apenas uma largura de traço, mas manteve todo o desenho em uma coleção de `SKPath` objetos.

O **dedo de pintura com salvar** página no exemplo [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) também retém todo o desenho em uma coleção de `SKPath` objetos, mas também renderiza o desenho em um bitmap, que pode ser salvo em sua biblioteca de fotos.

Grande parte desse programa é semelhante ao programa original de **pintura a dedo** . Um aprimoramento é que o arquivo XAML agora instancia os botões rotulados como **Clear** e **Save**:

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

O arquivo code-behind mantém um campo do tipo `SKBitmap` chamado `saveBitmap` . Esse bitmap é criado ou recriado no `PaintSurface` manipulador sempre que o tamanho da superfície de exibição é alterado. Se o bitmap precisar ser recriado, o conteúdo do bitmap existente será copiado para o novo bitmap para que tudo seja retido, independentemente de como a superfície de exibição muda de tamanho:

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

O desenho feito pelo `PaintSurface` manipulador ocorre no final e consiste apenas na renderização do bitmap.

O processamento de toque é semelhante ao programa anterior. O programa mantém duas coleções `inProgressPaths` e `completedPaths` , que contêm tudo o que o usuário desenhou desde a última vez em que a exibição foi limpa. Para cada evento de toque, o `OnTouchEffectAction` manipulador chama `UpdateBitmap` :

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

O `UpdateBitmap` método redesenha `saveBitmap` criando um novo `SKCanvas` , limpando-o e renderizando todos os caminhos no bitmap. Ela é concluída pela invalidação `canvasView` para que o bitmap possa ser desenhado na exibição.

Aqui estão os manipuladores para os dois botões. O botão **limpar** limpa as coleções de caminhos, atualizações `saveBitmap` (o que resulta na limpeza do bitmap) e invalida o `SKCanvasView` :

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

O manipulador do botão **salvar** usa o [`Encode`](xref:SkiaSharp.SKImage.Encode) método simplificado de `SKImage` . Esse método codifica usando o formato PNG. O `SKImage` objeto é criado com base em `saveBitmap` e o `SKData` objeto contém o arquivo PNG codificado.

O `ToArray` método de `SKData` Obtém uma matriz de bytes. Isso é o que é passado para o `SavePhotoAsync` método, junto com um nome de pasta fixa, e um nome de arquivo exclusivo construído a partir da data e hora atuais.

Veja o programa em ação:

[![Salvamento de pintura digital](saving-images/FingerPaintSave.png "Salvamento de pintura digital")](saving-images/FingerPaintSave-Large.png#lightbox)

Uma técnica muito semelhante é usada no exemplo de [**SpinPaint**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-spinpaint) . Esse também é um programa de pintura a dedos, exceto que o usuário pinta em um disco de rotação que, em seguida, reproduz os designs em seus outros quatro quadrantes. A cor da pintura do dedo muda à medida que o disco está girando:

[![Pincel de rotação](saving-images/SpinPaint.png "Pincel de rotação")](saving-images/SpinPaint-Large.png#lightbox)

O botão **salvar** da `SpinPaint` classe é semelhante à **pintura digital** , pois salva a imagem em um nome de pasta fixa (**SpainPaint**) e um nome de arquivo construído a partir da data e hora.

## <a name="related-links"></a>Links relacionados

- [APIs do SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
- [SpinPaint (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-spinpaint)
