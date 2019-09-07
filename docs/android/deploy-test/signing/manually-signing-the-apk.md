---
title: Assinando manualmente o APK
ms.prod: xamarin
ms.assetid: 08549E1C-7F04-4D20-9E7A-794B9D09FD12
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 3be55c2149aa58bf6d8462e5c1ff24166078355f
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70755893"
---
# <a name="manually-signing-the-apk"></a>Assinando manualmente o APK

Depois de o aplicativo ser compilado para liberação, o APK deverá ser assinado antes da distribuição para que ele possa ser executado em um dispositivo Android. Normalmente, esse processo é tratado no IDE, no entanto, há algumas situações em que é necessário assinar o APK manualmente, na linha de comando. As seguintes etapas estão envolvidas com a assinatura de um APK:

1. **Criar uma Chave Privada** &ndash; Esta etapa deve ser executada apenas uma vez. Uma chave privada é necessária para assinar digitalmente o APK.
    Depois que a chave privada foi preparada, essa etapa pode ser ignorada para compilações de versão futuras.

2. **Efetuar o Zipalign no APK** &ndash; *Zipalign* é um processo de otimização que é executado em um aplicativo. Ele permite que o Android interaja de forma mais eficiente com o APK no tempo de execução. O Xamarin.Android realiza uma verificação no tempo de execução e não permitirá que o aplicativo seja executado se o APK não teve o zipalign efetuado.

3. **Assinar o APK** &ndash; essa etapa envolve usar o **apksigner** por meio do SDK do Android e assinar o APK com a chave privada criada na etapa anterior. Aplicativos desenvolvidos com versões das ferramentas de build do SDK do Android anteriores à v24.0.3 usarão o aplicativo **jarsigner** por meio do JDK. Ambas as ferramentas serão abordadas em mais detalhes abaixo. 

A ordem das etapas é importante e depende de qual ferramenta é usada para assinar o APK. Ao usar **apksigner**, é importante primeiro usar **zipalign** no aplicativo e, em seguida, assiná-lo com **apksigner**.  Caso seja necessário usar **jarsigner** para assinar o APK, é importante primeiro assinar o APK e, em seguida, executar **zipalign**. 

## <a name="prerequisites"></a>Pré-requisitos

Este guia se concentrará no uso do **apksigner** usando as ferramentas de build do SDK de Android v24.0.3 ou superior. Ele pressupõe que um APK já foi compilado.

