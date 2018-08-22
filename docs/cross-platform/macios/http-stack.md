---
title: HttpClient e seletor de implementação SSL/TLS para iOS/macOS
description: A pilha de HttpClient e SSL/TLS seletor de implementação determina a implementação de HttpClient e SSL/TLS que será usada pelo seu aplicativo do Xamarin iOS, tvOS ou macOS.
ms.prod: xamarin
ms.assetid: 12101297-BB04-4410-85F0-A0D41B7E6591
author: asb3993
ms.author: amburns
ms.date: 04/20/2018
ms.openlocfilehash: fd48c7148aadd8d156544113e2d719295294bf40
ms.sourcegitcommit: 47709db4d115d221e97f18bc8111c95723f6cb9b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/13/2018
ms.locfileid: "40251217"
---
# <a name="httpclient-and-ssltls-implementation-selector-for-iosmacos"></a>Seletor de implementação de HttpClient e SSL/TLS para iOS/macOS

O **seletor de implementação de HttpClient** para xamarin. IOS, tvos e xamarin. Mac controla qual `HttpClient` implementação a ser usada. Você pode alternar para uma implementação que usa transportes native iOS, tvOS ou macOS (`NSUrlSession` ou `CFNetwork`, dependendo do sistema operacional). A vantagem é binários do TLS 1.2 suporte, menores e mais rápido baixa; a desvantagem é que ele requer que o loop de eventos esteja em execução para operações assíncronas a ser executado.

Projetos devem fazer referência a **HTTP** assembly.

> [!WARNING]
> **Abril de 2018** – devido a aumentar a segurança requisitos, incluindo a conformidade de PCI, principais provedores de nuvem e servidores web devem parar de dar suporte a TLS versões anteriores a 1.2.  Projetos do Xamarin criados em versões anteriores do padrão do Visual Studio para usar versões mais antigas do TLS.
>
> Para garantir que seus aplicativos continuem a trabalhar com esses servidores e serviços, **você deve atualizar seus projetos do Xamarin com o `NSUrlSession` definir conforme mostrado abaixo, em seguida, recriar e reimplantar seus aplicativos** para seus usuários.

### <a name="selecting-an-httpclient-stack"></a>Selecionar uma pilha de HttpClient

Para ajustar o `HttpClient` que está sendo usada por seu aplicativo:

1. Clique duas vezes o **nome do projeto** na **Gerenciador de soluções** para abrir as opções de projeto.
2. Alternar para o **construir** as configurações para seu projeto (por exemplo, **Build do iOS** para um aplicativo xamarin. IOS).
3. Dos **implementação de HttpClient** lista suspensa, selecione o `HttpClient` digite como um dos seguintes: **NSUrlSession** (recomendado), **CFNetwork**, ou  **Gerenciado**.

