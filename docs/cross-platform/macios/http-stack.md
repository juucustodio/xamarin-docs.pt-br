---
title: Seletor de implementação de HttpClient e SSL/TLS para iOS/macOS
description: O seletor de implementação de HttpClient Stack e SSL/TLS determina a implementação de HttpClient e SSL/TLS que será usada pelo seu aplicativo Xamarin iOS, tvOS ou macOS.
ms.prod: xamarin
ms.assetid: 12101297-BB04-4410-85F0-A0D41B7E6591
author: conceptdev
ms.author: crdun
ms.date: 04/20/2018
ms.openlocfilehash: f3c30e8edc36c6d92b6fac0bd0e199aa26e16993
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70280921"
---
# <a name="httpclient-and-ssltls-implementation-selector-for-iosmacos"></a>Seletor de implementação de HttpClient e SSL/TLS para iOS/macOS

O **seletor de implementação HttpClient** para xamarin. Ios, xamarin. tvOS e xamarin. Mac controla `HttpClient` a implementação a ser usada. Você pode alternar para uma implementação que usa Transportações nativas Ios, tvOS ou MacOS (`NSUrlSession` ou `CFNetwork`, dependendo do sistema operacional). A vantagem é o TLS 1,2-suporte, binários menores e downloads mais rápidos; a desvantagem é que requer que o loop de eventos esteja em execução para que as operações assíncronas sejam executadas.

Os projetos devem referenciar o assembly do **System .net. http** .

> [!WARNING]
> **Abril de 2018** – devido a requisitos de segurança maiores, incluindo a conformidade com PCI, os principais provedores de nuvem e servidores Web devem parar de dar suporte a versões de TLS anteriores a 1,2. Os projetos do Xamarin criados em versões anteriores do Visual Studio usam o padrão para usar versões mais antigas do TLS.
>
> Para garantir que seus aplicativos continuem a trabalhar com esses servidores e serviços, **você deve atualizar seus projetos do Xamarin com `NSUrlSession` a configuração mostrada abaixo e, em seguida, recompilar e reimplantar seus aplicativos** para os usuários.

### <a name="selecting-an-httpclient-stack"></a>Selecionando uma pilha HttpClient

Para ajustar o `HttpClient` que está sendo usado pelo seu aplicativo:

1. Clique duas vezes no **nome do projeto** no **Gerenciador de soluções** para abrir as opções do projeto.
2. Alterne para as configurações de **compilação** do seu projeto (por exemplo, **Build do IOS** para um aplicativo Xamarin. Ios).
3. Na lista suspensa **implementação de HttpClient** , selecione `HttpClient` o tipo como um dos seguintes: **NSUrlSession** (recomendado), **CFNetwork**ou **gerenciado**.

