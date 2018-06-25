---
title: Executando o Google Android Emulator
description: Como depurar seu aplicativo com o Google Android Emulator
ms.prod: xamarin
ms.assetid: AEA165A4-D81A-411B-91DF-2DED2EED27B5
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 05/30/2018
ms.openlocfilehash: 0f4a8dc2ad581dac71f76d8dd1de09534b63abaa
ms.sourcegitcommit: a7febc19102209b21e0696256c324f366faa444e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/04/2018
ms.locfileid: "34732301"
---
# <a name="running-the-google-android-emulator"></a>Executando o Google Android Emulator

_Este guia mostra como iniciar um dispositivo virtual no Google Android Emulator para depurar e testar seu aplicativo._

## <a name="using-a-pre-configured-virtual-device"></a>Usando um dispositivo virtual pré-configurado

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

O Visual Studio inclui dispositivos virtuais pré-configurados que aparecem no menu suspenso do dispositivo. Por exemplo, na seguinte captura de tela do Visual Studio 2017, vários dispositivos virtuais pré-configurados estão disponíveis:

-   **VisualStudio\_android-23\_arm\_phone**

-   **VisualStudio\_android-23\_arm\_tablet**

-   **VisualStudio\_android-23\_x86\_phone** 

-   **VisualStudio\_android-23\_x86\_tablet** 

