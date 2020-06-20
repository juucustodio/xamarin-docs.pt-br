---
title: Imagens emXamarin.Forms
description: As imagens podem ser compartilhadas entre plataformas com o Xamarin.Forms , elas podem ser carregadas especificamente para cada plataforma ou podem ser baixadas para exibição.
ms.prod: xamarin
ms.assetid: C025AB53-05CC-49BA-9815-75D6DF9E40B7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/19/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 7117bb809c43ab5edb67e8367840b17cd1d97ef9
ms.sourcegitcommit: c000c0ed15b7b2ef2a8f46a39171e11b6d9f8a5d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84980084"
---
# <a name="images-in-xamarinforms"></a>Imagens emXamarin.Forms

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithimages)

_As imagens podem ser compartilhadas entre plataformas com o Xamarin.Forms , elas podem ser carregadas especificamente para cada plataforma ou podem ser baixadas para exibição._

As imagens são uma parte crucial da navegação, da usabilidade e da identidade visual do aplicativo. Xamarin.Formsos aplicativos precisam ser capazes de compartilhar imagens em todas as plataformas, mas também podem exibir imagens diferentes em cada plataforma.

As imagens específicas da plataforma também são necessárias para ícones e telas de abertura; Eles precisam ser configurados por plataforma.

## <a name="display-images"></a>Exibir imagens

Xamarin.Formsusa a [`Image`](xref:Xamarin.Forms.Image) exibição para exibir imagens em uma página. Ele tem várias propriedades importantes:

- [`Source`](xref:Xamarin.Forms.Image.Source)-Uma [`ImageSource`](xref:Xamarin.Forms.ImageSource) instância, o arquivo, o URI ou o recurso, que define a imagem a ser exibida.
- [`Aspect`](xref:Xamarin.Forms.Image.Aspect)-Como dimensionar a imagem dentro dos limites que ela está sendo exibida em (seja para alongar, cortar ou Letterbox).

[`ImageSource`](xref:Xamarin.Forms.ImageSource)as instâncias podem ser obtidas usando métodos estáticos para cada tipo de origem de imagem:

- [`FromFile`](xref:Xamarin.Forms.ImageSource.FromFile(System.String))-Requer um filename ou caminho de arquivo que possa ser resolvido em cada plataforma.
- [`FromUri`](xref:Xamarin.Forms.ImageSource.FromUri(System.Uri))– Requer um objeto Uri, por exemplo,  `new Uri("http://server.com/image.jpg")` .
- [`FromResource`](xref:Xamarin.Forms.ImageSource.FromResource*)-Requer um identificador de recurso para um arquivo de imagem inserido no aplicativo ou .NET Standard projeto de biblioteca, com uma **ação de compilação: EmbeddedResource**.
- [`FromStream`](xref:Xamarin.Forms.ImageSource.FromStream(System.Func{System.IO.Stream}))– Requer um fluxo que forneça dados de imagem.

A [`Aspect`](xref:Xamarin.Forms.Image.Aspect) propriedade determina como a imagem será dimensionada para se ajustar à área de exibição:

- [`Fill`](xref:Xamarin.Forms.Aspect.Fill)– Alonga a imagem para preencher completamente e exatamente a área de exibição. Isso pode resultar na distorção da imagem.
- [`AspectFill`](xref:Xamarin.Forms.Aspect.AspectFill)-Corta a imagem para que ela preencha a área de exibição enquanto preserva o aspecto (ou seja, sem distorção).
- [`AspectFit`](xref:Xamarin.Forms.Aspect.AspectFit)-Letterboxes a imagem (se necessário) para que toda a imagem caiba na área de exibição, com espaço em branco adicionado à parte superior/inferior ou aos lados, dependendo se a imagem for larga ou comprida.

