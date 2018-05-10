---
title: Pilha de HttpClient e seletor de implementação de SSL/TLS para iOS/macOS
description: A pilha de HttpClient e seletor de implementação de SSL/TLS determina a implementação de HttpClient e SSL/TLS que será usada pelo aplicativo Xamarin iOS, tvOS ou macOS.
ms.prod: xamarin
ms.assetid: 12101297-BB04-4410-85F0-A0D41B7E6591
author: asb3993
ms.author: amburns
ms.date: 04/20/2018
ms.openlocfilehash: dcbdb4d20bca9764731b08e551a4d3b8a26a2ab4
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/09/2018
---
# <a name="httpclient-stack-and-ssltls-implementation-selector-for-iosmacos"></a>Pilha de HttpClient e seletor de implementação de SSL/TLS para iOS/macOS

O **HttpClient implementação seletor** para xamarin, Xamarin.tvOS e Xamarin.Mac controla quais `HttpClient` implementação para usar. Você pode alternar para uma implementação que usa o iOS, tvOS ou macOS transportes nativo (`NSUrlSession` ou `CFNetwork`, dependendo do sistema operacional). A vantagem é binários TLS 1.2 suporte, menores e downloads mais rápidos; a desvantagem é que ele requer que o loop de eventos esteja em execução para operações assíncronas ser executado.

Projetos devem fazer referência a **System** assembly.

> [!WARNING]
> **Abril de 2018** – devido a aumentar a segurança requisitos, incluindo conformidade PCI, principais provedores de nuvem e servidores web são esperados para interromper a dar suporte a TLS versões anteriores a 1.2.  Xamarin os projetos criados em versões anteriores do padrão do Visual Studio para usar versões mais antigas do TLS.
>
> Para garantir que seus aplicativos continuam a trabalhar com esses servidores e serviços, **devem atualizar seus projetos do Xamarin com o `NSUrlSession` configuração conforme mostrado abaixo, em seguida, recriar e reimplantar seus aplicativos** para seus usuários.

<a name="Selecting-a-HttpClient-Stack" />

### <a name="selecting-a-httpclient-stack"></a>Selecionando uma pilha HttpClient

Para ajustar o `HttpClient` que está sendo usado pelo seu aplicativo:

1. Clique duas vezes o **nome do projeto** no **Solution Explorer** para abrir as opções de projeto.
2. Alterne para o **criar** configurações para seu projeto (por exemplo, **iOS Build** para um aplicativo xamarin).
3. Do **HttpClient implementação** lista suspensa, selecione o `HttpClient` tipo como um dos seguintes: **NSUrlSession** (recomendado), **CFNetwork**, ou  **Gerenciado**.

[![Escolher HttpClient implementação gerenciado, CFNetwork ou NSUrlSession](http-stack-images/http-xs-sml.png)](http-stack-images/http-xs.png#lightbox)

> [!TIP]
> Para suporte a TLS 1.2 o `NSUrlSession` opção é recomendada.

<a name="NSUrlSession" />

### <a name="nsurlsession"></a>NSUrlSession

O `NSURLSession`-com base manipulador baseia nativo `NSURLSession` framework disponível no iOS 7 e versões mais recente. 
**Essa é a configuração recomendada.**

#### <a name="pros"></a>Profissionais

- Ele usa as APIs nativas para melhor desempenho e o menor tamanho executável.
- Suporte para os padrões mais recentes, como TLS 1.2.

#### <a name="cons"></a>Contras

- Requer iOS 7 ou posterior.
- Alguns `HttpClient` recursos/opções não estão disponíveis.

<a name="CFNetwork" />

### <a name="cfnetwork"></a>CFNetwork

O `CFNetwork`-com base manipulador baseia nativo `CFNetwork` framework disponível no iOS 6 e mais recente.

#### <a name="pros"></a>Profissionais

- Ele usa as APIs nativas para melhor desempenho e o menor tamanho executável.
- Suporte para os padrões mais recentes, como TLS 1.2.

#### <a name="cons"></a>Contras

- Requer iOS 6 ou posterior.
- Não disponível em watchOS.
- Alguns recursos de HttpClient/opções não estão disponíveis.

<a name="Managed" />

### <a name="managed"></a>Gerenciado

O manipulador gerenciado é o manipulador de HttpClient totalmente gerenciado que é enviado com a versão anterior do Xamarin.

#### <a name="pros"></a>Profissionais

- Ele tem o recurso mais compatível definida com o Microsoft .NET e versões mais antigas do Xamarin.

#### <a name="cons"></a>Contras

- Ele não está totalmente integrado com os sistemas operacionais Apple e é limitado a TLS 1.0. Pode não ser capaz de se conectar para proteger os servidores web ou serviços de nuvem no futuro.
- Ele geralmente muito mais lento em coisas como criptografia de APIs nativas.
- Ele exige mais código gerenciado, criando assim um aplicativo maior distribuível.

### <a name="programmatically-setting-the-httpmessagehandler"></a>Configurando programaticamente o HttpMessageHandler

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

## <a name="ssltls-implementation"></a>Implementação de SSL/TLS

SSL (Secure Socket Layer) e seu sucessor, o TLS (Transport Layer Security), oferecem suporte para HTTP e outras conexões de rede por meio de `System.Net.Security.SslStream`. Xamarin, Xamarin.tvOS ou do Xamarin.Mac `System.Net.Security.SslStream` implementação chamará a implementação de SSL/TLS nativa da Apple em vez de usar a implementação gerenciada fornecida pelo Mono. Implementação nativa da Apple oferece suporte a TLS 1.2.

<a name="App-Transport-Security" />

## <a name="app-transport-security"></a>Segurança de transporte do aplicativo

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

## <a name="related-links"></a>Links relacionados

- [TLS (Transport Layer Security)](~/cross-platform/app-fundamentals/transport-layer-security.md)
- [Segurança de transporte de aplicativo](~/ios/app-fundamentals/ats.md)
