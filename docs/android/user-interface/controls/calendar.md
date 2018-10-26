---
title: Calendário
ms.prod: xamarin
ms.assetid: 78666541-CA14-4CD8-A94A-A9621C57813E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: d3cba67f27d5b7de7bdcbfa5439061067f759890
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50112520"
---
# <a name="calendar"></a>Calendário


## <a name="calendar-api"></a>API de calendário

Um novo conjunto de APIs do Android 4 do calendário dá suporte a aplicativos que são projetados para ler ou gravar dados para o provedor de calendário. Essas APIs dão suporte a uma ampla gama de opções de interação com os dados de calendário, incluindo a capacidade de ler e gravar eventos, os participantes e lembretes. Usando o provedor de calendário em seu aplicativo, adicionar por meio da API de dados serão exibida no aplicativo do calendário interno que acompanha o Android 4.


## <a name="adding-permissions"></a>Adicionando permissões

Ao trabalhar com o novo calendário APIs em seu aplicativo, a primeira coisa que você precisa fazer é adicionar as permissões apropriadas para o manifesto do Android. As permissões que você precisa adicionar estiverem `android.permisson.READ_CALENDAR` e `android.permission.WRITE_CALENDAR`, dependendo se você estiver lendo e/ou gravação de dados de calendário.


## <a name="using-the-calendar-contract"></a>Usando o contrato de calendário

Depois de definir as permissões, você pode interagir com os dados de calendário usando o `CalendarContract` classe. Essa classe fornece um modelo de dados que os aplicativos podem usar ao interagirem com o provedor de calendário. O `CalendarContract` permite que os aplicativos resolver os Uris para entidades de calendário, como calendários e eventos. Ele também fornece uma maneira de interagir com os diversos campos em cada entidade, como nome e ID, ou iniciar um evento e data de término de um calendário.

Vejamos um exemplo que usa a API Calendar. Neste exemplo, vamos examinar como enumerar os calendários e seus eventos, bem como adicionar um novo evento em um calendário.


## <a name="listing-calendars"></a>Listar calendários

Primeiro, vamos examinar como enumerar os calendários que foram registrados no aplicativo de calendário. Para fazer isso, podemos pode instanciar um `CursorLoader`. Introduzido no Android 3.0 (API 11), `CursorLoader` é a maneira preferencial para consumir um `ContentProvider`. No mínimo, precisamos especificar o Uri de conteúdo de calendários e as colunas que desejamos retornar; Essa especificação de coluna é conhecida como um _projeção_.

Chamar o `CursorLoader.LoadInBackground` método permite consultar um provedor de conteúdo para dados, como o provedor de calendário.
`LoadInBackground` executa a operação de carregamento real e retorna um `Cursor` com os resultados da consulta.

O `CalendarContract` nos especificando o conteúdo ajuda `Uri` e projeção. Para obter o conteúdo `Uri` para consultar calendários, podemos simplesmente usar o `CalendarContract.Calendars.ContentUri` propriedade como este:

```csharp
var calendarsUri = CalendarContract.Calendars.ContentUri;
```

Usando o `CalendarContract` para especificar qual calendário colunas que queremos é igualmente simples. Podemos adicionar apenas campos no `CalendarContract.Calendars.InterfaceConsts` classe para uma matriz. Por exemplo, o código a seguir inclui a ID do calendário, o nome de exibição e nome da conta:

```csharp
string[] calendarsProjection = {
    CalendarContract.Calendars.InterfaceConsts.Id,
    CalendarContract.Calendars.InterfaceConsts.CalendarDisplayName,
    CalendarContract.Calendars.InterfaceConsts.AccountName
};
```

O `Id` é importante incluir se você estiver usando um `SimpleCursorAdapter` para associar os dados na interface do usuário, como veremos daqui a pouco. Com o Uri e projeção no local de conteúdo, criamos uma instância de `CursorLoader` e chamar o `CursorLoader.LoadInBackground` método retorne um cursor com os dados de calendário, conforme mostrado abaixo:

```csharp
var loader = new CursorLoader(this, calendarsUri, calendarsProjection, null, null, null);
var cursor = (ICursor)loader.LoadInBackground();

```

A interface do usuário para este exemplo contém um `ListView`, com cada item na lista que representa um único calendário. O XML a seguir mostra a marcação que inclui o `ListView`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
android:orientation="vertical"
android:layout_width="fill_parent"
android:layout_height="fill_parent">
  <ListView
    android:id="@android:id/android:list"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content" />
</LinearLayout>
```

Além disso, é preciso especificar a interface do usuário para cada item de lista, o que podemos colocar em um arquivo XML separado, da seguinte maneira:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
android:orientation="vertical"
android:layout_width="fill_parent"
android:layout_height="wrap_content">
  <TextView android:id="@+id/calDisplayName"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:textSize="16dip" />
  <TextView android:id="@+id/calAccountName"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:textSize="12dip" />
</LinearLayout>
```

