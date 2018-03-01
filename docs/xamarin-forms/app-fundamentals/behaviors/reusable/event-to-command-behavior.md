---
title: Reusable EventToCommandBehavior
description: "Comportamentos podem ser usados para associar a controles que não foram projetados para interagir com os comandos de comandos. Este artigo demonstra como usar um comportamento xamarin. Forms para invocar um comando quando um evento é acionado."
ms.topic: article
ms.prod: xamarin
ms.assetid: EC7F6556-9776-40B8-9424-A8094482A2F3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: 2727d83e55e305af1372ece35bdf22abfc653fe7
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="reusable-eventtocommandbehavior"></a>Reusable EventToCommandBehavior

_Comportamentos podem ser usados para associar a controles que não foram projetados para interagir com os comandos de comandos. Este artigo demonstra como usar um comportamento xamarin. Forms para invocar um comando quando um evento é acionado._

## <a name="overview"></a>Visão geral

O `EventToCommandBehavior` classe é um comportamento personalizado reutilizável xamarin. Forms que executa um comando em resposta a *qualquer* acionamento do evento. Por padrão, os argumentos de evento para o evento será passado para o comando e pode ser opcionalmente convertido por um [ `IValueConverter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.IValueConverter/) implementação.

As seguintes propriedades de comportamento devem ser definidas para usar o comportamento:

- **EventName** – o nome do evento o comportamento de escuta.
- **Comando** – a **ICommand** a ser executado. O comportamento de espera encontrar o `ICommand` de instância no [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) do controle anexado, que pode ser herdado de um elemento pai.

Também podem ser definidas as propriedades de comportamento opcional a seguir:

- **CommandParameter** – um `object` que será passado para o comando.
- **Conversor** – um [ `IValueConverter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.IValueConverter/) implementação que irá alterar o formato dos dados de argumento do evento como ele é passado entre *fonte* e *destino*pelo mecanismo de associação.

## <a name="creating-the-behavior"></a>Criando o comportamento

O `EventToCommandBehavior` classe deriva o `BehaviorBase<T>` classe, que por sua vez deriva o [ `Behavior<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior%3CT%3E/) classe. A finalidade de `BehaviorBase<T>` classe é fornecer uma classe base para todos os comportamentos xamarin. Forms exigem o [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) do comportamento a ser definido como o controle anexado. Isso garante que o comportamento pode associar e executar o `ICommand` especificado pelo `Command` propriedade quando o comportamento é consumido.

O `BehaviorBase<T>` classe fornece um substituível [ `OnAttachedTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnAttachedTo/p/Xamarin.Forms.BindableObject/) método que define o [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) o comportamento e um substituível [ `OnDetachingFrom` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnDetachingFrom/p/Xamarin.Forms.BindableObject/)método limpa o `BindingContext`. Além disso, a classe armazena uma referência para o controle anexado no `AssociatedObject` propriedade.

### <a name="implementing-bindable-properties"></a>Implementando propriedades vinculáveis

O `EventToCommandBehavior` classe define quatro [ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/) instâncias, execute um usuário definido pelo comando quando um evento é acionado. Essas propriedades são mostradas no exemplo de código a seguir:

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

Quando o `EventToCommandBehavior` classe é consumida, o `Command` propriedade deve ser dados associados a um `ICommand` para ser executado em resposta ao acionamento do evento que é definido no `EventName` propriedade. O comportamento deverá localizar o `ICommand` no [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) do controle anexado.

Por padrão, os argumentos de evento para o evento serão passados para o comando. Esses dados podem ser convertidos, opcionalmente, que é passado entre *fonte* e *destino* pelo mecanismo de associação, especificando um [ `IValueConverter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.IValueConverter/) implementação como o `Converter` o valor da propriedade. Como alternativa, um parâmetro pode ser passado para o comando especificando o `CommandParameter` o valor da propriedade.

### <a name="implementing-the-overrides"></a>Implementando as substituições

O `EventToCommandBehavior` substituições de classe a [ `OnAttachedTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnAttachedTo/p/Xamarin.Forms.BindableObject/) e [ `OnDetachingFrom` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnDetachingFrom/p/Xamarin.Forms.BindableObject/) métodos do `BehaviorBase<T>` de classe, conforme mostrado no exemplo de código a seguir:

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

