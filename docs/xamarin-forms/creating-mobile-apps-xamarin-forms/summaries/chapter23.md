---
title: Resumo do capítulo 23. Gatilhos e comportamentos
description: 'Criando aplicativos móveis com Xamarin.Forms : Resumo do capítulo 23. Gatilhos e comportamentos'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 19E84B5D-46B4-4B6D-A255-87BEFB011261
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: bdbc795c0669115f822e6908590a3754af31a2d0
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93373985"
---
# <a name="summary-of-chapter-23-triggers-and-behaviors"></a>Resumo do capítulo 23. Gatilhos e comportamentos

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23)

> [!NOTE]
> Este livro foi publicado na Primavera de 2016 e não foi atualizado desde então. Há muito no livro que permanece valioso, mas alguns materiais estão desatualizados e alguns tópicos não estão mais totalmente corretos ou completos.

Gatilhos e comportamentos são semelhantes, pois ambos devem ser usados em arquivos XAML para simplificar as interações de elemento além do uso de associações de dados e para estender a funcionalidade de elementos XAML. Os gatilhos e os comportamentos são quase sempre usados com objetos de interface do usuário visual.

Para dar suporte a gatilhos e comportamentos, `VisualElement` e `Style` dar suporte a duas propriedades de coleção:

- [`VisualElement.Triggers`](xref:Xamarin.Forms.VisualElement.Triggers) e [`Style.Triggers`](xref:Xamarin.Forms.Style.Triggers) do tipo `IList<TriggerBase>`
- [`VisualElement.Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) e [`Style.Behaviors`](xref:Xamarin.Forms.Style.Behaviors) do tipo `IList<Behavior>`

## <a name="triggers"></a>Gatilhos

Um gatilho é uma condição (uma alteração de propriedade ou o acionamento de um evento) que resulta em uma resposta (outra alteração de propriedade ou execução de algum código). A `Triggers` propriedade de `VisualElement` e `Style` é do tipo `IList<TriggersBase>` . [`TriggerBase`](xref:Xamarin.Forms.TriggerBase) é uma classe abstrata da qual são derivadas quatro classes lacradas:

- [`Trigger`](xref:Xamarin.Forms.Trigger) para respostas com base nas alterações de propriedade
- [`EventTrigger`](xref:Xamarin.Forms.EventTrigger) para respostas com base nos disparos de eventos
- [`DataTrigger`](xref:Xamarin.Forms.DataTrigger) para respostas baseadas em associações de dados
- [`MultiTrigger`](xref:Xamarin.Forms.MultiTrigger) para respostas com base em vários gatilhos

O gatilho é sempre definido no elemento cuja propriedade está sendo alterada pelo gatilho.

### <a name="the-simplest-trigger"></a>O gatilho mais simples

A [`Trigger`](xref:Xamarin.Forms.Trigger) classe verifica se há uma alteração de um valor de propriedade e responde definindo outra Propriedade do mesmo elemento.

`Trigger` define três propriedades:

- [`Property`](xref:Xamarin.Forms.Trigger.Property) do tipo `BindableProperty`
- [`Value`](xref:Xamarin.Forms.Trigger.Value) do tipo `Object`
- [`Setters`](xref:Xamarin.Forms.Trigger.Setters) do tipo `IList<SetterBase>` , a propriedade de conteúdo de `Trigger`

Além disso, `Trigger` o requer que a seguinte propriedade seja herdada de `TriggerBase` ser definida:

- [`TargetType`](xref:Xamarin.Forms.TriggerBase.TargetType) para indicar o tipo do elemento no qual o `Trigger` está anexado

O `Property` e o `Value` compõem a condição e a `Setters` coleção é a resposta. Quando indicado `Property` tem o valor indicado por `Value` , os `Setter` objetos na `Setters` coleção são aplicados. Quando o `Property` tiver um valor diferente, os setters serão removidos. `Setter` define duas propriedades que são iguais às duas primeiras propriedades de `Trigger` :

- [`Property`](xref:Xamarin.Forms.Setter.Property) do tipo `BindableProperty`
- [`Value`](xref:Xamarin.Forms.Setter.Value) do tipo `Object`

O exemplo [**EntryPop**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EntryPop) demonstra como um `Trigger` aplicado a um `Entry` pode aumentar o tamanho do `Entry` por meio da `Scale` propriedade quando a `IsFocused` Propriedade do `Entry` é `true` .

Embora não seja comum, o `Trigger` pode ser definido no código, como demonstra o exemplo de [**EntryPopCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EntryPopCode) .

O exemplo [**StyledTriggers**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/StyledTriggers) demonstra como o `Trigger` pode ser definido em um `Style` para aplicar a vários `Entry` elementos.

### <a name="trigger-actions-and-animations"></a>Disparar ações e animações

Também é possível executar um pouco de código com base em um gatilho. Esse código pode ser uma animação que tem como alvo uma propriedade. Uma maneira comum é usar um [`EventTrigger`](xref:Xamarin.Forms.EventTrigger) , que define duas propriedades:

- [`Event`](xref:Xamarin.Forms.EventTrigger.Event) do tipo `string` , o nome de um evento
- [`Actions`](xref:Xamarin.Forms.EventTrigger.Actions) do tipo `IList<TriggerAction>` , uma lista de ações a serem executadas em resposta.

Para usar isso, você precisa escrever uma classe derivada de [`TriggerAction<T>`](xref:Xamarin.Forms.TriggerAction`1) , geralmente `TriggerAction<VisualElement>` . Você pode definir propriedades nessa classe. Essas são propriedades CLR simples em vez de propriedades vinculáveis porque `TriggerAction` não deriva de `BindableObject` . Você deve substituir o [`Invoke`](xref:Xamarin.Forms.TriggerAction`1.Invoke*) método que é chamado quando a ação é chamada. O argumento é o elemento de destino.

A [`ScaleAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ScaleAction.cs) classe na biblioteca [**Xamarin.Forms book. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) é um exemplo. Ele chama a `ScaleTo` propriedade para animar a `Scale` propriedade de um elemento. Como uma de suas propriedades é do tipo `Easing` , a [`EasingConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/EasingConverter.cs) classe permite que você use os `Easing` campos estáticos padrão em XAML.

O exemplo [**EntrySwell**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EntrySwell) demonstra como invocar os `ScaleAction` `EventTrigger` objetos from que monitoram os `Focused` `Unfocused` eventos e.

O exemplo [**CustomEasingSwell**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/CustomEasingSwell) mostra como definir uma função de atenuação personalizada para `ScaleAction` em um arquivo code-behind.

Você também pode invocar ações usando um `Trigger` (como diferenciado de `EventTrigger` ). Isso exige que você esteja ciente de que `TriggerBase` define duas coleções:

- [`EnterActions`](xref:Xamarin.Forms.TriggerBase.EnterActions) do tipo `IList<TriggerAction>`
- [`ExitActions`](xref:Xamarin.Forms.TriggerBase.ExitActions) do tipo `IList<TriggerAction>`

O exemplo [**EnterExitSwell**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EnterExitSwell) demonstra como usar essas coleções.

### <a name="more-event-triggers"></a>Mais gatilhos de eventos

A [`ScaleUpAndDownAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ScaleUpAndDownAction.cs) classe na biblioteca [**Xamarin.Forms book. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) chama `ScaleTo` duas vezes para escalar ou reduzir verticalmente. O exemplo [**ButtonGrowth**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ButtonGrowth) usa isso com um estilo `EventTrigger` para fornecer comentários visuais quando um `Button` é pressionado. Essa animação dupla também é possível usando duas ações na coleção de tipos [`DelayedScaleAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/DelayedScaleAction.cs)

A [`ShiverAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ShiverAction.cs) classe na biblioteca **Xamarin.Forms book. Toolkit** define uma ação Shiver personalizável. O exemplo [**ShiverButtonDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ShiverButtonDemo) demonstra isso.

