---
title: Google Emulator Manager
description: Como criar e gerenciar dispositivos virtuais Android usando o Google Emulator Manager
ms.prod: xamarin
ms.assetid: 0C0BBEC0-C84A-4558-B905-4EF81FCD62F9
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: a399aa1c314f1e93377a7831b430e563d9fd1b13
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="google-emulator-manager"></a>Google Emulator Manager

Depois de confirmar se a aceleração de hardware está habilitada (conforme descrito em [Aceleração de Hardware do Android Emulator](~/android/get-started/installation/android-emulator/hardware-acceleration.md)), a etapa seguinte é criar dispositivos virtuais para testar e depurar o aplicativo. É possível usar o Google Emulator Manager herdado (também conhecido como *AVD*) para criar dispositivos virtuais para uso com o Emulador de SDK do Android.

> [!NOTE]
> Se o seu destino é o Android 8.0 Oreo, é necessário usar o [Gerenciador de Dispositivos Xamarin Android](~/android/get-started/installation/android-emulator/xamarin-device-manager.md) para criar e configurar dispositivos virtuais.


## <a name="installing-system-images"></a>Instalando imagens do sistema

Dependendo dos níveis de API do Android que deseja ter como destino, você precisa baixar e instalar imagens do sistema específicas ao nível da API que são usadas pelo emulador do SDK do Android. Para cada nível de API do Android, há um conjunto de imagens de sistema **x86** que você precisará baixar e instalar para criar dispositivos virtuais.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Para instalar as imagens de sistema necessárias, inicie o Gerenciador do SDK do Android (**Ferramentas > Android > Gerenciador do SDK do Android**) e role até os níveis de API a que deseja dar suporte. Para cada nível de API, habilite a marca de seleção ao lado das seguintes imagens do sistema:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Para instalar as imagens de sistema necessárias, inicie o Gerenciador do SDK do Android (**Ferramentas > Gerenciador do SDK**) e role até os níveis de API a que deseja dar suporte. Para cada nível de API, habilite a marca de seleção ao lado das seguintes imagens do sistema:

-----

-   **Imagem de sistema do Intel x86 Atom**
-   **Imagem de sistema do Intel x86 Atom das APIs do Google**

A última imagem de sistema adiciona APIs do Google (por exemplo, APIs do Google Maps) ao dispositivo virtual. 

