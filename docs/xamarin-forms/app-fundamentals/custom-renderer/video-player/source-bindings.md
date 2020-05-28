---
title: ''
description: Este artigo explica como associar fontes de vídeo ao Player de vídeo usando o Xamarin.Forms .
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c02764d6160bb8ce2481a3cf67e357933d25934f
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84133725"
---
# <a name="binding-video-sources-to-the-player"></a>Associando fontes de vídeo ao player

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)

Quando a propriedade `Source` da exibição `VideoPlayer` for definida como um novo arquivo de vídeo, o vídeo existente interromperá a execução e o novo vídeo será iniciado. Isso é demonstrado pela página **Selecionar vídeo da Web** do exemplo [**VideoPlayerDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos). A página inclui um `ListView` com os títulos dos três vídeos referenciados no arquivo **App.xaml**:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             x:Class="VideoPlayerDemos.SelectWebVideoPage"
             Title="Select Web Video">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="2*" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <video:VideoPlayer x:Name="videoPlayer"
                           Grid.Row="0" />

        <ListView Grid.Row="1"
                  ItemSelected="OnListViewItemSelected">
            <ListView.ItemsSource>
                <x:Array Type="{x:Type x:String}">
                    <x:String>Elephant's Dream</x:String>
                    <x:String>Big Buck Bunny</x:String>
                    <x:String>Sintel</x:String>
                </x:Array>
            </ListView.ItemsSource>
        </ListView>
    </Grid>
</ContentPage>
```

Quando um vídeo for selecionado, o manipulador de eventos `ItemSelected` no arquivo code-behind será executado. O manipulador remove quaisquer espaços em branco e apóstrofos do título e usa isso como uma chave para obter um dos recursos definidos no arquivo **App.xaml**. Esse objeto `UriVideoSource` é definido como a propriedade `Source` do `VideoPlayer`.

```csharp
namespace VideoPlayerDemos
{
    public partial class SelectWebVideoPage : ContentPage
    {
        public SelectWebVideoPage()
        {
            InitializeComponent();
        }

        void OnListViewItemSelected(object sender, SelectedItemChangedEventArgs args)
        {
            if (args.SelectedItem != null)
            {
                string key = ((string)args.SelectedItem).Replace(" ", "").Replace("'", "");
                videoPlayer.Source = (UriVideoSource)Application.Current.Resources[key];
            }
        }
    }
}
```

Quando a página é carregada pela primeira vez, nenhum item é selecionado no `ListView`; portanto, é necessário selecionar um para que o vídeo comece a ser reproduzido:

[![Selecionar vídeo da Web](source-bindings-images/selectwebvideo-small.png "Selecionar vídeo da Web")](source-bindings-images/selectwebvideo-large.png#lightbox "Selecionar vídeo da Web")

A propriedade `Source` de `VideoPlayer` é apoiada por uma propriedade associável, o que significa que ela pode ser o destino de uma associação de dados. Isso é demonstrado pela página **Associar a VideoPlayer**. A marcação no arquivo **BindToVideoPlayer.xaml** é compatível com a seguinte classe que encapsula um título de um vídeo e um objeto `VideoSource` correspondente:

```csharp
namespace VideoPlayerDemos
{
    public class VideoInfo
    {
        public string DisplayName { set; get; }

        public VideoSource VideoSource { set; get; }

        public override string ToString()
        {
            return DisplayName;
        }
    }
}
```

O `ListView` no arquivo **BindToVideoPlayer.xaml** contém uma matriz desses objetos `VideoInfo`, cada uma inicializada com um título do vídeo e o objeto `UriVideoSource` do dicionário de recursos em **App.xaml**:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:VideoPlayerDemos"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             x:Class="VideoPlayerDemos.BindToVideoPlayerPage"
             Title="Bind to VideoPlayer">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="2*" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <video:VideoPlayer x:Name="videoPlayer"
                           Grid.Row="0"
                           Source="{Binding Source={x:Reference listView},
                                            Path=SelectedItem.VideoSource}" />

        <ListView x:Name="listView"
                  Grid.Row="1">
            <ListView.ItemsSource>
                <x:Array Type="{x:Type local:VideoInfo}">
                    <local:VideoInfo DisplayName="Elephant's Dream"
                                     VideoSource="{StaticResource ElephantsDream}" />

                    <local:VideoInfo DisplayName="Big Buck Bunny"
                                     VideoSource="{StaticResource BigBuckBunny}" />

                    <local:VideoInfo DisplayName="Sintel"
                                     VideoSource="{StaticResource Sintel}" />
                </x:Array>
            </ListView.ItemsSource>
        </ListView>
    </Grid>
</ContentPage>
```

A propriedade `Source` do `VideoPlayer` está associada ao `ListView`. O `Path` da associação é especificado como `SelectedItem.VideoSource`, que é um demarcador composto que consiste em duas propriedades: `SelectedItem` é uma propriedade de `ListView`. O item selecionado é do tipo `VideoInfo`, que tem uma propriedade `VideoSource`.

Assim como acontece com a primeira página **Selecionar vídeo da Web**, nenhum item é inicialmente selecionado no `ListView`; portanto, é necessário selecionar um dos vídeos antes de começar a reproduzi-lo.

## <a name="related-links"></a>Links relacionados

- [Demonstrações do player de vídeo (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)
