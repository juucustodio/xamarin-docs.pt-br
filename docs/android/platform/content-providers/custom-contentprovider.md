---
title: Criando um ContentProvider personalizado
description: A seção anterior demonstrou como consumir dados de uma implementação incorporada do ContentProvider. Esta seção explicará como construir um Provedor de Conteúdo personalizado e, em seguida, consumirá seus dados.
ms.prod: xamarin
ms.assetid: 36742B59-607E-070E-5D0E-B9C18917D3F4
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/07/2018
ms.openlocfilehash: 3e57e0cd2fa87db8035fa68995b69f231151fa09
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73020534"
---
# <a name="creating-a-custom-contentprovider"></a>Criando um ContentProvider personalizado

_A seção anterior demonstrou como consumir dados de uma implementação incorporada do ContentProvider. Esta seção explicará como construir um Provedor de Conteúdo personalizado e, em seguida, consumirá seus dados._

## <a name="about-contentproviders"></a>Sobre os Provedores de Conteúdo

Uma classe de provedor `ContentProvider`de conteúdo deve herdar de . Ele deve consistir em um armazenamento de dados interno que é usado para responder a consultas e deve expor Uris e Mime Types como constantes para ajudar a consumir código fazer solicitações válidas de dados.

### <a name="uri-authority"></a>URI (Autoridade)

`ContentProviders`são acessados no Android usando um Uri. Um aplicativo que `ContentProvider` expõe um conjunto de Uris que ele responderá em seu arquivo **AndroidManifest.xml.** Quando o aplicativo é instalado, esses Uris são registrados para que outros aplicativos possam acessá-los.

Em Mono para Android, a classe `[ContentProvider]` do provedor de conteúdo deve ter um atributo para especificar o Uri (ou Uris) que deve ser adicionado ao **AndroidManifest.xml**.

### <a name="mime-type"></a>Tipo de Mime

O formato típico para tipos MIME consiste em duas partes. O `ContentProviders` Android geralmente usa essas duas strings para a primeira parte do tipo MIME:

1. `vnd.android.cursor.item`&ndash; para representar uma única `ContentResolver.CursorItemBaseType` linha, use a constante em código.

1. `vnd.android.cursor.dir`&ndash; para várias linhas, `ContentResolver.CursorDirBaseType` use a constante em código.

A segunda parte do Type MIME é específica para a sua aplicação, `vnd.` e deve usar um padrão DNS reverso com um prefixo. O código `vnd.com.xamarin.sample.Vegetables`de amostra usa .

### <a name="data-model-metadata"></a>Metadados do modelo de dados

Os aplicativos de consumo precisam construir consultas uri para acessar diferentes tipos de dados. A base Uri pode ser expandida para se referir a uma determinada tabela de dados e também pode incluir parâmetros para filtrar os resultados. As colunas e cláusulas usadas com o cursor resultante para exibir dados também devem ser declaradas.

Para garantir que apenas consultas uri válidas sejam construídas, é costume fornecer as strings válidas como valores constantes. Isso facilita o `ContentProvider` acesso porque torna os valores descobertos via conclusão de código, e evita erros de digitação nas strings.

No exemplo anterior, a `android.provider.ContactsContract` classe expôs os metadados dos dados contatos. Para nosso `ContentProvider` costume, vamos apenas expor as constantes na própria classe.

## <a name="implementation"></a>Implementação

Existem três passos para criar `ContentProvider`e consumir um costume:

1. **Crie uma classe de banco de** &ndash; dados Implement . `SQLiteOpenHelper`

2. **Crie `ContentProvider` um implemento** &ndash; `ContentProvider` de classe com uma instância do banco de dados, metadados expostos como valores e métodos constantes para acessar os dados.

3. **Acesse `ContentProvider` o através de seu Uri** &ndash; Populate a `CursorAdapter` usando o `ContentProvider`, acessado através de seu Uri.

Como discutido anteriormente, `ContentProviders` pode ser consumido a partir de aplicações diferentes de onde eles são definidos. Neste exemplo, os dados são consumidos no mesmo aplicativo, mas tenha em mente que outros aplicativos também podem acessá-los desde que conheçam o Uri e informações sobre o esquema (que geralmente é exposto como valores constantes).

## <a name="create-a-database"></a>Criar um banco de dados

A `ContentProvider` maioria das implementações `SQLite` será baseada em um banco de dados. O código de banco de dados de exemplo em **SimpleContentProvider/VegetableDatabase.cs** cria um banco de dados de duas colunas muito simples, como mostrado:

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

A implementação do banco de dados em si `ContentProvider`não precisa de considerações especiais para ser exposta com um , no entanto, se você pretende vincular os `ContentProvider's` dados a um `ListView` controle, então uma coluna inteira única nomeada `_id` deve fazer parte do conjunto de resultados. Consulte o documento [ListViews and Adapters](~/android/user-interface/layouts/list-view/index.md) `ListView` para obter mais detalhes sobre o uso do controle.

## <a name="create-the-contentprovider"></a>Criar o ContentProvider

O resto desta seção fornece instruções passo a passo sobre como a classe de exemplo **SimpleContentProvider/VegetableProvider.cs** foi construída.

### <a name="initialize-the-database"></a>Inicialize o Banco de Dados

O primeiro passo é `ContentProvider` subclasse e adicionar o banco de dados que ele usará.

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

