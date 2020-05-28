---
title: Fontes emXamarin.Forms
description: Este artigo explica como especificar informações sobre fontes em controles que exibem texto em Xamarin.Forms aplicativos.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 3201340c8056fb1a7e36240eb329df14bd960ca3
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84136208"
---
# <a name="fonts-in-xamarinforms"></a>Fontes emXamarin.Forms

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithfonts)

Este artigo descreve como o Xamarin.Forms permite especificar atributos de fonte (incluindo peso e tamanho) em controles que exibem texto. As informações de fonte podem ser [especificadas no código](#Setting_Font_in_Code) ou [especificadas em XAML](#Setting_Font_in_Xaml). Também é possível usar uma [fonte personalizada](#use-a-custom-font)e [exibir ícones de fonte](#display-font-icons).

<a name="Setting_Font_in_Code" />

## <a name="set-the-font-in-code"></a>Definir a fonte no código

Use as três propriedades relacionadas à fonte de todos os controles que exibem texto:

- **FontFamily** &ndash; o `string` nome da fonte.
- **FontSize** &ndash; o tamanho da fonte como um `double` .
- **Fonte de** &ndash; uma cadeia de caracteres que especifica informações de estilo como *itálico* e **negrito** (usando a `FontAttributes` enumeração em C#).

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

O valor de tamanho é medido em unidades independentes de dispositivo. Para obter mais informações, consulte [unidades de medida](~/xamarin-forms/user-interface/controls/common-properties.md#units-of-measurement).

Xamarin.Formstambém define os campos na [`NamedSize`](xref:Xamarin.Forms.NamedSize) enumeração que representam tamanhos de fonte específicos. Para obter mais informações sobre tamanhos de fonte nomeados, consulte [tamanhos de fonte nomeados](#named-font-sizes).

<a name="FontAttributes" />

### <a name="font-attributes"></a>Atributos de fonte

Estilos de fonte, como **negrito** e *itálico* , podem ser definidos na `FontAttributes` propriedade. Atualmente, há suporte para os seguintes valores:

- **Nenhum**
- **Aplique**
- **Colocadas**

A `FontAttribute` enumeração pode ser usada da seguinte maneira (você pode especificar um único atributo ou `OR` eles juntos):

```csharp
label.FontAttributes = FontAttributes.Bold | FontAttributes.Italic;
```

### <a name="set-font-info-per-platform"></a>Definir informações de fonte por plataforma

Como alternativa, a `Device.RuntimePlatform` propriedade pode ser usada para definir nomes de fonte diferentes em cada plataforma, conforme demonstrado neste código:

```csharp
label.FontFamily = Device.RuntimePlatform == Device.iOS ? "MarkerFelt-Thin" :
   Device.RuntimePlatform == Device.Android ? "Lobster-Regular.ttf#Lobster-Regular" : "Assets/Fonts/ArimaMadurai-Black.ttf#Arima Madurai",
label.FontSize = Device.RuntimePlatform == Device.iOS ? 24 :
   Device.RuntimePlatform == Device.Android ? Device.GetNamedSize(NamedSize.Medium, label) : Device.GetNamedSize(NamedSize.Large, label);
```

Uma boa fonte de informações de fonte para iOS é [iosfonts.com](http://iosfonts.com).

<a name="Setting_Font_in_Xaml" />

## <a name="set-the-font-in-xaml"></a>Definir a fonte em XAML

Xamarin.Formscontroles que exibem texto têm uma `FontSize` propriedade que pode ser definida em XAML. A maneira mais simples de definir a fonte em XAML é usar os valores de enumeração de tamanho nomeado, conforme mostrado neste exemplo:

```xaml
<Label Text="Login" FontSize="Large"/>
<Label Text="Instructions" FontSize="Small"/>
```

Há um conversor interno para a `FontSize` propriedade que permite que todas as configurações de fonte sejam expressas como um valor de cadeia de caracteres em XAML. Além disso, a `FontAttributes` propriedade pode ser usada para especificar atributos de fonte:

```xaml
<Label Text="Italics are supported" FontAttributes="Italic" />
<Label Text="Biggest NamedSize" FontSize="Large" />
<Label Text="Use size 72" FontSize="72" />
```

A [`Device.RuntimePlatform`](~/xamarin-forms/platform/device.md#provide-platform-specific-values) propriedade também pode ser usada em XAML para renderizar uma fonte diferente em cada plataforma. O exemplo a seguir usa fontes diferentes em cada plataforma:

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

Xamarin.Formsdefine os campos na [`NamedSize`](xref:Xamarin.Forms.NamedSize) enumeração que representam tamanhos de fonte específicos. A tabela a seguir mostra os `NamedSize` Membros e seus tamanhos padrão no Ios, no Android e no plataforma universal do Windows (UWP):

| Membro | iOS | Android | UWP |
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

Os tamanhos de fonte nomeados podem ser definidos por meio de XAML e código. Além disso, o `Device.GetNamedSize` método pode ser chamado para retornar um `double` que represente o tamanho da fonte nomeada:

```csharp
label.FontSize = Device.GetNamedSize(NamedSize.Small, typeof(Label));
```

> [!NOTE]
> No iOS e no Android, os tamanhos de fonte nomeados serão dimensionamento automático com base nas opções de acessibilidade do sistema operacional. Esse comportamento pode ser desabilitado no iOS com uma plataforma específica. Para obter mais informações, consulte [dimensionamento de acessibilidade para tamanhos de fonte nomeados no Ios](~/xamarin-forms/platform/ios/named-font-size-scaling.md).

## <a name="use-a-custom-font"></a>Usar uma fonte personalizada

Fontes personalizadas podem ser adicionadas ao seu Xamarin.Forms projeto compartilhado e consumidas por projetos de plataforma sem qualquer trabalho adicional. O processo para realizar isso é o seguinte:

1. Adicione a fonte ao seu Xamarin.Forms projeto compartilhado como um recurso inserido (**ação de compilação: EmbeddedResource**).
1. Registre o arquivo de fonte com o assembly, em um arquivo como **AssemblyInfo.cs**, usando o `ExportFont` atributo. Um alias opcional também pode ser especificado.

> [!IMPORTANT]
> Fontes inseridas exigem o uso de Xamarin.Forms 4.5.0.530 ou superior.

O exemplo a seguir mostra a fonte Lobster-regular que está sendo registrada com o assembly, juntamente com um alias:

```csharp
using Xamarin.Forms;

[assembly: ExportFont("Lobster-Regular.ttf", Alias = "Lobster")]
```

> [!NOTE]
> A fonte pode residir em qualquer pasta no projeto compartilhado, sem precisar especificar o nome da pasta ao registrar a fonte com o assembly.

A fonte pode ser consumida em cada plataforma referenciando seu nome, sem a extensão de arquivo:

```xaml
<!-- Use font name -->
<Label Text="Hello Xamarin.Forms"
       FontFamily="Lobster-Regular" />
```

Como alternativa, ele pode ser consumido em cada plataforma referenciando seu alias:

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
> No Windows, o nome do arquivo de fonte e o nome da fonte podem ser diferentes. Para descobrir o nome da fonte no Windows, clique com o botão direito do mouse no arquivo. ttf e selecione **Visualizar**. O nome da fonte pode ser determinado na janela de visualização.

## <a name="display-font-icons"></a>Exibir ícones de fonte

Os ícones de fonte podem ser exibidos por Xamarin.Forms aplicativos especificando os dados de ícone de fonte em um `FontImageSource` objeto. Essa classe, que deriva da [`ImageSource`](xref:Xamarin.Forms.ImageSource) classe, tem as seguintes propriedades:

- `Glyph`– o valor do caractere Unicode do ícone de fonte, especificado como um `string` .
- `Size`– um `double` valor que indica o tamanho, em unidades independentes de dispositivo, do ícone de fonte renderizado. O valor padrão é 30. Além disso, essa propriedade pode ser definida como um tamanho de fonte nomeado.
- `FontFamily`– a `string` que representa a família de fontes à qual o ícone de fonte pertence.
- `Color`– um [`Color`](xref:Xamarin.Forms.Color) valor opcional a ser usado ao exibir o ícone de fonte.

Esses dados são usados para criar um PNG, que pode ser exibido por qualquer exibição que possa exibir um `ImageSource` . Essa abordagem permite que ícones de fonte, como emojis, sejam exibidos por várias exibições, em vez de limitar a exibição do ícone de fonte a uma exibição de apresentação de texto único, como um [`Label`](xref:Xamarin.Forms.Label) .

> [!IMPORTANT]
> Os ícones de fonte só podem ser especificados por sua representação de caractere Unicode.

O exemplo XAML a seguir tem um único ícone de fonte sendo exibido por uma [`Image`](xref:Xamarin.Forms.Image) exibição:

```xaml
<Image BackgroundColor="#D1D1D1">
    <Image.Source>
        <FontImageSource Glyph="&#xf30c;"
                         FontFamily="{OnPlatform iOS=Ionicons, Android=ionicons.ttf#}"
                         Size="44" />
    </Image.Source>
</Image>
```

Esse código exibe um ícone do XBox, da família de fontes Ionicons, em uma [`Image`](xref:Xamarin.Forms.Image) exibição. Observe que, embora o caractere Unicode para esse ícone seja `\uf30c` , ele deve ser ignorado em XAML e, portanto, se torna `&#xf30c;` . Este é o código C# equivalente:

```csharp
Image image = new Image { BackgroundColor = Color.FromHex("#D1D1D1") };
image.Source = new FontImageSource
{
    Glyph = "\uf30c",
    FontFamily = Device.RuntimePlatform == Device.iOS ? "Ionicons" : "ionicons.ttf#",
    Size = 44
};
```

As capturas de tela a seguir, do exemplo de [layouts vinculáveis](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablelayouts) , mostram vários ícones de fonte sendo exibidos por um layout vinculável:

![Captura de tela dos ícones de fonte que estão sendo exibidos, no iOS e no Android](fonts-images/font-image-source.png "Ícones de fonte sendo exibidos em uma exibição de imagem")

## <a name="related-links"></a>Links relacionados

- [FontsSample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithfonts)
- [Texto (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)
- [Layouts vinculáveis (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablelayouts)
- [Layouts vinculáveis](~/xamarin-forms/user-interface/layouts/bindable-layouts.md)
