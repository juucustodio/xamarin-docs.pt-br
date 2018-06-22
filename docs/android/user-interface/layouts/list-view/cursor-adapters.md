---
title: Usando CursorAdapters
ms.prod: xamarin
ms.assetid: 60DE467E-A5DA-4420-52E5-D86AD1678FE6
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 10/25/2017
ms.openlocfilehash: 20311cc50c87638391d8b078c405bc61baceb373
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
ms.locfileid: "30766657"
---
# <a name="using-cursoradapters"></a>Usando CursorAdapters


## <a name="overview"></a>Visão geral

Fornece classes de adaptador especificamente para exibir dados de uma consulta de banco de dados SQLite do Android:

 **SimpleCursorAdapter** – semelhante a um `ArrayAdapter` porque ele pode ser usado sem subclassificação. Basta fornecer os parâmetros necessários (como um cursor e informações de layout) no construtor e, em seguida, atribuir a um `ListView`.

 **CursorAdapter** – valores de uma classe base que você pode herdar de quando você precisa de mais controle sobre a associação de dados a controles de layout (por exemplo, mostrando/ocultando controles ou alterar suas propriedades).

Adaptadores de cursor fornecem um modo de alto desempenho para rolar listas longas de dados que são armazenados no SQLite. O código deve definir uma consulta SQL em um `Cursor` de objeto e, em seguida, descrevem como criar e popular os modos de exibição para cada linha.


## <a name="creating-an-sqlite-database"></a>Criando um banco de dados SQLite

Para demonstrar os adaptadores de cursor requer uma implementação de banco de dados SQLite simples. O código em **SimpleCursorTableAdapter/VegetableDatabase.cs** contém o código e o SQL para criar uma tabela e preenchê-la com alguns dados.
Completo `VegetableDatabase` é mostrada aqui:

```csharp
class VegetableDatabase  : SQLiteOpenHelper {
   public static readonly string create_table_sql =
       "CREATE TABLE [vegetables] ([_id] INTEGER PRIMARY KEY AUTOINCREMENT NOT NULL UNIQUE, [name] TEXT NOT NULL UNIQUE)";
   public static readonly string DatabaseName = "vegetables.db";
   public static readonly int DatabaseVersion = 1;
   public VegetableDatabase(Context context) : base(context, DatabaseName, null, DatabaseVersion) { }
   public override void OnCreate(SQLiteDatabase db)
   {
       db.ExecSQL(create_table_sql);
       // seed with data
       db.ExecSQL("INSERT INTO vegetables (name) VALUES ('Vegetables')");
       db.ExecSQL("INSERT INTO vegetables (name) VALUES ('Fruits')");
       db.ExecSQL("INSERT INTO vegetables (name) VALUES ('Flower Buds')");
       db.ExecSQL("INSERT INTO vegetables (name) VALUES ('Legumes')");
       db.ExecSQL("INSERT INTO vegetables (name) VALUES ('Bulbs')");
       db.ExecSQL("INSERT INTO vegetables (name) VALUES ('Tubers')");
   }
   public override void OnUpgrade(SQLiteDatabase db, int oldVersion, int newVersion)
   {   // not required until second version :)
       throw new NotImplementedException();
   }
}
```

O `VegetableDatabase` classe será instanciada no `OnCreate` método o `HomeScreen` atividade. O `SQLiteOpenHelper` gerencia a configuração do arquivo de banco de dados de classe base e garante que o SQL em seu `OnCreate` método só é executado uma vez. Essa classe é usada em dois exemplos a seguir para `SimpleCursorAdapter` e `CursorAdapter`.

