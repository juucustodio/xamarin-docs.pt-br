---
title: Fontes no xamarin. Forms
description: Este artigo explica como especificar informações de fonte em controles que exibem texto em aplicativos xamarin. Forms.
ms.prod: xamarin
ms.assetid: 49DD2249-C575-41AE-AE06-08F890FD6031
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/22/2017
ms.openlocfilehash: e6635bc13214a5a4e728fa3e71db86a8ea1c39d6
ms.sourcegitcommit: 4c0093ee5d4aeb16c0e6f0c740c4796736971651
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2018
ms.locfileid: "39202949"
---
# <a name="fonts-in-xamarinforms"></a>Fontes no xamarin. Forms

Este artigo descreve como o xamarin. Forms permite especificar atributos de fonte (incluindo o tamanho e peso) em controles que exibem texto. Informações de fonte podem ser [especificado no código](#Setting_Font_in_Code) ou [especificado no XAML](#Setting_Font_in_Xaml).
Também é possível usar um [fonte personalizada](#Using_a_Custom_Font).

<a name="Setting_Font_in_Code" />

## <a name="setting-font-in-code"></a>Configurando a fonte no código

Use as três propriedades de fonte de todos os controles que exibem texto:

- **FontFamily** &ndash; o `string` nome da fonte.
- **FontSize** &ndash; o tamanho da fonte como um `double`.
- **FontAttributes** &ndash; especificando informações de estilo, como uma cadeia de caracteres *itálico* e **negrito** (usando o `FontAttributes` enumeração em c#).

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

Você também pode usar o `NamedSize` enumeração que tem quatro opções internas; Xamarin. Forms escolhe o melhor tamanho de cada plataforma.

-  **Micro**
-  **Pequeno**
-  **Médio**
-  **Grande**

O `NamedSize` enumeração pode ser usado onde quer que um `FontSize` pode ser especificado usando o `Device.GetNamedSize` método para converter o valor de um `double`:

```csharp
label.FontSize = Device.GetNamedSize(NamedSize.Small, typeof(Label));
```

<a name="FontAttributes" />

### <a name="font-attributes"></a>Atributos de fonte

Estilos de fonte, como **negrito** e *itálico* podem ser definidos na `FontAttributes` propriedade. Atualmente, há suporte para os seguintes valores:

-  **Nenhum**
-  **Negrito**
-  **Itálico**

O `FontAttribute` enumeração pode ser usada da seguinte maneira (você pode especificar um único atributo ou `OR` -los juntos):

```csharp
label.FontAttributes = FontAttributes.Bold | FontAttributes.Italic;
```

### <a name="setting-font-info-per-platform"></a>Informações de fonte de configuração por plataforma

Como alternativa, o `Device.RuntimePlatform` propriedade pode ser usada para definir os nomes de fontes diferentes em cada plataforma, conforme demonstrado neste código:

```csharp
label.FontFamily = Device.RuntimePlatform == Device.iOS ? "Lobster-Regular" :
   Device.RuntimePlatform == Device.Android ? "Lobster-Regular.ttf#Lobster-Regular" : "Assets/Fonts/Lobster-Regular.ttf#Lobster",
label.FontSize = Device.RuntimePlatform == Device.iOS ? 24 :
   Device.RuntimePlatform == Device.Android ? Device.GetNamedSize(NamedSize.Medium, label) : Device.GetNamedSize(NamedSize.Large, label);
```

É uma boa fonte de informações de fonte para iOS [iosfonts.com](http://iosfonts.com).

<a name="Setting_Font_in_Xaml" />

## <a name="setting-the-font-in-xaml"></a>Definição da fonte em XAML

Xamarin. Forms controla esse texto de exibição todas têm um `Font` propriedade que pode ser definida em XAML. A maneira mais simples para definir a fonte em XAML é usar os valores de enumeração de tamanho, conforme mostrado neste exemplo:

```xaml
<Label Text="Login" FontSize="Large"/>
<Label Text="Instructions" FontSize="Small"/>
```

Não há um conversor interno para o `Font` propriedade que permite que todas as configurações de fonte a ser expressa como um valor de cadeia de caracteres em XAML. Os exemplos a seguir mostram como você pode especificar atributos de fonte e tamanhos no XAML:

```xaml
<Label Text="Italics are supported" FontAttributes="Italic" />
<Label Text="Biggest NamedSize" FontSize="Large" />
<Label Text="Use size 72" FontSize="72" />
```

Para especificar várias `Font` configurações, combinar as configurações necessárias em um único `Font` cadeia de caracteres de atributo. A cadeia de caracteres de atributo de fonte deve ser formatada como `"[font-face],[attributes],[size]"`. A ordem dos parâmetros é importante, todos os parâmetros são opcionais e várias `attributes` podem ser especificados, por exemplo:

```xaml
<Label Text="Small bold text" Font="Bold, Micro" />
<Label Text="Medium custom font" Font="MarkerFelt-Thin, 42" />
<Label Text="Really big bold and italic text" Font="Bold, Italic, 72"  />
```

[`Device.RuntimePlatform`](~/xamarin-forms/platform/device.md#providing-platform-values) também pode ser usado em XAML para renderizar uma fonte diferente em cada plataforma. O exemplo a seguir usa uma face de fonte personalizada no iOS (<span style="font-family:MarkerFelt-Thin">MarkerFelt-finos</span>) e especifica o tamanho/atributos somente em outras plataformas:

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

<a name="Using_a_Custom_Font" />

## <a name="using-a-custom-font"></a>Usando uma fonte personalizada

Usando uma fonte que não sejam de tipos internos requer alguma codificação específicos da plataforma. Esta captura de tela mostra a fonte personalizada **Lobster** partir [fontes de código-fonte aberto do Google](https://www.google.com/fonts) renderizado usando o xamarin. Forms.

 [![A fonte personalizada no iOS e Android](fonts-images/custom-sml.png "Custom fontes Example")](fonts-images/custom.png#lightbox "exemplo de fontes personalizadas")

As etapas necessárias para cada plataforma são descritas abaixo. Ao incluir arquivos de fonte personalizado com um aplicativo, certifique-se de verificar que permite a licença da fonte para distribuição.

### <a name="ios"></a>iOS

É possível exibir uma fonte personalizada primeiro garantir que ele é carregado e, em seguida, fazendo referência a ele por nome usando o xamarin. Forms `Font` métodos.
Siga as instruções em [esta postagem de blog](http://blog.xamarin.com/custom-fonts-in-ios/):

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

### <a name="windows"></a>Windows

Xamarin. Forms para plataformas do Windows pode fazer referência a uma fonte personalizada que foi adicionada ao projeto seguindo um padrão de nomenclatura específico. Primeiro, adicione o arquivo de fonte para o **/Assets/fontes/** pasta no projeto de aplicativo e defina as <span class="UIItem">compilar: conteúdo da ação</span>. Em seguida, use a fonte e o caminho do nome de arquivo completo, seguido por um hash (#) e o <span class="UIItem">nome da fonte</span>, como o trecho de código a seguir demonstra:

```csharp
new Label
{
    Text = "Hello, Forms!",
    FontFamily = Device.RuntimePlatform == Device.UWP ? "Assets/Fonts/Lobster-Regular.ttf#Lobster" : null // set only for UWP apps
}
```

> [!NOTE]
> Observe que o nome do arquivo de fonte e o nome da fonte podem ser diferentes. Para descobrir o nome da fonte no Windows, clique no arquivo. ttf e selecione **visualização**. O nome da fonte pode ser determinado, em seguida, na janela de visualização.

Agora o código comum para o aplicativo está concluído. O código de discagem do telefone específico da plataforma agora será implementado como um [DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md).

### <a name="xaml"></a>XAML

Você também pode usar [ `Device.RuntimePlatform` ](~/xamarin-forms/platform/device.md#providing-platform-values) no XAML para renderizar uma fonte personalizada:

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

<a name="Summary" />

## <a name="summary"></a>Resumo

Xamarin. Forms fornece configurações padrão simples para que você possa dimensionar texto facilmente para todas as plataformas com suporte. Ele também permite que você especifique a face de fonte e tamanho &ndash; até mesmo diferentes para cada plataforma &ndash; quando um controle mais refinado é necessário.

Informações de fonte também podem ser especificadas em XAML usando atributos de fonte formatado corretamente.

## <a name="related-links"></a>Links relacionados

- [FontsSample](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithFonts/)
- [Texto (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text/)