O [ `OnAttachedTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnAttachedTo/p/Xamarin.Forms.BindableObject/) método executa a configuração chamando o `RegisterEvent` método, passando o valor da `EventName` a propriedade como um parâmetro. O [ `OnDetachingFrom` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnDetachingFrom/p/Xamarin.Forms.BindableObject/) método realiza a limpeza chamando o `DeregisterEvent` método, passando o valor da `EventName` a propriedade como um parâmetro.

### <a name="implementing-the-behavior-functionality"></a>A implementação da funcionalidade de comportamento

A finalidade do comportamento é executar o comando definido pelo `Command` propriedade em resposta ao acionamento do evento que é definido pelo `EventName` propriedade. A funcionalidade principal do comportamento é mostrada no exemplo de código a seguir:

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

O `RegisterEvent` o método é executado em resposta ao `EventToCommandBehavior` que está sendo anexado a um controle e recebe o valor da `EventName` a propriedade como um parâmetro. O método, em seguida, tenta localizar o evento definido no `EventName` propriedade no controle anexado. Desde que o evento pode ser localizado, o `OnEvent` método está registrado para ser o método de manipulador para o evento.

O `OnEvent` método é executado em resposta ao acionamento do evento que é definido no `EventName` propriedade. Desde que o `Command` propriedade faz referência a uma opção válida `ICommand`, o método tenta recuperar um parâmetro para passar para o `ICommand` da seguinte maneira:

- Se o `CommandParameter` propriedade define um parâmetro, ele será recuperado.
- Caso contrário, se o `Converter` propriedade define um [ `IValueConverter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.IValueConverter/) implementação, o conversor é executada e converte os dados de argumento do evento conforme ele é transmitido entre *fonte* e *destino* pelo mecanismo de associação.
- Caso contrário, os argumentos de evento devem para ser o parâmetro.

Os dados associados `ICommand` , em seguida, é executada, passando o parâmetro para o comando, desde que o [ `CanExecute` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Command.CanExecute/p/System.Object/) método retornará `true`.

Embora não mostrado aqui, o `EventToCommandBehavior` também inclui um `DeregisterEvent` que é executado pelo método de [ `OnDetachingFrom` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnDetachingFrom/p/Xamarin.Forms.BindableObject/) método. O `DeregisterEvent` método é usado para localizar e cancelar o registro de evento definido no `EventName` propriedade, para qualquer memória possível perdas de limpeza.

## <a name="consuming-the-behavior"></a>O comportamento de consumo

O `EventToCommandBehavior` classe pode ser anexada ao [ `Behaviors` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Behaviors/) coleção de um controle, como demonstrado no exemplo de código XAML a seguir:

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

O `Command` propriedade do comportamento é associada por dados ao `OutputAgeCommand` propriedade do ViewModel associado, enquanto o `Converter` está definida como o `SelectedItemConverter` da instância, que retorna o [ `SelectedItem` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.SelectedItem/)do [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) do [ `SelectedItemChangedEventArgs` ](https://developer.xamarin.com/api/type/Xamarin.Forms.SelectedItemChangedEventArgs/).

Em tempo de execução, o comportamento de responder à interação com o controle. Quando um item é selecionado no [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/), o [ `ItemSelected` ](https://developer.xamarin.com/api/event/Xamarin.Forms.ListView.ItemSelected/) evento será disparado, que executará o `OutputAgeCommand` no ViewModel. Por sua vez Isso atualiza o ViewModel `SelectedItemText` propriedade que o [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) associa a, conforme mostrado nas capturas de tela seguir:

[ ![](event-to-command-behavior-images/screenshots-sml.png "Exemplo de aplicativo com EventToCommandBehavior")](event-to-command-behavior-images/screenshots.png "aplicativo com EventToCommandBehavior de exemplo")

A vantagem de usar esse comportamento para executar um comando quando um evento é acionado, é que os comandos podem ser associados a controles que não foram projetados para interagir com os comandos. Além disso, isso removerá o código de manipulação de eventos de placa clichê de arquivos code-behind.

## <a name="summary"></a>Resumo

Este artigo demonstrou usando um comportamento xamarin. Forms para invocar um comando quando um evento é acionado. Comportamentos podem ser usados para associar a controles que não foram projetados para interagir com os comandos de comandos.


## <a name="related-links"></a>Links relacionados

- [Comportamento de EventToCommand (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/behaviors/eventtocommandbehavior/)
- [Comportamento](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior/)
- [Comportamento<T>](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior%3CT%3E/)
