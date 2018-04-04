---
title: Fontes de vídeo de associação para o player
ms.prod: xamarin
ms.assetid: 504E0C7E-051A-4AF2-B654-BAB4D0957928
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: bebf6fd905dd374822eb6974b28f1ac92a36c1bc
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="binding-video-sources-to-the-player"></a>Fontes de vídeo de associação para o player

Quando o `Source` propriedade o `VideoPlayer` exibição é definida como um novo arquivo de vídeo, o vídeo existente execução é interrompida e o novo vídeo começa. Isso é demonstrado pelo **selecione Web vídeo** página do [ **VideoPlayerDemos** ](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/) exemplo. A página inclui um `ListView` com os títulos dos três vídeos referenciados a partir de **App** arquivo:

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

Quando um vídeo é selecionado, o `ItemSelected` manipulador de eventos no arquivo code-behind é executado. O manipulador de remove quaisquer espaços em branco e apóstrofos o título e a usa como uma chave para obter um dos recursos definidos no **App** arquivo. Que `UriVideoSource` objeto é definido como o `Source` propriedade o `VideoPlayer`.

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

Quando a página for carregada pela primeira vez, nenhum item é selecionado no `ListView`, portanto, você deve selecionar uma para o vídeo seja reproduzido:

[![Selecione Web vídeo](source-bindings-images/selectwebvideo-small.png "selecione Web vídeo")](source-bindings-images/selectwebvideo-large.png#lightbox "selecione vídeo de Web")

O `Source` propriedade `VideoPlayer` é apoiado por uma propriedade ligável, o que significa que ele pode ser o destino de uma associação de dados. Isso é demonstrado pelo **associar VideoPlayer** página. A marcação no **BindToVideoPlayer.xaml** a seguinte classe que encapsula um título de um vídeo e correspondente oferece suporte ao arquivo `VideoSource` objeto:

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

O `ListView` no **BindToVideoPlayer.xaml** arquivo contém uma matriz desses `VideoInfo` objetos, cada um inicializado com um título de vídeo e o `UriVideoSource` objeto de dicionário de recursos em  **App**:

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

O `Source` propriedade o `VideoPlayer` está associada ao `ListView`. O `Path` da associação é especificado como `SelectedItem.VideoSource`, que é um caminho composto consiste em duas propriedades: `SelectedItem` é uma propriedade de `ListView`. O item selecionado é do tipo `VideoInfo`, que tem um `VideoSource` propriedade.

Assim como acontece com a primeira **selecione Web vídeo** página, nenhum item inicialmente é selecionado do `ListView`, portanto, você precisa selecionar um dos vídeos antes de começar a execução.


## <a name="related-links"></a>Links relacionados

- [Demonstrações em vídeo Player (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/)
