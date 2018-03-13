---
title: "Resumo do capítulo 23. Gatilhos e comportamentos"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 19E84B5D-46B4-4B6D-A255-87BEFB011261
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 9a53cddcf216efd2bb86c838e280d599ff26c191
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2018
---
# <a name="summary-of-chapter-23-triggers-and-behaviors"></a>Resumo do capítulo 23. Gatilhos e comportamentos

Gatilhos e comportamentos são semelhantes, em que eles são destinados a ser usado nos arquivos XAML para simplificar as interações do elemento além do uso de associações de dados e para estender a funcionalidade de elementos XAML. Gatilhos e comportamentos quase sempre são usados com objetos de interface do usuário visual.

Para dar suporte a gatilhos e comportamentos, ambos `VisualElement` e `Style` oferece suporte a duas propriedades de coleção:

- [`VisualElement.Triggers`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Triggers/) e [ `Style.Triggers` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.Triggers/) do tipo `IList<TriggerBase>`
- [`VisualElement.Behaviors`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Behaviors/) e [ `Style.Behaviors` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.Behaviors/) do tipo `IList<Behavior>`

## <a name="triggers"></a>Gatilhos

Um gatilho é uma condição (uma alteração de propriedade ou o acionamento de um evento) que resulta em uma resposta (outra alteração de propriedade ou executando algum código). O `Triggers` propriedade `VisualElement` e `Style` é do tipo `IList<TriggersBase>`. [`TriggerBase`](https://developer.xamarin.com/api/type/Xamarin.Forms.TriggerBase/) é uma classe abstrata da qual derivam quatro classes lacradas:

- [`Trigger`](https://developer.xamarin.com/api/type/Xamarin.Forms.Trigger/) para respostas com base nas alterações de propriedade
- [`EventTrigger`](https://developer.xamarin.com/api/type/Xamarin.Forms.EventTrigger/) para respostas com base em acionamentos de evento
- [`DataTrigger`](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTrigger/) para respostas com base em associações de dados
- [`MultiTrigger`](https://developer.xamarin.com/api/type/Xamarin.Forms.MultiTrigger/) para respostas com base em vários gatilhos

O gatilho é sempre definido no elemento cuja propriedade está sendo alterada pelo disparador.

### <a name="the-simplest-trigger"></a>O gatilho mais simples

O [ `Trigger` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Trigger/) verifica uma alteração de um valor de propriedade de classe e responde definindo outra propriedade do mesmo elemento.

`Trigger` define três propriedades:

- [`Property`](https://developer.xamarin.com/api/property/Xamarin.Forms.Trigger.Property/) do tipo `BindableProperty`
- [`Value`](https://developer.xamarin.com/api/property/Xamarin.Forms.Trigger.Value/) do tipo `Object`
- [`Setters`](https://developer.xamarin.com/api/property/Xamarin.Forms.Trigger.Setters/) tipo `IList<SetterBase>`, a propriedade de conteúdo de `Trigger`

Além disso, `Trigger` requer que a seguinte propriedade herdada do `TriggerBase` ser definido:

- [`TargetType`](https://developer.xamarin.com/api/property/Xamarin.Forms.TriggerBase.TargetType/) para indicar o tipo do elemento no qual o `Trigger` está anexado

O `Property` e `Value` compõem a condição e o `Setters` coleção é a resposta. Quando o indicado `Property` tem o valor indicado pelo `Value`, em seguida, o `Setter` objetos no `Setters` coleção são aplicadas. Quando o `Property` tem um valor diferente, os setters são removidos. `Setter` define duas propriedades que são os mesmos que as primeiras duas propriedades de `Trigger`:

- [`Property`](https://developer.xamarin.com/api/property/Xamarin.Forms.Setter.Property/) do tipo `BindableProperty`
- [`Value`](https://developer.xamarin.com/api/property/Xamarin.Forms.Setter.Value/) do tipo `Object`

O [ **EntryPop** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EntryPop) demonstra como um `Trigger` aplicado a um `Entry` pode aumentar o tamanho do `Entry` via o `Scale` propriedade quando o `IsFocused`propriedade o `Entry` é `true`.

Embora não seja comum, o `Trigger` pode ser definida no código, como o [ **EntryPopCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EntryPopCode) demonstra.

O [ **StyledTriggers** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/StyledTriggers) demonstra como o `Trigger` pode ser definido em um `Style` para aplicar a vários `Entry` elementos.

### <a name="trigger-actions-and-animations"></a>Ações de gatilho e animações

Também é possível executar um pouco de código com base em um disparador. Esse código pode ser uma animação que tem como alvo uma propriedade. Uma maneira comum é usar um [ `EventTrigger` ](https://developer.xamarin.com/api/type/Xamarin.Forms.EventTrigger/), que define duas propriedades:

- [`Event`](https://developer.xamarin.com/api/property/Xamarin.Forms.EventTrigger.Event/) tipo `string`, o nome de um evento
- [`Actions`](https://developer.xamarin.com/api/property/Xamarin.Forms.EventTrigger.Actions/) tipo `IList<TriggerAction>`, uma lista de ações a serem executadas em resposta.

Para usá-la, você precisa escrever uma classe que deriva de [ `TriggerAction<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TriggerAction%3CT%3E/), em geral, `TriggerAction<VisualElement>`. Você pode definir as propriedades dessa classe. Essas são propriedades CLR simples em vez de propriedades vinculáveis porque `TriggerAction` não deriva de `BindableObject`. Você deve substituir o [ `Invoke` ](https://developer.xamarin.com/api/member/Xamarin.Forms.TriggerAction%3CT%3E.Invoke/p/T/) método é chamado quando a ação é invocada. O argumento é o elemento de destino.

O [ `ScaleAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ScaleAction.cs) classe no [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca é um exemplo. Ele chama o `ScaleTo` propriedade para animar a `Scale` propriedade de um elemento. Porque uma de suas propriedades é do tipo `Easing`, o [ `EasingConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/EasingConverter.cs) classe permite que você use o padrão de `Easing` campos estáticos em XAML.

O [ **EntrySwell** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EntrySwell) demonstra como invocar o `ScaleAction` de `EventTrigger` objetos que monitora o `Focused` e `Unfocused` eventos.

O [ **CustomEasingSwell** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/CustomEasingSwell) exemplo mostra como definir uma função personalizada de atenuação para `ScaleAction` em um arquivo code-behind.

Você também pode chamar ações usando um `Trigger` (distinguished de `EventTrigger`). Isso exige que você esteja ciente de que `TriggerBase` define duas coleções:

- [`EnterActions`](https://developer.xamarin.com/api/property/Xamarin.Forms.TriggerBase.EnterActions/) do tipo `IList<TriggerAction>`
- [`ExitActions`](https://developer.xamarin.com/api/property/Xamarin.Forms.TriggerBase.ExitActions/) do tipo `IList<TriggerAction>`

O [ **EnterExitSwell** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EnterExitSwell) demonstra como usar essas coleções.

### <a name="more-event-triggers"></a>Disparadores de evento mais

O [ `ScaleUpAndDownAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ScaleUpAndDownAction.cs) classe no [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) chamadas para a biblioteca `ScaleTo` duas vezes para aumentar e diminuir. O [ **ButtonGrowth** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ButtonGrowth) exemplo usa isso em um estilo `EventTrigger` para fornecer comentários visuais quando um `Button` é pressionado. Esta animação double também é possível usar duas ações na coleção de tipo [`DelayedScaleAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/DelayedScaleAction.cs)

O [ `ShiverAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ShiverAction.cs) classe no **Xamarin.FormsBook.Toolkit** biblioteca define uma ação de shiver personalizável. O [ **ShiverButtonDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ShiverButtonDemo) demonstra a ele.

O [ `NumericValidationAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NumericValidationAction.cs) classe no **Xamarin.FormsBook.Toolkit** biblioteca é restrita a `Entry` elementos e conjuntos a `TextColor` propriedade vermelho se o `Text` propriedade não é um `double`. O [ **TriggerEntryValidation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/TriggerEntryValidation) demonstra a ele.

### <a name="data-triggers"></a>Gatilhos de dados

O [ `DataTrigger` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTrigger/) é semelhante de `Trigger` exceto que, em vez de uma propriedade para alterações de valor de monitoramento, ele monitora uma associação de dados. Isso permite que uma propriedade em um elemento para afetar uma propriedade em outro elemento.

`DataTrigger` define três propriedades:

- [`Binding`](https://developer.xamarin.com/api/property/Xamarin.Forms.DataTrigger.Binding/) do tipo `BindingBase`
- [`Value`](https://developer.xamarin.com/api/property/Xamarin.Forms.DataTrigger.Value/) do tipo `Object`
- [`Setters`](https://developer.xamarin.com/api/property/Xamarin.Forms.DataTrigger.Setters/) do tipo `IList<SetterBase>`

O [ **GenderColors** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/GenderColors) exemplo requer o [ **SchoolOfFineArt** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/SchoolOfFineArt) biblioteca e define as cores dos nomes dos alunos como azul ou rosa baseia o `Sex` propriedade:

[![Tripla captura de tela de cores de gênero](images/ch23fg04-small.png "cores sexo")](images/ch23fg04-large.png#lightbox "cores sexo")

O [ **ButtonEnabler** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ButtonEnabler) conjuntos de exemplo o `IsEnabled` propriedade de um `Entry` para `False` se o `Length` propriedade o `Text` propriedade o `Entry`é igual a 0. Observe que o `Text` propriedade é inicializada para uma cadeia de caracteres vazia; por padrão é `null`e o `DataTrigger` não funcione corretamente.

### <a name="combining-conditions-in-the-multitrigger"></a>Combinar condições no MultiTrigger

O [ `MultiTrigger` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MultiTrigger/) é uma coleção de condições. Quando eles são todos os `true`, em seguida, são aplicadas setters. A classe define duas propriedades:

- [`Conditions`](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiTrigger.Conditions/) do tipo `IList<Condition>`
- [`Setters`](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiTrigger.Setters/) do tipo `IList<Setter>`

[`Condition`](https://developer.xamarin.com/api/type/Xamarin.Forms.Condition/) é uma classe abstrata e tem duas classes descendentes:

- [`PropertyCondition`](https://developer.xamarin.com/api/type/Xamarin.Forms.Condition/), que tem [ `Property` ](https://developer.xamarin.com/api/property/Xamarin.Forms.PropertyCondition.Property/) e [ `Value` ](https://developer.xamarin.com/api/property/Xamarin.Forms.PropertyCondition.Value/) propriedades como `Trigger`
- [`BindingCondition`](https://developer.xamarin.com/api/type/Xamarin.Forms.BindingCondition/), que tem [ `Binding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindingCondition.Binding/) e [ `Value` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindingCondition.Value/) propriedades como `DataTrigger`

No [ **ECONDIÇÕES** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/AndConditions) exemplo, um `BoxView` é colorido somente quando quatro `Switch` elementos estão ativados.

O [ **OrConditions** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/OrConditions) demonstra como você pode fazer uma `BoxView` uma cor quando *qualquer* de quatro `Switch` elementos estão ativados. Isso requer que um aplicativo da lei De Morgan e reverter toda a lógica.

Combinando e e ou lógica não é tão fácil e geralmente requer invisível `Switch` elementos para resultados intermediários. O [ **XorConditions** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/XorConditions) demonstra como um `Button` pode ser habilitada se qualquer um dos dois `Entry` elementos têm algum texto digitado, mas não se ambos têm algum texto digitado na.

## <a name="behaviors"></a>Comportamentos

Tudo que você pode fazer com um gatilho, você também pode fazer com um comportamento, mas comportamentos sempre exigem uma classe que deriva de [ `Behavior<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior%3CT%3E/) e substitui os dois métodos a seguir:

- [`OnAttachedTo`](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnAttachedTo/p/T/)
- [`OnDetachingFrom`](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnDetachingFrom/p/T/)

O argumento é o elemento que o comportamento está associado. Em geral, o `OnAttachedTo` método anexa alguns manipuladores de eventos e `OnDetachingFrom` desanexa-los. Como essa classe geralmente salva algum estado, ele geralmente não pode ser compartilhado em um `Style`.

[**BehaviorEntryValidation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/BehaviorEntryValidation) exemplo é semelhante ao **TriggerEntryValidation** exceto que ele usa um comportamento & #x 2014; o [ `NumericValidationBehavior` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NumericValidationBehavior.cs) classe o [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca.

### <a name="behaviors-with-properties"></a>Comportamentos de propriedades

`Behavior<T>` deriva `Behavior`, que é derivada de `BindableObject`, portanto, as propriedades vinculáveis podem ser definidas em um comportamento. Essas propriedades podem estar ativas em associações de dados.

Isso é demonstrado no [ **EmailValidationDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EmailValidationDemo) programa que usa do [ `ValidEmailBehavior` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ValidEmailBehavior.cs) classe no [  **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca. `ValidEmailBehavior` tem uma propriedade ligável somente leitura e serve como uma fonte em associações de dados.

O [ **EmailValidationConv** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EmailValidationConv) exemplo usa esse mesmo comportamento para exibir outro tipo de indicador para sinalizar que um endereço de email é válido.

O [ **EmailValidationTrigger** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EmailValidationTrigger) exemplo é uma variação do exemplo anterior. ButtonGlide usa um `DataTrigger` em combinação com esse comportamento.

### <a name="toggles-and-check-boxes"></a>Alterna e caixas de seleção

É possível encapsular o comportamento de um botão de alternância em uma classe, como [ `ToggleBehavior` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ToggleBehavior.cs) no [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca e, em seguida, definir todos os os elementos visuais para alternância inteiramente em XAML.

O [ **ToggleLabel** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ToggleLabel) usa o `ToggleBehavior` com um `DataTrigger` para usar um `Label` com duas cadeias de caracteres de texto para a alternância.

O [ **FormattedTextToggle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/FormattedTextToggle) exemplo expande esse conceito, alternando entre dois `FormattedString` objetos.

O [ `ToggleBase` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ToggleBase.cs) classe no **Xamarin.FormsBook.Toolkit** biblioteca deriva `ContentView`, define um `IsToggled` propriedade e incorpora um `ToggleBehavior` para alternância lógica. Isso torna mais fácil definir o botão de alternância em XAML, conforme demonstrado a [ **TranditionalCheckBox** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/TraditionalCheckBox) exemplo.

O [ **SwitchCloneDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/SwitchCloneDemo) inclui um [ `SwitchClone` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter23/SwitchCloneDemo/SwitchCloneDemo/SwitchCloneDemo/SwitchClone.cs) classe que deriva de `ToggleBase` e usa um [ `TranslateAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/TranslateAction.cs)classe para criar um botão de alternância que se parece com o xamarin. Forms `Switch`.

Um [ `RotateAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/RotateAction.cs) no **Xamarin.FormsBook.Toolkit** fornece uma animação usada para fazer uma alavanca animada no [ **LeverToggle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/LeverToggle)exemplo.

### <a name="responding-to-taps"></a>Respondendo a toques

Uma desvantagem de `EventTrigger` é que não é possível anexá-lo para um `TapGestureRecognizer` para responder a toques. Esse problema é a finalidade de [ `TapBehavior` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/TapBehavior.cs) no [ **Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)

O [ **BoxViewTapShiver** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/BoxViewTapShiver) usa `TapBehavior` para usar o anteriores `ShiverAction` para tocado `BoxView` elementos.

O [ **ShiverViews** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ShiverViews) exemplo mostra como a reduzir a marcação, encapsulando uma `ShiverView` classe.

### <a name="radio-buttons"></a>Botões de opção

O [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca também tem um [ `RadioBehavior` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/RadioBehavior.cs) classe para tornar os botões de opção são agrupados por um `string` nome do grupo.

O [ **RadioLabels** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/RadioLabels) programa usa cadeias de caracteres de texto para o botão de opção. O [ **RadioStyle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/RadioStyle) usa um `Style` à diferença na aparência entre os botões marcados e desmarcados. O [ **RadioImages** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/RadioImages) exemplo usa imagens demarcadas seus botões de opção:

[![Tripla captura de tela de rádio imagens](images/ch23fg17-small.png "imagens de botão de opção")](images/ch23fg17-large.png#lightbox "imagens de botão de opção")

O [ **TraditionalRadios** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/TraditionalRadios) exemplo desenha tradicional botões de opção que aparece com um ponto dentro de um círculo.

### <a name="fades-and-orientation"></a>Desaparecer e orientação

O exemplo final, [ **MultiColorSliders** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/MultiColorSliders) permite que você alterne entre três modos diferentes de seleção de cor usando botões de opção. As três exibições fade in e check-out usando um [ `FadeEnableAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/FadeEnableAction.cs) no [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca.

O programa também responderá às alterações na orientação entre retrato e paisagem usando um [ `GridOrientationBehavior` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/GridOrientationBehavior.cs) no **Xamarin.FormsBook.Toolkit** biblioteca.



## <a name="related-links"></a>Links relacionados

- [23 de capítulo de texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch23-Apr2016.pdf)
- [Exemplos de capítulo 23](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23)
- [Trabalhando com gatilhos](~/xamarin-forms/app-fundamentals/triggers.md)
- [Comportamentos do Xamarin.Forms](~/xamarin-forms/app-fundamentals/behaviors/creating.md)
