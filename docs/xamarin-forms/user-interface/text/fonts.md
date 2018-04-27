---
title: Fontes
description: Definindo fontes no xamarin. Forms
ms.prod: xamarin
ms.assetid: 49DD2249-C575-41AE-AE06-08F890FD6031
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/22/2017
ms.openlocfilehash: 52c86c63c328729211c4fbd22bd10b5eb1e56615
ms.sourcegitcommit: 1561c8022c3585655229a869d9ef3510bf83f00a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2018
---
# <a name="fonts"></a>Fontes

Este artigo descreve como xamarin. Forms permite que você especifique os atributos de fonte (incluindo o tamanho e o peso) que exibem texto. Informações sobre a fonte pode ser [especificado no código](#Setting_Font_in_Code) ou [especificado em Xaml](#Setting_Font_in_Xaml).
Também é possível usar um [fonte personalizada](#Using_a_Custom_Font).

<a name="Setting_Font_in_Code" />

## <a name="setting-font-in-code"></a>Fonte de configuração no código

Use as três propriedades de fonte de qualquer controle que exibe o texto:

- **FontFamily** &ndash; o `string` nome da fonte.
- **FontSize** &ndash; o tamanho da fonte como um `double`.
- **FontAttributes** &ndash; uma cadeia de caracteres especificando informações de estilo, como *itálico* e **negrito** (usando o `FontAttributes` enumeração em c#).

Este código mostra como criar um rótulo e especifique o tamanho da fonte e o peso para exibir:

```csharp
var about = new Label {
    FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)),
    FontAttributes = FontAttributes.Bold,
    Text = "Medium Bold Font"
};
```

<a name="FontSize" />

### <a name="font-size"></a>Tamanho da fonte

O `FontSize` propriedade pode ser definida para um valor duplo, por exemplo:

```csharp
label.FontSize = 24;
```

Você também pode usar o `NamedSize` enumeração que tem quatro opções internas; Xamarin. Forms escolhe o melhor tamanho de cada plataforma.

-  **Micro**
-  **Pequeno**
-  **Médio**
-  **Grande**


O `NamedSize` enumeração pode ser usado sempre que um `FontSize` pode ser especificado usando o `Device.GetNamedSize` método para converter o valor de uma `double`:

```csharp
label.FontSize = Device.GetNamedSize(NamedSize.Small, typeof(Label));
```

<a name="FontAttributes" />

### <a name="font-attributes"></a>Atributos de fonte

Estilos de fonte, como **negrito** e *itálico* podem ser definidas no `FontAttributes` propriedade. Atualmente há suporte para os seguintes valores:

-  **Nenhum**
-  **Negrito**
-  **Itálico**

O `FontAttribute` enumeração pode ser usada da seguinte maneira (você pode especificar um único atributo ou `OR` -los juntos):

```csharp
label.FontAttributes = FontAttributes.Bold | FontAttributes.Italic;
```

### <a name="formattedstring"></a>FormattedString

Alguns controles xamarin. Forms (como `Label`) também dar suporte a atributos de fonte diferente dentro de uma cadeia de caracteres usando o `FormattedString` classe. Um `FormattedString` consiste em um ou mais `Span`s, cada um deles pode ter suas própria formatação atributos.

O `Span` classe possui os seguintes atributos:

* **Texto** &ndash; o valor a ser exibido
* **FontFamily** &ndash; o nome da fonte
* **FontSize** &ndash; o tamanho da fonte
* **FontAttributes** &ndash; , como informações de estilo *itálico* e **em negrito**
* **ForegroundColor** &ndash; cor do texto
* **BackgroundColor** &ndash; cor do plano de fundo

Um exemplo de como criar e exibir um `FormattedString` é mostrado abaixo &ndash; Observe que ele está atribuído aos rótulos de `FormattedText` propriedade e não o `Text` propriedade.

```csharp
var labelFormatted = new Label ();
var fs = new FormattedString ();
fs.Spans.Add (new Span { Text="Red, ", ForegroundColor = Color.Red, FontSize = 20, FontAttributes = FontAttributes.Italic });
fs.Spans.Add (new Span { Text=" blue, ", ForegroundColor = Color.Blue, FontSize = 32 });
fs.Spans.Add (new Span { Text=" and green!", ForegroundColor = Color.Green, FontSize = 12 });
labelFormatted.FormattedText = fs;
```


### <a name="setting-font-info-per-platform"></a>Informações de fonte de configuração por plataforma

Como alternativa, o `Device.RuntimePlatform` propriedade pode ser usada para definir nomes de fontes diferentes em cada plataforma, conforme demonstrado neste código:

```csharp
label.FontFamily = Device.RuntimePlatform == Device.iOS ? "Lobster-Regular" :
   Device.RuntimePlatform == Device.Android ? "Lobster-Regular.ttf#Lobster-Regular" : "Assets/Fonts/Lobster-Regular.ttf#Lobster",
label.FontSize = Device.RuntimePlatform == Device.iOS ? 24 :
   Device.RuntimePlatform == Device.Android ? Device.GetNamedSize(NamedSize.Medium, label) : Device.GetNamedSize(NamedSize.Large, label);
```

É uma boa fonte de informações de fonte para iOS [iosfonts.com](http://iosfonts.com).

<a name="Setting_Font_in_Xaml" />

## <a name="setting-the-font-in-xaml"></a>Definição da fonte em Xaml

Xamarin. Forms controla esse texto de exibição têm um `Font` propriedade que pode ser definida em Xaml. A maneira mais simples para definir a fonte em Xaml é usar os valores de enumeração de tamanho nomeado, conforme mostrado neste exemplo:

```xaml
<Label Text="Login" FontSize="Large"/>
<Label Text="Instructions" FontSize="Small"/>
```

Não há um conversor interno para o `Font` propriedade que permite que todas as configurações de fonte ser expresso como um valor de cadeia de caracteres em Xaml. Os exemplos a seguir mostram como você pode especificar atributos de fonte e tamanhos em Xaml:

```xaml
<Label Text="Italics are supported" FontAttributes="Italic" />
<Label Text="Biggest NamedSize" FontSize="Large" />
<Label Text="Use size 72" FontSize="72" />
```

Para especificar várias `Font` configurações, combinar as configurações necessárias em uma cadeia de caracteres de atributo de fonte única. A cadeia de caracteres de atributo de fonte deve ser formatada como `"[font-face],[attributes],[size]"`. A ordem dos parâmetros é importante, todos os parâmetros são opcionais e várias `attributes` podem ser especificados, por exemplo:

```xaml
<Label Text="Small bold text" FontAttributes="Bold" FontSize="Micro" />
<Label Text="Really big italic text" FontAttributes="Italic" FontSize="72" />
```

O `FormattedString` classe também pode ser usada em XAML, como mostrado aqui:

```xaml
<Label>
    <Label.FormattedText>
        <FormattedString>
            <FormattedString.Spans>
                <Span Text="Red, " ForegroundColor="Red" FontAttributes="Italic" FontSize="20" />
                <Span Text=" blue, " ForegroundColor="Blue" FontSize="32" />
                <Span Text=" and green! " ForegroundColor="Green" FontSize="12"/>
            </FormattedString.Spans>
        </FormattedString>
    </Label.FormattedText>
</Label>
```

[`Device.RuntimePlatform`](~/xamarin-forms/platform/device.md#providing-platform-values) também pode ser usado em XAML para renderizar uma fonte diferente em cada plataforma. O exemplo a seguir usa uma face de fonte personalizada no iOS (<span style="font-family:MarkerFelt-Thin">MarkerFelt-fino</span>) e especifica apenas o tamanho/atributos em outras plataformas:

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

Ao especificar uma face de fonte personalizada, é sempre uma boa ideia usar `OnPlatform`, pois é difícil encontrar uma fonte que está disponível em todas as plataformas.

<a name="Using_a_Custom_Font" />

## <a name="using-a-custom-font"></a>Usando uma fonte personalizada

Usando uma fonte que não sejam de tipos internos requer alguma codificação específica de plataforma. Esta captura de tela mostra a fonte personalizada **Lobster** de [fontes do código-fonte aberto do Google](https://www.google.com/fonts) renderizado usando xamarin. Forms.

 [![A fonte personalizada no iOS e Android](fonts-images/custom-sml.png "personalizado fontes exemplo")](fonts-images/custom.png#lightbox "exemplo de fontes personalizadas")

As etapas necessárias para cada plataforma são descritas abaixo. Ao incluir arquivos de fonte personalizado com um aplicativo, certifique-se de verificar se a licença da fonte permite para distribuição.

### <a name="ios"></a>iOS

É possível exibir uma fonte personalizada, primeiro assegurar que ele seja carregado e fazer referência a ela por nome usando o xamarin. Forms `Font` métodos.
Siga as instruções em [esta postagem de blog](http://blog.xamarin.com/custom-fonts-in-ios/):

1. Adicione o arquivo de fonte com **ação de compilação: BundleResource**, e
2. Atualização de **Info. plist** arquivo (**fontes fornecidas pelo aplicativo**, ou `UIAppFonts`, key), em seguida,
3. Se referir a ele pelo nome onde quer que você definir uma fonte em xamarin. Forms!

```csharp
new Label
{
    Text = "Hello, Forms!",
    FontFamily = Device.RuntimePlatform == Device.iOS ? "Lobster-Regular" : null // set only for iOS
}
```

### <a name="android"></a>Android

Xamarin. Forms para Android podem fazer referência a uma fonte personalizada que foi adicionada ao projeto seguindo um padrão de nomenclatura específico. Primeiro, adicione o arquivo de fonte para o **ativos** pasta do projeto de aplicativo e o conjunto *ação de compilação: AndroidAsset*. Em seguida, use o caminho completo e *nome da fonte* separados por um hash (#) como o nome da fonte no xamarin. Forms, como mostra o trecho de código a seguir:

```csharp
new Label
{
  Text = "Hello, Forms!",
  FontFamily = Device.RuntimePlatform == Device.Android ? "Lobster-Regular.ttf#Lobster-Regular" : null // set only for Android
}
```

### <a name="windows"></a>Windows

Xamarin. Forms plataformas do Windows pode fazer referência a uma fonte personalizada que foi adicionada ao projeto seguindo um padrão de nomenclatura específico. Primeiro adicionar o arquivo de fonte para o **/Assets fontes/** pasta no projeto de aplicativo e definir o <span class="UIItem">compilar ação: conteúdo</span>. Em seguida, use a fonte e o caminho do nome de arquivo completo, seguido por um hash (#) e o <span class="UIItem">nome da fonte</span>, como o trecho de código a seguir demonstra:

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

Você também pode usar [ `Device.RuntimePlatform` ](~/xamarin-forms/platform/device.md#providing-platform-values) em XAML para renderizar uma fonte personalizada:

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

Xamarin. Forms fornece configurações padrão simples para permitir que você dimensione texto facilmente para todas as plataformas com suporte. Ele também permite que você especifique a face de fonte e tamanho &ndash; mesmo diferentes para cada plataforma &ndash; quando é necessário um controle mais refinado. O `FormattedString` classe pode ser usada para construir uma cadeia de caracteres que contém especificações de fonte diferente usando o `Span` classe.

Informações de fonte também podem ser especificadas em Xaml usando atributos de uma fonte formatado corretamente ou o `FormattedString` elemento com `Span` filhos.


## <a name="related-links"></a>Links relacionados

- [FontsSample](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithFonts/)
- [Texto (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text/)
