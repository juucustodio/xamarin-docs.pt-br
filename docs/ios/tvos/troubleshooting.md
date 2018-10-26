---
title: Solucionar problemas de aplicativos do tvOS criados com o Xamarin
description: Este artigo fornece várias dicas para ajudar a solucionar problemas durante o desenvolvimento de um aplicativo tvOS criado com o Xamarin. Ele descreve problemas conhecidos e erros específicos.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 124E4953-4DFA-42B0-BCFC-3227508FE4A6
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 45c57aa6d6308697d9bc581bf8d1691f3b29a9e5
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50120574"
---
# <a name="troubleshooting-tvos-apps-built-with-xamarin"></a>Solucionar problemas de aplicativos do tvOS criados com o Xamarin

_Este artigo aborda Saiba que esses problemas, você pode encontrar ao trabalhar com o suporte de tvOS do Xamarin._

<a name="Known-Issues" />

## <a name="known-issues"></a>Problemas Conhecidos

A versão atual do suporte de tvOS do Xamarin tem os seguintes problemas conhecidos:

- **Estrutura mono** – Mono 4.3 Cryptography.ProtectedData não conseguir descriptografar os dados da versão 4.2 do Mono. Como resultado, os pacotes do NuGet serão Falha na restauração com o erro `Data unprotection failed` quando uma origem de NuGet protegida é configurada.
    - **Solução alternativa** – no Visual Studio para Mac, você precisará adicionar novamente qualquer pacote NuGet que usar autenticação de senha antes de tentar novamente restaurar os pacotes de códigos-fonte.
- **O Visual Studio para Mac com F# Add-in** – erro ao criar um F# modelo Android no Windows. Isso ainda deverá funcionar corretamente no Mac.
- **Xamarin. Mac** – ao executar o projeto de modelo unificado do xamarin. Mac com a estrutura de destino definido como `Unsupported`, o pop-up `Could not connect to the debugger` pode aparecer.
    - **Solução alternativa potencial** – fazer Downgrade da versão do Mono framework disponível em nosso canal estável.
- **Xamarin para Visual Studio e xamarin. IOS** – ao implantar aplicativos do WatchKit no Visual studio, o erro `The file ‘bin\iPhoneSimulator\Debug\WatchKitApp1WatchKitApp.app\WatchKitApp1WatchKitApp’ does not exist` pode aparecer.

