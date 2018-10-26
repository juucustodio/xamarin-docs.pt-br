---
title: Criar um ContentProvider personalizado
description: A seção anterior demonstrou como consumir dados de uma implementação de ContentProvider interna. Esta seção explicará como criar um ContentProvider personalizado e, em seguida, consumir seus dados.
ms.prod: xamarin
ms.assetid: 36742B59-607E-070E-5D0E-B9C18917D3F4
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/07/2018
ms.openlocfilehash: da8aacac1f282fefb6b8d0e84cae168cf3a7148b
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108009"
---
# <a name="creating-a-custom-contentprovider"></a>Criar um ContentProvider personalizado

_A seção anterior demonstrou como consumir dados de uma implementação de ContentProvider interna. Esta seção explicará como criar um ContentProvider personalizado e, em seguida, consumir seus dados._

## <a name="about-contentproviders"></a>Sobre ContentProviders

Uma classe de provedor de conteúdo deve herdar de `ContentProvider`. Ele deve consistir em um repositório de dados interno que é usado para responder a consultas e ela deve expor Uris e tipos de MIME, conforme constantes para ajudar a consumindo código fazem solicitações válidas para os dados.

### <a name="uri-authority"></a>URI (autoridade de certificação)

`ContentProviders` são acessados no Android usando um Uri. Um aplicativo que expõe um `ContentProvider` define os Uris que ele responderá a em seu **androidmanifest. XML** arquivo. Quando o aplicativo é instalado, esses Uris são registrados para que outros aplicativos podem acessá-los.

No Mono para Android, a classe de provedor de conteúdo deve ter uma `[ContentProvider]` atributo para especificar o Uri (ou Uris) que deve ser adicionado ao **androidmanifest. XML**.


### <a name="mime-type"></a>Tipo de MIME

O formato típico para tipos MIME consiste em duas partes. Android `ContentProviders` geralmente usam essas duas cadeias de caracteres para a primeira parte do tipo MIME:

1. `vnd.android.cursor.item` &ndash; para representar uma única linha, use o `ContentResolver.CursorItemBaseType` constante no código.

1. `vnd.android.cursor.dir` &ndash; para várias linhas, use o `ContentResolver.CursorDirBaseType` constante no código.

A segunda parte do tipo MIME é específica para seu aplicativo e deve usar um padrão de DNS reverso com um `vnd.` prefixo. O código de exemplo usa `vnd.com.xamarin.sample.Vegetables`.


### <a name="data-model-metadata"></a>Metadados do modelo de dados

Aplicativos de consumo precisam construir consultas de Uri para acessar diferentes tipos de dados. O Uri de base pode ser expandido para fazer referência a uma determinada tabela de dados e também pode incluir parâmetros para filtrar os resultados. As colunas e as cláusulas usadas com o cursor resultante para exibir os dados também devem ser declaradas.

Para garantir que apenas as consultas Uri válidas são construídas, é comum para fornecer as cadeias de caracteres válidas como valores de constante. Isso torna mais fácil para acessar o `ContentProvider` porque ele faz com que os valores podem ser descobertos por meio de conclusão de código e evita erros de digitação nas cadeias de caracteres.

No exemplo anterior a `android.provider.ContactsContract` classe exposta os metadados para os dados de contatos. Para nosso custom `ContentProvider` vamos apenas expor as constantes na classe em si.


## <a name="implementation"></a>Implementação

Há três etapas para criar e consumir um personalizado `ContentProvider`:

1. **Criar uma classe de banco de dados** &ndash; implementar `SQLiteOpenHelper`.

2. **Criar uma `ContentProvider` classe** &ndash; implementam `ContentProvider` com uma instância do banco de dados, os metadados expostos como valores constantes e métodos para acessar os dados.

3. **Acesso a `ContentProvider` por meio de seu Uri** &ndash; popular uma `CursorAdapter` usando o `ContentProvider`, acessado por meio de seu Uri.

Conforme discutido anteriormente, `ContentProviders` pode ser consumido de aplicativos diferentes em que eles são definidos. Neste exemplo, os dados são consumidos no mesmo aplicativo, mas tenha em mente que outros aplicativos também podem acessá--lo, desde que eles conhecem o Uri e informações sobre o esquema (que geralmente é exposta como valores de constante).


## <a name="create-a-database"></a>Criar um banco de dados

A maioria dos `ContentProvider` implementações se basearão em uma `SQLite` banco de dados. O código de banco de dados de exemplo na **SimpleContentProvider/VegetableDatabase.cs** cria um banco de dados muito simple de duas colunas, conforme mostrado:

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

A implementação do banco de dados em si não precisa ser expostos com considerações especiais uma `ContentProvider`, no entanto se você pretende associar o `ContentProvider's` dados para um `ListView` controlar, em seguida, uma coluna de inteiro exclusivo chamada `_id` devem fazer parte do conjunto de resultados. Consulte a [ListViews e adaptadores](~/android/user-interface/layouts/list-view/index.md) documento para obter mais detalhes sobre como usar o `ListView` controle.


## <a name="create-the-contentprovider"></a>Criar o ContentProvider

O restante desta seção fornece instruções passo a passo sobre como o **SimpleContentProvider/VegetableProvider.cs** classe de exemplo foi criado.


### <a name="initialize-the-database"></a>Inicializar o banco de dados

