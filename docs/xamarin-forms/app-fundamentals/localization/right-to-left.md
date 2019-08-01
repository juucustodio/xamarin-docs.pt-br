---
title: Localização da direita para a esquerda
description: A localização da direita para a esquerda adiciona suporte para a direção do fluxo da direita para a esquerda aos aplicativos Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 90E0CB16-C42A-4CC8-A70E-0C2CFB64A429
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 05/07/2018
ms.openlocfilehash: 78288680a1a522b2c6c413e1f8a2cec2a07835d6
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656977"
---
# <a name="right-to-left-localization"></a>Localização da direita para a esquerda

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/todolocalizedrtl)

_A localização da direita para a esquerda adiciona suporte para a direção do fluxo da direita para a esquerda aos aplicativos Xamarin.Forms._

> [!NOTE]
> A localização da direita para a esquerda exige o uso do iOS 9 ou superior e da API 17 ou superior no Android.

A direção do fluxo é a direção na qual os elementos de interface do usuário na página são detectados pelos olhos. Alguns idiomas, como árabe e hebraico, exigem que os elementos de interface do usuário sejam dispostos em uma direção de fluxo da direita para a esquerda. Isso pode ser feito com a definição da propriedade [`VisualElement.FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection). Essa propriedade obtém ou define a direção na qual os elementos de interface do usuário fluem nos elementos pai que controlam seu layout e deve ser definida com um dos valores de enumeração [`FlowDirection`](xref:Xamarin.Forms.FlowDirection):

- [`LeftToRight`](xref:Xamarin.Forms.FlowDirection.LeftToRight)
- [`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft)
- [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent)