A consulta de cursor *deve* tem uma coluna de inteiros `_id` para o `CursorAdapter` para trabalhar. Se a tabela subjacente não tiver uma coluna de número inteiro denominada `_id` , em seguida, use um alias de coluna para outro inteiro exclusivo no `RawQuery` que compõe o cursor. Consulte o [documentos Android](https://developer.xamarin.com/api/type/Android.Widget.CursorAdapter/) para obter mais informações.


### <a name="creating-the-cursor"></a>Criando o Cursor

Os exemplos usam um `RawQuery` para ativar uma consulta SQL em um `Cursor` objeto. Lista de colunas retornado pelo cursor define as colunas de dados que estão disponíveis para exibição no adaptador do cursor. O código que cria o banco de dados no **SimpleCursorTableAdapter/HomeScreen.cs** `OnCreate` método é mostrado aqui:

```csharp
vdb = new VegetableDatabase(this);
cursor = vdb.ReadableDatabase.RawQuery("SELECT * FROM vegetables", null); // cursor query
StartManagingCursor(cursor);
// use either SimpleCursorAdapter or CursorAdapter subclass here!
```

Qualquer código que chama `StartManagingCursor` também deve chamar `StopManagingCursor`. Os exemplos usam `OnCreate` para iniciar, e `OnDestroy` para fechar o cursor. O `OnDestroy` método contém este código:

```csharp
StopManagingCursor(cursor);
cursor.Close();
```

Depois que um aplicativo tem um banco de dados SQLite disponível e tiver criado um objeto de cursor, conforme mostrado, ela pode utilizar um `SimpleCursorAdapter` ou uma subclasse de `CusorAdapter` para exibir linhas em um `ListView`.


## <a name="using-simplecursoradapter"></a>Usando SimpleCursorAdapter

`SimpleCursorAdapter` é como o `ArrayAdapter`, mas especializado para uso com SQLite. Ele não exige subclassificação – apenas definir alguns parâmetros simples ao criar o objeto e, em seguida, atribuí-lo a um `ListView`do `Adapter` propriedade.

Os parâmetros do construtor SimpleCursorAdapter são:

 **Contexto** – uma referência para a atividade que contém.

 **Layout** – a ID de recurso do modo de exibição de linha a ser usado.

 **ICursor** – um cursor que contém a consulta do SQLite para os dados serem exibidos.

 **De** matriz de cadeia de caracteres – uma matriz de cadeias de caracteres correspondentes aos nomes das colunas no cursor.

 **Para** matriz de inteiros – uma matriz de IDs de layout que correspondem aos controles no layout de linha. O valor da coluna especificada no `from` matriz será associada ao ControlID especificado nesta matriz no mesmo índice.

O `from` e `to` matrizes devem ter o mesmo número de entradas, pois elas formam um mapeamento de fonte de dados para os controles de layout no modo de exibição.

O **SimpleCursorTableAdapter/HomeScreen.cs** fios de código de exemplo para cima um `SimpleCursorAdapter` como este:

```csharp
// which columns map to which layout controls
string[] fromColumns = new string[] {"name"};
int[] toControlIDs = new int[] {Android.Resource.Id.Text1};
// use a SimpleCursorAdapter
listView.Adapter = new SimpleCursorAdapter (this, Android.Resource.Layout.SimpleListItem1, cursor,
       fromColumns,
       toControlIDs);
```

`SimpleCursorAdapter` é uma maneira rápida e simple para exibir dados SQLite em um `ListView`. A principal limitação é que ele só pode vincular os valores de coluna para exibir controles, ele não permite alterar outros aspectos do layout da linha (por exemplo, Mostrar/ocultar controles ou alterando as propriedades).


## <a name="subclassing-cursoradapter"></a>Subclasses CursorAdapter

Um `CursorAdapter` subclasse tem os mesmos benefícios de desempenho que o `SimpleCursorAdapter` para exibir dados do SQLite, mas também fornece controle completo sobre a criação e o layout do modo de exibição de cada linha. O `CursorAdapter` implementação é muito diferente da subclassificação `BaseAdapter` porque não substitui `GetView`, `GetItemId`, `Count` ou `this[]` indexador.

Dado um trabalho SQLite de banco de dados, você só precisará substituir dois métodos para criar um `CursorAdapter` subclasse:

- **BindView** – devido a uma exibição, atualizá-lo para exibir os dados no cursor fornecido.

- **Nova exibição** – chamado quando o `ListView` requer um novo modo de exibição para exibir. O `CursorAdapter` cuidará de reciclagem exibições (diferente de `GetView` método nos adaptadores regulares).

As subclasses de adaptador nos exemplos anteriores tem métodos para retornar o número de linhas e recuperar o item atual – o `CursorAdapter` não requer esses métodos porque essas informações podem ser obtidas do cursor em si. Dividindo a criação e a população de cada exibição para esses dois métodos, o `CursorAdapter` impõe a reutilização de exibição. Isso é em contraste com um adaptador regular onde é possível ignorar o `convertView` parâmetro o `BaseAdapter.GetView` método.


### <a name="implementing-the-cursoradapter"></a>Implementando o CursorAdapter

O código em **CursorTableAdapter/HomeScreenCursorAdapter.cs** contém um `CursorAdapter` subclasse. Ele armazena uma referência de contexto passada para o construtor para que ele possa acessar um `LayoutInflater` no `NewView` método. A classe completa tem esta aparência:

```csharp
public class HomeScreenCursorAdapter : CursorAdapter {
   Activity context;
   public HomeScreenCursorAdapter(Activity context, ICursor c)
       : base(context, c)
   {
       this.context = context;
   }
   public override void BindView(View view, Context context, ICursor cursor)
   {
       var textView = view.FindViewById<TextView>(Android.Resource.Id.Text1);
       textView.Text = cursor.GetString(1); // 'name' is column 1 in the cursor query
   }
   public override View NewView(Context context, ICursor cursor, ViewGroup parent)
   {
       return this.context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleListItem1, parent, false);
   }
}
```


### <a name="assigning-the-cursoradapter"></a>Atribuindo o CursorAdapter

No `Activity` que exibirá o `ListView`, cria o cursor e `CursorAdapter` , em seguida, atribuí-la à exibição de lista.

O código que executa esta ação de **CursorTableAdapter/HomeScreen.cs** `OnCreate` método é mostrado aqui:

```csharp
// create the cursor
vdb = new VegetableDatabase(this);
cursor = vdb.ReadableDatabase.RawQuery("SELECT * FROM vegetables", null);
StartManagingCursor(cursor);

// create the CursorAdapter
listView.Adapter = (IListAdapter)new HomeScreenCursorAdapter(this, cursor, false);
```

O `OnDestroy` método contém o `StopManagingCursor` chamada de método descrita anteriormente.



## <a name="related-links"></a>Links relacionados

- [SimpleCursorTableAdapter (exemplo)](https://developer.xamarin.com/samples/SimpleCursorTableAdapter/)
- [CursorTableAdapter (exemplo)](https://developer.xamarin.com/samples/CursorTableAdapter/)
