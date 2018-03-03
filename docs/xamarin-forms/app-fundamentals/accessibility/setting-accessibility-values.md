---
title: "Configurando valores de acessibilidade em elementos de Interface do usuário"
description: "Xamarin. Forms permite valores de acessibilidade ser definido em elementos de interface do usuário usando propriedades anexadas da classe AutomationProperties, que por sua vez, valores do conjunto de acessibilidade nativo. Este artigo explica como usar a classe AutomationProperties, para que os leitores de tela podem falar sobre os elementos na página."
ms.topic: article
ms.prod: xamarin
ms.assetid: c0bb6893-fd26-47e7-88e5-3c333c9f786c
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2017
ms.openlocfilehash: 4aeeea7f946a121b12741d2da217daf531935849
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2018
---
# <a name="setting-accessibility-values-on-user-interface-elements"></a>Configurando valores de acessibilidade em elementos de Interface do usuário

_Xamarin. Forms permite valores de acessibilidade ser definido em elementos de interface do usuário usando propriedades anexadas da classe AutomationProperties, que por sua vez, valores do conjunto de acessibilidade nativo. Este artigo explica como usar a classe AutomationProperties, para que os leitores de tela podem falar sobre os elementos na página._

## <a name="overview"></a>Visão geral

Xamarin. Forms permite valores de acessibilidade ser definida em elementos de interface do usuário por meio das propriedades anexadas a seguir:

