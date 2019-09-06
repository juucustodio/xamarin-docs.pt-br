---
title: Alterações de arquitetura no iOS 11
description: Este documento descreve a reprovação de aplicativos de 32 bits no iOS 11. Ele aborda como atualizar aplicativos para as arquiteturas de destino de 64 bits.
ms.prod: xamarin
ms.assetid: 55F62F3F-8570-402B-B7D9-2875F76CB946
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 09/13/2016
ms.openlocfilehash: 15cd6139cc83639146e6044d2b791d94ee30f2d9
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70286344"
---
# <a name="architecture-changes-in-ios-11"></a>Alterações de arquitetura no iOS 11

Uma das maiores alterações que você deve estar ciente com o iOS 11 é a substituição do suporte de 32 bits para aplicativos, conforme detalhado na versão [de imprensa da Apple](https://developer.apple.com/news/?id=06282017b) . Todos os novos aplicativos e atualizações para aplicativos existentes devem dar suporte a 64 bits. os aplicativos de 32 bits **não serão iniciados** no Ios 11.

Para atualizar seu aplicativo no Visual Studio para Mac, use as seguintes etapas:

1. Clique com o botão direito do mouse no nome do projeto para abrir **Opções de projeto**.
2. Navegue até a guia **Build do IOS** .
3. Defina a lista suspensa **arquiteturas com suporte** para **x86_64** para **debug | iPhoneSimulator** e **Release | iPhoneSimulator**:

    ![Menu suspenso de arquiteturas do simulador de alteração](architecture-changes-images/image1.png)

4. Para dispositivos iOS, altere a configuração para **depurar | iPhone** e defina a lista suspensa **arquiteturas com suporte** para **ARM64**:

    ![Menu suspenso alterar arquiteturas de dispositivo](architecture-changes-images/image2.png)

5. Altere a configuração para **Release | iPhone** e defina a lista suspensa **arquiteturas com suporte** para **ARM64**.

Para obter mais informações sobre as arquiteturas de 32 bits e de 64 bits, consulte o guia de [considerações de plataforma do 32/64 bit](~/cross-platform/macios/32-and-64/index.md#ios) .

## <a name="related-links"></a>Links relacionados

- [O que há de novo no iOS 11 (Apple)](https://developer.apple.com/ios/)
- [Página de produto da App Store atualizada (Apple)](https://developer.apple.com/app-store/product-page/)
- [Atualizando seu aplicativo para iOS 11 (WWDC) (vídeo)](https://developer.apple.com/videos/play/wwdc2017/204/)
