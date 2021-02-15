---
title: Perfil do Usuário
ms.prod: xamarin
ms.assetid: 6BB01F75-5E98-49A1-BBA0-C2680905C59D
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/22/2018
ms.openlocfilehash: 1b7202a199315bf0be1664bf34725c26d12a66c8
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91458064"
---
# <a name="user-profile"></a>Perfil do Usuário

O Android tem suporte para enumerar contatos com o provedor [ContactsContract](xref:Android.Provider.ContactsContract) desde o nível de API 5. Por exemplo, listar contatos é tão simples quanto usar a classe [ContactContracts. Contacts](xref:Android.Provider.ContactsContract.Contacts) , conforme mostrado no exemplo de código a seguir:

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

A partir do Android 4 (API nível 14), a classe [ContactsContact. Profile](xref:Android.Provider.ContactsContract.Profile) está disponível por meio do `ContactsContract` provedor. O `ContactsContact.Profile` fornece acesso ao perfil pessoal para o proprietário de um dispositivo, que inclui dados de contato, como o nome do proprietário do dispositivo e o número de telefone.

## <a name="required-permissions"></a>Permissões necessárias

Para ler e gravar dados de contato, os aplicativos devem solicitar as `READ_CONTACTS` `WRITE_CONTACTS` permissões e, respectivamente.
Além disso, para ler e editar o perfil do usuário, os aplicativos devem solicitar as `READ_PROFILE` `WRITE_PROFILE` permissões e.

## <a name="updating-profile-data"></a>Atualizando dados de perfil

Depois que essas permissões tiverem sido definidas, um aplicativo poderá usar as técnicas normais do Android para interagir com os dados do perfil do usuário. Por exemplo, para atualizar o nome de exibição do perfil, chame [ContentResolver retornem. Update](xref:Android.Content.ContentResolver.Update*) com um `Uri` recuperado por meio da propriedade [ContactsContract. Profile. ContentRawContactsUri](xref:Android.Provider.ContactsContract.Profile.ContentRawContactsUri) , conforme mostrado abaixo:

```csharp
var values = new ContentValues ();
values.Put (ContactsContract.Contacts.InterfaceConsts.DisplayName, "John Doe");

// Update the user profile with the name "John Doe":
ContentResolver.Update (ContactsContract.Profile.ContentRawContactsUri, values, null, null);
```

## <a name="reading-profile-data"></a>Lendo dados de perfil

Emitir uma consulta para o [ContactsContact. Profile. ContentUri](xref:Android.Provider.ContactsContract.Profile.ContentUri) lê os dados do perfil. Por exemplo, o código a seguir lerá o nome de exibição do perfil do usuário:

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

Por fim, para navegar até o perfil do usuário, crie uma intenção com uma `ActionView` ação e `ContactsContract.Profile.ContentUri` , em seguida, passe-a para o `StartActivity` método como este:

```csharp
var intent = new Intent (Intent.ActionView,
    ContactsContract.Profile.ContentUri);
StartActivity (intent);
```

Ao executar o código acima, o perfil do usuário é exibido conforme ilustrado na captura de tela a seguir:

[![Captura de tela do perfil exibindo o perfil de usuário John Doe](user-profile-images/01-profile-screen-sml.png)](user-profile-images/01-profile-screen.png#lightbox)

Trabalhar com o perfil do usuário é semelhante a interagir com outros dados no Android e oferece um nível adicional de personalização de dispositivos.

## <a name="related-links"></a>Links Relacionados

- [ContactsProviderDemo (exemplo)](/samples/xamarin/monodroid-samples/contactsproviderdemo)