---
title: Seletor de implementação de HttpClient Stack e SSL/TLS para Android
description: Os seletores de implementação de pilha HttpClient e SSL/TLS determinam a implementação de HttpClient e SSL/TLS que será usada por seus aplicativos Xamarin. Android.
ms.prod: xamarin
ms.assetid: D7ABAFAB-5CA2-443D-B902-2C7F3AD69CE2
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/20/2018
ms.openlocfilehash: f9f9b112a083615f9cf1d74d7cf81f5ca4f7901f
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73019243"
---
# <a name="httpclient-stack-and-ssltls-implementation-selector-for-android"></a>Seletor de implementação de HttpClient Stack e SSL/TLS para Android

Os seletores de implementação de pilha HttpClient e SSL/TLS determinam a implementação de HttpClient e SSL/TLS que será usada por seus aplicativos Xamarin. Android.

Os projetos devem referenciar o assembly do **System .net. http** .

> [!WARNING]
> **Abril de 2018** – devido a requisitos de segurança maiores, incluindo a conformidade com PCI, os principais provedores de nuvem e servidores Web devem parar de dar suporte a versões de TLS anteriores a 1,2. Os projetos do Xamarin criados em versões anteriores do Visual Studio usam o padrão para usar versões mais antigas do TLS.
>
> Para garantir que seus aplicativos continuem a trabalhar com esses servidores e serviços, **você deve atualizar seus projetos do Xamarin com as configurações `Android HttpClient` e `Native TLS 1.2` mostradas abaixo e, em seguida, recompilar e reimplantar seus aplicativos** para os usuários.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

A configuração HttpClient do Xamarin. Android está em **Opções do projeto > opções do Android**e, em seguida, clique no botão **Opções avançadas** .

Estas são as configurações recomendadas para o suporte a TLS 1,2:

[Opções do![Visual Studio Android](http-stack-images/android-win-sml.png)](http-stack-images/android-win.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

A configuração HttpClient do Xamarin. Android está em **Opções de projeto > compilar >** configurações de compilação do Android e clique na guia **geral** .

Estas são as configurações recomendadas para o suporte a TLS 1,2:

[Opções do![Visual Studio para Mac Android](http-stack-images/android-mac-sml.png)](http-stack-images/android-mac.png#lightbox)

-----

## <a name="alternative-configuration-options"></a>Opções de configuração alternativas

### <a name="androidclienthandler"></a>AndroidClientHandler

AndroidClientHandler é o novo manipulador que delega para código Java/OS nativo em vez de implementar tudo no código gerenciado.
**Essa é a opção recomendada.**

#### <a name="pros"></a>Prós

- Use a API nativa para melhorar o desempenho e o tamanho do executável menor.
- Suporte para os padrões mais recentes, por exemplo, TLS 1,2.

#### <a name="cons"></a>Contras

- Requer o Android 4,1 ou posterior.
- Alguns recursos/opções do HttpClient não estão disponíveis.

### <a name="managed-httpclienthandler"></a>Gerenciado (HttpClientHandler)

O manipulador gerenciado é o manipulador HttpClient totalmente gerenciado que foi enviado com versões anteriores do Xamarin. Android.

#### <a name="pros"></a>Prós

- Ele é o mais compatível (recursos) com o MS .NET e versões anteriores do Xamarin.

#### <a name="cons"></a>Contras

- Ele não está totalmente integrado ao sistema operacional (por exemplo, limitado a TLS 1,0).
- Normalmente, é muito mais lento (por exemplo, criptografia) do que a API nativa.
- Ele requer mais código gerenciado, criando aplicativos maiores.

### <a name="choosing-a-handler"></a>Escolhendo um manipulador

A escolha entre `AndroidClientHandler` e `HttpClientHandler` depende das necessidades do seu aplicativo. o `AndroidClientHandler` é recomendado para o suporte de segurança mais atualizado, por exemplo,

- Você precisa de suporte a TLS 1.2 +.
- Seu aplicativo está direcionando para o Android 4,1 (API 16) ou posterior.
- Você precisa de suporte a TLS 1.2 + para `HttpClient`.
- Você não precisa de suporte a TLS 1.2 + para `WebClient`.

`HttpClientHandler` é uma boa opção se você precisar de suporte a TLS 1.2 +, mas deve oferecer suporte a versões do Android anteriores ao Android 4,1. Também é uma boa opção se você precisar de suporte a TLS 1.2 + para `WebClient`.

A partir do Xamarin. Android 8,3, o `HttpClientHandler` assume como padrão o SSL enfadonho (`btls`) como o provedor TLS subjacente. O provedor de TLS SSL enfadonho oferece as seguintes vantagens:

- Ele dá suporte a TLS 1.2 +.
- Ele dá suporte a todas as versões do Android.
- Ele fornece suporte a TLS 1.2 + para `HttpClient` e `WebClient`.

A desvantagem de usar o SSL sem graça como o sob o provedor TLS é que ele pode aumentar o tamanho do APK resultante (ele adiciona cerca de 1MB de tamanho adicional de APK por ABI com suporte).

A partir do Xamarin. Android 8,3, o provedor TLS padrão é um SSL enfadonho (`btls`). Se você não quiser usar o SSL sem graça, poderá reverter para a implementação de SSL gerenciada por histórico definindo a propriedade `$(AndroidTlsProvider)` como `legacy` (para obter mais informações sobre como definir propriedades de compilação, consulte [processo de compilação](~/android/deploy-test/building-apps/build-process.md)).

### <a name="programatically-using-androidclienthandler"></a>Programaticamente usando `AndroidClientHandler`

O `Xamarin.Android.Net.AndroidClientHandler` é uma implementação de `HttpMessageHandler` especificamente para o Xamarin. Android.
As instâncias dessa classe usarão a implementação de `java.net.URLConnection` nativa para todas as conexões HTTP. Teoricamente, isso proporcionará um aumento no desempenho de HTTP e em tamanhos de APK menores.

Este trecho de código é um exemplo de como explicitamente para uma única instância da classe `HttpClient`:

```csharp
// Android 4.1 or higher, Xamarin.Android 6.1 or higher
HttpClient client = new HttpClient(new Xamarin.Android.Net.AndroidClientHandler ());
```

> [!NOTE]
> O dispositivo Android subjacente deve dar suporte a TLS 1,2 (IE. Android 4,1 e posterior). Observe que o suporte oficial para TLS 1,2 está no Android 5.0 +. No entanto, alguns dispositivos dão suporte a TLS 1,2 no Android 4.1 +.

## <a name="ssltls-implementation-build-option"></a>Opção de Build de implementação de SSL/TLS

Essa opção de projeto controla qual biblioteca TLS subjacente será usada por todas as solicitações da Web, `HttpClient` e `WebRequest`. Por padrão, o TLS 1,2 é selecionado:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[caixa de combinação de implementação de TLS/SSL do![no Visual Studio](http-stack-images/tls06-vs.png)](http-stack-images/tls05-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![caixa de combinação de implementação TLS/SSL no Visual Studio para Mac](http-stack-images/tls06-xs.png)](http-stack-images/tls05-xs.png#lightbox)

-----

Por exemplo:

```csharp
var client = new HttpClient();
```

Se a implementação de HttpClient foi definida como **gerenciada** e a implementação de TLS tiver sido definida para o **TLS 1.2 + nativo**, o objeto de `client` usará automaticamente o `HttpClientHandler` gerenciado e o TLS 1,2 (fornecido pela biblioteca BORINGSSL) para seu http pedido.

No entanto, se a **implementação de HttpClient** for definida como `AndroidHttpClient`, todos os objetos de `HttpClient` usarão a classe Java subjacente `java.net.URLConnection` e não serão afetados pelo valor de **implementação de TLS/SSL** . `WebRequest` objetos usaria a biblioteca BoringSSL.

## <a name="other-ways-to-control-ssltls-configuration"></a>Outras maneiras de controlar a configuração de SSL/TLS

Há três maneiras que um aplicativo Xamarin. Android pode controlar as configurações de TLS:

1. Selecione a implementação de HttpClient e a biblioteca TLS padrão nas opções do projeto.
2. Programaticamente usando `Xamarin.Android.Net.AndroidClientHandler`.
3. Declare variáveis de ambiente (opcional).

Das três opções, a abordagem recomendada é usar as opções de projeto do Xamarin. Android para declarar o `HttpMessageHandler` e o TLS padrão para todo o aplicativo. Em seguida, se necessário, crie programaticamente `Xamarin.Android.Net.AndroidClientHandler` objetos. Essas opções são descritas acima.

A terceira opção &ndash; usar variáveis de ambiente &ndash; é explicada abaixo.

### <a name="declare-environment-variables"></a>Declarar variáveis de ambiente

Há duas variáveis de ambiente relacionadas ao uso de TLS no Xamarin. Android:

- `XA_HTTP_CLIENT_HANDLER_TYPE` &ndash; essa variável de ambiente declara o `HttpMessageHandler` padrão que o aplicativo usará. Por exemplo:

    ```csharp
    XA_HTTP_CLIENT_HANDLER_TYPE=Xamarin.Android.Net.AndroidClientHandler
    ```

- `XA_TLS_PROVIDER` &ndash; essa variável de ambiente irá declarar qual biblioteca TLS será usada, `btls`, `legacy`ou `default` (que é o mesmo que omitir essa variável):

    ```csharp
    XA_TLS_PROVIDER=btls
    ```

Essa variável de ambiente é definida adicionando um _arquivo de ambiente_ ao projeto. Um arquivo de ambiente é um arquivo de texto simples formatado para UNIX com uma ação de compilação de **AndroidEnvironment**:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Captura de tela da ação de Build do AndroidEnvironment no Visual Studio.](http-stack-images/tls03-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![Captura de tela da ação de Build do AndroidEnvironment no Visual Studio para Mac.](http-stack-images/tls03-xs.png)

-----

Consulte o guia do [ambiente Xamarin. Android](~/android/deploy-test/environment.md) para obter mais detalhes sobre as variáveis de ambiente e Xamarin. Android.

## <a name="related-links"></a>Links relacionados

- [TLS (Transport Layer Security)](~/cross-platform/app-fundamentals/transport-layer-security.md)
