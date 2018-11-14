---
title: Pilha de HttpClient e seletor de implementação SSL/TLS para Android
description: Os seletores de pilha de HttpClient e implementação SSL/TLS determinam a implementação de HttpClient e SSL/TLS que será usada por seus aplicativos xamarin. Android.
ms.prod: xamarin
ms.assetid: D7ABAFAB-5CA2-443D-B902-2C7F3AD69CE2
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/20/2018
ms.openlocfilehash: 680fe2f8980d66b6dc80ec9a98898f9925df25f4
ms.sourcegitcommit: f3f28722198e172d81c16bdeab0cb0a581a08dd0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2018
ms.locfileid: "51598880"
---
# <a name="httpclient-stack-and-ssltls-implementation-selector-for-android"></a>Pilha de HttpClient e seletor de implementação SSL/TLS para Android

Os seletores de pilha de HttpClient e implementação SSL/TLS determinam a implementação de HttpClient e SSL/TLS que será usada por seus aplicativos xamarin. Android.

Projetos devem fazer referência a **HTTP** assembly.

> [!WARNING]
> **Abril de 2018** – devido a aumentar a segurança requisitos, incluindo a conformidade de PCI, principais provedores de nuvem e servidores web devem parar de dar suporte a TLS versões anteriores a 1.2.  Projetos do Xamarin criados em versões anteriores do padrão do Visual Studio para usar versões mais antigas do TLS.
>
> Para garantir que seus aplicativos continuem a trabalhar com esses servidores e serviços, **você deve atualizar seus projetos do Xamarin com o `Android HttpClient` e `Native TLS 1.2` configurações mostradas abaixo, em seguida, recriar e reimplantar seus aplicativos** para seu usuários.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

A configuração do xamarin. Android HttpClient está em **opções de projeto > Opções do Android**, em seguida, clique o **opções avançadas de** botão.

Estas são as configurações recomendadas para suporte a TLS 1.2:

[![Opções do Visual Studio Android](http-stack-images/android-win-sml.png)](http-stack-images/android-win.png#lightbox)


# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

A configuração do xamarin. Android HttpClient está em **opções de projeto > Build > Build do Android** as configurações e clique no **geral** guia.

Estas são as configurações recomendadas para suporte a TLS 1.2:

[![O Visual Studio para Mac opções do Android](http-stack-images/android-mac-sml.png)](http-stack-images/android-mac.png#lightbox)

-----

## <a name="alternative-configuration-options"></a>Opções de configuração alternativa

### <a name="androidclienthandler"></a>AndroidClientHandler

AndroidClientHandler é o novo manipulador que delega para o código de Java/sistema operacional nativo em vez de implementar tudo no código gerenciado.
**Essa é a opção recomendada.**

#### <a name="pros"></a>Profissionais

- Use a API nativa para melhorar o desempenho e o menor tamanho do executável.
- Suporte para os padrões mais recentes, por exemplo. TLS 1.2.

#### <a name="cons"></a>Contras

- Requer o Android 5.0 ou posterior.
- Alguns recursos do HttpClient/opções não estão disponíveis.

### <a name="managed-httpclienthandler"></a>Gerenciado (HttpClientHandler)

Manipulador gerenciado é o manipulador de HttpClient totalmente gerenciado que foi enviado com versões anteriores do xamarin. Android.

#### <a name="pros"></a>Profissionais

- É mais compatível (recursos) com o MS .NET e versões mais antigas do Xamarin.

#### <a name="cons"></a>Contras

- Ele não está totalmente integrado com o sistema operacional (por exemplo. limitado a TLS 1.0).
- É geralmente muito mais lenta (por exemplo. criptografia) que a API nativa.
- Ele requer mais código gerenciado, criando aplicativos maiores.



### <a name="choosing-a-handler"></a>Escolhendo um manipulador

A escolha entre `AndroidClientHandler` e `HttpClientHandler` varia de acordo com as necessidades do seu aplicativo. `AndroidClientHandler` é recomendado para o suporte de segurança mais recente, por exemplo.

-   Você precisa que dar suporte a TLS 1.2 +.
-   Seu aplicativo está definindo como destino o Android 5.0 (API 21) ou posterior.
-   É necessário o TLS 1.2 + suporte para `HttpClient`.
-   Você não precisa dar suporte a TLS 1.2 + para `WebClient`.

`HttpClientHandler` é uma boa opção se você precisar que o TLS 1.2 + suporte, mas deve dar suporte a versões do Android anteriores ao Android 5.0. Também é uma boa opção se você precisar que o TLS 1.2 + suporte para `WebClient`.

Começando com o xamarin. Android 8.3 `HttpClientHandler` Boring SSL o padrão é (`btls`) como o provedor subjacente do TLS. O provedor de chato SSL TLS oferece as seguintes vantagens:

-   Ele dá suporte a TLS 1.2 +.
-   Ele dá suporte a todas as versões do Android.
-   Ele fornece suporte a TLS 1.2 + para ambos `HttpClient` e `WebClient`.

A desvantagem de usar Boring SSL como o provedor TLS subjacente é que ela pode aumentar o tamanho do APK resultante (ele adiciona cerca de 1MB de tamanho adicional de APK por ABI com suporte).

Começando com o xamarin. Android 8.3, o provedor TLS padrão é Boring SSL (`btls`). Se você não quiser usar Boring SSL, você poderá reverter para a implementação de SSL gerenciada histórica, definindo o `$(AndroidTlsProvider)` propriedade para `legacy` (para obter mais informações sobre como definir propriedades de compilação, consulte [processo de compilação](~/android/deploy-test/building-apps/build-process.md)).


### <a name="programatically-using-androidclienthandler"></a>Usando de forma programática `AndroidClientHandler`

O `Xamarin.Android.Net.AndroidClientHandler` é um `HttpMessageHandler` implementação especificamente para xamarin. Android.
As instâncias dessa classe usará nativo `java.net.URLConnection` implementação para todas as conexões de HTTP. Teoricamente, isso fornecerá um aumento no desempenho de HTTP e tamanhos menores de APK.

Este trecho de código é um exemplo de como explicitamente para uma única instância da `HttpClient` classe:

```csharp
// Android 5.0 or higher, Xamarin.Android 6.1 or higher
HttpClient client = new HttpClient(new Xamarin.Android.Net.AndroidClientHandler ());
```

> [!NOTE]
> O dispositivo Android subjacente deve dar suporte a TLS 1.2 (ie. Android 5.0 e posterior)


## <a name="ssltls-implementation-build-option"></a>Opção de compilação de implementação SSL/TLS

Essa opção de projeto controla qual biblioteca TLS subjacente será usada por todas as solicitações da web, ambos `HttpClient` e `WebRequest`. Por padrão, o TLS 1.2 é selecionado:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Caixa de combinação de implementação de TLS/SSL no Visual Studio](http-stack-images/tls06-vs.png)](http-stack-images/tls05-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![Caixa de combinação de implementação de TLS/SSL no Visual Studio para Mac](http-stack-images/tls06-xs.png)](http-stack-images/tls05-xs.png#lightbox)

-----

Por exemplo:

```csharp
var client = new HttpClient();
```

Se a implementação de HttpClient foi definida como **gerenciado** e a implementação de TLS foi definida como **TLS 1.2 + nativo**, em seguida, a `client` objeto automaticamente usaria o gerenciado `HttpClientHandler` e TLS 1.2 (fornecidos pela biblioteca BoringSSL) para suas solicitações HTTP.

No entanto, se o **implementação de HttpClient** é definido como `AndroidHttpClient`, em seguida, todos `HttpClient` objetos usará a classe Java subjacente `java.net.URLConnection` e serão afetados pela **deimplementaçãodeTLS/SSL** valor. `WebRequest` objetos seriam usar a biblioteca de BoringSSL.

## <a name="other-ways-to-control-ssltls-configuration"></a>Outras maneiras de controlar a configuração de SSL/TLS

Há três maneiras que um aplicativo xamarin. Android pode controlar as configurações de TLS:

1. Selecione a biblioteca TLS de implementação e padrão do HttpClient nas opções do projeto.
2. Usando de forma programática `Xamarin.Android.Net.AndroidClientHandler`.
3. Declare as variáveis de ambiente (opcional).

Das três opções, a abordagem recomendada é usar as opções de projeto do xamarin. Android para declarar o padrão `HttpMessageHandler` e TLS para todo o aplicativo. Em seguida, se necessário, instanciar programaticamente `Xamarin.Android.Net.AndroidClientHandler` objetos. Essas opções são descritas acima.

A terceira opção &ndash; usando variáveis de ambiente &ndash; é explicado abaixo.

### <a name="declare-environment-variables"></a>Declare as variáveis de ambiente

Há duas variáveis de ambiente que estão relacionadas ao uso do TLS no xamarin. Android:

- `XA_HTTP_CLIENT_HANDLER_TYPE` &ndash; Essa variável de ambiente declara o padrão `HttpMessageHandler` que o aplicativo usará. Por exemplo:

    ```csharp
    XA_HTTP_CLIENT_HANDLER_TYPE=Xamarin.Android.Net.AndroidClientHandler
    ```

- `XA_TLS_PROVIDER` &ndash; Essa variável de ambiente irá declarar qual biblioteca TLS será usada, tanto `btls`, `legacy`, ou `default` (que é o mesmo que omitir essa variável):

    ```csharp
    XA_TLS_PROVIDER=btls
    ```

Essa variável de ambiente é definida com a adição de um _arquivo de ambiente_ ao projeto. Um arquivo de ambiente é um arquivo de texto sem formatação formato Unix com uma ação de build **AndroidEnvironment**:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Captura de tela da ação de compilação AndroidEnvironment no Visual Studio.](http-stack-images/tls03-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![Captura de tela do AndroidEnvironment ação de compilação no Visual Studio para Mac.](http-stack-images/tls03-xs.png)

-----

Consulte a [ambiente de xamarin. Android](~/android/deploy-test/environment.md) guia para obter mais detalhes sobre as variáveis de ambiente e o xamarin. Android.


## <a name="related-links"></a>Links relacionados

- [TLS (Transport Layer Security)](~/cross-platform/app-fundamentals/transport-layer-security.md)
