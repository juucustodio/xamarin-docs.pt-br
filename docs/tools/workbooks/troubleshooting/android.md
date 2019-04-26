---
title: Solução de problemas de pastas de trabalho do Xamarin no Android
description: Este documento fornece dicas de solução de problemas para trabalhar com pastas de trabalho do Xamarin no Android. Ele aborda o suporte do emulador, pastas de trabalho que não é possível carregar e outros tópicos.
ms.prod: xamarin
ms.assetid: F1BD293B-4EB7-4C18-A699-718AB2844DFB
author: lobrien
ms.author: laobri
ms.date: 03/30/2017
ms.openlocfilehash: a93288829ff99027a4b33e7720a7f849df37e9b1
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61423776"
---
# <a name="troubleshooting-xamarin-workbooks-on-android"></a>Solução de problemas de pastas de trabalho do Xamarin no Android

## <a name="emulator-support"></a>Suporte do emulador

Para executar uma pasta de trabalho do Android, emulador do Android deve estar disponível para uso. Não há suporte para dispositivos Android físicos.

Recomendamos o emulador do Google com HAXM se o computador oferecer suporte a ele.
Se você deve ter o Hyper-V habilitado em seu sistema, ir com o emulador Android do Visual Studio.

Você deve ter um emulador que executa o Android 5.0 ou posterior. Não há suporte para emuladores ARM. Use `x86` ou `x86_64` somente para dispositivos.

Leia [nossa documentação sobre como configurar os emuladores de Android] [ android-emu] se você não estiver familiarizado com o processo.

> [!NOTE]
> Pastas de trabalho, 1.1 e anteriores serão tente (e falhar!) para usar emuladores ARM se elas estiverem disponíveis. Para contornar isso, emulador de inicialização x86 de sua escolha, antes de abrir ou criar uma pasta de trabalho do Android. Pastas de trabalho sempre preferirão para se conectar ao emulador em execução, desde que ele é compatível.

## <a name="workbooks-wont-load"></a>Pastas de trabalho não pôde ser carregada

### <a name="workbook-window-spins-forever-never-loads-windows"></a>Janela de pasta de trabalho para sempre, nunca gira cargas (Windows)

Primeiro, verifique que o emulador tem acesso à rede de trabalho totalmente pelo teste de qualquer site no navegador de web do emulador.

### <a name="visual-studio-android-emulator-cannot-connect-to-the-internet"></a>Emulador Android do Visual Studio não pode se conectar à internet

Se o emulador não tem acesso à rede, você precisa seguir estas etapas para corrigir o comutador de rede do Hyper-V. Se você alternar entre as redes de Wi-Fi com frequência você precisa repetir isso periodicamente:

0. **Certifique-se de que todas as operações críticas de rede forem concluídas, pois isso pode desconectar-se temporariamente Windows da internet.**
1. Feche os emuladores.
2. Abra `Hyper-V Manager`.
3. Sob `Actions`, abra `Virtual Switch Manager...`.
4. Exclua todos os comutadores virtuais.
5. Clique em `OK`.
6. Inicie o emulador Android do VS. Você provavelmente precisará recriar o comutador de rede virtual.
7. Teste de que o navegador do emulador do VS Android pode acessar a internet.

[android-emu]: https://developer.xamarin.com/guides/android/deployment,_testing,_and_metrics/debug-on-emulator/


## <a name="related-links"></a>Links relacionados

- [Relatório de Bugs](~/tools/workbooks/install.md#reporting-bugs)
