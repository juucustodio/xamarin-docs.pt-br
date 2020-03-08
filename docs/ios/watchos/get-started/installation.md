---
title: Instalar e usar o watchOS no Xamarin
description: Este documento descreve como instalar e usar o watchOS com Xamarin. Ele aborda a instalação, o projeto do watchOS estrutura como usar o designer do iOS, a integração do Xcode e fornece dicas de solução de problemas.
ms.prod: xamarin
ms.assetid: 69F21F15-198D-4B42-A703-21D35CAB0CCA
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 12/05/2017
ms.openlocfilehash: f986099011dbccb0eb43c62d253ee497d46ca08e
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2020
ms.locfileid: "78915631"
---
# <a name="installing-and-using-watchos-in-xamarin"></a>Instalar e usar o watchOS no Xamarin

watchOS 4 exige o macOS Sierra (10.12) com o Xcode 9.

watchOS 1 originalmente exigia o OS X Yosemite (10.10) com o Xcode 7.

> [!WARNING]
> [as atualizações do watchOS 1 não serão aceitas após 1º de abril de 2018](https://developer.apple.com/news/?id=11162017a). Atualizações futuras devem usar watchOS 2 SDK ou versão posterior; Compilando com o watchOS 4 do SDK é recomendado.

## <a name="project-structure"></a>Estrutura do Projeto

Um aplicativo de inspeção consiste em três projetos:

- **Projeto de aplicativo xamarin. IOS iPhone** – este é um projeto do iPhone normal, podendo ser qualquer um dos modelos do Xamarin. Ios. O aplicativo de inspeção e sua extensão serão empacotados dentro desse projeto principal.

- **Inspecionar projeto de extensão** -contém o código (como classes de controlador) para o aplicativo Watch.

- **Inspecionar projeto de aplicativo** -contém o arquivo de storyboard da interface do usuário com todos os recursos de interface de usuário do aplicativo Watch.

A solução de [exemplo de catálogo do Watch kit](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog) é parecida com esta no Xamarin. Studio:

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

![](installation-images/catalog-solution.png "The solution in Visual Studio")

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![](installation-images/catalog-solution-vs.png "The solution in Visual Studio")

-----

Baixe e execute o exemplo de [WatchKitCatalog](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog) para começar.
As telas do exemplo podem ser encontradas na página [controles](~/ios/watchos/user-interface/index.md) .

## <a name="creating-a-new-project"></a>Criando um novo projeto

Você não pode criar uma nova "Watch solução"... em vez disso, você pode adicionar um aplicativo de inspeção para um aplicativo iOS existente. Siga estas etapas para criar um aplicativo de inspeção:

1. Se você não tiver um projeto existente, primeiro escolha **arquivo > nova solução** e crie um aplicativo IOS (por exemplo, um **único aplicativo de exibição**):

    [![](installation-images/cycle8-2-sml.png "Choose File > New Solution and create an iOS app")](installation-images/cycle8-2.png#lightbox)

2. Depois que o aplicativo iOS for criado (ou você planeja usar seu aplicativo iOS existente), clique com o botão direito do mouse na solução e escolha **adicionar > adicionar novo projeto..** . Na janela **novo projeto** , selecione **watchOS > aplicativo > aplicativo WatchKit**:

    [![](installation-images/cycle8-6-sml.png "Select watchOS > App > WatchKit App")](installation-images/cycle8-6.png#lightbox)

3. A próxima tela permite que você escolha qual projeto de aplicativo do iOS deve incluir o aplicativo watch:

    [![](installation-images/cycle8-7-sml.png "Choose which iOS app project should include the watch app")](installation-images/cycle8-7.png#lightbox)

4. Por fim, escolha o local para salvar o projeto (e opcionalmente habilitado o controle de origem):

    [![](installation-images/cycle8-8-sml.png "Choose the location to save the project")](installation-images/cycle8-8.png#lightbox)

5. Visual Studio para Mac configura automaticamente [as referências do projeto e as configurações do **info. plist** ](~/ios/watchos/get-started/project-references.md) para você.

## <a name="creating-the-watch-user-interface"></a>Criando a Interface do usuário de inspeção

<a name="designer" />

### <a name="using-the-xamarin-ios-designer"></a>Usando o Designer do iOS Xamarin

Clique duas vezes na interface do aplicativo Watch **. Storyboard** para editar usando o designer do Ios. Você pode arrastar controladores de interface e controles de interface do usuário para o storyboard da **caixa de ferramentas** e configurá-los usando o painel de **Propriedades** :

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

[![](installation-images/iosdesigner-sml.png "The storyboard in the Designer")](installation-images/iosdesigner.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![](installation-images/iosdesigner-sml-vs.png "The storyboard in the Designer")](installation-images/iosdesigner-vs.png#lightbox)

-----

Você deve dar a cada novo controlador de interface uma **classe** selecionando-o e, em seguida, inserindo o nome no painel de C# **Propriedades** (isso criará os arquivos de code-behind necessários automaticamente):

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

![](installation-images/iosdesigner-classname.png "Give each new interface controller a Class")

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![](installation-images/iosdesigner-classname-vs.png "Give each new interface controller a Class")

-----

Crie continuações por **Ctrl + arrastando** de um botão, tabela ou controlador de interface para outro controlador de interface.

### <a name="using-xcode-on-the-mac"></a>Usando o Xcode no Mac

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Você pode continuar a usar o Xcode para criar a interface do usuário clicando com o botão direito do mouse no arquivo interface. Storyboard e selecionando **abrir com > Xcode Interface Builder**:

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Usuários do Visual Studio também podem usar o Xcode para criar sua interface do usuário, alternando para usar o Host de Build do Mac diretamente.
Abra sua solução no Visual Studio para Mac e clique com o botão direito do mouse no arquivo interface. Storyboard e selecione **abrir com > Xcode Interface Builder**:

-----

![](installation-images/openwith-xcode.png "Open the Interface.storyboard in Xcode Interface Builder")

Se estiver usando o Xcode, você deverá seguir as mesmas etapas para observar aplicativos como para [storyboards normais do aplicativo IOS](~/ios/user-interface/storyboards/index.md) (como a criação de saídas e ações por **Ctrl + arrastar** para o arquivo de cabeçalho **. h** ).

Ao salvar o storyboard no Xcode Interface Builder ele adicionará automaticamente as saídas e as ações que você criar aos C# arquivos **. designer.cs** no projeto de extensão Watch.

### <a name="adding-additional-screens-in-xcode"></a>Adicionando telas adicionais no Xcode

Quando você adiciona telas adicionais (além do que está incluído no modelo por padrão) ao seu storyboard usando o Xcode Interface Builder **você deve adicionar manualmente C# os arquivos de código** para cada novo controlador de interface.

Consulte as [instruções avançadas sobre como adicionar novos controladores de interface a um storyboard](~/ios/watchos/troubleshooting.md#add).

*O Xamarin iOS designer faz isso automaticamente, nenhuma etapa manual é necessária.*

## <a name="building"></a>Compilação

Cria um projeto que inclui um aplicativo de inspeção, como outros projetos do iOS. Processo de criação de resultará em um aplicativo de iPhone (. app) que contém uma extensão de inspeção (.appex), que por sua vez, contém o aplicativo de inspeção de código sem (. app).

## <a name="launching"></a>Iniciando

Você pode iniciar aplicativos do watch no simulador usando o Visual Studio para Mac ou Visual Studio (que começa em um Host de Build do Mac).

Há dois modos para iniciar um aplicativo WatchKit:

- modo de aplicativo normal (padrão), e
- [Notificações](~/ios/watchos/platform/notifications.md) (que requer uma carga de notificação de teste no formato JSON).

### <a name="xcode-8-support"></a>Suporte ao Xcode 8

Depois que o Xcode 8 (ou posterior) é instalado, os simuladores de Apple Watch são separados dos simuladores do iOS (ao contrário do [Xcode 6](#xcode6), onde eles apareciam como uma *exibição externa*).
Quando você seleciona o projeto de aplicativo Watch e o torna o projeto de inicialização, a lista de simuladores mostrará *simuladores do IOS* para escolher (como mostrado abaixo).

[![](installation-images/xs-xcode8-watchos3-sml.png "Selecting the Simulator type")](installation-images/xs-xcode8-watchos3.png#lightbox)

Quando você inicia a depuração, *dois* simuladores devem iniciar o simulador do IOS *e* o simulador de Apple Watch. Use o **Comando + Shift + H** para navegar até o menu Watch e o relógio Clock; e use o menu de **hardware** para definir a **pressão de Force Touch**. Rolagem no mouse ou trackpad simulará a usando o Crown Digital.

#### <a name="troubleshooting"></a>Solução de problemas

O erro a seguir aparecerá na **saída do aplicativo** se você tentar iniciar o para um simulador que não tem uma inspeção emparelhada:

```csharp
error MT0000: Unexpected error - Please file a bug report at https://github.com/xamarin/xamarin-macios/issues/new
error HE0020: Could not find a paired Watch device for the iOS device 'iPhone 6'.
```

Consulte os [fóruns da Apple](https://forums.developer.apple.com/thread/7783) para obter instruções sobre como configurar os simuladores, se os padrões não funcionarem.

<a name="xcode6" />

### <a name="xcode-6-and-watchos-1"></a>O Xcode 6 e watchOS 1

Você deve tornar o projeto de *extensão de inspeção* o **projeto de inicialização** antes de executar ou depurar o aplicativo. Você não pode "Iniciar" o aplicativo de inspeção em si, e se você escolher o aplicativo do iOS, em seguida, ele será iniciado normalmente no simulador de iOS.

Por padrão, um aplicativo Watch é iniciado no modo de **aplicativo** normal (não no modo de visão geral ou notificações) dos comandos **executar** ou **depurar** de Visual Studio para Mac.

Ao usar o Xcode 6, somente o iPhone 5, iPhone 5S, iPhone 6 e iPhone 6 Plus podem ativar a exibição externa para **Apple Watch-38 mm** ou **Apple Watch-42 mm** onde os aplicativos de inspeção serão exibidos.

> [!NOTE]
> Lembre-se de que a tela Watch não aparece automaticamente no simulador de iOS ao usar o Xcode 6.
> Use o menu de **exibição de Hardware > externo** para mostrar a tela de inspeção.

<a name="custommodes" />

## <a name="launching-notification-mode"></a>Iniciar modo de notificação

Consulte a [página notificações](~/ios/watchos/platform/notifications.md) para obter informações sobre como lidar com notificações no código.

Visual Studio para Mac pode iniciar o aplicativo Watch com um _modo de inicialização_ de notificação para notificações:

Clique com o botão direito do mouse no projeto do aplicativo de inspeção e escolha **executar com > configuração personalizada...** :

[![](installation-images/runwith-customparams-sml.png "Running a Custom Configuration")](installation-images/runwith-customparams.png#lightbox)

Isso abre a janela **parâmetros personalizados** , na qual você pode selecionar **notificação** (e fornecer uma carga JSON) e, em seguida, pressione **executar** para iniciar o aplicativo Watch no simulador:

[![](installation-images/runwith-execargs-sml.png "Setting the Notification and Payload")](installation-images/runwith-execargs.png#lightbox)

## <a name="debugging"></a>Depuração

Há suporte para a depuração no Visual Studio para Mac e Visual Studio.
Lembre-se fornecer um arquivo JSON de notificação durante a depuração no modo de notificações. Esta captura de tela mostra um ponto de interrupção de depuração está sendo atingido em um aplicativo de inspeção:

![](installation-images/debug-sml.png "This screenshot shows a debug breakpoint being hit in a watch app")

Depois de seguir as instruções de inicialização, você acabará com seu aplicativo Watch em execução no **simulador do IOS (Watch)** .
Para o modo de notificação, você pode selecionar **depurar > log do sistema aberto** (**cmd +/** ) e usar `Console.WriteLine` em seu código.

### <a name="debugging-lifecycle-event-handlers"></a>Manipuladores de eventos de ciclo de vida de depuração

<!--
To test the functionality in your  and 
  methods, use the **Hardware > Lock** command in the iOS Simulator.
  Locking will trigger the `DidDeactivate` method and the watch simulator
  will indicate that it has been locked. Swipe the iOS Simulator to unlock,
  which triggers the `WillActivate` method of the watch app.
-->

Os arquivos de modelo watchOS (como `InterfaceController`, `ExtensionDelegate`, `NotificationController`e `ComplicationController`) vêm com seus métodos de ciclo de vida necessários já implementados. Adicione chamadas de `Console.WriteLine` e leia a **saída do aplicativo** para entender melhor o ciclo de vida do evento.

## <a name="related-links"></a>Links relacionados

- [WatchKitCatalog (exemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [Primeiro vídeo do aplicativo Watch](https://blog.xamarin.com/your-first-watch-kit-app/)
- [Dicas de WatchKit da Apple](https://developer.apple.com/watchkit/tips/)
