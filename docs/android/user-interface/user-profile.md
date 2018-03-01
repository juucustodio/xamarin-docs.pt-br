---
title: "Perfil de usuário"
ms.topic: article
ms.prod: xamarin
ms.assetid: 1C58E12B-4634-4691-BF59-D5A3F6B0E6F7
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/21/2017
ms.openlocfilehash: 53ac30abea05095583fcac5ddc315f93ce7024f2
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="user-profile"></a>Perfil de usuário

Android tem suporte para enumerar os contatos com a `ContactsContract` provedor desde API nível 5. Por exemplo, a lista de contatos é tão simples quanto usar o `ContactContracts.Contacts` de classe, conforme mostrado no código a seguir:

```csharp
var uri = ContactsContract.Contacts.ContentUri;
           
string[] projection = {
    ContactsContract.Contacts.InterfaceConsts.Id,
    ContactsContract.Contacts.InterfaceConsts.DisplayName };
           
var cursor = ManagedQuery (uri, projection, null, null, null);
           
if (cursor.MoveToFirst ()) {
    do {
        Console.WriteLine ("Contact ID: {0}, Contact Name: {1}",
            cursor.GetString (cursor.GetColumnIndex (projection [0])),
            cursor.GetString (cursor.GetColumnIndex (projection [1])));
                   
    } while (cursor.MoveToNext());
}
```

Com 4 Android (API nível 14), um novo `ContactsContact.Profile` classe está disponível por meio do provedor de ContactsContract. O `ContactsContact.Profile` fornece acesso a um perfil pessoal para o proprietário de um dispositivo, o que inclui dados de contato, como do proprietário do dispositivo nome e número de telefone.

<a name="Required_Permissions" />

## <a name="required-permissions"></a>Permissões necessárias

Para ler e gravar dados de contato, aplicativos devem solicitar o `Read_Contacts` e `Write_Contacts` permissões, respectivamente. Além disso, para ler e editar o perfil de usuário, os aplicativos devem exigir a `Read_Profile` e `Write_Profile` permissões.

<a name="Updating_Profile_Data" />

## <a name="updating-profile-data"></a>Atualização de dados de perfil

Depois que essas permissões foram definidas, um aplicativo pode usar técnicas de Android normais para interagir com os dados do perfil do usuário. Por exemplo, para atualizar o nome para exibição do perfil devemos chamar `ContentResolver.Update` com um `Uri` recuperados por meio de `ContactsContract.Profile.ContentRawContactsUri` propriedade, conforme mostrado abaixo:

```csharp
var values = new ContentValues ();
          
values.Put (ContactsContract.Contacts.InterfaceConsts.DisplayName,
    "John Doe");
           
ContentResolver.Update (ContactsContract.Profile.ContentRawContactsUri,
    values, null, null);
```

<a name="Reading_Profile_Data" />

## <a name="reading-profile-data"></a>Lendo dados de perfil

Emitir uma consulta para o `ContactsContact.Profile.ContentUri` leituras de volta os dados do perfil. Por exemplo, o código a seguir lerá o nome para exibição do perfil do usuário:

```csharp
string[] projection = {
    ContactsContract.Contacts.InterfaceConsts.DisplayName };
           
var cursor = ManagedQuery (uri, projection, null, null, null);

if (cursor.MoveToFirst ()) {
    Console.WriteLine(
        cursor.GetString (cursor.GetColumnIndex (projection [0])));
}
```

<a name="Navigating_to_the_People_App" />

## <a name="navigating-to-the-people-app"></a>Navegar para o aplicativo de pessoas

Por fim, para navegar para o perfil de usuário no novo aplicativo de pessoas que acompanha o Android 4, basta criar uma tentativa com um `ActionView` ação e um `ContactsContract.Profile.ContentUri`e passá-lo para o `StartActivity` método assim:

```csharp
var intent = new Intent (Intent.ActionView,
    ContactsContract.Profile.ContentUri);           
StartActivity (intent);
```

Ao executar o código acima, o aplicativo de pessoas será carregado para o perfil de usuário, conforme mostrado na seguinte captura de tela:

[![Aplicativo de captura de tela de pessoas exibindo o perfil de usuário de John Doe](user-profile-images/15-people-app.png)](user-profile-images/15-people-app.png)

Trabalhar com o perfil do usuário agora é semelhante a interagir com outros dados no Android e oferece um nível adicional de personalização do dispositivo.



## <a name="related-links"></a>Links relacionados

- [ContactsProviderDemo (exemplo)](https://developer.xamarin.com/samples/monodroid/ContactsProviderDemo/)
- [Introdução ao sabor de sorvete Sandwich](http://www.android.com/about/ice-cream-sandwich/)
- [Plataforma 4.0 Android](http://developer.android.com/sdk/android-4.0.html)
