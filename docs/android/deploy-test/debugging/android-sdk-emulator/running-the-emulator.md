---
title: Executando o Google Android Emulator
description: Como depurar seu aplicativo com o Google Android Emulator
ms.prod: xamarin
ms.assetid: AEA165A4-D81A-411B-91DF-2DED2EED27B5
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 0e290b24c0d7a98b1abaf647fe76e56867042645
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2018
---
# <a name="running-the-google-android-emulator"></a>Executando o Google Android Emulator

Este guia mostra como iniciar um dispositivo virtual no Google Android Emulator para depuração e teste de aplicativo.

## <a name="using-a-pre-configured-virtual-device"></a>Usando um dispositivo virtual pré-configurado

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

O Visual Studio inclui dispositivos virtuais pré-configurados que aparecem no menu suspenso do dispositivo. Por exemplo, na seguinte captura de tela do Visual Studio 2017, vários dispositivos virtuais pré-configurados estão disponíveis:

-   **VisualStudio\_android-23\_arm\_phone**

-   **VisualStudio\_android-23\_arm\_tablet**

-   **VisualStudio\_android-23\_x86\_phone** 

-   **VisualStudio\_android-23\_x86\_tablet** 

[![Dispositivos virtuais](running-the-emulator-images/win/01-virtual-devices-sml.png)](running-the-emulator-images/win/01-virtual-devices.png#lightbox)

Normalmente, você selecionaria o dispositivo virtual **VisualStudio\_android-23\_x86\_phone** para testar e depurar um aplicativo de telefone. Se um desses dispositivos virtuais pré-configurados atender às suas necessidades (ou seja, corresponder ao nível de API de destino de seu aplicativo), passe para [Iniciando o emulador](#launching) para começar a executar o aplicativo no emulador. (Se ainda não estiver familiarizado com os níveis de API do Android, consulte [Understanding Android API Levels](~/android/app-fundamentals/android-api-levels.md) [Noções básicas dos níveis de API do Android]).

Se seu projeto Xamarin.Android estiver usando um nível de Estrutura de destino incompatível com os dispositivos virtuais disponíveis, o menu suspenso listará os dispositivos virtuais inutilizáveis em **Dispositivos sem Suporte**. Por exemplo, o projeto a seguir tem a Estrutura de destino definida como **Android 7.1 Nougat (API 25)**, que é incompatível com os dispositivos virtuais **Android 6.0** fornecidos por padrão:

[![Dispositivo virtual incompatível](running-the-emulator-images/win/02-incompatible-level-sml.png)](running-the-emulator-images/win/02-incompatible-level.png#lightbox)

Você pode clicar em **Alterar Destino Android Mínimo** para alterar a Versão mínima do Android do projeto de forma que ela corresponda ao nível da API dos dispositivos virtuais disponíveis. Você também pode usar o **Gerenciador do Emulador do Android** para criar novos dispositivos virtuais que dão suporte ao nível da API de destino, conforme explicado mais adiante em [Configurando dispositivos virtuais](#virtualdevice). Antes de configurar dispositivos virtuais para um novo nível de API, você precisa instalar as imagens do sistema correspondente para esse nível de API &ndash; isso é explicado na próxima seção.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

O Visual Studio para Mac inclui dispositivos virtuais pré-configurados que aparecem no menu suspenso do dispositivo. Por exemplo, na seguinte captura de tela, dois dispositivos virtuais pré-configurados estão disponíveis:

-   **Android\_Accelerated\_x86**

-   **Android\_ARMv7a**

[![Dispositivos virtuais](running-the-emulator-images/mac/01-virtual-devices-sml.png)](running-the-emulator-images/mac/01-virtual-devices.png#lightbox)

Normalmente, você selecionaria o dispositivo virtual **Android\_Accelerated\_x86** para testar e depurar um aplicativo de telefone. Se esse dispositivo virtual pré-configurado atender às suas necessidades (ou seja, corresponder ao nível de API de destino de seu aplicativo), passe para [Iniciando o emulador](#launching) para começar a executar o aplicativo no emulador. (Se ainda não estiver familiarizado com os níveis de API do Android, consulte [Understanding Android API Levels](~/android/app-fundamentals/android-api-levels.md) [Noções básicas dos níveis de API do Android]).

-----

## <a name="creating-custom-virtual-devices"></a>Criar Dispositivos Virtuais Personalizados

Para criar dispositivos virtuais personalizados, é necessário usar o Gerenciador de Dispositivos Android do Xamarin ou o Google Emulator Manager herdado que faz parte do SDK do Android. Para saber mais sobre a criação e personalização de dispositivos virtuais, consulte [Gerenciador de Dispositivos Android do Xamarin](~/android/get-started/installation/android-emulator/xamarin-device-manager.md).
Caso prefira usar o Google Emulator Manager herdado, consulte [Google Emulator Manager](~/android/get-started/installation/android-emulator/google-emulator-manager.md).

Se você estiver desenvolvendo para o Android 8.0 Oreo, será necessário usar o Gerenciador de Dispositivos Android do Xamarin.

<a name="launching" />

## <a name="launching-the-emulator"></a>Iniciando o Emulador

Próximo ao topo do IDE, há um menu suspenso que pode ser usado para selecionar o modo de **Depuração** ou **Versão**. Escolher a opção **Depuração** faz com que o depurador seja anexado ao processo do aplicativo em execução no emulador. 

Depois de escolher um dispositivo virtual no menu suspenso do dispositivo, selecione o modo de **Depuração** ou **Versão** e, em seguida, clique no botão **Executar** para executar o aplicativo:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Modos de Depuração e Versão, botão Executar](running-the-emulator-images/win/17-debug-release-sml.png)](running-the-emulator-images/win/17-debug-release.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Modos de Depuração e Versão, botão Executar](running-the-emulator-images/mac/16-debug-release-sml.png)](running-the-emulator-images/mac/16-debug-release.png#lightbox)

-----

Após o emulador do Android ser iniciado, o Xamarin.Android implantará o aplicativo no emulador. O emulador executa o aplicativo com a imagem de dispositivo virtual configurada. Uma captura de tela de exemplo do Google Android Emulator é exibida abaixo (o emulador está executando um aplicativo em branco chamado **MyApp**):

![Emulador executando um aplicativo em branco](running-the-emulator-images/emulator-running.png)

O emulador pode ser deixado em execução, não é necessário desligá-lo e reiniciá-lo toda vez que o aplicativo for executado. A primeira vez que um aplicativo Xamarin.Android for executado no emulador, o tempo de execução compartilhado do Xamarin.Android para o nível da API de destino será instalado, seguido pelo aplicativo. A instalação do tempo de execução pode levar alguns minutos, portanto, tenha paciência. A instalação do tempo de execução ocorre somente quando o primeiro aplicativo Xamarin.Android é implantado no emulador &ndash; as implantações subsequentes são mais rápidas porque somente o aplicativo é copiado para o emulador.

Para obter mais informações sobre como usar o Google Android Emulator, confira os seguintes tópicos do Android Developer:

-   [Navegando na tela](https://developer.android.com/studio/run/emulator.html#navigate)

-   [Executando tarefas básicas no emulador](https://developer.android.com/studio/run/emulator.html#tasks)

-   [Trabalhando com os Controles estendidos, as Configurações e a Ajuda](https://developer.android.com/studio/run/emulator.html#extended)

