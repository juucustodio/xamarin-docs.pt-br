---
title: GDB
ms.prod: xamarin
ms.assetid: CD0BE462-FA38-4881-B481-82AD05B3B8FE
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/05/2018
ms.openlocfilehash: 51ebb8932f4b1ef8f10ba699e59ce53c98a38a50
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73021484"
---
# <a name="gdb"></a>GDB

## <a name="overview"></a>Visão geral

O Xamarin.Android 4.10 introduziu o suporte parcial para uso de `gdb` usando o destino de MSBuild `_Gdb`. 

> [!NOTE]
> O suporte a `gdb` requer que o NDK do Android esteja instalado.

Há três maneiras de usar o `gdb`:

1. [Builds de depuração com o Fast Deployment habilitado](#Debug_Builds_with_Fast_Deployment).
1. [Builds de depuração com o Fast Deployment desabilitado](#Debug_Builds_without_Fast_Deployment).
1. [Builds de lançamento](#Release_Builds).

Quando as coisas derem errado, consulte a seção [Solução de problemas](#Troubleshooting).

<a name="Debug_Builds_with_Fast_Deployment" />

### <a name="debug-builds-with-fast-deployment"></a>Builds de depuração com o Fast Deployment

Ao compilar e implantar um build de depuração com o Fast Deployment habilitado, `gdb` pode ser anexado usando o destino do MSBuild `_Gdb`.

Primeiro, instale o aplicativo. Isso pode ser feito por meio do IDE ou da linha de comando:

```bash
$ /Library/Frameworks/Mono.framework/Commands/xbuild /t:Install *.csproj
```

Em segundo lugar, execute o destino `_Gdb`. No final da execução, uma linha de comando `gdb` será impressa:

```bash
$ /Library/Frameworks/Mono.framework/Commands/xbuild /t:_Gdb *.csproj
...
    Target _Gdb:
        "/opt/android/ndk/toolchains/arm-linux-androideabi-4.4.3/prebuilt/darwin-x86/bin/arm-linux-androideabi-gdb" -x "/Users/jon/Development/Projects/Scratch.HelloXamarin20//gdb-symbols/gdb.env"
...
```

O destino `_Gdb` iniciará uma atividade launcher declarada dentro de seu arquivo `AndroidManifest.xml`. Para especificar explicitamente quais atividade seja executada, use a propriedade do MSBuild `RunActivity`. No momento, iniciar serviços e outros constructos do Android não é uma ação compatível.

O destino `_Gdb` criará um diretório `gdb-symbols` e copiará o conteúdo dos diretórios `/system/lib` e `$APPDIR/lib` do seu destino para lá.

> [!NOTE]
> O conteúdo do diretório `gdb-symbols` está vinculado ao destino do Android no qual você implantou, e não será automaticamente substituído se você mudar o destino. (Considere isso um bug.) Se você alterar dispositivos alvo do Android, você precisará excluir manualmente este diretório.

Por fim, copie o comando `gdb` gerado e execute-o no shell:

```bash
$ "/opt/android/ndk/toolchains/arm-linux-androideabi-4.4.3/prebuilt/darwin-x86/bin/arm-linux-androideabi-gdb" -x "/Users/jon/Development/Projects/Scratch.HelloXamarin20//gdb-symbols/gdb.env"
GNU gdb (GDB) 7.3.1-gg2
...
(gdb) bt
#0  0x40082e84 in nanosleep () from /Users/jon/Development/Projects/Scratch.HelloXamarin20/gdb-symbols/libc.so
#1  0x4008ffe6 in sleep () from /Users/jon/Development/Projects/Scratch.HelloXamarin20/gdb-symbols/libc.so
#2  0x74e46240 in ?? ()
#3  0x74e46240 in ?? ()
(gdb) c
```

<a name="Debug_Builds_without_Fast_Deployment" />

## <a name="debug-builds-without-fast-deployment"></a>Builds de depuração sem o Fast Deployment

Builds de depuração *com* o Fast Deployment funcionam copiando o programa `gdbserver` do NDK do Android para o diretório `.__override__` do Fast Deployment. Quando o Fast Deployment está desabilitado, esse diretório pode não existir.

Há duas soluções alternativas:

- Definir a propriedade do sistema `debug.mono.log` de modo que o diretório `.__override__` seja criado.
- Incluir `gdbserver` dentro de `.apk`.

### <a name="setting-the-debugmonolog-system-property"></a>Definir a propriedade do sistema `debug.mono.log`

Para definir a propriedade do sistema `debug.mono.log`, use o comando `adb`:

```bash
$ adb shell setprop debug.mono.log gc
```

Uma vez que a propriedade do sistema foi definida, execute o destino `_Gdb` e o comando `gdb` impresso, assim como com a configuração Builds de depuração com o Fast Deployment:

```bash
$ /Library/Frameworks/Mono.framework/Commands/xbuild /t:_Gdb *.csproj
  ...
    Target _Gdb:
        "/opt/android/ndk/toolchains/arm-linux-androideabi-4.4.3/prebuilt/darwin-x86/bin/arm-linux-androideabi-gdb" -x "/Users/jon/Development/Projects/Scratch.HelloXamarin20//gdb-symbols/gdb.env"
  ...
$ "/opt/android/ndk/toolchains/arm-linux-androideabi-4.4.3/prebuilt/darwin-x86/bin/arm-linux-androideabi-gdb" -x "/Users/jon/Development/Projects/Scratch.HelloXamarin20//gdb-symbols/gdb.env"
GNU gdb (GDB) 7.3.1-gg2
...
(gdb) c
```

### <a name="including-gdbserver-in-your-app"></a>Incluir `gdbserver` em seu aplicativo

Para incluir `gdbserver` dentro de seu aplicativo:

1. Localize `gdbserver` dentro de seu NDK do Android **$ANDROID\_NDK\_PATH/prebuilt/android-arm/gdbserver/gdbserver**) e copie-o para seu diretório do projeto.

2. Renomeie `gdbserver` para **libs/armeabi-v7a/libgdbserver.so**.

3. Adicione **libs/armeabi-v7a/libgdbserver.so** ao seu projeto com um **ação de build** de `AndroidNativeLibrary`.

4. Recompile e reinstale seu aplicativo.

Uma vez que o aplicativo tiver sido reinstalado, execute o destino `_Gdb` e o comando `gdb` impresso, assim como com a configuração Builds de depuração com o Fast Deployment:

```bash
$ /Library/Frameworks/Mono.framework/Commands/xbuild /t:_Gdb *.csproj
  ...
    Target _Gdb:
        "/opt/android/ndk/toolchains/arm-linux-androideabi-4.4.3/prebuilt/darwin-x86/bin/arm-linux-androideabi-gdb" -x "/Users/jon/Development/Projects/Scratch.HelloXamarin20//gdb-symbols/gdb.env"
  ...
$ "/opt/android/ndk/toolchains/arm-linux-androideabi-4.4.3/prebuilt/darwin-x86/bin/arm-linux-androideabi-gdb" -x "/Users/jon/Development/Projects/Scratch.HelloXamarin20//gdb-symbols/gdb.env"
GNU gdb (GDB) 7.3.1-gg2
...
(gdb) c
```

<a name="Release_Builds" />

## <a name="release-builds"></a>Builds de lançamento

O suporte a `gdb` requer três coisas:

1. A permissão de `INTERNET`.
2. Depuração de Aplicativo habilitada.
3. Um `gdbserver` acessível.

A permissão `INTERNET` é habilitada por padrão em aplicativos de depuração. Se ela não ainda estiver presente no seu aplicativo, você poderá adicioná-la editando **Properties/AndroidManifest.xml** ou editando as [Propriedades do Projeto](https://github.com/xamarin/recipes/tree/master/Recipes/android/general/projects/add_permissions_to_android_manifest).

A depuração de aplicativo pode ser habilitada configurando a propriedade de atributo personalizado [ApplicationAttribute.Debugging](xref:Android.App.ApplicationAttribute.Debuggable) para `true`, ou editando **Properties/AndroidManifest.xml** e definindo o atributo `//application/@android:debuggable` para `true`:

```xml
<application android:label="Example.Name.Here" android:debuggable="true">
```

Um `gdbserver` acessível pode ser fornecido seguindo a seção [Builds de depuração sem o Fast Deployment](#Debug_Builds_without_Fast_Deployment).

Um probleminha: o destino `_Gdb` do MSBuild finalizará quaisquer instâncias de aplicativo em execução anteriormente. Isso não funcionará em destinos com Android anterior à v4.0.

<a name="Troubleshooting" />

## <a name="troubleshooting"></a>Solução de problemas

### <a name="mono_pmip-doesnt-work"></a>`mono_pmip` não funciona

A função `mono_pmip` (útil para [obter registros de ativação gerenciados](https://www.mono-project.com/docs/debug+profile/debug/#debugging-with-gdb)) é exportada do `libmonosgen-2.0.so`, cujo pull não é efetuado pelo destino `_Gdb` atualmente. (Isso será corrigido em uma versão futura.)

Para habilitar a chamada a funções localizadas em `libmonosgen-2.0.so`, copie-o do dispositivo de destino para o diretório `gdb-symbols`:

```bash
$ adb pull /data/data/Mono.Android.DebugRuntime/lib/libmonosgen-2.0.so Project/gdb-symbols
```

Reinicie então sua sessão de depuração.

### <a name="bus-error-10-when-running-the-gdb-command"></a>Erro de barramento: 10 ao executar o comando `gdb`

Quando o comando `gdb` encontrar um erro com `"Bus error: 10"`, reinicie o dispositivo Android.

```bash
$ "/path/to/arm-linux-androideabi-gdb" -x "Project/gdb-symbols/gdb.env"
GNU gdb (GDB) 7.3.1-gg2
Copyright (C) 2011 Free Software Foundation, Inc.
...
Bus error: 10
$
```

### <a name="no-stack-trace-after-attach"></a>Nenhum rastreamento de pilha depois de anexar

```bash
$ "/path/to/arm-linux-androideabi-gdb" -x "Project/gdb-symbols/gdb.env"
GNU gdb (GDB) 7.3.1-gg2
Copyright (C) 2011 Free Software Foundation, Inc.
...
(gdb) bt
No stack.
```

Isso geralmente é um sinal de que o conteúdo do diretório `gdb-symbols` não está sincronizado com o destino do Android. (Você alterou o destino do Android?)

Exclua o diretório `gdb-symbols` e tente novamente.
