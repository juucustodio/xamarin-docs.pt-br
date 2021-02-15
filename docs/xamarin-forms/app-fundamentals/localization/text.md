---
title: Localização de cadeia de caracteres e imagem em Xamarin.Forms
description: Xamarin.Forms os aplicativos podem ser localizados usando arquivos de recursos do .NET.
zone_pivot_groups: platform
ms.prod: xamarin
ms.assetid: 852B4ED3-2D2D-48A5-A759-A6591F6A1509
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 11/01/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: cb811972d622ffdcf6042e030528a88fb18abaf7
ms.sourcegitcommit: 10c7dd16fe78226053d1d036492b6c9102fc421b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "93369799"
---
# <a name="xamarinforms-string-and-image-localization"></a>Xamarin.Forms Localização de cadeia de caracteres e imagem

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/usingresxlocalization)

A localização é o processo de adaptação de um aplicativo para atender à linguagem específica ou aos requisitos culturais de um mercado-alvo. Para realizar a localização, o texto e as imagens em um aplicativo podem precisar ser traduzidos em vários idiomas. Um aplicativo localizado exibe automaticamente o texto traduzido com base nas configurações de cultura do dispositivo móvel:

![Capturas de tela do aplicativo de localização no iOS e no Android](text-images/localizationdemo-screenshots.png)

O .NET Framework inclui um mecanismo interno para localizar aplicativos usando arquivos de [recurso resx](/dotnet/framework/resources/creating-resource-files-for-desktop-apps). Um arquivo de recurso armazena texto e outro conteúdo como pares de nome/valor que permitem que o aplicativo recupere conteúdo para uma chave fornecida. Os arquivos de recurso permitem que o conteúdo localizado seja separado do código do aplicativo.

O uso de arquivos de recursos para localizar Xamarin.Forms aplicativos exige que você execute as seguintes etapas:

