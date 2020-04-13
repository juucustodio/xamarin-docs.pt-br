---
title: Resumo do Capítulo 13. Bitmaps
description: 'Criando aplicativos móveis com Xamarin.Forms: Resumo do Capítulo 13. Bitmaps'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 5D153857-B6B7-4A14-8FB9-067DE198C2C7
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: e4746ed94a008d382ce15bb9cd7c52365d9ba574
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "78291547"
---
# <a name="summary-of-chapter-13-bitmaps"></a>Resumo do Capítulo 13. Bitmaps

[![Baixar](~/media/shared/download.png) amostra Baixar a amostra](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13)

> [!NOTE]
> As notas nesta página indicam áreas onde xamarin.forms divergiu do material apresentado no livro.

O elemento Xamarin.Forms [`Image`](xref:Xamarin.Forms.Image) exibe um bitmap. Todas as plataformas Xamarin.Forms suportam os formatos de arquivos JPEG, PNG, GIF e BMP.

Bitmaps em Xamarin.Forms vêm de quatro lugares:

- Pela web, conforme especificado por uma URL
- Incorporado como um recurso na biblioteca compartilhada
- Incorporado como um recurso nos projetos de aplicativos da plataforma
- De qualquer lugar que possa ser `Stream` referenciado por um objeto .NET, incluindo`MemoryStream`

Os recursos do Bitmap na biblioteca compartilhada são independentes da plataforma, enquanto os recursos do bitmap nos projetos da plataforma são específicos da plataforma.

> [!NOTE]
> O texto do livro faz referências às Bibliotecas de Classe Portáteis, que foram substituídas por bibliotecas padrão .NET. Todo o código de amostra do livro foi convertido para usar bibliotecas padrão .NET.

O bitmap é especificado [`Source`](xref:Xamarin.Forms.Image.Source) definindo a propriedade de `Image` um objeto de tipo [`ImageSource`](xref:Xamarin.Forms.ImageSource), uma classe abstrata com três derivativos:

- [`UriImageSource`](xref:Xamarin.Forms.UriImageSource)para acessar um bitmap sobre a `Uri` web com [`Uri`](xref:Xamarin.Forms.UriImageSource.Uri) base em um objeto definido para sua propriedade
- [`FileImageSource`](xref:Xamarin.Forms.FileImageSource)para acessar um bitmap armazenado em um projeto de aplicativo de [`File`](xref:Xamarin.Forms.FileImageSource.File) plataforma com base em uma pasta e caminho de arquivo definido para sua propriedade
- [`StreamImageSource`](xref:Xamarin.Forms.StreamImageSource)para carregar um bitmap `Stream` usando um objeto .NET `Func` especificado [`Stream`](xref:Xamarin.Forms.StreamImageSource.Stream) retornando um `Stream` de um conjunto para sua propriedade

Alternativamente (e mais comumente) você pode usar os `ImageSource` seguintes métodos `ImageSource` estáticos da classe, todos os quais retornam objetos:

- [`ImageSource.FromUri`](xref:Xamarin.Forms.ImageSource.FromUri(System.Uri))para acessar um bitmap na web `Uri` com base em um objeto
- [`ImageSource.FromResource`](xref:Xamarin.Forms.ImageSource.FromResource*)para acessar um bitmap armazenado como um recurso incorporado no aplicativo PCL; [`ImageSource.FromResource`](xref:Xamarin.Forms.ImageSource.FromResource(System.String,System.Type)) ou [`ImageSource.FromResource`](xref:Xamarin.Forms.ImageSource.FromResource(System.String,System.Reflection.Assembly)) para acessar um bitmap em outra montagem de origem
- [`ImageSource.FromFile`](xref:Xamarin.Forms.ImageSource.FromFile(System.String))para acessar um bitmap de um projeto de aplicativo de plataforma
- [`ImageSource.FromStream`](xref:Xamarin.Forms.ImageSource.FromStream(System.Func{System.IO.Stream}))para carregar um bitmap `Stream` com base em um objeto

