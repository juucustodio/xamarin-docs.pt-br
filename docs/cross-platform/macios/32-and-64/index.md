---
title: Considerações sobre a plataforma de 32/64 bits
description: Este documento descreve várias considerações para ter em mente ao direcionar arquiteturas de 32 bits e 64 bits para um aplicativo xamarin. IOS ou xamarin. Mac.
ms.prod: xamarin
ms.assetid: F7126340-04B2-4A10-B14D-394E23527C1A
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: 31eb0bfae58ecdca40548e46d1d9d95828be67b4
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61347793"
---
# <a name="3264-bit-platform-considerations"></a>Considerações sobre a plataforma de 32/64 bits

Embora o iOS e macOS historicamente têm suporte para aplicativos de 32 e 64 bits, a Apple preteriu gradualmente o suporte de 32 bits.

A partir do iOS 11, aplicativos de 32 bits não serão iniciados, e [todos os envios para o aplicativo Store devem oferecer suporte a 64-bit](https://developer.apple.com/news/?id=06282017b).

A partir de janeiro de 2018 [novos aplicativos enviados para a Mac App Store devem oferecer suporte a 64-bit](https://developer.apple.com/news/?id=06282017a), e os aplicativos existentes devem ser atualizados de junho de 2018.

API de clássico do Xamarin (`XamMac.dll` e `monotouch.dll`) tem suporte somente aplicativos de 32 bits. No entanto, usam os novos aplicativos xamarin. IOS e xamarin. Mac os [API unificada](~/cross-platform/macios/unified/index.md) (`Xamarin.iOS` e `Xamarin.Mac`) por padrão, e, portanto, pode direcionar 32 e 64 bits, conforme necessário.

## <a name="ios"></a>iOS

<a name="enable-64" />

### <a name="enabling-64-bit-builds-of-xamarinios-apps"></a>Habilitando 64-bit compilações de aplicativos xamarin. IOS

> [!WARNING]
> Esta seção está incluída por razões históricas e para ajudar a mover projetos mais antigos do xamarin. IOS para a API unificada e dar suporte a 64 bits. Todos os novos projetos do xamarin. IOS usará a API unificada e o destino de 64 bits por padrão.

Para aplicativos móveis xamarin. IOS que foram convertidos para a API unificada, os desenvolvedores devem atualizar manualmente as configurações de build para 64 bits de destino:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. No **painel de soluções**, clique duas vezes no projeto do aplicativo para abrir o **opções de projeto** janela.
2. Selecione **Build do iOS**.
3. Para o iPhone simulador, no **arquiteturas com suporte** lista suspensa, selecione **x86\_64** ou **i386 + x86\_64**:

   [![A configuração de arquiteturas com suporte como x86\_64 ou i386 + x86\_64](Images/Image01.png "Setting Supported architectures to x86\_64 or i386 + x86\_64")](Images/Image01-large.png#lightbox) 

4. Para dispositivos físicos, selecione uma das disponíveis **ARM64** combinações:

   [![A configuração de arquiteturas com suporte como uma das combinações ARM64](Images/Image02.png "arquiteturas de configuração com suporte para uma das combinações ARM64")](Images/Image02-large.png#lightbox)

5. Clique em **OK**.
6. Execute uma compilação limpa.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. No **Gerenciador de soluções**, clique com botão direito do aplicativo e selecione **propriedades**.
2. Selecione **Build do iOS**.
3. Para o simulador do iPhone, defina **arquiteturas com suporte** para um **x86\_64** ou **i386 + x86\_64**: 

   [![A configuração de arquiteturas com suporte como x86_64 ou i386 + x86\_64](Images/VS02.png "Setting Supported architectures to x86_64 or i386 + x86\_64")](Images/VS02-large.png#lightbox)

4. Para dispositivos físicos, selecione uma das disponíveis **ARM64** combinações:
    
   [![A configuração de arquiteturas com suporte como uma das combinações ARM64](Images/VS01.png "arquiteturas de configuração com suporte para uma das combinações ARM64")](Images/VS01-large.png#lightbox)

5. Salve as alterações.
6. Execute uma compilação limpa.

-----

ARMv7s é suportado apenas pelo processador A6 incluído no iPhone 5 (ou superior). ARMv7 código é mais rápido e menor do que a ARMv6, só funciona com o iPhone 3GS e versões posteriores e é exigido pela Apple ao direcionar o iPad ou uma versão mínima do iOS do 5.0. ARMv6 funciona em todos os dispositivos, mas não é mais suportado pelo compilador fornecido com o Xcode 4.5 e posterior. 

ARM64 é necessária para dar suporte ao iOS 8 no iPhone 6 ou outros dispositivos de 64 bits e será exigido pela Apple ao enviar novos ou atualizar saindo de aplicativos na iTunes App Store.

Para obter uma visão abrangente dos recursos de vários dispositivos iOS, check-out da Apple [compatibilidade de dispositivos](https://developer.apple.com/library/content/documentation/DeviceInformation/Reference/iOSDeviceCompatibility/DeviceCompatibilityMatrix/DeviceCompatibilityMatrix.html) documento.

### <a name="64-bit-and-binary-size-increases"></a>aumenta de tamanho de 64 bits e binário

Durante a transição da Apple do iOS de 32 bits para 64 bits, aplicativos precisará ser executado em hardware de 32 bits e 64 bits. Por isso, a API unificada do Xamarin permite que os desenvolvedores se concentrem ambos.

Direcionamento de arquiteturas de 32 bits e 64 bits aumentará significativamente o tamanho de um aplicativo. No entanto, fazer isso permitirá dispositivos mais recentes executar o código otimizado e ainda dar suporte a dispositivos mais antigos.

> [!IMPORTANT]
> Se você receber a seguinte mensagem de erro ao enviar um aplicativo iOS ao iTunes App Store, _"aviso ITMS-9000: Suporte de 64 bits ausente. Começando em 1º de fevereiro de 2015, novo iOS aplicativos carregados para o aplicativo Store devem incluir o suporte de 64 bits e ser criados com o iOS 8 SDK, incluído no Xcode 6 ou posterior. Para habilitar 64-bit em seu projeto, é recomendável usar o padrão de definição de "Standard architectures" de build do Xcode para criar um único binário com o código de 32 bits e 64 bits. "_ Você precisa alternar as arquiteturas com suporte para um dos disponíveis **ARM64** combinação (como mostrado acima), recompile e envie novamente.

## <a name="mac"></a>Mac

> [!IMPORTANT]
> A partir de janeiro de 2018, novos aplicativos Mac, enviados para a Mac App Store devem dar suporte a 64 bits. Os aplicativos existentes do Mac App Store e suas atualizações devem oferecer suporte de 64 bits a partir de junho de 2018. Ver [comunicados da Apple](https://developer.apple.com/news/?id=06282017a) e [um guia que descreve como atualizar seus aplicativos xamarin. Mac para 64 bits](~/cross-platform/macios/32-and-64/mac-64-bit.md).

Computadores de Mac mais modernos dão suporte a aplicativos de 32 bits e 64 bits.   10.6 MacOS (Leopard neve) foi o último sistema operacional seja executado nos sistemas de 32 bits.   A maioria dos Macs lançados, pois 2010 dá suporte a ambos os sistemas.

Ao contrário do iOS, muitas das novas estruturas introduzidas em versões recentes do macOS só têm suporte no modo de 64 bits (CloudKit, Kit de eventos, GameController, LocalAuthentication, MediaLibrary, MultipeerConnectivity, NotificationCenter, GLKit, SpriteKit, Social, e MapKit, entre outros).

A API unificada permitem aos desenvolvedores escolher quais tipos de aplicativos desejam produzir: 32 bits ou 64 bits.

**aplicativos de 32 bits** serão executados em computadores Mac de 32 bits e 64 bits, tem um espaço de endereço limitado a 32 bits e exige que todas as bibliotecas são de 32 bits.

Normalmente você usará esse modo se você tiver dependências de 32 bits que não são executados no modo de 64 bits, se você quiser ter um download menor, ou se não houver nenhum benefício de desempenho na movimentação para 64 bits.

Esse modo está limitando a, você não poderá usar muitas estruturas disponíveis no macOS Mavericks e o macOS Yosemite.

**aplicativos de 64 bits** só será executado em dispositivos Mac de 64 bits.

Para Mac, isso é o modo preferido de operação, como a maioria dos Macs em uso atualmente dão suporte a modo de 64 bits, e você tem acesso ao conjunto completo de estruturas fornecidas pela Apple.

### <a name="enabling-64-bit-builds-of-xamarinmac-apps"></a>Habilitando 64-bit compilações de aplicativos xamarin. Mac

Para obter informações sobre a criação de um aplicativo de 64 bits usando o xamarin. Mac, consulte a [aplicativos atualizando unificado do xamarin. Mac para 64 bits](~/cross-platform/macios/32-and-64/mac-64-bit.md) guia.

## <a name="related-links"></a>Links relacionados

- [Diferenças de API unificada do vs clássico](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
