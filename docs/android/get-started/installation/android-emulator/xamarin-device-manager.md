---
title: Gerenciador de Dispositivos do Xamarin Android
description: O Gerenciador de Dispositivos do Xamarin Android, atualmente em versão prévia, substitui o Gerenciador de Dispositivos herdado do Google. Este guia explica como usar o Gerenciador de Dispositivos do Xamarin Android para criar e configurar AVDs (Dispositivos Virtuais Android) que emulam dispositivos Android. É possível usar esses dispositivos virtuais para executar e testar aplicativos, sem a necessidade de um dispositivo físico.
ms.prod: xamarin
ms.assetid: ECB327F3-FF1C-45CC-9FA6-9C11032BD5EF
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/20/2018
ms.openlocfilehash: 3dd67f25e389adda1af31f4a2b85abd4cfd751d0
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="xamarin-android-device-manager"></a>Gerenciador de Dispositivos do Xamarin Android

_O Gerenciador de Dispositivos do Xamarin Android, atualmente em versão prévia, substitui o Gerenciador de Dispositivos herdado do Google. Este guia explica como usar o Gerenciador de Dispositivos do Xamarin Android para criar e configurar AVDs (Dispositivos Virtuais Android) que emulam dispositivos Android. É possível usar esses dispositivos virtuais para executar e testar aplicativos, sem a necessidade de um dispositivo físico._

![Atualmente em versão prévia](~/media/shared/preview.png)

 
## <a name="overview"></a>Visão geral

Depois de confirmar se a aceleração de hardware está habilitada (conforme descrito em [Aceleração de hardware](~/android/get-started/installation/android-emulator/hardware-acceleration.md)), a etapa seguinte é criar dispositivos virtuais para testar e depurar o aplicativo. É possível usar o Gerenciador de Dispositivos do Xamarin Android para criar dispositivos virtuais para uso pelo Emulador do SDK do Android.

Por que usar o Gerenciador de Dispositivos do Xamarin Android em vez do [Gerenciador de Dispositivos do Google](~/android/get-started/installation/android-emulator/google-emulator-manager.md)?
O Google removeu o suporte para gerenciadores de AVD e SDK baseados em interface do usuário a partir da versão 26.0.1 do Android SDK Tools, com o objetivo de favorecer suas novas ferramentas de CLI (Interface de Linha de Comando). Por causa dessa alteração, é necessário usar o [Gerenciador de Xamarin SDK](~/android/get-started/installation/android-sdk.md) e o Gerenciador de Dispositivos do Xamarin Android ao atualizar o Android SDK Tools 26.0.1 e versões posteriores (exigido para o desenvolvimento do Android 8.0 Oreo).


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Este guia explica como instalar e utilizar o Gerenciador de Dispositivos do Xamarin Android para Visual Studio no Windows (ou [para Mac](?tabs=vsmac)):

