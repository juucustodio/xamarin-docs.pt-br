---
title: EventKit
description: "Este guia fornece uma visão geral sobre como acessar e trabalhar com dados de calendários, CalendarEvents e lembretes armazenados no banco de dados de calendário, como exposto por meio de EventKit. Ele aborda as principais classes e suas funções na programação EventKit, bem como um número de tarefas comuns associadas com o framework EventKit."
ms.topic: article
ms.prod: xamarin
ms.assetid: 00E88629-357D-1FCD-4FCE-1330D5D9D32C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: a08bc67a9af653a9a646ad62071df0400ce58c12
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="eventkit"></a>EventKit

_Este guia fornece uma visão geral sobre como acessar e trabalhar com dados de calendários, CalendarEvents e lembretes armazenados no banco de dados de calendário, como exposto por meio de EventKit. Ele aborda as principais classes e suas funções na programação EventKit, bem como um número de tarefas comuns associadas com o framework EventKit._

iOS tem dois aplicativos relacionados ao calendário internos: o aplicativo de calendário e o aplicativo de lembretes. É simples o suficiente entender como o aplicativo de calendário gerencia dados de calendário, mas o aplicativo de lembretes é menos óbvio. Lembretes podem ter datas associadas a eles em termos de quando elas foram devido quando estiver concluídas, etc. Como tal, o iOS armazena todos os dados de calendário, seja em eventos de calendário ou lembretes, em um local, chamado de *banco de dados de calendário*.

A estrutura EventKit fornece uma maneira de acessar o *calendários*, *eventos de calendário*, e *lembretes* dados que armazena o banco de dados do calendário. Acesso a calendários e calendário de eventos está disponível desde o iOS 4, mas o acesso lembretes é novo no iOS 6.

Este guia, vamos abordar:

-   **Noções básicas de EventKit** – isso apresentará as partes fundamentais da EventKit por meio de classes principais e oferece uma compreensão de sua utilização. Esta seção é necessária ler antes de lidar com a próxima parte do documento. 
-   **Tarefas comuns de** – a seção de tarefas comuns deve ser uma referência rápida sobre como realizar tarefas comuns como; enumerando calendários, criando, salvando e recuperando calendário eventos e lembretes, bem como usando os controladores internos para Criando e modificando eventos de calendário. Esta seção não precisa ler frente para trás, pois ele tem deve ser uma referência para tarefas específicas. 


