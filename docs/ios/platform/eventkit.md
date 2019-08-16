---
title: Kit no Xamarin. iOS
description: Este documento descreve o kit e como usá-lo no Xamarin. iOS. Ele aborda calendários, eventos de calendário e lembretes, examina classes comumente usadas ao programar com kit e muito mais.
ms.prod: xamarin
ms.assetid: 00E88629-357D-1FCD-4FCE-1330D5D9D32C
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: bb64a0457ad5228e32f233d630576da2836547c1
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69528050"
---
# <a name="eventkit-in-xamarinios"></a>Kit no Xamarin. iOS

o iOS tem dois aplicativos relacionados a calendário internos: o aplicativo de calendário e o aplicativo de lembretes. É bastante simples entender como o aplicativo de calendário gerencia dados de calendário, mas o aplicativo lembretes é menos óbvio. Os lembretes podem realmente ter datas associadas a eles em termos de quando eles estão vencidos, quando eles são concluídos etc. Dessa forma, o iOS armazena todos os dados de calendário, sejam eles eventos de calendário ou lembretes, em um local, chamado de banco de dados de *calendário*.

A estrutura Kit fornece uma maneira de acessar osdados de calendários, *eventos de calendário*e *lembretes* que o banco de dados de calendário armazena. O acesso aos calendários e eventos de calendário está disponível desde o iOS 4, mas o acesso a lembretes é novo no iOS 6.

Neste guia, vamos abordar:

- **Noções básicas do kit** – isso apresentará as partes fundamentais de kit por meio das principais classes e fornecerá uma compreensão de seu uso. Esta seção é necessária para a leitura antes de lidar com a próxima parte do documento. 
- **Tarefas comuns** – a seção tarefas comuns destina-se a ser uma referência rápida sobre como fazer coisas comuns, como; enumerar calendários, criar, salvar e recuperar eventos de calendário e lembretes, bem como usar os controladores internos para criar e modificar eventos de calendário. Esta seção não precisa ser lida de frente para trás, pois ela deve ser uma referência para tarefas específicas. 


