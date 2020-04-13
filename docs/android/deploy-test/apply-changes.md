---
title: Aplicar alterações
description: Como começar a usar aplicar alterações em projetos Xamarin.Android.
ms.assetid: 38950B0C-8880-448F-B12E-08D5BFE87D0D
author: JonDouglas
ms.author: jodou
ms.date: 03/09/2020
ms.openlocfilehash: 03bace97908a53ac6112cd2600b20d429fe2f521
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "80215344"
---
# <a name="apply-changes"></a>Aplicar alterações

Aplicar alterações permite que você empurre alterações de recursos para o aplicativo em execução sem reiniciar o aplicativo. Isso ajuda você a controlar o quanto do seu aplicativo é reiniciado quando você deseja implantar e testar pequenas alterações incrementais, preservando o estado atual do seu dispositivo ou emulador.

Aplicar alterações usa recursos na implementação do [Android JVMTI,](https://docs.oracle.com/javase/8/docs/platform/jvmti/jvmti.html#bci) que é suportado em dispositivos ou emuladores que executam o Android 8.0 (Nível 26 da API) ou superior.

## <a name="requirements"></a>Requisitos

A lista a seguir mostra os requisitos para usar Alterações aplicadas:

- **Visual Studio** - No Windows, atualize para visual studio versão 16.5 ou posterior. No macOS, atualize para o Visual Studio 2019 para a versão 8.5 do Mac ou posterior.
- **Xamarin.Android** - Xamarin.Android 10.2 ou posterior deve ser instalado com o Visual Studio (Xamarin.Android é automaticamente instalado como parte do desenvolvimento móvel com carga de trabalho **.NET** no Windows e instalado como parte do **Visual Studio para Mac Installer**).
- **Android SDK** - Android API 28 ou superior deve ser instalado através do Android SDK Manager.
- **Dispositivo de destino ou emulador** - Seu dispositivo ou emulador deve executar o Android 8.0 (Nível API 26) ou superior.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

## <a name="get-started"></a>Introdução

Para começar com Aplicar Alterações, você precisará garantir que um dispositivo ou emulador esteja executando o Android 8.0 (Nível 26 da API) ou superior. Em seguida, execute seu aplicativo Android com ou sem depuração.

Em seguida, você pode interagir com aplicar alterações com as seguintes abordagens:

1. **Ícone da barra de ferramentas.** Você pode clicar no ícone Aplicar alterações na barra de ferramentas para aplicar alterações no dispositivo de destino ou emulador.

    [![Aplicar alterações - Ícone da barra de ferramentas](apply-changes-images/Apply-Changes-Toolbar.png)](apply-changes-images/Apply-Changes-Toolbar.png#lightbox)

2. **Atalho de teclado.** Você pode usar o atalho de teclado **Shift + Alt + F5** para aplicar alterações no seu dispositivo de destino ou emulador.
3. **Menu de depuração.** Você pode usar o **item de menu Debug > Apply Changes** para aplicar alterações no dispositivo de destino ou emulador.

    [![Aplicar alterações - Menu de depuração](apply-changes-images/Apply-Changes-Debug-Menu.png)](apply-changes-images/Apply-Changes-Debug-Menu.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

## <a name="get-started"></a>Introdução

Para começar com Aplicar Alterações, você precisará garantir que um dispositivo ou emulador esteja executando o Android 8.0 (Nível 26 da API) ou superior. Em seguida, execute seu aplicativo Android com ou sem depuração.

Em seguida, você pode interagir com aplicar alterações com as seguintes abordagens:

1. **Atalho de teclado.** Você pode usar o atalho do teclado **R** para aplicar alterações no dispositivo de destino ou emulador.
2. **Executar menu.** Você pode usar o item de menu **Executar > Aplicar Alterações** para aplicar alterações no dispositivo de destino ou emulador.

    [![Aplicar alterações - Menu de depuração](apply-changes-images/Apply-Changes-Debug-Menu-Mac.png)](apply-changes-images/Apply-Changes-Debug-Menu-Mac.png#lightbox)

-----

## <a name="limitations"></a>Limitações

As seguintes alterações requerem uma reinicialização do aplicativo:

- Mudando o código C#.
- Adicionando ou removendo um recurso.
- Alterando o AndroidManifest.xml.
- Alterando bibliotecas nativas (.assim arquivos).

## <a name="related-links"></a>Links relacionados

- [Aplicar alterações](https://developer.android.com/studio/run#apply-changes)