A [`NumericValidationAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NumericValidationAction.cs) classe na biblioteca **Xamarin.Forms book. Toolkit** é restrita aos `Entry` elementos e define a `TextColor` propriedade como vermelho se a `Text` propriedade não for um `double` . O exemplo [**TriggerEntryValidation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/TriggerEntryValidation) demonstra isso.

### <a name="data-triggers"></a>Gatilhos de dados

O [`DataTrigger`](xref:Xamarin.Forms.DataTrigger) é semelhante ao, `Trigger` exceto que, em vez de monitorar uma propriedade para alterações de valor, ele monitora uma ligação de dados. Isso permite que uma propriedade em um elemento afete uma propriedade em outro elemento.

`DataTrigger` define três propriedades:

- [`Binding`](xref:Xamarin.Forms.DataTrigger.Binding) do tipo `BindingBase`
- [`Value`](xref:Xamarin.Forms.DataTrigger.Value) do tipo `Object`
- [`Setters`](xref:Xamarin.Forms.DataTrigger.Setters) do tipo `IList<SetterBase>`

O exemplo [**GenderColors**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/GenderColors) requer a biblioteca [**SchoolOfFineArt**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/SchoolOfFineArt) e define as cores dos nomes dos alunos como azul ou rosa com base na `Sex` Propriedade:

[![Captura de tela tripla de cores de sexo](images/ch23fg04-small.png "Cores do gênero")](images/ch23fg04-large.png#lightbox "Cores do gênero")

O exemplo [**ButtonEnabler**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ButtonEnabler) define a `IsEnabled` propriedade de um `Entry` como `False` se a `Length` propriedade da `Text` propriedade de `Entry` for igual a 0. Observe que a `Text` propriedade é inicializada para uma cadeia de caracteres vazia; por padrão, ela é `null` , e o não `DataTrigger` funcionará corretamente.

### <a name="combining-conditions-in-the-multitrigger"></a>Combinando condições no disparador de multigatilho

O [`MultiTrigger`](xref:Xamarin.Forms.MultiTrigger) é uma coleção de condições. Quando são todos `true` , os setters são aplicados. A classe define duas propriedades:

- [`Conditions`](xref:Xamarin.Forms.MultiTrigger.Conditions) do tipo `IList<Condition>`
- [`Setters`](xref:Xamarin.Forms.MultiTrigger.Setters) do tipo `IList<Setter>`

[`Condition`](xref:Xamarin.Forms.Condition) é uma classe abstrata e tem duas classes descendentes:

- [`PropertyCondition`](xref:Xamarin.Forms.Condition), que tem [`Property`](xref:Xamarin.Forms.PropertyCondition.Property) e [`Value`](xref:Xamarin.Forms.PropertyCondition.Value) Propriedades como `Trigger`
- [`BindingCondition`](xref:Xamarin.Forms.BindingCondition), que tem [`Binding`](xref:Xamarin.Forms.BindingCondition.Binding) e [`Value`](xref:Xamarin.Forms.BindingCondition.Value) Propriedades como `DataTrigger`

No exemplo de [**AndConditions**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/AndConditions) , um `BoxView` é colorido apenas quando quatro `Switch` elementos estão todos ativados.

O exemplo [**OrConditions**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/OrConditions) demonstra como você pode fazer uma `BoxView` cor quando *qualquer* um dos quatro `Switch` elementos são ativados. Isso exige um aplicativo da lei da Morgan e a reversão de toda a lógica.

A combinação de AND e OR lógica não é tão fácil e geralmente requer elementos invisíveis `Switch` para resultados intermediários. O exemplo [**XorConditions**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/XorConditions) demonstra como um `Button` pode ser habilitado se qualquer um dos dois `Entry` elementos tiver algum texto digitado, mas não se ambos tiverem texto digitado.

## <a name="behaviors"></a>Comportamentos

Tudo o que você pode fazer com um gatilho, também pode fazer com um comportamento, mas os comportamentos sempre exigem uma classe derivada de [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) e substituem os dois métodos a seguir:

- [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo*)
- [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom*)

O argumento é o elemento ao qual o comportamento está anexado. Em geral, o `OnAttachedTo` método anexa alguns manipuladores de eventos e os `OnDetachingFrom` desanexa. Como essa classe geralmente salva algum estado, ela geralmente não pode ser compartilhada em um `Style` .

O exemplo de [**BehaviorEntryValidation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/BehaviorEntryValidation) é semelhante a **TriggerEntryValidation** , exceto pelo fato de que ele usa um comportamento &mdash; da [`NumericValidationBehavior`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NumericValidationBehavior.cs) classe na biblioteca [**Xamarin.Forms book. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) .

### <a name="behaviors-with-properties"></a>Comportamentos com propriedades

`Behavior<T>` deriva de `Behavior` , que deriva de `BindableObject` , portanto, as propriedades vinculáveis podem ser definidas em um comportamento. Essas propriedades podem estar ativas em associações de dados.

Isso é demonstrado no programa [**EmailValidationDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EmailValidationDemo) que usa a [`ValidEmailBehavior`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ValidEmailBehavior.cs) classe na biblioteca [**Xamarin.Forms book. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) . `ValidEmailBehavior` tem uma propriedade vinculável somente leitura e serve como uma fonte em associações de dados.

O exemplo [**EmailValidationConv**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EmailValidationConv) usa esse mesmo comportamento para exibir outro tipo de indicador para sinalizar que um endereço de email é válido.

O exemplo de [**EmailValidationTrigger**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EmailValidationTrigger) é uma variação no exemplo anterior. ButtonGlide usa uma `DataTrigger` combinação em conjunto com esse comportamento.

### <a name="toggles-and-check-boxes"></a>Alterna e caixas de seleção

É possível encapsular o comportamento de um botão de alternância em uma classe como [`ToggleBehavior`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ToggleBehavior.cs) na biblioteca [**Xamarin.Forms book. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) e, em seguida, definir todos os visuais para a alternância inteiramente no XAML.

O exemplo [**ToggleLabel**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ToggleLabel) usa o `ToggleBehavior` com um `DataTrigger` para usar um `Label` com duas cadeias de caracteres de texto para a alternância.

O exemplo [**FormattedTextToggle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/FormattedTextToggle) estende esse conceito alternando entre dois `FormattedString` objetos.

A [`ToggleBase`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ToggleBase.cs) classe na biblioteca **Xamarin.Forms book. Toolkit** deriva de `ContentView` , define uma `IsToggled` propriedade e incorpora uma `ToggleBehavior` para a lógica de alternância. Isso facilita a definição do botão de alternância em XAML, conforme demonstrado pelo exemplo de [**TraditionalCheckBox**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/TraditionalCheckBox) .

O [**SwitchCloneDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/SwitchCloneDemo) inclui uma [`SwitchClone`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter23/SwitchCloneDemo/SwitchCloneDemo/SwitchCloneDemo/SwitchClone.cs) classe que deriva de `ToggleBase` e usa uma [`TranslateAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/TranslateAction.cs) classe para construir um botão de alternância que se assemelha ao Xamarin.Forms `Switch` .

