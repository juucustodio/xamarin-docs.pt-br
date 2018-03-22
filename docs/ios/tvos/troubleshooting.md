---
title: "Solução de problemas"
description: "Este artigo abrange sabe problemas que você pode encontrar ao trabalhar com o suporte do Xamarin tvOS."
ms.topic: article
ms.prod: xamarin
ms.assetid: 124E4953-4DFA-42B0-BCFC-3227508FE4A6
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: c72d0dd89593002dbb6ea8ac9369666b811f3a73
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2018
---
# <a name="troubleshooting"></a>Solução de problemas

_Este artigo abrange sabe problemas que você pode encontrar ao trabalhar com o suporte do Xamarin tvOS._

<a name="Known-Issues" />

## <a name="known-issues"></a>Problemas Conhecidos

A versão atual de suporte de tvOS do Xamarin tem os seguintes problemas conhecidos:

- **Estrutura mono** – Mono 4.3 Cryptography.ProtectedData Falha ao descriptografar dados de Mono 4.2. Como resultado, os pacotes do NuGet falhará restaurar com o erro `Data unprotection failed` quando uma origem protegida do NuGet é configurada.
    - **Solução alternativa** – no Visual Studio para Mac, você precisará adicionar novamente qualquer pacote do NuGet fontes que usar autenticação de senha antes de tentar novamente restaurar os pacotes.
- **O Visual Studio para Mac com F # Add-in** – erro ao criar um modelo de F # Android no Windows. Isso ainda deve funcionar corretamente no Mac.
- **Xamarin.Mac** – quando a execução do projeto de modelo unificada de Xamarin.Mac com o Framework de destino definido como `Unsupported`, o pop-up `Could not connect to the debugger` pode aparecer.
    - **Solução alternativa potencial** – fazer Downgrade da versão do framework Mono disponível em nosso canal estável.
- **Visual Studio do Xamarin & xamarin** – ao implantar aplicativos WatchKit no Visual studio, o erro `The file ‘bin\iPhoneSimulator\Debug\WatchKitApp1WatchKitApp.app\WatchKitApp1WatchKitApp’ does not exist` pode aparecer.

