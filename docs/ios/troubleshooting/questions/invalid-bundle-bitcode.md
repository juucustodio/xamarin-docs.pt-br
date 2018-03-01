---
title: "Erro ao enviar ao repositório de aplicativo: \"O pacote inválido - opções não podem ser inseridas em bitcode forem detectadas no envio\""
ms.topic: article
ms.prod: xamarin
ms.assetid: 137313FB-3D29-428B-93C1-5A05DC8F7C03
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: b8ce643d392945e7e746c28b13063a2b0b7ebb48
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="error-when-submitting-to-app-store-invalid-bundle---options-not-allowed-to-be-embedded-in-bitcode-are-detected-in-the-submission"></a>Erro ao enviar ao repositório de aplicativo: "O pacote inválido - opções não podem ser inseridas em bitcode forem detectadas no envio"

watchOS aplicativos e tvOS _exigem_ bitcode quando eles são enviados à loja de aplicativos. Ao criar e enviar aplicativos watchOS e tvOS usando o Xcode 8.3 ou anterior, o seguinte erro pode ocorrer (por meio de notificação por email) ao tentar carregar a loja de aplicativos:

>Pacote inválido - o aplicativo não pode ser processado porque as opções não podem ser inseridas em bitcode são detectadas o envio. É provável que você não está criando o aplicativo com a cadeia de ferramentas fornecida no Xcode.

A solução para esse problema é criar aplicativos com o Xcode 9 e a última versão do xamarin.
