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
ms.openlocfilehash: a6eb3167fd0880984a74245c4653642ea3979354
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "72678836"
---
# <a name="right-to-left-localization"></a>Localização da direita para a esquerda

[![Baixar](~/media/shared/download.png) amostra Baixar a amostra](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/todolocalizedrtl)

_A localização da direita para a esquerda adiciona suporte para a direção do fluxo da direita para a esquerda aos aplicativos Xamarin.Forms._

> [!NOTE]
> A localização da direita para a esquerda exige o uso do iOS 9 ou superior e da API 17 ou superior no Android.

A direção do fluxo é a direção na qual os elementos de interface do usuário na página são detectados pelos olhos. Alguns idiomas, como árabe e hebraico, exigem que os elementos de interface do usuário sejam dispostos em uma direção de fluxo da direita para a esquerda. Isso pode ser alcançado [`VisualElement.FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) definindo a propriedade. Esta propriedade obtém ou define a direção na qual os elementos de IU fluem dentro de qualquer elemento pai que controla seu layout e deve ser definido como um dos valores de [`FlowDirection`](xref:Xamarin.Forms.FlowDirection) enumeração:

- [`LeftToRight`](xref:Xamarin.Forms.FlowDirection.LeftToRight)
- [`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft)
- [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent)

