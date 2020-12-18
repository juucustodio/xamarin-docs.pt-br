---
title: Fontes em Xamarin.Forms
description: Este artigo explica como especificar informações sobre fontes em controles que exibem texto em Xamarin.Forms aplicativos.
ms.prod: xamarin
ms.assetid: 49DD2249-C575-41AE-AE06-08F890FD6031
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/04/2020
ms.custom: contperf-fy21q2
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 146c67655c420611b2a0901efa6cc22ef780713e
ms.sourcegitcommit: c5e72d2ca4152b62ab6583f0dbe84b3ba29d8283
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/18/2020
ms.locfileid: "97677515"
---
# <a name="fonts-in-no-locxamarinforms"></a>Fontes em Xamarin.Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/workingwithfonts)

Por padrão, Xamarin.Forms o usa uma fonte de sistema definida por cada plataforma. No entanto, os controles que exibem texto definem as propriedades que você pode usar para alterar essa fonte:

- `FontAttributes`, do tipo `FontAttributes` , que é uma enumeração com três membros: `None` , `Bold` e `Italic` . O valor padrão dessa propriedade é `None`.
- `FontSize`, de tipo `double`.
- `FontFamily`, de tipo `string`.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que eles podem ser destinos de associações de dados e com estilo.

## <a name="set-font-attributes"></a>Definir atributos de fonte

Controles que exibem texto podem definir a `FontAttributes` propriedade para especificar atributos de fonte:

```xaml
<Label Text="Italics"
       FontAttributes="Italic" />
<Label Text="Bold and italics"
       FontAttributes="Bold, Italic" />
```

Este é o código C# equivalente:

```csharp
Label label1 = new Label
{
    Text = "Italics",
    FontAttributes = FontAttributes.Italic
};

Label label2 = new Label
{
    Text = "Bold and italics",
    FontAttributes = FontAttributes.Bold | FontAttributes.Italic
};    
```

## <a name="set-the-font-size"></a>Definir o tamanho da fonte

Controles que exibem texto podem definir a `FontSize` propriedade para especificar o tamanho da fonte. A `FontSize` propriedade pode ser definida com um `double` valor diretamente ou por um [`NamedSize`](xref:Xamarin.Forms.NamedSize) valor de enumeração:

```xaml
<Label Text="Font size 24"
       FontSize="24" />
<Label Text="Large font size"
       FontSize="Large" />
```

Este é o código C# equivalente:

```csharp
Label label1 = new Label
{
    Text = "Font size 24",
    FontSize = 24
};

Label label2 = new Label
{
    Text = "Large font size",
    FontSize = Device.GetNamedSize(NamedSize.Large, typeof(Label))
};
```

Como alternativa, o `Device.GetNamedSize` método tem uma substituição que especifica o segundo argumento como um [`Element`](xref:Xamarin.Forms.Element) :

```csharp
Label myLabel = new Label
{
    Text = "Large font size",
};
myLabel.FontSize = Device.GetNamedSize(NamedSize.Large, myLabel);
```

