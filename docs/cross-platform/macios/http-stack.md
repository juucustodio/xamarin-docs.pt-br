---
title: "Pilha de HttpClient e seletor de implementação de SSL/TLS para iOS/macOS"
description: "A pilha de HttpClient e seletor de implementação de SSL/TLS determina a implementação de HttpClient e SSL/TLS que será usada pelo aplicativo Xamarin iOS, tvOS ou macOS."
ms.topic: article
ms.prod: xamarin
ms.assetid: 12101297-BB04-4410-85F0-A0D41B7E6591
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 06/12/2017
ms.openlocfilehash: d4fce635b26b0c367e836e4ec41d4f51a10b9c35
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="httpclient-stack-selector"></a>Seletor de pilha HttpClient

Disponível para xamarin, Xamarin.tvOS e Xamarin.Mac: Isso controla quais `HttpClient` implementação para usar. O padrão continua sendo um HttpClient que é alimentado por `HttpWebRequest`, enquanto você agora pode, opcionalmente, alterne para uma implementação que usa o iOS, tvOS ou macOS transportes nativo (`NSUrlSession` ou `CFNetwork` dependendo do sistema operacional). A vantagem é binários menores e downloads mais rápidos, a desvantagem é que ele requer que o loop de eventos esteja em execução para operações assíncronas ser executado.

Projetos devem fazer referência a **System** assembly.

<a name="Selecting-a-HttpClient-Stack" />

## <a name="selecting-a-httpclient-stack"></a>Selecionando uma pilha HttpClient

Para ajustar o HttpClient que está sendo usado pelo seu aplicativo:

1. Clique duas vezes o **nome do projeto** no **Solution Explorer** para abrir as opções de projeto.
2. Alterne para o **criar** configurações para seu projeto (por exemplo, **iOS Build** para um aplicativo xamarin).
3. Do **HttpClient implementação** lista suspensa, selecione o HttpClient tipo como um dos seguintes: **gerenciado**, **CFNetwork** ou **NSUrlSession**.

[ ![Escolher HttpClient implementação gerenciado, CFNetwork ou NSUrlSession](http-stack-images/http-xs-sml.png)](http-stack-images/http-xs.png)

<a name="Managed" />

## <a name="managed-default"></a>Gerenciado (padrão)

O manipulador gerenciado é o manipulador de HttpClient totalmente gerenciado que é enviado com a versão anterior do Xamarin.

### <a name="pros"></a>Prós:

 - Ele tem o recurso mais compatível definida com o Microsoft .NET e versões mais antigas do Xamarin.

### <a name="cons"></a>Contras:

 - Ele não está totalmente integrado com os sistemas operacionais Apple e é limitado a TLS 1.0.
 - Ele geralmente muito mais lento em coisas como criptografia de APIs nativas.
 - Ele exige mais código gerenciado, criando assim um aplicativo maior distribuível.

<a name="CFNetwork" />

## <a name="cfnetwork"></a>CFNetwork

O manipulador de CFNetwork baseia nativo `CFNetwork` framework disponível no iOS 6 e mais recente.

### <a name="pros"></a>Prós:

 - Ele usa as APIs nativas para melhor desempenho e o menor tamanho executável.
 - Oferece suporte para os padrões mais recentes, como TLS 1.2.

### <a name="cons"></a>Contras:

 - Requer iOS 6 ou posterior.
 - Não disponível em watchOS.
 - Alguns recursos de HttpClient/opções não estão disponíveis.

<a name="NSUrlSession" />

## <a name="nsurlsession"></a>NSUrlSession

O manipulador de NSURLSession baseia nativo `NSURLSession` framework disponível no iOS 7 e versões mais recente.

### <a name="pros"></a>Prós:

 - Ele usa as APIs nativas para melhor desempenho e o menor tamanho executável.
 - Oferece suporte para os padrões mais recentes, como TLS 1.2.

### <a name="cons"></a>Contras:

 - Requer iOS 7 ou posterior.
 - Alguns recursos de HttpClient/opções não estão disponíveis.


## <a name="programmatically-setting-the-httpmessagehandler"></a>Configurando programaticamente o HttpMessageHandler

Além da configuração de todo o projeto mostrada acima, você também pode instanciar uma `HttpClient` e injetar desejado `HttpMessageHandler` por meio do construtor, conforme demonstrado nestes trechos de código:

```csharp
// This will use the default message handler for the application; as
// set in the Project Options for the project.
HttpClient client = new HttpClient();

// This will create an HttpClient that explicitly uses the CFNetworkHandler
HttpClient client = new HttpClient(new CFNetworkHandler());

// This will create an HttpClient that explicitly uses NSUrlSessionHandler
HttpClient client = new HttpClient(new NSUrlSessionHandler());
```

Isso torna possível usar outra `HttpMessageHandler` de declarado no **opções de projeto** caixa de diálogo.

<a name="New-SSL-TLS-implementation-build-option" />
<a name="Selecting-a-SSL-TLS-implementation" />
<a name="Apple-TLS" />

# <a name="ssltls-implementation-build"></a>Compilação de implementação de SSL/TLS

SSL (Secure Socket Layer) e seu sucessor, o TLS (Transport Layer Security), oferecem suporte para HTTP e outras conexões de rede por meio de `System.Net.Security.SslStream`. Xamarin, Xamarin.tvOS ou do Xamarin.Mac `System.Net.Security.SslStream` implementação chamará a implementação de SSL/TLS nativa da Apple em vez de usar a implementação gerenciada fornecida pelo Mono. Implementação nativa da Apple oferece suporte a TLS 1.2.

<a name="Mono" />
> [!WARNING]
> O **Mono/gerenciado** provedor TLS é limitada a v3 SSL e TLS v1. Este provedor TLS foi preterido e não está mais disponível para aplicativos xamarin. 

<a name="App-Transport-Security" />

# <a name="app-transport-security"></a>Segurança de transporte do aplicativo

Da Apple _segurança de transporte do aplicativo_ (ATS) impõe conexões seguras entre os recursos de internet (como o servidor de back-end do aplicativo) e seu aplicativo. ATS garante que todas as comunicações de internet esteja em conformidade para proteger a conexão práticas recomendadas, impedindo assim a divulgação acidental de informações confidenciais diretamente por meio de seu aplicativo ou uma biblioteca que está consumindo.

Como ATS é habilitada por padrão em aplicativos criados para o iOS 9, tvOS 9 e OS X 10.11 (El Capitan) e mais recente, todas as conexões usando `NSUrlConnection`, `CFUrl` ou `NSUrlSession` estarão sujeitos às necessidades de segurança ATS. Se as conexões não atender a esses requisitos, ele falhará com uma exceção.

Com base em suas seleções de pilha HttpClient e implementação de SSL/TLS, talvez seja necessário fazer modificações ao seu aplicativo para funcionar corretamente com ATS.

Para obter mais informações sobre ATS, consulte nosso [guia de segurança de transporte do aplicativo](~/ios/app-fundamentals/ats.md).

## <a name="known-issues"></a>Problemas Conhecidos

Esta seção aborda problemas conhecidos com o suporte a TLS no xamarin.

### <a name="project-failed-to-load-with-error-requested-value-appletls-wasnt-found"></a>Falha ao carregar com o erro "Valor solicitado não foi possível encontrar AppleTLS" projeto

Xamarin 9,8 introduziu algumas novas configurações que continha o **. csproj** arquivo para um aplicativo xamarin. Essas alterações podem causar problemas quando o projeto é aberto com versões anteriores do xamarin. Captura de tela a seguir está um exemplo da mensagem de erro que pode ser exibida neste cenário:

![Captura de tela de erro ao tentar carregar o projeto, solicitado herdado do valor não encontrado](http-stack-images/tlserror-xs.png)

Este erro é causado pela introdução do `MtouchTlsProvider` configuração para o arquivo de projeto 9,8 xamarin. Se não for possível atualizar para xamarin 9,8 (ou superior), o contorno é editar manualmente o **. csproj** arquivo de aplicativo, remova o `MtouchTlsprovider` elemento e, em seguida, salve o arquivo de projeto foi modificado.

O trecho a seguir é um exemplo do que o `MtouchTlsProvider` configuração ficará como dentro de um **. csproj** arquivo:

```xml
<MtouchTlsProvider>Default</MtouchTlsProvider>
```
s


## <a name="related-links"></a>Links relacionados

- [Transport Layer Security (TLS)](~/cross-platform/app-fundamentals/transport-layer-security.md)
- [Segurança de transporte do aplicativo](~/ios/app-fundamentals/ats.md)
