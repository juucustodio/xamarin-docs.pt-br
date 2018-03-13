---
title: Criando um ContentProvider personalizado
description: "A seção anterior demonstrou como consumir dados de uma implementação interna de ContentProvider. Esta seção explica como criar um ContentProvider personalizado e, em seguida, consumir seus dados."
ms.topic: article
ms.prod: xamarin
ms.assetid: 36742B59-607E-070E-5D0E-B9C18917D3F4
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/07/2018
ms.openlocfilehash: 9fac4a233cecd9332602047bc83830d145b5fb08
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="creating-a-custom-contentprovider"></a>Criando um ContentProvider personalizado

_A seção anterior demonstrou como consumir dados de uma implementação interna de ContentProvider. Esta seção explica como criar um ContentProvider personalizado e, em seguida, consumir seus dados._

## <a name="about-contentproviders"></a>Sobre ContentProviders

Uma classe de provedor de conteúdo deve herdar de `ContentProvider`. Ele deve consistir em um repositório de dados interno que é usado para responder a consultas e ela deve expor Uris e tipos de MIME como constantes para ajudar o código de consumo fazem solicitações válidas para dados.

### <a name="uri-authority"></a>URI (autoridade)

`ContentProviders` são acessados no Android usando um Uri. Um aplicativo que expõe um `ContentProvider` define os Uris que ele responde na sua **AndroidManifest.xml** arquivo. Quando o aplicativo é instalado, esses Uris são registrados para que outros aplicativos possam acessá-las.

Em Mono para Android, a classe de provedor de conteúdo deve ter uma `[ContentProvider]` atributo para especificar o Uri (ou Uris) que deve ser adicionado ao **AndroidManifest.xml**.


### <a name="mime-type"></a>Tipo de MIME

O formato típico para tipos de MIME consiste em duas partes. Android `ContentProviders` geralmente usam essas duas cadeias de caracteres para a primeira parte do tipo de MIME:

1. `vnd.android.cursor.item` &ndash; para representar uma única linha, use o `ContentResolver.CursorItemBaseType` constante no código.

1. `vnd.android.cursor.dir` &ndash; para várias linhas, use o `ContentResolver.CursorDirBaseType` constante no código.

A segunda parte do tipo de MIME é específica para seu aplicativo e deve usar um padrão de DNS inverso com um `vnd.` prefixo. O código de exemplo usa `vnd.com.xamarin.sample.Vegetables`.


### <a name="data-model-metadata"></a>Metadados do modelo de dados

Aplicativos de consumo precisam construir consultas de Uri para acessar diferentes tipos de dados. O Uri de base pode ser expandido para se referir a uma tabela específica de dados e também pode incluir parâmetros para filtrar os resultados. As colunas e as cláusulas usadas com o cursor resultante para exibir dados também devem ser declaradas.

Para garantir que apenas as consultas Uri válidas são construídas, é comum para fornecer as cadeias de caracteres válidas como valores de constante. Isso torna mais fácil acesso a `ContentProvider` porque ele faz com que os valores podem ser descobertos por meio de conclusão de código e evita erros de digitação em cadeias de caracteres.

No exemplo anterior a `android.provider.ContactsContract` classe exposta os metadados para os dados de contatos. Para nosso personalizado `ContentProvider` vamos apenas expor as constantes da classe em si.


## <a name="implementation"></a>Implementação

Há três etapas para criar e consumir um personalizado `ContentProvider`:

1. **Criar uma classe de banco de dados** &ndash; implementar `SQLiteOpenHelper`.

2. **Criar um `ContentProvider` classe** &ndash; implementar `ContentProvider` com uma instância do banco de dados, metadados expostos como valores constantes e métodos para acessar os dados.

3. **Acesso a `ContentProvider` por meio de seu Uri** &ndash; preencher um `CursorAdapter` usando o `ContentProvider`, acessado por meio de seu Uri.

Conforme discutido anteriormente, `ContentProviders` pode ser consumido de aplicativos diferente de onde elas estão definidas. Neste exemplo, os dados são consumidos no mesmo aplicativo, mas tenha em mente que outros aplicativos também podem acessar-ele desde que eles saibam o Uri e informações sobre o esquema (que geralmente é exposto como valores de constante).


