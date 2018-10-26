---
title: Perfil de usuário
ms.prod: xamarin
ms.assetid: 6BB01F75-5E98-49A1-BBA0-C2680905C59D
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/22/2018
ms.openlocfilehash: 2d9dc54801c4df084007a2903becf0c68bf1c6df
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50109959"
---
# <a name="user-profile"></a>Perfil de usuário

Tem suporte para o Android enumeração contatos com a [ContactsContract](https://developer.xamarin.com/api/type/Android.Provider.ContactsContract/) provedor desde a API de nível 5. Por exemplo, a listagem de contatos é tão simple quanto usar o [ContactContracts.Contacts](https://developer.xamarin.com/api/type/Android.Provider.ContactsContract+Contacts/) classe conforme mostrado no exemplo de código a seguir:

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

Começando com o Android 4 (API nível 14), o [ContactsContact.Profile](https://developer.xamarin.com/api/type/Android.Provider.ContactsContract+Profile/) classe está disponível por meio de `ContactsContract` provedor. O `ContactsContact.Profile` fornece acesso ao perfil pessoal para o proprietário de um dispositivo, que inclui dados de contato, como do proprietário do dispositivo nome e número de telefone.


## <a name="required-permissions"></a>Permissões necessárias

Para ler e gravar dados de contato, os aplicativos devem solicitar a `READ_CONTACTS` e `WRITE_CONTACTS` permissões, respectivamente.
Além disso, para ler e editar o perfil do usuário, aplicativos devem solicitar a `READ_PROFILE` e `WRITE_PROFILE` permissões.


## <a name="updating-profile-data"></a>Atualização de dados de perfil

Depois que essas permissões tiverem sido definidas, um aplicativo pode usar técnicas normais de Android para interagir com os dados do perfil do usuário. Por exemplo, para atualizar o nome de exibição do perfil, chame [ContentResolver.Update](https://developer.xamarin.com/api/member/Android.Content.ContentResolver.Update) com um `Uri` recuperados por meio de [ContactsContract.Profile.ContentRawContactsUri](https://developer.xamarin.com/api/property/Android.Provider.ContactsContract+Profile.ContentRawContactsUri/) propriedade, conforme mostrado abaixo:

```csharp
var values = new ContentValues ();
values.Put (ContactsContract.Contacts.InterfaceConsts.DisplayName, "John Doe");

// Update the user profile with the name "John Doe":
ContentResolver.Update (ContactsContract.Profile.ContentRawContactsUri, values, null, null);
```

## <a name="reading-profile-data"></a>Leitura de dados de perfil

Emitir uma consulta para o [ContactsContact.Profile.ContentUri](https://developer.xamarin.com/api/property/Android.Provider.ContactsContract+Profile.ContentUri/) leituras de volta os dados de perfil. Por exemplo, o código a seguir lerá o nome de exibição do perfil do usuário:

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

## <a name="navigating-to-the-user-profile"></a>Navegando até o perfil do usuário

Por fim, para navegar até o perfil do usuário, crie uma intenção com um `ActionView` ação e uma `ContactsContract.Profile.ContentUri` , em seguida, passá-lo para o `StartActivity` método como este:

```csharp
var intent = new Intent (Intent.ActionView,
    ContactsContract.Profile.ContentUri);           
StartActivity (intent);
```

Ao executar o código acima, o perfil do usuário é exibido, conforme ilustrado na captura de tela a seguir:

[![Captura de tela de perfil para exibir o perfil de usuário de John Doe](user-profile-images/01-profile-screen-sml.png)](user-profile-images/01-profile-screen.png#lightbox)

Trabalhar com o perfil do usuário é semelhante a interagir com outros dados no Android, e oferece um nível adicional de personalização do dispositivo.



## <a name="related-links"></a>Links relacionados

- [ContactsProviderDemo (amostra)](https://developer.xamarin.com/samples/monodroid/ContactsProviderDemo/)
- [Apresentando o Ice Cream Sandwich](http://www.android.com/about/ice-cream-sandwich/)
- [Plataforma 4.0 Android](http://developer.android.com/sdk/android-4.0.html)
