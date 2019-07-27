---
title: Usando CursorAdapters
ms.prod: xamarin
ms.assetid: 60DE467E-A5DA-4420-52E5-D86AD1678FE6
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 10/25/2017
ms.openlocfilehash: a2af40d7af5cac39cc92c8e2bc88b5fbcf81291e
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68510040"
---
# <a name="using-cursoradapters-with-xamarinandroid"></a>Usando o CursorAdapters com Xamarin. Android

O Android fornece classes de adaptador especificamente para exibir dados de uma consulta de banco de dados SQLite:

 **SimpleCursorAdapter** – semelhante a um `ArrayAdapter` porque ele pode ser usado sem subclasses. Basta fornecer os parâmetros necessários (como informações de cursor e de layout) no construtor e, em seguida, atribuir `ListView`a um.

 **CursorAdapter** – uma classe base que pode ser herdada quando você precisa de mais controle sobre a associação de valores de dados a controles de layout (por exemplo, ocultando/mostrando controles ou alterando suas propriedades).

Os adaptadores de cursor fornecem uma maneira de alto desempenho para rolar por listas longas de dados armazenados no SQLite. O código de consumo deve definir uma consulta SQL em `Cursor` um objeto e, em seguida, descrever como criar e preencher as exibições para cada linha.


## <a name="creating-an-sqlite-database"></a>Criando um banco de dados SQLite

Para demonstrar que os adaptadores de cursor exigem uma implementação de banco de dados SQLite simples. O código em **SimpleCursorTableAdapter/VegetableDatabase. cs** contém o código e o SQL para criar uma tabela e preenchê-la com alguns dados.
A classe `VegetableDatabase` completa é mostrada aqui:

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

A `VegetableDatabase` classe será instanciada `OnCreate` no método da `HomeScreen` atividade. A `SQLiteOpenHelper` classe base gerencia a configuração do arquivo de banco de dados e garante que o SQL `OnCreate` em seu método seja executado apenas uma vez. Essa classe é usada nos dois exemplos a seguir para `SimpleCursorAdapter` o `CursorAdapter`e o.

A consulta do cursor *deve* ter uma coluna `_id` de inteiros `CursorAdapter` para que o funcione. Se a tabela subjacente não tiver uma coluna de inteiros chamada `_id` , use um alias de coluna para outro inteiro exclusivo `RawQuery` no que compõe o cursor. Consulte os [documentos do Android](xref:Android.Widget.CursorAdapter) para obter mais informações.


### <a name="creating-the-cursor"></a>Criando o cursor

Os exemplos usam um `RawQuery` para transformar uma consulta SQL em um `Cursor` objeto. A lista de colunas retornada do cursor define as colunas de dados que estão disponíveis para exibição no adaptador de cursor. O código que cria o banco de dados no método **SimpleCursorTableAdapter/homescreen. cs** `OnCreate` é mostrado aqui:

```csharp
vdb = new VegetableDatabase(this);
cursor = vdb.ReadableDatabase.RawQuery("SELECT * FROM vegetables", null); // cursor query
StartManagingCursor(cursor);
// use either SimpleCursorAdapter or CursorAdapter subclass here!
```

Qualquer código que chama `StartManagingCursor` também deve chamar `StopManagingCursor`. Os exemplos são `OnCreate` usados para iniciar e `OnDestroy` fechar o cursor. O `OnDestroy` método contém este código:

```csharp
StopManagingCursor(cursor);
cursor.Close();
```

Depois que um aplicativo tiver um banco de dados SQLite disponível e tiver criado um objeto cursor como mostrado, ele poderá `SimpleCursorAdapter` utilizar uma ou uma subclasse `CusorAdapter` de para exibir linhas em `ListView`um.


## <a name="using-simplecursoradapter"></a>Usando SimpleCursorAdapter

`SimpleCursorAdapter`é como o `ArrayAdapter`, mas é especializado para uso com o SQLite. Ele não requer subclasses – basta definir alguns parâmetros simples ao criar o objeto e, em seguida, atribuí- `ListView`lo `Adapter` à propriedade de uma.

