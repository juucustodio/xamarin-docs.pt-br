---
title: Ambiente do Xamarin.Android
ms.topic: article
ms.prod: xamarin
ms.assetid: 67BFD4E1-276C-4B9F-9BD8-A5218D2BD529
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/15/2018
ms.openlocfilehash: 66f4dcf14cd179795e9a23bccabe4289d74c7c5b
ms.sourcegitcommit: d450ae06065d8f8c80f3588bc5a614cfd97b5a67
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/21/2018
---
# <a name="xamarinandroid-environment"></a>Ambiente do Xamarin.Android

## <a name="execution-environment"></a>Ambiente de execução

O *ambiente de execução* é o conjunto de variáveis de ambiente e propriedades do sistema Android que influenciam a execução do programa. As propriedades do sistema Android podem ser definidas com o comando `adb shell setprop`, enquanto as variáveis de ambiente podem ser configuradas definindo-se a propriedade do sistema `debug.mono.env`:

```shell
## Enable GREF logging
adb shell setprop debug.mono.log gref

## Set the MONO_LOG_LEVEL and MONO_LOG_MASK environment variables
## so that additional Mono messages will be written to `adb logcat`.
adb shell setprop debug.mono.env "'MONO_LOG_LEVEL=info|MONO_LOG_MASK=asm'"
```

As propriedades do sistema Android são definidas para todos os processos no dispositivo de destino.

Começando com o Xamarin.Android 4.6, as propriedades do sistema e as variáveis de ambiente podem ser definidas ou substituídas em uma base por aplicativo adicionando-se um *arquivo de ambiente* ao projeto. Um arquivo do ambiente é um arquivo de texto sem formatação em formato Unix com uma [**ação de build** de `AndroidEnvironment`](~/android/deploy-test/building-apps/build-process.md).
O arquivo de ambiente contém linhas com o formato *chave=valor*.
Os comentários são linhas que começam com `#`. Linhas em branco são ignoradas.

Se a *chave* começa com uma letra maiúscula, *chave* é tratada como uma variável de ambiente e **setenv**(3) é usado para definir a variável de ambiente para o *valor* especificado durante a inicialização do processo.

Se a *chave* começa com uma letra minúscula, a *chave* é tratada como uma propriedade do sistema Android e *valor* é o *valor padrão*: propriedades do sistema Android que controlam o comportamento de execução do Xamarin.Android são pesquisadas primeiro do repositório de propriedades do sistema Android e, se nenhum valor está presente, o valor especificado no arquivo de ambiente é usado. Isso é para permitir que `adb shell setprop` seja usado para substituir valores provenientes do arquivo de ambiente para fins de diagnóstico.

## <a name="xamarinandroid-environment-variables"></a>Variáveis de ambiente do Xamarin.Android

O Xamarin.Android é compatível com a variável `XA_HTTP_CLIENT_HANDLER_TYPE`, que pode ser definida por meio de `adb shell setprop debug.mono.env` ou por meio da ação de build `$(AndroidEnvironment)`.


### `XA_HTTP_CLIENT_HANDLER_TYPE`

