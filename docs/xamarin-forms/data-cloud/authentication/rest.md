---
title: Autenticar um serviço Web RESTful
description: A autenticação básica fornece acesso a recursos somente para os clientes que têm as credenciais corretas. Este artigo explica como usar a autenticação básica para proteger o acesso aos recursos do serviço Web RESTful.
ms.prod: xamarin
ms.assetid: 7B5FFDC4-F2AA-4B12-A30A-1DACC7FECBF1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/22/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 98b59b1dc9e32b1af1be991055057b94223a4223
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563257"
---
# <a name="authenticate-a-restful-web-service"></a>Autenticar um serviço Web RESTful

_O HTTP dá suporte ao uso de vários mecanismos de autenticação para controlar o acesso aos recursos. A autenticação básica fornece acesso a recursos somente para os clientes que têm as credenciais corretas. Este artigo demonstra como usar a autenticação básica para proteger o acesso aos recursos do serviço Web RESTful._

> [!NOTE]
> No iOS 9 e superior, a ATS (segurança de transporte de aplicativo) impõe conexões seguras entre os recursos da Internet (como o servidor back-end do aplicativo) e o aplicativo, impedindo, assim, a divulgação acidental de informações confidenciais. Como o ATS está habilitado por padrão em aplicativos criados para iOS 9, todas as conexões estarão sujeitas a requisitos de segurança de ATS. Se as conexões não atenderem a esses requisitos, elas falharão com uma exceção.
> O ATS pode ser recusado se não for possível usar o `HTTPS` protocolo e a comunicação segura para recursos da Internet. Isso pode ser feito atualizando o arquivo **info. plist** do aplicativo. Para obter mais informações, consulte [segurança do transporte de aplicativo](~/ios/app-fundamentals/ats.md).

## <a name="authenticating-users-over-http"></a>Autenticando usuários por HTTP

A autenticação básica é o mecanismo de autenticação mais simples suportado pelo HTTP e envolve o cliente que envia o nome de usuário e a senha como texto codificado em base64 não criptografado. Ele funciona da seguinte maneira:

- Se um serviço Web receber uma solicitação de um recurso protegido, ele rejeitará a solicitação com um código de status HTTP 401 (acesso negado) e definirá o cabeçalho de resposta WWW-Authenticate, conforme mostrado no diagrama a seguir:

![Falha na autenticação básica](rest-images/basic-authentication-fail.png)

- Se um serviço Web receber uma solicitação de um recurso protegido, com o `Authorization` cabeçalho definido corretamente, o serviço Web responderá com um código de status HTTP 200, que indica que a solicitação foi bem-sucedida e que as informações solicitadas estão na resposta. Esse cenário é mostrado no diagrama a seguir:

![Autenticação básica com sucesso](rest-images/basic-authentication-success.png)

> [!NOTE]
> A autenticação básica só deve ser usada em uma conexão HTTPS. Quando usado em uma conexão HTTP, o `Authorization` cabeçalho poderá ser facilmente decodificado se o tráfego http for capturado por um invasor.

## <a name="specifying-basic-authentication-in-a-web-request"></a>Especificando a autenticação básica em uma solicitação da Web

O uso da autenticação básica é especificado da seguinte maneira:

1. A cadeia de caracteres "Basic" é adicionada ao `Authorization` cabeçalho da solicitação.
1. O nome de usuário e a senha são combinados em uma cadeia de caracteres com o formato "username: password", que é codificado em Base64 e adicionado ao `Authorization` cabeçalho da solicitação.

Portanto, com um nome de usuário de ' XamarinUser ' e uma senha de ' XamarinPassword ', o cabeçalho se torna:

```csharp
Authorization: Basic WGFtYXJpblVzZXI6WGFtYXJpblBhc3N3b3Jk
```

A `HttpClient` classe pode definir o `Authorization` valor do cabeçalho na `HttpClient.DefaultRequestHeaders.Authorization` propriedade. Como a `HttpClient` instância existe em várias solicitações, o `Authorization` cabeçalho precisa ser definido apenas uma vez, em vez de ao fazer cada solicitação, conforme mostrado no exemplo de código a seguir:

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

Em seguida, quando uma solicitação é feita a uma operação de serviço Web, a solicitação é assinada com o `Authorization` cabeçalho, indicando se o usuário tem permissão para invocar a operação.

> [!IMPORTANT]
> Embora esse código armazene credenciais como constantes, elas não devem ser armazenadas em um formato não seguro em um aplicativo publicado.

## <a name="processing-the-authorization-header-server-side"></a>Processando o lado do servidor de cabeçalho de autorização

O serviço REST deve decorar cada ação com o `[BasicAuthentication]` atributo. Esse atributo é usado para analisar o `Authorization` cabeçalho e determinar se as credenciais codificadas em base64 são válidas comparando-as com os valores armazenados em *Web.config*. Embora essa abordagem seja adequada para um serviço de exemplo, ela requer a extensão de um serviço Web voltado ao público.

No módulo de autenticação básica usado pelo IIS, os usuários são autenticados em relação às suas credenciais do Windows. Portanto, os usuários devem ter contas no domínio do servidor. No entanto, o modelo de autenticação básica pode ser configurado para permitir a autenticação personalizada, em que as contas de usuário são autenticadas em uma fonte externa, como um banco de dados. Para obter mais informações, consulte [autenticação básica em ASP.NET Web API](https://www.asp.net/web-api/overview/security/basic-authentication) no site ASP.net.

> [!NOTE]
> A autenticação básica não foi projetada para gerenciar o logout. Portanto, a abordagem de autenticação básica padrão para fazer logoff é encerrar a sessão.

## <a name="related-links"></a>Links Relacionados

- [Consumir um serviço Web RESTful](~/xamarin-forms/data-cloud/web-services/rest.md)
- [HttpClient](/dotnet/api/system.net.http.httpclient)