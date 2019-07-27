---
title: Segurança de camada de transporte (TLS) 1,2
description: Este documento descreve como habilitar o TLS 1,2 para projetos Xamarin. iOS, Xamarin. Android e Xamarin. Mac. Ele demonstra como fazer isso no Visual Studio 2019 e Visual Studio para Mac.
ms.prod: xamarin
ms.assetid: 399F71C6-16A4-4ABC-B30D-AF17D066A5FA
author: asb3993
ms.author: amburns
ms.date: 04/20/2018
ms.openlocfilehash: d904ab25ee5a91fc33d9fa09c66a043e4bd33872
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68511404"
---
# <a name="transport-layer-security-tls-12"></a>Segurança de camada de transporte (TLS) 1,2

Usar a versão mais recente [ _do protocolo_ TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security) é importante para garantir que as comunicações de rede do aplicativo sejam seguras.

> [!WARNING]
> **Abril de 2018** – devido a requisitos de segurança maiores, incluindo a conformidade com PCI, os principais provedores de nuvem e servidores Web devem parar de dar suporte a versões de TLS anteriores a 1,2.  Os projetos do Xamarin criados em versões anteriores do Visual Studio usam o padrão para usar versões mais antigas do TLS.
>
> Para garantir que seus aplicativos continuem a trabalhar com esses servidores e serviços, **você deve atualizar seus projetos do Xamarin para usar as configurações abaixo e, em seguida, recompilar e implantar novamente seus aplicativos** para os usuários.

Os projetos devem referenciar o assembly **System .net. http** e ser configurado como mostrado abaixo.

## <a name="update-xamarinandroid-to-tls-12"></a>Atualizar Xamarin. Android para TLS 1,2

Atualize a **implementação de HttpClient** e as opções de **implementação de SSL/TLS** para habilitar a segurança TLS 1,2.

> [!NOTE]
> Requer Android 5,0 ou mais recente.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Essas configurações podem ser encontradas nas **Propriedades do projeto > opções do Android** e, em seguida, clicando no botão **avançado** :

