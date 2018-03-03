---
title: Transport Layer Security (TLS)
description: Habilitando o TLS 1.2 para projetos do Xamarin no Android, iOS e Mac
ms.topic: article
ms.prod: xamarin
ms.assetid: 399F71C6-16A4-4ABC-B30D-AF17D066A5FA
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 10/10/2017
ms.openlocfilehash: 5237ed35116e5f8983df579d0ab68363996fb06f
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="transport-layer-security-tls"></a>Transport Layer Security (TLS)

_Habilitando o TLS 1.2 para projetos do Xamarin no Android, iOS e Mac_

Usando a versão mais recente do [ _Transport Layer Security_ (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security) é importante para garantir que as comunicações de rede do aplicativo são seguras.

> [!NOTE]
> Xamarin libera desde [fevereiro de 2017](https://releases.xamarin.com/stable-release-cycle-9/) usam o TLS 1.2 em novos projetos por padrão.

Suporte a TLS 1.2 agora está disponível em:

* Mono 4.8 (inclui [suporte a TLS 1.2](http://www.mono-project.com/docs/about-mono/releases/4.8.0/#tls-12-support))
* Xamarin.iOS
* Xamarin.Mac
* Xamarin (requer Android 5.0 ou mais recente)

Projetos devem fazer referência a **System** assembly. 

## <a name="updating-to-tls-12"></a>Atualizando para o TLS 1.2

Esta seção explica algumas das opções de configuração de rede em projetos de Xamarin, para que você possa atualizar seu _existente_ aplicativos para tirar proveito do protocolo mais seguro.


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Essas configurações podem ser encontradas em **opções de projeto > Opções Android** e, em seguida, clicando no **avançado** botão: 

[![Configurar HttpClient e TLS no Visual Studio](transport-layer-security-images/properties-vs-sml.png)](transport-layer-security-images/properties-vs.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)
Essas configurações podem ser encontradas em **propriedades do projeto > Opções de compilação > Avançado** guia:

[![Configurar HttpClient e TLS no Xamarin Studio e o Visual Studio para Mac](transport-layer-security-images/properties-xs-sml.png)](transport-layer-security-images/properties-xs.png)

-----


### <a name="httpclient-implementation"></a>Implementação de HttpClient

Os desenvolvedores do Xamarin sempre tem sido capazes de usar as classes de rede nativo no seu código, no entanto, há também uma opção que determina quais pilha de rede é usada pelo `HttpClient` classes. Isso fornece uma API .NET familiarizado com as vantagens de segurança e velocidade da plataforma nativo.

As opções são:

- **Pilha gerenciada** – a funcionalidade de rede fornecida Mono, ou
- **Pilha nativa** – várias APIs fornecidos pelas plataformas subjacentes (Android, iOS ou macOS) de rede.

A pilha gerenciada fornece o mais alto nível de compatibilidade com o código existente do .NET, no entanto, ele pode ser mais lento e resultar em maior executável.

As opções nativo podem ser mais rápidas e melhor segurança (incluindo o protocolo TLS 1.2), mas não pode fornecer todos os recursos e opções do `HttpClient` classe.


### <a name="ssltls-implementation"></a>Implementação de SSL/TLS

Opções de projeto também permitem que você escolha qual implementação SSL/TLS para dar suporte a:

- **Mono/gerenciado** – TLS 1.1 no Android, TLS 1.0 no iOS e macOS.
- **Nativo** – TLS 1.2 no Android, iOS e macOS.

Novos projetos Xamarin padrão para a implementação nativo que dá suporte a TLS 1.2 (o que é recomendado para todos os projetos), porém, você pode alternar para o código gerenciado se necessário por razões de compatibilidade.

> [!IMPORTANT]
> O **Mono/gerenciado** opção será removida em uma [versão futura](https://developer.xamarin.com/releases/ios/xamarin.ios_10/xamarin.ios_10.8/).
>
> A opção nativo é recomendada.

# <a name="platform-specific-details"></a>Detalhes específicos de plataforma

O resumo acima explica as configurações de nível de projeto para implementação HttpClient e SSL/TLS em projetos do Xamarin. A implementação de HttpClient também pode ser definida dinamicamente em código e no iOS há duas opções nativo à sua escolha.

- [**Android**](~/android/app-fundamentals/http-stack.md)
- [**iOS e Mac**](~/cross-platform/macios/http-stack.md)


# <a name="summary"></a>Resumo

Aplicativos devem usar a segurança de camada de transporte (TLS) 1.2, sempre que possível.
Novos aplicativos agora padrão para essa configuração, no entanto, talvez seja necessário atualizar as configurações em aplicativos existentes de acordo com as instruções neste artigo.

## <a name="related-links"></a>Links relacionados

- [Segurança de transporte do aplicativo](~/ios/app-fundamentals/ats.md)
- [Xamarin.Android Environment](~/android/deploy-test/environment.md)
- [Ciclo de Xamarin 9 (fevereiro de 2017)](https://releases.xamarin.com/stable-release-cycle-9/)
- [TLS (Wikipédia)](https://en.wikipedia.org/wiki/Transport_Layer_Security)
- [Notas de versão mono 4.8 - suporte a TLS 1.2](http://www.mono-project.com/docs/about-monohttps://developer.xamarin.com/releases/4.8.0/#tls-12-support)
- [BoringSSL](https://boringssl.googlesource.com/boringssl/)
- [HttpClient, HttpClientHandler e WebRequestHandler explicado](https://blogs.msdn.microsoft.com/henrikn/2012/08/07/httpclient-httpclienthandler-and-webrequesthandler-explained/)
- [System.Net.HttpClient](https://msdn.microsoft.com/en-us/library/system.net.http.httpclient(v=vs.118).aspx)
- [System.Net.HttpClientHandler](https://msdn.microsoft.com/en-us/library/system.net.http.httpclienthandler(v=vs.118).aspx)
- [System.Net.HttpMessageHandler](https://msdn.microsoft.com/en-us/library/system.net.http.httpmessagehandler(v=vs.118).aspx)
- [System.Net.HttpWebRequest](https://msdn.microsoft.com/en-us/library/system.net.httpwebrequest(v=vs.110).aspx)
- [System.Net.WebClient](https://msdn.microsoft.com/en-us/library/system.net.webclient(v=vs.110).aspx)
- [System.Net.WebRequest](https://msdn.microsoft.com/en-us/library/system.net.webrequest(v=vs.110).aspx)
- [java.net.URLConnection](http://developer.android.com/reference/java/net/URLConnection.html)
- [Foundation.CFNetwork](https://developer.xamarin.com/api/type/CoreFoundation.CFNetwork/)
- [Foundation.NSUrlConnection](https://developer.xamarin.com/api/type/Foundation.NSUrlConnection/)
- [System.Net.WebRequest](https://msdn.microsoft.com/en-us/library/system.net.webrequest(v=vs.110).aspx)
- [Cliente de HTTP (exemplo)](https://developer.xamarin.com/samples/monotouch/HttpClient/)
