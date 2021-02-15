---
title: Segurança de transporte de aplicativo no Xamarin. iOS
description: A ATS (segurança de transporte de aplicativo) impõe conexões seguras entre os recursos da Internet (como o servidor back-end do aplicativo) e seu aplicativo.
ms.prod: xamarin
ms.assetid: F8C5E444-2D05-4D9B-A2EF-EB052CD6F007
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/13/2017
ms.openlocfilehash: 797122d82f13a178986d7d19203a8f6eeddaf483
ms.sourcegitcommit: e27e29c14b783263e063baaa65d4eecb8dd31f57
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98628885"
---
# <a name="app-transport-security-in-xamarinios"></a>Segurança de transporte de aplicativo no Xamarin. iOS

_A ATS (segurança de transporte de aplicativo) impõe conexões seguras entre os recursos da Internet (como o servidor back-end do aplicativo) e seu aplicativo._

Este artigo apresentará as alterações de segurança que a segurança de transporte de aplicativo impõe em um aplicativo iOS 9 e [o que isso significa para seus projetos do Xamarin. Ios](#xamarinsupport), que abordará as [Opções de configuração do ATS](#config) e abordará como [recusar](#optout) o ATS ATS, se necessário. Como o ATS está habilitado por padrão, as conexões de Internet não seguras gerarão uma exceção nos aplicativos do iOS 9 (a menos que você tenha explicitamente permitido).

## <a name="about-app-transport-security"></a>Sobre a segurança de transporte de aplicativo

Como mencionado acima, o ATS garante que todas as comunicações da Internet no iOS 9 e no OS X El Capitan estejam de acordo com as práticas recomendadas de conexão segura, impedindo, assim, a divulgação acidental de informações confidenciais diretamente por meio de seu aplicativo ou de uma biblioteca consumida.

Para aplicativos existentes, implemente o `HTTPS` protocolo sempre que possível. Para novos aplicativos Xamarin. iOS, você deve usar `HTTPS` exclusivamente ao se comunicar com recursos da Internet. Além disso, a comunicação de API de alto nível deve ser criptografada usando TLS versão 1,2 com sigilo de encaminhamento.

Qualquer conexão feita com [NSUrlConnection](xref:Foundation.NSUrlConnection), [CFUrl](xref:CoreFoundation.CFUrl) ou [NSUrlSession](xref:Foundation.NSUrlSession) usará o ATS por padrão em aplicativos criados para IOS 9 e os X 10,11 (El Capitan).

## <a name="default-ats-behavior"></a>Comportamento padrão de ATS

Como o ATS está habilitado por padrão em aplicativos criados para iOS 9 e OS X 10,11 (El Capitan), todas as conexões que usam [NSUrlConnection](xref:Foundation.NSUrlConnection), [CFUrl](xref:CoreFoundation.CFUrl) ou [NSUrlSession](xref:Foundation.NSUrlSession) estarão sujeitas a requisitos de segurança de ATS. Se suas conexões não atenderem a esses requisitos, elas falharão com uma exceção.

### <a name="ats-connection-requirements"></a>Requisitos de conexão de ATS

O ATS irá impor os seguintes requisitos para todas as conexões com a Internet:

- Todas as codificações de conexão devem estar usando o sigilo de encaminhamento. Consulte a lista de codificações aceitas abaixo.
- O protocolo TLS deve ser a versão 1,2 ou superior.
- Pelo menos uma impressão digital SHA256 com uma chave RSA de 2048 bits ou maior, ou uma chave de 256 bits ou mais Elliptic-Curve (ECC) deve ser usada para todos os certificados.

Novamente, como o ATS está habilitado por padrão no iOS 9, qualquer tentativa de estabelecer uma conexão que não atenda a esses requisitos resultará em uma exceção sendo gerada.

<a name="ATS-Compatible-Ciphers"></a>

### <a name="ats-compatible-ciphers"></a>Codificações compatíveis com ATS

O seguinte tipo de codificação de sigilo de encaminhamento é aceito pelas comunicações seguras da Internet do ATS:

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

Para obter mais informações sobre como trabalhar com classes de comunicação da Internet do iOS, consulte referência de [classe NSURLConnection](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/Foundation/Classes/NSURLConnection_Class/index.html#//apple_ref/doc/uid/TP40003755)da Apple, referência de [CFURL](https://developer.apple.com/library/prerelease/ios/documentation/CoreFoundation/Reference/CFURLRef/index.html#//apple_ref/doc/uid/20001206) ou [referência de classe NSURLSession](https://developer.apple.com/library/prerelease/ios/documentation/Foundation/Reference/NSURLSession_class/index.html#//apple_ref/doc/uid/TP40013435).

<a name="xamarinsupport"></a>

## <a name="supporting-ats-in-xamarinios"></a>Oferecendo suporte ao ATS no Xamarin. iOS

Como o ATS está habilitado por padrão no iOS 9 e no OS X El Capitan, se seu aplicativo Xamarin. iOS ou qualquer biblioteca ou serviço que estiver usando fizer a conexão com a Internet, você precisará executar alguma ação ou suas conexões resultarão em uma exceção sendo gerada.

Para um aplicativo existente, a Apple sugere que você ofereça suporte ao protocolo assim que `HTTPS` possível. Se você não puder porque você está se conectando a um serviço Web de terceiros que não dá suporte a `HTTPS` ou se o suporte for `HTTPS` inviável, você poderá recusar o ATS. Consulte a seção [recusando o ATS](#optout) abaixo para obter mais detalhes.

Para um novo aplicativo Xamarin. iOS, você deve usar `HTTPS` exclusivamente ao se comunicar com recursos da Internet. Novamente, pode haver situações (como usar um serviço Web de terceiros) em que isso não é possível e você precisará recusar o ATS.

Além disso, o ATS impõe a comunicação de API de alto nível para ser criptografada usando TLS versão 1,2 com sigilo de encaminhamento. Consulte as seções [requisitos de conexão de ATS](#ats-connection-requirements) e [codificações compatíveis com ATS](#ats-compatible-ciphers) acima para obter mais detalhes.

Embora você talvez não esteja familiarizado com o TLS ([Transport Layer Security](https://en.wikipedia.org/wiki/Transport_Layer_Security)), ele é o sucessor do SSL ([Secure Socket Layer](https://en.wikipedia.org/wiki/Transport_Layer_Security)) e fornece uma coleção de protocolos de criptografia para impor segurança em conexões de rede.

O nível de TLS é controlado pelo serviço Web que você está consumindo e, portanto, fora do controle do aplicativo. O `HttpClient` e o `ModernHttpClient` devem usar automaticamente o nível mais alto de criptografia TLS com suporte do servidor.

Dependendo do servidor no qual você está conversando (especialmente se for um serviço de terceiros), talvez seja necessário desabilitar o sigilo de encaminhamento ou selecionar um nível de TLS inferior. Consulte a seção [Configurando opções de ATS](#configuring-ats-options) abaixo para obter mais detalhes.

> [!IMPORTANT]
> A segurança de transporte de aplicativo não se aplica a aplicativos Xamarin usando **implementações HttpClient gerenciadas**. Ele se aplica a conexões usando **implementações CFNetwork HttpClient** ou **implementações NSURLSession HttpClient** apenas.

### <a name="setting-the-httpclient-implementation"></a>Definindo a implementação de HTTPClient

Para definir a implementação de HTTPClient usada por um aplicativo iOS, clique duas vezes no **projeto** no **Gerenciador de soluções** para abrir as **Opções de projeto**. Navegue até **Build do IOS** e selecione o tipo de cliente desejado no menu suspenso de **implementação de HttpClient** :

![Definindo as opções de Build do iOS](ats-images/client01.png)

#### <a name="managed-handler"></a>Manipulador gerenciado

O manipulador gerenciado é o manipulador HttpClient totalmente gerenciado que foi enviado com versões anteriores do Xamarin. iOS e é o manipulador padrão.

Prós:

- É o mais compatível com Microsoft .NET e a versão mais antiga do Xamarin.

Contras:

- Ele não está totalmente integrado ao iOS (por exemplo, é limitado a TLS 1,0).
- Normalmente, é muito mais lento do que as APIs nativas.
- Ele requer mais código gerenciado e cria aplicativos maiores.

#### <a name="cfnetwork-handler"></a>Manipulador de CFNetwork

O manipulador baseado em CFNetwork é baseado na `CFNetwork` estrutura nativa.

Prós:

- Usa a API nativa para obter melhor desempenho e tamanhos executáveis menores.
- Adiciona suporte para padrões mais recentes, como o TLS 1,2.

Contras:

- Requer o iOS 6 ou posterior.
- Não disponível de watchOS.
- Alguns recursos e opções do HttpClient não estão disponíveis.

#### <a name="nsurlsession-handler"></a>Manipulador de NSUrlSession

O manipulador baseado em NSUrlSession é baseado na `NSUrlSession` API nativa.

Prós:

- Usa a API nativa para obter melhor desempenho e tamanhos executáveis menores.
- Adiciona suporte para padrões mais recentes, como o TLS 1,2.

Contras:

- Requer o iOS 7 ou posterior.
- Alguns recursos e opções do HttpClient não estão disponíveis.

## <a name="diagnosing-ats-issues"></a>Diagnosticando problemas de ATS

Ao tentar se conectar à Internet, seja diretamente ou de uma exibição da Web no iOS 9, você pode receber um erro no formato:

> A segurança de transporte de aplicativo bloqueou uma carga de recurso HTTP () de texto não criptografado `http://www.-the-blocked-domain.com` , pois ela não é segura. Exceções temporárias podem ser configuradas por meio do arquivo info. plist do seu aplicativo.

No iOS9, a ATS (segurança de transporte de aplicativo) impõe conexões seguras entre os recursos da Internet (como o servidor back-end do aplicativo) e seu aplicativo. Além disso, o ATS exige a comunicação usando o `HTTPS` protocolo e a comunicação de API de alto nível para ser criptografada usando TLS versão 1,2 com sigilo de encaminhamento.

Como o ATS está habilitado por padrão em aplicativos criados para iOS 9 e OS X 10,11 (El Capitan), todas as `NSURLConnection` conexões `CFURL` que usam ou `NSURLSession` estarão sujeitas a requisitos de segurança de ATS. Se suas conexões não atenderem a esses requisitos, elas falharão com uma exceção.

A Apple também fornece o [aplicativo de exemplo TLSTool](https://developer.apple.com/library/mac/samplecode/sc1236/Introduction/Intro.html#//apple_ref/doc/uid/DTS40014927-Intro-DontLinkElementID_2) que pode ser compilado (ou, opcionalmente, transcodificado para Xamarin e C#) e usado para diagnosticar problemas de ATS/TLS. Consulte a seção [recusando o ATS](#optout) abaixo para obter informações sobre como resolver esse problema.

<a name="config"></a>

## <a name="configuring-ats-options"></a>Configurando opções de ATS

Você pode configurar vários dos recursos do ATS definindo valores para chaves específicas no arquivo **info. plist** do seu aplicativo. As seguintes chaves estão disponíveis para controlar a ATS (_recuada para mostrar como elas são aninhadas_):

```
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

Cada chave tem o seguinte tipo e significado:

- **NSAppTransportSecurity** ( `Dictionary` )-contém todas as chaves de configuração e valores para ATS.
- **NSAllowsArbitraryLoads** ( `Boolean` ) – se o `YES` ATS será desabilitado para qualquer domínio **não** listado no `NSExceptionDomains` . Para domínios listados, as configurações de segurança especificadas serão usadas.
- **NSAllowsArbitraryLoadsInWebContent** ( `Boolean` ) – se `YES` permitirá que as páginas da Web sejam carregadas corretamente enquanto a proteção de ATS (segurança de transporte da Apple) ainda estiver habilitada para o restante do aplicativo.
- **NSExceptionDomains** ( `Dictionary` ) – uma coleção de domínios que e as configurações de segurança que a ATS deve usar para um determinado domínio.
- **\<domain-name-for-exception-as-string>** ( `Dictionary` ) – Uma coleção de exceções para um determinado domínio (por exemplo, `www.xamarin.com`).
- **NSExceptionMinimumTLSVersion** ( `String` ) – a versão mínima de TLS como `TLSv1.0` `TLSv1.1` ou `TLSv1.2` (que é o padrão).
- **NSExceptionRequiresForwardSecrecy** ( `Boolean` ) – se `NO` o domínio não precisar usar uma codificação com segurança de encaminhamento. O valor padrão é `YES`.
- **NSExceptionAllowsInsecureHTTPLoads** ( `Boolean` ) – se `NO` (o padrão) todas as comunicações com esse domínio devem estar no `HTTPS` protocolo.
- **NSRequiresCertificateTransparency** ( `Boolean` ) – se `YES` o protocolo SSL do domínio (SSL) deve incluir dados de transparência válidos. O valor padrão é `NO`.
- **NSIncludesSubdomains** ( `Boolean` ) – se `YES` essas configurações substituirem todos os subdomínios deste domínio. O valor padrão é `NO`.
- **NSThirdPartyExceptionMinimumTLSVersion** ( `String` ) – a versão TLS usada quando o domínio é um serviço de terceiros fora do controle do desenvolvedor.
- **NSThirdPartyExceptionRequiresForwardSecrecy** ( `Boolean` ) – se `YES` um domínio de terceiros exigir sigilo de encaminhamento.
- **NSThirdPartyExceptionAllowsInsecureHTTPLoads** ( `Boolean` ) – se `YES` o ATS permitir a comunicação não segura com domínios de terceiros.

<a name="optout"></a>

### <a name="opting-out-of-ats"></a>Opting-Out de ATS

Embora a Apple sugira usar o `HTTPS` protocolo e a comunicação segura com as informações baseadas na Internet, pode haver ocasiões em que isso nem sempre é possível. Por exemplo, se você estiver se comunicando com um serviço Web de terceiros ou usando anúncios entregues pela Internet em seu aplicativo.

Se seu aplicativo Xamarin. iOS precisar fazer uma solicitação para um domínio não seguro, as alterações a seguir no arquivo **info. plist** do aplicativo desabilitarão os padrões de segurança que o ATS impõe para um determinado domínio:

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

Dentro de Visual Studio para Mac, clique duas vezes no `Info.plist` arquivo na **Gerenciador de soluções**, alterne para a exibição de **origem** e adicione as chaves acima:

[![A exibição de origem do arquivo info. plist depois de adicionar as chaves especificadas.](ats-images/ats01.png)](ats-images/ats01.png#lightbox)

Se seu aplicativo precisar carregar e exibir conteúdo da Web de sites não seguros, adicione o seguinte ao arquivo **info. plist** do seu aplicativo para permitir que as páginas da Web sejam carregadas corretamente enquanto a proteção de ATS (segurança de transporte da Apple) ainda estiver habilitada para o restante do aplicativo:

```xml
<key>NSAppTransportSecurity</key>
<dict>
    <key> NSAllowsArbitraryLoadsInWebContent</key>
    <true/>
</dict>
```

Opcionalmente, você pode fazer as seguintes alterações no arquivo **info. plist** do aplicativo para desabilitar completamente o ATS para todos os domínios e comunicação com a Internet:

```xml
<key>NSAppTransportSecurity</key>
<dict>
    <key>NSAllowsArbitraryLoads</key>
    <true/>
</dict>
```

Dentro de Visual Studio para Mac, clique duas vezes no `Info.plist` arquivo na **Gerenciador de soluções**, alterne para a exibição de **origem** e adicione as chaves acima:

[![A exibição de origem do arquivo info. plist depois de especificar NSAllowsArbitraryLoadsInWebContent e NSAllowsArbitraryLoads.](ats-images/ats02.png)](ats-images/ats02.png#lightbox)

> [!IMPORTANT]
> Se seu aplicativo exigir uma conexão com um site da Web não seguro, você **sempre** deverá inserir o domínio como uma exceção usando `NSExceptionDomains` , em vez de desativar completamente o ATS usando `NSAllowsArbitraryLoads` . `NSAllowsArbitraryLoads` Só deve ser usado em situações de emergência extremas.

Novamente, desabilitar o ATS _só_ deve ser usado como último recurso, se a alternância para conexões seguras estiver indisponível ou impraticável.

<a name="Summary"></a>

## <a name="summary"></a>Resumo

Este artigo introduziu a ATS (segurança de transporte de aplicativo) e descreveu a maneira como ela impõe comunicações seguras com a Internet. Primeiro, abordamos as alterações que o ATS requer para um aplicativo Xamarin. iOS em execução no iOS 9. Em seguida, abordamos o controle de recursos e opções de ATS. Finalmente, abordamos a ausência de ATS em seu aplicativo Xamarin. iOS.

## <a name="related-links"></a>Links Relacionados

- [Amostras do iOS 9](/samples/browse/?products=xamarin&term=Xamarin.iOS%2biOS9)
- [iOS 9 para desenvolvedores](https://developer.apple.com/ios/pre-release/)
- [iOS 9,0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)