[![Escolher a implementação de HttpClient gerenciados, CFNetwork ou NSUrlSession](http-stack-images/http-xs-sml.png)](http-stack-images/http-xs.png#lightbox)

> [!TIP]
> Para suporte a TLS 1.2 o `NSUrlSession` opção é recomendada.

### <a name="nsurlsession"></a>NSUrlSession

O `NSURLSession`-manipulador com base se baseia no nativo `NSURLSession` framework disponível no iOS 7 e versões mais recente. 
**Essa é a configuração recomendada.**

#### <a name="pros"></a>Profissionais

- Ele usa APIs nativas para melhorar o desempenho e o menor tamanho do executável.
- Suporte para os padrões mais recentes, como TLS 1.2.

#### <a name="cons"></a>Contras

- Requer iOS 7 ou posterior.
- Alguns `HttpClient` recursos/opções não estão disponíveis.

### <a name="cfnetwork"></a>CFNetwork

O `CFNetwork`-manipulador com base se baseia no nativo `CFNetwork` framework disponível no iOS 6 e versões mais recente.

#### <a name="pros"></a>Profissionais

- Ele usa APIs nativas para melhorar o desempenho e o menor tamanho do executável.
- Suporte a padrões mais recentes, como TLS 1.2.

#### <a name="cons"></a>Contras

- Requer iOS 6 ou posterior.
- Não disponível no watchOS.
- Alguns recursos do HttpClient/opções não estão disponíveis.

### <a name="managed"></a>Gerenciado

O manipulador gerenciado é o manipulador de HttpClient totalmente gerenciado que foi enviado com a versão anterior do Xamarin.

#### <a name="pros"></a>Profissionais

- Ele tem o recurso mais compatível com Microsoft .NET e versões mais antigas do Xamarin.

#### <a name="cons"></a>Contras

- Ele não está totalmente integrado com os sistemas operacionais Apple e é limitado a TLS 1.0. Pode não ser capaz de se conectar para proteger servidores web ou serviços de nuvem no futuro.
- Ele normalmente muito mais lenta em coisas como a criptografia que as APIs nativas.
- Ele requer mais código gerenciado, criando assim um aplicativo maior distribuível.

### <a name="programmatically-setting-the-httpmessagehandler"></a>Configurando programaticamente o HttpMessageHandler

Além da configuração de todo o projeto mostrada acima, você também pode instanciar uma `HttpClient` e inserir os detalhes desejados `HttpMessageHandler` por meio do construtor, conforme demonstrado nesses trechos de código:

```csharp
// This will use the default message handler for the application; as
// set in the Project Options for the project.
HttpClient client = new HttpClient();

// This will create an HttpClient that explicitly uses the CFNetworkHandler
HttpClient client = new HttpClient(new CFNetworkHandler());

// This will create an HttpClient that explicitly uses NSUrlSessionHandler
HttpClient client = new HttpClient(new NSUrlSessionHandler());
```

Isso torna possível usar outro `HttpMessageHandler` do que está declarado na **opções de projeto** caixa de diálogo.

## <a name="ssltls-implementation"></a>Implementação SSL/TLS

SSL (Secure Socket Layer) e seu sucessor, TLS (Transport Layer Security) fornecem suporte para HTTP e outras conexões de rede por meio de `System.Net.Security.SslStream`. Xamarin. IOS, tvos ou do xamarin. Mac `System.Net.Security.SslStream` implementação chamará a implementação de SSL/TLS nativa da Apple em vez de usar a implementação gerenciada fornecida pelo Mono. Implementação de nativo da Apple dá suporte a TLS 1.2.

> [!WARNING]
> A próxima versão do xamarin. Mac 4.8 dará suporte somente a macOS 10.9 ou posterior.
> As versões anteriores do xamarin. Mac suporte macOS 10.7 ou superior, mas essas versões mais antigas do macOS não possuem infra-estrutura TLS suficiente para dar suporte ao TLS 1.2. Para macOS 10.7 ou macOS 10.8 de destino, use o xamarin. Mac 4.6 ou anterior.

## <a name="app-transport-security"></a>Segurança de transporte de aplicativo

Da Apple _segurança de transporte de aplicativo_ (ATS) impõe conexões seguras entre recursos da internet (como o servidor de back-end do aplicativo) e seu aplicativo. ATS garante que todas as comunicações de internet está em conformidade para proteger a conexão as práticas recomendadas, impedindo assim a divulgação acidental de informações confidenciais diretamente por meio de seu aplicativo ou uma biblioteca que ele está consumindo.

Desde que o ATS é habilitado por padrão nos aplicativos criados para o iOS 9, tvOS 9 e OS X 10.11 (El Capitan) e mais recente, todas as conexões usando `NSUrlConnection`, `CFUrl` ou `NSUrlSession` estará sujeito aos requisitos de segurança ATS. Se suas conexões não atender a esses requisitos, ele falhará com uma exceção.

Com base nas suas seleções de pilha de HttpClient e implementação SSL/TLS, você talvez precise fazer modificações ao seu aplicativo para funcionar corretamente com o ATS.

Para obter mais informações sobre o ATS, consulte nosso [guia de segurança de transporte de aplicativo](~/ios/app-fundamentals/ats.md).

## <a name="known-issues"></a>Problemas conhecidos

Esta seção abordará os problemas conhecidos com o suporte do TLS no xamarin. IOS.

### <a name="project-failed-to-load-with-error-requested-value-appletls-wasnt-found"></a>Falha ao carregar com o erro "Valor solicitado AppleTLS não foi encontrado" projeto

Xamarin. IOS 9.8 introduziu algumas novas configurações que continha os **. csproj** arquivo para um aplicativo xamarin. IOS. Essas alterações podem causar problemas quando o projeto é aberto com versões mais antigas do xamarin. IOS. Captura de tela a seguir está um exemplo da mensagem de erro que pode ser exibida neste cenário:

![Captura de tela de erro ao tentar carregar o projeto, solicitado herdado do valor não encontrado](http-stack-images/tlserror-xs.png)

Esse erro é causado pela introdução a `MtouchTlsProvider` definir como arquivo de projeto no xamarin. IOS 9.8. Se não for possível atualizar para o xamarin. IOS 9.8 (ou superior), a solução alternativa é editar manualmente o **. csproj** arquivos de aplicativo, remova o `MtouchTlsprovider` elemento e, em seguida, salve o arquivo de projeto foi modificado.

O trecho a seguir está um exemplo do que o `MtouchTlsProvider` configuração pode se parecer como dentro de um **. csproj** arquivo:

```xml
<MtouchTlsProvider>Default</MtouchTlsProvider>
```

## <a name="related-links"></a>Links relacionados

- [TLS (Transport Layer Security)](~/cross-platform/app-fundamentals/transport-layer-security.md)
- [Segurança de transporte de aplicativo](~/ios/app-fundamentals/ats.md)
