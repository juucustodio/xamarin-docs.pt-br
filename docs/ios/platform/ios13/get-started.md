---
title: Introdução ao iOS 13, iPadOS 13, tvOS 13 e watchOS 6
description: Este documento descreve como ser configurado para compilar aplicativos iOS 13, iPadOS 13, tvOS 13 e watchOS 6 com o Xamarin. Ele discute como baixar o Xcode 11 e atualizar Visual Studio para Mac.
ms.prod: xamarin
ms.assetid: 97414545-85D2-433C-9246-63B6763F488A
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 07/01/2019
ms.openlocfilehash: 1f190ef2f99e71c8cf21680f9902caccf3454450
ms.sourcegitcommit: 09bc69d7119a04684c9e804c5cb113b8b1bb7dfc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71206328"
---
# <a name="get-started-with-ios-13"></a>Introdução ao iOS 13

Este documento descreve como começar a criar aplicativos Xamarin que chamam APIs lançadas com o Xcode 11, para iOS 13. O uso da visualização requer o macOS 10.14.4 (Mojave) ou mais recente.

## <a name="download-and-install"></a>Baixar e instalar

1. **Instalar o Xcode 11** – os desenvolvedores da Apple registrados podem baixar e instalar a versão mais recente do Xcode 11 do [portal do desenvolvedor da Apple](https://developer.apple.com/download/) ou da loja de **aplicativos**.

2. **Executar o Xcode 11** – execute o Xcode 11 antes de atualizar e executar o Visual Studio para Mac, pois ele instala algumas ferramentas exigidas pelo Xamarin.

3. Em Visual Studio para Mac, selecione **Visual Studio > verificar se há atualizações...** , selecione o canal **estável** e instale as atualizações disponíveis.

4. Adicional **Instale o Ios 13 em seus dispositivos IOS** – para testes de dispositivo de aplicativos que usam APIs introduzidas com o Xcode 11, os desenvolvedores da Apple registrados podem [baixar](https://developer.apple.com/download) e instalar o sistema operacional em seus dispositivos. 

   > [!TIP]
   > Mesmo que seu aplicativo não use nenhuma nova API, certifique-se de compilá-lo com os mais recentes SDKs do Xcode 11 e testá-lo para garantir que tudo funcione conforme o esperado. Se um aplicativo não chamar novas APIs, você poderá recompilá-lo com esses novos SDKs e testá-lo em dispositivos que ainda não foram atualizados para o novo sistema operacional.
   >
   > Antes de atualizar seus dispositivos para as versões mais recentes do sistema operacional da Apple para testar seus aplicativos Xamarin, certifique-se de:
   >
   > - Leia as [notas de versão da Apple](https://developer.apple.com/download/) para as atualizações do sistema operacional.

## <a name="related-links"></a>Links relacionados

- [Baixar o Xcode](https://developer.apple.com/download/)
- [Notas sobre a versão do Xamarin.iOS](/xamarin/ios/release-notes/13/13.0)