[![Captura de tela que mostra o Gerenciador de Dispositivos do Xamarin Android na guia Dispositivos](xamarin-device-manager-images/win/01-devices-dialog-sml.png)](xamarin-device-manager-images/win/01-devices-dialog.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Este guia explica como instalar e utilizar o Gerenciador de Dispositivos do Xamarin Android para Visual Studio para Mac (ou [para Windows](?tabs=vswin)):

[![Captura de tela que mostra o Gerenciador de Dispositivos do Xamarin Android na guia Dispositivos](xamarin-device-manager-images/mac/01-devices-dialog-sml.png)](xamarin-device-manager-images/mac/01-devices-dialog.png#lightbox)

> [!NOTE]
> Este guia se aplica apenas ao Visual Studio para Mac.
O Xamarin Studio não é compatível com o Gerenciador de Dispositivos do Xamarin Android.

-----

O Gerenciador de Dispositivos do Xamarin Android é usado para criar e configurar *AVDs* (Dispositivos Virtuais Android) executados no [Emulador do SDK do Android](~/android/deploy-test/debugging/android-sdk-emulator/index.md).
Cada AVD é uma configuração de emulador que simula um dispositivo Android físico. Assim, é possível executar e testar aplicativos em uma variedade de configurações que simulam diferentes dispositivos Android físicos. O Gerenciador de Dispositivos do Xamarin Android substitui o Gerenciador de AVD autônomo do Google (que foi preterido).

Este guia mostra como instalar e iniciar o Gerenciador de Dispositivos Android. Você aprenderá a criar, duplicar, personalizar e inicializar dispositivos virtuais. Este guia também explica como configurar as propriedades de cada dispositivo virtual (como nível da API, CPU, memória e resolução), a fim de habilitar/desabilitar sensores simulados, como acelerômetro, GPS, orientação e sensor de luz, bem como configurar o tipo de aceleração de hardware usado pelo dispositivo virtual em questão.

## <a name="requirements"></a>Requisitos

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Para usar o Gerenciador de Dispositivos do Xamarin Android, serão necessários:

-   Visual Studio 2017 versão 15.5 ou posterior. Visual Studio Community e superior tem suporte.

-   Xamarin para Visual Studio versão 4.8 ou posterior. Para saber mais sobre a atualização do Xamarin, consulte [Alterar o Canal de Atualizações](https://developer.xamarin.com/recipes/cross-platform/ide/change_updates_channel/).

-   A versão mais recente do [instalador do Gerenciador de Dispositivos do Xamarin](https://go.microsoft.com/fwlink/?linkid=865528) para Windows.

-   **SDK do Android** &ndash; O SDK do Android deve ser instalado (consulte [Instalação do SDK do Android](~/android/get-started/installation/android-sdk.md)) e a versão 26.0 das SDK Tools deve ser instalada conforme descrito na seção a seguir. Lembre-se de instalar o SDK do Android neste local (se não estiver instalado): **C:\\Program Files (x86)\\Android\\android-sdk**.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

-   Visual Studio para Mac 7.4 ou posterior.

-   A versão mais recente do [instalador do Gerenciador de Dispositivos do Xamarin](https://go.microsoft.com/fwlink/?linkid=865527) para macOS.

-   **SDK do Android** &ndash; O SDK do Android 8.0 (API 26) ou posterior deve ser instalado por meio do Gerenciador de SDK.

-----

 
## <a name="installing-the-device-manager"></a>Instalar o Gerenciador de Dispositivos

Siga estas etapas para instalar o Gerenciador de Dispositivos do Xamarin Android:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Baixe o [instalador do Gerenciador de Dispositivos do Xamarin](https://go.microsoft.com/fwlink/?linkid=865528) para Windows.

2. Clique duas vezes em **Xamarin.DeviceManager.msi** e siga as instruções de instalação: 

    ![Assistente de instalação do Gerenciador de Dispositivos do Xamarin Android](xamarin-device-manager-images/win/30-installer.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Baixe o [instalador do Gerenciador de Dispositivos do Xamarin](https://go.microsoft.com/fwlink/?linkid=865527) para macOS.

2. Clique duas vezes em **AndroidDevices.pkg** e siga as instruções de instalação: 

    [![Assistente de instalação do Gerenciador de Dispositivos do Xamarin Android](xamarin-device-manager-images/mac/30-installer-sml.png)](xamarin-device-manager-images/mac/30-installer.png#lightbox)

-----

 
## <a name="launching-the-device-manager"></a>Inicializar o Gerenciador de Dispositivos

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

No Visual Studio 15.6 Versão Prévia 3 e posterior, é possível iniciar o Gerenciador de Dispositivos do Xamarin Android no menu **Ferramentas**. Se estiver usando o Visual Studio 15.6 Versão Prévia 3 ou posterior, clique em **Ferramentas > Gerenciador do Android Emulator** para iniciar o Gerenciador de Dispositivos:

[![Inicializando no menu Ferramentas](xamarin-device-manager-images/win/04-tools-menu-sml.png)](xamarin-device-manager-images/win/04-tools-menu.png#lightbox)

Se você estiver usando uma versão anterior do Visual Studio, inicie o Gerenciador de Dispositivos do Xamarin Android no menu **Iniciar** do Windows.

![Gerenciador de Dispositivos do Xamarin Android no menu Iniciar](xamarin-device-manager-images/win/31-start-menu.png)

Clique com o botão direito do mouse em **Gerenciador de Dispositivos do Xamarin Android** e selecione **Mais > Executar como administrador**. Se a caixa de diálogo de erro a seguir for exibida na inicialização, consulte a seção [Solução de Problemas](#troubleshooting) para obter instruções de soluções alternativas:

![Erro de instância do SDK do Android](xamarin-device-manager-images/win/32-sdk-error.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

No Visual Studio para Mac 7.6 Versão Prévia 3 (atualmente no canal alfa) ou posterior, você pode iniciar o Gerenciador de Dispositivos do Xamarin Android selecionando **Ferramentas > Emulator Manager**:

[![Inicializando no menu Ferramentas](xamarin-device-manager-images/mac/16-tools-menu-sml.png)](xamarin-device-manager-images/mac/16-tools-menu.png#lightbox)

Se você estiver usando uma versão anterior do Visual Studio para Mac, inicie o Gerenciador de Dispositivos do Xamarin Android de maneira independente. Na pasta **Aplicativos**, localize **Dispositivos Android** e clique duas vezes para iniciar:

[![Local do Gerenciador de Dispositivos do Xamarin Android no Finder](xamarin-device-manager-images/mac/31-location-in-finder-sml.png)](xamarin-device-manager-images/mac/31-location-in-finder.png#lightbox)


-----

Antes de usar o Gerenciador de Dispositivos Android, é necessário instalar as Android SDK Tools versão 26.0.0 ou posterior. Se as Android SDK Tools 26.0.0 ou posterior não estiverem instaladas, este erro será exibido na inicialização:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Caixa de diálogo de erro de instância do SDK do Android](xamarin-device-manager-images/win/02-sdk-instance-error.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

![Caixa de diálogo de erro de instância do SDK do Android](xamarin-device-manager-images/mac/02-sdk-instance-error.png)

-----

Se essa caixa de diálogo de erro for exibida, clique em **OK** para abrir o Gerenciador do SDK do Android. No Gerenciador do SDK do Android, clique na guia **Ferramentas** e instale as **Android SDK Tools 26.0.2** ou posterior, as **Ferramentas de Plataforma do SDK do Android 26.0.0** ou posterior e as **Ferramentas de Build do SDK do Android 26.0.0** (ou posterior):


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Instalação das Android SDK Tools 26.0](xamarin-device-manager-images/win/03-sdk-tools-sml.png)](xamarin-device-manager-images/win/03-sdk-tools.png#lightbox)

Depois de instalar esses pacotes, feche o Gerenciador de SDK e reinicie o Gerenciador de Dispositivos Android.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Instalação das Android SDK Tools 26.0](xamarin-device-manager-images/mac/03-sdk-tools-sml.png)](xamarin-device-manager-images/mac/03-sdk-tools.png#lightbox)

-----

 
## <a name="main-screen"></a>Tela principal

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Quando o Gerenciador de Dispositivos Android é iniciado pela primeira vez, uma tela exibe todos os dispositivos virtuais configurados no momento. O **Nome**, o **Sistema Operacional** (nível da API Android), a **CPU**, o tamanho da **Memória** e a resolução de tela de cada dispositivo são exibidos:

[![Lista de dispositivos instalados e seus parâmetros](xamarin-device-manager-images/win/05-installed-list-sml.png)](xamarin-device-manager-images/win/05-installed-list.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Quando o Gerenciador de Dispositivos Android é iniciado pela primeira vez, uma tela exibe todos os dispositivos virtuais configurados no momento. O **Nome**, a **Imagem do Sistema** (nível da API Android), a **CPU**, o tamanho da **Memória** e a resolução de tela de cada dispositivo são exibidos:

[![Lista de dispositivos instalados e seus parâmetros](xamarin-device-manager-images/mac/05-devices-list-sml.png)](xamarin-device-manager-images/mac/05-devices-list.png#lightbox)

-----

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Ao clicar em um dispositivo na lista, o botão **Iniciar** é exibido no lado direito. É possível clicar no botão **Iniciar** para inicializar o emulador com este dispositivo virtual:

[![Botão Iniciar de uma imagem de dispositivo](xamarin-device-manager-images/win/06-start-button-sml.png)](xamarin-device-manager-images/win/06-start-button.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Clique no botão **Reproduzir** para inicializar o emulador com o dispositivo virtual de sua escolha:
 
[![Botão Iniciar de uma imagem de dispositivo](xamarin-device-manager-images/mac/06-start-button-sml.png)](xamarin-device-manager-images/mac/06-start-button.png#lightbox)
 
-----

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Depois da inicialização do emulador com o dispositivo virtual selecionado, o botão **Iniciar** se transforma em um botão **Parar**, que pode ser utilizado para interromper o emulador:

[![Botão Parar do dispositivo em execução](xamarin-device-manager-images/win/07-stop-button-sml.png)](xamarin-device-manager-images/win/07-stop-button.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Depois da inicialização do emulador com o dispositivo virtual selecionado, o botão **Reproduzir** se transforma em um botão **Parar**, que pode ser utilizado para interromper o emulador:
 
[![Botão Parar do dispositivo em execução](xamarin-device-manager-images/mac/07-stop-button-sml.png)](xamarin-device-manager-images/mac/07-stop-button.png#lightbox)
 
-----

 
### <a name="new-device"></a>Novo dispositivo

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Para criar um novo dispositivo, clique no botão **Novo** (na área superior direita da tela):

[![Botão Novo para criar um novo dispositivo](xamarin-device-manager-images/win/08-new-button-sml.png)](xamarin-device-manager-images/win/08-new-button.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Para criar um novo dispositivo, clique no botão **Novo Dispositivo** (na área superior direita da tela):
 
[![Botão Novo para criar um novo dispositivo](xamarin-device-manager-images/mac/08-new-button-sml.png)](xamarin-device-manager-images/mac/08-new-button.png#lightbox)
 
-----

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Ao clicar em **Novo**, a tela **Novo Dispositivo** é iniciada:

[![Tela Novo Dispositivo do Gerenciador de Dispositivos](xamarin-device-manager-images/win/09-new-device-editor-sml.png)](xamarin-device-manager-images/win/09-new-device-editor.png#lightbox)

Para configurar um novo dispositivo na tela **Novo Dispositivo**, siga estas etapas:

1. Selecione um dispositivo físico para emular clicando no menu suspenso **Dispositivo**:

    [![Menu suspenso Dispositivo](xamarin-device-manager-images/win/10-device-menu-sml.png)](xamarin-device-manager-images/win/10-device-menu.png#lightbox)

2. Selecione uma imagem do sistema para usar com esse dispositivo virtual clicando no menu suspenso **Imagem do sistema**. Esse menu lista as imagens do sistema instaladas em **Instalados**. A seção **Download** mostra as imagens do sistema indisponíveis atualmente no computador de desenvolvimento, mas que podem ser instaladas automaticamente:

    [![Menu suspenso Imagem do sistema](xamarin-device-manager-images/win/11-system-image-menu-sml.png)](xamarin-device-manager-images/win/11-system-image-menu.png#lightbox)

3. Nomeie o novo dispositivo. No exemplo a seguir, o nome do novo dispositivo é **Nexus 5 API 25**:

    [![Nomear o novo dispositivo](xamarin-device-manager-images/win/12-device-name-sml.png)](xamarin-device-manager-images/win/12-device-name.png#lightbox)

4. Edite todas as propriedades que você precisa modificar. Para alterar as propriedades, consulte [Propriedades de Perfil](#properties) mais adiante neste guia.

5. Adicione todas as propriedades adicionais que precisem ser definidas explicitamente. A tela **Novo Dispositivo** mostra apenas as propriedades mais frequentemente modificadas, mas é possível clicar no menu suspenso **Adicionar Propriedade** (no canto inferior esquerdo) para adicionar outras propriedades. No exemplo a seguir, a propriedade `hw.lcd.backlight` é adicionada:

    [![Menu suspenso Adicionar Propriedade](xamarin-device-manager-images/win/13-add-property-menu-sml.png)](xamarin-device-manager-images/win/13-add-property-menu.png#lightbox)

6. Clique no botão **Criar** (canto inferior direito) para criar o novo dispositivo:

    ![Botão Criar](xamarin-device-manager-images/win/14-create-button.png)

7. A tela **Aceitação da Licença** pode ser exibida. Clique em **Aceitar** se concordar com os termos de licença:

    ![Tela de Aceitação da Licença](xamarin-device-manager-images/win/15-license-acceptance.png)

8. O Gerenciador de Dispositivos Android adiciona o novo dispositivo à lista de dispositivos virtuais instalados com um indicador de progresso **Criando** enquanto o dispositivo é criado:

    [![Indicador de progresso de Criação](xamarin-device-manager-images/win/16-creating-the-device-sml.png)](xamarin-device-manager-images/win/16-creating-the-device.png#lightbox)

9. Quando o processo de criação é concluído, o novo dispositivo é mostrado na lista de dispositivos virtuais instalados com um botão **Iniciar**, pronto para ser inicializado:

   [![Dispositivo recém-criado pronto para ser inicializado](xamarin-device-manager-images/win/17-created-device-sml.png)](xamarin-device-manager-images/win/17-created-device.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Clicar em **Novo** inicia a tela **Novo Dispositivo**:

[![Tela Novo Dispositivo do Gerenciador de Dispositivos](xamarin-device-manager-images/mac/09-new-device-editor-sml.png)](xamarin-device-manager-images/mac/09-new-device-editor.png#lightbox)

Para configurar um novo dispositivo na tela **Novo Dispositivo**, siga estas etapas:

1. Selecione um dispositivo físico para emular clicando no menu suspenso **Dispositivo**:

    [![Menu suspenso Dispositivo](xamarin-device-manager-images/mac/10-device-menu-sml.png)](xamarin-device-manager-images/mac/10-device-menu.png#lightbox)

2. Selecione uma imagem do sistema para usar com esse dispositivo virtual clicando no menu suspenso **Imagem do sistema**. Esse menu lista as imagens do sistema instaladas em **Instalados**. A seção **Download** (se mostrada) lista as imagens do sistema indisponíveis atualmente no computador de desenvolvimento, mas que podem ser instaladas automaticamente:

    [![Menu suspenso Imagem do sistema](xamarin-device-manager-images/mac/11-system-image-menu-sml.png)](xamarin-device-manager-images/mac/11-system-image-menu.png#lightbox)

3. Nomeie o novo dispositivo. No exemplo a seguir, o nome do novo dispositivo é **Nexus 5X API 25**:

    [![Nomear o novo dispositivo](xamarin-device-manager-images/mac/12-device-name-sml.png)](xamarin-device-manager-images/mac/12-device-name.png#lightbox)

4. Edite todas as propriedades que você precisa modificar. Para alterar as propriedades, consulte [Propriedades de Perfil](#properties) mais adiante neste guia.

5. Adicione todas as propriedades adicionais que precisem ser definidas explicitamente. A tela **Novo Dispositivo** mostra apenas as propriedades mais frequentemente modificadas, mas é possível clicar no menu suspenso **Adicionar Propriedade** (no canto inferior esquerdo) para adicionar outras propriedades:

    [![Menu suspenso Adicionar Propriedade](xamarin-device-manager-images/mac/13-add-property-menu-sml.png)](xamarin-device-manager-images/mac/13-add-property-menu.png#lightbox)

6. Também é possível clicar em **Personalizar** para definir uma nova propriedade para o dispositivo:

    ![Botão Criar](xamarin-device-manager-images/mac/14-custom-button.png)

7. Clique no botão **Criar** (canto inferior direito) para criar o novo dispositivo:

    ![Botão Criar](xamarin-device-manager-images/mac/15-create-button.png)

8. A tela **Aceitação da Licença** pode ser exibida. Clique em **Aceitar** se concordar com os termos de licença.

9. Enquanto o dispositivo é criado, o Gerenciador de Dispositivos Android adiciona o novo dispositivo à lista de dispositivos virtuais instalados com um indicador de progresso **Criando**:

    [![Indicador de progresso de Criação](xamarin-device-manager-images/mac/17-creating-the-device-sml.png)](xamarin-device-manager-images/mac/17-creating-the-device.png#lightbox)

10. Quando o processo de criação é concluído, o novo dispositivo é mostrado na lista de dispositivos com um botão **Reproduzir**, pronto para ser inicializado:

   [![Dispositivo recém-criado pronto para ser inicializado](xamarin-device-manager-images/mac/18-created-device-sml.png)](xamarin-device-manager-images/mac/18-created-device.png#lightbox)

-----


<a name="device-edit" />


### <a name="edit-device"></a>Editar Dispositivo

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Para editar um dispositivo virtual existente, selecione-o e clique no botão **Editar** (localizado no canto superior direito da tela):

[![Botão Editar para modificar um novo dispositivo](xamarin-device-manager-images/win/19-edit-button-sml.png)](xamarin-device-manager-images/win/19-edit-button.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Para editar um dispositivo virtual existente, clique no menu suspenso **Opções Adicionais** (ícone de engrenagem) e selecione **Editar**:
 
[![Seleção do menu Editar para modificar um novo dispositivo](xamarin-device-manager-images/mac/19-edit-button-sml.png)](xamarin-device-manager-images/mac/19-edit-button.png#lightbox)
 
-----

Clicar em **Editar** inicializa o Editor de Dispositivo do dispositivo virtual selecionado:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Tela do Editor de Dispositivos](xamarin-device-manager-images/win/20-device-editor-sml.png)](xamarin-device-manager-images/win/20-device-editor.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)
 
[![Tela do Editor de Dispositivos](xamarin-device-manager-images/mac/20-device-editor-sml.png)](xamarin-device-manager-images/mac/20-device-editor.png#lightbox)
 
-----

A tela **Editor de Dispositivos** lista as propriedades do dispositivo virtual na primeira coluna, com os valores correspondentes de cada propriedade na segunda coluna. Ao selecionar uma propriedade, uma descrição detalhada é exibida à direita.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Por exemplo, na captura de tela a seguir, a propriedade `hw.lcd.density` é alterada de **420** para **240**:

[![Exemplo de Edição de Dispositivo](xamarin-device-manager-images/win/21-device-editing-sml.png)](xamarin-device-manager-images/win/21-device-editing.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Por exemplo, na captura de tela a seguir, a propriedade `hw.lcd.density` é alterada de **320** para **240**; e a propriedade `hw.ramSize` para **768**:
 
[![Exemplo de Edição de Dispositivo](xamarin-device-manager-images/mac/21-device-editing-sml.png)](xamarin-device-manager-images/mac/21-device-editing.png#lightbox)
 
-----

Depois de fazer as alterações de configuração necessárias, clique no botão **Salvar**.
Para saber mais sobre a alteração de propriedades de dispositivos virtuais, consulte [Propriedades de Perfil](#properties) mais adiante neste guia.


 
### <a name="additional-options"></a>Opções Adicionais

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

As opções adicionais para trabalhar com os dispositivos estão disponíveis no menu &hellip;, no canto superior direito:

[![Local do menu de opções adicionais](xamarin-device-manager-images/win/22-overflow-menu-sml.png)](xamarin-device-manager-images/win/22-overflow-menu.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

As opções adicionais para trabalhar com os dispositivos estão disponíveis no menu suspenso localizado à esquerda do botão **Reproduzir**:

[![Local do menu de opções adicionais](xamarin-device-manager-images/mac/22-overflow-menu-sml.png)](xamarin-device-manager-images/mac/22-overflow-menu.png#lightbox)

-----

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

O menu de opções adicionais contém os seguintes itens:

-   **Duplicar e Editar** &ndash; Duplica o dispositivo selecionado no momento e o abre na tela **Novo Dispositivo** com um nome único e diferente. Por exemplo, selecionar **VisualStudio_android-23_x86_phone** e clicar em **Duplicar e Editar** acrescenta um contador ao nome:

    [![Tela Duplicar e Editar](xamarin-device-manager-images/win/23-dupe-and-edit-sml.png)](xamarin-device-manager-images/win/23-dupe-and-edit.png#lightbox)

-   **Revelar no Explorer** &ndash; Abre uma janela do Windows Explorer na pasta que contém os arquivos do dispositivo virtual. Por exemplo, selecionar **Nexus 5X API 25** e clicar em **Revelar no Explorer** abre uma janela como esta:

    [![Resultados de clicar em Revelar no Explorer](xamarin-device-manager-images/win/24-reveal-in-explorer-sml.png)](xamarin-device-manager-images/win/24-reveal-in-explorer.png#lightbox)

-   **Redefinição de Fábrica** &ndash; Redefine o dispositivo selecionado com suas configurações padrão, apagando todas as alterações feitas pelo usuário no estado interno do dispositivo enquanto ele estava em execução. Essa alteração não altera as modificações feitas no dispositivo virtual durante a criação e a edição. Uma caixa de diálogo será exibida com o lembrete de que essa redefinição não pode ser desfeita. Clique em **Apagar dados do usuário** para confirmar a redefinição.

-   **Excluir** &ndash; Exclui permanentemente o dispositivo selecionado.
    Uma caixa de diálogo será exibida com o lembrete de que a ação de excluir um dispositivo não pode ser desfeita. Clique em **Excluir** se tiver certeza de que deseja excluir o dispositivo.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

O menu de opções adicionais contém os seguintes itens:

-   **Editar** &ndash; Abre o dispositivo selecionado no momento no Editor de Dispositivos, conforme descrito anteriormente em [Editar Dispositivo](#device-edit).

-   **Duplicar e Editar** &ndash; Duplica o dispositivo selecionado no momento e o abre na tela **Novo Dispositivo** com um nome único e diferente.
    Por exemplo, selecionar **Nexus 5X API 25** e clicar em **Duplicar e Editar** acrescenta um contador ao nome:

    [![Tela Duplicar e Editar](xamarin-device-manager-images/mac/23-dupe-and-edit-sml.png)](xamarin-device-manager-images/mac/23-dupe-and-edit.png#lightbox)

-   **Revelar no Finder** &ndash; Abre uma janela do Finder do macOS na pasta que contém os arquivos do dispositivo virtual. Por exemplo, selecionar **Nexus 5X API 25** e clicar em **Revelar no Finder** abre uma janela como esta:

    [![Resultados de clicar em Revelar no Explorer](xamarin-device-manager-images/mac/24-reveal-in-finder-sml.png)](xamarin-device-manager-images/mac/24-reveal-in-finder.png#lightbox)

-   **Redefinição de Fábrica** &ndash; Redefine o dispositivo selecionado com suas configurações padrão, apagando todas as alterações feitas pelo usuário no estado interno do dispositivo enquanto ele estava em execução. Essa alteração não altera as modificações feitas no dispositivo virtual durante a criação e a edição. Uma caixa de diálogo será exibida com o lembrete de que essa redefinição não pode ser desfeita. Clique em **Apagar dados do usuário** para confirmar a redefinição.

-   **Excluir** &ndash; Exclui permanentemente o dispositivo selecionado.
    Uma caixa de diálogo será exibida com o lembrete de que a ação de excluir um dispositivo não pode ser desfeita. Clique em **Excluir** se tiver certeza de que deseja excluir o dispositivo.

-----

<a name="properties" />
 

## <a name="profile-properties"></a>Propriedades de Perfil

As telas **Novo Dispositivo** e **Editor de Dispositivos** listam as propriedades do dispositivo virtual na primeira coluna, com os valores correspondentes de cada propriedade na segunda coluna. Ao selecionar uma propriedade, uma descrição detalhada é exibida à direita. É possível modificar as *propriedades de perfil de hardware* e as *propriedades de AVD*.
As propriedades de perfil de hardware (como `hw.ramSize` e `hw.accelerometer`) descrevem as características físicas do dispositivo emulado. Essas características são o tamanho da tela, a quantidade de RAM disponível e se existe um acelerômetro. As propriedades de AVD especificam a operação do AVD quando ele é executado. Por exemplo, as propriedades de AVD podem ser configuradas para especificar como o AVD usa a placa gráfica do computador de desenvolvimento para renderização.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

É possível alterar as propriedades usando as seguintes diretrizes:

-   Para alterar uma propriedade booliana, clique na marca de seleção à direita dela:

    ![Alterar uma propriedade booliana](xamarin-device-manager-images/win/25-boolean-value.png)

-   Para alterar uma propriedade *enum* (enumerada), clique na seta para baixo à direita da propriedade e escolha um novo valor.

    ![Alterar uma propriedade de enumeração](xamarin-device-manager-images/win/27-enum-value.png)

-   Para alterar uma propriedade de cadeia de caracteres ou inteiro, clique duas vezes na configuração da cadeia de caracteres ou inteiro atual na coluna valor e insira um novo valor.

    ![Alterar uma propriedade de inteiro](xamarin-device-manager-images/win/26-integer-value.png)


# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

É possível alterar as propriedades usando as seguintes diretrizes:

-   Para alterar uma propriedade booliana, clique na marca de seleção à direita dela:

    ![Alterar uma propriedade booliana](xamarin-device-manager-images/mac/25-boolean-value.png)

-   Para alterar uma propriedade *enum* (enumerada), clique no menu suspenso à direita da propriedade e escolha um novo valor.

    ![Alterar uma propriedade de enumeração](xamarin-device-manager-images/mac/27-enum-value.png)

-   Para alterar uma propriedade de cadeia de caracteres ou inteiro, clique duas vezes na configuração da cadeia de caracteres ou inteiro atual na coluna valor e insira um novo valor.

    ![Alterar uma propriedade de inteiro](xamarin-device-manager-images/mac/26-integer-value.png)


-----

A tabela a seguir mostra uma explicação detalhada das propriedades listadas nas telas **Novo Dispositivo** e **Editor de Dispositivos**:

[!include[](~/android/includes/emulator-properties.md)]

Para saber mais sobre essas propriedades, consulte [Propriedades de Perfil de Hardware](https://developer.android.com/studio/run/managing-avds.html#hpproperties).


<a name="troubleshooting" />

## <a name="troubleshooting"></a>Solução de problemas

A seguir, problemas e soluções alternativas comuns do Gerenciador de Dispositivos do Xamarin Android são descritos:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

### <a name="android-sdk-in-non-standard-location"></a>SDK do Android em local não padrão

Normalmente, o SDK do Android está instalado neste local:

**C:\\Program Files (x86)\\Android\\android-sdk**

Se o SDK não estiver instalado nesse local, este erro poderá ocorrer na inicialização:

![Erro de instância do SDK do Android](xamarin-device-manager-images/win/32-sdk-error.png)

Para solucionar esse problema, faça o seguinte:

1. Na área de trabalho do Windows, navegue até **C:\\Users\\*nomedeusuário*\\AppData\\Roaming\\XamarinDeviceManager**:

    ![Local do arquivo de log do Gerenciador de Dispositivos Xamarin](xamarin-device-manager-images/win/33-log-files.png)

2. Clique duas vezes para abrir um dos arquivos de log e localize o **Caminho do arquivo de configuração**. Por exemplo:

    [![Caminho do arquivo de configuração no arquivo de log](xamarin-device-manager-images/win/34-config-file-path-sml.png)](xamarin-device-manager-images/win/34-config-file-path.png#lightbox)

3. Navegue até esse local e clique duas vezes em **user.config** para abri-lo. 

4. Em **user.config**, localize o elemento **&lt;UserSettings&gt;** e adicione um atributo **AndroidSdkPath** a ele. Defina esse atributo no caminho em que o SDK do Android está instalado no computador e salve o arquivo. Por exemplo, **&lt;UserSettings&gt;** seria parecido com o seguinte se o SDK do Android estivesse instalado em **C:\\Programs\\Android\\SDK**:
        
    ```xml
    <UserSettings SdkLibLastWriteTimeUtcTicks="636409365200000000" AndroidSdkPath="C:\Programs\Android\SDK" />
    ```

Depois de fazer essa alteração em **user.config**, você poderá inicializar o Gerenciador de Dispositivos do Xamarin Android.

### <a name="snapshot-disables-wifi-on-android-oreo"></a>O Snapshot desabilita o WiFi no Android Oreo

Se você configurou um AVD para o Android Oreo com acesso Wi-Fi simulado, reiniciar o AVD após um instantâneo pode fazer com esse acesso seja desabilitado.

Para solucionar esse problema:

1. Selecione o AVD no Gerenciador de Dispositivos do Xamarin.

2. No menu de opções adicionais, clique em **Revelar no Explorer**.

3. Navegue até **snapshots > default_boot**.

4. Exclua o arquivo **snapshot.pb**:

    [![Local do arquivo snapshot.pb](xamarin-device-manager-images/win/36-delete-snapshot-sml.png)](xamarin-device-manager-images/win/36-delete-snapshot.png#lightbox)

5. Reinicie o AVD. 

Depois que essas alterações forem feitas, o AVD será reiniciado em um estado que permita que o Wi-Fi funcione novamente.


# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

### <a name="snapshot-disables-wifi-on-android-oreo"></a>O Snapshot desabilita o WiFi no Android Oreo

Se você configurou um AVD para o Android Oreo com acesso Wi-Fi simulado, reiniciar o AVD após um instantâneo pode fazer com esse acesso seja desabilitado.

Para solucionar esse problema:

1. Selecione o AVD no Gerenciador de Dispositivos do Xamarin.

2. No menu de opções adicionais, clique em **Revelar no Localizador**.

3. Navegue até **snapshots > default_boot**.

4. Exclua o arquivo **snapshot.pb**:

    [![Local do arquivo snapshot.pb](xamarin-device-manager-images/mac/36-delete-snapshot-sml.png)](xamarin-device-manager-images/mac/36-delete-snapshot.png#lightbox)

5. Reinicie o AVD. 

Depois que essas alterações forem feitas, o AVD será reiniciado em um estado que permita que o Wi-Fi funcione novamente.

-----


### <a name="generating-a-bug-report"></a>Gerando um relatório de bugs

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Se encontrar um problema com o Gerenciador de Dispositivos do Xamarin Android que não pode ser resolvido usando as dicas de solução de problemas acima, envie um relatório de bugs clicando com o botão direito do mouse na barra de título e selecionando **Gerar Relatório de Bugs**:

![Local do item de menu para enviar um relatório de bugs](xamarin-device-manager-images/win/35-bug-report.png)


# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Se encontrar um problema com o Gerenciador de Dispositivos do Xamarin Android que não possa ser resolvido usando as dicas de solução de problemas acima, envie um relatório de bugs clicando em **Ajuda > Gerar Relatório de Bugs**:

![Local do item de menu para enviar um relatório de bugs](xamarin-device-manager-images/mac/35-bug-report.png)

-----

 
 
## <a name="summary"></a>Resumo

Este guia apresentou o Gerenciador de Dispositivos do Xamarin Android disponível no Visual Studio para Mac e no Xamarin para Visual Studio. Os recursos essenciais foram explicados, como iniciar e parar o Android Emulator, selecionar um AVD (Dispositivo Virtual Android) para execução, criar novos dispositivos virtuais e como editar um dispositivo virtual. Também foi explicado como editar propriedades de perfil de hardware para personalização adicional.


## <a name="related-links"></a>Links relacionados

- [Alterações nas Ferramentas do SDK do Android](~/android/troubleshooting/sdk-cli-tooling-changes.md)
- [Depuração com o Emulador do SDK do Android](~/android/deploy-test/debugging/android-sdk-emulator/index.md)
- [Notas de Versão das SDK Tools (Google)](https://developer.android.com/studiohttps://developer.xamarin.com/releases/sdk-tools.html)
- [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html)
- [sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html)
