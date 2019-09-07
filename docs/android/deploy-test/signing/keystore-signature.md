---
title: Encontrar a assinatura de seu repositório de chaves
ms.prod: xamarin
ms.assetid: 1b511fec-e6f6-453e-89c8-810aafb02b77
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 6cada2c7426cc50723321d77e21852a92fe64d63
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70755790"
---
# <a name="finding-your-keystores-signature"></a>Encontrar a assinatura de seu repositório de chaves

A assinatura MD5 ou SHA1 de um aplicativo Xamarin.Android depende do arquivo **.keystore** que foi usado para assinar o APK. Normalmente, um build de depuração usará um arquivo **.keystore** diferente do arquivo de um build de lançamento.

## <a name="for-debug--non-custom-signed-builds"></a>Para compilações assinadas de Depuração/Não Personalizadas

O Xamarin.Android assina todas as compilações de depuração com o mesmo arquivo **debug.keystore**. Esse arquivo é gerado quando o Xamarin.Android é instalado pela primeira vez. As etapas a seguir detalham o processo para localizar a assinatura SHA1 ou MD5 do arquivo padrão **debug.keystore** do Xamarin.Android.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Localize o arquivo **debug.keystore** do Xamarin que é usado para assinar o aplicativo. Por padrão, o repositório de chaves que é usado para assinar as versões de depuração de um aplicativo Xamarin.Android pode ser encontrado no seguinte local:

**C:\\Usuários\\*NOMEDEUSUÁRIO*\\AppData\\Local\\Xamarin\\Mono for Android\\debug.keystore**

Informações sobre um repositório de chaves são obtidas executando o comando `keytool.exe` no JDK. Essa ferramenta normalmente é encontrada no seguinte local:

**C:\\Arquivos de Programas (x86)\\Java\\jdk*VERSÃO*\\bin\\keytool.exe**

Adicione o diretório que contém o **keytool.exe** na variável de ambiente `PATH`.
Abra um **Prompt de Comando** e execute `keytool.exe` usando o seguinte comando:

```cmd
keytool.exe -list -v -keystore "%LocalAppData%\Xamarin\Mono for Android\debug.keystore" -alias androiddebugkey -storepass android -keypass android
```

Ao executar, o **keytool.exe** deve gerar o seguinte texto. Os rótulos **MD5:** e **SHA1:** identificam as respectivas assinaturas:

```cmd
Alias name: androiddebugkey
Creation date: Aug 19, 2014
Entry type: PrivateKeyEntry
Certificate chain length: 1
Certificate[1]:
Owner: CN=Android Debug, O=Android, C=US
Issuer: CN=Android Debug, O=Android, C=US
Serial number: 53f3b126
Valid from: Tue Aug 19 13:18:46 PDT 2014 until: Sun Nov 15 12:18:46 PST 2043
Certificate fingerprints:
         MD5:  27:78:7C:31:64:C2:79:C6:ED:E5:80:51:33:9C:03:57
         SHA1: 00:E5:8B:DA:29:49:9D:FC:1D:DA:E7:EE:EE:1A:8A:C7:85:E7:31:23
         SHA256: 21:0D:73:90:1D:D6:3D:AB:4C:80:4E:C4:A9:CB:97:FF:34:DD:B4:42:FC:
08:13:E0:49:51:65:A6:7C:7C:90:45
         Signature algorithm name: SHA1withRSA
         Version: 3
```

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Localize o arquivo **debug.keystore** do Xamarin que é usado para assinar o aplicativo. Por padrão, o repositório de chaves que é usado para assinar as versões de depuração de um aplicativo Xamarin.Android pode ser encontrado no seguinte local:

**~/.local/share/Xamarin/Mono for Android/debug.keystore**

Informações sobre um repositório de chaves são obtidas executando o comando **keytool** no JDK. Essa ferramenta normalmente é encontrada no seguinte local:

**/System/Library/Java/JavaVirtualMachines/*VERSÃO*.jdk/Contents/Home/bin/keytool**

Adicione o diretório que contém o **keytool** na variável de ambiente **PATH**.
Abra um **Terminal** e execute o **keytool** usando o seguinte comando:

```bash
$ keytool -list -v -keystore ~/.local/share/Xamarin/Mono\ for\ Android/debug.keystore -alias androiddebugkey -storepass android -keypass android
```

Ao executar, o **keytool** deve gerar o seguinte texto. Os rótulos **MD5:** e **SHA1:** identificam as respectivas assinaturas:

```bash
Alias name: androiddebugkey
Creation date: Apr 16, 2015
Entry type: PrivateKeyEntry
Certificate chain length: 1
Certificate[1]:
Owner: CN=Android Debug, O=Android, C=US
Issuer: CN=Android Debug, O=Android, C=US
Serial number: 76afa120
Valid from: Thu Apr 16 10:52:27 PDT 2015 until: Sat Apr 08 10:52:27 PDT 2045
Certificate fingerprints:
     MD5:  0A:D3:7E:80:3D:40:2A:23:89:B9:AB:9C:4B:B6:63:36
     SHA1: 89:33:8F:F2:C5:0C:91:08:4A:CF:04:A5:EC:4A:31:80:84:18:0D:D4
     SHA256: 91:AC:3E:2F:CB:EF:50:07:2B:E0:D9:8D:8B:C2:42:87:6A:85:02:86:EB:44:84:10:34:02:ED:35:CE:C6:38:47
     Signature algorithm name: SHA256withRSA
     Version: 3

Extensions:

#1: ObjectId: 2.5.29.14 Criticality=false
SubjectKeyIdentifier [
KeyIdentifier [
0000: 65 6C FE 67 8E CD CB 9A   01 CD 9F E8 25 9C A4 A3  el.g........%...
0010: 4F 4E CF 17                                        ON..
]
]
```

-----

## <a name="for-release--custom-signed-builds"></a>Para Lançamento / Compilações com Assinaturas Personalizadas

Os processos para compilações de lançamento que são assinadas com um arquivo **.keystore** personalizado são os mesmos acima, com o arquivo **.keystore** de lançamento substituindo o arquivo **debug.keystore** que é usado pelo Xamarin.Android. Substitua, com os seus próprios valores, a senha do repositório de chaves e o nome do alias de quando o arquivo do repositório de chaves de lançamento foi criado.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Quando o assistente **Distribuir** do Visual Studio for usado para assinar um aplicativo Xamarin.Android, o repositório de chaves resultante estará no seguinte local:

**C:\\Usuários\\*NOME DE USUÁRIO*\\AppData\\Local\\Xamarin\\Mono for Android\\Keystore\\*alias*\\*alias*.keystore**

Por exemplo, se você seguiu as etapas em [Criar um Novo Certificado](~/android/deploy-test/signing/index.md#newcertvs) para criar uma nova chave de assinatura, o repositório de chaves resultante de exemplo está no seguinte local:

**C:\\Usuários\\*NOMEDEUSUÁRIO*\\AppData\\Local\\Xamarin\\Mono for Android\\Keystore\\chimp\\chimp.keystore**

Para obter mais informações sobre como assinar um aplicativo Xamarin.Android, consulte [Assinatura do Pacote de Aplicativo Android](~/android/deploy-test/signing/index.md).

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Quando o Assistente **Assinar e distribuir...** do Visual Studio para Mac assinar seu aplicativo, o repositório de chaves resultante estará no seguinte local:

**~/Library/Developer/Xamarin/Keystore/*alias*/*alias*.keystore**

Por exemplo, se você seguiu as etapas em [Criar um Novo Certificado](~/android/deploy-test/signing/index.md#newcertxs) para criar uma nova chave de assinatura, o repositório de chaves resultante de exemplo está no seguinte local:

**~/Library/Developer/Xamarin/Keystore/chimp/chimp.keystore**

Para obter mais informações sobre como assinar um aplicativo Xamarin.Android, consulte [Assinatura do Pacote de Aplicativo Android](~/android/deploy-test/signing/index.md).

-----
