---
title: EventToCommandBehavior reutilizável
description: Comportamentos podem ser usados para associar comandos a controles que não foram projetados para interagir com comandos. Este artigo demonstra como criar e consumir um Xamarin.Forms comportamento para invocar um comando quando um evento é disparado.
ms.prod: xamarin
ms.assetid: EC7F6556-9776-40B8-9424-A8094482A2F3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/09/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 46d1566c89de763a469f30ce8ed2c6ef919f1426
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84135792"
---
# <a name="reusable-eventtocommandbehavior"></a>EventToCommandBehavior reutilizável

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-eventtocommandbehavior)

_Os comportamentos podem ser usados para associar comandos a controles que não foram projetados para interagir com comandos. Este artigo demonstra como criar e consumir um Xamarin.Forms comportamento para invocar um comando quando um evento é disparado._

## <a name="overview"></a>Visão geral

A `EventToCommandBehavior` classe é um comportamento personalizado reutilizável Xamarin.Forms que executa um comando em resposta a *qualquer* acionamento de evento. Por padrão, os argumentos de evento para o evento serão passados para o comando e podem ser convertidos opcionalmente por uma [`IValueConverter`](xref:Xamarin.Forms.IValueConverter) implementação.

As seguintes propriedades de comportamento precisam ser definidas para que o comportamento seja usado:

- **EventName** – o nome do evento que o comportamento escuta.
- **Command** – o `ICommand` a ser executado. O comportamento espera encontrar a `ICommand` instância no [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) do controle anexado, que pode ser herdado de um elemento pai.

As seguintes propriedades de comportamento opcionais também podem ser definidas:

- **CommandParameter** – um `object` que será passado para o comando.
- **Converter** – uma implementação de [`IValueConverter`](xref:Xamarin.Forms.IValueConverter) que alterará o formato dos dados de argumento do evento conforme eles são passados entre a *origem* e o *destino* pelo mecanismo de associação.

> [!NOTE]
> O `EventToCommandBehavior` é uma classe personalizada que pode ser localizada no [exemplo de comportamento EventToCommand](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-eventtocommandbehavior)e não faz parte do Xamarin.Forms .

## <a name="creating-the-behavior"></a>Criação do comportamento

A `EventToCommandBehavior` classe deriva da `BehaviorBase<T>` classe, que, por sua vez, deriva da [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) classe. A finalidade da `BehaviorBase<T>` classe é fornecer uma classe base para quaisquer Xamarin.Forms comportamentos que exijam que o [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) comportamento seja definido para o controle anexado. Isso garante que o comportamento possa ser associado ao `ICommand` especificado pela propriedade `Command` e possa executá-lo quando o comportamento for consumido.

A `BehaviorBase<T>` classe fornece um Overridable [ `OnAttachedTo` ] (xref: Xamarin.Forms . Comportamento `1.OnAttachedTo(Xamarin.Forms.BindableObject)) method that sets the [` BindingContext `](xref:Xamarin.Forms.BindableObject.BindingContext) of the behavior and an overridable [` OnDetachingFrom `](xref:Xamarin.Forms.Behavior` 1. OnDetachingFrom ( Xamarin.Forms . Acopláobject)) que limpa o `BindingContext` . Além disso, a classe armazena uma referência ao controle anexado na propriedade `AssociatedObject`.

### <a name="implementing-bindable-properties"></a>Implementação de propriedades associáveis

A `EventToCommandBehavior` classe define quatro [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) instâncias, que executam um comando definido pelo usuário quando um evento é disparado. Essas propriedades são mostradas no seguinte exemplo de código:

```csharp
public class EventToCommandBehavior : BehaviorBase<View>
{
  public static readonly BindableProperty EventNameProperty =
    BindableProperty.Create ("EventName", typeof(string), typeof(EventToCommandBehavior), null, propertyChanged: OnEventNameChanged);
  public static readonly BindableProperty CommandProperty =
    BindableProperty.Create ("Command", typeof(ICommand), typeof(EventToCommandBehavior), null);
  public static readonly BindableProperty CommandParameterProperty =
    BindableProperty.Create ("CommandParameter", typeof(object), typeof(EventToCommandBehavior), null);
  public static readonly BindableProperty InputConverterProperty =
    BindableProperty.Create ("Converter", typeof(IValueConverter), typeof(EventToCommandBehavior), null);

  public string EventName { ... }
  public ICommand Command { ... }
  public object CommandParameter { ... }
  public IValueConverter Converter { ...  }
  ...
}
```

