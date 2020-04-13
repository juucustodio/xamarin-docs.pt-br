---
title: Xamarin.Forms MediaElement
description: Este artigo explica como usar o MediaElement para reproduzir vídeo e áudio em um aplicativo Xamarin.Forms.
ms.prod: xamarin
ms.assetid: e65f1e56-a80d-46c7-9ff4-7ae6650a3165
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/18/2020
ms.openlocfilehash: 6f6c51c428de569ceb09ed6a26cfc36881c86dc5
ms.sourcegitcommit: b93754b220fca3d6e3d131341e3cfbe233d10f84
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80628334"
---
# <a name="xamarinforms-mediaelement"></a>Xamarin.Forms MediaElement

![](~/media/shared/preview.png "This API is currently pre-release")

[![Baixar](~/media/shared/download.png) amostra Baixar a amostra](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-mediaelementdemos/)

[`MediaElement`](xref:Xamarin.Forms.MediaElement)é uma exibição para reproduzir vídeo e áudio. A mídia suportada pela plataforma subjacente pode ser reproduzida a partir das seguintes fontes:

- A web, usando um URI (HTTP ou HTTPS).
- Um recurso incorporado no aplicativo `ms-appx:///` da plataforma, usando o esquema URI.
- Arquivos provenientes das pastas de dados locais e `ms-appdata:///` temporários do aplicativo, usando o esquema URI.
- A biblioteca do dispositivo.

[`MediaElement`](xref:Xamarin.Forms.MediaElement)pode usar os controles de reprodução da plataforma, que são chamados de controles de transporte. No entanto, eles são desativados por padrão e podem ser substituídos por seus próprios controles de transporte. As capturas `MediaElement` de tela a seguir mostram a reprodução de um vídeo com os controles de transporte da plataforma:

