---
title: Solução de problemas Xamarin Workbooks no Android
description: Este documento fornece dicas de solução de problemas para trabalhar com o Xamarin Workbooks no Android. Ele aborda o suporte do emulador, pastas de trabalho que não serão carregadas e outros tópicos.
ms.prod: xamarin
ms.assetid: F1BD293B-4EB7-4C18-A699-718AB2844DFB
author: lobrien
ms.author: laobri
ms.date: 03/30/2017
ms.openlocfilehash: 0d04b42a8d9f230c48bb09059296eb3740336dc6
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68511843"
---
# <a name="troubleshooting-xamarin-workbooks-on-android"></a>Solução de problemas Xamarin Workbooks no Android

## <a name="emulator-support"></a>Suporte a emulador

Para executar uma pasta de trabalho do Android, um emulador do Android deve estar disponível para uso. Não há suporte para dispositivos Android físicos.

Recomendamos o emulador do Google junto com HAXM se o seu computador oferecer suporte a ele.
Se você precisar ter o Hyper-V habilitado no seu sistema, vá com o Android Emulator do Visual Studio em vez disso.

Você deve ter um emulador que executa o Android 5,0 ou posterior. Não há suporte para emuladores ARM. Use `x86` dispositivos `x86_64` ou somente.

Leia [nossa documentação sobre como configurar os emuladores do Android][android-emu] se você não estiver familiarizado com o processo.

> [!NOTE]
> As pastas de trabalho 1,1 e anteriores tentarão (e falharão!) para usar os emuladores do ARM, se estiverem disponíveis. Para contornar isso, inicie o emulador x86 de sua escolha antes de abrir ou criar uma pasta de trabalho do Android. As pastas de trabalho sempre preferem se conectar a um emulador em execução, desde que seja compatível.

## <a name="workbooks-wont-load"></a>Pastas de trabalho não serão carregadas

### <a name="workbook-window-spins-forever-never-loads-windows"></a>Rotação da janela da pasta de trabalho para sempre, nunca carrega (Windows)

Primeiro, verifique se o emulador tem acesso à rede totalmente funcional testando qualquer site no navegador da Web do emulador.

### <a name="visual-studio-android-emulator-cannot-connect-to-the-internet"></a>O Android Emulator do Visual Studio não pode se conectar à Internet

Se o emulador não tiver acesso à rede, talvez seja necessário seguir estas etapas para corrigir o comutador de rede do Hyper-V. Se você alternar entre redes Wi-Fi com frequência, talvez seja necessário repetir isso periodicamente:

1. **Verifique se todas as operações de rede críticas foram concluídas, pois isso pode desconectar temporariamente o Windows da Internet.**
1. Feche os emuladores.
1. Abra `Hyper-V Manager`.
1. Em `Actions`, abra `Virtual Switch Manager...`.
1. Exclua todos os comutadores virtuais.
1. Clique em `OK`.
1. Inicie o VS Android Emulator. Você provavelmente será solicitado a recriar o comutador de rede virtual.
1. Teste se o navegador do VS Android Emulator pode acessar a Internet.

[android-emu]: ~/android/deploy-test/debugging/debug-on-emulator.md

## <a name="related-links"></a>Links relacionados

- [Relatando bugs](~/tools/workbooks/install.md#reporting-bugs)
