---
title: Contatos e ContactsUI no Xamarin. iOS
description: Este artigo aborda como trabalhar com as novas estruturas de interface do usuário contatos e contatos em um aplicativo Xamarin. iOS. Essas estruturas substituem o catálogo de endereços existente e a interface do usuário do catálogo de endereços usada em versões anteriores do iOS.
ms.prod: xamarin
ms.assetid: 7b6fb66a-5e19-4a5a-9ed2-f6b02af099af
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/20/2017
ms.openlocfilehash: 9a5ca5988bc5bf43af4837aa689c89a016829282
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86939978"
---
# <a name="contacts-and-contactsui-in-xamarinios"></a>Contatos e ContactsUI no Xamarin. iOS

_Este artigo aborda como trabalhar com as novas estruturas de interface do usuário contatos e contatos em um aplicativo Xamarin. iOS. Essas estruturas substituem o catálogo de endereços existente e a interface do usuário do catálogo de endereços usada em versões anteriores do iOS._

Com a introdução do iOS 9, a Apple lançou duas novas estruturas `Contacts` e `ContactsUI` , que substituem o catálogo de endereços existente e as estruturas de interface do usuário do catálogo de endereços usadas pelo Ios 8 e versões anteriores.

As duas novas estruturas contêm a seguinte funcionalidade:

- [**Contatos**](#contacts) – fornece acesso aos dados da lista de contatos do usuário.
  Como a maioria dos aplicativos só requer acesso somente leitura, essa estrutura foi otimizada para acesso somente leitura de thread seguro.

- [**ContactsUI**](#contactsui) -fornece elementos da interface do usuário do Xamarin. Ios para exibir, editar, selecionar e criar contatos em dispositivos IOS.

[![Um exemplo de folha de contato em um dispositivo iOS](contacts-images/add01.png)](contacts-images/add01.png#lightbox)

> [!IMPORTANT]
> As `AddressBook` estruturas existentes e `AddressBookUI` usadas pelo Ios 8 (e anteriores) foram preteridas no Ios 9 e devem ser substituídas por novas `Contacts` e `ContactsUI` estruturas assim que possível para qualquer aplicativo Xamarin. Ios existente. Novos aplicativos devem ser escritos em relação às novas estruturas.

Nas seções a seguir, vamos dar uma olhada nessas novas estruturas e em como implementá-las em um aplicativo Xamarin. iOS.

<a name="contacts"></a>

## <a name="the-contacts-framework"></a>A estrutura de contatos

A estrutura de contatos fornece acesso ao Xamarin. iOS às informações de contato do usuário. Como a maioria dos aplicativos só requer acesso somente leitura, essa estrutura foi otimizada para acesso somente leitura de thread seguro.

<a name="Contact_Objects"></a>

### <a name="contact-objects"></a>Objetos de contato

A `CNContact` classe fornece acesso de thread seguro e somente leitura às propriedades de um contato, como nome, endereço ou números de telefone. `CNContact`funções como um `NSDictionary` e contêm várias coleções de propriedades somente leitura (como endereços ou números de telefone):

[![Visão geral do objeto de contato](contacts-images/contactobjects.png)](contacts-images/contactobjects.png#lightbox)

Para qualquer propriedade que possa ter vários valores (como endereço de email ou números de telefone), elas serão representadas como uma matriz de `NSLabeledValue` objetos. `NSLabeledValue`é uma tupla thread-safe que consiste em um conjunto somente leitura de rótulos e valores em que o rótulo define o valor para o usuário (por exemplo, email doméstico ou de trabalho). A estrutura de contatos fornece uma seleção de rótulos predefinidos (por meio das `CNLabelKey` `CNLabelPhoneNumberKey` classes e estáticas) que você pode usar em seu aplicativo ou tem a opção de definir rótulos personalizados para suas necessidades.

Para qualquer aplicativo Xamarin. iOS que precise ajustar os valores de um contato existente (ou criar novos), use a `NSMutableContact` versão da classe e suas subclasses (como `CNMutablePostalAddress` ).

Por exemplo, o código a seguir criará um novo contato e o adicionará à coleção de contatos do usuário:

```csharp
// Create a new Mutable Contact (read/write)
var contact = new CNMutableContact();

// Set standard properties
contact.GivenName = "John";
contact.FamilyName = "Appleseed";

// Add email addresses
var homeEmail = new CNLabeledValue<NSString>(CNLabelKey.Home, new NSString("john.appleseed@mac.com"));
var workEmail = new CNLabeledValue<NSString>(CNLabelKey.Work, new NSString("john.appleseed@apple.com"));
contact.EmailAddresses = new CNLabeledValue<NSString>[] { homeEmail, workEmail };

// Add phone numbers
var cellPhone = new CNLabeledValue<CNPhoneNumber>(CNLabelPhoneNumberKey.iPhone, new CNPhoneNumber("713-555-1212"));
var workPhone = new CNLabeledValue<CNPhoneNumber>("Work", new CNPhoneNumber("408-555-1212"));
contact.PhoneNumbers = new CNLabeledValue<CNPhoneNumber>[] { cellPhone, workPhone };

// Add work address
var workAddress = new CNMutablePostalAddress()
{
    Street = "1 Infinite Loop",
    City = "Cupertino",
    State = "CA",
    PostalCode = "95014"
};
contact.PostalAddresses = new CNLabeledValue<CNPostalAddress>[] { new CNLabeledValue<CNPostalAddress>(CNLabelKey.Work, workAddress) };

// Add birthday
var birthday = new NSDateComponents()
{
    Day = 1,
    Month = 4,
    Year = 1984
};
contact.Birthday = birthday;

// Save new contact
var store = new CNContactStore();
var saveRequest = new CNSaveRequest();
saveRequest.AddContact(contact, store.DefaultContainerIdentifier);

// Attempt to save changes
NSError error;
if (store.ExecuteSaveRequest(saveRequest, out error))
{
    Console.WriteLine("New contact saved");
}
else
{
    Console.WriteLine("Save error: {0}", error);
}
```

Se esse código for executado em um dispositivo iOS 9, um novo contato será adicionado à coleção do usuário. Por exemplo:

[![](contacts-images/add01.png "A new contact added to the user's collection")](contacts-images/add01.png#lightbox)

### <a name="contact-formatting-and-localization"></a>Formatação e localização de contatos

A estrutura de contatos contém vários objetos e métodos que podem ajudá-lo a formatar e localizar conteúdo para exibição para o usuário. Por exemplo, o código a seguir formataria corretamente um nome de contatos e o endereço de correspondência para exibição:

```csharp
Console.WriteLine(CNContactFormatter.GetStringFrom(contact, CNContactFormatterStyle.FullName));
Console.WriteLine(CNPostalAddressFormatter.GetStringFrom(workAddress, CNPostalAddressFormatterStyle.MailingAddress));
```

Para rótulos de propriedade que serão exibidos na interface do usuário do seu aplicativo, a estrutura de contato também tem métodos para localizar essas cadeias de caracteres. Novamente, isso se baseia na localidade atual do dispositivo iOS em que o aplicativo está sendo executado. Por exemplo:

```csharp
// Localized properties
Console.WriteLine(CNContact.LocalizeProperty(CNContactOptions.Nickname));
Console.WriteLine(CNLabeledValue<NSString>.LocalizeLabel(CNLabelKey.Home));
```

### <a name="fetching-existing-contacts"></a>Buscando contatos existentes

Usando uma instância da `CNContactStore` classe, você pode buscar informações de contato do banco de dados de contatos do usuário. O `CNContactStore` contém todos os métodos necessários para buscar ou atualizar contatos e grupos do banco de dados. Como esses métodos são síncronos, é recomendável que você os execute em um thread em segundo plano para impedir o bloqueio da interface do usuário.

Usando predicados (criados a partir da `CNContact` classe), você pode filtrar os resultados retornados ao buscar contatos do banco de dados. Para buscar somente contatos que contenham a cadeia de caracteres `Appleseed` , use o seguinte código:

```csharp
// Create predicate to locate requested contact
var predicate = CNContact.GetPredicateForContacts("Appleseed");
```

> [!IMPORTANT]
> Os predicados genérico e composto não são suportados pela estrutura de contatos.

Por exemplo, para limitar a busca apenas às propriedades de **determinadoname** e **familyName** do contato, use o seguinte código:

```csharp
// Define fields to be searched
var fetchKeys = new NSString[] {CNContactKey.GivenName, CNContactKey.FamilyName};
```

Por fim, para pesquisar o banco de dados e retornar os resultados, use o seguinte código:

```csharp
// Grab matching contacts
var store = new CNContactStore();
NSError error;
var contacts = store.GetUnifiedContacts(predicate, fetchKeys, out error);
```

Se esse código foi executado após o exemplo que criamos na seção de **objeto Contacts** acima, ele retornaria o contato "John Appleseed" que acabamos de criar.

### <a name="contact-access-privacy"></a>Contatar privacidade do Access

Como os usuários finais podem conceder ou negar acesso às suas informações de contato por aplicativo, na primeira vez que você fizer uma chamada para o `CNContactStore` , será apresentada uma caixa de diálogo solicitando que ele permita o acesso ao seu aplicativo.

A solicitação de permissão será apresentada apenas uma vez, na primeira vez em que o aplicativo for executado e as próximas execuções ou chamadas para o `CNContactStore` usarão a permissão que o usuário selecionou naquele momento.

Você deve projetar seu aplicativo para que ele manipule normalmente o usuário que nega o acesso ao banco de dados de contato.

#### <a name="fetching-partial-contacts"></a>Buscando contatos parciais

Um _contato parcial_ é um contato que apenas algumas das propriedades disponíveis foram buscadas na loja de contatos para o. Se você tentar acessar uma propriedade que não foi previamente buscada, ela resultará em uma exceção.

Você pode verificar facilmente se um determinado contato tem a propriedade desejada usando os `IsKeyAvailable` `AreKeysAvailable` métodos ou da `CNContact` instância. Por exemplo:

```csharp
// Does the contact contain the requested key?
if (!contact.IsKeyAvailable(CNContactOption.PostalAddresses)) {
    // No, re-request to pull required info
    var fetchKeys = new NSString[] {CNContactKey.GivenName, CNContactKey.FamilyName, CNContactKey.PostalAddresses};
    var store = new CNContactStore();
    NSError error;
    contact = store.GetUnifiedContact(contact.Identifier, fetchKeys, out error);
}
```

> [!IMPORTANT]
> Os `GetUnifiedContact` `GetUnifiedContacts` métodos e da `CNContactStore` classe retornam _apenas_ um contato parcial limitado às propriedades solicitadas das chaves de busca fornecidas.

### <a name="unified-contacts"></a>Contatos unificados

Um usuário pode ter fontes diferentes de informações de contato para uma única pessoa em seu banco de dados de contatos (como iCloud, Facebook ou Google mail). Nos aplicativos iOS e OS X, essas informações de contato serão automaticamente vinculadas e exibidas ao usuário como um único _contato unificado_:

[![Visão geral dos contatos unificados](contacts-images/unified01.png)](contacts-images/unified01.png#lightbox)

Este contato unificado é uma exibição temporária, na memória, das informações de contato do link que receberão seu próprio identificador exclusivo (que deve ser usado para buscar novamente o contato, se necessário). Por padrão, a estrutura de contatos retornará um contato unificado sempre que possível.

### <a name="creating-and-updating-contacts"></a>Criando e atualizando contatos

Como vimos na seção de [objetos de contato](#Contact_Objects) acima, você usa um `CNContactStore` e uma instância de a `CNMutableContact` para criar novos contatos que, em seguida, são gravados no banco de dados de contatos do usuário usando um `CNSaveRequest` :

```csharp
// Create a new Mutable Contact (read/write)
var contact = new CNMutableContact();

// Set standard properties
contact.GivenName = "John";
contact.FamilyName = "Appleseed";

// Save new contact
var store = new CNContactStore();
var saveRequest = new CNSaveRequest();
saveRequest.AddContact(contact, store.DefaultContainerIdentifier);

NSError error;
if (store.ExecuteSaveRequest(saveRequest, out error)) {
    Console.WriteLine("New contact saved");
} else {
    Console.WriteLine("Save error: {0}", error);
}
```

Um `CNSaveRequest` também pode ser usado para armazenar em cache várias alterações de grupo e de contato em uma única operação e em lote essas modificações no `CNContactStore` .

Para atualizar um contato não mutável obtido de uma operação de busca, você deve primeiro solicitar uma cópia mutável que você modifique e salve novamente na loja de contatos. Por exemplo:

```csharp
// Get mutable copy of contact
var mutable = contact.MutableCopy() as CNMutableContact;
var newEmail = new CNLabeledValue<NSString>(CNLabelKey.Home, new NSString("john.appleseed@xamarin.com"));

// Append new email
var emails = new NSObject[mutable.EmailAddresses.Length+1];
mutable.EmailAddresses.CopyTo(emails,0);
emails[mutable.EmailAddresses.Length+1] = newEmail;
mutable.EmailAddresses = emails;

// Update contact
var store = new CNContactStore();
var saveRequest = new CNSaveRequest();
saveRequest.UpdateContact(mutable);

NSError error;
if (store.ExecuteSaveRequest(saveRequest, out error)) {
    Console.WriteLine("Contact updated.");
} else {
    Console.WriteLine("Update error: {0}", error);
}
```

### <a name="contact-change-notifications"></a>Contatar notificações de alteração

Sempre que um contato é modificado, a loja de contatos posta um `CNContactStoreDidChangeNotification` no centro de notificações padrão. Se você tiver armazenado em cache ou estiver exibindo os contatos no momento, precisará atualizar esses objetos da loja de contatos ( `CNContactStore` ).

### <a name="containers-and-groups"></a>Contêineres e grupos

Os contatos de um usuário podem existir localmente no dispositivo do usuário ou como contatos sincronizados com o dispositivo de uma ou mais contas de servidor (como Facebook ou Google). Cada pool de contatos tem seu próprio _contêiner_ e um determinado contato só pode existir em um contêiner.

[![Visão geral de contêineres e grupos](contacts-images/containers01.png)](contacts-images/containers01.png#lightbox)

Alguns contêineres permitem que os contatos sejam organizados em um ou mais _grupos_ ou _subgrupos_. Esse comportamento é dependente do armazenamento de backup para um determinado contêiner. Por exemplo, o iCloud tem apenas um contêiner, mas pode ter muitos grupos (mas não subgrupos). O Microsoft Exchange, por outro lado, não oferece suporte a grupos, mas pode ter vários contêineres (um para cada pasta do Exchange).

[![Sobreposição em contêineres e grupos](contacts-images/containers02.png)](contacts-images/containers02.png#lightbox)

<a name="contactsui"></a>

## <a name="the-contactsui-framework"></a>A estrutura ContactsUI

Para situações em que seu aplicativo não precisa apresentar uma interface do usuário personalizada, você pode usar a estrutura ContactsUI para apresentar elementos da interface do usuário para exibir, editar, selecionar e criar contatos em seu aplicativo Xamarin. iOS.

Usando os controles internos da Apple, você não só reduz a quantidade de código que precisa criar para dar suporte a contatos em seu aplicativo Xamarin. iOS, mas apresenta uma interface consistente para os usuários do aplicativo.

### <a name="the-contact-picker-view-controller"></a>O controlador de exibição do seletor de contatos

O controlador de exibição do seletor de contatos ( `CNContactPickerViewController` ) gerencia a exibição padrão do seletor de contatos que permite ao usuário selecionar um contato ou uma propriedade de contato do banco de dados de contatos do usuário. O usuário pode selecionar um ou mais contatos (com base em seu uso) e o controlador de exibição do seletor de contatos não solicita permissão antes de exibir o seletor.

Antes de chamar a `CNContactPickerViewController` classe, você define quais propriedades o usuário pode selecionar e definir predicados para controlar a exibição e a seleção de propriedades de contato.

Use uma instância da classe que herda de `CNContactPickerDelegate` para responder à interação do usuário com o seletor. Por exemplo:

```csharp
using System;
using System.Linq;
using UIKit;
using Foundation;
using Contacts;
using ContactsUI;

namespace iOS9Contacts
{
    public class ContactPickerDelegate: CNContactPickerDelegate
    {
        #region Constructors
        public ContactPickerDelegate ()
        {
        }

        public ContactPickerDelegate (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ContactPickerDidCancel (CNContactPickerViewController picker)
        {
            Console.WriteLine ("User canceled picker");

        }

        public override void DidSelectContact (CNContactPickerViewController picker, CNContact contact)
        {
            Console.WriteLine ("Selected: {0}", contact);
        }

        public override void DidSelectContactProperty (CNContactPickerViewController picker, CNContactProperty contactProperty)
        {
            Console.WriteLine ("Selected Property: {0}", contactProperty);
        }
        #endregion
    }
}
```

Para permitir que o usuário selecione um endereço de email dos contatos em seu banco de dados, você pode usar o seguinte código:

```csharp
// Create a new picker
var picker = new CNContactPickerViewController();

// Select property to pick
picker.DisplayedPropertyKeys = new NSString[] {CNContactKey.EmailAddresses};
picker.PredicateForEnablingContact = NSPredicate.FromFormat("emailAddresses.@count > 0");
picker.PredicateForSelectionOfContact = NSPredicate.FromFormat("emailAddresses.@count == 1");

// Respond to selection
picker.Delegate = new ContactPickerDelegate();

// Display picker
PresentViewController(picker,true,null);
```

### <a name="the-contact-view-controller"></a>O controlador de exibição de contato

A classe do controlador de exibição de contato ( `CNContactViewController` ) fornece um controlador para apresentar uma exibição de contato padrão para o usuário final. O modo de exibição de contato pode exibir novos contatos novos, desconhecidos ou existentes e o tipo deve ser especificado antes que a exibição seja exibida chamando o construtor estático correto ( `FromNewContact` , `FromUnknownContact` , `FromContact` ). Por exemplo:

```csharp
// Create a new contact view
var view = CNContactViewController.FromContact(contact);

// Display the view
PresentViewController(view, true, null);
```

## <a name="summary"></a>Resumo

Este artigo deu uma visão detalhada de como trabalhar com as estruturas de interface do usuário de contato e contato em um aplicativo Xamarin. iOS. Primeiro, ele abordou os diferentes tipos de objetos que a estrutura de contato fornece e como usá-los para criar novos ou acessar contatos existentes. Ele também examinou a estrutura da interface do usuário do contato para selecionar os contatos existentes e exibir as informações de contato.

## <a name="related-links"></a>Links Relacionados

- [Exemplo de contatos](https://docs.microsoft.com/samples/xamarin/ios-samples/contacts/)
- [O que há de novo no iOS 9](https://developer.apple.com/library/content/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Referência da estrutura de contatos](https://developer.apple.com/documentation/contacts?language=objc)
- [Referência da estrutura do ContactsUI](https://developer.apple.com/documentation/contactsui?language=objc)
