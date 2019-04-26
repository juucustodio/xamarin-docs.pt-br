---
title: Contatos e ContactsUI no xamarin. IOS
description: Este artigo aborda como trabalhar com os novos contatos e contatos da interface do usuário estruturas em um aplicativo xamarin. IOS. Essas estruturas substituem o catálogo de endereços e a interface de usuário do catálogo de endereço usado em versões anteriores do iOS.
ms.prod: xamarin
ms.assetid: 7b6fb66a-5e19-4a5a-9ed2-f6b02af099af
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: e3f1533605d08df58d8d257714dd8135690c0e5d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61388877"
---
# <a name="contacts-and-contactsui-in-xamarinios"></a>Contatos e ContactsUI no xamarin. IOS

_Este artigo aborda como trabalhar com os novos contatos e contatos da interface do usuário estruturas em um aplicativo xamarin. IOS. Essas estruturas substituem o catálogo de endereços e a interface de usuário do catálogo de endereço usado em versões anteriores do iOS._

Com a introdução do iOS 9, a Apple lançou duas novas estruturas `Contacts` e `ContactsUI`, que substituir o existente catálogo de endereços e estruturas do catálogo de endereço da interface do usuário usados pelo iOS 8 e versões anteriores.

Duas novas estruturas contêm a seguinte funcionalidade:

- [**Contatos** ](#contacts) -fornece acesso aos dados de lista de contatos do usuário.
    Como a maioria dos aplicativos exigem apenas o acesso somente leitura, essa estrutura foi otimizada para acesso de thread-safe, somente leitura.

- [**ContactsUI** ](#contactsui) -elementos fornece interface do usuário de xamarin. IOS para exibir, editar, selecione e crie contatos em dispositivos iOS.

[![](contacts-images/add01.png "Um exemplo de planilha de contato em um dispositivo iOS")](contacts-images/add01.png#lightbox)

> [!IMPORTANT]
> Existente `AddressBook` e `AddressBookUI` estruturas uso pelo iOS 8 (e anterior) foram preteridos no iOS 9 e deve ser substituídos pelo novo `Contacts` e `ContactsUI` estruturas assim que possível para qualquer aplicativo existente do xamarin. IOS. Novos aplicativos devem ser escritos em relação a novas estruturas.




Nas seções a seguir, vamos dar uma olhada em dessas novas estruturas e como implementá-los em um aplicativo xamarin. IOS.

<a name="contacts" />

## <a name="the-contacts-framework"></a>A estrutura de contatos

A estrutura de contatos fornece acesso de xamarin. IOS para informações de contato do usuário. Como a maioria dos aplicativos exigem apenas o acesso somente leitura, essa estrutura foi otimizada para acesso de thread-safe, somente leitura.

<a name="Contact_Objects" />

### <a name="contact-objects"></a>Objetos de contato

O `CNContact` classe fornece acesso de thread-safe, somente leitura para as propriedades de um contato, como nome, endereço ou números de telefone. `CNContact` funciona como um `NSDictionary` e contém várias coleções somente leitura das propriedades (como endereços ou números de telefone):

[![](contacts-images/contactobjects.png "Entre em contato com a visão geral do objeto")](contacts-images/contactobjects.png#lightbox)

Para qualquer propriedade que pode ter vários valores (como números de telefone ou endereço de email), ele será representado como uma matriz de `NSLabeledValue` objetos. `NSLabeledValue` é uma tupla de thread-safe consiste em um conjunto de somente leitura de rótulos e valores em que o rótulo define o valor para o usuário (por exemplo doméstica ou trabalho de email). A estrutura de contatos fornece uma seleção de rótulos predefinidos (por meio de `CNLabelKey` e `CNLabelPhoneNumberKey` classes estáticas) que você pode usar em seu aplicativo ou você tem a opção de definição de rótulos personalizados para suas necessidades.

Para qualquer aplicativo xamarin. IOS que precisam ajustar os valores de um contato existente (ou criar novos), use o `NSMutableContact` versão da classe e suas classes de sub (como `CNMutablePostalAddress`).

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

Se esse código é executado em um dispositivo iOS 9, um novo contato será adicionado à coleção do usuário. Por exemplo:

[![](contacts-images/add01.png "Um novo contato adicionado à coleção do usuário")](contacts-images/add01.png#lightbox)

### <a name="contact-formatting-and-localization"></a>Formatação de contato e localização

O framework de contatos contém vários objetos e métodos que podem ajudá-lo a formatam e localizar o conteúdo para exibição ao usuário. Por exemplo, o código a seguir seria formate corretamente um nome de contatos e o endereço para exibição:

```csharp
Console.WriteLine(CNContactFormatter.GetStringFrom(contact, CNContactFormatterStyle.FullName));
Console.WriteLine(CNPostalAddressFormatter.GetStringFrom(workAddress, CNPostalAddressFormatterStyle.MailingAddress));
```

Para rótulos de propriedade que você deseja exibir na interface de usuário do seu aplicativo, a estrutura de contato tem métodos para localizar essas cadeias de caracteres. Novamente, isso se baseia na localidade atual do dispositivo iOS, que o aplicativo está sendo executado. Por exemplo:

```csharp
// Localized properties
Console.WriteLine(CNContact.LocalizeProperty(CNContactOptions.Nickname));
Console.WriteLine(CNLabeledValue<NSString>.LocalizeLabel(CNLabelKey.Home));
```

### <a name="fetching-existing-contacts"></a>Buscando contatos existentes

Usando uma instância da `CNContactStore` classe, você pode buscar informações de contato do banco de dados de contatos do usuário. O `CNContactStore` contém todos os métodos necessários para buscar ou atualizar contatos e grupos do banco de dados. Como esses métodos são síncronos, é recomendável executá-los em um thread em segundo plano para impedir que a interface do usuário de bloqueio.

Usando os predicados (criado a partir de `CNContact` classe), você pode filtrar os resultados retornados ao buscar contatos do banco de dados. Para buscar somente os contatos que contêm a cadeia de caracteres `Appleseed`, use o seguinte código:

```csharp
// Create predicate to locate requested contact
var predicate = CNContact.GetPredicateForContacts("Appleseed");
```

> [!IMPORTANT]
> Não há suporte para predicados genéricos e compostos pela estrutura de contatos.

Por exemplo, para limitar a busca apenas para o **GivenName** e **FamilyName** propriedades do contato, use o seguinte código:

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

Se esse código foi executado após a amostra que criamos na **contatos objeto** seção acima, ele retornaria o contato de "John Appleseed" que acabamos de criar.

### <a name="contact-access-privacy"></a>Entre em contato com acesso de privacidade

Como os usuários finais podem conceder ou negar acesso a suas informações de contato em uma base por aplicativo, na primeira vez que você faz uma chamada para o `CNContactStore`, será apresentada uma caixa de diálogo solicitando para permitir o acesso para seu aplicativo.

A solicitação de permissão só será apresentada uma vez, na primeira vez em que o aplicativo é executada e subsequente é executado ou chamadas para o `CNContactStore` usará a permissão que o usuário selecionado no momento.

Você deve projetar seu aplicativo para que ele manipula normalmente o usuário Negar o acesso ao seu banco de dados de contato.

#### <a name="fetching-partial-contacts"></a>Buscando contatos parciais

Um _entre em contato com parcial_ é um contato que apenas algumas das propriedades disponíveis têm sido buscadas no armazenamento de contatos para. Se você tentar acessar uma propriedade que não foi obtida anteriormente, resultará em uma exceção.

Você pode facilmente verificar para ver se um determinado contato tem a propriedade desejada, usando o `IsKeyAvailable` ou `AreKeysAvailable` métodos da `CNContact` instância. Por exemplo:

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
> O `GetUnifiedContact` e `GetUnifiedContacts` métodos das `CNContactStore` classe _apenas_ retornar um contato parcial limitado para as propriedades solicitadas das chaves de busca fornecido.

### <a name="unified-contacts"></a>Contatos unificados

Um usuário pode ter diferentes fontes de informações de contato de uma única pessoa no seu banco de dados de contato (como iCloud, Facebook ou Google Mail). No iOS e aplicativos dos X, essas informações de contato serão automaticamente vinculadas e exibidas ao usuário como um único _unificado entre em contato com_:

[![](contacts-images/unified01.png "Visão geral de contatos unificada")](contacts-images/unified01.png#lightbox)

Essa Unificação entre em contato com é uma exibição temporária na memória as link de informações de contato que terá seu próprio identificador exclusivo (que deve ser usado para buscar novamente o contato, se necessário). Por padrão, a estrutura de contatos retornará um contato Unified sempre que possível.

### <a name="creating-and-updating-contacts"></a>Criar e atualizar contatos

Como vimos na [objetos entre em contato com](#Contact_Objects) seção acima, você usa um `CNContactStore` e uma instância de uma `CNMutableContact` para criar novos contatos, em seguida, são escritos para o usuário entre em contato com banco de dados usando um `CNSaveRequest`:

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

Um `CNSaveRequest` também pode ser usado para armazenar em cache várias alterações de contato e de grupo em uma única operação e em lote essas modificações para o `CNContactStore`.

Para atualizar um contato não mutável obtido de uma operação de busca, você deve primeiro solicitar uma cópia mutável que você, em seguida, modificar e salva de volta para o armazenamento de contatos. Por exemplo:

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

### <a name="contact-change-notifications"></a>Notificações de alteração entre em contato com

Sempre que um contato é modificado, o contato Store posta um `CNContactStoreDidChangeNotification` para o Centro de notificação padrão. Se você tiver armazenado em cache ou está exibindo todos os contatos, você precisará atualizar esses objetos do Store contato (`CNContactStore`).

### <a name="containers-and-groups"></a>Contêineres e grupos

Contatos de um usuário podem existir ou localmente no dispositivo do usuário ou como os contatos sincronizados com o dispositivo de uma ou mais contas de servidor (como Facebook ou Google). Cada pool de contatos tem seu próprio _contêiner_ e um determinado contato só pode existir em um contêiner.

[![](contacts-images/containers01.png "Visão geral de contêineres e grupos")](contacts-images/containers01.png#lightbox)

Permitir que alguns contêineres para os contatos sejam organizados em uma ou mais _grupos_ ou _subgrupos_. Esse comportamento é dependente do repositório de backup para um determinado contêiner. Por exemplo, iCloud tem apenas um contêiner, mas pode ter muitos grupos (mas sem subgrupos). Por outro lado, o Microsoft Exchange não dá suporte a grupos mas pode ter vários contêineres (uma para cada pasta do Exchange).

[![](contacts-images/containers02.png "Se sobrepõem dentro de contêineres e grupos")](contacts-images/containers02.png#lightbox)

<a name="contactsui" />

## <a name="the-contactsui-framework"></a>A estrutura ContactsUI

Para situações em que seu aplicativo não precisa apresentar uma interface do usuário personalizada, você pode usar a estrutura ContactsUI para apresentar os elementos de interface do usuário para exibir, editar, selecione e crie contatos em seu aplicativo xamarin. IOS.

Usando os controles internos da Apple não apenas reduzir a quantidade de código que você precisa para criar a contatos de suporte em seu aplicativo xamarin. IOS, mas apresentar uma interface consistente para os usuários do aplicativo.

### <a name="the-contact-picker-view-controller"></a>O controlador de exibição de seletor de contato

O controlador de exibição de seletor de contato (`CNContactPickerViewController`) gerencia a exibição padrão de seletor de contato que permite que o usuário selecione um contato ou uma propriedade de contato do banco de dados de contato do usuário. O usuário pode selecionar um ou mais contato (com base no seu uso) e o controlador de exibição entre em contato com seletor não solicitará permissão antes de exibir o seletor.

Antes de chamar o `CNContactPickerViewController` classe, você define quais propriedades o usuário pode selecionar e definir predicados para controlar a exibição e a seleção de propriedades de contato.

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

Para permitir que o usuário selecione um endereço de email em contatos em seu banco de dados, você pode usar o código a seguir:

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

O controlador de exibição do contato (`CNContactViewController`) classe fornece um controlador para apresentar uma exibição padrão de contato para o usuário final. O modo de exibição do contato pode exibir novos contatos de novo, desconhecido ou existente e o tipo deve ser especificado antes do modo de exibição é exibido ao chamar o construtor estático correto (`FromNewContact`, `FromUnknownContact`, `FromContact`). Por exemplo:

```csharp
// Create a new contact view
var view = CNContactViewController.FromContact(contact);

// Display the view
PresentViewController(view, true, null);
```

## <a name="summary"></a>Resumo

Este artigo apresentou uma visão detalhada de como trabalhar com as estruturas de contato e de contato da interface do usuário em um aplicativo xamarin. IOS. Primeiro, ele abordou os diferentes tipos de objetos que fornece a estrutura de contato e como usá-los para criar um novo ou acessar os contatos existentes. Ele também examinado a estrutura de interface do usuário entre em contato com para selecionar contatos existentes e exibir informações de contato.


## <a name="related-links"></a>Links relacionados

- [Exemplo de QuickContacts](https://developer.xamarin.com/samples/monotouch/ios9/QuickContacts/)
- [O que há de novo no iOS 9](https://developer.apple.com/library/content/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Referência de estrutura de contatos](https://developer.apple.com/documentation/contacts?language=objc)
- [Referência de estrutura ContactsUI](https://developer.apple.com/documentation/contactsui?language=objc)
