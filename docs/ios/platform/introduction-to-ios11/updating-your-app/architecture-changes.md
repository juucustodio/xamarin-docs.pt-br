---
title: Alterações de arquitetura no iOS 11
description: Este documento descreve a substituição de aplicativos de 32 bits no iOS 11. Ele discute como atualizar os aplicativos para arquiteturas de 64 bits de destino.
ms.prod: xamarin
ms.assetid: 55F62F3F-8570-402B-B7D9-2875F76CB946
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/13/2016
ms.openlocfilehash: b7d1df6ed2a8bd480025681ebcbe48acd7592564
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61169330"
---
# <a name="architecture-changes-in-ios-11"></a>Alterações de arquitetura no iOS 11

Uma das maiores alterações que você deve estar atento com o iOS 11 é a substituição do suporte de 32 bits para aplicativos, conforme detalhado na [da Apple](https://developer.apple.com/news/?id=06282017b) comunicado à imprensa. Todos os novos aplicativos e atualizações para os aplicativos existentes devem dar suporte a 64 bits. aplicativos de 32 bits **não serão iniciados** no iOS 11.

Para atualizar seu aplicativo no Visual Studio para Mac, use as seguintes etapas:

1. Clique com botão direito no nome do projeto para abrir **opções de projeto**.
2. Navegue até a **Build do iOS** guia.
3. Defina as **arquiteturas com suporte** lista suspensa para **x86_64** para o **depurar | iPhoneSimulator** e **versão | iPhoneSimulator**:

    ![Alterar a lista suspensa de arquiteturas de simulador](architecture-changes-images/image1.png)

4. Para dispositivos iOS, altere a configuração para **depurar | iPhone** e defina as **arquiteturas com suporte** lista suspensa para **ARM64**:

    ![Alterar a lista suspensa de arquiteturas de dispositivo](architecture-changes-images/image2.png)

5. Altere a configuração para **versão | iPhone** e defina as **arquiteturas com suporte** lista suspensa para **ARM64**.

Para obter mais informações sobre as arquiteturas de 32 bits e 64 bits, consulte o [considerações sobre plataformas de 32/64 bits](~/cross-platform/macios/32-and-64/index.md#ios) guia.

## <a name="related-links"></a>Links relacionados

- [O que há de novo no iOS 11 (Apple)](https://developer.apple.com/ios/)
- [Página de Store de aplicativo atualizado do produto (Apple)](https://developer.apple.com/app-store/product-page/)
- [Atualizar seu aplicativo para iOS 11 (WWDC) (vídeo)](https://developer.apple.com/videos/play/wwdc2017/204/)
