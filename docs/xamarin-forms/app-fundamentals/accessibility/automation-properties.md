---
title: Propriedades de automação
description: Este artigo explica como usar a classe AutomationProperties em um Xamarin.Forms aplicativo, para que um leitor de tela possa falar sobre os elementos na página.
ms.prod: xamarin
ms.assetid: c0bb6893-fd26-47e7-88e5-3c333c9f786c
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/18/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f05ca6eaaaedea69e8b6eb951d31fb7701a2f0de
ms.sourcegitcommit: 836d54779190b1bef1b43bc0c2016c9b3034bfda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93281177"
---
# <a name="automation-properties-in-no-locxamarinforms"></a>Propriedades de automação em Xamarin.Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-accessibility)

_Xamarin.Forms permite que os valores de acessibilidade sejam definidos nos elementos da interface do usuário usando propriedades anexadas da classe AutomationProperties, que, por sua vez, define valores de acessibilidade nativos. Este artigo explica como usar a classe AutomationProperties para que um leitor de tela possa falar sobre os elementos na página._

Xamarin.Forms permite que as propriedades de automação sejam definidas nos elementos da interface do usuário por meio das seguintes propriedades anexadas:

- `AutomationProperties.IsInAccessibleTree` – indica se o elemento está disponível para um aplicativo acessível. Para obter mais informações, confira [AutomationProperties.IsInAccessibleTree](#automationpropertiesisinaccessibletree).
- `AutomationProperties.Name` – uma breve descrição do elemento que serve como um identificador falado para o elemento. Para obter mais informações, confira [AutomationProperties.Name](#automationpropertiesname).
- `AutomationProperties.HelpText` – uma descrição mais detalhada do elemento, que pode ser considerada como o texto de dica de ferramenta associado ao elemento. Para obter mais informações, confira [AutomationProperties.HelpText](#automationpropertieshelptext).
- `AutomationProperties.LabeledBy` – permite que outro elemento defina informações de acessibilidade sobre o elemento atual. Para obter mais informações, confira [AutomationProperties.LabeledBy](#automationpropertieslabeledby).

Essas propriedades anexadas definem valores de acessibilidade nativos, de modo que um leitor de tela pode falar sobre o elemento. Para obter mais informações sobre as propriedades anexadas, confira [Propriedades anexadas](~/xamarin-forms/xaml/attached-properties.md).

> [!IMPORTANT]
> Usar as propriedades anexadas `AutomationProperties` pode afetar a execução de testes de interface do usuário no Android. As [`AutomationId`](xref:Xamarin.Forms.Element.AutomationId) `AutomationProperties.Name` Propriedades, e `AutomationProperties.HelpText` definirão a propriedade nativa `ContentDescription` , com os valores de `AutomationProperties.Name` propriedade e com `AutomationProperties.HelpText` precedência sobre o `AutomationId` valor (se ambos `AutomationProperties.Name` e `AutomationProperties.HelpText` estiverem definidos, os valores serão concatenados). Isso significa que testes que procurarem `AutomationId` falharão se `AutomationProperties.Name` ou `AutomationProperties.HelpText` também estiverem definidos no elemento. Nesse cenário, testes da interface do usuário deverão ser alterados para procurar pelo valor de `AutomationProperties.Name` ou `AutomationProperties.HelpText`, ou por uma concatenação dos dois.

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
> Observe que o [ `SetValue` ] (xref: Xamarin.Forms . Acopláble. SetValue ( Xamarin.Forms . O método vinculproperty, System. Object)) também pode ser usado para definir a `AutomationProperties.IsInAccessibleTree` Propriedade anexada – `entry.SetValue(AutomationProperties.IsInAccessibleTreeProperty, true);`

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
> Observe que o [ `SetValue` ] (xref: Xamarin.Forms . Acopláble. SetValue ( Xamarin.Forms . O método vinculproperty, System. Object)) também pode ser usado para definir a `AutomationProperties.Name` Propriedade anexada – `activityIndicator.SetValue(AutomationProperties.NameProperty, "Progress indicator");`

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
> Observe que o [ `SetValue` ] (xref: Xamarin.Forms . Acopláble. SetValue ( Xamarin.Forms . O método vinculproperty, System. Object)) também pode ser usado para definir a `AutomationProperties.HelpText` Propriedade anexada – `button.SetValue(AutomationProperties.HelpTextProperty, "Tap to toggle the activity indicator");`

Em algumas plataformas, para controles de edição, como um [`Entry`](xref:Xamarin.Forms.Entry) , a `HelpText` propriedade pode, às vezes, ser omitida e substituída pelo texto de espaço reservado. Por exemplo, "Insira seu nome aqui" é um bom candidato para a [`Entry.Placeholder`](xref:Xamarin.Forms.InputView.Placeholder) propriedade que coloca o texto no controle antes da entrada real do usuário.

## <a name="automationpropertieslabeledby"></a>AutomationProperties.LabeledBy

A propriedade anexada `AutomationProperties.LabeledBy` permite que outro elemento defina informações de acessibilidade sobre o elemento atual. Por exemplo, um [`Label`](xref:Xamarin.Forms.Label) ao lado de um [`Entry`](xref:Xamarin.Forms.Entry) pode ser usado para descrever o que `Entry` representa. Isso pode ser feito no XAML da seguinte maneira:

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

> [!IMPORTANT]
> O `AutomationProperties.LabeledByProperty` ainda não tem suporte no Ios.

> [!NOTE]
> Observe que o [ `SetValue` ] (xref: Xamarin.Forms . Acopláble. SetValue ( Xamarin.Forms . O método vinculproperty, System. Object)) também pode ser usado para definir a `AutomationProperties.IsInAccessibleTree` Propriedade anexada – `entry.SetValue(AutomationProperties.LabeledByProperty, nameLabel);`

## <a name="accessibility-intricacies"></a>Complexidades de acessibilidade

As seções a seguir descrevem as complexidades da configuração dos valores de acessibilidade em certos controles.

### <a name="navigationpage"></a>NavigationPage

No Android, para definir o texto que os leitores de tela lerám para a seta voltar na barra de ação em um [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) , defina as `AutomationProperties.Name` `AutomationProperties.HelpText` Propriedades e em um [`Page`](xref:Xamarin.Forms.Page) . No entanto, observe que isso não terá um efeito em botões voltar de sistema operacional.

### <a name="masterdetailpage"></a>MasterDetailPage

No iOS e no Plataforma Universal do Windows (UWP), para definir o texto que os leitores de tela lerá para o botão de alternância em um [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) , defina as `AutomationProperties.Name` `AutomationProperties.HelpText` Propriedades e no `MasterDetailPage` ou na `IconImageSource` propriedade da `Master` página.

No Android, para definir o texto que os leitores de tela lerám para o botão de alternância em um [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) , adicione recursos de cadeia de caracteres ao projeto do Android:

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

No iOS, Android e UWP, os leitores de tela lerám o `Text` valor da propriedade de [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) instâncias, desde que os `AutomationProperties.Name` `AutomationProperties.HelpText` valores ou não sejam definidos.

No iOS e UWP, o valor da propriedade `AutomationProperties.Name` substituirá o valor da propriedade `Text` que é lido pelo leitor de tela.

No Android, os valores de propriedade `AutomationProperties.Name` e/ou `AutomationProperties.HelpText` substituirão completamente o valor da propriedade `Text` que está visível e é lida pelo leitor de tela. Observe que essa é uma limitação de APIs menores que 26.

## <a name="related-links"></a>Links Relacionados

- [Propriedades Anexadas](~/xamarin-forms/xaml/attached-properties.md)
- [Acessibilidade (amostra)](/samples/xamarin/xamarin-forms-samples/userinterface-accessibility)