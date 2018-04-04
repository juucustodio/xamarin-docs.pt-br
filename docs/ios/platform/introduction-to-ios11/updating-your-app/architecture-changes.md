---
title: Alterações de arquitetura
description: Explorando os novos recursos do iOS 11
ms.prod: xamarin
ms.assetid: 55F62F3F-8570-402B-B7D9-2875F76CB946
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/13/2016
ms.openlocfilehash: 6dd874fd803d2b3de21b0b604cbc2bafa5bf1aec
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="architecture-changes"></a>Alterações de arquitetura

_Explorando os novos recursos do iOS 11_

Uma das maiores alterações que você deve estar atento com iOS 11 é a substituição do suporte de 32 bits para aplicativos, conforme detalhado no [da Apple](https://developer.apple.com/news/?id=06282017b) imprensa. Todos os novos aplicativos e atualizações para aplicativos existentes devem oferecer suporte a 64 bits. aplicativos de 32 bits **não serão iniciados** no iOS 11.

Para atualizar seu aplicativo no Visual Studio para Mac, use as seguintes etapas:

1. Clique com botão direito no nome do projeto para abrir **opções de projeto**.
2. Navegue até o **iOS compilação** guia.
3. Definir o **arquiteturas compatíveis** lista suspensa para **x86_64** para o **depurar | iPhoneSimulator** e **versão | iPhoneSimulator**:

    ![Alterar a lista suspensa de arquiteturas de simulador](architecture-changes-images/image1.png)

4. Para dispositivos iOS, altere a configuração para **depurar | iPhone** e defina o **arquiteturas compatíveis** lista suspensa para **ARM64**:

    ![Alterar a lista suspensa de arquiteturas de dispositivo](architecture-changes-images/image2.png)

5. Alterar a configuração para **versão | iPhone** e defina o **arquiteturas compatíveis** lista suspensa para **ARM64**.

Para obter mais informações sobre as arquiteturas de 32 bits e 64 bits, consulte o [considerações sobre a plataforma de 32/64 bits](~/cross-platform/macios/32-and-64/index.md#ios) guia.

## <a name="related-links"></a>Links relacionados

- [O que há de novo no iOS 11 (Apple)](https://developer.apple.com/ios/)
- [Página de produto atualizados App Store (Apple)](https://developer.apple.com/app-store/product-page/)
- [Atualizar seu aplicativo para iOS 11 (WWDC) (vídeo)](https://developer.apple.com/videos/play/wwdc2017/204/)
