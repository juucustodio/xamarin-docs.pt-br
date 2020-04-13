---
title: Autenticar um serviço web RESTful
description: A autenticação básica fornece acesso aos recursos apenas para os clientes que possuem as credenciais corretas. Este artigo explica como usar a autenticação básica para proteger o acesso aos recursos de serviçoweb RESTful.
ms.prod: xamarin
ms.assetid: 7B5FFDC4-F2AA-4B12-A30A-1DACC7FECBF1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/22/2018
ms.openlocfilehash: 65606b72c3944809a09b8c70b9cdbb5524e0f856
ms.sourcegitcommit: 2a8eb8bce427e72d4e7edd06ce432e19f17dcdd7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2020
ms.locfileid: "80388584"
---
# <a name="authenticate-a-restful-web-service"></a>Autenticar um serviço web RESTful

_O HTTP suporta o uso de vários mecanismos de autenticação para controlar o acesso aos recursos. A autenticação básica fornece acesso aos recursos apenas para os clientes que possuem as credenciais corretas. Este artigo demonstra como usar a autenticação básica para proteger o acesso aos recursos de serviço web RESTful._

> [!NOTE]
> No iOS 9 ou maior, o APP Transport Security (ATS) impõe conexões seguras entre os recursos da internet (como o servidor back-end do aplicativo) e o aplicativo, impedindo assim a divulgação acidental de informações confidenciais. Uma vez que o ATS é habilitado por padrão em aplicativos construídos para o iOS 9, todas as conexões estarão sujeitas aos requisitos de segurança ats. Se as conexões não atenderem a esses requisitos, elas falharão com uma exceção.
> O ATS pode ser optado se `HTTPS` não for possível usar o protocolo e a comunicação segura para recursos de internet. Isso pode ser conseguido atualizando o arquivo **Info.plist** do aplicativo. Para obter mais informações, consulte [App Transport Security](~/ios/app-fundamentals/ats.md).

## <a name="authenticating-users-over-http"></a>Autenticando usuários sobre HTTP

A autenticação básica é o mecanismo de autenticação mais simples suportado pelo HTTP, e envolve o cliente enviando o nome de usuário e a senha como texto codificado base64 não criptografado. Ele funciona da seguinte maneira:

- Se um serviço web receber uma solicitação de um recurso protegido, ele rejeitará a solicitação com um código de status HTTP 401 (acesso negado) e definirá o cabeçalho de resposta WWW-Authenticate, conforme mostrado no diagrama a seguir:

![](rest-images/basic-authentication-fail.png "Basic Authentication Failing")

- Se um serviço web receber uma solicitação `Authorization` de um recurso protegido, com o cabeçalho corretamente definido, o serviço web responderá com um código de status HTTP 200, o que indica que a solicitação foi bem sucedida e que as informações solicitadas estão na resposta. Este cenário é mostrado no seguinte diagrama:

![](rest-images/basic-authentication-success.png "Basic Authentication Succeeding")

> [!NOTE]
> A autenticação básica só deve ser usada em uma conexão HTTPS. Quando usado através de `Authorization` uma conexão HTTP, o cabeçalho pode ser facilmente decodificado se o tráfego HTTP for capturado por um invasor.

## <a name="specifying-basic-authentication-in-a-web-request"></a>Especificando a autenticação básica em uma solicitação da Web

O uso da autenticação básica é especificado da seguinte forma:

1. A string "Basic" é `Authorization` adicionada ao cabeçalho da solicitação.
1. O nome de usuário e a senha são combinados em uma seqüência com o formato `Authorization` "nome de usuário:senha", que é então codificado e adicionado ao cabeçalho da solicitação.

Portanto, com um nome de usuário de 'XamarinUser' e uma senha de 'XamarinPassword', o cabeçalho se torna:

```csharp
Authorization: Basic WGFtYXJpblVzZXI6WGFtYXJpblBhc3N3b3Jk
```

A `HttpClient` classe pode `Authorization` definir o `HttpClient.DefaultRequestHeaders.Authorization` valor do cabeçalho na propriedade. Como `HttpClient` a instância existe em `Authorization` várias solicitações, o cabeçalho só precisa ser definido uma vez, em vez de fazer cada solicitação, como mostrado no exemplo de código a seguir:

```csharp
public class RestService : IRestService
{
  HttpClient _client;
  ...

  public RestService ()
  {
    var authData = string.Format ("{0}:{1}", Constants.Username, Constants.Password);
    var authHeaderValue = Convert.ToBase64String (Encoding.UTF8.GetBytes (authData));

    _client = new HttpClient ();
    _client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue ("Basic", authHeaderValue);
  }
  ...
}
```

Em seguida, quando uma solicitação é feita a `Authorization` uma operação de serviço web, a solicitação é assinada com o cabeçalho, indicando se o usuário tem ou não permissão para invocar a operação.

> [!IMPORTANT]
> Embora este código armazene credenciais como constantes, elas não devem ser armazenadas em um formato inseguro em um aplicativo publicado.

## <a name="processing-the-authorization-header-server-side"></a>Processando o lado do servidor do cabeçalho de autorização

O serviço REST deve decorar `[BasicAuthentication]` cada ação com o atributo. Este atributo é usado `Authorization` para analisar o cabeçalho e determinar se as credenciais codificadas base64 são válidas comparando-as com os valores armazenados na *Web.config*. Embora essa abordagem seja adequada para um serviço de amostra, ela requer uma extensão para um serviço web voltado para o público.

No módulo básico de autenticação usado pelo IIS, os usuários são autenticados contra suas credenciais do Windows. Portanto, os usuários devem ter contas no domínio do servidor. No entanto, o modelo de autenticação Básica pode ser configurado para permitir autenticação personalizada, onde contas de usuário são autenticadas contra uma fonte externa, como um banco de dados. Para obter mais informações, consulte [autenticação básica em ASP.NET API da Web](https://www.asp.net/web-api/overview/security/basic-authentication) no site ASP.NET.

> [!NOTE]
> A autenticação básica não foi projetada para gerenciar o logout. Portanto, a abordagem padrão de autenticação básica para logout é encerrar a sessão.

## <a name="related-links"></a>Links relacionados

- [Consumir um serviço web RESTful](~/xamarin-forms/data-cloud/web-services/rest.md)
- [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx)
