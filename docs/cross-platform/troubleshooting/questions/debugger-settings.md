---
title: "Quais configurações do projeto são necessárias para o depurador?"
ms.topic: article
ms.prod: xamarin
ms.assetid: 3A024E4E-ACA3-4C7A-ADEF-541665D15779
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.openlocfilehash: 6097dc8dfdff8807137ef68be86a08e4c9e23988
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="what-project-settings-are-required-for-the-debugger"></a>Quais configurações do projeto são necessárias para o depurador?

Para o depurador a funcionar como esperado (ocorrências de pontos de interrupção, exibir logs de depuração, etc.), exibição de informações de instrumentação e depuração de desenvolvedor deve ambos ser habilitada.

Siga estas etapas para verificar as configurações de ambiente:

## <a name="visual-studio"></a>Visual Studio
1. Abrir as opções de projeto
2. Vá para **Build > Avançado...** Definir as informações de depuração **completo**
3. Configurações para cada plataforma:
   - Vá para **Android opções > depuração opções**. Escala de **habilitar a instrumentação do desenvolvedor** caixa.
   - Vá para **iOS compilação > Opções de depuração**. Escala de **Ativar depuração** caixa.

## <a name="visual-studio-for-mac"></a>Visual Studio para Mac
1. Abrir as opções de projeto
2. Vá para **Build > compilador > Opções gerais**. Definir as informações de depuração **completo**
3. Configurações para cada plataforma:
  - Vá para **Construir > compilação Android > Opções de depuração**. Escala de **habilitar a instrumentação do desenvolvedor** caixa.
  - Vá para **Build > iOS depuração**. Escala de **Ativar depuração** caixa.

