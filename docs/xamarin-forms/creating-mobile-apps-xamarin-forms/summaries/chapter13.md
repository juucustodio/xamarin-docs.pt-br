---
title: Resumo do capítulo 13. Bitmaps
description: 'Criação de aplicativos móveis com xamarin. Forms: Resumo do capítulo 13. Bitmaps'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 5D153857-B6B7-4A14-8FB9-067DE198C2C7
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: ac4dd8ffc0ce407aeb9dbc36fe705a809a9c4058
ms.sourcegitcommit: 03dfb4a2c20ad68515875b415e7d84ee9b0a8cb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2018
ms.locfileid: "51563584"
---
# <a name="summary-of-chapter-13-bitmaps"></a>Resumo do capítulo 13. Bitmaps

> [!NOTE] 
> Observações sobre essa página indicam áreas onde o xamarin. Forms foi bifurcado do material apresentado no livro.

O xamarin. Forms [ `Image` ](xref:Xamarin.Forms.Image) elemento exibe um bitmap. Todas as plataformas do xamarin. Forms dão suporte os formatos de arquivo JPEG, PNG, GIF e BMP.

Bitmaps no xamarin. Forms é proveniente de quatro lugares:

- Por meio da web conforme especificado por uma URL
- Incorporado como um recurso na biblioteca compartilhada
- Incorporado como um recurso nos projetos de aplicativo da plataforma
- Em qualquer lugar que pode ser referenciado por um .NET `Stream` do objeto, incluindo `MemoryStream`

Recursos de bitmap na biblioteca compartilhada são independentes de plataforma, enquanto os recursos de bitmap em projetos de plataforma são específicos da plataforma.

> [!NOTE] 
> O texto do livro faz referência a bibliotecas de classes portáteis, foram substituídos por bibliotecas .NET Standard. O código de exemplo do livro foi convertido para usar bibliotecas padrão do .NET.

O bitmap é especificado pela configuração de [ `Source` ](xref:Xamarin.Forms.Image.Source) propriedade do `Image` a um objeto do tipo [ `ImageSource` ](xref:Xamarin.Forms.ImageSource), uma classe abstrata com três derivativos:

- [`UriImageSource`](xref:Xamarin.Forms.UriImageSource) para acessar um bitmap pela web com base em um `Uri` objeto definido como seu [ `Uri` ](xref:Xamarin.Forms.UriImageSource.Uri) propriedade
- [`FileImageSource`](xref:Xamarin.Forms.FileImageSource) para acessar um bitmap armazenado em um projeto de aplicativo de plataforma com base em um caminho de pasta e arquivo definido como seu [ `File` ](xref:Xamarin.Forms.FileImageSource.File) propriedade
- [`StreamImageSource`](xref:Xamarin.Forms.StreamImageSource) para carregar um bitmap usando um .NET `Stream` objeto especificado, retornando um `Stream` de uma `Func` definido como seu [ `Stream` ](xref:Xamarin.Forms.StreamImageSource.Stream) propriedade

Como alternativa (e mais comum), você pode usar os seguintes métodos estáticos do `ImageSource` classe, todos os quais retornam `ImageSource` objetos:

- [`ImageSource.FromUri`](xref:Xamarin.Forms.ImageSource.FromUri(System.Uri)) para acessar um bitmap pela web com base em um `Uri` objeto
- [`ImageSource.FromResource`](xref:Xamarin.Forms.ImageSource.FromResource*) para acessar um bitmap armazenado como um recurso inserido no aplicativo PCL; [ `ImageSource.FromResource` ](xref:Xamarin.Forms.ImageSource.FromResource(System.String,System.Type)) ou [ `ImageSource.FromResource` ](xref:Xamarin.Forms.ImageSource.FromResource(System.String,System.Reflection.Assembly)) para acessar um bitmap em outro assembly de código-fonte
- [`ImageSource.FromFile`](xref:Xamarin.Forms.ImageSource.FromFile(System.String)) para acessar um bitmap de um projeto de aplicativo de plataforma
- [`ImageSource.FromStream`](xref:Xamarin.Forms.ImageSource.FromStream(System.Func{System.IO.Stream})) para carregar um bitmap com base em um `Stream` objeto

Não há nenhum equivalente a classe a `Image.FromResource` métodos. O `UriImageSource` classe é útil se você precisar controlar o cache. O `FileImageSource` classe é útil em XAML. `StreamImageSource` é útil para o carregamento assíncrono do `Stream` objetos, enquanto `ImageSource.FromStream` é síncrono.

## <a name="platform-independent-bitmaps"></a>Bitmaps independente de plataforma