Relatório de quaisquer bugs que você encontre ao [Bugzilla](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

## <a name="troubleshooting"></a>Solução de problemas

As seções a seguir listam alguns problemas conhecidos que podem ocorrer ao usar tvOS 9 com xamarin. tvos e a solução para esses problemas:

### <a name="invalid-executable---the-executable-does-not-contain-bitcode"></a>Executável inválido - o executável não contêm bitcode

Ao tentar enviar um aplicativo xamarin. tvos para a Store de aplicativo da Apple TV, você poderá receber uma mensagem de erro no formulário _"Executável inválido - o executável não contêm bitcode"_.

Para resolver esse problema, faça o seguinte:

1. No Visual Studio para Mac, clique com botão direito no seu arquivo de projeto do xamarin. tvos na **Gerenciador de soluções** e selecione **opções**.
2. Selecione **Build do tvOS** e certifique-se de que você estiver usando o **versão** configuração: 

    [![](troubleshooting-images/ts01.png "Selecione as opções de Build do tvOS")](troubleshooting-images/ts01.png#lightbox)
3. Adicione `--bitcode=asmonly` para o **argumentos adicionais do mtouch** campo e clique no **Okey** botão.
4. Recompilar seu aplicativo na **versão** configuração.

### <a name="verifying-that-your-tvos-app-contains-bitcode"></a>Verificando se seu aplicativo contém Bitcode tvOS

Para verificar se sua compilação Xamarin.tvOS App contém Bitcode, abra o aplicativo Terminal e digite o seguinte:

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

Você precisará ter certeza de que qualquer terceiro estático (`.a`) foram construídas bibliotecas que você estiver usando bibliotecas de tvOS (não a bibliotecas de iOS) e que também inclui informações de bitcode.

Para aplicativos ou bibliotecas que incluem o bitcode válido a `size` será maior do que um. Há algumas situações em que uma biblioteca pode ter o marcador de bitcode, ainda não contêm bitcode válido. Por exemplo:

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

Observe a diferença no `size` entre as duas bibliotecas no exemplo listado é executado acima. A biblioteca deve ser gerada de um build de arquivo morto do Xcode com bitcode habilitado (configuração do Xcode `ENABLE_BITCODE`) como uma solução para esse problema de tamanho.

### <a name="apps-that-only-contain-the-arm64-slice-must-also-have-arm64-in-the-list-of-uirequireddevicecapabilities-in-infoplist"></a>Aplicativos que contêm apenas a fatia arm64 também devem ter "arm64" na lista de UIRequiredDeviceCapabilities no Info. plist

Ao enviar um aplicativo para a Store do Apple TV App para publicação, você poderá receber um erro no formulário:

_"O aplicativos que contêm apenas a fatia arm64 também devem ter"arm64"na lista de UIRequiredDeviceCapabilities no Info. plist"_

Se isso ocorrer, edite seu `Info.plist` de arquivo e certifique-se de que ele tem as seguintes chaves:

```xml
<key>UIRequiredDeviceCapabilities</key>
<array>
    <string>arm64</string>
</array>
```

Recompilar seu aplicativo para lançamento e envie novamente para o iTunes Connect.

### <a name="task-mtouch-execution----failed"></a>"MTouch" – Falha na execução de tarefas

Se você estiver usando uma biblioteca de terceiros 3ª (por exemplo, MonoGame) e sua compilação de versão falha com longa uma série de mensagens de erro que terminam em `Task "MTouch" execution -- FAILED`, tente adicionar `-gcc_flags="-framework OpenAL"` para seu **argumentos adicionais de toque**:

[![](troubleshooting-images/mtouch01.png "Execução de MTouch da tarefa")](troubleshooting-images/mtouch01.png#lightbox)

Você também deve incluir `--bitcode=asmonly` no **argumentos adicionais de toque**, definiu as opções do vinculador como **vincular todos** e fazer uma compilação limpa.

### <a name="itms-90471-error-the-large-icon-is-missing"></a>Erro do ITMS 90471. O ícone grande está ausente

Se você receber uma mensagem no formato "ITMS 90471 erro. O ícone grande está ausente"ao tentar enviar um aplicativo xamarin. tvos para a Store do Apple TV App para versão, verifique o seguinte:

1. Certifique-se de que você incluiu os ativos de ícone grande em seu `Assets.car` arquivo que você criou usando o [ícones do aplicativo](~/ios/tvos/app-fundamentals/icons-images.md#App-Icons) documentação.
2. Certifique-se de que você incluiu a `Assets.car` arquivo do [trabalhando com imagens e ícones](~/ios/tvos/app-fundamentals/icons-images.md) documentação no pacote de aplicativo final.

### <a name="invalid-bundle--an-app-that-supports-game-controllers-must-also-support-the-apple-tv-remote"></a>Pacote inválido – um aplicativo que dá suporte a controladores de jogos também deve dar suporte a Apple TV remoto

ou 

### <a name="invalid-bundle--apple-tv-apps-with-the-gamecontroller-framework-must-include-the-gcsupportedgamecontrollers-key-in-the-apps-infoplist"></a>Pacote inválido – aplicativos da Apple TV com o framework GameController deve incluir a chave de GCSupportedGameControllers no Info. plist do aplicativo

Controladores de jogos podem ser usados para aprimorar o jogo e fornecer um senso de imersão em um jogo. Eles também podem ser usados para controlar a interface de Apple TV padrão para que o usuário não precise alternar entre o computador remoto e o controlador.

Se você estiver enviando um aplicativo tvos com o suporte de controlador de jogo para Apple TV App store e recebam uma mensagem de erro na forma de:


_Descobrimos um ou mais problemas com sua recente entrega para "nome do aplicativo". Sua entrega foi bem-sucedida, mas você poderá corrigir os problemas a seguir em sua próxima entrega:_

_Pacote inválido – um aplicativo que dá suporte a controladores de jogos também deve dar suporte a Apple TV remoto._

ou 

_Pacote inválido – aplicativos da Apple TV com o framework GameController deve incluir a chave de GCSupportedGameControllers no Info. plist do aplicativo._

A solução é adicionar suporte para o Siri remoto (`GCMicroGamepad`) ao seu aplicativo `Info.plist` arquivo. O perfil do controlador de jogo Micro foi adicionado pela Apple para que o Siri remoto de destino. Por exemplo, incluem as seguintes chaves:

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
> Controladores de jogo de Bluetooth são uma compra adicional que os usuários finais podem fazer, seu aplicativo não pode forçar o usuário compra um. Se seu aplicativo dá suporte a controladores de jogos também deve suportar Siri remoto para que o jogo seja utilizável por todos os usuários de Apple TV.

Para obter mais informações, consulte nosso [trabalhar com controladores de jogo](~/ios/tvos/platform/remote-bluetooth.md#Working-with-Game-Controllers) seção do nosso [Siri remoto e controladores de Bluetooth](~/ios/tvos/platform/remote-bluetooth.md) documentação.

### <a name="incompatible-target-framework-netportable-versionv45-profileprofile78"></a>Estrutura de destino incompatível:. NetPortable, versão = v4.5, perfil = Profile78

Durante a tentativa de incluir uma biblioteca de classe portátil (PCL) em um projeto xamarin. tvos pode receber uma mensagem para formar:

_Estrutura de destino incompatível:. NetPortable, versão = v4.5, perfil = Profile78_

Para resolver esse problema, adicione um arquivo XML chamado ` Xamarin.TVOS.xml` com o seguinte conteúdo:

```xml
<Framework Identifier="Xamarin.TVOS" MinimumVersion="1.0" Profile="*" DisplayName="Xamarin.TVOS"/>
```

Para o seguinte caminho:

```csharp
/Library/Frameworks/Mono.framework/Versions/Current/lib/mono/xbuild-frameworks/.NETPortable/v4.5/Profile/Profile259/SupportedFrameworks/

```

Observe que o número de perfil no caminho deve corresponder ao número de perfil de PCL.

Com esse arquivo em vigor, você deve ser capaz de adicionar com êxito o arquivo PCL para o projeto xamarin. tvos.



## <a name="related-links"></a>Links relacionados

- [Amostras do tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [Guias de Interface humana do tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guia de programação de aplicativo para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
