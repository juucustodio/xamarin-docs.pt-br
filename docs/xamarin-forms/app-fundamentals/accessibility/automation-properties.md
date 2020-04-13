---
title: Propriedades de automação
description: Este artigo explica como usar a classe AutomationProperties em um aplicativo do Xamarin.Forms para que um leitor de tela possa falar sobre os elementos na página.
ms.prod: xamarin
ms.assetid: c0bb6893-fd26-47e7-88e5-3c333c9f786c
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/18/2018
ms.openlocfilehash: 12c6229c1922f0bd4a4d25ca796bcb46141a326c
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "77131135"
---
# <a name="automation-properties-in-xamarinforms"></a>Propriedades de automação no Xamarin.Forms

[![Baixar](~/media/shared/download.png) amostra Baixar a amostra](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-accessibility)

_O Xamarin.Forms permite que os valores de acessibilidade sejam definidos nos elementos da interface do usuário usando propriedades anexadas da classe AutomationProperties, que por sua vez define valores de acessibilidade nativa. Este artigo explica como usar a classe AutomaçãoPropriedades, para que um leitor de tela possa falar sobre os elementos na página._

O Xamarin.Forms permite que propriedades de automação sejam definidas em elementos da interface do usuário por meio das propriedades anexadas a seguir:

- `AutomationProperties.IsInAccessibleTree` – indica se o elemento está disponível para um aplicativo acessível. Para obter mais informações, confira [AutomationProperties.IsInAccessibleTree](#isinaccessibletree).
- `AutomationProperties.Name` – uma breve descrição do elemento que serve como um identificador falado para o elemento. Para obter mais informações, confira [AutomationProperties.Name](#name).
- `AutomationProperties.HelpText` – uma descrição mais detalhada do elemento, que pode ser considerada como o texto de dica de ferramenta associado ao elemento. Para obter mais informações, confira [AutomationProperties.HelpText](#helptext).
- `AutomationProperties.LabeledBy` – permite que outro elemento defina informações de acessibilidade sobre o elemento atual. Para obter mais informações, confira [AutomationProperties.LabeledBy](#labeledby).

Essas propriedades anexadas definem valores de acessibilidade nativos, de modo que um leitor de tela pode falar sobre o elemento. Para obter mais informações sobre as propriedades anexadas, confira [Propriedades anexadas](~/xamarin-forms/xaml/attached-properties.md).

> [!IMPORTANT]
> Usar as propriedades anexadas `AutomationProperties` pode afetar a execução de testes de interface do usuário no Android. As [`AutomationId`](xref:Xamarin.Forms.Element.AutomationId) `AutomationProperties.Name` propriedades `AutomationProperties.HelpText` e propriedades definirão a `ContentDescription` propriedade nativa, com `AutomationProperties.Name` os `AutomationId` valores `AutomationProperties.Name` e `AutomationProperties.HelpText` `AutomationProperties.HelpText` propriedades prevalecendo sobre o valor (se ambos forem definidos, os valores serão concatenados). Isso significa que testes que procurarem `AutomationId` falharão se `AutomationProperties.Name` ou `AutomationProperties.HelpText` também estiverem definidos no elemento. Nesse cenário, testes da interface do usuário deverão ser alterados para procurar pelo valor de `AutomationProperties.Name` ou `AutomationProperties.HelpText`, ou por uma concatenação dos dois.

Cada plataforma tem um leitor de tela diferente para narrar os valores de acessibilidade:

- O iOS tem o VoiceOver. Para obter mais informações, confira [Test Accessibility on Your Device with VoiceOver](https://developer.apple.com/library/content/technotes/TestingAccessibilityOfiOSApps/TestAccessibilityonYourDevicewithVoiceOver/TestAccessibilityonYourDevicewithVoiceOver.html) (Testando a acessibilidade em seu dispositivo com o VoiceOver) em developer.apple.com.
- O Android tem o TalkBack. Para obter mais informações, confira [Testing Your App's Accessibility](https://developer.android.com/training/accessibility/testing.html#talkback) (Testando a acessibilidade de seu aplicativo) em developer.android.com.
- O Windows tem o Narrador. Para obter mais informações, confira [Verificar cenários do aplicativo principal usando o Narrador](/windows/uwp/accessibility/accessibility-testing#verify-main-app-scenarios-by-using-narrator).

No entanto, o comportamento exato do leitor de tela depende do software e da configuração que o usuário faz nele. Por exemplo, a maioria dos leitores de tela lê o texto associado a um controle quando ele entra em foco, permitindo que os usuários se orientem enquanto percorrem os controles na página. Alguns leitores de tela também leem toda a interface do usuário do aplicativo quando a página é exibida, o que permite que o usuário receba todo o conteúdo informativo disponível na página antes de tentar navegar por ela.

Leitores de tela também leem diferentes valores de acessibilidade. No aplicativo de exemplo:

- O VoiceOver lê o valor de `Placeholder` de `Entry`, seguido por instruções de como usar o controle.
- O TalkBack lê o valor de `Placeholder` de `Entry`, seguido pelo valor de `AutomationProperties.HelpText` e por instruções de como usar o controle.
- O Narrador lê o valor de `AutomationProperties.LabeledBy` de `Entry`, seguido por instruções de como usar o controle.

Além disso, o Narrador prioriza `AutomationProperties.Name`, `AutomationProperties.LabeledBy` e, em seguida, `AutomationProperties.HelpText`. No Android, o TalkBack pode combinar os valores de `AutomationProperties.Name` e `AutomationProperties.HelpText`. Portanto, é recomendável que testes de acessibilidade minuciosos sejam executados em cada plataforma para garantir a experiência ideal.

<a name="isinaccessibletree" />

## <a name="automationpropertiesisinaccessibletree"></a>AutomationProperties.IsInAccessibleTree

A propriedade anexada `AutomationProperties.IsInAccessibleTree` é um `boolean` que determina se o elemento é acessível e, portanto, visível para leitores de tela. Ela deve ser definida como `true` para usar outras propriedades anexadas de acessibilidade. Isso pode ser feito no XAML da seguinte maneira:

```xaml
<Entry AutomationProperties.IsInAccessibleTree="true" />
```

Como alternativa, ela pode ser definida em C# da seguinte maneira:

```csharp
var entry = new Entry();
AutomationProperties.SetIsInAccessibleTree(entry, true);
```

> [!NOTE]
> Observe que [`SetValue`](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object)) o método também pode `AutomationProperties.IsInAccessibleTree` ser usado para definir a propriedade anexada –`entry.SetValue(AutomationProperties.IsInAccessibleTreeProperty, true);`

<a name="name" />

## <a name="automationpropertiesname"></a>AutomationProperties.Name

O valor da propriedade anexada `AutomationProperties.Name` deve ser uma cadeia de caracteres de texto curta e descritiva que usa de um leitor de tela para anunciar um elemento. Essa propriedade deve ser definida para elementos cujo significado é importante para entender o conteúdo ou para interagir com a interface do usuário. Isso pode ser feito no XAML da seguinte maneira:

```xaml
<ActivityIndicator AutomationProperties.IsInAccessibleTree="true"
                   AutomationProperties.Name="Progress indicator" />
```

Como alternativa, ela pode ser definida em C# da seguinte maneira:

```csharp
var activityIndicator = new ActivityIndicator();
AutomationProperties.SetIsInAccessibleTree(activityIndicator, true);
AutomationProperties.SetName(activityIndicator, "Progress indicator");
```

> [!NOTE]
> Observe que [`SetValue`](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object)) o método também pode `AutomationProperties.Name` ser usado para definir a propriedade anexada –`activityIndicator.SetValue(AutomationProperties.NameProperty, "Progress indicator");`

<a name="helptext" />

## <a name="automationpropertieshelptext"></a>AutomationProperties.HelpText

A propriedade anexada `AutomationProperties.HelpText` deve ser definida como o texto que descreve o elemento da interface do usuário e pode ser considerada como um texto de dica de ferramenta associado ao elemento. Isso pode ser feito no XAML da seguinte maneira:

```xaml
<Button Text="Toggle ActivityIndicator"
        AutomationProperties.IsInAccessibleTree="true"
        AutomationProperties.HelpText="Tap to toggle the activity indicator" />
```

Como alternativa, ela pode ser definida em C# da seguinte maneira:

```csharp
var button = new Button { Text = "Toggle ActivityIndicator" };
AutomationProperties.SetIsInAccessibleTree(button, true);
AutomationProperties.SetHelpText(button, "Tap to toggle the activity indicator");
```

> [!NOTE]
> Observe que [`SetValue`](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object)) o método também pode `AutomationProperties.HelpText` ser usado para definir a propriedade anexada –`button.SetValue(AutomationProperties.HelpTextProperty, "Tap to toggle the activity indicator");`

Em algumas plataformas, para controles [`Entry`](xref:Xamarin.Forms.Entry)de `HelpText` edição como um , a propriedade às vezes pode ser omitida e substituída por texto de espaço reservado. Por exemplo, "Insira seu nome aqui" é um bom candidato para a [`Entry.Placeholder`](xref:Xamarin.Forms.InputView.Placeholder) propriedade que coloca o texto no controle antes da entrada real do usuário.

<a name="labeledby" />

## <a name="automationpropertieslabeledby"></a>AutomationProperties.LabeledBy

A propriedade anexada `AutomationProperties.LabeledBy` permite que outro elemento defina informações de acessibilidade sobre o elemento atual. Por exemplo, [`Label`](xref:Xamarin.Forms.Label) um [`Entry`](xref:Xamarin.Forms.Entry) próximo a um pode `Entry` ser usado para descrever o que os representa. Isso pode ser feito no XAML da seguinte maneira:

```xaml
<Label x:Name="label" Text="Enter your name: " />
<Entry AutomationProperties.IsInAccessibleTree="true"
       AutomationProperties.LabeledBy="{x:Reference label}" />
```

Como alternativa, ela pode ser definida em C# da seguinte maneira:

```csharp
var nameLabel = new Label { Text = "Enter your name: " };
var entry = new Entry();
AutomationProperties.SetIsInAccessibleTree(entry, true);
AutomationProperties.SetLabeledBy(entry, nameLabel);
```

> [!NOTE]
> Observe que [`SetValue`](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object)) o método também pode `AutomationProperties.IsInAccessibleTree` ser usado para definir a propriedade anexada –`entry.SetValue(AutomationProperties.LabeledByProperty, nameLabel);`

## <a name="accessibility-intricacies"></a>Complexidades de acessibilidade

As seções a seguir descrevem as complexidades da configuração dos valores de acessibilidade em certos controles.

### <a name="navigationpage"></a>NavigationPage

No Android, para definir o texto que os leitores de [`NavigationPage`](xref:Xamarin.Forms.NavigationPage)tela lerão para a seta traseira na barra de ação em um , defina as `AutomationProperties.Name` propriedades em `AutomationProperties.HelpText` um [`Page`](xref:Xamarin.Forms.Page). No entanto, observe que isso não terá um efeito em botões voltar de sistema operacional.

### <a name="masterdetailpage"></a>MasterDetailPage

No iOS e na Universal Windows Platform (UWP), para definir o texto que [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage)os leitores de tela lerão para o botão de alternar em um `AutomationProperties.Name`, seja definir as propriedades e `AutomationProperties.HelpText` propriedades na `MasterDetailPage`, ou na `IconImageSource` propriedade da `Master` página.

No Android, para definir o texto que os leitores [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage)de tela lerão para o botão de alternar em um , adicione recursos de string ao projeto Android:

```xml
<resources>
    <string name="app_name">Xamarin Forms Control Gallery</string>
    <string name="btnMDPAutomationID_open">Open Side Menu message</string>
    <string name="btnMDPAutomationID_close">Close Side Menu message</string>
</resources>
```

Em seguida, defina a propriedade `AutomationId` da propriedade `IconImageSource` da página `Master` na cadeia de caracteres apropriada:

```csharp
var master = new ContentPage { ... };
master.IconImageSource.AutomationId = "btnMDPAutomationID";
```

### <a name="toolbaritem"></a>ToolbarItem

No iOS, Android e UWP, os `Text` leitores [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) de tela `AutomationProperties.Name` lerão o valor da propriedade das instâncias, desde que ou `AutomationProperties.HelpText` valores não sejam definidos.

No iOS e UWP, o valor da propriedade `AutomationProperties.Name` substituirá o valor da propriedade `Text` que é lido pelo leitor de tela.

No Android, os valores de propriedade `AutomationProperties.Name` e/ou `AutomationProperties.HelpText` substituirão completamente o valor da propriedade `Text` que está visível e é lida pelo leitor de tela. Observe que essa é uma limitação de APIs menores que 26.

## <a name="related-links"></a>Links relacionados

- [Propriedades anexadas](~/xamarin-forms/xaml/attached-properties.md)
- [Acessibilidade (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-accessibility)