[![Escolha a implementação de HttpClient de Managed, CFNetwork ou NSUrlSession](http-stack-images/http-xs-sml.png)](http-stack-images/http-xs.png#lightbox)

> [!TIP]
> Para suporte a TLS 1,2 `NSUrlSession` , a opção é recomendada.

### <a name="nsurlsession"></a>NSUrlSession

O `NSURLSession`manipulador baseado é baseado na estrutura nativa `NSURLSession` disponível no Ios 7 e mais recente. 
**Essa é a configuração recomendada.**

#### <a name="pros"></a>Prós

- Ele usa APIs nativas para melhorar o desempenho e o tamanho do executável menor.
- Suporte para os padrões mais recentes, como o TLS 1,2.

#### <a name="cons"></a>Contras

- Requer o iOS 7 ou posterior.
- Alguns `HttpClient` recursos/opções não estão disponíveis.

### <a name="cfnetwork"></a>CFNetwork

O `CFNetwork`manipulador baseado é baseado na estrutura nativa `CFNetwork` disponível no Ios 6 e mais recente.

#### <a name="pros"></a>Prós

- Ele usa APIs nativas para melhorar o desempenho e o tamanho do executável menor.
- Suporte para padrões mais recentes, como o TLS 1,2.

#### <a name="cons"></a>Contras

- Requer o iOS 6 ou posterior.
- Não disponível em watchOS.
- Alguns recursos/opções do HttpClient não estão disponíveis.

### <a name="managed"></a>Gerenciado

O manipulador gerenciado é o manipulador HttpClient totalmente gerenciado que foi enviado com a versão anterior do Xamarin.

#### <a name="pros"></a>Prós

- Ele tem o conjunto de recursos mais compatível com Microsoft .NET e versões anteriores do Xamarin.

#### <a name="cons"></a>Contras

- Ele não está totalmente integrado com os SOS da Apple e está limitado ao TLS 1,0. Talvez não seja possível se conectar a servidores Web ou serviços de nuvem seguros no futuro.
- Normalmente, isso é muito mais lento em coisas como criptografia do que as APIs nativas.
- Ele requer mais código gerenciado, criando assim um distribuído de aplicativo maior.

### <a name="programmatically-setting-the-httpmessagehandler"></a>Definindo programaticamente o HttpMessageHandler

Além da configuração de todo o projeto mostrada acima, você também pode instanciar `HttpClient` um e injetar `HttpMessageHandler` o desejado por meio do Construtor, conforme demonstrado nestes trechos de código:

```csharp
// This will use the default message handler for the application; as
// set in the Project Options for the project.
HttpClient client = new HttpClient();

// This will create an HttpClient that explicitly uses the CFNetworkHandler
HttpClient client = new HttpClient(new CFNetworkHandler());

// This will create an HttpClient that explicitly uses NSUrlSessionHandler
HttpClient client = new HttpClient(new NSUrlSessionHandler());
```

Isso torna possível usar um diferente `HttpMessageHandler` do que é declarado na caixa de diálogo **Opções do projeto** .

## <a name="ssltls-implementation"></a>Implementação de SSL/TLS

SSL (camada de soquete seguro) e seu sucessor, TLS (segurança de camada de transporte), fornecer suporte para HTTP e `System.Net.Security.SslStream`outras conexões de rede via. A implementação do `System.Net.Security.SslStream` xamarin. Ios, xamarin. tvOS ou xamarin. Mac irá chamar a implementação de SSL/TLS nativa da Apple em vez de usar a implementação gerenciada fornecida pelo mono. A implementação nativa da Apple dá suporte a TLS 1,2.

> [!WARNING]
> A próxima versão do Xamarin.Mac 4.8 dará suporte somente a macOS 10.9 ou posterior.
> As versões anteriores do Xamarin.Mac eram compatíveis com macOS 10.7 ou superior, mas essas versões mais antigas do macOS não têm infraestrutura TLS suficiente para dar suporte ao TLS 1.2. Para macOS 10.7 ou macOS 10.8, use o Xamarin.Mac 4.6 ou anterior.

## <a name="app-transport-security"></a>Segurança do transporte de aplicativo

A ATS ( _segurança de transporte de aplicativo_ ) da Apple impõe conexões seguras entre os recursos da Internet (como o servidor back-end do aplicativo) e seu aplicativo. O ATS garante que todas as comunicações com a Internet estejam em conformidade com as práticas recomendadas de conexão, impedindo a divulgação acidental de informações confidenciais diretamente por meio de seu aplicativo ou de uma biblioteca que esteja consumindo.

Como o ATS está habilitado por padrão em aplicativos criados para IOS 9, tvOS 9 e os X 10,11 (El Capitan) e mais recentes, todas `NSUrlConnection`as `CFUrl` conexões `NSUrlSession` que usam ou estarão sujeitos a requisitos de segurança de ATS. Se suas conexões não atenderem a esses requisitos, elas falharão com uma exceção.

Com base em suas seleções de implementação do HttpClient Stack e SSL/TLS, talvez seja necessário fazer modificações em seu aplicativo para funcionar corretamente com o ATS.

Para saber mais sobre o ATS, consulte nosso [Guia de segurança do transporte de aplicativo](~/ios/app-fundamentals/ats.md).

## <a name="known-issues"></a>Problemas conhecidos

Esta seção abordará problemas conhecidos com o suporte a TLS no Xamarin. iOS.

### <a name="project-failed-to-load-with-error-requested-value-appletls-wasnt-found"></a>Falha ao carregar o projeto com o erro "o valor solicitado AppleTLS não foi encontrado"

O Xamarin. iOS 9,8 introduziu algumas novas configurações que continham o arquivo **. csproj** para um aplicativo Xamarin. Ios. Essas alterações podem causar problemas quando o projeto é aberto com versões mais antigas do Xamarin. iOS. A captura de tela a seguir é um exemplo da mensagem de erro que pode ser exibida neste cenário:

![Captura de tela de erro ao tentar carregar o projeto, valor solicitado herdado não encontrado](http-stack-images/tlserror-xs.png)

Esse erro é causado pela introdução da `MtouchTlsProvider` configuração ao arquivo de projeto no Xamarin. Ios 9,8. Se não for possível atualizar para o Xamarin. Ios 9,8 (ou superior), a solução alternativa é editar manualmente o aplicativo de arquivo **. csproj** , remover o `MtouchTlsprovider` elemento e, em seguida, salvar o arquivo de projeto alterado.

O trecho a seguir é um exemplo de como `MtouchTlsProvider` a configuração pode parecer dentro de um arquivo **. csproj** :

```xml
<MtouchTlsProvider>Default</MtouchTlsProvider>
```

## <a name="related-links"></a>Links relacionados

- [TLS (Transport Layer Security)](~/cross-platform/app-fundamentals/transport-layer-security.md)
- [Segurança de transporte de aplicativo](~/ios/app-fundamentals/ats.md)
