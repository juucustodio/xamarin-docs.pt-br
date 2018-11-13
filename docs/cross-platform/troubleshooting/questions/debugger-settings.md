---
title: Quais configurações do projeto são necessárias para o depurador?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 3A024E4E-ACA3-4C7A-ADEF-541665D15779
author: asb3993
ms.author: amburns
ms.date: 05/08/2018
ms.openlocfilehash: 9a18c97ba227615ae42529424b5c22b5e144f5e5
ms.sourcegitcommit: 7eed80186e23e6aff3ddbbf7ce5cd1fa20af1365
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/11/2018
ms.locfileid: "51526697"
---
# <a name="what-project-settings-are-required-for-the-debugger"></a>Quais configurações do projeto são necessárias para o depurador?

Em ordem para o depurador funcionar conforme o esperado (ocorrências de pontos de interrupção, exibir logs de depuração, etc.), exibição de informações de instrumentação e depuração de desenvolvedor deverão estar habilitada.

Siga estas etapas para verificar as configurações de ambiente:

## <a name="visual-studio"></a>Visual Studio
1. Abrir as opções de projeto
2. Vá para **Build > Avançado...** Definir informações de depuração para **completo**
3. Configurações para cada plataforma:
   - Vá para **opções do Android > Opções de depuração**. Escala de **habilitar instrumentação do desenvolvedor** caixa.
   - Vá para **depuração do iOS > Depuração & instrumentação**. Escala de **Ativar depuração** caixa.

## <a name="visual-studio-for-mac"></a>Visual Studio para Mac
1. Abrir as opções de projeto
2. Vá para **Build > compilador > Opções gerais**. Definir informações de depuração para **completo**
3. Configurações para cada plataforma:
  - Vá para **Build > Build do Android > Opções de depuração**. Escala de **habilitar instrumentação do desenvolvedor** caixa.
  - Vá para **Build > depuração do iOS**. Escala de **Ativar depuração** caixa.

