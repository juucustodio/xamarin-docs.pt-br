---
title: Quais configurações do projeto são necessárias para que o depurador?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 3A024E4E-ACA3-4C7A-ADEF-541665D15779
author: asb3993
ms.author: amburns
ms.date: 05/08/2018
ms.openlocfilehash: 646ef7f708be2de6a851ace25d69a7c2f0b18a83
ms.sourcegitcommit: aa9b9b203ab4cd6a6b4fd51e27d865e2abf582c1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2018
ms.locfileid: "39350801"
---
# <a name="what-project-settings-are-required-for-the-debugger"></a>Quais configurações do projeto são necessárias para que o depurador?

Em ordem para o depurador funcionar conforme o esperado (ocorrências de pontos de interrupção, exibir logs de depuração, etc.), exibição de informações de instrumentação e depuração de desenvolvedor deverão estar habilitada.

Siga estas etapas para verificar as configurações de ambiente:

## <a name="visual-studio"></a>Visual Studio
1. Abrir as opções de projeto
2. Vá para **Build > Avançado...** Definir informações de depuração para **completo**
3. Configurações para cada plataforma:
   - Vá para **opções do Android > Opções de depuração**. Escala de **habilitar instrumentação do desenvolvedor** caixa.
   - Vá para **Build do iOS > Opções de depuração**. Escala de **Ativar depuração** caixa.

## <a name="visual-studio-for-mac"></a>Visual Studio para Mac
1. Abrir as opções de projeto
2. Vá para **Build > compilador > Opções gerais**. Definir informações de depuração para **completo**
3. Configurações para cada plataforma:
  - Vá para **Build > Build do Android > Opções de depuração**. Escala de **habilitar instrumentação do desenvolvedor** caixa.
  - Vá para **Build > depuração do iOS**. Escala de **Ativar depuração** caixa.

