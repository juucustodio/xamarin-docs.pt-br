---
title: Seletor de implementação de HttpClient Stack e SSL/TLS para Android
description: Os seletores de implementação de pilha HttpClient e SSL/TLS determinam a implementação de HttpClient e SSL/TLS que será usada por seus aplicativos Xamarin. Android.
ms.prod: xamarin
ms.assetid: D7ABAFAB-5CA2-443D-B902-2C7F3AD69CE2
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/20/2018
ms.openlocfilehash: 9f66764fef5c54563ffd03274b1f86a8c0bcc637
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69522145"
---
# <a name="httpclient-stack-and-ssltls-implementation-selector-for-android"></a>Seletor de implementação de HttpClient Stack e SSL/TLS para Android

Os seletores de implementação de pilha HttpClient e SSL/TLS determinam a implementação de HttpClient e SSL/TLS que será usada por seus aplicativos Xamarin. Android.

Os projetos devem referenciar o assembly do **System .net. http** .

> [!WARNING]
> **Abril de 2018** – devido a requisitos de segurança maiores, incluindo a conformidade com PCI, os principais provedores de nuvem e servidores Web devem parar de dar suporte a versões de TLS anteriores a 1,2. Os projetos do Xamarin criados em versões anteriores do Visual Studio usam o padrão para usar versões mais antigas do TLS.
>
> Para garantir que seus aplicativos continuem a trabalhar com esses servidores e serviços, **você deve atualizar seus projetos do Xamarin com `Android HttpClient` as `Native TLS 1.2` configurações do e mostradas abaixo e, em seguida, recompilar e reimplantar seus aplicativos** para os usuários.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

A configuração HttpClient do Xamarin. Android está em **Opções do projeto > opções do Android**e, em seguida, clique no botão **Opções avançadas** .

Estas são as configurações recomendadas para o suporte a TLS 1,2:

[![Opções do Visual Studio Android](http-stack-images/android-win-sml.png)](http-stack-images/android-win.png#lightbox)


# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

A configuração HttpClient do Xamarin. Android está em **Opções de projeto > compilar >** configurações de compilação do Android e clique na guia **geral** .

Estas são as configurações recomendadas para o suporte a TLS 1,2:

[![Opções do Visual Studio para Mac Android](http-stack-images/android-mac-sml.png)](http-stack-images/android-mac.png#lightbox)

-----

## <a name="alternative-configuration-options"></a>Opções de configuração alternativas

### <a name="androidclienthandler"></a>AndroidClientHandler

AndroidClientHandler é o novo manipulador que delega para código Java/OS nativo em vez de implementar tudo no código gerenciado.
**Essa é a opção recomendada.**

#### <a name="pros"></a>Prós

- Use a API nativa para melhorar o desempenho e o tamanho do executável menor.
- Suporte para os padrões mais recentes, por exemplo, TLS 1.2.

#### <a name="cons"></a>Contras

- Requer o Android 4,1 ou posterior.
- Alguns recursos/opções do HttpClient não estão disponíveis.

### <a name="managed-httpclienthandler"></a>Managed (HttpClientHandler)

O manipulador gerenciado é o manipulador HttpClient totalmente gerenciado que foi enviado com versões anteriores do Xamarin. Android.

#### <a name="pros"></a>Prós

- Ele é o mais compatível (recursos) com o MS .NET e versões anteriores do Xamarin.

#### <a name="cons"></a>Contras

- Ele não está totalmente integrado ao sistema operacional (por exemplo, limitado a TLS 1,0).
- Normalmente, é muito mais lento (por exemplo, criptografia) do que a API nativa.
- Ele requer mais código gerenciado, criando aplicativos maiores.



### <a name="choosing-a-handler"></a>Escolhendo um manipulador

A escolha entre `AndroidClientHandler` e `HttpClientHandler` depende das necessidades do seu aplicativo. `AndroidClientHandler`é recomendado para o suporte de segurança mais atualizado, por exemplo,

- Você precisa de suporte a TLS 1.2 +.
- Seu aplicativo está direcionando para o Android 4,1 (API 16) ou posterior.
- Você precisa de suporte a TLS 1.2 `HttpClient`+ para o.
- Você não precisa de suporte a TLS 1.2 `WebClient`+ para o.

`HttpClientHandler`é uma boa opção se você precisar de suporte a TLS 1.2 +, mas deve oferecer suporte a versões do Android anteriores ao Android 4,1. Também é uma boa opção se você precisar de suporte a TLS 1.2 + `WebClient`para o.

Começando com o Xamarin. Android 8,3 `HttpClientHandler` , o padrão é o SSL`btls`enfadonho () como o provedor TLS subjacente. O provedor de TLS SSL enfadonho oferece as seguintes vantagens:

- Ele dá suporte a TLS 1.2 +.
- Ele dá suporte a todas as versões do Android.
- Ele fornece suporte a TLS 1.2 + para `HttpClient` o `WebClient`e o.

A desvantagem de usar o SSL sem graça como o sob o provedor TLS é que ele pode aumentar o tamanho do APK resultante (ele adiciona cerca de 1MB de tamanho adicional de APK por ABI com suporte).

A partir do Xamarin. Android 8,3, o provedor TLS padrão é um SSL`btls`enfadonho (). Se você não quiser usar o SSL enfadonho, poderá reverter para a implementação de SSL gerenciada por histórico definindo a `$(AndroidTlsProvider)` Propriedade como `legacy` (para obter mais informações sobre como definir propriedades de compilação, consulte [processo de compilação](~/android/deploy-test/building-apps/build-process.md)).


### <a name="programatically-using-androidclienthandler"></a>Usando programaticamente`AndroidClientHandler`

O `Xamarin.Android.Net.AndroidClientHandler` é uma `HttpMessageHandler` implementação especificamente para o Xamarin. Android.
As instâncias dessa classe usarão a implementação nativa `java.net.URLConnection` para todas as conexões http. Teoricamente, isso proporcionará um aumento no desempenho de HTTP e em tamanhos de APK menores.

Este trecho de código é um exemplo de como explicitamente para uma única instância da `HttpClient` classe:

```csharp
// Android 4.1 or higher, Xamarin.Android 6.1 or higher
HttpClient client = new HttpClient(new Xamarin.Android.Net.AndroidClientHandler ());
```

> [!NOTE]
> O dispositivo Android subjacente deve dar suporte a TLS 1,2 (IE. Android 4,1 e posterior). Observe que o suporte oficial para TLS 1,2 está no Android 5.0 +. No entanto, alguns dispositivos dão suporte a TLS 1,2 no Android 4.1 +.


## <a name="ssltls-implementation-build-option"></a>Opção de Build de implementação de SSL/TLS

Essa opção de projeto controla qual biblioteca TLS subjacente será usada por todas as solicitações da Web `HttpClient` , `WebRequest`e. Por padrão, o TLS 1,2 é selecionado:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Caixa de combinação de implementação TLS/SSL no Visual Studio](http-stack-images/tls06-vs.png)](http-stack-images/tls05-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![Caixa de combinação de implementação de TLS/SSL no Visual Studio para Mac](http-stack-images/tls06-xs.png)](http-stack-images/tls05-xs.png#lightbox)

-----

Por exemplo:

```csharp
var client = new HttpClient();
```

Se a implementação de HttpClient tiver sido definida como gerenciada e a implementação de TLS tiver sido definida para o **TLS 1.2 + nativo**, o `client` objeto `HttpClientHandler` usará automaticamente o gerenciado e o TLS 1,2 (fornecido pela biblioteca BoringSSL) para seu Solicitações HTTP.

No entanto, se a **implementação** de HttpClient `AndroidHttpClient`for definida como `HttpClient` , todos os objetos usarão a classe `java.net.URLConnection` Java subjacente e não serão afetados pelo valor de **implementação de TLS/SSL** . `WebRequest`os objetos usariam a biblioteca BoringSSL.

## <a name="other-ways-to-control-ssltls-configuration"></a>Outras maneiras de controlar a configuração de SSL/TLS

Há três maneiras que um aplicativo Xamarin. Android pode controlar as configurações de TLS:

1. Selecione a implementação de HttpClient e a biblioteca TLS padrão nas opções do projeto.
2. Usando `Xamarin.Android.Net.AndroidClientHandler`programaticamente.
3. Declare variáveis de ambiente (opcional).

Das três opções, a abordagem recomendada é usar as opções de projeto do Xamarin. Android para declarar o padrão `HttpMessageHandler` e o TLS para todo o aplicativo. Em seguida, se necessário, crie `Xamarin.Android.Net.AndroidClientHandler` programaticamente objetos. Essas opções são descritas acima.

A terceira opção &ndash; que usa variáveis &ndash; de ambiente é explicada abaixo.

### <a name="declare-environment-variables"></a>Declarar variáveis de ambiente

Há duas variáveis de ambiente relacionadas ao uso de TLS no Xamarin. Android:

- `XA_HTTP_CLIENT_HANDLER_TYPE`Essa variável de ambiente declara o padrão `HttpMessageHandler` que o aplicativo usará. &ndash; Por exemplo:

    ```csharp
    XA_HTTP_CLIENT_HANDLER_TYPE=Xamarin.Android.Net.AndroidClientHandler
    ```

- `XA_TLS_PROVIDER`Essa variável de ambiente irá declarar qual biblioteca TLS será usada `btls` `legacy`, ou `default` (que é o mesmo que omitir essa variável): &ndash;

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
