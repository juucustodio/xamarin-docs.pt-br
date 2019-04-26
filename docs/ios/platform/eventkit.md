---
title: Kit de eventos no xamarin. IOS
description: Este documento descreve o Kit de eventos e como usá-la no xamarin. IOS. Ele aborda os calendários, eventos de calendário e lembretes, examina as classes comumente usadas durante a programação com o Kit de eventos e muito mais.
ms.prod: xamarin
ms.assetid: 00E88629-357D-1FCD-4FCE-1330D5D9D32C
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: ea03c8b382e2de29bd20ab1d696d7abb7733e182
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61369218"
---
# <a name="eventkit-in-xamarinios"></a>Kit de eventos no xamarin. IOS

iOS tem dois aplicativos relacionados ao calendário internos: o aplicativo de calendário e o aplicativo de lembretes. É simples o suficiente entender como o aplicativo de calendário gerencia dados de calendário, mas o aplicativo de lembretes é menos óbvio. Lembretes, na verdade, podem ter datas associadas a eles em termos de quando eles são devido, quando estiver concluídas, etc. Como tal, o iOS armazena todos os dados de calendário, seja em eventos de calendário ou lembretes, em um único local, chamado de *banco de dados de calendário*.

A estrutura do Kit de eventos fornece uma maneira de acessar o *calendários*, *eventos de calendário*, e *lembretes* dados que armazena o banco de dados de calendário. Acesso ao calendário e calendários de eventos está disponível desde o iOS 4, mas o acesso lembretes é novo no iOS 6.

Neste guia, vamos abordar:

-   **Noções básicas do Kit de eventos** – isso apresentará as partes fundamentais do Kit de eventos por meio das classes principais e fornece uma compreensão do seu uso. Esta seção é necessária ler antes de lidar com a próxima parte do documento. 
-   **Tarefas comuns de** – a seção de tarefas comuns se destina a ser uma referência rápida sobre como realizar tarefas comuns como; enumerando calendários, criando, salvando e recuperando calendário de eventos e lembretes, bem como usando os controladores internos para Criando e modificando eventos de calendário. Esta seção não precisarão ler de frente para trás, pois ele foi projetado para ser uma referência para tarefas específicas. 


