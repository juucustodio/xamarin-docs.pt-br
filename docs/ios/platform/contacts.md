---
title: Contatos e ContactsUI
description: "Este artigo aborda a trabalhar com os novos contatos e contatos da interface do usuário estruturas em um aplicativo xamarin. Essas estruturas substituem o catálogo de endereços e a interface de usuário do catálogo de endereço usado em versões anteriores do iOS."
ms.topic: article
ms.prod: xamarin
ms.assetid: 7b6fb66a-5e19-4a5a-9ed2-f6b02af099af
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/20/2017
ms.openlocfilehash: 996723db83a1f972cce26090d1253f97b6c818d3
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="contacts-and-contactsui"></a>Contatos e ContactsUI

_Este artigo aborda a trabalhar com os novos contatos e contatos da interface do usuário estruturas em um aplicativo xamarin. Essas estruturas substituem o catálogo de endereços e a interface de usuário do catálogo de endereço usado em versões anteriores do iOS._

Com a introdução do iOS 9, Apple lançou duas novas estruturas, `Contacts` e `ContactsUI`, que substitua o endereço de livro e estruturas do catálogo de endereço da interface do usuário usados pelo iOS 8 e versões anteriores.

As duas novas estruturas contenham a seguinte funcionalidade:

- [**Contatos** ](#contacts) -fornece acesso aos dados de lista de contatos do usuário.
    Como a maioria dos aplicativos exigem apenas acesso somente leitura, essa estrutura foi otimizada para acesso de thread safe, somente leitura.

- [**ContactsUI** ](#contactsui) -elementos de interface de usuário fornece xamarin para exibir, editar, selecione e criar contatos em dispositivos iOS.

[![](contacts-images/add01.png "Um exemplo de planilha de contato em um dispositivo iOS")](contacts-images/add01.png#lightbox)

> [!IMPORTANT]
> **Observação:** existente `AddressBook` e `AddressBookUI` estruturas uso pelo iOS 8 (e anterior) foram preteridos no iOS 9 e deve ser substituídos pelo novo `Contacts` e `ContactsUI` estruturas assim que possível para qualquer xamarin existente aplicativo. Novos aplicativos devem ser escritos em novas estruturas.




Nas seções a seguir, vamos dar uma olhada essas novas estruturas e como implementá-los em um aplicativo xamarin.

<a name="contacts" />

## <a name="the-contacts-framework"></a>A estrutura de contatos

A estrutura de contatos fornece xamarin acesso às informações de contato do usuário. Como a maioria dos aplicativos exigem apenas acesso somente leitura, essa estrutura foi otimizada para acesso de thread safe, somente leitura.

<a name="Contact_Objects" />

### <a name="contact-objects"></a>Objetos de contato

O `CNContact` classe fornece acesso de thread safe, somente leitura para propriedades de um contato, como nome, endereço ou números de telefone. `CNContact` funciona como um `NSDictionary` e contém várias coleções somente leitura das propriedades (como números de telefone ou endereços):

[![](contacts-images/contactobjects.png "Visão geral sobre o objeto de contato")](contacts-images/contactobjects.png#lightbox)

Para qualquer propriedade que pode ter vários valores (como números de endereço de email), eles serão representados como uma matriz de `NSLabeledValue` objetos. `NSLabeledValue` é uma tupla de thread seguro consiste em um conjunto de somente leitura dos rótulos e valores de onde o rótulo define o valor para o usuário (por exemplo email doméstica ou de trabalho). A estrutura de contatos fornece uma seleção de rótulos predefinidos (por meio de `CNLabelKey` e `CNLabelPhoneNumberKey` classes estáticas) que você pode usar em seu aplicativo, ou você tem a opção de definir rótulos personalizados para suas necessidades.

Para qualquer aplicativo xamarin que precisa para ajustar os valores de um contato existente (ou criar novos), use o `NSMutableContact` versão da classe e suas classes de sub (como `CNMutablePostalAddress`).

Por exemplo, o código a seguir irá criar um novo contato e adicioná-lo à coleção do usuário de contatos:

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

[![](contacts-images/add01.png "Um novo contato adicionado à coleção do usuário")](contacts-images/add01.png#lightbox)

### <a name="contact-formatting-and-localization"></a>Entre em contato com formatação e localização

A estrutura de contatos contém vários objetos e métodos que podem ajudá-lo a formatam e localizar conteúdo para exibição ao usuário. Por exemplo, o código a seguir corretamente formataria um nome de contatos e o endereço para exibição:

```csharp
Console.WriteLine(CNContactFormatter.GetStringFrom(contact, CNContactFormatterStyle.FullName));
Console.WriteLine(CNPostalAddressFormatter.GetStringFrom(workAddress, CNPostalAddressFormatterStyle.MailingAddress));
```

Para rótulos de propriedade que você deseja exibir na interface de usuário do seu aplicativo, a estrutura de contato tem métodos para localizar as cadeias de caracteres. Novamente, isso se baseia na localidade atual do dispositivo iOS, que o aplicativo está sendo executado. Por exemplo:

```csharp
// Localized properties
Console.WriteLine(CNContact.LocalizeProperty(CNContactOption.Nickname));
Console.WriteLine(CNLabeledValue.LocalizeLabel(CNLabelKey.Home));
```

### <a name="fetching-existing-contacts"></a>Buscar contatos existentes

Usando uma instância do `CNContactStore` classe, você pode buscar informações de contato do banco de dados de contatos do usuário. O `CNContactStore` contém todos os métodos necessários para buscar ou atualizar contatos e grupos do banco de dados. Como esses métodos são síncronos, é recomendável executá-los em um thread em segundo plano para impedir que a interface do usuário de bloqueio.

Usando os predicados (criado a partir de `CNContact` classe), você pode filtrar os resultados retornados ao buscar contatos do banco de dados. Para obter apenas os contatos que contêm a cadeia de caracteres `Appleseed`, use o seguinte código:

```csharp
// Create predicate to locate requested contact
var predicate = CNContact.GetPredicateForContacts("Appleseed");
```

> [!IMPORTANT]
> **Observação:** genérico e não há suporte para predicados compostos pela estrutura de contatos.

Por exemplo, para limitar a busca apenas o **GivenName** e **FamilyName** propriedades do contato, use o seguinte código:

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

Se esse código for executado depois de amostra que criamos no **contatos objeto** seção acima, será retornado o contato "John Appleseed" que acabamos de criar.

### <a name="contact-access-privacy"></a>Entre em contato com acesso de privacidade

Como os usuários finais podem conceder ou negar acesso a suas informações de contato em uma base por aplicativo, na primeira vez que você faz uma chamada para o `CNContactStore`, uma caixa de diálogo será exibida solicitando que eles para permitir o acesso para seu aplicativo.

A solicitação de permissão só aparecerá uma vez, na primeira vez em que o aplicativo é executada e subsequentes executa ou chamadas para o `CNContactStore` usará a permissão que o usuário selecionado no momento.

Você deve projetar seu aplicativo para que ele trata normalmente o usuário Negar acesso a seu banco de dados de contato.

#### <a name="fetching-partial-contacts"></a>Buscar contatos parciais

Um _contate parcial_ é um contato que apenas algumas das propriedades disponíveis foram buscadas de armazenamento de contato para. Se você tentar acessar uma propriedade que não foi obtida anteriormente, resultará em uma exceção.

Você pode facilmente verificar para ver se um determinado contato tem a propriedade desejada usando o `IsKeyAvailable` ou `AreKeysAvailable` métodos da `CNContact` instância. Por exemplo:

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
> **Observação:** o `GetUnifiedContact` e `GetUnifiedContacts` métodos do `CNContactStore` classe _somente_ retornar um contato parcial limitado para as propriedades solicitadas das chaves de busca fornecidas.

### <a name="unified-contacts"></a>Contatos unificados

Um usuário pode ter diferentes fontes de informações de contato para uma única pessoa em seu banco de dados de contato (como o iCloud, Facebook ou Google Mail). No iOS e OS X aplicativos, essas informações de contato serão automaticamente vinculadas e exibidas ao usuário como um único _Unificação entre em contato com_:

[![](contacts-images/unified01.png "Visão geral de contatos unificada")](contacts-images/unified01.png#lightbox)

Essa Unificação entre em contato com é uma exibição temporária em memória das informações de contato link terá seu próprio identificador exclusivo (que deve ser usado para buscar novamente o contato, se necessário). Por padrão, a estrutura de contatos retornará um contato unificado sempre que possível.

### <a name="creating-and-updating-contacts"></a>Criando e atualizando contatos

Conforme vimos no [objetos entre em contato com](#Contact_Objects) seção acima, você usa um `CNContactStore` e uma instância de um `CNMutableContact` para criar novos contatos, em seguida, são gravados para o usuário entre em contato com banco de dados usando um `CNSaveRequest`:

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

Um `CNSaveRequest` também pode ser usado para armazenar em cache várias alterações de contato e de grupo em uma única operação e lote essas modificações para o `CNContactStore`.

Para atualizar um contato não mutável obtido de uma operação de busca, você deve primeiro solicitar uma cópia mutável que, em seguida, modificar e salvar novamente para o repositório de contato. Por exemplo:

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

### <a name="contact-change-notifications"></a>Notificações de alteração de contato

Sempre que um contato é alterado, o repositório de contato lança um `CNContactStoreDidChangeNotification` no Centro de notificação padrão. Se você tiver armazenado em cache ou está exibindo os contatos, você precisará atualizar esses objetos do repositório de contato (`CNContactStore`).

### <a name="containers-and-groups"></a>Contêineres e grupos

Contatos de um usuário podem existir ou localmente no dispositivo do usuário ou como contatos sincronizados para o dispositivo de uma ou mais contas de servidor (como o Facebook ou Google). Cada pool de contatos tem seu próprio _contêiner_ e um contato específico só pode existir em um contêiner.

[![](contacts-images/containers01.png "Visão geral dos contêineres e grupos")](contacts-images/containers01.png#lightbox)

Permitir que alguns contêineres para os contatos sejam organizados em uma ou mais _grupos_ ou _subgrupos_. Esse comportamento é dependente de repositório de backup para um contêiner específico. Por exemplo, iCloud tem apenas um contêiner, mas pode ter muitos grupos (mas sem subgrupos). Microsoft Exchange por outro lado, não dá suporte a grupos, mas pode ter múltiplos contêineres (uma para cada pasta do Exchange).

[![](contacts-images/containers02.png "Sobreponha contêineres e grupos")](contacts-images/containers02.png#lightbox)

<a name="contactsui" />

## <a name="the-contactsui-framework"></a>A estrutura ContactsUI

Em situações em que seu aplicativo não precisa para apresentar uma interface de usuário personalizada, você pode usar a estrutura de ContactsUI para apresentar os elementos de interface do usuário para exibir, editar, selecione e criar contatos em seu aplicativo xamarin.

Usando controles internos da Apple não só reduz a quantidade de código que você precisa criar para oferecer suporte a contatos em seu aplicativo xamarin, mas é apresentar uma interface consistente aos usuários do aplicativo.

### <a name="the-contact-picker-view-controller"></a>O controlador de exibição de seletor de contato

O controlador de exibição de seletor de contato (`CNContactPickerViewController`) gerencia o modo de exibição de seletor de contato padrão que permite que o usuário selecione um contato ou uma propriedade de contato do banco de dados de contato do usuário. O usuário pode selecionar um ou mais contatos (com base no seu uso) e o controlador de exibição de seletor de entrar em contato com não solicitará permissão antes de exibir o seletor.

Antes de chamar o `CNContactPickerViewController` classe, você define as propriedades que o usuário pode selecionar e definir predicados para controlar a exibição e a seleção de propriedades de contato.

Usar uma instância da classe que herda de `CNContactPickerDelegate` para responder à interação do usuário com o seletor. Por exemplo:

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

Para permitir que o usuário selecione um endereço de email dos contatos em seu banco de dados, você pode usar o código a seguir:

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

### <a name="the-contact-view-controller"></a>O controlador de exibição do contato

O controlador de exibição do contato (`CNContactViewController`) classe fornece um controlador para apresentar uma exibição padrão de contato para o usuário final. O modo de exibição do contato pode exibir novos contatos de novo, desconhecido ou existente e o tipo deve ser especificado para o modo de exibição é exibido ao chamar o construtor estático correto (`FromNewContact`, `FromUnknownContact`, `FromContact`). Por exemplo:

```csharp
// Create a new contact view
var view = CNContactViewController.FromContact(contact);

// Display the view
PresentViewController(view, true, null);
```

## <a name="summary"></a>Resumo

Este artigo obteve uma visão detalhada de como trabalhar com as estruturas de contato e entre em contato com interface do usuário em um aplicativo xamarin. Primeiro, ele abordados os diferentes tipos de objetos que fornece a estrutura de contato e como usá-los para criar ou acessar contatos existentes. Ele também examinar a estrutura de interface do usuário entre em contato com para selecionar contatos existentes e exibir informações de contato.


## <a name="related-links"></a>Links relacionados

- [Exemplo de QuickContacts](https://developer.xamarin.com/samples/monotouch/ios9/QuickContacts/)
- [O que há de novo no iOS 9](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html#//apple_ref/doc/uid/TP40016198-DontLinkElementID_14))
- [Referência de estrutura de contatos](https://developer.apple.com/library/prerelease/ios/documentation/Contacts/Reference/Contacts_Framework/index.html#//apple_ref/doc/uid/TP40015328)
- [Referência ContactsUI Framework](https://developer.apple.com/library/prerelease/ios/documentation/ContactsUI/Reference/ContactsUI_Framework/index.html#//apple_ref/doc/uid/TP40016207)
