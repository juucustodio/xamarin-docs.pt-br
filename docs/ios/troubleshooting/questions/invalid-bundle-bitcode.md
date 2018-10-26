---
title: 'Erro ao enviar para a App Store: "O pacote inválido – as opções não podem ser inseridas no bitcode são detectadas no envio"'
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 137313FB-3D29-428B-93C1-5A05DC8F7C03
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 04/03/2018
ms.openlocfilehash: 867ad29abfa6a38971b60ac9ebf181905949dafd
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50106982"
---
# <a name="error-when-submitting-to-app-store-invalid-bundle---options-not-allowed-to-be-embedded-in-bitcode-are-detected-in-the-submission"></a>Erro ao enviar para a App Store: "O pacote inválido – as opções não podem ser inseridas no bitcode são detectadas no envio"

watchOS aplicativos e tvOS _exigem_ bitcode quando eles são enviados para a App Store. Ao criar e enviar aplicativos watchOS e tvOS usando o Xcode 8.3 ou anterior, o seguinte erro pode ocorrer (por meio de notificação por email) ao tentar carregar para a App Store:

>Pacote inválido – o aplicativo não pode ser processado porque as opções não podem ser inseridas no bitcode são detectadas no envio. É provável que você não está compilando o aplicativo com a cadeia de ferramentas fornecida no Xcode.

A solução para esse problema é criar os aplicativos com o Xcode 9 e a versão mais recente do xamarin. IOS.
