---
title: Localização da direita para a esquerda
description: A localização da direita para a esquerda adiciona suporte à direção de fluxo da direita para a esquerda para Xamarin.Forms aplicativos.
ms.prod: xamarin
ms.assetid: 90E0CB16-C42A-4CC8-A70E-0C2CFB64A429
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 05/07/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 212674472706a36c66436a3955ab7b988f8e246b
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84137547"
---
# <a name="right-to-left-localization"></a>Localização da direita para a esquerda

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/todolocalizedrtl)

_A localização da direita para a esquerda adiciona suporte à direção de fluxo da direita para a esquerda para Xamarin.Forms aplicativos._

> [!NOTE]
> A localização da direita para a esquerda exige o uso do iOS 9 ou superior e da API 17 ou superior no Android.

A direção do fluxo é a direção na qual os elementos de interface do usuário na página são detectados pelos olhos. Alguns idiomas, como árabe e hebraico, exigem que os elementos de interface do usuário sejam dispostos em uma direção de fluxo da direita para a esquerda. Isso pode ser feito definindo a [`VisualElement.FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) propriedade. Essa propriedade Obtém ou define a direção na qual os elementos da interface do usuário fluem dentro de qualquer elemento pai que controla seu layout e deve ser definido como um dos [`FlowDirection`](xref:Xamarin.Forms.FlowDirection) valores de enumeração:

- [`LeftToRight`](xref:Xamarin.Forms.FlowDirection.LeftToRight)
- [`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft)
- [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent)