Em geral, a definição da propriedade [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) como [`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft) em um elemento define o alinhamento para a direita, o sentido de leitura como direita para esquerda e o layout do controle para fluir da direita para a esquerda:

[![TodoItemPage em árabe com uma direção de fluxo da direita para a esquerda](rtl-images/TodoItemPage-Arabic.png "TodoItemPage em árabe com uma direção de fluxo da direita para a esquerda")](rtl-images/TodoItemPage-Arabic-Large.png#lightbox "TodoItemPage em árabe com uma direção de fluxo da direita para a esquerda")

> [!TIP]
> Você só deve definir a propriedade [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) no layout inicial. A alteração desse valor em tempo de execução causa um processo caro de layout que afetará o desempenho.

O valor padrão da propriedade [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) de um elemento sem um pai é [`LeftToRight`](xref:Xamarin.Forms.FlowDirection.LeftToRight), enquanto o `FlowDirection` padrão de um elemento com um pai é [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent). Portanto, um elemento herda o valor da propriedade `FlowDirection` de seu pai na árvore visual e qualquer elemento pode substituir o valor obtido de seu pai.

> [!TIP]
> Ao localizar um aplicativo para idiomas da direita para a esquerda, defina a propriedade [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) em uma página ou um layout raiz. Isso faz com que todos os elementos contidos na página ou layout raiz respondam de forma apropriada à direção do fluxo.

## <a name="respecting-device-flow-direction"></a>Respeitando a direção do fluxo do dispositivo

O respeito à direção do fluxo do dispositivo com base na região e no idioma selecionados é uma opção explícita do desenvolvedor e não ocorre automaticamente. Isso pode ser feito com a definição da propriedade [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) em uma página ou no layout raiz com o valor `static` [`Device.FlowDirection`](xref:Xamarin.Forms.Device.FlowDirection):

```xaml
<ContentPage ... FlowDirection="{x:Static Device.FlowDirection}"> />
```

```csharp
this.FlowDirection = Device.FlowDirection;
```

Em seguida, todos os elementos filho da página ou o layout raiz, por padrão, herdarão o valor [`Device.FlowDirection`](xref:Xamarin.Forms.Device.FlowDirection).

## <a name="platform-setup"></a>Instalação da plataforma

A instalação da plataforma específica é necessária para habilitar localidades com leitura da direita para a esquerda.

### <a name="ios"></a>iOS

A localidade com leitura da direita para a esquerda necessária deve ser adicionada como um idioma compatível aos itens de matriz da chave `CFBundleLocalizations` em **Info.plist**. O seguinte exemplo mostra o árabe adicionado à matriz da chave `CFBundleLocalizations`:

```xml
<key>CFBundleLocalizations</key>
<array>
    <string>en</string>
    <string>ar</string>
</array>
```

![Idiomas com suporte em Info.plist](rtl-images/ios-locales.png "Idiomas com suporte em Info.plist")

Para obter mais informações, confira [Noções básicas de localização no iOS](https://docs.microsoft.com/xamarin/ios/app-fundamentals/localization/#localization-basics-in-ios).

A localização da direita para a esquerda pode então ser testada, alterando o idioma e a região no dispositivo/simulador para uma localidade com leitura da direita para a esquerda que foi especificada em **Info.plist**.

> [!WARNING]
> Observe que ao alterar o idioma e a região para uma localidade com leitura da direita para a esquerda no iOS, as exibições [`DatePicker`](xref:Xamarin.Forms.DatePicker) gerarão uma exceção se você não incluir os recursos necessários para a localidade. Por exemplo, ao testar um aplicativo em árabe que tenha um `DatePicker`, verifique se a opção **oriente médio** está selecionada na seção **Internacionalização** no painel **Build do iOS**.

### <a name="android"></a>Android

O arquivo **AndroidManifest.xml** do aplicativo deve ser atualizado para que o nó `<uses-sdk>` defina o atributo `android:minSdkVersion` como 17 e o nó `<application>` defina o atributo `android:supportsRtl` como `true`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest ... >
    <uses-sdk android:minSdkVersion="17" ... />
    <application ... android:supportsRtl="true">
    </application>
</manifest>
```

A localização da direita para a esquerda pode então ser testada, alterando o dispositivo/emulador para usar o idioma da direita para a esquerda ou habilitando a opção **Forçar direção do layout da direita para esquerda** em **Configurações > Opções do Desenvolvedor**.

### <a name="universal-windows-platform-uwp"></a>UWP (Plataforma Universal do Windows)

Os recursos de idioma necessários devem ser especificados no nó `<Resources>` do arquivo **Package.appxmanifest**. O seguinte exemplo mostra o árabe adicionado ao nó `<Resources>`:

```xml
<Resources>
    <Resource Language="x-generate"/>
    <Resource Language="en" />
    <Resource Language="ar" />
</Resources>
```

Além disso, o UWP exige que a cultura padrão do aplicativo seja explicitamente definida na biblioteca do .NET Standard. Isso pode ser feito com a definição do atributo `NeutralResourcesLanguage` em `AssemblyInfo.cs`, ou em outra classe, como a cultura padrão:

```csharp
using System.Resources;

[assembly: NeutralResourcesLanguage("en")]
```

A localização da direita para a esquerda pode então ser testada, alterando o idioma e a região no dispositivo para a localidade da direita para a esquerda apropriada.

## <a name="limitations"></a>Limitações

Atualmente, a localização da direita para a esquerda do Xamarin.Forms tem várias limitações:

- A localização do botão [`NavigationPage`](xref:Xamarin.Forms.NavigationPage), a localização do item da barra de ferramentas e a animação de transição são controlados pela localidade do dispositivo, em vez de pela propriedade [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection).
- A direção de passar o dedo de [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) não é invertida.
- O conteúdo visual de [`Image`](xref:Xamarin.Forms.Image) não é invertido.
- A orientação de [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)) e [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet(System.String,System.String,System.String,System.String[])) é controlada pela localidade do dispositivo, em vez de pela propriedade [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection).
- O conteúdo de [`WebView`](xref:Xamarin.Forms.WebView) não respeita a propriedade [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection).
- Uma propriedade `TextDirection` precisa ser adicionada, para controlar o alinhamento do texto.

### <a name="ios"></a>iOS

- A orientação de [`Stepper`](xref:Xamarin.Forms.Stepper) é controlada pela localidade do dispositivo, em vez de pela propriedade [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection).
- O alinhamento do texto de [`EntryCell`](xref:Xamarin.Forms.EntryCell) é controlado pela localidade do dispositivo, em vez de pela propriedade [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection).
- Os gestos e o alinhamento de [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions) não são revertidos.

### <a name="android"></a>Android

- A orientação de [`SearchBar`](xref:Xamarin.Forms.SearchBar) é controlada pela localidade do dispositivo, em vez de pela propriedade [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection).
- O posicionamento de [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions) é controlado pela localidade do dispositivo, em vez de pela propriedade [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection).

### <a name="uwp"></a>UWP

- O alinhamento do texto de [`Editor`](xref:Xamarin.Forms.Editor) é controlado pela localidade do dispositivo, em vez de pela propriedade [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection).
- A propriedade de [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) não é herdada pelos filhos de [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage).
- O alinhamento do texto de [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions) é controlado pela localidade do dispositivo, em vez de pela propriedade [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection).

## <a name="right-to-left-language-support-with-xamarinuniversity"></a>Suporte a idiomas da direita para a esquerda com o Xamarin.University

> [!VIDEO https://youtube.com/embed/f2lQ5yw3iiU]

**Vídeo sobre o suporte da direita para a esquerda do Xamarin.Forms 3.0**

## <a name="related-links"></a>Links relacionados

- [Aplicativo de exemplo TodoLocalizedRTL](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/todolocalizedrtl)
