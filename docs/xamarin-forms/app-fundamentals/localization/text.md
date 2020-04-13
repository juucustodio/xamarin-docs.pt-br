---
title: Localização de cordas e imagens em Xamarin.Forms
description: Os aplicativos Xamarin.Forms podem ser localizados usando arquivos de recursos .NET.
zone_pivot_groups: platform
ms.prod: xamarin
ms.assetid: 852B4ED3-2D2D-48A5-A759-A6591F6A1509
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 11/01/2019
ms.openlocfilehash: bf99873d88a69a715cdf7969ad94afd66372b5e3
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "74135350"
---
# <a name="xamarinforms-string-and-image-localization"></a>Xamarin.Forms String and Image Localization

[![Baixar](~/media/shared/download.png) amostra Baixar a amostra](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/usingresxlocalization)

Localização é o processo de adaptação de um aplicativo para atender aos requisitos específicos de linguagem ou cultura de um mercado-alvo. Para realizar a localização, o texto e as imagens em um aplicativo podem precisar ser traduzidos para vários idiomas. Um aplicativo localizado exibe automaticamente o texto traduzido com base nas configurações de cultura do dispositivo móvel:

![Capturas de tela do aplicativo de localização no iOS e Android](text-images/localizationdemo-screenshots.png)

O framework .NET inclui um mecanismo incorporado para localizar aplicativos usando [arquivos de recursos Resx](https://docs.microsoft.com/dotnet/framework/resources/creating-resource-files-for-desktop-apps). Um arquivo de recurso armazena texto e outros conteúdos como pares de nome/valor que permitem que o aplicativo recupere conteúdo para uma chave fornecida. Os arquivos de recursos permitem que o conteúdo localizado seja separado do código do aplicativo.

O uso de arquivos de recursos para localizar os aplicativos Xamarin.Forms exige que você execute as seguintes etapas:

1. [Crie arquivos Resx](#create-resx-files) contendo texto traduzido.
1. [Especifique a cultura padrão](#specify-the-default-culture) no projeto compartilhado.
1. [Localize texto em Xamarin.Forms](#localize-text-in-xamarinforms).
1. [Localize imagens](#localize-images) com base em configurações de cultura para cada plataforma.
1. [Localize o nome do aplicativo](#localize-the-application-name) em cada plataforma.
1. [Teste a localização](#test-localization) em cada plataforma.

## <a name="create-resx-files"></a>Criar arquivos Resx

Os arquivos de recursos são arquivos XML com uma extensão **.resx** que são compilados em arquivos binários de recursos (.resources) durante o processo de compilação. O Visual Studio 2019 gera uma classe que fornece uma API usada para recuperar recursos. Um aplicativo localizado normalmente contém um arquivo de recurso padrão com todas as strings usadas no aplicativo, bem como arquivos de recursos para cada idioma suportado. O aplicativo de exemplo tem uma pasta **Resx** no projeto compartilhado que contém os arquivos de recursos e seu arquivo de recurso padrão chamado **AppResources.resx**.

Os arquivos de recursos contêm as seguintes informações para cada item:

- **O nome** especifica a chave usada para acessar o texto em código.
- **O valor** especifica o texto traduzido.
- **Comentário** é um campo opcional que contém informações adicionais.

::: zone pivot="windows"

Um arquivo de recurso é adicionado com a caixa de diálogo **Adicionar novo item** no Visual Studio 2019:

![Adicione um novo recurso no Visual Studio 2019](text-images/pc-add-resource-file.png)

Uma vez que o arquivo é adicionado, as linhas podem ser adicionadas para cada recurso de texto:

![Especifique os recursos de texto padrão em um arquivo .resx](text-images/pc-default-strings.png)

A configuração de sumidção **do Modificador** de acesso determina como o Visual Studio gera a classe usada para acessar recursos. Definir o Modificador de Acesso aos resultados **públicos** ou **internos** em uma classe gerada com o nível de acessibilidade especificado. A configuração do Modificador de Acesso a **Nenhuma geração de código** não gera um arquivo de classe. O arquivo de recurso padrão deve ser configurado para gerar um arquivo de classe, o que resulta em um arquivo com a extensão **.designer.cs** sendo adicionada ao projeto.

Uma vez que o arquivo de recurso padrão é criado, arquivos adicionais podem ser criados para cada cultura que o aplicativo suporta. Cada arquivo de recurso adicional deve incluir a cultura de tradução no nome do arquivo e deve ter o **Modificador de acesso** definido como **Sem geração de código**. 

Em tempo de execução, o aplicativo tenta resolver uma solicitação de recurso por ordem de especificidade. Por exemplo, se a cultura do dispositivo estiver **em en-US,** o aplicativo procurará arquivos de recursos nesta ordem:

1. AppResources.en-US.resx
1. AppResources.en.resx
1. AppResources.resx (padrão)

A captura de tela a seguir mostra um arquivo de tradução em espanhol chamado **AppResources.es.cs**:

![Especifique os recursos de texto padrão em um arquivo .resx](text-images/pc-spanish-strings.png)

O arquivo de tradução usa os **mesmos valores de Nome** especificados no arquivo padrão, mas contém strings de idioma espanhol na coluna **Valor.** Além disso, o **Modificador de Acesso** está definido **como Sem geração de código**.

::: zone-end
::: zone pivot="macos"

Um arquivo de recurso é adicionado com a caixa de diálogo **Adicionar novo arquivo** no Visual Studio 2019 para Mac:

![Adicione um novo recurso no Visual Studio 2019 para Mac](text-images/mac-add-resource-file.png)

Uma vez que um arquivo de recurso padrão `data` tenha sido `root` criado, o texto pode ser adicionado criando elementos dentro do elemento no arquivo de recursos:

```xml
<?xml version="1.0" encoding="utf-8"?>
<root>
    ...
    <data name="AddButton" xml:space="preserve">
        <value>Add Note</value>
    </data>
    <data name="NotesLabel" xml:space="preserve">
        <value>Notes:</value>
    </data>
    <data name="NotesPlaceholder" xml:space="preserve">
        <value>e.g. Get Milk</value>
    </data>
</root>
```

Um arquivo de classe **designer.cs** pode ser criado definindo uma propriedade **De Ferramenta Personalizada** nas opções de arquivo de recursos:

![Ferramenta personalizada especificada nas propriedades de um arquivo de recurso](text-images/mac-resx-properties.png)

A configuração **da ferramenta personalizada** para **PublicResXFileCodeGenerator** resultará em classe gerada com `public` acesso. A configuração **da ferramenta personalizada** para **InternalResXFileCodeGenerator** resultará em uma classe gerada com `internal` acesso. Um valor **de ferramenta personalizado** vazio não gerará uma classe. O nome da classe gerada corresponderá ao nome do arquivo de recurso. Por exemplo, o arquivo **AppResources.resx** resultará `AppResources` na criação de uma classe em um arquivo chamado **AppResources.designer.cs**.

Arquivos de recursos adicionais podem ser criados para cada cultura suportada. Cada arquivo de idioma deve incluir a cultura de tradução no nome do arquivo para que um arquivo direcionado a **es-MX** deve ser nomeado **AppResources.es-MX.resx**.

Em tempo de execução, o aplicativo tenta resolver uma solicitação de recurso por ordem de especificidade. Por exemplo, se a cultura do dispositivo estiver **em en-US,** o aplicativo procurará arquivos de recursos nesta ordem:

1. AppResources.en-US.resx
1. AppResources.en.resx
1. AppResources.resx (padrão)

Os arquivos de tradução de idioma devem ter os **mesmos** valores de Nome especificados como o arquivo padrão. O XML a seguir mostra o arquivo de tradução em espanhol chamado **AppResources.es.resx**:

```xml
<?xml version="1.0" encoding="utf-8"?>
<root>
    ...
    <data name="NotesLabel" xml:space="preserve">
        <value>Notas:</value>
    </data>
    <data name="NotesPlaceholder" xml:space="preserve">
        <value>por ejemplo . comprar leche</value>
    </data>
    <data name="AddButton" xml:space="preserve">
        <value>Agregar nuevo elemento</value>
    </data>
</root>
```

::: zone-end

## <a name="specify-the-default-culture"></a>Especifique a cultura padrão

Para que os arquivos de recursos funcionem corretamente, o aplicativo deve ter um `NeutralResourcesLanguage` especificado. No projeto compartilhado, o arquivo **AssemblyInfo.cs** deve ser personalizado para especificar a cultura padrão. O código a seguir `NeutralResourcesLanguage` mostra como definir o **"en-US"** no arquivo **AssemblyInfo.cs:**

```csharp
using System.Resources;

[assembly: NeutralResourcesLanguage("en-US")]
```

> [!WARNING]
> Se você não `NeutralResourcesLanguage` especificar `ResourceManager` o `null` atributo, a classe retorna valores para quaisquer culturas sem um arquivo de recurso específico. Quando a cultura padrão é `ResourceManager` especificada, os retornos resultam do arquivo Resx padrão para culturas não suportadas. Portanto, é recomendável que você `NeutralResourcesLanguage` sempre especifique o para que o texto seja exibido para culturas sem suporte.

Uma vez que um arquivo de recurso padrão tenha sido criado e a cultura padrão especificada no arquivo **AssemblyInfo.cs,** o aplicativo pode recuperar strings localizadas em tempo de execução.

Para obter mais informações sobre arquivos de recursos, consulte [Criar arquivos de recursos para aplicativos .NET](https://docs.microsoft.com/dotnet/framework/resources/creating-resource-files-for-desktop-apps).

## <a name="localize-text-in-xamarinforms"></a>Localize texto em Xamarin.Forms

O texto é localizado em Xamarin.Forms usando a classe gerada. `AppResources` Esta classe é nomeada com base no nome do arquivo de recurso padrão. Como o arquivo de recurso do projeto de amostra `AppResources`é chamado **AppResources.cs,** o Visual Studio gera uma classe correspondente chamada . Propriedades estáticas `AppResources` são geradas na classe para cada linha no arquivo de recursos. As seguintes propriedades estáticas são `AppResources` geradas na classe do aplicativo de amostra:

- AddButton
- NotasRótulo
- NotasSuporte de lugar

Acessar esses valores como [x:Propriedades estáticas](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md#the-xstatic-markup-extension) permite que o texto localizado seja exibido em XAML:

```xaml
<ContentPage ...
             xmlns:resources="clr-namespace:LocalizationDemo.Resx">
    <Label Text="{x:Static resources:AppResources.NotesLabel}" />
    <Entry Placeholder="{x:Static resources:AppResources.NotesPlaceholder}" />
    <Button Text="{x:Static resources:AppResources.AddButton}" />
</ContentPage>
```

O texto localizado também pode ser recuperado em código:

```csharp
public LocalizedCodePage()
{
    Label notesLabel = new Label
    {
        Text = AppResources.NotesLabel,
        // ...
    };
    
    Entry notesEntry = new Entry
    {
        Placeholder = AppResources.NotesPlaceholder,
        //...
    };
    
    Button addButton = new Button
    {
        Text = AppResources.AddButton,
        // ...
    };
    
    Content = new StackLayout
    {
        Children = {
            notesLabel,
            notesEntry,
            addButton
        }
    };
}
```

As propriedades `AppResources` da classe usam o `System.Globalization.CultureInfo.CurrentUICulture` valor atual do para determinar de qual arquivo de recurso de cultura para recuperar valores.

## <a name="localize-images"></a>Localize imagens

Além de armazenar texto, os arquivos Resx são capazes de armazenar mais do que apenas texto, eles também podem armazenar imagens e dados binários. No entanto, os dispositivos móveis têm uma gama de tamanhos e densidades de tela e cada plataforma móvel tem funcionalidade para exibir imagens dependentes da densidade. Portanto, a funcionalidade de localização de imagens da plataforma deve ser usada em vez de armazenar imagens em arquivos de recursos.

### <a name="localize-images-on-android"></a>Localize imagens no Android

No Android, os desenheáveis localizados (imagens) são armazenados usando uma convenção de nomeação para pastas no diretório **Recursos.** As pastas são nomeadas **desenhadas** com um sufixo para o idioma de destino. Por exemplo, a pasta em espanhol é chamada **drawable-es**.

Quando um código local de quatro letras é necessário, o Android requer um **r** adicional seguindo o painel. Por exemplo, a pasta locale do México (es-MX) deve ser nomeada **drawable-es-rMX**. Os nomes dos arquivos de imagem em cada pasta local devem ser idênticos:

![Imagens localizadas no projeto Android](text-images/pc-android-images.png)

Para obter mais informações, consulte [Android Localizaçãoization](~/android/app-fundamentals/localization.md).

### <a name="localize-images-on-ios"></a>Localize imagens no iOS

No iOS, as imagens localizadas são armazenadas usando uma convenção de nomeação para pastas no diretório **Recursos.** A pasta padrão é chamada **Base.lproj**. As pastas específicas do idioma são nomeadas com o idioma ou nome local, seguidas por **.lproj**. Por exemplo, a pasta em espanhol é chamada **es.lproj**.

Códigos locais de quatro letras funcionam como códigos de linguagem de duas letras. Por exemplo, a pasta locale do México (es-MX) deve ser nomeada **es-MX.lproj**. Os nomes dos arquivos de imagem em cada pasta local devem ser idênticos:

![Imagens localizadas no projeto iOS](text-images/pc-ios-images.png)

> [!NOTE]
> O iOS suporta a criação de um Catálogo de Ativos localizado em vez de usar a estrutura da pasta .lproj. No entanto, estes devem ser criados e gerenciados em Xcode.

Para obter mais informações, consulte [iOS Localização .](~/ios/app-fundamentals/localization/index.md)

### <a name="localize-images-on-uwp"></a>Localize imagens no UWP

No UWP, as imagens localizadas são armazenadas usando uma convenção de nomeação para pastas no diretório **Ativos/Imagens.** As pastas são nomeadas com o idioma ou local. Por exemplo, a pasta em espanhol é nomeada **es** e a pasta locale do México deve ser nomeada **es-MX**. Os nomes dos arquivos de imagem em cada pasta local devem ser idênticos:

![Imagens localizadas no projeto UWP](text-images/pc-uwp-images.png)

Para obter mais informações, confira [Localização da UWP](/windows/uwp/design/globalizing/globalizing-portal/).

### <a name="consume-localized-images"></a>Consumir imagens localizadas

Como cada plataforma armazena imagens com uma estrutura `OnPlatform` de arquivo `ImageSource` única, o XAML usa a classe para definir a propriedade com base na plataforma atual:

```xaml
<Image>
    <Image.Source>
        <OnPlatform x:TypeArguments="ImageSource">
            <On Platform="iOS, Android" Value="flag.png" />
            <On Platform="UWP" Value="Assets/Images/flag.png" />
        </OnPlatform>
    </Image.Source>
</Image>
```

> [!NOTE]
> A `OnPlatform` extensão de marcação oferece uma maneira mais concisa de especificar valores específicos da plataforma. Para obter mais informações, consulte [a extensão de marcação OnPlatform](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform-markup-extension).

A fonte de imagem pode `Device.RuntimePlatform` ser definida com base na propriedade em código:

```csharp
string imgSrc = Device.RuntimePlatform == Device.UWP ? "Assets/Images/flag.png" : "flag.png";
Image flag = new Image
{
    Source = ImageSource.FromFile(imgSrc),
    WidthRequest = 100
};
```

## <a name="localize-the-application-name"></a>Localize o nome do aplicativo

O nome do aplicativo é especificado por plataforma e não usa arquivos de recursos da Resx. Para localizar o nome do aplicativo no Android, consulte [o nome do aplicativo Localize no Android](~/android/app-fundamentals/localization.md#stringsxml-file-format). Para localizar o nome do aplicativo no iOS, consulte [o nome do aplicativo Localize no iOS](~/ios/app-fundamentals/localization/index.md#app-name). Para localizar o nome do aplicativo no UWP, consulte [Localize strings no manifesto do pacote UWP](https://docs.microsoft.com/windows/uwp/app-resources/localize-strings-ui-manifest).

## <a name="test-localization"></a>Localização do teste

Testar a localização é melhor realizado mudando o idioma do seu dispositivo. É possível definir o `System.Globalization.CultureInfo.CurrentUICulture` valor do código, mas o comportamento é inconsistente em todas as plataformas, portanto, isso não é recomendado para testes.

No iOS, no aplicativo de configurações, você pode definir o idioma para cada aplicativo especificamente sem alterar o idioma do seu dispositivo.

No Android, as configurações do idioma são detectadas e armazenadas em cache quando o aplicativo é iniciado. Se você alterar idiomas, talvez seja necessário sair e reiniciar o aplicativo para ver as alterações aplicadas.

## <a name="related-links"></a>Links relacionados

- [Projeto amostral de localização](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/usingresxlocalization)
- [Criar arquivos de recurso para aplicativos .NET](https://docs.microsoft.com/dotnet/framework/resources/creating-resource-files-for-desktop-apps)
- [Localização multiplataforma](~/cross-platform/app-fundamentals/localization.md)
- [Usando a classe CultureInfo (MSDN)](https://docs.microsoft.com/dotnet/api/system.globalization.cultureinfo)
- [Localização no Android](~/android/app-fundamentals/localization.md)
- [Localização no iOS](~/ios/app-fundamentals/localization/index.md)
- [Localização na UWP](/windows/uwp/design/globalizing/globalizing-portal/)
- [Localizando e usando recursos para uma cultura específica (MSDN)](https://msdn.microsoft.com/library/s9ckwb4b%28v=vs.90%29.aspx)