O [ **WebBitmapCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/WebBitmapCode) projeto carrega um bitmap com o uso de web `ImageSource.FromUri`. O `Image` é definido como o `Content` propriedade do `ContentPage`, portanto, ele é restrito para o tamanho da página. Independentemente do tamanho do bitmap, um restrito `Image` elemento é estendido para o tamanho de seu contêiner e o bitmap é exibido em seu tamanho máximo dentro a `Image` elemento mantendo a taxa de proporção do bitmap. Áreas do `Image` além de bitmap pode ser colorido com [ `BackgroundColor` ](xref:Xamarin.Forms.VisualElement.BackgroundColor).

O [ **WebBitmapXaml** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/WebBitmapXaml) exemplo é semelhante, mas simplesmente define o `Source` propriedade para a URL. A conversão é tratada pelos [ `ImageSourceConverter` ](xref:Xamarin.Forms.ImageSourceConverter) classe.

### <a name="fit-and-fill"></a>Ajuste e preenchimento

Você pode controlar como o bitmap é esticado definindo a [ `Aspect` ](xref:Xamarin.Forms.Image.Aspect) propriedade do `Image` para um dos seguintes membros da [ `Aspect` ](xref:Xamarin.Forms.Aspect) enumeração:

- [`AspectFit`](xref:Xamarin.Forms.Aspect.AspectFit): respeita a taxa de proporção (padrão)
- [`Fill`](xref:Xamarin.Forms.Aspect.Fill): preenche a área, não respeita a taxa de proporção
- [`AspectFill`](xref:Xamarin.Forms.Aspect.AspectFill): preenche a área, mas respeita a taxa de proporção, realizada por parte do bitmap de corte

### <a name="embedded-resources"></a>Recursos inseridos

Você pode adicionar um arquivo de bitmap para uma PCL ou para uma pasta na PCL. Dê a ela um **ação de compilação** dos **EmbeddedResource**. O [ **ResourceBitmapCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ResourceBitmapCode) exemplo demonstra como usar `ImageSource.FromResource` para carregar o arquivo. O nome do recurso passado para o método consiste o nome do assembly, seguido por um ponto, seguido pelo nome da pasta opcional e um ponto, seguido pelo nome de arquivo.

O programa define a `VerticalOptions` e `HorizontalOptions` propriedades da `Image` para `LayoutOptions.Center`, que torna o `Image` elemento irrestrita. O `Image` e o bitmap são do mesmo tamanho:

- No iOS e Android, o `Image` é o tamanho de pixel do bitmap. Há um mapeamento individual entre pixels de bitmap e tela.
- Na plataforma Universal do Windows, o `Image` é o tamanho de pixel do bitmap em unidades independentes de dispositivo. Na maioria dos dispositivos, cada pixel de bitmap ocupa várias pixels da tela.

O [ **StackedBitmap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/StackedBitmap) exemplo coloca um `Image` em uma vertical `StackLayout` em XAML. Uma extensão de marcação chamada [ `ImageResourceExtension` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter13/StackedBitmap/StackedBitmap/StackedBitmap/ImageResourceExtension.cs) ajuda a fazer referência ao recurso incorporado no XAML. Essa classe carrega apenas recursos do assembly no qual ele está localizado, portanto, ele não pode ser colocado em uma biblioteca.

### <a name="more-on-sizing"></a>Obter mais informações sobre dimensionamento

Muitas vezes é desejável bitmaps de tamanho consistentemente entre todas as plataformas.
Experimentando **StackedBitmap**, você pode definir um `WidthRequest` sobre o `Image` elemento em uma vertical `StackLayout` tornar o tamanho consistente entre as plataformas, mas você pode apenas reduzir o tamanho usando essa técnica.

Você também pode definir o `HeightRequest` garantir a imagem dimensiona consistente nas plataformas, mas a restrita largura do bitmap limitará a versatilidade dessa técnica. Para uma imagem em uma vertical `StackLayout`, `HeightRequest` deve ser evitado.

A melhor abordagem é começar com um bitmap mais largo do que a largura do telefone em unidades independentes de dispositivo e definir `WidthRequest` para uma largura desejada em unidades independentes de dispositivo. Isso é demonstrado na [ **DeviceIndBitmapSize** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/DeviceIndBitmapSize) exemplo.

O [ **MadTeaParty** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/MadTeaParty) exibe o capítulo 7 de Lewis Carroll *aventuras de Alice no país das Maravilhas* com as ilustrações originais por John Tenniel:

