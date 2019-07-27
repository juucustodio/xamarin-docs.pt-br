---
title: Calendário do Xamarin. Android
ms.prod: xamarin
ms.assetid: 78666541-CA14-4CD8-A94A-A9621C57813E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 0172a602f3e85f0de66b39613b4a28e49344ba08
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68510343"
---
# <a name="xamarinandroid-calendar"></a>Calendário do Xamarin. Android


## <a name="calendar-api"></a>API de calendário

Um novo conjunto de APIs de calendário introduzidas no Android 4 dá suporte a aplicativos projetados para ler ou gravar dados no provedor de calendário. Essas APIs oferecem suporte a uma infinidade de opções de interação com dados de calendário, incluindo a capacidade de ler e gravar eventos, participantes e lembretes. Usando o provedor de calendário em seu aplicativo, os dados que você adicionar por meio da API aparecerão no aplicativo de calendário interno fornecido com o Android 4.


## <a name="adding-permissions"></a>Adicionando permissões

Ao trabalhar com as novas APIs de calendário em seu aplicativo, a primeira coisa que você precisa fazer é adicionar as permissões apropriadas ao manifesto do Android. As permissões que você precisa adicionar são `android.permisson.READ_CALENDAR` e `android.permission.WRITE_CALENDAR`, dependendo se você está lendo e/ou gravando dados de calendário.


## <a name="using-the-calendar-contract"></a>Usando o contrato de calendário

Depois de definir as permissões, você pode interagir com os dados do calendário usando `CalendarContract` a classe. Essa classe fornece um modelo de dados que os aplicativos podem usar quando interagem com o provedor de calendário. O `CalendarContract` permite que os aplicativos resolvam os URIs para entidades de calendário, como calendários e eventos. Ele também fornece uma maneira de interagir com vários campos em cada entidade, como o nome e a ID de um calendário, ou a data de início e de término de um evento.

Vejamos um exemplo que usa a API de calendário. Neste exemplo, examinaremos como enumerar calendários e seus eventos, bem como adicionar um novo evento a um calendário.


## <a name="listing-calendars"></a>Listando calendários

Primeiro, vamos examinar como enumerar os calendários que foram registrados no aplicativo de calendário. Para fazer isso, podemos instanciar um `CursorLoader`. Introduzido no Android 3,0 (API 11), `CursorLoader` é a maneira preferida de consumir um `ContentProvider`. No mínimo, precisaremos especificar o URI de conteúdo para calendários e as colunas que desejamos retornar; Essa especificação de coluna é conhecida como _projeção_.

Chamar o `CursorLoader.LoadInBackground` método nos permite consultar um provedor de conteúdo para dados, como o provedor de calendário.
`LoadInBackground`executa a operação de carregamento real e retorna `Cursor` um com os resultados da consulta.

O `CalendarContract` nos ajuda a especificar o conteúdo `Uri` e a projeção. Para obter o conteúdo `Uri` para consultar calendários, podemos simplesmente usar a `CalendarContract.Calendars.ContentUri` Propriedade como esta:

```csharp
var calendarsUri = CalendarContract.Calendars.ContentUri;
```

Usar o `CalendarContract` para especificar quais colunas de calendário queremos é igualmente simples. Apenas adicionamos campos na `CalendarContract.Calendars.InterfaceConsts` classe a uma matriz. Por exemplo, o código a seguir inclui a ID do calendário, o nome de exibição e o nome da conta:

```csharp
string[] calendarsProjection = {
    CalendarContract.Calendars.InterfaceConsts.Id,
    CalendarContract.Calendars.InterfaceConsts.CalendarDisplayName,
    CalendarContract.Calendars.InterfaceConsts.AccountName
};
```

O `Id` é importante incluir se você estiver usando um `SimpleCursorAdapter` para associar os dados à interface do usuário, como veremos em breve. Com o URI de conteúdo e a projeção em vigor, `CursorLoader` criamos uma `CursorLoader.LoadInBackground` instância do e chamamos o método para retornar um cursor com os dados do calendário, conforme mostrado abaixo:

```csharp
var loader = new CursorLoader(this, calendarsUri, calendarsProjection, null, null, null);
var cursor = (ICursor)loader.LoadInBackground();

```

A interface do usuário para este exemplo `ListView`contém um, com cada item na lista que representa um único calendário. O XML a seguir mostra a marcação que inclui `ListView`:

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

Além disso, precisamos especificar a interface do usuário para cada item de lista, que colocamos em um arquivo XML separado da seguinte maneira:

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

A partir desse ponto, é apenas o código Android normal para associar os dados do cursor à interface do usuário. Usaremos o `SimpleCursorAdapter` seguinte:

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