[![Configurar HttpClient e TLS no Visual Studio](transport-layer-security-images/android-win-sml.png)](transport-layer-security-images/android-win.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Essas configurações podem ser encontradas em **Opções de projeto > compilação > guia Build do Android** :

[![Configurar HttpClient e TLS no Visual Studio para Mac](transport-layer-security-images/android-mac-sml.png)](transport-layer-security-images/android-mac.png#lightbox)

-----

## <a name="update-xamarinios-to-tls-12"></a>Atualizar Xamarin. iOS para TLS 1,2

Atualize a opção de **implementação HttpClient** para habilitar a segurança de TSL 1,2.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Essa configuração pode ser encontrada nas **Propriedades do projeto > Build do IOS**:

[![Configurar HttpClient e TLS no Visual Studio](transport-layer-security-images/ios-win-sml.png)](transport-layer-security-images/ios-win.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Essa configuração pode ser encontrada em **Opções de projeto > compilação > guia Build do IOS** :

[![Configurar HttpClient no Visual Studio para Mac](transport-layer-security-images/ios-mac-sml.png)](transport-layer-security-images/ios-mac.png#lightbox)

-----

## <a name="update-xamarinmac-to-tls-12"></a>Atualizar o Xamarin. Mac para TLS 1,2

No Visual Studio para Mac, para habilitar o TLS 1,2 em um aplicativo Xamarin. Mac, atualize a opção de **implementação HttpClient** em **opções de projeto > compilar > Build do Mac**:

[![Configurar HttpClient no Visual Studio para Mac](transport-layer-security-images/macos-mac-sml.png)](transport-layer-security-images/macos-mac.png#lightbox)

> [!WARNING]
> A próxima versão do Xamarin.Mac 4.8 dará suporte somente a macOS 10.9 ou posterior.
> As versões anteriores do Xamarin.Mac eram compatíveis com macOS 10.7 ou superior, mas essas versões mais antigas do macOS não têm infraestrutura TLS suficiente para dar suporte ao TLS 1.2. Para macOS 10.7 ou macOS 10.8, use o Xamarin.Mac 4.6 ou anterior.

## <a name="alternative-configuration-options"></a>Opções de configuração alternativas

Esta seção discute alternativas para as configurações com suporte do TLS 1,2 mostradas acima.
Os desenvolvedores de aplicativos só devem considerar essas alternativas se entenderem os riscos de usar diferentes níveis de suporte a TLS.

### <a name="httpclient-implementation"></a>Implementação de HttpClient

Os desenvolvedores do Xamarin sempre conseguiram usar as classes de rede nativas em seu código, no entanto, há também uma opção que determina qual pilha de `HttpClient` rede é usada pelas classes. Isso fornece uma API .NET familiar que tem as vantagens de velocidade e segurança da plataforma nativa.

As opções são:

- **Pilha gerenciada** – a funcionalidade de rede fornecida por mono ou
- **Pilha nativa** – várias APIs de rede fornecidas pelas plataformas subjacentes (Android, Ios ou MacOS).

A pilha gerenciada fornece o nível mais alto de compatibilidade com o código .NET existente, no entanto, pode ser mais lento e resultar em um tamanho de executável maior.

As opções nativas podem ser mais rápidas e ter mais segurança (incluindo o TLS 1,2), mas podem não fornecer toda a funcionalidade e `HttpClient` as opções da classe.

### <a name="ssltls-implementation-android"></a>Implementação de SSL/TLS (Android)

As opções de projeto do Android também permitem que você escolha a implementação de SSL/TLS com suporte:

- **Mono/gerenciado** – TLS 1,1 no Android
- **Nativo** – TLS 1,2 no Android.

Os novos projetos do Xamarin são padronizados para a implementação nativa que dá suporte a TLS 1,2 (que é recomendado para todos os projetos), no entanto, você pode alternar de volta para o código gerenciado, se necessário por motivos de compatibilidade.

> [!IMPORTANT]
> A opção **mono/Managed** foi removida [das opções de projeto do IOS e do Mac](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/xamarin.ios_10/xamarin.ios_10.8.md) .
>
> A opção nativa é sempre usada em plataformas iOS e Mac.

## <a name="platform-specific-details"></a>Detalhes específicos da plataforma

O resumo acima explica as configurações de nível de projeto para a implementação de HttpClient e SSL/TLS em projetos do Xamarin. A implementação de HttpClient também pode ser definida dinamicamente no código. Consulte estes guias específicos da plataforma para obter mais informações:

- [**Android**](~/android/app-fundamentals/http-stack.md)
- [**iOS e Mac**](~/cross-platform/macios/http-stack.md)

## <a name="summary"></a>Resumo

Os aplicativos devem usar o protocolo TLS 1,2 sempre que possível.
Você deve atualizar as configurações em aplicativos existentes de acordo com as instruções neste artigo e, em seguida, recompilar e reimplantar para seus clientes.

## <a name="related-links"></a>Links relacionados

- [Segurança de transporte de aplicativo](~/ios/app-fundamentals/ats.md)
- [Ambiente Xamarin. Android](~/android/deploy-test/environment.md)
- [Xamarin ciclo 9 (fevereiro de 2017)](https://releases.xamarin.com/stable-release-cycle-9/)
- [TLS (Wikipédia)](https://en.wikipedia.org/wiki/Transport_Layer_Security)
- [Notas de versão do mono 4,8 – suporte a TLS 1,2](https://www.mono-project.com/docs/about-mono/releases/4.8.0/#tls-12-support)
- [BoringSSL](https://boringssl.googlesource.com/boringssl/)
- [HttpClient, HttpClientHandler e WebRequestHandler explicados](https://blogs.msdn.microsoft.com/henrikn/2012/08/07/httpclient-httpclienthandler-and-webrequesthandler-explained/)
- [System.Net.HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.118).aspx)
- [System.Net.HttpClientHandler](https://msdn.microsoft.com/library/system.net.http.httpclienthandler(v=vs.118).aspx)
- [System.Net.HttpMessageHandler](https://msdn.microsoft.com/library/system.net.http.httpmessagehandler(v=vs.118).aspx)
- [System.Net.HttpWebRequest](https://msdn.microsoft.com/library/system.net.httpwebrequest(v=vs.110).aspx)
- [System.Net.WebClient](https://msdn.microsoft.com/library/system.net.webclient(v=vs.110).aspx)
- [System.Net.WebRequest](https://msdn.microsoft.com/library/system.net.webrequest(v=vs.110).aspx)
- [java.net.URLConnection](https://developer.android.com/reference/java/net/URLConnection.html)
- [Foundation.CFNetwork](xref:CoreFoundation.CFNetwork)
- [Foundation.NSUrlConnection](xref:Foundation.NSUrlConnection)
- [System.Net.WebRequest](https://msdn.microsoft.com/library/system.net.webrequest(v=vs.110).aspx)
- [Cliente HTTP (exemplo)](https://developer.xamarin.com/samples/monotouch/HttpClient/)
