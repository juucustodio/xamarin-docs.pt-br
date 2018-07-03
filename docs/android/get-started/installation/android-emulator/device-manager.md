---
title: Gerenciando dispositivos virtuais com o Android Device Manager
description: Este artigo explica como usar o Android Device Manager para criar e configurar AVDs (Dispositivos Virtuais Android) que emulam dispositivos físicos Android. É possível usar esses dispositivos virtuais para executar e testar aplicativos, sem a necessidade de um dispositivo físico.
ms.prod: xamarin
ms.assetid: ECB327F3-FF1C-45CC-9FA6-9C11032BD5EF
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/22/2018
ms.openlocfilehash: a7c1aeafd94d7e2639617cda13312ee8a09e2c94
ms.sourcegitcommit: 26033c087f49873243751deded8037d2da701655
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/25/2018
ms.locfileid: "36935322"
---
# <a name="managing-virtual-devices-with-the-android-device-manager"></a>Gerenciando dispositivos virtuais com o Android Device Manager

_Este artigo explica como usar o Android Device Manager para criar e configurar AVDs (Dispositivos Virtuais Android) que emulam dispositivos físicos Android. É possível usar esses dispositivos virtuais para executar e testar aplicativos, sem a necessidade de um dispositivo físico._

## <a name="overview"></a>Visão geral

Depois de ter verificado se a aceleração de hardware está habilitada (conforme descrito em [Aceleração de hardware para o desempenho do emulador](~/android/get-started/installation/android-emulator/hardware-acceleration.md)), a próxima etapa é usar o _Android Device Manager_ (também conhecido como o _Xamarin Android Device Manager_) para criar dispositivos virtuais que você pode usar para testar e depurar o aplicativo.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Este artigo explica como usar o Android Device Manager para criar, duplicar, personalizar e iniciar dispositivos virtuais Android.

