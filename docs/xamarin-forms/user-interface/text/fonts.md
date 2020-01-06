---
title: Fontes no xamarin. Forms
description: Este artigo explica como especificar informações de fonte em controles que exibem texto em aplicativos xamarin. Forms.
ms.prod: xamarin
ms.assetid: 49DD2249-C575-41AE-AE06-08F890FD6031
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/04/2019
ms.openlocfilehash: 62bc5d2012df4880e9257ac70d0fc2e0fca4af64
ms.sourcegitcommit: 619b32f4f96a255140963afcf87629ca690af93d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/27/2019
ms.locfileid: "75500449"
---
# <a name="fonts-in-xamarinforms"></a>Fontes no xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithfonts)

Este artigo descreve como o xamarin. Forms permite especificar atributos de fonte (incluindo o tamanho e peso) em controles que exibem texto. Informações de fonte podem ser [especificado no código](#Setting_Font_in_Code) ou [especificado no XAML](#Setting_Font_in_Xaml). Também é possível usar uma [fonte personalizada](#Using_a_Custom_Font)e [exibir ícones de fonte](#display-font-icons).

<a name="Setting_Font_in_Code" />

## <a name="set-the-font-in-code"></a>Definir a fonte no código

Use as três propriedades de fonte de todos os controles que exibem texto:

- **FontFamily** &ndash; o `string` nome da fonte.
- **FontSize** &ndash; o tamanho da fonte como um `double`.
- **FontAttributes** &ndash; especificando informações de estilo, como uma cadeia de caracteres *itálico* e **negrito** (usando o `FontAttributes` enumeração em C#).

Este código mostra como criar um rótulo e especificar o tamanho da fonte e o peso para exibir:

```csharp
var about = new Label {
    FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)),
    FontAttributes = FontAttributes.Bold,
    Text = "Medium Bold Font"
};
```

<a name="FontSize" />

### <a name="font-size"></a>Tamanho da fonte

O `FontSize` propriedade pode ser definida como um valor duplo, por exemplo:

```csharp
label.FontSize = 24;
```

O valor de tamanho é medido em unidades independentes de dispositivo. Para obter mais informações, consulte [unidades de medida](~/xamarin-forms/user-interface/controls/common-properties.md#units-of-measurement).

O Xamarin. Forms também define campos na enumeração de [`NamedSize`](xref:Xamarin.Forms.NamedSize) que representam tamanhos de fonte específicos. Para obter mais informações sobre tamanhos de fonte nomeados, consulte [tamanhos de fonte nomeados](#named-font-sizes).

<a name="FontAttributes" />

### <a name="font-attributes"></a>Atributos de fonte

Estilos de fonte, como **negrito** e *itálico* podem ser definidos na `FontAttributes` propriedade. Atualmente, há suporte para os seguintes valores:

- **Nenhum**
- **Negrito**
- **Itálico**

O `FontAttribute` enumeração pode ser usada da seguinte maneira (você pode especificar um único atributo ou `OR` -los juntos):

```csharp
label.FontAttributes = FontAttributes.Bold | FontAttributes.Italic;
```

### <a name="set-font-info-per-platform"></a>Definir informações de fonte por plataforma

Como alternativa, o `Device.RuntimePlatform` propriedade pode ser usada para definir os nomes de fontes diferentes em cada plataforma, conforme demonstrado neste código:

```csharp
label.FontFamily = Device.RuntimePlatform == Device.iOS ? "Lobster-Regular" :
   Device.RuntimePlatform == Device.Android ? "Lobster-Regular.ttf#Lobster-Regular" : "Assets/Fonts/Lobster-Regular.ttf#Lobster",
label.FontSize = Device.RuntimePlatform == Device.iOS ? 24 :
   Device.RuntimePlatform == Device.Android ? Device.GetNamedSize(NamedSize.Medium, label) : Device.GetNamedSize(NamedSize.Large, label);
```

É uma boa fonte de informações de fonte para iOS [iosfonts.com](http://iosfonts.com).

<a name="Setting_Font_in_Xaml" />

## <a name="set-the-font-in-xaml"></a>Definir a fonte em XAML

Xamarin. Forms controla esse texto de exibição todas têm um `FontSize` propriedade que pode ser definida em XAML. A maneira mais simples para definir a fonte em XAML é usar os valores de enumeração de tamanho, conforme mostrado neste exemplo:

```xaml
<Label Text="Login" FontSize="Large"/>
<Label Text="Instructions" FontSize="Small"/>
```

Não há um conversor interno para o `FontSize` propriedade que permite que todas as configurações de fonte a ser expressa como um valor de cadeia de caracteres em XAML. Além disso, a propriedade `FontAttributes` pode ser usada para especificar atributos de fonte:

```xaml
<Label Text="Italics are supported" FontAttributes="Italic" />
<Label Text="Biggest NamedSize" FontSize="Large" />
<Label Text="Use size 72" FontSize="72" />
```

[`Device.RuntimePlatform`](~/xamarin-forms/platform/device.md#providing-platform-specific-values) também pode ser usado em XAML para renderizar uma fonte diferente em cada plataforma. O exemplo a seguir usa uma face de fonte personalizada no iOS (MarkerFelt-fino) e especifica apenas o tamanho/atributos nas outras plataformas:

```xaml
<Label Text="Hello Forms with XAML">
    <Label.FontFamily>
        <OnPlatform x:TypeArguments="x:String">
                <On Platform="iOS" Value="MarkerFelt-Thin" />
                <On Platform="Android" Value="Lobster-Regular.ttf#Lobster-Regular" />
                <On Platform="UWP" Value="Assets/Fonts/Lobster-Regular.ttf#Lobster" />
        </OnPlatform>
    </Label.FontFamily>
</Label>
```

Ao especificar uma face de fonte personalizado, é sempre uma boa ideia usar `OnPlatform`, que é difícil encontrar uma fonte que está disponível em todas as plataformas.

## <a name="named-font-sizes"></a>Tamanhos de fonte nomeados

O Xamarin. Forms define os campos na enumeração de [`NamedSize`](xref:Xamarin.Forms.NamedSize) que representam tamanhos de fonte específicos. A tabela a seguir mostra os membros do `NamedSize` e seus tamanhos padrão no iOS, Android e na Plataforma Universal do Windows (UWP):

| {1&gt;Membro&lt;1} | iOS | Android | UWP |
| --- | --- | --- | --- |
| `Default` | 16 | 14 | 14 |
| `Micro` | 11 | 10 | 15,667 |
| `Small` | 13 | 14 | 18,667 |
| `Medium` | 16 | 17 | 22,667 |
| `Large` | 20 | 22 | 32 |
| `Body` | 17 | 16 | 14 |
| `Header` | 17 | 96 | 46 |
| `Title` | 28 | 24 | 24 |
| `Subtitle` | 22 | 16 | 20 |
| `Caption` | 12 | 12 | 12 |

Os valores de tamanho são medidos em unidades independentes de dispositivo. Para obter mais informações, consulte [unidades de medida](~/xamarin-forms/user-interface/controls/common-properties.md#units-of-measurement).

Os tamanhos de fonte nomeados podem ser definidos por meio de XAML e código. Além disso, o método `Device.GetNamedSize` pode ser chamado para retornar um `double` que representa o tamanho da fonte nomeada:

```csharp
label.FontSize = Device.GetNamedSize(NamedSize.Small, typeof(Label));
```

> [!NOTE]
> No iOS e no Android, os tamanhos de fonte nomeados serão dimensionamento automático com base nas opções de acessibilidade do sistema operacional. Esse comportamento pode ser desabilitado no iOS com uma plataforma específica. Para obter mais informações, consulte [dimensionamento de acessibilidade para tamanhos de fonte nomeados no Ios](~/xamarin-forms/platform/ios/named-font-size-scaling.md).

<a name="Using_a_Custom_Font" />

## <a name="use-a-custom-font"></a>Usar uma fonte personalizada

Usando uma fonte que não sejam de tipos internos requer alguma codificação específicos da plataforma. Esta captura de tela mostra a fonte personalizada **Lobster** partir [fontes de código-fonte aberto do Google](https://www.google.com/fonts) renderizado usando o xamarin. Forms.

 [![Fonte personalizada no iOS e Android](fonts-images/custom-sml.png "Exemplo de fontes personalizadas")](fonts-images/custom.png#lightbox "Exemplo de fontes personalizadas")

As etapas necessárias para cada plataforma são descritas abaixo. Ao incluir arquivos de fonte personalizado com um aplicativo, certifique-se de verificar que permite a licença da fonte para distribuição.

### <a name="ios"></a>iOS

É possível exibir uma fonte personalizada primeiro garantir que ele é carregado e, em seguida, fazendo referência a ele por nome usando o xamarin. Forms `Font` métodos.
Siga as instruções em [esta postagem de blog](https://devblogs.microsoft.com/xamarin/custom-fonts-in-ios/):

1. Adicione o arquivo de fonte com **ação de compilação: BundleResource**, e
2. Atualização do **Info. plist** arquivo (**fontes fornecidas pelo aplicativo**, ou `UIAppFonts`, key), em seguida,
3. Fazer referência a ele por nome sempre que você definir uma fonte no xamarin. Forms!

```csharp
new Label
{
    Text = "Hello, Forms!",
    FontFamily = Device.RuntimePlatform == Device.iOS ? "Lobster-Regular" : null // set only for iOS
}
```

### <a name="android"></a>Android

Xamarin. Forms para Android pode fazer referência a uma fonte personalizada que foi adicionada ao projeto seguindo um padrão de nomenclatura específico. Primeiro, adicione o arquivo de fonte para o **ativos** pasta no projeto de aplicativo e defina *Build Action: AndroidAsset*. Em seguida, use o caminho completo e *nome da fonte* separados por um hash (#) como o nome da fonte no xamarin. Forms, como o trecho de código a seguir demonstra:

```csharp
new Label
{
  Text = "Hello, Forms!",
  FontFamily = Device.RuntimePlatform == Device.Android ? "Lobster-Regular.ttf#Lobster-Regular" : null // set only for Android
}
```

### <a name="windows"></a>Portal

Xamarin. Forms para plataformas do Windows pode fazer referência a uma fonte personalizada que foi adicionada ao projeto seguindo um padrão de nomenclatura específico. Primeiro, adicione o arquivo de fonte para o **/Assets/fontes/** pasta no projeto de aplicativo e defina as **compilar: conteúdo da ação**. Em seguida, use a fonte e o caminho do nome de arquivo completo, seguido por um hash (#) e o **nome da fonte**, como o trecho de código a seguir demonstra:

```csharp
new Label
{
    Text = "Hello, Forms!",
    FontFamily = Device.RuntimePlatform == Device.UWP ? "Assets/Fonts/Lobster-Regular.ttf#Lobster" : null // set only for UWP apps
}
```

> [!NOTE]
> Observe que o nome do arquivo de fonte e o nome da fonte podem ser diferentes. Para descobrir o nome da fonte no Windows, clique no arquivo. ttf e selecione **visualização**. O nome da fonte pode ser determinado, em seguida, na janela de visualização.

### <a name="xaml"></a>XAML

Você também pode usar [ `Device.RuntimePlatform` ](~/xamarin-forms/platform/device.md#interact-with-the-ui-from-background-threads) no XAML para renderizar uma fonte personalizada:

```xaml
<Label Text="Hello Forms with XAML">
    <Label.FontFamily>
        <OnPlatform x:TypeArguments="x:String">
                <On Platform="iOS" Value="Lobster-Regular" />
                <On Platform="Android" Value="Lobster-Regular.ttf#Lobster-Regular" />
                <On Platform="UWP" Value="Assets/Fonts/Lobster-Regular.ttf#Lobster" />
        </OnPlatform>
    </Label.FontFamily>
</Label>
```

## <a name="display-font-icons"></a>Exibir ícones de fonte

Os ícones de fonte podem ser exibidos por aplicativos Xamarin. Forms especificando os dados de ícone de fonte em um objeto `FontImageSource`. Essa classe, que deriva da classe [`ImageSource`](xref:Xamarin.Forms.ImageSource) , tem as seguintes propriedades:

- `Glyph` – o valor de caractere Unicode do ícone de fonte, especificado como um `string`.
- `Size` – um valor de `double` que indica o tamanho, em unidades independentes de dispositivo, do ícone de fonte renderizado. O valor padrão é 30. Além disso, essa propriedade pode ser definida como um tamanho de fonte nomeado.
- `FontFamily` – um `string` que representa a família de fontes à qual o ícone de fonte pertence.
- `Color` – um valor de [`Color`](xref:Xamarin.Forms.Color) opcional a ser usado ao exibir o ícone de fonte.

Esses dados são usados para criar um PNG, que pode ser exibido por qualquer exibição que possa exibir um `ImageSource`. Essa abordagem permite que ícones de fonte, como emojis, sejam exibidos por várias exibições, em vez de limitar a exibição do ícone de fonte a uma exibição de apresentação de texto único, como uma [`Label`](xref:Xamarin.Forms.Label).

> [!IMPORTANT]
> Os ícones de fonte só podem ser especificados por sua representação de caractere Unicode.

O exemplo XAML a seguir tem um único ícone de fonte sendo exibido por um [`Image`](xref:Xamarin.Forms.Image) exibição:

```xaml
<Image BackgroundColor="#D1D1D1">
    <Image.Source>
        <FontImageSource Glyph="&#xf30c;"
                         FontFamily="{OnPlatform iOS=Ionicons, Android=ionicons.ttf#}"
                         Size="44" />
    </Image.Source>
</Image>
```

Esse código exibe um ícone do XBox, da família de fontes Ionicons, em uma exibição [`Image`](xref:Xamarin.Forms.Image) . Observe que, embora o caractere Unicode para esse ícone seja `\uf30c`, ele deve ser ignorado em XAML e, portanto, se torna `&#xf30c;`. Este é o código C# equivalente:

```csharp
Image image = new Image { BackgroundColor = Color.FromHex("#D1D1D1") };
image.Source = new FontImageSource
{
    Glyph = "\uf30c",
    FontFamily = Device.RuntimePlatform == Device.iOS ? "Ionicons" : "ionicons.ttf#",
    Size = 44
};
```

As capturas de tela a seguir, do exemplo de [layouts vinculáveis](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablelayouts), mostram vários ícones de fonte sendo exibidos por um layout vinculável:

![Captura de tela dos ícones de fonte que estão sendo exibidos, no iOS e no Android](fonts-images/font-image-source.png "Ícones de fonte sendo exibidos em uma exibição de imagem")

## <a name="related-links"></a>Links relacionados

- [FontsSample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithfonts)
- [Texto (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)
- [Layouts vinculáveis (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablelayouts)
- [Layouts Associáveis](~/xamarin-forms/user-interface/layouts/bindable-layouts.md)
