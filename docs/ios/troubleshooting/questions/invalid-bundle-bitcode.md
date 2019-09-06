---
title: 'Erro ao enviar para a loja de aplicativos: "As opções de agrupamento inválidas que não têm permissão para serem inseridas no BitCode são detectadas no envio"'
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 137313FB-3D29-428B-93C1-5A05DC8F7C03
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 04/03/2018
ms.openlocfilehash: 84244e0c4c24a8ca6ac71a79de963bedf5c1ee68
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70292542"
---
# <a name="error-when-submitting-to-app-store-invalid-bundle---options-not-allowed-to-be-embedded-in-bitcode-are-detected-in-the-submission"></a>Erro ao enviar para a loja de aplicativos: "As opções de agrupamento inválidas que não têm permissão para serem inseridas no BitCode são detectadas no envio"

aplicativos watchOS e aplicativos tvOS _exigem_ BitCode quando são enviados para a loja de aplicativos. Ao criar e enviar aplicativos watchOS e tvOS usando o Xcode 8,3 ou anterior, o seguinte erro pode ocorrer (por meio de notificação por email) ao tentar carregar para a loja de aplicativos:

>Pacote inválido-o aplicativo não pode ser processado porque as opções que não podem ser inseridas em BitCode são detectadas no envio. É provável que você não esteja criando o aplicativo com o ferramentas fornecido no Xcode.

A solução para esse problema é criar aplicativos com o Xcode 9 e a versão mais recente do Xamarin. iOS.
