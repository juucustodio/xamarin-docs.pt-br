---
title: "Dispositivos de vários núcleos e o Xamarin.Android"
description: "O Android pode executar em diversas arquiteturas de computador diferentes. Este documento aborda as arquiteturas de CPU diferentes que podem ser utilizadas em um aplicativo Xamarin.Android. Este documento também explica como aplicativos Android são empacotados para dar suporte a diferentes arquiteturas de CPU. A ABI (interface binária de aplicativo) é apresentada e são fornecidas diretrizes sobre quais ABIs para usar em um aplicativo Xamarin.Android."
ms.topic: article
ms.prod: xamarin
ms.assetid: D812883C-A14A-E74B-0F72-E50071E96328
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/05/2018
ms.openlocfilehash: 2a7b2a856d51447d6b7ab2032ebf7445d3f06ecb
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="multi-core-devices--xamarinandroid"></a>Dispositivos de vários núcleos e o Xamarin.Android

_O Android pode executar em diversas arquiteturas de computador diferentes. Este documento aborda as arquiteturas de CPU diferentes que podem ser utilizadas em um aplicativo Xamarin.Android. Este documento também explica como aplicativos Android são empacotados para dar suporte a diferentes arquiteturas de CPU. A ABI (interface binária de aplicativo) é apresentada e são fornecidas diretrizes sobre quais ABIs para usar em um aplicativo Xamarin.Android._

<a name="Overview" />

## <a name="overview"></a>Visão geral

O Android permite a criação de binários multiarquitetura, um único arquivo `.apk` que contém o código de computador que é compatível com várias arquiteturas de CPU diferentes. Isso é feito associando-se cada trecho de código de computador com uma *interface binária de aplicativo*. A ABI será usada para controlar qual código de computador será executado em um dispositivo de hardware específico. Por exemplo, para um aplicativo do Android ser executado em um dispositivo x86, é necessário incluir suporte a ABI x86 ao compilar o aplicativo.

Especificamente, cada aplicativo do Android dará suporte a pelo menos uma EABI (*interface binária de aplicativo incorporado*). EABIs são convenções específicas para programas de software inseridos. Um EABI típico descreverá coisas como:

-   O conjunto de instruções de CPU.

-   A ordenação de bits de armazenamentos e carregamentos de memória em tempo de execução.

-   O formato binário dos arquivos de objeto e bibliotecas do programa, bem como qual tipo de conteúdo é permitido ou compatível com esses arquivos e bibliotecas.

-   As convenções de vários usadas para passar dados entre o código do aplicativo e o sistema (por exemplo: como os registros e/ou a pilha são usados quando as funções são chamadas, restrições de alinhamento, etc.)

-   Restrições de tamanho e de alinhamento para tipos de enumeração, estruturas, campos e matrizes.

-   A lista de símbolos de função disponíveis para seu código de computador em tempo de execução, geralmente de um conjunto de bibliotecas selecionado muito específico.



### <a name="armeabi-and-thread-safety"></a>armeabi e acesso thread-safe

A interface binária de aplicativo será discutida em detalhes abaixo, mas é importante lembrar que o tempo de execução `armeabi` usado pelo Xamarin.Android *não é thread-safe*. Se um aplicativo compatível com `armeabi` é implantado em um dispositivo `armeabi-v7a`, muitas exceções estranhas e inexplicáveis ocorrerão.

Devido a um bug no Android 4.0.0 4.0.1, 4.0.2 e 4.0.3, as bibliotecas nativas serão retiradas do diretório `armeabi` mesmo que haja um diretório `armeabi-v7a` presente e que se trate de um dispositivo `armeabi-v7a`.

> [!NOTE]
> **Observação**: o Xamarin.Android garantirá que `.so` sejam adicionados ao APK na ordem correta. Esse bug não deve ser um problema para os usuários do Xamarin.Android.

<a name="ABI_Descriptions" />

### <a name="abi-descriptions"></a>Descrições de ABIs

Cada ABI compatível com o Android é identificada por um nome exclusivo.


<a name="armeabi" />

#### <a name="armeabi"></a>armeabi

Esse é o nome de uma EABI para CPUs baseadas em ARM, compatível com pelo menos o conjunto de instruções ARMv5TE. O Android segue a ABI little endian ARM GNU/Linux. Esta ABI não é compatível com cálculos de ponto flutuante assistidos por hardware. Todas as operações de FP são executadas por funções auxiliares de software que são provenientes da biblioteca estática `libgcc.a` do compilador. Os dispositivos SMP não são compatíveis com o `armeabi`.

**Observação**: o código `armeabi` do Xamarin.Android não é thread-safe e não deve ser usado em dispositivos `armeabi-v7a`com várias CPUs (descritos abaixo). O uso de código `aremabi` em um dispositivo `armeabi-v7a` de núcleo único é seguro.


