---
title: Resumo do capítulo 13. Bitmaps
description: 'Criando aplicativos móveis com Xamarin.Forms : Resumo do capítulo 13. Bitmaps'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 5D153857-B6B7-4A14-8FB9-067DE198C2C7
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 43caf088ad6cb816f049e7862a287c17839c2170
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84136767"
---
# <a name="summary-of-chapter-13-bitmaps"></a>Resumo do capítulo 13. Bitmaps

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13)

> [!NOTE]
> As observações nesta página indicam áreas em que Xamarin.Forms houve uma divergência do material apresentado no livro.

O Xamarin.Forms [`Image`](xref:Xamarin.Forms.Image) elemento exibe um bitmap. Todas as Xamarin.Forms plataformas dão suporte aos formatos de arquivo JPEG, png, GIF e BMP.

Os bitmaps Xamarin.Forms vêm de quatro locais:

- Pela Web, conforme especificado por uma URL
- Inserido como um recurso na biblioteca compartilhada
- Inserido como um recurso nos projetos de aplicativo da plataforma
- De qualquer lugar que possa ser referenciado por um `Stream` objeto .net, incluindo`MemoryStream`

Os recursos de bitmap na biblioteca compartilhada são independentes de plataforma, enquanto os recursos de bitmap nos projetos de plataforma são específicos da plataforma.

> [!NOTE]
> O texto do livro faz referências a bibliotecas de classes portáteis, que foram substituídas por .NET Standard bibliotecas. Todo o código de exemplo do livro foi convertido para usar bibliotecas do .NET Standard.

O bitmap é especificado definindo a [`Source`](xref:Xamarin.Forms.Image.Source) propriedade de `Image` como um objeto do tipo [`ImageSource`](xref:Xamarin.Forms.ImageSource) , uma classe abstrata com três derivativos:

- [`UriImageSource`](xref:Xamarin.Forms.UriImageSource)para acessar um bitmap na Web com base em um `Uri` objeto definido para sua [`Uri`](xref:Xamarin.Forms.UriImageSource.Uri) Propriedade
- [`FileImageSource`](xref:Xamarin.Forms.FileImageSource)para acessar um bitmap armazenado em um projeto de aplicativo de plataforma com base em uma pasta e um caminho de arquivo definidos para sua [`File`](xref:Xamarin.Forms.FileImageSource.File) Propriedade
- [`StreamImageSource`](xref:Xamarin.Forms.StreamImageSource)para carregar um bitmap usando um `Stream` objeto .net especificado retornando um `Stream` de um `Func` conjunto para sua [`Stream`](xref:Xamarin.Forms.StreamImageSource.Stream) Propriedade

Como alternativa (e mais comumente), você pode usar os seguintes métodos estáticos da `ImageSource` classe, todos os quais retornam `ImageSource` objetos:

- [`ImageSource.FromUri`](xref:Xamarin.Forms.ImageSource.FromUri(System.Uri))para acessar um bitmap na Web com base em um `Uri` objeto
- [`ImageSource.FromResource`](xref:Xamarin.Forms.ImageSource.FromResource*)para acessar um bitmap armazenado como um recurso incorporado no aplicativo PCL; [`ImageSource.FromResource`](xref:Xamarin.Forms.ImageSource.FromResource(System.String,System.Type))ou [`ImageSource.FromResource`](xref:Xamarin.Forms.ImageSource.FromResource(System.String,System.Reflection.Assembly)) para acessar um bitmap em outro assembly de origem
- [`ImageSource.FromFile`](xref:Xamarin.Forms.ImageSource.FromFile(System.String))para acessar um bitmap de um projeto de aplicativo de plataforma
- [`ImageSource.FromStream`](xref:Xamarin.Forms.ImageSource.FromStream(System.Func{System.IO.Stream}))para carregar um bitmap com base em um `Stream` objeto

Não há nenhum equivalente de classe dos `Image.FromResource` métodos. A `UriImageSource` classe será útil se você precisar controlar o cache. A `FileImageSource` classe é útil em XAML. `StreamImageSource`é útil para o carregamento assíncrono de `Stream` objetos, enquanto `ImageSource.FromStream` é síncrono.