As imagens podem ser carregadas de um [arquivo local](#local-images), de um [recurso inserido](#embedded-images), [baixado](#download-images)ou carregado a partir de um fluxo. Além disso, os ícones de fonte podem ser exibidos pelo [`Image`](xref:Xamarin.Forms.Image) modo de exibição especificando os dados de ícone de fonte em um `FontImageSource` objeto. Para obter mais informações, consulte [exibir ícones de fonte](~/xamarin-forms/user-interface/text/fonts.md#display-font-icons) no guia [fontes](~/xamarin-forms/user-interface/text/fonts.md) .

## <a name="local-images"></a>Imagens locais

Os arquivos de imagem podem ser adicionados a cada projeto de aplicativo e referenciados do Xamarin.Forms código compartilhado. Esse método de distribuição de imagens é necessário quando as imagens são específicas da plataforma, como ao usar resoluções diferentes em diferentes plataformas ou designs ligeiramente diferentes.

Para usar uma única imagem em todos os aplicativos, *o mesmo nome de arquivo deve ser usado em todas as plataformas*e deve ser um nome de recurso Android válido (ou seja, apenas letras minúsculas, numerais, sublinhado e o período são permitidos).

- **Ios** -a maneira preferida de gerenciar e dar suporte a imagens desde o Ios 9 é usar **conjuntos de imagens de catálogo de ativos**, que devem conter todas as versões de uma imagem que são necessárias para dar suporte a vários dispositivos e fatores de escala para um aplicativo. Para obter mais informações, consulte [adicionando imagens a um conjunto de imagens do catálogo de ativos](~/ios/app-fundamentals/images-icons/displaying-an-image.md).
- Imagens no local do **Android** no diretório de **recursos/empates** com a **ação de compilação: AndroidResource**. Versões de alto e baixo DPI de uma imagem também podem ser fornecidas (em subdiretórios de **recursos** nomeados apropriadamente, como **Drawable-ldpi**, **estilododesenho-hdpi**e **estilododesenho-xhdpi**).
- **Plataforma universal do Windows (UWP)** – por padrão, as imagens devem ser colocadas no diretório raiz do aplicativo com a **ação de compilação: conteúdo**. Como alternativa, as imagens podem ser colocadas em um diretório diferente, que é especificado com uma plataforma específica. Para obter mais informações, consulte [diretório de imagem padrão no Windows](~/xamarin-forms/platform/windows/default-image-directory.md).

> [!IMPORTANT]
> Antes do iOS 9, as imagens normalmente eram colocadas na pasta de **recursos** com a **ação de Build: BundleResource**. No entanto, esse método de trabalhar com imagens em um aplicativo iOS foi preterido pela Apple. Para obter mais informações, consulte [tamanhos de imagem e nomes de](~/ios/app-fundamentals/images-icons/displaying-an-image.md)dados.

Obedecer a essas regras para a nomeação e o posicionamento de arquivos permite que o XAML a seguir carregue e exiba a imagem em todas as plataformas:

```xaml
<Image Source="waterfront.jpg" />
```

O código C# equivalente é o seguinte:

```csharp
var image = new Image { Source = "waterfront.jpg" };
```

As capturas de tela a seguir mostram o resultado da exibição de uma imagem local em cada plataforma:

[![Aplicativo de exemplo exibindo uma imagem local](images-images/local-sml.png)](images-images/local.png#lightbox)

Para obter mais flexibilidade, a `Device.RuntimePlatform` propriedade pode ser usada para selecionar um caminho ou arquivo de imagem diferente para algumas ou todas as plataformas, conforme mostrado neste exemplo de código:

```csharp
image.Source = Device.RuntimePlatform == Device.Android
                ? ImageSource.FromFile("waterfront.jpg")
                : ImageSource.FromFile("Images/waterfront.jpg");
```

> [!IMPORTANT]
> Para usar o mesmo nome de arquivo de imagem em todas as plataformas, o nome deve ser válido em todas as plataformas. O Android drawables tem restrições de nomenclatura – somente letras minúsculas, números, sublinhados e pontos são permitidos – e para compatibilidade entre plataformas, isso também deve ser seguido em todas as outras plataformas. O nome de arquivo de exemplo **waterfront.png** segue as regras, mas exemplos de nomes de arq inválidos incluem "água front.png", "WaterFront.png", "water-front.png" e "wåterfront.png".

### <a name="native-resolutions-retina-and-high-dpi"></a>Resoluções nativas (retina e High-DPI)

o iOS, o Android e o UWP incluem suporte para diferentes resoluções de imagem, em que o sistema operacional escolhe a imagem apropriada em tempo de execução com base nos recursos do dispositivo. Xamarin.Formsusa as APIs das plataformas nativas para carregar imagens locais, portanto, ela automaticamente oferece suporte a resoluções alternativas se os arquivos forem nomeados e localizados corretamente no projeto.

A maneira preferida de gerenciar imagens desde o iOS 9 é arrastar imagens para cada resolução necessária para o conjunto de imagens apropriado do catálogo de ativos. Para obter mais informações, consulte [adicionando imagens a um conjunto de imagens do catálogo de ativos](~/ios/app-fundamentals/images-icons/displaying-an-image.md).

Antes do iOS 9, as versões da retina da imagem poderiam ser colocadas na pasta de **recursos** -duas e três vezes a resolução com um **@2x** ou **@3x** sufixos no nome do arquivo antes da extensão de arquivos (por exemplo, **myimage@2x.png**). No entanto, esse método de trabalhar com imagens em um aplicativo iOS foi preterido pela Apple. Para obter mais informações, consulte [tamanhos de imagem e nomes de](~/ios/app-fundamentals/images-icons/displaying-an-image.md)dados.

As imagens de resolução alternativa do Android devem ser colocadas em [diretórios especialmente nomeados](https://developer.android.com/guide/practices/screens_support.html) no projeto do Android, conforme mostrado na seguinte captura de tela:

[![Local da imagem de várias resoluções do Android](images-images/xs-highdpisolution-sml.png)](images-images/xs-highdpisolution.png#lightbox)

Os nomes de arquivo [de imagem UWP podem ter `.scale-xxx` um sufixo antes da extensão de arquivo](https://docs.microsoft.com/windows/uwp/app-resources/images-tailored-for-scale-theme-contrast), em que `xxx` é a porcentagem de dimensionamento aplicada ao ativo, por exemplo, **myimage.scale-200.png**. As imagens podem então ser referenciadas em código ou XAML sem o modificador de escala, por exemplo, apenas **myimage.png**. A plataforma selecionará a escala de ativos apropriada mais próxima com base no DPI atual do vídeo.

### <a name="additional-controls-that-display-images"></a>Controles adicionais que exibem imagens

Alguns controles têm propriedades que exibem uma imagem, como:

- [`Button`](xref:Xamarin.Forms.Button)tem uma [`ImageSource`](xref:Xamarin.Forms.Button.ImageSource) propriedade que pode ser definida como uma imagem de bitmap a ser exibida no `Button` . Para obter mais informações, consulte [usando bitmaps com botões](~/xamarin-forms/user-interface/button.md#using-bitmaps-with-buttons).
- [`ImageButton`](xref:Xamarin.Forms.Button)tem uma [`Source`](xref:Xamarin.Forms.ImageButton.Source) propriedade que pode ser definida como a imagem a ser exibida no `ImageButton` . Para obter mais informações, consulte [definindo a origem da imagem](~/xamarin-forms/user-interface/imagebutton.md#setting-the-image-source).
- [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem)tem uma [`IconImageSource`](xref:Xamarin.Forms.MenuItem.IconImageSource) propriedade que pode ser definida como uma imagem que é carregada de um arquivo, recurso inserido, URI ou fluxo.
- [`ImageCell`](xref:Xamarin.Forms.ImageCell)tem uma [`ImageSource`](xref:Xamarin.Forms.ImageCell.ImageSource) propriedade que pode ser definida como uma imagem recuperada de um arquivo, recurso inserido, URI ou fluxo.
- [`Page`](xref:Xamarin.Forms.Page). Qualquer tipo de página que deriva de `Page` tem [`IconImageSource`](xref:Xamarin.Forms.Page.IconImageSource) [`BackgroundImageSource`](xref:Xamarin.Forms.Page.BackgroundImageSource) Propriedades e, que pode ser atribuído a um arquivo, recurso inserido, URI ou fluxo. Em determinadas circunstâncias, como quando um [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) está exibindo um [`ContentPage`](xref:Xamarin.Forms.ContentPage) , o ícone será exibido se houver suporte da plataforma.

  > [!IMPORTANT]
  > No iOS, a [`Page.IconImageSource`](xref:Xamarin.Forms.Page.IconImageSource) propriedade não pode ser populada de uma imagem em um conjunto de imagens do catálogo de ativos. Em vez disso, carregue imagens de ícone para a `Page.IconImageSource` propriedade de um arquivo, recurso inserido, URI ou fluxo.

## <a name="embedded-images"></a>Imagens inseridas

Imagens inseridas também são fornecidas com um aplicativo (como imagens locais), mas em vez de ter uma cópia da imagem na estrutura de arquivos de cada aplicativo, o arquivo de imagem é inserido no assembly como um recurso. Esse método de distribuição de imagens é recomendado quando imagens idênticas são usadas em cada plataforma e é especialmente adequada para a criação de componentes, pois a imagem é agrupada com o código.

Para inserir uma imagem em um projeto, clique com o botão direito do mouse para adicionar novos itens e selecione a imagem/s que você deseja adicionar. Por padrão, a imagem terá a **ação de compilação: nenhuma**; Isso precisa ser definido para a **ação de Build: EmbeddedResource**.

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![Definir ação de compilação para recurso inserido](images-images/vs-buildaction-sml.png)](images-images/vs-buildaction.png#lightbox)

A **ação de compilação** pode ser exibida e alterada na janela **Propriedades** de um arquivo.

Neste exemplo, a ID do recurso é **WorkingWithImages.beach.jpg**.
O IDE gerou esse padrão concatenando o **namespace padrão** para esse projeto com o nome de arquivo, usando um ponto (.) entre cada valor.
<!-- https://msdn.microsoft.com/library/ms950960.aspx -->

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

![](images-images/xs-buildaction.png "Set Build Action: EmbeddedResource")

A **ação de Build** também pode ser exibida e alterada no painel de **Propriedades** de um arquivo.
Esse painel mostra a **ID de recurso** usada para referenciar o recurso no código. Na captura de tela abaixo, a **ID do recurso** é **WorkingWithImages.beach.jpg**.
O IDE gerou esse padrão concatenando o **namespace padrão** para esse projeto com o nome de arquivo, usando um ponto (.) entre cada valor.
Essa ID pode ser editada no painel de **Propriedades** , mas, para esses exemplos, o valor **WorkingWithImages.beach.jpg** será usado.

[![Painel de propriedades do recurso inserido](images-images/xs-embeddedproperties-sml.png)](images-images/xs-embeddedproperties.png#lightbox)

-----

Se você inserir imagens incorporadas em pastas dentro de seu projeto, os nomes de pastas também serão separados por pontos (.) na ID do recurso. Mover a imagem de **beach.jpg** para uma pasta chamada **myImages** resultaria em uma ID de recurso de **WorkingWithImages.MyImages.beach.jpg**

O código para carregar uma imagem inserida simplesmente passa a **ID do recurso** para o [`ImageSource.FromResource`](xref:Xamarin.Forms.ImageSource.FromResource*) método, conforme mostrado abaixo:

```csharp
var embeddedImage = new Image {
      Source = ImageSource.FromResource(
        "WorkingWithImages.beach.jpg",
        typeof(EmbeddedImages).GetTypeInfo().Assembly
      ) };
```

> [!NOTE]
> Para dar suporte à exibição de imagens inseridas no modo de liberação no Plataforma Universal do Windows, é necessário usar a sobrecarga de `ImageSource.FromResource` que especifica o assembly de origem no qual Pesquisar a imagem.

Atualmente, não há conversão implícita para identificadores de recurso. Em vez disso, você deve usar [`ImageSource.FromResource`](xref:Xamarin.Forms.ImageSource.FromResource*) ou `new ResourceImageSource()` para carregar imagens inseridas.

As capturas de tela a seguir mostram o resultado da exibição de uma imagem inserida em cada plataforma:

[![Aplicativo de exemplo exibindo uma imagem inserida](images-images/resource-sml.png)](images-images/resource.png#lightbox)

### <a name="xaml"></a>XAML

Como não há nenhum conversor de tipo interno de `string` para `ResourceImageSource` , esses tipos de imagens não podem ser carregados NATIVAMENTE pelo XAML. Em vez disso, uma extensão de marcação XAML personalizada simples pode ser escrita para carregar imagens usando uma **ID de recurso** especificada em XAML:

```csharp
[ContentProperty (nameof(Source))]
public class ImageResourceExtension : IMarkupExtension
{
 public string Source { get; set; }

 public object ProvideValue (IServiceProvider serviceProvider)
 {
   if (Source == null)
   {
     return null;
   }

   // Do your translation lookup here, using whatever method you require
   var imageSource = ImageSource.FromResource(Source, typeof(ImageResourceExtension).GetTypeInfo().Assembly);

   return imageSource;
 }
}
```

> [!NOTE]
> Para dar suporte à exibição de imagens inseridas no modo de liberação no Plataforma Universal do Windows, é necessário usar a sobrecarga de `ImageSource.FromResource` que especifica o assembly de origem no qual Pesquisar a imagem.

Para usar essa extensão, adicione um personalizado `xmlns` ao XAML, usando o namespace correto e os valores de assembly para o projeto. A origem da imagem pode ser definida usando essa sintaxe: `{local:ImageResource WorkingWithImages.beach.jpg}` . Um exemplo de XAML completo é mostrado abaixo:

```xaml
<?xml version="1.0" encoding="UTF-8" ?>
<ContentPage
   xmlns="http://xamarin.com/schemas/2014/forms"
   xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
   xmlns:local="clr-namespace:WorkingWithImages;assembly=WorkingWithImages"
   x:Class="WorkingWithImages.EmbeddedImagesXaml">
 <StackLayout VerticalOptions="Center" HorizontalOptions="Center">
   <!-- use a custom Markup Extension -->
   <Image Source="{local:ImageResource WorkingWithImages.beach.jpg}" />
 </StackLayout>
</ContentPage>
```

### <a name="troubleshoot-embedded-images"></a>Solucionar problemas de imagens inseridas

#### <a name="debug-code"></a>Depurar o código

Como, às vezes, é difícil entender por que um determinado recurso de imagem não está sendo carregado, o código de depuração a seguir pode ser adicionado temporariamente a um aplicativo para ajudar a confirmar se os recursos estão configurados corretamente. Ele produzirá todos os recursos conhecidos inseridos no assembly fornecido para o **console** para ajudar a depurar problemas de carregamento de recursos.

```csharp
using System.Reflection;
// ...
// NOTE: use for debugging, not in released app code!
var assembly = typeof(EmbeddedImages).GetTypeInfo().Assembly;
foreach (var res in assembly.GetManifestResourceNames())
{
    System.Diagnostics.Debug.WriteLine("found resource: " + res);
}
```

#### <a name="images-embedded-in-other-projects"></a>Imagens inseridas em outros projetos

Por padrão, o `ImageSource.FromResource` método só procura imagens no mesmo assembly que o código que chama o `ImageSource.FromResource` método. Usando o código de depuração acima, você pode determinar quais assemblies contêm um recurso específico alterando a `typeof()` instrução para um `Type` conhecido a ser em cada assembly.

No entanto, o assembly de origem que está sendo pesquisado para uma imagem inserida pode ser especificado como um argumento para o `ImageSource.FromResource` método:

```csharp
var imageSource = ImageSource.FromResource("filename.png",
            typeof(MyClass).GetTypeInfo().Assembly);
```

## <a name="download-images"></a>Baixar imagens

As imagens podem ser baixadas automaticamente para exibição, conforme mostrado no XAML a seguir:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
       x:Class="WorkingWithImages.DownloadImagesXaml">
  <StackLayout VerticalOptions="Center" HorizontalOptions="Center">
    <Label Text="Image UriSource Xaml" />
    <Image Source="https://aka.ms/campus.jpg" />
    <Label Text="campus.jpg gets downloaded from microsoft.com" />
  </StackLayout>
</ContentPage>
```

O código C# equivalente é o seguinte:

```csharp
var webImage = new Image {
     Source = ImageSource.FromUri(
        new Uri("https://aka.ms/campus.jpg")
     ) };
```

O [`ImageSource.FromUri`](xref:Xamarin.Forms.ImageSource.FromUri(System.Uri)) método requer um `Uri` objeto e retorna um novo [`UriImageSource`](xref:Xamarin.Forms.UriImageSource) que lê do `Uri` .

Há também uma conversão implícita para cadeias de caracteres de URI, portanto, o exemplo a seguir também funcionará:

```csharp
webImage.Source = "https://aka.ms/campus.jpg";
```

As capturas de tela a seguir mostram o resultado da exibição de uma imagem remota em cada plataforma:

[![Aplicativo de exemplo exibindo uma imagem baixada](images-images/download-sml.png)](images-images/download.png#lightbox)

### <a name="downloaded-image-caching"></a>Cache de imagem baixado

Um [`UriImageSource`](xref:Xamarin.Forms.UriImageSource) também dá suporte ao cache de imagens baixadas, configuradas por meio das seguintes propriedades:

- [`CachingEnabled`](xref:Xamarin.Forms.UriImageSource.CachingEnabled)-Se o Caching está habilitado ( `true` por padrão).
- [`CacheValidity`](xref:Xamarin.Forms.UriImageSource.CacheValidity)-A `TimeSpan` que define por quanto tempo a imagem será armazenada localmente.

O Caching é habilitado por padrão e armazenará a imagem localmente por 24 horas. Para desabilitar o cache de uma imagem específica, crie uma instância da origem da imagem da seguinte maneira:

```csharp
image.Source = new UriImageSource { CachingEnabled = false, Uri = new Uri("https://server.com/image") };
```

Para definir um período de cache específico (por exemplo, 5 dias), crie uma instância da origem da imagem da seguinte maneira:

```csharp
webImage.Source = new UriImageSource
{
    Uri = new Uri("https://aka.ms/campus.jpg"),
    CachingEnabled = true,
    CacheValidity = new TimeSpan(5,0,0,0)
};
```

O cache interno facilita muito o suporte a cenários como listas de rolagem de imagens, onde você pode definir (ou associar) uma imagem em cada célula e deixar o cache interno cuidar da recarga da imagem quando a célula é rolada de volta para a exibição.

## <a name="animated-gifs"></a>GIFs animados

Xamarin.Formsinclui suporte para a exibição de GIFs pequenos e animados. Isso é feito definindo a [`Image.Source`](xref:Xamarin.Forms.Image.Source) propriedade como um arquivo GIF animado:

```xaml
<Image Source="demo.gif" />
```

> [!IMPORTANT]
> Embora o suporte a GIF animado no Xamarin.Forms inclua a capacidade de baixar arquivos, ele não dá suporte a caching ou GIFs animados de streaming.

Por padrão, quando um GIF animado é carregado, ele não será reproduzido. Isso ocorre porque a `IsAnimationPlaying` propriedade, que controla se um GIF animado está tocando ou parado, tem um valor padrão de `false` . Essa propriedade, do tipo `bool` , é apoiada por um [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objeto, o que significa que ele pode ser o destino de uma vinculação de dados e com o estilo.

Portanto, quando um GIF animado é carregado, ele não será reproduzido até que a `IsAnimationPlaying` propriedade seja definida como `true` . A reprodução pode ser interrompida definindo a `IsAnimationPlaying` propriedade como `false` . Observe que essa propriedade não tem nenhum efeito ao exibir uma fonte de imagem não GIF.

> [!NOTE]
> No Android, o suporte a GIF animado requer que seu aplicativo esteja usando renderizadores rápidos e não funcionará se você tiver optado por usar os renderizadores herdados.
> No UWP, o suporte a GIF animado requer uma versão mínima da atualização de aniversário do Windows 10 (versão 1607).

## <a name="icons-and-splash-screens"></a>Ícones e telas de abertura

Embora não esteja relacionado à [`Image`](xref:Xamarin.Forms.Image) exibição, os ícones de aplicativos e as telas de abertura também são um importante uso das imagens em Xamarin.Forms projetos.

A definição de ícones e telas de abertura para Xamarin.Forms aplicativos é feita em cada um dos projetos de aplicativo. Isso significa gerar imagens corretamente dimensionadas para iOS, Android e UWP. Essas imagens devem ser nomeadas e localizadas de acordo com os requisitos de cada plataforma.

## <a name="icons"></a>Ícones

Consulte as diretrizes de [trabalho do IOS trabalhando com imagens](~/ios/app-fundamentals/images-icons/index.md), [Google iconografia](https://developer.android.com/design/style/iconography.html)e [UWP para ver os ativos de bloco e ícone](/windows/uwp/controls-and-patterns/tiles-and-notifications-app-assets/) para obter mais informações sobre como criar esses recursos de aplicativo.

Além disso, os ícones de fonte podem ser exibidos pelo [`Image`](xref:Xamarin.Forms.Image) modo de exibição especificando os dados de ícone de fonte em um `FontImageSource` objeto. Para obter mais informações, consulte [exibir ícones de fonte](~/xamarin-forms/user-interface/text/fonts.md#display-font-icons) no guia [fontes](~/xamarin-forms/user-interface/text/fonts.md) .

## <a name="splash-screens"></a>Telas iniciais

Somente os aplicativos iOS e UWP exigem uma tela inicial (também chamada de tela de inicialização ou imagem padrão).

Consulte a documentação do [Ios trabalhando com imagens](~/ios/app-fundamentals/images-icons/index.md) e [telas de abertura](/windows/uwp/launch-resume/splash-screens/) no centro de desenvolvimento do Windows.

## <a name="related-links"></a>Links relacionados

- [WorkingWithImages (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithimages)
- [iOS trabalhando com imagens](~/ios/app-fundamentals/images-icons/index.md)
- [Iconografia Android](https://developer.android.com/design/style/iconography.html)
- [Diretrizes para ativos de bloco e ícone](/windows/uwp/controls-and-patterns/tiles-and-notifications-app-assets/)
