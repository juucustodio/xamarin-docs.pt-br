---
title: Xamarin.Forms MediaElement
description: Este artigo explica como usar o MediaElement para reproduzir vídeo e áudio em um Xamarin.Forms aplicativo.
ms.prod: xamarin
ms.assetid: e65f1e56-a80d-46c7-9ff4-7ae6650a3165
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/18/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 0daae3edcfbf95d75c566b3ce10f20c83d099e13
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93368304"
---
# <a name="no-locxamarinforms-mediaelement"></a>Xamarin.Forms MediaElement

![API de pré-lançamento](~/media/shared/preview.png)

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/userinterface-mediaelementdemos/)

[`MediaElement`](xref:Xamarin.Forms.MediaElement) é uma exibição para reproduzir vídeo e áudio. A mídia com suporte da plataforma subjacente pode ser reproduzida a partir das seguintes fontes:

- A Web, usando um URI (HTTP ou HTTPS).
- Um recurso inserido no aplicativo da plataforma, usando o `ms-appx:///` esquema de URI.
- Arquivos que vêm das pastas de dados locais e temporárias do aplicativo, usando o `ms-appdata:///` esquema de URI.
- A biblioteca do dispositivo.

[`MediaElement`](xref:Xamarin.Forms.MediaElement) pode usar os controles de reprodução de plataforma, que são chamados de controles de transporte. No entanto, eles são desabilitados por padrão e podem ser substituídos por seus próprios controles de transporte. As capturas de tela a seguir mostram a `MediaElement` reprodução de um vídeo com os controles de transporte da plataforma:

[![Captura de tela de um MediaElement executando um vídeo, no iOS e no Android](mediaelement-images/playback-controls.png "MediaElement reproduzindo um vídeo")](mediaelement-images/playback-controls-large.png#lightbox "MediaElement reproduzindo um vídeo")

[`MediaElement`](xref:Xamarin.Forms.MediaElement) está disponível em Xamarin.Forms 4,5. No entanto, ele está experimental e só pode ser usado adicionando a seguinte linha de código ao arquivo *app.XAML.cs* :

```csharp
Device.SetFlags(new string[]{ "MediaElement_Experimental" });
```

> [!NOTE]
> [`MediaElement`](xref:Xamarin.Forms.MediaElement) está disponível em iOS, Android, Plataforma Universal do Windows (UWP), macOS, Windows Presentation Foundation e tizen.

[`MediaElement`](xref:Xamarin.Forms.MediaElement) define as seguintes propriedades:

- [`Aspect`](xref:Xamarin.Forms.MediaElement.Aspect), do tipo [`Aspect`](xref:Xamarin.Forms.Aspect) , determina como a mídia será dimensionada para se ajustar à área de exibição. O valor padrão dessa propriedade é `AspectFit`.
- [`AutoPlay`](xref:Xamarin.Forms.MediaElement.AutoPlay), do tipo `bool` , indica se a reprodução de mídia será iniciada automaticamente quando a [`Source`](xref:Xamarin.Forms.MediaElement.Source) propriedade for definida. O valor padrão dessa propriedade é `true`.
- [`BufferingProgress`](xref:Xamarin.Forms.MediaElement.BufferingProgress), do tipo `double` , indica o progresso atual do buffer. O valor padrão dessa propriedade é 0,0.
- [`CanSeek`](xref:Xamarin.Forms.MediaElement.CanSeek), do tipo `bool` , indica se a mídia pode ser reposicionada definindo o valor da [`Position`](xref:Xamarin.Forms.MediaElement.Position) propriedade. Trata-se de uma propriedade somente leitura.
- [`CurrentState`](xref:Xamarin.Forms.MediaElement.CurrentState), do tipo [`MediaElementState`](xref:Xamarin.Forms.MediaElementState) , indica o status atual do controle. Esta é uma propriedade somente leitura, cujo valor padrão é `MediaElementState.Closed` .
- [`Duration`](xref:Xamarin.Forms.MediaElement.Duration), do tipo `TimeSpan?` , indica a duração da mídia aberta no momento. Esta é uma propriedade somente leitura cujo valor padrão é `null` .
- [`IsLooping`](xref:Xamarin.Forms.MediaElement.IsLooping), do tipo `bool` , descreve se a fonte de mídia carregada atualmente deve retomar a reprodução do início depois de atingir seu fim. O valor padrão dessa propriedade é `false`.
- [`KeepScreenOn`](xref:Xamarin.Forms.MediaElement.KeepScreenOn), do tipo `bool` , determina se a tela do dispositivo deve permanecer durante a reprodução de mídia. O valor padrão dessa propriedade é `false`.
- [`Position`](xref:Xamarin.Forms.MediaElement.Position), do tipo `TimeSpan` , descreve o progresso atual por meio do tempo de reprodução da mídia. O valor padrão dessa propriedade é `TimeSpan.Zero`.
- [`ShowsPlaybackControls`](xref:Xamarin.Forms.MediaElement.ShowsPlaybackControls), do tipo `bool` , determina se os controles de reprodução de plataformas são exibidos. O valor padrão dessa propriedade é `false`.
- [`Source`](xref:Xamarin.Forms.MediaElement.Source), do tipo [`MediaSource`](xref:Xamarin.Forms.MediaSource) , indica a origem da mídia carregada no controle.
- [`VideoHeight`](xref:Xamarin.Forms.MediaElement.VideoHeight), do tipo `int` , indica a altura do controle. Trata-se de uma propriedade somente leitura.
- [`VideoWidth`](xref:Xamarin.Forms.MediaElement.VideoWidth), do tipo `int` , indica a largura do controle. Trata-se de uma propriedade somente leitura.
- [`Volume`](xref:Xamarin.Forms.MediaElement.Volume), do tipo `double` , determina o volume da mídia, que é representado em uma escala linear entre 0 e 1. Essa propriedade usa uma `TwoWay` associação, e seu valor padrão é 1.

Essas propriedades, com exceção da `CanSeek` propriedade, são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que eles podem ser destinos de associações de dados e estilizados.

A [`MediaElement`](xref:Xamarin.Forms.MediaElement) classe também define quatro eventos:

- [`MediaOpened`](xref:Xamarin.Forms.MediaElement.MediaOpened) é acionado quando o fluxo de mídia é validado e aberto.
- [`MediaEnded`](xref:Xamarin.Forms.MediaElement.MediaEnded) é acionado quando o `MediaElement` termina de reproduzir sua mídia.
- [`MediaFailed`](xref:Xamarin.Forms.MediaElement.MediaFailed) é acionado quando há um erro associado à origem da mídia.
- [`SeekCompleted`](xref:Xamarin.Forms.MediaElement.SeekCompleted) é acionado quando o ponto de busca de uma operação de busca solicitada está pronto para reprodução.

Além disso, [`MediaElement`](xref:Xamarin.Forms.MediaElement) inclui [`Play`](xref:Xamarin.Forms.MediaElement.Play) os [`Pause`](xref:Xamarin.Forms.MediaElement.Pause) métodos, e [`Stop`](xref:Xamarin.Forms.MediaElement.Stop) .

Para obter informações sobre os formatos de mídia com suporte no Android, consulte [formatos de mídia com suporte](https://developer.android.com/guide/topics/media/media-formats) no Developer.Android.com. Para obter informações sobre os formatos de mídia com suporte no Plataforma Universal do Windows (UWP), consulte [codecs com suporte](/windows/uwp/audio-video-camera/supported-codecs).

## <a name="play-remote-media"></a>Reproduzir mídia remota

Um [`MediaElement`](xref:Xamarin.Forms.MediaElement) pode reproduzir arquivos de mídia remota usando os esquemas de URI http e HTTPS. Isso é feito definindo a [`Source`](xref:Xamarin.Forms.MediaElement.Source) propriedade como o URI do arquivo de mídia:

```xaml
<MediaElement Source="https://sec.ch9.ms/ch9/5d93/a1eab4bf-3288-4faf-81c4-294402a85d93/XamarinShow_mid.mp4"
              ShowsPlaybackControls="True" />
```

Por padrão, a mídia definida pela [`Source`](xref:Xamarin.Forms.MediaElement.Source) propriedade é reproduzida imediatamente após a mídia ser aberta. Para suprimir a reprodução automática de mídia, defina a [`AutoPlay`](xref:Xamarin.Forms.MediaElement.AutoPlay) propriedade como `false` .

Os controles de reprodução de mídia são desabilitados por padrão e são habilitados pela definição da [`ShowsPlaybackControls`](xref:Xamarin.Forms.MediaElement.ShowsPlaybackControls) propriedade como `true` . [`MediaElement`](xref:Xamarin.Forms.MediaElement) usará os controles de reprodução da plataforma.

## <a name="play-local-media"></a>Reproduzir mídia local

A mídia local pode ser reproduzida a partir das seguintes fontes:

- Um recurso inserido no aplicativo da plataforma, usando o `ms-appx:///` esquema de URI.
- Arquivos que vêm das pastas de dados locais e temporárias do aplicativo, usando o `ms-appdata:///` esquema de URI.
- A biblioteca do dispositivo.

Para obter mais informações sobre esses esquemas de URI, consulte [esquemas de URI](/windows/uwp/app-resources/uri-schemes).

### <a name="play-media-embedded-in-the-app-package"></a>Reproduzir mídia inserida no pacote do aplicativo

Um [`MediaElement`](xref:Xamarin.Forms.MediaElement) pode reproduzir arquivos de mídia inseridos no pacote do aplicativo, usando o `ms-appx:///` esquema de URI. Os arquivos de mídia são inseridos no pacote do aplicativo colocando-os no projeto da plataforma.

O armazenamento de um arquivo de mídia no projeto de plataforma é diferente para cada plataforma:

- No iOS, os arquivos de mídia devem ser armazenados na pasta **recursos** ou uma subpasta da pasta **recursos** . O arquivo de mídia deve ter um `Build Action` de `BundleResource` .
- No Android, os arquivos de mídia devem ser armazenados em uma subpasta de **recursos** denominada **RAW**. A pasta **raw** não pode conter subpastas. O arquivo de mídia deve ter um `Build Action` de `AndroidResource` .
- No UWP, os arquivos de mídia podem ser armazenados em qualquer pasta no projeto. O arquivo de mídia deve ter um `BuildAction` de `Content` .

Os arquivos de mídia que atendem a esses critérios podem ser reproduzidos usando o `ms-appx:///` esquema de URI:

```xaml
<MediaElement Source="ms-appx:///XamarinForms101UsingEmbeddedImages.mp4"
              ShowsPlaybackControls="True" />
```

Ao usar a vinculação de dados, um conversor de valor pode ser usado para aplicar este esquema de URI:

```csharp
public class VideoSourceConverter : IValueConverter
{
    public object Convert(object value, Type targetType, object parameter, CultureInfo culture)
    {
        if (value == null)
            return null;

        if (string.IsNullOrWhiteSpace(value.ToString()))
            return null;

        if (Device.RuntimePlatform == Device.UWP)
            return new Uri($"ms-appx:///Assets/{value}");
        else
            return new Uri($"ms-appx:///{value}");
    }
    // ...
}
```

Uma instância do `VideoSourceConverter` pode ser usada para aplicar o esquema de `ms-appx:///` URI a um arquivo de mídia inserido:

```xaml
<MediaElement Source="{Binding MediaSource, Converter={StaticResource VideoSourceConverter}}"
              ShowsPlaybackControls="True" />
```

Para obter mais informações sobre o esquema de URI do MS-Appx, consulte [MS-Appx e MS-Appx-Web](/windows/uwp/app-resources/uri-schemes#ms-appx-and-ms-appx-web).

### <a name="play-media-from-the-apps-local-and-temporary-folders"></a>Reproduzir mídia das pastas locais e temporárias do aplicativo

Um [`MediaElement`](xref:Xamarin.Forms.MediaElement) pode reproduzir arquivos de mídia que são copiados para as pastas de dados locais ou temporárias do aplicativo, usando o `ms-appdata:///` esquema de URI.

O exemplo a seguir mostra a [`Source`](xref:Xamarin.Forms.MediaElement.Source) propriedade definida como um arquivo de mídia que é armazenado na pasta de dados local do aplicativo:

```xaml
<MediaElement Source="ms-appdata:///local/XamarinVideo.mp4"
              ShowsPlaybackControls="True" />
```

O exemplo a seguir mostra a [`Source`](xref:Xamarin.Forms.MediaElement.Source) propriedade para um arquivo de mídia que é armazenado na pasta de dados temporária do aplicativo:

```xaml
<MediaElement Source="ms-appdata:///temp/XamarinVideo.mp4"
              ShowsPlaybackControls="True" />
```

> [!IMPORTANT]
> Além de reproduzir arquivos de mídia que são armazenados nas pastas de dados locais ou temporárias do aplicativo, a UWP também pode reproduzir arquivos de mídia localizados na pasta de roaming do aplicativo. Isso pode ser obtido com a prefixação do arquivo de mídia com `ms-appdata:///roaming/` .

Ao usar a vinculação de dados, um conversor de valor pode ser usado para aplicar este esquema de URI:

```csharp
public class VideoSourceConverter : IValueConverter
{
    public object Convert(object value, Type targetType, object parameter, CultureInfo culture)
    {
        if (value == null)
            return null;

        if (string.IsNullOrWhiteSpace(value.ToString()))
            return null;

        return new Uri($"ms-appdata:///{value}");
    }
    // ...
}
```

Uma instância do `VideoSourceConverter` pode ser usada para aplicar o esquema de `ms-appdata:///` URI a um arquivo de mídia na pasta de dados local ou temporária do aplicativo:

```xaml
<MediaElement Source="{Binding MediaSource, Converter={StaticResource VideoSourceConverter}}"
              ShowsPlaybackControls="True" />
```

Para obter mais informações sobre o esquema de URI do MS-AppData, consulte [MS-AppData](/windows/uwp/app-resources/uri-schemes#ms-appdata).

#### <a name="copying-a-media-file-to-the-apps-local-or-temporary-data-folder"></a>Copiando um arquivo de mídia para a pasta de dados local ou temporária do aplicativo

A reprodução de um arquivo de mídia armazenado na pasta de dados local ou temporária do aplicativo exige que o arquivo de mídia seja copiado ali pelo aplicativo. Isso pode ser feito, por exemplo, copiando um arquivo de mídia do pacote do aplicativo:

```csharp
// This method copies the video from the app package to the app data
// directory for your app. To copy the video to the temp directory
// for your app, comment out the first line of code, and uncomment
// the second line of code.
public static async Task CopyVideoIfNotExists(string filename)
{
    string folder = FileSystem.AppDataDirectory;
    //string folder = Path.GetTempPath();
    string videoFile = Path.Combine(folder, "XamarinVideo.mp4");

    if (!File.Exists(videoFile))
    {
        using (Stream inputStream = await FileSystem.OpenAppPackageFileAsync(filename))
        {
            using (FileStream outputStream = File.Create(videoFile))
            {
                await inputStream.CopyToAsync(outputStream);
            }
        }
    }
}
```

> [!NOTE]
> O exemplo de código acima usa a `FileSystem` classe incluída no Xamarin.Essentials . Para obter mais informações, consulte [ Xamarin.Essentials : auxiliares do sistema de arquivos](~/essentials/file-system-helpers.md?context=xamarin%2Fxamarin-forms&tabs=android).

### <a name="play-media-from-the-device-library"></a>Reproduzir mídia da biblioteca de dispositivos

A maioria dos dispositivos móveis e computadores desktops modernos tem a capacidade de gravar vídeos e áudio usando a câmera e o microfone do dispositivo. A mídia criada é então armazenada como arquivos no dispositivo. Esses arquivos podem ser recuperados da biblioteca e reproduzidos pelo [`MediaElement`](xref:Xamarin.Forms.MediaElement) .

Cada uma das plataformas inclui uma instalação que permite ao usuário selecionar mídia na biblioteca do dispositivo. No Xamarin.Forms , os projetos de plataforma podem invocar essa funcionalidade e podem ser chamados pela [`DependencyService`](xref:Xamarin.Forms.DependencyService) classe.

O serviço de dependência de separação de vídeo usado no aplicativo de exemplo é muito semelhante a um definido na [separação de uma foto da biblioteca de imagens](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md), exceto que o seletor retorna um nome de arquivo em vez de um `Stream` objeto. O projeto de código compartilhado define uma interface chamada `IVideoPicker` , que define um único método chamado `GetVideoFileAsync` . Em seguida, cada plataforma implementa essa interface em uma `VideoPicker` classe.

O exemplo de código a seguir mostra como recuperar um arquivo de mídia da biblioteca de dispositivos:

```csharp
string filename = await DependencyService.Get<IVideoPicker>().GetVideoFileAsync();
if (!string.IsNullOrWhiteSpace(filename))
{
    mediaElement.Source = new FileMediaSource
    {
        File = filename
    };
}
```

O serviço de dependência de separação de vídeo é invocado chamando o `DependencyService.Get` método para obter a implementação de uma `IVideoPicker` interface no projeto de plataforma. `GetVideoFileAsync`Em seguida, o método é chamado nessa instância, e o FileName retornado é usado para criar um [`FileMediaSource`](xref:Xamarin.Forms.FileMediaSource) objeto e defini-lo como a [`Source`](xref:Xamarin.Forms.MediaElement.Source) Propriedade do [`MediaElement`](xref:Xamarin.Forms.MediaElement) .

## <a name="change-video-aspect-ratio"></a>Alterar a taxa de proporção do vídeo

A [`Aspect`](xref:Xamarin.Forms.MediaElement.Aspect) propriedade determina como a mídia de vídeo será dimensionada para caber na área de exibição. Por padrão, essa propriedade é definida como o `AspectFit` membro de enumeração, mas pode ser definida como qualquer um dos [`Aspect`](xref:Xamarin.Forms.Aspect) membros de enumeração:

- `AspectFit` indica que o vídeo será letterboxed, se necessário, para se ajustar à área de exibição, preservando a taxa de proporção.
- `AspectFill` indica que o vídeo será recortado para que ele preencha a área de exibição, preservando a taxa de proporção.
- `Fill` indica que o vídeo será alongado para preencher a área de exibição.

## <a name="poll-for-position-data"></a>Pesquisa de dados de posição

A notificação de alteração de propriedade para a [`Position`](xref:Xamarin.Forms.MediaElement.Position) propriedade vinculável só é acionada na chave momentos, como a reprodução iniciada e terminação e a pausa ocorre. Portanto, a vinculação de dados com a `Position` propriedade não produzirá dados de posição precisos. Em vez disso, você deve configurar um temporizador e sondar a propriedade.

Um bom lugar para fazer isso é na `OnAppearing` substituição da página que requer os dados de posição conforme a mídia é reproduzida:

```csharp
bool polling = true;

protected override void OnAppearing()
{
    base.OnAppearing();

    Device.StartTimer(TimeSpan.FromMilliseconds(1000), () =>
    {
        Device.BeginInvokeOnMainThread(() =>
        {
            positionLabel.Text = mediaElement.Position.ToString("hh\\:mm\\:ss");
        });
        return polling;
    });
}

protected override void OnDisappearing()
{
    base.OnDisappearing();
    polling = false;
}
```

Neste exemplo, a `OnAppearing` substituição inicia um temporizador que atualiza `positionLabel` com o `Position` valor a cada segundo. O retorno de chamada do temporizador é invocado a cada segundo, até o retorno de chamada retornar `false` . Quando a navegação de página ocorre `OnDisappearing` , a substituição é executada, o que interrompe a chamada do retorno de chamada do temporizador.

## <a name="understand-mediasource-types"></a>Entender os tipos de Mediary

Um [`MediaElement`](xref:Xamarin.Forms.MediaElement) pode reproduzir mídia definindo sua [`Source`](xref:Xamarin.Forms.MediaElement.Source) propriedade como um arquivo de mídia local ou remoto. A `Source` propriedade é do tipo [`MediaSource`](xref:Xamarin.Forms.MediaSource) , e essa classe define dois métodos estáticos:

- [`FromFile`](xref:Xamarin.Forms.MediaSource.FromFile*), retorna uma [`MediaSource`](xref:Xamarin.Forms.MediaSource) instância de um `string` argumento.
- [`FromUri`](xref:Xamarin.Forms.MediaSource.FromUri*), retorna uma [`MediaSource`](xref:Xamarin.Forms.MediaSource) instância de um `Uri` argumento.

Além disso, a [`MediaSource`](xref:Xamarin.Forms.MediaSource) classe também tem operadores implícitos que retornam `MediaSource` instâncias de `string` `Uri` argumentos e.

> [!NOTE]
> Quando a [`Source`](xref:Xamarin.Forms.MediaElement.Source) propriedade é definida em XAML, um conversor de tipo é chamado para retornar uma [`MediaSource`](xref:Xamarin.Forms.MediaSource) instância de um `string` ou `Uri` .

A [`MediaSource`](xref:Xamarin.Forms.MediaSource) classe também tem duas classes derivadas:

- [`UriMediaSource`](xref:Xamarin.Forms.UriMediaSource), que é usado para especificar um arquivo de mídia remoto de um URI. Essa classe tem uma [`Uri`](xref:Xamarin.Forms.UriMediaSource.Uri) propriedade que pode ser definida como um `Uri` .
- [`FileMediaSource`](xref:Xamarin.Forms.FileMediaSource), que é usado para especificar um arquivo de mídia local de um `string` . Essa classe tem uma [`File`](xref:Xamarin.Forms.FileMediaSource.File) propriedade que pode ser definida como um `string` . Além disso, essa classe tem operadores implícitos para converter um `string` em um `FileMediaSource` objeto e um `FileMediaSource` objeto para um `string` .

> [!NOTE]
> Quando um [`FileMediaSource`](xref:Xamarin.Forms.FileMediaSource) objeto é criado em XAML, um conversor de tipo é chamado para retornar uma [`FileMediaSource`](xref:Xamarin.Forms.FileMediaSource) instância de um `string` .

## <a name="determine-mediaelement-status"></a>Determinar o status do MediaElement

A [`MediaElement`](xref:Xamarin.Forms.MediaElement) classe define uma propriedade vinculável somente leitura chamada [`CurrentState`](xref:Xamarin.Forms.MediaElement.CurrentState) , do tipo [`MediaElementState`](xref:Xamarin.Forms.MediaElementState) . Essa propriedade indica o status atual do controle, como se a mídia está sendo reproduzida ou pausada, ou se ainda não está pronta para reproduzir a mídia.

A [`MediaElementState`](xref:Xamarin.Forms.MediaElementState) enumeração define os seguintes membros:

- `Closed` indica que o `MediaElement` não contém mídia.
- `Opening` indica que o `MediaElement` está validando e tentando carregar a origem especificada.
- `Buffering` indica que o `MediaElement` está carregando a mídia para reprodução. Sua [`Position`](xref:Xamarin.Forms.MediaElement.Position) propriedade não avança durante esse estado. Se o `MediaElement` vídeo estava em execução, continua exibindo o último quadro exibido.
- `Playing` indica que o `MediaElement` está reproduzindo a origem da mídia.
- `Paused` indica que o `MediaElement` não avança sua [`Position`](xref:Xamarin.Forms.MediaElement.Position) propriedade. Se o `MediaElement` vídeo estava em execução, continua exibindo o quadro atual.
- `Stopped` indica que a `MediaElement` mídia contém, mas não está sendo reproduzida ou pausada. Sua [`Position`](xref:Xamarin.Forms.MediaElement.Position) propriedade é 0 e não avança. Se a mídia carregada for vídeo, o `MediaElement` exibirá o primeiro quadro.

Geralmente, não é necessário examinar a [`CurrentState`](xref:Xamarin.Forms.MediaElement.CurrentState) propriedade ao usar os [`MediaElement`](xref:Xamarin.Forms.MediaElement) controles de transporte. No entanto, essa propriedade se torna importante ao implementar seus próprios controles de transporte.

## <a name="implement-custom-transport-controls"></a>Implementar controles de transporte personalizados

Os controles de transporte de um player de mídia incluem os botões que executam as funções **reproduzir** , **Pausar** e **parar**. Em geral, esses botões são identificados com ícones conhecidos, em vez de um texto, e as funções **Reproduzir** e **Pausar** geralmente são combinadas em um botão.

Por padrão, os [`MediaElement`](xref:Xamarin.Forms.MediaElement) controles de reprodução são desabilitados. Isso permite que você controle o `MediaElement` programaticamente ou fornecendo seus próprios controles de transporte. Para dar suporte a isso, `MediaElement` inclui os [`Play`](xref:Xamarin.Forms.MediaElement.Play) [`Pause`](xref:Xamarin.Forms.MediaElement.Pause) métodos, e [`Stop`](xref:Xamarin.Forms.MediaElement.Stop) .

O exemplo XAML a seguir mostra uma página que contém um [`MediaElement`](xref:Xamarin.Forms.MediaElement) e controles de transporte personalizados:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="MediaElementDemos.CustomTransportPage"
             Title="Custom transport">
    <Grid>
        ...
        <MediaElement x:Name="mediaElement"
                      AutoPlay="False"
                      ... />
        <StackLayout BindingContext="{x:Reference mediaElement}"
                     ...>
            <Button Text="&#x25B6;&#xFE0F; Play"
                    HorizontalOptions="CenterAndExpand"
                    Clicked="OnPlayPauseButtonClicked">
                <Button.Triggers>
                    <DataTrigger TargetType="Button"
                                 Binding="{Binding CurrentState}"
                                 Value="{x:Static MediaElementState.Playing}">
                        <Setter Property="Text"
                                Value="&#x23F8; Pause" />
                    </DataTrigger>
                    <DataTrigger TargetType="Button"
                                 Binding="{Binding CurrentState}"
                                 Value="{x:Static MediaElementState.Buffering}">
                        <Setter Property="IsEnabled"
                                Value="False" />
                    </DataTrigger>
                </Button.Triggers>
            </Button>
            <Button Text="&#x23F9; Stop"
                    HorizontalOptions="CenterAndExpand"
                    Clicked="OnStopButtonClicked">
                <Button.Triggers>
                    <DataTrigger TargetType="Button"
                                 Binding="{Binding CurrentState}"
                                 Value="{x:Static MediaElementState.Stopped}">
                        <Setter Property="IsEnabled"
                                Value="False" />
                    </DataTrigger>
                </Button.Triggers>
            </Button>
        </StackLayout>
    </Grid>
</ContentPage>
```

Neste exemplo, os controles de transporte personalizados são definidos como [`Button`](xref:Xamarin.Forms.Button) objetos. No entanto, há apenas dois `Button` objetos, com o primeiro `Button` representando **Play** e **Pause** , e a segunda `Button` representando **Stop**. [`DataTrigger`](xref:Xamarin.Forms.DataTrigger) os objetos são usados para habilitar e desabilitar os botões e para alternar o primeiro botão entre **reproduzir** e **Pausar**. Para obter mais informações sobre gatilhos de dados, consulte [ Xamarin.Forms gatilhos](~/xamarin-forms/app-fundamentals/triggers.md).

O arquivo code-behind tem os manipuladores para os [`Clicked`](xref:Xamarin.Forms.Button.Clicked) eventos:

```csharp
void OnPlayPauseButtonClicked(object sender, EventArgs args)
{
    if (mediaElement.CurrentState == MediaElementState.Stopped ||
        mediaElement.CurrentState == MediaElementState.Paused)
    {
        mediaElement.Play();
    }
    else if (mediaElement.CurrentState == MediaElementState.Playing)
    {
        mediaElement.Pause();
    }
}

void OnStopButtonClicked(object sender, EventArgs args)
{
    mediaElement.Stop();
}
```

O botão **Play** pode ser pressionado, quando ele se torna habilitado, para começar a reprodução:

[![Captura de tela de um MediaElement com controles de transporte personalizados em iOS e Android](mediaelement-images/custom-transport-playback.png "MediaElement reproduzindo um vídeo")](mediaelement-images/custom-transport-playback-large.png#lightbox "MediaElement reproduzindo um vídeo")

Pressionar o botão **Pausar** resulta na pausa da reprodução:

[![Captura de tela de um MediaElement com reprodução pausada, no iOS e no Android](mediaelement-images/custom-transport-paused.png "MediaElement com um vídeo em pausa")](mediaelement-images/custom-transport-paused-large.png#lightbox "MediaElement com um vídeo em pausa")

Pressionar o botão **parar** interrompe a reprodução e retorna a posição do arquivo de mídia para o início.

## <a name="implement-a-custom-position-bar"></a>Implementar uma barra de posição personalizada

Os controles de transporte implementados por cada plataforma incluem uma barra de posição. Essa barra é semelhante a um controle deslizante e mostra o local atual da mídia dentro de sua duração total. Além disso, você pode manipular a barra de posição para avançar ou retroceder para uma nova posição no vídeo.

A implementação de uma barra de posição personalizada requer conhecer a duração da mídia e a posição de reprodução atual. Esses dados estão disponíveis nas [`Duration`](xref:Xamarin.Forms.MediaElement.Duration) Propriedades e [`Position`](xref:Xamarin.Forms.MediaElement.Position) .

> [!IMPORTANT]
> O [`Position`](xref:Xamarin.Forms.MediaElement.Position) deve ser sondado para obter dados de posição precisos. Para obter mais informações, consulte [sondagem de dados de posição](#poll-for-position-data).

Uma barra de posição personalizada pode ser implementada usando um [`Slider`](xref:Xamarin.Forms.Slider) , conforme mostrado no exemplo a seguir:

```csharp
public class PositionSlider : Slider
{
    public static readonly BindableProperty DurationProperty =
        BindableProperty.Create(nameof(Duration), typeof(TimeSpan), typeof(PositionSlider), new TimeSpan(1),
                                propertyChanged: (bindable, oldValue, newValue) =>
                                {
                                    ((PositionSlider)bindable).SetTimeToEnd();
                                    double seconds = ((TimeSpan)newValue).TotalSeconds;
                                    ((Slider)bindable).Maximum = seconds <= 0 ? 1 : seconds;
                                });

    public TimeSpan Duration
    {
        get { return (TimeSpan)GetValue(DurationProperty); }
        set { SetValue(DurationProperty, value); }
    }

    public static readonly BindableProperty PositionProperty =
        BindableProperty.Create(nameof(Position), typeof(TimeSpan), typeof(PositionSlider), new TimeSpan(0),
                                propertyChanged: (bindable, oldValue, newValue) => ((PositionSlider)bindable).SetTimeToEnd());

    public TimeSpan Position
    {
        get { return (TimeSpan)GetValue(PositionProperty); }
        set { SetValue(PositionProperty, value); }
    }

    static readonly BindablePropertyKey TimeToEndPropertyKey =
        BindableProperty.CreateReadOnly(nameof(TimeToEnd), typeof(TimeSpan), typeof(PositionSlider), new TimeSpan());

    public static readonly BindableProperty TimeToEndProperty = TimeToEndPropertyKey.BindableProperty;

    public TimeSpan TimeToEnd
    {
        get { return (TimeSpan)GetValue(TimeToEndProperty); }
        private set { SetValue(TimeToEndPropertyKey, value); }
    }

    public PositionSlider()
    {
        PropertyChanged += (sender, args) =>
        {
            if (args.PropertyName == "Value")
            {
                TimeSpan newPosition = TimeSpan.FromSeconds(Value);
                if (Math.Abs(newPosition.TotalSeconds - Position.TotalSeconds) / Duration.TotalSeconds > 0.01)
                {
                    Position = newPosition;
                }
            }
        };
    }

    void SetTimeToEnd()
    {
        TimeToEnd = Duration - Position;
    }
}
```

A `PositionSlider` classe define suas `Duration` Propriedades próprias e `Position` vinculáveis e uma `TimeToEnd` propriedade vinculável. Todas as três propriedades são do tipo `TimeSpan` . O manipulador de propriedade alterada para a `Duration` propriedade define a `Maximum` Propriedade do [`Slider`](xref:Xamarin.Forms.Slider) para a `TotalSeconds` Propriedade do `TimeSpan` valor. A `TimeToEnd` propriedade é calculada com base nas alterações `Duration` nas `Position` Propriedades e e começa na duração da mídia e diminui para zero à medida que a reprodução prossegue.

O `PositionSlider` é atualizado a partir do subjacente [`Slider`](xref:Xamarin.Forms.Slider) quando o `Slider` é movido para indicar que a mídia deve ser avançada ou revertida para uma nova posição. Isso é detectado no `PropertyChanged` manipulador no `PositionSlider` Construtor. O manipulador verifica uma alteração na propriedade `Value` e, se for diferente da propriedade `Position`, então a propriedade `Position` será definida com base na propriedade `Value`. Para obter mais informações sobre como usar um [`Slider`](xref:Xamarin.Forms.Slider) [ Xamarin.Forms controle deslizante](~/xamarin-forms/user-interface/slider.md) , consulte

> [!NOTE]
> No Android, o [`Slider`](xref:Xamarin.Forms.Slider) tem apenas 1000 etapas discretas, independentemente das `Minimum` `Maximum` configurações e. Se o comprimento da mídia for maior que 1000 segundos, os dois `Position` valores diferentes corresponderão ao mesmo `Value` do `Slider` . É por isso que o código acima verifica se a nova posição e a posição existente são maiores que um centésimo da duração geral.

O exemplo a seguir mostra o `PositionSlider` que está sendo consumido em uma página:

```xaml
<controls:PositionSlider x:Name="positionSlider"
                         BindingContext="{x:Reference mediaElement}"
                         Duration="{Binding Duration}"
                         ValueChanged="OnPositionSliderValueChanged">
    <controls:PositionSlider.Triggers>
        <DataTrigger TargetType="controls:PositionSlider"
                     Binding="{Binding CurrentState}"
                     Value="{x:Static MediaElementState.Buffering}">
            <Setter Property="IsEnabled" Value="False" />
        </DataTrigger>
    </controls:PositionSlider.Triggers>
</controls:PositionSlider>
```

Neste exemplo, a `Duration` propriedade de `PositionSlider` é vinculada a dados à [`Duration`](xref:Xamarin.Forms.MediaElement.Duration) Propriedade do [`MediaElement`](xref:Xamarin.Forms.MediaElement) . Quando a [`Value`](xref:Xamarin.Forms.Slider.Value) propriedade das [`Slider`](xref:Xamarin.Forms.Slider) alterações, o `ValueChanged` evento é acionado e o `OnPositionSliderValueChanged` manipulador é executado. Esse manipulador define a [`MediaElement.Position`](xref:Xamarin.Forms.MediaElement.Position) propriedade como o valor da `PositionSlider.Position` propriedade. Portanto, arrastar os `Slider` resultados na posição de reprodução de mídia alterando:

[![Captura de tela de um MediaElement com uma barra de posição personalizada, no iOS e no Android](mediaelement-images/custom-position-bar.png "MediaElement com uma barra de posição personalizada")](mediaelement-images/custom-position-bar-large.png#lightbox "MediaElement com uma barra de posição personalizada")

Além disso, um [`DataTrigger`](xref:Xamarin.Forms.DataTrigger) objeto é usado para desabilitar o `PositionSlider` quando a mídia está em buffer. Para obter mais informações sobre gatilhos de dados, consulte [ Xamarin.Forms gatilhos](~/xamarin-forms/app-fundamentals/triggers.md).

## <a name="implement-a-custom-volume-control"></a>Implementar um controle de volume personalizado

Os controles de reprodução de mídia implementados por cada plataforma incluem uma barra de volume. Essa barra é semelhante a um controle deslizante e mostra o volume da mídia. Além disso, você pode manipular a barra de volume para aumentar ou diminuir o volume.

Uma barra de volume personalizada pode ser implementada usando um [`Slider`](xref:Xamarin.Forms.Slider) , conforme mostrado no exemplo a seguir:

```xaml
<StackLayout>
    <MediaElement AutoPlay="False"
                  Source="{StaticResource AdvancedAsync}" />
    <Slider Maximum="1.0"
            Minimum="0.0"
            Value="{Binding Volume}"
            Rotation="270"
            WidthRequest="100" />
</StackLayout>
```

Neste exemplo, os [`Slider`](xref:Xamarin.Forms.Slider) dados associam sua `Value` Propriedade à [`Volume`](xref:Xamarin.Forms.MediaElement.Volume) Propriedade do [`MediaElement`](xref:Xamarin.Forms.MediaElement) . Isso é possível porque a `Volume` propriedade usa uma `TwoWay` associação. Portanto, alterar a `Value` Propriedade fará com que a `Volume` propriedade seja alterada.

> [!NOTE]
> A [`Volume`](xref:Xamarin.Forms.MediaElement.Volume) propriedade tem um retorno de chamada de validação que garante que seu valor seja maior ou igual a 0,0 e menor ou igual a 1,0.

Para obter mais informações sobre como usar um [`Slider`](xref:Xamarin.Forms.Slider) [ Xamarin.Forms controle deslizante](~/xamarin-forms/user-interface/slider.md) , consulte

## <a name="related-links"></a>Links relacionados

- [MediaElementDemos (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-mediaelementdemos/)
- [Esquemas de URI](/windows/uwp/app-resources/uri-schemes)
- [Xamarin.Forms Gatilhos](~/xamarin-forms/app-fundamentals/triggers.md)
- [Xamarin.Forms Classificação](~/xamarin-forms/user-interface/slider.md)
- [Android: formatos de mídia com suporte](https://developer.android.com/guide/topics/media/media-formats)
- [UWP: codecs com suporte](/windows/uwp/audio-video-camera/supported-codecs)