Definir [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) a [`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft) propriedade em um elemento geralmente define o alinhamento à direita, a ordem de leitura para a direita para a esquerda e o layout do controle para fluir da direita para a esquerda:

[![TodoItemPage em árabe com uma direção de fluxo da direita para a esquerda](rtl-images/TodoItemPage-Arabic.png "TodoItemPage em árabe com uma direção de fluxo da direita para a esquerda")](rtl-images/TodoItemPage-Arabic-Large.png#lightbox "TodoItemPage em árabe com uma direção de fluxo da direita para a esquerda")

> [!TIP]
> Você só deve [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) definir a propriedade no layout inicial. A alteração desse valor em runtime causa um processo caro de layout que afetará o desempenho.

O [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) valor de propriedade padrão para [`LeftToRight`](xref:Xamarin.Forms.FlowDirection.LeftToRight)um elemento `FlowDirection` sem um pai [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent)é, enquanto o padrão para um elemento com um pai é . Portanto, um elemento herda o valor da propriedade `FlowDirection` de seu pai na árvore visual e qualquer elemento pode substituir o valor obtido de seu pai.

> [!TIP]
> Ao localizar um aplicativo para idiomas da direita [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) para a esquerda, defina a propriedade em uma página ou layout raiz. Isso faz com que todos os elementos contidos na página ou layout raiz respondam de forma apropriada à direção do fluxo.

## <a name="respecting-device-flow-direction"></a>Respeitando a direção do fluxo do dispositivo

O respeito à direção do fluxo do dispositivo com base na região e no idioma selecionados é uma opção explícita do desenvolvedor e não ocorre automaticamente. Ele pode ser alcançado [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) definindo a propriedade em uma `static` [`Device.FlowDirection`](xref:Xamarin.Forms.Device.FlowDirection) página, ou layout raiz, para o valor:

```xaml
<ContentPage ... FlowDirection="{x:Static Device.FlowDirection}"> />
```

```csharp
this.FlowDirection = Device.FlowDirection;
```

Todos os elementos da página, ou layout raiz, herdarão o [`Device.FlowDirection`](xref:Xamarin.Forms.Device.FlowDirection) valor.

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

![Idiomas suportados pelo Info.plist](rtl-images/ios-locales.png "Idiomas suportados pelo Info.plist")

Para obter mais informações, confira [Noções básicas de localização no iOS](https://docs.microsoft.com/xamarin/ios/app-fundamentals/localization/#localization-basics-in-ios).

A localização da direita para a esquerda pode então ser testada, alterando o idioma e a região no dispositivo/simulador para uma localidade com leitura da direita para a esquerda que foi especificada em **Info.plist**.

> [!WARNING]
> Observe que ao alterar o idioma e a região para um local [`DatePicker`](xref:Xamarin.Forms.DatePicker) da direita para a esquerda no iOS, quaisquer visualizações abrirão uma exceção se você não incluir os recursos necessários para o local. Por exemplo, ao testar um aplicativo em árabe que tenha um `DatePicker`, verifique se a opção **oriente médio** está selecionada na seção **Internacionalização** no painel **Build do iOS**.

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

- [`NavigationPage`](xref:Xamarin.Forms.NavigationPage)localização do botão, localização do item da barra de ferramentas e [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) animação de transição é controlada pela localização do dispositivo, em vez da propriedade.
- [`CarouselPage`](xref:Xamarin.Forms.CarouselPage)a direção do deslizamento não vira.
- [`Image`](xref:Xamarin.Forms.Image)conteúdo visual não vira.
- [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String))e [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet(System.String,System.String,System.String,System.String[])) a orientação é controlada pela [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) localidade do dispositivo, em vez da propriedade.
- [`WebView`](xref:Xamarin.Forms.WebView)o conteúdo não [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) respeita a propriedade.
- Uma propriedade `TextDirection` precisa ser adicionada, para controlar o alinhamento do texto.

### <a name="ios"></a>iOS

- [`Stepper`](xref:Xamarin.Forms.Stepper)a orientação é controlada pela localidade [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) do dispositivo, em vez da propriedade.
- [`EntryCell`](xref:Xamarin.Forms.EntryCell)o alinhamento do texto é controlado pela [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) localização do dispositivo, em vez da propriedade.
- [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions)gestos e alinhamento não são invertidos.

### <a name="android"></a>Android

- [`SearchBar`](xref:Xamarin.Forms.SearchBar)a orientação é controlada pela localidade [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) do dispositivo, em vez da propriedade.
- [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions)a colocação é controlada pela localização [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) do dispositivo, em vez da propriedade.

### <a name="uwp"></a>UWP

- [`Editor`](xref:Xamarin.Forms.Editor)o alinhamento do texto é controlado pela [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) localização do dispositivo, em vez da propriedade.
- [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection)propriedade não é [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) herdada por crianças.
- [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions)o alinhamento do texto é controlado pela [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) localização do dispositivo, em vez da propriedade.

## <a name="force-right-to-left-layout"></a>Forçar o layout da direita para a esquerda

Os aplicativos Xamarin.iOS e Xamarin.Android podem ser forçados a usar sempre um layout da direita para a esquerda, independentemente das configurações do dispositivo, modificando os respectivos projetos da plataforma.

### <a name="ios"></a>iOS

Os aplicativos Xamarin.iOS podem ser forçados a usar sempre um layout da direita para a esquerda modificando a classe **AppDelegate** da seguinte forma:

1. Declare `IntPtr_objc_msgSend` a função como a `AppDelegate` primeira linha da sua classe:

   ```csharp
   [System.Runtime.InteropServices.DllImport(ObjCRuntime.Constants.ObjectiveCLibrary, EntryPoint = "objc_msgSend")]
   internal extern static IntPtr IntPtr_objc_msgSend(IntPtr receiver, IntPtr selector, UISemanticContentAttribute arg1);
   ```

1. Ligue `IntPtr_objc_msgSend` para a `FinishedLaunching` função a partir `FinshedLaunching` do método, antes de retornar do método:

   ```csharp
   bool result = base.FinishedLaunching(app, options);

   ObjCRuntime.Selector selector = new ObjCRuntime.Selector("setSemanticContentAttribute:");
   IntPtr_objc_msgSend(UIView.Appearance.Handle, selector.Handle, UISemanticContentAttribute.ForceRightToLeft);

   return result;
   ```

Essa abordagem é útil para aplicativos que sempre requerem um layout da direita [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) para a esquerda e remove a exigência de definir a propriedade.

Para obter mais `IntrPtr_objc_msgSend` informações sobre o método, consulte [seletores Objective-C em Xamarin.iOS](~/ios/internals/objective-c-selectors.md).

### <a name="android"></a>Android

Os aplicativos Xamarin.Android podem ser forçados a usar sempre um layout da direita para a esquerda modificando a classe **MainActivity** para incluir a seguinte linha:

```csharp
Window.DecorView.LayoutDirection = LayoutDirection.Rtl;
```

Essa abordagem é útil para aplicativos que sempre requerem um layout da direita [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) para a esquerda e remove a exigência de definir a propriedade.

## <a name="right-to-left-language-support-with-xamarinuniversity"></a>Suporte a idiomas da direita para a esquerda com o Xamarin.University

> [!VIDEO https://youtube.com/embed/f2lQ5yw3iiU]

**Vídeo sobre o suporte da direita para a esquerda do Xamarin.Forms 3.0**

## <a name="related-links"></a>Links relacionados

- [Aplicativo de exemplo TodoLocalizedRTL](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/todolocalizedrtl)