O tipo qualificado por assembly que deve herdar de [HttpMessageHandler](https://docs.microsoft.com/dotnet/api/system.net.http.httpmessagehandler?view=xamarinandroid-7.1) e é construído por meio do [construtor padrão `HttpClient()`](https://docs.microsoft.com/dotnet/api/system.net.http.httpclient.-ctor?view=xamarinandroid-7.1#System_Net_Http_HttpClient__ctor).

No Xamarin.Android 6.1, essa variável de ambiente não é definida por padrão e [HttpClientHandler](https://docs.microsoft.com/dotnet/api/system.net.http.httpclienthandler?view=xamarinandroid-7.1) é usada.

Como alternativa, o valor `Xamarin.Android.Net.AndroidClientHandler` pode ser especificado para usar [`java.net.URLConnection`](https://developer.xamarin.com/api/type/Java.Net.URLConnection/) para acesso à rede, o que *pode* permitir o uso do TLS 1.2 quando o Android é compatível com ele.

Adicionado no Xamarin.Android 6.1.

## <a name="xamarinandroid-system-properties"></a>Propriedades do sistema Xamarin.Android

O Xamarin.Android é compatível com as seguintes variáveis do sistema, que podem ser definidas por meio de `adb shell setprop` ou por meio da ação de build `$(AndroidEnvironment)`.

* `debug.mono.debug`
* `debug.mono.env`
* `debug.mono.gc`
* `debug.mono.log`
* `debug.mono.max_grefc`
* `debug.mono.profile`
* `debug.mono.runtime_args`
* `debug.mono.trace`
* `debug.mono.wref`
* `XA_HTTP_CLIENT_HANDLER_TYPE`

### `debug.mono.debug`

O valor da propriedade do sistema `debug.mono.debug` é um inteiro. Se `1`, comporte-se "como se" o processo começasse com `mono --debug`.
Geralmente, isso mostra informações de arquivo e de linha em rastreamentos de pilha, etc., sem a necessidade de o aplicativo ser iniciado de um depurador.

### `debug.mono.env`

Contém uma lista de variáveis de ambiente separadas por `|`.

### `debug.mono.gc`

O valor da propriedade do sistema `debug.mono.debug` é um inteiro.
Se `1`, as informações de GC devem ser registradas.

Isso é equivalente a fazer com que propriedade do sistema `debug.mono.log` contenha `gc`.

### `debug.mono.log`

Controla quais informações adicionais o Xamarin.Android registrará em `adb logcat`.
É uma cadeia de caracteres separada por vírgulas (`,`), que contém um dos seguintes valores:

* `all`: imprimir *todas* as mensagens. Isso raramente é uma boa ideia, pois inclui mensagens `lref`.
* `assembly`: imprimir mensagens de análise de assembly e `.apk`.
* `gc`: imprimir mensagens relacionadas a GC.
* `gref`: imprimir mensagens de referência global de JNI.
* `lref`: imprimir mensagens de referência local de JNI.  
    *Observação*: isso *realmente* enviará spam para `adb logcat`.  
    No Xamarin.Android 5.1, isso também criará um arquivo `.__override__/lrefs.txt`, que pode ficar *gigantesco*.  
    Evite isso.
* `timing`: imprimir algumas informações de tempo de método. Isso também criará os arquivos `.__override__/methods.txt` e `.__override__/counters.txt`.


### `debug.mono.max_grefc`

O valor da propriedade do sistema `debug.mono.max_grefc` é um inteiro.
O valor dela *substitui* a contagem máxima de GREF padrão detectada para o dispositivo de destino.

*Observação:* isso só pode ser usado com `adb shell setprop
debug.mono.max_grefc`, pois o valor não estará disponível em tempo com um arquivo **environment.txt**.

### `debug.mono.profile`

A propriedade do sistema `debug.mono.profile` habilita o criador de perfil.
Ele equivale à opção `mono --profile` e usa os mesmos valores que ela. (Consulte a página de manual do [**mono**(1)](http://docs.go-mono.com/?link=man%3amono(1)) para obter mais informações.)

### `debug.mono.runtime_args`

A propriedade do sistema `debug.mono.runtime_args` contém opções adicionais que devem ser analisadas por **mono**.

### `debug.mono.trace`

A propriedade do sistema `debug.mono.trace` habilita o rastreamento.
Ele equivale à opção `mono --trace` e usa os mesmos valores que ela. (Consulte a página de manual do [**mono**(1)](http://docs.go-mono.com/?link=man%3amono(1)) para obter mais informações.)

Em geral, a recomendação é *não usar*. O uso de rastreamento enviará a saída `adb logcat` como spam, deixando o comportamento do programa severamente mais lento; além disso, ele alterará o comportamento do programa (até mesmo incluindo a adição de condições de erro adicionais).

*Às vezes*, no entanto, ele permite que alguma investigação adicional seja executada...

### `debug.mono.wref`

A propriedade do sistema `debug.mono.wref` permite substituir o mecanismo de referência fraca JNI padrão detectado. Há dois valores compatíveis:

* `jni`: use referências fracas JNI, conforme criadas pelo `JNIEnv::NewWeakGlobalRef()` e destruídas por `JNIEnv::DeleteWeakGlobalREf()`.
* `java`: use referências globais JNI que referenciam instâncias de `java.lang.WeakReference`.

`java` é usado, por padrão, até a API-7 e na API-19 (Kit Kat) com ART habilitado. (A API-8 adicionou referências `jni` e o ART *interrompeu* `jni` referências.)

Essa propriedade do sistema é útil para testes e para certas formas de investigação.
*Em geral*, ela não deve ser alterada.

### <a name="xahttpclienthandlertype"></a>XA\_HTTP\_CLIENT\_HANDLER\_TYPE

Introduzida pela primeira vez no Xamarin.Android 6.1, essa variável de ambiente declara a implementação `HttpMessageHandler` padrão que será usada pelo `HttpClient`. Por padrão, essa variável não é definida e o Xamarin.Android usa a `HttpClientHandler`.

```shell
XA_HTTP_CLIENT_HANDLER_TYPE=Xamarin.Android.Net.AndroidClientHandler
```

> [!NOTE]
> O dispositivo Android subjacente deve ser compatível com TLS 1.2.
O Android 5.0 e versões posteriores dão suporte à TLS 1.2


## <a name="example"></a>Exemplo

```shell
## Comments are lines which start with '#'
## Blank lines are ignored.


## Enable GREF messages to `adb logcat`
debug.mono.log=gref

## Clear out a Mono environment variable to decrease logging
MONO_LOG_LEVEL=
```



## <a name="related-links"></a>Links relacionados

- [Transport Layer Security](~/cross-platform/app-fundamentals/transport-layer-security.md)
