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
ms.openlocfilehash: bd239a8dcf192c0bdbc6265769208f4fc989bbbe
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91434484"
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

## <a name="picking-a-contact"></a>Escolhendo um contato

Chamar `Contacts.PickContactAsync()` a caixa de diálogo de contato será exibido e permitirá que o usuário receba informações sobre o usuário.


```csharp
try
{
    var contact = await Contacts.PickContactAsync();

    if(contact == null)
        return;

    var name = contact.Name;
    var contactType = contact.ContactType; // Unknown, Personal, Work
    var numbers = contact.Numbers; // List of phone numbers
    var emails = contact.Emails; // List of email addresses 
    
}
catch (Exception ex)
{
    // Handle exception here.
}
```


## <a name="api"></a>API

- [Código-fonte de contatos](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/Contacts)
- [Documentação da API de contatos](xref:Xamarin.Essentials.Contacts)
