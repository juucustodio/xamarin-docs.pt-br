---
title: Autenticar um serviço Web RESTful
description: Autenticação básica oferece acesso a recursos somente a clientes que possuem as credenciais corretas. Este artigo explica como usar a autenticação básica para proteger o acesso aos recursos do serviço web RESTful.
ms.prod: xamarin
ms.assetid: 7B5FFDC4-F2AA-4B12-A30A-1DACC7FECBF1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/22/2017
ms.openlocfilehash: e2ab6c053901ad6c1668c5ae5be9ab04d9d05e8a
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2018
ms.locfileid: "53050329"
---
# <a name="authenticating-a-restful-web-service"></a>Autenticar um serviço Web RESTful

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoREST/)

_HTTP oferece suporte ao uso de vários mecanismos de autenticação para controlar o acesso aos recursos. Autenticação básica oferece acesso a recursos somente a clientes que possuem as credenciais corretas. Este artigo demonstra como usar a autenticação básica para proteger o acesso aos recursos do serviço web RESTful._

O aplicativo de exemplo que acompanha este artigo do xamarin. Forms consome um serviço hospedado de Xamarin REST que fornece acesso somente leitura para o serviço web. Portanto, as operações que criar, atualizar e excluam dados não alterará os dados consumidos no aplicativo. No entanto, uma versão hospedável do serviço REST está disponível na *TodoRESTService* pasta no aplicativo de exemplo e instruções sobre como configurar o serviço pode ser encontrada lá. Esta versão hospedável do serviço REST fornece inteira criar, atualizar, ler e excluir o acesso aos dados.

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
> Autenticação básica deve ser usada apenas ao longo de uma conexão HTTPS. Quando usado em uma conexão HTTP, o <code>Authorization</code> cabeçalho pode ser decodificado com facilidade se o tráfego HTTP é capturado por um invasor.

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
  HttpClient client;
  ...

  public RestService ()
  {
    var authData = string.Format ("{0}:{1}", Constants.Username, Constants.Password);
    var authHeaderValue = Convert.ToBase64String (Encoding.UTF8.GetBytes (authData));

    client = new HttpClient ();
    ...
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue ("Basic", authHeaderValue);
  }
  ...
}
```

Em seguida, quando é feita uma solicitação para uma operação de serviço da web a solicitação é assinada com o `Authorization` cabeçalho, que indica se o usuário tem permissão para invocar a operação.

> [!NOTE]
> Enquanto o serviço REST de exemplo armazena credenciais como constantes, eles não devem ser armazenados em um formato inseguro em um aplicativo publicado. O [Xamarith.Auth](https://www.nuget.org/packages/Xamarin.Auth/) NuGet fornece funcionalidade para armazenar com segurança as credenciais. Para obter mais informações, consulte [armazenando e recuperando informações da conta em dispositivos](~/xamarin-forms/data-cloud/authentication/oauth.md).


## <a name="processing-the-authorization-header-server-side"></a>Processamento do lado do servidor de cabeçalho de autorização

O serviço REST de exemplo que acompanha este artigo decora cada ação com o `[BasicAuthentication]` atributo. Esse atributo é implementado pelos `BasicAuthenticationAttribute` classe na solução e é usado para analisar o `Authorization` cabeçalho e determinar se as credenciais de codificada em base64 são válidas, comparando-os com base nos valores armazenados em *Web. config*. Embora essa abordagem é adequada para o serviço de exemplo, ele requer a extensão para um serviço da web voltados ao público.

O módulo de autenticação básica usada pelo IIS, os usuários são autenticados em relação às suas credenciais do Windows. Portanto, os usuários devem ter contas de domínio do servidor. No entanto, o modelo de autenticação básica pode ser configurado para permitir a autenticação personalizada, em que as contas de usuário são autenticadas em relação a uma fonte externa, como um banco de dados. Para obter mais informações, consulte [autenticação básica na API Web ASP.NET](http://www.asp.net/web-api/overview/security/basic-authentication) no site do ASP.NET.

> [!NOTE]
> Autenticação básica não foi projetada para gerenciar o log de. Portanto, é a abordagem de autenticação básica padrão para fazer logoff encerrar a sessão.

## <a name="summary"></a>Resumo

Este artigo demonstrou como adicionar autenticação básica para solicitações da web feitas por um aplicativo xamarin. Forms usando o `HttpClient` classe. Autenticação básica oferece acesso a recursos somente a clientes que possuem as credenciais corretas. Para obter informações sobre como usar [AUTH](https://www.nuget.org/packages/Xamarin.Auth/) para gerenciar o processo de autenticação em um aplicativo xamarin. Forms para que os usuários possam compartilhar um back-end tendo somente acesso aos seus dados, consulte [autenticando usuários com um provedor de identidade](~/xamarin-forms/data-cloud/authentication/oauth.md).


## <a name="related-links"></a>Links relacionados

- [TodoREST (amostra)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoREST/)
- [Consumir um serviço web RESTful](~/xamarin-forms/data-cloud/consuming/rest.md)
- [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx)
