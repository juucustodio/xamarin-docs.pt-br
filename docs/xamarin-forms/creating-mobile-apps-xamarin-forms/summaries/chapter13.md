---
title: Resumo do capítulo 13. Bitmaps
description: 'Criação de aplicativos móveis com xamarin. Forms: Resumo do capítulo 13. Bitmaps'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 5D153857-B6B7-4A14-8FB9-067DE198C2C7
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: e4746ed94a008d382ce15bb9cd7c52365d9ba574
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2020
ms.locfileid: "78291547"
---
# <a name="summary-of-chapter-13-bitmaps"></a>Resumo do capítulo 13. Bitmaps

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13)

> [!NOTE]
> Observações sobre essa página indicam áreas onde o xamarin. Forms foi bifurcado do material apresentado no livro.

O elemento [`Image`](xref:Xamarin.Forms.Image) Xamarin. Forms exibe um bitmap. Todas as plataformas do xamarin. Forms dão suporte os formatos de arquivo JPEG, PNG, GIF e BMP.

Bitmaps no xamarin. Forms é proveniente de quatro lugares:

- Por meio da web conforme especificado por uma URL
- Incorporado como um recurso na biblioteca compartilhada
- Incorporado como um recurso nos projetos de aplicativo da plataforma
- De qualquer lugar que possa ser referenciado por um objeto .NET `Stream`, incluindo `MemoryStream`

Recursos de bitmap na biblioteca compartilhada são independentes de plataforma, enquanto os recursos de bitmap em projetos de plataforma são específicos da plataforma.

> [!NOTE]
> O texto do livro faz referência a bibliotecas de classes portáteis, foram substituídos por bibliotecas .NET Standard. O código de exemplo do livro foi convertido para usar bibliotecas padrão do .NET.

O bitmap é especificado definindo a propriedade [`Source`](xref:Xamarin.Forms.Image.Source) de `Image` como um objeto do tipo [`ImageSource`](xref:Xamarin.Forms.ImageSource), uma classe abstrata com três derivativos:

- [`UriImageSource`](xref:Xamarin.Forms.UriImageSource) para acessar um bitmap na Web com base em um objeto de `Uri` definido como sua propriedade [`Uri`](xref:Xamarin.Forms.UriImageSource.Uri)
- [`FileImageSource`](xref:Xamarin.Forms.FileImageSource) para acessar um bitmap armazenado em um projeto de aplicativo da plataforma com base em uma pasta e um caminho de arquivo definidos para sua propriedade [`File`](xref:Xamarin.Forms.FileImageSource.File)
- [`StreamImageSource`](xref:Xamarin.Forms.StreamImageSource) para carregar um bitmap usando um objeto .net `Stream` especificado retornando um `Stream` de um conjunto de `Func` para sua propriedade [`Stream`](xref:Xamarin.Forms.StreamImageSource.Stream)

Como alternativa (e mais comumente), você pode usar os seguintes métodos estáticos da classe `ImageSource`, todos os quais retornam `ImageSource` objetos:

- [`ImageSource.FromUri`](xref:Xamarin.Forms.ImageSource.FromUri(System.Uri)) para acessar um bitmap na Web com base em um objeto `Uri`
- [`ImageSource.FromResource`](xref:Xamarin.Forms.ImageSource.FromResource*) para acessar um bitmap armazenado como um recurso incorporado no aplicativo PCL; [`ImageSource.FromResource`](xref:Xamarin.Forms.ImageSource.FromResource(System.String,System.Type)) ou [`ImageSource.FromResource`](xref:Xamarin.Forms.ImageSource.FromResource(System.String,System.Reflection.Assembly)) para acessar um bitmap em outro assembly de origem
- [`ImageSource.FromFile`](xref:Xamarin.Forms.ImageSource.FromFile(System.String)) para acessar um bitmap de um projeto de aplicativo de plataforma
- [`ImageSource.FromStream`](xref:Xamarin.Forms.ImageSource.FromStream(System.Func{System.IO.Stream})) para carregar um bitmap com base em um objeto `Stream`