O resto do código formará a implementação real do provedor de conteúdo que permite que os dados sejam descobertos e consultados.

## <a name="add-metadata-for-consumers"></a>Adicionar metadados para consumidores

Existem quatro tipos diferentes de metadados que `ContentProvider` vamos expor na classe. Apenas a autoridade é necessária, o resto é feito por convenção.

- **Autoridade** &ndash; `ContentProvider` O atributo *deve* ser adicionado à classe para que ele seja registrado no Android quando o aplicativo estiver instalado.

- **Uri** &ndash; `CONTENT_URI` O é exposto como uma constante para que seja fácil de usar em código. Deve corresponder à Autoridade, mas incluir o esquema e o caminho base.

- **Tipos** &ndash; de MIME Listas de resultados e resultados únicos são tratados como diferentes tipos de conteúdo, por isso definimos dois tipos mime para representá-los.

- **InterfaceConsts** &ndash; Fornecer um valor constante para cada nome da coluna de dados, de modo que o código de consumo pode facilmente descobrir e se referir a eles sem arriscar erros tipográficos.

Este código mostra como cada um desses itens é implementado, adicionando à definição de banco de dados da etapa anterior:

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

## <a name="implement-the-uri-parsing-helper"></a>Implementar o uri parsing helper

Como o código de consumo usa `ContentProvider`Uris para fazer solicitações de um, precisamos ser capazes de analisar essas solicitações para determinar quais dados retornar. A `UriMatcher` classe pode ajudar a analisar Uris, uma vez que `ContentProvider` foi inicializado com os padrões Uri que o suporte suporta.

O `UriMatcher` exemplo será inicializado com dois Uris:

1. *"com.xamarin.sample.VegetableProvider/vegetables"* &ndash; solicite a devolução da lista completa de legumes.

2. *"com.xamarin.sample.VegetableProvider/vegetables/\#"* &ndash; \# onde o espaço reservado para um parâmetro `_id` numérico (o da linha no banco de dados). Um espaço reservado asterisco ("\*") também pode ser usado para corresponder a um parâmetro de texto.

No código usamos as constantes para se referir a\_valores de metadados como o AUTHORITY e o BASE PATH. Os códigos de devolução serão usados em métodos que fazem a análise uri, para determinar quais dados retornar.

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

Este código é todo `ContentProvider` privado para a classe. Consulte a [documentação urimatcher do Google](xref:Android.Content.UriMatcher) para obter mais informações.

## <a name="implement-the-querymethod"></a>Implementar o Método de Consulta

O método `ContentProvider` mais simples `Query` de implementar é o método. A implementação `UriMatcher` abaixo usa `uri` o parâmetro para analisar o parâmetro e chamar o método correto de banco de dados. Se `uri` o conter um parâmetro de ID, o inteiro `LastPathSegment`será analisado (usando) e usado na consulta do banco de dados.

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

O `GetType` método também deve ser substituído. Este método pode ser chamado para determinar o tipo de conteúdo que será devolvido para um determinado Uri.
Isso pode dizer ao aplicativo de consumo como lidar com esses dados.

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

Nosso exemplo simples não permite a edição ou exclusão de dados, mas os métodos Inserir, Atualizar e Excluir devem ser implementados para adicioná-los sem uma implementação:

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

Isso completa a `ContentProvider` implementação básica. Uma vez instalado o aplicativo, os dados que ele expõe estarão disponíveis tanto dentro do aplicativo como também para qualquer outro aplicativo que conheça o Uri para referencia-lo.

## <a name="access-the-contentprovider"></a>Acesse o ContentProvider

Uma `VegetableProvider` vez implementado, o acesso é feito da mesma forma que o provedor contatos no início deste documento: obtenha um cursor usando o Uri especificado e, em seguida, use um adaptador para acessar os dados.

## <a name="bind-a-listview-to-a-contentprovider"></a>Vincular uma listaVer a um Provedor de conteúdo

Para preencher `ListView` um com dados usamos o Uri que corresponde à lista não filtrada de vegetais. No código usamos o `VegetableProvider.CONTENT_URI`valor constante, que `com.xamarin.sample.vegetableprovider/vegetables`sabemos que resolve. Nossa `VegetableProvider.Query` implementação retornará um cursor que `ListView`pode então ser vinculado ao .

O código `SimpleContentProvider/HomeScreen.cs` em mostra como é simples `ContentProvider`exibir dados de:

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

A aplicação resultante é a seguinte:

[![Captura de tela de app listando Legumes, Frutas, Botões de Flores, Legumes, Bulbos, Tubérculos](custom-contentprovider-images/api11-contentprovider2.png)](custom-contentprovider-images/api11-contentprovider2.png#lightbox)

## <a name="retrieve-a-single-item-from-a-contentprovider"></a>Recuperar um único item de um provedor de conteúdo

Um aplicativo de consumo também pode querer acessar linhas únicas de dados, o que pode ser feito construindo um Uri diferente que se refere a uma linha específica (por exemplo).

Use `ContentResolver` diretamente para acessar um único item, construindo `Id`um Uri com o necessário .

```csharp
Uri.WithAppendedPath(VegetableProvider.CONTENT_URI, id.ToString());
```

O método completo é assim:

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

- [SimpleContentProvider (amostra)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/platformfeatures-simplecontentprovider)
