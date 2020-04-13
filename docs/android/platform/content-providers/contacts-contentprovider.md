---
title: Usando o ContentProvider de contatos
ms.prod: xamarin
ms.assetid: 21C5D1B4-3783-6090-33AB-78A484E65925
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 01/22/2018
ms.openlocfilehash: fca57b7af34ae2b28dda9bf20a95183138cbc641
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73020538"
---
# <a name="using-the-contacts-contentprovider"></a>Usando o ContentProvider de contatos

O código que usa dados `ContentProvider` de acesso expostos `ContentProvider` por a não requer uma referência à classe. Em vez disso, um Uri é usado para criar `ContentProvider`um cursor sobre os dados expostos pelo . O Android usa o Uri para pesquisar o `ContentProvider` sistema para o aplicativo que expõe um com esse identificador. O Uri é uma string, tipicamente em um `com.android.contacts/data`formato DNS reverso, como .

Em vez de fazer os desenvolvedores lembrarem dessa string, `android.provider.ContactsContract` o provedor de *Contatos* do Android expõe seus metadados na classe. Esta classe é usada para `ContentProvider` determinar o Uri do, bem como os nomes das tabelas e colunas que podem ser consultados.

Alguns tipos de dados também exigem permissão especial para acessar. A lista de contatos `android.permission.READ_CONTACTS` incorporada requer a permissão no arquivo **AndroidManifest.xml.**

Existem três maneiras de criar um cursor a partir do Uri:

1. **ManagedQuery()** &ndash; A abordagem preferida no Android 2.3 (API Nível 10) e anteriormente, um `ManagedQuery` retorna um cursor e também gerencia automaticamente a atualização dos dados e o fechamento do cursor. Este método é preterido no Android 3.0 (API Nível 11).

1. **ContentResolver.Query()** &ndash; Retorna um cursor não gerenciado, o que significa que ele deve ser atualizado e fechado explicitamente em código.

1. **CursorLoader(). LoadInBackground()** &ndash; Introduzido no Android 3.0 (API Nível 11), `CursorLoader` `ContentProvider` é agora a maneira preferida de consumir um . `CursorLoader`consulta um `ContentResolver` em um segmento de fundo para que a ui não seja bloqueada.
   Esta classe pode ser acessada em versões mais antigas do Android usando a biblioteca de compatibilidade v4.

Cada um desses métodos tem o mesmo conjunto básico de entradas:

- **Uri** &ndash; O nome totalmente `ContentProvider` qualificado do .
- **Especificação** &ndash; de projeção de quais colunas selecionar para o cursor.
- **Seleção** &ndash; Semelhante a `WHERE` uma cláusula SQL.
- **SeleçãoParâmetros** &ndash; a serem substituídos na Seleção.
- **Classificar colunas de ordem** &ndash; para classificar.

## <a name="creating-inputs-for-a-query"></a>Criando entradas para uma consulta

O `ContactsProvider` código de exemplo executa uma consulta muito simples contra o provedor de contatos incorporados do Android. Você não precisa saber os nomes reais de Uri ou coluna `ContentProvider` - todas as informações necessárias para consultar os Contatos estão disponíveis como constantes expostas pela `ContactsContract` classe.

Independentemente de qual método é usado para recuperar o cursor, esses mesmos objetos são usados como parâmetros mostrados no arquivo *ContactsProvider/ContactsAdapter.cs:*

```csharp
var uri = ContactsContract.Contacts.ContentUri;
string[] projection = {
   ContactsContract.Contacts.InterfaceConsts.Id,
   ContactsContract.Contacts.InterfaceConsts.DisplayName,
   ContactsContract.Contacts.InterfaceConsts.PhotoId,
};
```

Para este exemplo, `selectionArgs` `sortOrder` o `selection`, e será `null`ignorado definindo-os para .

## <a name="creating-a-cursor-from-a-content-provider-uri"></a>Criando um cursor a partir de um Uri provedor de conteúdo

Uma vez que os objetos de parâmetro tenham sido criados, eles podem ser usados de uma das três maneiras a seguir:

### <a name="using-a-managed-query"></a>Usando uma consulta gerenciada

Aplicativos direcionados ao Android 2.3 (API Nível 10) ou anteriores devem usar este método:

```csharp
var cursor = activity.ManagedQuery(uri, projection, null, null, null);
```

Este cursor será gerenciado pelo Android para que você não precise fechá-lo.

### <a name="using-contentresolver"></a>Usando conteúdoresolver

Acessar `ContentResolver` diretamente para obter um `ContentProvider` cursor contra um pode ser feito assim:

```csharp
var cursor = activity.ContentResolver(uri, projection, null, null, null);
```

Este cursor não é gerenciado, por isso deve ser fechado quando não for mais necessário.
Certifique-se de que o código fecha um cursor aberto, caso contrário, ocorrerá um erro.

```csharp
cursor.Close();
```

Alternativamente, você `StartManagingCursor()` pode `StopManagingCursor()` ligar e 'gerenciar' o cursor. Os cursores gerenciados são automaticamente desativados e re-consultados quando as atividades são interrompidas e reiniciadas.

### <a name="using-cursorloader"></a>Usando cursorLoader

Aplicativos construídos para Android 3.0 (API Nível 11) ou mais recente devem usar este método:

```csharp
var loader = new CursorLoader (activity, uri, projection, null, null, null);
var cursor = (ICursor)loader.LoadInBackground();
```

O `CursorLoader` assegura que todas as operações do cursor sejam feitas em um segmento de fundo e pode reutilizar inteligentemente um cursor existente em instâncias de atividade quando uma atividade é reiniciada (por exemplo, devido a uma alteração de configuração) em vez de recarregar os dados novamente.

Versões anteriores `CursorLoader` do Android também podem usar a classe usando as [bibliotecas de suporte v4](https://developer.android.com/tools/support-library/index.html).

## <a name="displaying-the-cursor-data-with-a-custom-adapter"></a>Exibindo os dados do cursor com um adaptador personalizado

Para exibir a imagem de contato, usaremos um adaptador personalizado, para que possamos resolver manualmente a `PhotoId` referência a um caminho de arquivo de imagem.

Para exibir dados com um adaptador `CursorLoader` personalizado, o exemplo usa um para recuperar todos os dados de contato em uma coleção local no método **FillContacts** do **ContactsProvider/ContactsAdapter.cs**:

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

Em seguida, implemente os métodos `contactList` do BaseAdapter usando a coleção. O adaptador é implementado assim como seria &ndash; com qualquer outra coleção não há tratamento `ContentProvider`especial aqui porque os dados são provenientes de um :

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

A imagem é exibida (se existir) usando o Uri para o arquivo de imagem no dispositivo. O aplicativo é assim:

[![Captura de tela do aplicativo que exibe contatos em uma ListView; uma imagem é exibida à esquerda de uma entrada](contacts-contentprovider-images/contactsprovider.png)](contacts-contentprovider-images/contactsprovider.png#lightbox)

Usando um padrão de código semelhante, seu aplicativo pode acessar uma grande variedade de dados do sistema, incluindo fotos, vídeos e músicas do usuário.
Alguns tipos de dados exigem permissões especiais para serem solicitadas no **AndroidManifest.xml**do projeto.

## <a name="displaying-the-cursor-data-with-a-simplecursoradapter"></a>Exibindo os dados do cursor com um SimpleCursorAdapter

O cursor também pode ser `SimpleCursorAdapter` exibido com um (embora apenas o nome seja exibido, não a foto). Este código demonstra como `ContentProvider` `SimpleCursorAdapter` usar a (este código não aparece na amostra):

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

Consulte o [ListViews e os Adaptadores](~/android/user-interface/layouts/list-view/index.md) para obter mais informações sobre a implementação `SimpleCursorAdapter`.

## <a name="related-links"></a>Links relacionados

- [Demonstração do Adaptador de Contatos (amostra)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/platformfeatures-contactsadapterdemo)