Desse ponto em diante, é normal apenas código do Android para associar os dados a partir do cursor na interface do usuário. Vamos usar um `SimpleCursorAdapter` da seguinte maneira:

```csharp
string[] sourceColumns = {
    CalendarContract.Calendars.InterfaceConsts.CalendarDisplayName,
    CalendarContract.Calendars.InterfaceConsts.AccountName };

int[] targetResources = {
    Resource.Id.calDisplayName, Resource.Id.calAccountName };      

SimpleCursorAdapter adapter = new SimpleCursorAdapter (this,
    Resource.Layout.CalListItem, cursor, sourceColumns, targetResources);

ListAdapter = adapter;
```

No código acima, o adaptador usa as colunas especificadas na `sourceColumns` de matriz e os grava os elementos de interface do usuário no `targetResources` matriz para cada entrada de calendário no cursor. A atividade usada aqui é uma subclasse de `ListActivity`; ele inclui o `ListAdapter` propriedade para o qual podemos definir o adaptador.

Aqui está uma captura de tela mostrando o resultado final, com as informações de calendário exibida no `ListView`:

[![CalendarDemo em execução no emulador, exibindo duas entradas do calendário](calendar-images/11-calendar.png)](calendar-images/11-calendar.png#lightbox)



## <a name="listing-calendar-events"></a>Listagem de eventos de calendário

Próxima vamos dar uma olhada em como enumerar os eventos para um determinado calendário.
Complementando o exemplo acima, apresentaremos uma lista de eventos quando o usuário seleciona um dos calendários. Portanto, precisaremos lidar com a seleção de item no código anterior:

```csharp
ListView.ItemClick += (sender, e) => {
    int i = (e as ItemEventArgs).Position;

    cursor.MoveToPosition(i);
    int calId =
        cursor.GetInt (cursor.GetColumnIndex (calendarsProjection [0]));

    var showEvents = new Intent(this, typeof(EventListActivity));
    showEvents.PutExtra("calId", calId);
    StartActivity(showEvents);
};
```

Nesse código, estamos criando uma tentativa de abrir uma atividade do tipo `EventListActivity`, passando a ID do calendário na intenção. Precisamos saber qual calendário para consulta de eventos a ID. No `EventListActivity`do `OnCreate` método, podemos recuperar a ID do `Intent` conforme mostrado abaixo:

```csharp
_calId = Intent.GetIntExtra ("calId", -1);
```

Agora vamos de eventos de consulta para este calendário ID. O processo de consulta de eventos é semelhante à forma como são consultados para obter uma lista de calendários anteriormente, só que desta vez, trabalharemos com a `CalendarContract.Events` classe. O código a seguir cria uma consulta para recuperar eventos:

```csharp
var eventsUri = CalendarContract.Events.ContentUri;

string[] eventsProjection = {
    CalendarContract.Events.InterfaceConsts.Id,
    CalendarContract.Events.InterfaceConsts.Title,
    CalendarContract.Events.InterfaceConsts.Dtstart
};

var loader = new CursorLoader(this, eventsUri, eventsProjection,
                   String.Format ("calendar_id={0}", _calId), null, "dtstart ASC");
var cursor = (ICursor)loader.LoadInBackground();
```

Nesse código, primeiro obtemos o conteúdo `Uri` eventos do `CalendarContract.Events.ContentUri` propriedade. Em seguida, podemos especificar as colunas de evento que queremos recuperar na matriz eventsProjection.
Por fim, podemos criar uma instância de um `CursorLoader` com essas informações e a chamada de carregador `LoadInBackground` método retorne um `Cursor` com os dados do evento.

Para exibir os dados do evento na interface do usuário, podemos usar marcação e código exatamente como fizemos antes de exibir a lista de calendários. Novamente, usamos `SimpleCursorAdapter` para associar os dados para um `ListView` conforme mostrado no código a seguir:

```csharp
string[] sourceColumns = {
    CalendarContract.Events.InterfaceConsts.Title,
    CalendarContract.Events.InterfaceConsts.Dtstart };

int[] targetResources = {
    Resource.Id.eventTitle,
    Resource.Id.eventStartDate };

var adapter = new SimpleCursorAdapter (this, Resource.Layout.EventListItem,
    cursor, sourceColumns, targetResources);

adapter.ViewBinder = new ViewBinder ();       
ListAdapter = adapter;
```

A principal diferença entre esse código e o código que usamos anteriormente para mostrar a lista de calendário é o uso de um `ViewBinder`, que é definido na linha:

```csharp
adapter.ViewBinder = new ViewBinder ();
```

O `ViewBinder` classe nos permite maior controle sobre como podemos associar valores a modos de exibição. Nesse caso, podemos usá-lo para converter a hora de início do evento de milissegundos para uma cadeia de caracteres de data, conforme mostrado na seguinte implementação:

```csharp
class ViewBinder : Java.Lang.Object, SimpleCursorAdapter.IViewBinder
{    
    public bool SetViewValue (View view, Android.Database.ICursor cursor,
        int columnIndex)
    {
        if (columnIndex == 2) {
            long ms = cursor.GetLong (columnIndex);

            DateTime date = new DateTime (1970, 1, 1, 0, 0, 0,
                DateTimeKind.Utc).AddMilliseconds (ms).ToLocalTime ();

            TextView textView = (TextView)view;
            textView.Text = date.ToLongDateString ();

            return true;
        }
        return false;
    }    
}
```

Isso exibe uma lista de eventos, conforme mostrado abaixo:

[![Captura de tela do aplicativo de exemplo exibindo três eventos de calendário](calendar-images/12-events.png)](calendar-images/12-events.png#lightbox)



## <a name="adding-a-calendar-event"></a>Adição de um evento de calendário

Já vimos como ler dados de calendário. Agora vamos ver como adicionar um evento em um calendário. Para isso funcionar, certifique-se de incluir o `android.permission.WRITE_CALENDAR` permissão mencionado anteriormente. Para adicionar um evento em um calendário, iremos:

1.  Criar um `ContentValues` instância.
1.  Use chaves dos `CalendarContract.Events.InterfaceConsts` classe para preencher o `ContentValues` instância.
1.  Defina o fuso horário para o início do evento e horários de término.
1.  Use um `ContentResolver` para inserir os dados do evento no calendário.


O código a seguir ilustra essas etapas:

```csharp
ContentValues eventValues = new ContentValues ();

eventValues.Put (CalendarContract.Events.InterfaceConsts.CalendarId,
    _calId);
eventValues.Put (CalendarContract.Events.InterfaceConsts.Title,
    "Test Event from M4A");
eventValues.Put (CalendarContract.Events.InterfaceConsts.Description,
    "This is an event created from Xamarin.Android");
eventValues.Put (CalendarContract.Events.InterfaceConsts.Dtstart,
    GetDateTimeMS (2011, 12, 15, 10, 0));
eventValues.Put (CalendarContract.Events.InterfaceConsts.Dtend,
    GetDateTimeMS (2011, 12, 15, 11, 0));

eventValues.Put(CalendarContract.Events.InterfaceConsts.EventTimezone,
    "UTC");
eventValues.Put(CalendarContract.Events.InterfaceConsts.EventEndTimezone,
    "UTC");

var uri = ContentResolver.Insert (CalendarContract.Events.ContentUri,
    eventValues);
```

Observe que, se não podemos definir o fuso horário, uma exceção do tipo `Java.Lang.IllegalArgumentException` será lançada. Porque valores de hora do evento devem ser expressa em milissegundos desde a época, podemos criar uma `GetDateTimeMS` método (em `EventListActivity`) para converter nosso especificações de data no formato de milissegundo:

```csharp
long GetDateTimeMS (int yr, int month, int day, int hr, int min)
{
    Calendar c = Calendar.GetInstance (Java.Util.TimeZone.Default);

    c.Set (Java.Util.CalendarField.DayOfMonth, 15);
    c.Set (Java.Util.CalendarField.HourOfDay, hr);
    c.Set (Java.Util.CalendarField.Minute, min);
    c.Set (Java.Util.CalendarField.Month, Calendar.December);
    c.Set (Java.Util.CalendarField.Year, 2011);

    return c.TimeInMillis;
}
```

Se adicionar um botão para a lista de eventos da interface do usuário e executar o código acima no botão de manipulador de eventos clicar, o evento é adicionado ao calendário e atualizado em nossa lista conforme mostrado abaixo:

[![Captura de tela do aplicativo de exemplo com eventos de calendário, seguido do botão Adicionar evento de exemplo](calendar-images/13.png)](calendar-images/13.png#lightbox)

Se abrimos o aplicativo de calendário, em seguida, veremos que o evento será gravado existe também:

[![Captura de tela do aplicativo de calendário, exibindo o evento de calendário selecionado](calendar-images/14.png)](calendar-images/14.png#lightbox)

Como você pode ver, o Android permite acesso eficiente e fácil recuperar e persistir os dados de calendário, permitindo que os aplicativos ao integrar perfeitamente os recursos de calendário.


## <a name="related-links"></a>Links relacionados

- [Demonstração de calendário (amostra)](https://developer.xamarin.com/samples/CalendarDemo/)
- [Apresentando o Ice Cream Sandwich](http://www.android.com/about/ice-cream-sandwich/)
- [Plataforma 4.0 Android](http://developer.android.com/sdk/android-4.0.html)
