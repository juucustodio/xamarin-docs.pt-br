---
title: Imagens no xamarin. Forms
description: Imagens podem ser compartilhadas entre plataformas com o xamarin. Forms, podem ser carregados especificamente para cada plataforma ou podem ser baixados para exibição.
ms.prod: xamarin
ms.assetid: C025AB53-05CC-49BA-9815-75D6DF9E40B7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/04/2019
ms.openlocfilehash: 255d3f2f532e4899b1a890405af942a7ca2da8ea
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79304117"
---
# <a name="images-in-xamarinforms"></a>Imagens no xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithimages)

_As imagens podem ser compartilhadas entre plataformas com o Xamarin. Forms, elas podem ser carregadas especificamente para cada plataforma ou podem ser baixadas para exibição._

Imagens são uma parte crucial da navegação do aplicativo, a usabilidade e a identidade visual. Aplicativos xamarin. Forms precisam ser capaz de compartilhar imagens entre todas as plataformas, mas também pode exibir imagens diferentes em cada plataforma.

Imagens específicas da plataforma também são necessárias para os ícones e telas de abertura; eles precisam ser configurados em uma base por plataforma.

## <a name="display-images"></a>Exibir imagens

O Xamarin. Forms usa a exibição [`Image`](xref:Xamarin.Forms.Image) para exibir imagens em uma página. Ele tem duas propriedades importantes:

- [`Source`](xref:Xamarin.Forms.Image.Source) -uma instância de [`ImageSource`](xref:Xamarin.Forms.ImageSource) , arquivo, URI ou recurso, que define a imagem a ser exibida.
- [`Aspect`](xref:Xamarin.Forms.Image.Aspect) -como dimensionar a imagem dentro dos limites que ela está sendo exibida em (seja para alongar, cortar ou Letterbox).

[`ImageSource`](xref:Xamarin.Forms.ImageSource) instâncias podem ser obtidas usando métodos estáticos para cada tipo de origem de imagem:

- [`FromFile`](xref:Xamarin.Forms.ImageSource.FromFile(System.String)) -requer um filename ou caminho de arquivo que possa ser resolvido em cada plataforma.
- [`FromUri`](xref:Xamarin.Forms.ImageSource.FromUri(System.Uri)) -requer um objeto Uri, por exemplo,  `new Uri("http://server.com/image.jpg")` .
- [`FromResource`](xref:Xamarin.Forms.ImageSource.FromResource*) -requer um identificador de recurso para um arquivo de imagem inserido no aplicativo ou .net Standard projeto de biblioteca, com uma **ação de compilação: EmbeddedResource**.
- [`FromStream`](xref:Xamarin.Forms.ImageSource.FromStream(System.Func{System.IO.Stream})) -requer um fluxo que forneça dados de imagem.

A propriedade [`Aspect`](xref:Xamarin.Forms.Image.Aspect) determina como a imagem será dimensionada para se ajustar à área de exibição:

- [`Fill`](xref:Xamarin.Forms.Aspect.Fill) -alonga a imagem para preencher de forma completa e exata a área de exibição. Isso pode resultar na imagem que está sendo distorcida.
- [`AspectFill`](xref:Xamarin.Forms.Aspect.AspectFill) -corta a imagem para que ela preencha a área de exibição enquanto preserva o aspecto (ou seja, sem distorção).
- [`AspectFit`](xref:Xamarin.Forms.Aspect.AspectFit) -letterboxes a imagem (se necessário) para que toda a imagem caiba na área de exibição, com espaço em branco adicionado à parte superior/inferior ou aos lados, dependendo se a imagem é larga ou comprida.

