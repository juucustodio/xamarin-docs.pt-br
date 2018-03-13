---
title: "Autenticação de usuários com um serviço de SimpleDB Amazon"
description: "Amazon SimpleDB não tem seu próprio sistema de permissões com base em recursos. Em vez disso, a autenticação em relação a um provedor de identidade pode ser usada para garantir que os usuários só tenham acesso aos seus próprios dados no domínio SimpleDB. Este artigo explica como restringir o acesso dos usuários para seus próprios dados SimpleDB."
ms.topic: article
ms.prod: xamarin
ms.assetid: 797C91A5-9720-4DAC-89D8-5C85996584C8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2016
ms.openlocfilehash: ac4c788b4bd48991d7628d892ad1ece3d2451228
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="authenticating-users-with-an-amazon-simpledb-service"></a>Autenticação de usuários com um serviço de SimpleDB Amazon

_Amazon SimpleDB não tem seu próprio sistema de permissões com base em recursos. Em vez disso, a autenticação em relação a um provedor de identidade pode ser usada para garantir que os usuários só tenham acesso aos seus próprios dados no domínio SimpleDB. Este artigo explica como restringir o acesso dos usuários para seus próprios dados SimpleDB._

[Xamarin.Auth](https://github.com/xamarin/Xamarin.Auth) é usado no aplicativo de exemplo para gerenciar o processo de autenticação de usuário e armazenar com segurança os detalhes da conta de usuário no dispositivo. Para obter mais informações, consulte [autenticando usuários com um provedor de identidade](~/xamarin-forms/data-cloud/authentication/oauth.md).

## <a name="allowing-an-authenticated-user-access-to-simpledb-domain-data"></a>Permitir que um usuário autenticado de acesso a dados de domínio SimpleDB

O aplicativo de exemplo usa o `TodoItem` classe para dados de modelo. Para armazenar um `TodoItem` instância em um serviço SimpleDB primeiro deve ser convertida em uma `List` de `ReplaceableAttribute` objetos. Para obter mais informações, consulte [SimpleDB criando objetos](~/xamarin-forms/data-cloud/consuming/aws.md).

> [!NOTE]
> No iOS 9 e superior, a segurança de transporte do aplicativo (ATS) impõe conexões seguras entre os recursos de internet (como o servidor de back-end do aplicativo) e o aplicativo, impedindo assim a divulgação acidental de informações confidenciais. Como ATS está habilitado por padrão em aplicativos criados para o iOS 9, todas as conexões serão sujeitos a requisitos de segurança ATS. Se as conexões não atender a esses requisitos, eles falham com uma exceção.
> ATS pode ser incluído em se não for possível usar o `HTTPS` de protocolo e proteger a comunicação para recursos da internet. Isso pode ser obtido pela atualização do aplicativo **Info. plist** arquivo. Para obter mais informações, consulte [segurança de transporte do aplicativo](~/ios/app-fundamentals/ats.md).

Para garantir que os usuários tenham acesso a apenas seus próprios dados no domínio SimpleDB, o `ToSimpleDBReplaceableAttributes` método armazena um atributo adicional para um `TodoItem` de instância, conforme mostrado no exemplo de código a seguir:

```csharp
List<ReplaceableAttribute> ToSimpleDBReplaceableAttributes (TodoItem item)
{
  return new List<ReplaceableAttribute> () {
    ...
    new ReplaceableAttribute () {
      Name = "User",
      Value = App.User.Email,
      Replace = true
    },
  };
}
```

Esse atributo garante que cada item armazenado no domínio SimpleDB tem um endereço de email associado para um usuário, que é usado para identificar exclusivamente o usuário que pertencem os dados. Quando o conteúdo de um domínio é recuperado chamando o `AmazonSimpleDBClient.SelectAsync` método, a expressão de consulta garante que apenas os itens para o usuário autenticado são recuperados, conforme mostrado no exemplo de código a seguir:

```csharp
public async Task<List<TodoItem>> RefreshDataAsync ()
{
  ...
  var request = new SelectRequest () {
    SelectExpression = string.Format ("SELECT * from {0} WHERE User = '{1}'", tableName, App.User.Email)
  };
  var response = await client.SelectAsync (request);
  ...
}
```

O `SelectAsync` método retorna uma resposta que contém uma coleção de itens e atributos associados que correspondem à expressão de consulta. A expressão de consulta garante que apenas os itens que correspondam o endereço de email do usuário serão recuperados. Para obter mais informações sobre expressões de consulta, consulte [usando Select para criar consultas de SimpleDB Amazon](http://docs.aws.amazon.com/AmazonSimpleDB/latest/DeveloperGuide/UsingSelect.html) no site da Amazon.

> [!NOTE]
> Certifique-se de seguir as regras de cotação ao construir a expressão de consulta. Para obter mais informações, consulte [selecionar regras citando](http://docs.aws.amazon.com/AmazonSimpleDB/latest/DeveloperGuide/QuotingRulesSelect.html) no site da Amazon.

## <a name="summary"></a>Resumo

Este artigo explicou como restringir o acesso dos usuários para seus próprios dados SimpleDB. Amazon SimpleDB não tem seu próprio sistema de permissões com base em recursos. Em vez disso, a autenticação em relação a um provedor de identidade pode ser usada para garantir que os usuários tenham acesso a apenas seus próprios dados no domínio SimpleDB.


## <a name="related-links"></a>Links relacionados

- [TodoAWSAuth (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAWSAuth/)
- [Consumir um serviço de SimpleDB Amazon](~/xamarin-forms/data-cloud/consuming/aws.md)
- [Autenticação de usuários com um provedor de identidade](~/xamarin-forms/data-cloud/authentication/oauth.md)
- [Amazon Cognito Identity](http://docs.aws.amazon.com/cognito/devguide/identity/)
- [Documentação do desenvolvedor de SimpleDB Amazon](http://docs.aws.amazon.com/AmazonSimpleDB/latest/DeveloperGuide/Welcome.html)
- [Amazon Web Services SDK para .NET](https://www.nuget.org/packages?q=Tags%3A%22aws-sdk-v3%22)