Quando a classe `EventToCommandBehavior` é consumida, a propriedade `Command` deve ser associada a um `ICommand`, a ser executado em resposta ao acionamento do evento definido na propriedade `EventName`. O comportamento esperará localizar o `ICommand` no [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) do controle anexado.

Por padrão, os argumentos do evento para o evento serão passados para o comando. Esses dados podem ser opcionalmente convertidos conforme são passados entre a *origem* e o *destino* pelo mecanismo de associação, especificando uma implementação de [`IValueConverter`](xref:Xamarin.Forms.IValueConverter) como o valor da propriedade `Converter`. Como alternativa, um parâmetro pode ser passado para o comando especificando o valor da propriedade `CommandParameter`.

### <a name="implementing-the-overrides"></a>Implementação de substituições

A `EventToCommandBehavior` classe substitui o [ `OnAttachedTo` ] (xref: Xamarin.Forms . Comportamento `1.OnAttachedTo(Xamarin.Forms.BindableObject)) and [` OnDetachingFrom `](xref:Xamarin.Forms.Behavior` 1. OnDetachingFrom ( Xamarin.Forms . Bindobject)) da `BehaviorBase<T>` classe, conforme mostrado no exemplo de código a seguir:

```csharp
public class EventToCommandBehavior : BehaviorBase<View>
{
  ...
  protected override void OnAttachedTo (View bindable)
  {
    base.OnAttachedTo (bindable);
    RegisterEvent (EventName);
  }

  protected override void OnDetachingFrom (View bindable)
  {
    DeregisterEvent (EventName);
    base.OnDetachingFrom (bindable);
  }
  ...
}
```

O [ `OnAttachedTo` ] (xref: Xamarin.Forms . Comportamento `1.OnAttachedTo(Xamarin.Forms.BindableObject)) method performs setup by calling the ` RegisterEvent ` method, passing in the value of the ` EventName ` property as a parameter. The [` OnDetachingFrom `](xref:Xamarin.Forms.Behavior` 1. OnDetachingFrom ( Xamarin.Forms . Acopláobject)) executa a limpeza chamando o `DeregisterEvent` método, passando o valor da `EventName` propriedade como um parâmetro.

### <a name="implementing-the-behavior-functionality"></a>Implementação da funcionalidade de comportamento

A finalidade do comportamento é executar o comando definido pela propriedade `Command` em resposta ao acionamento do evento definido pela propriedade `EventName`. A principal funcionalidade de comportamento é mostrada no seguinte exemplo de código:

```csharp
public class EventToCommandBehavior : BehaviorBase<View>
{
  ...
  void RegisterEvent (string name)
  {
    if (string.IsNullOrWhiteSpace (name)) {
      return;
    }

    EventInfo eventInfo = AssociatedObject.GetType ().GetRuntimeEvent (name);
    if (eventInfo == null) {
      throw new ArgumentException (string.Format ("EventToCommandBehavior: Can't register the '{0}' event.", EventName));
    }
    MethodInfo methodInfo = typeof(EventToCommandBehavior).GetTypeInfo ().GetDeclaredMethod ("OnEvent");
    eventHandler = methodInfo.CreateDelegate (eventInfo.EventHandlerType, this);
    eventInfo.AddEventHandler (AssociatedObject, eventHandler);
  }

  void OnEvent (object sender, object eventArgs)
  {
    if (Command == null) {
      return;
    }

    object resolvedParameter;
    if (CommandParameter != null) {
      resolvedParameter = CommandParameter;
    } else if (Converter != null) {
      resolvedParameter = Converter.Convert (eventArgs, typeof(object), null, null);
    } else {
      resolvedParameter = eventArgs;
    }        

    if (Command.CanExecute (resolvedParameter)) {
      Command.Execute (resolvedParameter);
    }
  }
  ...
}
```

O método `RegisterEvent` é executado em resposta ao `EventToCommandBehavior` ser anexado a um controle e recebe o valor da propriedade `EventName` como um parâmetro. Em seguida, o método tenta localizar no controle anexado o evento definido na propriedade `EventName`. Desde que o evento possa ser localizado, o método `OnEvent` é registrado para ser o método de manipulador para o evento.

O método `OnEvent` é executado em resposta ao acionamento do evento definido na propriedade `EventName`. Desde que a propriedade `Command` referencie um `ICommand` válido, o método tenta recuperar um parâmetro para passá-lo para o `ICommand` da seguinte maneira:

- Se a propriedade `CommandParameter` define um parâmetro, ela é recuperada.
- Caso contrário, se a `Converter` propriedade definir uma [`IValueConverter`](xref:Xamarin.Forms.IValueConverter) implementação, o conversor será executado e converterá os dados de argumento de evento conforme eles forem passados entre a *origem* e o *destino* pelo mecanismo de associação.
- Caso contrário, os argumentos do evento deverão ser o parâmetro.

O limite de dados `ICommand` é então executado, passando o parâmetro para o comando, desde que o [`CanExecute`](xref:Xamarin.Forms.Command.CanExecute(System.Object)) método retorne `true` .

Embora não seja mostrado aqui, o `EventToCommandBehavior` também inclui um `DeregisterEvent` método que é executado pelo [ `OnDetachingFrom` ] (xref: Xamarin.Forms . Comportamento `1.OnDetachingFrom(Xamarin.Forms.BindableObject)) method. The ` DeregisterEvent ` method is used to locate and deregister the event defined in the ` EventName ' para limpar possíveis vazamentos de memória.

## <a name="consuming-the-behavior"></a>Consumo do comportamento

A `EventToCommandBehavior` classe pode ser anexada à [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) coleção de um controle, conforme demonstrado no exemplo de código XAML a seguir:

```xaml
<ListView ItemsSource="{Binding People}">
    <ListView.ItemTemplate>
        <DataTemplate>
            <TextCell Text="{Binding Name}" />
        </DataTemplate>
    </ListView.ItemTemplate>
    <ListView.Behaviors>
        <local:EventToCommandBehavior EventName="ItemSelected" Command="{Binding OutputAgeCommand}" Converter="{StaticResource SelectedItemConverter}" />
    </ListView.Behaviors>
</ListView>
<Label Text="{Binding SelectedItemText}" />
```

O código C# equivalente é mostrado no exemplo de código a seguir:

```csharp
var listView = new ListView();
listView.SetBinding(ItemsView<Cell>.ItemsSourceProperty, "People");
listView.ItemTemplate = new DataTemplate(() =>
{
    var textCell = new TextCell();
    textCell.SetBinding(TextCell.TextProperty, "Name");
    return textCell;
});
listView.Behaviors.Add(new EventToCommandBehavior
{
    EventName = "ItemSelected",
    Command = ((HomePageViewModel)BindingContext).OutputAgeCommand,
    Converter = new SelectedItemEventArgsToSelectedItemConverter()
});

var selectedItemLabel = new Label();
selectedItemLabel.SetBinding(Label.TextProperty, "SelectedItemText");
```

A `Command` Propriedade do comportamento são dados associados à `OutputAgeCommand` Propriedade do ViewModel associado, enquanto a `Converter` propriedade é definida como a `SelectedItemConverter` instância, que retorna o [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) do do [`ListView`](xref:Xamarin.Forms.ListView) [`SelectedItemChangedEventArgs`](xref:Xamarin.Forms.SelectedItemChangedEventArgs) .

Em runtime, o comportamento responderá à interação com o controle. Quando um item é selecionado no [`ListView`](xref:Xamarin.Forms.ListView) , o [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) evento será acionado, o que executará o `OutputAgeCommand` no ViewModel. Por sua vez, isso atualiza a `SelectedItemText` Propriedade ViewModel à qual o está [`Label`](xref:Xamarin.Forms.Label) associado, conforme mostrado nas seguintes capturas de tela:

[![](event-to-command-behavior-images/screenshots-sml.png "Sample Application with EventToCommandBehavior")](event-to-command-behavior-images/screenshots.png#lightbox "Sample Application with EventToCommandBehavior")

A vantagem de usar esse comportamento para executar um comando quando um evento é disparado é que os comandos podem ser associados a controles que não foram projetados para interagir com comandos. Além disso, isso remove o código de manipulação de eventos de texto clichê dos arquivos code-behind.

## <a name="summary"></a>Resumo

Este artigo demonstrou o uso de um Xamarin.Forms comportamento para invocar um comando quando um evento é disparado. Comportamentos podem ser usados para associar comandos a controles que não foram projetados para interagir com comandos.

## <a name="related-links"></a>Links relacionados

- [Comportamento de EventToCommand (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-eventtocommandbehavior)
- [Comportamento](xref:Xamarin.Forms.Behavior)
- [Comportamento &lt; T&gt;](xref:Xamarin.Forms.Behavior`1)