R [`RotateAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/RotateAction.cs) no **Xamarin.Forms book. Toolkit** fornece uma animação usada para criar uma alavanca animada no exemplo de [**LeverToggle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/LeverToggle) .

### <a name="responding-to-taps"></a>Respondendo a toques

Uma desvantagem do `EventTrigger` é que você não pode anexá-lo a um `TapGestureRecognizer` para responder a toques. Resolver esse problema é a finalidade do [`TapBehavior`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/TapBehavior.cs) no [ **Xamarin.Forms book. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)

O exemplo [**BoxViewTapShiver**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/BoxViewTapShiver) usa `TapBehavior` para usar o anterior `ShiverAction` para elementos tocadas `BoxView` .

O exemplo [**ShiverViews**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ShiverViews) mostra como reduzir a marcação encapsulando uma `ShiverView` classe.

### <a name="radio-buttons"></a>Botões de opção

A biblioteca [**Xamarin.Forms book. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) também tem uma [`RadioBehavior`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/RadioBehavior.cs) classe para fazer botões de opção agrupados por um `string` nome de grupo.

O programa [**RadioLabels**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/RadioLabels) usa cadeias de caracteres de texto para o botão de opção. O [**exemplo de**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/RadioStyle) usa um `Style` para a diferença na aparência entre os botões marcado e desmarcado. O exemplo [**RadioImages**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/RadioImages) usa imagens emolduradas para seus botões de opção:

[![Captura de tela tripla de imagens de rádio](images/ch23fg17-small.png "Imagens do botão de opção")](images/ch23fg17-large.png#lightbox "Imagens do botão de opção")

O exemplo [**TraditionalRadios**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/TraditionalRadios) desenha botões de opção que aparecem tradicionais com um ponto dentro de um círculo.

### <a name="fades-and-orientation"></a>Fade e orientação

O exemplo final, [**MultiColorSliders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/MultiColorSliders) permite alternar entre três exibições de seleção de cor diferentes usando botões de opção. As três exibições esmaecem e saem usando um [`FadeEnableAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/FadeEnableAction.cs) na biblioteca [**Xamarin.Forms book. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) .

O programa também responde às alterações na orientação entre retrato e paisagem usando um [`GridOrientationBehavior`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/GridOrientationBehavior.cs) na biblioteca **Xamarin.Forms book. Toolkit** .

## <a name="related-links"></a>Links relacionados

- [Capítulo 23 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch23-Apr2016.pdf)
- [Capítulo 23 amostras](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23)
- [Trabalhando com gatilhos](~/xamarin-forms/app-fundamentals/triggers.md)
- [Xamarin.Forms Comportamento](~/xamarin-forms/app-fundamentals/behaviors/creating.md)
