---
title: Usando o ContentProvider contatos
ms.topic: article
ms.prod: xamarin
ms.assetid: 21C5D1B4-3783-6090-33AB-78A484E65925
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 01/22/2018
ms.openlocfilehash: 730cc1f815641d79350784790e3b33b743d1aebe
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="using-the-contacts-contentprovider"></a>Usando o ContentProvider contatos

Código que use acesso a dados apresentados por um `ContentProvider` não requer uma referência para o `ContentProvider` classe em todos os. Em vez disso, um Uri é usado para criar um cursor com os dados apresentados pelo `ContentProvider`. Android usa o Uri para pesquisar o sistema para o aplicativo que expõe um `ContentProvider` com esse identificador. O Uri é uma cadeia de caracteres, normalmente em um formato de DNS reverso, como `com.android.contacts/data`.

Em vez de fazer com que os desenvolvedores Lembre-se essa cadeia de caracteres, o Android *contatos* provedor expõe seus metadados no `android.provider.ContactsContract` classe. Essa classe é usada para determinar o Uri do `ContentProvider` , bem como os nomes das tabelas e colunas que podem ser consultadas.

Alguns tipos de dados também exigem permissão especial para acessar. A lista de contatos interna requer o `android.permission.READ_CONTACTS` permissão no **AndroidManifest.xml** arquivo.

Há três maneiras de criar um cursor do Uri:

1. **ManagedQuery()** &ndash; a abordagem preferencial no Android 2.3 (API nível 10) e anterior, um `ManagedQuery` retorna um cursor e gerencia automaticamente a atualização de dados e fechar o cursor. Este método é preterido 3.0 Android (API nível 11).

1. **ContentResolver.Query()** &ndash; retorna um cursor não gerenciado, o que significa que deve ser atualizada e fechada explicitamente no código.

1. **CursorLoader(). LoadInBackground()** &ndash; introduzido no Android 3.0 (API nível 11), `CursorLoader` agora é o modo preferencial para consumir um `ContentProvider` . `CursorLoader` consultas um `ContentResolver` em um thread em segundo plano para a interface do usuário não está bloqueado.
   Essa classe pode ser acessada em versões mais antigas do Android usando a biblioteca de compatibilidade de v4.


Cada um desses métodos tem o mesmo conjunto básico de entradas:

-  **URI** &ndash; o nome totalmente qualificado do `ContentProvider` .
-  **Projeção** &ndash; especificação de quais colunas a serem selecionadas para o cursor.
-  **Seleção de** &ndash; semelhante a um SQL `WHERE` cláusula.
-  **SelectionArgs** &ndash; parâmetros a serem substituídos na seleção.
-  **SortOrder** &ndash; colunas para classificar por.



## <a name="creating-inputs-for-a-query"></a>Criando entradas para uma consulta

O `ContactsProvider` código de exemplo executa uma consulta muito simple no provedor interno de contatos do Android. Você não precisa saber os Uri ou coluna nomes reais - todas as informações necessárias para consultar os contatos `ContentProvider` está disponível como constantes expostos pelo `ContactsContract` classe.

Independentemente de qual método é usado para recuperar o cursor, esses mesmos objetos são usados como parâmetros conforme o *ContactsProvider/ContactsAdapter.cs* arquivo:

```csharp
var uri = ContactsContract.Contacts.ContentUri;
string[] projection = {
   ContactsContract.Contacts.InterfaceConsts.Id,
   ContactsContract.Contacts.InterfaceConsts.DisplayName,
   ContactsContract.Contacts.InterfaceConsts.PhotoId,
};
```

Neste exemplo, o `selection`, `selectionArgs` e `sortOrder` será ignorado pelo configurá-los com `null`.



## <a name="creating-a-cursor-from-a-content-provider-uri"></a>Criar um Cursor de um Uri do provedor de conteúdo

Depois que os objetos de parâmetro tiverem sido criados, eles podem ser usados em uma das três maneiras a seguir:



### <a name="using-a-managed-query"></a>Usando uma consulta gerenciada

Aplicativos voltados para o Android 2.3 (API nível 10) ou anterior, deve usar esse método:

```csharp
var cursor = activity.ManagedQuery(uri, projection, null, null, null);
```

Esse cursor será gerenciado pelo Android, você não precisa fechá-lo.



### <a name="using-contentresolver"></a>Usando ContentResolver

Acessando `ContentResolver` diretamente para obter um cursor em relação a um `ContentProvider` pode ser assim:

```csharp
var cursor = activity.ContentResolver(uri, projection, null, null, null);
```

Esse cursor é não gerenciado, para que ele deve ser fechado quando não é mais necessário.
Certifique-se de que o código fecha um cursor que é aberto, caso contrário, ocorrerá um erro.

```csharp
cursor.Close();
```

Como alternativa, você pode chamar `StartManagingCursor()` e `StopManagingCursor()` ' Gerenciar ' o cursor. Cursores gerenciados são automaticamente desativados e consultadas novamente quando as atividades interrompidas e reiniciadas.



### <a name="using-cursorloader"></a>Usando CursorLoader

Aplicativos criados para o Android 3.0 (API nível 11) ou mais recente deve usar esse método:

```csharp
var loader = new CursorLoader (activity, uri, projection, null, null, null);
var cursor = (ICursor)loader.LoadInBackground();
```

O `CursorLoader` garante que todas as operações de cursor são feitas em um thread em segundo plano em podem inteligente reutilizar um cursor existente em instâncias de atividade quando uma atividade é reiniciada (por exemplo, devido a uma alteração de configuração), em vez que recarregar os dados novamente.

