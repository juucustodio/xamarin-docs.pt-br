---
title: Criação de perfil de aplicativos Android
description: Este guia explica como usar ferramentas de criação de perfil para examinar o desempenho e o uso de memória de um aplicativo Android.
ms.topic: article
ms.prod: xamarin
ms.assetid: 8C823FEE-A6F6-4C31-9EB6-E51407A2FD8E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/03/2018
ms.openlocfilehash: 400075a1cbd2303f2ecddb9b1cc9465bbcbde32d
ms.sourcegitcommit: f255aa286bd52e8a80ffa620c2e93c97f069f8ec
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68680258"
---
# <a name="profiling-android-apps"></a>Criação de perfil de aplicativos Android

Antes de implantar seu aplicativo em uma loja de aplicativos, é importante identificar e corrigir gargalos de desempenho, problemas de uso excessivo de memória ou uso ineficaz de recursos de rede. Duas ferramentas de criação de perfil estão disponíveis para atender a esse propósito:

-  Criador de perfil do Xamarin 
-  Android Profiler no Android Studio

Este guia apresenta o Xamarin Profiler e fornece informações detalhadas para começar a usar o Android Profiler.

 
## <a name="xamarin-profiler"></a>Criador de perfil do Xamarin

O Xamarin Profiler é um aplicativo autônomo integrado com o Visual Studio e o Visual Studio para Mac para criação de perfil de aplicativos Xamarin no IDE. Para obter mais informações sobre o uso do Xamarin Profiler, veja [Xamarin Profiler](~/tools/profiler/index.md).