[![Captura de tela de um MediaElement reproduzindo um vídeo, no iOS e Android](mediaelement-images/playback-controls.png "MediaElement reproduzindo um vídeo")](mediaelement-images/playback-controls-large.png#lightbox "MediaElement reproduzindo um vídeo")

[`MediaElement`](xref:Xamarin.Forms.MediaElement)está disponível em Xamarin.Forms 4.5. No entanto, atualmente é experimental e só pode ser usado adicionando a seguinte linha de código ao seu arquivo *App.xaml.cs:*

```csharp
Device.SetFlags(new string[]{ "MediaElement_Experimental" });
```

> [!NOTE]
> [`MediaElement`](xref:Xamarin.Forms.MediaElement)está disponível no iOS, Android, Universal Windows Platform (UWP), macOS, Windows Presentation Foundation e Tizen.

[`MediaElement`](xref:Xamarin.Forms.MediaElement)define as seguintes propriedades:

- [`Aspect`](xref:Xamarin.Forms.MediaElement.Aspect), do [`Aspect`](xref:Xamarin.Forms.Aspect)tipo, determina como a mídia será dimensionada para se adequar à área de exibição. O valor padrão dessa propriedade é `AspectFit`.
- [`AutoPlay`](xref:Xamarin.Forms.MediaElement.AutoPlay), do `bool`tipo, indica se a reprodução [`Source`](xref:Xamarin.Forms.MediaElement.Source) de mídia começará automaticamente quando a propriedade estiver definida. O valor padrão dessa propriedade é `true`.
- [`BufferingProgress`](xref:Xamarin.Forms.MediaElement.BufferingProgress), do `double`tipo, indica o progresso do buffering atual. O valor padrão desta propriedade é 0.0.
- [`CanSeek`](xref:Xamarin.Forms.MediaElement.CanSeek), do `bool`tipo, indica se a mídia pode ser [`Position`](xref:Xamarin.Forms.MediaElement.Position) reposicionada definindo o valor da propriedade. Trata-se de uma propriedade somente leitura.
- [`CurrentState`](xref:Xamarin.Forms.MediaElement.CurrentState), do [`MediaElementState`](xref:Xamarin.Forms.MediaElementState)tipo, indica o estado atual do controle. Esta é uma propriedade somente leitura, `MediaElementState.Closed`cujo valor padrão é .
- [`Duration`](xref:Xamarin.Forms.MediaElement.Duration), do `TimeSpan?`tipo, indica a duração da mídia aberta atualmente. Esta é uma propriedade somente leitura `null`cujo valor padrão é .
- [`IsLooping`](xref:Xamarin.Forms.MediaElement.IsLooping), do `bool`tipo , descreve se a fonte de mídia atualmente carregada deve retomar a reprodução desde o início após chegar ao seu fim. O valor padrão dessa propriedade é `false`.
- [`KeepScreenOn`](xref:Xamarin.Forms.MediaElement.KeepScreenOn), do `bool`tipo, determina se a tela do dispositivo deve permanecer acesa durante a reprodução da mídia. O valor padrão dessa propriedade é `false`.
- [`Position`](xref:Xamarin.Forms.MediaElement.Position), do `TimeSpan`tipo, descreve o progresso atual através do tempo de reprodução da mídia. O valor padrão dessa propriedade é `TimeSpan.Zero`.
- [`ShowsPlaybackControls`](xref:Xamarin.Forms.MediaElement.ShowsPlaybackControls), do `bool`tipo, determina se os controles de reprodução das plataformas são exibidos. O valor padrão dessa propriedade é `false`.
- [`Source`](xref:Xamarin.Forms.MediaElement.Source), do [`MediaSource`](xref:Xamarin.Forms.MediaSource)tipo, indica a fonte da mídia carregada no controle.
- [`VideoHeight`](xref:Xamarin.Forms.MediaElement.VideoHeight), do `int`tipo, indica a altura do controle. Trata-se de uma propriedade somente leitura.
- [`VideoWidth`](xref:Xamarin.Forms.MediaElement.VideoWidth), do `int`tipo, indica a largura do controle. Trata-se de uma propriedade somente leitura.
- [`Volume`](xref:Xamarin.Forms.MediaElement.Volume), do `double`tipo, determina o volume da mídia, que é representada em uma escala linear entre 0 e 1. Esta propriedade `TwoWay` usa uma vinculação, e seu valor padrão é 1.

Essas propriedades, com exceção da propriedade, são apoiadas por `CanSeek` [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que podem ser alvos de vinculações de dados e estilizados.

A [`MediaElement`](xref:Xamarin.Forms.MediaElement) classe também define quatro eventos:

- [`MediaOpened`](xref:Xamarin.Forms.MediaElement.MediaOpened)é disparado quando o fluxo de mídia foi validado e aberto.
- [`MediaEnded`](xref:Xamarin.Forms.MediaElement.MediaEnded)é demitido `MediaElement` quando o acabamento saem jogando sua mídia.
- [`MediaFailed`](xref:Xamarin.Forms.MediaElement.MediaFailed)é disparado quando há um erro associado com a fonte de mídia.
- [`SeekCompleted`](xref:Xamarin.Forms.MediaElement.SeekCompleted)é acionado quando o ponto de busca de uma operação de busca solicitada está pronto para reprodução.

Além [`MediaElement`](xref:Xamarin.Forms.MediaElement) disso, [`Play`](xref:Xamarin.Forms.MediaElement.Play)inclui [`Pause`](xref:Xamarin.Forms.MediaElement.Pause), [`Stop`](xref:Xamarin.Forms.MediaElement.Stop) e métodos.

Para obter informações sobre formatos de mídia suportados no Android, consulte [formatos de mídia suportados](https://developer.android.com/guide/topics/media/media-formats) em developer.android.com. Para obter informações sobre formatos de mídia suportados na Universal Windows Platform (UWP), consulte [codecs suportados](/windows/uwp/audio-video-camera/supported-codecs).

## <a name="play-remote-media"></a>Reproduzir mídia remota

A [`MediaElement`](xref:Xamarin.Forms.MediaElement) pode reproduzir arquivos de mídia remota usando os esquemas HTTP e HTTPS URI. Isso é feito [`Source`](xref:Xamarin.Forms.MediaElement.Source) definindo a propriedade para o URI do arquivo de mídia:

```xaml
<MediaElement Source="https://sec.ch9.ms/ch9/5d93/a1eab4bf-3288-4faf-81c4-294402a85d93/XamarinShow_mid.mp4"
              ShowsPlaybackControls="True" />
```

Por padrão, a mídia definida [`Source`](xref:Xamarin.Forms.MediaElement.Source) pela propriedade é reproduzido imediatamente após a mídia ser aberta. Para suprimir a reprodução [`AutoPlay`](xref:Xamarin.Forms.MediaElement.AutoPlay) automática `false`de mídia, defina a propriedade como .

Os controles de reprodução de mídia são desativados [`ShowsPlaybackControls`](xref:Xamarin.Forms.MediaElement.ShowsPlaybackControls) por `true`padrão e são ativados definindo a propriedade para . [`MediaElement`](xref:Xamarin.Forms.MediaElement)em seguida, usará os controles de reprodução da plataforma.

## <a name="play-local-media"></a>Reproduzir mídia local

A mídia local pode ser reproduzida a partir das seguintes fontes:

- Um recurso incorporado no aplicativo `ms-appx:///` da plataforma, usando o esquema URI.
- Arquivos provenientes das pastas de dados locais e `ms-appdata:///` temporários do aplicativo, usando o esquema URI.
- A biblioteca do dispositivo.

Para obter mais informações sobre esses esquemas URI, consulte [esquemas URI](/windows/uwp/app-resources/uri-schemes).

### <a name="play-media-embedded-in-the-app-package"></a>Reproduzir mídia incorporada no pacote do aplicativo

Uma [`MediaElement`](xref:Xamarin.Forms.MediaElement) pode reproduzir arquivos de mídia que estão `ms-appx:///` incorporados no pacote do aplicativo, usando o esquema URI. Os arquivos de mídia são incorporados no pacote do aplicativo, colocando-os no projeto da plataforma.

Armazenar um arquivo de mídia no projeto da plataforma é diferente para cada plataforma:

- No iOS, os arquivos de mídia devem ser armazenados na pasta **Recursos** ou em uma subpasta da pasta **Recursos.** O arquivo de `Build Action` mídia `BundleResource`deve ter um de .
- No Android, os arquivos de mídia devem ser armazenados em uma subpasta de **recursos** denominada **raw**. A pasta **raw** não pode conter subpastas. O arquivo de `Build Action` mídia `AndroidResource`deve ter um de .
- No UWP, os arquivos de mídia podem ser armazenados em qualquer pasta do projeto. O arquivo de `BuildAction` mídia `Content`deve ter um de .

Os arquivos de mídia que atendam a `ms-appx:///` esses critérios podem ser reproduzidos usando o esquema URI:

```xaml
<MediaElement Source="ms-appx:///XamarinForms101UsingEmbeddedImages.mp4"
              ShowsPlaybackControls="True" />
```

Ao usar a vinculação de dados, um conversor de valor pode ser usado para aplicar este esquema URI:

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

Uma instância `VideoSourceConverter` do pode então ser `ms-appx:///` usada para aplicar o esquema URI a um arquivo de mídia incorporado:

```xaml
<MediaElement Source="{Binding MediaSource, Converter={StaticResource VideoSourceConverter}}"
              ShowsPlaybackControls="True" />
```

Para obter mais informações sobre o esquema uri ms-appx, consulte [ms-appx e ms-appx-web](/windows/uwp/app-resources/uri-schemes#ms-appx-and-ms-appx-web).

### <a name="play-media-from-the-apps-local-and-temporary-folders"></a>Reproduzir mídia das pastas locais e temporárias do aplicativo

Uma [`MediaElement`](xref:Xamarin.Forms.MediaElement) pode reproduzir arquivos de mídia que são copiados para as `ms-appdata:///` pastas de dados locais ou temporárias do aplicativo, usando o esquema URI.

O exemplo a [`Source`](xref:Xamarin.Forms.MediaElement.Source) seguir mostra a propriedade definida como um arquivo de mídia armazenado na pasta de dados local do aplicativo:

```xaml
<MediaElement Source="ms-appdata:///local/XamarinVideo.mp4"
              ShowsPlaybackControls="True" />
```

O exemplo a [`Source`](xref:Xamarin.Forms.MediaElement.Source) seguir mostra a propriedade para um arquivo de mídia armazenado na pasta de dados temporários do aplicativo:

```xaml
<MediaElement Source="ms-appdata:///temp/XamarinVideo.mp4"
              ShowsPlaybackControls="True" />
```

> [!IMPORTANT]
> Além de reproduzir arquivos de mídia armazenados nas pastas de dados locais ou temporárias do aplicativo, o UWP também pode reproduzir arquivos de mídia que estão localizados na pasta de roaming do aplicativo. Isso pode ser conseguido prefixando `ms-appdata:///roaming/`o arquivo de mídia com .

Ao usar a vinculação de dados, um conversor de valor pode ser usado para aplicar este esquema URI:

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

Uma instância `VideoSourceConverter` do pode então ser `ms-appdata:///` usada para aplicar o esquema URI a um arquivo de mídia na pasta de dados local ou temporária do aplicativo:

```xaml
<MediaElement Source="{Binding MediaSource, Converter={StaticResource VideoSourceConverter}}"
              ShowsPlaybackControls="True" />
```

Para obter mais informações sobre o esquema uri ms-appdata, consulte [ms-appdata](/windows/uwp/app-resources/uri-schemes#ms-appdata).

#### <a name="copying-a-media-file-to-the-apps-local-or-temporary-data-folder"></a>Copiando um arquivo de mídia para a pasta de dados local ou temporária do aplicativo

Reproduzir um arquivo de mídia armazenado na pasta de dados local ou temporário do aplicativo requer que o arquivo de mídia seja copiado pelo aplicativo. Isso pode ser feito, por exemplo, copiando um arquivo de mídia do pacote do aplicativo:

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
> O exemplo de `FileSystem` código acima usa a classe incluída em Xamarin.Essentials. Para obter mais informações, consulte [Xamarin.Essentials: File System Helpers](~/essentials/file-system-helpers.md?context=xamarin%2Fxamarin-forms&tabs=android).

### <a name="play-media-from-the-device-library"></a>Reproduzir mídia da biblioteca de dispositivos

A maioria dos dispositivos móveis e computadores de mesa modernos têm a capacidade de gravar vídeos e áudio usando a câmera e o microfone do dispositivo. As mídias criadas são então armazenadas como arquivos no dispositivo. Esses arquivos podem ser recuperados da biblioteca [`MediaElement`](xref:Xamarin.Forms.MediaElement)e reproduzidos pelo .

Cada uma das plataformas inclui uma facilidade que permite ao usuário selecionar mídia na biblioteca do dispositivo. No Xamarin.Forms, os projetos da plataforma podem invocar essa [`DependencyService`](xref:Xamarin.Forms.DependencyService) funcionalidade, e ela pode ser chamada pela classe.

O serviço de dependência de escolha de vídeo usado no aplicativo de amostra é muito semelhante ao definido em [Escolher uma foto da Biblioteca de imagens,](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md)exceto que o seletor retorna um nome de arquivo em vez de um `Stream` objeto. O projeto de código compartilhado `IVideoPicker`define uma interface nomeada `GetVideoFileAsync`, que define um único método chamado . Cada plataforma então implementa `VideoPicker` essa interface em uma classe.

O exemplo de código a seguir mostra como recuperar um arquivo de mídia da biblioteca do dispositivo:

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

O serviço de dependência de seleção de vídeo é invocado chamando o `DependencyService.Get` método para obter a implementação de uma `IVideoPicker` interface no projeto da plataforma. O `GetVideoFileAsync` método é então chamado nessa instância, e o nome [`FileMediaSource`](xref:Xamarin.Forms.FileMediaSource) de arquivo retornado [`Source`](xref:Xamarin.Forms.MediaElement.Source) é [`MediaElement`](xref:Xamarin.Forms.MediaElement)usado para criar um objeto e defini-lo como propriedade do .

## <a name="change-video-aspect-ratio"></a>Mudar a proporção do vídeo

A [`Aspect`](xref:Xamarin.Forms.MediaElement.Aspect) propriedade determina como a mídia de vídeo será dimensionada para se adequar à área de exibição. Por padrão, esta propriedade `AspectFit` é definida como membro de enumeração, [`Aspect`](xref:Xamarin.Forms.Aspect) mas pode ser definida para qualquer um dos membros de enumeração:

- `AspectFit`indica que o vídeo será em caixa de correio, se necessário, para caber na área de exibição, preservando a proporção.
- `AspectFill`indica que o vídeo será cortado de modo que preencha a área de exibição, preservando a proporção.
- `Fill`indica que o vídeo será estendido para preencher a área de exibição.

## <a name="poll-for-position-data"></a>Enquete para dados de posição

A notificação de [`Position`](xref:Xamarin.Forms.MediaElement.Position) alteração de propriedade para a propriedade vinculável só é acionada em momentos-chave, como início e término da reprodução e pausa. Portanto, os dados `Position` vinculados à propriedade não produzirão dados de posição precisos. Em vez disso, você deve configurar um temporizador e pesquisar a propriedade.

Um bom lugar para fazer `OnAppearing` isso está na substituição da página que requer os dados de posição à medida que a mídia é reproduzida:

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

Neste exemplo, `OnAppearing` a substituição inicia um temporizador que atualiza `positionLabel` com o valor a `Position` cada segundo. O retorno do cronotemizador é invocado `false`a cada segundo, até que o retorno do retorno do retorno . Quando ocorre a `OnDisappearing` navegação da página, a substituição é executada, o que impede que o retorno do crono temporizador seja invocado.

## <a name="understand-mediasource-types"></a>Entenda os tipos de MediaSource

Uma [`MediaElement`](xref:Xamarin.Forms.MediaElement) mídia pode reproduzir [`Source`](xref:Xamarin.Forms.MediaElement.Source) definindo sua propriedade para um arquivo de mídia remoto ou local. A `Source` propriedade é [`MediaSource`](xref:Xamarin.Forms.MediaSource)do tipo , e esta classe define dois métodos estáticos:

- [`FromFile`](xref:Xamarin.Forms.MediaSource.FromFile*), retorna [`MediaSource`](xref:Xamarin.Forms.MediaSource) uma `string` instância de um argumento.
- [`FromUri`](xref:Xamarin.Forms.MediaSource.FromUri*), retorna [`MediaSource`](xref:Xamarin.Forms.MediaSource) uma `Uri` instância de um argumento.

Além disso, [`MediaSource`](xref:Xamarin.Forms.MediaSource) a classe também tem `MediaSource` operadores implícitos que retornam instâncias `string` e `Uri` argumentos.

> [!NOTE]
> Quando [`Source`](xref:Xamarin.Forms.MediaElement.Source) a propriedade é definida em XAML, um [`MediaSource`](xref:Xamarin.Forms.MediaSource) conversor `string` de `Uri`tipo é invocado para retornar uma instância de um ou .

A [`MediaSource`](xref:Xamarin.Forms.MediaSource) classe também tem duas classes derivadas:

- [`UriMediaSource`](xref:Xamarin.Forms.UriMediaSource), que é usado para especificar um arquivo de mídia remota de um URI. Esta classe [`Uri`](xref:Xamarin.Forms.UriMediaSource.Uri) tem uma propriedade que `Uri`pode ser definida como um .
- [`FileMediaSource`](xref:Xamarin.Forms.FileMediaSource), que é usado para especificar `string`um arquivo de mídia local a partir de um . Esta classe [`File`](xref:Xamarin.Forms.FileMediaSource.File) tem uma propriedade que `string`pode ser definida como um . Além disso, esta classe tem operadores `FileMediaSource` implícitos `FileMediaSource` para converter `string`um a um `string` objeto, e um objeto para um .

> [!NOTE]
> Quando [`FileMediaSource`](xref:Xamarin.Forms.FileMediaSource) um objeto é criado em XAML, um conversor de tipo é invocado para retornar uma [`FileMediaSource`](xref:Xamarin.Forms.FileMediaSource) instância de um `string`.

## <a name="determine-mediaelement-status"></a>Determine o status do MediaElement

A [`MediaElement`](xref:Xamarin.Forms.MediaElement) classe define uma propriedade vinculável [`CurrentState`](xref:Xamarin.Forms.MediaElement.CurrentState)somente [`MediaElementState`](xref:Xamarin.Forms.MediaElementState)leitura chamada , do tipo . Esta propriedade indica o status atual do controle, como se a mídia está reproduzindo ou pausada, ou se ainda não está pronta para reproduzir a mídia.

A [`MediaElementState`](xref:Xamarin.Forms.MediaElementState) enumeração define os seguintes membros:

- `Closed`indica que `MediaElement` o não contém mídia.
- `Opening`indica que `MediaElement` o está validando e tentando carregar a fonte especificada.
- `Buffering`indica que `MediaElement` o está carregando a mídia para reprodução. Sua [`Position`](xref:Xamarin.Forms.MediaElement.Position) propriedade não avança durante este estado. Se `MediaElement` o vídeo estava reproduzindo, ele continua a exibir o último quadro exibido.
- `Playing`indica que `MediaElement` o está reproduzindo a fonte de mídia.
- `Paused`indica que `MediaElement` o não [`Position`](xref:Xamarin.Forms.MediaElement.Position) avança sua propriedade. Se `MediaElement` o vídeo estava reproduzindo, ele continua a exibir o quadro atual.
- `Stopped`indica que `MediaElement` a mídia contém, mas não está sendo reproduzida ou pausada. Sua [`Position`](xref:Xamarin.Forms.MediaElement.Position) propriedade é 0 e não avança. Se a mídia carregada for `MediaElement` vídeo, o quadro será exibido.

Geralmente não é necessário examinar [`CurrentState`](xref:Xamarin.Forms.MediaElement.CurrentState) a propriedade [`MediaElement`](xref:Xamarin.Forms.MediaElement) ao usar os controles de transporte. No entanto, essa propriedade torna-se importante ao implementar seus próprios controles de transporte.

## <a name="implement-custom-transport-controls"></a>Implementar controles de transporte personalizados

Os controles de transporte de um media player incluem os botões que executam as funções **Play**, **Pause**e **Stop**. Em geral, esses botões são identificados com ícones conhecidos, em vez de um texto, e as funções **Reproduzir** e **Pausar** geralmente são combinadas em um botão.

Por padrão, [`MediaElement`](xref:Xamarin.Forms.MediaElement) os controles de reprodução estão desativados. Isso permite que `MediaElement` você controle a programação ou fornecendo seus próprios controles de transporte. Em apoio a `MediaElement` isso, [`Pause`](xref:Xamarin.Forms.MediaElement.Pause)inclui [`Stop`](xref:Xamarin.Forms.MediaElement.Stop) [`Play`](xref:Xamarin.Forms.MediaElement.Play), e métodos.

O exemplo XAML a seguir [`MediaElement`](xref:Xamarin.Forms.MediaElement) mostra uma página que contém controles de transporte personalizados e a seguir:

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

Neste exemplo, os controles de transporte [`Button`](xref:Xamarin.Forms.Button) personalizados são definidos como objetos. No entanto, `Button` há apenas dois `Button` objetos, com o `Button` primeiro representando **Play** and **Pause**, e o segundo representando **Stop**. [`DataTrigger`](xref:Xamarin.Forms.DataTrigger)objetos são usados para ativar e desativar os botões e para alternar o primeiro botão entre **Reproduzir** e **Pausar**. Para obter mais informações sobre gatilhos de dados, consulte [Xamarin.Forms Triggers](~/xamarin-forms/app-fundamentals/triggers.md).

O arquivo de código atrás tem [`Clicked`](xref:Xamarin.Forms.Button.Clicked) os manipuladores para os eventos:

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

O botão **Play** pode ser pressionado, uma vez ativado, para iniciar a reprodução:

[![Captura de tela de um MediaElement com controles de transporte personalizados, no iOS e Android](mediaelement-images/custom-transport-playback.png "MediaElement reproduzindo um vídeo")](mediaelement-images/custom-transport-playback-large.png#lightbox "MediaElement reproduzindo um vídeo")

Pressionar o botão **Pausar** resulta em pausa de reprodução:

[![Captura de tela de um MediaElement com reprodução pausada, no iOS e Android](mediaelement-images/custom-transport-paused.png "MediaElement com um vídeo pausado")](mediaelement-images/custom-transport-paused-large.png#lightbox "MediaElement com um vídeo pausado")

Pressionar o botão **Parar** interrompe a reprodução e retorna a posição do arquivo de mídia para o início.

## <a name="implement-a-custom-position-bar"></a>Implementar uma barra de posição personalizada

Os controles de transporte implementados por cada plataforma incluem uma barra de posição. Esta barra se assemelha a um controle deslizante e mostra a localização atual da mídia dentro de sua duração total. Além disso, você pode manipular a barra de posição para mover para frente ou para trás para uma nova posição no vídeo.

A implementação de uma barra de posição personalizada requer saber a duração da mídia e a posição de reprodução atual. Esses dados estão [`Duration`](xref:Xamarin.Forms.MediaElement.Duration) disponíveis [`Position`](xref:Xamarin.Forms.MediaElement.Position) nas propriedades.

> [!IMPORTANT]
> Deve [`Position`](xref:Xamarin.Forms.MediaElement.Position) ser pesquisado para obter dados de posição precisos. Para obter mais informações, consulte [Pesquisa para dados de posição](#poll-for-position-data).

Uma barra de posição personalizada [`Slider`](xref:Xamarin.Forms.Slider)pode ser implementada usando a, como mostrado no exemplo a seguir:

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

A `PositionSlider` classe define `Duration` suas `Position` propriedades próprias `TimeToEnd` e vinculáveis, e uma propriedade vinculável. Todas as três `TimeSpan`propriedades são do tipo . O manipulador alterado de `Duration` propriedade `Maximum` para a [`Slider`](xref:Xamarin.Forms.Slider) propriedade `TotalSeconds` define `TimeSpan` a propriedade do para a propriedade do valor. A `TimeToEnd` propriedade é calculada com `Duration` `Position` base em alterações nas propriedades e começa na duração da mídia e diminui para zero à medida que a reprodução prossegue.

O `PositionSlider` é atualizado a [`Slider`](xref:Xamarin.Forms.Slider) partir `Slider` do subjacente quando o é movido para indicar que a mídia deve ser avançada ou revertida para uma nova posição. Isso é detectado `PropertyChanged` no manipulador `PositionSlider` da construtora. O manipulador verifica uma alteração na propriedade `Value` e, se for diferente da propriedade `Position`, então a propriedade `Position` será definida com base na propriedade `Value`. Para obter mais [`Slider`](xref:Xamarin.Forms.Slider) informações sobre como usar um controle de voz, [Xamarin.Forms Slider](~/xamarin-forms/user-interface/slider.md)

> [!NOTE]
> No Android, [`Slider`](xref:Xamarin.Forms.Slider) o único tem 1000 passos `Minimum` `Maximum` discretos, independentemente das configurações. Se o comprimento da mídia for superior a `Position` 1000 segundos, então dois valores diferentes corresponderiam ao mesmo `Value` do `Slider`. É por isso que o código acima verifica se a nova posição e a posição existentes são maiores que um centésimo da duração total.

O exemplo a `PositionSlider` seguir mostra o ser consumido em uma página:

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

Neste exemplo, `Duration` a propriedade `PositionSlider` do está vinculado [`Duration`](xref:Xamarin.Forms.MediaElement.Duration) aos [`MediaElement`](xref:Xamarin.Forms.MediaElement)dados da propriedade do . Quando [`Value`](xref:Xamarin.Forms.Slider.Value) a propriedade [`Slider`](xref:Xamarin.Forms.Slider) das `ValueChanged` alterações, `OnPositionSliderValueChanged` o evento é acionado e o manipulador é executado. Este manipulador [`MediaElement.Position`](xref:Xamarin.Forms.MediaElement.Position) define a propriedade `PositionSlider.Position` para o valor da propriedade. Portanto, arrastando `Slider` os resultados na posição de reprodução de mídia mudando:

[![Captura de tela de um MediaElement com uma barra de posição personalizada, no iOS e Android](mediaelement-images/custom-position-bar.png "MediaElement com uma barra de posição personalizada")](mediaelement-images/custom-position-bar-large.png#lightbox "MediaElement com uma barra de posição personalizada")

Além disso, [`DataTrigger`](xref:Xamarin.Forms.DataTrigger) um objeto é `PositionSlider` usado para desativar quando a mídia está buffering. Para obter mais informações sobre gatilhos de dados, consulte [Xamarin.Forms Triggers](~/xamarin-forms/app-fundamentals/triggers.md).

## <a name="implement-a-custom-volume-control"></a>Implementar um controle de volume personalizado

Os controles de reprodução de mídia implementados por cada plataforma incluem uma barra de volume. Esta barra se assemelha a um controle deslizante e mostra o volume da mídia. Além disso, você pode manipular a barra de volume para aumentar ou diminuir o volume.

Uma barra de volume personalizada [`Slider`](xref:Xamarin.Forms.Slider)pode ser implementada usando a, como mostrado no exemplo a seguir:

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

Neste exemplo, [`Slider`](xref:Xamarin.Forms.Slider) os dados `Value` vinculam [`Volume`](xref:Xamarin.Forms.MediaElement.Volume) sua propriedade [`MediaElement`](xref:Xamarin.Forms.MediaElement)à propriedade do . Isso é possível `Volume` porque `TwoWay` a propriedade usa uma vinculação. Portanto, a `Value` mudança do imóvel `Volume` resultará na mudança do imóvel.

> [!NOTE]
> A [`Volume`](xref:Xamarin.Forms.MediaElement.Volume) propriedade possui um retorno de chamada de vlidação que garante que seu valor seja maior ou igual a 0,0, e menor ou igual a 1,0.

Para obter mais [`Slider`](xref:Xamarin.Forms.Slider) informações sobre como usar um controle de voz, [Xamarin.Forms Slider](~/xamarin-forms/user-interface/slider.md)

## <a name="related-links"></a>Links relacionados

- [MediaElementDemos (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-mediaelementdemos/)
- [Esquemas de URI](/windows/uwp/app-resources/uri-schemes)
- [Gatilhos do Xamarin.Forms](~/xamarin-forms/app-fundamentals/triggers.md)
- [Xamarin.Forms Slider](~/xamarin-forms/user-interface/slider.md)
- [Android: Formatos de mídia suportados](https://developer.android.com/guide/topics/media/media-formats)
- [UWP: Codecs suportados](/windows/uwp/audio-video-camera/supported-codecs)
