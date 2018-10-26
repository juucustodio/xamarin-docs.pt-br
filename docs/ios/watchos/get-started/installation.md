---
title: Instalar e usar o watchOS no Xamarin
description: Este documento descreve como instalar e usar o watchOS com Xamarin. Ele aborda a instalação, o projeto do watchOS estrutura como usar o designer do iOS, a integração do Xcode e fornece dicas de solução de problemas.
ms.prod: xamarin
ms.assetid: 69F21F15-198D-4B42-A703-21D35CAB0CCA
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 12/05/2017
ms.openlocfilehash: daece8029bd6a97923d6469f9b42d69efbd3f905
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50119812"
---
# <a name="installing-and-using-watchos-in-xamarin"></a>Instalar e usar o watchOS no Xamarin

watchOS 4 exige o macOS Sierra (10.12) com o Xcode 9.

watchOS 1 originalmente exigia o OS X Yosemite (10.10) com o Xcode 7.

> [!WARNING]
> [atualizações do watchOS 1 não serão aceito após 1 de abril de 2018](https://developer.apple.com/news/?id=11162017a). Atualizações futuras devem usar watchOS 2 SDK ou versão posterior; Compilando com o watchOS 4 do SDK é recomendado.

## <a name="project-structure"></a>Estrutura do projeto

Um aplicativo de inspeção consiste em três projetos:

- **Projeto de aplicativo do xamarin. IOS iPhone** -este é um projeto de iPhone normal, ele pode ser qualquer um dos modelos do xamarin. IOS. O aplicativo de inspeção e sua extensão serão empacotados dentro desse projeto principal.

- **Projeto de extensão de inspeção** – isso contém o código (como classes de controlador) para o aplicativo Watch.

- **Projeto de aplicativo de inspeção** – isso contém o arquivo de storyboard do Interface do usuário com todos os recursos de interface do usuário para o aplicativo Watch.

O [exemplo de catálogo do Kit de inspeção](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/) solução tem esta aparência no xamarin. Studio:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![](installation-images/catalog-solution.png "A solução no Visual Studio")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![](installation-images/catalog-solution-vs.png "A solução no Visual Studio")

-----

Baixe e execute o [WatchKitCatalog](https://developer.xamarin.com/samples/monotouch/WatchKit/WatchKitCatalog/) exemplo para começar a usar.
Telas de exemplo podem ser encontradas na [controles](~/ios/watchos/user-interface/index.md) página.


## <a name="creating-a-new-project"></a>Criando um novo projeto

Você não pode criar uma nova "Watch solução"... em vez disso, você pode adicionar um aplicativo de inspeção para um aplicativo iOS existente. Siga estas etapas para criar um aplicativo de inspeção:

1. Se você não tiver um projeto existente, primeiro escolha **arquivo > nova solução** e criar um aplicativo iOS (por exemplo, um **aplicativo de exibição única**):

    [![](installation-images/cycle8-2-sml.png "Escolha Arquivo > nova solução e criar um aplicativo iOS")](installation-images/cycle8-2.png#lightbox)

2. Depois que o aplicativo iOS é criado (ou você planeja usar seu aplicativo iOS existente), clique com botão direito na solução e escolha **Adicionar > Adicionar novo projeto...** . No **novo projeto** janela select **watchOS > aplicativo > WatchKit App**:

    [![](installation-images/cycle8-6-sml.png "Selecione watchOS > aplicativo > WatchKit App")](installation-images/cycle8-6.png#lightbox)

3. A próxima tela permite que você escolha qual projeto de aplicativo do iOS deve incluir o aplicativo watch:

    [![](installation-images/cycle8-7-sml.png "Escolha qual projeto de aplicativo do iOS deve incluir o aplicativo de inspeção")](installation-images/cycle8-7.png#lightbox)

4. Por fim, escolha o local para salvar o projeto (e opcionalmente habilitado o controle de origem):

    [![](installation-images/cycle8-8-sml.png "Escolha o local para salvar o projeto")](installation-images/cycle8-8.png#lightbox)

5. Configura automaticamente o Visual Studio para Mac [referências do projeto e **Info. plist** configurações](~/ios/watchos/get-started/project-references.md) para você.

## <a name="creating-the-watch-user-interface"></a>Criando a Interface do usuário de inspeção

<a name="designer" />

### <a name="using-the-xamarin-ios-designer"></a>Usando o Designer do iOS Xamarin

Clique duas vezes no aplicativo de inspeção **Interface.storyboard** Editar usando o Designer do iOS. Você pode arrastar controles de interface do usuário e controladores de interface para o storyboard na **caixa de ferramentas** e configurá-los usando o **propriedades** painel:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![](installation-images/iosdesigner-sml.png "O storyboard no Designer")](installation-images/iosdesigner.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](installation-images/iosdesigner-sml-vs.png "O storyboard no Designer")](installation-images/iosdesigner-vs.png#lightbox)

-----

Você deve atribuir cada novo controlador de interface uma **classe** por selecionando-o e, em seguida, inserir o nome na **propriedades** pad (Isso criará o necessária C# code-behind arquivos automaticamente):

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![](installation-images/iosdesigner-classname.png "Fornecer uma classe de cada novo controlador de interface")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![](installation-images/iosdesigner-classname-vs.png "Fornecer uma classe de cada novo controlador de interface")

-----

Criar segues pela **Ctrl + arrastar** de um controlador de botão, a tabela ou a interface no outro controlador de interface.


### <a name="using-xcode-on-the-mac"></a>Usando o Xcode no Mac

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Você pode continuar a usar o Xcode para criar sua interface do usuário clicando duas vezes no arquivo Interface.storyboard e selecionando **abrir com > Interface Builder do Xcode**:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Usuários do Visual Studio também podem usar o Xcode para criar sua interface do usuário, alternando para usar o Host de Build do Mac diretamente.
Abra sua solução no Visual Studio para Mac e, em seguida, clique com botão direito no arquivo Interface.storyboard e selecione **abrir com > Interface Builder do Xcode**:

-----

![](installation-images/openwith-xcode.png "Abra o Interface.storyboard no Xcode Interface Builder")

Se o uso do Xcode, em seguida, você deve seguir as mesmas etapas para aplicativos do watch para normal [storyboards de aplicativo do iOS](~/ios/user-interface/storyboards/index.md) (como a criação de saídas e ações por **Ctrl + arrastar** para o **. h**arquivo de cabeçalho).

Quando você salva o storyboard no Xcode Interface Builder, ele adicionará automaticamente as saídas e ações que você cria para o C# **. designer.cs** arquivos no projeto de extensão de inspeção.


### <a name="adding-additional-screens-in-xcode"></a>Adicionando telas adicionais no Xcode

Quando você adiciona as telas adicionais (além do que está incluído no modelo por padrão) ao seu storyboard usando o Xcode Interface Builder **você deve adicionar manualmente o C# arquivos de código** para cada novo controlador de interface.

Consulte a [avançadas de instruções sobre como adicionar novos controladores de interface para um storyboard](~/ios/watchos/troubleshooting.md#add).

*O Designer do iOS Xamarin faz isso automaticamente, nenhuma etapa manual é necessária.*


## <a name="building"></a>Compilação

Cria um projeto que inclui um aplicativo de inspeção, como outros projetos do iOS. Processo de criação de resultará em um aplicativo de iPhone (. app) que contém uma extensão de inspeção (.appex), que por sua vez, contém o aplicativo de inspeção de código sem (. app).


## <a name="launching"></a>Iniciando

Você pode iniciar aplicativos do watch no simulador usando o Visual Studio para Mac ou Visual Studio (que começa em um Host de Build do Mac).

Há dois modos para iniciar um aplicativo WatchKit:

 - modo de aplicativo normal (padrão), e
- [Notificações](~/ios/watchos/platform/notifications.md) (que exige uma carga de notificação de teste no formato JSON).

### <a name="xcode-8-support"></a>Suporte ao Xcode 8

Depois de instalar o Xcode 8 (ou posterior), simuladores de Apple Watch são separados dos simuladores de iOS (diferentemente [Xcode 6](#xcode6), em que eles tivessem aparecido como um *vídeo externo*).
Quando você seleciona o projeto de aplicativo de inspeção e torná-lo o projeto de inicialização, mostrará a lista de simulador *simuladores de iOS* à sua escolha (como mostrado abaixo).

[![](installation-images/xs-xcode8-watchos3-sml.png "Selecionar o tipo de simulador")](installation-images/xs-xcode8-watchos3.png#lightbox)

Quando você iniciar a depuração *dois* simuladores deve começar - no iOS Simulator *e* o simulador de Apple Watch. Use **comando + Shift + H** para navegar até o menu e o relógio do mostrador; e usar o **Hardware** menu para definir o **força Touch pressão**. Rolagem no mouse ou trackpad simulará a usando o Crown Digital.

#### <a name="troubleshooting"></a>Solução de problemas

O seguinte erro aparecerá na **saída do aplicativo** se você tentar iniciar a um simulador de que não tem um watch emparelhado:

```csharp
error MT0000: Unexpected error - Please file a bug report at http://bugzilla.xamarin.com
error HE0020: Could not find a paired Watch device for the iOS device 'iPhone 6'.
```

Consulte a [fóruns da Apple](https://forums.developer.apple.com/thread/7783) para obter instruções sobre como configurar os simuladores, se os padrões não funcionam.


<a name="xcode6" />

### <a name="xcode-6-and-watchos-1"></a>O Xcode 6 e watchOS 1

Você deve fazer o *projeto de extensão de inspeção* as **projeto de inicialização** antes de executar ou depurar o aplicativo. Você não pode "Iniciar" o aplicativo de inspeção em si, e se você escolher o aplicativo do iOS, em seguida, ele será iniciado normalmente no simulador de iOS.

Por padrão, um aplicativo de inspeção é iniciado no modo normal **app** mode (modo não rapidamente ou notificações) do Visual Studio para Mac **execute** ou **depurar** comandos.

Ao usar o Xcode 6, somente o iPhone 5, iPhone 5S, iPhone 6 e iPhone 6 Plus pode ativar o monitor externo para uma **Apple Watch - 38mm** ou **Apple Watch - 42mm** onde estarão os aplicativos do watch exibido.

**Observação:** Lembre-se de que a tela de inspeção não aparecem automaticamente no simulador de iOS ao usar o Xcode 6.
Use o **Hardware > monitores externos** menu para mostrar a tela de inspeção.

<a name="custommodes" />

## <a name="launching-notification-mode"></a>Iniciar modo de notificação

Consulte a [página notificações](~/ios/watchos/platform/notifications.md) para obter informações como lidar com notificações no código.


O Visual Studio para Mac pode iniciar o aplicativo watch com uma notificação _modos de inicialização_ para notificações:



Clique com botão direito no projeto de aplicativo de inspeção e escolha **executar com > Configuração personalizada...** :


[![](installation-images/runwith-customparams-sml.png "Executar uma configuração personalizada")](installation-images/runwith-customparams.png#lightbox)


Isso abre o **parâmetros personalizados** janela onde você pode selecionar **notificação** (e fornecer uma carga JSON), em seguida, pressione **execute** para iniciar o aplicativo watch no simulador:


[![](installation-images/runwith-execargs-sml.png "Configurando a notificação e a carga")](installation-images/runwith-execargs.png#lightbox)



## <a name="debugging"></a>Depuração

Há suporte para a depuração no Visual Studio para Mac e Visual Studio.
Lembre-se fornecer um arquivo JSON de notificação durante a depuração no modo de notificações. Esta captura de tela mostra um ponto de interrupção de depuração está sendo atingido em um aplicativo de inspeção:

![](installation-images/debug-sml.png "Esta captura de tela mostra um ponto de interrupção de depuração está sendo atingido em um aplicativo de inspeção")

Depois de seguir as instruções de inicialização você acabará com seu aplicativo de inspeção executando o **iOS Simulator (Watch)**.
Para o modo de notificação, você pode selecionar **Depurar > Abrir Log do sistema** (**CMD + /**) e usar `Console.WriteLine` em seu código.

### <a name="debugging-lifecycle-event-handlers"></a>Manipuladores de eventos de ciclo de vida de depuração

<!--
To test the functionality in your  and 
    methods, use the **Hardware > Lock** command in the iOS Simulator.
    Locking will trigger the `DidDeactivate` method and the watch simulator
    will indicate that it has been locked. Swipe the iOS Simulator to unlock,
    which triggers the `WillActivate` method of the watch app.
-->

Os arquivos de modelo do watchOS (como `InterfaceController`, `ExtensionDelegate`, `NotificationController`, e `ComplicationController`) vêm com seus métodos de ciclo de vida necessários já implementados. Adicione `Console.WriteLine` chamadas e leia as **saída do aplicativo** para entender melhor o ciclo de vida do evento.



## <a name="related-links"></a>Links relacionados

- [WatchKitCatalog (amostra)](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [Primeiro vídeo de aplicativo de inspeção](http://blog.xamarin.com/your-first-watch-kit-app/)
- [Dicas de WatchKit da Apple](https://developer.apple.com/watchkit/tips/)