## <a name="create-a-database"></a>Criar um banco de dados

A maioria dos `ContentProvider` implementações serão baseadas em um `SQLite` banco de dados. O código de banco de dados de exemplo na **SimpleContentProvider/VegetableDatabase.cs** cria um banco de dados muito simple de duas colunas, conforme mostrado:

```csharp
class VegetableDatabase  : SQLiteOpenHelper {
  const string create_table_sql =
    "CREATE TABLE [vegetables] ([_id] INTEGER PRIMARY KEY AUTOINCREMENT NOT NULL UNIQUE, [name] TEXT NOT NULL UNIQUE)";
  const string DatabaseName = "vegetables.db";
  const int DatabaseVersion = 1;

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
  {
    throw new NotImplementedException();
  }
}
```

A implementação do banco de dados em si não precisa de considerações especiais para ser exposto a com um `ContentProvider`, no entanto se você pretende associar o `ContentProvider's` dados para um `ListView` controle, em seguida, uma coluna de inteiro exclusivo chamada `_id` devem fazer parte do conjunto de resultados. Consulte o [ListViews e adaptadores](~/android/user-interface/layouts/list-view/index.md) documento para obter mais detalhes sobre como usar o `ListView` controle.


## <a name="create-the-contentprovider"></a>Criar o ContentProvider

O restante desta seção fornece instruções passo a passo sobre como o **SimpleContentProvider/VegetableProvider.cs** classe de exemplo foi criado.


### <a name="initialize-the-database"></a>Inicializar o banco de dados

A primeira etapa é a subclasse `ContentProvider` e adicione o banco de dados que serão usados.

```csharp
public class VegetableProvider : ContentProvider 
{
    VegetableDatabase vegeDB;
    public override bool OnCreate()
    {
       vegeDB = new VegetableDatabase(Context);
        return true;
    }
}
```

O restante do código formará a implementação de provedor de conteúdo real que permite que os dados sejam detectados e consultada.



## <a name="add-metadata-for-consumers"></a>Adicionar metadados para os consumidores

Há quatro tipos diferentes de metadados que usaremos para expor no `ContentProvider` classe. Apenas a autoridade necessária, o restante é feita por convenção.

- **Autoridade** &ndash; o `ContentProvider` atributo *deve* ser adicionado à classe para que ele está registrado com o Android quando o aplicativo está instalado.

- **URI** &ndash; o `CONTENT_URI` é exposta como uma constante para que seja fácil de usar no código. Ele deve corresponder a autoridade, mas incluir o caminho base e o esquema.

- **Tipos MIME** &ndash; listas de resultados e os resultados são tratadas como diferentes tipos de conteúdo, portanto, definimos dois tipos de MIME para representá-los.

- **InterfaceConsts** &ndash; fornecer um valor constante para cada nome de coluna de dados, para que o código de consumo pode facilmente descobrir e consultá-los sem o risco de erros de digitação.

Este código mostra como cada um desses itens é implementado, adicionando a definição do banco de dados da etapa anterior:

```csharp
[ContentProvider(new string[] { CursorTableAdapter.VegetableProvider.AUTHORITY })]
public class VegetableProvider : ContentProvider 
{
   public const string AUTHORITY = "com.xamarin.sample.VegetableProvider";
   static string BASE_PATH = "vegetables";
   public static readonly Android.Net.Uri CONTENT_URI = Android.Net.Uri.Parse("content://" + AUTHORITY + "/" + BASE_PATH);
   // MIME types used for getting a list, or a single vegetable
   public const string VEGETABLES_MIME_TYPE = ContentResolver.CursorDirBaseType + "/vnd.com.xamarin.sample.Vegetables";
   public const string VEGETABLE_MIME_TYPE = ContentResolver.CursorItemBaseType + "/vnd.com.xamarin.sample.Vegetables";
   // Column names
   public static class InterfaceConsts {
       public const string Id = "_id";
       public const string Name = "name";
   }
   VegetableDatabase vegeDB;
   public override bool OnCreate()
   {
       vegeDB = new VegetableDatabase(Context);
       return true;
   }
}
```


