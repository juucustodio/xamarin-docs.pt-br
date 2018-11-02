---
title: EventToCommandBehavior reutilizável
description: Comportamentos de podem ser usados para associar a controles que não foram projetados para interagir com os comandos de comandos. Este artigo demonstra como criar e consumir um comportamento de xamarin. Forms para invocar um comando quando um evento é acionado.
ms.prod: xamarin
ms.assetid: EC7F6556-9776-40B8-9424-A8094482A2F3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: 2609ce4ea677c6b03021a919599c250abff663a3
ms.sourcegitcommit: 06a52ac36031d0d303ac7fc8163a59c178799c80
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/01/2018
ms.locfileid: "50911548"
---
# <a name="reusable-eventtocommandbehavior"></a>EventToCommandBehavior reutilizável

_Comportamentos de podem ser usados para associar a controles que não foram projetados para interagir com os comandos de comandos. Este artigo demonstra como criar e consumir um comportamento de xamarin. Forms para invocar um comando quando um evento é acionado._

## <a name="overview"></a>Visão geral

O `EventToCommandBehavior` classe é um comportamento personalizado reutilizável xamarin. Forms que executa um comando em resposta às *qualquer* acionamento do evento. Por padrão, os argumentos de evento para o evento será passado para o comando e pode ser opcionalmente convertido por um [ `IValueConverter` ](xref:Xamarin.Forms.IValueConverter) implementação.

As seguintes propriedades de comportamento devem ser definidas para usar o comportamento:

- **EventName** – o nome do evento o comportamento de escuta.
- **Comando** – a `ICommand` a ser executado. O comportamento de espera encontrar o `ICommand` de instância em de [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) do controle anexado, o que pode ser herdado de um elemento pai.

As seguintes propriedades de comportamento opcional também podem ser definidas:

- **CommandParameter** – um `object` que serão passados para o comando.
- **Conversor** – um [ `IValueConverter` ](xref:Xamarin.Forms.IValueConverter) implementação que irá alterar o formato dos dados de argumento do evento conforme ele é passado entre *origem* e *destino*pelo mecanismo de associação.

