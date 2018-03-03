---
title: "Segurança de transporte do aplicativo"
description: "Segurança de transporte do aplicativo (ATS) impõe conexões seguras entre os recursos de internet (como o servidor de back-end do aplicativo) e seu aplicativo."
ms.topic: article
ms.prod: xamarin
ms.assetid: 0E2217F1-FC96-4D0A-ABAB-D40AD8F96502
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/13/2017
ms.openlocfilehash: 60858e05e222725f05eb67bd7aaa4e56d2ff3880
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="app-transport-security"></a>Segurança de transporte do aplicativo

_Segurança de transporte do aplicativo (ATS) impõe conexões seguras entre os recursos de internet (como o servidor de back-end do aplicativo) e seu aplicativo._

Este artigo apresenta as alterações de segurança que impõe a segurança de transporte do aplicativo em um aplicativo do iOS 9 e [para seus projetos de xamarin, isso significa](#xamarinsupport), ele aborda o [opções de configuração de ATS](#config) e Ele aborda como [recusar ATS](#optout) ATS se necessário. Como ATS está habilitado por padrão, todas as conexões da internet não segura gerará uma exceção em aplicativos do iOS 9 (a menos que você explicitamente permitido-lo).


## <a name="about-app-transport-security"></a>Sobre a segurança de transporte do aplicativo

Como mencionado acima, ATS garante que todas as comunicações de internet no iOS 9 e OS X El Capitan esteja em conformidade para proteger a conexão práticas recomendadas, impedindo assim a divulgação acidental de informações confidenciais diretamente por meio de seu aplicativo ou uma biblioteca que é consumindo.

Para aplicativos existentes, implemente o `HTTPS` protocolo sempre que possível. Para novos aplicativos xamarin, você deve usar `HTTPS` exclusivamente ao se comunicar com os recursos da internet. Além disso, a comunicação de API de alto nível deve ser criptografada usando TLS versão 1.2 com sigilo total.

Qualquer conexão feita com [NSUrlConnection](https://developer.xamarin.com/api/type/Foundation.NSUrlConnection/), [CFUrl](https://developer.xamarin.com/api/type/CoreFoundation.CFUrl/) ou [NSUrlSession](https://developer.xamarin.com/api/type/Foundation.NSUrlSession/) usará ATS por padrão em aplicativos criados para o iOS 9 e OS X 10.11 (El Capitan).

## <a name="default-ats-behavior"></a>Comportamento ATS padrão

Como ATS é habilitada por padrão em aplicativos criados para o iOS 9 e OS X 10.11 (El Capitan), todas as conexões usando [NSUrlConnection](https://developer.xamarin.com/api/type/Foundation.NSUrlConnection/), [CFUrl](https://developer.xamarin.com/api/type/CoreFoundation.CFUrl/) ou [NSUrlSession](https://developer.xamarin.com/api/type/Foundation.NSUrlSession/) estará sujeito Requisitos de segurança ATS. Se as conexões não atender a esses requisitos, ele falhará com uma exceção.

### <a name="ats-connection-requirements"></a>Requisitos de Conexão ATS

ATS aplicará os seguintes requisitos para todas as conexões de internet:

- Todas as criptografias de conexão devem usar sigilo total. Consulte a lista de codificações aceitas abaixo.
- O protocolo de segurança de camada de transporte (TLS) deve ser a versão 1.2 ou superior.
- Pelo menos uma impressão digital de SHA256 com um 2048 bits ou maior chave RSA, ou uma 256 bits ou maior chave de curva Elliptic (ECC) deve ser usada para todos os certificados.

Novamente, como ATS está habilitado por padrão no iOS 9, qualquer tentativa de fazer uma conexão que não atenda a esses requisitos resultará em uma exceção sendo lançada. 

<a name="ATS-Compatible-Ciphers" />

### <a name="ats-compatible-ciphers"></a>Codificações compatíveis ATS

O seguinte tipo de codificação sigilo são aceitos pelo ATS internet comunicações seguras:

- `TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384`
- `TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256`
- `TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384`
- `TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA`
- `TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256`
- `TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA`
- `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
- `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`
- `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`
- `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
- `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA`

Para obter mais informações sobre como trabalhar com classes de comunicação da internet iOS, consulte da Apple [referência de classe NSURLConnection](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/Foundation/Classes/NSURLConnection_Class/index.html#//apple_ref/doc/uid/TP40003755), [CFURL referência](https://developer.apple.com/library/prerelease/ios/documentation/CoreFoundation/Reference/CFURLRef/index.html#//apple_ref/doc/uid/20001206) ou [NSURLSession Referência de classe ](https://developer.apple.com/library/prerelease/ios/documentation/Foundation/Reference/NSURLSession_class/index.html#//apple_ref/doc/uid/TP40013435).

<a name="xamarinsupport" />

## <a name="supporting-ats-in-xamarinios"></a>Dando suporte ATS em xamarin

Como ATS está habilitado por padrão no iOS 9 e OS X El Capitan, se seu aplicativo xamarin ou qualquer serviço que está usando ou biblioteca faz conexão com a internet, você precisará executar alguma ação ou as conexões resultará em uma exceção sendo lançada.

Para um aplicativo existente, Apple sugere que você oferece suporte a `HTTPS` protocolo assim que possível. Se um não é possível porque você está se conectando de um 3º participante serviço web que não dá suporte a `HTTPS` ou se estiver dando suporte `HTTPS` seria impraticável, você pode recusar ATS. Consulte o [Opting-Out de ATS](#Opting-Out-of-ATS) seção abaixo para obter mais detalhes.

Para um novo aplicativo xamarin, você deve usar `HTTPS` exclusivamente ao se comunicar com os recursos da internet. Novamente, pode haver situações (como usar um serviço de web 3ª parte) em que isso não é possível e você precisará sair do ATS.

Além disso, ATS impõe comunicação alto nível da API a ser criptografado usando TLS versão 1.2 com sigilo total. Consulte o [requisitos de Conexão ATS](#ATS-Connection-Requirements) e [codificações compatíveis ATS](#ATS-Compatible-Ciphers) seções acima para obter mais detalhes.

Enquanto você pode não estar familiarizado com o TLS ([Transport Layer Security](https://en.wikipedia.org/wiki/Transport_Layer_Security)) é o sucessor do SSL ([Secure Socket Layer](https://en.wikipedia.org/wiki/Transport_Layer_Security)) e fornece um conjunto de protocolos de criptografia para aplicar a segurança em conexões de rede.

O nível TLS é controlado pelo serviço da web que está consumindo e, portanto, está fora do controle do aplicativo. Tanto o `HttpClient` e `ModernHttpClient` devem usar automaticamente o nível mais alto de criptografia de TLS com suporte pelo servidor.

Dependendo do servidor que você estiver falando com (especialmente se ele é um serviço de terceiros 3º), talvez seja necessário desabilitar sigilo ou selecione um nível mais baixo de TLS. Consulte o [Configurando opções de ATS](#Configuring-ATS-Options) seção abaixo para obter mais detalhes.

> [!IMPORTANT]
> **Observação:** segurança de transporte do aplicativo não se aplica a aplicativos Xamarin usando **implementações HTTPClient gerenciado**. Ele se aplica a conexões usando CFNetwork **implementações HTTPClient** ou **implementações NSURLSession HTTPClient** somente.

### <a name="setting-the-httpclient-implementation"></a>Definindo a implementação de HTTPClient

Para definir a implementação de HTTPClient usada por um aplicativo iOS, clique duas vezes o **projeto** no **Solution Explorer** para abrir o **opções de projeto**. Navegue até **iOS compilação** e selecione o tipo de cliente desejada no **HttpClient implementação** suspensa:

![](ats-images/client01.png "Definir as opções de compilação do iOS")


#### <a name="managed-handler"></a>Manipulador gerenciado

O manipulador gerenciado é o manipulador de HttpClient totalmente gerenciado que é enviado com versões anteriores do xamarin e é o manipulador padrão.

Prós:

- Ele é mais compatível com o Microsoft .NET e a versão mais antiga do Xamarin.

Contras:

- Ele não está totalmente integrado com o iOS (por exemplo, ele é limitado a TLS 1.0).
- É geralmente muito mais lenta que as APIs nativas.
- Ele requer mais código gerenciado e cria aplicativos maiores.

#### <a name="cfnetwork-handler"></a>Manipulador de CFNetwork

O manipulador CFNetwork com base em se baseia nativo `CFNetwork` framework.

Prós:

- Usa a API nativa para melhor desempenho e o executável menores.
- Adiciona suporte para os padrões mais recentes, como TLS 1.2.

Contras:

- Requer iOS 6 ou posterior.
- Não disponível em watchOS.
- Alguns recursos de HttpClient e opções não estão disponíveis.

#### <a name="nsurlsession-handler"></a>NSUrlSession Handler

O manipulador NSUrlSession com base em se baseia nativo `NSUrlSession` API.

Prós:

- Usa a API nativa para melhor desempenho e o executável menores.
- Adiciona suporte para os padrões mais recentes, como TLS 1.2.

Contras:

- Requer iOS 7 ou posterior.
- Alguns recursos de HttpClient e opções não estão disponíveis. 

## <a name="diagnosing-ats-issues"></a>Diagnosticar problemas ATS

Ao tentar se conectar à internet, seja diretamente ou de uma exibição da web no iOS 9, você poderá receber um erro no formato:

> Segurança de transporte do aplicativo bloqueou uma carga de recursos HTTP (http://www.-the-blocked-domain.com) de texto não criptografado porque é não segura. Exceções temporárias podem ser configuradas por meio do arquivo de info. plist do aplicativo.

IOS9, segurança de transporte do aplicativo (ATS) estabelece conexões seguras entre os recursos de internet (como o servidor de back-end do aplicativo) e seu aplicativo. Além disso, ATS requer comunicação usando o `HTTPS` comunicação alto nível da API a ser criptografado usando TLS versão 1.2 com sigilo e protocolo.

Como ATS é habilitada por padrão em aplicativos criados para o iOS 9 e OS X 10.11 (El Capitan), todas as conexões usando `NSURLConnection`, `CFURL` ou `NSURLSession` estarão sujeitos às necessidades de segurança ATS. Se as conexões não atender a esses requisitos, ele falhará com uma exceção.

Apple também fornece o [aplicativo de exemplo TLSTool](https://developer.apple.com/library/mac/samplecode/sc1236/Introduction/Intro.html#//apple_ref/doc/uid/DTS40014927-Intro-DontLinkElementID_2) que pode ser compilado (ou, opcionalmente, transcodificação para Xamarin e c#) e usados para diagnosticar problemas ATS/TLS. Consulte o [Opting-Out de ATS](#Opting-Out_of_ATS) seção abaixo para obter informações sobre como resolver esse problema.


<a name="config" />

## <a name="configuring-ats-options"></a>Configurando opções de ATS

Você pode configurar vários recursos do ATS definindo valores de chaves específicas em seu aplicativo **Info. plist** arquivo. As chaves a seguir estão disponíveis para controlar ATS (_recuado para mostrar como eles são aninhados_):

```csharp
NSAppTransportSecurity
    NSAllowsArbitraryLoads
    NSAllowsArbitraryLoadsInWebContent
    NSExceptionDomains
    <domain-name-for-exception-as-string>
        NSExceptionMinimumTLSVersion
        NSExceptionRequiresForwardSecrecy
        NSExceptionAllowsInsecureHTTPLoads
        NSRequiresCertificateTransparency
        NSIncludesSubdomains
        NSThirdPartyExceptionMinimumTLSVersion
        NSThirdPartyExceptionRequiresForwardSecrecy
        NSThirdPartyExceptionAllowsInsecureHTTPLoads
```

Cada chave tem o tipo e o significado a seguir:

- **NSAppTransportSecurity** (`Dictionary`) - contém todas as chaves de configuração e valores para ATS.
- **NSAllowsArbitraryLoads** (`Boolean`) - se `YES` ATS será desabilitado para qualquer domínio **não** listados em `NSExceptionDomains`. Para domínios listados, as configurações de segurança especificadas serão usadas.
- **NSAllowsArbitraryLoadsInWebContent** (`Boolean`) - se `YES` permitirá que páginas da web para carregar corretamente enquanto a proteção de segurança de transporte da Apple (ATS) ainda está habilitada para o restante do aplicativo.
- **NSExceptionDomains** (`Dictionary`)-uma coleção de domínios que e as configurações de segurança que ATS deve usar para um determinado domínio.
- **< Domain-name-for-exception-as-string >** (`Dictionary`)-uma coleção de exceções para um determinado domínio (por exemplo. `www.xamarin.com`).
- **NSExceptionMinimumTLSVersion** (`String`)-a versão mínima do TLS como `TLSv1.0`, `TLSv1.1` ou `TLSv1.2` (que é o padrão).
- **NSExceptionRequiresForwardSecrecy** (`Boolean`) - se `NO` o domínio não precisa usar uma codificação com segurança direta. O valor padrão é `YES`.
- **NSExceptionAllowsInsecureHTTPLoads** (`Boolean`) - se `NO` (o padrão) em que todas as comunicações com este domínio devem ser o `HTTPS` protocolo.
- **NSRequiresCertificateTransparency** (`Boolean`) - se `YES` protocolo (SSL do domínio do) devem incluir dados de transparência válido. O valor padrão é `NO`.
- **NSIncludesSubdomains** (`Boolean`) - se `YES` essas configurações substituem todos os subdomínios deste domínio. O valor padrão é `NO`.
- **NSThirdPartyExceptionMinimumTLSVersion** (`String`)-TLS a versão usada quando o domínio é um serviço de terceiros 3º fora do controle do desenvolvedor.
- **NSThirdPartyExceptionRequiresForwardSecrecy** (`Boolean`) - se `YES` um domínio de terceiros 3º requer sigilo total.
- **NSThirdPartyExceptionAllowsInsecureHTTPLoads** (`Boolean`) - se `YES` o ATS permitirá que a comunicação não segura com domínios de 3ª parte.

<a name="optout" />

### <a name="opting-out-of-ats"></a>Aceitar-Out de ATS

Enquanto Apple altamente sugere usando o `HTTPS` informações baseadas em protocolo e comunicação segura com a internet, pode haver momentos em que isso nem sempre é possível. Por exemplo, se você estiver se comunicando com um serviço de web 3ª parte ou usando a internet entregues anúncios em seu aplicativo.

Se seu aplicativo xamarin deve fazer uma solicitação para inseguro, as seguintes alterações ao seu aplicativo **Info. plist** arquivo desabilitará os padrões de segurança que impõe ATS para um determinado domínio:

```xml
<key>NSAppTransportSecurity</key>
<dict>
    <key>NSExceptionDomains</key>
    <dict>
        <key>www.the-domain-name.com</key>
        <dict>
            <key>NSExceptionMinimumTLSVersion</key>
            <string>TLSv1.0</string>
            <key>NSExceptionRequiresForwardSecrecy</key>
            <false/>
            <key>NSExceptionAllowsInsecureHTTPLoads</key>
            <true/>
            <key>NSIncludesSubdomains</key>
            <true/>
        </dict>
    </dict>
</dict>
```

Dentro do Visual Studio para Mac, clique duas vezes o `Info.plist` do arquivo no **Gerenciador de soluções**, alterne para o **fonte** exibir e adicionar as chaves acima:

[ ![](ats-images/ats01.png "A exibição da fonte do arquivo. plist")](ats-images/ats01.png)


Se seu aplicativo precisa carregar e exibir o conteúdo da web de sites não seguras, adicione o seguinte ao seu aplicativo **Info. plist** arquivo para permitir que páginas da web para carregar corretamente enquanto a proteção de segurança de transporte da Apple (ATS) ainda está habilitada para o restante do aplicativo:

```xml
<key>NSAppTransportSecurity</key>
<dict>
    <key> NSAllowsArbitraryLoadsInWebContent</key>
    <true/>
</dict>
```

Opcionalmente, você pode fazer as seguintes alterações ao seu aplicativo **Info. plist** arquivo para desabilitar completamente ATS para todos os domínios e comunicação na internet:

```xml
<key>NSAppTransportSecurity</key>
<dict>
    <key>NSAllowsArbitraryLoads</key>
    <true/>
</dict>
```

Dentro do Visual Studio para Mac, clique duas vezes o `Info.plist` do arquivo no **Gerenciador de soluções**, alterne para o **fonte** exibir e adicionar as chaves acima:

[ ![](ats-images/ats02.png "A exibição da fonte do arquivo. plist")](ats-images/ats02.png)

> [!IMPORTANT]
> **Observação:** se seu aplicativo requer uma conexão para um site inseguro, você deve **sempre** digite o domínio como uma exceção usando `NSExceptionDomains` em vez de como ATS desativar completamente usando `NSAllowsArbitraryLoads`. `NSAllowsArbitraryLoads` deve ser usado somente em situações de emergências extremas.




Novamente, desabilitando ATS deve _somente_ ser usado como um último recurso, se alternar para conexões de seguras não está disponível ou.

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo apresentou a segurança de transporte do aplicativo (ATS) e descritos a maneira como ele impõe a proteger as comunicações com a internet. Primeiro, abordamos as alterações que ATS requer para um aplicativo xamarin em execução no iOS 9. Em seguida, abordamos opções e recursos ATS controlar. Por fim, abordamos recusando ATS em seu aplicativo xamarin.



## <a name="related-links"></a>Links relacionados

- [Exemplos do iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [iOS 9 para desenvolvedores](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
