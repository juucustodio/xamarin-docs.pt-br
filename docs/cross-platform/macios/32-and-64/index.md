---
title: Considerações sobre a plataforma de 32/64 bits
description: Este documento descreve as várias considerações para ter em mente ao direcionar arquiteturas de 32 bits e 64 bits para um aplicativo xamarin ou Xamarin.Mac.
ms.prod: xamarin
ms.assetid: F7126340-04B2-4A10-B14D-394E23527C1A
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/29/2017
ms.openlocfilehash: c722efc0bc6e8a4ea29af603f88c0e0644c2ed8c
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34781543"
---
# <a name="3264-bit-platform-considerations"></a>Considerações sobre a plataforma de 32/64 bits

Embora iOS e macOS historicamente tem suporte para aplicativos de 32 e 64 bits, Apple gradualmente foi preterido suporte de 32 bits.

A partir do iOS 11, aplicativos de 32 bits não serão iniciado, e [todos os envios à loja de aplicativos devem oferecer suporte a 64-bit](https://developer.apple.com/news/?id=06282017b).

Iniciando em janeiro de 2018, [novos aplicativos enviados ao Mac App Store devem oferecer suporte a 64 bits](https://developer.apple.com/news/?id=06282017a), e os aplicativos existentes devem ser atualizados por de 2018 de junho.

API clássica do Xamarin (`XamMac.dll` e `monotouch.dll`) tem suporte somente aplicativos de 32 bits. No entanto, usam os novos aplicativos xamarin e Xamarin.Mac o [API unificada](~/cross-platform/macios/unified/index.md) (`Xamarin.iOS` e `Xamarin.Mac`) por padrão, e pode, portanto, destino de 32 e 64 bits, conforme necessário.

## <a name="ios"></a>iOS

<a name="enable-64" />

### <a name="enabling-64-bit-builds-of-xamarinios-apps"></a>Habilitação de 64 bits compilações de aplicativos xamarin

> [!WARNING]
> Esta seção está incluída por razões históricas e para ajudar a mover projetos mais antigos do xamarin para a API unificada de 64 bits. Todos os novos projetos de xamarin usará a API unificada e o destino de 64 bits por padrão.

Para aplicativos móveis xamarin que foram convertidos para a API unificada, os desenvolvedores devem atualizar manualmente as configurações de compilação para o destino de 64 bits:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. No **solução preenchimento**, clique duas vezes no projeto do aplicativo para abrir o **opções de projeto** janela.
2. Selecione **iOS compilação**.
3. Para o simulador de iPhone no **arquiteturas compatíveis** lista suspensa, selecione **x86\_64** ou **i386 + x86\_64**:

   [![Definindo as arquiteturas com suporte para x86\_64 ou i386 + x86\_64](Images/Image01.png "Setting Supported architectures to x86\_64 or i386 + x86\_64")](Images/Image01-large.png#lightbox) 

4. Para dispositivos físicos, selecione uma das disponíveis **ARM64** combinações:

   [![Definindo as arquiteturas com suporte para uma das combinações ARM64](Images/Image02.png "arquiteturas de configuração com suporte para uma das combinações ARM64")](Images/Image02-large.png#lightbox)

5. Clique em **OK**.
6. Execute uma compilação limpa.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. No **Solution Explorer**, clique com botão direito do aplicativo e selecione **propriedades**.
2. Selecione **iOS compilação**.
3. Para o simulador de iPhone, defina **arquiteturas compatíveis** como **x86\_64** ou **i386 + x86\_64**: 

   [![Definindo as arquiteturas com suporte para x86_64 ou i386 + x86\_64](Images/VS02.png "Setting Supported architectures to x86_64 or i386 + x86\_64")](Images/VS02-large.png#lightbox)

4. Para dispositivos físicos, selecione uma das disponíveis **ARM64** combinações:
    
   [![Definindo as arquiteturas com suporte para uma das combinações ARM64](Images/VS01.png "arquiteturas de configuração com suporte para uma das combinações ARM64")](Images/VS01-large.png#lightbox)

5. Salve as alterações.
6. Execute uma compilação limpa.

-----

ARMv7s é suportado apenas pelo processador A6 incluído no iPhone 5 (ou superior). Código ARMv7 é mais rápido e menor do que o ARMv6, só funciona com o 3GS iPhone e versões posteriores e é exigido pela Apple quando o objetivo iPad ou uma versão mínima do iOS do 5.0. ARMv6 funciona em todos os dispositivos, mas não é mais suportado pelo compilador fornecido com o Xcode 4.5 e posterior. 

ARM64 é necessário para dar suporte a iOS 8 no iPhone 6 ou outros dispositivos de 64 bits e serão exigidas pela Apple durante o envio de novo ou atualização saindo de aplicativos na iTunes App Store.

Para obter uma visão abrangente dos recursos de vários dispositivos iOS, check-out da Apple [dispositivo compatibilidade](https://developer.apple.com/library/content/documentation/DeviceInformation/Reference/iOSDeviceCompatibility/DeviceCompatibilityMatrix/DeviceCompatibilityMatrix.html) documento.

### <a name="64-bit-and-binary-size-increases"></a>aumenta o tamanho de 64 bits e binário

Durante a transição da Apple do iOS de 32 bits para 64 bits, serão necessário aplicativos executados em hardware de 32 bits e 64 bits. Por isso, Unified API do Xamarin permite aos desenvolvedores alvo ambos.

Direcionamento de arquiteturas de 32 bits e 64 bits aumentar significativamente o tamanho de um aplicativo. No entanto, fazer assim permitirá que os dispositivos mais recentes executar código otimizado e ainda dar suporte a dispositivos mais antigos.

> [!IMPORTANT]
> Se você receber a seguinte mensagem de erro ao enviar um aplicativo iOS na iTunes App Store, _"aviso ITMS-9000: sem suporte de 64 bits. Iniciando iOS de 1º de fevereiro de 2015, novos aplicativos carregados para a loja de aplicativos devem incluir suporte para 64 bits e ser compilados com o iOS 8 SDK, incluído no Xcode 6 ou posterior. Para habilitar 64 bits em seu projeto, é recomendável usar o padrão de configuração de "Arquiteturas padrão" de compilação do Xcode para criar um único binário com o código de 32 bits e 64 bits. "_ É necessário alternar as arquiteturas com suporte para um disponíveis do **ARM64** combinação (conforme mostrado acima), recompilação e envie novamente.

## <a name="mac"></a>Mac

> [!IMPORTANT]
> A partir de janeiro de 2018, novos aplicativos Mac, enviados para a loja de aplicativos Mac devem suportar 64 bits. Aplicativos existentes do Mac App Store e suas atualizações devem oferecer suporte de 64 bits a partir de junho de 2018. Consulte [lançamento da Apple](https://developer.apple.com/news/?id=06282017a) e [um guia que descreve como atualizar seus aplicativos Xamarin.Mac para 64 bits](~/cross-platform/macios/32-and-64/mac-64-bit.md).

Computadores de Mac mais modernos oferecem suporte a aplicativos de 32 bits e 64 bits.   MacOS 10.6 (Leopard neve) foi o último sistema operacional sejam executados em sistemas de 32 bits.   A maioria dos Macs lançadas desde 2010 dão suporte a ambos os sistemas.

Ao contrário do iOS, muitas das novas estruturas de introduzidos em versões recentes do macOS só têm suporte no modo de 64 bits (CloudKit, EventKit, GameController, LocalAuthentication, MediaLibrary, MultipeerConnectivity, NotificationCenter, GLKit, SpriteKit, Social, e MapKit, entre outros).

A API unificada permitem que os desenvolvedores escolher o tipo de aplicativos desejam produzir: 32 bits ou 64 bits.

**aplicativos de 32 bits** executados em computadores Mac de 32 bits e 64 bits, tem um espaço de endereço limitado a 32 bits e exige que todas as bibliotecas são de 32 bits.

Normalmente você usará esse modo se você tiver dependências de 32 bits que não são executados no modo de 64 bits, se você deseja ter um download menor, ou se não houver nenhum benefícios de desempenho para mover para 64 bits.

Esse modo é limitar o que você não poderá usar muitas estruturas disponíveis no macOS Mavericks e macOS Yosemite.

**aplicativos 64-bit** será executado somente em dispositivos de Mac de 64 bits.

Para Mac, esse é o modo preferido de operação como Macs mais em uso hoje suportam modo de 64 bits, e você tem acesso ao conjunto completo de estruturas fornecidas pela Apple.

### <a name="enabling-64-bit-builds-of-xamarinmac-apps"></a>Habilitação de 64 bits compilações de Xamarin.Mac aplicativos

Para obter informações sobre como criar um aplicativo de 64 bits usando Xamarin.Mac, consulte o [atualização Xamarin.Mac Unified aplicativos 64-bit](~/cross-platform/macios/32-and-64/mac-64-bit.md) guia.

## <a name="related-links"></a>Links relacionados

- [Diferenças de API unificada do vs clássico](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
