---
title: Consumir um serviço de SimpleDB Amazon
description: Amazon SimpleDB é um serviço web que fornece a capacidade de armazenar e consultar dados na nuvem da Amazon. Este artigo explica como usar o AWS SDK para .NET para consultar, criar, substituir e excluir dados armazenados em um serviço SimpleDB.
ms.prod: xamarin
ms.assetid: 823819AA-15F9-4144-B355-78A10AD37513
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2016
ms.openlocfilehash: 1602319dbf5a5d00ac5de75f2d438b9aea692699
ms.sourcegitcommit: 271d3f7ea4abfcf87734d2c747a68cb8114d743c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/12/2018
---
# <a name="consuming-an-amazon-simpledb-service"></a>Consumir um serviço de SimpleDB Amazon

_Amazon SimpleDB é um serviço web que fornece a capacidade de armazenar e consultar dados na nuvem da Amazon. Este artigo explica como usar o AWS SDK para .NET para consultar, criar, substituir e excluir dados armazenados em um serviço SimpleDB._

Serviços de SimpleDB usam um modelo de solicitação e resposta familiar para os consumidores de serviços REST. As operações são invocadas no serviço SimpleDB enviando uma solicitação, que pode conter dados. Depois de processar a solicitação, o serviço SimpleDB retorna uma resposta que contém todos os resultados. Um serviço de SimpleDB devem ser criado programaticamente e não pode ser criado por meio de [AWS Console](https://aws.amazon.com). No entanto, uma conta AWS é necessária para criar e acessar qualquer Amazon web Services.

Em um serviço SimpleDB, os dados são organizados em domínios, no qual os dados podem ser colocados e executem consultas nos dados. Domínios consistem em itens que são descritos por pares de nome-valor do atributo. Domínios podem ser pensados como sendo similares às tabelas, com atributos sendo similares para itens sendo similares às linhas e colunas. Para obter mais informações sobre o modelo de dados SimpleDB, consulte [modelo de dados](http://docs.aws.amazon.com/AmazonSimpleDB/latest/DeveloperGuide/DataModel.html) no site da Amazon.

As instruções sobre como configurar os serviços necessários do Amazon podem ser encontradas no arquivo Leiame que acompanha o aplicativo de exemplo. Quando o aplicativo de exemplo é executado, ele se conectará a um pool de identidade Cognito Amazon para autorizar o acesso ao serviço SimpleDB, conforme mostrado na seguinte captura de tela:

![](aws-images/portal.png "Aplicativo de exemplo")

> [!NOTE]
> No iOS 9 e superior, a segurança de transporte do aplicativo (ATS) impõe conexões seguras entre os recursos de internet (como o servidor de back-end do aplicativo) e o aplicativo, impedindo assim a divulgação acidental de informações confidenciais. Como ATS está habilitado por padrão em aplicativos criados para o iOS 9, todas as conexões serão sujeitos a requisitos de segurança ATS. Se as conexões não atender a esses requisitos, eles falham com uma exceção.
> ATS pode ser incluído em se não for possível usar o `HTTPS` de protocolo e proteger a comunicação para recursos da internet. Isso pode ser obtido pela atualização do aplicativo **Info. plist** arquivo. Para obter mais informações, consulte [segurança de transporte do aplicativo](~/ios/app-fundamentals/ats.md).

## <a name="consuming-a-simpledb-service"></a>Consumir um serviço SimpleDB

Identidade do Amazon Cognito permite que os serviços AWS, como SimpleDB, seja invocado a partir de um aplicativo sem credenciais AWS embutir em código no aplicativo. Em vez disso, um pool de identidade exclusiva é criado no [Amazon Cognito Console](https://console.aws.amazon.com/cognito/home). O pool de identidade contém identidades que usam funções para especificar os recursos, como SimpleDB, que a identidade pode acessar.

O [AWS SDK para .NET](https://www.nuget.org/packages?q=Tags%3A%22aws-sdk-v3%22) fornece o `CognitoAWSCredentials` e `AmazonSimpleDBClient` classes, que são usados por um aplicativo xamarin. Forms para acessar o serviço SimpleDB, conforme mostrado no exemplo de código a seguir:

```csharp
AmazonSimpleDBClient client;
...

public SimpleDBStorage ()
{
  var credentials = new CognitoAWSCredentials (
                      Constants.CognitoIdentityPoolId,
                      RegionEndpoint.USEast1);
  var config = new AmazonSimpleDBConfig ();
  config.RegionEndpoint = RegionEndpoint.USWest2;
  client = new AmazonSimpleDBClient (credentials, config);
  ...
}
```

Uma nova instância do `CognitoAWSCredentials` classe é criada, fornecendo a id do pool de identidade exclusiva e a região da conta de identidade Cognito. No momento da gravação, identidade Cognito só está disponível nas regiões USEast1 e EUWest1. No entanto, ele pode se comunicar com serviços Amazon fora essas regiões.

Quando o `AmazonSimpleDBClient` instância é criada, o `CognitoAWSCredentials` instância deve ser fornecida, juntamente com um `AmazonSimpleDBConfig` instância que especifica a região geográfica em que reside o serviço SimpleDB. O `CognitoAWSCredentials` instância garante que o serviço de SimpleDB que é acessado um associado à conta AWS no qual o pool de identidade foi criado, evitando a necessidade de inserir uma chave de acesso do AWS e a chave secreta do aplicativo.

Um domínio de serviço SimpleDB é criado chamando o `AmazonSimpleDBClient.CreateDomainAsync` método, conforme mostrado no exemplo de código a seguir:

```csharp
string tableName = "Todo";
...

async Task CreateDomain ()
{
  ...
  await client.CreateDomainAsync (new CreateDomainRequest { DomainName = tableName });
  ...
}
```

O `CreateDomainAsync` método exige um `CreateDomainRequest` instância como um parâmetro. O `CreateDomainRequest` instância inicializa o `DomainName` propriedade para o valor a ser usado para identificar o domínio. Para criar o domínio, esse valor deve ser exclusivo entre os domínios associados à conta AWS. Caso contrário, o domínio não será criado e nenhuma resposta de erro será enviada para indicar que. Todas as operações em relação ao nome de domínio, em seguida, ocorrerá em domínio existente, em vez de um domínio recém-criado.

### <a name="creating-simpledb-objects"></a>Criando objetos SimpleDB

O aplicativo de exemplo usa o `TodoItem` classe para dados de modelo. Para armazenar um `TodoItem` instância em um serviço SimpleDB primeiro deve ser convertida em uma `List` de `ReplaceableAttribute` objetos. Isso é feito usando o `ToSimpleDBReplaceableAttributes` método, conforme mostrado no exemplo de código a seguir:

```csharp
List<ReplaceableAttribute> ToSimpleDBReplaceableAttributes (TodoItem item)
{
  return new List<ReplaceableAttribute> () {
    new ReplaceableAttribute () {
      Name = "Name",
      Value = item.Name,
      Replace = true
    },
    new ReplaceableAttribute () {
      Name = "Notes",
      Value = item.Notes,
      Replace = true
    },
    new ReplaceableAttribute () {
      Name = "Done",
      Value = item.Done.ToString (),
      Replace = true
    }
  };
}
```

Esse método cria um `List` do novo `ReplaceableAttribute` instâncias, com o `List` que representa um único `TodoItem` instância. Cada `ReplaceableAttribute` instância representa uma única propriedade da `TodoItem` instância. Para obter mais informações sobre o `ReplaceableAttribute` de classe, consulte [ReplaceableAttribute classe](http://docs.aws.amazon.com/sdkfornet1/latest/apidocs/html/T_Amazon_SimpleDB_Model_ReplaceableAttribute.htm) no site da Amazon.

Da mesma forma, quando os dados são recuperados do serviço de SimpleDB, ele deve ser convertido de um `List` de `Attribute` instâncias para um `TodoItem` instância. Isso é feito com o `FromSimpleDBAttributes` método, conforme mostrado no exemplo de código a seguir:

```csharp
TodoItem FromSimpleDBAttributes (List<Amazon.SimpleDB.Model.Attribute> attributeList, string id)
{
  var todoItem = new TodoItem ();
  todoItem.ID = id;
  todoItem.Name = attributeList.Where (attr => attr.Name == "Name").FirstOrDefault ().Value;
  todoItem.Notes = attributeList.Where (attr => attr.Name == "Notes").FirstOrDefault ().Value;
  todoItem.Done = Convert.ToBoolean (attributeList.Where (attr => attr.Name == "Done").FirstOrDefault ().Value);
  return todoItem;
}
```

Este método simplesmente recupera cada `Attribute` da instância do `List` e define-a no recém-criado `TodoItem` instância.

Para obter mais informações sobre o `Attribute` de classe, consulte [classe de atributo](http://docs.aws.amazon.com/sdkfornet1/latest/apidocs/html/T_Amazon_SimpleDB_Model_Attribute.htm) no site da Amazon.

### <a name="querying-data"></a>Consultar Dados

O conteúdo de um domínio pode ser recuperado chamando o `AmazonSimpleDBClient.SelectAsync` método, conforme mostrado no exemplo de código a seguir:

```csharp
public async Task<List<TodoItem>> RefreshDataAsync ()
{
  ...
  var request = new SelectRequest () {
      SelectExpression = string.Format ("SELECT * from {0}", tableName)
  };
  var response = await client.SelectAsync (request);
  foreach (var item in response.Items) {
    Items.Add (FromSimpleDBAttributes (item.Attributes, item.Name));
  }
  ...
}
```

O `SelectAsync` método aceita um `SelectRequest` instância como um parâmetro que especifica um `Select` consulta de expressão do `SelectExpression` propriedade. O formato da expressão de consulta é semelhante ao formato do padrão SQL `SELECT` instrução. Para obter mais informações sobre a expressão de consulta, consulte [usando Select para criar consultas de SimpleDB Amazon](http://docs.aws.amazon.com/AmazonSimpleDB/latest/DeveloperGuide/UsingSelect.html) no site da Amazon.

> [!NOTE]
> Certifique-se de seguir as regras de cotação ao construir a expressão de consulta. Para obter mais informações, consulte [selecionar regras citando](http://docs.aws.amazon.com/AmazonSimpleDB/latest/DeveloperGuide/QuotingRulesSelect.html) no site da Amazon.

O `SelectAsync` método retorna uma resposta que contém uma coleção de itens e atributos associados que correspondem à expressão de consulta. Essa coleção é convertida em um `List` de `TodoItem` instâncias para exibição.

### <a name="creating-and-replacing-data"></a>Criando e substituindo dados

O `AmazonSimpleDBClient.PutAttributesAsync` método é usado para criar e substituir os dados no domínio de serviço SimpleDB, conforme mostrado no exemplo de código a seguir:

```csharp
public async Task SaveTodoItemAsync (TodoItem todoItem)
{
  ...
  var attributeList = ToSimpleDBReplaceableAttributes (todoItem);
  var request = new PutAttributesRequest () {
      DomainName = tableName,
      ItemName = todoItem.ID,
      Attributes = attributeList
  };
  await client.PutAttributesAsync (request);
  ...
}
```

O `PutAttributesAsync` método aceita um `PutAttributesRequest` instância como um parâmetro. O `PutAttributesRequest` instância Especifica os pares de nome-valor do atributo a ser criado como um novo item ou substituída em um item existente. O `List` de `ReplaceableAttribute` instâncias é criada com o `ToSimpleDBReplaceableAttributes` método. Esse método também define o `Replace` propriedade de cada `ReplaceableAttribute` para `true`. Isso fará com que o novo valor de atributo substituir um valor de atributo existente se dados está sendo substituídos. No entanto, a tentativa de substituir os valores de atributo que não existem não resultará em uma resposta de erro.

O valor de `PutAttributesRequest.ItemName` propriedade controla se um novo item será adicionado ao domínio, ou se um item existente será substituído. Quando o aplicativo cria um novo item, ele define o `TodoItem.ID` propriedade para um novo `Guid`. Isso garante que cada `TodoItem` instância tem um identificador exclusivo. Portanto, se o `PutAttributesRequest.ItemName` propriedade é definida como um valor que não existe no domínio, o serviço SimpleDB criará um novo item que contém os pares de nome-valor do atributo especificado. Se o `PutAttributesRequest.ItemName` propriedade é definida como um valor que já existe no domínio, o serviço SimpleDB atualizará o item com os pares de nome-valor do atributo especificado.

### <a name="deleting-data"></a>Excluindo dados

O `AmazonSimpleDBClient.DeleteAttributesAsync` método é usado para excluir dados de domínio de serviço SimpleDB, conforme mostrado no exemplo de código a seguir:

```csharp
public async Task DeleteTodoItemAsync (TodoItem todoItem)
{
  ...
  var attributeList = ToSimpleDBAttributes (todoItem);
  var request = new DeleteAttributesRequest () {
      DomainName = tableName,
      ItemName = todoItem.ID,
      Attributes = attributeList
  };
  await client.DeleteAttributesAsync (request);
  ...
}
```

O `DeleteAttributesAsync` método aceita um `DeleteAttributesRequest` instância como um parâmetro.  O `DeleteAttributesRequest` instância Especifica os atributos que devem ser excluídos do item, com o `List` de `Attribute` instâncias a ser excluído está sendo compilado pelo `ToSimpleDBAttributes` método. O item é excluído desde que todos os atributos do item são excluídos.

## <a name="summary"></a>Resumo

Este artigo explicou como usar o AWS SDK para .NET para consultar, criar e substitua e excluir dados armazenados em um serviço SimpleDB. Esse SDK fornece o `CognitoAWSCredentials` e `AmazonSimpleDBClient` classes que são usadas por um aplicativo xamarin. Forms para acessar o serviço SimpleDB.


## <a name="related-links"></a>Links relacionados

- [TodoAWS (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAWS/)
- [Amazon Web Services SDK guia do desenvolvedor do Xamarin](http://docs.aws.amazon.com/mobile/sdkforxamarin/developerguide/)
- [Amazon Cognito Identity](http://docs.aws.amazon.com/cognito/devguide/identity/)
- [Documentação do desenvolvedor de SimpleDB Amazon](http://docs.aws.amazon.com/AmazonSimpleDB/latest/DeveloperGuide/Welcome.html)
- [Classe AmazonSimpleDBClient](http://docs.aws.amazon.com/sdkfornet1/latest/apidocs/html/T_Amazon_SimpleDB_AmazonSimpleDBClient.htm)
- [Amazon Web Services SDK para .NET](https://www.nuget.org/packages?q=Tags%3A%22aws-sdk-v3%22)