[![Dispositivos virtuais](running-the-emulator-images/win/01-virtual-devices-sml.png)](running-the-emulator-images/win/01-virtual-devices.png#lightbox)

Normalmente, você selecionaria o dispositivo virtual **VisualStudio\_android-23\_x86\_phone** para testar e depurar um aplicativo de telefone. Se um desses dispositivos virtuais pré-configurados atender às suas necessidades (ou seja, corresponder ao nível de API de destino de seu aplicativo), passe para [Iniciando o emulador](#launching) para começar a executar o aplicativo no emulador. (Se ainda não estiver familiarizado com os níveis de API do Android, consulte [Understanding Android API Levels](~/android/app-fundamentals/android-api-levels.md) [Noções básicas dos níveis de API do Android]).

Se o projeto Xamarin.Android estiver usando um nível de Estrutura de destino incompatível com os dispositivos virtuais disponíveis, o menu suspenso listará os dispositivos virtuais não utilizáveis em **Dispositivos Sem Suporte**. Por exemplo, o projeto a seguir tem a Estrutura de destino definida como **Android 7.1 Nougat (API 25)**, que é incompatível com os dispositivos virtuais **Android 6.0** listados neste exemplo:

[![Dispositivo virtual incompatível](running-the-emulator-images/win/02-incompatible-level-sml.png)](running-the-emulator-images/win/02-incompatible-level.png#lightbox)

Você pode clicar em **Alterar Destino Android Mínimo** para alterar a Versão mínima do Android do projeto de forma que ela corresponda ao nível da API dos dispositivos virtuais disponíveis. Você também pode usar o [Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md) para criar novos dispositivos virtuais que dão suporte ao nível da API de destino.
Antes de configurar dispositivos virtuais para um novo nível da API, você precisa instalar as imagens do sistema correspondente para esse nível da API (consulte [Configurando o SDK do Android para o Xamarin.Android](~/android/get-started/installation/android-sdk.md)).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

O Visual Studio para Mac inclui dispositivos virtuais pré-configurados que aparecem no menu suspenso do dispositivo. Por exemplo, na seguinte captura de tela, dois dispositivos virtuais pré-configurados estão disponíveis:

-   **Android\_Accelerated\_x86**

-   **Android\_ARMv7a**

[![Dispositivos virtuais](running-the-emulator-images/mac/01-virtual-devices-sml.png)](running-the-emulator-images/mac/01-virtual-devices.png#lightbox)

Normalmente, você selecionaria o dispositivo virtual **Android\_Accelerated\_x86** para testar e depurar um aplicativo de telefone. Se esse dispositivo virtual pré-configurado atender às suas necessidades (ou seja, corresponder ao nível de API de destino de seu aplicativo), passe para [Iniciando o emulador](#launching) para começar a executar o aplicativo no emulador. (Se ainda não estiver familiarizado com os níveis de API do Android, consulte [Understanding Android API Levels](~/android/app-fundamentals/android-api-levels.md) [Noções básicas dos níveis de API do Android]).

-----

## <a name="editing-virtual-devices"></a>Edição de dispositivos virtuais

Para modificar os dispositivos virtuais (ou criar novos), você deve usar o [Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md).


<a name="launching" />

## <a name="launching-the-emulator"></a>Iniciando o Emulador

Perto da parte superior do Visual Studio, há um menu suspenso que pode ser usado para selecionar o modo de **Depuração** ou **Versão**. Escolher a opção **Depuração** faz com que o depurador seja anexado ao processo do aplicativo em execução no emulador depois que o aplicativo é iniciado. Escolher o modo **Versão** desabilita o depurador (no entanto, você ainda poderá executar o aplicativo e usar as instruções de log para depuração). Depois de escolher um dispositivo virtual no menu suspenso do dispositivo, selecione o modo de **Depuração** ou **Versão** e, em seguida, clique no botão Executar para executar o aplicativo:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Modos de Depuração e Versão, botão Executar](running-the-emulator-images/win/17-debug-release-sml.png)](running-the-emulator-images/win/17-debug-release.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Modos de Depuração e Versão, botão Executar](running-the-emulator-images/mac/16-debug-release-sml.png)](running-the-emulator-images/mac/16-debug-release.png#lightbox)

-----

Após o emulador ser iniciado, o Xamarin.Android implantará o aplicativo no emulador. O emulador executa o aplicativo com a imagem de dispositivo virtual configurada. Veja uma captura de tela de exemplo do Google Android Emulator abaixo. Neste exemplo, o emulador está executando um aplicativo em branco chamado **MyApp**:

![Emulador executando um aplicativo em branco](running-the-emulator-images/emulator-running.png)

O emulador pode ser deixado em execução: não é necessário desligá-lo e aguardar seu reinício toda vez que o aplicativo é iniciado. A primeira vez que um aplicativo Xamarin.Android for executado no emulador, o tempo de execução compartilhado do Xamarin.Android para o nível da API de destino será instalado, seguido pelo aplicativo. A instalação do tempo de execução pode levar alguns minutos, portanto, tenha paciência. A instalação do tempo de execução ocorre somente quando o primeiro aplicativo Xamarin.Android é implantado no emulador &ndash; as implantações subsequentes são mais rápidas porque somente o aplicativo é copiado para o emulador.

## <a name="quick-boot"></a>Inicialização Rápida

Versões mais recentes do Google Android Emulator incluem um recurso chamado _Inicialização Rápida_ que o inicia o emulador em apenas alguns segundos. Quando você fecha o emulador, ele tira um instantâneo do estado do dispositivo virtual para que ele possa ser restaurado rapidamente a mesmo estado quando ele for reiniciado.
Para acessar esse recurso, você precisará do seguinte:

-   Android Emulator versão 27.0.2 ou posterior
-   Android SDK Tools versão 26.1.1 ou posterior

Quando as versões listadas acima do emulador e das SDK Tools estão instaladas, o recurso de Inicialização Rápida é habilitado por padrão. 

A primeira inicialização a frio do dispositivo virtual ocorre sem a melhoria da velocidade porque o instantâneo ainda não foi criado:

![Captura de tela da inicialização a frio](running-the-emulator-images/cold-boot.png)

Quando você sai do emulador, a Inicialização Rápida salva o estado do emulador em um instantâneo:

![Salvando o estado no desligamento](running-the-emulator-images/saving-state.png)

O dispositivo virtual subsequente é iniciado muito mais rapidamente porque o emulador simplesmente restaura o estado em que você fechou o emulador.

![Carregamento do estado na reinicialização](running-the-emulator-images/loading-state.png)

Para obter mais informações sobre como usar o Google Android Emulator, confira os seguintes tópicos do Android Developer:

-   [Navegando na tela](https://developer.android.com/studio/run/emulator.html#navigate)

-   [Executando tarefas básicas no emulador](https://developer.android.com/studio/run/emulator.html#tasks)

-   [Trabalhando com os Controles estendidos, as Configurações e a Ajuda](https://developer.android.com/studio/run/emulator.html#extended)

-   [Executar o emulador com Inicialização Rápida](https://developer.android.com/studio/run/emulator#quickboot)