Todas as tarefas neste guia estão disponíveis no aplicativo de amostra complementar:

 [![](eventkit-images/01.png "As telas de aplicativo de exemplo complementar")](eventkit-images/01.png#lightbox)

## <a name="requirements"></a>Requisitos

EventKit foi introduzido no iOS 4.0, mas o acesso aos dados de lembretes foi introduzido no iOS 6.0. Assim, para fazer o desenvolvimento de EventKit geral, você precisará de destino pelo menos versão 4.0 e 6.0 para lembretes.

Além disso, o aplicativo lembretes não está disponível no simulador, o que significa que os dados de lembretes também não estará disponíveis, a menos que você adicionar primeiro. Além disso, as solicitações de acesso somente são mostradas ao usuário no dispositivo real. Como tal, desenvolvimento EventKit melhor é testado no dispositivo.

## <a name="event-kit-basics"></a>Noções básicas sobre o Kit de evento

Ao trabalhar com EventKit, é importante ter um entendimento das classes comuns e seu uso. Todas essas classes podem ser encontradas no `EventKit` e `EventKitUI` (para o `EKEventEditController`).

### <a name="eventstore"></a>EventStore

O *EventStore* classe é a classe mais importante no EventKit porque ele é necessário para executar operações em EventKit. Ele pode ser considerado como o mecanismo de banco de dados, para todos os dados de EventKit ou o armazenamento persistente. De `EventStore` você tem acesso aos calendários e eventos de calendário no aplicativo de calendário, bem como lembretes no aplicativo lembretes.

Porque `EventStore` é como um mecanismo de banco de dados, ele deve ser vida longa, o que significa que devem ser criado e destruído mínimo possível durante o tempo de vida de uma instância de aplicativo. Na verdade, é recomendável que depois que você criar uma instância de um `EventStore` em um aplicativo, você manter que fazem referência ao redor de todo o tempo de vida do aplicativo, se você não precisa dele novamente. Além disso, todas as chamadas devem ir para um único `EventStore` instância. Por esse motivo, o padrão de Singleton é recomendado para manter uma única instância disponível.

#### <a name="creating-an-event-store"></a>Criando um repositório de eventos

O código a seguir ilustra uma maneira eficiente de criar uma única instância de `EventStore` classe e disponibilizá-lo estaticamente de dentro de um aplicativo:

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

O código acima usa o padrão de Singleton para criar uma instância do `EventStore` quando o aplicativo é carregado. O `EventStore` possam ser acessados globalmente de dentro do aplicativo da seguinte maneira:

```csharp
App.Current.EventStore;
```

Observe que todos os exemplos aqui usam esse padrão, para que eles fazem referência a `EventStore` via `App.Current.EventStore`.

#### <a name="requesting-access-to-calendar-and-reminder-data"></a>Solicitando acesso ao calendário e dados de lembrete

Antes de poder acessar os dados por meio de EventStore, um aplicativo deve primeiramente solicitar acesso ao dados de eventos de calendário ou dados lembretes, dependendo de qual delas você precisa. Para facilitar isso, o `EventStore` expõe um método chamado `RequestAccess` que, quando chamado — mostra uma exibição de alerta para o usuário informando que o aplicativo está solicitando acesso a dados de calendário ou dados de lembrete, dependendo de qual `EKEntityType`é passada para ele. Porque ele gera uma exibição de alerta, a chamada é assíncrona e chamará um manipulador de conclusão passado como um `NSAction` (ou Lambda) a ele que receberá dois parâmetros; um valor booleano de estarem ou não foi concedido acesso e um `NSError`, que, se não null contém informações de erro na solicitação. Por exemplo, a código a seguir solicita acesso a dados de evento de calendário e mostrar um alerta exibir se a solicitação não foi concedida.

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

Depois que recebeu a solicitação, ele será lembrado enquanto o aplicativo está instalado no dispositivo e um alerta para o usuário não será exibida.
No entanto, o acesso é fornecido apenas para o tipo de recurso, eventos de calendário ou lembretes concedidas. Se um aplicativo precisa acessar ambos, ele deve solicitar ambos.

Como a permissão será lembrada, é relativamente barata fazer a solicitação de cada vez, portanto, é uma boa ideia sempre solicitar acesso antes de executar uma operação.

Além disso, como o processador de conclusão é chamado em um thread separado (sem interface do usuário), todas as atualizações para a interface do usuário no manipulador de conclusão devem ser chamadas por meio de `InvokeOnMainThread`, caso contrário, uma exceção será lançada e se não detectada, o aplicativo falhará.

### <a name="ekentitytype"></a>EKEntityType

`EKEntityType` é uma enumeração que descreve o tipo de `EventKit` dados ou item. Ele tem dois valores: `Event` e lembretes. Ele é usado em um número de métodos, incluindo `EventStore.RequestAccess` para informar `EventKit` o tipo de dados para obter acesso ao ou recuperar.

### <a name="ekcalendar"></a>EKCalendar

 *EKCalendar* representa um calendário, que contém um grupo de eventos de calendário. Calendários podem ser armazenados em vários lugares diferentes, como localmente, no *iCloud*, em um 3ª parte local do provedor, como um *do Exchange Server* ou *Google*, etc. Muitas vezes `EKCalendar` é usado para informar `EventKit` onde procurar eventos, ou onde salvá-los.

### <a name="ekeventeditcontroller"></a>EKEventEditController

 *EKEventEditController* pode ser encontrado no `EventKitUI` namespace e é um controlador interno que pode ser usado para editar ou criar eventos de calendário. Assim como interna em controladores de câmera `EKEventEditController` faz o trabalho pesado por você na exibição da interface do usuário e tratamento de salvar.

### <a name="ekevent"></a>EKEvent

 *EKEvent* representa um evento de calendário. Ambos `EKEvent` e `EKReminder` herdam `EKCalendarItem` e ter campos como `Title`, `Notes`e assim por diante.

### <a name="ekreminder"></a>EKReminder

 *EKReminder* representa um item de lembrete.

### <a name="ekspan"></a>EKSpan

*EKSpan* é uma enumeração que descreve o alcance de eventos ao modificar os eventos que podem se repetir e tem dois valores: *ThisEvent* e *FutureEvents*. `ThisEvent` significa que as alterações somente ocorrerá para o evento específico na série que está sendo referenciada, enquanto `FutureEvents` afetará desse evento e todas as recorrências futuras.

## <a name="tasks"></a>Tarefas

Para facilidade de uso, uso de EventKit foi dividido em tarefas comuns, como descritas nas seções a seguir.

### <a name="enumerate-calendars"></a>Enumerar calendários

Para enumerar os calendários que o usuário configurado no dispositivo, chame `GetCalendars` no `EventStore` e passe o tipo de calendários (lembretes ou eventos) que você deseja receber:

```csharp
EKCalendar[] calendars = 
App.Current.EventStore.GetCalendars ( EKEntityType.Event );
```

### <a name="add-or-modify-an-event-using-the-built-in-controller"></a>Adicionar ou modificar um evento usando o controlador interno

O *EKEventEditViewController* faz muita o trabalho pesado por você, se você deseja criar ou editar um evento com a mesma interface do usuário é apresentada ao usuário ao usar o aplicativo de calendário:

 [![](eventkit-images/02.png "A interface do usuário que é apresentado ao usuário usando o aplicativo de calendário")](eventkit-images/02.png#lightbox)

Para usá-lo, você desejará declará-la como uma variável de nível de classe para que nada é coletado como lixo se ela é declarada dentro de um método:

```csharp
public class HomeController : DialogViewController
{
        protected CreateEventEditViewDelegate eventControllerDelegate;
        ...
}
```

Em seguida, iniciá-lo: instanciá-la, dê a ele uma referência para o `EventStore`, conectar um *EKEventEditViewDelegate* delegar a ele e, em seguida, exibi-los usando `PresentViewController`:

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

Opcionalmente, se você deseja preencher previamente o evento, você pode criar um novo evento (conforme mostrado abaixo), ou você pode recuperar um evento salvo:

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

Se você deseja preencher previamente a interface do usuário, certifique-se de definir a propriedade de evento no controlador:

```csharp
eventController.Event = newEvent;
```

Para usar um evento existente, consulte o *recuperar um evento por ID* seção posteriormente.

O representante deve substituir o `Completed` método, que é chamado pelo controlador de quando o usuário for concluído com a caixa de diálogo:

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

Opcionalmente, o delegado, você pode verificar o *ação* no `Completed` método para modificar o evento e salvá-la novamente, ou fazer outras coisas, se ele for cancelado, ou outras:

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

### <a name="creating-an-event-programmatically"></a>Criar um evento de forma programática

Para criar um evento no código, use o *FromStore* método de fábrica no `EKEvent` classe e definir quaisquer dados nela:

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

Você deve definir o calendário que deseja que o evento salvos no, mas se você não tiver nenhuma preferência, você pode usar o padrão:

```csharp
newEvent.Calendar = App.Current.EventStore.DefaultCalendarForNewEvents;
```

Para salvar o evento, chame o *SaveEvent* método sobre o `EventStore`:

```csharp
NSError e;
App.Current.EventStore.SaveEvent ( newEvent, EKSpan.ThisEvent, out e );
```

Depois que ele for salvo, o *EventIdentifier* propriedade será atualizada com um identificador exclusivo que pode ser usado posteriormente para recuperar o evento:

```csharp
Console.WriteLine ("Event Saved, ID: " + newEvent.CalendarItemIdentifier);
```

 `EventIdentifier` é que uma cadeia de caracteres formatada GUID.

### <a name="create-a-reminder-programmatically"></a>Criar um lembrete de forma programática

Criando um lembrete em código é semelhante a criar um evento de calendário:

```csharp
EKReminder reminder = EKReminder.Create ( App.Current.EventStore );
reminder.Title = "Do something awesome!";
reminder.Calendar = App.Current.EventStore.DefaultCalendarForNewReminders;
```

Para salvar, chame o *SaveReminder* método sobre o `EventStore`:

```csharp
NSError e;
App.Current.EventStore.SaveReminder ( reminder, true, out e );
```

### <a name="retrieving-an-event-by-id"></a>Recuperando um evento por ID

Para recuperar um evento, ele é o ID, use o *EventFromIdentifier* método o `EventStore` e passá-lo a `EventIdentifier` que foi obtido a partir do evento:

```csharp
EKEvent mySavedEvent = App.Current.EventStore.EventFromIdentifier ( newEvent.EventIdentifier );
```

Para eventos, há duas outras propriedades de identificador, mas `EventIdentifier` é o único que funciona para isso.

### <a name="retrieving-a-reminder-by-id"></a>Recuperando um lembrete por ID

Para recuperar um lembrete, use o *GetCalendarItem* método o `EventStore` e passá-lo a *CalendarItemIdentifier*:

```csharp
EKCalendarItem myReminder = App.Current.EventStore.GetCalendarItem ( reminder.CalendarItemIdentifier );
```

Porque `GetCalendarItem` retorna um `EKCalendarItem`, ele deve ser convertido em `EKReminder` se você precisa acessar dados de lembrete ou usar a instância como um `EKReminder` mais tarde.

Não use `GetCalendarItem` para eventos de calendário, como no momento da gravação, eles não funcionam.

### <a name="deleting-an-event"></a>Excluir um evento

Para excluir um evento de calendário, chame *RemoveEvent* em seu `EventStore` e passar uma referência para o evento e as `EKSpan`:

```csharp
NSError e;
App.Current.EventStore.RemoveEvent ( mySavedEvent, EKSpan.ThisEvent, true, out e);
```

Observe, no entanto, depois que um evento foi excluído, a referência de evento será `null`.

### <a name="deleting-a-reminder"></a>Excluir um lembrete

Para excluir um lembrete, chame *RemoveReminder* no `EventStore` e passar uma referência para o lembrete:

```csharp
NSError e;
App.Current.EventStore.RemoveReminder ( myReminder as EKReminder, true, out e);
```

Observe que, no código acima, há uma conversão para `EKReminder`, pois `GetCalendarItem` foi usado para recuperá-lo

### <a name="searching-for-events"></a>Pesquisando eventos

Para procurar eventos de calendário, você deve criar um *NSPredicate* objeto por meio de *PredicateForEvents* método no `EventStore`. Um `NSPredicate` é uma consulta que iOS usa para localizar correspondências de objeto de dados:

```csharp
DateTime startDate = DateTime.Now.AddDays ( -7 );
DateTime endDate = DateTime.Now;
// the third parameter is calendars we want to look in, to use all calendars, we pass null
NSPredicate query = App.Current.EventStore.PredicateForEvents ( startDate, endDate, null );
```

Depois de criar o `NSPredicate`, use o *EventsMatching* método o `EventStore`:

```csharp
// execute the query
EKCalendarItem[] events = App.Current.EventStore.EventsMatching ( query );
```

Observe que as consultas são síncronas (bloqueio) e podem levar algum tempo, dependendo da consulta, para que talvez você queira criar um novo thread ou tarefa para fazê-lo.

### <a name="searching-for-reminders"></a>Procurando lembretes

Procurando lembretes é semelhante a eventos. ele requer um predicado, mas a chamada já é assíncrona, portanto você não precisa se preocupar sobre bloquear o thread:

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

Este documento dá uma visão geral de ambas as partes importantes do framework EventKit e um número de tarefas mais comuns. No entanto, a estrutura EventKit é muito grande e poderoso e inclui recursos que ainda não foram apresentados aqui, como: atualizações, configurar alarmes, a configuração de recorrência sobre eventos, registrar e escuta de alterações ao banco de dados do calendário do lote Definindo GeoFences e muito mais.  Para obter mais informações, consulte da Apple [calendário e a guia de programação de lembretes](https://developer.apple.com/library/prerelease/ios/#documentation/DataManagement/Conceptual/EventKitProgGuide/Introduction/Introduction.html).


## <a name="related-links"></a>Links relacionados

- [Calendários (exemplo)](https://developer.xamarin.com/samples/monotouch/Calendars/)
- [Introdução ao iOS 6](~/ios/platform/introduction-to-ios6/index.md)
- [Introdução aos calendários e lembretes](https://developer.apple.com/library/prerelease/ios/#documentation/DataManagement/Conceptual/EventKitProgGuide/Introduction/Introduction.html)
