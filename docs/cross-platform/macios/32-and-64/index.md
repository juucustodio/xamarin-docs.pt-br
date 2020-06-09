---
title: considerações sobre a plataforma de 32/64 bits
description: Este documento descreve várias considerações para ter em mente ao direcionar as arquiteturas de 32 bits e 64 bits para um aplicativo Xamarin. iOS ou Xamarin. Mac.
ms.prod: xamarin
ms.assetid: F7126340-04B2-4A10-B14D-394E23527C1A
author: davidortinau
ms.author: daortin
ms.date: 03/29/2017
ms.openlocfilehash: d2c00410581a35e67807f512b5b6fb6d5349ad0f
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84571045"
---
# <a name="3264-bit-platform-considerations"></a>considerações sobre a plataforma de 32/64 bits

Embora o iOS e o macOS tenham suporte historicamente a aplicativos de 32 e 64 bits, a Apple preteriu o suporte de 32 bits gradualmente.

A partir do iOS 11, os aplicativos de 32 bits não serão mais inicializados, e [todos os envios para a loja de aplicativos deverão oferecer suporte a 64 bits](https://developer.apple.com/news/?id=06282017b).

A partir de janeiro de 2018, [novos aplicativos enviados para a Mac App Store devem dar suporte a 64 bits](https://developer.apple.com/news/?id=06282017a)e aplicativos existentes devem ser atualizados até junho de 2018.

O API Clássica do Xamarin ( `XamMac.dll` e `monotouch.dll` ) tem suporte apenas para aplicativos de 32 bits. No entanto, novos aplicativos Xamarin. iOS e Xamarin. Mac usam o [API unificada](~/cross-platform/macios/unified/index.md) ( `Xamarin.iOS` e `Xamarin.Mac` ) por padrão e, portanto, podem ter como alvo 32 e 64 bits, conforme necessário.

## <a name="ios"></a>iOS

<a name="enable-64"></a>

### <a name="enabling-64-bit-builds-of-xamarinios-apps"></a>Habilitando compilações de 64 bits de aplicativos Xamarin. iOS

> [!WARNING]
> Esta seção está incluída por razões históricas e para ajudar a mover projetos anteriores do Xamarin. iOS para o API Unificada e dar suporte a 64 bits. Todos os novos projetos do Xamarin. iOS usarão o API Unificada e o destino de 64 bits por padrão.

Para aplicativos móveis Xamarin. iOS que foram convertidos no API Unificada, os desenvolvedores devem atualizar manualmente as configurações de compilação para o destino de 64 bits:

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

1. No **painel de soluções**, clique duas vezes no projeto do aplicativo para abrir a janela **Opções do projeto** .
2. Selecione **Build do IOS**.
3. Para o simulador de iPhone, na lista suspensa **arquiteturas com suporte** , selecione **x86 \_ 64** ou **i386 + x86 \_ 64**:

   [![Configurando arquiteturas com suporte para x86 \_ 64 ou i386 + x86 \_ 64](Images/Image01.png "Setting Supported architectures to x86\_64 or i386 + x86\_64")](Images/Image01-large.png#lightbox) 

4. Para dispositivos físicos, selecione uma das combinações de **ARM64** disponíveis:

   [![Configurando arquiteturas com suporte para uma das combinações de ARM64](Images/Image02.png "Configurando arquiteturas com suporte para uma das combinações de ARM64")](Images/Image02-large.png#lightbox)

5. Clique em **OK**.
6. Execute uma compilação limpa.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Na **Gerenciador de soluções**, clique com o botão direito do mouse no projeto do aplicativo e selecione **Propriedades**.
2. Selecione **Build do IOS**.
3. Para o simulador de iPhone, defina as **arquiteturas com suporte** para **x86 \_ 64** ou **i386 + x86 \_ 64**: 

   [![Configurando arquiteturas com suporte para x86_64 ou i386 + x86 \_ 64](Images/VS02.png "Setting Supported architectures to x86_64 or i386 + x86\_64")](Images/VS02-large.png#lightbox)

4. Para dispositivos físicos, selecione uma das combinações de **ARM64** disponíveis:
    
   [![Configurando arquiteturas com suporte para uma das combinações de ARM64](Images/VS01.png "Configurando arquiteturas com suporte para uma das combinações de ARM64")](Images/VS01-large.png#lightbox)

5. Salve suas alterações.
6. Execute uma compilação limpa.

-----

O ARMv7s tem suporte apenas pelo processador a6 incluído no iPhone 5 (ou superior). O código ARMv7 é mais rápido e menor do que o ARMv6, só funciona com o iPhone 3GS e posterior e é exigido pela Apple ao direcionar para o iPad ou uma versão mínima do iOS 5,0. O ARMv6 funciona em todos os dispositivos, mas não é mais suportado pelo compilador fornecido com o Xcode 4,5 e posterior. 

O ARM64 é necessário para dar suporte ao iOS 8 no iPhone 6 ou outros dispositivos de 64 bits e será exigido pela Apple ao enviar aplicativos novos ou atualizados na iTunes App Store.

Para uma visão abrangente dos recursos de vários dispositivos iOS, confira o documento de [compatibilidade de dispositivo](https://developer.apple.com/library/content/documentation/DeviceInformation/Reference/iOSDeviceCompatibility/DeviceCompatibilityMatrix/DeviceCompatibilityMatrix.html) da Apple.

### <a name="64-bit-and-binary-size-increases"></a>64 bits e tamanho binário aumenta

Durante a transição da Apple de 32 bits para 64 bits, os aplicativos iOS precisarão ser executados no hardware de 32 bits e de 64 bits. Por isso, o API Unificada do Xamarin permite aos desenvolvedores direcionar ambos.

Direcionar as arquiteturas de 32 bits e 64 bits aumentará significativamente o tamanho de um aplicativo. No entanto, isso permitirá que dispositivos mais recentes executem código otimizado e ainda ofereçam suporte a dispositivos mais antigos.

> [!IMPORTANT]
> Se você receber a seguinte mensagem ao enviar um aplicativo iOS para a iTunes App Store, _"Warning ITMS-9000: suporte a 64 bits ausente. A partir de 1º de fevereiro de 2015, novos aplicativos iOS carregados para a App Store devem incluir suporte a 64 bits e ser compilados com o SDK do iOS 8, incluído no Xcode 6 ou posterior. Para habilitar o 64 bits em seu projeto, é recomendável usar a configuração de Build padrão do Xcode de "arquiteturas padrão" para criar um binário único com código de 32 bits e de 64 bits. "_ Você precisa alternar as arquiteturas com suporte para uma das combinações de **ARM64** disponíveis (conforme mostrado acima), recompilar e reenviar.

## <a name="mac"></a>Mac

> [!IMPORTANT]
> A partir de janeiro de 2018, todos os novos aplicativos Mac enviados para o Mac App Store devem dar suporte a 64 bits. Os aplicativos existentes da loja de aplicativos Mac e suas atualizações devem dar suporte a 64 bits a partir de 2018 de junho. Consulte a [apresentação da Apple](https://developer.apple.com/news/?id=06282017a) e [um guia que descreve como atualizar seus aplicativos Xamarin. Mac para 64 bits](~/cross-platform/macios/32-and-64/mac-64-bit.md).

A maioria dos computadores Mac modernos oferece suporte a aplicativos de 32 bits e 64 bits.   O MacOS 10,6 (neve leopardo) foi o último sistema operacional a ser executado em sistemas de 32 bits.   A maioria dos Macs foi lançada desde 2010 para ambos os sistemas.

Ao contrário do iOS, muitas das novas estruturas introduzidas em versões recentes do macOS só têm suporte no modo de 64 bits (CloudKit, Kit, GameController, LocalAuthentication, MediaLibrary, MultipeerConnectivity, NotificationCenter, GLKit, SpriteKit, social e MapKit, entre outros).

O API Unificada permitir que os desenvolvedores escolham o tipo de aplicativos que desejam produzir: 32 bits ou 64 bits.

os **aplicativos de 32 bits** serão executados em computadores Mac de 32 bits e de 64 bits, terão um espaço de endereço limitado a 32 bits e exigirão que todas as bibliotecas sejam 32 bits.

Normalmente, você usará esse modo se tiver dependências de 32 bits que não são executadas no modo de 64 bits, se você quiser ter um download menor ou se não houver nenhum benefício de desempenho ao migrar para o 64-bit.

Esse modo está limitando, pois você não poderá usar muitas estruturas disponíveis no macOS Mavericks e no macOS Yosemite.

**os aplicativos de 64 bits** serão executados somente em dispositivos Mac de 64 bits.

Para Mac, esse é o modo preferencial de operação, pois a maioria dos Macs em uso atualmente dá suporte ao modo de 64 bits e você tem acesso ao conjunto completo de estruturas fornecidas pela Apple.

### <a name="enabling-64-bit-builds-of-xamarinmac-apps"></a>Habilitando compilações de 64 bits de aplicativos Xamarin. Mac

Para obter informações sobre como criar um aplicativo de 64 bits usando o Xamarin. Mac, consulte a [atualização de aplicativos unificados do xamarin. Mac para o guia de 64 bits](~/cross-platform/macios/32-and-64/mac-64-bit.md) .

## <a name="related-links"></a>Links relacionados

- [Diferenças clássicas do vs API Unificada](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md)