Todas as tarefas deste guia estão disponíveis no aplicativo de exemplo complementar:

 [![](eventkit-images/01.png "As telas de aplicativo de exemplo complementares")](eventkit-images/01.png#lightbox)

## <a name="requirements"></a>Requisitos

O kit foi introduzido no iOS 4,0, mas o acesso a dados de lembretes foi introduzido no iOS 6,0. Assim, para fazer o desenvolvimento geral de kit, você precisará ter como destino, pelo menos, a versão 4,0 e 6,0 para lembretes.

Além disso, o aplicativo lembretes não está disponível no simulador, o que significa que os dados dos lembretes também não estarão disponíveis, a menos que você os adicione primeiro. Além disso, as solicitações de acesso são mostradas apenas para o usuário no dispositivo real. Assim, o desenvolvimento de kit é melhor testado no dispositivo.

## <a name="event-kit-basics"></a>Noções básicas do kit de eventos

Ao trabalhar com kit, é importante ter uma noção das classes comuns e seu uso. Todas essas classes podem ser encontradas no `EventKit` e `EventKitUI` (para o `EKEventEditController`).

### <a name="eventstore"></a>EventStore

A classe *EventStore* é a classe mais importante no kit porque ela é necessária para executar qualquer operação no kit. Pode ser pensado como armazenamento persistente, ou mecanismo de banco de dados, para todos os Kit. A `EventStore` partir de você tem acesso aos calendários e eventos de calendário no aplicativo de calendário, bem como lembretes no aplicativo lembretes.

Como `EventStore` o é como um mecanismo de banco de dados, ele deve ser de longa duração, o que significa que ele deve ser criado e destruído o mínimo possível durante o tempo de vida de uma instância do aplicativo. Na verdade, é recomendável que, depois de criar uma instância de `EventStore` um em um aplicativo, você mantenha essa referência por todo o tempo de vida do aplicativo, a menos que você tenha certeza de que não precisará dela novamente. Além disso, todas as chamadas devem ir para `EventStore` uma única instância. Por esse motivo, o padrão singleton é recomendado para manter uma única instância disponível.

#### <a name="creating-an-event-store"></a>Criando um repositório de eventos

O código a seguir ilustra uma maneira eficiente de criar uma única instância da `EventStore` classe e torná-la disponível estaticamente de dentro de um aplicativo:

```csharp
public class App
{
    public static App Current {
            get { return current; }
    }
    private static App current;

    public EKEventStore EventStore {
            get { return eventStore; }
    }
    protected EKEventStore eventStore;

    static App ()
    {
            current = new App();
    }
    protected App () 
    {
            eventStore = new EKEventStore ( );
    }
}
```

O código acima usa o padrão singleton para instanciar uma instância do `EventStore` quando o aplicativo é carregado. O `EventStore` pode então ser acessado globalmente de dentro do aplicativo da seguinte maneira:

```csharp
App.Current.EventStore;
```

Observe que todos os exemplos aqui usam esse padrão, portanto, eles fazem `EventStore` referência `App.Current.EventStore`ao via.

#### <a name="requesting-access-to-calendar-and-reminder-data"></a>Solicitando acesso aos dados de calendário e lembrete

Antes de ter permissão para acessar quaisquer dados por meio do EventStore, um aplicativo deve primeiro solicitar acesso aos dados de eventos de calendário ou lembretes, dependendo de qual deles você precisa. Para facilitar isso, o `EventStore` expõe um método chamado `RequestAccess` , quando chamado — mostrará uma exibição de alerta ao usuário informando que o aplicativo está solicitando acesso aos dados do calendário ou aos dados do lembrete, dependendo de quais `EKEntityType`é passado para ele. Como ele gera uma exibição de alerta, a chamada é assíncrona e chamará um manipulador de conclusão passado `NSAction` como um (ou lambda) para ela, que receberá dois parâmetros; um booliano de se o acesso foi concedido `NSError`ou não, e um, que, se não for NULL, será conter qualquer informação de erro na solicitação. Por exemplo, o código a seguir solicitará acesso aos dados de eventos de calendário e mostrará uma exibição de alerta se a solicitação não tiver sido concedida.

```csharp
App.Current.EventStore.RequestAccess (EKEntityType.Event, 
    (bool granted, NSError e) => {
            if (granted)
                    //do something here
            else
                    new UIAlertView ( "Access Denied", 
"User Denied Access to Calendar Data", null,
"ok", null).Show ();
            } );
```

Depois que a solicitação tiver sido concedida, ela será lembrada desde que o aplicativo seja instalado no dispositivo e não exibirá um alerta para o usuário.
No entanto, o acesso é fornecido apenas para o tipo de recurso, os eventos de calendário ou lembretes concedidos. Se um aplicativo precisar de acesso a ambos, ele deverá solicitar ambos.

Como a permissão é lembrada, é relativamente barato fazer a solicitação a cada vez, portanto, é uma boa ideia sempre solicitar acesso antes de executar uma operação.

Além disso, como o manipulador de conclusão é chamado em um thread separado (não interface do usuário), todas as atualizações da interface do usuário no manipulador de `InvokeOnMainThread`conclusão devem ser chamadas por meio de, caso contrário, uma exceção será lançada e, se não for detectada, o aplicativo falhará.

### <a name="ekentitytype"></a>EKEntityType

`EKEntityType`é uma enumeração que descreve o tipo de `EventKit` item ou dados. Ele tem dois valores: `Event` e lembrete. Ele é usado em vários métodos, incluindo `EventStore.RequestAccess` para informar `EventKit` que tipo de dados obter acesso ou recuperar.

### <a name="ekcalendar"></a>EKCalendar

 *EKCalendar* representa um calendário, que contém um grupo de eventos de calendário. Os calendários podem ser armazenados em vários locais diferentes, como localmente, no *icloud*, em um local de provedor de terceiros, como um *Exchange Server* ou *Google*, etc. Muitas vezes `EKCalendar` é usado para informar `EventKit` onde procurar eventos ou onde salvá-los.

### <a name="ekeventeditcontroller"></a>EKEventEditController

 *EKEventEditController* pode ser encontrado no `EventKitUI` namespace e é um controlador interno que pode ser usado para editar ou criar eventos de calendário. Assim como os controladores de câmera internos, `EKEventEditController` o trabalho pesado é para você exibir a interface do usuário e manipular o salvamento.

### <a name="ekevent"></a>EKEvent

 *EKEvent* representa um evento de calendário. Ambos `EKEvent` `EKCalendarItem` e `EKReminder` herdam de e têm campos como `Title`, `Notes`e assim por diante.

### <a name="ekreminder"></a>EKReminder

 *EKReminder* representa um item de lembrete.

### <a name="ekspan"></a>EKSpan

*EKSpan* é uma enumeração que descreve o intervalo de eventos ao modificar eventos que podem ocorrer novamente e tem dois valores: *Essa* e *FutureEvents*. `ThisEvent`significa que qualquer alteração ocorrerá apenas para o evento específico na série que está sendo referenciada, enquanto `FutureEvents` afetará esse evento e todas as recorrências futuras.

## <a name="tasks"></a>Tarefas

Para facilitar o uso, o uso de kit foi dividido em tarefas comuns, descritas nas seções a seguir.

### <a name="enumerate-calendars"></a>Enumerar calendários

Para enumerar os calendários que o usuário configurou no dispositivo, chame `GetCalendars` `EventStore` no e passe o tipo de calendários (lembretes ou eventos) que você deseja receber:

```csharp
EKCalendar[] calendars = 
App.Current.EventStore.GetCalendars ( EKEntityType.Event );
```

### <a name="add-or-modify-an-event-using-the-built-in-controller"></a>Adicionar ou modificar um evento usando o controlador interno

O *EKEventEditViewController* faz grande parte do trabalho pesado para você se você quiser criar ou editar um evento com a mesma interface do usuário que é apresentada a ele ao usar o aplicativo de calendário:

 [![](eventkit-images/02.png "A interface de usuário que é apresentada ao usuário ao usar o aplicativo de calendário")](eventkit-images/02.png#lightbox)

Para usá-lo, você desejará declará-lo como uma variável de nível de classe para que ele não obtenha coleta de lixo se ele for declarado dentro de um método:

```csharp
public class HomeController : DialogViewController
{
        protected CreateEventEditViewDelegate eventControllerDelegate;
        ...
}
```

Em seguida, para iniciá-lo: instanciá-lo, dar a `EventStore`ele uma referência ao, conectar um delegado *EKEventEditViewDelegate* a ele e, em `PresentViewController`seguida, exibi-lo usando:

```csharp
EventKitUI.EKEventEditViewController eventController = 
        new EventKitUI.EKEventEditViewController ();

// set the controller's event store - it needs to know where/how to save the event
eventController.EventStore = App.Current.EventStore;

// wire up a delegate to handle events from the controller
eventControllerDelegate = new CreateEventEditViewDelegate ( eventController );
eventController.EditViewDelegate = eventControllerDelegate;

// show the event controller
PresentViewController ( eventController, true, null );
```

Opcionalmente, se você quiser preencher previamente o evento, você pode criar um evento novo (como mostrado abaixo) ou recuperar um evento salvo:

```csharp
EKEvent newEvent = EKEvent.FromStore ( App.Current.EventStore );
// set the alarm for 10 minutes from now
newEvent.AddAlarm ( EKAlarm.FromDate ( DateTime.Now.AddMinutes ( 10 ) ) );
// make the event start 20 minutes from now and last 30 minutes
newEvent.StartDate = DateTime.Now.AddMinutes ( 20 );
newEvent.EndDate = DateTime.Now.AddMinutes ( 50 );
newEvent.Title = "Get outside and exercise!";
newEvent.Notes = "This is your reminder to go and exercise for 30 minutes.”;
```

Se você quiser preencher previamente a interface do usuário, certifique-se de definir a propriedade de evento no controlador:

```csharp
eventController.Event = newEvent;
```

Para usar um evento existente, consulte a seção *recuperar um evento por ID* posteriormente.

O delegado deve substituir o `Completed` método, que é chamado pelo controlador quando o usuário termina com a caixa de diálogo:

```csharp
protected class CreateEventEditViewDelegate : EventKitUI.EKEventEditViewDelegate
{
        // we need to keep a reference to the controller so we can dismiss it
        protected EventKitUI.EKEventEditViewController eventController;

        public CreateEventEditViewDelegate (EventKitUI.EKEventEditViewController eventController)
        {
                // save our controller reference
                this.eventController = eventController;
        }

        // completed is called when a user eith
        public override void Completed (EventKitUI.EKEventEditViewController controller, EKEventEditViewAction action)
        {
                eventController.DismissViewController (true, null);
                }
        }
}
```

Opcionalmente, no delegado, você pode verificar a *ação* no `Completed` método para modificar o evento e salvar novamente, ou fazer outras coisas, se ele for cancelado, etc:

```csharp
public override void Completed (EventKitUI.EKEventEditViewController controller, EKEventEditViewAction action)
{
        eventController.DismissViewController (true, null);

        switch ( action ) {

        case EKEventEditViewAction.Canceled:
                break;
        case EKEventEditViewAction.Deleted:
                break;
        case EKEventEditViewAction.Saved:
                // if you wanted to modify the event you could do so here,
// and then save:
                //App.Current.EventStore.SaveEvent ( controller.Event, )
                break;
        }
}
```

### <a name="creating-an-event-programmatically"></a>Criando um evento programaticamente

Para criar um evento no código, use o método de fábrica *FromStore* na `EKEvent` classe e defina todos os dados nele:

```csharp
EKEvent newEvent = EKEvent.FromStore ( App.Current.EventStore );
// set the alarm for 10 minutes from now
newEvent.AddAlarm ( EKAlarm.FromDate ( DateTime.Now.AddMinutes ( 10 ) ) );
// make the event start 20 minutes from now and last 30 minutes
newEvent.StartDate = DateTime.Now.AddMinutes ( 20 );
newEvent.EndDate = DateTime.Now.AddMinutes ( 50 );
newEvent.Title = "Get outside and do some exercise!";
newEvent.Notes = "This is your motivational event to go and do 30 minutes of exercise. Super important. Do this.";
```

Você deve definir o calendário no qual deseja salvar o evento, mas, se não tiver preferência, poderá usar o padrão:

```csharp
newEvent.Calendar = App.Current.EventStore.DefaultCalendarForNewEvents;
```

Para salvar o evento, chame o método *SaveEvent* no `EventStore`:

```csharp
NSError e;
App.Current.EventStore.SaveEvent ( newEvent, EKSpan.ThisEvent, out e );
```

Depois de salvo, a propriedade *EventIdentifier* será atualizada com um identificador exclusivo que pode ser usado posteriormente para recuperar o evento:

```csharp
Console.WriteLine ("Event Saved, ID: " + newEvent.CalendarItemIdentifier);
```

 `EventIdentifier`é um GUID formatado de cadeia de caracteres.

### <a name="create-a-reminder-programmatically"></a>Criar um lembrete programaticamente

Criar um lembrete no código é muito semelhante a criar um evento de calendário:

```csharp
EKReminder reminder = EKReminder.Create ( App.Current.EventStore );
reminder.Title = "Do something awesome!";
reminder.Calendar = App.Current.EventStore.DefaultCalendarForNewReminders;
```

Para salvar, chame o método *SaveReminder* no `EventStore`:

```csharp
NSError e;
App.Current.EventStore.SaveReminder ( reminder, true, out e );
```

### <a name="retrieving-an-event-by-id"></a>Recuperando um evento por ID

Para recuperar um evento por sua ID, use o método *EventFromIdentifier* no `EventStore` e passe-o `EventIdentifier` que foi extraído do evento:

```csharp
EKEvent mySavedEvent = App.Current.EventStore.EventFromIdentifier ( newEvent.EventIdentifier );
```

Para eventos, há duas outras propriedades de identificador, mas `EventIdentifier` é a única que funciona para isso.

### <a name="retrieving-a-reminder-by-id"></a>Recuperando um lembrete por ID

Para recuperar um lembrete, use o método *GetCalendarItem* no `EventStore` e passe-o de *CalendarItemIdentifier*:

```csharp
EKCalendarItem myReminder = App.Current.EventStore.GetCalendarItem ( reminder.CalendarItemIdentifier );
```

Como `GetCalendarItem` retorna um `EKCalendarItem`, ele deve ser convertido para `EKReminder` se você precisar acessar dados de lembrete ou usar a instância como `EKReminder` um mais tarde.

Não use `GetCalendarItem` para eventos de calendário, como no momento da gravação, ele não funciona.

### <a name="deleting-an-event"></a>Excluindo um evento

Para excluir um evento de calendário, chame *RemoveEvent* no `EventStore` seu e passe uma referência para o evento e o apropriado `EKSpan`:

```csharp
NSError e;
App.Current.EventStore.RemoveEvent ( mySavedEvent, EKSpan.ThisEvent, true, out e);
```

No entanto, observe que, depois que um evento tiver sido excluído, `null`a referência do evento será.

### <a name="deleting-a-reminder"></a>Excluindo um lembrete

Para excluir um lembrete, chame *RemoveReminder* no `EventStore` e passe uma referência para o lembrete:

```csharp
NSError e;
App.Current.EventStore.RemoveReminder ( myReminder as EKReminder, true, out e);
```

Observe que, no código acima, há uma conversão para `EKReminder`, porque `GetCalendarItem` foi usado para recuperá-lo

### <a name="searching-for-events"></a>Procurando eventos

Para pesquisar eventos de calendário, você deve criar um objeto *NSPredicate* por meio do `EventStore`método PredicateForEvents no. Um `NSPredicate` é um objeto de dados de consulta que o IOS usa para localizar correspondências:

```csharp
DateTime startDate = DateTime.Now.AddDays ( -7 );
DateTime endDate = DateTime.Now;
// the third parameter is calendars we want to look in, to use all calendars, we pass null
NSPredicate query = App.Current.EventStore.PredicateForEvents ( startDate, endDate, null );
```

Depois de criar o `NSPredicate`, use o método *EventsMatching* no: `EventStore`

```csharp
// execute the query
EKCalendarItem[] events = App.Current.EventStore.EventsMatching ( query );
```

Observe que as consultas são síncronas (bloqueio) e podem demorar muito tempo, dependendo da consulta, para que você possa desejar criar um novo thread ou tarefa para fazer isso.

### <a name="searching-for-reminders"></a>Procurando lembretes

Procurar lembretes é semelhante a eventos; Ele requer um predicado, mas a chamada já é assíncrona, portanto, você não precisa se preocupar em bloquear o thread:

```csharp
// create our NSPredicate which we'll use for the query
NSPredicate query = App.Current.EventStore.PredicateForReminders ( null );

// execute the query
App.Current.EventStore.FetchReminders (
        query, ( EKReminder[] items ) => {
                // do someting with the items
        } );
```

## <a name="summary"></a>Resumo

Este documento forneceu uma visão geral das partes importantes da estrutura kit e várias das tarefas mais comuns. No entanto, a estrutura kit é muito grande e poderosa e inclui recursos que não foram introduzidos aqui, como: atualizações em lote, configuração de alarmes, configuração de recorrência de eventos, registro e escuta de alterações no banco de dados de calendário, definir limites geográficos e muito mais.  Para obter mais informações, consulte o [Guia de programação de calendário e lembretes](https://developer.apple.com/library/prerelease/ios/#documentation/DataManagement/Conceptual/EventKitProgGuide/Introduction/Introduction.html)da Apple.


## <a name="related-links"></a>Links relacionados

- [Calendários (exemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/calendars)
- [Introdução ao iOS 6](~/ios/platform/introduction-to-ios6/index.md)
- [Introdução aos calendários e lembretes](https://developer.apple.com/library/prerelease/ios/#documentation/DataManagement/Conceptual/EventKitProgGuide/Introduction/Introduction.html)