Versões anteriores de Android também podem usar o `CursorLoader` classe usando o [v4 suporte bibliotecas](http://developer.android.com/tools/support-library/index.html).



## <a name="displaying-the-cursor-data-with-a-custom-adapter"></a>Exibindo os dados de Cursor com um adaptador personalizado

Para exibir a imagem de contato, usaremos um adaptador personalizado, para que pode resolver manualmente o `PhotoId` referência a um caminho de arquivo de imagem.

Para exibir dados com um adaptador personalizado, o exemplo usa um `CursorLoader` para recuperar todos os dados de contato em uma coleção de local no **FillContacts** método de **ContactsProvider/ContactsAdapter.cs**:

```csharp
void FillContacts ()
{
   var uri = ContactsContract.Contacts.ContentUri;
   string[] projection = {
       ContactsContract.Contacts.InterfaceConsts.Id,
       ContactsContract.Contacts.InterfaceConsts.DisplayName,
       ContactsContract.Contacts.InterfaceConsts.PhotoId
  };
   // CursorLoader introduced in Honeycomb (3.0, API11)
   var loader = new CursorLoader(activity, uri, projection, null, null, null);
   var cursor = (ICursor)loader.LoadInBackground();
   contactList = new List<Contact> ();
   if (cursor.MoveToFirst ()) {
      do {
          contactList.Add (new Contact{
              Id = cursor.GetLong (cursor.GetColumnIndex (projection [0])),
              DisplayName = cursor.GetString (cursor.GetColumnIndex (projection [1])),
              PhotoId = cursor.GetString (cursor.GetColumnIndex (projection [2]))
          });
       } while (cursor.MoveToNext());
   }
}
```

Em seguida, implementar métodos do BaseAdapter usando o `contactList` coleção. O adaptador é implementado como seria com qualquer outra coleção &ndash; não há nenhum tratamento especial aqui porque os dados foi originados em um `ContentProvider`:

```csharp
Activity activity;
public ContactsAdapter (Activity activity)
{
   this.activity = activity;
   FillContacts ();
}
public override int Count {
   get { return contactList.Count; }
}
public override Java.Lang.Object GetItem (int position)
{
  return null; // could wrap a Contact in a Java.Lang.Object to return it here if needed
}
public override long GetItemId (int position)
{
   return contactList [position].Id;
}
public override View GetView (int position, View convertView, ViewGroup parent)
{
   var view = convertView ?? activity.LayoutInflater.Inflate (Resource.Layout.ContactListItem, parent, false);
   var contactName = view.FindViewById<TextView> (Resource.Id.ContactName);
   var contactImage = view.FindViewById<ImageView> (Resource.Id.ContactImage);
   contactName.Text = contactList [position].DisplayName;
   if (contactList [position].PhotoId == null) {
       contactImage = view.FindViewById<ImageView> (Resource.Id.ContactImage);
       contactImage.SetImageResource (Resource.Drawable.ContactImage);
   } else {
       var contactUri = ContentUris.WithAppendedId (ContactsContract.Contacts.ContentUri, contactList [position].Id);
       var contactPhotoUri = Android.Net.Uri.WithAppendedPath (contactUri, Contacts.Photos.ContentDirectory);
       contactImage.SetImageURI (contactPhotoUri);
   }
   return view;
}
```

A imagem é exibida (se houver) usando o Uri para o arquivo de imagem no dispositivo. O aplicativo tem esta aparência:

[![Captura de tela do aplicativo exibindo contatos em uma ListView; uma imagem é exibida à esquerda de uma entrada](contacts-contentprovider-images/contactsprovider.png)](contacts-contentprovider-images/contactsprovider.png#lightbox)

Usando um padrão de código semelhante, seu aplicativo pode acessar uma ampla variedade de dados de sistema, incluindo o usuário fotos, vídeos e música.
Alguns tipos de dados exigem permissões especiais ao ser solicitado do projeto **AndroidManifest.xml**.



## <a name="displaying-the-cursor-data-with-a-simplecursoradapter"></a>Exibindo os dados de Cursor com um SimpleCursorAdapter

O cursor também pode ser exibido com um `SimpleCursorAdapter` (embora somente o nome será exibido, não a foto). Esse código demonstra como usar um `ContentProvider` com `SimpleCursorAdapter` (esse código não aparecem no exemplo):

```csharp
var uri = ContactsContract.Contacts.ContentUri;
string[] projection = {
   ContactsContract.Contacts.InterfaceConsts.Id,
   ContactsContract.Contacts.InterfaceConsts.DisplayName
};
var loader = new CursorLoader (this, uri, projection, null, null, null);
var cursor = (ICursor)loader.LoadInBackground();
var fromColumns = new string[] {ContactsContract.Contacts.InterfaceConsts.DisplayName};
var toControlIds = new int[] {Android.Resource.Id.Text1};
adapter = new SimpleCursorAdapter (this, Android.Resource.Layout.SimpleListItem1, cursor, fromColumns, toControlsIds);
listView.Adapter = adapter;
```

Consulte o [ListViews e adaptadores](~/android/user-interface/layouts/list-view/index.md) para obter mais informações sobre como implementar `SimpleCursorAdapter`.


## <a name="related-links"></a>Links relacionados

- [Demonstração de ContactsAdapter (exemplo)](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/ContactsAdapterDemo/)
