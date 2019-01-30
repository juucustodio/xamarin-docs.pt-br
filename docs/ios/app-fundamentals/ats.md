---
title: Segurança de transporte de aplicativo no xamarin. IOS
description: Segurança de transporte de aplicativo (ATS) impõe conexões seguras entre recursos da internet (como o servidor de back-end do aplicativo) e seu aplicativo.
ms.prod: xamarin
ms.assetid: F8C5E444-2D05-4D9B-A2EF-EB052CD6F007
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/13/2017
ms.openlocfilehash: f9308d3a746a5a0a43cf47cc5ea809c0f82bbe7b
ms.sourcegitcommit: a1a58afea68912c79d16a3f64de9a0c1feb2aeb4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55233816"
---
# <a name="app-transport-security-in-xamarinios"></a>Segurança de transporte de aplicativo no xamarin. IOS

_Segurança de transporte de aplicativo (ATS) impõe conexões seguras entre recursos da internet (como o servidor de back-end do aplicativo) e seu aplicativo._

Este artigo apresenta as alterações de segurança que impõe a segurança de transporte de aplicativo em um aplicativo do iOS 9 e [para seus projetos do xamarin. IOS, isso significa](#xamarinsupport), ele abordará as [opções de configuração de ATS](#config) e Ele aborda como [recusar ATS](#optout) ATS, se necessário. Como ATS está habilitado por padrão, todas as conexões não seguras internet irá gerar uma exceção em aplicativos do iOS 9 (a menos que você explicitamente permitido-lo).


## <a name="about-app-transport-security"></a>Sobre a segurança de transporte de aplicativo

Como mencionado acima, ATS garante que todas as comunicações de internet no iOS 9 e nos X El Capitan está em conformidade para proteger a conexão as práticas recomendadas, impedindo assim a divulgação acidental de informações confidenciais diretamente por meio de seu aplicativo ou uma biblioteca que é consumindo.

Para aplicativos existentes, implemente o `HTTPS` protocolo sempre que possível. Para novos aplicativos do xamarin. IOS, você deve usar `HTTPS` exclusivamente ao se comunicar com recursos da internet. Além disso, a comunicação alto nível da API deve ser criptografada usando TLS versão 1.2 com sigilo.

Qualquer conexão feita com [NSUrlConnection](xref:Foundation.NSUrlConnection), [CFUrl](xref:CoreFoundation.CFUrl) ou [NSUrlSession](xref:Foundation.NSUrlSession) usará ATS por padrão nos aplicativos criados para o iOS 9 e OS X 10.11 (El Capitan).

## <a name="default-ats-behavior"></a>Comportamento ATS padrão

Uma vez que o ATS é habilitado por padrão nos aplicativos criados para o iOS 9 e OS X 10.11 (El Capitan), todas as conexões usando [NSUrlConnection](xref:Foundation.NSUrlConnection), [CFUrl](xref:CoreFoundation.CFUrl) ou [NSUrlSession](xref:Foundation.NSUrlSession) estará sujeito Requisitos de segurança ATS. Se suas conexões não atenderem a esses requisitos, ele falhará com uma exceção.

### <a name="ats-connection-requirements"></a>Requisitos de Conexão de ATS

ATS irá impor os seguintes requisitos para todas as conexões de internet:

- Todas as codificações de conexão devem estar usando o sigilo. Consulte a lista de criptografias aceitas abaixo.
- O protocolo de segurança de camada de transporte (TLS) deve ser a versão 1.2 ou superior.
- Pelo menos uma impressão digital de SHA256 com uma 2048 bits ou chave RSA maior, ou 256 bits ou maior chave de curva Elliptic (ECC) deve ser usada para todos os certificados.

Novamente, desde que o ATS é habilitado por padrão no iOS 9, qualquer tentativa de fazer uma conexão que não atenda a esses requisitos resultará em uma exceção sendo lançada. 

<a name="ATS-Compatible-Ciphers" />

### <a name="ats-compatible-ciphers"></a>Codificações ATS compatíveis

O seguinte tipo de codificação de sigilo são aceitas por ATS protegido comunicações pela internet:

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

## <a name="supporting-ats-in-xamarinios"></a>Suporte a ATS no xamarin. IOS

Como o ATS é habilitado por padrão no iOS 9 e nos X El Capitan, se seu aplicativo xamarin. IOS ou qualquer biblioteca ou o serviço está usando faz conexão com a internet, será necessário executar alguma ação ou suas conexões resultará em uma exceção sendo lançada.

Para um aplicativo existente, a Apple sugere que você oferece suporte a `HTTPS` protocolo assim que possível. Se um não é possível porque você está se conectando para serviço web que não oferece suporte de terceiros de uma 3ª `HTTPS` ou se o suporte a `HTTPS` seria impraticável, você pode recusar ATS. Consulte a [Opting-Out de ATS](#optout) seção abaixo para obter mais detalhes.

Para um novo aplicativo xamarin. IOS, você deve usar `HTTPS` exclusivamente ao se comunicar com recursos da internet. Novamente, pode haver situações (como usar um serviço web de terceiros 3ª) em que isso não é possível e você precisará recusar ATS.

Além disso, o ATS impõe comunicação alto nível da API a ser criptografado usando TLS versão 1.2 com sigilo. Consulte a [requisitos de Conexão de ATS](#ATS-Connection-Requirements) e [codificações compatíveis de ATS](#ATS-Compatible-Ciphers) seções acima para obter mais detalhes.

Embora você não pode estar familiarizado com o TLS ([Transport Layer Security](https://en.wikipedia.org/wiki/Transport_Layer_Security)) é o sucessor do SSL ([Secure Socket Layer](https://en.wikipedia.org/wiki/Transport_Layer_Security)) e fornece uma coleção de protocolos de criptografia para impor a segurança ao longo conexões de rede.

O nível TLS é controlado pelo serviço da web que está consumindo e, portanto, fora do controle do aplicativo. Tanto a `HttpClient` e o `ModernHttpClient` deve usar automaticamente o nível mais alto de criptografia de TLS com suporte pelo servidor.

Dependendo do servidor que você está se comunicando com (especialmente se ele é um serviço de terceiros 3ª), talvez seja necessário desabilitar o sigilo ou selecione um nível inferior do TLS. Consulte a [Configurando opções de ATS](#Configuring-ATS-Options) seção abaixo para obter mais detalhes.

> [!IMPORTANT]
> Segurança de transporte de aplicativo não se aplica aos aplicativos do Xamarin usando **implementações HTTPClient gerenciados**. Ele se aplica a conexões usando CFNetwork **implementações HTTPClient** ou **implementações NSURLSession HTTPClient** somente.

### <a name="setting-the-httpclient-implementation"></a>Definindo a implementação de HTTPClient

Para definir a implementação de HTTPClient usada por um aplicativo iOS, clique duas vezes o **projeto** na **Gerenciador de soluções** para abrir o **opções de projeto**. Navegue até **Build do iOS** e selecione o tipo de cliente desejada na **implementação de HttpClient** lista suspensa:

![](ats-images/client01.png "Definindo o opções de Build do iOS")


#### <a name="managed-handler"></a>Manipulador gerenciado

O manipulador gerenciado é o manipulador de HttpClient totalmente gerenciado que foi enviado com versões anteriores do xamarin. IOS e é o manipulador padrão.

Prós:

- Ele é mais compatível com o Microsoft .NET e a versão mais antiga do Xamarin.

Contras:

- Ele não é totalmente integrado com o iOS (por exemplo, ela é limitada a TLS 1.0).
- Ele é normalmente muito mais lento do que as APIs nativas.
- Ela requer mais código gerenciado e cria aplicativos maiores.

#### <a name="cfnetwork-handler"></a>Manipulador de CFNetwork

O manipulador CFNetwork com base em se baseia no nativo `CFNetwork` framework.

Prós:

- Usa a API nativa para um melhor desempenho e tamanhos menores de executáveis.
- Adiciona suporte para novos padrões, como TLS 1.2.

Contras:

- Requer iOS 6 ou posterior.
- Não disponível do watchOS.
- Alguns recursos de HttpClient e opções não estão disponíveis.

#### <a name="nsurlsession-handler"></a>Manipulador de NSUrlSession

O manipulador NSUrlSession com base em se baseia no nativo `NSUrlSession` API.

Prós:

- Usa a API nativa para um melhor desempenho e tamanhos menores de executáveis.
- Adiciona suporte para novos padrões, como TLS 1.2.

Contras:

- Requer iOS 7 ou posterior.
- Alguns recursos de HttpClient e opções não estão disponíveis. 

## <a name="diagnosing-ats-issues"></a>Diagnosticando problemas ATS

Ao tentar se conectar à internet, seja diretamente ou de uma exibição da web no iOS 9, você poderá receber um erro no formulário:

> Segurança de transporte de aplicativo bloqueou um texto não criptografado, o HTTP (http://www.-the-blocked-domain.com) carga de recursos, pois ele não é seguro. Exceções temporárias podem ser configuradas por meio do arquivo de info. plist do seu aplicativo.

IOS9, segurança de transporte de aplicativo (ATS) impõe a conexões seguras entre recursos da internet (como o servidor de back-end do aplicativo) e seu aplicativo. Além disso, o ATS requer comunicação usando o `HTTPS` protocolo e a comunicação de alto nível API a ser criptografado usando TLS versão 1.2 com sigilo.

Uma vez que o ATS é habilitado por padrão nos aplicativos criados para o iOS 9 e OS X 10.11 (El Capitan), todas as conexões usando `NSURLConnection`, `CFURL` ou `NSURLSession` estará sujeito aos requisitos de segurança ATS. Se suas conexões não atenderem a esses requisitos, ele falhará com uma exceção.

Apple também fornece o [aplicativo de exemplo TLSTool](https://developer.apple.com/library/mac/samplecode/sc1236/Introduction/Intro.html#//apple_ref/doc/uid/DTS40014927-Intro-DontLinkElementID_2) que pode ser compilado (ou, opcionalmente, transcodificados para Xamarin e C#) e usados para diagnosticar problemas ATS/TLS. Consulte a [Opting-Out de ATS](#optout) seção abaixo para obter informações sobre como resolver esse problema.


<a name="config" />

## <a name="configuring-ats-options"></a>Configurando as opções de ATS

Você pode configurar vários recursos de ATS definindo valores para chaves específicas em seu aplicativo **Info. plist** arquivo. As chaves a seguir estão disponíveis para controlar o ATS (_recuado para mostrar como eles estão aninhados_):

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

- **NSAppTransportSecurity** (`Dictionary`) - contém todas as chaves de configuração e os valores para ATS.
- **NSAllowsArbitraryLoads** (`Boolean`) – se `YES` ATS serão desabilitados para qualquer domínio **não** listados no `NSExceptionDomains`. Para domínios listados, as configurações de segurança especificadas serão usadas.
- **NSAllowsArbitraryLoadsInWebContent** (`Boolean`) – se `YES` permitirá que as páginas da web para carregar corretamente enquanto a proteção de segurança de transporte da Apple (ATS) ainda está habilitada para o restante do aplicativo.
- **NSExceptionDomains** (`Dictionary`)-uma coleção de domínios que e as configurações de segurança que ATS deve usar para um determinado domínio.
- **< Domain-name-for-exception-as-string >** (`Dictionary`)-uma coleção de exceções para um determinado domínio (por exemplo. `www.xamarin.com`).
- **NSExceptionMinimumTLSVersion** (`String`) – a versão mínima do TLS como `TLSv1.0`, `TLSv1.1` ou `TLSv1.2` (que é o padrão).
- **NSExceptionRequiresForwardSecrecy** (`Boolean`) – se `NO` o domínio não precisa usar uma criptografia com segurança de encaminhamento. O valor padrão é `YES`.
- **NSExceptionAllowsInsecureHTTPLoads** (`Boolean`) – se `NO` (o padrão) em que todas as comunicações com esse domínio devem ser o `HTTPS` protocolo.
- **NSRequiresCertificateTransparency** (`Boolean`) – se `YES` protocolo (SSL do domínio do) deve incluir dados de transparência válido. O valor padrão é `NO`.
- **NSIncludesSubdomains** (`Boolean`) – se `YES` essas configurações substituem todos os subdomínios desse domínio. O valor padrão é `NO`.
- **NSThirdPartyExceptionMinimumTLSVersion** (`String`)-versão do TLS a usada quando o domínio é um serviço de terceiros 3ª fora do controle do desenvolvedor.
- **NSThirdPartyExceptionRequiresForwardSecrecy** (`Boolean`) – se `YES` um domínio de terceiros 3ª requer sigilo.
- **NSThirdPartyExceptionAllowsInsecureHTTPLoads** (`Boolean`) – se `YES` ATS permitirá não segura a comunicação com o 3º domínios de terceiros.

<a name="optout" />

### <a name="opting-out-of-ats"></a>Aceitar-Out de ATS

Enquanto a Apple sugere altamente usando o `HTTPS` informações baseadas em protocolo e comunicação segura à internet, pode haver ocasiões em que isso nem sempre é possível. Por exemplo, se você estiver se comunicando com um serviço web de terceiros 3ª ou usando a internet entregues anúncios em seu aplicativo.

Se seu aplicativo xamarin. IOS deve fazer uma solicitação para inseguro, as seguintes alterações ao seu aplicativo **Info. plist** arquivo desabilitará os padrões de segurança que ATS impõe em um determinado domínio:

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

Dentro do Visual Studio para Mac, clique duas vezes o `Info.plist` de arquivo no **Gerenciador de soluções**, alterne para o **fonte** exibir e adicionar as chaves acima:

[![](ats-images/ats01.png "A exibição da fonte do arquivo Info. plist")](ats-images/ats01.png#lightbox)


Se seu aplicativo precisa carregar e exibir o conteúdo da web de sites não segura, adicione o seguinte ao seu aplicativo **Info. plist** arquivo para permitir que páginas da web para carregar corretamente enquanto a proteção de segurança de transporte da Apple (ATS) ainda está habilitada para o restante do aplicativo:

```xml
<key>NSAppTransportSecurity</key>
<dict>
    <key> NSAllowsArbitraryLoadsInWebContent</key>
    <true/>
</dict>
```

Opcionalmente, você pode fazer as seguintes alterações ao seu aplicativo **Info. plist** arquivo para desabilitar completamente o ATS para todos os domínios e comunicação na internet:

```xml
<key>NSAppTransportSecurity</key>
<dict>
    <key>NSAllowsArbitraryLoads</key>
    <true/>
</dict>
```

Dentro do Visual Studio para Mac, clique duas vezes o `Info.plist` de arquivo no **Gerenciador de soluções**, alterne para o **fonte** exibir e adicionar as chaves acima:

[![](ats-images/ats02.png "A exibição da fonte do arquivo Info. plist")](ats-images/ats02.png#lightbox)

> [!IMPORTANT]
> Se seu aplicativo requer uma conexão em um site inseguro, você deve **sempre** inserir o domínio como uma exceção usando `NSExceptionDomains` em vez de como o ATS desativar completamente usando `NSAllowsArbitraryLoads`. `NSAllowsArbitraryLoads` só deve ser usado em situações extremas de emergências.




Novamente, desabilitar o ATS devem _apenas_ ser usado como último recurso, se mudar para conexões seguras não está disponível ou impraticáveis.

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo apresentou a segurança de transporte de aplicativo (ATS) e descritos da forma que ele impõe a proteger as comunicações com a internet. Primeiro, abordamos as alterações que ATS requer para um aplicativo xamarin. IOS no iOS 9. Em seguida, abordamos a opções e os recursos de controle ATS. Por fim, abordamos a recusa de ATS em seu aplicativo xamarin. IOS.



## <a name="related-links"></a>Links relacionados

- [Amostras do iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [iOS 9 para desenvolvedores](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