Não há classe equivalente `Image.FromResource` aos métodos. A `UriImageSource` classe é útil se você precisar controlar o cache. A `FileImageSource` classe é útil em XAML. `StreamImageSource`é útil para o carregamento `Stream` assíncrono de objetos, enquanto `ImageSource.FromStream` que é síncrono.

## <a name="platform-independent-bitmaps"></a>Bitmaps independentes de plataforma

O projeto [**WebBitmapCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/WebBitmapCode) carrega um bitmap `ImageSource.FromUri`na Web usando . O `Image` elemento é `Content` definido como `ContentPage`propriedade do , por isso é limitado ao tamanho da página. Independentemente do tamanho do bitmap, `Image` um elemento restrito é estendido ao tamanho de seu recipiente, e `Image` o bitmap é exibido em seu tamanho máximo dentro do elemento, mantendo a proporção do bitmap. Áreas `Image` além do bitmap podem [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor)ser coloridas com .

A amostra [**WebBitmapXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/WebBitmapXaml) é semelhante, mas simplesmente define a `Source` propriedade para a URL. A conversão é feita [`ImageSourceConverter`](xref:Xamarin.Forms.ImageSourceConverter) pela classe.

### <a name="fit-and-fill"></a>Ajuste e encha

Você pode controlar como o bitmap [`Aspect`](xref:Xamarin.Forms.Image.Aspect) é estendido `Image` definindo a propriedade [`Aspect`](xref:Xamarin.Forms.Aspect) do para um dos seguintes membros da enumeração:

- [`AspectFit`](xref:Xamarin.Forms.Aspect.AspectFit): respeita a razão de aspecto (padrão)
- [`Fill`](xref:Xamarin.Forms.Aspect.Fill): preenche área, não respeita proporção
- [`AspectFill`](xref:Xamarin.Forms.Aspect.AspectFill): preenche área, mas respeita proporção, realizada pelo corte de parte do bitmap

### <a name="embedded-resources"></a>Recursos incorporados

Você pode adicionar um arquivo bitmap a um PCL, ou a uma pasta no PCL. Dê-lhe uma ação de **compilação** **do EmbeddedResource**. A amostra [**ResourceBitmapCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ResourceBitmapCode) demonstra `ImageSource.FromResource` como usar para carregar o arquivo. O nome do recurso passado para o método consiste no nome do conjunto, seguido por um ponto, seguido pelo nome da pasta opcional e um ponto, seguido pelo nome do arquivo.

O programa `VerticalOptions` define `HorizontalOptions` as `Image` propriedades `LayoutOptions.Center`do to `Image` , o que torna o elemento sem restrições. O `Image` bitmap e o bitmap são do mesmo tamanho:

- No iOS e `Image` Android, o é o tamanho do pixel do bitmap. Há um mapeamento de um para um entre pixels de bitmap e pixels de tela.
- Na Universal Windows `Image` Platform, o tamanho do pixel do bitmap em unidades independentes de dispositivos. Na maioria dos dispositivos, cada pixel bitmap ocupa vários pixels de tela.

A amostra [**StackedBitmap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/StackedBitmap) coloca `Image` `StackLayout` um em uma vertical em XAML. Uma extensão [`ImageResourceExtension`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter13/StackedBitmap/StackedBitmap/StackedBitmap/ImageResourceExtension.cs) de marcação nomeada ajuda a referenciar o recurso incorporado no XAML. Esta classe só carrega recursos da montagem em que está localizada, por isso não pode ser colocada em uma biblioteca.

### <a name="more-on-sizing"></a>Mais sobre dimensionamento

Muitas vezes é desejável dimensionar bitmaps consistentemente entre todas as plataformas.
Experimentando com **o StackedBitmap,** `WidthRequest` você `Image` pode definir `StackLayout` um sobre o elemento em uma vertical para tornar o tamanho consistente entre as plataformas, mas você só pode reduzir o tamanho usando esta técnica.

Você também pode `HeightRequest` definir o para tornar os tamanhos de imagem consistentes nas plataformas, mas a largura restrita do bitmap limitará a versatilidade desta técnica. Para uma imagem `StackLayout`em `HeightRequest` uma vertical, deve-se evitar.

