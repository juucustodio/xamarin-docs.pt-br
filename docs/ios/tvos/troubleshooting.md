---
title: Solução de problemas de aplicativos tvOS criados com o Xamarin
description: Este artigo fornece várias dicas para ajudar a solucionar problemas durante o desenvolvimento de um aplicativo tvOS criado com o Xamarin. Ele descreve problemas conhecidos e erros específicos.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 124E4953-4DFA-42B0-BCFC-3227508FE4A6
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/16/2017
ms.openlocfilehash: 3fb479321686e4b956fc6ffee90dd5b0b2c16d9c
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70291189"
---
# <a name="troubleshooting-tvos-apps-built-with-xamarin"></a>Solução de problemas de aplicativos tvOS criados com o Xamarin

_Este artigo aborda os problemas conhecidos que você pode encontrar ao trabalhar com o suporte do tvOS do Xamarin._

<a name="Known-Issues" />

## <a name="known-issues"></a>Problemas Conhecidos

A versão atual do suporte do tvOS do Xamarin tem os seguintes problemas conhecidos:

- **Estrutura mono** – mono 4,3 Cryptography. ProtectedData falha ao descriptografar dados do mono 4,2. Como resultado, os pacotes NuGet não conseguirão restaurar com o `Data unprotection failed` erro quando uma origem do NuGet protegida for configurada.
  - **Solução alternativa** – em Visual Studio para Mac você precisará adicionar todas as fontes de pacote NuGet que usam autenticação de senha antes de tentar restaurar os pacotes novamente.
- **Visual Studio para Mac w/ F# Add-in** – erro ao criar um F# modelo do Android no Windows. Isso ainda deve funcionar corretamente no Mac.
- **Xamarin. Mac** – ao executar o projeto de modelo unificado do Xamarin. Mac com a `Unsupported`estrutura de destino `Could not connect to the debugger` definida como, o pop-up pode aparecer.
  - **Possível solução alternativa** – faça downgrade da versão do mono Framework disponível em nosso canal estável.
- **Xamarin Visual Studio & xamarin. Ios** – ao implantar aplicativos WatchKit no Visual Studio, o erro `The file ‘bin\iPhoneSimulator\Debug\WatchKitApp1WatchKitApp.app\WatchKitApp1WatchKitApp’ does not exist` pode ser exibido.