A primeira etapa é a subclasse `ContentProvider` e adicione o banco de dados que será usado.

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

O restante do código formará a implementação de provedor de conteúdo real que permite que os dados a serem descobertos e consultados.



## <a name="add-metadata-for-consumers"></a>Adicionar metadados para os consumidores

Há quatro tipos diferentes de metadados, vamos expor no `ContentProvider` classe. Apenas a autoridade necessária, o restante são feitas por convenção.

- **Autoridade** &ndash; as `ContentProvider` atributo *deve* ser adicionado à classe para que ele é registrado com o Android quando o aplicativo está instalado.

- **URI** &ndash; o `CONTENT_URI` é exposta como uma constante para que seja fácil de usar no código. Ele deve corresponder a autoridade, mas incluem o esquema e o caminho base.

- **Tipos de MIME** &ndash; listas de resultados e resultados únicos são tratadas como diferentes tipos de conteúdo, portanto, definimos dois tipos de MIME para representá-los.

- **InterfaceConsts** &ndash; fornecer um valor constante para cada nome de coluna de dados, para que o consumo código pode facilmente descobrir e consultá-los sem arriscar erros tipográficos.

Este código mostra como cada um desses itens é implementada, adicionando a definição do banco de dados da etapa anterior:

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


## <a name="implement-the-uri-parsing-helper"></a>Implementar o auxiliar de análise de URI

Como o consumo código usa Uris para fazer solicitações de um `ContentProvider`, é preciso ser capaz de analisar essas solicitações para determinar quais dados serão retornados. O `UriMatcher` classe pode ajudar a analisar Uris, depois que ele foi inicializado com o Uri de padrões que o `ContentProvider` dá suporte.

O `UriMatcher` no exemplo será inicializado com dois Uris:

1. *"" com.xamarin.sample.VegetableProvider/vegetables* &ndash; solicitação para retornar a lista completa de legumes.

2. *"com.xamarin.sample.VegetableProvider/vegetables/\#"* &ndash; onde a \# é um espaço reservado para um parâmetro numérico (o `_id` da linha no banco de dados). Um espaço reservado de asterisco ("\*") também pode ser usado para corresponder a um parâmetro de texto.

No código, usamos as constantes para fazer referência a valores de metadados, como a autoridade e a BASE\_caminho. Os códigos de retorno serão usados em métodos que fazem de análise de Uri, para determinar quais dados serão retornados.

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

Esse código é tudo privado para o `ContentProvider` classe. Consulte a [documentação de UriMatcher do Google](https://developer.xamarin.com/api/type/Android.Content.UriMatcher/) para obter mais informações.


## <a name="implement-the-querymethod"></a>Implementar o QueryMethod

A forma mais simples `ContentProvider` é o método para implementar o `Query` método. A implementação abaixo usa o `UriMatcher` para analisar o `uri` parâmetro e chame o método de banco de dados correto. Se o `uri` contém um parâmetro de ID, em seguida, o inteiro é analisado (usando `LastPathSegment`) e usado na consulta de banco de dados.

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
Isso pode informar o aplicativo de consumo como lidar com esses dados.

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

Nosso exemplo simple não permite a edição ou exclusão de dados, mas os métodos Insert, Update e Delete devem ser implementados então adicioná-los sem uma implementação:

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

Isso conclui o basic `ContentProvider` implementação. Depois que o aplicativo tiver sido instalado, os dados que ele expõe estarão disponíveis tanto dentro do aplicativo, mas também para qualquer outro aplicativo que sabe que o Uri para fazer referência a ela.


## <a name="access-the-contentprovider"></a>Acessar o ContentProvider

Uma vez o `VegetableProvider` tiver sido implementado, acessá-lo é feito da mesma forma que o provedor de contatos no início deste documento: obter um cursor usando o Uri especificado e, em seguida, usar um adaptador para acessar os dados.


## <a name="bind-a-listview-to-a-contentprovider"></a>Associar um ListView a um ContentProvider

Para popular um `ListView` com os dados, podemos usar o Uri que corresponde à lista não filtrada de legumes. No código, usamos o valor da constante `VegetableProvider.CONTENT_URI`, que sabemos que resolvem para `com.xamarin.sample.vegetableprovider/vegetables`. Nossos `VegetableProvider.Query` implementação retornará um cursor que, em seguida, pode ser vinculado ao `ListView`.

O código na `SimpleContentProvider/HomeScreen.cs` mostra como é simples exibir dados de um `ContentProvider`:

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

[![Captura de tela do aplicativo listando legumes, frutas, botões de flor, Legumes, lâmpadas, Tubers](custom-contentprovider-images/api11-contentprovider2.png)](custom-contentprovider-images/api11-contentprovider2.png#lightbox)



## <a name="retrieve-a-single-item-from-a-contentprovider"></a>Recuperar um único Item de um ContentProvider

Um aplicativo de consumo também poderá acessar linhas simples de dados, que podem ser feitas criando um Uri diferente que se refere a uma linha específica (por exemplo).

Use `ContentResolver` diretamente para acessar um único item, ao criar um Uri com os necessários `Id`.

```csharp
Uri.WithAppendedPath(VegetableProvider.CONTENT_URI, id.ToString());
```

O método completo tem esta aparência:

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

- [SimpleContentProvider (amostra)](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/SimpleContentProvider)
