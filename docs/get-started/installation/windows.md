---
title: Instalando o Xamarin no Visual Studio 2017
description: Este documento descreve como instalar o Xamarin no Visual Studio 2017. Ele aborda os requisitos, o processo de instalação e a verificação da instalação.
ms.prod: xamarin
ms.assetid: E20D4463-368E-4B60-A059-F50DB8C5552D
author: conceptdev
ms.author: crdun
ms.date: 08/28/2018
ms.openlocfilehash: d9f4717a4dd76e2fb3767c7baf9aaa868cfc21ff
ms.sourcegitcommit: d62732ce6f3f9d8dc929d72d4acac3e592cba073
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/01/2019
ms.locfileid: "57199812"
---
# <a name="installing-xamarin-in-visual-studio-2017"></a>Instalando o Xamarin no Visual Studio 2017

<a name="requirements" />

Para começar, verifique os [requisitos do sistema](~/cross-platform/get-started/requirements.md).

## <a name="installation"></a>Instalação

[!include[](~/cross-platform/includes/install-xamarin-windows.md)]

Para conferir se instalou o Xamarin no Visual Studio 2017, clique no menu **Ajuda**. Se o Xamarin estiver instalado, você verá o item de menu **Xamarin**, conforme mostrado nesta captura de tela:

![Item de menu Xamarin no menu de Ajuda](windows-images/12-xamarin-menu-item.png "Item de menu Xamarin no menu de Ajuda")

Como alternativa, clique em **Ajuda > Sobre o Microsoft Visual Studio** e role a tela até a lista de produtos instalados para ver se encontra o Xamarin:

![Tela de produtos instalados do Visual Studio 2015](windows-images/13-xamarin-is-installed.png "Tela de produtos instalados do Visual Studio 2015")

Para saber mais sobre como localizar informações de versão, consulte [Where can I find my version information and logs?](~/cross-platform/troubleshooting/questions/version-logs.md) (Onde posso encontrar minhas informações de versão e logs?)

## <a name="next-steps"></a>Próximas etapas

A instalação do Xamarin no Visual Studio 2017 permite que você comece a escrever código para seus aplicativos, mas é preciso uma configuração adicional para criar e implantar os aplicativos no simulador, no emulador e no dispositivo. Acesse os guias a seguir para concluir a instalação e iniciar a criação de aplicativos multiplataforma.

### <a name="ios"></a>iOS

Para obter informações mais detalhadas, consulte o guia [Como instalar o Xamarin.iOS no Windows](~/ios/get-started/installation/windows/index.md). 

1. [Instalar o Visual Studio para Mac](https://docs.microsoft.com/visualstudio/mac/installation)
2. [Conectar o Visual Studio ao host de build do Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md)
3. [Configuração do desenvolvedor iOS](~/ios/get-started/installation/device-provisioning/index.md) – necessária para executar o aplicativo no dispositivo
5. [Simulador de iOS remoto](~/tools/ios-simulator/index.md)
6. [Introdução ao Xamarin.iOS para Visual Studio](~/ios/get-started/installation/windows/introduction-to-xamarin-ios-for-visual-studio.md)

### <a name="android"></a>Android

Para obter informações mais detalhadas, consulte o guia [Instalando o Xamarin.Android no Windows](~/android/get-started/installation/windows.md).

1. [Configuração do Xamarin.Android](~/android/get-started/installation/windows.md#configuration)
2. [Usando o Gerenciador de SDK do Xamarin Android](~/android/get-started/installation/android-sdk.md?ide=vs)
3. [Emulador do SDK do Android](~/android/get-started/installation/android-emulator/index.md)
4. [Configurar o dispositivo para desenvolvimento](~/android/get-started/installation/set-up-device-for-development.md)