Os parâmetros para o Construtor SimpleCursorAdapter são:

 **Contexto** – uma referência à atividade que o contém.

 **Layout** – a ID de recurso da exibição de linha a ser usada.

 **ICursor** – um cursor que contém a consulta do SQLite para os dados a serem exibidos.

 A **partir da** matriz de cadeia de caracteres – uma matriz de cadeia de caracteres correspondente aos nomes das colunas no cursor.

 **Para** matriz de inteiros – uma matriz de IDs de layout que correspondem aos controles no layout de linha. O valor da coluna especificada na `from` matriz será associado à ControlID especificada nessa matriz no mesmo índice.

As `from` matrizes e `to` devem ter o mesmo número de entradas porque formam um mapeamento da fonte de dados para os controles de layout na exibição.

O código de exemplo **SimpleCursorTableAdapter/homescreen. cs** se conecta `SimpleCursorAdapter` assim:

```csharp
// which columns map to which layout controls
string[] fromColumns = new string[] {"name"};
int[] toControlIDs = new int[] {Android.Resource.Id.Text1};
// use a SimpleCursorAdapter
listView.Adapter = new SimpleCursorAdapter (this, Android.Resource.Layout.SimpleListItem1, cursor,
       fromColumns,
       toControlIDs);
```

`SimpleCursorAdapter`é uma maneira rápida e simples de exibir dados do SQLite em `ListView`um. A principal limitação é que ele só pode associar valores de coluna a controles de exibição, ele não permite que você altere outros aspectos do layout de linha (por exemplo, mostrando/ocultando controles ou alterando Propriedades).


## <a name="subclassing-cursoradapter"></a>CursorAdapter de subclasse

Uma `CursorAdapter` subclasse tem os mesmos benefícios de desempenho que o `SimpleCursorAdapter` para exibir dados do SQLite, mas também oferece controle total sobre a criação e o layout de cada exibição de linha. A `CursorAdapter` implementação é muito diferente da `BaseAdapter` subclasse porque não substitui `GetView`, `GetItemId` `Count` ou `this[]` indexador.

Dado um banco de dados SQLite em funcionamento, você só precisa substituir dois métodos para `CursorAdapter` criar uma subclasse:

- **BindView** – dado um modo de exibição, atualize-o para exibir os dados no cursor fornecido.

- **NewView** – chamado quando o `ListView` requer uma nova exibição para exibir. O `CursorAdapter` cuidará da reciclagem de exibições (ao `GetView` contrário do método em adaptadores regulares).

As subclasses de adaptadores nos exemplos anteriores têm métodos para retornar o número de linhas e para recuperar o item atual `CursorAdapter` – o não requer esses métodos porque essas informações podem ser obtidas do próprio cursor. Ao dividir a criação e a população de cada exibição nesses dois métodos, o impõe `CursorAdapter` a reutilização da exibição. Isso é diferente de um adaptador regular onde é possível ignorar o `convertView` parâmetro `BaseAdapter.GetView` do método.


### <a name="implementing-the-cursoradapter"></a>Implementando o CursorAdapter

O código em **CursorTableAdapter/HomeScreenCursorAdapter. cs** contém uma `CursorAdapter` subclasse. Ele armazena uma referência de contexto passada para o construtor para que ele possa acessar `LayoutInflater` um `NewView` no método. A classe completa tem a seguinte aparência:

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

No que exibirá o `ListView`, crie o cursor e `CursorAdapter` , em seguida, atribua-o à exibição de lista. `Activity`

O código que executa essa ação no método **CursorTableAdapter/homescreen. cs** `OnCreate` é mostrado aqui:

```csharp
// create the cursor
vdb = new VegetableDatabase(this);
cursor = vdb.ReadableDatabase.RawQuery("SELECT * FROM vegetables", null);
StartManagingCursor(cursor);

// create the CursorAdapter
listView.Adapter = (IListAdapter)new HomeScreenCursorAdapter(this, cursor, false);
```

O `OnDestroy` método contém a `StopManagingCursor` chamada de método descrita anteriormente.



## <a name="related-links"></a>Links relacionados

- [SimpleCursorTableAdapter (exemplo)](https://developer.xamarin.com/samples/monodroid/SimpleCursorTableAdapter/)
- [CursorTableAdapter (exemplo)](https://developer.xamarin.com/samples/monodroid/CursorTableAdapter/)
