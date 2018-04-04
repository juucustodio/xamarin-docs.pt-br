---
title: Resumo do capítulo 13. Bitmaps
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 5D153857-B6B7-4A14-8FB9-067DE198C2C7
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 76551057abc1abdd150591c0a1be39e9f68c4278
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="summary-of-chapter-13-bitmaps"></a>Resumo do capítulo 13. Bitmaps

O xamarin. Forms [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) elemento exibe um bitmap. Todas as plataformas xamarin. Forms oferecem suporte os formatos de arquivo PNG, JPEG, GIF e BMP.

Bitmaps em xamarin. Forms vir de quatro casas:

- O Web conforme especificado por uma URL
- Inserido como um recurso na biblioteca de classes portátil comuns
- Inserido como um recurso nos projetos de aplicativo de plataforma
- Em qualquer lugar que pode ser referenciado por um .NET `Stream` do objeto, incluindo `MemoryStream`

Recursos de bitmap no PCL são independentes de plataforma, enquanto os recursos de bitmap dos projetos de plataforma são específicos de plataforma.

O bitmap é especificado pela configuração de [ `Source` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Image.Source/) propriedade `Image` para um objeto do tipo [ `ImageSource` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ImageSource/), uma classe abstrata com três derivações:

- [`UriImageSource`](https://developer.xamarin.com/api/type/Xamarin.Forms.UriImageSource/) para acessar um bitmap pela web com base em um `Uri` objeto definido para seu [ `Uri` ](https://developer.xamarin.com/api/property/Xamarin.Forms.UriImageSource.Uri/) propriedade
- [`FileImageSource`](https://developer.xamarin.com/api/type/Xamarin.Forms.FileImageSource/) para acessar um bitmap armazenado em um projeto de aplicativo da plataforma com base em um caminho de pasta e arquivo definido como seu [ `File` ](https://developer.xamarin.com/api/property/Xamarin.Forms.FileImageSource.File/) propriedade
- [`StreamImageSource`](https://developer.xamarin.com/api/type/Xamarin.Forms.StreamImageSource/) para carregar um bitmap usando .NET `Stream` o objeto especificado, retornando um `Stream` de um `Func` definido como seu [ `Stream` ](https://developer.xamarin.com/api/property/Xamarin.Forms.StreamImageSource.Stream/) propriedade

Como alternativa (e mais comum), você pode usar os seguintes métodos estáticos do `ImageSource` classe, todos os quais retornar `ImageSource` objetos:

- [`ImageSource.FromUri`](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromUri/p/System.Uri/) para acessar um bitmap pela web com base em um `Uri` objeto
- [`ImageSource.FromResource`](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromResource/p/System.String/) para acessar um bitmap armazenado como um recurso inserido no aplicativo PCL, ou [ `ImageSource.FromResource` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromResource/p/System.String/System.Type/) ou [ `ImageSource.FromResource` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromResource/p/System.String/System.Reflection.Assembly/) para acessar um bitmap em outro assembly de origem
- [`ImageSource.FromFile`](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromFile/p/System.String/) para acessar um bitmap de um projeto de aplicativo de plataforma
- [`ImageSource.FromStream`](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromStream/p/System.Func%7BSystem.IO.Stream%7D/) para carregar um bitmap com base em um `Stream` objeto

Não há nenhum equivalente a classe de `Image.FromResource` métodos. O `UriImageSource` classe é útil se você precisar controlar o armazenamento em cache. O `FileImageSource` classe é útil em XAML. `StreamImageSource` é útil para o carregamento assíncrono da `Stream` objetos, enquanto `ImageSource.FromStream` é síncrono.

## <a name="platform-independent-bitmaps"></a>Bitmaps independente de plataforma

O [ **WebBitmapCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/WebBitmapCode) projeto carrega um bitmap com o uso de web `ImageSource.FromUri`. O `Image` é definido como o `Content` propriedade o `ContentPage`, portanto, ele é restrito para o tamanho da página. Independentemente do tamanho do bitmap, um restrito `Image` elemento redimensionado para o tamanho de seu contêiner e o bitmap é exibido em seu tamanho máximo no `Image` elemento mantendo a taxa de proporção do bitmap. Áreas do `Image` além de bitmap pode ser colorido com [ `BackgroundColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.BackgroundColor/).

O [ **WebBitmapXaml** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/WebBitmapXaml) exemplo é semelhante, mas simplesmente define o `Source` propriedade para a URL. A conversão é tratada pelo [ `ImageSourceConverter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ImageSourceConverter/) classe.

### <a name="fit-and-fill"></a>O ajuste e preenchimento

Você pode controlar como o bitmap é estendido ao definir o [ `Aspect` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Image.Aspect/) propriedade do `Image` para um dos seguintes membros o [ `Aspect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Aspect/) enumeração:

- [`AspectFit`](https://developer.xamarin.com/api/field/Xamarin.Forms.Aspect.AspectFit/): respeita a taxa de proporção (padrão)
- [`Fill`](https://developer.xamarin.com/api/field/Xamarin.Forms.Aspect.Fill/): preenche a área, não respeita a taxa de proporção
- [`AspectFill`](https://developer.xamarin.com/api/type/Xamarin.Forms.Aspect.AspectFill/): preenche a área, mas respeita a taxa de proporção, realizada por parte do bitmap de corte

### <a name="embedded-resources"></a>Recursos incorporados

Você pode adicionar um arquivo de bitmap para uma PCL ou para uma pasta em que o PCL. Dê a ele um **ação de compilação** de **EmbeddedResource**. O [ **ResourceBitmapCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ResourceBitmapCode) demonstra como usar `ImageSource.FromResource` para carregar o arquivo. O nome de recurso passado para o método consiste do nome do assembly, seguido por um ponto, seguidos pelo nome da pasta opcional e um ponto, seguido pelo nome de arquivo.

O programa define a `VerticalOptions` e `HorizontalOptions` propriedades do `Image` para `LayoutOptions.Center`, que torna o `Image` elemento irrestrita. O `Image` e o bitmap têm o mesmo tamanho:

- No iOS e Android, o `Image` é o tamanho de pixel do bitmap. Há um mapeamento entre pixels de bitmap e tela.
- Nas plataformas de tempo de execução do Windows, o `Image` é o tamanho de pixel do bitmap em unidades independentes de dispositivo. Na maioria dos dispositivos, cada pixel de bitmap ocupa várias pixels da tela.

O [ **StackedBitmap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/StackedBitmap) exemplo coloca um `Image` na vertical `StackLayout` em XAML. Uma extensão de marcação denominada [ `ImageResourceExtension` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter13/StackedBitmap/StackedBitmap/StackedBitmap/ImageResourceExtension.cs) ajuda a fazer referência ao recurso incorporado em XAML. Essa classe carrega apenas os recursos do assembly no qual ela está localizada, portanto ele não pode ser colocado em uma biblioteca.

### <a name="more-on-sizing"></a>Mais informações sobre dimensionamento

Geralmente é desejável bitmaps de tamanho consistente entre todas as plataformas.
Experimentar **StackedBitmap**, você pode definir um `WidthRequest` no `Image` elemento vertical `StackLayout` tornar o tamanho consistente entre as plataformas, mas você pode apenas reduzir o tamanho usando essa técnica.

Você também pode definir o `HeightRequest` criar a imagem tamanhos consistente nas plataformas, mas a largura restrita do bitmap limitará a versatilidade dessa técnica. Para uma imagem na vertical `StackLayout`, `HeightRequest` devem ser evitadas.

A melhor abordagem é começar com um bitmap maior do que a largura de telefone em unidades independentes de dispositivo e definir `WidthRequest` para uma largura desejada em unidades independentes de dispositivo. Isso é demonstrado no [ **DeviceIndBitmapSize** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/DeviceIndBitmapSize) exemplo.

O [ **MadTeaParty** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/MadTeaParty) exibe capítulo 7 de Lewis Carroll *tempo livre de Alice no país das Maravilhas* com as ilustrações originais por John Tenniel:

[![Captura de tela tripla da parte de chá mad](images/ch13fg16-small.png "Mad Hatters chá de catálogo de texto")](images/ch13fg16-large.png#lightbox "Mad Hatters chá de catálogo de texto")

### <a name="browsing-and-waiting"></a>Navegar e aguardando

O [ **ImageBrowser** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ImageBrowser) exemplo permite que o usuário navegue pelas ações imagens armazenadas no site da web Xamarin. Ele usa o .NET `WebRequest` classe para baixar um arquivo JSON com a lista de bitmaps.

O programa usa uma [ `ActivityIndicator` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ActivityIndicator/) para indicar que algo está acontecendo. Como cada bitmap está carregando, somente leitura [ `IsLoading` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Image.IsLoading/) propriedade `Image` é `true`. O `IsLoading` propriedade é feita por uma propriedade ligável, então um `PropertyChanged` evento é acionado quando essa propriedade é alterado. O programa anexa um manipulador para esse evento e usa a configuração atual de `IsLoaded` para definir o [ `IsRunning` ](https://api/property/Xamarin.Forms.ActivityIndicator.IsRunning/) propriedade o `ActivityIndicator`.

## <a name="streaming-bitmaps"></a>Bitmaps de streaming

O `ImageSource.FromStream` método cria um `ImageSource` com base em .NET `Stream`. O método deve ser passado um `Func` objeto que retorna um `Stream` objeto.

### <a name="accessing-the-streams"></a>Acessando os fluxos

O [ **BitmapStreams** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/BitmapStreams) demonstra como usar o `ImaageSource.FromStream` método para carregar um bitmap armazenado como um recurso inserido e para carregar um bitmap em web.

### <a name="generating-bitmaps-at-run-time"></a>Gerar bitmaps em tempo de execução

O formato de arquivo não compactado BMP, que é fácil de criar no código e, em seguida, armazenar em dar suporte a todas as plataformas xamarin. Forms um `MemoryStream`. Essa técnica permite a criação de maneira algorítmica bitmaps em tempo de execução, conforme implementado o [ `BmpMaker` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BmpMaker.cs) classe no **Xamrin.FormsBook.Toolkit** biblioteca.

O "faça você mesmo" [ **DiyGradientBitmap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/DiyGradientBitmap) exemplo demonstra o uso de `BmpMaker` para criar um bitmap com uma imagem de gradiente.

## <a name="platform-specific-bitmaps"></a>Bitmaps específica de plataforma

Todas as plataformas xamarin. Forms permitem armazenar bitmaps nos assemblies de plataforma do aplicativo. Quando for recuperada por um aplicativo xamarin. Forms, esses bitmaps de plataforma são do tipo [ `FileImageSource` ](https://developer.xamarin.com/api/type/Xamarin.Forms.FileImageSource/). Você usá-los para:

- o [ `Icon` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MenuItem.Icon/) propriedade [`MenuItem`](https://developer.xamarin.com/api/type/Xamarin.Forms.MenuItem/)
- o [ `Icon` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ToolbarItem.Icon/) propriedade [`ToolbarItem`](https://developer.xamarin.com/api/type/Xamarin.Forms.ToolbarItem/)
- o [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) propriedade `Button`

Os assemblies de plataforma já conterem bitmaps, ícones e telas:

- No projeto do iOS, no **recursos** pasta
- No projeto Android, nas subpastas do **recursos** pasta
- Em projetos do Windows, no **ativos** pasta (embora as plataformas do Windows não restringem bitmaps para essa pasta)

O [ **PlatformBitmaps** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/PlatformBitmaps) exemplo usa o código para exibir um ícone de projetos de aplicativo de plataforma.

### <a name="bitmap-resolutions"></a>Resoluções de bitmap

Todas as plataformas permitem armazenar várias versões de imagens de bitmap para resoluções de dispositivos diferentes. Em tempo de execução, a versão correta é carregada com base na resolução da tela do dispositivo.

No iOS, esses bitmaps são diferenciados por um sufixo de nome de arquivo:

- Nenhum sufixo para dispositivos de 160 DPI (1 pixel para a unidade independente de dispositivo)
- '@2x' sufixo para dispositivos de 320 DPI (2 pixels para o DIU)
- '@3x' sufixo para dispositivos DPI 480 (3 pixels para o DIU)

Um bitmap que se destina a ser exibida como uma polegada quadrada existirá em três versões:

- MyImage.jpg em 160 pixels quadrados
- MyImage@2x.jpg em 320 pixels quadrados
- MyImage@3x.jpg 480 pixels quadrados

O programa faz referência a este bitmap como MyImage.jpg, mas a versão correta é recuperada em tempo de execução com base na resolução da tela. Quando irrestritas, o bitmap sempre será renderizado em unidades de 160 independente de dispositivo.

Para o Android, os bitmaps são armazenados em várias subpastas do **recursos** pasta:

- drawable-ldpi (baixa DPI) para dispositivos de 120 DPI (0,75 pixels para o DIU)
- drawable-mdpi (médio) para dispositivos de 160 DPI (1 pixel para o DIU)
- drawable-hdpi (alto) para dispositivos de 240 DPI (1,5 pixels para o DIU)
- drawable-xhdpi (muito alta) para dispositivos de 320 DPI (2 pixels para o DIU)
- drawable-xxhdpi (extra muito alta) para dispositivos DPI 480 (3 pixels para o DIU)
- drawable-xxxhdpi (três mais altos) para dispositivos DPI 640 (4 pixels para o DIU)

Para um bitmap que se destina a ser renderizado em uma polegada quadrada, as várias versões do bitmap terão o mesmo nome, mas um tamanho diferente e ser armazenadas nessas pastas:

- drawable-ldpi/MyImage.jpg em 120 pixels quadrados
- drawable-mdpi/MyImage.jpg em 160 pixels quadrados
- drawable-hdpi/MyImage.jpg em 240 pixels quadrados
- drawable-xhdpi/MyImage.jpg em 320 pixels quadrados
- drawable-xxhdpi/MyImage.jpg em 480 pixels quadrados
- drawable-xxxhdpi/MyImage.jpg em 640 pixels quadrados

O bitmap sempre será renderizado em unidades de 160 independente de dispositivo. (O modelo de solução padrão xamarin. Forms só inclui pastas xxhdpi, hdpi e xhdpi.)

Os projetos de tempo de execução do Windows oferecem suporte a um bitmap de nomenclatura do esquema que consiste em um fator de escala em pixels por unidade independente de dispositivo como uma porcentagem, por exemplo:

- MyImage.scale-200.jpg em 320 pixels quadrados

Somente alguns porcentagens são válidas. Os programas de exemplo para este catálogo incluem apenas imagens com **escala 200** sufixos, mas modelos de solução atuais xamarin. Forms incluem **escala 100**, **escala 125**, **escala 150**, e **400 escala**. 

Ao adicionar bitmaps para os projetos de plataforma, o **ação de compilação** deve ser:

- iOS: **BundleResource**
- Android: **AndroidResource**
- Tempo de execução do Windows: **conteúdo**

O [ **ImageTap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ImageTap) exemplo cria dois objetos de botão como consiste em `Image` elementos com um `TapGestureRecognizer` instalado. Ele destina-se que os objetos ser polegadas um quadrado. O `Source` propriedade `Image` é definido usando `OnPlatform` e `On` objetos para fazer referência a nomes de arquivo potencialmente diferentes plataformas. As imagens de bitmap incluem números que indica o tamanho de pixel, para que você pode ver quais bitmap de tamanho é recuperado e processado.

### <a name="toolbars-and-their-icons"></a>Barras de ferramentas e seus ícones

Um dos principais usos de bitmaps específica de plataforma é a barra de ferramentas do xamarin. Forms, que é construída com a adição de [ `ToolbarItem` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ToolbarItem/) objetos para o [ `ToolbarItems` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.ToolbarItems/) coleção definida pelo `Page`. `ToobarItem` deriva [ `MenuItem` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MenuItem/) da qual ela herda algumas propriedades.

O mais importante `ToolbarItem` propriedades são:

- [`Text`](https://developer.xamarin.com/api/property/Xamarin.Forms.MenuItem.Text/) para o texto que pode aparecer dependendo da plataforma e `Order`
- [`Icon`](https://developer.xamarin.com/api/property/Xamarin.Forms.ToolbarItem.Icon/) tipo `FileImageSource` para a imagem que pode aparecer dependendo da plataforma e `Order`
- [`Order`](https://developer.xamarin.com/api/property/Xamarin.Forms.ToolbarItem.Order/) tipo [ `ToolbarItemOrder` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ToolbarItemOrder/), uma enumeração com três membros, [ `Default` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ToolbarItemOrder.Default/), [ `Primary` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ToolbarItemOrder.Primary/), e [ `Secondary` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ToolbarItemOrder.Secondary/).

O número de `Primary` itens devem ser limitados a três ou quatro. Você deve incluir um `Text` configuração para todos os itens. Para a maioria das plataformas, somente o `Primary` itens que exigem um `Icon` mas Windows 8.1 requer um `Icon` para todos os itens. Os ícones devem ser 32 unidades independentes de dispositivo quadradas. O `FileImageSource` tipo indica que são específicos de plataforma.

O `ToolbarItem` dispara uma [ `Clicked` ](https://developer.xamarin.com/api/event/Xamarin.Forms.MenuItem.Clicked/) evento quando tocado, semelhante a um `Button`. `ToolbarItem` também oferece suporte a [ `Command` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ToolbarItem.Command/) e [ `CommandParameter` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ToolbarItem.CommandParameter/) propriedades usadas em conjunto com MVVM. (Consulte [capítulo 18, MVVM](chapter18.md)).

IOS e Android exigem que uma página que exibe uma barra de ferramentas seja um [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) ou uma página para onde navegado por uma `NavigationPage`. O [ **ToolbarDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ToolbarDemo) conjuntos de programa a `MainPage` propriedade do seu `App` de classe para o [ `NavigationPage` construtor](https://developer.xamarin.com/api/constructor/Xamarin.Forms.NavigationPage.NavigationPage/p/Xamarin.Forms.Page/) com um `ContentPage` argumento e demonstra o manipulador de evento e a construção de uma barra de ferramentas.

### <a name="button-images"></a>Imagens de botão

Você também pode usar bitmaps específico da plataforma para definir o [ `Image` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.Image/) propriedade de `Button` para um bitmap de quadrado 32 unidades de independentes de dispositivo, conforme demonstrado a [ **ButtonImage** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ButtonImage) exemplo.



## <a name="related-links"></a>Links relacionados

- [Capítulo 13 de texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch13-Apr2016.pdf)
- [Exemplos de capítulo 13](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13)
- [Trabalhando com imagens](~/xamarin-forms/user-interface/images.md)
