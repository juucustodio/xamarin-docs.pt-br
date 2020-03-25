---
title: Aplicar alterações
description: Como começar a usar aplicar alterações em projetos do Xamarin. Android.
ms.assetid: 38950B0C-8880-448F-B12E-08D5BFE87D0D
author: JonDouglas
ms.author: jodou
ms.date: 03/09/2020
ms.openlocfilehash: 03bace97908a53ac6112cd2600b20d429fe2f521
ms.sourcegitcommit: ec112800a76089ab1db66fe24b8bbcc510e067b4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "80215344"
---
# <a name="apply-changes"></a>Aplicar alterações

Aplicar alterações permite que você envie por push alterações de recursos para seu aplicativo em execução sem reiniciar seu aplicativo. Isso ajuda a controlar a quantidade de seu aplicativo reiniciada quando você deseja implantar e testar pequenas alterações incrementais enquanto preserva o estado atual do seu dispositivo ou emulador.

Aplicar alterações usa recursos na [implementação do Android JVMTI](https://docs.oracle.com/javase/8/docs/platform/jvmti/jvmti.html#bci) que tem suporte em dispositivos ou emuladores que executam o Android 8,0 (API nível 26) ou superior.

## <a name="requirements"></a>Requisitos

A lista a seguir mostra os requisitos para usar aplicar alterações:

- **Visual Studio** -no Windows, atualize para o visual Studio 2019 versão 16,5 ou posterior. No macOS, atualize para o Visual Studio 2019 para Mac versão 8,5 ou posterior.
- **Xamarin. Android** -Xamarin. Android 10,2 ou posterior deve ser instalado com o Visual Studio (o Xamarin. Android é instalado automaticamente como parte do **desenvolvimento móvel com** carga de trabalho do .net no Windows e instalado como parte do **instalador do Visual Studio para Mac**).
- **SDK do Android** -a API 28 ou superior do Android deve ser instalada por meio do gerenciador de SDK do Android.
- **Dispositivo de destino ou emulador** -seu dispositivo ou emulador deve executar o Android 8,0 (API nível 26) ou superior.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

## <a name="get-started"></a>Introdução

Para começar a aplicar as alterações, você precisará garantir que um dispositivo ou emulador esteja executando o Android 8,0 (API nível 26) ou superior. Em seguida, execute seu aplicativo Android com ou sem depuração.

Em seguida, você pode interagir com aplicar alterações com as seguintes abordagens:

1. **Ícone da barra de ferramentas.** Você pode clicar no ícone da barra de ferramentas aplicar alterações para aplicar as alterações ao seu dispositivo de destino ou emulador.

    [![aplicar alterações – ícone da barra de ferramentas](apply-changes-images/Apply-Changes-Toolbar.png)](apply-changes-images/Apply-Changes-Toolbar.png#lightbox)

2. **Atalho de teclado.** Você pode usar o atalho de teclado **Shift + Alt + F5** para aplicar as alterações ao seu dispositivo de destino ou emulador.
3. **Menu Depurar.** Você pode usar o item de menu **Debug > Apply Changes** para aplicar alterações ao seu dispositivo de destino ou emulador.

    [![aplicar alterações – menu Depurar](apply-changes-images/Apply-Changes-Debug-Menu.png)](apply-changes-images/Apply-Changes-Debug-Menu.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

## <a name="get-started"></a>Introdução

Para começar a aplicar as alterações, você precisará garantir que um dispositivo ou emulador esteja executando o Android 8,0 (API nível 26) ou superior. Em seguida, execute seu aplicativo Android com ou sem depuração.

Em seguida, você pode interagir com aplicar alterações com as seguintes abordagens:

1. **Atalho de teclado.** Você pode usar o atalho de teclado **⌥ + ⇧ + R** para aplicar alterações ao seu dispositivo de destino ou emulador.
2. **Menu executar.** Você pode usar o item de menu **executar > aplicar alterações** para aplicar as alterações ao seu dispositivo de destino ou emulador.

    [![aplicar alterações – menu Depurar](apply-changes-images/Apply-Changes-Debug-Menu-Mac.png)](apply-changes-images/Apply-Changes-Debug-Menu-Mac.png#lightbox)

-----

## <a name="limitations"></a>Limitações

As seguintes alterações exigem uma reinicialização do aplicativo:

- Alterando C# o código.
- Adicionando ou removendo um recurso.
- Alterando o AndroidManifest. xml.
- Alterando bibliotecas nativas (arquivos. so).

## <a name="related-links"></a>Links relacionados

- [Aplicar alterações](https://developer.android.com/studio/run#apply-changes)
