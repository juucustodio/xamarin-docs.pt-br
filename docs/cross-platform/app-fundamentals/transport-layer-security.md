---
title: Transport Layer Security (TLS) 1.2
description: Este documento descreve como habilitar o TLS 1.2 para projetos xamarin. IOS, xamarin. Android e xamarin. Mac. Ele demonstra como fazer isso no Visual Studio 2017 e Visual Studio para Mac.
ms.prod: xamarin
ms.assetid: 399F71C6-16A4-4ABC-B30D-AF17D066A5FA
author: asb3993
ms.author: amburns
ms.date: 04/20/2018
ms.openlocfilehash: 3946a4ba190e1d8ba5c68a2d8ac36cf42900862f
ms.sourcegitcommit: a1a58afea68912c79d16a3f64de9a0c1feb2aeb4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55233972"
---
# <a name="transport-layer-security-tls-12"></a>Transport Layer Security (TLS) 1.2

Usando a versão mais recente do [ _Transport Layer Security_ (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security) é importante garantir que as comunicações de rede do aplicativo são seguras.

> [!WARNING]
> **Abril de 2018** – devido a aumentar a segurança requisitos, incluindo a conformidade de PCI, principais provedores de nuvem e servidores web devem parar de dar suporte a TLS versões anteriores a 1.2.  Projetos do Xamarin criados em versões anteriores do padrão do Visual Studio para usar versões mais antigas do TLS.
>
> Para garantir que seus aplicativos continuem a trabalhar com esses servidores e serviços, **você deve atualizar seus projetos do Xamarin para usar as configurações a seguir, em seguida, recriar e reimplantar seus aplicativos** para seus usuários.

Projetos devem fazer referência a **HTTP** assembly e ser configurados conforme mostrado abaixo.

## <a name="update-xamarinandroid-to-tls-12"></a>Atualizar o xamarin. Android para o TLS 1.2

Atualizar o **implementação de HttpClient** e **implementação SSL/TLS** opções para habilitar a segurança TLS 1.2.

> [!NOTE]
> Requer o Android 5.0 ou mais recente.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Essas configurações podem ser encontradas no **propriedades do projeto > Opções do Android** e, em seguida, clicando no **avançado** botão:

[![Configurar o HttpClient e TLS no Visual Studio](transport-layer-security-images/android-win-sml.png)](transport-layer-security-images/android-win.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Essas configurações podem ser encontradas no **opções de projeto > Build > Build do Android** guia:

[![Configurar o HttpClient e TLS no Visual Studio para Mac](transport-layer-security-images/android-mac-sml.png)](transport-layer-security-images/android-mac.png#lightbox)

-----

## <a name="update-xamarinios-to-tls-12"></a>Atualizar o xamarin. IOS para o TLS 1.2

Atualizar o **implementação de HttpClient** opção para habilitar a segurança TLS 1.2.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Essa configuração pode ser encontrada em **propriedades do projeto > Build do iOS**:

[![Configurar o HttpClient e TLS no Visual Studio](transport-layer-security-images/ios-win-sml.png)](transport-layer-security-images/ios-win.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Essa configuração pode ser encontrada em **opções de projeto > Build > Build do iOS** guia:

[![Configurar o HttpClient no Visual Studio para Mac](transport-layer-security-images/ios-mac-sml.png)](transport-layer-security-images/ios-mac.png#lightbox)

-----

## <a name="update-xamarinmac-to-tls-12"></a>Atualizar o xamarin. Mac para o TLS 1.2

No Visual Studio para Mac, para habilitar o TLS 1.2 em um aplicativo xamarin. Mac, atualize o **implementação de HttpClient** opção **opções de projeto > Build > Build do Mac**:

[![Configurar o HttpClient no Visual Studio para Mac](transport-layer-security-images/macos-mac-sml.png)](transport-layer-security-images/macos-mac.png#lightbox)

> [!WARNING]
> A próxima versão do Xamarin.Mac 4.8 dará suporte somente a macOS 10.9 ou posterior.
> As versões anteriores do Xamarin.Mac eram compatíveis com macOS 10.7 ou superior, mas essas versões mais antigas do macOS não têm infraestrutura TLS suficiente para dar suporte ao TLS 1.2. Para macOS 10.7 ou macOS 10.8, use o Xamarin.Mac 4.6 ou anterior.

## <a name="alternative-configuration-options"></a>Opções de configuração alternativa

Esta seção discute alternativas para as configurações com suporte do TLS 1.2 mostradas acima.
Os desenvolvedores de aplicativos devem considerar apenas estas alternativas se compreender os riscos do uso de diferentes níveis de suporte do TLS.

### <a name="httpclient-implementation"></a>Implementação de HttpClient

Os desenvolvedores de Xamarin sempre foram capazes de usar as classes nativas de rede em seu código, no entanto, há também uma opção que determina qual pilha de rede é usada pelo `HttpClient` classes. Isso fornece uma API familiares do .NET que tem as vantagens de velocidade e segurança da plataforma nativa.

As opções são:

- **Pilha gerenciada** – a funcionalidade de rede fornecido pelo Mono, ou
- **Pilha nativa** – várias APIs fornecidas pelo plataformas subjacentes (Android, iOS ou macOS) de rede.

A pilha gerenciada fornece o maior nível de compatibilidade com o código existente do .NET, no entanto, ele pode ser mais lento e resultar em maior tamanho do executável.

As opções nativas podem ser mais rápidas e ter uma melhor segurança (incluindo o TLS 1.2), mas pode não fornecer toda a funcionalidade e opções do `HttpClient` classe.

### <a name="ssltls-implementation-android"></a>Implementação SSL/TLS (Android)

Opções de projeto do Android também permitem que você escolha qual implementação SSL/TLS para dar suporte a:

- **Mono/gerenciados** – TLS 1.1 no Android
- **Nativo** – TLS 1.2 no Android.

Padrão de novos projetos do Xamarin à implementação nativa que dá suporte a TLS 1.2 (que é recomendado para todos os projetos), porém você pode alternar novamente para o código gerenciado se exigido por razões de compatibilidade.

> [!IMPORTANT]
> O **Mono/gerenciado** opção tiver sido [removido do iOS e Mac](https://developer.xamarin.com/releases/ios/xamarin.ios_10/xamarin.ios_10.8/) opções do projeto.
>
> A opção nativa é sempre usada em plataformas iOS e Mac.

## <a name="platform-specific-details"></a>Detalhes específicos da plataforma

O resumo acima explica as configurações de nível de projeto para a implementação de HttpClient e SSL/TLS em projetos do Xamarin. A implementação de HttpClient também pode ser definida dinamicamente no código. Esses guias específicos da plataforma para obter mais informações, consulte:

- [**Android**](~/android/app-fundamentals/http-stack.md)
- [**iOS e Mac**](~/cross-platform/macios/http-stack.md)

## <a name="summary"></a>Resumo

Aplicativos devem usar a segurança de camada de transporte (TLS) 1.2, sempre que possível.
Você deve atualizar as configurações de acordo com as instruções neste artigo, os aplicativos existentes e em seguida, recrie e implante novamente aos seus clientes.

## <a name="related-links"></a>Links relacionados

- [Segurança de transporte de aplicativo](~/ios/app-fundamentals/ats.md)
- [Xamarin.Android Environment](~/android/deploy-test/environment.md)
- [Xamarin Cycle 9 (fevereiro de 2017)](https://releases.xamarin.com/stable-release-cycle-9/)
- [TLS (Wikipédia)](https://en.wikipedia.org/wiki/Transport_Layer_Security)
- [Notas de versão do mono 4.8 - suporte a TLS 1.2](http://www.mono-project.com/docs/about-mono/releases/4.8.0/#tls-12-support)
- [BoringSSL](https://boringssl.googlesource.com/boringssl/)
- [HttpClient, HttpClientHandler e WebRequestHandler explicado](https://blogs.msdn.microsoft.com/henrikn/2012/08/07/httpclient-httpclienthandler-and-webrequesthandler-explained/)
- [System.Net.HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.118).aspx)
- [System.Net.HttpClientHandler](https://msdn.microsoft.com/library/system.net.http.httpclienthandler(v=vs.118).aspx)
- [System.Net.HttpMessageHandler](https://msdn.microsoft.com/library/system.net.http.httpmessagehandler(v=vs.118).aspx)
- [System.Net.HttpWebRequest](https://msdn.microsoft.com/library/system.net.httpwebrequest(v=vs.110).aspx)
- [System.Net.WebClient](https://msdn.microsoft.com/library/system.net.webclient(v=vs.110).aspx)
- [System.Net.WebRequest](https://msdn.microsoft.com/library/system.net.webrequest(v=vs.110).aspx)
- [java.net.URLConnection](http://developer.android.com/reference/java/net/URLConnection.html)
- [Foundation.CFNetwork](xref:CoreFoundation.CFNetwork)
- [Foundation.NSUrlConnection](xref:Foundation.NSUrlConnection)
- [System.Net.WebRequest](https://msdn.microsoft.com/library/system.net.webrequest(v=vs.110).aspx)
- [Cliente HTTP (amostra)](https://developer.xamarin.com/samples/monotouch/HttpClient/)