> [!NOTE]
> O `FontSize` valor, quando especificado como um `double` , é medido em unidades independentes de dispositivo. Para obter mais informações, consulte [unidades de medida](~/xamarin-forms/user-interface/controls/common-properties.md#units-of-measurement).

Para obter mais informações sobre tamanhos de fonte nomeados, consulte [entender tamanhos de fonte nomeados](#understand-named-font-sizes).

## <a name="set-the-font-family"></a>Definir a família de fontes

Controles que exibem texto podem definir a `FontFamily` propriedade como um nome de família de fontes, como "Times Roman". No entanto, isso só funcionará se essa família de fontes tiver suporte na plataforma específica.

Há várias técnicas que podem ser usadas para tentar derivar as fontes que estão disponíveis em uma plataforma. No entanto, a presença de um arquivo de fonte TTF (true type Format) não implica necessariamente em uma família de fontes, e os TTFs geralmente são incluídos e não se destinam ao uso em aplicativos. Além disso, as fontes instaladas em uma plataforma podem ser alteradas com a versão da plataforma. Portanto, a abordagem mais confiável para especificar uma família de fontes é usar uma fonte personalizada.

Fontes personalizadas podem ser adicionadas ao seu Xamarin.Forms projeto compartilhado e consumidas por projetos de plataforma sem qualquer trabalho adicional. O processo para realizar isso é o seguinte:

1. Adicione a fonte ao seu Xamarin.Forms projeto compartilhado como um recurso inserido (**ação de compilação: EmbeddedResource**).
1. Registre o arquivo de fonte com o assembly, em um arquivo como **AssemblyInfo.cs**, usando o `ExportFont` atributo. Um alias opcional também pode ser especificado.

O exemplo a seguir mostra a fonte de Lobster-Regular que está sendo registrada com o assembly, juntamente com um alias:

```csharp
using Xamarin.Forms;

[assembly: ExportFont("Lobster-Regular.ttf", Alias = "Lobster")]
```

> [!NOTE]
> A fonte pode residir em qualquer pasta no projeto compartilhado, sem precisar especificar o nome da pasta ao registrar a fonte com o assembly.
>
> No Windows, o nome do arquivo de fonte e o nome da fonte podem ser diferentes. Para descobrir o nome da fonte no Windows, clique com o botão direito do mouse no arquivo. ttf e selecione **Visualizar**. O nome da fonte pode ser determinado na janela de visualização.

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
> Para Builds de versão no Windows, verifique se o assembly que contém a fonte personalizada é passado como um argumento na `Forms.Init` chamada de método. Para saber mais, consulte a [Solução de problemas](~/xamarin-forms/platform/windows/installation/index.md#troubleshooting).

## <a name="set-font-properties-per-platform"></a>Definir propriedades de fonte por plataforma

As [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) [`On`](xref:Xamarin.Forms.On) classes e podem ser usadas em XAML para definir propriedades de fonte por plataforma. O exemplo a seguir define diferentes famílias de fontes e tamanhos em cada plataforma:

```xaml
<Label Text="Different font properties on different platforms"
       FontSize="{OnPlatform iOS=20, Android=Medium, UWP=24}">
    <Label.FontFamily>
        <OnPlatform x:TypeArguments="x:String">
            <On Platform="iOS" Value="MarkerFelt-Thin" />
            <On Platform="Android" Value="Lobster-Regular" />
            <On Platform="UWP" Value="ArimaMadurai-Black" />
        </OnPlatform>
    </Label.FontFamily>
</Label>
```

A [`Device.RuntimePlatform`](~/xamarin-forms/platform/device.md#provide-platform-specific-values) propriedade pode ser usada no código para definir as propriedades da fonte por plataforma

```csharp
Label label = new Label
{
    Text = "Different font properties on different platforms"
};

label.FontSize = Device.RuntimePlatform == Device.iOS ? 20 :
    Device.RuntimePlatform == Device.Android ? Device.GetNamedSize(NamedSize.Medium, label) : 24;
label.FontFamily = Device.RuntimePlatform == Device.iOS ? "MarkerFelt-Thin" :
   Device.RuntimePlatform == Device.Android ? "Lobster-Regular" : "ArimaMadurai-Black";
```

Para obter mais informações sobre como fornecer valores específicos da plataforma, consulte [fornecer valores específicos da plataforma](~/xamarin-forms/platform/device.md#provide-platform-specific-values). Para obter informações sobre a `OnPlatform` extensão de marcação, consulte [extensão de marcação da plataforma](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform-markup-extension).

## <a name="understand-named-font-sizes"></a>Entender os tamanhos de fonte nomeados

Xamarin.Forms define os campos na [`NamedSize`](xref:Xamarin.Forms.NamedSize) enumeração que representam tamanhos de fonte específicos. A tabela a seguir mostra os `NamedSize` Membros e seus tamanhos padrão no Ios, no Android e no plataforma universal do Windows (UWP):

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

> [!NOTE]
> No iOS e no Android, os tamanhos de fonte nomeados serão dimensionamento automático com base nas opções de acessibilidade do sistema operacional. Esse comportamento pode ser desabilitado no iOS com uma plataforma específica. Para obter mais informações, consulte [dimensionamento de acessibilidade para tamanhos de fonte nomeados no Ios](~/xamarin-forms/platform/ios/named-font-size-scaling.md).

## <a name="display-font-icons"></a>Exibir ícones de fonte

Os ícones de fonte podem ser exibidos por Xamarin.Forms aplicativos especificando os dados de ícone de fonte em um `FontImageSource` objeto. Essa classe, que deriva da [`ImageSource`](xref:Xamarin.Forms.ImageSource) classe, tem as seguintes propriedades:

- `Glyph` – o valor do caractere Unicode do ícone de fonte, especificado como um `string` .
- `Size` – um `double` valor que indica o tamanho, em unidades independentes de dispositivo, do ícone de fonte renderizado. O valor padrão é 30. Além disso, essa propriedade pode ser definida como um tamanho de fonte nomeado.
- `FontFamily` – a `string` que representa a família de fontes à qual o ícone de fonte pertence.
- `Color` – um [`Color`](xref:Xamarin.Forms.Color) valor opcional a ser usado ao exibir o ícone de fonte.

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

As capturas de tela a seguir, do exemplo de [layouts vinculáveis](/samples/xamarin/xamarin-forms-samples/userinterface-bindablelayouts) , mostram vários ícones de fonte sendo exibidos por um layout vinculável:

![Captura de tela dos ícones de fonte que estão sendo exibidos, no iOS e no Android](fonts-images/font-image-source.png "Ícones de fonte sendo exibidos em uma exibição de imagem")

## <a name="related-links"></a>Links relacionados

- [FontsSample](/samples/xamarin/xamarin-forms-samples/workingwithfonts)
- [Texto (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-text)
- [Layouts vinculáveis (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-bindablelayouts)
- [Fornecer valores específicos da plataforma](~/xamarin-forms/platform/device.md#provide-platform-specific-values)
- [Extensão de marcação onplatform](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform-markup-extension)
- [Layouts associáveis](~/xamarin-forms/user-interface/layouts/bindable-layouts.md)