## <a name="platform-independent-bitmaps"></a>Bitmaps independentes de plataforma

O projeto [**WebBitmapCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/WebBitmapCode) carrega um bitmap na Web usando `ImageSource.FromUri` . O `Image` elemento é definido como a `Content` propriedade de `ContentPage` , portanto, é restrito ao tamanho da página. Independentemente do tamanho do bitmap, um `Image` elemento restrito é ampliado para o tamanho de seu contêiner e o bitmap é exibido em seu tamanho máximo dentro do elemento, mantendo `Image` a taxa de proporção do bitmap. As áreas do que `Image` estão além do bitmap podem ser coloridas com [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) .

O exemplo de [**WebBitmapXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/WebBitmapXaml) é semelhante, mas simplesmente define a `Source` propriedade para a URL. A conversão é manipulada pela [`ImageSourceConverter`](xref:Xamarin.Forms.ImageSourceConverter) classe.

### <a name="fit-and-fill"></a>Ajustar e preencher

Você pode controlar como o bitmap é ampliado definindo a [`Aspect`](xref:Xamarin.Forms.Image.Aspect) propriedade de `Image` como um dos seguintes membros da [`Aspect`](xref:Xamarin.Forms.Aspect) enumeração:

- [`AspectFit`](xref:Xamarin.Forms.Aspect.AspectFit): taxa de proporção de aspectos (padrão)
- [`Fill`](xref:Xamarin.Forms.Aspect.Fill): área de preenchimentos, não respeita a taxa de proporção
- [`AspectFill`](xref:Xamarin.Forms.Aspect.AspectFill): preenche a área, mas respeita a taxa de proporção, realizada pela parte de corte do bitmap

### <a name="embedded-resources"></a>Recursos inseridos

Você pode adicionar um arquivo de bitmap a um PCL ou a uma pasta no PCL. Dê a ele uma **ação de Build** de **EmbeddedResource**. O exemplo [**ResourceBitmapCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ResourceBitmapCode) demonstra como usar `ImageSource.FromResource` o para carregar o arquivo. O nome do recurso passado para o método consiste no nome do assembly, seguido por um ponto, seguido pelo nome da pasta opcional e um ponto, seguido pelo nome do arquivo.

O programa define as `VerticalOptions` `HorizontalOptions` Propriedades e do `Image` para, o `LayoutOptions.Center` que torna o `Image` elemento irrestrito. O `Image` e o bitmap têm o mesmo tamanho:

- No iOS e no Android, o `Image` é o tamanho do pixel do bitmap. Há um mapeamento de um para um entre pixels de bitmap e de tela.
- Em Plataforma Universal do Windows, o `Image` é o tamanho do pixel do bitmap em unidades independentes do dispositivo. Na maioria dos dispositivos, cada pixel de bitmap ocupa vários pixels da tela.

O exemplo [**StackedBitmap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/StackedBitmap) coloca um `Image` em uma vertical `StackLayout` em XAML. Uma extensão de marcação chamada [`ImageResourceExtension`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter13/StackedBitmap/StackedBitmap/StackedBitmap/ImageResourceExtension.cs) ajuda a referenciar o recurso inserido em XAML. Essa classe só carrega recursos do assembly no qual ele está localizado, portanto, ele não pode ser colocado em uma biblioteca.

### <a name="more-on-sizing"></a>Mais sobre o dimensionamento

Geralmente, é desejável dimensionar bitmaps consistentemente entre todas as plataformas.
Experimentando o **StackedBitmap**, você pode definir um `WidthRequest` no `Image` elemento em uma vertical `StackLayout` para tornar o tamanho consistente entre as plataformas, mas você só pode reduzir o tamanho usando essa técnica.

Você também pode definir o `HeightRequest` para tornar os tamanhos de imagem consistentes nas plataformas, mas a largura restrita do bitmap limitará a versatilidade dessa técnica. Para uma imagem em uma vertical `StackLayout` , `HeightRequest` deve ser evitada.