<a name="armeabi-v7a" />

#### <a name="armeabi-v7a"></a>armeabi-v7a

Este é outro conjunto de instruções de CPU baseada em ARM que estende o EABI `armeabi` descrito acima. O EABI `armeabi-v7a` é compatível com operações de ponto flutuante de hardware e com dispositivos com várias CPUs (SMP). Um aplicativo que usa a EABI `armeabi-v7a` pode esperar melhorias substanciais de desempenho em relação a um aplicativo que usa `armeabi`.

**Observação:** o código de computador `armeabi-v7a` não será executado em dispositivos ARMv5.


<a name="arm64-v8a" />

#### <a name="arm64-v8a"></a>arm64-v8a

Este é um conjunto de instruções de 64 bits com base na arquitetura de CPU ARMv8. Essa arquitetura é usada no *Nexus 9*.
O Xamarin.Android 5.1 dá suporte experimental a essa arquitetura (para obter mais informações, consulte [Recursos experimentais](https://developer.xamarin.com/releases/android/xamarin.android_5/xamarin.android_5.1/#Experimental_Features)).


<a name="x86" />

#### <a name="x86"></a>x86

Este é o nome de uma ABI para CPUs compatíveis com o conjunto de instruções normalmente nomeado *x86* ou *IA-32*. Este ABI corresponde às instruções para o conjunto de instruções Pentium Pro, incluindo os conjuntos de instruções MMX, SSE, SSE2 e SSE3. Ele não inclui nenhuma outra extensão de conjunto de instruções IA-32 opcional, por exemplo:

-  a instrução MOVBE.
-  extensão SSE3 complementar (SSSE3).
-  qualquer variante de SSE4.


**Observação:** o Google TV, embora seja executado em x86, não é compatível com o NDK do Android ou com o Xamarin.Android. <a name="mips" />


<a name="x86_64" />

#### <a name="x8664"></a>x86_64

Este é o nome de uma ABI para CPUs compatíveis com o conjunto de instruções x86 de 64 bits (também conhecido como *x64* ou *AMD64*). O Xamarin.Android 5.1 dá suporte experimental a essa arquitetura (para obter mais informações, consulte [Recursos experimentais](https://developer.xamarin.com/releases/android/xamarin.android_5/xamarin.android_5.1/#Experimental_Features)).


#### <a name="mips"></a>mips

Esse é o nome de uma ABI para CPUs baseadas em MIPS, compatível com pelo menos o conjunto de instruções `MIPS32r1`. Nem MIPS 16 nem `micromips` são compatíveis com o Android.

**Observação:** dispositivos MIPS atualmente não são compatíveis com o Xamarin.Android, mas serão em uma versão futura.


<a name="APK_File_Format" />

#### <a name="apk-file-format"></a>Formato de Arquivo APK

O pacote do aplicativo Android é o formato de arquivo que contém todos os códigos, ativos, recursos, e certificados necessários para um aplicativo Android. É um arquivo `.zip`, mas usa a extensão de nome de arquivo `.apk`. Quando expandido, o conteúdo de um `.apk` criado pelo Xamarin.Android pode ser visto na captura de tela abaixo:

[ ![Conteúdo do .apk](multicore-devices-images/00.png)](multicore-devices-images/00.png)

Uma breve descrição do conteúdo do arquivo `.apk`:

-   **AndroidManifest.xml** &ndash; esse é o arquivo `AndroidManifest.xml` em formato XML binário.

-   **classes.dex** &ndash; contém o código do aplicativo compilado para o formato de arquivo `dex`, que é usado pela VM do tempo de execução do Android.

-   **resources.ARSC** &ndash; esse arquivo contém todos os recursos pré-compilados para o aplicativo.

-   **lib** &ndash; esse diretório contém o código compilado para cada ABI. Ele contém uma subpasta para cada ABI que foi descrita na seção anterior. Na captura de tela acima, o `.apk` em questão tem bibliotecas nativas para ambos `armeabi-v7a` e `x86`.

-   **META-INF** &ndash; esse diretório (se presente) é usado para armazenar informações de assinatura, o pacote e dados de configuração de extensão.

-   **res** &ndash; esse diretório contém os recursos que não foram compilados em `resources.arsc`.

> [!NOTE]
> **Observação**: o arquivo `libmonodroid.so` é a biblioteca nativa exigida por todos os aplicativos Xamarin.Android.


<a name="Android_Device_ABI_Support" />

#### <a name="android-device-abi-support"></a>Suporte a ABI de dispositivos Android

Cada dispositivo Android é compatível com a execução do código nativo em até duas ABIs:

-   **A ABI "primária"** &ndash; isso corresponde ao código de computador usado na imagem do sistema.

-   **Uma ABI "secundária"** &ndash; trata-se de uma ABI opcional que também é compatível com a imagem do sistema.


Por exemplo, um dispositivo ARMv5TE típico só terá uma ABI primária de `armeabi`, enquanto um dispositivo ARMv7 deve especificar uma ABI primária de `armeabi-v7a` e uma ABI secundária de `armeabi`. Um dispositivo x86 típico só deverá especificar uma ABI primária de `x86`.

<a name="Android_Native_Library_Installation" />

### <a name="android-native-library-installation"></a>Instalação da biblioteca nativa do Android

No tempo de instalação do pacote, bibliotecas nativas dentro de `.apk` são extraídas no diretório de bibliotecas nativas do aplicativo (normalmente `/data/data/<package-name>/lib`) e depois disso são referidas como `$APP/lib`.

O comportamento de instalação de biblioteca nativa do Android varia consideravelmente entre versões do Android.



#### <a name="installing-native-libraries-pre-android-40"></a>Instalar bibliotecas nativas: pré-Android 4.0

Android anterior ao 4.0 Ice Cream Sandwich apenas extrairá bibliotecas nativas de uma *única ABI* dentro do `.apk`. Aplicativos do Android dessa época tentarão primeiro extrair todas as bibliotecas nativas para a ABI primária e, se essas bibliotecas não existirem, o Android extrairá então todas as bibliotecas nativas para a ABI secundária. Nenhuma "mesclagem" é realizada.

Por exemplo, considere uma situação em que um aplicativo é instalado em um dispositivo `armeabi-v7a`. O `.apk,`, que é compatível com ambos `armeabi` e `armeabi-v7a`, contém os seguintes diretórios e arquivos `lib` de ABI:

```shell
lib/armeabi/libone.so
lib/armeabi/libtwo.so
lib/armeabi-v7a/libtwo.so
```

Após a instalação, o diretório de biblioteca nativa conterá:

```shell
$APP/lib/libtwo.so # from the armeabi-v7a directory in the apk
```

Em outras palavras, nenhum `libone.so` é instalado. Isso causará problemas, pois `libone.so` não estará presente para que o aplicativo seja carregado em tempo de execução. Esse comportamento, embora inesperado, foi registrado como um bug e reclassificado como "[funcionando como esperado](http://code.google.com/p/android/issues/detail?id=9089)."

Consequentemente, ao usar versões do Android anteriores à 4.0 como destino, é necessário fornecer *todas* as bibliotecas nativas para *cada* ABI à qual o aplicativo em questão dará suporte, ou seja, o `.apk` deve conter:

```shell
lib/armeabi/libone.so
lib/armeabi/libtwo.so
lib/armeabi-v7a/libone.so
lib/armeabi-v7a/libtwo.so
```


#### <a name="installing-native-libraries-android-40-ndash-android-403"></a>Instalando bibliotecas nativas: Android 4.0 &ndash; Android 4.0.3

O Android 4.0 Ice Cream Sandwich altera essa lógica de extração. Ele enumerará todas as bibliotecas nativas, verá se o nome base do arquivo já foi extraído e, se ambas as seguintes condições forem atendidas, a biblioteca será extraída:

-   Ele já não foi extraído.

-   ABI da biblioteca nativa corresponde à ABI primária ou secundária do destino.


Cumprir essas condições permite um comportamento de "mesclagem"; ou seja, se nós temos um `.apk` com o seguinte conteúdo:

```shell
lib/armeabi/libone.so
lib/armeabi/libtwo.so
lib/armeabi-v7a/libtwo.so
```

Em seguida, após a instalação, o diretório de biblioteca nativa conterá:

```shell
$APP/lib/libone.so
$APP/lib/libtwo.so
```

Infelizmente, esse comportamento é dependente da ordem, conforme descrito no seguinte documento – [Problema 24321: o Galaxy Nexus 4.0.2 usa código nativo armeabi quando ambos armeabi e armeabi-v7a estão incluídos no apk](http://code.google.com/p/android/issues/detail?id=25321).

As bibliotecas nativas são processadas "em ordem" (conforme listadas, por exemplo, pela descompactação) e a *primeira correspondência* é extraída. Já que o `.apk` contém as versões `armeabi` e `armeabi-v7a` de `libtwo.so` e o `armeabi` é listado primeiro, é a versão `armeabi` que é extraída, *não* a versão `armeabi-v7a`:

```shell
$APP/lib/libone.so # armeabi
$APP/lib/libtwo.so # armeabi, NOT armeabi-v7a!
```

Além disso, mesmo se os duas ABIs `armeabi` e `armeabi-v7a` são especificadas (conforme descrito abaixo na seção *Declarar ABIs compatíveis*), o Xamarin.Android cria o seguinte elemento no .csproj:
`csproj`:

```xml
<AndroidSupportedAbis>armeabi,armeabi-v7a</AndroidSupportedAbis>
```

Consequentemente, o `armeabi` `libmonodroid.so` será encontrado primeiro dentro do `.apk` e o `armeabi` `libmonodroid.so` será o extraído, embora o `armeabi-v7a` `libmonodroid.so` esteja presente e otimizado para o destino. Isso também pode resultar em erros de tempo de execução obscuros, pois `armeabi` não é seguro para SMP.


##### <a name="installing-native-libraries-android-404-and-later"></a>Instalar bibliotecas nativas: Android 4.0.4 e posterior

O Android 4.0.4 altera a lógica de extração: ele enumerará todas as bibliotecas nativas, lerá o nome base do arquivo e, em seguida, extrairá a versão da ABI primária (se presente) ou da ABI secundária (se presente). Isso permite um comportamento de "mesclagem"; ou seja, se nós temos um `.apk` com o seguinte conteúdo:

```shell
lib/armeabi/libone.so
lib/armeabi/libtwo.so
lib/armeabi-v7a/libtwo.so
```

Em seguida, após a instalação, o diretório de biblioteca nativa conterá:

```shell
$APP/lib/libone.so # from armeabi
$APP/lib/libtwo.so # from armeabi-v7a
```

<a name="Xamarin.Android_and_ABIs" />

### <a name="xamarinandroid-and-abis"></a>Xamarin.Android e ABIs

O Xamarin.Android é compatível com as seguintes arquiteturas:

-  `armeabi`
-  `armeabi-v7a`
-  `x86`

O Xamarin.Android dá suporte experimental às arquiteturas a seguir:

-  `arm64-v8a`
-  `x86_64`


Observe que os tempos de execução de 64 bits *não* são necessários para a execução de seu aplicativo em dispositivos de 64 bits. Para obter mais informações sobre recursos experimentais no Xamarin.Android 5.1, consulte [Recursos Experimentais](https://developer.xamarin.com/releases/android/xamarin.android_5/xamarin.android_5.1/#Experimental_Features).

O Xamarin.Android não é compatível com `mips` no momento.


<a name="Declaring_Supported_ABIs" />

### <a name="declaring-supported-abis"></a>Declarar ABIs compatíveis

Por padrão, o Xamarin.Android usará `armeabi-v7a` para builds de **versão**, mas usará `armeabi-v7a` e `x86` para builds de **depuração**. O suporte para ABIs diferentes pode ser definido com as Opções de Projeto para um projeto Xamarin.Android. No Visual Studio, isso pode ser definido na seção **Opções do Android** do projeto **Propriedades**, na guia **Avançado**, conforme mostrado na seguinte captura de tela:

![Propriedades de Opções Avançadas do Android](multicore-devices-images/vs-abi-selections.png)


No Visual Studio para Mac, as arquiteturas compatíveis podem ser selecionadas na página **Build do Android** das **Opções de Projeto**, na guia **Avançado**, conforme mostrado na seguinte captura de tela:

[![ABIs compatíveis com o build do Android](multicore-devices-images/xs-abi-selections-sml.png)](multicore-devices-images/xs-abi-selections.png)

Há algumas situações em que é necessário declarar suporte a ABIs adicionais, como ao:

-   Implantar o aplicativo em um dispositivo `x86`.

-   Implantar o aplicativo em um dispositivo `armeabi-v7a` para garantir o acesso thread-safe.



## <a name="summary"></a>Resumo

Este documento aborda as arquiteturas de CPU diferentes em que um aplicativo Android pode ser executado. Ele apresentou a interface binária de aplicativo e como ela é usada pelo Android para dar suporte a arquiteturas de CPU diferentes.
Na sequência, ele abordou como especificar o suporte a ABI em um aplicativo Xamarin.Android e destacou os problemas que podem surgir ao usar aplicativos Xamarin.Android em um dispositivo `armeabi-v7a` destinado apenas para `armeabi`.


## <a name="related-links"></a>Links relacionados

- [Arquitetura de MIPS](http://www.mips.com/products/product-materials/processor/mips-architecture)
- [ABI para a arquitetura ARM (PDF)](http://infocenter.arm.com/help/topic/com.arm.doc.ihi0036b/IHI0036B_bsabi.pdf)
- [NDK do Android](http://developer.android.com/tools/sdk/ndk/index.html)
- [Problema 9089: Nexus One – não é possível carregar NENHUMA biblioteca nativa do armeabi se há pelo menos uma biblioteca no armeabi-v7a](http://code.google.com/p/android/issues/detail?id=9089)
- [Problema 24321: o Galaxy Nexus 4.0.2 usa código nativo do armeabi quando ambos o armeabi e o armeabi-v7a são incluídos no apk](http://code.google.com/p/android/issues/detail?id=25321)
