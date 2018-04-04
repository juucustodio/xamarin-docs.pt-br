---
title: Solucionando problemas de pastas de trabalho do Xamarin Android
ms.prod: xamarin
ms.assetid: F1BD293B-4EB7-4C18-A699-718AB2844DFB
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.openlocfilehash: 03c775f6f14263878f9f4a4633ba72931b233b17
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="troubleshooting-xamarin-workbooks-on-android"></a>Solucionando problemas de pastas de trabalho do Xamarin Android

## <a name="emulator-support"></a>Suporte do emulador

Para executar uma pasta de trabalho do Android, um emulador Android deve estar disponível para uso. Não há suporte para dispositivos Android físicos.

É recomendável o emulador do Google junto com HAXM se o computador oferecer suporte a ele.
Se você deve ter o Hyper-V habilitada no seu sistema, ir com o emulador Android do Visual Studio.

Você deve ter um emulador que executa o Android 5.0 ou posterior. Não há suporte para os emuladores ARM. Use `x86` ou `x86_64` somente para dispositivos.

Leia [nossa documentação sobre como configurar os emuladores Android] [ android-emu] se você não estiver familiarizado com o processo.

> [!NOTE]
> Pastas de trabalho 1.1 e anteriores serão tente (e falha!) para uso ARM emuladores se elas estiverem disponíveis. Para contornar isso, emulador de inicialização x86 de sua escolha antes de abrir ou criar uma pasta de trabalho do Android. Pastas de trabalho sempre preferirá para se conectar a um emulador em execução, desde que ele é compatível.

## <a name="workbooks-wont-load"></a>Não é possível carregar pastas de trabalho

### <a name="workbook-window-spins-forever-never-loads-windows"></a>Janela de pasta de trabalho para sempre, nunca gira cargas (Windows)

Primeiro, verifique se o emulador tem acesso à rede de trabalho totalmente testando qualquer site da Web no navegador do emulador do Windows.

### <a name="visual-studio-android-emulator-cannot-connect-to-the-internet"></a>Emulador Android do Visual Studio não pode se conectar à internet

Se o emulador não tem acesso à rede, você precisará seguir estas etapas para corrigir o comutador de rede do Hyper-V. Se você alternar entre as redes Wi-Fi com frequência, talvez seja necessário repetir essa periodicamente:

0. **Verifique se que todas as operações críticas de rede estiverem concluídas, pois isso pode desconectar-se temporariamente Windows da internet.**
1. Feche os emuladores.
2. Abra `Hyper-V Manager`.
3. Em `Actions`, abra `Virtual Switch Manager...`.
4. Exclua todos os comutadores virtuais.
5. Clique em `OK`.
6. Inicie o emulador Android VS. Você provavelmente precisará recriar o comutador de rede virtual.
7. Teste de que o navegador do emulador do VS Android pode acessar a internet.

[android-emu]: https://developer.xamarin.com/guides/android/deployment,_testing,_and_metrics/debug-on-emulator/


## <a name="related-links"></a>Links relacionados

- [Relatório de Bugs](~/tools/workbooks/install.md#reporting-bugs)