Definir a [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) propriedade como [`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft) em um elemento geralmente define o alinhamento à direita, a ordem de leitura da direita para a esquerda e o layout do controle para fluir da direita para a esquerda:

[![TodoItemPage em árabe com direção de fluxo da direita para a esquerda](rtl-images/TodoItemPage-Arabic.png "TodoItemPage em árabe com direção de fluxo da direita para a esquerda")](rtl-images/TodoItemPage-Arabic-Large.png#lightbox "TodoItemPage em árabe com direção de fluxo da direita para a esquerda")

> [!TIP]
> Você só deve definir a [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) propriedade no layout inicial. A alteração desse valor em runtime causa um processo caro de layout que afetará o desempenho.

O [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) valor da propriedade padrão para um elemento sem um pai é [`LeftToRight`](xref:Xamarin.Forms.FlowDirection.LeftToRight) , enquanto o padrão `FlowDirection` para um elemento com um pai é [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent) . Portanto, um elemento herda o valor da propriedade `FlowDirection` de seu pai na árvore visual e qualquer elemento pode substituir o valor obtido de seu pai.

> [!TIP]
> Ao localizar um aplicativo para idiomas da direita para a esquerda, defina a [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) propriedade em um layout de página ou raiz. Isso faz com que todos os elementos contidos na página ou layout raiz respondam de forma apropriada à direção do fluxo.

## <a name="respecting-device-flow-direction"></a>Respeitando a direção do fluxo do dispositivo

O respeito à direção do fluxo do dispositivo com base na região e no idioma selecionados é uma opção explícita do desenvolvedor e não ocorre automaticamente. Pode ser obtido definindo a [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) propriedade em uma página ou layout raiz, para o `static` [`Device.FlowDirection`](xref:Xamarin.Forms.Device.FlowDirection) valor:

```xaml
<ContentPage ... FlowDirection="{x:Static Device.FlowDirection}"> />
```

```csharp
this.FlowDirection = Device.FlowDirection;
```

Todos os elementos filho da página ou layout raiz, por padrão, herdarão o [`Device.FlowDirection`](xref:Xamarin.Forms.Device.FlowDirection) valor.

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

![Idiomas com suporte do info. plist](rtl-images/ios-locales.png "Idiomas com suporte do info. plist")

Para obter mais informações, confira [Noções básicas de localização no iOS](https://docs.microsoft.com/xamarin/ios/app-fundamentals/localization/#localization-basics-in-ios).

A localização da direita para a esquerda pode então ser testada, alterando o idioma e a região no dispositivo/simulador para uma localidade com leitura da direita para a esquerda que foi especificada em **Info.plist**.

> [!WARNING]
> Observe que, ao alterar o idioma e a região para uma localidade da direita para a esquerda no iOS, qualquer [`DatePicker`](xref:Xamarin.Forms.DatePicker) exibição gerará uma exceção se você não incluir os recursos necessários para a localidade. Por exemplo, ao testar um aplicativo em árabe que tenha um `DatePicker`, verifique se a opção **oriente médio** está selecionada na seção **Internacionalização** no painel **Build do iOS**.

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

### <a name="universal-windows-platform-uwp"></a>Plataforma Universal do Windows (UWP)

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

Xamarin.Formsa localização da direita para a esquerda atualmente tem uma série de limitações:

- [`NavigationPage`](xref:Xamarin.Forms.NavigationPage)o local do botão, a localização do item da barra de ferramentas e a animação da transição são controlados pela localidade do dispositivo, em vez da [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) propriedade.
- [`CarouselPage`](xref:Xamarin.Forms.CarouselPage)a direção do dedo não inverte.
- [`Image`](xref:Xamarin.Forms.Image)o conteúdo Visual não é invertido.
- [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String))e a [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet(System.String,System.String,System.String,System.String[])) orientação é controlada pela localidade do dispositivo, em vez da [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) propriedade.
- [`WebView`](xref:Xamarin.Forms.WebView)o conteúdo não respeita a [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) propriedade.
- Uma propriedade `TextDirection` precisa ser adicionada, para controlar o alinhamento do texto.

### <a name="ios"></a>iOS

- [`Stepper`](xref:Xamarin.Forms.Stepper)a orientação é controlada pela localidade do dispositivo, em vez da [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) propriedade.
- [`EntryCell`](xref:Xamarin.Forms.EntryCell)o alinhamento de texto é controlado pela localidade do dispositivo, em vez da [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) propriedade.
- [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions)gestos e alinhamento não são revertidos.

### <a name="android"></a>Android

- [`SearchBar`](xref:Xamarin.Forms.SearchBar)a orientação é controlada pela localidade do dispositivo, em vez da [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) propriedade.
- [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions)o posicionamento é controlado pela localidade do dispositivo, em vez da [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) propriedade.

### <a name="uwp"></a>UWP

- [`Editor`](xref:Xamarin.Forms.Editor)o alinhamento de texto é controlado pela localidade do dispositivo, em vez da [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) propriedade.
- [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection)a propriedade não é herdada por [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) filhos.
- [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions)o alinhamento de texto é controlado pela localidade do dispositivo, em vez da [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) propriedade.

## <a name="force-right-to-left-layout"></a>Forçar layout da direita para a esquerda

Os aplicativos xamarin. iOS e Xamarin. Android podem ser forçados a usar sempre um layout da direita para a esquerda, independentemente das configurações do dispositivo, modificando os projetos da respectiva plataforma.

### <a name="ios"></a>iOS

Os aplicativos Xamarin. iOS podem ser forçados a usar sempre um layout da direita para a esquerda, modificando a classe **AppDelegate** da seguinte maneira:

1. Declare a `IntPtr_objc_msgSend` função como a primeira linha em sua `AppDelegate` classe:

   ```csharp
   [System.Runtime.InteropServices.DllImport(ObjCRuntime.Constants.ObjectiveCLibrary, EntryPoint = "objc_msgSend")]
   internal extern static IntPtr IntPtr_objc_msgSend(IntPtr receiver, IntPtr selector, UISemanticContentAttribute arg1);
   ```

1. Chame a `IntPtr_objc_msgSend` função do `FinishedLaunching` método, antes de retornar do `FinshedLaunching` método:

   ```csharp
   bool result = base.FinishedLaunching(app, options);

   ObjCRuntime.Selector selector = new ObjCRuntime.Selector("setSemanticContentAttribute:");
   IntPtr_objc_msgSend(UIView.Appearance.Handle, selector.Handle, UISemanticContentAttribute.ForceRightToLeft);

   return result;
   ```

Essa abordagem é útil para aplicativos que sempre exigem um layout da direita para a esquerda e remove a necessidade de definir a [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) propriedade.

Para obter mais informações sobre o `IntrPtr_objc_msgSend` método, consulte os [seletores de Objective-C no Xamarin. Ios](~/ios/internals/objective-c-selectors.md).

### <a name="android"></a>Android

Os aplicativos Xamarin. Android podem ser forçados a usar sempre um layout da direita para a esquerda, modificando a classe **MainActivity** para incluir a seguinte linha:

```csharp
Window.DecorView.LayoutDirection = LayoutDirection.Rtl;
```

Essa abordagem é útil para aplicativos que sempre exigem um layout da direita para a esquerda e remove a necessidade de definir a [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) propriedade.

## <a name="right-to-left-language-support-with-xamarinuniversity"></a>Suporte a idiomas da direita para a esquerda com o Xamarin.University

> [!VIDEO https://youtube.com/embed/f2lQ5yw3iiU]

**Xamarin.Formsvídeo de suporte da direita para a esquerda 3,0**

## <a name="related-links"></a>Links relacionados

- [Aplicativo de exemplo TodoLocalizedRTL](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/todolocalizedrtl)
