---
title: Compilação de APKs específicos para ABI
description: Este documento discute como compilar um APK que será direcionado a uma única ABI usando o Xamarin.Android.
ms.prod: xamarin
ms.assetid: D21B195B-4530-4EB2-8704-5C4349A2CDD8
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/15/2018
ms.openlocfilehash: 89a78c8dd1243dcfea9d14bd9758c5403d1d21ef
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
ms.locfileid: "30769072"
---
# <a name="building-abi-specific-apks"></a>Compilação de APKs específicos para ABI

_Este documento discute como compilar um APK que será direcionado a uma única ABI usando o Xamarin.Android._



## <a name="overview"></a>Visão geral

Em algumas situações, pode ser vantajoso para um aplicativo ter vários APKs – cada APK é assinado com o mesmo armazenamento de chaves e compartilha o mesmo nome de pacote, mas é compilado para uma configuração do Android ou dispositivo específico. Essa não é a abordagem recomendada, pois é muito mais simples ter um APK que pode dar suporte a vários dispositivos e configurações. Há algumas situações em que criar vários APKs pode ser útil, tais como:

-  **Reduzir o tamanho do APK** – o Google Play impõe um limite de tamanho de 100 MB para arquivos APK. A criação de APKs específicos de um dispositivo pode reduzir o tamanho do APK, pois você só precisa fornecer um subconjunto de ativos e recursos para o aplicativo.

-  **Dar suporte a diferentes arquiteturas de CPU** – se seu aplicativo tem bibliotecas compartilhadas para CPUs específicas, você pode distribuir apenas as compartilhadas para uma determinada CPU.


