---
title: EventToCommandBehavior reutilizável
description: Comportamentos podem ser usados para associar comandos a controles que não foram projetados para interagir com comandos. Este artigo demonstra como criar e consumir um comportamento do Xamarin.Forms para invocar um comando quando um evento é disparado.
ms.prod: xamarin
ms.assetid: EC7F6556-9776-40B8-9424-A8094482A2F3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/09/2018
ms.openlocfilehash: 292a6aaaea4fb0f84138e04c88f001c72ddd096d
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68650906"
---
# <a name="reusable-eventtocommandbehavior"></a>EventToCommandBehavior reutilizável

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-eventtocommandbehavior)

_Comportamentos podem ser usados para associar comandos a controles que não foram projetados para interagir com comandos. Este artigo demonstra como criar e consumir um comportamento do Xamarin.Forms para invocar um comando quando um evento é disparado._

## <a name="overview"></a>Visão geral

A classe `EventToCommandBehavior` é um comportamento personalizado reutilizável do Xamarin.Forms que executa um comando em resposta a *qualquer* acionamento de evento. Por padrão, os argumentos de evento para o evento serão passados para o comando e podem ser opcionalmente convertidos por uma implementação de [`IValueConverter`](xref:Xamarin.Forms.IValueConverter).

As seguintes propriedades de comportamento precisam ser definidas para que o comportamento seja usado:

- **EventName** – o nome do evento que o comportamento escuta.
- **Command** – o `ICommand` a ser executado. O comportamento espera encontrar a instância `ICommand` do [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) do controle anexado, que pode ser herdada de um elemento pai.

As seguintes propriedades de comportamento opcionais também podem ser definidas:

- **CommandParameter** – um `object` que será passado para o comando.
- **Converter** – uma implementação de [`IValueConverter`](xref:Xamarin.Forms.IValueConverter) que alterará o formato dos dados de argumento do evento conforme eles são passados entre a *origem* e o *destino* pelo mecanismo de associação.

> [!NOTE]
> O `EventToCommandBehavior` é uma classe personalizada que pode estar localizada na [amostra de Comportamento de EventToCommand](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-eventtocommandbehavior), não fazendo parte do Xamarin.Forms.

## <a name="creating-the-behavior"></a>Criação do comportamento

A classe `EventToCommandBehavior` deriva da classe `BehaviorBase<T>`, que, por sua vez, deriva da classe [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1). A finalidade da classe `BehaviorBase<T>` é fornecer uma classe base para todos os comportamentos do Xamarin.Forms que exigem que o [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) do comportamento seja definido como o controle anexado. Isso garante que o comportamento possa ser associado ao `ICommand` especificado pela propriedade `Command` e possa executá-lo quando o comportamento for consumido.

A classe `BehaviorBase<T>` fornece um método [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) substituível que define o [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) do comportamento e um método [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) substituível que limpa o `BindingContext`. Além disso, a classe armazena uma referência ao controle anexado na propriedade `AssociatedObject`.

### <a name="implementing-bindable-properties"></a>Implementação de propriedades associáveis

A classe `EventToCommandBehavior` define quatro instâncias de [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), que executam um comando definido pelo usuário quando um evento é disparado. Essas propriedades são mostradas no seguinte exemplo de código:

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

Quando a classe `EventToCommandBehavior` é consumida, a propriedade `Command` deve ser associada a um `ICommand`, a ser executado em resposta ao acionamento do evento definido na propriedade `EventName`. O comportamento esperará encontrar o `ICommand` no [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) do controle anexado.

Por padrão, os argumentos do evento para o evento serão passados para o comando. Esses dados podem ser opcionalmente convertidos conforme são passados entre a *origem* e o *destino* pelo mecanismo de associação, especificando uma implementação de [`IValueConverter`](xref:Xamarin.Forms.IValueConverter) como o valor da propriedade `Converter`. Como alternativa, um parâmetro pode ser passado para o comando especificando o valor da propriedade `CommandParameter`.

### <a name="implementing-the-overrides"></a>Implementação de substituições

A classe `EventToCommandBehavior` substitui os métodos [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) e [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) da classe `BehaviorBase<T>`, conforme mostrado no seguinte exemplo de código:

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

O método [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) executa a instalação por meio da chamada ao método `RegisterEvent`, passando o valor da propriedade `EventName` como um parâmetro. O método [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) executa a limpeza por meio da chamada ao método `DeregisterEvent`, passando o valor da propriedade `EventName` como um parâmetro.

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
- Caso contrário, se a propriedade `Converter` define uma implementação de [`IValueConverter`](xref:Xamarin.Forms.IValueConverter), o conversor é executado e converte os dados de argumento do evento conforme eles são passados entre a *origem* e o *destino* pelo mecanismo de associação.
- Caso contrário, os argumentos do evento deverão ser o parâmetro.

Em seguida, o `ICommand` associado a dados é executado, passando o parâmetro para o comando, desde que o método [`CanExecute`](xref:Xamarin.Forms.Command.CanExecute(System.Object)) retorne `true`.

Embora não seja mostrado aqui, o `EventToCommandBehavior` também inclui um método `DeregisterEvent` que é executado pelo método [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)). O método `DeregisterEvent` é usado para localizar e cancelar o registro do evento definido na propriedade `EventName`, para limpar possíveis perdas de memória.

## <a name="consuming-the-behavior"></a>Consumo do comportamento

A classe `EventToCommandBehavior` pode ser anexada à coleção [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) de um controle, conforme demonstrado no seguinte exemplo de código XAML:

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

A propriedade `Command` do comportamento é associado a dados à propriedade `OutputAgeCommand` do ViewModel associado, enquanto a propriedade `Converter` é definida como a instância `SelectedItemConverter`, que retorna o [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) da [`ListView`](xref:Xamarin.Forms.ListView) nos [`SelectedItemChangedEventArgs`](xref:Xamarin.Forms.SelectedItemChangedEventArgs).

Em tempo de execução, o comportamento responderá à interação com o controle. Quando um item for selecionado na [`ListView`](xref:Xamarin.Forms.ListView), o evento [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) será disparado, o que executará o `OutputAgeCommand` no ViewModel. Isso, por sua vez, atualiza a propriedade `SelectedItemText` do ViewModel à qual o [`Label`](xref:Xamarin.Forms.Label) está associado, conforme mostrado nas seguintes capturas de tela:

[![](event-to-command-behavior-images/screenshots-sml.png "Aplicativo de exemplo com EventToCommandBehavior")](event-to-command-behavior-images/screenshots.png#lightbox "Aplicativo de exemplo com EventToCommandBehavior")

A vantagem de usar esse comportamento para executar um comando quando um evento é disparado é que os comandos podem ser associados a controles que não foram projetados para interagir com comandos. Além disso, isso remove o código de manipulação de eventos de texto clichê dos arquivos code-behind.

## <a name="summary"></a>Resumo

Este artigo demonstrou como usar um comportamento do Xamarin.Forms para invocar um comando quando um evento é disparado. Comportamentos podem ser usados para associar comandos a controles que não foram projetados para interagir com comandos.

## <a name="related-links"></a>Links relacionados

- [Comportamento de EventToCommand (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-eventtocommandbehavior)
- [Comportamento](xref:Xamarin.Forms.Behavior)
- [Comportamento&lt;T&gt;](xref:Xamarin.Forms.Behavior`1)