Informe todos os bugs encontrados no [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

## <a name="troubleshooting"></a>Solução de problemas

As seções a seguir listam alguns problemas conhecidos que podem ocorrer ao usar o tvOS 9 com o Xamarin. tvOS e a solução para esses problemas:

### <a name="invalid-executable---the-executable-does-not-contain-bitcode"></a>Executável inválido-o executável não contém BitCode

Ao tentar enviar um aplicativo Xamarin. tvOS para a loja de aplicativos da Apple TV, você pode receber uma mensagem de erro no formato _"executável inválido-o executável não contém BitCode"_ .

Para resolver esse problema, faça o seguinte:

1. Em Visual Studio para Mac, clique com o botão direito do mouse no arquivo de projeto Xamarin. tvOS na **Gerenciador de soluções** e selecione **Opções**.
2. Selecione **TvOS Build** e verifique se você está na configuração de **versão** : 

    [![](troubleshooting-images/ts01.png "Selecione as opções de Build do tvOS")](troubleshooting-images/ts01.png#lightbox)
3. Adicione `--bitcode=asmonly` ao campo **argumentos mTouch adicionais** e clique no botão **OK** .
4. Recrie seu aplicativo na configuração de **versão** .

### <a name="verifying-that-your-tvos-app-contains-bitcode"></a>Verificando se seu aplicativo tvOS contém BitCode

Para verificar se o Build do aplicativo Xamarin. tvOS contém BitCode, abra o aplicativo terminal e insira o seguinte:

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

`addr`e `size` serão diferentes, mas outros campos devem ser idênticos.

Você precisará certificar-se de que todas as bibliotecas estáticas (`.a`) de terceiros que você está usando foram criadas em relação a bibliotecas tvOS (não bibliotecas Ios) e que também incluem informações de BitCode.

Para aplicativos ou bibliotecas que incluem BitCode válidas `size` , o será maior que um. Há algumas situações em que uma biblioteca pode ter o marcador BitCode, mas não contém BitCode válido. Por exemplo:

**BitCode inválido**

```csharp
 $ otool -arch arm64 libLibrary.a | grep __bitcode -A 3
   sect name __bitcode
   segname __LLVM
      add 0x0000000000000670
      size 0x0000000000000001
```

**BitCode válido** 

```csharp
$ otool -l -arch arm64 libDownloadableAgent-tvos.a |grep __bitcode -A 3
   sectname __bitcode
   segname __LLVM
      addr 0x000000000001d2d0
      size 0x0000000000045440
```

Observe que a diferença `size` entre as duas bibliotecas no exemplo listado é executada acima. A biblioteca deve ser gerada a partir de uma compilação de arquivo do Xcode com BitCode `ENABLE_BITCODE`habilitado (configuração do Xcode) como uma solução para esse problema de tamanho.

### <a name="apps-that-only-contain-the-arm64-slice-must-also-have-arm64-in-the-list-of-uirequireddevicecapabilities-in-infoplist"></a>Os aplicativos que contêm apenas a fatia arm64 também devem ter "arm64" na lista de UIRequiredDeviceCapabilities no info. plist

Ao enviar um aplicativo para a loja de aplicativos da Apple TV para publicação, você pode receber um erro no formato:

_"Os aplicativos que contêm apenas a fatia arm64 também devem ter" arm64 "na lista de UIRequiredDeviceCapabilities no info. plist"_

Se isso ocorrer, edite `Info.plist` o arquivo e verifique se ele tem as seguintes chaves:

```xml
<key>UIRequiredDeviceCapabilities</key>
<array>
  <string>arm64</string>
</array>
```

Recompile seu aplicativo para liberação e reenvie para o iTunes Connect.

### <a name="task-mtouch-execution----failed"></a>Execução da tarefa "MTouch"--com falha

Se você estiver usando uma biblioteca de terceiros (como monogames) e a compilação da versão falhar com uma longa série de mensagens de erro terminando em `Task "MTouch" execution -- FAILED`, tente adicionar `-gcc_flags="-framework OpenAL"` aos seus argumentos de **toque adicionais**:

[![](troubleshooting-images/mtouch01.png "Execução de MTouch de tarefa")](troubleshooting-images/mtouch01.png#lightbox)

Você também deve incluir `--bitcode=asmonly` os **argumentos de toque adicionais**, ter suas opções de vinculador definidas para **vincular tudo** e fazer uma compilação limpa.

### <a name="itms-90471-error-the-large-icon-is-missing"></a>Erro de ITMS-90471. O ícone grande está ausente

Se você receber uma mensagem no formato "ITMS-90471 Error. O ícone grande está ausente "ao tentar enviar um aplicativo Xamarin. tvOS para a versão da loja de aplicativos da Apple TV para o Release, verifique o seguinte:

1. Verifique se você incluiu os ativos de ícone grande no `Assets.car` arquivo que você criou usando a documentação de [ícones de aplicativo](~/ios/tvos/app-fundamentals/icons-images.md#App-Icons) .
2. Certifique-se de incluir `Assets.car` o arquivo da documentação [trabalhando com ícones e imagens](~/ios/tvos/app-fundamentals/icons-images.md) em seu pacote de aplicativos final.

### <a name="invalid-bundle--an-app-that-supports-game-controllers-must-also-support-the-apple-tv-remote"></a>Pacote inválido – um aplicativo que dá suporte a controladores de jogos também deve dar suporte ao Apple TV Remote

ou 

### <a name="invalid-bundle--apple-tv-apps-with-the-gamecontroller-framework-must-include-the-gcsupportedgamecontrollers-key-in-the-apps-infoplist"></a>Pacote inválido – os aplicativos da Apple TV com a estrutura GameController devem incluir a chave GCSupportedGameControllers no info. plist do aplicativo

Os controladores de jogo podem ser usados para aprimorar jogos e fornecer uma noção de imersão em um jogo. Eles também podem ser usados para controlar a interface padrão da Apple TV para que o usuário não precise alternar entre o remoto e o controlador.

Se você estiver enviando um aplicativo Xamarin. tvOS com suporte do controlador de jogo para a loja de aplicativos da Apple TV e receber uma mensagem de erro na forma de:


_Descobrimos um ou mais problemas com sua entrega recente para "nome do aplicativo". Sua entrega foi bem-sucedida, mas talvez você queira corrigir os seguintes problemas em sua próxima entrega:_

_Pacote inválido – um aplicativo que dá suporte a controladores de jogos também deve dar suporte ao Apple TV Remote._

ou 

_Pacote inválido – os aplicativos da Apple TV com a estrutura GameController devem incluir a chave GCSupportedGameControllers no info. plist do aplicativo._

A solução é adicionar suporte para o Siri remoto (`GCMicroGamepad`) ao arquivo do `Info.plist` aplicativo. O perfil do micro Game Controller foi adicionado pela Apple para direcionar o Siri remoto. Por exemplo, inclua as seguintes chaves:

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
> Controladores de jogos Bluetooth são uma compra opcional que os usuários finais podem fazer, seu aplicativo não pode forçar o usuário a comprar um. Se seu aplicativo der suporte a controladores de jogos, ele também deverá oferecer suporte ao Siri remoto para que o jogo seja utilizável por todos os usuários da Apple TV.

Para obter mais informações, consulte [a seção trabalhando com controladores de jogos](~/ios/tvos/platform/remote-bluetooth.md#Working-with-Game-Controllers) da documentação de nossa Siri de [controladores remotos e Bluetooth](~/ios/tvos/platform/remote-bluetooth.md) .

### <a name="incompatible-target-framework-netportable-versionv45-profileprofile78"></a>Estrutura de destino incompatível:. Netportável, versão = v 4.5, perfil = Profile78

Ao tentar incluir uma PCL (biblioteca de classes portátil) em um projeto Xamarin. tvOS, você pode receber uma mensagem para formar:

_Estrutura de destino incompatível:. Netportável, versão = v 4.5, perfil = Profile78_

Para resolver esse problema, adicione um arquivo XML chamado `Xamarin.TVOS.xml` com o seguinte conteúdo:

```xml
<Framework Identifier="Xamarin.TVOS" MinimumVersion="1.0" Profile="*" DisplayName="Xamarin.TVOS"/>
```

Para o seguinte caminho:

```csharp
/Library/Frameworks/Mono.framework/Versions/Current/lib/mono/xbuild-frameworks/.NETPortable/v4.5/Profile/Profile259/SupportedFrameworks/

```

Observe que o número do perfil no caminho deve corresponder ao número de perfil do PCL.

Com esse arquivo em vigor, você deve ser capaz de adicionar com êxito o arquivo PCL ao projeto Xamarin. tvOS.



## <a name="related-links"></a>Links relacionados

- [Amostras do tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guias de interface humana do tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guia de programação de aplicativo para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