Vários APKs podem complicar a distribuição – um problema que é abordado pelo Google Play. O Google Play garantirá que o APK correto seja entregue a um dispositivo com base no código da versão do aplicativo e outros metadados contidos em **AndroidManifest.XML**. Para obter detalhes específicos e restrições no modo como o Google Play é compatível com vários APKs para um aplicativo, consulte a [Documentação do Google sobre o suporte a vários APKs](http://developer.android.com/google/play/publishing/multiple-apks.html).

Este guia abordará como criar o script o build de múltiplos APKs para um aplicativo Xamarin.Android, cada APK direcionado a um ABI específico. Ele aborda os seguintes tópicos:

1.  Criar um *código de versão* exclusivo para o APK.
1.  Criar uma versão temporária de **AndroidManifest.XML**, que será usada para este APK.
1.  Compile o aplicativo usando o **AndroidManifest.XML** da etapa anterior.
1.  Prepare o APK para lançamento, assinando-o e submetendo-o a zipalign.


No final deste guia há um passo a passo que demonstra como criar o script dessas etapas usando o [Rake](http://martinfowler.com/articles/rake.html).



### <a name="creating-the-version-code-for-the-apk"></a>Criar um código de versão para o APK

O Google recomenda um algoritmo específico para o código de versão, que usa um código de versão de sete dígitos (consulte a seção *Usando um esquema de código de versão* no [Documento de suporte a vários APKs](http://developer.android.com/google/play/publishing/multiple-apks.html)).
Expandindo esse esquema de código da versão de oito dígitos, será possível incluir, no código de versão, algumas informações de ABI que garantirão que o Google Play distribuirá o APK correto para um dispositivo. A lista a seguir explica este formato de código de versão de oito dígitos (indexado da esquerda para a direita):

-   **Índice 0** (vermelho no diagrama abaixo) &ndash; um inteiro para a ABI:
    -   1 &ndash; `armeabi`
    -   2 &ndash; `armeabi-v7a`
    -   6 &ndash; `x86`

-   **Índice 1-2** (laranja no diagrama abaixo) &ndash; o nível da API mínimo compatível com o aplicativo.

-   **Índice 3-4** (azul no diagrama abaixo) &ndash; os tamanhos de tela compatíveis:
    -   1 &ndash; pequeno
    -   2 &ndash; normal
    -   3 &ndash; grande
    -   4 &ndash; xlarge

-   **Índice 5-7** (verde no diagrama abaixo) &ndash; um número exclusivo para o código de versão. 
    Ele é definido pelo desenvolvedor. Ele deve aumentar a cada versão pública do aplicativo.

O diagrama a seguir ilustra a posição de cada código descrito na lista acima:

[![Diagrama do formato de código de versão de oito dígitos, codificado por cor](abi-specific-apks-images/image00.png)](abi-specific-apks-images/image00.png#lightbox)


O Google Play garantirá que o APK correto seja entregue ao dispositivo com base no `versionCode` e na configuração de APK. O APK com o código da versão mais alto será entregue ao dispositivo. Como um exemplo, um aplicativo pode ter três APKs com os seguintes códigos de versão:

-  11413456 – a ABI é `armeabi`, o nível da API de direcionamento é 14, telas pequenas a grandes e com um número de versão 456.
-  21423456 – a ABI é `armeabi-v7a`, o nível da API de direcionamento é 14, telas normais &amp; grandes e com um número de versão 456.
-  61423456 – a ABI é `x86`, o nível da API de direcionamento é 14, telas normais &amp; grandes e com um número de versão 456.

Para continuar com este exemplo, imagine que foi corrigido um bug que era específico para `armeabi-v7a`. A versão do aplicativo aumenta para 457, e um novo APK é criado com o `android:versionCode` definido para 21423457. Os versionCodes para as versões `armeabi` e `x86` permanecerão os mesmos.

Agora, imagine que a versão x86 recebe algumas atualizações ou correções de bug que se destinam a uma API mais recente (nível da API 19), tornando esta a versão 500 do aplicativo. O novo `versionCode` seria alterado para 61923500, enquanto o armeabi/armeabi-v7a permaneceriam inalterados. Nesse momento, os códigos de versão seriam:

-  11413456 – a ABI é `armeabi`, o nível da API de direcionamento é 14, telas pequenas a grandes e com um nome de versão de 456.
-  21423457 – a ABI é `armeabi-v7a`, o nível da API de direcionamento é 14, telas normais &amp; grandes e com um nome de versão de 457.
-  61923500 – a ABI é `x86`, o nível da API de direcionamento é 19, telas normais &amp; grandes e com um nome de versão de 500.


Manter esses códigos de versão manualmente pode ser um fardo significativo para o desenvolvedor. O processo de calcular o `android:versionCode` correto e posteriormente compilar os APKs deve ser automatizado.
Um exemplo de como fazer isso será abordado no passo a passo no final deste documento.


### <a name="create-a-temporary-androidmanifestxml"></a>Criar um AndroidManifest.XML temporário

Embora não seja estritamente necessário, criar um **AndroidManifest.XML** temporário para cada ABI pode ajudar a evitar problemas que possam surgir com vazamento de informações um APK ao outro. Por exemplo, é crucial que o atributo `android:versionCode` seja exclusivo para cada APK.

O modo como isso é feito depende do sistema de script envolvido, mas geralmente envolve fazer uma cópia do manifesto do Android usado durante o desenvolvimento, modificá-lo e, em seguida, usar esse manifesto modificado durante o processo de build.



### <a name="compiling-the-apk"></a>Compilar o APK

Compilar o APK por ABI melhor é realizado usando `xbuild` ou `msbuild`, conforme mostrado na seguinte linha de comando de exemplo:

```bash
/Library/Frameworks/Mono.framework/Commands/xbuild /t:Package /p:AndroidSupportedAbis=<TARGET_ABI> /p:IntermediateOutputPath=obj.<TARGET_ABI>/ /p:AndroidManifest=<PATH_TO_ANDROIDMANIFEST.XML> /p:OutputPath=bin.<TARGET_ABI> /p:Configuration=Release <CSPROJ FILE>
```

A lista a seguir explica cada parâmetro de linha de comando:

-   `/t:Package` &ndash; Cria um APK Android que é assinado usando o repositório de chaves de depuração

-   `/p:AndroidSupportedAbis=<TARGET_ABI>` &ndash; Esta é a ABI a ser usada como destino. Deve ser uma entre `armeabi`, `armeabi-v7a` ou `x86`

-   `/p:IntermediateOutputPath=obj.<TARGET_ABI>/` &ndash; Este é o diretório que conterá os arquivos intermediários que são criados como parte do build. Se necessário, o Xamarin.Android criará um diretório com o nome da ABI, tal como `obj.armeabi-v7a`. É recomendável usar uma pasta para cada ABI, pois isso impede problemas resultantes do "vazamento" de arquivos de um build para o outro. Observe que esse valor é encerrado com um separador de diretório (um `/` no caso de OS X).

-   `/p:AndroidManifest` &ndash; Essa propriedade especifica o caminho para o arquivo **AndroidManifest.XML**, que será usado durante o build.

-   `/p:OutputPath=bin.<TARGET_ABI>` &ndash; Este é o diretório que conterá o APK final. O Xamarin.Android criará um diretório com o nome da ABI, por exemplo, `bin.armeabi-v7a`.

-   `/p:Configuration=Release` &ndash; Execute um build de versão do APK. Os builds de depuração podem não ser carregados no Google Play.

-   `<CS_PROJ FILE>` &ndash; Este é o caminho para o arquivo `.csproj` do projeto do Xamarin.Android.



### <a name="sign-and-zipalign-the-apk"></a>Assinar e efetuar o zipalign no APK

É necessário assinar o APK antes que ele possa ser distribuído por meio do Google Play. Isso pode ser feito usando o aplicativo `jarsigner`, que faz parte do Kit do Desenvolvedor Java. A linha de comando a seguir demonstra como usar `jarsigner` na linha de comando:

```shell
jarsigner -verbose -sigalg SHA1withRSA -digestalg SHA1 -keystore <PATH/TO/KEYSTORE> -storepass <PASSWORD> -signedjar <PATH/FOR/SIGNED_JAR> <PATH/FOR/JAR/TO/SIGN> <NAME_OF_KEY_IN_KEYSTORE>
```

O zipalign deve ser efetuado em todos os aplicativos Xamarin.Android antes que eles sejam executados em um dispositivo. Este é o formato da linha de comando a ser usada:

```shell
zipalign -f -v 4 <SIGNED_APK_TO_ZIPALIGN> <PATH/TO/ZIP_ALIGNED.APK>
```


## <a name="automating-apk-creation-with-rake"></a>Automatização da criação de APK com o Rake

O projeto de exemplo [OneABIPerAPK](https://github.com/xamarin/monodroid-samples/tree/master/OneABIPerAPK) é um projeto Android simples, que demonstra como calcular um número de versão específico de uma ABI e compilar três APKs separados para cada uma das seguintes ABIs:

-  armeabi
-  armeabi-v7a
-  x86


O [rakefile](https://github.com/xamarin/monodroid-samples/blob/master/OneABIPerAPK/Rakefile.rb) no projeto de exemplo executa cada uma das etapas descritas nas seções anteriores:

1. [Criar um android: versionCode](https://github.com/xamarin/monodroid-samples/blob/master/OneABIPerAPK/Rakefile.rb#L30) para o APK.

1. [Gravar o android: versionCode](https://github.com/xamarin/monodroid-samples/blob/master/OneABIPerAPK/Rakefile.rb#L55) em um **AndroidManifest.XML** personalizado para esse APK.

1. [Compilar um build de versão](https://github.com/xamarin/monodroid-samples/blob/master/OneABIPerAPK/Rakefile.rb#L63) do projeto Xamarin.Android que terá como destino exclusivo a ABI, usando o **AndroidManifest.XML** que foi criado na etapa anterior.

1. [Assinar o APK ](https://github.com/xamarin/monodroid-samples/blob/master/OneABIPerAPK/Rakefile.rb#L66) com um repositório de chaves de produção.

1. [Efetuar o zipalign](https://github.com/xamarin/monodroid-samples/blob/master/OneABIPerAPK/Rakefile.rb#L67) no APK.


Para compilar todos os APKs para o aplicativo, execute a tarefa do Rake `build` da linha de comando:

```shell
$ rake build
==> Building an APK for ABI armeabi with ./Properties/AndroidManifest.xml.armeabi, android:versionCode = 10814120.
==> Building an APK for ABI x86 with ./Properties/AndroidManifest.xml.x86, android:versionCode = 60814120.
==> Building an APK for ABI armeabi-v7a with ./Properties/AndroidManifest.xml.armeabi-v7a, android:versionCode = 20814120.
```

Após a tarefa rake ser concluída, haverá três pastas `bin` com o arquivo `xamarin.helloworld.apk`. A próxima captura de tela mostra cada uma dessas pastas com os respectivos conteúdos:

[![Locais de pastas específicas de uma plataforma que contêm xamarin.helloworld.apk](abi-specific-apks-images/image01.png)](abi-specific-apks-images/image01.png#lightbox)


> [!NOTE]
> O processo de build descrito neste guia pode ser implementado em um dos diversos sistemas de build. Embora não tenhamos um exemplo escrito anteriormente, isso também deverá ser possível com [Powershell](http://technet.microsoft.com/en-ca/scriptcenter/powershell.aspx) / [psake](https://github.com/psake/psake) ou [Fake](http://fsharp.github.io/FAKE/).


## <a name="summary"></a>Resumo

Este guia fornece algumas sugestões de como criar APKs do Android que se destinam a uma ABI específica. Ele também mostrou um esquema possível para a criação de `android:versionCodes` que identificará a arquitetura de CPU à qual o APK é destinado. O passo a passo inclui um projeto de exemplo que tem o script do seu build escrito usando Rake.



## <a name="related-links"></a>Links relacionados

- [OneABIPerAPK (amostra)](https://developer.xamarin.com/samples/OneABIPerAPK/)
- [Publicando um aplicativo](~/android/deploy-test/publishing/index.md)
- [Suporte a vários APKs para Google Play](http://developer.android.com/google/play/publishing/multiple-apks.html)
