---
title: "Pilha de HttpClient e seletor de implementação de SSL/TLS para Android"
description: "Os seletores de pilha HttpClient e implementação de SSL/TLS determinam a implementação de HttpClient e SSL/TLS que será usada por seus aplicativos xamarin."
ms.topic: article
ms.prod: xamarin
ms.assetid: D7ABAFAB-5CA2-443D-B902-2C7F3AD69CE2
ms.technology: xamarin-android
author: topgenorth
ms.author: toopge
ms.date: 03/09/2018
ms.openlocfilehash: 5c63bda11a57c0f27efa1db6f0455b25f7da531b
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2018
---
# <a name="httpclient-stack-and-ssltls-implementation-selector-for-android"></a>Pilha de HttpClient e seletor de implementação de SSL/TLS para Android

_Os seletores de pilha HttpClient e implementação de SSL/TLS determinam a implementação de HttpClient e SSL/TLS que será usada por seus aplicativos xamarin._

## <a name="overview"></a>Visão geral

Xamarin fornece duas caixas de combinação que irão controlar as configurações de TLS para um aplicativo do Android. Uma caixa de combinação que identificará `HttpMessageHandler` usará ao instanciar um `HttpClient` do objeto, enquanto o outro identifica qual implementação de TLS será usada por solicitações da web.

> [!NOTE]
> Projetos devem fazer referência a **System** assembly.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

As configurações para a pilha de HttpClient são encontradas nas opções de projeto para um projeto xamarin. Clique no **opções Android** guia e, em seguida, clique no **opções avançadas** botão. Isso exibirá a **opções avançadas de Android** caixa de diálogo que tem caixas de combinação de dois, uma para a implementação de HttpClient e outra para a implementação de SSL/TLS:


[![Opções de Android do Visual Studio](http-stack-images/tls07-vs-sml.png)](http-stack-images/tls07-vs.png#lightbox)

## <a name="httpclient-stack-selector"></a>Seletor de pilha HttpClient

Essa opção de projeto controla qual `HttpMessageHandler` implementação será usada sempre que um `HttpClient` objeto é instanciado. Por padrão, isso é gerenciado `HttpClientHandler`.

[![Caixa de combinação de implementação HttpClient Android no Visual Studio](http-stack-images/tls04-vs-sml.png)](http-stack-images/tls04-vs.png#lightbox) 


# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

As configurações para a pilha de HttpClient são encontradas nas opções de projeto para um projeto xamarin. Clique no **Construir > compilar Android** configurações e clique no **geral** guia:

[![O Visual Studio para Mac opções de Android](http-stack-images/tls07-xs-sml.png)](http-stack-images/tls07-xs.png#lightbox)

## <a name="httpclient-stack-selector"></a>Seletor de pilha HttpClient

Essa opção de projeto controla qual `HttpMessageHandler` implementação será usada sempre que um `HttpClient` objeto é instanciado. Por padrão, isso é gerenciado `HttpClientHandler`.

![Caixa de combinação de implementação HttpClient Android no Visual Studio para Mac](http-stack-images/tls04-xs.png )

-----

### <a name="managed-httpclienthandler"></a>Managed (HttpClientHandler)

Manipulador gerenciado é o manipulador de HttpClient totalmente gerenciado que é enviado com versões anteriores do xamarin.

#### <a name="pros"></a>Prós:

- É mais compatível (recursos) com MS .NET e versões mais antigas do Xamarin.

#### <a name="cons"></a>Contras:

- Ele não está totalmente integrado com o sistema operacional (por exemplo. limitado a TLS 1.0).
- É geralmente muito mais lento (por exemplo. criptografia) de API nativa.
- Ele exige mais código gerenciado, criar aplicativos maiores.

### <a name="androidclienthandler"></a>AndroidClientHandler

AndroidClientHandler é o novo manipulador que delega para o código Java/OS nativo em vez de implementar tudo no código gerenciado.

#### <a name="pros"></a>Prós:

- Use a API nativa para melhor desempenho e o menor tamanho executável.
- Suporte para os padrões mais recentes, por exemplo. TLS 1.2.

#### <a name="cons"></a>Contras:

- Requer Android 5.0 ou posterior.
- Alguns recursos de HttpClient/opções não estão disponíveis.

### <a name="choosing-a-handler"></a>Escolhendo um manipulador

A escolha entre `AndroidClientHandler` e `HttpClientHandler` depende das necessidades do seu aplicativo. `AndroidClientHandler` é uma boa opção se todos os itens a seguir se aplicam:

-   Você precisa que dar suporte a TLS 1.2 +.
-   Seu aplicativo está direcionando Android 5.0 (API 21) ou posterior.
-   É necessário o TLS 1.2 + suporte para `HttpClient`.
-   Não é necessário para suporte a TLS 1.2 + `WebClient`.

`HttpClientHandler` é uma boa opção se você precisar de TLS 1.2 + suporte, mas deve oferecer suporte a versões anteriores ao Android 5.0 do Android. Também é uma boa opção se você precisar de TLS 1.2 + suporte para `WebClient`.

Começando com o xamarin 8.3, `HttpClientHandler` padrões chato SSL (`btls`) como o provedor subjacente do TLS. O provedor chato SSL TLS oferece as seguintes vantagens:

-   Ele dá suporte a TLS 1.2.
-   Ele dá suporte a todas as versões de Android.
-   Ele fornece suporte a TLS 1.2 para ambos `HttpClient` e `WebClient`.

A desvantagem do uso de SSL chato como o provedor TLS subjacente é que ele pode aumentar o tamanho do APK resultante (adiciona cerca de 1MB de tamanho APK adicional por ABI com suporte).

Começando com o xamarin 8.3, o provedor TLS padrão é chato SSL (`btls`). Se você não quiser usar SSL chato, você poderá reverter para a implementação de SSL gerenciada histórica definindo o `$(AndroidTlsProvider)` propriedade `legacy` (para obter mais informações sobre propriedades de configuração de compilação, consulte [processo de compilação](~/android/deploy-test/building-apps/build-process.md)).


### <a name="programatically-using-androidclienthandler"></a>Uso de maneira programática `AndroidClientHandler`

O `Xamarin.Android.Net.AndroidClientHandler` é um `HttpMessageHandler` implementação especificamente para xamarin.
As instâncias dessa classe usará nativo `java.net.URLConnection` implementação para todas as conexões de HTTP. Isso fornecerá um aumento no desempenho de HTTP e os tamanhos menores de APK teoricamente.

Este trecho de código é um exemplo de como explicitamente para uma única instância de `HttpClient` classe:

```csharp
// Android 5.0 or higher, Xamarin.Android 6.1 or higher
HttpClient client = new HttpClient(new Xamarin.Android.Net.AndroidClientHandler ());
```

> [!NOTE]
> O dispositivo Android subjacente deve dar suporte a TLS 1.2 (ie. Android 5.0 e versões posterior)


## <a name="ssltls-implementation-build-option"></a>Opção de compilação de implementação de SSL/TLS

Essa opção de projeto controla quais biblioteca TLS subjacente será usada por todas as solicitações da web, ambos `HttpClient` e `WebRequest`. Por padrão, o TLS 1.2 é selecionado:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Caixa de combinação de implementação de TLS/SSL no Visual Studio](http-stack-images/tls06-vs.png)](http-stack-images/tls05-vs.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Caixa de combinação de implementação de TLS/SSL no Visual Studio para Mac](http-stack-images/tls06-xs.png)](http-stack-images/tls05-xs.png#lightbox)

-----

Por exemplo:

```csharp
var client = new HttpClient();
```

Se a implementação de HttpClient foi definida como **gerenciado** e a implementação de TLS foi definida como **nativo TLS 1.2 +**, o `client` objeto automaticamente usaria gerenciado `HttpClientHandler` e TLS 1.2 (fornecido pela biblioteca BoringSSL) para suas solicitações HTTP.

No entanto, se o **HttpClient implementação** é definido como `AndroidHttpClient`, em seguida, todos os `HttpClient` objetos usará a classe base do Java `java.net.URLConnection` e serão afetados pelo **deimplementaçãodeTLS/SSL** valor. `WebRequest` objetos seriam usar a biblioteca de BoringSSL.

## <a name="other-ways-to-control-ssltls-configuration"></a>Outras maneiras de controlar a configuração de SSL/TLS

Há três maneiras que um aplicativo xamarin pode controlar as configurações de TLS:

1. Selecione a biblioteca TLS de implementação e padrão de HttpClient nas opções de projeto.
2. Programaticamente usando `Xamarin.Android.Net.AndroidClientHandler`.
3. Declare variáveis de ambiente (opcional).

Três opções, a abordagem recomendada é usar as opções de projeto xamarin para declarar o padrão `HttpMessageHandler` e TLS para o aplicativo inteiro. Em seguida, se necessário, instanciar programaticamente `Xamarin.Android.Net.AndroidClientHandler` objetos. Essas opções são descritas acima.

A terceira opção &ndash; usando variáveis de ambiente &ndash; são explicados abaixo.

### <a name="declare-environment-variables"></a>Declare variáveis de ambiente

Há duas variáveis de ambiente que estão relacionadas ao uso de TLS em xamarin:

-   `XA_HTTP_CLIENT_HANDLER_TYPE` &ndash; Essa variável de ambiente declara o padrão `HttpMessageHandler` que o aplicativo usará. Por exemplo:

    ```csharp
    XA_HTTP_CLIENT_HANDLER_TYPE=Xamarin.Android.Net.AndroidClientHandler
    ```

-   `XA_TLS_PROVIDER` &ndash; Essa variável de ambiente irá declarar que biblioteca TLS será usada, ou `btls`, `legacy`, ou `default` (que é o mesmo que omitir essa variável):

    ```csharp
    XA_TLS_PROVIDER=btls
    ```

Essa variável de ambiente é definida adicionando um _arquivo do ambiente_ ao projeto. Um arquivo do ambiente é um arquivo de texto sem formatação Unix formatado com uma ação de compilação de **AndroidEnvironment**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Captura de tela da ação de compilação AndroidEnvironment no Visual Studio.](http-stack-images/tls03-vs.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

![Captura de tela do AndroidEnvironment de compilação no Visual Studio para Mac.](http-stack-images/tls03-xs.png)

-----

Consulte o [xamarin ambiente](~/android/deploy-test/environment.md) guia para obter mais detalhes sobre as variáveis de ambiente e xamarin.


## <a name="related-links"></a>Links relacionados

- [TLS (Transport Layer Security)](~/cross-platform/app-fundamentals/transport-layer-security.md)