As imagens podem ser carregadas de um [arquivo local](#local-images), de um [recurso inserido](#embedded-images), [baixado](#download-images)ou carregado a partir de um fluxo. Além disso, os ícones de fonte podem ser exibidos pelo [`Image`](xref:Xamarin.Forms.Image) exibição especificando os dados de ícone de fonte em um objeto `FontImageSource`. Para obter mais informações, consulte [exibir ícones de fonte](~/xamarin-forms/user-interface/text/fonts.md#display-font-icons) no guia [fontes](~/xamarin-forms/user-interface/text/fonts.md) .

## <a name="local-images"></a>Imagens locais

Arquivos de imagem podem ser adicionados a cada projeto de aplicativo e referenciados no código do xamarin. Forms compartilhado. Esse método de distribuição de imagens é necessário quando as imagens são específicas da plataforma, como ao usar resoluções diferentes em diferentes plataformas ou designs ligeiramente diferentes.

Para usar uma única imagem em todos os aplicativos, *o mesmo nome de arquivo deve ser usado em todas as plataformas*e deve ser um nome de recurso Android válido (ou seja, apenas letras minúsculas, numerais, sublinhado e o período são permitidos).

- **Ios** -a maneira preferida de gerenciar e dar suporte a imagens desde o Ios 9 é usar **conjuntos de imagens de catálogo de ativos**, que devem conter todas as versões de uma imagem que são necessárias para dar suporte a vários dispositivos e fatores de escala para um aplicativo. Para obter mais informações, consulte [adicionando imagens a um conjunto de imagens do catálogo de ativos](~/ios/app-fundamentals/images-icons/displaying-an-image.md).
- Imagens no local do **Android** no diretório de **recursos/empates** com a **ação de compilação: AndroidResource**. Versões de alto e baixo DPI de uma imagem também podem ser fornecidas (em subdiretórios de **recursos** nomeados apropriadamente, como **Drawable-ldpi**, **estilododesenho-hdpi**e **estilododesenho-xhdpi**).
- **Plataforma universal do Windows (UWP)** -Coloque as imagens no diretório raiz do aplicativo com a **ação de compilação: conteúdo**.

> [!IMPORTANT]
> Antes do iOS 9, as imagens normalmente eram colocadas na pasta de **recursos** com a **ação de Build: BundleResource**. No entanto, esse método de trabalhar com imagens em um aplicativo iOS foi substituído pela Apple. Para obter mais informações, consulte [tamanhos de imagem e nomes de](~/ios/app-fundamentals/images-icons/displaying-an-image.md)dados.

Aderir a essas regras de nomenclatura de arquivo e o posicionamento permite que o XAML a seguir carregar e exibir a imagem em todas as plataformas:

```xaml
<Image Source="waterfront.jpg" />
```

O equivalente a C# código é o seguinte:

```csharp
var image = new Image { Source = "waterfront.jpg" };
```

As capturas de tela a seguir mostram o resultado de exibição de uma imagem local em cada plataforma:

[![aplicativo de exemplo exibindo uma imagem local](images-images/local-sml.png)](images-images/local.png#lightbox)

Para obter mais flexibilidade, a propriedade `Device.RuntimePlatform` pode ser usada para selecionar um caminho ou arquivo de imagem diferente para algumas ou todas as plataformas, conforme mostrado neste exemplo de código:

```csharp
image.Source = Device.RuntimePlatform == Device.Android 
                ? ImageSource.FromFile("waterfront.jpg") 
                : ImageSource.FromFile("Images/waterfront.jpg");
```

> [!IMPORTANT]
> Para usar o mesmo nome de arquivo de imagem em todas as plataformas, o nome deve ser válido em todas as plataformas. Desenháveis Android têm restrições de nomenclatura – são permitidos apenas letras minúsculas, números, sublinhado e período – e para compatibilidade de plataforma cruzada isso deve ser seguido em todas as outras plataformas também. O FileName de exemplo **orla marítima. png** segue as regras, mas exemplos de nomes de arquivo inválidos incluem "água frontal. png", "orla marítima. png", "Water-front. png" e "wåterfront. png".

### <a name="native-resolutions-retina-and-high-dpi"></a>Resoluções nativas (retina e High-DPI)

iOS, Android e UWP incluem suporte para resoluções de imagem diferente, em que o sistema operacional escolherá a imagem apropriada em tempo de execução com base nos recursos do dispositivo. Xamarin. Forms usa APIs de plataformas nativas para carregar imagens locais, portanto, ele automaticamente dá suporte a resoluções alternativas se os arquivos corretamente são chamados e localizados no projeto.

A maneira preferencial para gerenciar imagens, pois o iOS 9 é arrastar imagens para cada resolução necessária para o conjunto de imagens do catálogo de ativos apropriado. Para obter mais informações, consulte [adicionando imagens a um conjunto de imagens do catálogo de ativos](~/ios/app-fundamentals/images-icons/displaying-an-image.md).

Antes do iOS 9, as versões da retina da imagem poderiam ser colocadas na pasta de **recursos** -duas e três vezes a resolução com um **@2x** ou **@3x** sufixos no nome do arquivo antes da extensão de arquivos (por exemplo, **myimage@2x.png** ). No entanto, esse método de trabalhar com imagens em um aplicativo iOS foi substituído pela Apple. Para obter mais informações, consulte [tamanhos de imagem e nomes de](~/ios/app-fundamentals/images-icons/displaying-an-image.md)dados.

As imagens de resolução alternativa do Android devem ser colocadas em [diretórios especialmente nomeados](https://developer.android.com/guide/practices/screens_support.html) no projeto do Android, conforme mostrado na seguinte captura de tela:

[local da imagem de várias resoluções do Android ![](images-images/xs-highdpisolution-sml.png)](images-images/xs-highdpisolution.png#lightbox)

Os nomes de arquivo [de imagem UWP podem ser sufixados com `.scale-xxx` antes da extensão de arquivo](https://docs.microsoft.com/windows/uwp/app-resources/images-tailored-for-scale-theme-contrast), em que `xxx` é a porcentagem de dimensionamento aplicada ao ativo, por exemplo, **MyImage. Scale-200. png**. As imagens podem então ser referenciadas em código ou XAML sem o modificador de escala, por exemplo, apenas **MyImage. png**. A plataforma selecionará a escala mais próxima do ativo apropriado com base em DPI de atual da tela.

### <a name="additional-controls-that-display-images"></a>Controles adicionais que exibem imagens

Alguns controles têm propriedades que exibem uma imagem, como:

- [`Button`](xref:Xamarin.Forms.Button) tem uma propriedade [`ImageSource`](xref:Xamarin.Forms.Button.ImageSource) que pode ser definida como uma imagem de bitmap a ser exibida no `Button`. Para obter mais informações, consulte [usando bitmaps com botões](~/xamarin-forms/user-interface/button.md#using-bitmaps-with-buttons).
- [`ImageButton`](xref:Xamarin.Forms.Button) tem uma propriedade [`Source`](xref:Xamarin.Forms.ImageButton.Source) que pode ser definida como a imagem a ser exibida no `ImageButton`. Para obter mais informações, consulte [definindo a origem da imagem](~/xamarin-forms/user-interface/imagebutton.md#setting-the-image-source).
- [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) tem uma propriedade [`IconImageSource`](xref:Xamarin.Forms.MenuItem.IconImageSource) que pode ser definida como uma imagem que é carregada de um arquivo, recurso inserido, URI ou fluxo.
- [`ImageCell`](xref:Xamarin.Forms.ImageCell) tem uma propriedade [`ImageSource`](xref:Xamarin.Forms.ImageCell.ImageSource) que pode ser definida como uma imagem recuperada de um arquivo, recurso inserido, URI ou fluxo.
- [`Page`](xref:Xamarin.Forms.Page). Qualquer tipo de página que deriva de `Page` tem [`IconImageSource`](xref:Xamarin.Forms.Page.IconImageSource) e [`BackgroundImageSource`](xref:Xamarin.Forms.Page.BackgroundImageSource) Propriedades, que podem ser atribuídas a um arquivo, recurso inserido, URI ou fluxo. Em determinadas circunstâncias, como quando um [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) estiver exibindo uma [`ContentPage`](xref:Xamarin.Forms.ContentPage), o ícone será exibido se houver suporte da plataforma.

  > [!IMPORTANT]
  > No iOS, a propriedade [`Page.IconImageSource`](xref:Xamarin.Forms.Page.IconImageSource) não pode ser populada a partir de uma imagem em um conjunto de imagens do catálogo de ativos. Em vez disso, carregue imagens de ícone para a propriedade `Page.IconImageSource` de um arquivo, recurso inserido, URI ou fluxo.

## <a name="embedded-images"></a>Imagens inseridas

Imagens inseridas também são fornecidas com um aplicativo (como imagens locais), mas em vez de ter uma cópia da imagem na estrutura de arquivos de cada aplicativo a imagem do arquivo é inserido no assembly como um recurso. Esse método de distribuição de imagens é recomendado quando imagens idênticas são usadas em cada plataforma e é especialmente adequado para a criação de componentes, como a imagem é fornecida com o código.

Para inserir uma imagem em um projeto, clique com botão direito para adicionar novos itens e selecione a imagem/s que você deseja adicionar. Por padrão, a imagem terá a **ação de compilação: nenhuma**; Isso precisa ser definido para a **ação de Build: EmbeddedResource**.

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![definir ação de compilação para recurso inserido](images-images/vs-buildaction-sml.png)](images-images/vs-buildaction.png#lightbox)

A **ação de compilação** pode ser exibida e alterada na janela **Propriedades** de um arquivo.

Neste exemplo, a ID de recurso é **WorkingWithImages. praia. jpg**.
O IDE gerou esse padrão concatenando o **namespace padrão** para esse projeto com o nome de arquivo, usando um ponto (.) entre cada valor.
<!-- https://msdn.microsoft.com/library/ms950960.aspx -->

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

![](images-images/xs-buildaction.png "Set Build Action: EmbeddedResource")

A **ação de Build** também pode ser exibida e alterada no painel de **Propriedades** de um arquivo.
Esse painel mostra a **ID de recurso** usada para referenciar o recurso no código. Na captura de tela abaixo, a **ID do recurso** é **WorkingWithImages. praia. jpg**.
O IDE gerou esse padrão concatenando o **namespace padrão** para esse projeto com o nome de arquivo, usando um ponto (.) entre cada valor.
Essa ID pode ser editada no painel de **Propriedades** , mas, para esses exemplos, o valor **WorkingWithImages. praia. jpg** será usado.

[painel de propriedades do recurso inserido ![](images-images/xs-embeddedproperties-sml.png)](images-images/xs-embeddedproperties.png#lightbox)

-----

Se você colocar imagens inseridas em pastas dentro de seu projeto, os nomes das pastas também são separados por pontos (.) na ID do recurso. Mover a imagem da **praia. jpg** para uma pasta chamada **myImages** resultaria em uma ID de recurso de **WorkingWithImages. myImages. praia. jpg**

O código para carregar uma imagem inserida simplesmente passa a **ID do recurso** para o método [`ImageSource.FromResource`](xref:Xamarin.Forms.ImageSource.FromResource*) , conforme mostrado abaixo:

```csharp
var embeddedImage = new Image { 
      Source = ImageSource.FromResource(
        "WorkingWithImages.beach.jpg", 
        typeof(EmbeddedImages).GetTypeInfo().Assembly
      ) };
```

> [!NOTE]
> Para dar suporte à exibição de imagens inseridas no modo de liberação na Plataforma Universal do Windows, é necessário usar a sobrecarga de `ImageSource.FromResource` que especifica o assembly de origem no qual Pesquisar a imagem.

Atualmente não há nenhuma conversão implícita para identificadores de recurso. Em vez disso, você deve usar [`ImageSource.FromResource`](xref:Xamarin.Forms.ImageSource.FromResource*) ou `new ResourceImageSource()` para carregar imagens inseridas.

As capturas de tela a seguir mostram o resultado de exibição de uma imagem inserida em cada plataforma:

[![aplicativo de exemplo exibindo uma imagem inserida](images-images/resource-sml.png)](images-images/resource.png#lightbox)

### <a name="xaml"></a>XAML

Como não há nenhum conversor de tipo interno de `string` para `ResourceImageSource`, esses tipos de imagens não podem ser carregados nativamente pelo XAML. Em vez disso, uma extensão de marcação XAML personalizada simples pode ser escrita para carregar imagens usando uma **ID de recurso** especificada em XAML:

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
> Para dar suporte à exibição de imagens inseridas no modo de liberação na Plataforma Universal do Windows, é necessário usar a sobrecarga de `ImageSource.FromResource` que especifica o assembly de origem no qual Pesquisar a imagem.

Para usar essa extensão, adicione um `xmlns` personalizado ao XAML, usando o namespace correto e os valores de assembly para o projeto. A origem da imagem pode ser definida usando essa sintaxe: `{local:ImageResource WorkingWithImages.beach.jpg}`. Um exemplo XAML completo é mostrado abaixo:

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

Porque, às vezes, é difícil entender por que um recurso de imagem em particular não está sendo carregado, o seguinte código de depuração pode ser adicionado temporariamente a um aplicativo para ajudar a confirmar que os recursos estão configurados corretamente. Ele produzirá todos os recursos conhecidos inseridos no assembly fornecido para o **console** para ajudar a depurar problemas de carregamento de recursos.

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

Por padrão, o método `ImageSource.FromResource` só procura imagens no mesmo assembly que o código que chama o método `ImageSource.FromResource`. Usando o código de depuração acima, você pode determinar quais assemblies contêm um recurso específico alterando a instrução `typeof()` para uma `Type` conhecida em cada assembly.

No entanto, o assembly de origem que está sendo pesquisado para uma imagem inserida pode ser especificado como um argumento para o método `ImageSource.FromResource`:

```csharp
var imageSource = ImageSource.FromResource("filename.png", 
            typeof(MyClass).GetTypeInfo().Assembly);
```

## <a name="download-images"></a>Baixar imagens

Imagens podem ser baixadas automaticamente para a exibição, como mostra o XAML a seguir:

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
       x:Class="WorkingWithImages.DownloadImagesXaml">
  <StackLayout VerticalOptions="Center" HorizontalOptions="Center">
    <Label Text="Image UriSource Xaml" />
    <Image Source="https://xamarin.com/content/images/pages/forms/example-app.png" />
    <Label Text="example-app.png gets downloaded from xamarin.com" />
  </StackLayout>
</ContentPage>
```

O equivalente a C# código é o seguinte:

```csharp
var webImage = new Image { 
     Source = ImageSource.FromUri(
        new Uri("https://xamarin.com/content/images/pages/forms/example-app.png")
     ) };
```

O método [`ImageSource.FromUri`](xref:Xamarin.Forms.ImageSource.FromUri(System.Uri)) requer um objeto `Uri` e retorna uma nova [`UriImageSource`](xref:Xamarin.Forms.UriImageSource) que lê do `Uri`.

Também há uma conversão implícita de cadeias de caracteres do URI, portanto, o exemplo a seguir também funcionará:

```csharp
webImage.Source = "https://xamarin.com/content/images/pages/forms/example-app.png";
```

As capturas de tela a seguir mostram o resultado de exibição de uma imagem remota em cada plataforma:

[![aplicativo de exemplo exibindo uma imagem baixada](images-images/download-sml.png)](images-images/download.png#lightbox)

### <a name="downloaded-image-caching"></a>Cache de imagem baixado

Um [`UriImageSource`](xref:Xamarin.Forms.UriImageSource) também dá suporte ao cache de imagens baixadas, configuradas por meio das seguintes propriedades:

- [`CachingEnabled`](xref:Xamarin.Forms.UriImageSource.CachingEnabled) -se o Caching está habilitado (`true` por padrão).
- [`CacheValidity`](xref:Xamarin.Forms.UriImageSource.CacheValidity) -um `TimeSpan` que define por quanto tempo a imagem será armazenada localmente.

Armazenamento em cache é habilitado por padrão e armazenará a imagem localmente para 24 horas. Para desabilitar o cache de uma imagem específica, instanciar a origem da imagem da seguinte maneira:

```csharp
image.Source = new UriImageSource { CachingEnabled = false, Uri = new Uri("http://server.com/image") };
```

Para definir um período de cache específica (por exemplo, 5 dias) instanciar a origem da imagem da seguinte maneira:

```csharp
webImage.Source = new UriImageSource
{
    Uri = new Uri("https://xamarin.com/content/images/pages/forms/example-app.png"),
    CachingEnabled = true,
    CacheValidity = new TimeSpan(5,0,0,0)
};
```

Armazenamento em cache interno facilita muito dar suporte a cenários, como listas de imagens, onde você pode definir (ou associar) uma imagem de rolagem em cada célula e permitir que o cache interno cuidar de carregar novamente a imagem quando a célula é rolada novamente no modo de exibição.

## <a name="animated-gifs"></a>GIFs animados

O Xamarin. Forms inclui suporte para a exibição de GIFs pequenos e animados. Isso é feito definindo a propriedade [`Image.Source`](xref:Xamarin.Forms.Image.Source) como um arquivo GIF animado:

```xaml
<Image Source="demo.gif" />
```

> [!IMPORTANT]
> Embora o suporte a GIF animado no Xamarin. Forms inclua a capacidade de baixar arquivos, ele não dá suporte a caching ou GIFs animados de streaming.

Por padrão, quando um GIF animado é carregado, ele não será reproduzido. Isso ocorre porque a propriedade `IsAnimationPlaying`, que controla se um GIF animado está tocando ou parado, tem um valor padrão de `false`. Essa propriedade, do tipo `bool`, é apoiada por um objeto [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , o que significa que ele pode ser o destino de uma vinculação de dados e com estilo.

Portanto, quando um GIF animado é carregado, ele não será reproduzido até que a propriedade `IsAnimationPlaying` seja definida como `true`. A reprodução pode ser interrompida definindo a propriedade `IsAnimationPlaying` como `false`. Observe que essa propriedade não tem nenhum efeito ao exibir uma fonte de imagem não GIF.

> [!NOTE]
> No Android, o suporte a GIF animado requer que seu aplicativo esteja usando renderizadores rápidos e não funcionará se você tiver optado por usar os renderizadores herdados.
> No UWP, o suporte a GIF animado requer uma versão mínima da atualização de aniversário do Windows 10 (versão 1607).

## <a name="icons-and-splash-screens"></a>Ícones e telas de abertura

Embora não esteja relacionado à exibição de [`Image`](xref:Xamarin.Forms.Image) , os ícones de aplicativo e as telas de abertura também são um uso importante de imagens em projetos do Xamarin. Forms.

Configurando ícones e telas de abertura para aplicativos xamarin. Forms é feita em cada um dos projetos de aplicativo. Isso significa que gerar corretamente dimensionada de imagens para iOS, Android e UWP. Essas imagens devem ser nomeadas e localizadas de acordo com os requisitos de cada das plataformas.

## <a name="icons"></a>Ícones

Consulte as diretrizes de [trabalho do IOS trabalhando com imagens](~/ios/app-fundamentals/images-icons/index.md), [Google iconografia](https://developer.android.com/design/style/iconography.html)e [UWP para ver os ativos de bloco e ícone](/windows/uwp/controls-and-patterns/tiles-and-notifications-app-assets/) para obter mais informações sobre como criar esses recursos de aplicativo.

Além disso, os ícones de fonte podem ser exibidos pelo [`Image`](xref:Xamarin.Forms.Image) exibição especificando os dados de ícone de fonte em um objeto `FontImageSource`. Para obter mais informações, consulte [exibir ícones de fonte](~/xamarin-forms/user-interface/text/fonts.md#display-font-icons) no guia [fontes](~/xamarin-forms/user-interface/text/fonts.md) .

## <a name="splash-screens"></a>Telas de abertura

Somente aplicativos iOS e UWP exigem uma tela inicial (também chamada de uma imagem de tela ou padrão de inicialização).

Consulte a documentação do [Ios trabalhando com imagens](~/ios/app-fundamentals/images-icons/index.md) e [telas de abertura](/windows/uwp/launch-resume/splash-screens/) no centro de desenvolvimento do Windows.

## <a name="related-links"></a>Links relacionados

- [WorkingWithImages (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithimages)
- [iOS trabalhando com imagens](~/ios/app-fundamentals/images-icons/index.md)
- [Iconografia Android](https://developer.android.com/design/style/iconography.html)
- [Diretrizes para ativos de bloco e ícone](/windows/uwp/controls-and-patterns/tiles-and-notifications-app-assets/)