A melhor abordagem é começar com um bitmap mais largo do que a largura do telefone nas unidades independentes do dispositivo e definir `WidthRequest` como uma largura desejada em unidades independentes do dispositivo. Isso é demonstrado no exemplo de [**DeviceIndBitmapSize**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/DeviceIndBitmapSize) .

O [**MadTeaParty**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/MadTeaParty) exibe o capítulo 7 da aventuras de Alice de Lewis Carroll *em Wonderland* com as ilustrações originais de John Tenniel:

[![Captura de tela tripla da parte do Mad de chá](images/ch13fg16-small.png "Texto do livro de terceiros de Hatters de chá de Mad")](images/ch13fg16-large.png#lightbox "Texto do livro de terceiros de Hatters de chá de Mad")

### <a name="browsing-and-waiting"></a>Navegando e aguardando

O exemplo [**ImageBrowser**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ImageBrowser) permite que o usuário navegue pelas imagens de estoque armazenadas no site da Xamarin. Ele usa a [`WebRequest`](xref:System.Net.WebRequest) classe .net para baixar um arquivo JSON com a lista de bitmaps.

> [!NOTE]
> Xamarin.Formsos programas devem usar [`HttpClient`](xref:System.Net.Http.HttpClient) o em vez de [`WebRequest`](xref:System.Net.WebRequest) para acessar arquivos pela Internet.

O programa usa um [`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator) para indicar que algo está acontecendo. À medida que cada bitmap é carregado, a propriedade somente leitura [`IsLoading`](xref:Xamarin.Forms.Image.IsLoading) de `Image` é `true` . A `IsLoading` propriedade é apoiada por uma propriedade vinculável, portanto, um `PropertyChanged` evento é acionado quando essa propriedade é alterada. O programa anexa um manipulador a esse evento e usa a configuração atual de `IsLoaded` para definir a [`IsRunning`](xref:Xamarin.Forms.ActivityIndicator.IsRunning) Propriedade do `ActivityIndicator` .

## <a name="streaming-bitmaps"></a>Bitmaps de streaming

O `ImageSource.FromStream` método cria um `ImageSource` com base em um .NET `Stream` . O método deve ser passado a um `Func` objeto que retorna um `Stream` objeto.

### <a name="accessing-the-streams"></a>Acessando os fluxos

O exemplo [**BitmapStreams**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/BitmapStreams) demonstra como usar o `ImaageSource.FromStream` método para carregar um bitmap armazenado como um recurso incorporado e para carregar um bitmap na Web.

### <a name="generating-bitmaps-at-run-time"></a>Gerando bitmaps em tempo de execução

Todas as Xamarin.Forms plataformas dão suporte ao formato de arquivo BMP descompactado, que é fácil de construir no código e, em seguida, armazenar em um `MemoryStream` . Essa técnica permite forma algorítmica a criação de bitmaps em tempo de execução, conforme implementado na [`BmpMaker`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BmpMaker.cs) classe na biblioteca **Xamrin. FormsBook. Toolkit** .

O exemplo de [**DiyGradientBitmap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/DiyGradientBitmap) "faça você mesmo" demonstra o uso de `BmpMaker` para criar um bitmap com uma imagem gradual.

## <a name="platform-specific-bitmaps"></a>Bitmaps específicos da plataforma

Todas as Xamarin.Forms plataformas permitem armazenar bitmaps nos assemblies de aplicativo da plataforma. Quando recuperados por um Xamarin.Forms aplicativo, esses bitmaps de plataforma são do tipo [`FileImageSource`](xref:Xamarin.Forms.FileImageSource) . Você os usa para:

- a [`Icon`](xref:Xamarin.Forms.MenuItem.Icon) propriedade de[`MenuItem`](xref:Xamarin.Forms.MenuItem)
- a [`Icon`](xref:Xamarin.Forms.MenuItem.Icon) propriedade de[`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem)
- a [`Image`](xref:Xamarin.Forms.Button) propriedade de`Button`

Os assemblies da plataforma já contêm bitmaps para ícones e telas de abertura:

- No projeto do iOS, na pasta **recursos**
- No projeto do Android, em subpastas da pasta **recursos**
- Nos projetos do Windows, na pasta **ativos** (embora as plataformas do Windows não restrinjam os bitmaps para essa pasta)

O exemplo [**PlatformBitmaps**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/PlatformBitmaps) usa o código para exibir um ícone dos projetos de aplicativo da plataforma.

### <a name="bitmap-resolutions"></a>Resoluções de bitmap

Todas as plataformas permitem armazenar várias versões de imagens de bitmap para diferentes resoluções de dispositivo. Em tempo de execução, a versão apropriada é carregada com base na resolução do dispositivo da tela.

No iOS, esses bitmaps são diferenciados por um sufixo no nome do arquivo:

- Nenhum sufixo para dispositivos de 160 DPI (1 pixel para a unidade independente de dispositivo)
- @2xsufixo ' ' para dispositivos de 320 DPI (2 pixels para o DIU)
- @3xsufixo ' ' para dispositivos de 480 DPI (3 pixels para o DIU)

Um bitmap destinado a ser exibido como um quadrado de uma polegada existirá em três versões:

- MyImage.jpg a 160 pixels de quadrado
- MyImage@2x.jpga 320 pixels quadrado
- MyImage@3x.jpga 480 pixels quadrado

O programa se referiria a esse bitmap como MyImage.jpg, mas a versão apropriada é recuperada em tempo de execução com base na resolução da tela. Quando irrestrito, o bitmap sempre será renderizado em unidades independentes de dispositivo 160.

Para o Android, os bitmaps são armazenados em várias subpastas da pasta de **recursos** :

- estilododesenho-ldpi (baixo DPI) para dispositivos de 120 DPI (0,75 pixels para o DIU)
- Draw-mdpi (médio) para dispositivos de 160 DPI (1 pixel para o DIU)
- Draw-hdpi (alta) para dispositivos de 240 DPI (1,5 pixels para o DIU)
- Draw-xhdpi (extra alta) para dispositivos de 320 DPI (2 pixels para o DIU)
- Draw-xxhdpi (extra extra) para dispositivos de 480 DPI (3 pixels para o DIU)
- Draw-xxxhdpi (três alto extras) para dispositivos de 640 DPI (4 pixels para o DIU)

Para um bitmap destinado a ser renderizado em uma polegada quadrada, as várias versões do bitmap terão o mesmo nome, mas um tamanho diferente, e serão armazenadas nessas pastas:

- estilododesenho-ldpi/MyImage.jpg a 120 pixels quadrados
- estilododesenho-mdpi/MyImage.jpg a 160 pixels quadrados
- estilododesenho-hdpi/MyImage.jpg a 240 pixels quadrados
- estilododesenho-xhdpi/MyImage.jpg a 320 pixels quadrados
- estilododesenho-xxhdpi/MyImage.jpg a 480 pixels quadrados
- estilododesenho-xxxhdpi/MyImage.jpg a 640 pixels quadrados

O bitmap sempre será renderizado em unidades independentes de dispositivo 160. (O Xamarin.Forms modelo de solução padrão inclui apenas as pastas hdpi, xhdpi e xxhdpi.)

O projeto UWP dá suporte a um esquema de nomenclatura de bitmap que consiste em um fator de dimensionamento em pixels por unidade independente de dispositivo como uma porcentagem, por exemplo:

- MyImage.scale-200.jpg a 320 pixels de quadrado

Apenas algumas porcentagens são válidas. Os programas de exemplo para este livro incluem apenas imagens com sufixos **de escala-200** , mas os Xamarin.Forms modelos de solução atuais incluem **Scale-100**, **dimension-125**, **Scale-150**e **Scale-400**.

Ao adicionar bitmaps aos projetos da plataforma, a **ação de compilação** deve ser:

- iOS: **BundleResource**
- Android: **AndroidResource**
- UWP: **conteúdo**

O exemplo de [**ImageTap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ImageTap) cria dois objetos do tipo botão que consistem em `Image` elementos com um `TapGestureRecognizer` instalado. O objetivo é que os objetos sejam um quadrado de uma polegada. A `Source` propriedade de `Image` é definida usando `OnPlatform` `On` objetos e para referenciar nomes de filepotencialmente diferentes nas plataformas. As imagens de bitmap incluem números que indicam o tamanho do pixel, para que você possa ver qual bitmap de tamanho é recuperado e renderizado.

### <a name="toolbars-and-their-icons"></a>Barras de ferramentas e seus ícones

Um dos principais usos de bitmaps específicos da plataforma é a Xamarin.Forms barra de ferramentas, que é construída pela adição de [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) objetos à [`ToolbarItems`](xref:Xamarin.Forms.Page.ToolbarItems) coleção definida por `Page` . `ToobarItem`deriva de [`MenuItem`](xref:Xamarin.Forms.MenuItem) onde ele herda algumas propriedades.

As propriedades mais importantes `ToolbarItem` são:

- [`Text`](xref:Xamarin.Forms.MenuItem.Text)para texto que pode aparecer dependendo da plataforma e`Order`
- [`Icon`](xref:Xamarin.Forms.MenuItem.Icon)do tipo `FileImageSource` para a imagem que pode aparecer dependendo da plataforma e`Order`
- [`Order`](xref:Xamarin.Forms.ToolbarItem.Order)do tipo [`ToolbarItemOrder`](xref:Xamarin.Forms.ToolbarItemOrder) , uma enumeração com três membros, [`Default`](xref:Xamarin.Forms.ToolbarItemOrder.Default) , [`Primary`](xref:Xamarin.Forms.ToolbarItemOrder.Primary) e [`Secondary`](xref:Xamarin.Forms.ToolbarItemOrder.Secondary) .

O número de `Primary` itens deve ser limitado a três ou quatro. Você deve incluir uma `Text` configuração para todos os itens. Para a maioria das plataformas, somente os `Primary` itens exigem um `Icon` , mas Windows 8.1 requer um `Icon` para todos os itens. Os ícones devem ser de 32 unidades independentes de dispositivo quadrado. O `FileImageSource` tipo indica que eles são específicos da plataforma.

O `ToolbarItem` dispara um [`Clicked`](xref:Xamarin.Forms.MenuItem.Clicked) evento quando tocado, muito parecido com um `Button` . `ToolbarItem`também suporta [`Command`](xref:Xamarin.Forms.MenuItem.Command) e [`CommandParameter`](xref:Xamarin.Forms.MenuItem.CommandParameter) Propriedades geralmente usadas em conexão com o MVVM. (Consulte o [capítulo 18, MVVM](chapter18.md)).

O iOS e o Android exigem que uma página que exibe uma barra de ferramentas seja uma [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) ou uma página navegada por um `NavigationPage` . O programa [**ToolbarDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ToolbarDemo) define a `MainPage` propriedade de sua `App` classe para o [ `NavigationPage` constructor] (xref: Xamarin.Forms . NavigationPage .% 23ctor ( Xamarin.Forms . ) Com um `ContentPage` argumento e demonstra a construção e o manipulador de eventos de uma barra de ferramentas.

### <a name="button-images"></a>Imagens de botão

Você também pode usar bitmaps específicos da plataforma para definir a [`Image`](xref:Xamarin.Forms.Button.Image) propriedade de `Button` como um bitmap do quadrado de unidades independentes de dispositivo 32, conforme demonstrado pelo exemplo de [**ButtonImage**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ButtonImage) .

> [!NOTE]
> O uso de imagens em botões foi aprimorado. Consulte [usando bitmaps com botões](~/xamarin-forms/user-interface/button.md#using-bitmaps-with-buttons).

## <a name="related-links"></a>Links relacionados

- [Capítulo 13 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch13-Apr2016.pdf)
- [Capítulo 13 amostras](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13)
- [Trabalhando com imagens](~/xamarin-forms/user-interface/images.md)
- [Usando bitmaps com botões](~/xamarin-forms/user-interface/button.md#using-bitmaps-with-buttons)
