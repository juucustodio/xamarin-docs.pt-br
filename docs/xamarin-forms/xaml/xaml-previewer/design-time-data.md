---
title: Usar dados de tempo de design com o previsor XAML
description: Este artigo explica como usar dados de tempo de design para mostrar layouts com dados pesados no visualizador XAML sem executar seu aplicativo.
ms.prod: xamarin
ms.assetid: 0F608019-5951-4BE6-80E0-9EEE1733D642
ms.technology: xamarin-forms
author: maddyleger1
ms.author: maleger
ms.date: 03/27/2019
ms.openlocfilehash: a6a34615adc9cf290ff6bf9dd344487e5f29cfa2
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69887848"
---
# <a name="use-design-time-data-with-the-xaml-previewer"></a>Usar dados de tempo de design com o previsor XAML

_Alguns layouts são difíceis de Visualizar sem dados. Use essas dicas para tirar o máximo proveito da visualização de páginas pesadas de dados no visualizador XAML._

## <a name="design-time-data-basics"></a>Noções básicas de dados de tempo de design

Os dados de tempo de design são dados falsos que você define para facilitar a visualização de seus controles no visualizador XAML. Para começar, adicione as seguintes linhas de código ao cabeçalho da sua página XAML:

```xaml
xmlns:d="http://xamarin.com/schemas/2014/forms/design"
xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
mc:Ignorable="d"
```

Depois de adicionar os namespaces, você pode `d:` colocar na frente de qualquer atributo ou controle para mostrá-lo no previsor XAML. Elementos com `d:` não são mostrados em tempo de execução.

Por exemplo, você pode adicionar texto a um rótulo que geralmente tem dados associados a ele.

```xaml
<Label Text="{Binding Name}" d:Text="Name!" />
```

[![Dados de tempo de design com texto em um rótulo](xaml-previewer-images/designtimedata-label-sm.png "Dados de tempo de design com texto um rótulo")](xaml-previewer-images/designtimedata-label-lg.png#lightbox)

Neste exemplo, sem `d:Text`, o visualizador XAML não mostraria nada para o rótulo. Em vez disso, ele mostra "Name!" onde o rótulo terá dados reais em tempo de execução.

Você pode usar `d:` com qualquer atributo para um controle Xamarin. Forms, como cores, tamanhos de fonte e espaçamento. Você pode até mesmo adicioná-lo ao próprio controle:

```xaml
<d:Button Text="Design Time Button" />
```

[![Dados de tempo de design com um controle de botão](xaml-previewer-images/designtimedata-controls-sm.png "Dados de tempo de design com um controle de botão")](xaml-previewer-images/designtimedata-controls-lg.png#lightbox)

Neste exemplo, o botão só aparece em tempo de design. Use este método para colocar um espaço reservado no para um [controle personalizado sem suporte pelo Visualizador XAML](render-custom-controls.md).

## <a name="preview-images-at-design-time"></a>Visualizar imagens em tempo de design

Você pode definir uma fonte de tempo de design para imagens vinculadas à página ou carregadas dinamicamente. Em seu projeto Android, adicione a imagem que você deseja mostrar no previsor XAML para os **recursos >** pasta desenhável. Em seu projeto do iOS, adicione a imagem à pasta **recursos** . Em seguida, você pode mostrar essa imagem no visualizador XAML em tempo de design:

```xaml
<Image Source={Binding ProfilePicture} d:Source="DesignTimePicture.jpg" />
```

[![Dados de tempo de design com imagens](xaml-previewer-images/designtimedata-image-sm.png "Dados de tempo de design com iamges")](xaml-previewer-images/designtimedata-image-lg.png#lightbox)

## <a name="design-time-data-for-listviews"></a>Dados de tempo de design para ListViews

ListViews são uma maneira popular de exibir dados em um aplicativo móvel. No entanto, é difícil Visualizar sem dados reais. Para usar dados de tempo de design com eles, você precisa criar uma matriz de tempo de design para usar como uma ItemsSource. O visualização de XAML exibe o que está nessa matriz em seu ListView em tempo de design.

```xaml
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

[![Dados de tempo de design com um ListView](xaml-previewer-images/designtimedata-itemssource-sm.png "Dados de tempo de design com um ListView")](xaml-previewer-images/designtimedata-itemssource-lg.png#lightbox)

Este exemplo mostrará um ListView de três textcéls no previsor XAML. Você pode alterar `x:String` para um modelo de dados existente em seu projeto.

Consulte o [aplicativo Hanselman. Forms de James Montemagno](https://github.com/jamesmontemagno/Hanselman.Forms/blob/vnext/src/Hanselman/Views/Podcasts/PodcastDetailsPage.xaml#L26-L47) para obter um exemplo mais complexo.

## <a name="alternative-hardcode-a-static-viewmodel"></a>Opcional Codificar um ViewModel estático

Se você não quiser adicionar dados de tempo de design a controles individuais, poderá configurar um armazenamento de dados fictício para associar à sua página. Consulte a postagem de blog de James Montemagno [sobre como adicionar dados de tempo de design](http://motzcod.es/post/143702671962/xamarinforms-xaml-previewer-design-time-data) para ver como associar a um ViewModel estático em XAML.

## <a name="troubleshooting"></a>Solução de problemas

### <a name="requirements"></a>Requisitos

Os dados de tempo de design exigem uma versão mínima do Xamarin. Forms 3,6.

### <a name="intellisense-shows-squiggly-lines-under-my-design-time-data"></a>O IntelliSense mostra linhas onduladas sob meus dados de tempo de design

Esse é um problema conhecido e será corrigido em uma versão futura do Visual Studio. O projeto ainda será compilado sem erros.

### <a name="the-xaml-previewer-stopped-working"></a>O previsor XAML parou de funcionar

Tente fechar e reabrir o arquivo XAML e limpar e recompilar seu projeto.