Aplicativos compilados usando uma versão anterior das Ferramentas de Build do SDK de Android devem usar **jarsigner** conforme descrito em [Assinar o APK com jarsigner](#Sign_the_APK_with_jarsigner) abaixo.

## <a name="create-a-private-keystore"></a>Criar um Repositório de Chaves Privado

Um *repositório de chaves* é um banco de dados de certificados de segurança criado usando o programa [keytool](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html) do SDK do Java. Um repositório de chaves é essencial para publicar um aplicativo Xamarin.Android, pois o Android não executará aplicativos que não foram assinados digitalmente.

Durante o desenvolvimento, o Xamarin.Android usa um repositório de chaves de depuração para assinar o aplicativo, que permite que o aplicativo seja implantado diretamente no emulador ou em dispositivos configurados para usar aplicativos depuráveis.
No entanto, esse repositório de chaves não é reconhecido como um repositório de chaves válido para fins de distribuição de aplicativos.

Por esse motivo, um repositório de chaves privado deve ser criado e usado para assinar aplicativos. Essa é uma etapa que deve ser executada apenas uma vez, já que a mesma chave será usada para a publicação de atualizações e, em seguida, poderá ser usada para assinar outros aplicativos.

É importante proteger esse repositório de chaves. Se ele for perdido, não será possível publicar atualizações no aplicativo com o Google Play.
A única solução para o problema causado por um repositório de chaves perdido seria criar um novo repositório de chaves, assinar novamente o APK com a nova chave e, em seguida, enviar um novo aplicativo. Em seguida, o aplicativo antigo teria que ser removido do Google Play. Da mesma forma, quando esse novo repositório de chaves é comprometido ou distribuído publicamente, é possível que versões não oficiais ou mal-intencionadas de um aplicativo sejam distribuídas.

### <a name="create-a-new-keystore"></a>Criar um Novo Repositório de Chaves

A criação de um novo repositório de chaves requer a ferramenta da linha de comando [keytool](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html) do SDK do Java. O snippet a seguir é um exemplo de como usar **keytool** (substitua `<my-filename>` pelo nome do arquivo para o repositório de chaves e `<key-name>` pelo nome da chave no repositório de chaves):

```shell
$ keytool -genkeypair -v -keystore <filename>.keystore -alias <key-name> -keyalg RSA \
          -keysize 2048 -validity 10000
```

A primeira coisa que o **keytool** solicitará é a senha do repositório de chaves. Em seguida, pedirá algumas informações para ajudar na criação da chave. O snippet a seguir é um exemplo da criação de uma nova chave chamada `publishingdoc` que será armazenada no arquivo `xample.keystore`:

```shell
$ keytool -genkeypair -v -keystore xample.keystore -alias publishingdoc -keyalg RSA -keysize 2048 -validity 10000
Enter keystore password:
Re-enter new password:
What is your first and last name?
  [Unknown]:  Ham Chimpanze
What is the name of your organizational unit?
  [Unknown]:  NASA
What is the name of your organization?
  [Unknown]:  NASA
What is the name of your City or Locality?
  [Unknown]:  Cape Canaveral
What is the name of your State or Province?
  [Unknown]:  Florida
What is the two-letter country code for this unit?
  [Unknown]:  US
Is CN=Ham Chimpanze, OU=NASA, O=NASA, L=Cape Canaveral, ST=Florida, C=US correct?
  [no]:  yes

Generating 2,048 bit RSA key pair and self-signed certificate (SHA1withRSA) with a validity of 10,000 days
        for: CN=Ham Chimpanze, OU=NASA, O=NASA, L=Cape Canaveral, ST=Florida, C=US
Enter key password for <publishingdoc>
        (RETURN if same as keystore password):
Re-enter new password:
[Storing xample.keystore]
```

Para listar as chaves armazenadas em um repositório de chaves, use o **keytool** com a opção &ndash; `list`:

```shell
$ keytool -list -keystore xample.keystore
```

## <a name="zipalign-the-apk"></a>Efetuar o zipalign no APK

Antes de assinar um APK com **apksigner**, é importante primeiro otimizar o arquivo usando a ferramenta **zipalign** no SDK do Android. A ferramenta **zipalign** reestruturará os recursos em um APK junto com limites de 4 bytes. Esse alinhamento permite que o Android carregue rapidamente os recursos por meio do APK, aumentando o desempenho do aplicativo e potencialmente reduzindo o uso de memória. O Xamarin.Android realizará uma verificação de tempo de execução para determinar se o APK teve o zipalign efetuado. Se o APK não tiver o zipalign efetuado, o aplicativo não será executado.

O comando a seguir usará o APK assinado e produzirá um sinal, o APK com zipalign **helloworld.apk** que está pronto para distribuição.

```shell
$ zipalign -f -v 4 mono.samples.helloworld-unsigned.apk helloworld.apk
```

## <a name="sign-the-apk"></a>Assinar o APK

Depois de usar zipalign no APK, é necessário assiná-lo usando um repositório de chaves. Isso é feito com a ferramenta **apksigner**, encontrada no diretório **build-tools** da versão das ferramentas de build do SDK.  Por exemplo, se as ferramentas de build do SDK de Android v25.0.3 estiverem instaladas, **apksigner** poderá ser encontrado no diretório:

```bash
$ ls $ANDROID_HOME/build-tools/25.0.3/apksigner
/Users/tom/android-sdk-macosx/build-tools/25.0.3/apksigner*
```

O snippet a seguir supõe que o **apksigner** esteja acessível pela variável de ambiente `PATH`. Ele assinará um APK usando o alias de chave `publishingdoc` contido no arquivo **xample.keystore**:

```shell
$ apksigner sign --ks xample.keystore --ks-key-alias publishingdoc mono.samples.helloworld.apk
```

Quando esse comando for executado, **apksigner** solicitará a senha para o repositório de chaves, se necessário.

Consulte a [documentação do Google](https://developer.android.com/studio/command-line/apksigner.html) para obter mais detalhes sobre o uso do **apksigner**.

> [!NOTE]
> De acordo com o [problema do Google 62696222](https://issuetracker.google.com/issues/62696222), **apksigner** está "ausente" no SDK do Android. A solução para isso é instalar as ferramentas de build do SDK do Android v25.0.3 e usar essa versão do **apksigner**.  

<a name="Sign_the_APK_with_jarsigner" />

### <a name="sign-the-apk-with-jarsigner"></a>Assinar o APK com jarsigner

> [!WARNING]
> Esta seção aplica-se somente caso seja necessário assinar o APK com o utilitário **jarsigner**. Os desenvolvedores são incentivados a usar o **apksigner** para assinar o APK.

Essa técnica envolve a assinatura do arquivo APK usando o comando **[jarsigner](https://docs.oracle.com/javase/8/docs/technotes/tools/windows/jarsigner.html)** do SDK do Java.  A ferramenta **jarsigner** é fornecida pelo SDK do Java. 

O seguinte mostra como assinar um APK usando **jarsigner** e a chave `publishingdoc` que é contida em um arquivo de repositório de chaves denominado **xample.keystore**:

```shell
$ jarsigner -verbose -sigalg SHA1withRSA -digestalg SHA1 -keystore xample.keystore mono.samples.helloworld.apk publishingdoc
```

> [!NOTE]
> Ao usar **jarsigner**, é importante assinar o APK _primeiro_ e, depois, usar o **zipalign**.  

## <a name="related-links"></a>Links relacionados

- [Assinatura de aplicativos](https://source.android.com/security/apksigning/)
- [Assinatura de JAR Java](https://docs.oracle.com/javase/8/docs/technotes~/jar/jar.html#Signed_JAR_File)
- [jarsigner](https://docs.oracle.com/javase/8/docs/technotes/tools/windows/jarsigner.html)
- [keytool](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html)
- [zipalign](https://developer.android.com/studio/command-line/zipalign.html)
- [Ferramentas de build 26.0.0 – para onde foi o apksigner?](https://issuetracker.google.com/issues/62696222)