> [!NOTE]
> O `EventToCommandBehavior` é uma classe personalizada que pode estar localizada em de [exemplo de comportamento EventToCommand](https://developer.xamarin.com/samples/xamarin-forms/behaviors/eventtocommandbehavior/), e não faz parte do xamarin. Forms.

## <a name="creating-the-behavior"></a>Criando o comportamento

O `EventToCommandBehavior` classe deriva de `BehaviorBase<T>` classe, que por sua vez deriva o [ `Behavior<T>` ](xref:Xamarin.Forms.Behavior`1) classe. A finalidade de `BehaviorBase<T>` classe é fornecer uma classe base para todos os comportamentos xamarin. Forms que exigem a [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) do comportamento a ser definido como o controle anexado. Isso garante que o comportamento pode associar e executar o `ICommand` especificado pelo `Command` propriedade quando o comportamento é consumido.

O `BehaviorBase<T>` classe fornece um substituível [ `OnAttachedTo` ](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) método que define os [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) o comportamento e um substituível [ `OnDetachingFrom` ](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject))método que limpa o `BindingContext`. Além disso, a classe armazena uma referência para o controle anexado no `AssociatedObject` propriedade.

### <a name="implementing-bindable-properties"></a>Implementando propriedades vinculáveis

O `EventToCommandBehavior` classe define quatro [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) instâncias, que executam um usuário definido pelo comando quando um evento é acionado. Essas propriedades são mostradas no exemplo de código a seguir:

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

Quando o `EventToCommandBehavior` classe for consumido, o `Command` propriedade deve ser associada a dados para um `ICommand` a ser executado em resposta ao acionamento do evento que é definido no `EventName` propriedade. O comportamento esperam encontrar o `ICommand` sobre o [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) do controle anexado.

Por padrão, os argumentos do evento para o evento serão ser passados para o comando. Esses dados podem ser convertidos, opcionalmente, que é passado entre *fonte* e *destino* pelo mecanismo de associação, especificando um [ `IValueConverter` ](xref:Xamarin.Forms.IValueConverter) implementação como o `Converter` valor da propriedade. Como alternativa, um parâmetro pode ser passado para o comando especificando o `CommandParameter` valor da propriedade.

### <a name="implementing-the-overrides"></a>Implementando as substituições

O `EventToCommandBehavior` substituições de classe de [ `OnAttachedTo` ](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) e [ `OnDetachingFrom` ](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) métodos do `BehaviorBase<T>` de classe, conforme mostrado no exemplo de código a seguir:

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

O [ `OnAttachedTo` ](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) método executa a instalação por meio da chamada a `RegisterEvent` método, passando o valor da `EventName` a propriedade como um parâmetro. O [ `OnDetachingFrom` ](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) método realiza a limpeza, chamando o `DeregisterEvent` método, passando o valor da `EventName` a propriedade como um parâmetro.

### <a name="implementing-the-behavior-functionality"></a>A implementação da funcionalidade de comportamento

A finalidade do comportamento é executar o comando definido pelos `Command` propriedade em resposta ao acionamento do evento que é definida pela `EventName` propriedade. A principal funcionalidade do comportamento é mostrada no exemplo de código a seguir:

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

O `RegisterEvent` método é executado em resposta ao `EventToCommandBehavior` que está sendo anexado a um controle e ele recebe o valor da `EventName` a propriedade como um parâmetro. O método tenta localizar o evento definido em, em seguida, o `EventName` propriedade no controle anexado. Desde que o evento pode ser localizado, o `OnEvent` método está registrado para ser o método de manipulador para o evento.

O `OnEvent` método é executado em resposta ao acionamento do evento que é definido no `EventName` propriedade. Desde que o `Command` referências de propriedade válido `ICommand`, o método tenta recuperar um parâmetro para passar para o `ICommand` da seguinte maneira:

- Se o `CommandParameter` propriedade define um parâmetro, ele será recuperado.
- Caso contrário, se o `Converter` propriedade define uma [ `IValueConverter` ](xref:Xamarin.Forms.IValueConverter) implementação, o conversor é executada e converte os dados de argumento do evento conforme ele é passado entre *origem* e *destino* pelo mecanismo de associação.
- Caso contrário, os argumentos do evento devem para ser o parâmetro.

Os dados associados `ICommand` , em seguida, é executada, passando o parâmetro para o comando, desde que o [ `CanExecute` ](xref:Xamarin.Forms.Command.CanExecute(System.Object)) retorno do método `true`.

Embora não seja mostrado aqui, o `EventToCommandBehavior` também inclui um `DeregisterEvent` que é executado pelo método de [ `OnDetachingFrom` ](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) método. O `DeregisterEvent` método é usado para localizar e cancelar o registro de evento definido no `EventName` limpar vazamentos de memória potencial em qualquer propriedade.

## <a name="consuming-the-behavior"></a>Consumindo o comportamento

O `EventToCommandBehavior` classe pode ser anexado ao [ `Behaviors` ](xref:Xamarin.Forms.VisualElement.Behaviors) coleção de um controle, conforme demonstrado no exemplo de código XAML a seguir:

```xaml
<ListView ItemsSource="{Binding People}">
  <ListView.Behaviors>
    <local:EventToCommandBehavior EventName="ItemSelected" Command="{Binding OutputAgeCommand}"
                                  Converter="{StaticResource SelectedItemConverter}" />
  </ListView.Behaviors>
</ListView>
<Label Text="{Binding SelectedItemText}" />
```

O código C# equivalente é mostrado no exemplo de código a seguir:

```csharp
var listView = new ListView ();
listView.SetBinding (ItemsView<Cell>.ItemsSourceProperty, "People");
listView.Behaviors.Add (new EventToCommandBehavior {
  EventName = "ItemSelected",
  Command = ((HomePageViewModel)BindingContext).OutputAgeCommand,
  Converter = new SelectedItemEventArgsToSelectedItemConverter ()
});

var selectedItemLabel = new Label ();
selectedItemLabel.SetBinding (Label.TextProperty, "SelectedItemText");
```

O `Command` é de propriedade do comportamento associado a dados a `OutputAgeCommand` propriedade de ViewModel associado, enquanto o `Converter` estiver definida como o `SelectedItemConverter` da instância, que retorna o [ `SelectedItem` ](xref:Xamarin.Forms.ListView.SelectedItem)do [ `ListView` ](xref:Xamarin.Forms.ListView) do [ `SelectedItemChangedEventArgs` ](xref:Xamarin.Forms.SelectedItemChangedEventArgs).

Em tempo de execução, o comportamento será responder à interação com o controle. Quando um item for selecionado na [ `ListView` ](xref:Xamarin.Forms.ListView), o [ `ItemSelected` ](xref:Xamarin.Forms.ListView.ItemSelected) evento será acionado, o qual será executada a `OutputAgeCommand` no ViewModel. Por sua vez Isso atualiza o ViewModel `SelectedItemText` propriedade que o [ `Label` ](xref:Xamarin.Forms.Label) associa a, conforme mostrado nas capturas de tela seguir:

[![](event-to-command-behavior-images/screenshots-sml.png "Exemplo de aplicativo com EventToCommandBehavior")](event-to-command-behavior-images/screenshots.png#lightbox "exemplo de aplicativo com EventToCommandBehavior")

A vantagem de usar esse comportamento para executar um comando quando um evento é acionado, é que os comandos podem ser associados a controles que não foram projetados para interagir com os comandos. Além disso, isso remove o código de manipulação de eventos de texto clichê clichê do arquivos code-behind.

## <a name="summary"></a>Resumo

Este artigo demonstrou usando um comportamento de xamarin. Forms para invocar um comando quando um evento é acionado. Comportamentos de podem ser usados para associar a controles que não foram projetados para interagir com os comandos de comandos.


## <a name="related-links"></a>Links relacionados

- [Comportamento de EventToCommand (amostra)](https://developer.xamarin.com/samples/xamarin-forms/behaviors/eventtocommandbehavior/)
- [Comportamento](xref:Xamarin.Forms.Behavior)
- [Comportamento&lt;T&gt;](xref:Xamarin.Forms.Behavior`1)
