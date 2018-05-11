---
title: Imagens
description: Imagens podem ser compartilhadas entre plataformas com xamarin. Forms, eles podem ser carregados especificamente para cada plataforma ou podem ser baixadas para exibição.
ms.prod: xamarin
ms.assetid: C025AB53-05CC-49BA-9815-75D6DF9E40B7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/15/2017
ms.openlocfilehash: caa7884920e842a8f83e2b0fdb5e0fa4b358ca8e
ms.sourcegitcommit: b0a1c3969ab2a7b7fe961f4f470d1aa57b1ff2c6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2018
---
# <a name="images"></a>Imagens

_Imagens podem ser compartilhadas entre plataformas com xamarin. Forms, eles podem ser carregados especificamente para cada plataforma ou podem ser baixadas para exibição._

Imagens são uma parte essencial da navegação do aplicativo, a usabilidade e a identidade visual. Aplicativos xamarin. Forms precisam ser capaz de compartilhar imagens entre todas as plataformas, mas também potencialmente exibir diferentes imagens em cada plataforma.

Imagens específicas da plataforma também são necessárias para ícones e telas; eles precisam ser configurados em uma base por plataforma.

Este documento aborda os seguintes tópicos:

- [ **Imagens locais** ](#Local_Images) -exibindo imagens fornecido com o aplicativo, incluindo a resolução de resoluções nativas como iOS versões de alto DPI de Retina, Android ou UWP de uma imagem.
- [ **Imagens incorporadas** ](#Embedded_Images) -exibindo imagens inseridas como um recurso de assembly.
- [ **Baixado imagens** ](#Downloading_Images) - baixar e exibir imagens.
- [ **Ícones e telas iniciais** ](#Icons_and_splashscreens) -ícones específicos de plataforma e imagens de inicialização.

## <a name="displaying-images"></a>Exibindo imagens

Xamarin. Forms usa o [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) para exibir imagens em uma página. Ele tem duas propriedades importantes:

- [`Source`](https://developer.xamarin.com/api/property/Xamarin.Forms.Image.Source/) -Um [ `ImageSource` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ImageSource/) instância, ou o arquivo, o Uri ou recurso, que define a imagem a ser exibida.
- [`Aspect`](https://developer.xamarin.com/api/property/Xamarin.Forms.Image.Aspect/) -Como dimensionar a imagem dentro dos limites que está sendo exibido dentro de (se stretch, corte ou letterbox).

[`ImageSource`](https://developer.xamarin.com/api/type/Xamarin.Forms.ImageSource/) instâncias podem ser obtidas usando métodos estáticos para cada tipo de origem da imagem:

- [`FromFile`](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromFile/p/System.String/) -Requer um nome de arquivo ou caminho de arquivo que pode ser resolvido em cada plataforma.
- [`FromUri`](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromUri/p/System.Uri/) -Requer um objeto Uri, por exemplo.  `new Uri("http://server.com/image.jpg")` .
- [`FromResource`](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromResource/p/System.String/) -Requer um identificador de recurso para um arquivo de imagem inserido no aplicativo ou projeto de biblioteca do .NET padrão, com um **compilar ação: EmbeddedResource**.
- [`FromStream`](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromStream/p/System.Func%7BSystem.IO.Stream%7D/) -Requer um fluxo que fornece dados de imagem.

O [ `Aspect` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Image.Aspect/) propriedade determina como a imagem será dimensionada para caber na área de exibição:

- [`Fill`](https://developer.xamarin.com/api/field/Xamarin.Forms.Aspect.Fill/) -Alonga a imagem para completamente e exatamente preencher a área de exibição. Isso pode resultar na imagem que está sendo distorcida.
- [`AspectFill`](https://developer.xamarin.com/api/field/Xamarin.Forms.Aspect.AspectFill/) -Recorta a imagem para que ela preenche a área de exibição, preservando o aspecto (ie. nenhuma distorção).
- [`AspectFit`](https://developer.xamarin.com/api/field/Xamarin.Forms.Aspect.AspectFit/) -Letterboxes a imagem (se necessário) para que a imagem inteira se encaixa na área de exibição, com espaço em branco adicionado de cima para baixo ou lados dependendo se a imagem é largura ou altura.

Imagens podem ser carregadas de um [arquivo local](#Local_Images_in_Xaml), uma [recurso incorporado](#embedded_images), ou [baixado](#Downloading_Images).

<a name="Local_Images" />

## <a name="local-images"></a>Imagens locais

Arquivos de imagem podem ser adicionados para cada projeto de aplicativo e referenciados no código do xamarin. Forms compartilhado. Para usar uma única imagem em todos os aplicativos, *o mesmo nome de arquivo deve ser usado em todas as plataformas*, e ele deve ser um nome de recurso Android válido (ie. são permitidos apenas letras minúsculas, números, sublinhado e o período).

- **iOS** - o preferencial a maneira de gerenciar e suporte a imagens, pois é usar o iOS 9 **conjuntos de imagem do catálogo de ativos**, que deve conter todas as versões de uma imagem que são necessárias para dar suporte a vários dispositivos e dimensionar fatores para um aplicativo. Para obter mais informações, consulte [adicionar imagens a um conjunto de imagem de catálogo Asset](~/ios/app-fundamentals/images-icons/displaying-an-image.md).
- **Android** -colocar imagens no **recursos/drawable** diretório com **ação de compilação: AndroidResource**. Versões de DPI alto e baixo de uma imagem também podem ser fornecidas (adequadamente denominado **recursos** subdiretórios, como **drawable ldpi**, **drawable hdpi**e **drawable xhdpi**).
- **Windows UWP (plataforma universal)** -colocar imagens no diretório de raiz do aplicativo com **ação de compilação: conteúdo**.

> [!IMPORTANT]
> Antes do iOS 9, imagens normalmente foram colocadas no **recursos** pasta com **ação de compilação: BundleResource**. No entanto, esse método de trabalhar com imagens em um aplicativo iOS foi substituído pela Apple. Para obter mais informações, consulte [tamanhos de imagem e nomes de arquivo](~/ios/app-fundamentals/images-icons/displaying-an-image.md).

Aderir a essas regras para nomes de arquivos e o posicionamento permite que o XAML a seguir carregar e exibir a imagem em todas as plataformas:

```xaml
<Image Source="waterfront.jpg" />
```

O código c# equivalente é o seguinte:

```csharp
var image = new Image { Source = "waterfront.jpg" };
```

As capturas de tela a seguir mostram o resultado de exibição de uma imagem do local em cada plataforma:

[![Local ImageSource](images-images/local-sml.png "exibindo uma imagem do Local do aplicativo de exemplo")](images-images/local.png#lightbox "exibindo uma imagem do Local do aplicativo de exemplo")

Para obter mais flexibilidade a `Device.RuntimePlatform` propriedade pode ser usada para selecionar um outro arquivo de imagem ou o caminho de algumas ou todas as plataformas, como mostrado neste exemplo de código:

```csharp
image.Source = Device.RuntimePlatform == Device.Android ? ImageSource.FromFile("waterfront.jpg") : ImageSource.FromFile("Images/waterfront.jpg");
```

> [!IMPORTANT]
> Para usar o mesmo nome de arquivo de imagem em todas as plataformas, o nome deve ser válido em todas as plataformas. Drawables Android tem restrições de nomenclatura – são permitidos apenas letras minúsculas, números, sublinhados e período – e para fins de compatibilidade de plataforma cruzada isso deve ser seguido em outras plataformas muito. O nome do arquivo de exemplo **waterfront.png** segue as regras, mas os exemplos de nomes de arquivos inválidos incluem "água front.png", "WaterFront.png", "água front.png" e "wåterfront.png".

<a name="Native_Resolutions" />

### <a name="native-resolutions-retina-and-high-dpi"></a>Resoluções nativas (Retina e alto DPI)

iOS, Android e UWP incluem suporte para resoluções de imagem diferente, onde o sistema operacional escolhe a imagem apropriada em tempo de execução com base nos recursos do dispositivo. Xamarin. Forms usa APIs das plataformas nativo para carregar imagens locais, portanto ele automaticamente dá suporte a soluções alternativas se os arquivos estão corretamente nomeados e localizados no projeto.

A melhor maneira de gerenciar imagens desde iOS 9 é arrastar imagens para cada resolução necessária para o conjunto de imagem do catálogo de ativos apropriado. Para obter mais informações, consulte [adicionar imagens a um conjunto de imagem de catálogo Asset](~/ios/app-fundamentals/images-icons/displaying-an-image.md).

Antes do iOS 9, versões de retina da imagem podem ser colocadas no **recursos** pasta - dois e três vezes a resolução com um **@2x** ou **@3x**sufixos de nome de arquivo antes da extensão de arquivo (por exemplo, **myimage@2x.png**). No entanto, esse método de trabalhar com imagens em um aplicativo iOS foi substituído pela Apple. Para obter mais informações, consulte [tamanhos de imagem e nomes de arquivo](~/ios/app-fundamentals/images-icons/displaying-an-image.md).

Imagens de resolução alternativo Android devem ser colocadas em [diretórios especialmente chamado](http://developer.android.com/guide/practices/screens_support.html) no projeto do Android, conforme mostrado na seguinte captura de tela:

[![Local da imagem de resolução de vários Android](images-images/xs-highdpisolution-sml.png "local da imagem de resolução de vários Android")](images-images/xs-highdpisolution.png#lightbox "local da imagem de resolução de vários Android")

Nomes de arquivo de imagem UWP [pode ser com o sufixo `.scale-xxx` antes da extensão de arquivo](https://docs.microsoft.com/windows/uwp/app-resources/images-tailored-for-scale-theme-contrast), onde `xxx` é a porcentagem de dimensionamento aplicado ao ativo, por exemplo, **myimage.scale 200.png**. Imagens, em seguida, podem ser chamadas para no código ou XAML sem o modificador de escala, por exemplo, apenas **myimage.png**. A plataforma selecionará a escala mais próxima do ativo apropriado com base em DPI atual da exibição.

### <a name="additional-controls-that-display-images"></a>Controles adicionais que exibem imagens

Alguns controles têm propriedades que exibem uma imagem, como:

- [`Page`](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) -Qualquer tipo que deriva de página `Page` tem [ `Icon` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.Icon/) e [ `BackgroundImage` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.BackgroundImage/) propriedades, que podem ser atribuídas a uma referência de arquivo local. Em determinadas circunstâncias, como quando um [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) está exibindo um [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/), o ícone será exibido se houver suporte pela plataforma.

  > [!IMPORTANT]
  > No iOS, o [ `Page.Icon` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.Icon/) propriedade não é possível preencher a partir de uma imagem em um conjunto de imagem do catálogo de ativos. Em vez disso, carregar ícones para o `Page.Icon` propriedade o **recursos** pasta do projeto do iOS.

- [`ToolbarItem`](https://developer.xamarin.com/api/type/Xamarin.Forms.ToolbarItem/) -Tem um [ `Icon` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ToolbarItem.Icon/) propriedade que pode ser definida como uma referência de arquivo local.
- [`ImageCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.ImageCell/) -Tem um [ `ImageSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ImageCell.ImageSource/) propriedade que pode ser definida como uma imagem é recuperada de um arquivo local, um recurso inserido ou um URI.

<a name="embedded_images" />

## <a name="embedded-images"></a>Imagens inseridas

Imagens inseridas também são fornecidas com um aplicativo (como imagens locais), mas em vez de ter uma cópia da imagem na estrutura de arquivos de cada aplicativo a imagem do arquivo é inserido no assembly como um recurso. Esse método de distribuição de imagens é especialmente adequado para a criação de componentes, como a imagem é fornecida com o código.

Para inserir uma imagem em um projeto, clique com botão direito para adicionar novos itens e selecione a imagem/s que você deseja adicionar. Por padrão, a imagem será necessário **ação de compilação: nenhum**; isso deve ser definido como **ação de compilação: EmbeddedResource**.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![](images-images/vs-buildaction.png "Definir ação de compilação: EmbeddedResource")

O **ação de compilação** podem ser exibidas e alterados no **propriedades** janela para um arquivo.

Neste exemplo é a ID de recurso **WorkingWithImages.beach.jpg**.
O IDE gerou esse padrão concatenando o **Namespace padrão** para este projeto com o nome do arquivo, usando um ponto (.) entre cada valor.
<!-- https://msdn.microsoft.com/library/ms950960.aspx -->

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

![](images-images/xs-buildaction.png "Definir ação de compilação: EmbeddedResource")

**Ação de compilação** também podem ser exibidas e alterados no **propriedades** preenchimento para um arquivo.
Este painel mostra o **ID de recurso** que é usado para referenciar o recurso no código. Na captura de tela abaixo, o **ID de recurso** é **WorkingWithImages.beach.jpg**.
O IDE gerou esse padrão concatenando o **Namespace padrão** para este projeto com o nome do arquivo, usando um ponto (.) entre cada valor.
Essa ID pode ser editada no **propriedades** teclado, mas para esses exemplos o valor **WorkingWithImages.beach.jpg** será usado.

![](images-images/xs-embeddedproperties.png "Painel de propriedades EmbeddedResource")

-----

Se você colocar imagens inseridas em pastas dentro de seu projeto, os nomes de pasta também serão separados por pontos (.) na ID de recurso. Movendo o **beach.jpg** imagem em uma pasta chamada **MyImages** resultaria em uma ID de recurso de **WorkingWithImages.MyImages.beach.jpg**

O código para carregar uma imagem inserida simplesmente passa o **ID de recurso** para o [ `ImageSource.FromResource` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromResource/p/System.String/) método conforme mostrado abaixo:

```csharp
var embeddedImage = new Image { Source = ImageSource.FromResource("WorkingWithImages.beach.jpg") };
```

Atualmente não há nenhuma conversão implícita para identificadores de recursos. Em vez disso, você deve usar [ `ImageSource.FromResource` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromResource/p/System.String/) ou `new ResourceImageSource()` para carregar imagens inseridas.

As capturas de tela a seguir mostram o resultado de exibição de uma imagem inserida em cada plataforma:

[![ResourceImageSource](images-images/resource-sml.png "exibindo uma imagem inserida do aplicativo de exemplo")](images-images/resource.png#lightbox "exibindo uma imagem inserida do aplicativo de exemplo")

<a name="Embedded_Images_in_Xaml" />

### <a name="using-xaml"></a>Usando XAML

Porque não há nenhum conversor de tipo interno de `string` para `ResourceImageSource`, esses tipos de imagens não podem ser carregados nativamente pelo XAML. Em vez disso, uma extensão de marcação XAML personalizada simple pode ser gravada para carregar imagens usando um **ID de recurso** especificado em XAML:

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
   var imageSource = ImageSource.FromResource(Source);

   return imageSource;
 }
}
```

Para usar essa extensão, adicione um personalizado `xmlns` para XAML, usando os valores corretos de namespace e assembly para o projeto. A origem da imagem pode ser definida usando a seguinte sintaxe: `{local:ImageResource WorkingWithImages.beach.jpg}`. Um exemplo completo de XAML é mostrado abaixo:

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

Porque às vezes é difícil de entender por que um recurso de imagem específico não está sendo carregado, o seguinte código de depuração pode ser adicionado temporariamente a um aplicativo para confirmar que os recursos estão configurados corretamente. Ele mostrará conhecidos todos os recursos incorporados no assembly especificado para o <span class="UIItem">Console</span> para ajudar a depurar problemas de carregamento de recursos.

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

#### <a name="images-embedded-in-other-projects-dont-appear"></a>Imagens inseridas em outros projetos não são exibidos

`Image.FromResource` procura somente imagens no mesmo assembly como chamar o código `FromResource`. Usando o código de depuração anteriormente, você pode determinar quais assemblies contêm um recurso específico, alterando o `typeof()` instrução para um `Type` conhecido em cada assembly.

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

O código c# equivalente é o seguinte:

```csharp
var webImage = new Image { Source = ImageSource.FromUri(new Uri("https://xamarin.com/content/images/pages/forms/example-app.png")) };
```

O [ `ImageSource.FromUri` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromUri/p/System.Uri/) método exige um `Uri` de objeto e retorna um novo [ `UriImageSource` ](https://developer.xamarin.com/api/type/Xamarin.Forms.UriImageSource/) que lê a partir de `Uri`.

Também há uma conversão implícita de cadeias de caracteres URI para que o exemplo a seguir também funcionará:

```csharp
webImage.Source = "https://xamarin.com/content/images/pages/forms/example-app.png";
```

As capturas de tela a seguir mostram o resultado de exibição de uma imagem remota em cada plataforma:

[![Baixado ImageSource](images-images/download-sml.png "exibindo uma imagem de download do aplicativo de exemplo")](images-images/download.png#lightbox "exibindo uma imagem de download do aplicativo de exemplo")

<a name="Image_Caching" />

### <a name="downloaded-image-caching"></a>Cache de imagem baixada

Um [ `UriImageSource` ](https://developer.xamarin.com/api/type/Xamarin.Forms.UriImageSource/) também oferece suporte a cache de imagens baixadas, configuradas por meio das seguintes propriedades:

- [`CachingEnabled`](https://developer.xamarin.com/api/property/Xamarin.Forms.UriImageSource.CachingEnabled/) – Se o cache está habilitado (`true` por padrão).
- [`CacheValidity`](https://developer.xamarin.com/api/property/Xamarin.Forms.UriImageSource.CacheValidity/) -A `TimeSpan` que define por quanto tempo a imagem será armazenada localmente.

O cache é habilitado por padrão e armazenará a imagem localmente por 24 horas. Para desabilitar o cache de uma imagem específica, instanciar a origem da imagem da seguinte maneira:

```csharp
image.Source = new UriImageSource { CachingEnabled = false, Uri="http://server.com/image" };
```

Para definir um período de cache específico (por exemplo, 5 dias) instanciar a origem da imagem da seguinte maneira:

```csharp
webImage.Source = new UriImageSource
{
    Uri = new Uri("https://xamarin.com/content/images/pages/forms/example-app.png"),
    CachingEnabled = true,
    CacheValidity = new TimeSpan(5,0,0,0)
};
```

Armazenamento em cache interno facilita muito dar suporte a cenários, como listas de imagens, onde você pode definir (ou associar) uma imagem de rolagem em cada célula e permitir que o cache interno cuidar de carregar novamente a imagem quando a célula é rolada para o modo de exibição.

<a name="Icons_and_splashscreens" />

## <a name="icons-and-splashscreens"></a>Ícones e telas iniciais

Embora não relacionadas ao [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) exibição, os ícones de aplicativo e telas iniciais também são um importante uso de imagens em projetos do xamarin. Forms.

Configurar ícones e telas iniciais para aplicativos xamarin. Forms é feita em cada um dos projetos de aplicativo. Isso significa gerar corretamente em tamanho de imagens para iOS, Android e UWP. Essas imagens devem ser nomeadas e localizadas de acordo com os requisitos de cada plataforma.

## <a name="icons"></a>Ícones

Consulte o [iOS trabalhando com imagens](~/ios/app-fundamentals/images-icons/index.md), [Google iconografia](http://developer.android.com/design/style/iconography.html), e [diretrizes para ativos de imagem e ícone](/windows/uwp/controls-and-patterns/tiles-and-notifications-app-assets/) para obter mais informações sobre como criar esses recursos de aplicativo.

## <a name="splashscreens"></a>Telas iniciais

Somente aplicativos UWP e iOS exigem uma tela inicial (também chamado de uma imagem de tela ou padrão de inicialização).

Consulte a documentação para [iOS trabalhando com imagens](~/ios/app-fundamentals/images-icons/index.md) e [inicial telas](/windows/uwp/launch-resume/splash-screens/) no Centro de desenvolvimento do Windows.

## <a name="summary"></a>Resumo

Xamarin. Forms oferece várias maneiras diferentes para incluir imagens em um aplicativo de plataforma cruzada, permitindo que a mesma imagem a ser usado em plataformas ou imagens específicas da plataforma seja especificado. Imagens baixadas são automaticamente armazenados em cache, automatizando a um cenário de codificação comuns.

Imagem de ícone e a tela inicial do aplicativo é configuração e configurado para não xamarin. Forms applications - siga as mesmas orientações usada para aplicativos de plataforma específica.


## <a name="related-links"></a>Links relacionados

- [WorkingWithImages (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithImages/)
- [iOS trabalhando com imagens](~/ios/app-fundamentals/images-icons/index.md)
- [Iconografia Android](http://developer.android.com/design/style/iconography.html)
- [Diretrizes para ativos de imagem e ícone](/windows/uwp/controls-and-patterns/tiles-and-notifications-app-assets/)