## <a name="implement-the-uri-parsing-helper"></a>Implementar o URI análise auxiliar

Como o código de consumo usa Uris para fazer solicitações de um `ContentProvider`, é preciso analisar essas solicitações para determinar quais dados a serem retornados. O `UriMatcher` classe pode ajudar a analisar Uris, quando ela foi inicializada com o Uri de padrões que o `ContentProvider` oferece suporte.

O `UriMatcher` no exemplo será inicializado com dois Uris:

1. *"com.xamarin.sample.VegetableProvider/vegetables"* &ndash; solicitação para retornar a lista completa de legumes.

2. *"com.xamarin.sample.VegetableProvider/vegetables/\#"* &ndash; onde o \# é um espaço reservado para um parâmetro numérico (o `_id` da linha no banco de dados). Um espaço reservado de asterisco ("\*") também pode ser usado para corresponder a um parâmetro de texto.

No código, usamos as constantes para fazer referência a valores de metadados, como a autoridade e BASE\_caminho. Os códigos de retorno serão usados em métodos que fazem o Uri de análise, para determinar quais dados a serem retornados.

```csharp
const int GET_ALL = 0; // return code when list of Vegetables requested
const int GET_ONE = 1; // return code when a single Vegetable is requested by ID
static UriMatcher uriMatcher = BuildUriMatcher();
static UriMatcher BuildUriMatcher()
{
  var matcher = new UriMatcher(UriMatcher.NoMatch);
  // Uris to match, and the code to return when matched
  matcher.AddURI(AUTHORITY, BASE_PATH, GET_ALL); // all vegetables
  matcher.AddURI(AUTHORITY, BASE_PATH + "/#", GET_ONE); // specific vegetable by numeric ID
  return matcher;
}
```

