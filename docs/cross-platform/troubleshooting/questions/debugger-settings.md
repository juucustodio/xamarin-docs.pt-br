---
title: Quais configurações do projeto são necessárias para o depurador?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 3A024E4E-ACA3-4C7A-ADEF-541665D15779
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.openlocfilehash: 7dff69e90b5105401da162c52053d884ed9b038b
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
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