> [!NOTE]
> Você deve ser um assinante do [Visual Studio Enterprise](https://visualstudio.microsoft.com/vs/compare/) para desbloquear o recurso do Xamarin Profiler no Visual Studio Enterprise no Windows ou no Visual Studio para Mac.
 
## <a name="android-studio-profiler"></a>Criador de perfil do Android Studio

O Android Studio 3.0 e posterior inclui uma ferramenta Android Profiler. Você pode usar o Android Profiler para medir o desempenho de um aplicativo Xamarin Android criado com o Visual Studio &ndash; sem a necessidade de uma licença do Visual Studio Enterprise. No entanto, ao contrário do Xamarin Profiler, o Android Profiler não é integrado com o Visual Studio e só pode ser usado para criar o perfil de um APK (pacote de aplicativo Android) que foi previamente criado e importado para o Android Profiler.

### <a name="launching-a-xamarin-android-app-in-android-profiler"></a>Iniciar um aplicativo Xamarin Android no Android Profiler

As etapas a seguir explicam como iniciar um aplicativo Xamarin Android na ferramenta Android Profiler do Android Studio. Nas capturas de tela de exemplo abaixo, o aplicativo [XamagonXuzzle](https://docs.microsoft.com/samples/xamarin/mobile-samples/liveplayer-xamagonxuzzlelp/) do Xamarin Forms é criado e analisado usando o Android Profiler:

1.  Nas opções de build do projeto do Android, desabilite **Usar Tempo de Execução Compartilhado**. Isso garante que o APK (pacote de aplicativo Android) seja compilado sem dependência do tempo de desenvolvimento compartilhado do tempo de execução Mono.

    ![Desabilitando Usar Tempo de Execução Compartilhado](profiling-images/vswin/01-turn-off-shared-runtime.png)

2.  Compile o aplicativo para **Depuração** e implante-o em um dispositivo físico ou um emulador. Isso faz com que uma versão de **Depuração** assinada do APK seja compilada.
    Quanto ao exemplo do **XamagonXuzzle**, o APK resultante é denominado **com.companyname.XamagonXuzzle-Signed.apk**.

3.  Abra a pasta do projeto e navegue até **bin/Debug**. Nessa pasta, localize a versão **Signed.apk** do aplicativo e copie-a em um local de fácil acesso (como a área de trabalho). Na captura de tela abaixo, o APK **com.companyname.XamagonXuzzle-Signed.apk** é localizado e copiado na área de trabalho:

    [![Local do arquivo APK assinado para depuração](profiling-images/vswin/02-locating-the-debug-apk-sml.png)](profiling-images/vswin/02-locating-the-debug-apk.png#lightbox)

4.  Inicie o Android Studio e selecione **Criar perfil ou depurar APK**:

    ![Iniciando o criador de perfil na tela de inicialização do Android Studio](profiling-images/vswin/03-android-studio.png)

5.  Na caixa de diálogo **Selecionar arquivo APK**, navegue até o APK que você compilou e copiou anteriormente. Selecione o APK e clique em **OK**: 
    
    ![Selecionando o APK na caixa de diálogo Selecionar arquivo APK](profiling-images/vswin/04-select-apk-dialog.png)

6.  O Android Studio carregará o APK e desmontará o **classes.dex**:

    ![Configurando o APK](profiling-images/vswin/05-setting-up-the-apk.png)

7.  Depois que o APK é carregado, o Android Studio exibe a seguinte tela de projeto para o APK. Clique com o botão direito do mouse no nome do aplicativo no modo de exibição de árvore à esquerda e selecione **Abrir Configurações de Módulo**:

    [![Local do item de menu Abrir Configurações de Módulo](profiling-images/vswin/06-open-module-settings-sml.png)](profiling-images/vswin/06-open-module-settings.png#lightbox)

8.  Navegue até **Configurações do Projeto > Módulos**, selecione o nó **-Assinado** do aplicativo e, em seguida, clique em **&lt;Sem SDK&gt;** :

    [![Navegando até a configuração de SDK](profiling-images/vswin/07-project-settings-modules-sml.png)](profiling-images/vswin/07-project-settings-modules.png#lightbox)

9.  No menu suspenso **Módulo SDK**, selecione o nível do SDK do Android que foi usado para compilar o aplicativo (neste exemplo, o nível da API 26 foi usado para compilar o **XamagonXuzzle**):

    [![Definindo o nível do SDK do projeto](profiling-images/vswin/08-project-sdk-level-sml.png)](profiling-images/vswin/08-project-sdk-level.png#lightbox)

    Clique em **Aplicar** e em **OK** para salvar essa configuração.

10. Inicie o criador de perfil, usando ícone da barra de ferramentas:

    [![Local do ícone da barra de ferramentas do criador de perfil](profiling-images/vswin/09-launch-profiler-sml.png)](profiling-images/vswin/09-launch-profiler.png#lightbox)

11. Selecione o destino de implantação para executar/criar perfil do aplicativo e clique em **OK**. O destino de implantação pode ser um dispositivo físico ou um dispositivo virtual em execução em um emulador. Neste exemplo, um dispositivo Nexus 5X é usado:

    ![Selecionando o destino de implantação](profiling-images/vswin/10-select-deployment-target.png)

12. Após iniciar o criador de perfil, levará alguns segundos para que ele se conecte ao dispositivo de implantação e ao processo do aplicativo. Enquanto ele estiver instalando o APK, o Android Profiler relatará **Nenhum dispositivo conectado** e **Nenhum processo depurável**.

    [![O criador de perfil instala o APK](profiling-images/vswin/11-no-connected-devices-sml.png)](profiling-images/vswin/11-no-connected-devices.png#lightbox)

13. Após alguns segundos, o Android Profiler concluirá a instalação do APK e iniciará o APK, relatando o nome do dispositivo e o nome do processo de aplicativo dos quais está sendo criado perfil (neste exemplo, **LGE Nexus 5X** e **com.companyname.XamagonXuzzle**, respectivamente):

    [![Janela do criador de perfil após o início](profiling-images/vswin/12-profiler-starts-sml.png)](profiling-images/vswin/12-profiler-starts.png#lightbox)

14. Depois que o dispositivo e o processo depurável são identificados, o Android Profiler inicia a criação de perfil do aplicativo:

    [![Exibições do criador de perfil para o aplicativo em execução](profiling-images/vswin/13-profiler-running-sml.png)](profiling-images/vswin/13-profiler-running.png#lightbox)

15. Se você tocar no botão **RANDOMIZE** no **XamagonXuzzle** (fazendo com que ele mude e embaralhe os blocos), você verá o uso da CPU aumentar durante o intervalo do embaralhamento do aplicativo:

    [![Uso da CPU quando o botão RANDOMIZE é tocado](profiling-images/vswin/14-tap-randomize-sml.png)](profiling-images/vswin/14-tap-randomize.png#lightbox)


### <a name="using-the-android-profiler"></a>Usando o Android Profiler

Há informações detalhadas de como usar o Android Profiler incluídas na [documentação do Android Studio](https://developer.android.com/studio/profile/android-profiler.html).
Os tópicos a seguir serão de interesse dos desenvolvedores do Xamarin Android:

-   [Criador de perfil de CPU](https://developer.android.com/studio/profile/cpu-profiler.html) &ndash; Explica como inspecionar a atividade de thread e o uso de CPU do aplicativo em tempo real.

-   [Criador de perfil de memória](https://developer.android.com/studio/profile/memory-profiler.html) &ndash; Exibe um grafo em tempo real do uso de memória do aplicativo e inclui um botão para gravar alocações de memória para análise.

-   [Criador de perfil de rede](https://developer.android.com/studio/profile/network-profiler.html) &ndash; Exibe a atividade de rede em tempo real dos dados enviados e recebidos pelo aplicativo.
