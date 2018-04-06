---
title: Emulador do Visual Studio Android
description: Este guia explica como configurar e usar o Emulador do Visual Studio Android para desenvolver aplicativos Xamarin.Android no Visual Studio 2015.
ms.prod: xamarin
ms.assetid: CD128CB9-499F-4558-B49F-77248824EFDF
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/30/2018
ms.openlocfilehash: bcc88ba4c46aa9abe9038d6ea65bc9c17f337da6
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="visual-studio-android-emulator"></a>Emulador do Visual Studio Android

_Este guia explica como configurar e usar o Android Emulator do Visual Studio para desenvolver aplicativos Xamarin.Android no Visual Studio 2015._

## <a name="visual-studio-android-emulator-overview"></a>Visão geral do Android Emulator do Visual Studio

O Microsoft Visual Studio 2015 inclui um emulador do Android que pode ser usado como um destino para depurar um aplicativo Xamarin.Android: *Emulador do Visual Studio para Android*. Esse emulador usa os recursos do Hyper-V do seu computador de desenvolvimento, resultando em inicialização e tempos de execução mais rápidos que no emulador padrão fornecido com o SDK do Android. O Emulador do Visual Studio para Android pode ser usado como uma alternativa ao emulador do SDK do Android padrão ao desenvolver um aplicativo Xamarin.Android.

> [!NOTE]
> O Android Emulator do Visual Studio é compatível apenas com o Visual Studio 2015 &ndash; ele não funciona com o Visual Studio 2017.

Este guia explica como inicializar o emulador do Microsoft Android do Visual Studio para testar seu aplicativo e descreve os diversos recursos disponíveis no emulador. Você aprenderá a selecionar *perfis de dispositivo* (semelhantes a definições de dispositivo no emulador do SDK do Android padrão) para simular tipos diferentes de dispositivos com Android. Finalmente, uma seção de solução de problemas explica soluções alternativas e armadilhas comuns.

## <a name="requirements"></a>Requisitos

