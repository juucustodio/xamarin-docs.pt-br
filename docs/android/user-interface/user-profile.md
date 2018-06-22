---
title: Perfil de usuário
ms.prod: xamarin
ms.assetid: 6BB01F75-5E98-49A1-BBA0-C2680905C59D
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/22/2018
ms.openlocfilehash: 1eaae86ab9eacf007eca792d96e889db6f367922
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
ms.locfileid: "30765500"
---
# <a name="user-profile"></a>Perfil de usuário

Android tem suporte para enumerar os contatos com a [ContactsContract](https://developer.xamarin.com/api/type/Android.Provider.ContactsContract/) provedor desde API nível 5. Por exemplo, a lista de contatos é tão simple quanto usar o [ContactContracts.Contacts](https://developer.xamarin.com/api/type/Android.Provider.ContactsContract+Contacts/) classe conforme mostrado no exemplo de código a seguir:

```csharp
// Get the URI for the user's contacts:
var uri = ContactsContract.Contacts.ContentUri;

// Setup the "projection" (columns we want) for only the ID and display name:
string[] projection = {
    ContactsContract.Contacts.InterfaceConsts.Id, 
    ContactsContract.Contacts.InterfaceConsts.DisplayName };

// Use a CursorLoader to retrieve the user's contacts data:
CursorLoader loader = new CursorLoader(this, uri, projection, null, null, null);
ICursor cursor = (ICursor)loader.LoadInBackground();

// Print the contact data to the console if reading back succeeds:
if (cursor != null)
{
    if (cursor.MoveToFirst())
    {
        do
        {
            Console.WriteLine("Contact ID: {0}, Contact Name: {1}",
                               cursor.GetString(cursor.GetColumnIndex(projection[0])),
                               cursor.GetString(cursor.GetColumnIndex(projection[1])));
        } while (cursor.MoveToNext());
    }
}
```

Começando com o Android 4 (API nível 14), o [ContactsContact.Profile](https://developer.xamarin.com/api/type/Android.Provider.ContactsContract+Profile/) classe está disponível por meio de `ContactsContract` provedor. O `ContactsContact.Profile` fornece acesso ao perfil pessoal para o proprietário de um dispositivo, o que inclui dados de contato, como do proprietário do dispositivo nome e número de telefone.


## <a name="required-permissions"></a>Permissões necessárias

Para ler e gravar dados de contato, aplicativos devem solicitar o `READ_CONTACTS` e `WRITE_CONTACTS` permissões, respectivamente.
Além disso, para ler e editar o perfil de usuário, os aplicativos devem exigir a `READ_PROFILE` e `WRITE_PROFILE` permissões.


## <a name="updating-profile-data"></a>Atualização de dados de perfil

Depois que essas permissões foram definidas, um aplicativo pode usar técnicas de Android normais para interagir com os dados do perfil do usuário. Por exemplo, para atualizar o nome para exibição do perfil, chame [ContentResolver.Update](https://developer.xamarin.com/api/member/Android.Content.ContentResolver.Update) com um `Uri` recuperados por meio de [ContactsContract.Profile.ContentRawContactsUri](https://developer.xamarin.com/api/property/Android.Provider.ContactsContract+Profile.ContentRawContactsUri/) propriedade, conforme mostrado abaixo:

```csharp
var values = new ContentValues ();
values.Put (ContactsContract.Contacts.InterfaceConsts.DisplayName, "John Doe");

// Update the user profile with the name "John Doe":
ContentResolver.Update (ContactsContract.Profile.ContentRawContactsUri, values, null, null);
```

## <a name="reading-profile-data"></a>Lendo dados de perfil

Emitir uma consulta para o [ContactsContact.Profile.ContentUri](https://developer.xamarin.com/api/property/Android.Provider.ContactsContract+Profile.ContentUri/) leituras de volta os dados do perfil. Por exemplo, o código a seguir lerá o nome para exibição do perfil do usuário:

```csharp
// Read the profile
var uri = ContactsContract.Profile.ContentUri;

// Setup the "projection" (column we want) for only the display name:
string[] projection = {
    ContactsContract.Contacts.InterfaceConsts.DisplayName };

// Use a CursorLoader to retrieve the data:
CursorLoader loader = new CursorLoader(this, uri, projection, null, null, null);
ICursor cursor = (ICursor)loader.LoadInBackground();
if (cursor != null)
{
    if (cursor.MoveToFirst ())
    {
        Console.WriteLine(cursor.GetString (cursor.GetColumnIndex (projection [0])));
    }
}
```

## <a name="navigating-to-the-user-profile"></a>Navegar para o perfil de usuário

Finalmente, para navegar para o perfil de usuário, crie uma tentativa com um `ActionView` ação e um `ContactsContract.Profile.ContentUri` , em seguida, passá-lo para o `StartActivity` método assim:

```csharp
var intent = new Intent (Intent.ActionView,
    ContactsContract.Profile.ContentUri);           
StartActivity (intent);
```

Ao executar o código acima, o perfil de usuário é exibido conforme ilustrado na captura de tela a seguir:

[![Captura de tela de perfil para exibir o perfil de usuário de John Doe](user-profile-images/01-profile-screen-sml.png)](user-profile-images/01-profile-screen.png#lightbox)

Trabalhar com o perfil de usuário é semelhante a interagir com outros dados no Android, e oferece um nível adicional de personalização do dispositivo.



## <a name="related-links"></a>Links relacionados

- [ContactsProviderDemo (exemplo)](https://developer.xamarin.com/samples/monodroid/ContactsProviderDemo/)
- [Introdução ao sabor de sorvete Sandwich](http://www.android.com/about/ice-cream-sandwich/)
- [Plataforma 4.0 Android](http://developer.android.com/sdk/android-4.0.html)
