---
title: Usar dados de tempo de Design com o pré-visualizador XAML
description: Este artigo explica como usar dados de tempo de design para mostrar os layouts com uso intenso de dados no XAML pré-visualizador sem executar seu aplicativo.
ms.prod: xamarin
ms.assetid: 0F608019-5951-4BE6-80E0-9EEE1733D642
ms.technology: xamarin-forms
author: maddyleger1
ms.author: maleger
ms.date: 03/27/2019
ms.openlocfilehash: 0ff9f8b5ee6f9468650b6535745706bee8f96536
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60876304"
---
# <a name="use-design-time-data-with-the-xaml-previewer"></a>Usar dados de tempo de Design com o pré-visualizador XAML

_Alguns layouts são difíceis de visualizar sem dados. Use estas dicas para fazer o máximo proveito de visualização de suas páginas com uso intenso de dados no Visualizador de XAML._

## <a name="design-time-data-basics"></a>Noções básicas de dados de tempo de design

Os dados são dados falsos que você definir para tornar seus controles mais fáceis de visualizar no pré-visualizador XAML do tempo de design. Para começar, adicione as seguintes linhas de código para o cabeçalho da página XAML:

```csharp
xmlns:d="http://xamarin.com/schemas/2014/forms/design"
xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
mc:Ignorable="d"
```

Depois de adicionar os namespaces, você pode colocar `d:` na frente de qualquer atributo ou o controle para mostrá-lo no Visualizador de XAML. Elementos com `d:` não são mostrados em tempo de execução.

Por exemplo, você pode adicionar texto a um rótulo que geralmente tem dados associados a ele.

```csharp
<Label Text={Binding Name} d:Text="Name" />
```

[![Dados de tempo com o texto em um rótulo de design](xaml-previewer-images/designtimedata-label-sm.png "Design um rótulo de dados com o texto de hora")](xaml-previewer-images/designtimedata-label-lg.png#lightbox)

 Neste exemplo, sem `d:Text`, pré-visualizador XAML mostraria nada para o rótulo. Em vez disso, ele mostra "Name" no qual o rótulo terá dados reais no tempo de execução.

Você pode usar `d:` com qualquer atributo para um controle xamarin. Forms, como cores, tamanhos de fonte e espaçamento. Você pode até mesmo adicioná-lo ao próprio controle:

```csharp
<d:Button Text="Design Time Button" />
```

[![Criar dados de tempo com um controle de botão](xaml-previewer-images/designtimedata-controls-sm.png "dados com um controle de botão de tempo de Design")](xaml-previewer-images/designtimedata-controls-lg.png#lightbox)

Neste exemplo, o botão aparece apenas em tempo de design. Use esse método para colocar um espaço reservado para um [controle personalizado não compatível com o pré-visualizador XAML](render-custom-controls.md).

## <a name="preview-images-at-design-time"></a>Imagens de visualização em tempo de design

Você pode definir um fonte de tempo de design para imagens que está vinculada à página ou carregado na dinamicamente. Em seu projeto Android, adicione a imagem que você deseja mostrar no pré-visualizador XAML para o **recursos > Drawable** pasta. Em seu projeto do iOS, adicione a imagem para o **recursos** pasta. Em seguida, você pode mostrar essa imagem no pré-visualizador XAML em tempo de design:

```csharp
<Image Source={Binding ProfilePicture} d:Source="DesignTimePicture.jpg" />
```
[![Criar dados de tempo com imagens](xaml-previewer-images/designtimedata-image-sm.png "projetar os dados de tempo com iamges")](xaml-previewer-images/designtimedata-image-lg.png#lightbox)

## <a name="design-time-data-for-listviews"></a>Dados de tempo de design para ListViews

ListViews são uma maneira popular para exibir dados em um aplicativo móvel. No entanto, eles são difíceis de visualizar sem dados reais. Para usar dados de tempo de design com eles, você precisará criar uma matriz de tempo de design para usar como um ItemsSource. O pré-visualizador de XAML exibe o que está na matriz na sua ListView em tempo de design.

```csharp
<StackLayout>
    <ListView ItemsSource="{Binding Items}">
        <d:ListView.ItemsSource>
            <x:Array Type="{x:Type x:String}">
                <x:String>Item One</x:String>
                <x:String>Item Two</x:String>
                <x:String>Item Three</x:String>
            </x:Array>
        </d:ListView.ItemsSource>
        <ListView.ItemTemplate>
            <DataTemplate>
                <TextCell Text="{Binding ItemName}"
                          d:Text="{Binding .}" />
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
</StackLayout>
```

[![Criar dados de tempo com um ListView](xaml-previewer-images/designtimedata-itemssource-sm.png "projetar os dados de tempo com um ListView")](xaml-previewer-images/designtimedata-itemssource-lg.png#lightbox)

Este exemplo mostrará um ListView de três TextCells no pré-visualizador XAML. Você pode alterar `x:String` para um modelo de dados existente em seu projeto.

Consulte a [aplicativo de Hanselman.Forms de James Montemagno](https://github.com/jamesmontemagno/Hanselman.Forms/blob/vnext/src/Hanselman/Views/Podcasts/PodcastDetailsPage.xaml#L36-L57) para obter um exemplo mais complexo.


## <a name="alternative-hardcode-a-static-viewmodel"></a>Alternativa: Codificar um ViewModel estático

Se você não quiser adicionar dados de tempo de design para controles individuais, você pode configurar um repositório de dados fictícios para associar à sua página. Consulte de James Montemagno [postagem de blog sobre como adicionar dados de tempo de design](http://motzcod.es/post/143702671962/xamarinforms-xaml-previewer-design-time-data) para ver como associar a um ViewModel estático em XAML.

## <a name="troubleshooting"></a>Solução de problemas

### <a name="requirements"></a>Requisitos

Dados de tempo de design requer uma versão mínima do xamarin. Forms 3.6.

### <a name="intellisense-shows-squiggly-lines-under-my-design-time-data"></a>O IntelliSense mostra linhas onduladas sob os meus dados de tempo de design

Isso é um problema conhecido e será corrigido em uma versão futura do Visual Studio. O projeto ainda será compilado sem erros.

### <a name="the-xaml-previewer-stopped-working"></a>O pré-visualizador XAML parou de funcionar

Tente fechar e reabrir o arquivo XAML e a limpeza e recriar o projeto.