No código acima, o adaptador pega as colunas especificadas na `sourceColumns` matriz e as grava nos elementos da interface do usuário `targetResources` na matriz para cada entrada de calendário no cursor. A atividade usada aqui é uma subclasse de `ListActivity`; ela inclui a `ListAdapter` Propriedade à qual definimos o adaptador.

Aqui está uma captura de tela mostrando o resultado final, com as informações de calendário `ListView`exibidas no:

[![CalendarDemo em execução no emulador, exibindo duas entradas de calendário](calendar-images/11-calendar.png)](calendar-images/11-calendar.png#lightbox)



## <a name="listing-calendar-events"></a>Listando eventos de calendário

Em seguida, vamos examinar como enumerar os eventos para um determinado calendário.
Com base no exemplo acima, apresentaremos uma lista de eventos quando o usuário selecionar um dos calendários. Portanto, precisaremos manipular a seleção de item no código anterior:

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

Nesse código, estamos criando uma intenção para abrir uma atividade do tipo `EventListActivity`, passando a ID do calendário na intenção. Precisaremos da ID para saber qual calendário deve ser consultado em busca de eventos. No método `OnCreate` `Intent` do, podemos recuperar a ID do conforme mostrado abaixo: `EventListActivity`

```csharp
_calId = Intent.GetIntExtra ("calId", -1);
```

Agora, vamos consultar eventos para esta ID de calendário. O processo de consulta de eventos é semelhante à maneira como consultamos uma lista de calendários anteriormente, apenas desta vez vamos trabalhar com a `CalendarContract.Events` classe. O código a seguir cria uma consulta para recuperar eventos:

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

Nesse código, primeiro obtemos o conteúdo `Uri` de eventos `CalendarContract.Events.ContentUri` da propriedade. Em seguida, especificamos as colunas de evento que desejamos recuperar na matriz eventsProjection.
Por fim, criamos `CursorLoader` uma instância com essas informações e chamamos o `LoadInBackground` método do carregador para `Cursor` retornar um com os dados do evento.

Para exibir os dados de evento na interface do usuário, podemos usar marcação e código da mesma forma que fizemos antes para exibir a lista de calendários. Novamente, usamos `SimpleCursorAdapter` para associar os dados a um `ListView` , conforme mostrado no código a seguir:

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

A principal diferença entre esse código e o código que usamos anteriormente para mostrar a lista de calendários é o uso de `ViewBinder`um, que é definido na linha:

```csharp
adapter.ViewBinder = new ViewBinder ();
```

A `ViewBinder` classe nos permite controlar ainda mais como associamos valores a exibições. Nesse caso, usamos-o para converter a hora de início do evento de milissegundos em uma cadeia de caracteres de data, conforme mostrado na seguinte implementação:

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



## <a name="adding-a-calendar-event"></a>Adicionando um evento de calendário

Vimos como ler os dados do calendário. Agora, vamos ver como adicionar um evento a um calendário. Para que isso funcione, não se esqueça de incluir `android.permission.WRITE_CALENDAR` a permissão mencionada anteriormente. Para adicionar um evento a um calendário, iremos:

1.  Crie uma `ContentValues` instância.
1.  Use as chaves da `CalendarContract.Events.InterfaceConsts` classe para popular a `ContentValues` instância.
1.  Defina os fusos horários para as horas de início e término do evento.
1.  Use um `ContentResolver` para inserir os dados do evento no calendário.


O código a seguir ilustra estas etapas:

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

Observe que se não definirmos o fuso horário, uma exceção do tipo `Java.Lang.IllegalArgumentException` será lançada. Como os valores de tempo do evento devem ser expressos em milissegundos desde a `GetDateTimeMS` época, criamos um método (em `EventListActivity`) para converter nossas especificações de data em formato de milissegundo:

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

Se adicionarmos um botão à interface do usuário da lista de eventos e executarmos o código acima no manipulador de eventos Click do botão, o evento será adicionado ao calendário e atualizado em nossa lista, conforme mostrado abaixo:

[![Captura de tela do aplicativo de exemplo com eventos de calendário seguidos do botão Adicionar evento de exemplo](calendar-images/13.png)](calendar-images/13.png#lightbox)

Se abrirmos o aplicativo de calendário, veremos que o evento também é escrito lá:

[![Captura de tela do aplicativo de calendário exibindo o evento de calendário selecionado](calendar-images/14.png)](calendar-images/14.png#lightbox)

Como você pode ver, o Android permite um acesso poderoso e fácil para recuperar e persistir dados de calendário, permitindo que os aplicativos integrem diretamente os recursos de calendário.


## <a name="related-links"></a>Links relacionados

- [Demonstração do calendário (exemplo)](https://developer.xamarin.com/samples/monodroid/CalendarDemo/)
- [Introdução ao sanduíche de sorvete](http://www.android.com/about/ice-cream-sandwich/)
- [Plataforma Android 4,0](https://developer.android.com/sdk/android-4.0.html)