Para executar o emulador, o computador deve atender aos requisitos para executar o Hyper-V. Hyper-V requer uma versão de 64 bits da edição Pro do Windows 8, Windows 8.1, Windows 10 ou superior. Para saber mais sobre requisitos, consulte [Requisitos de sistema para o Emulador do Visual Studio para Android](https://msdn.microsoft.com/en-us/library/mt228280.aspx).

> [!NOTE]
> Não é possível usar o HAXM (usado pelo Emulador do SDK do Android) enquanto o Hyper-V está habilitado. Para obter mais informações sobre as limitações e possíveis problemas com a HAXM, consulte [Conflitos de virtualização do HAXM](~/android/deploy-test/debugging/android-sdk-emulator/troubleshooting.md#virt-conflicts).


## <a name="running-the-emulator"></a>Como executar o emulador

O Visual Studio disponibiliza vários perfis de dispositivo de destino pré-configurados no menu suspenso **Destino de Depuração** (como visto na captura de tela a seguir). Os destinos de Emulador Microsoft Android são precedidos com **Emulador VS**:

[![Perfis de dispositivo de destino pré-configurados](visual-studio-android-emulator-images/01-vs-emulator-defaults-vs-sml.png)](visual-studio-android-emulator-images/01-vs-emulator-defaults-vs.png#lightbox)

Quando o Visual Studio inicia um aplicativo Xamarin.Android, o emulador é iniciado com o destino do dispositivo escolhido e o aplicativo é implantado no emulador. Uma mensagem será exibida no canto inferior esquerdo do Visual Studio, indicando que o emulador está iniciando:

[![Iniciando o Emulador do VS](visual-studio-android-emulator-images/02-emulator-starting-vs-sml.png)](visual-studio-android-emulator-images/02-emulator-starting-vs.png#lightbox)

Após um atraso de inicialização, a tela do emulador aparece como mostrado à esquerda abaixo. Arraste o ícone de bloqueio na tela para cima para desbloquear o dispositivo.
Em seguida, o aplicativo Xamarin.Android deve ser executado no emulador conforme mostrado à direita:

[![Capturas de tela do emulador](visual-studio-android-emulator-images/03-first-screen-vs-sml.png)](visual-studio-android-emulator-images/03-first-screen-vs.png#lightbox)

Assim como acontece com o emulador do SDK do Android padrão, é possível definir pontos de interrupção no código, inspecionar variáveis e exibir a pilha de chamadas. A barra de ferramentas vertical à direita do emulador fornece acesso a recursos do emulador:

[![Botões na barra de ferramentas vertical](visual-studio-android-emulator-images/04-vertical-toolbar-vs-sml.png)](visual-studio-android-emulator-images/04-vertical-toolbar-vs.png#lightbox)

A lista a seguir resume a função de cada botão na barra de ferramentas vertical:

-   **Fechar** &ndash; desliga o aplicativo do emulador. Esse botão não é usado com frequência &ndash; normalmente, o emulador é deixado em execução após a primeira inicialização (para evitar o atraso de reinício do emulador) e fechado apenas quando não é mais necessário.

-   **Minimizar** &ndash; deixa o emulador em execução, mas minimiza-o para a barra de tarefas.

-   **Ligar/Desligar** &ndash; simula ligar e desligar o dispositivo. (O emulador permanece em execução.)

-   **Multitoque** &ndash; sobrepõe vários pontos na tela do dispositivo que atuam como pontos de contato para gestos de pinçar e zoom.
    Arrastar um ponto faz com que o outro ponto se mover na direção oposta, simulando o movimento de dois dedos.

-   **Entrada de Mouse de Ponto Único** &ndash; retorna o dispositivo para a entrada de ponto único (depois de usar a entrada Multitoque).

-   **Girar para a Esquerda/Girar para a Direita** &ndash; ajuda a testar como o aplicativo responde a alterações de orientação. Por exemplo, na primeira vez que o botão *Girar para a Esquerda* é clicado, o emulador mudará para modo paisagem. Quando o botão *Girar para a Direita* for pressionado, o emulador voltará ao modo retrato.

-   **Ajustar à Tela** &ndash; ajusta o tamanho da tela do emulador para que caiba na tela da área de trabalho.

-   **Aplicar Zoom** &ndash; dimensiona a tela do emulador em 33%, 50%, 66%, 100% ou algum percentual personalizado.


O botão *Ferramentas Adicionais* exibirá uma caixa de diálogo aberta mostrando os recursos adicionais do emulador:

[![Caixa de diálogo Ferramentas Adicionais](visual-studio-android-emulator-images/05-additional-tools-vs-sml.png)](visual-studio-android-emulator-images/05-additional-tools-vs.png#lightbox)


Cada recurso adicional está disponível em uma linha de guias na parte superior da caixa de diálogo:

-   **Acelerômetro** &ndash; simula o movimento de dispositivo em um espaço 3D.

-   **Local** &ndash; apresenta um mapa que pode ser usado para selecionar e simular um local de GPS. Neste mapa, *pontos do mapa* podem ser criados para simular a movimentação entre locais.

-   **Bateria** &ndash; fornece um controle deslizante para simular a quantidade de carga restante na bateria.

-   **Captura de tela** &ndash; nessa guia, o botão **Captura** faz uma captura de tela e exibe uma visualização instantânea. O botão *Salvar* salvará a captura de tela.

-   **Câmera** &ndash; simula tirar uma foto por meio de uma imagem animada fixa, uma imagem de um arquivo ou uma webcam anexada ao computador host. É possível selecionar a câmera frontal ou a câmera traseira.

-   **Cartão SD** &ndash; o emulador pode disponibilizar uma pasta no computador host para o dispositivo como um cartão SD.
    Quando o aplicativo lê e grava arquivos no cartão SD simulado, eles podem ser acessados diretamente da área de trabalho sem usar o comando `adb`.

-   **Rede** &ndash; exibe um resumo das configurações de rede do emulador (emulador reutiliza a conexão de rede do computador host).

Para saber mais sobre como usar esses recursos, consulte [Introdução ao Emulador do Visual Studio para Android](https://blogs.msdn.microsoft.com/visualstudioalm/2014/11/12/introducing-visual-studios-emulator-for-android/).



## <a name="configuring-device-profiles"></a>Como configurar perfis de dispositivo

O emulador Microsoft Android inclui um conjunto de perfis de dispositivo que representam as versões mais populares do Android, tamanhos de tela e propriedades de hardware de dispositivos Android no mercado. Além disso, esses perfis de dispositivo já estão configurados para várias versões do Android, como KitKat, Lollipop e Marshmallow.

O *Gerenciador de Emulador* é usado para instalar, desinstalar e iniciar os perfis de dispositivo. No menu **Ferramentas**, selecione **Emulador do Visual Studio para Android...** conforme indicado nesta captura de tela:

[![Iniciando o emulador no menu Ferramentas](visual-studio-android-emulator-images/06-launch-emulator-manager-vs-sml.png)](visual-studio-android-emulator-images/06-launch-emulator-manager-vs.png#lightbox)

Isso abre a caixa de diálogo **Perfis de Dispositivo**. Os perfis instalados são realçados na parte superior da lista de perfis do dispositivo. Perfis que não estão instalados (mas estão disponíveis para instalação) ficam esmaecidos:

[![Ícones de perfis de dispositivo](visual-studio-android-emulator-images/07-device-profiles-vs-sml.png)](visual-studio-android-emulator-images/07-device-profiles-vs.png#lightbox)

Para instalar um novo perfil, clique no ícone de instalação do perfil (uma seta apontada para baixo como mostra a captura de tela acima). Por exemplo, quando você clicar no ícone de instalação do perfil para o **Telefone XHDPI Marshmallow (6.0.0) de 5.7"**, o Gerenciador de Emulador baixa o perfil conforme mostrado aqui:

[![Exemplo de download de perfis](visual-studio-android-emulator-images/08-downloading-profile-vs-sml.png)](visual-studio-android-emulator-images/08-downloading-profile-vs.png#lightbox)

Depois de baixar o perfil do dispositivo, ele é realçado para indicar que o perfil foi instalado com êxito. Clicar no ícone *Mostrar detalhes* exibirá o tipo de plataforma, a arquitetura de CPU, o tamanho/resolução da tela e a memória disponível para o dispositivo:

[![Mostrar detalhes do perfil do dispositivo](visual-studio-android-emulator-images/09-show-details-vs-sml.png)](visual-studio-android-emulator-images/09-show-details-vs.png#lightbox)

Quando o menu suspenso **Destino de Depuração** do Visual Studio é aberto, o perfil de dispositivo recém-instalado agora fica disponível como um destino:

[![Novo perfil no menu suspenso de destino](visual-studio-android-emulator-images/10-debug-target-vs-sml.png)](visual-studio-android-emulator-images/10-debug-target-vs.png#lightbox)

Essa lista pode ser reduzida clicando em **Desinstalar este perfil** no *Gerenciador de Emulador* para remover perfis de dispositivos não utilizados. Observe que, no momento, não há como criar um perfil de dispositivo personalizado nesse emulador.


## <a name="troubleshooting"></a>Solução de problemas

Esta seção descreve alguns erros e soluções alternativas comuns ao usar o *Emulador do Visual Studio para Android* com Xamarin.Android.

<a name="cant_connect" />

### <a name="emulator-will-not-start"></a>O emulador não é iniciado

Em alguns casos, o emulador não será iniciado se houver incompatibilidades entre o processador do host e a máquina virtual Hyper-V. Para contornar esse problema, configure o Hyper-V para limitar os recursos do processador que uma máquina virtual pode ter &ndash; isso melhora a compatibilidade da máquina virtual com as diferentes versões do processador de host.
Use as seguintes etapas para fazer essa alteração:

1.  Clique no botão **Iniciar**, digite **MMC** e pressione **Enter**. Clique em **Gerenciador do Hyper-V** conforme ilustrado aqui:

    [![Gerenciador do Hyper-V](visual-studio-android-emulator-images/15-launch-hyperv-manager.png)](visual-studio-android-emulator-images/15-launch-hyperv-manager.png#lightbox)

2.  No Gerenciador do Hyper-V, painel **Máquinas Virtuais**, clique com o botão direito do mouse no emulador a editar e usar e clique em **Configurações...**:

    [![Item de menu Configurações de Máquinas Virtuais](visual-studio-android-emulator-images/16-vm-settings.png)](visual-studio-android-emulator-images/16-vm-settings.png#lightbox)

3.  Na janela de configurações, localize a seção **Compatibilidade** (em **Hardware > Processador**) e habilite **Migrar para um computador físico com uma versão de processador diferente**:

    [![Opção Migrar marcada](visual-studio-android-emulator-images/17-set-compatibility-vs-sml.png)](visual-studio-android-emulator-images/17-set-compatibility-vs.png#lightbox)

4.  Clique em **OK** e feche a janela do Gerenciador do Hyper-V.



### <a name="app-deploys-and-starts-but-fails-immediately"></a>O aplicativo é implantado e iniciado, mas falha imediatamente

Nessa situação, o emulador é iniciado, o aplicativo é implantado com êxito no emulador e o aplicativo é iniciado. No entanto, o aplicativo falha imediatamente.
Em muitos casos, isso também é causado por incompatibilidades entre o processador do host e a máquina virtual Hyper-V. Para resolver esse erro, siga as instruções em [O emulador não é iniciado](#cant_connect) (acima).


### <a name="emulator-stops-with-the-diagnostic-message-libaot-mscorlibdllso-not-found"></a>O emulador para com a mensagem de diagnóstico: **libaot-mscorlib.dll.so não encontrado**

Para resolver esse erro, use as etapas a seguir para desabilitar a implantação rápida:

1.  Siga as instruções em [O emulador não é iniciado](#cant_connect) (acima).

2.  Clique duas vezes em **Propriedades** do projeto.

3.  Clique em **Opções do Android** e desmarque **Usar Implantação Rápida (somente modo de depuração)**:

    [![Opção Usar Implantação Rápida desmarcada](visual-studio-android-emulator-images/18-fast-deployment-vs-sml.png)](visual-studio-android-emulator-images/18-fast-deployment-vs.png#lightbox)



### <a name="drag-and-drop-does-not-work"></a>Arrastar e soltar não funciona

Se o *Emulador do Visual Studio para Android* for iniciado como um Administrador (ou se você o inicializar do Visual Studio enquanto o Visual Studio está sendo executado com privilégios de Administrador), arrastar e soltar arquivos .APK ou .ZIP poderá não funcionar. Para contornar esse problema, execute o *Emulador do Visual Studio para Android* sem permissões elevadas (ou seja, não como Administrador).


### <a name="other-errors"></a>Outros erros

As dicas de solução de problemas acima abordam os problemas mais comuns ao usar o Emulador de Android do Visual Studio com Xamarin.Android. Para obter um guia mais completo para solução de problemas do Emulador de Android do Visual Studio, consulte [Solução de problemas do Emulador do Visual Studio para Android](https://msdn.microsoft.com/en-us/library/mt228282.aspx).



## <a name="summary"></a>Resumo

Este artigo apresentou o *Emulador do Visual Studio para Android*; explicou como usar o emulador para depurar aplicativos Xamarin.Android no Visual Studio, descreveu as funções dos botões na barra de ferramentas vertical e apresentou uma visão geral dos recursos disponíveis na caixa de diálogo **Ferramentas Adicionais**. Explica como usar o  *Gerenciador de Emulador* para instalar, desinstalar e iniciar os perfis de dispositivo. Uma seção de *Solução de Problemas* explicou problemas e soluções alternativas comuns ao usar o emulador.


## <a name="related-links"></a>Links relacionados

- [Emulador do Visual Studio para Android](https://www.visualstudio.com/en-us/explore/msft-android-emulator-vs.aspx)
- [Introdução ao emulador do Visual Studio para Android](https://blogs.msdn.microsoft.com/visualstudioalm/2014/11/12/introducing-visual-studios-emulator-for-android/)
