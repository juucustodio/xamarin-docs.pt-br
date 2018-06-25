---
title: Implantação sem fio para aplicativos Xamarin.iOS e tvOS
description: Este documento descreve como implantar sem fio um aplicativo Xamarin.iOS em um dispositivo iOS por meio do Visual Studio para Mac ou do Visual Studio 2017.
ms.prod: xamarin
ms.assetid: 5AB4C5A9-4FBB-4DCB-BD72-0022D5439E65
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 02/09/2018
ms.openlocfilehash: ade7eb7ff26fec8df616401801585e499ddf4206
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34785512"
---
# <a name="wireless-deployment-for-xamarinios-and-tvos-apps"></a>Implantação sem fio para aplicativos Xamarin.iOS e tvOS

Uma parte importante do fluxo de trabalho do desenvolvedor é a implantação em um dispositivo. O Xcode 9 introduziu a opção de implantação em um dispositivo iOS ou Apple TV por meio de uma rede, em vez de exigir instalações físicas sempre que você quiser implantar e depurar o aplicativo. Esse recurso foi introduzido nas versões do Visual Studio para Mac 7.4 e do Visual Studio 15.6.

Este guia explica como emparelhar e implantar em um dispositivo na rede.

## <a name="requirements"></a>Requisitos

A implantação sem fio está disponível como um recurso no Visual Studio para Mac e no Visual Studio.

Para usar a implantação sem fio, você deve ter o seguinte:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

- macOS 10.12.4
- A versão mais recente do Visual Studio para Mac
- Xcode 9.0 ou posterior
- Um dispositivo com iOS 11.0 ou tvOS 11.0 e posterior

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

- A versão mais recente do Visual Studio
- Um dispositivo com iOS 11.0 ou tvOS 11.0 e posterior

No host de compilação do Mac, os componentes a seguir devem ser instalados:

- macOS 10.12.4
- Visual Studio para Mac
- Xcode 9.0 ou posterior

-----

## <a name="connecting-a-device"></a>Conectar um dispositivo

Para implantar e depurar sem fio no dispositivo, você deve emparelhar o dispositivo iOS ou Apple TV com o Xcode no Mac. Depois do emparelhamento, você pode selecioná-lo na lista de dispositivos de destino no Visual Studio. 

O processo de emparelhamento a seguir só é necessário uma vez por dispositivo. O Xcode manterá as configurações de conexão.

<a name="pair" />

### <a name="pairing-an-ios-device-with-xcode"></a>Emparelhamento de um dispositivo iOS com o Xcode

1. Abra o Xcode e vá para **Janela > Dispositivos e Simuladores**.
2. Conecte o dispositivo iOS ao Mac usando um cabo de alimentação. Talvez seja necessário selecionar **Confiar Neste Computador** no dispositivo.
3. Selecione o dispositivo e marque a caixa de seleção **Conectar via rede** para emparelhá-lo: ![janela Dispositivo e Simulador mostrando a opção Conectar via rede](wireless-deployment-images/image2.png)

### <a name="pairing-an-apple-tv-with-xcode"></a>Emparelhamento de uma Apple TV com o Xcode

1. O Mac e a Apple TV devem estar conectados à mesma rede.

2. Abra o Xcode e vá para **Janela > Dispositivos e Simuladores**.

3. Na Apple TV, vá para **Ajustes > Remotos e Dispositivos > Aplicativo Remoto e Dispositivos**.

4. Selecione a Apple TV na área **Descoberto** do Xcode e insira o código de verificação exibido na Apple TV.

5. Clique no botão **Conectar**. Após o emparelhamento, um ícone de conexão de rede é exibido ao lado da Apple TV.

## <a name="deploy-to-a-device"></a>Implantar em um dispositivo

Quando um dispositivo está conectado sem fio e pronto para ser usado para a implantação, ele é exibido na lista de dispositivos de destino, como se estivesse conectado por USB.

Para testar em um dispositivo físico, ele deve ser [provisionado](~/ios/get-started/installation/device-provisioning/index.md). Faça isso antes de tentar implantar em um dispositivo. 

Para implantar em um dispositivo iOS ou tvOS, siga as seguintes etapas:

1. A máquina de implantação e o dispositivo de destino devem estar na mesma rede sem fio. 

2. Selecione o dispositivo na lista de dispositivos de destino e execute o aplicativo.

2. Se o dispositivo estiver bloqueado, você será solicitado a desbloqueá-lo. Depois que o dispositivo estiver desbloqueado, o aplicativo será implantado nele.

A depuração sem fio é habilitada automaticamente após a implantação sem fio, para que seja possível usar pontos de interrupção previamente definidos e continuar o fluxo de trabalho de depuração como sempre.

## <a name="troubleshooting"></a>Solução de problemas

1. Sempre verifique se o dispositivo iOS ou Apple TV está conectado à mesma rede que o Mac.

2. Se o dispositivo não aparecer no Visual Studio, verifique a janela **Dispositivos e Simuladores** do Xcode. 

    * Se o Xcode **não** mostrar o dispositivo como conectado, tente [emparelhar](#pair) o dispositivo novamente.

    * Se o Xcode mostrar o dispositivo como conectado, tente reiniciar o Visual Studio e o dispositivo.

3. Se você ainda não tiver feito isso, será preciso [provisionar](~/ios/get-started/installation/device-provisioning/index.md) o dispositivo.

4. Caso tenha problemas com esse recurso que não possam ser corrigidos com as etapas anteriores, registre um problema na [Comunidade de Desenvolvedores](https://developercommunity.visualstudio.com/spaces/41/index.html).

## <a name="related-links"></a>Links relacionados

- [Emparelhar um dispositivo sem fio com o Xcode](https://help.apple.com/xcode/mac/9.0/index.html?localePath=en.lproj#/devbc48d1bad)
