---
title: Fontes em Xamarin.Forms
description: Este artigo explica como especificar informações de fonte sobre controles que exibem texto em aplicativos Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 49DD2249-C575-41AE-AE06-08F890FD6031
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/01/2020
ms.openlocfilehash: ca4d3b242fcc73bb73e8d6ab1f817eefcc2ade4d
ms.sourcegitcommit: 89b3e383a37db5b940f0c63bbfe9cb806dc7d5d1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81388811"
---
# <a name="fonts-in-xamarinforms"></a>Fontes em Xamarin.Forms

[![Baixar](~/media/shared/download.png) amostra Baixar a amostra](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithfonts)

Este artigo descreve como o Xamarin.Forms permite especificar atributos de fonte (incluindo peso e tamanho) em controles que exibem texto. As informações da fonte podem ser [especificadas em código](#Setting_Font_in_Code) ou [especificadas em XAML](#Setting_Font_in_Xaml). Também é possível usar uma [fonte personalizada](#use-a-custom-font)e exibir [ícones de fonte.](#display-font-icons)

<a name="Setting_Font_in_Code" />

## <a name="set-the-font-in-code"></a>Defina a fonte em código

Use as três propriedades relacionadas à fonte de quaisquer controles que exibam texto:

- **FontFamily** &ndash; `string` o nome da fonte.
- **FontDimensione** &ndash; o tamanho `double`da fonte como um .
- **FontAtribui** &ndash; uma seqüência especificando informações de estilo `FontAttributes` como *Itálico* e **Negrito** (usando a enumeração em C#).

Este código mostra como criar um rótulo e especificar o tamanho e o peso da fonte a serem exibidos:

```csharp
var about = new Label
{
    FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)),
    FontAttributes = FontAttributes.Bold,
    Text = "Medium Bold Font"
};
```

<a name="FontSize" />

### <a name="font-size"></a>Tamanho da fonte

A `FontSize` propriedade pode ser definida como um valor duplo, por exemplo:

```csharp
label.FontSize = 24;
```

O valor do tamanho é medido em unidades independentes de dispositivos. Para obter mais informações, consulte [Unidades de Medida](~/xamarin-forms/user-interface/controls/common-properties.md#units-of-measurement).

Xamarin.Forms também define campos [`NamedSize`](xref:Xamarin.Forms.NamedSize) na enumeração que representam tamanhos de fonte específicos. Para obter mais informações sobre tamanhos de fonte nomeados, consulte [Tamanhos de fonte nomeados](#named-font-sizes).

<a name="FontAttributes" />

### <a name="font-attributes"></a>Atributos de fonte

Estilos de fonte como **negrito** e *itálico* podem ser definidos na `FontAttributes` propriedade. Os seguintes valores são suportados atualmente:

- **Nenhum**
- **Negrito**
- **Itálico**

A `FontAttribute` enumeração pode ser usada da seguinte forma `OR` (você pode especificar um único atributo ou eles juntos):

```csharp
label.FontAttributes = FontAttributes.Bold | FontAttributes.Italic;
```

### <a name="set-font-info-per-platform"></a>Definir informações de fonte por plataforma

Alternativamente, `Device.RuntimePlatform` a propriedade pode ser usada para definir diferentes nomes de fonte em cada plataforma, conforme demonstrado neste código:

```csharp
label.FontFamily = Device.RuntimePlatform == Device.iOS ? "MarkerFelt-Thin" :
   Device.RuntimePlatform == Device.Android ? "Lobster-Regular.ttf#Lobster-Regular" : "Assets/Fonts/ArimaMadurai-Black.ttf#Arima Madurai",
label.FontSize = Device.RuntimePlatform == Device.iOS ? 24 :
   Device.RuntimePlatform == Device.Android ? Device.GetNamedSize(NamedSize.Medium, label) : Device.GetNamedSize(NamedSize.Large, label);
```

Uma boa fonte de informações de fonte para iOS é [iosfonts.com](http://iosfonts.com).

<a name="Setting_Font_in_Xaml" />

## <a name="set-the-font-in-xaml"></a>Defina a fonte em XAML

Os controles Xamarin.Forms que exibem texto têm uma `FontSize` propriedade que pode ser definida em XAML. A maneira mais simples de definir a fonte em XAML é usar os valores de enumeração de tamanho nomeados, como mostrado neste exemplo:

```xaml
<Label Text="Login" FontSize="Large"/>
<Label Text="Instructions" FontSize="Small"/>
```

Há um conversor embutido `FontSize` para a propriedade que permite que todas as configurações da fonte sejam expressas como um valor de string em XAML. Além disso, `FontAttributes` a propriedade pode ser usada para especificar atributos de fonte:

```xaml
<Label Text="Italics are supported" FontAttributes="Italic" />
<Label Text="Biggest NamedSize" FontSize="Large" />
<Label Text="Use size 72" FontSize="72" />
```

A [`Device.RuntimePlatform`](~/xamarin-forms/platform/device.md#providing-platform-specific-values) propriedade também pode ser usada em XAML para renderizar uma fonte diferente em cada plataforma. O exemplo abaixo usa fontes diferentes em cada plataforma:

```xaml
<Label Text="Hello Forms with XAML">
    <Label.FontFamily>
        <OnPlatform x:TypeArguments="x:String">
                <On Platform="iOS" Value="MarkerFelt-Thin" />
                <On Platform="Android" Value="Lobster-Regular.ttf#Lobster-Regular" />
                <On Platform="UWP" Value="Assets/Fonts/ArimaMadurai-Black.ttf#Arima Madurai" />
        </OnPlatform>
    </Label.FontFamily>
</Label>
```

## <a name="named-font-sizes"></a>Tamanhos de fonte nomeados

Xamarin.Forms define campos [`NamedSize`](xref:Xamarin.Forms.NamedSize) na enumeração que representam tamanhos de fonte específicos. A tabela a `NamedSize` seguir mostra os membros e seus tamanhos padrão no iOS, Android e na Universal Windows Platform (UWP):

| Membro | iOS | Android | UWP |
| --- | --- | --- | --- |
| `Default` | 16 | 14 | 14 |
| `Micro` | 11 | 10 | 15.667 |
| `Small` | 13 | 14 | 18.667 |
| `Medium` | 16 | 17 | 22.667 |
| `Large` | 20 | 22 | 32 |
| `Body` | 17 | 16 | 14 |
| `Header` | 17 | 96 | 46 |
| `Title` | 28 | 24 | 24 |
| `Subtitle` | 22 | 16 | 20 |
| `Caption` | 12 | 12 | 12 |

Os valores de tamanho são medidos em unidades independentes de dispositivos. Para obter mais informações, consulte [Unidades de Medida](~/xamarin-forms/user-interface/controls/common-properties.md#units-of-measurement).

Os tamanhos de fonte nomeados podem ser definidos através de XAML e código. Além disso, `Device.GetNamedSize` o método pode `double` ser chamado para retornar um que represente o tamanho da fonte nomeado:

```csharp
label.FontSize = Device.GetNamedSize(NamedSize.Small, typeof(Label));
```

> [!NOTE]
> No iOS e Android, tamanhos de fonte nomeados serão dimensionados automaticamente com base nas opções de acessibilidade do sistema operacional. Esse comportamento pode ser desativado no iOS com uma plataforma específica. Para obter mais informações, consulte [Dimensionamento de acessibilidade para tamanhos de fonte nomeados no iOS](~/xamarin-forms/platform/ios/named-font-size-scaling.md).

## <a name="use-a-custom-font"></a>Use uma fonte personalizada

Fontes personalizadas podem ser adicionadas ao seu projeto compartilhado Xamarin.Forms e consumidas por projetos de plataforma sem qualquer trabalho adicional. O processo para realizar isso é o seguinte:

1. Adicione a fonte ao seu projeto compartilhado Xamarin.Forms como um recurso incorporado **(Build Action: EmbeddedResource**).
1. Registre o arquivo de fonte com o **AssemblyInfo.cs**conjunto, `ExportFont` em um arquivo como AssemblyInfo.cs , usando o atributo. Um alias opcional também pode ser especificado.

> [!IMPORTANT]
> Fontes incorporadas exigem o uso de Xamarin.Forms 4.5.0.530 ou superior.

O exemplo a seguir mostra a fonte Lagosta-Regular sendo registrada no conjunto, juntamente com um alias:

```csharp
using Xamarin.Forms;

[assembly: ExportFont("Lobster-Regular.ttf", Alias = "Lobster")]
```

> [!NOTE]
> A fonte pode residir em qualquer pasta do projeto compartilhado, sem ter que especificar o nome da pasta ao registrar a fonte com o conjunto.

A fonte pode então ser consumida em cada plataforma fazendo referência ao seu nome, sem a extensão do arquivo:

```xaml
<!-- Use font name -->
<Label Text="Hello Xamarin.Forms"
       FontFamily="Lobster-Regular" />
```

Alternativamente, ele pode ser consumido em cada plataforma fazendo referência ao seu alias:

```xaml
<!-- Use font alias -->
<Label Text="Hello Xamarin.Forms"
       FontFamily="Lobster" />
```

Este é o código C# equivalente:

```csharp
// Use font name
Label label1 = new Label
{
    Text = "Hello Xamarin.Forms!",
    FontFamily = "Lobster-Regular"
};

// Use font alias
Label label2 = new Label
{
    Text = "Hello Xamarin.Forms!",
    FontFamily = "Lobster"
};
```

As capturas de tela a seguir mostram a fonte personalizada:

[![Fonte personalizada no iOS e Android](fonts-images/custom-sml.png "Exemplo de fontes personalizadas")](fonts-images/custom.png#lightbox "Exemplo de fontes personalizadas")

> [!IMPORTANT]
> No Windows, o nome do arquivo de fonte e o nome da fonte podem ser diferentes. Para descobrir o nome da fonte no Windows, clique com o botão direito do mouse no arquivo .ttf e selecione **'Visualizar 'Visualização**'. O nome da fonte pode então ser determinado a partir da janela de visualização.

## <a name="display-font-icons"></a>Exibir ícones de fonte

Os ícones de fonte podem ser exibidos por aplicativos Xamarin.Forms especificando os dados do ícone da fonte em um `FontImageSource` objeto. Esta classe, que deriva [`ImageSource`](xref:Xamarin.Forms.ImageSource) da classe, tem as seguintes propriedades:

- `Glyph`– o valor de caractere unicode do `string`ícone de fonte, especificado como a .
- `Size`– `double` um valor que indica o tamanho, em unidades independentes do dispositivo, do ícone de fonte renderizada. O valor padrão é 30. Além disso, esta propriedade pode ser definida como um tamanho de fonte nomeado.
- `FontFamily`– `string` uma representação da família de fontes à qual o ícone de fonte pertence.
- `Color`– um [`Color`](xref:Xamarin.Forms.Color) valor opcional a ser usado ao exibir o ícone da fonte.

Esses dados são usados para criar um PNG, que pode `ImageSource`ser exibido por qualquer exibição que possa exibir um . Essa abordagem permite que ícones de fonte, como emojis, sejam exibidos por vários pontos de vista, [`Label`](xref:Xamarin.Forms.Label)em vez de limitar a exibição do ícone de fonte a um único texto que apresente exibição, como um .

> [!IMPORTANT]
> Os ícones de fonte só podem ser especificados atualmente pela representação de caracteres unicode.

O exemplo XAML a seguir tem um [`Image`](xref:Xamarin.Forms.Image) único ícone de fonte sendo exibido por uma exibição:

```xaml
<Image BackgroundColor="#D1D1D1">
    <Image.Source>
        <FontImageSource Glyph="&#xf30c;"
                         FontFamily="{OnPlatform iOS=Ionicons, Android=ionicons.ttf#}"
                         Size="44" />
    </Image.Source>
</Image>
```

Este código exibe um ícone XBox, da família de [`Image`](xref:Xamarin.Forms.Image) fontes Ionicons, em uma exibição. Observe que, enquanto o caractere `\uf30c`unicode para este ícone é, ele `&#xf30c;`tem que ser escapado em XAML e assim se torna . Este é o código C# equivalente:

```csharp
Image image = new Image { BackgroundColor = Color.FromHex("#D1D1D1") };
image.Source = new FontImageSource
{
    Glyph = "\uf30c",
    FontFamily = Device.RuntimePlatform == Device.iOS ? "Ionicons" : "ionicons.ttf#",
    Size = 44
};
```

As capturas de tela a seguir, da amostra ['Layouts' bindable,](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablelayouts) mostram vários ícones de fonte sendo exibidos por um layout vinculável:

![Captura de tela dos ícones de fonte que estão sendo exibidos, no iOS e Android](fonts-images/font-image-source.png "Ícones de fonte sendo exibidos em uma exibição de imagem")

## <a name="related-links"></a>Links relacionados

- [FontsSample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithfonts)
- [Texto (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)
- [Layouts vinculáveis (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablelayouts)
- [Layouts Associáveis](~/xamarin-forms/user-interface/layouts/bindable-layouts.md)