Não há nenhum equivalente de classe dos métodos `Image.FromResource`. A classe `UriImageSource` será útil se você precisar controlar o cache. A classe `FileImageSource` é útil em XAML. `StreamImageSource` é útil para o carregamento assíncrono de objetos `Stream`, enquanto `ImageSource.FromStream` é síncrono.

## <a name="platform-independent-bitmaps"></a>Bitmaps independente de plataforma

O projeto [**WebBitmapCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/WebBitmapCode) carrega um bitmap na web usando `ImageSource.FromUri`. O elemento `Image` é definido como a propriedade `Content` da `ContentPage`, portanto, é restrito ao tamanho da página. Independentemente do tamanho do bitmap, um elemento de `Image` restrito é ampliado para o tamanho de seu contêiner e o bitmap é exibido em seu tamanho máximo dentro do elemento `Image` enquanto mantém a taxa de proporção do bitmap. As áreas do `Image` além do bitmap podem ser coloridas com [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor).

O exemplo [**WebBitmapXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/WebBitmapXaml) é semelhante, mas simplesmente define a propriedade `Source` como a URL. A conversão é manipulada pela classe [`ImageSourceConverter`](xref:Xamarin.Forms.ImageSourceConverter) .

### <a name="fit-and-fill"></a>Ajuste e preenchimento

Você pode controlar como o bitmap é ampliado definindo a propriedade [`Aspect`](xref:Xamarin.Forms.Image.Aspect) do `Image` como um dos seguintes membros da enumeração [`Aspect`](xref:Xamarin.Forms.Aspect) :

- [`AspectFit`](xref:Xamarin.Forms.Aspect.AspectFit): taxa de proporção de respeitar (padrão)
- [`Fill`](xref:Xamarin.Forms.Aspect.Fill): área de preenchimentos, não respeita a taxa de proporção
- [`AspectFill`](xref:Xamarin.Forms.Aspect.AspectFill): preenche a área, mas respeita a taxa de proporção, realizada pela parte de corte do bitmap

### <a name="embedded-resources"></a>Recursos inseridos

Você pode adicionar um arquivo de bitmap para uma PCL ou para uma pasta na PCL. Dê a ele uma **ação de Build** de **EmbeddedResource**. O exemplo [**ResourceBitmapCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ResourceBitmapCode) demonstra como usar `ImageSource.FromResource` para carregar o arquivo. O nome do recurso passado para o método consiste o nome do assembly, seguido por um ponto, seguido pelo nome da pasta opcional e um ponto, seguido pelo nome de arquivo.

O programa define as propriedades `VerticalOptions` e `HorizontalOptions` do `Image` como `LayoutOptions.Center`, o que torna o elemento `Image` irrestrito. O `Image` e o bitmap têm o mesmo tamanho:

- No iOS e no Android, o `Image` é o tamanho do pixel do bitmap. Há um mapeamento individual entre pixels de bitmap e tela.
- Em Plataforma Universal do Windows, a `Image` é o tamanho do pixel do bitmap em unidades independentes do dispositivo. Na maioria dos dispositivos, cada pixel de bitmap ocupa várias pixels da tela.

O exemplo [**StackedBitmap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/StackedBitmap) coloca uma `Image` em uma `StackLayout` vertical em XAML. Uma extensão de marcação chamada [`ImageResourceExtension`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter13/StackedBitmap/StackedBitmap/StackedBitmap/ImageResourceExtension.cs) ajuda a referenciar o recurso inserido em XAML. Essa classe carrega apenas recursos do assembly no qual ele está localizado, portanto, ele não pode ser colocado em uma biblioteca.

### <a name="more-on-sizing"></a>Obter mais informações sobre dimensionamento

Muitas vezes é desejável bitmaps de tamanho consistentemente entre todas as plataformas.
Experimentando o **StackedBitmap**, você pode definir um `WidthRequest` no elemento `Image` em uma `StackLayout` vertical para tornar o tamanho consistente entre as plataformas, mas só é possível reduzir o tamanho usando essa técnica.