Esse código é privativo todos o `ContentProvider` classe. Consulte [documentação de UriMatcher do Google](https://developer.xamarin.com/api/type/Android.Content.UriMatcher/) para obter mais informações.


## <a name="implement-the-querymethod"></a>Implementar o QueryMethod

A forma mais simples `ContentProvider` para implementar o método é o `Query` método. A implementação abaixo usa o `UriMatcher` para analisar o `uri` parâmetro e chame o método de banco de dados correto. Se o `uri` contém um parâmetro de ID de inteiro é analisado (usando `LastPathSegment`) e usado na consulta de banco de dados.

```csharp
public override Android.Database.ICursor Query(Android.Net.Uri uri, string[] projection, string selection, string[] selectionArgs, string sortOrder)
{
  switch (uriMatcher.Match(uri)) {
  case GET_ALL:
    return GetFromDatabase();
  case GET_ONE:
    var id = uri.LastPathSegment;
    return GetFromDatabase(id); // the ID is the last part of the Uri
  default:
    throw new Java.Lang.IllegalArgumentException("Unknown Uri: " + uri);
  }
}
Android.Database.ICursor GetFromDatabase()
{
  return vegeDB.ReadableDatabase.RawQuery("SELECT _id, name FROM vegetables", null);
}
Android.Database.ICursor GetFromDatabase(string id)
{
  return vegeDB.ReadableDatabase.RawQuery("SELECT _id, name FROM vegetables WHERE _id = " + id, null);
}
```

O `GetType` método também deve ser substituído. Esse método pode ser chamado para determinar o tipo de conteúdo que será retornado para um determinado Uri.
Isso pode informar a manipulação de dados para o aplicativo de consumo.

```csharp
public override String GetType(Android.Net.Uri uri)
{
  switch (uriMatcher.Match(uri)) {
  case GET_ALL:
    return VEGETABLES_MIME_TYPE; // list
  case GET_ONE:
    return VEGETABLE_MIME_TYPE; // single item
  default:
    throw new Java.Lang.IllegalArgumentExceptoin ("Unknown Uri: " + uri);
   }
}
```


## <a name="implement-the-other-overrides"></a>Implementar as outras substituições

Não permitir que o nosso exemplo simple para edição ou exclusão de dados, mas os métodos Insert, Update e Delete devem ser implementados para adicioná-los sem uma implementação:

```csharp
public override int Delete(Android.Net.Uri uri, string selection, string[] selectionArgs)
{
  throw new Java.Lang.UnsupportedOperationException();
}
public override Android.Net.Uri Insert(Android.Net.Uri uri, ContentValues values)
{
  throw new Java.Lang.UnsupportedOperationException();
}
public override int Update(Android.Net.Uri uri, ContentValues values, string selection, string[] selectionArgs)
{
  throw new Java.Lang.UnsupportedOperationException();
}
```

Isso conclui o basic `ContentProvider` implementação. Depois que o aplicativo tiver sido instalado, os dados que ele expõe estará disponíveis dentro do aplicativo mas também para qualquer outro aplicativo que sabe a Uri para fazer referência a ela.


## <a name="access-the-contentprovider"></a>Acesso a ContentProvider

Uma vez o `VegetableProvider` tiver sido implementado, acessá-lo é feito da mesma forma que o provedor de contatos no início deste documento: obter um cursor usando o Uri especificado e, em seguida, usar um adaptador para acessar os dados.


## <a name="bind-a-listview-to-a-contentprovider"></a>Associar um ListView para um ContentProvider

Para popular um `ListView` com dados, usamos o Uri que corresponde à lista de legumes não filtrada. No código, usamos o valor da constante `VegetableProvider.CONTENT_URI`, que sabemos que resolvem para `com.xamarin.sample.vegetableprovider/vegetables`. Nosso `VegetableProvider.Query` implementação retornará um cursor que pode ser vinculado, em seguida, para o `ListView`.

O código em `SimpleContentProvider/HomeScreen.cs` mostra como é simples para exibir dados de um `ContentProvider`:

```csharp
listView = FindViewById<ListView>(Resource.Id.List);
string[] projection = new string[] { VegetableProvider.InterfaceConsts.Id, VegetableProvider.InterfaceConsts.Name} ;
string[] fromColumns = new string[] { VegetableProvider.InterfaceConsts.Name };
int[] toControlIds = new int[] { Android.Resource.Id.Text1 };

// CursorLoader introduced in Honeycomb (3.0, API_11)
var loader = new CursorLoader(this,
   VegetableProvider.CONTENT_URI, projection, null, null, null);
cursor = (ICursor)loader.LoadInBackground();

// Create a SimpleCursorAdapter
adapter = new SimpleCursorAdapter(this, Android.Resource.Layout.SimpleListItem1, cursor, fromColumns, toControlIds);
listView.Adapter = adapter;
```

O aplicativo resultante tem esta aparência:

[![Captura de tela do aplicativo listando legumes, frutas, botões flor, Legumes, lâmpadas, Tubers](custom-contentprovider-images/api11-contentprovider2.png)](custom-contentprovider-images/api11-contentprovider2.png#lightbox)



## <a name="retrieve-a-single-item-from-a-contentprovider"></a>Recuperar um único Item de um ContentProvider

Um aplicativo de consumo também poderá acessar linhas únicas de dados, que podem ser feitas criando um Uri diferente que se refere a uma linha específica (por exemplo).

Use `ContentResolver` diretamente para acessar um único item, pela criação de um Uri com necessários `Id`.

```csharp
Uri.WithAppendedPath(VegetableProvider.CONTENT_URI, id.ToString());
```

O método complete tem esta aparência:

```csharp
protected void OnListItemClick(object sender, AdapterView.ItemClickEventArgs e)
{
  var id = e.Id;
  string[] projection = new string[] { "name" };
  var uri = Uri.WithAppendedPath(VegetableProvider.CONTENT_URI, id.ToString());
  ICursor vegeCursor = ContentResolver.Query(uri, projection, null, new string[] { id.ToString() }, null);
  string text = "";
  if (vegeCursor.MoveToFirst()) {
    text = vegeCursor.GetInt(0) + " " + vegeCursor.GetString(1);
    Android.Widget.Toast.MakeText(this, text, Android.Widget.ToastLength.Short).Show();
  }
  vegeCursor.Close();
}
```


## <a name="related-links"></a>Links relacionados

- [SimpleContentProvider (exemplo)](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/SimpleContentProvider)
