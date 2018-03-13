---
title: "Instalação e Configuração"
description: Configurando para desenvolver para watchOS
ms.topic: article
ms.prod: xamarin
ms.assetid: 69F21F15-198D-4B42-A703-21D35CAB0CCA
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 12/05/2017
ms.openlocfilehash: c423c9bf49c735673793f8e61134f7e705816d54
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="installation"></a>Instalação

watchOS 4 requer macOS Serra (10.12) com o Xcode 9.

watchOS 1 necessário originalmente OS X Yosemite (10.10) com o Xcode 7.

> [!WARNING]
> [não serão aceitas atualizações watchOS 1 após 1 de abril de 2018](https://developer.apple.com/news/?id=11162017a). Atualizações futuras devem usar watchOS 2 SDK ou versão posterior; Compilando com o watchOS SDK 4 é recomendado.

## <a name="project-structure"></a>Estrutura do projeto

Um aplicativo de inspeção consiste em três projetos:

- **Projeto de aplicativo do iPhone xamarin** -este é um projeto do iPhone normal, ele pode ser qualquer um dos modelos xamarin. O aplicativo de inspeção e sua extensão serão empacotadas dentro desse projeto principal.

- **Projeto de extensão de inspeção** -contém o código (como classes de controlador) para o aplicativo de inspeção.

- **Projeto de aplicativo de inspeção** -contém o arquivo de storyboard de Interface do usuário com todos os recursos de interface do usuário para o aplicativo de inspeção.

O [exemplo inspecionar Kit catálogo](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/) solução tem esta aparência em Xamarin.Studio:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

![](installation-images/catalog-solution.png "A solução no Visual Studio")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![](installation-images/catalog-solution-vs.png "A solução no Visual Studio")

-----

Baixe e execute o [WatchKitCatalog](https://developer.xamarin.com/samples/monotouch/WatchKit/WatchKitCatalog/) exemplo para começar.
Telas do exemplo podem ser encontradas na [controles](~/ios/watchos/user-interface/index.md) página.


## <a name="creating-a-new-project"></a>Criando um novo projeto

Não é possível criar uma nova "Watch solução"... em vez disso, você pode adicionar um aplicativo de inspeção para um aplicativo iOS existente. Siga estas etapas para criar um aplicativo de inspeção:

1. Se você não tiver um projeto existente, primeiro escolha **arquivo > nova solução** e criar um aplicativo iOS (por exemplo, um **único aplicativo de exibição**):

    [![](installation-images/cycle8-2-sml.png "Escolher arquivo > nova solução e crie um aplicativo iOS")](installation-images/cycle8-2.png#lightbox)

2. Depois que o aplicativo do iOS é criado (ou você planeja usar seu aplicativo iOS existente), clique com botão direito na solução e escolha **Adicionar > Adicionar novo projeto.** . No **novo projeto** janela Selecione **watchOS > aplicativo > WatchKit App**:

    [![](installation-images/cycle8-6-sml.png "Selecione watchOS > aplicativo > WatchKit App")](installation-images/cycle8-6.png#lightbox)

3. A próxima tela permite que você escolha qual projeto de aplicativo do iOS deve incluir o aplicativo de inspeção:

    [![](installation-images/cycle8-7-sml.png "Escolha qual projeto de aplicativo do iOS deve incluir o aplicativo de inspeção")](installation-images/cycle8-7.png#lightbox)

4. Por fim, escolha o local para salvar o projeto (e opcionalmente o controle de origem de habilitado):

    [![](installation-images/cycle8-8-sml.png "Escolha o local para salvar o projeto")](installation-images/cycle8-8.png#lightbox)

5. Configura automaticamente o Visual Studio para Mac [referências de projeto e **Info. plist** configurações](~/ios/watchos/get-started/project-references.md) para você.

## <a name="creating-the-watch-user-interface"></a>Criando a Interface do usuário de inspeção

<a name="designer" />

### <a name="using-the-xamarin-ios-designer"></a>Usando o Designer do Xamarin iOS

Clique duas vezes em que o aplicativo de inspeção **Interface.storyboard** Editar usando o Designer do iOS. Você pode arrastar os controladores de interface e controles de interface do usuário para o storyboard do **caixa de ferramentas** e configurá-los usando o **propriedades** teclado:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![](installation-images/iosdesigner-sml.png "O storyboard no Designer")](installation-images/iosdesigner.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](installation-images/iosdesigner-sml-vs.png "O storyboard no Designer")](installation-images/iosdesigner-vs.png#lightbox)

-----

Você deve atribuir a cada novo controlador de interface um **classe** por selecionando-o e, em seguida, inserir o nome no **propriedades** pad (Isso criará os arquivos de code-behind c# necessários automaticamente):

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

![](installation-images/iosdesigner-classname.png "Fornecer uma classe de cada novo controlador de interface")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![](installation-images/iosdesigner-classname-vs.png "Fornecer uma classe de cada novo controlador de interface")

-----

Criar segues por **Ctrl + arrastar** de um controlador de botão, a tabela ou a interface para outro controlador de interface.


### <a name="using-xcode-on-the-mac"></a>Usando o Xcode no Mac

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Você pode continuar a usar o Xcode para criar sua interface de usuário clicando duas vezes no arquivo Interface.storyboard e selecionando **abrir com > Xcode Interface Builder**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Usuários do Visual Studio também podem usar o Xcode para criar sua interface do usuário, alternando para usar o Host de compilação de Mac diretamente.
Abra sua solução no Visual Studio para Mac e, em seguida, clique com botão direito no arquivo Interface.storyboard e selecione **abrir com > Xcode Interface Builder**:

-----

![](installation-images/openwith-xcode.png "Abra o Interface.storyboard no Xcode Interface Builder")

Se usando o Xcode, em seguida, você deve seguir as mesmas etapas para aplicativos de inspeção para normal [storyboards de aplicativo do iOS](~/ios/user-interface/storyboards/index.md) (como a criação de lojas e ações de **Ctrl + arrastar** para o **. h**arquivo de cabeçalho).

Quando você salva o storyboard no Xcode Interface Builder adicionará automaticamente as saídas e as ações que você criar para c# **. designer.cs** arquivos no projeto de extensão de inspeção.


### <a name="adding-additional-screens-in-xcode"></a>Adicionar telas adicionais no Xcode

Quando você adiciona telas adicionais (além do que está incluído no modelo por padrão) para o storyboard usando o construtor de Interface do Xcode **você deve adicionar manualmente os arquivos de código c#** para cada novo controlador de interface.

Consulte o [avançado instruções sobre como adicionar novos controladores de interface para um storyboard](~/ios/watchos/troubleshooting.md#add).

*O Designer do Xamarin iOS faz isso automaticamente, nenhuma etapa manual é necessária.*


## <a name="building"></a>Compilação

Cria um projeto que inclui um aplicativo de inspeção como outros projetos do iOS. Processo de criação de resultará em um aplicativo de iPhone (. app) que contém uma extensão de inspeção (.appex), que por sua vez, contém o aplicativo de inspeção sem código (. app).


## <a name="launching"></a>Iniciando

Você pode iniciar aplicativos inspecionar no simulador usando o Visual Studio para Mac ou o Visual Studio (é iniciada no Host de compilação do Mac).

Há dois modos para iniciar um aplicativo WatchKit:

 - modo de aplicativo normal (padrão), e
- [Notificações](~/ios/watchos/platform/notifications.md) (que exige uma carga de notificação de teste no formato JSON).

### <a name="xcode-8-support"></a>Suporte do Xcode 8

Depois de instalar o Xcode 8 (ou posterior), simuladores de Apple Watch são separadas do iOS simuladores (ao contrário de [Xcode 6](#xcode6), onde que aparecem como uma *vídeo externo*).
Quando você seleciona o projeto do aplicativo de inspeção e torná-lo o projeto de inicialização, a lista de simulador mostrará *iOS simuladores* à sua escolha (conforme mostrado abaixo).

[![](installation-images/xs-xcode8-watchos3-sml.png "Selecionar o tipo do simulador")](installation-images/xs-xcode8-watchos3.png#lightbox)

Quando você iniciar a depuração, *duas* simuladores deve iniciar - o simulador de iOS *e* Apple Watch simulador. Use **comando + Shift + H** para navegar até a face de menu e o relógio do relógio; e usar o **Hardware** menu para definir o **Force Touch pressão**. Rolagem no trackpad ou mouse simulará usando o coroa Digital.

#### <a name="troubleshooting"></a>Solução de problemas

O seguinte erro aparecerá no **saída do aplicativo** se você tentar iniciar em um simulador que não tenha uma inspeção par:

```csharp
error MT0000: Unexpected error - Please file a bug report at http://bugzilla.xamarin.com
error HE0020: Could not find a paired Watch device for the iOS device 'iPhone 6'.
```

Consulte [fóruns da Apple](https://forums.developer.apple.com/thread/7783) para obter instruções sobre como configurar os simuladores, se os padrões não funcionam.


<a name="xcode6" />

### <a name="xcode-6-and-watchos-1"></a>Xcode 6 e watchOS 1

Você deve fazer o *projeto de extensão de inspeção* o **projeto de inicialização** antes de executar ou depurar o aplicativo. Você não pode "Iniciar" o aplicativo de inspeção em si, e se você escolher o aplicativo do iOS, em seguida, ele será iniciado normalmente no simulador iOS.

Por padrão, um aplicativo de inspeção inicia no modo normal **aplicativo** modo (modo não rapidamente ou notificações) do Visual Studio para Mac **executar** ou **depurar** comandos.

Ao usar o Xcode 6, somente o iPhone 5, 5S iPhone, iPhone 6 e iPhone 6 Plus pode ativar o monitor externo para um **Apple Watch - 38mm** ou **Apple Watch - 42mm** onde estarão os aplicativos de inspeção exibido.

**Observação:** Lembre-se de que a tela de inspeção não aparecem automaticamente no simulador iOS ao usar o Xcode 6.
Use o **Hardware > monitores externos** menu para mostrar a tela de observação.

<a name="custommodes" />

## <a name="launching-notification-mode"></a>Iniciar modo de notificação

Consulte o [página notificações](~/ios/watchos/platform/notifications.md) para obter informações como lidar com as notificações no código.


O Visual Studio para Mac pode iniciar o aplicativo de inspeção com uma notificação _modos de inicialização_ para notificações:



Clique com botão direito no projeto de aplicativo de inspeção e escolha **executar com > Configuração personalizada...** :


[![](installation-images/runwith-customparams-sml.png "Executando uma configuração personalizada")](installation-images/runwith-customparams.png#lightbox)


Isso abre o **parâmetros personalizados** janela onde você pode selecionar **notificação** (e fornecer uma carga JSON), em seguida, pressione **executar** para iniciar o aplicativo watch no simulador:


[![](installation-images/runwith-execargs-sml.png "Configurando a notificação e a carga")](installation-images/runwith-execargs.png#lightbox)



## <a name="debugging"></a>Depuração

Há suporte para a depuração no Visual Studio para Mac e o Visual Studio.
Lembre-se de fornecer um arquivo JSON de notificação durante a depuração no modo de notificações. Esta captura de tela mostra um ponto de interrupção de depuração está sendo atingido em um aplicativo de inspeção:

![](installation-images/debug-sml.png "Esta captura de tela mostra um ponto de interrupção de depuração está sendo atingido em um aplicativo de inspeção")

Após seguir as instruções de inicialização você acabará com seu aplicativo de inspeção em execução no **(Observação) do simulador de iOS**.
Para o modo de notificação, você pode selecionar **Depurar > Abrir Log do sistema** (**CMD + /**) e usar `Console.WriteLine` no seu código.

### <a name="debugging-lifecycle-event-handlers"></a>Manipuladores de eventos de ciclo de vida de depuração

<!--
To test the functionality in your  and 
    methods, use the **Hardware > Lock** command in the iOS Simulator.
    Locking will trigger the `DidDeactivate` method and the watch simulator
    will indicate that it has been locked. Swipe the iOS Simulator to unlock,
    which triggers the `WillActivate` method of the watch app.
-->

Os arquivos de modelo watchOS (como `InterfaceController`, `ExtensionDelegate`, `NotificationController`, e `ComplicationController`) vêm com seus métodos de ciclo de vida necessária já foi implementados. Adicionar `Console.WriteLine` chamadas e ler o **saída do aplicativo** para entender melhor o ciclo de vida do evento.



## <a name="related-links"></a>Links relacionados

- [WatchKitCatalog (exemplo)](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [Vídeo do primeiro aplicativo Watch](http://blog.xamarin.com/your-first-watch-kit-app/)
- [Dicas de WatchKit da Apple](https://developer.apple.com/watchkit/tips/)
