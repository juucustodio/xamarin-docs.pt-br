---
title: Autenticar um serviço Web RESTful
description: Autenticação básica oferece acesso a recursos somente a clientes que possuem as credenciais corretas. Este artigo explica como usar a autenticação básica para proteger o acesso aos recursos do serviço web RESTful.
ms.prod: xamarin
ms.assetid: 7B5FFDC4-F2AA-4B12-A30A-1DACC7FECBF1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/22/2018
ms.openlocfilehash: 5a0e820c8a9f04b7ad9173893852285d53dbe7a6
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69529208"
---
# <a name="authenticate-a-restful-web-service"></a>Autenticar um serviço Web RESTful

_HTTP oferece suporte ao uso de vários mecanismos de autenticação para controlar o acesso aos recursos. Autenticação básica oferece acesso a recursos somente a clientes que possuem as credenciais corretas. Este artigo demonstra como usar a autenticação básica para proteger o acesso aos recursos do serviço web RESTful._

> [!NOTE]
> No iOS 9 e superior, a segurança de transporte de aplicativo (ATS) impõe conexões seguras entre recursos da internet (como o servidor de back-end do aplicativo) e o aplicativo, impedindo assim a divulgação acidental de informações confidenciais. Desde que o ATS é habilitado por padrão em aplicativos criados para o iOS 9, todas as conexões serão sujeitos a requisitos de segurança ATS. Se as conexões não atender a esses requisitos, eles falharão com uma exceção.
> ATS poderá ser aceito de se ele não é possível usar o `HTTPS` de protocolo e proteger a comunicação para recursos da internet. Isso pode ser feito atualizando o aplicativo **Info. plist** arquivo. Para obter mais informações, consulte [segurança de transporte de aplicativo](~/ios/app-fundamentals/ats.md).

## <a name="authenticating-users-over-http"></a>Autenticar usuários via HTTP

Autenticação básica é o mecanismo de autenticação mais simples compatível com HTTP e envolve o cliente que envia o nome de usuário e a senha como texto não criptografado codificado na base64. Ele funciona da seguinte maneira:

- Se um serviço web recebe uma solicitação para um recurso protegido, ele rejeita a solicitação com um código de status HTTP 401 (acesso negado) e define o cabeçalho de resposta WWW-Authenticate, conforme mostrado no diagrama a seguir:

![](rest-images/basic-authentication-fail.png "Falha de autenticação básica")

- Se um serviço web recebe uma solicitação para um recurso protegido, com o `Authorization` cabeçalho definido corretamente, o web serviço responde com um código de status HTTP 200, que indica se a solicitação foi bem-sucedida e que as informações solicitadas estão na resposta. Este cenário é mostrado no diagrama a seguir:

![](rest-images/basic-authentication-success.png "Após a autenticação básica")

> [!NOTE]
> Autenticação básica deve ser usada apenas ao longo de uma conexão HTTPS. Quando usado em uma conexão HTTP, o `Authorization` cabeçalho pode ser decodificado com facilidade se o tráfego HTTP é capturado por um invasor.

## <a name="specifying-basic-authentication-in-a-web-request"></a>Especificar a autenticação básica em uma solicitação da Web

Uso da autenticação básica é especificado da seguinte maneira:

1. A cadeia de caracteres "Básico" é adicionado para o `Authorization` cabeçalho da solicitação.
1. O nome de usuário e senha são combinados em uma cadeia de caracteres com o formato "nomedeusuario: senha", que é, em seguida, na Base 64 codificados e adicionado ao `Authorization` cabeçalho da solicitação.

Portanto, com um nome de usuário de 'XamarinUser' e uma senha de 'XamarinPassword', o cabeçalho se torna:

```csharp
Authorization: Basic WGFtYXJpblVzZXI6WGFtYXJpblBhc3N3b3Jk
```

O `HttpClient` classe pode definir o `Authorization` valor do cabeçalho no `HttpClient.DefaultRequestHeaders.Authorization` propriedade. Porque o `HttpClient` instância existe em várias solicitações, o `Authorization` cabeçalho só precisa ser definida uma vez, em vez de quando fazer cada solicitação, conforme mostrado no exemplo de código a seguir:

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

Em seguida, quando é feita uma solicitação para uma operação de serviço da web a solicitação é assinada com o `Authorization` cabeçalho, que indica se o usuário tem permissão para invocar a operação.

> [!NOTE]
> Embora esse código armazene credenciais como constantes, elas não devem ser armazenadas em um formato não seguro em um aplicativo publicado. O [Xamarith.Auth](https://www.nuget.org/packages/Xamarin.Auth/) NuGet fornece funcionalidade para armazenar com segurança as credenciais. Para obter mais informações, consulte [armazenando e recuperando informações da conta em dispositivos](~/xamarin-forms/data-cloud/authentication/oauth.md).

## <a name="processing-the-authorization-header-server-side"></a>Processamento do lado do servidor de cabeçalho de autorização

O serviço REST deve decorar cada ação com o `[BasicAuthentication]` atributo. Esse atributo é usado para analisar o `Authorization` cabeçalho e determinar se as credenciais codificadas em base64 são válidas comparando-as com os valores armazenados em *Web. config*. Embora essa abordagem seja adequada para um serviço de exemplo, ela requer a extensão de um serviço Web voltado ao público.

O módulo de autenticação básica usada pelo IIS, os usuários são autenticados em relação às suas credenciais do Windows. Portanto, os usuários devem ter contas de domínio do servidor. No entanto, o modelo de autenticação básica pode ser configurado para permitir a autenticação personalizada, em que as contas de usuário são autenticadas em relação a uma fonte externa, como um banco de dados. Para obter mais informações, consulte [autenticação básica na API Web ASP.NET](http://www.asp.net/web-api/overview/security/basic-authentication) no site do ASP.NET.

> [!NOTE]
> Autenticação básica não foi projetada para gerenciar o log de. Portanto, é a abordagem de autenticação básica padrão para fazer logoff encerrar a sessão.

## <a name="related-links"></a>Links relacionados

- [Consumir um serviço Web RESTful](~/xamarin-forms/data-cloud/web-services/rest.md)
- [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx)