- `AutomationProperties.IsInAccessibleTree` – Indica se o elemento está disponível para um aplicativo acessível. Para obter mais informações, consulte [AutomationProperties.IsInAccessibleTree](#isinaccessibletree).
- `AutomationProperties.Name` – uma breve descrição do elemento que serve como um identificador speakable para o elemento. Para obter mais informações, consulte [AutomationProperties.Name](#name).
- `AutomationProperties.HelpText` – uma descrição mais detalhada do elemento, que pode ser pensada como texto de dica de ferramenta associado ao elemento. Para obter mais informações, consulte [AutomationProperties.HelpText](#helptext).
- `AutomationProperties.LabeledBy` – permite que outro elemento definir informações de acessibilidade para o elemento atual. Para obter mais informações, consulte [AutomationProperties.LabeledBy](#labeledby).

Esses anexado acessibilidade nativo valores do conjunto de propriedades para que os leitores de tela podem falar sobre o elemento. Para obter mais informações sobre propriedades anexadas, consulte [propriedades anexadas](~/xamarin-forms/xaml/attached-properties.md).

> [!IMPORTANT]
> Usando o `AutomationProperties` propriedades anexadas podem afetar a execução de teste de interface do usuário no Android. O [ `AutomationId` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.AutomationId/), `AutomationProperties.Name` e `AutomationProperties.HelpText` duas propriedades serão definidas nativo `ContentDescription` propriedade com o `AutomationProperties.Name` e `AutomationProperties.HelpText` valores de propriedade tendo precedência sobre o `AutomationId`valor (se ambos os `AutomationProperties.Name` e `AutomationProperties.HelpText` estão configurados, os valores serão concatenados). Isso significa que quaisquer testes procurando `AutomationId` falhará se `AutomationProperties.Name` ou `AutomationProperties.HelpText` também são definidas no elemento. Nesse cenário, os testes de interface do usuário deve ser alterado para procurar o valor de `AutomationProperties.Name` ou `AutomationProperties.HelpText`, ou uma concatenação de ambos.

Cada plataforma tem um leitor de tela diferentes para narrar os valores de acessibilidade:

- iOS tem narração. Para obter mais informações, consulte [acessibilidade de teste em seu dispositivo com narração](https://developer.apple.com/library/content/technotes/TestingAccessibilityOfiOSApps/TestAccessibilityonYourDevicewithVoiceOver/TestAccessibilityonYourDevicewithVoiceOver.html) em developer.apple.com.
- Android tem o TalkBack. Para obter mais informações, consulte [acessibilidade de testar seu aplicativo](https://developer.android.com/training/accessibility/testing.html#talkback) em developer.android.com.
- O Windows tem o Narrator. Para obter mais informações, consulte [verificar os cenários de aplicativo principal, usando o Narrator](/windows/uwp/accessibility/accessibility-testing#verify-main-app-scenarios-by-using-narrator/).

No entanto, o comportamento exato de leitores de tela depende do software e a configuração do usuário do mesmo. Por exemplo, a maioria dos leitores de tela ler o texto associado a um controle quando ele recebe o foco, permitindo que os usuários para orientá-los conforme se move entre os controles na página. Alguns leitores de tela também ler a interface do usuário de aplicativo inteiro quando uma página é exibida, que permite que o usuário recebe todo o conteúdo de informação disponível da página antes de tentar navegar por ele.

Leitores de tela também ler valores diferentes de acessibilidade. O aplicativo de exemplo:

- Narração lerá o `Placeholder` valor o `Entry`, seguido por instruções sobre como usar o controle.
- Lerá o talkBack o `Placeholder` valor da `Entry`, seguido de `AutomationProperties.HelpText` valor, seguido por instruções sobre como usar o controle.
- O Narrator lerá o `AutomationProperties.LabeledBy` valor o `Entry`, seguido por instruções sobre como usar o controle.

Além disso, o Narrator será priorizar `AutomationProperties.Name`, `AutomationProperties.LabeledBy`e, em seguida, `AutomationProperties.HelpText`. No Android, o TalkBack pode combinar o `AutomationProperties.Name` e `AutomationProperties.HelpText` valores. Portanto, é recomendável que teste acessibilidade completa é executada em cada plataforma para garantir uma experiência ideal.

<a name="isinaccessibletree" />

## <a name="automationpropertiesisinaccessibletree"></a>AutomationProperties.IsInAccessibleTree

O `AutomationProperties.IsInAccessibleTree` propriedade anexada é um `boolean` que determina se o elemento é acessível e, portanto, visíveis aos leitores de tela. Ele deve ser definido como `true` usar a acessibilidade outra propriedades anexadas. Isso pode ser feito em XAML da seguinte maneira:

```xaml
<Entry AutomationProperties.IsInAccessibleTree="true" />
```

Como alternativa, pode ser definido em c# como a seguir:

```csharp
var entry = new Entry();
AutomationProperties.SetIsInAccessibleTree(entry, true);
```

> [!NOTE]
> Observe que o [ `SetValue` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.SetValue/p/Xamarin.Forms.BindableProperty/System.Object/) método também pode ser usado para definir o `AutomationProperties.IsInAccessibleTree` propriedade – anexada `entry.SetValue(AutomationProperties.IsInAccessibleTreeProperty, true);`

<a name="name" />

## <a name="automationpropertiesname"></a>AutomationProperties.Name

O `AutomationProperties.Name` valor da propriedade anexada deve ser uma cadeia de caracteres de texto curto e descritivo que usa um leitor de tela de anunciar um elemento. Essa propriedade deve ser definida para os elementos que têm um significado que é importante para entender o conteúdo ou interagir com a interface do usuário. Isso pode ser feito em XAML da seguinte maneira:

```xaml
<ActivityIndicator AutomationProperties.IsInAccessibleTree="true"
                   AutomationProperties.Name="Progress indicator" />
```

Como alternativa, pode ser definido em c# como a seguir:

```csharp
var activityIndicator = new ActivityIndicator();
AutomationProperties.SetIsInAccessibleTree(activityIndicator, true);
AutomationProperties.SetName(activityIndicator, "Progress indicator");
```

> [!NOTE]
> Observe que o [ `SetValue` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.SetValue/p/Xamarin.Forms.BindableProperty/System.Object/) método também pode ser usado para definir o `AutomationProperties.Name` propriedade – anexada `activityIndicator.SetValue(AutomationProperties.NameProperty, "Progress indicator");`

<a name="helptext" />

## <a name="automationpropertieshelptext"></a>AutomationProperties.HelpText

O `AutomationProperties.HelpText` anexado propriedade deve ser definida para o texto que descreve o elemento de interface do usuário, e pode ser a ideia de como o texto de dica de ferramenta associada ao elemento. Isso pode ser feito em XAML da seguinte maneira:

```xaml
<Button Text="Toggle ActivityIndicator"
        AutomationProperties.IsInAccessibleTree="true"
        AutomationProperties.HelpText="Tap to toggle the activity indicator" />
```

Como alternativa, pode ser definido em c# como a seguir:

```csharp
var button = new Button { Text = "Toggle ActivityIndicator" };
AutomationProperties.SetIsInAccessibleTree(button, true);
AutomationProperties.SetHelpText(button, "Tap to toggle the activity indicator");
```

> [!NOTE]
> Observe que o [ `SetValue` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.SetValue/p/Xamarin.Forms.BindableProperty/System.Object/) método também pode ser usado para definir o `AutomationProperties.HelpText` propriedade – anexada `button.SetValue(AutomationProperties.HelpTextProperty, "Tap to toggle the activity indicator");`

Em algumas plataformas para edição de controles, como um [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/), o `HelpText` propriedade às vezes pode ser omitida e substituirá o texto do espaço reservado. Por exemplo, "digite seu nome aqui" é um bom candidato para o [ `Entry.Placeholder` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.Placeholder/) propriedade que coloca o texto no controle antes da entrada do usuário atual.

<a name="labeledby" />

## <a name="automationpropertieslabeledby"></a>AutomationProperties.LabeledBy

O `AutomationProperties.LabeledBy` propriedade anexada permite que outro elemento definir informações de acessibilidade para o elemento atual. Por exemplo, um [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) lado um [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) pode ser usado para descrever o que o `Entry` representa. Isso pode ser feito em XAML da seguinte maneira:

```xaml
<Label x:Name="label" Text="Enter your name: " />
<Entry AutomationProperties.IsInAccessibleTree="true"
       AutomationProperties.LabeledBy="{x:Reference label}" />
```

Como alternativa, pode ser definido em c# como a seguir:

```csharp
var nameLabel = new Label { Text = "Enter your name: " };
var entry = new Entry();
AutomationProperties.SetIsInAccessibleTree(entry, true);
AutomationProperties.SetLabeledBy(entry, nameLabel);
```

> [!NOTE]
> Observe que o [ `SetValue` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.SetValue/p/Xamarin.Forms.BindableProperty/System.Object/) método também pode ser usado para definir o `AutomationProperties.IsInAccessibleTree` propriedade – anexada `entry.SetValue(AutomationProperties.LabeledByProperty, nameLabel);`

## <a name="summary"></a>Resumo

Este artigo explicou como definir valores de acessibilidade no usuário elementos de interface em um aplicativo xamarin. Forms usando propriedades anexadas do `AutomationProperties` classe. Esses anexado acessibilidade nativo valores do conjunto de propriedades para que os leitores de tela podem falar sobre os elementos na página.


## <a name="related-links"></a>Links relacionados

- [Propriedades anexadas](~/xamarin-forms/xaml/attached-properties.md)
- [Acessibilidade (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Accessibility/)
