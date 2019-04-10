---
title: Gerenciando dispositivos virtuais com o Android Device Manager
description: Este artigo explica como usar o Android Device Manager para criar e configurar AVDs (Dispositivos Virtuais Android) que emulam dispositivos físicos Android. É possível usar esses dispositivos virtuais para executar e testar aplicativos, sem a necessidade de um dispositivo físico.
zone_pivot_groups: platform
ms.prod: xamarin
ms.assetid: ECB327F3-FF1C-45CC-9FA6-9C11032BD5EF
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.custom: video
ms.date: 01/22/2019
ms.openlocfilehash: 3dad36dc93f5c23e5d9ef1e05a1b9419b7a724b6
ms.sourcegitcommit: c4be32ef914465e808d89767c4d5ee72afe93cc6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2019
ms.locfileid: "58855101"
---
# <a name="managing-virtual-devices-with-the-android-device-manager"></a>Gerenciando dispositivos virtuais com o Android Device Manager

_Este artigo explica como usar o Android Device Manager para criar e configurar AVDs (Dispositivos Virtuais Android) que emulam dispositivos físicos Android. É possível usar esses dispositivos virtuais para executar e testar aplicativos, sem a necessidade de um dispositivo físico._

Depois de ter verificado se a aceleração de hardware está habilitada (conforme descrito em [Aceleração de hardware para o desempenho do emulador](~/android/get-started/installation/android-emulator/hardware-acceleration.md)), a próxima etapa é usar o _Android Device Manager_ (também conhecido como o _Xamarin Android Device Manager_) para criar dispositivos virtuais que você pode usar para testar e depurar o aplicativo.

::: zone pivot="windows"

## <a name="android-device-manager-on-windows"></a>Android Device Manager no Windows

Este artigo explica como usar o Android Device Manager para criar, duplicar, personalizar e iniciar dispositivos virtuais Android.

