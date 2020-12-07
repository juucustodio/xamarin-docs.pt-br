---
title: 'Xamarin.Essentials: Contatos'
description: A classe Contacts no Xamarin.Essentials permite que um usuário escolha um contato e recupere informações sobre ele.
ms.assetid: 02280c42-720a-44c3-979e-4818a20c9821
author: jamesmontemagno
ms.author: jamont
ms.date: 09/22/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 19c9929706b7cf285b22562b094d2de2a25ff77d
ms.sourcegitcommit: 0a41c4aa6db72cd2d0cecbe0dc893024cecac71d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2020
ms.locfileid: "96749885"
---
# <a name="no-locxamarinessentials-contacts"></a>Xamarin.Essentials: Contatos

A classe **Contacts** permite que um usuário escolha um contato e recupere informações sobre ele.

![API de pré-lançamento](~/media/shared/preview.png)

## <a name="get-started"></a>Introdução

[!include[](~/essentials/includes/get-started.md)]

Para acessar a funcionalidade de **contatos** , a configuração específica de plataforma a seguir é necessária.

# <a name="android"></a>[Android](#tab/android)

A permissão `ReadContacts` é necessária e deve ser configurada no projeto do Android. Isso pode ser usado das seguintes maneiras:

Abra o arquivo **AssemblyInfo.cs** na pasta **Propriedades** e adicione:

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.ReadContacts)]
```

OU Atualize o Manifesto do Android:

Abra o arquivo **AndroidManifest.xml** na pasta **Propriedades** e adicione o seguinte no nó do **manifesto** .

```xml
<uses-permission android:name="android.permission.READ_CONTACTS" /> />
```

Ou clique com o botão direito do mouse no projeto do Android e abra as propriedades do projeto. Em **manifesto do Android** , localize as **permissões necessárias:** área e verifique essa permissão. Isso atualizará automaticamente o arquivo **AndroidManifest.xml**.

# <a name="ios"></a>[iOS](#tab/ios)

Em seu `Info.plist` adicione as seguintes chaves:

```xml
<key>NSContactsUsageDescription</key>
<string>This app needs access to contacts to pick a contact and get info.</string>
```

Certifique-se de atualizar o `<string>` na descrição específica para seu aplicativo, pois ele será mostrado aos usuários.

# <a name="uwp"></a>[UWP](#tab/uwp)

Em `Package.appxmanifest` em **recursos** , verifique se a `Contact` funcionalidade está marcada.

-----

## <a name="pick-a-contact"></a>Escolha um contato

Chamar `Contacts.PickContactAsync()` a caixa de diálogo de contato será exibido e permitirá que o usuário receba informações sobre o usuário.


```csharp
try
{
    var contact = await Contacts.PickContactAsync();

    if(contact == null)
        return;

    var id = contact.Id;
    var namePrefix = contact.NamePrefix;
    var givenName = contact.GivenName;
    var middleName = contact.MiddleName;
    var familyName = contact.FamilyName;
    var nameSuffix = contact.NameSuffix;
    var displayName = contact.DisplayName;
    var phones = contact.Phones; // List of phone numbers
    var emails = contact.Emails; // List of email addresses
}
catch (Exception ex)
{
    // Handle exception here.
}
```

## <a name="get-all-contacts"></a>Obter todos os contatos

```csharp
ObservableCollection<Contact> contactsCollect = new ObservableCollection<Contact>();

try
{
    // cancellationToken parameter is optional
    var cancellationToken = default(CancellationToken);
    var contacts = await Contacts.GetAllAsync(cancellationToken);

    if (contacts == null)
        return;

    foreach (var contact in contacts)
        contactsCollect.Add(contact);
}
catch (Exception ex)
{
    // Handle exception here.
}
```

## <a name="platform-differences"></a>Diferenças de plataforma

# <a name="android"></a>[Android](#tab/android)

- O `cancellationToken` parâmetro no `GetAllAsync` método é usado somente em UWP.

# <a name="ios"></a>[iOS](#tab/ios)

- O `cancellationToken` parâmetro no `GetAllAsync` método é usado somente em UWP.
- A plataforma iOS não oferece suporte à `DisplayName` Propriedade nativamente, portanto, o `DisplayName` valor é construído como "extiveship familyName".

# <a name="uwp"></a>[UWP](#tab/uwp)

Sem diferenças entre plataformas.

-----


## <a name="api"></a>API

- [Código-fonte de contatos](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/Contacts)
- [Documentação da API de contatos](xref:Xamarin.Essentials.Contacts)