[![Captura de tela que mostra o Android Device Manager na guia Dispositivos](device-manager-images/win/01-devices-dialog-sml.png)](device-manager-images/win/01-devices-dialog.png#lightbox)

O Android Device Manager é usado para criar e configurar _AVDs_ (Dispositivos Virtuais Android) que são executados no [Android Emulator](~/android/deploy-test/debugging/debug-on-emulator.md).
Cada AVD é uma configuração de emulador que simula um dispositivo Android físico. Assim, é possível executar e testar aplicativos em uma variedade de configurações que simulam diferentes dispositivos Android físicos.

## <a name="requirements"></a>Requisitos

Para usar o Android Device Manager, serão necessários:

-   Visual Studio 2017 versão 15.7 ou posterior. As edições Community, Professional e Enterprise do Visual Studio são compatíveis.

-   Ferramentas do Visual Studio para Xamarin versão 4.9 ou posterior.

-   O SDK do Android precisa ser instalado (veja [Configuração do SDK do Android para Xamarin.Android](~/android/get-started/installation/android-sdk.md)) e a versão 26.1.1 ou posterior das SDK Tools precisa ser instalada conforme descrito na seção a seguir. Lembre-se de instalar o SDK do Android neste local (se não estiver instalado): **C:\\Program Files (x86)\\Android\\android-sdk**.


## <a name="launching-the-device-manager"></a>Inicializar o Gerenciador de Dispositivos

Inicie o Android Device Manager no menu **Ferramentas** clicando em **Ferramentas > Android > Android Device Manager**:

[![Inicializando no menu Ferramentas](device-manager-images/win/04-tools-menu-sml.png)](device-manager-images/win/04-tools-menu.png#lightbox)

Se a seguinte caixa de diálogo de erro for exibida na inicialização, consulte [Solução de problemas do Android Emulator](~/android/get-started/installation/android-emulator/troubleshooting.md) para instruções de solução alternativa:

![Erro de instância do SDK do Android](device-manager-images/win/32-sdk-error.png)

Antes de usar o Android Device Manager, é necessário instalar a versão 26.1.1 ou posterior das Android SDK Tools. Se a versão 26.1.1 ou posterior das Android SDK Tools não estiverem instaladas, este erro será exibido na inicialização:

![Caixa de diálogo de erro de instância do SDK do Android](device-manager-images/win/02-sdk-instance-error.png)

Se essa caixa de diálogo de erro for exibida, clique em **Abrir Gerenciador de SDK** para abrir o Gerenciador do SDK do Android. No Gerenciador de SDK do Android, clique na guia **Ferramentas** e instale o seguinte:

-   **Android SDK Tools 26.1.1** ou posterior 
-   **Ferramentas da Plataforma SDK do Android 27.0.1** ou posterior  
-   **Ferramentas de Build do SDK do Android 27.0.3** ou posterior

Esses pacotes devem ser exibidos com status **Instalado**, como mostrado na captura de tela a seguir:

[![Instalação do Android SDK Tools 27.0](device-manager-images/win/03-sdk-tools-sml.png)](device-manager-images/win/03-sdk-tools.png#lightbox)

Depois de instalar esses pacotes, feche o Gerenciador de SDK e reinicie o Gerenciador de Dispositivos Android.

## <a name="main-screen"></a>Tela principal

Quando o Gerenciador de Dispositivos Android é iniciado pela primeira vez, uma tela exibe todos os dispositivos virtuais configurados no momento. O **Nome**, o **Sistema Operacional** (nível da API Android), a **CPU**, o tamanho da **Memória** e a resolução de tela de cada dispositivo são exibidos:

[![Lista de dispositivos instalados e seus parâmetros](device-manager-images/win/05-installed-list-sml.png)](device-manager-images/win/05-installed-list.png#lightbox)

Ao clicar em um dispositivo na lista, o botão **Iniciar** é exibido no lado direito. É possível clicar no botão **Iniciar** para inicializar o emulador com este dispositivo virtual:

[![Botão Iniciar de uma imagem de dispositivo](device-manager-images/win/06-start-button-sml.png)](device-manager-images/win/06-start-button.png#lightbox)

Depois da inicialização do emulador com o dispositivo virtual selecionado, o botão **Iniciar** se transforma em um botão **Parar**, que pode ser utilizado para interromper o emulador:

[![Botão Parar do dispositivo em execução](device-manager-images/win/07-stop-button-sml.png)](device-manager-images/win/07-stop-button.png#lightbox)

### <a name="new-device"></a>Novo dispositivo

Para criar um novo dispositivo, clique no botão **Novo** (na área superior direita da tela):

[![Botão Novo para criar um novo dispositivo](device-manager-images/win/08-new-button-sml.png)](device-manager-images/win/08-new-button.png#lightbox)

Ao clicar em **Novo**, a tela **Novo Dispositivo** é iniciada:

[![Tela Novo Dispositivo do Gerenciador de Dispositivos](device-manager-images/win/09-new-device-editor-sml.png)](device-manager-images/win/09-new-device-editor.png#lightbox)

Para configurar um novo dispositivo na tela **Novo Dispositivo**, siga estas etapas:

1. Selecione um dispositivo físico para emular clicando no menu suspenso **Dispositivo**:

    [![Menu suspenso Dispositivo](device-manager-images/win/10-device-menu-sml.png)](device-manager-images/win/10-device-menu.png#lightbox)

2. Selecione uma imagem do sistema para usar com esse dispositivo virtual clicando no menu suspenso **Imagem do sistema**. Esse menu lista as imagens de gerenciador de dispositivos do sistema instaladas em **Instalados**. A seção **Download** mostra as imagens de gerenciador de dispositivos do sistema não disponíveis atualmente em seu computador de desenvolvimento, mas que podem ser instaladas automaticamente:

    [![Menu suspenso Imagem do sistema](device-manager-images/win/11-system-image-menu-sml.png)](device-manager-images/win/11-system-image-menu.png#lightbox)

3. Nomeie o novo dispositivo. No exemplo a seguir, o nome do novo dispositivo é **Nexus 5 API 25**:

    [![Nomear o novo dispositivo](device-manager-images/win/12-device-name-sml.png)](device-manager-images/win/12-device-name.png#lightbox)

4. Edite todas as propriedades que você precisa modificar. Para alterar as propriedades, consulte [Edição de propriedades do Dispositivo Virtual Android](~/android/get-started/installation/android-emulator/device-properties.md).

5. Adicione todas as propriedades adicionais que precisem ser definidas explicitamente. A tela **Novo Dispositivo** mostra apenas as propriedades mais frequentemente modificadas, mas é possível clicar no menu suspenso **Adicionar Propriedade** (no canto inferior esquerdo) para adicionar outras propriedades. No exemplo a seguir, a propriedade `hw.lcd.backlight` é adicionada:

    [![Menu suspenso Adicionar Propriedade](device-manager-images/win/13-add-property-menu-sml.png)](device-manager-images/win/13-add-property-menu.png#lightbox)

6. Clique no botão **Criar** (canto inferior direito) para criar o novo dispositivo:

    ![Botão Criar](device-manager-images/win/14-create-button.png)

7. A tela **Aceitação da Licença** pode ser exibida. Clique em **Aceitar** se concordar com os termos de licença:

    ![Tela de Aceitação da Licença](device-manager-images/win/15-license-acceptance.png)

8. O Android Device Manager adiciona o novo dispositivo à lista de dispositivos virtuais instalados, mostrando um indicador de progresso **Criando** durante a criação do dispositivo:

    [![Indicador de progresso de Criação](device-manager-images/win/16-creating-the-device-sml.png)](device-manager-images/win/16-creating-the-device.png#lightbox)

9. Quando o processo de criação é concluído, o novo dispositivo é mostrado na lista de dispositivos virtuais instalados com um botão **Iniciar**, pronto para ser inicializado:

   [![Dispositivo recém-criado pronto para ser inicializado](device-manager-images/win/17-created-device-sml.png)](device-manager-images/win/17-created-device.png#lightbox)


### <a name="edit-device"></a>Editar Dispositivo

Para editar um dispositivo virtual existente, selecione-o e clique no botão **Editar** (localizado no canto superior direito da tela):

[![Botão Editar para modificar um novo dispositivo](device-manager-images/win/19-edit-button-sml.png)](device-manager-images/win/19-edit-button.png#lightbox)

Clicar em **Editar** inicializa o Editor de Dispositivo do dispositivo virtual selecionado:

[![Tela do Editor de Dispositivos](device-manager-images/win/20-device-editor-sml.png)](device-manager-images/win/20-device-editor.png#lightbox)

A tela **Editor de Dispositivos** lista as propriedades do dispositivo virtual na primeira coluna, com os valores correspondentes de cada propriedade na segunda coluna. Ao selecionar uma propriedade, uma descrição detalhada é exibida à direita.

Por exemplo, na captura de tela a seguir, a propriedade `hw.lcd.density` é alterada de **420** para **240**:

[![Exemplo de Edição de Dispositivo](device-manager-images/win/21-device-editing-sml.png)](device-manager-images/win/21-device-editing.png#lightbox)

Depois de fazer as alterações de configuração necessárias, clique no botão **Salvar**.
Para obter mais informações sobre como alterar propriedades do dispositivo virtual, consulte [Edição de propriedades do Dispositivo Virtual Android](~/android/get-started/installation/android-emulator/device-properties.md).

 
### <a name="additional-options"></a>Opções Adicionais

As opções adicionais para trabalhar com os dispositivos estão disponíveis no menu &hellip;, no canto superior direito:

[![Local do menu de opções adicionais](device-manager-images/win/22-overflow-menu-sml.png)](device-manager-images/win/22-overflow-menu.png#lightbox)

O menu de opções adicionais contém os seguintes itens:

-   **Duplicar e Editar** &ndash; Duplica o dispositivo selecionado no momento e o abre na tela **Novo Dispositivo** com um nome único e diferente. Por exemplo, selecionar **VisualStudio_android-23_x86_phone** e clicar em **Duplicar e Editar** acrescenta um contador ao nome:

    [![Tela Duplicar e Editar](device-manager-images/win/23-dupe-and-edit-sml.png)](device-manager-images/win/23-dupe-and-edit.png#lightbox)

-   **Revelar no Explorer** &ndash; Abre uma janela do Windows Explorer na pasta que contém os arquivos do dispositivo virtual. Por exemplo, selecionar **Nexus 5X API 25** e clicar em **Revelar no Explorer** abre uma janela como esta:

    [![Resultados de clicar em Revelar no Explorer](device-manager-images/win/24-reveal-in-explorer-sml.png)](device-manager-images/win/24-reveal-in-explorer.png#lightbox)

-   **Redefinição de Fábrica** &ndash; Redefine o dispositivo selecionado com suas configurações padrão, apagando todas as alterações feitas pelo usuário no estado interno do dispositivo enquanto ele estava em execução (isso também apaga o instantâneo [Inicialização Rápida](~/android/deploy-test/debugging/debug-on-emulator.md#quick-boot) atual, se houver). Essa alteração não altera as modificações feitas no dispositivo virtual durante a criação e a edição. Uma caixa de diálogo será exibida com o lembrete de que essa redefinição não pode ser desfeita. Clique em **Apagar dados do usuário** para confirmar a redefinição.

-   **Excluir** &ndash; Exclui permanentemente o dispositivo selecionado.
    Uma caixa de diálogo será exibida com o lembrete de que a ação de excluir um dispositivo não pode ser desfeita. Clique em **Excluir** se tiver certeza de que deseja excluir o dispositivo.


# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Este artigo explica como usar o Android Device Manager para criar, duplicar, personalizar e iniciar dispositivos virtuais Android.

[![Captura de tela que mostra o Android Device Manager na guia Dispositivos](device-manager-images/mac/01-devices-dialog-sml.png)](device-manager-images/mac/01-devices-dialog.png#lightbox)

> [!NOTE]
> Este guia se aplica apenas ao Visual Studio para Mac.
O Xamarin Studio não é compatível com o Android Device Manager.

O Android Device Manager é usado para criar e configurar *AVDs* (Dispositivos Virtuais Android) que são executados no [Android Emulator](~/android/deploy-test/debugging/debug-on-emulator.md).
Cada AVD é uma configuração de emulador que simula um dispositivo Android físico. Assim, é possível executar e testar aplicativos em uma variedade de configurações que simulam diferentes dispositivos Android físicos.

## <a name="requirements"></a>Requisitos

- Visual Studio para Mac 7.5 ou posterior.

- O SDK do Android 8.0 (API 26) ou posterior deve ser instalado por meio do Gerenciador de SDK do Android.


## <a name="launching-the-device-manager"></a>Inicializar o Gerenciador de Dispositivos

Inicie o Android Device Manager, clicando em **Ferramentas > Gerenciador de Dispositivos**:

[![Inicializando no menu Ferramentas](device-manager-images/mac/16-tools-menu-sml.png)](device-manager-images/mac/16-tools-menu.png#lightbox)

Antes de usar o Android Device Manager, é necessário instalar a versão 26.0.2 ou posterior das Android SDK Tools. Se a versão 26.0.2 ou posterior das Android SDK Tools não estiver instalada, este erro será exibido na inicialização:

![Caixa de diálogo de erro de instância do SDK do Android](device-manager-images/mac/02-sdk-instance-error.png)

Se essa caixa de diálogo de erro for exibida, clique em **OK** para abrir o Gerenciador do SDK do Android. No Gerenciador de SDK do Android, clique na guia **Ferramentas** e instale o seguinte:

-   **Android SDK Tools 26.0.2** ou posterior 
-   **Ferramentas da Plataforma SDK do Android 26.0.0** ou posterior 
-   **Ferramentas de Build do SDK do Android 26.0.0** ou posterior

Esses pacotes devem ser exibidos com status **Instalado**, como mostrado na captura de tela a seguir:

[![Instalação das Android SDK Tools 26.0](device-manager-images/mac/03-sdk-tools-sml.png)](device-manager-images/mac/03-sdk-tools.png#lightbox)

## <a name="main-screen"></a>Tela principal

Quando o Gerenciador de Dispositivos Android é iniciado pela primeira vez, uma tela exibe todos os dispositivos virtuais configurados no momento. O **Nome**, a **Imagem do Sistema** (nível da API Android), a **CPU**, o tamanho da **Memória** e a resolução de tela de cada dispositivo são exibidos:

[![Lista de dispositivos instalados e seus parâmetros](device-manager-images/mac/05-devices-list-sml.png)](device-manager-images/mac/05-devices-list.png#lightbox)

Clique no botão **Reproduzir** para inicializar o emulador com o dispositivo virtual de sua escolha:

[![Botão Iniciar de uma imagem de dispositivo](device-manager-images/mac/06-start-button-sml.png)](device-manager-images/mac/06-start-button.png#lightbox)

Depois da inicialização do emulador com o dispositivo virtual selecionado, o botão **Reproduzir** se transforma em um botão **Parar**, que pode ser utilizado para interromper o emulador:

[![Botão Parar do dispositivo em execução](device-manager-images/mac/07-stop-button-sml.png)](device-manager-images/mac/07-stop-button.png#lightbox)

### <a name="new-device"></a>Novo dispositivo

Para criar um novo dispositivo, clique no botão **Novo Dispositivo** (na área superior direita da tela):

[![Botão Novo para criar um novo dispositivo](device-manager-images/mac/08-new-button-sml.png)](device-manager-images/mac/08-new-button.png#lightbox)

Clicar em **Novo** inicia a tela **Novo Dispositivo**:

[![Tela Novo Dispositivo do Gerenciador de Dispositivos](device-manager-images/mac/09-new-device-editor-sml.png)](device-manager-images/mac/09-new-device-editor.png#lightbox)

Para configurar um novo dispositivo na tela **Novo Dispositivo**, siga estas etapas:

1. Selecione um dispositivo físico para emular clicando no menu suspenso **Dispositivo**:

    [![Menu suspenso Dispositivo](device-manager-images/mac/10-device-menu-sml.png)](device-manager-images/mac/10-device-menu.png#lightbox)

2. Selecione uma imagem do sistema para usar com esse dispositivo virtual clicando no menu suspenso **Imagem do sistema**. Esse menu lista as imagens de gerenciador de dispositivos do sistema instaladas em **Instalados**. A seção **Download** (se exibida) mostra as imagens de gerenciador de dispositivos do sistema não disponíveis atualmente em seu computador de desenvolvimento, mas que podem ser instaladas automaticamente:

    [![Menu suspenso Imagem do sistema](device-manager-images/mac/11-system-image-menu-sml.png)](device-manager-images/mac/11-system-image-menu.png#lightbox)

3. Nomeie o novo dispositivo. No exemplo a seguir, o nome do novo dispositivo é **Nexus 5X API 25**:

    [![Nomear o novo dispositivo](device-manager-images/mac/12-device-name-sml.png)](device-manager-images/mac/12-device-name.png#lightbox)

4. Edite todas as propriedades que você precisa modificar. Para alterar as propriedades, consulte [Edição de propriedades do Dispositivo Virtual Android](~/android/get-started/installation/android-emulator/device-properties.md).

5. Adicione todas as propriedades adicionais que precisem ser definidas explicitamente. A tela **Novo Dispositivo** mostra apenas as propriedades mais frequentemente modificadas, mas é possível clicar no menu suspenso **Adicionar Propriedade** (no canto inferior esquerdo) para adicionar outras propriedades:

    [![Menu suspenso Adicionar Propriedade](device-manager-images/mac/13-add-property-menu-sml.png)](device-manager-images/mac/13-add-property-menu.png#lightbox)

6. Também é possível clicar em **Personalizar** para definir uma nova propriedade para o dispositivo:

    ![Botão Criar](device-manager-images/mac/14-custom-button.png)

7. Clique no botão **Criar** (canto inferior direito) para criar o novo dispositivo:

    ![Botão Criar](device-manager-images/mac/15-create-button.png)

8. A tela **Aceitação da Licença** pode ser exibida. Clique em **Aceitar** se concordar com os termos de licença.

9. O Android Device Manager adiciona o novo dispositivo à lista de dispositivos virtuais instalados, mostrando um indicador de progresso **Criando** durante a criação do dispositivo:

    [![Indicador de progresso de Criação](device-manager-images/mac/17-creating-the-device-sml.png)](device-manager-images/mac/17-creating-the-device.png#lightbox)

10. Quando o processo de criação é concluído, o novo dispositivo é mostrado na lista de dispositivos com um botão **Reproduzir**, pronto para ser inicializado:

   [![Dispositivo recém-criado pronto para ser inicializado](device-manager-images/mac/18-created-device-sml.png)](device-manager-images/mac/18-created-device.png#lightbox)


### <a name="edit-device"></a>Editar Dispositivo

Para editar um dispositivo virtual existente, clique no menu suspenso **Opções Adicionais** (ícone de engrenagem) e selecione **Editar**:
 
[![Seleção do menu Editar para modificar um novo dispositivo](device-manager-images/mac/19-edit-button-sml.png)](device-manager-images/mac/19-edit-button.png#lightbox)

Clicar em **Editar** inicializa o Editor de Dispositivo do dispositivo virtual selecionado:
 
[![Tela do Editor de Dispositivos](device-manager-images/mac/20-device-editor-sml.png)](device-manager-images/mac/20-device-editor.png#lightbox)

A tela **Editor de Dispositivos** lista as propriedades do dispositivo virtual na primeira coluna, com os valores correspondentes de cada propriedade na segunda coluna. Ao selecionar uma propriedade, uma descrição detalhada é exibida à direita.

Por exemplo, na captura de tela a seguir, a propriedade `hw.lcd.density` é alterada de **320** para **240**; e a propriedade `hw.ramSize` para **768**:
 
[![Exemplo de Edição de Dispositivo](device-manager-images/mac/21-device-editing-sml.png)](device-manager-images/mac/21-device-editing.png#lightbox)

Depois de fazer as alterações de configuração necessárias, clique no botão **Salvar**.
Para obter mais informações sobre como alterar propriedades do dispositivo virtual, consulte [Edição de propriedades do Dispositivo Virtual Android](~/android/get-started/installation/android-emulator/device-properties.md).

 
### <a name="additional-options"></a>Opções Adicionais

As opções adicionais para trabalhar com os dispositivos estão disponíveis no menu suspenso localizado à esquerda do botão **Reproduzir**:

[![Local do menu de opções adicionais](device-manager-images/mac/22-overflow-menu-sml.png)](device-manager-images/mac/22-overflow-menu.png#lightbox)

O menu de opções adicionais contém os seguintes itens:

-   **Editar** &ndash; Abre o dispositivo selecionado no momento no editor de dispositivos, conforme descrito anteriormente.

-   **Duplicar e Editar** &ndash; Duplica o dispositivo selecionado no momento e o abre na tela **Novo Dispositivo** com um nome único e diferente.
    Por exemplo, selecionar **Nexus 5X API 25** e clicar em **Duplicar e Editar** acrescenta um contador ao nome:

    [![Tela Duplicar e Editar](device-manager-images/mac/23-dupe-and-edit-sml.png)](device-manager-images/mac/23-dupe-and-edit.png#lightbox)

-   **Revelar no Finder** &ndash; Abre uma janela do Finder do macOS na pasta que contém os arquivos do dispositivo virtual. Por exemplo, selecionar **Nexus 5X API 25** e clicar em **Revelar no Finder** abre uma janela como esta:

    [![Resultados de clicar em Revelar no Explorer](device-manager-images/mac/24-reveal-in-finder-sml.png)](device-manager-images/mac/24-reveal-in-finder.png#lightbox)

-   **Redefinição de Fábrica** &ndash; Redefine o dispositivo selecionado com suas configurações padrão, apagando todas as alterações feitas pelo usuário no estado interno do dispositivo enquanto ele estava em execução. Essa alteração não altera as modificações feitas no dispositivo virtual durante a criação e a edição. Uma caixa de diálogo será exibida com o lembrete de que essa redefinição não pode ser desfeita. Clique em **Apagar dados do usuário** para confirmar a redefinição.

-   **Excluir** &ndash; Exclui permanentemente o dispositivo selecionado.
    Uma caixa de diálogo será exibida com o lembrete de que a ação de excluir um dispositivo não pode ser desfeita. Clique em **Excluir** se tiver certeza de que deseja excluir o dispositivo.

-----

## <a name="troubleshooting"></a>Solução de problemas

As seções a seguir explicam como diagnosticar e solucionar problemas que podem ocorrer durante o uso do Android Device Manager para configurar dispositivos virtuais.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

### <a name="android-sdk-in-non-standard-location"></a>SDK do Android em local não padrão

Normalmente, o SDK do Android está instalado neste local:

**C:\\Program Files (x86)\\Android\\android-sdk**

Se o SDK não estiver instalado nesse local, você poderá ver este erro ao iniciar o Android Device Manager:

![Erro de instância do SDK do Android](troubleshooting-images/win/01-sdk-error.png)

Para solucionar esse problema, faça o seguinte:

1. Na área de trabalho do Windows, navegue até **C:\\Users\\*nomedeusuário*\\AppData\\Roaming\\XamarinDeviceManager**:

    ![Local do arquivo de log do Android Device Manager](troubleshooting-images/win/02-log-files.png)

2. Clique duas vezes para abrir um dos arquivos de log e localize o **Caminho do arquivo de configuração**. Por exemplo:

    [![Caminho do arquivo de configuração no arquivo de log](troubleshooting-images/win/03-config-file-path-sml.png)](troubleshooting-images/win/03-config-file-path.png#lightbox)

3. Navegue até esse local e clique duas vezes em **user.config** para abri-lo. 

4. Em **user.config**, localize o elemento **&lt;UserSettings&gt;** e adicione um atributo **AndroidSdkPath** a ele. Defina esse atributo no caminho em que o SDK do Android está instalado no computador e salve o arquivo. Por exemplo, **&lt;UserSettings&gt;** seria parecido com o seguinte se o SDK do Android estivesse instalado em **C:\\Programs\\Android\\SDK**:
        
    ```xml
    <UserSettings SdkLibLastWriteTimeUtcTicks="636409365200000000" AndroidSdkPath="C:\Programs\Android\SDK" />
    ```

Depois de fazer essa alteração em **user.config**, você poderá iniciar o Android Device Manager.

### <a name="snapshot-disables-wifi-on-android-oreo"></a>O Snapshot desabilita o WiFi no Android Oreo

Se você configurou um AVD para o Android Oreo com acesso Wi-Fi simulado, reiniciar o AVD após um instantâneo pode fazer com esse acesso seja desabilitado.

Para solucionar esse problema:

1. Selecione o AVD no Android Device Manager.

2. No menu de opções adicionais, clique em **Revelar no Explorer**.

3. Navegue até **snapshots > default_boot**.

4. Exclua o arquivo **snapshot.pb**:

    ![Local do arquivo snapshot.pb](troubleshooting-images/win/05-delete-snapshot.png)

5. Reinicie o AVD. 

Depois que essas alterações forem feitas, o AVD será reiniciado em um estado que permita que o Wi-Fi funcione novamente.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

### <a name="snapshot-disables-wifi-on-android-oreo"></a>O Snapshot desabilita o WiFi no Android Oreo

Se você configurou um AVD para o Android Oreo com acesso Wi-Fi simulado, reiniciar o AVD após um instantâneo pode fazer com esse acesso seja desabilitado.

Para solucionar esse problema:

1. Selecione o AVD no Android Device Manager.

2. No menu de opções adicionais, clique em **Revelar no Localizador**.

3. Navegue até **snapshots > default_boot**.

4. Exclua o arquivo **snapshot.pb**:

    [![Local do arquivo snapshot.pb](troubleshooting-images/mac/02-delete-snapshot-sml.png)](troubleshooting-images/mac/02-delete-snapshot.png#lightbox)

5. Reinicie o AVD. 

Depois que essas alterações forem feitas, o AVD será reiniciado em um estado que permita que o Wi-Fi funcione novamente.

-----

### <a name="generating-a-bug-report"></a>Gerando um relatório de bugs

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Se encontrar um problema com o Android Device Manager que não pode ser resolvido usando as dicas de solução de problemas acima, envie um relatório de bugs clicando com o botão direito do mouse na barra de título e selecionando **Gerar Relatório de Bugs**:

[![Local do item de menu para enviar um relatório de bugs](troubleshooting-images/win/04-bug-report-sml.png)](troubleshooting-images/win/04-bug-report.png#lightbox)


# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Se encontrar um problema com o Android Device Manager que não possa ser resolvido usando as dicas de solução de problemas acima, envie um relatório de bugs clicando em **Ajuda > Gerar Relatório de Bugs**:

[![Local do item de menu para enviar um relatório de bugs](troubleshooting-images/mac/01-bug-report-sml.png)](troubleshooting-images/mac/01-bug-report.png#lightbox)


-----

## <a name="summary"></a>Resumo

Este guia apresentou o Android Device Manager disponível no Visual Studio para Mac e nas Ferramentas do Visual Studio para Xamarin. Os recursos essenciais foram explicados, como iniciar e parar o Android Emulator, selecionar um AVD (Dispositivo Virtual Android) para execução, criar novos dispositivos virtuais e como editar um dispositivo virtual. Foi explicado como editar propriedades de hardware de perfil para personalização adicional e fornecidas dicas de solução de problemas comuns.


## <a name="related-links"></a>Links relacionados

- [Alterações nas Ferramentas do SDK do Android](~/android/troubleshooting/sdk-cli-tooling-changes.md)
- [Depurando no Android Emulator](~/android/deploy-test/debugging/debug-on-emulator.md)
- [Notas de Versão das SDK Tools (Google)](https://developer.android.com/studio/releases/sdk-tools)
- [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html)
- [sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html)