Na captura de tela a seguir, imagens do **Intel x86 Atom** serão instaladas para que dispositivos virtuais que executam o Android 6.0 possam ser criados:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Selecionando imagens de sistema do Android 6.0 x86 para o Emulador do Android](google-emulator-manager-images/win/03-select-x86-images-sml.png)](google-emulator-manager-images/win/03-select-x86-images.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Selecionando imagens de sistema do Android 6.0 x86 para o Emulador do Android](google-emulator-manager-images/mac/02-select-x86-images-sml.png)](google-emulator-manager-images/mac/02-select-x86-images.png#lightbox)

-----

Se estiver desenvolvendo aplicativos de 64 bits, instale as seguintes imagens de sistema:

-   **Imagem de sistema do Intel x86 Atom_64**
-   **Imagem de sistema do Intel x86 Atom_64 das APIs do Google**

Você pode usar essas imagens de sistema de 64 bits para executar aplicativos de 32 bits. No entanto, a **Imagem de sistema do Intel x86 Atom** de 32 bits é executada um pouco mais rapidamente no emulador do SDK do Android.

Se estiver desenvolvendo aplicativos para o Android Wear, instale as seguintes imagens de sistema:

-   **Imagem de sistema do Intel x86 Atom do Android Wear**
-   **Imagem de sistema do Intel x86 Atom das APIs do Google**

Após essas imagens de sistema serem instaladas, você pode criar dispositivos virtuais Android baseados em **x86** selecionando o nível apropriado da API e as opções de CPU/ABI durante a configuração do dispositivo virtual (isso é descrito a seguir).


## <a name="configuring-virtual-devices"></a>Configurando Dispositivos Virtuais

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Dispositivos virtuais são configurados por meio do **Gerenciador do Emulador do Android** (também chamado de _Gerenciador de Dispositivo Virtual Android_ ou _Gerenciador de AVD_). Para iniciar o Gerenciador do Emulador do Android no Visual Studio, clique no ícone do **Gerenciador do Emulador do Android** na barra de ferramentas:

[![Local do ícone do AVD](google-emulator-manager-images/win/04-avd-icon-sml.png)](google-emulator-manager-images/win/04-avd-icon.png#lightbox)

Também é possível iniciar o Gerenciador do Emulador do Android na barra de menus selecionando **Ferramentas > Android > Gerenciador de Emulador do Android**:

[![Local do item de menu do Gerenciador do Emulador do Android](google-emulator-manager-images/win/05-avd-manager-menu-item-sml.png)](google-emulator-manager-images/win/05-avd-manager-menu-item.png#lightbox)

A caixa de diálogo **Gerenciador de Dispositivos Virtuais Android (AVD)** exibe a lista dos Dispositivos Virtuais Android existentes:

[![Gerenciador de Dispositivo Virtual do Android](google-emulator-manager-images/win/06-virtual-device-manager-sml.png)](google-emulator-manager-images/win/06-virtual-device-manager.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Dispositivos virtuais são configurados por meio do **Gerenciador do Emulador do Android** (também chamado de _Gerenciador de Dispositivo Virtual Android_ ou _Gerenciador de AVD_). 

É possível iniciar o Gerenciador do emulador do Android na barra de menus selecionando **Ferramentas > Gerenciador de emulador do Google**:

[![Local do item de menu do Gerenciador do Emulador do Android](google-emulator-manager-images/mac/03-avd-manager-menu-item-sml.png)](google-emulator-manager-images/mac/03-avd-manager-menu-item.png#lightbox)

A caixa de diálogo **Gerenciador de Dispositivos Virtuais Android (AVD)** exibe a lista dos Dispositivos Virtuais Android existentes:

[![Gerenciador de Dispositivo Virtual do Android](google-emulator-manager-images/mac/05-virtual-device-manager-sml.png)](google-emulator-manager-images/mac/05-virtual-device-manager.png#lightbox)

-----

Você pode criar novas imagens de dispositivo virtual com diferentes características de dispositivo e níveis de API &ndash; a próxima seção explica como criar dispositivos virtuais e definições de dispositivo personalizadas.


### <a name="creating-a-custom-device-definition"></a>Criando uma definição de dispositivo personalizada

Para criar uma definição de dispositivo personalizada, clique em **Criar...** no **Gerenciador de dispositivo virtual do Android (AVD)**. Isso abre a caixa de diálogo **Criar novo AVD (Dispositivo Virtual Android)**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Definição de dispositivo personalizada baseada em Nexus 6](google-emulator-manager-images/win/07-custom-device-sml.png)](google-emulator-manager-images/win/07-custom-device.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Definição de dispositivo personalizada baseada em Nexus 6](google-emulator-manager-images/mac/06-custom-device-sml.png)](google-emulator-manager-images/mac/06-custom-device.png#lightbox)

-----

Nesta caixa de diálogo, configure as seguintes opções:

-   **Nome do AVD** &ndash; o nome exclusivo para sua definição de dispositivo. Na captura de tela de exemplo acima, o nome está definido como **MyNexus**. Observe que o nome do AVD não pode conter espaços &ndash; o botão **OK** será desabilitado se você tentar usar espaços no nome do AVD.

-   **Dispositivo** &ndash; selecione o perfil de hardware que deseja emular (por exemplo, **Nexus 5** ou **Nexus 6**).

-   **Destino** &ndash; selecione o nível da API do Android para o dispositivo virtual. Essa configuração deve ser maior ou igual à Versão Mínima do Android do seu aplicativo.

-   **CPU/ABI** &ndash; selecione **Google APIs Intel Atom (x86)** para que as APIs do Google fiquem disponíveis em sua definição de dispositivo.

-   **Capa** &ndash; selecione a aparência do dispositivo virtual. Na captura de tela de exemplo acima, a capa **HVGA** está selecionada (captura de tela do emulador no final deste artigo é um exemplo da capa **HVGA**).

-   **Opções de Memória** &ndash; normalmente, a configuração de RAM padrão é muito alta e, no Windows, gera o aviso: **emular RAM maior que 768M pode causar falhas**. Para a maioria dos usuários, recomendamos configurar a RAM como 768 MB (conforme mostrado na captura de tela acima). Valores de RAM mais altos podem reduzir a velocidade o emulador.

-   **Usar GPU do Host** &ndash; esta opção faz com que o emulador use a GPU (unidade de processamento gráfico) do computador host para executar operações de gráfico. É recomendável habilitar esta opção para melhorar ainda mais o desempenho do emulador. Para obter mais informações sobre a seção **Opções de emulação**, consulte [What are Snapshot and Use Host GPU emulation options used for?](https://android.stackexchange.com/questions/51739/what-is-snapshot-and-use-host-gpu-emulation-options-for) (Para que as opções de emulação Instantâneo e Usar GPU do Host são usadas?)


As opções restantes podem ser deixadas com as configurações padrão. Quando estiver pronto, clique em **OK** para criar o novo dispositivo virtual. Os resultados da nova configuração do dispositivo virtual são detalhados na próxima caixa de diálogo:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Caixa de diálogo de resultados depois de criar um novo AVD](google-emulator-manager-images/win/08-create-results.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

![Caixa de diálogo de resultados depois de criar um novo AVD](google-emulator-manager-images/mac/07-create-results.png)

-----

Para obter uma explicação detalhada das propriedades de configuração, confira as [Propriedades de perfil de hardware](https://developer.android.com/studio/run/managing-avds.html#hpproperties).
Após você clicar em **OK**, a nova configuração do dispositivo será exibida na lista de dispositivos virtuais Android existentes. Na captura de tela a seguir, **MyNexus** foi adicionado à lista:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![MyNexus adicionado à lista de dispositivos](google-emulator-manager-images/win/09-added-to-list-sml.png)](google-emulator-manager-images/win/09-added-to-list.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![MyNexus adicionado à lista de dispositivos](google-emulator-manager-images/mac/08-added-to-list-sml.png)](google-emulator-manager-images/mac/08-added-to-list.png#lightbox)

-----

O novo dispositivo virtual personalizado também é adicionado ao menu suspenso do dispositivo:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![MyNexus adicionado ao menu suspenso do dispositivo](google-emulator-manager-images/win/10-available-custom-device-sml.png)](google-emulator-manager-images/win/10-available-custom-device.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![MyNexus adicionado ao menu suspenso do dispositivo](google-emulator-manager-images/mac/09-available-custom-device-sml.png)](google-emulator-manager-images/mac/09-available-custom-device.png#lightbox)

-----



### <a name="cloning-a-device-definition"></a>Clonando uma Definição de Dispositivo

É possível selecionar uma definição de dispositivo existente e *cloná-la* para criar uma nova definição de dispositivo personalizada. Essa é uma boa estratégia para quando houver uma definição de dispositivo existente que precisa apenas de alguns pequenos ajustes para atender às suas necessidades. A guia **Definições de dispositivo** no **Gerenciador de dispositivos virtuais Android (AVD)** lista todas as definições de dispositivo disponíveis:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Lista de definições de dispositivo disponíveis](google-emulator-manager-images/win/11-device-definitions-sml.png)](google-emulator-manager-images/win/11-device-definitions.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Lista de definições de dispositivo disponíveis](google-emulator-manager-images/mac/10-device-definitions-sml.png)](google-emulator-manager-images/mac/10-device-definitions.png#lightbox)

-----

Os dispositivos pré-configurados nessa lista não podem ser modificados &ndash; somente dispositivos virtuais criados pelo usuário podem ser editados. É possível derivar uma nova definição de dispositivo de uma definição de dispositivo pré-configurada selecionando essa definição e clicando em **Clonar**. Por exemplo, ao selecionar a definição **Nexus 5** e clicar em **Clonar**, a seguinte caixa de diálogo será exibida:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Caixa de diálogo Clonar dispositivo](google-emulator-manager-images/win/12-clone-device-sml.png)](google-emulator-manager-images/win/12-clone-device.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Caixa de diálogo Clonar dispositivo](google-emulator-manager-images/mac/11-clone-device-sml.png)](google-emulator-manager-images/mac/11-clone-device.png#lightbox)

-----

Na próxima captura de tela, o nome é alterado para **Nexus 5 Personalizado** e os parâmetros do dispositivo são modificados para criar uma nova definição de dispositivo personalizada:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![AVD Nexus 5 Personalizado](google-emulator-manager-images/win/13-custom-nexus.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![AVD Nexus 5 Personalizado](google-emulator-manager-images/mac/12-custom-nexus-sml.png)](google-emulator-manager-images/mac/12-custom-nexus.png#lightbox)

-----

Ao clicar em **Clonar dispositivo**, uma nova definição de dispositivo é criada, que agora aparece na lista **Definições de dispositivo**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Nexus 5 Personalizado aparece como uma nova definição de dispositivo do usuário](google-emulator-manager-images/win/14-new-definition-sml.png)](google-emulator-manager-images/win/14-new-definition.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Nexus 5 Personalizado aparece como uma nova definição de dispositivo do usuário](google-emulator-manager-images/mac/13-new-definition-sml.png)](google-emulator-manager-images/mac/13-new-definition.png#lightbox)

-----

Observe que cada definição de dispositivo criada pelo usuário é exibida com um ícone verde, conforme mostrado acima. Essa nova definição de dispositivo pode ser usada para criar um novo AVD selecionando a definição e clicando em **Criar AVD**. Isso exibe a caixa de diálogo **Gerenciador de Dispositivo Virtual Android (AVD)**. No exemplo a seguir, o nome **AVD\_para\_Nexus\_5\_Personalizado** foi gerado automaticamente para o novo dispositivo virtual:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Criar um AVD usando a definição de dispositivo do usuário Nexus 5 Personalizado](google-emulator-manager-images/win/15-create-avd-sml.png)](google-emulator-manager-images/win/15-create-avd.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Criar um AVD usando a definição de dispositivo do usuário Nexus 5 Personalizado](google-emulator-manager-images/mac/14-create-avd-sml.png)](google-emulator-manager-images/mac/14-create-avd.png#lightbox)

-----

Após você clicar em **OK**, a configuração de dispositivo personalizada é exibida na lista de dispositivos Virtuais Android existentes. Além disso, ela é adicionada ao menu suspenso do dispositivo:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Novo AVD personalizado adicionado ao menu suspenso do dispositivo](google-emulator-manager-images/win/16-new-avd-sml.png)](google-emulator-manager-images/win/16-new-avd.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Novo AVD personalizado adicionado ao menu suspenso do dispositivo](google-emulator-manager-images/mac/15-new-avd-sml.png)](google-emulator-manager-images/mac/15-new-avd.png#lightbox)

-----

