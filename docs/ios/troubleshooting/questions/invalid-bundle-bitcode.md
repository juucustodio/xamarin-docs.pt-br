---
title: 'Erro ao enviar para a loja de aplicativos: "as opções de agrupamento inválidas que não podem ser inseridas em BitCode são detectadas no envio"'
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 137313FB-3D29-428B-93C1-5A05DC8F7C03
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/03/2018
ms.openlocfilehash: c6a03fa3327e81f577f85b05a033d9c97495eb2e
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031072"
---
# <a name="error-when-submitting-to-app-store-invalid-bundle---options-not-allowed-to-be-embedded-in-bitcode-are-detected-in-the-submission"></a>Erro ao enviar para a loja de aplicativos: "as opções de agrupamento inválidas que não podem ser inseridas em BitCode são detectadas no envio"

aplicativos watchOS e aplicativos tvOS _exigem_ BitCode quando são enviados para a loja de aplicativos. Ao criar e enviar aplicativos watchOS e tvOS usando o Xcode 8,3 ou anterior, o seguinte erro pode ocorrer (por meio de notificação por email) ao tentar carregar para a loja de aplicativos:

>Pacote inválido-o aplicativo não pode ser processado porque as opções que não podem ser inseridas em BitCode são detectadas no envio. É provável que você não esteja criando o aplicativo com o ferramentas fornecido no Xcode.

A solução para esse problema é criar aplicativos com o Xcode 9 e a versão mais recente do Xamarin. iOS.