[![SCaptura de tela do Android Device Manager na guia Dispositivos](device-manager-images/win/01-devices-dialog-sml.png)](device-manager-images/win/01-devices-dialog.png#lightbox)

O Android Device Manager é usado para criar e configurar _AVDs_ (Dispositivos Virtuais Android) que são executados no [Android Emulator](~/android/deploy-test/debugging/debug-on-emulator.md).
Cada AVD é uma configuração de emulador que simula um dispositivo Android físico. Assim, é possível executar e testar aplicativos em uma variedade de configurações que simulam diferentes dispositivos Android físicos.

## <a name="requirements"></a>Requisitos

Para usar o Android Device Manager, os seguintes serão necessários:

- Visual Studio 2019 Community, Professional ou Enterprise.

- OU o Visual Studio 2017 versão 15.8 ou posterior é necessário. As edições Community, Professional e Enterprise do Visual Studio são compatíveis.

- Ferramentas do Visual Studio para Xamarin versão 4.9 ou posterior.

- O SDK do Android precisa ser instalado (confira [Configurando o SDK do Android para Xamarin.Android](~/android/get-started/installation/android-sdk.md)).
  Certifique-se de instalar o SDK do Android em seu local padrão, se ainda não estiver instalado: **C:\\Program Files (x86)\\Android\\android-sdk**.

- Os pacotes a seguir precisam ser instalados (por meio do [Gerenciador do SDK do Android](~/android/get-started/installation/android-sdk.md)): 
    - **Android SDK Tools versão 26.1.1** ou posterior
    - **Ferramentas da Plataforma SDK do Android 27.0.1** ou posterior
    - **Ferramentas de Build do SDK do Android 27.0.3** ou posterior 
    - **Android Emulator 27.2.7** ou posterior. 

  Esses pacotes devem ser exibidos com o status **Instalado**, conforme mostrado na captura de tela a seguir:

  [![IInstalando o Android SDK Tools](device-manager-images/win/02-sdk-tools-sml.png)](device-manager-images/win/02-sdk-tools.png#lightbox)


## <a name="launching-the-device-manager"></a>Inicializar o Gerenciador de Dispositivos

Inicie o Android Device Manager no menu **Ferramentas** clicando em **Ferramentas > Android > Android Device Manager**:

[![LIniciando o Device Manager no menu Ferramentas](device-manager-images/win/03-tools-menu-sml.png)](device-manager-images/win/03-tools-menu.png#lightbox)

Se a caixa de diálogo de erro a seguir for exibida na inicialização, confira a seção [Solução de Problemas](#troubleshooting) para obter instruções de soluções alternativas:

![Caixa de diálogo de erro de instância do SDK do Android](device-manager-images/win/04-sdk-error.png)


## <a name="main-screen"></a>Tela principal

Quando o Gerenciador de Dispositivos Android é iniciado pela primeira vez, uma tela exibe todos os dispositivos virtuais configurados no momento. Para cada dispositivo virtual, o **Nome**, o **Sistema Operacional** (versão do Android), o **Processador**, o tamanho da **Memória** e a **Resolução** de tela são exibidos:

[![LLista de dispositivos instalados e seus parâmetros](device-manager-images/win/05-installed-list-sml.png)](device-manager-images/win/05-installed-list.png#lightbox)

Ao selecionar um dispositivo na lista, o botão **Iniciar** é exibido no lado direito. É possível clicar no botão **Iniciar** para inicializar o emulador com este dispositivo virtual:

[![SBotão Iniciar de uma imagem de dispositivo](device-manager-images/win/06-start-button-sml.png)](device-manager-images/win/06-start-button.png#lightbox)

Depois da inicialização do emulador com o dispositivo virtual selecionado, o botão **Iniciar** se transforma em um botão **Parar**, que pode ser utilizado para interromper o emulador:

[![SBotão Parar do dispositivo em execução](device-manager-images/win/07-stop-button-sml.png)](device-manager-images/win/07-stop-button.png#lightbox)

### <a name="new-device"></a>Novo dispositivo

Para criar um novo dispositivo, clique no botão **Novo** (na área superior direita da tela):

[![NBotão Novo para criar um dispositivo](device-manager-images/win/08-new-button-sml.png)](device-manager-images/win/08-new-button.png#lightbox)

Ao clicar em **Novo**, a tela **Novo Dispositivo** é iniciada:

[![NTela Novo Dispositivo do Device Manager](device-manager-images/win/09-new-device-editor-sml.png)](device-manager-images/win/09-new-device-editor.png#lightbox)

Para configurar um novo dispositivo na tela **Novo Dispositivo**, siga estas etapas:

1. Nomeie o novo dispositivo. No exemplo a seguir, o nome do novo dispositivo é **Pixel_API_27**:

   [![NDando um nome para o novo dispositivo](device-manager-images/win/10-device-name-sml.png)](device-manager-images/win/10-device-name.png#lightbox)

2. Selecione um dispositivo físico a ser emulado clicando no menu suspenso **Dispositivo Base**:

   [![SSelecionando o dispositivo físico a ser emulado](device-manager-images/win/11-device-menu-sml.png)](device-manager-images/win/11-device-menu.png#lightbox)

3. Selecione um tipo de processador para este dispositivo virtual clicando no menu suspenso **Processador**. Selecionar **x86** fornecerá o melhor desempenho porque permite que o emulador tirar proveito da [aceleração de hardware](~/android/get-started/installation/android-emulator/hardware-acceleration.md).
   A opção **x86_64** também utiliza a aceleração de hardware, mas é executado um pouco mais lentamente do que a **x86** (normalmente, a opção **x86_64** é usada para testar aplicativos de 64 bits):

   [![SSelecionando o tipo de processador](device-manager-images/win/12-processor-type-menu-sml.png)](device-manager-images/win/12-processor-type-menu.png#lightbox)

4. Selecione a versão do Android (nível da API) clicando no menu suspenso **SO**. Por exemplo, selecione **Oreo 8.1 – API 27** para criar um dispositivo virtual para o nível da API 27:

   [![SSelecionando a versão do Android](device-manager-images/win/13-android-version-w158-sml.png)](device-manager-images/win/13-android-version-w158.png#lightbox)

   Se você selecionar um nível da API do Android que ainda não foi instalado, o Device Manager exibirá a mensagem **Um novo dispositivo será baixado** na parte inferior da tela &ndash; ele baixará e instalará os arquivos necessários conforme cria o novo dispositivo virtual:

   ![Uma nova imagem do dispositivo será baixada](device-manager-images/win/14-automatic-download-w158.png)

5. Se você quiser incluir APIs do Google Play Services em seu dispositivo virtual, habilite a opção **APIs do Google**. Para incluir o aplicativo do Google Play Store, habilite a opção **Google Play Store**:

   [![SSelecionando Google Play Services e Google Play Store](device-manager-images/win/15-google-play-services-sml.png)](device-manager-images/win/15-google-play-services.png#lightbox)

   Observe que as imagens do Google Play Store estão disponíveis apenas para alguns tipos de dispositivos básicos, como Pixel, Pixel 2, Nexus 5 e Nexus 5X.

6. Edite todas as propriedades que você precisa modificar. Para alterar as propriedades, consulte [Edição de propriedades do Dispositivo Virtual Android](~/android/get-started/installation/android-emulator/device-properties.md).

7. Adicione todas as propriedades adicionais que precisem ser definidas explicitamente. A tela **Novo Dispositivo** mostra apenas as propriedades modificadas maior frequência, mas é possível clicar no menu suspenso **Adicionar Propriedade** (na parte inferior) para adicionar outras propriedades:

   [![AMenu suspenso Adicionar Propriedade](device-manager-images/win/16-add-property-menu-sml.png)](device-manager-images/win/16-add-property-menu.png#lightbox)

    Você também pode definir uma propriedade personalizada selecionando **Personalizado...**  na parte superior da lista de propriedades.

8. Clique no botão **Criar** (canto inferior direito) para criar o novo dispositivo:

   [![CBotão Criar](device-manager-images/win/17-create-button-sml.png)](device-manager-images/win/17-create-button.png#lightbox)

9. A tela **Aceitação da Licença** pode ser exibida. Clique em **Aceitar** se concordar com os termos de licença:

   [![LTela Aceitação da licença](device-manager-images/win/18-license-acceptance-sml.png)](device-manager-images/win/18-license-acceptance.png#lightbox)

10. O Android Device Manager adiciona o novo dispositivo à lista de dispositivos virtuais instalados, mostrando um indicador de progresso **Criando** durante a criação do dispositivo:

    [![CIndicador de progresso de criação](device-manager-images/win/19-creating-the-device-sml.png)](device-manager-images/win/19-creating-the-device.png#lightbox)

11. Quando o processo de criação é concluído, o novo dispositivo é mostrado na lista de dispositivos virtuais instalados com um botão **Iniciar**, pronto para ser inicializado:

    [![NDispositivo recém-criado pronto para ser iniciado](device-manager-images/win/20-created-device-sml.png)](device-manager-images/win/20-created-device.png#lightbox)


### <a name="edit-device"></a>Editar Dispositivo

Para editar um dispositivo virtual existente, selecione-o e clique no botão **Editar** (localizado no canto superior direito da tela):

[![EBotão Editar para modificar um dispositivo](device-manager-images/win/21-edit-button-sml.png)](device-manager-images/win/21-edit-button.png#lightbox)

Clicar em **Editar** inicializa o Editor de Dispositivo do dispositivo virtual selecionado:

[![DTela do Editor de Dispositivos](device-manager-images/win/22-device-editor-sml.png)](device-manager-images/win/22-device-editor.png#lightbox)

A tela **Editor de Dispositivos** lista as propriedades do dispositivo virtual na coluna **Propriedade**, com os valores correspondentes de cada propriedade na coluna **Valor**. Ao selecionar uma propriedade, uma descrição detalhada é exibida à direita.

Para alterar uma propriedade, edite seu valor na coluna **Valor**.
Por exemplo, na captura de tela a seguir, a propriedade `hw.lcd.density` é alterada de **480** para **240**:

[![DExemplo de Edição de Dispositivo](device-manager-images/win/23-device-editing-sml.png)](device-manager-images/win/23-device-editing.png#lightbox)

Depois de fazer as alterações de configuração necessárias, clique no botão **Salvar**.
Para obter mais informações sobre como alterar propriedades do dispositivo virtual, consulte [Edição de propriedades do Dispositivo Virtual Android](~/android/get-started/installation/android-emulator/device-properties.md).


### <a name="additional-options"></a>Opções Adicionais

Opções adicionais para trabalhar com dispositivos estão disponíveis no menu suspenso **Opções Adicionais** (&hellip;) no canto superior direito:

[![LLocal do menu de opções adicionais](device-manager-images/win/24-overflow-menu-sml.png)](device-manager-images/win/24-overflow-menu.png#lightbox)

O menu de opções adicionais contém os seguintes itens:

- **Duplicar e Editar** &ndash; Duplica o dispositivo selecionado no momento e o abre na tela **Novo Dispositivo** com um nome único e diferente. Por exemplo, selecionar **Pixel_API_27** e clicar em **Duplicar e Editar** acrescenta um contador ao nome:

  [![DTela Duplicar e Editar](device-manager-images/win/25-dupe-and-edit-sml.png)](device-manager-images/win/25-dupe-and-edit.png#lightbox)

- **Revelar no Explorer** &ndash; Abre uma janela do Windows Explorer na pasta que contém os arquivos do dispositivo virtual. Por exemplo, selecionar **Pixel_API_27** e clicar em **Revelar no Explorer** abre uma janela como o exemplo a seguir:

  [![RResultados de clicar em Revelar no Explorer](device-manager-images/win/26-reveal-in-explorer-sml.png)](device-manager-images/win/26-reveal-in-explorer.png#lightbox)

- **Redefinição de Fábrica** &ndash; Redefine o dispositivo selecionado com suas configurações padrão, apagando todas as alterações feitas pelo usuário no estado interno do dispositivo enquanto ele estava em execução (isso também apaga o instantâneo [Inicialização Rápida](~/android/deploy-test/debugging/debug-on-emulator.md#quick-boot) atual, se houver). Essa alteração não altera as modificações feitas no dispositivo virtual durante a criação e a edição. Uma caixa de diálogo será exibida com o lembrete de que essa redefinição não pode ser desfeita. Clique em **Redefinição de Fábrica** para confirmar a redefinição:

  ![Caixa de diálogo Redefinição de Fábrica](device-manager-images/win/27-factory-reset.png)

- **Excluir** &ndash; Exclui permanentemente o dispositivo selecionado. Uma caixa de diálogo será exibida com o lembrete de que a ação de excluir um dispositivo não pode ser desfeita. Clique em **Excluir** se tiver certeza de que deseja excluir o dispositivo.

  ![Caixa de diálogo Excluir](device-manager-images/win/28-delete-device-w158.png)


::: zone-end
::: zone pivot="macos"

## <a name="android-device-manager-on-macos"></a>Android Device Manager no macOS

Este artigo explica como usar o Android Device Manager para criar, duplicar, personalizar e iniciar dispositivos virtuais Android.

[![SCaptura de tela do Android Device Manager na guia Dispositivos](device-manager-images/mac/01-devices-dialog-sml.png)](device-manager-images/mac/01-devices-dialog.png#lightbox)

> [!NOTE]
> Este guia se aplica apenas ao Visual Studio para Mac.
O Xamarin Studio não é compatível com o Android Device Manager.

O Android Device Manager é usado para criar e configurar *AVDs* (Dispositivos Virtuais Android) que são executados no [Android Emulator](~/android/deploy-test/debugging/debug-on-emulator.md).
Cada AVD é uma configuração de emulador que simula um dispositivo Android físico. Assim, é possível executar e testar aplicativos em uma variedade de configurações que simulam diferentes dispositivos Android físicos.

## <a name="requirements"></a>Requisitos

Para usar o Android Device Manager, os seguintes serão necessários:

- Visual Studio para Mac 7.6 ou posterior.

- O SDK do Android precisa ser instalado (confira [Configurando o SDK do Android para Xamarin.Android](~/android/get-started/installation/android-sdk.md)).

- Os pacotes a seguir precisam ser instalados (por meio do [Gerenciador do SDK do Android](~/android/get-started/installation/android-sdk.md)): 
    -  **SDK Tools versão 26.1.1** ou posterior
    -  **Ferramentas da Plataforma SDK do Android 28.0.1** ou posterior 
    -  **Ferramentas de Build do SDK do Android 26.0.3** ou posterior

  Esses pacotes devem ser exibidos com o status **Instalado**, conforme mostrado na captura de tela a seguir:

  [![IInstalando o Android SDK Tools](device-manager-images/mac/02-sdk-tools-sml.png)](device-manager-images/mac/02-sdk-tools.png#lightbox)


## <a name="launching-the-device-manager"></a>Inicializar o Gerenciador de Dispositivos

Inicie o Android Device Manager, clicando em **Ferramentas > Gerenciador de Dispositivos**:

[![LIniciando o Device Manager no menu Ferramentas](device-manager-images/mac/03-tools-menu-sml.png)](device-manager-images/mac/03-tools-menu.png#lightbox)

Se a caixa de diálogo de erro a seguir for exibida na inicialização, confira a seção [Solução de Problemas](#troubleshooting) para obter instruções de soluções alternativas:

![Caixa de diálogo de erro de instância do SDK do Android](device-manager-images/mac/04-sdk-instance-error.png)


## <a name="main-screen"></a>Tela principal

Quando o Gerenciador de Dispositivos Android é iniciado pela primeira vez, uma tela exibe todos os dispositivos virtuais configurados no momento. Para cada dispositivo virtual, o **Nome**, o **Sistema Operacional** (versão do Android), o **Processador**, o tamanho da **Memória** e a **Resolução** de tela são exibidos:

[![LLista de dispositivos instalados e seus parâmetros](device-manager-images/mac/05-devices-list-sml.png)](device-manager-images/mac/05-devices-list.png#lightbox)

Ao selecionar um dispositivo na lista, o botão **Executar** é exibido no lado direito. É possível clicar no botão **Executar** para inicializar o emulador com este dispositivo virtual:

[![PBotão Executar de uma imagem de dispositivo](device-manager-images/mac/06-start-button-sml.png)](device-manager-images/mac/06-start-button.png#lightbox)

Depois da inicialização do emulador com o dispositivo virtual selecionado, o botão **Reproduzir** se transforma em um botão **Parar**, que pode ser utilizado para interromper o emulador:

[![SBotão Parar do dispositivo em execução](device-manager-images/mac/07-stop-button-sml.png)](device-manager-images/mac/07-stop-button.png#lightbox)

Quando parar o emulador, você poderá ver um prompt perguntando se deseja salvar o estado atual para a próxima Inicialização Rápida:

![Caixa de diálogo Salvar o estado atual para Inicialização Rápida](device-manager-images/mac/08-save-for-quick-boot-m76.png)

Salvar o estado atual fará com que o emulador seja inicializado com mais rapidez quando este dispositivo virtual for iniciado novamente. Para obter mais informações sobre a Inicialização Rápida, confira [Inicialização Rápida](~/android/deploy-test/debugging/debug-on-emulator.md#quick-boot).

### <a name="new-device"></a>Novo dispositivo

Para criar um novo dispositivo, clique no botão **Novo Dispositivo** (na área superior esquerda da tela):

[![NBotão Novo para criar um dispositivo](device-manager-images/mac/09-new-button-sml.png)](device-manager-images/mac/09-new-button.png#lightbox)

Clicar em **Novo** inicia a tela **Novo Dispositivo**:

[![NTela Novo Dispositivo do Device Manager](device-manager-images/mac/10-new-device-editor-sml.png)](device-manager-images/mac/10-new-device-editor.png#lightbox)

Para configurar um novo dispositivo na tela **Novo Dispositivo**, siga estas etapas:

1. Nomeie o novo dispositivo. No exemplo a seguir, o nome do novo dispositivo é **Pixel_API_27**:

   [![NDando um nome para o novo dispositivo](device-manager-images/mac/11-device-name-m76-sml.png)](device-manager-images/mac/11-device-name-m76.png#lightbox)

2. Selecione um dispositivo físico a ser emulado clicando no menu suspenso **Dispositivo Base**:

   [![SSelecionando o dispositivo físico a ser emulado](device-manager-images/mac/12-device-menu-m76-sml.png)](device-manager-images/mac/12-device-menu-m76.png#lightbox)

3. Selecione um tipo de processador para este dispositivo virtual clicando no menu suspenso **Processador**. Selecionar **x86** fornecerá o melhor desempenho porque permite que o emulador tirar proveito da [aceleração de hardware](~/android/get-started/installation/android-emulator/hardware-acceleration.md).
   A opção **x86_64** também utiliza a aceleração de hardware, mas é executado um pouco mais lentamente do que a **x86** (normalmente, a opção **x86_64** é usada para testar aplicativos de 64 bits):

   [![SSelecionando o tipo de processador](device-manager-images/mac/13-processor-type-menu-m76-sml.png)](device-manager-images/mac/13-processor-type-menu-m76.png#lightbox)

4. Selecione a versão do Android (nível da API) clicando no menu suspenso **SO**. Por exemplo, selecione **Oreo 8.1 – API 27** para criar um dispositivo virtual para o nível da API 27:

   [![SSelecionando a versão do Android](device-manager-images/mac/14-android-screenshot-m76-sml.png)](device-manager-images/mac/14-android-screenshot-m76.png#lightbox)

   Se você selecionar um nível da API do Android que ainda não foi instalado, o Device Manager exibirá a mensagem **Um novo dispositivo será baixado** na parte inferior da tela &ndash; ele baixará e instalará os arquivos necessários conforme cria o novo dispositivo virtual:

   ![Uma nova imagem do dispositivo será baixada](device-manager-images/mac/15-automatic-download-m76.png)

5. Se você quiser incluir APIs do Google Play Services em seu dispositivo virtual, habilite a opção **APIs do Google**. Para incluir o aplicativo do Google Play Store, habilite a opção **Google Play Store**:

   [![SSelecionando Google Play Services e Google Play Store](device-manager-images/mac/16-google-play-services-m76-sml.png)](device-manager-images/mac/16-google-play-services-m76.png#lightbox)

   Observe que as imagens do Google Play Store estão disponíveis apenas para alguns tipos de dispositivos básicos, como Pixel, Pixel 2, Nexus 5 e Nexus 5X.

6. Edite todas as propriedades que você precisa modificar. Para alterar as propriedades, consulte [Edição de propriedades do Dispositivo Virtual Android](~/android/get-started/installation/android-emulator/device-properties.md).

7. Adicione todas as propriedades adicionais que precisem ser definidas explicitamente. A tela **Novo Dispositivo** mostra apenas as propriedades modificadas maior frequência, mas é possível clicar no menu suspenso **Adicionar Propriedade** (na parte inferior) para adicionar outras propriedades:

   [![AMenu suspenso Adicionar Propriedade](device-manager-images/mac/17-add-property-menu-m76-sml.png)](device-manager-images/mac/17-add-property-menu-m76.png#lightbox)

   Você também pode definir uma propriedade personalizada clicando em **Personalizado...** na parte superior da lista de propriedades.

8. Clique no botão **Criar** (canto inferior direito) para criar o novo dispositivo:

   ![Botão Criar](device-manager-images/mac/18-create-button-m76.png)

9. O Android Device Manager adiciona o novo dispositivo à lista de dispositivos virtuais instalados, mostrando um indicador de progresso **Criando** durante a criação do dispositivo:

   [![CIndicador de progresso de criação](device-manager-images/mac/19-creating-the-device-m76-sml.png)](device-manager-images/mac/19-creating-the-device-m76.png#lightbox)

10. Quando o processo de criação é concluído, o novo dispositivo é mostrado na lista de dispositivos virtuais instalados com um botão **Iniciar**, pronto para ser inicializado:

    [![NDispositivo recém-criado pronto para ser iniciado](device-manager-images/mac/20-created-device-m76-sml.png)](device-manager-images/mac/20-created-device-m76.png#lightbox)


### <a name="edit-device"></a>Editar Dispositivo

Para editar um dispositivo virtual existente, clique no menu suspenso **Opções Adicionais** (ícone de engrenagem) e selecione **Editar**:

[![ESeleção do menu Editar para modificar um novo dispositivo](device-manager-images/mac/21-edit-button-m76-sml.png)](device-manager-images/mac/21-edit-button-m76.png#lightbox)

Clicar em **Editar** inicializa o Editor de Dispositivo do dispositivo virtual selecionado:

[![DTela do Editor de Dispositivos](device-manager-images/mac/22-device-editor-sml.png)](device-manager-images/mac/22-device-editor.png#lightbox)

A tela **Editor de Dispositivos** lista as propriedades do dispositivo virtual na coluna **Propriedade**, com os valores correspondentes de cada propriedade na coluna **Valor**. Ao selecionar uma propriedade, uma descrição detalhada é exibida à direita.

Para alterar uma propriedade, edite seu valor na coluna **Valor**.
Por exemplo, na captura de tela a seguir, a propriedade `hw.lcd.density` é alterada de **480** para **240**:

[![DExemplo de Edição de Dispositivo](device-manager-images/mac/23-device-editing-sml.png)](device-manager-images/mac/23-device-editing.png#lightbox)

Depois de fazer as alterações de configuração necessárias, clique no botão **Salvar**.
Para obter mais informações sobre como alterar propriedades do dispositivo virtual, consulte [Edição de propriedades do Dispositivo Virtual Android](~/android/get-started/installation/android-emulator/device-properties.md).


### <a name="additional-options"></a>Opções Adicionais

As opções adicionais para trabalhar com os dispositivos estão disponíveis no menu suspenso localizado à esquerda do botão **Reproduzir**:

[![LLocal do menu de opções adicionais](device-manager-images/mac/24-overflow-menu-sml.png)](device-manager-images/mac/24-overflow-menu.png#lightbox)

O menu de opções adicionais contém os seguintes itens:

- **Editar** &ndash; Abre o dispositivo selecionado no momento no editor de dispositivos, conforme descrito anteriormente.

- **Duplicar e Editar** &ndash; Duplica o dispositivo selecionado no momento e o abre na tela **Novo Dispositivo** com um nome único e diferente. Por exemplo, selecionar **Pixel 2 API 28** e clicar em **Duplicar e Editar** acrescenta um contador ao nome:

  [![DTela Duplicar e Editar](device-manager-images/mac/25-dupe-and-edit-sml.png)](device-manager-images/mac/25-dupe-and-edit.png#lightbox)

- **Revelar no Finder** &ndash; Abre uma janela do Finder do macOS na pasta que contém os arquivos do dispositivo virtual. Por exemplo, selecionar **Pixel 2 API 28** e clicar em **Revelar no Finder** abre uma janela como a do exemplo a seguir:

  [![RResultados de clicar em Revelar no Localizador](device-manager-images/mac/26-reveal-in-finder-sml.png)](device-manager-images/mac/26-reveal-in-finder.png#lightbox)

- **Redefinição de Fábrica** &ndash; Redefine o dispositivo selecionado com suas configurações padrão, apagando todas as alterações feitas pelo usuário no estado interno do dispositivo enquanto ele estava em execução (isso também apaga o instantâneo [Inicialização Rápida](~/android/deploy-test/debugging/debug-on-emulator.md#quick-boot) atual, se houver). Essa alteração não altera as modificações feitas no dispositivo virtual durante a criação e a edição. Uma caixa de diálogo será exibida com o lembrete de que essa redefinição não pode ser desfeita. Clique em **Redefinição de Fábrica** para confirmar a redefinição.

  ![Caixa de diálogo Redefinição de Fábrica](device-manager-images/mac/27-factory-reset-m76.png)

- **Excluir** &ndash; Exclui permanentemente o dispositivo selecionado. Uma caixa de diálogo será exibida com o lembrete de que a ação de excluir um dispositivo não pode ser desfeita. Clique em **Excluir** se tiver certeza de que deseja excluir o dispositivo.

  ![Caixa de diálogo Excluir](device-manager-images/mac/28-delete-device-m76.png)

-----


<a name="troubleshooting" />

## <a name="troubleshooting"></a>Solução de problemas

As seções a seguir explicam como diagnosticar e solucionar problemas que podem ocorrer durante o uso do Android Device Manager para configurar dispositivos virtuais.

# [<a name="visual-studio"></a>Visual Studio](#tab/windows)

### <a name="android-sdk-in-non-standard-location"></a>SDK do Android em local não padrão

Normalmente, o SDK do Android está instalado neste local:

**C:\\Arquivos de Programas (x86)\\Android\\android-sdk**

Se o SDK não estiver instalado nesse local, você poderá ver este erro ao iniciar o Android Device Manager:

![Erro de instância do SDK do Android](device-manager-images/win/29-sdk-error.png)

Para contornar esse problema, execute as etapas a seguir:

1. Na área de trabalho do Windows, navegue até **C:\\Users\\*nomedeusuário*\\AppData\\Roaming\\XamarinDeviceManager**:

   ![Local do arquivo de log do Android Device Manager](device-manager-images/win/30-log-files.png)

2. Clique duas vezes para abrir um dos arquivos de log e localize o **Caminho do arquivo de configuração**. Por exemplo:

   [![CCaminho do arquivo de configuração no arquivo de log](device-manager-images/win/31-config-file-path-sml.png)](device-manager-images/win/31-config-file-path.png#lightbox)

3. Navegue até esse local e clique duas vezes em **user.config** para abri-lo.

4. Em **user.config**, localize o elemento `<UserSettings>` e adicione um atributo **AndroidSdkPath** a ele. Defina esse atributo no caminho em que o SDK do Android está instalado no computador e salve o arquivo. Por exemplo, `<UserSettings>` seria parecido com o seguinte se o SDK do Android estivesse instalado em **C:\\Programas\\Android\\SDK**:

   ```xml
   <UserSettings SdkLibLastWriteTimeUtcTicks="636409365200000000" AndroidSdkPath="C:ProgramsAndroidSDK" />
   ```

Depois de fazer essa alteração em **user.config**, você poderá iniciar o Android Device Manager.


### <a name="wrong-version-of-android-sdk-tools"></a>Versão incorreta do Android SDK Tools

Se a versão 26.1.1 ou posterior das Android SDK Tools não estiverem instaladas, este erro será exibido na inicialização:

![Caixa de diálogo de erro de instância do SDK do Android](device-manager-images/win/32-sdk-instance-error.png)

Se essa caixa de diálogo de erro for exibida, clique em **Abrir Gerenciador de SDK** para abrir o Gerenciador do SDK do Android. No Gerenciador do SDK do Android, clique na guia **Ferramentas** e instale os seguintes pacotes:

- **Android SDK Tools 26.1.1** ou posterior
- **Ferramentas da Plataforma SDK do Android 27.0.1** ou posterior
- **Ferramentas de Build do SDK do Android 27.0.3** ou posterior


### <a name="snapshot-disables-wifi-on-android-oreo"></a>O Snapshot desabilita o WiFi no Android Oreo

Se você configurou um AVD para o Android Oreo com acesso Wi-Fi simulado, reiniciar o AVD após um instantâneo pode fazer com esse acesso seja desabilitado.

Para solucionar esse problema:

1. Selecione o AVD no Android Device Manager.

2. No menu de opções adicionais, clique em **Revelar no Explorer**.

3. Navegue até **snapshots > default_boot**.

4. Exclua o arquivo **snapshot.pb**:

   ![Local do arquivo snapshot.pb](device-manager-images/win/33-delete-snapshot.png)

5. Reinicie o AVD.

Depois que essas alterações forem feitas, o AVD será reiniciado em um estado que permita que o Wi-Fi funcione novamente.


# [<a name="visual-studio-for-mac"></a>Visual Studio para Mac](#tab/macos)

### <a name="wrong-version-of-android-sdk-tools"></a>Versão incorreta do Android SDK Tools

Se a versão 26.1.1 ou posterior das Android SDK Tools não estiverem instaladas, este erro será exibido na inicialização:

![Caixa de diálogo de erro de instância do SDK do Android](device-manager-images/mac/29-sdk-instance-error.png)

Se essa caixa de diálogo de erro for exibida, clique em **OK** para abrir o Gerenciador do SDK do Android. No Gerenciador do SDK do Android, clique na guia **Ferramentas** e instale os seguintes pacotes:

- **Android SDK Tools 26.1.1** ou posterior
- **Ferramentas da Plataforma SDK do Android 28.0.1** ou posterior
- **Ferramentas de Build do SDK do Android 26.0.3** ou posterior

### <a name="snapshot-disables-wifi-on-android-oreo"></a>O Snapshot desabilita o WiFi no Android Oreo

Se você configurou um AVD para o Android Oreo com acesso Wi-Fi simulado, reiniciar o AVD após um instantâneo pode fazer com esse acesso seja desabilitado.

Para solucionar esse problema:

1. Selecione o AVD no Android Device Manager.

2. No menu de opções adicionais, clique em **Revelar no Localizador**.

3. Navegue até **snapshots > default_boot**.

4. Exclua o arquivo **snapshot.pb**:

   [![LLocal do arquivo snapshot.pb](device-manager-images/mac/30-delete-snapshot-sml.png)](device-manager-images/mac/30-delete-snapshot.png#lightbox)

5. Reinicie o AVD.

Depois que essas alterações forem feitas, o AVD será reiniciado em um estado que permita que o Wi-Fi funcione novamente.

-----

### <a name="generating-a-bug-report"></a>Gerando um relatório de bugs

# [<a name="visual-studio"></a>Visual Studio](#tab/windows)

Se encontrar um problema com o Android Device Manager que não pode ser resolvido usando as dicas de solução de problemas acima, envie um relatório de bugs clicando com o botão direito do mouse na barra de título e selecionando **Gerar Relatório de Bugs**:

[![LLocal do item de menu para enviar um relatório de bugs](device-manager-images/win/34-bug-report-sml.png)](device-manager-images/win/34-bug-report.png#lightbox)


# [<a name="visual-studio-for-mac"></a>Visual Studio para Mac](#tab/macos)

Se encontrar um problema com o Android Device Manager que não possa ser resolvido usando as dicas de solução de problemas acima, envie um relatório de bugs clicando em **Ajuda > Relatar um Problema**:

[![LLocal do item de menu para enviar um relatório de bugs](device-manager-images/mac/31-bug-report-sml.png)](device-manager-images/mac/31-bug-report.png#lightbox)

::: zone-end

## <a name="summary"></a>Resumo

Este guia apresentou o Android Device Manager disponível nas Ferramentas do Visual Studio para Xamarin e no Visual Studio para Mac. Os recursos essenciais foram explicados, como iniciar e parar o Android Emulator, selecionar um AVD (Dispositivo Virtual Android) para execução, criar novos dispositivos virtuais e como editar um dispositivo virtual. Foi explicado como editar propriedades de hardware de perfil para personalização adicional e fornecidas dicas de solução de problemas comuns.


## <a name="related-links"></a>Links relacionados

- [Alterações nas Ferramentas do SDK do Android](~/android/troubleshooting/sdk-cli-tooling-changes.md)
- [Depurando no Android Emulator](~/android/deploy-test/debugging/debug-on-emulator.md)
- [Notas de Versão das SDK Tools (Google)](https://developer.android.com/studio/releases/sdk-tools)
- [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html)
- [sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html)

## <a name="related-video"></a>Vídeo relacionados

> [!Video https://channel9.msdn.com/Shows/XamarinShow/How-to-Create-and-Manage-Your-Own-Android-Emulators/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
