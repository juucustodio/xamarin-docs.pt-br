---
title: Transport Layer Security (TLS) 1.2
description: Este documento descreve como a habilitar o protocolo TLS 1.2 para projetos de xamarin, xamarin e Xamarin.Mac. Ele demonstra como fazer isso no 2017 do Visual Studio e o Visual Studio para Mac.
ms.prod: xamarin
ms.assetid: 399F71C6-16A4-4ABC-B30D-AF17D066A5FA
author: asb3993
ms.author: amburns
ms.date: 04/20/2018
ms.openlocfilehash: 6f27d7713f2fe6426fa28f268b8e97838893aa76
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34781391"
---
# <a name="transport-layer-security-tls-12"></a>Transport Layer Security (TLS) 1.2

Usando a versão mais recente do [ _Transport Layer Security_ (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security) é importante para garantir que as comunicações de rede do aplicativo são seguras.

> [!WARNING]
> **Abril de 2018** – devido a aumentar a segurança requisitos, incluindo conformidade PCI, principais provedores de nuvem e servidores web são esperados para interromper a dar suporte a TLS versões anteriores a 1.2.  Xamarin os projetos criados em versões anteriores do padrão do Visual Studio para usar versões mais antigas do TLS.
>
> Para garantir que seus aplicativos continuam a trabalhar com esses servidores e serviços, **devem atualizar seus projetos do Xamarin para usar as configurações a seguir, em seguida, recriar e reimplantar seus aplicativos** para seus usuários.

Projetos devem fazer referência a **System** assembly e ser configurado conforme mostrado abaixo.

## <a name="update-android-to-tls-12"></a>Atualizar Android ao TLS 1.2

Atualização de **HttpClient implementação** e **implementação SSL/TLS** opções para habilitar a segurança de protocolo TLS 1.2.

> [!NOTE]
> Requer Android 5.0 ou mais recente.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Essas configurações podem ser encontradas em **propriedades do projeto > Opções Android** e, em seguida, clicando no **avançado** botão:

[![Configurar HttpClient e TLS no Visual Studio](transport-layer-security-images/android-win-sml.png)](transport-layer-security-images/android-win.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Essas configurações podem ser encontradas em **opções de projeto > compilar > compilar Android** guia:

[![Configurar HttpClient e TLS no Visual Studio para Mac](transport-layer-security-images/android-mac-sml.png)](transport-layer-security-images/android-mac.png#lightbox)

-----

## <a name="update-ios-to-tls-12"></a>Atualização de iOS para TLS 1.2

Atualização de **HttpClient implementação** opção para habilitar a segurança TLS 1.2.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Essa configuração pode ser encontrada em **propriedades do projeto > iOS compilação**:

[![Configurar HttpClient e TLS no Visual Studio](transport-layer-security-images/ios-win-sml.png)](transport-layer-security-images/ios-win.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Essa configuração pode ser encontrada em **opções de projeto > compilar > iOS compilação** guia:

[![Configurar HttpClient no Visual Studio para Mac](transport-layer-security-images/ios-mac-sml.png)](transport-layer-security-images/ios-mac.png#lightbox)

-----

## <a name="update-macos-to-tls-12-in-visual-studio-for-mac"></a>Atualizar macOS para TLS 1.2 no Visual Studio para Mac

Atualização de **HttpClient implementação** opção **opções de projeto > compilar > Mac criar** guia para habilitar a segurança do TLS 1.2:

[![Configurar HttpClient no Visual Studio para Mac](transport-layer-security-images/macos-mac-sml.png)](transport-layer-security-images/macos-mac.png#lightbox)

## <a name="alternative-configuration-options"></a>Opções de configuração alternativa

Esta seção discute as alternativas para as configurações com suporte ao protocolo TLS 1.2 mostradas acima.
Os desenvolvedores de aplicativos devem ser consideradas apenas essas alternativas se compreender os riscos do uso de níveis diferentes de suporte do TLS.

### <a name="httpclient-implementation"></a>Implementação de HttpClient

Os desenvolvedores do Xamarin sempre tem sido capazes de usar as classes de rede nativo no seu código, no entanto, há também uma opção que determina quais pilha de rede é usada pelo `HttpClient` classes. Isso fornece uma API .NET familiarizado com as vantagens de segurança e velocidade da plataforma nativo.

As opções são:

- **Pilha gerenciada** – a funcionalidade de rede fornecida Mono, ou
- **Pilha nativa** – várias APIs fornecidos pelas plataformas subjacentes (Android, iOS ou macOS) de rede.

A pilha gerenciada fornece o mais alto nível de compatibilidade com o código existente do .NET, no entanto, ele pode ser mais lento e resultar em maior executável.

As opções nativo podem ser mais rápidas e melhor segurança (incluindo o protocolo TLS 1.2), mas não pode fornecer todos os recursos e opções do `HttpClient` classe.

### <a name="ssltls-implementation-android"></a>Implementação de TLS/SSL (Android)

Opções de projeto Android também permitem que você escolha qual implementação SSL/TLS para dar suporte a:

- **Mono/gerenciado** – TLS 1.1 no Android
- **Nativo** – TLS 1.2 no Android.

Novos projetos Xamarin padrão para a implementação nativo que dá suporte a TLS 1.2 (o que é recomendado para todos os projetos), porém, você pode alternar para o código gerenciado se necessário por razões de compatibilidade.

> [!IMPORTANT]
> O **Mono/gerenciado** opção foi [removido do iOS e Mac](https://developer.xamarin.com/releases/ios/xamarin.ios_10/xamarin.ios_10.8/) opções de projeto.
>
> A opção nativo é sempre usada em plataformas do iOS e Mac.

## <a name="platform-specific-details"></a>Detalhes específicos de plataforma

O resumo acima explica as configurações de nível de projeto para implementação HttpClient e SSL/TLS em projetos do Xamarin. A implementação de HttpClient também pode ser definida dinamicamente no código. Consulte esses guias específico da plataforma para obter mais informações:

- [**Android**](~/android/app-fundamentals/http-stack.md)
- [**iOS e Mac**](~/cross-platform/macios/http-stack.md)


## <a name="summary"></a>Resumo

Aplicativos devem usar a segurança de camada de transporte (TLS) 1.2, sempre que possível.
Você deve atualizar as configurações de acordo com as instruções neste artigo, os aplicativos existentes, em seguida, recriar e reimplantar a seus clientes.

## <a name="related-links"></a>Links relacionados

- [Segurança de transporte de aplicativo](~/ios/app-fundamentals/ats.md)
- [Ambiente de xamarin](~/android/deploy-test/environment.md)
- [Ciclo de Xamarin 9 (fevereiro de 2017)](https://releases.xamarin.com/stable-release-cycle-9/)
- [TLS (Wikipédia)](https://en.wikipedia.org/wiki/Transport_Layer_Security)
- [Notas de versão mono 4.8 - suporte a TLS 1.2](http://www.mono-project.com/docs/about-mono/releases/4.8.0/#tls-12-support)
- [BoringSSL](https://boringssl.googlesource.com/boringssl/)
- [HttpClient, HttpClientHandler e WebRequestHandler explicado](https://blogs.msdn.microsoft.com/henrikn/2012/08/07/httpclient-httpclienthandler-and-webrequesthandler-explained/)
- [System.Net.HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.118).aspx)
- [System.Net.HttpClientHandler](https://msdn.microsoft.com/library/system.net.http.httpclienthandler(v=vs.118).aspx)
- [System.Net.HttpMessageHandler](https://msdn.microsoft.com/library/system.net.http.httpmessagehandler(v=vs.118).aspx)
- [System.Net.HttpWebRequest](https://msdn.microsoft.com/library/system.net.httpwebrequest(v=vs.110).aspx)
- [System.Net.WebClient](https://msdn.microsoft.com/library/system.net.webclient(v=vs.110).aspx)
- [System.Net.WebRequest](https://msdn.microsoft.com/library/system.net.webrequest(v=vs.110).aspx)
- [java.net.URLConnection](http://developer.android.com/reference/java/net/URLConnection.html)
- [Foundation.CFNetwork](https://developer.xamarin.com/api/type/CoreFoundation.CFNetwork/)
- [Foundation.NSUrlConnection](https://developer.xamarin.com/api/type/Foundation.NSUrlConnection/)
- [System.Net.WebRequest](https://msdn.microsoft.com/library/system.net.webrequest(v=vs.110).aspx)
- [Cliente de HTTP (exemplo)](https://developer.xamarin.com/samples/monotouch/HttpClient/)