Relatório de qualquer bugs que você encontre para [Bugzilla](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

## <a name="troubleshooting"></a>Solução de problemas

As seções a seguir listam alguns problemas conhecidos que podem ocorrer ao usar tvOS 9 com Xamarin.tvOS e a solução para esses problemas:

### <a name="invalid-executable---the-executable-does-not-contain-bitcode"></a>Executável inválido - o executável não contém bitcode

Quando tentar enviar um aplicativo Xamarin.tvOS a loja de aplicativos Apple TV, você pode receber uma mensagem de erro no formato _"Executável inválido - o executável não contém bitcode"_.

Para resolver esse problema, faça o seguinte:

1. No Visual Studio para Mac, clique em seu arquivo de projeto no Xamarin.tvOS o **Solution Explorer** e selecione **opções**.
2. Selecione **tvOS Build** e certifique-se de que você estiver usando o **versão** configuração: 

    [![](troubleshooting-images/ts01.png "Selecione tvOS opções de compilação")](troubleshooting-images/ts01.png#lightbox)
3. Adicionar `--bitcode=asmonly` para o **argumentos adicionais mtouch** campo e clique no **Okey** botão.
4. Recriar seu aplicativo o **versão** configuração.

### <a name="verifying-that-your-tvos-app-contains-bitcode"></a>Verificando se seu aplicativo contém Bitcode tvOS

Para verificar seu build Xamarin.tvOS App contém Bitcode, abra o aplicativo de Terminal e digite o seguinte:

```csharp
otool -l /path/to/your/tv.app/tv
```

Na saída, procure o seguinte:

```csharp
Section
  sectname __bundle
   segname __LLVM
      addr 0x0000000100001000
      size 0x000000000000124f
    offset 4096
     align 2^0 (1)
    reloff 0
    nreloc 0
     flags 0x00000000
 reserved1 0
 reserved2 0
```

`addr` e `size` serão diferentes, mas outros campos devem ser idênticos.

Você precisará garantir que qualquer terceiro estático (`.a`) foram construídas bibliotecas que você estiver usando bibliotecas de tvOS (não iOS bibliotecas) e que também inclui informações de bitcode.

Para aplicativos ou bibliotecas que incluem bitcode válido o `size` será maior do que um. Há algumas situações em que uma biblioteca pode ter o marcador bitcode ainda não contêm bitcode válido. Por exemplo:

**Bitcode inválido**

```csharp
 $ otool -arch arm64 libLibrary.a | grep __bitcode -A 3
   sect name __bitcode
   segname __LLVM
      add 0x0000000000000670
      size 0x0000000000000001
```

**Bitcode válido** 

```csharp
$ otool -l -arch arm64 libDownloadableAgent-tvos.a |grep __bitcode -A 3
   sectname __bitcode
   segname __LLVM
      addr 0x000000000001d2d0
      size 0x0000000000045440
```

Observe a diferença de `size` entre as duas bibliotecas no exemplo listado é executado acima. A biblioteca deve ser gerada de uma compilação de arquivo morto Xcode com bitcode habilitado (configuração do Xcode `ENABLE_BITCODE`) como uma solução para esse problema de tamanho.

### <a name="apps-that-only-contain-the-arm64-slice-must-also-have-arm64-in-the-list-of-uirequireddevicecapabilities-in-infoplist"></a>Aplicativos que contêm apenas a fatia arm64 também devem ter "arm64" na lista de UIRequiredDeviceCapabilities no Info. plist

Ao enviar um aplicativo à loja de aplicativos Apple TV para publicação, você poderá receber um erro no formato:

_"O aplicativos que contêm apenas a fatia arm64 também devem ter"arm64"na lista de UIRequiredDeviceCapabilities no Info. plist"_

Se isso ocorrer, edite seu `Info.plist` de arquivo e certifique-se de que ele tem as seguintes chaves:

```xml
<key>UIRequiredDeviceCapabilities</key>
<array>
    <string>arm64</string>
</array>
```

Recompilar o aplicativo para a versão e envie novamente para conectar-se de iTunes.

### <a name="task-mtouch-execution----failed"></a>"MTouch" - Falha na execução da tarefa

Se você estiver usando uma biblioteca de terceiros 3º (como MonoGame) e a compilação de versão falhou com uma longa série de mensagens de erro que terminam em `Task "MTouch" execution -- FAILED`, tente adicionar `-gcc_flags="-framework OpenAL"` para sua **argumentos adicionais toque**:

[![](troubleshooting-images/mtouch01.png "Execução da tarefa MTouch")](troubleshooting-images/mtouch01.png#lightbox)

Você também deve incluir `--bitcode=asmonly` no **argumentos adicionais toque**, configurou suas opções de vinculador para **todos os links** e fazer uma compilação limpa.

### <a name="itms-90471-error-the-large-icon-is-missing"></a>Erro de ITMS 90471. O ícone grande está ausente

Se você receber uma mensagem no formato "ITMS 90471 erro. O ícone grande está ausente"ao tentar enviar um aplicativo Xamarin.tvOS a loja de aplicativos Apple TV, versão, verifique o seguinte:

1. Certifique-se de que você incluiu os ativos de ícone grande em seu `Assets.car` arquivo que você criou usando o [ícones de aplicativo](~/ios/tvos/app-fundamentals/icons-images.md#App-Icons) documentação.
2. Certifique-se de que você incluiu o `Assets.car` arquivo o [trabalhando com imagens e ícones](~/ios/tvos/app-fundamentals/icons-images.md) documentação em seu pacote de aplicativo final.

### <a name="invalid-bundle--an-app-that-supports-game-controllers-must-also-support-the-apple-tv-remote"></a>Pacote inválido – um aplicativo que oferece suporte a controladores de jogo também deve dar suporte a Apple TV remoto

ou 

### <a name="invalid-bundle--apple-tv-apps-with-the-gamecontroller-framework-must-include-the-gcsupportedgamecontrollers-key-in-the-apps-infoplist"></a>Pacote inválido – aplicativos da Apple TV com a estrutura de GameController deve incluir a chave GCSupportedGameControllers no Info. plist do aplicativo

Controladores de jogo podem ser usados para aprimorar a jogos e fornecer uma noção de imersão em um jogo. Eles também podem ser usados para controlar a interface Apple TV padrão para que o usuário não tenha que alternar entre o controle remoto e o controlador.

Se você estiver enviando um aplicativo Xamarin.tvOS com suporte de controlador de jogo para Apple TV App store e você está recebendo uma mensagem de erro na forma de:


_Descobrimos um ou mais problemas com sua recente entrega para "nome do aplicativo". A entrega teve êxito, mas você poderá corrigir os seguintes problemas na sua próxima entrega:_

_Pacote inválido – um aplicativo que oferece suporte a controladores de jogo também deve dar suporte a Apple TV remoto._

ou 

_Pacote inválido – aplicativos da Apple TV com a estrutura de GameController deve incluir a chave GCSupportedGameControllers no Info. plist do aplicativo._

A solução é adicionar suporte para o computador remoto Siri (`GCMicroGamepad`) para seu aplicativo `Info.plist` arquivo. O perfil do controlador de jogo Micro foi adicionado pela Apple para Siri remoto de destino. Por exemplo, inclua as seguintes chaves:

```xml
<key>GCSupportedGameControllers</key>  
  <array>  
    <dict>  
      <key>ProfileName</key>  
      <string>ExtendedGamepad</string>  
    </dict>  
    <dict>  
      <key>ProfileName</key>  
      <string>MicroGamepad</string>  
    </dict>  
  </array>  
<key>GCSupportsControllerUserInteraction</key>  
<true/>
```

> [!IMPORTANT]
> Os controladores de jogo Bluetooth são uma compra opcional que os usuários finais podem fazer, seu aplicativo não é possível forçar o usuário a comprar um. Se seu aplicativo dá suporte a controladores de jogo também deve suportar Siri remoto para que o jogo seja utilizável por todos os usuários da Apple TV.

Para obter mais informações, consulte nosso [trabalhando com os controladores de jogo](~/ios/tvos/platform/remote-bluetooth.md#Working-with-Game-Controllers) seção do nosso [Siri remoto e controladores Bluetooth](~/ios/tvos/platform/remote-bluetooth.md) documentação.

### <a name="incompatible-target-framework-netportable-versionv45-profileprofile78"></a>Estrutura de destino incompatível:. NetPortable, Version = v 4.5, perfil = Profile78

Ao tentar incluir uma biblioteca de classe portátil (PCL) em um projeto de Xamarin.tvOS pode receber uma mensagem para formar:

_Estrutura de destino incompatível:. NetPortable, Version = v 4.5, perfil = Profile78_

Para solucionar esse problema, adicione um arquivo XML chamado ` Xamarin.TVOS.xml` com o seguinte conteúdo:

```xml
<Framework Identifier="Xamarin.TVOS" MinimumVersion="1.0" Profile="*" DisplayName="Xamarin.TVOS"/>
```

O seguinte caminho:

```csharp
/Library/Frameworks/Mono.framework/Versions/Current/lib/mono/xbuild-frameworks/.NETPortable/v4.5/Profile/Profile259/SupportedFrameworks/

```

Observe que o número de perfil no caminho deve corresponder ao número de perfil do PCL.

Com esse arquivo em vigor, você deve ser capaz de adicionar o arquivo PCL com êxito ao projeto Xamarin.tvOS.



## <a name="related-links"></a>Links relacionados

- [Amostras do tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS guias de Interface Humana](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guia de programação de aplicativo para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