[![Captura de tela tripla de terceiros de chá mad](images/ch13fg16-small.png "Mad Hatters chá terceiros catálogo de texto")](images/ch13fg16-large.png#lightbox "Mad Hatters chá terceiros catálogo de texto")

### <a name="browsing-and-waiting"></a>Navegando e aguardando

O [ **ImageBrowser** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ImageBrowser) amostra permite que o usuário navegue por meio de imagens de estoque armazenadas no site do Xamarin. Ele usa o .NET [ `WebRequest` ](xref:System.Net.WebRequest) classe para baixar um arquivo JSON com a lista de bitmaps.

> [!NOTE]
> Programas do xamarin. Forms devem usar [ `HttpClient` ](xref:System.Net.Http.HttpClient) vez [ `WebRequest` ](xref:System.Net.WebRequest) para acessar arquivos pela internet. 

O programa usa uma [ `ActivityIndicator` ](xref:Xamarin.Forms.ActivityIndicator) para indicar que algo está acontecendo. Conforme cada bitmap está carregando, somente leitura [ `IsLoading` ](xref:Xamarin.Forms.Image.IsLoading) propriedade do `Image` é `true`. O `IsLoading` propriedade é feita por uma propriedade vinculável, portanto, um `PropertyChanged` evento é disparado quando essa propriedade é alterado. O programa anexa um manipulador para este evento e usará a configuração atual do `IsLoaded` para definir o [ `IsRunning` ](https://api/property/Xamarin.Forms.ActivityIndicator.IsRunning/) propriedade do `ActivityIndicator`.

## <a name="streaming-bitmaps"></a>Bitmaps de streaming

O `ImageSource.FromStream` método cria um `ImageSource` com base em um .NET `Stream`. O método deve receber uma `Func` objeto que retorna um `Stream` objeto.

### <a name="accessing-the-streams"></a>Acessando os fluxos

O [ **BitmapStreams** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/BitmapStreams) exemplo demonstra como usar o `ImaageSource.FromStream` método para carregar um bitmap armazenado como um recurso inserido e para carregar um bitmap em toda a web.

### <a name="generating-bitmaps-at-run-time"></a>Gerar bitmaps em tempo de execução

O formato de arquivo não compactado do BMP, que é fácil construir no código e, em seguida, armazenar em dar suporte a todas as plataformas do xamarin. Forms um `MemoryStream`. Essa técnica permite a criação de forma algorítmica bitmaps em tempo de execução, conforme implementado de [ `BmpMaker` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BmpMaker.cs) classe no **Xamrin.FormsBook.Toolkit** biblioteca.

O "faça você mesmo" [ **DiyGradientBitmap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/DiyGradientBitmap) exemplo demonstra o uso de `BmpMaker` para criar um bitmap com uma imagem gradiente.

## <a name="platform-specific-bitmaps"></a>Bitmaps de específico da plataforma

Todas as plataformas do xamarin. Forms permitem armazenar bitmaps nos assemblies do aplicativo de plataforma. Quando recuperada por um aplicativo xamarin. Forms, esses bitmaps de plataforma são do tipo [ `FileImageSource` ](xref:Xamarin.Forms.FileImageSource). Você usá-los para:

- o [ `Icon` ](xref:Xamarin.Forms.MenuItem.Icon) propriedade de [`MenuItem`](xref:Xamarin.Forms.MenuItem)
- o [ `Icon` ](xref:Xamarin.Forms.MenuItem.Icon) propriedade de [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem)
- o [ `Image` ](xref:Xamarin.Forms.Button) propriedade de `Button`

Os assemblies de plataforma já conterem bitmaps, ícones e telas de abertura:

- No projeto do iOS, nos **recursos** pasta
- No projeto do Android, nas subpastas dos **recursos** pasta
- Em projetos do Windows, nos **ativos** pasta (embora as plataformas Windows não restringir bitmaps para essa pasta)

O [ **PlatformBitmaps** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/PlatformBitmaps) exemplo usa o código para exibir um ícone de projetos de aplicativos de plataforma.

### <a name="bitmap-resolutions"></a>Resoluções de bitmap

Todas as plataformas permitem armazenar várias versões das imagens de bitmap para resoluções de dispositivos diferentes. Em tempo de execução, a versão apropriada é carregada com base na resolução da tela do dispositivo.

No iOS, esses bitmaps são diferenciados por um sufixo de nome de arquivo:

- Nenhum sufixo para dispositivos de 160 DPI (de 1 pixel para a unidade independente de dispositivo)
- '@2x' sufixo para dispositivos de 320 DPI (2 pixels para o DIU)
- '@3x' sufixo para dispositivos de 480 DPI (3 pixels para o DIU)

Um bitmap que se destina a ser exibido como uma polegada quadrada existiria em três versões:

- MyImage em 160 pixels quadrados
- MyImage@2x.jpg em 320 pixels quadrados
- MyImage@3x.jpg em 480 pixels quadrados

O programa deve se referir a esse bitmap como myImage, mas a versão correta é recuperada em tempo de execução com base na resolução da tela. Quando irrestrita, o bitmap sempre será renderizado em unidades de 160 independentes de dispositivo.

Para o Android, os bitmaps são armazenados em várias subpastas do **recursos** pasta:

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

Somente alguns porcentagens são válidas. Programas de exemplo deste livro incluem apenas imagens com **escala 200** sufixos, mas os modelos de solução xamarin. Forms atuais incluem **escala 100**, **escala 125**, **escala 150**, e **400 escala**.

Ao adicionar bitmaps para os projetos de plataforma, o **ação de compilação** deve ser:

- iOS: **BundleResource**
- Android: **AndroidResource**
- UWP: **conteúdo**

O [ **ImageTap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ImageTap) exemplo cria dois objetos de tipo de botão consiste `Image` elementos com um `TapGestureRecognizer` instalado. Ele destina-se que os objetos sejam uma polegada quadrada. O `Source` propriedade de `Image` é definido usando `OnPlatform` e `On` objetos para fazer referência a nomes de arquivos potencialmente diferentes nas plataformas. As imagens de bitmap incluem números indicando seu tamanho de pixel, para que você possa ver quais bitmap de tamanho é recuperado e processado.

### <a name="toolbars-and-their-icons"></a>Barras de ferramentas e seus ícones

Um dos principais usos de bitmaps de específico da plataforma é a barra de ferramentas do xamarin. Forms, que é construída, adicionando [ `ToolbarItem` ](xref:Xamarin.Forms.ToolbarItem) objetos para o [ `ToolbarItems` ](xref:Xamarin.Forms.Page.ToolbarItems) coleção definida pelo `Page`. `ToobarItem` deriva [ `MenuItem` ](xref:Xamarin.Forms.MenuItem) da qual ela herda algumas propriedades.

O mais importante `ToolbarItem` propriedades são:

- [`Text`](xref:Xamarin.Forms.MenuItem.Text) para o texto que pode aparecer dependendo da plataforma e `Order`
- [`Icon`](xref:Xamarin.Forms.MenuItem.Icon) do tipo `FileImageSource` para a imagem que pode aparecer dependendo da plataforma e `Order`
- [`Order`](xref:Xamarin.Forms.ToolbarItem.Order) do tipo [ `ToolbarItemOrder` ](xref:Xamarin.Forms.ToolbarItemOrder), uma enumeração com três membros, [ `Default` ](xref:Xamarin.Forms.ToolbarItemOrder.Default), [ `Primary` ](xref:Xamarin.Forms.ToolbarItemOrder.Primary), e [ `Secondary` ](xref:Xamarin.Forms.ToolbarItemOrder.Secondary).

O número de `Primary` itens devem ser limitados a três ou quatro. Você deve incluir um `Text` configuração para todos os itens. Para a maioria das plataformas, apenas o `Primary` itens que exigem uma `Icon` , mas requer o Windows 8.1 um `Icon` para todos os itens. Os ícones devem ser 32 unidades independentes de dispositivo quadradas. O `FileImageSource` tipo indica que são específicos da plataforma.

O `ToolbarItem` dispara uma [ `Clicked` ](xref:Xamarin.Forms.MenuItem.Clicked) eventos quando tocada, muito parecido com um `Button`. `ToolbarItem` também dá suporte à [ `Command` ](xref:Xamarin.Forms.MenuItem.Command) e [ `CommandParameter` ](xref:Xamarin.Forms.MenuItem.CommandParameter) propriedades geralmente usadas em conjunto com o MVVM. (Consulte [capítulo 18, MVVM](chapter18.md)).

IOS e Android exigem que uma página que exibe uma barra de ferramentas é ser um [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) ou uma página para onde navegadas por uma `NavigationPage`. O [ **ToolbarDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ToolbarDemo) conjuntos de programa a `MainPage` propriedade do seu `App` classe para o [ `NavigationPage` construtor](xref:Xamarin.Forms.NavigationPage.%23ctor(Xamarin.Forms.Page)) com um `ContentPage` argumento e demonstra o manipulador de evento e a construção de uma barra de ferramentas.

### <a name="button-images"></a>Imagens de botão

Você também pode usar os bitmaps de específico da plataforma para definir a [ `Image` ](xref:Xamarin.Forms.Button.Image) propriedade do `Button` em um bitmap de quadrado de unidades de 32 independentes de dispositivo, como demonstrado pelo [ **ButtonImage** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ButtonImage) exemplo.

> [!NOTE]
> O uso de imagens nos botões foi aprimorado. Ver [através de bitmaps com botões](~/xamarin-forms/user-interface/button.md#using-bitmaps-with-buttons).

## <a name="related-links"></a>Links relacionados

- [Capítulo 13 de texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch13-Apr2016.pdf)
- [Exemplos do capítulo 13](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13)
- [Trabalhando com imagens](~/xamarin-forms/user-interface/images.md)
- [Usar bitmaps com botões](~/xamarin-forms/user-interface/button.md#using-bitmaps-with-buttons)