1. [Crie arquivos resx](#create-resx-files) contendo texto traduzido.
1. [Especifique a cultura padrão](#specify-the-default-culture) no projeto compartilhado.
1. [Localizar texto em Xamarin.Forms ](#localize-text-in-xamarinforms).
1. [Localize imagens](#localize-images) com base nas configurações de cultura para cada plataforma.
1. [Localize o nome do aplicativo](#localize-the-application-name) em cada plataforma.
1. [Teste a localização](#test-localization) em cada plataforma.

## <a name="create-resx-files"></a>Criar arquivos resx

Arquivos de recurso são arquivos XML com uma extensão **. resx** que são compilados em arquivos de recurso binário (. Resources) durante o processo de compilação. O Visual Studio 2019 gera uma classe que fornece uma API usada para recuperar recursos. Um aplicativo localizado normalmente contém um arquivo de recurso padrão com todas as cadeias de caracteres usadas no aplicativo, bem como arquivos de recursos para cada idioma com suporte. O aplicativo de exemplo tem uma pasta **resx** no projeto compartilhado que contém os arquivos de recurso e seu arquivo de recurso padrão chamado **AppResources. resx**.

Os arquivos de recurso contêm as seguintes informações para cada item:

- **Nome** especifica a chave usada para acessar o texto no código.
- **Valor** especifica o texto traduzido.
- **Comment** é um campo opcional que contém informações adicionais.

::: zone pivot="windows"

Um arquivo de recurso é adicionado com a caixa de diálogo **Adicionar novo item** no Visual Studio 2019:

![Adicionar um novo recurso no Visual Studio 2019](text-images/pc-add-resource-file.png)

Depois que o arquivo é adicionado, as linhas podem ser adicionadas para cada recurso de texto:

![Especificar recursos de texto padrão em um arquivo. resx](text-images/pc-default-strings.png)

A configuração suspensa do **modificador de acesso** determina como o Visual Studio gera a classe usada para acessar recursos. Definir o modificador de acesso como resultados **públicos** ou **internos** em uma classe gerada com o nível de acessibilidade especificado. Definir o modificador de acesso como **sem geração de código** não gera um arquivo de classe. O arquivo de recurso padrão deve ser configurado para gerar um arquivo de classe, o que resulta em um arquivo com a extensão **. designer.cs** que está sendo adicionada ao projeto.

Depois que o arquivo de recurso padrão é criado, arquivos adicionais podem ser criados para cada cultura à qual o aplicativo dá suporte. Cada arquivo de recurso adicional deve incluir a cultura de tradução no nome de arquivo e deve ter o **modificador de acesso** definido como **sem geração de código**.

Em tempo de execução, o aplicativo tenta resolver uma solicitação de recurso em ordem de especificidade. Por exemplo, se a cultura do dispositivo for **en-US** , o aplicativo procura por arquivos de recurso nesta ordem:

1. AppResources. en-US. resx
1. AppResources. en. resx
1. AppResources. resx (padrão)

A captura de tela a seguir mostra um arquivo de tradução em espanhol chamado **AppResources.es.cs**:

![Especificar os recursos de texto padrão do espanhol em um arquivo. resx](text-images/pc-spanish-strings.png)

O arquivo de conversão usa os mesmos valores de **nome** especificados no arquivo padrão, mas contém cadeias de caracteres de idioma espanhol na coluna **valor** . Além disso, o **modificador de acesso** é definido como **sem geração de código**.

::: zone-end
::: zone pivot="macos"

Um arquivo de recurso é adicionado com a caixa de diálogo **Adicionar novo arquivo** no Visual Studio 2019 para Mac:

![Adicionar um novo recurso no Visual Studio 2019 para Mac](text-images/mac-add-resource-file.png)

Depois que um arquivo de recurso padrão tiver sido criado, o texto poderá ser adicionado criando `data` elementos dentro do `root` elemento no arquivo de recurso:

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

Um arquivo de classe **. designer.cs** pode ser criado definindo-se uma propriedade de **ferramenta personalizada** nas opções de arquivo de recurso:

![Ferramenta personalizada especificada nas propriedades de um arquivo de recurso](text-images/mac-resx-properties.png)

Definir a **ferramenta personalizada** como **PublicResXFileCodeGenerator** resultará na classe gerada com `public` acesso. Definir a **ferramenta personalizada** como **InternalResXFileCodeGenerator** resultará em uma classe gerada com `internal` acesso. Um valor de **ferramenta personalizada** vazio não gerará uma classe. O nome de classe gerado corresponderá ao nome do arquivo de recurso. Por exemplo, o arquivo **AppResources. resx** resultará na criação de uma `AppResources` classe em um arquivo chamado **AppResources.designer.cs**.

Arquivos de recurso adicionais podem ser criados para cada cultura com suporte. Cada arquivo de idioma deve incluir a cultura de tradução no nome de arquivo, de modo que um file Targeting **es-MX** deve ser nomeado **AppResources.es-MX. resx**.

Em tempo de execução, o aplicativo tenta resolver uma solicitação de recurso em ordem de especificidade. Por exemplo, se a cultura do dispositivo for **en-US** , o aplicativo procura por arquivos de recurso nesta ordem:

1. AppResources. en-US. resx
1. AppResources. en. resx
1. AppResources. resx (padrão)

Os arquivos de tradução de idioma devem ter os mesmos valores de **nome** especificados como o arquivo padrão. O XML a seguir mostra o arquivo de tradução em espanhol chamado **AppResources. es. resx**:

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

## <a name="specify-the-default-culture"></a>Especificar a cultura padrão

Para que os arquivos de recurso funcionem corretamente, o aplicativo deve ter um `NeutralResourcesLanguage` especificado. No projeto compartilhado, o arquivo **AssemblyInfo.cs** deve ser personalizado para especificar a cultura padrão. O código a seguir mostra como definir o `NeutralResourcesLanguage` para **en-US** no arquivo **AssemblyInfo.cs** :

```csharp
using System.Resources;

// The resources from the neutral language .resx file are stored directly
// within the library assembly. For that reason, changing en-US to a different
// language in this line will not by itself change the language shown in the
// app. See the discussion of UltimateResourceFallbackLocation in the
// documentation for additional information:
// https://docs.microsoft.com/dotnet/api/system.resources.neutralresourceslanguageattribute
[assembly: NeutralResourcesLanguage("en-US")]
```

> [!WARNING]
> Se você não especificar o `NeutralResourcesLanguage` atributo, a `ResourceManager` classe retornará `null` valores para quaisquer culturas sem um arquivo de recurso específico. Quando a cultura padrão é especificada, o `ResourceManager` retorna os resultados do arquivo resx padrão para culturas sem suporte. Portanto, é recomendável que você sempre especifique o `NeutralResourcesLanguage` para que o texto seja exibido para culturas sem suporte.

Depois que um arquivo de recurso padrão tiver sido criado e a cultura padrão especificada no arquivo **AssemblyInfo.cs** , o aplicativo poderá recuperar cadeias de caracteres localizadas em tempo de execução.

Para obter mais informações sobre arquivos de recursos, consulte [criar arquivos de recursos para aplicativos .net](/dotnet/framework/resources/creating-resource-files-for-desktop-apps).

## <a name="specify-supported-languages-on-ios"></a>Especificar idiomas com suporte no iOS

No iOS, você deve declarar todos os idiomas com suporte no arquivo **info. plist** para seu projeto. No arquivo **info. plist** , use a exibição de **origem** para definir uma matriz para a `CFBundleLocalizations` chave e forneça valores que correspondam aos arquivos resx. Além disso, certifique-se de definir um idioma esperado por meio da `CFBundleDevelopmentRegion` chave:

![Captura de tela do editor info. plist mostrando a seção localizações](text-images/info-plist.png)

Como alternativa, abra o arquivo **info. plist** em um editor de XML e adicione o seguinte:

```xml
<key>CFBundleLocalizations</key>
<array>
    <string>de</string>
    <string>es</string>
    <string>fr</string>
    <string>ja</string>
    <string>pt</string> <!-- Brazil -->
    <string>pt-PT</string> <!-- Portugal -->
    <string>ru</string>
    <string>zh-Hans</string>
    <string>zh-Hant</string>
</array>
<key>CFBundleDevelopmentRegion</key>
<string>en</string>
```

> [!NOTE]
> A Apple trata os Português de forma ligeiramente diferente do que você pode esperar. Para obter mais informações, consulte [adicionando idiomas](https://developer.apple.com/library/archive/documentation/MacOSX/Conceptual/BPInternational/LocalizingYourApp/LocalizingYourApp.html#//apple_ref/doc/uid/10000171i-CH5-SW2) em developer.Apple.com.

Para obter mais informações, consulte [especificando os idiomas padrão e com suporte no info. plist](~/ios/app-fundamentals/localization/index.md#specifying-default-and-supported-languages-in-infoplist).

## <a name="localize-text-in-xamarinforms"></a>Localizar texto em Xamarin.Forms

O texto é localizado em Xamarin.Forms usando a `AppResources` classe gerada. Essa classe é nomeada com base no nome do arquivo de recurso padrão. Como o arquivo de recurso de projeto de exemplo é denominado **AppResources.cs**, o Visual Studio gera uma classe correspondente chamada `AppResources` . As propriedades estáticas são geradas na `AppResources` classe para cada linha no arquivo de recurso. As propriedades estáticas a seguir são geradas na classe do aplicativo de exemplo `AppResources` :

- AddButton
- NotesLabel
- NotesPlaceholder

Acessar esses valores como propriedades [x:static](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md#the-xstatic-markup-extension) permite que o texto localizado seja exibido em XAML:

```xaml
<ContentPage ...
             xmlns:resources="clr-namespace:LocalizationDemo.Resx">
    <Label Text="{x:Static resources:AppResources.NotesLabel}" />
    <Entry Placeholder="{x:Static resources:AppResources.NotesPlaceholder}" />
    <Button Text="{x:Static resources:AppResources.AddButton}" />
</ContentPage>
```

O texto localizado também pode ser recuperado no código:

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

As propriedades na `AppResources` classe usam o valor atual de `System.Globalization.CultureInfo.CurrentUICulture` para determinar para qual arquivo de recurso de cultura recuperar valores.

## <a name="localize-images"></a>Localizar imagens

Além de armazenar texto, os arquivos resx são capazes de armazenar mais do que apenas texto, eles também podem armazenar imagens e dados binários. No entanto, os dispositivos móveis têm uma variedade de tamanhos de tela e densidades, e cada plataforma móvel tem funcionalidade para exibir imagens dependentes de densidade. Portanto, a funcionalidade de localização de imagem de plataforma deve ser usada em vez de armazenar imagens em arquivos de recursos.

### <a name="localize-images-on-android"></a>Localizar imagens no Android

No Android, drawables localizadas (imagens) são armazenadas usando uma Convenção de nomenclatura para pastas no diretório de **recursos** . As pastas são nomeadas como **empates** com um sufixo para o idioma de destino. Por exemplo, a pasta espanhol-Language é denominada **Drawable-es**.

Quando um código de localidade de quatro letras é necessário, o Android requer um **r** adicional após o traço. Por exemplo, a pasta de localidade do México (es-MX) deve ser nomeada **draw-es-rMX**. Os nomes de arquivo de imagem em cada pasta de localidade devem ser idênticos:

![Imagens localizadas no projeto do Android](text-images/pc-android-images.png)

Para obter mais informações, consulte [localização do Android](~/android/app-fundamentals/localization.md).

### <a name="localize-images-on-ios"></a>Localizar imagens no iOS

No iOS, as imagens localizadas são armazenadas usando uma Convenção de nomenclatura para pastas no diretório de **recursos** . A pasta padrão é denominada **base. lproj**. As pastas específicas do idioma são nomeadas com o idioma ou o nome da localidade, seguido de **. lproj**. Por exemplo, a pasta espanhol-Language é chamada **es. lproj**.

Os códigos locais de quatro letras funcionam exatamente como códigos de idioma de duas letras. Por exemplo, a pasta de localidade do México (es-MX) deve ser nomeada **es-MX. lproj**. Os nomes de arquivo de imagem em cada pasta de localidade devem ser idênticos:

![Imagens localizadas no projeto do iOS](text-images/pc-ios-images.png)

> [!NOTE]
> o iOS dá suporte à criação de um catálogo de ativos localizado em vez de usar a estrutura de pastas. lproj. No entanto, eles devem ser criados e gerenciados no Xcode.

Para obter mais informações, consulte [localização do IOS](~/ios/app-fundamentals/localization/index.md).

### <a name="localize-images-on-uwp"></a>Localizar imagens no UWP

No UWP, as imagens localizadas são armazenadas usando uma Convenção de nomenclatura para pastas no diretório de **ativos/imagens** . As pastas são nomeadas com o idioma ou a localidade. Por exemplo, a pasta espanhol-Language é chamada **es** e a pasta de localidade do México deve ser nomeada **es-MX**. Os nomes de arquivo de imagem em cada pasta de localidade devem ser idênticos:

![Imagens localizadas no projeto UWP](text-images/pc-uwp-images.png)

Para obter mais informações, confira [Localização da UWP](/windows/uwp/design/globalizing/globalizing-portal/).

### <a name="consume-localized-images"></a>Consumir imagens localizadas

Como cada plataforma armazena imagens com uma estrutura de arquivo exclusiva, o XAML usa a `OnPlatform` classe para definir a `ImageSource` propriedade com base na plataforma atual:

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
> A `OnPlatform` extensão de marcação oferece uma maneira mais concisa de especificar valores específicos da plataforma. Para obter mais informações, consulte [extensão de marcação do onplatform](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform-markup-extension).

A origem da imagem pode ser definida com base na `Device.RuntimePlatform` propriedade no código:

```csharp
string imgSrc = Device.RuntimePlatform == Device.UWP ? "Assets/Images/flag.png" : "flag.png";
Image flag = new Image
{
    Source = ImageSource.FromFile(imgSrc),
    WidthRequest = 100
};
```

## <a name="localize-the-application-name"></a>Localizar o nome do aplicativo

O nome do aplicativo é especificado por plataforma e não usa arquivos de recurso resx. Para localizar o nome do aplicativo no Android, consulte [localizar o nome do aplicativo no Android](~/android/app-fundamentals/localization.md#stringsxml-file-format). Para localizar o nome do aplicativo no iOS, consulte [localizar o nome do aplicativo no Ios](~/ios/app-fundamentals/localization/index.md#app-name). Para localizar o nome do aplicativo no UWP, consulte [Localizar cadeias de caracteres no manifesto do pacote UWP](/windows/uwp/app-resources/localize-strings-ui-manifest).

## <a name="test-localization"></a>Localização de teste

O teste da localização é melhor feito alterando o idioma do dispositivo. É possível definir o valor de `System.Globalization.CultureInfo.CurrentUICulture` no código, mas o comportamento é inconsistente entre as plataformas para que isso não seja recomendado para teste.

No iOS, no aplicativo configurações, você pode definir o idioma para cada aplicativo especificamente sem alterar o idioma do dispositivo.

No Android, as configurações de idioma são detectadas e armazenadas em cache quando o aplicativo é iniciado. Se você alterar os idiomas, talvez seja necessário sair e reiniciar o aplicativo para ver as alterações aplicadas.

## <a name="related-links"></a>Links relacionados

- [Projeto de exemplo de localização](/samples/xamarin/xamarin-forms-samples/usingresxlocalization)
- [Criar arquivos de recurso para aplicativos .NET](/dotnet/framework/resources/creating-resource-files-for-desktop-apps)
- [Localização multiplataforma](~/cross-platform/app-fundamentals/localization.md)
- [Usando a classe CultureInfo (MSDN)](/dotnet/api/system.globalization.cultureinfo)
- [Localização no Android](~/android/app-fundamentals/localization.md)
- [Localização no iOS](~/ios/app-fundamentals/localization/index.md)
- [Localização na UWP](/windows/uwp/design/globalizing/globalizing-portal/)
- [Localizando e usando recursos para uma cultura específica (MSDN)](/previous-versions/visualstudio/visual-studio-2008/s9ckwb4b(v=vs.90))