Todas as tarefas neste guia estão disponíveis no aplicativo de exemplo complementar:

 [![](eventkit-images/01.png "As telas do aplicativo de exemplo complementar")](eventkit-images/01.png#lightbox)

## <a name="requirements"></a>Requisitos

Kit de eventos foi introduzido no iOS 4.0, mas o acesso aos dados de lembretes foi introduzido no iOS 6.0. Assim, para fazer desenvolvimento geral do Kit de eventos, você precisará de destino pelo menos versão 4.0 e 6.0 para lembretes.

Além disso, o aplicativo de lembretes não está disponível no simulador, que significa que os dados de lembretes também não estará disponíveis, a menos que você adicioná-los pela primeira vez. Além disso, as solicitações de acesso só são mostradas ao usuário no dispositivo real. Como tal, desenvolvimento de kit de eventos é mais bem testado no dispositivo.

## <a name="event-kit-basics"></a>Noções básicas sobre o Kit de evento

Ao trabalhar com o Kit de eventos, é importante ter uma melhor compreensão das classes comuns e seu uso. Todas essas classes podem ser encontradas na `EventKit` e `EventKitUI` (para o `EKEventEditController`).

### <a name="eventstore"></a>EventStore

O *EventStore* é a classe mais importante no Kit de eventos porque ele é necessário realizar nenhuma operação no Kit de eventos. Ele pode ser pensado como o armazenamento persistente, ou o mecanismo de banco de dados, para todos os dados do Kit de eventos. De `EventStore` você tem acesso aos calendários e eventos de calendário no aplicativo Calendar, bem como lembretes no aplicativo lembretes.

Porque `EventStore` é como um mecanismo de banco de dados, ele deve ser longa duração, o que significa que devem ser criado e destruído mínimo possível durante o tempo de vida de uma instância do aplicativo. Na verdade, é recomendável que, depois de criar uma instância de um `EventStore` em um aplicativo, você mantenha essa referência ao redor para todo o tempo de vida do aplicativo, a menos que você tiver certeza, você não precisará dela novamente. Além disso, todas as chamadas devem ir para um único `EventStore` instância. Por esse motivo, o padrão Singleton é recomendado para manter uma única instância disponível.

#### <a name="creating-an-event-store"></a>Criação de um evento Store

O código a seguir ilustra uma maneira eficiente de criar uma única instância da `EventStore` de classe e disponibilizá-lo estaticamente em dentro de um aplicativo:

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

O código acima usa o padrão Singleton para instanciar uma instância da `EventStore` quando o aplicativo é carregado. O `EventStore` pode ser acessado globalmente de dentro do aplicativo da seguinte maneira:

```csharp
App.Current.EventStore;
```

Observe que todos os exemplos aqui usam esse padrão, para que eles fazem referência a `EventStore` via `App.Current.EventStore`.

#### <a name="requesting-access-to-calendar-and-reminder-data"></a>Solicitando acesso ao calendário e a Data de lembrete

Antes de terem permissão para acessar quaisquer dados por meio de EventStore, um aplicativo deve primeiro solicitar acesso a dados de eventos de calendário ou dados de lembretes, dependendo de qual deles você precisa. Para facilitar isso, o `EventStore` expõe um método chamado `RequestAccess` que — quando chamado — mostrará uma exibição de alerta para o usuário informando que o aplicativo está solicitando acesso a dados de calendário ou data de lembrete, dependendo de qual `EKEntityType`é passado para ele. Porque ele gera uma exibição de alerta, a chamada é assíncrona e chamará um manipulador de conclusão passado como um `NSAction` (ou Lambda) a ele que recebe dois parâmetros; um valor booliano de ou não foi concedido acesso e um `NSError`, que, se não null será contém informações de erro na solicitação. Por exemplo, o seguinte código solicita acesso a dados de evento de calendário e mostrar um alerta exibir se a solicitação não foi concedida.

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

Depois que a solicitação tiver sido dado, ele será lembrado, desde que o aplicativo está instalado no dispositivo e um alerta para o usuário não será exibida.
No entanto, o acesso é concedido somente ao tipo de recurso, eventos de calendário ou lembretes concedidas. Se um aplicativo precisa de acesso a ambos, ele deve solicitar ambos.

Porque a permissão será lembrada, é relativamente barato fazer a solicitação de cada vez, portanto, é uma boa ideia sempre solicitar acesso antes de executar uma operação.

Além disso, como o manipulador de conclusão é chamado em um thread separado (sem interface de usuário), todas as atualizações na interface do usuário no manipulador de conclusão devem ser chamadas por meio de `InvokeOnMainThread`, caso contrário, uma exceção será gerada e se não detectada, o aplicativo falhará.

### <a name="ekentitytype"></a>EKEntityType

`EKEntityType` é uma enumeração que descreve o tipo de `EventKit` dados ou item. Ele tem dois valores: `Event` e lembrete. Ele é usado em uma série de métodos, incluindo `EventStore.RequestAccess` para informar ao `EventKit` o tipo de dados para obter acesso ao ou recuperar.

### <a name="ekcalendar"></a>EKCalendar

 *EKCalendar* representa um calendário, que contém um grupo de eventos de calendário. Calendários podem ser armazenados em vários lugares diferentes, como localmente, no *iCloud*, em uma 3ª local do provedor de terceiros, como um *Exchange Server* ou *Google*, etc. Muitas vezes `EKCalendar` é usada para informar `EventKit` onde procurar eventos ou onde salvá-los.

### <a name="ekeventeditcontroller"></a>EKEventEditController

 *EKEventEditController* podem ser encontradas no `EventKitUI` namespace e é um controlador interno que pode ser usado para editar ou criar eventos de calendário. Assim como na compilação em controladores de câmera, `EKEventEditController` faz o trabalho pesado para você no exibindo a interface do usuário e tratamento de salvar.

### <a name="ekevent"></a>EKEvent

 *EKEvent* representa um evento de calendário. Ambos `EKEvent` e `EKReminder` herdar `EKCalendarItem` e ter campos como `Title`, `Notes`e assim por diante.

### <a name="ekreminder"></a>EKReminder

 *EKReminder* representa um item de lembrete.

### <a name="ekspan"></a>EKSpan

*EKSpan* é uma enumeração que descreve o alcance de eventos durante a modificação de eventos que podem se repetir e tem dois valores: *ThisEvent* e *FutureEvents*. `ThisEvent` significa que todas as alterações somente ocorrerá para o evento específico na série que está sendo referenciado, enquanto `FutureEvents` afetará desse evento e todas as recorrências futuras.

## <a name="tasks"></a>Tarefas

Para facilidade de uso, uso do Kit de eventos foi dividido em tarefas comuns, descritas nas seções a seguir.

### <a name="enumerate-calendars"></a>Enumerar calendários

Para enumerar os calendários que o usuário tiver configurado no dispositivo, chame `GetCalendars` sobre o `EventStore` e passe o tipo de calendários (lembretes ou eventos) que você deseja receber:

```csharp
EKCalendar[] calendars = 
App.Current.EventStore.GetCalendars ( EKEntityType.Event );
```

### <a name="add-or-modify-an-event-using-the-built-in-controller"></a>Adicionar ou modificar um evento usando o controlador interno

O *EKEventEditViewController* faz grande parte do trabalho pesado para você, se você quiser criar ou editar um evento com a mesma interface do usuário é apresentado ao usuário ao usar o aplicativo de calendário:

 [![](eventkit-images/02.png "A interface do usuário que é apresentado ao usuário ao usar o aplicativo de calendário")](eventkit-images/02.png#lightbox)

Para usá-lo, você deve declará-lo como uma variável de nível de classe para que ele não fique coletado como lixo se ela for declarada dentro de um método:

```csharp
public class HomeController : DialogViewController
{
        protected CreateEventEditViewDelegate eventControllerDelegate;
        ...
}
```

Em seguida, iniciá-lo: instanciá-lo, dê a ele uma referência para o `EventStore`, conectar um *EKEventEditViewDelegate* delegar a ele e, em seguida, exibi-lo usando `PresentViewController`:

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

Opcionalmente, se você quiser preencher previamente o evento, você pode criar um novo evento (conforme mostrado abaixo), ou você pode recuperar um evento salvo:

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

Para usar um evento existente, consulte o *recuperação de um evento por ID* seção mais adiante.

O delegado deve substituir o `Completed` método, que é chamado pelo controlador de quando o usuário é concluído com a caixa de diálogo:

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

Opcionalmente, o delegado, você pode verificar a *ação* no `Completed` método para modificar o evento e salvá-la novamente, ou fazer outras coisas, se ele for cancelado, ou outras:

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

Para criar um evento no código, use o *FromStore* método alocador no `EKEvent` de classe e defina todos os dados nele:

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

Você deve definir o calendário que você deseja que o evento salvo no, mas se você não tiver nenhuma preferência, você pode usar o padrão:

```csharp
newEvent.Calendar = App.Current.EventStore.DefaultCalendarForNewEvents;
```

Para salvar o evento, chame o *SaveEvent* método o `EventStore`:

```csharp
NSError e;
App.Current.EventStore.SaveEvent ( newEvent, EKSpan.ThisEvent, out e );
```

Depois que ele é salvo, o *EventIdentifier* propriedade será atualizada com um identificador exclusivo que pode ser usado posteriormente para recuperar o evento:

```csharp
Console.WriteLine ("Event Saved, ID: " + newEvent.CalendarItemIdentifier);
```

 `EventIdentifier` é que uma cadeia de caracteres formatada GUID.

### <a name="create-a-reminder-programmatically"></a>Criar um lembrete de forma programática

Criando um lembrete no código é muito semelhante a criação de um evento de calendário:

```csharp
EKReminder reminder = EKReminder.Create ( App.Current.EventStore );
reminder.Title = "Do something awesome!";
reminder.Calendar = App.Current.EventStore.DefaultCalendarForNewReminders;
```

Para salvar, chame o *SaveReminder* método o `EventStore`:

```csharp
NSError e;
App.Current.EventStore.SaveReminder ( reminder, true, out e );
```

### <a name="retrieving-an-event-by-id"></a>Recuperando um evento por ID

Para recuperar um evento, ele é o ID, use o *EventFromIdentifier* método o `EventStore` e passá-lo a `EventIdentifier` que foi obtido a partir do evento:

```csharp
EKEvent mySavedEvent = App.Current.EventStore.EventFromIdentifier ( newEvent.EventIdentifier );
```

Para eventos, há são duas outras propriedades do identificador, mas `EventIdentifier` é o único que funciona para isso.

### <a name="retrieving-a-reminder-by-id"></a>Recuperando um lembrete por ID

Para recuperar um lembrete, use o *GetCalendarItem* método sobre o `EventStore` e passá-lo a *CalendarItemIdentifier*:

```csharp
EKCalendarItem myReminder = App.Current.EventStore.GetCalendarItem ( reminder.CalendarItemIdentifier );
```

Porque `GetCalendarItem` retorna um `EKCalendarItem`, ele deve ser convertido em `EKReminder` se você precisa acessar os dados de lembrete ou usar a instância como um `EKReminder` mais tarde.

Não use `GetCalendarItem` para eventos de calendário, como no momento da gravação, ele não funciona.

### <a name="deleting-an-event"></a>Excluindo um evento

Para excluir um evento de calendário, chame *RemoveEvent* no seu `EventStore` e passar uma referência para o evento e apropriado `EKSpan`:

```csharp
NSError e;
App.Current.EventStore.RemoveEvent ( mySavedEvent, EKSpan.ThisEvent, true, out e);
```

Observe, no entanto, depois que um evento foi excluído, a referência de eventos será `null`.

### <a name="deleting-a-reminder"></a>Excluir um lembrete

Para excluir um lembrete, chame *RemoveReminder* sobre o `EventStore` e passar uma referência para o lembrete:

```csharp
NSError e;
App.Current.EventStore.RemoveReminder ( myReminder as EKReminder, true, out e);
```

Observe que, no código acima, há uma conversão `EKReminder`, pois `GetCalendarItem` foi usado para recuperá-la

### <a name="searching-for-events"></a>Procurando eventos

Para procurar eventos de calendário, você deve criar uma *NSPredicate* por meio do objeto de *PredicateForEvents* método no `EventStore`. Um `NSPredicate` é uma consulta se iOS usa para localizar correspondências de objeto de dados:

```csharp
DateTime startDate = DateTime.Now.AddDays ( -7 );
DateTime endDate = DateTime.Now;
// the third parameter is calendars we want to look in, to use all calendars, we pass null
NSPredicate query = App.Current.EventStore.PredicateForEvents ( startDate, endDate, null );
```

Depois de criar o `NSPredicate`, use o *EventsMatching* método no `EventStore`:

```csharp
// execute the query
EKCalendarItem[] events = App.Current.EventStore.EventsMatching ( query );
```

Observe que as consultas são síncronas (bloqueio) e podem levar muito tempo, dependendo da consulta, portanto, talvez você queira criar um novo thread ou tarefa fazê-lo.

### <a name="searching-for-reminders"></a>Procurando lembretes

Procurando lembretes é semelhante aos eventos; ele requer um predicado, mas a chamada já é assíncrona, portanto, você não precisa se preocupar sobre bloquear o thread:

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

Este documento forneceu uma visão geral de ambas as partes importantes do framework Kit de eventos e um número das tarefas mais comuns. No entanto, a estrutura do Kit de eventos é muito grande e poderoso e inclui recursos que ainda não foram introduzidos aqui, tais como: atualizações, configurando alarmes, configurando a recorrência em eventos, registrando e escuta de alterações ao banco de dados do calendário do lote definindo limites geográficos e muito mais.  Para obter mais informações, consulte da Apple [guia de programação de lembretes e de calendário](https://developer.apple.com/library/prerelease/ios/#documentation/DataManagement/Conceptual/EventKitProgGuide/Introduction/Introduction.html).


## <a name="related-links"></a>Links relacionados

- [Calendários (amostra)](https://developer.xamarin.com/samples/monotouch/Calendars/)
- [Introdução ao iOS 6](~/ios/platform/introduction-to-ios6/index.md)
- [Introdução aos calendários e lembretes](https://developer.apple.com/library/prerelease/ios/#documentation/DataManagement/Conceptual/EventKitProgGuide/Introduction/Introduction.html)
