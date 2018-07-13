---
title: Definindo valores de acessibilidade em elementos de Interface do usuário
description: Este artigo explica como usar a classe AutomationProperties, para que um leitor de tela pode falar sobre os elementos na página.
ms.prod: xamarin
ms.assetid: c0bb6893-fd26-47e7-88e5-3c333c9f786c
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2017
ms.openlocfilehash: 576fe34b0536c83825aa39bdd7111143d9474225
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998902"
---
# <a name="setting-accessibility-values-on-user-interface-elements"></a>Definindo valores de acessibilidade em elementos de Interface do usuário

_Xamarin. Forms permite que os valores de acessibilidade a serem definidos nos elementos da interface do usuário por meio de propriedades anexadas da classe AutomationProperties, que por sua vez, definir valores de acessibilidade nativo. Este artigo explica como usar a classe AutomationProperties, para que um leitor de tela pode falar sobre os elementos na página._

## <a name="overview"></a>Visão geral

Xamarin. Forms permite que os valores de acessibilidade a serem definidos nos elementos de interface do usuário por meio das propriedades anexadas a seguir:

- `AutomationProperties.IsInAccessibleTree` – Indica se o elemento está disponível para um aplicativo acessível. Para obter mais informações, consulte [AutomationProperties.IsInAccessibleTree](#isinaccessibletree).
- `AutomationProperties.Name` – uma breve descrição do elemento que serve como um identificador speakable para o elemento. Para obter mais informações, consulte [AutomationProperties.Name](#name).
- `AutomationProperties.HelpText` – uma descrição mais detalhada sobre o elemento, que pode ser pensada como texto de dica de ferramenta associado ao elemento. Para obter mais informações, consulte [AutomationProperties.HelpText](#helptext).
- `AutomationProperties.LabeledBy` – permite que outro elemento definir as informações de acessibilidade para o elemento atual. Para obter mais informações, consulte [AutomationProperties.LabeledBy](#labeledby).

Eles anexado valores nativos de acessibilidade do conjunto de propriedades, de modo que um leitor de tela pode falar sobre o elemento. Para obter mais informações sobre propriedades anexadas, consulte [propriedades anexadas](~/xamarin-forms/xaml/attached-properties.md).

> [!IMPORTANT]
> Usando o `AutomationProperties` propriedades anexadas podem afetar a execução de teste de interface do usuário no Android. O [ `AutomationId` ](xref:Xamarin.Forms.Element.AutomationId), `AutomationProperties.Name` e `AutomationProperties.HelpText` duas propriedades serão definidas nativo `ContentDescription` propriedade, com o `AutomationProperties.Name` e `AutomationProperties.HelpText` valores de propriedade tendo precedência sobre o `AutomationId`valor (se ambos `AutomationProperties.Name` e `AutomationProperties.HelpText` forem definidos, os valores serão concatenados). Isso significa que todos os testes procurando `AutomationId` falhará se `AutomationProperties.Name` ou `AutomationProperties.HelpText` também são definidos no elemento. Nesse cenário, os testes de interface do usuário deve ser alterados para procurar o valor de `AutomationProperties.Name` ou `AutomationProperties.HelpText`, ou uma concatenação dos dois.

Cada plataforma tem um leitor de tela diferentes para narrar os valores de acessibilidade:

- iOS tem VoiceOver. Para obter mais informações, consulte [acessibilidade de teste em seu dispositivo com VoiceOver](https://developer.apple.com/library/content/technotes/TestingAccessibilityOfiOSApps/TestAccessibilityonYourDevicewithVoiceOver/TestAccessibilityonYourDevicewithVoiceOver.html) no developer.apple.com.
- O Android tem TalkBack. Para obter mais informações, consulte [acessibilidade Testando seu aplicativo](https://developer.android.com/training/accessibility/testing.html#talkback) em developer.android.com.
- Windows tem o Narrator. Para obter mais informações, consulte [verificar os cenários de aplicativo principal, usando o Narrator](/windows/uwp/accessibility/accessibility-testing#verify-main-app-scenarios-by-using-narrator/).

No entanto, o comportamento exato do leitor de tela depende do software e a configuração do usuário dele. Por exemplo, a maioria dos leitores de tela ler o texto associado a um controle quando ele recebe o foco, permitindo que os usuários para orientá-los quando eles passam entre os controles na página. Alguns leitores de tela leia também a interface do usuário todo o aplicativo quando uma página for exibida, que permite que o usuário receba todo o conteúdo de informação disponível da página antes de tentar navegar por ele.

Leitores de tela também ler valores de acessibilidade diferente. No aplicativo de exemplo:

- VoiceOver lerá os `Placeholder` valor da `Entry`, seguida por instruções sobre como usar o controle.
- TalkBack lerá os `Placeholder` valor da `Entry`, seguido pelo `AutomationProperties.HelpText` valor, seguido por instruções sobre como usar o controle.
- O Narrator lerá o `AutomationProperties.LabeledBy` valor da `Entry`, seguida por instruções sobre como usar o controle.

Além disso, o Narrador priorizará `AutomationProperties.Name`, `AutomationProperties.LabeledBy`e, em seguida, `AutomationProperties.HelpText`. No Android, TalkBack pode combinar as `AutomationProperties.Name` e `AutomationProperties.HelpText` valores. Portanto, é recomendável que o teste de acessibilidade completa é executada em cada plataforma para garantir uma experiência ideal.

<a name="isinaccessibletree" />

## <a name="automationpropertiesisinaccessibletree"></a>AutomationProperties.IsInAccessibleTree

O `AutomationProperties.IsInAccessibleTree` propriedade anexada é um `boolean` que determina se o elemento é acessível e, portanto, visíveis para os leitores de tela. Ele deve ser definido como `true` usar outros recursos de acessibilidade propriedades anexadas. Isso pode ser feito no XAML da seguinte maneira:

```xaml
<Entry AutomationProperties.IsInAccessibleTree="true" />
```

Como alternativa, ela pode ser definida em c# da seguinte maneira:

```csharp
var entry = new Entry();
AutomationProperties.SetIsInAccessibleTree(entry, true);
```

> [!NOTE]
> Observe que o [ `SetValue` ](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object)) método também pode ser usado para definir o `AutomationProperties.IsInAccessibleTree` propriedade – anexada `entry.SetValue(AutomationProperties.IsInAccessibleTreeProperty, true);`

<a name="name" />

## <a name="automationpropertiesname"></a>AutomationProperties.Name

O `AutomationProperties.Name` valor da propriedade anexada deve ser uma cadeia de caracteres de texto curto e descritivo que usa de um leitor de tela para anunciar um elemento. Essa propriedade deve ser definida para elementos que têm um significado que é importante para entender o conteúdo ou interagir com a interface do usuário. Isso pode ser feito no XAML da seguinte maneira:

```xaml
<ActivityIndicator AutomationProperties.IsInAccessibleTree="true"
                   AutomationProperties.Name="Progress indicator" />
```

Como alternativa, ela pode ser definida em c# da seguinte maneira:

```csharp
var activityIndicator = new ActivityIndicator();
AutomationProperties.SetIsInAccessibleTree(activityIndicator, true);
AutomationProperties.SetName(activityIndicator, "Progress indicator");
```

> [!NOTE]
> Observe que o [ `SetValue` ](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object)) método também pode ser usado para definir o `AutomationProperties.Name` propriedade – anexada `activityIndicator.SetValue(AutomationProperties.NameProperty, "Progress indicator");`

<a name="helptext" />

## <a name="automationpropertieshelptext"></a>AutomationProperties.HelpText

O `AutomationProperties.HelpText` anexados a propriedade deve ser definida como o texto que descreve o elemento de interface do usuário e pode ser a ideia de como o texto de dica de ferramenta associada ao elemento. Isso pode ser feito no XAML da seguinte maneira:

```xaml
<Button Text="Toggle ActivityIndicator"
        AutomationProperties.IsInAccessibleTree="true"
        AutomationProperties.HelpText="Tap to toggle the activity indicator" />
```

Como alternativa, ela pode ser definida em c# da seguinte maneira:

```csharp
var button = new Button { Text = "Toggle ActivityIndicator" };
AutomationProperties.SetIsInAccessibleTree(button, true);
AutomationProperties.SetHelpText(button, "Tap to toggle the activity indicator");
```

> [!NOTE]
> Observe que o [ `SetValue` ](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object)) método também pode ser usado para definir o `AutomationProperties.HelpText` propriedade – anexada `button.SetValue(AutomationProperties.HelpTextProperty, "Tap to toggle the activity indicator");`

Em algumas plataformas, para editar controles, como um [ `Entry` ](xref:Xamarin.Forms.Entry), o `HelpText` propriedade às vezes, pode ser omitida e substituída pelo texto de espaço reservado. Por exemplo, "digite seu nome aqui" é um bom candidato para o [ `Entry.Placeholder` ](xref:Xamarin.Forms.Entry.Placeholder) propriedade que coloca o texto no controle antes da entrada do usuário real.

<a name="labeledby" />

## <a name="automationpropertieslabeledby"></a>AutomationProperties.LabeledBy

O `AutomationProperties.LabeledBy` propriedade anexada permite que outro elemento definir as informações de acessibilidade para o elemento atual. Por exemplo, uma [ `Label` ](xref:Xamarin.Forms.Label) próximo a um [ `Entry` ](xref:Xamarin.Forms.Entry) pode ser usado para descrever o que o `Entry` representa. Isso pode ser feito no XAML da seguinte maneira:

```xaml
<Label x:Name="label" Text="Enter your name: " />
<Entry AutomationProperties.IsInAccessibleTree="true"
       AutomationProperties.LabeledBy="{x:Reference label}" />
```

Como alternativa, ela pode ser definida em c# da seguinte maneira:

```csharp
var nameLabel = new Label { Text = "Enter your name: " };
var entry = new Entry();
AutomationProperties.SetIsInAccessibleTree(entry, true);
AutomationProperties.SetLabeledBy(entry, nameLabel);
```

> [!NOTE]
> Observe que o [ `SetValue` ](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object)) método também pode ser usado para definir o `AutomationProperties.IsInAccessibleTree` propriedade – anexada `entry.SetValue(AutomationProperties.LabeledByProperty, nameLabel);`

## <a name="summary"></a>Resumo

Este artigo explica como definir valores de acessibilidade em usuário de elementos de interface em um aplicativo xamarin. Forms usando propriedades anexadas do `AutomationProperties` classe. Eles anexado valores nativos de acessibilidade do conjunto de propriedades, de modo que um leitor de tela pode falar sobre os elementos na página.


## <a name="related-links"></a>Links relacionados

- [Propriedades anexadas](~/xamarin-forms/xaml/attached-properties.md)
- [Acessibilidade (amostra)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Accessibility/)
