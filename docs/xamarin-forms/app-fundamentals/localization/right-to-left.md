---
title: Localização da direita para esquerda
description: Localização da direita para esquerda adiciona suporte para a direção do fluxo da direita para a esquerda para aplicativos xamarin. Forms.
ms.prod: xamarin
ms.assetid: 90E0CB16-C42A-4CC8-A70E-0C2CFB64A429
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 05/07/2018
ms.openlocfilehash: 94cf937a810e84ead0bdc1f69933968b42090a6f
ms.sourcegitcommit: d80d93957040a14b4638a91b0eac797cfaade840
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/07/2018
ms.locfileid: "34846426"
---
# <a name="right-to-left-localization"></a>Localização da direita para esquerda

_Localização da direita para esquerda adiciona suporte para a direção do fluxo da direita para a esquerda para aplicativos xamarin. Forms._

> [!NOTE]
> Localização da direita para esquerda requer o uso do iOS 9 ou superior e a API de 17 ou superior no Android.

Direção do fluxo é a direção na qual os elementos de interface do usuário na página são verificados pelos olhos. Alguns idiomas, como árabe e hebraico, exigem que os elementos de interface do usuário são dispostos em uma direção de fluxo da direita para esquerda. Isso pode ser feito definindo o [ `VisualElement.FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) propriedade. Esta propriedade obtém ou define a direção na qual fluxo de elementos de interface do usuário em qualquer elemento pai que controlem seu layout e deve ser definido como um do [ `FlowDirection` ](xref:Xamarin.Forms.FlowDirection) valores de enumeração:

- [`LeftToRight`](xref:Xamarin.Forms.FlowDirection.LeftToRight)
- [`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft)
- [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent)

Definindo o [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) propriedade [ `RightToLeft` ](xref:Xamarin.Forms.FlowDirection.RightToLeft) em um elemento geralmente define o alinhamento à direita, a ordem de leitura da direita para esquerda e o layout do controle de fluxo de direita para esquerda:

[![TodoItemPage em árabe com uma direção de fluxo da direita para esquerda](rtl-images/TodoItemPage-Arabic.png "TodoItemPage em árabe com uma direção de fluxo da direita para esquerda")](rtl-images/TodoItemPage-Arabic-Large.png#lightbox "TodoItemPage em árabe com uma direção de fluxo da direita para esquerda")

> [!TIP]
> Você só deve definir o [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) propriedade no layout inicial. A alteração desse valor no tempo de execução faz com que um processo caro de layout que afeta o desempenho.

O padrão [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) valor da propriedade para um elemento sem um pai é [ `LeftToRight` ](xref:Xamarin.Forms.FlowDirection.LeftToRight), enquanto o padrão `FlowDirection` para um elemento com um pai é [ `MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent). Portanto, um elemento herda o `FlowDirection` valor da propriedade de seu pai na árvore visual e qualquer elemento pode substituir o valor que ele obtém de seus pais.

> [!TIP]
> Ao localizar um aplicativo para idiomas da direita para esquerda, defina o [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) propriedade em um layout de página ou raiz. Isso faz com que todos os elementos contidos dentro da página ou layout de raiz, para responder adequadamente para a direção do fluxo.

## <a name="respecting-device-flow-direction"></a>Respeitando a direção do fluxo de dispositivo

Respeitando a direção do fluxo do dispositivo com base no idioma selecionado e região é uma opção do desenvolvedor explícita e não ocorre automaticamente. Isso pode ser feito definindo o [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) em uma página ou layout de raiz para o `static` [ `Device.FlowDirection` ](xref:Xamarin.Forms.Device.FlowDirection) valor:

```xaml
<ContentPage ... FlowDirection="{x:Static Device.FlowDirection}"> />
```

```csharp
this.FlowDirection = Device.FlowDirection;
```

Todos os elementos filho da página ou layout de raiz, por padrão, em seguida, herdarão o [ `Device.FlowDirection` ](xref:Xamarin.Forms.Device.FlowDirection) valor.

## <a name="platform-setup"></a>Instalação de plataforma

Instalação da plataforma específica é necessária para habilitar localidades da direita para esquerda.

### <a name="ios"></a>iOS

A localidade da direita para esquerda necessária deve ser adicionada como um idioma com suporte para os itens de matriz para o `CFBundleLocalizations` chave em **Info. plist**. O exemplo a seguir mostra árabe tendo foi adicionado para a matriz para o `CFBundleLocalizations` chave:

```xml
<key>CFBundleLocalizations</key>
<array>
    <string>en</string>
    <string>ar</string>
</array>
```

![Idiomas com suporte de info. plist](rtl-images/ios-locales.png "Info. plist de idiomas com suporte")

Para obter mais informações, consulte [Noções básicas de localização no iOS](https://docs.microsoft.com/en-gb/xamarin/ios/app-fundamentals/localization/#localization-basics-in-ios).

Localização da direita para esquerda, em seguida, pode ser testada, alterando o idioma e região no simulador/dispositivo para uma localidade da direita para esquerda que foi especificada na **Info. plist**.

> [!WARNING]
> Observe que, ao alterar o idioma e região para uma localidade da direita para esquerda no iOS, qualquer [ `DatePicker` ](xref:Xamarin.Forms.DatePicker) exibições lançará uma exceção se você não incluir os recursos necessários para a localidade. Por exemplo, quando um aplicativo de teste em árabe que tem um `DatePicker`, certifique-se de que **Ásia** está selecionado no **internacionalização** seção o **iOS compilação** painel.

### <a name="android"></a>Android

O aplicativo **AndroidManifest.xml** arquivo deve ser atualizado para que o `<uses-sdk>` conjuntos de nó a `android:minSdkVersion` atributo 17 e o `<application>` conjuntos de nó de `android:supportsRtl` atributo `true`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest ... >
    <uses-sdk android:minSdkVersion="17" ... />
    <application ... android:supportsRtl="true">
    </application>
</manifest>
```

Localização da direita para esquerda, em seguida, pode ser testada, alterando o dispositivo/emulador para usar o idioma da direita para esquerda, ou habilitando **direção do layout RTL Force** na **Configurações > Opções do desenvolvedor**.

### <a name="universal-windows-platform-uwp"></a>UWP (Plataforma Universal do Windows)

Os recursos de idioma necessário devem ser especificados no `<Resources>` nó do **Package. appxmanifest** arquivo. O exemplo a seguir mostra árabe tendo foi adicionado para o `<Resources>` nó:

```xml
<Resources>
    <Resource Language="x-generate"/>
    <Resource Language="en" />
    <Resource Language="ar" />
</Resources>
```

Além disso, UWP requer que a cultura do padrão do aplicativo é explicitamente definida na biblioteca do .NET padrão. Isso pode ser feito definindo o `NeutralResourcesLanguage` atributo `AssemblyInfo.cs`, ou em outra classe, para a cultura padrão:

```csharp
using System.Resources;

[assembly: NeutralResourcesLanguage("en")]
```

Localização da direita para esquerda, em seguida, pode ser testada, alterando o idioma e região no dispositivo para a localidade apropriada da direita para esquerda.

## <a name="limitations"></a>Limitações

Localização da direita para esquerda de xamarin. Forms atualmente tem várias limitações:

- [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) local de botão de barra de ferramentas item local e a animação de transição é controlada pela localidade do dispositivo, em vez de [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) propriedade.
- [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) não inverter a direção de passe o dedo.
- [`Image`](xref:Xamarin.Forms.Image) o conteúdo visual não inverte.
- [`DisplayAlert`](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayAlert/p/System.String/System.String/System.String/) e [ `DisplayActionSheet` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayActionSheet/p/System.String/System.String/System.String/System.String[]/) orientação é controlada pela localidade do dispositivo, em vez de [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) propriedade.
- [`WebView`](xref:Xamarin.Forms.WebView) conteúdo não respeita a [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) propriedade.
- Um `TextDirection` a propriedade deve ser adicionado, para controlar o alinhamento de texto.

### <a name="ios"></a>iOS

- [`Stepper`](xref:Xamarin.Forms.Stepper) orientação é controlada pela localidade do dispositivo, em vez de [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) propriedade.
- [`EntryCell`](xref:Xamarin.Forms.EntryCell) alinhamento de texto é controlado pela localidade do dispositivo, em vez de [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) propriedade.
- [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions) gestos e alinhamento não são revertidas.

### <a name="android"></a>Android

- [`SearchBar`](xref:Xamarin.Forms.SearchBar) orientação é controlada pela localidade do dispositivo, em vez de [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) propriedade.
- [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions) posicionamento é controlado pela localidade do dispositivo, em vez de [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) propriedade.

### <a name="uwp"></a>UWP

- [`Editor`](xref:Xamarin.Forms.Editor) alinhamento de texto é controlado pela localidade do dispositivo, em vez de [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) propriedade.
- [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) propriedade não é herdada por [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) filhos.
- [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions) alinhamento de texto é controlado pela localidade do dispositivo, em vez de [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) propriedade.

## <a name="right-to-left-language-support-with-xamarinuniversity"></a>Direita para esquerda idioma suporte com Xamarin.University

> [!VIDEO https://youtube.com/embed/f2lQ5yw3iiU]

**Suporte de xamarin. Forms 3.0 da direita para a esquerda, por [University Xamarin](https://university.xamarin.com/)**

## <a name="related-links"></a>Links relacionados

- [Aplicativo de exemplo TodoLocalizedRTL](https://developer.xamarin.com/samples/xamarin-forms/TodoLocalizedRTL/)