A melhor abordagem é começar com um bitmap maior do `WidthRequest` que a largura do telefone em unidades independentes de dispositivos e definir uma largura desejada em unidades independentes de dispositivos. Isso é demonstrado na amostra [**DeviceIndBitmapSize.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/DeviceIndBitmapSize)

The [**MadTeaParty**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/MadTeaParty) exibe o Capítulo 7 de *As Aventuras de Alice no País das Maravilhas, de* Lewis Carroll, com as ilustrações originais de John Tenniel:

[![Captura de tela tripla de festa do chá louco](images/ch13fg16-small.png "Livro do Livro do Dia do Chá dos Chapeleiros Loucos")](images/ch13fg16-large.png#lightbox "Livro do Livro do Dia do Chá dos Chapeleiros Loucos")

### <a name="browsing-and-waiting"></a>Navegando e esperando

A amostra [**ImageBrowser**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ImageBrowser) permite que o usuário navegue através de imagens de estoque armazenadas no site da Xamarin. Ele usa a [`WebRequest`](xref:System.Net.WebRequest) classe .NET para baixar um arquivo JSON com a lista de bitmaps.

> [!NOTE]
> Os programas Xamarin.Forms devem ser usados [`HttpClient`](xref:System.Net.Http.HttpClient) em vez de [`WebRequest`](xref:System.Net.WebRequest) acessar arquivos pela internet.

O programa [`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator) usa um para indicar que algo está acontecendo. Como cada bitmap está carregando, [`IsLoading`](xref:Xamarin.Forms.Image.IsLoading) a `Image` `true`propriedade somente leitura é . A `IsLoading` propriedade é apoiada por uma propriedade `PropertyChanged` vinculável, então um evento é acionado quando essa propriedade muda. O programa anexa um manipulador a este evento `IsLoaded` e usa [`IsRunning`](xref:Xamarin.Forms.ActivityIndicator.IsRunning) a `ActivityIndicator`configuração atual de definir a propriedade do .

## <a name="streaming-bitmaps"></a>Bitmaps de streaming

O `ImageSource.FromStream` método `ImageSource` cria uma base `Stream`em um .NET . O método deve `Func` ser passado `Stream` por um objeto que retorna um objeto.

### <a name="accessing-the-streams"></a>Acessando os córregos

A amostra [**BitmapStreams**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/BitmapStreams) demonstra `ImaageSource.FromStream` como usar o método para carregar um bitmap armazenado como um recurso incorporado e para carregar um bitmap na Web.

### <a name="generating-bitmaps-at-run-time"></a>Gerando bitmaps em tempo de execução

Todas as plataformas Xamarin.Forms suportam o formato de arquivo BMP não compactado, que é fácil de construir em código e, em seguida, armazenar em um `MemoryStream`. Essa técnica permite criar algoritmicamente bitmaps [`BmpMaker`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BmpMaker.cs) em tempo de execução, conforme implementado na classe na biblioteca **Xamrin.FormsBook.Toolkit.**

A amostra "Do It Yourself" [**DiyGradientBitmap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/DiyGradientBitmap) demonstra o uso de `BmpMaker` criar um bitmap com uma imagem gradiente.

## <a name="platform-specific-bitmaps"></a>Bitmaps específicos da plataforma

Todas as plataformas Xamarin.Forms permitem armazenar bitmaps nos conjuntos de aplicativos da plataforma. Quando recuperados por um aplicativo Xamarin.Forms, esses [`FileImageSource`](xref:Xamarin.Forms.FileImageSource)bitmaps de plataforma são do tipo . Você os usa para:

- a [`Icon`](xref:Xamarin.Forms.MenuItem.Icon) propriedade de[`MenuItem`](xref:Xamarin.Forms.MenuItem)
- a [`Icon`](xref:Xamarin.Forms.MenuItem.Icon) propriedade de[`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem)
- a [`Image`](xref:Xamarin.Forms.Button) propriedade de`Button`

Os conjuntos de plataformas já contêm bitmaps para ícones e telas de respingo:

- No projeto iOS, na pasta **Recursos**
- No projeto Android, em subpastas da pasta **Recursos**
- Nos projetos do Windows, na pasta **Ativos** (embora as plataformas do Windows não restrinjam bitmaps a essa pasta)

A amostra [**PlatformBitmaps**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/PlatformBitmaps) usa código para exibir um ícone dos projetos de aplicativos da plataforma.

### <a name="bitmap-resolutions"></a>Resoluções do Bitmap

Todas as plataformas permitem armazenar várias versões de imagens de bitmap para diferentes resoluções de dispositivos. Em tempo de execução, a versão adequada é carregada com base na resolução do dispositivo da tela.

No iOS, esses bitmaps são diferenciados por um sufixo no nome do arquivo:

- Sem sufixo para dispositivos DPI de 160 (1 pixel para a unidade independente do dispositivo)
- '@2x' sufixo para dispositivos De 320 DPI (2 pixels para o DIU)
- '@3x' sufixo para dispositivos De 480 DPI (3 pixels para o DIU)

Um bitmap destinado a ser exibido como quadrado de uma polegada existiria em três versões:

- MyImage.jpg a 160 pixels quadrados
- MyImage@2x.jpga 320 pixels quadrados
- MyImage@3x.jpga 480 pixels quadrados

O programa se refere a este bitmap como MyImage.jpg, mas a versão adequada é recuperada em tempo de execução com base na resolução da tela. Quando sem restrições, o bitmap sempre renderizará em 160 unidades independentes de dispositivos.

Para Android, os bitmaps são armazenados em várias subpastas da pasta **Recursos:**

- ldpi de sedíope (DPI baixo) para dispositivos De 120 DPI (0,75 pixels para o DIU)
- drawable-mdpi (médio) para dispositivos De 160 DPI (1 pixel para o DIU)
- hdpi de desenho (alto) para dispositivos De 240 DPI (1,5 pixels para o DIU)
- cável-xhdpi (extra alto) para dispositivos De 320 DPI (2 pixels para o DIU)
- drawable-xxhdpi (extra extra alto) para dispositivos De 480 DPI (3 pixels para o DIU)
- drawable-xxxhdpi (três altos extras) para dispositivos DPI de 640 (4 pixels para o DIU)

Para um bitmap destinado a ser renderizado em uma polegada quadrada, as várias versões do bitmap terão o mesmo nome, mas um tamanho diferente, e serão armazenadas nessas pastas:

- drawable-ldpi/MyImage.jpg a 120 pixels quadrados
- drawable-mdpi/MyImage.jpg a 160 pixels quadrados
- drawable-hdpi/MyImage.jpg a 240 pixels quadrados
- drawable-xhdpi/MyImage.jpg a 320 pixels quadrados
- drawable-xxhdpi/MyImage.jpg a 480 pixels quadrados
- drawable-xxxhdpi/MyImage.jpg a 640 pixels quadrados

O bitmap será sempre renderizado em 160 unidades independentes do dispositivo. (O modelo de solução Xamarin.Forms padrão inclui apenas as pastas hdpi, xhdpi e xxhdpi.)

O projeto UWP suporta um esquema de nomeação de bitmap que consiste em um fator de dimensionamento de pixels por unidade independente de dispositivo como porcentagem, por exemplo:

- MyImage.scale-200.jpg a 320 pixels quadrados

Apenas algumas porcentagens são válidas. Os programas de amostra para este livro incluem apenas imagens com sufixos **de escala-200,** mas os modelos atuais de solução Xamarin.Forms incluem **escala-100,** **escala-125,** **escala-150**e **escala-400**.

Ao adicionar bitmaps aos projetos da plataforma, a **Ação de Construção** deve ser:

- iOS: **PacoteResource**
- Android: **AndroidResource**
- UWP: **Conteúdo**

A amostra [**ImageTap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ImageTap) cria dois objetos `Image` semelhantes `TapGestureRecognizer` a botões que consistem em elementos com um instalado. Pretende-se que os objetos sejam quadrados de uma polegada. A `Source` propriedade `Image` de `OnPlatform` é `On` definida usando e objetos para referenciar nomes de arquivos potencialmente diferentes nas plataformas. As imagens do bitmap incluem números que indicam seu tamanho de pixel, para que você possa ver qual tamanho o bitmap é recuperado e renderizado.

### <a name="toolbars-and-their-icons"></a>Barras de ferramentas e seus ícones

Um dos principais usos de bitmaps específicos da plataforma é a barra [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) de ferramentas [`ToolbarItems`](xref:Xamarin.Forms.Page.ToolbarItems) Xamarin.Forms, que é construída adicionando objetos à coleção definida por `Page`. `ToobarItem`deriva do [`MenuItem`](xref:Xamarin.Forms.MenuItem) qual herda algumas propriedades.

As propriedades `ToolbarItem` mais importantes são:

- [`Text`](xref:Xamarin.Forms.MenuItem.Text)para texto que pode aparecer dependendo da plataforma e`Order`
- [`Icon`](xref:Xamarin.Forms.MenuItem.Icon)de `FileImageSource` tipo para a imagem que pode aparecer dependendo da plataforma e`Order`
- [`Order`](xref:Xamarin.Forms.ToolbarItem.Order)de [`ToolbarItemOrder`](xref:Xamarin.Forms.ToolbarItemOrder)tipo, uma enumeração [`Default`](xref:Xamarin.Forms.ToolbarItemOrder.Default)com [`Primary`](xref:Xamarin.Forms.ToolbarItemOrder.Primary)três [`Secondary`](xref:Xamarin.Forms.ToolbarItemOrder.Secondary)membros, e .

O número `Primary` de itens deve ser limitado a três ou quatro. Você deve `Text` incluir uma configuração para todos os itens. Para a maioria das `Primary` plataformas, `Icon` apenas os itens exigem `Icon` um, mas o Windows 8.1 requer um para todos os itens. Os ícones devem ser quadrados de 32 unidades independentes do dispositivo. O `FileImageSource` tipo indica que eles são específicos da plataforma.

O `ToolbarItem` fogo [`Clicked`](xref:Xamarin.Forms.MenuItem.Clicked) é um evento quando `Button`tocado, muito parecido com um . `ToolbarItem`também suporta [`Command`](xref:Xamarin.Forms.MenuItem.Command) [`CommandParameter`](xref:Xamarin.Forms.MenuItem.CommandParameter) e propriedades frequentemente usadas em conexão com mVVM. (Ver [capítulo 18, MVVM](chapter18.md)).

Tanto o iOS quanto o Android exigem que [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) uma página que exibe `NavigationPage`uma barra de ferramentas seja uma ou uma página navegada por um . O programa [**ToolbarDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ToolbarDemo) `MainPage` define `App` a propriedade de sua `ContentPage` classe para o [ `NavigationPage` construtor](xref:Xamarin.Forms.NavigationPage.%23ctor(Xamarin.Forms.Page)) com um argumento, e demonstra o manipulador de construção e eventos de uma barra de ferramentas.

### <a name="button-images"></a>Imagens de botão

Você também pode usar bitmaps específicos [`Image`](xref:Xamarin.Forms.Button.Image) da `Button` plataforma para definir a propriedade de um bitmap de 32 unidades independentes de dispositivos quadrados, como demonstrado pela amostra [**ButtonImage.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ButtonImage)

> [!NOTE]
> O uso de imagens nos botões foi aprimorado. Consulte [Usando bitmaps com botões](~/xamarin-forms/user-interface/button.md#using-bitmaps-with-buttons).

## <a name="related-links"></a>Links relacionados

- [Capítulo 13 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch13-Apr2016.pdf)
- [Capítulo 13 amostras](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13)
- [Trabalhando com Imagens](~/xamarin-forms/user-interface/images.md)
- [Usando bitmaps com botões](~/xamarin-forms/user-interface/button.md#using-bitmaps-with-buttons)
