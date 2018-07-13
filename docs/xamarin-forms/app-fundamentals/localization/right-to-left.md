---
title: Localização da direita para esquerda
description: Localização da direita para esquerda adiciona suporte para a direção do fluxo da direita para esquerda para aplicativos xamarin. Forms.
ms.prod: xamarin
ms.assetid: 90E0CB16-C42A-4CC8-A70E-0C2CFB64A429
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 05/07/2018
ms.openlocfilehash: 6c0de68f974c704b5f43232a1fc98065c90ee4f7
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995714"
---
# <a name="right-to-left-localization"></a>Localização da direita para esquerda

_Localização da direita para esquerda adiciona suporte para a direção do fluxo da direita para esquerda para aplicativos xamarin. Forms._

> [!NOTE]
> Localização da direita para esquerda requer o uso de API 17 ou superior no Android e iOS 9 ou superior.

Direção do fluxo é a direção na qual os elementos de interface do usuário na página são verificados pelo olhos. Algumas linguagens, como árabe e hebraico, exigem que os elementos de interface do usuário são dispostos em uma direção de fluxo da direita para esquerda. Isso pode ser feito definindo a [ `VisualElement.FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) propriedade. Esta propriedade obtém ou define a direção na qual fluxo de elementos de interface do usuário dentre quaisquer elementos pai que controlem seu layout e devem ser definido como um dos [ `FlowDirection` ](xref:Xamarin.Forms.FlowDirection) valores de enumeração:

- [`LeftToRight`](xref:Xamarin.Forms.FlowDirection.LeftToRight)
- [`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft)
- [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent)

Definindo o [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) propriedade [ `RightToLeft` ](xref:Xamarin.Forms.FlowDirection.RightToLeft) em um elemento geralmente define o alinhamento para a direita, a ordem de leitura para a direita para esquerda e o layout do controle para fluir de direita para esquerda:

[![TodoItemPage em árabe com uma direção de fluxo da direita para esquerda](rtl-images/TodoItemPage-Arabic.png "TodoItemPage em árabe com uma direção de fluxo da direita para esquerda")](rtl-images/TodoItemPage-Arabic-Large.png#lightbox "TodoItemPage em árabe com uma direção de fluxo da direita para esquerda")

> [!TIP]
> Você só deve definir a [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) propriedade no layout inicial. A alteração desse valor em tempo de execução faz com que um processo caro de layout que afeta o desempenho.

O padrão [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) é o valor de propriedade para um elemento sem um pai [ `LeftToRight` ](xref:Xamarin.Forms.FlowDirection.LeftToRight), enquanto o padrão `FlowDirection` para um elemento com um pai é [ `MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent). Portanto, um elemento herda o `FlowDirection` valor da propriedade de seu pai na árvore visual e qualquer elemento pode substituir o valor obtido do seu pai.

> [!TIP]
> Ao localizar um aplicativo para idiomas da direita para esquerda, defina as [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) propriedade em um layout de página ou raiz. Isso faz com que todos os elementos contidos dentro da página ou o layout de raiz, responder de forma apropriada para a direção do fluxo.

## <a name="respecting-device-flow-direction"></a>Respeitar a direção do fluxo de dispositivo

Respeitar a direção do fluxo do dispositivo com base no idioma selecionado e a região é uma opção explícita de desenvolvedor e não acontece automaticamente. Isso pode ser feito definindo a [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) em uma página ou o layout de raiz para o `static` [ `Device.FlowDirection` ](xref:Xamarin.Forms.Device.FlowDirection) valor:

```xaml
<ContentPage ... FlowDirection="{x:Static Device.FlowDirection}"> />
```

```csharp
this.FlowDirection = Device.FlowDirection;
```

Todos os elementos filho da página ou layout raiz, por padrão, em seguida, herdará as [ `Device.FlowDirection` ](xref:Xamarin.Forms.Device.FlowDirection) valor.

## <a name="platform-setup"></a>Instalação de plataforma

Instalação de plataforma específica é necessária para habilitar localidades da direita para esquerda.

### <a name="ios"></a>iOS

A localidade da direita para esquerda necessária deve ser adicionada como um idioma com suporte para os itens da matriz para o `CFBundleLocalizations` principais no **Info. plist**. O exemplo a seguir mostra o árabe, tendo sido adicionado à matriz para o `CFBundleLocalizations` chave:

```xml
<key>CFBundleLocalizations</key>
<array>
    <string>en</string>
    <string>ar</string>
</array>
```

![Idiomas com suporte de info. plist](rtl-images/ios-locales.png "Info. plist de idiomas com suporte")

Para obter mais informações, consulte [Noções básicas de localização no iOS](https://docs.microsoft.com/en-gb/xamarin/ios/app-fundamentals/localization/#localization-basics-in-ios).

Localização da direita para esquerda, em seguida, pode ser testada, alterando o idioma e região no dispositivo/simulador para uma localidade da direita para esquerda que foi especificada na **Info. plist**.

> [!WARNING]
> Observe que ao alterar o idioma e região para uma localidade da direita para a esquerda no iOS, qualquer [ `DatePicker` ](xref:Xamarin.Forms.DatePicker) exibições lançará uma exceção se você não incluir os recursos necessários para a localidade. Por exemplo, ao testar um aplicativo em árabe que tem um `DatePicker`, certifique-se de que **Ásia** está selecionado no **internacionalização** seção o **Build do iOS** painel.

### <a name="android"></a>Android

O aplicativo **androidmanifest. XML** arquivo deve ser atualizado para que o `<uses-sdk>` conjuntos de nós a `android:minSdkVersion` 17, do atributo e o `<application>` conjuntos de nó a `android:supportsRtl` atributo para `true`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest ... >
    <uses-sdk android:minSdkVersion="17" ... />
    <application ... android:supportsRtl="true">
    </application>
</manifest>
```

Localização da direita para esquerda, em seguida, pode ser testada, alterando o dispositivo/emulador para usar o idioma da direita para esquerda, ou habilitando **direção do layout RTL força** na **Configurações > Opções do desenvolvedor**.

### <a name="universal-windows-platform-uwp"></a>UWP (Plataforma Universal do Windows)

Os recursos de idioma necessário devem ser especificados na `<Resources>` nó do **Package. appxmanifest** arquivo. O exemplo a seguir mostra o árabe ter que foram adicionado ao `<Resources>` nó:

```xml
<Resources>
    <Resource Language="x-generate"/>
    <Resource Language="en" />
    <Resource Language="ar" />
</Resources>
```

Além disso, a UWP requer que a cultura do aplicativo padrão é explicitamente definida na biblioteca do .NET Standard. Isso pode ser feito definindo a `NeutralResourcesLanguage` de atributo em `AssemblyInfo.cs`, ou em outra classe, para a cultura padrão:

```csharp
using System.Resources;

[assembly: NeutralResourcesLanguage("en")]
```

Localização da direita para esquerda, em seguida, pode ser testada, alterando o idioma e região no dispositivo para a localidade apropriada da direita para esquerda.

## <a name="limitations"></a>Limitações

Atualmente, a localização da direita para esquerda de xamarin. Forms tem várias limitações:

- [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) local do botão, a barra de ferramentas do item local e, em seguida, animação de transição é controlada pela localidade do dispositivo, em vez de [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) propriedade.
- [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) não inverter a direção de passar o dedo.
- [`Image`](xref:Xamarin.Forms.Image) conteúdo visual não Inverter.
- [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)) e [ `DisplayActionSheet` ](xref:Xamarin.Forms.Page.DisplayActionSheet(System.String,System.String,System.String,System.String[])) orientação é controlada pela localidade do dispositivo, em vez de [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) propriedade.
- [`WebView`](xref:Xamarin.Forms.WebView) conteúdo não respeita a [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) propriedade.
- Um `TextDirection` propriedade precisa ser adicionado, para controlar o alinhamento do texto.

### <a name="ios"></a>iOS

- [`Stepper`](xref:Xamarin.Forms.Stepper) orientação é controlada pela localidade do dispositivo, em vez de [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) propriedade.
- [`EntryCell`](xref:Xamarin.Forms.EntryCell) alinhamento de texto é controlado pela localidade do dispositivo, em vez de [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) propriedade.
- [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions) gestos e o alinhamento não são revertidas.

### <a name="android"></a>Android

- [`SearchBar`](xref:Xamarin.Forms.SearchBar) orientação é controlada pela localidade do dispositivo, em vez de [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) propriedade.
- [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions) o posicionamento é controlado pela localidade do dispositivo, em vez de [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) propriedade.

### <a name="uwp"></a>UWP

- [`Editor`](xref:Xamarin.Forms.Editor) alinhamento de texto é controlado pela localidade do dispositivo, em vez de [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) propriedade.
- [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) propriedade não é herdada pela [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) filhos.
- [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions) alinhamento de texto é controlado pela localidade do dispositivo, em vez de [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) propriedade.

## <a name="right-to-left-language-support-with-xamarinuniversity"></a>Suporte a direita para esquerda linguagem com Xamarin.University

> [!VIDEO https://youtube.com/embed/f2lQ5yw3iiU]

**Suporte de xamarin. Forms 3.0 da direita para a esquerda, por [Xamarin University](https://university.xamarin.com/)**

## <a name="related-links"></a>Links relacionados

- [Aplicativo de exemplo TodoLocalizedRTL](https://developer.xamarin.com/samples/xamarin-forms/TodoLocalizedRTL/)
