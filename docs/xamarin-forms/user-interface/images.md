---
title: Imagens no xamarin. Forms
description: Imagens podem ser compartilhadas entre plataformas com o xamarin. Forms, podem ser carregados especificamente para cada plataforma ou podem ser baixados para exibição.
ms.prod: xamarin
ms.assetid: C025AB53-05CC-49BA-9815-75D6DF9E40B7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/15/2017
ms.openlocfilehash: ea7ec0d297e144966f8ff7545e6b12160f66c9b0
ms.sourcegitcommit: 3e980fbf92c69c3dd737554e8c6d5b94cf69ee3a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2018
ms.locfileid: "37935534"
---
# <a name="images-in-xamarinforms"></a>Imagens no xamarin. Forms

_Imagens podem ser compartilhadas entre plataformas com o xamarin. Forms, podem ser carregados especificamente para cada plataforma ou podem ser baixados para exibição._

Imagens são uma parte crucial da navegação do aplicativo, a usabilidade e a identidade visual. Aplicativos xamarin. Forms precisam ser capaz de compartilhar imagens entre todas as plataformas, mas também pode exibir imagens diferentes em cada plataforma.

Imagens específicas da plataforma também são necessárias para os ícones e telas de abertura; eles precisam ser configurados em uma base por plataforma.

Este documento discute os seguintes tópicos:

- [ **Imagens locais** ](#Local_Images) -exibindo imagens fornecidos com o aplicativo, incluindo a resolução de resoluções nativas como iOS Retina, Android ou UWP versões de DPI alto de uma imagem.
- [ **Imagens embutidas** ](#Embedded_Images) -exibindo imagens incorporadas como um recurso de assembly.
- [ **Download de imagens** ](#Downloading_Images) - baixar e exibir imagens.
- [ **Ícones e splashscreens** ](#Icons_and_splashscreens) -ícones específicos da plataforma e imagens de inicialização.

## <a name="displaying-images"></a>Exibindo imagens

Xamarin. Forms usa o [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) modo de exibição para exibir imagens em uma página. Ele tem duas propriedades importantes:

- [`Source`](https://developer.xamarin.com/api/property/Xamarin.Forms.Image.Source/) -An [ `ImageSource` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ImageSource/) instância, arquivo, Uri ou recurso, que define a imagem a ser exibida.
- [`Aspect`](https://developer.xamarin.com/api/property/Xamarin.Forms.Image.Aspect/) -Como dimensionar a imagem dentro dos limites que está sendo exibido dentro de (seja para stretch, cortar ou letterbox).

[`ImageSource`](https://developer.xamarin.com/api/type/Xamarin.Forms.ImageSource/) instâncias podem ser obtidas usando métodos estáticos para cada tipo de origem da imagem:

- [`FromFile`](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromFile/p/System.String/) -Requer um nome de arquivo ou caminho do arquivo que pode ser resolvido em cada plataforma.
- [`FromUri`](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromUri/p/System.Uri/) -Requer um objeto de Uri, por exemplo.  `new Uri("http://server.com/image.jpg")` .
- [`FromResource`](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromResource/p/System.String/) -Requer um identificador de recurso para um arquivo de imagem inserido no aplicativo ou projeto de biblioteca .NET Standard, com um **ação de Build: EmbeddedResource**.
- [`FromStream`](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromStream/p/System.Func%7BSystem.IO.Stream%7D/) -Requer um fluxo que fornece dados de imagem.

O [ `Aspect` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Image.Aspect/) propriedade determina como a imagem será dimensionada para caber na área de exibição:

- [`Fill`](xref:Xamarin.Forms.Aspect.Fill) -Alonga a imagem para a área de exibição de preencher completamente e exatamente. Isso pode resultar na imagem que está sendo distorcida.
- [`AspectFill`](xref:Xamarin.Forms.Aspect.AspectFill) -Recorta a imagem de modo que ele preencha a área de exibição, preservando o aspecto (ie. nenhuma distorção).
- [`AspectFit`](xref:Xamarin.Forms.Aspect.AspectFit) -E Letterbox a imagem (se necessário) para que a imagem inteira se adapta a área de exibição, com espaço em branco adicionado para o de cima para baixo ou lados dependendo se a imagem é altas ou largas.

Imagens podem ser carregadas de um [arquivo local](#Local_Images_in_Xaml), um [recurso incorporado](#embedded_images), ou [baixado](#Downloading_Images).

<a name="Local_Images" />

## <a name="local-images"></a>Imagens locais

Arquivos de imagem podem ser adicionados a cada projeto de aplicativo e referenciados no código do xamarin. Forms compartilhado. Para usar uma única imagem em todos os aplicativos, *o mesmo nome de arquivo deve ser usado em todas as plataformas*, e ele deve ser um nome de recurso válido do Android (ie. são permitidos apenas letras minúsculas, números, sublinhado e o período de).

- **iOS** – o preferencial a maneira de gerenciar e dar suporte a imagens desde que o iOS 9 é usar **conjuntos de imagem do catálogo de ativos**, que deve conter todas as versões de uma imagem que são necessárias para dar suporte a vários dispositivos e fatores de escala um aplicativo. Para obter mais informações, consulte [adicionar imagens a um conjunto de imagem de catálogo ativo](~/ios/app-fundamentals/images-icons/displaying-an-image.md).
- **Android** -colocar imagens em de **recursos/drawable** diretório com **Build Action: AndroidResource**. Versões de DPI alto e baixo de uma imagem também podem ser fornecidas (adequadamente chamado **recursos** subdiretórios, como **desenháveis ldpi**, **drawable-hdpi**e **desenháveis xhdpi**).
- **Plataforma universal do Windows (UWP)** -Insira imagens no diretório de raiz do aplicativo com **Build Action: conteúdo**.

> [!IMPORTANT]
> Antes do iOS 9, imagens normalmente foram colocadas na **recursos** pasta com **Build Action: BundleResource**. No entanto, esse método de trabalhar com imagens em um aplicativo iOS foi substituído pela Apple. Para obter mais informações, consulte [tamanhos de imagem e nomes de arquivo](~/ios/app-fundamentals/images-icons/displaying-an-image.md).

Aderir a essas regras de nomenclatura de arquivo e o posicionamento permite que o XAML a seguir carregar e exibir a imagem em todas as plataformas:

```xaml
<Image Source="waterfront.jpg" />
```

O código c# equivalente é da seguinte maneira:

```csharp
var image = new Image { Source = "waterfront.jpg" };
```

As capturas de tela a seguir mostram o resultado de exibição de uma imagem local em cada plataforma:

[![Local ImageSource](images-images/local-sml.png "exibindo uma imagem Local do aplicativo de exemplo")](images-images/local.png#lightbox "exibindo uma imagem Local do aplicativo de exemplo")

Para obter mais flexibilidade a `Device.RuntimePlatform` propriedade pode ser usada para selecionar um arquivo de imagem diferente ou o caminho para algumas ou todas as plataformas, conforme mostrado neste exemplo de código:

```csharp
image.Source = Device.RuntimePlatform == Device.Android ? ImageSource.FromFile("waterfront.jpg") : ImageSource.FromFile("Images/waterfront.jpg");
```

> [!IMPORTANT]
> Para usar o mesmo nome de arquivo de imagem em todas as plataformas, o nome deve ser válido em todas as plataformas. Desenháveis Android têm restrições de nomenclatura – são permitidos apenas letras minúsculas, números, sublinhado e período – e para compatibilidade de plataforma cruzada isso deve ser seguido em todas as outras plataformas também. O nome do arquivo de exemplo **waterfront.png** segue as regras, mas os exemplos de nomes de arquivo inválidos incluem "água front.png", "WaterFront.png", "água front.png" e "wåterfront.png".

<a name="Native_Resolutions" />

### <a name="native-resolutions-retina-and-high-dpi"></a>Resoluções nativas (Retina e com alto DPI)

iOS, Android e UWP incluem suporte para resoluções de imagem diferente, em que o sistema operacional escolherá a imagem apropriada em tempo de execução com base nos recursos do dispositivo. Xamarin. Forms usa APIs de plataformas nativas para carregar imagens locais, portanto, ele automaticamente dá suporte a resoluções alternativas se os arquivos corretamente são chamados e localizados no projeto.

A maneira preferencial para gerenciar imagens, pois o iOS 9 é arrastar imagens para cada resolução necessária para o conjunto de imagens do catálogo de ativos apropriado. Para obter mais informações, consulte [adicionar imagens a um conjunto de imagem de catálogo ativo](~/ios/app-fundamentals/images-icons/displaying-an-image.md).

Antes do iOS 9, versões de retina da imagem pode ser colocadas na **recursos** pasta - dois e três vezes a resolução com um **@2x** ou **@3x**sufixos de nome de arquivo antes da extensão de arquivo (por exemplo. **myimage@2x.png**). No entanto, esse método de trabalhar com imagens em um aplicativo iOS foi substituído pela Apple. Para obter mais informações, consulte [tamanhos de imagem e nomes de arquivo](~/ios/app-fundamentals/images-icons/displaying-an-image.md).

Imagens de resolução alternativo Android devem ser colocadas em [diretórios especialmente denominada](http://developer.android.com/guide/practices/screens_support.html) no projeto do Android, conforme mostrado na seguinte captura de tela:

[![Local da imagem de resolução de vários Android](images-images/xs-highdpisolution-sml.png "local de imagem de resolução de vários Android")](images-images/xs-highdpisolution.png#lightbox "local da imagem de resolução de vários Android")

Nomes de arquivo de imagem UWP [pode ser com o sufixo `.scale-xxx` antes da extensão de arquivo](https://docs.microsoft.com/windows/uwp/app-resources/images-tailored-for-scale-theme-contrast), onde `xxx` é a porcentagem de dimensionamento aplicado ao ativo, por exemplo, **myimage.scale 200.png**. Imagens, em seguida, podem ser chamadas para no código ou XAML sem o modificador de escala, por exemplo, apenas **myimage.png**. A plataforma selecionará a escala mais próxima do ativo apropriado com base em DPI de atual da tela.

### <a name="additional-controls-that-display-images"></a>Controles adicionais que exibem imagens

Alguns controles têm propriedades que exibem uma imagem, como:

- [`Page`](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) -Qualquer tipo que deriva de página `Page` tem [ `Icon` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.Icon/) e [ `BackgroundImage` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.BackgroundImage/) propriedades, que podem ser atribuídas a uma referência de arquivo local. Em determinadas circunstâncias, como quando um [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) está exibindo um [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/), o ícone será exibido se houver suporte pela plataforma.

  > [!IMPORTANT]
  > No iOS, o [ `Page.Icon` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.Icon/) não é possível popular a propriedade de uma imagem em um conjunto de imagens de catálogo ativo. Em vez disso, carregar imagens de ícone para o `Page.Icon` propriedade a partir de **recursos** pasta no projeto do iOS.

- [`ToolbarItem`](https://developer.xamarin.com/api/type/Xamarin.Forms.ToolbarItem/) – Tem um [ `Icon` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ToolbarItem.Icon/) propriedade que pode ser definida como uma referência de arquivo local.
- [`ImageCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.ImageCell/) – Tem um [ `ImageSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ImageCell.ImageSource/) recuperados de propriedade que pode ser definida como uma imagem de um arquivo local, um recurso inserido ou um URI.

<a name="embedded_images" />

## <a name="embedded-images"></a>Imagens inseridas

Imagens inseridas também são fornecidas com um aplicativo (como imagens locais), mas em vez de ter uma cópia da imagem na estrutura de arquivos de cada aplicativo a imagem do arquivo é inserido no assembly como um recurso. Esse método de distribuição de imagens é especialmente adequado para a criação de componentes, como a imagem é fornecida com o código.

Para inserir uma imagem em um projeto, clique com botão direito para adicionar novos itens e selecione a imagem/s que você deseja adicionar. Por padrão a imagem terá **ação de compilação: None**; isso deve ser definido como **Build Action: EmbeddedResource**.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![](images-images/vs-buildaction.png "Definir ação de compilação: EmbeddedResource")

O **ação de compilação** podem ser exibidos e alterados na **propriedades** janela para um arquivo.

Neste exemplo é a ID de recurso **WorkingWithImages.beach.jpg**.
O IDE gerou esse padrão por meio da concatenação de **Namespace padrão** para este projeto com o nome do arquivo, usando um ponto (.) entre cada valor.
<!-- https://msdn.microsoft.com/library/ms950960.aspx -->

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

![](images-images/xs-buildaction.png "Definir ação de compilação: EmbeddedResource")

**Ação de Build** também podem ser exibidos e alterados na **propriedades** painel para um arquivo.
Este painel mostra as **ID do recurso** que é usado para referenciar o recurso no código. Na captura de tela abaixo, o **ID do recurso** é **WorkingWithImages.beach.jpg**.
O IDE gerou esse padrão por meio da concatenação de **Namespace padrão** para este projeto com o nome do arquivo, usando um ponto (.) entre cada valor.
Essa ID pode ser editada na **propriedades** teclado, mas para esses exemplos o valor **WorkingWithImages.beach.jpg** será usado.

![](images-images/xs-embeddedproperties.png "Painel de propriedades EmbeddedResource")

-----

Se você colocar imagens inseridas em pastas dentro de seu projeto, os nomes das pastas também são separados por pontos (.) na ID do recurso. Movendo o **beach.jpg** imagem em uma pasta chamada **MyImages** resultaria em uma ID de recurso de **WorkingWithImages.MyImages.beach.jpg**

O código para carregar uma imagem inserida simplesmente passa o **ID do recurso** para o [ `ImageSource.FromResource` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromResource/p/System.String/) método conforme mostrado abaixo:

```csharp
var embeddedImage = new Image { Source = ImageSource.FromResource("WorkingWithImages.beach.jpg", typeof(EmbeddedImages).GetTypeInfo().Assembly) };
```

> [!NOTE]
> Para dar suporte à exibição de imagens inseridas no modo de versão na plataforma Universal do Windows, é necessário usar a sobrecarga do `ImageSource.FromResource` que especifica o assembly de origem no qual pesquisar a imagem.

Atualmente não há nenhuma conversão implícita para identificadores de recurso. Em vez disso, você deve usar [ `ImageSource.FromResource` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromResource/p/System.String/) ou `new ResourceImageSource()` para carregar imagens inseridas.

As capturas de tela a seguir mostram o resultado de exibição de uma imagem inserida em cada plataforma:

[![ResourceImageSource](images-images/resource-sml.png "exibindo uma imagem inserida de aplicativo de exemplo")](images-images/resource.png#lightbox "exibindo uma imagem inserida de aplicativo de exemplo")

<a name="Embedded_Images_in_Xaml" />

### <a name="using-xaml"></a>Usando XAML

Porque não há nenhum conversor de tipo interno do `string` para `ResourceImageSource`, esses tipos de imagens de modo nativo não podem ser carregados pelo XAML. Em vez disso, uma extensão de marcação XAML personalizada simple pode ser escrita para carregar imagens usando um **ID do recurso** especificado no XAML:

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
> Para dar suporte à exibição de imagens inseridas no modo de versão na plataforma Universal do Windows, é necessário usar a sobrecarga do `ImageSource.FromResource` que especifica o assembly de origem no qual pesquisar a imagem.

Para usar essa extensão de adicionar personalizado `xmlns` para XAML, usando os valores corretos de namespace e assembly para o projeto. A origem da imagem, em seguida, pode ser definida usando esta sintaxe: `{local:ImageResource WorkingWithImages.beach.jpg}`. Um exemplo XAML completo é mostrado abaixo:

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

### <a name="troubleshooting-embedded-images"></a>Imagens inseridas de solução de problemas

<a name="Debugging_Embedded_Images" />

#### <a name="debugging-code"></a>Depurando código

Porque, às vezes, é difícil entender por que um recurso de imagem em particular não está sendo carregado, o seguinte código de depuração pode ser adicionado temporariamente a um aplicativo para ajudar a confirmar que os recursos estão configurados corretamente. Ele terá como saída conhecidos todos os recursos incorporados no assembly fornecido para o <span class="UIItem">Console</span> para ajudar a depurar problemas de carregamento de recursos.

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

Por padrão, o `ImageSource.FromResource` método procura apenas imagens no mesmo assembly que o código chamar o `ImageSource.FromResource` método. Usando o código de depuração acima, você pode determinar quais assemblies contêm um recurso específico, alterando a `typeof()` instrução para um `Type` conhecido para estar em cada assembly.

No entanto, o assembly de origem que está sendo pesquisado para uma imagem inserida pode ser especificado como um argumento para o `ImageSource.FromResource` método:

```csharp
var imageSource = ImageSource.FromResource("filename.png", typeof(MyClass).GetTypeInfo().Assembly);
```

<a name="Downloading_Images" />

## <a name="downloading-images"></a>Download de imagens

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

O código c# equivalente é da seguinte maneira:

```csharp
var webImage = new Image { Source = ImageSource.FromUri(new Uri("https://xamarin.com/content/images/pages/forms/example-app.png")) };
```

O [ `ImageSource.FromUri` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromUri/p/System.Uri/) método exige um `Uri` do objeto e retorna um novo [ `UriImageSource` ](https://developer.xamarin.com/api/type/Xamarin.Forms.UriImageSource/) que lê do `Uri`.

Também há uma conversão implícita de cadeias de caracteres do URI, portanto, o exemplo a seguir também funcionará:

```csharp
webImage.Source = "https://xamarin.com/content/images/pages/forms/example-app.png";
```

As capturas de tela a seguir mostram o resultado de exibição de uma imagem remota em cada plataforma:

[![Baixado ImageSource](images-images/download-sml.png "exibindo uma imagem baixada do aplicativo de exemplo")](images-images/download.png#lightbox "exibindo uma imagem baixada do aplicativo de exemplo")

<a name="Image_Caching" />

### <a name="downloaded-image-caching"></a>Cache de imagem baixada

Um [ `UriImageSource` ](https://developer.xamarin.com/api/type/Xamarin.Forms.UriImageSource/) também dá suporte ao cache de imagens baixadas, configuradas por meio das seguintes propriedades:

- [`CachingEnabled`](https://developer.xamarin.com/api/property/Xamarin.Forms.UriImageSource.CachingEnabled/) – Se o cache está habilitado (`true` por padrão).
- [`CacheValidity`](https://developer.xamarin.com/api/property/Xamarin.Forms.UriImageSource.CacheValidity/) -A `TimeSpan` que define por quanto tempo a imagem será armazenada localmente.

Armazenamento em cache é habilitado por padrão e armazenará a imagem localmente para 24 horas. Para desabilitar o cache de uma imagem específica, instanciar a origem da imagem da seguinte maneira:

```csharp
image.Source = new UriImageSource { CachingEnabled = false, Uri="http://server.com/image" };
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

<a name="Icons_and_splashscreens" />

## <a name="icons-and-splashscreens"></a>Ícones e splashscreens

Enquanto não relacionados para o [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) exibição, os ícones de aplicativo e splashscreens também são um importante uso das imagens em projetos do xamarin. Forms.

Configurando ícones e splashscreens para aplicativos xamarin. Forms é feita em cada um dos projetos de aplicativo. Isso significa que gerar corretamente dimensionada de imagens para iOS, Android e UWP. Essas imagens devem ser nomeadas e localizadas de acordo com os requisitos de cada das plataformas.

## <a name="icons"></a>Ícones

Consulte a [iOS trabalhando com imagens](~/ios/app-fundamentals/images-icons/index.md), [Google iconografia](http://developer.android.com/design/style/iconography.html), e [diretrizes para ativos de bloco e ícone](/windows/uwp/controls-and-patterns/tiles-and-notifications-app-assets/) para obter mais informações sobre como criar esses recursos de aplicativo.

## <a name="splashscreens"></a>Splashscreens

Somente aplicativos iOS e UWP exigem uma tela inicial (também chamado de uma imagem de tela ou padrão de inicialização).

Consulte a documentação para [iOS trabalhando com imagens](~/ios/app-fundamentals/images-icons/index.md) e [telas de abertura](/windows/uwp/launch-resume/splash-screens/) no Centro de desenvolvimento do Windows.

## <a name="summary"></a>Resumo

Xamarin. Forms oferece um número de diferentes maneiras de incluir imagens em um aplicativo de plataforma cruzada, permitindo que para a mesma imagem a ser usado em plataformas ou para imagens específicas da plataforma seja especificado. Imagens baixadas são automaticamente armazenadas em cache, automatizando um cenário comum de codificação.

Imagens de ícone e a tela inicial do aplicativo são a configuração e configurado como para aplicativos não xamarin. Forms - sigam a mesma orientação usada para aplicativos específicos da plataforma.

## <a name="related-links"></a>Links relacionados

- [WorkingWithImages (amostra)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithImages/)
- [iOS trabalhando com imagens](~/ios/app-fundamentals/images-icons/index.md)
- [Iconografia Android](http://developer.android.com/design/style/iconography.html)
- [Diretrizes para ativos de bloco e ícone](/windows/uwp/controls-and-patterns/tiles-and-notifications-app-assets/)