Você também pode definir o `HeightRequest` para tornar os tamanhos de imagem consistentes nas plataformas, mas a largura restrita do bitmap limitará a versatilidade dessa técnica. Para uma imagem em um `StackLayout`vertical, `HeightRequest` deve ser evitado.

A melhor abordagem é começar com um bitmap mais largo do que a largura do telefone nas unidades independentes do dispositivo e definir `WidthRequest` como uma largura desejada em unidades independentes do dispositivo. Isso é demonstrado no exemplo de [**DeviceIndBitmapSize**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/DeviceIndBitmapSize) .

O [**MadTeaParty**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/MadTeaParty) exibe o capítulo 7 da aventuras de Alice de Lewis Carroll *em Wonderland* com as ilustrações originais de John Tenniel:

[![Captura de tela tripla da parte do Mad de chá](images/ch13fg16-small.png "Texto do livro de terceiros de Hatters de chá de Mad")](images/ch13fg16-large.png#lightbox "Texto do livro de terceiros de Hatters de chá de Mad")

### <a name="browsing-and-waiting"></a>Navegando e aguardando

O exemplo [**ImageBrowser**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ImageBrowser) permite que o usuário navegue pelas imagens de estoque armazenadas no site da Xamarin. Ele usa a classe .NET [`WebRequest`](xref:System.Net.WebRequest) para baixar um arquivo JSON com a lista de bitmaps.

> [!NOTE]
> Os programas Xamarin. Forms devem usar [`HttpClient`](xref:System.Net.Http.HttpClient) em vez de [`WebRequest`](xref:System.Net.WebRequest) para acessar arquivos pela Internet.

O programa usa um [`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator) para indicar que algo está acontecendo. À medida que cada bitmap é carregado, a propriedade [`IsLoading`](xref:Xamarin.Forms.Image.IsLoading) somente leitura de `Image` é `true`. A propriedade `IsLoading` é apoiada por uma propriedade vinculável, para que um evento de `PropertyChanged` seja acionado quando essa propriedade for alterada. O programa anexa um manipulador a esse evento e usa a configuração atual de `IsLoaded` para definir a propriedade [`IsRunning`](xref:Xamarin.Forms.ActivityIndicator.IsRunning) do `ActivityIndicator`.

## <a name="streaming-bitmaps"></a>Bitmaps de streaming

O método `ImageSource.FromStream` cria um `ImageSource` com base em um `Stream`.NET. O método deve ser passado para um objeto `Func` que retorna um objeto `Stream`.

### <a name="accessing-the-streams"></a>Acessando os fluxos

O exemplo [**BitmapStreams**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/BitmapStreams) demonstra como usar o método `ImaageSource.FromStream` para carregar um bitmap armazenado como um recurso incorporado e carregar um bitmap na Web.

### <a name="generating-bitmaps-at-run-time"></a>Gerar bitmaps em tempo de execução

Todas as plataformas Xamarin. Forms dão suporte ao formato de arquivo BMP descompactado, que é fácil de construir no código e, em seguida, armazenar em um `MemoryStream`. Essa técnica permite forma algorítmica a criação de bitmaps em tempo de execução, conforme implementado na classe [`BmpMaker`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BmpMaker.cs) na biblioteca **Xamrin. FormsBook. Toolkit** .

O exemplo de [**DiyGradientBitmap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/DiyGradientBitmap) "faça você mesmo" demonstra o uso de `BmpMaker` para criar um bitmap com uma imagem gradual.

## <a name="platform-specific-bitmaps"></a>Bitmaps de específico da plataforma

Todas as plataformas do xamarin. Forms permitem armazenar bitmaps nos assemblies do aplicativo de plataforma. Quando recuperados por um aplicativo Xamarin. Forms, esses bitmaps de plataforma são do tipo [`FileImageSource`](xref:Xamarin.Forms.FileImageSource). Você usá-los para:

- a propriedade [`Icon`](xref:Xamarin.Forms.MenuItem.Icon) de [`MenuItem`](xref:Xamarin.Forms.MenuItem)
- a propriedade [`Icon`](xref:Xamarin.Forms.MenuItem.Icon) de [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem)
- a propriedade [`Image`](xref:Xamarin.Forms.Button) de `Button`

Os assemblies de plataforma já conterem bitmaps, ícones e telas de abertura:

- No projeto do iOS, na pasta **recursos**
- No projeto do Android, em subpastas da pasta **recursos**
- Nos projetos do Windows, na pasta **ativos** (embora as plataformas do Windows não restrinjam os bitmaps para essa pasta)

O exemplo [**PlatformBitmaps**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/PlatformBitmaps) usa o código para exibir um ícone dos projetos de aplicativo da plataforma.

### <a name="bitmap-resolutions"></a>Resoluções de bitmap

Todas as plataformas permitem armazenar várias versões das imagens de bitmap para resoluções de dispositivos diferentes. Em tempo de execução, a versão apropriada é carregada com base na resolução da tela do dispositivo.

No iOS, esses bitmaps são diferenciados por um sufixo de nome de arquivo:

- Nenhum sufixo para dispositivos de 160 DPI (de 1 pixel para a unidade independente de dispositivo)
- sufixo '@2x' para dispositivos de 320 DPI (2 pixels para o DIU)
- sufixo '@3x' para dispositivos de 480 DPI (3 pixels para o DIU)

Um bitmap que se destina a ser exibido como uma polegada quadrada existiria em três versões:

- MyImage em 160 pixels quadrados
- MyImage@2x.jpg a 320 pixels de quadrado
- MyImage@3x.jpg a 480 pixels de quadrado

O programa deve se referir a esse bitmap como myImage, mas a versão correta é recuperada em tempo de execução com base na resolução da tela. Quando irrestrita, o bitmap sempre será renderizado em unidades de 160 independentes de dispositivo.

Para o Android, os bitmaps são armazenados em várias subpastas da pasta de **recursos** :

- drawable-ldpi (baixo DPI) para dispositivos de 120 DPI (0,75 pixels para o DIU)
- drawable-mdpi (médio) para dispositivos de 160 DPI (de 1 pixel para o DIU)
- drawable-hdpi (alto) para dispositivos de 240 DPI (1,5 pixels para o DIU)
- drawable-xhdpi (muito alta) para dispositivos de 320 DPI (2 pixels para o DIU)
- drawable-xxhdpi (muito alta extra) para dispositivos de 480 DPI (3 pixels para o DIU)
- drawable-xxxhdpi (três extras altos) para dispositivos de 640 DPI (4 pixels para o DIU)

Para um bitmap que se destina a ser renderizado em uma polegada quadrada, as várias versões do bitmap terão o mesmo nome, mas um tamanho diferente e ser armazenadas nessas pastas:

- drawable-ldpi/myImage em 120 pixels quadrados
- drawable-mdpi/myImage em 160 pixels quadrados
- drawable-hdpi/myImage em 240 pixels quadrados
- drawable-xhdpi/myImage em 320 pixels quadrados
- drawable-xxhdpi/myImage em 480 pixels quadrados
- drawable-xxxhdpi/myImage em 640 pixels quadrados

O bitmap sempre será renderizado em unidades de 160 independentes de dispositivo. (O modelo de solução do xamarin. Forms padrão só inclui o hdpi, xhdpi e pastas xxhdpi.)

O projeto UWP dá suporte a um esquema de nomenclatura de bitmap que consiste em um fator de escala em pixels por unidade independente de dispositivo como uma porcentagem, por exemplo:

- MyImage.scale-200.jpg em 320 pixels quadrados

Somente alguns porcentagens são válidas. Os programas de exemplo para este livro incluem apenas imagens com sufixos **de escala-200** , mas os modelos de solução Xamarin. Forms atuais incluem **Scale-100**, **Dimension-125**, **Scale-150**e **Scale-400**.

Ao adicionar bitmaps aos projetos da plataforma, a **ação de compilação** deve ser:

- iOS: **BundleResource**
- Android: **AndroidResource**
- UWP: **conteúdo**

O exemplo de [**ImageTap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ImageTap) cria dois objetos de botão que consistem em elementos `Image` com um `TapGestureRecognizer` instalado. Ele destina-se que os objetos sejam uma polegada quadrada. A propriedade `Source` de `Image` é definida usando objetos `OnPlatform` e `On` para referenciar nomes de filepotencialmente diferentes nas plataformas. As imagens de bitmap incluem números indicando seu tamanho de pixel, para que você possa ver quais bitmap de tamanho é recuperado e processado.

### <a name="toolbars-and-their-icons"></a>Barras de ferramentas e seus ícones

Um dos principais usos de bitmaps específicos da plataforma é a barra de ferramentas do Xamarin. Forms, que é construída com a adição de [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) objetos à coleção de [`ToolbarItems`](xref:Xamarin.Forms.Page.ToolbarItems) definida por `Page`. `ToobarItem` deriva de [`MenuItem`](xref:Xamarin.Forms.MenuItem) da qual ele herda algumas propriedades.

As propriedades `ToolbarItem` mais importantes são:

- [`Text`](xref:Xamarin.Forms.MenuItem.Text) de texto que podem aparecer dependendo da plataforma e `Order`
- [`Icon`](xref:Xamarin.Forms.MenuItem.Icon) do tipo `FileImageSource` para a imagem que pode aparecer dependendo da plataforma e `Order`
- [`Order`](xref:Xamarin.Forms.ToolbarItem.Order) do tipo [`ToolbarItemOrder`](xref:Xamarin.Forms.ToolbarItemOrder), uma enumeração com três membros, [`Default`](xref:Xamarin.Forms.ToolbarItemOrder.Default), [`Primary`](xref:Xamarin.Forms.ToolbarItemOrder.Primary)e [`Secondary`](xref:Xamarin.Forms.ToolbarItemOrder.Secondary).

O número de itens de `Primary` deve ser limitado a três ou quatro. Você deve incluir uma configuração de `Text` para todos os itens. Para a maioria das plataformas, somente os `Primary` itens exigem uma `Icon`, mas Windows 8.1 requer um `Icon` para todos os itens. Os ícones devem ser 32 unidades independentes de dispositivo quadradas. O tipo de `FileImageSource` indica que eles são específicos da plataforma.

O `ToolbarItem` aciona um evento de [`Clicked`](xref:Xamarin.Forms.MenuItem.Clicked) quando tocado, de forma semelhante a uma `Button`. `ToolbarItem` também dá suporte às propriedades [`Command`](xref:Xamarin.Forms.MenuItem.Command) e [`CommandParameter`](xref:Xamarin.Forms.MenuItem.CommandParameter) frequentemente usadas em conexão com o MVVM. (Consulte o [capítulo 18, MVVM](chapter18.md)).

O iOS e o Android exigem que uma página que exibe uma barra de ferramentas seja uma [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) ou uma página navegada por um `NavigationPage`. O programa [**ToolbarDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ToolbarDemo) define a propriedade `MainPage` de sua classe `App` para o [Construtor`NavigationPage`](xref:Xamarin.Forms.NavigationPage.%23ctor(Xamarin.Forms.Page)) com um argumento `ContentPage` e demonstra a construção e o manipulador de eventos de uma barra de ferramentas.

### <a name="button-images"></a>Imagens de botão

Você também pode usar bitmaps específicos da plataforma para definir a propriedade [`Image`](xref:Xamarin.Forms.Button.Image) de `Button` para um bitmap de 32 unidades independentes de dispositivo, conforme demonstrado pelo exemplo [**ButtonImage**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ButtonImage) .

> [!NOTE]
> O uso de imagens nos botões foi aprimorado. Consulte [usando bitmaps com botões](~/xamarin-forms/user-interface/button.md#using-bitmaps-with-buttons).

## <a name="related-links"></a>Links relacionados

- [Capítulo 13 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch13-Apr2016.pdf)
- [Capítulo 13 amostras](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13)
- [Trabalhando com imagens](~/xamarin-forms/user-interface/images.md)
- [Usando bitmaps com botões](~/xamarin-forms/user-interface/button.md#using-bitmaps-with-buttons)
