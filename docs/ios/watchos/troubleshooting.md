---
title: Solução de problemas do watchOS
description: Este documento discute problemas conhecidos e soluções alternativas para o desenvolvimento de watchOS com o Xamarin. Ele descreve imagens com problemas, adicionar manualmente os arquivos do controlador de interface, iniciar um aplicativo de inspeção na linha de comando e muito mais.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 27C31DB8-451E-4888-BBC1-CE0DFC2F9DEC
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 7e56eed866cb647bd654370d587b02bcaba04d4e
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91432811"
---
# <a name="watchos-troubleshooting"></a>Solução de problemas do watchOS

Esta página contém informações adicionais e soluções alternativas para os problemas que você pode encontrar.

- [Problemas conhecidos](#knownissues)

- [Removendo o canal alfa de imagens de ícone](#noalpha)

- [Adicionando manualmente os arquivos do controlador de interface](#add) para o Xcode Interface Builder.

- [Iniciando o WatchApp da linha de comando](#command_line).

<a name="knownissues"></a>

## <a name="known-issues"></a>Problemas conhecidos

### <a name="general"></a>Geral

<a name="deploy"></a>

- Versões anteriores do Visual Studio para Mac mostram incorretamente um dos ícones de **AppleCompanionSettings** como sendo 88x88 pixels; que resulta em um **erro de ícone ausente** se você tentar enviar para a loja de aplicativos.
    Esse ícone deve ser 87x87 pixels (29 unidades para **@3x** telas da retina). Não é possível corrigir isso em Visual Studio para Mac-edite o ativo da imagem no Xcode ou edite manualmente o **Contents.jsno** arquivo.

- Se o info. plist do projeto de extensão de inspeção **> ID do pacote WKApp** não estiver [definido corretamente](~/ios/watchos/get-started/project-references.md) para corresponder à **ID do pacote**do aplicativo de inspeção, o depurador não conseguirá se conectar e Visual Studio para Mac aguardará a mensagem *"aguardando o depurador para se conectar"*.

- A depuração tem suporte no modo de **notificações** , mas pode ser não confiável. Tentar novamente, às vezes, funcionará. Confirme se o **info. plist** do aplicativo de inspeção `WKCompanionAppBundleIdentifier` está definido para corresponder ao identificador do pacote do aplicativo pai/contêiner do IOS (ou seja, aquele que é executado no iPhone).

- o iOS designer não mostra as setas de ponto de entrada para os controladores da interface de notificação e resumido.

- Você não pode adicionar dois `WKNotificationControllers` a um Storyboard.
    Solução alternativa: o `notificationCategory` elemento no XML do storyboard é sempre inserido com o mesmo `id` . Para contornar esse problema, você pode adicionar dois (ou mais) controladores de notificação, abrir o arquivo de storyboard em um editor de texto e alterar manualmente o `id` elemento para ser exclusivo.

    [![Abrir o arquivo de storyboard em um editor de texto e alterar manualmente o elemento de ID para ser exclusivo](troubleshooting-images/duplicate-id-sml.png)](troubleshooting-images/duplicate-id.png#lightbox)

- Você pode ver um erro "o aplicativo não foi criado" ao tentar iniciar o aplicativo. Isso ocorre depois de uma **limpeza** quando o projeto de inicialização é definido como o projeto de extensão de inspeção.
    A correção é selecionar **compilar > Recompilar tudo** e, em seguida, reiniciar o aplicativo.

### <a name="visual-studio"></a>Visual Studio

O suporte ao designer do iOS para o kit de inspeção *requer* que a solução seja configurada corretamente. Se as referências do projeto não estiverem definidas (consulte [como definir referências](~/ios/watchos/get-started/project-references.md)), a superfície de design não funcionará corretamente.

<a name="noalpha"></a>

## <a name="removing-the-alpha-channel-from-icon-images"></a>Removendo o canal alfa de imagens de ícone

Os ícones não devem conter um canal alfa (o canal alfa define áreas transparentes de uma imagem); caso contrário, o aplicativo será rejeitado durante o envio da loja de aplicativos com um erro semelhante a este:

```csharp
Invalid Icon - The watch application '...watchkitextension.appex/WatchApp.app'
contains an icon file '...watchkitextension.appex/WatchApp.app/Icon-27.5@2x.png'
with an alpha channel. Icons should not have an alpha channel.
```

É fácil remover o canal alfa em Mac OS X usando o aplicativo de **Visualização** :

1. Abra a imagem do ícone na **Visualização** e escolha **arquivo > exportar**.

2. O diálogo que aparece incluirá uma caixa de seleção **alfa** se um canal alfa estiver presente.

    ![O diálogo que aparece incluirá uma caixa de seleção alfa, se um canal alfa estiver presente](troubleshooting-images/remove-alpha-sml.png)

3. *Desmarque* a caixa de seleção **alfa** e **salve** o arquivo no local correto.

4. A imagem do ícone agora deve passar nas verificações de validação da Apple.

<a name="add"></a>

## <a name="manually-adding-interface-controller-files"></a>Adicionando manualmente os arquivos do controlador de interface

> [!IMPORTANT]
> O suporte do WatchKit da Xamarin inclui o design de storyboards de inspeção no designer do iOS (no Visual Studio para Mac e no Visual Studio), que não requer as etapas descritas abaixo. Basta dar a um controlador de interface um nome de classe no painel de propriedades Visual Studio para Mac e os arquivos de código C# serão criados automaticamente.

*Se* você estiver usando o Interface Builder do Xcode, siga estas etapas para criar novos controladores de interface para seu aplicativo Watch e habilitar a sincronização com o Xcode para que as saídas e ações estejam disponíveis em C#:

1. Abra a interface do aplicativo Watch **. Storyboard** no **Xcode Interface Builder**.

    ![Abrindo o storyboard no Xcode Interface Builder](troubleshooting-images/add-6.png)

2. Arraste um novo `InterfaceController` no storyboard:

    ![Um InterfaceController](troubleshooting-images/add-1.png)

3. Agora você pode arrastar controles para o controlador de interface (por exemplo, rótulos e botões), mas você não pode criar saídas ou ações ainda, porque não há nenhum arquivo de cabeçalho **. h** . As etapas a seguir farão com que o arquivo de cabeçalho **. h** necessário seja criado.

    ![Um botão no layout](troubleshooting-images/add-2.png)

4. Feche o storyboard e retorne para Visual Studio para Mac. Crie um novo arquivo C# **MyInterfaceController.cs** (ou qualquer nome que desejar) no projeto de **extensão do aplicativo Watch** (não o próprio aplicativo Watch onde o storyboard é). Adicione o seguinte código (Atualizando o namespace, ClassName e o nome do Construtor):

    ```csharp
    using System;
    using WatchKit;
    using Foundation;

    namespace WatchAppExtension  // remember to update this
    {
        public partial class MyInterfaceController // remember to update this
        : WKInterfaceController
        {
            public MyInterfaceController // remember to update this
            (IntPtr handle) : base (handle)
            {
            }
            public override void Awake (NSObject context)
            {
                base.Awake (context);
                // Configure interface objects here.
                Console.WriteLine ("{0} awake with context", this);
            }
            public override void WillActivate ()
            {
                // This method is called when the watch view controller is about to be visible to the user.
                Console.WriteLine ("{0} will activate", this);
            }
            public override void DidDeactivate ()
            {
                // This method is called when the watch view controller is no longer visible to the user.
                Console.WriteLine ("{0} did deactivate", this);
            }
        }
    }
    ```

5. Crie outro novo arquivo C# **MyInterfaceController.designer.cs** no projeto de **extensão do aplicativo Watch** e adicione o código abaixo. Certifique-se de atualizar o namespace, o nome de classe e o `Register` atributo:

    ```csharp
    using Foundation;
    using System.CodeDom.Compiler;

    namespace HelloWatchExtension  // remember to update this
    {
        [Register ("MyInterfaceController")] // remember to update this
        partial class MyInterfaceController  // remember to update this
        {
            void ReleaseDesignerOutlets ()
            {
            }
        }
    }
    ```

    > [!TIP]
    > Você pode (opcionalmente) tornar esse arquivo um nó filho do primeiro arquivo arrastando-o para o outro arquivo C# na Painel de Soluções de Visual Studio para Mac. Ele será exibido assim:

    ![O painel de solução](troubleshooting-images/add-5.png)

6. Selecione **compilar > compilar tudo** para que a sincronização do Xcode reconheça a nova classe (por meio do `Register` atributo) que usamos.

7. Abra novamente o storyboard clicando com o botão direito do mouse no arquivo de storyboard do aplicativo de inspeção e selecionando  **abrir com > Xcode Interface Builder**:

    ![Abrindo o storyboard em Interface Builder](troubleshooting-images/add-6.png)

8. Selecione o novo controlador de interface e dê a ele o ClassName que você definiu acima, por exemplo, `MyInterfaceController`.
    Se tudo funcionou corretamente, ele deverá aparecer automaticamente na lista suspensa **classe:** e você poderá selecioná-lo a partir daí.

    ![Definindo uma classe personalizada](troubleshooting-images/add-4.png)

9. Escolha o modo de exibição **Editor do assistente** no Xcode (o ícone com dois círculos sobrepostos) para que você possa ver o storyboard e o código lado a lado:

    ![O item da barra de ferramentas do editor do assistente](troubleshooting-images/add-7.png)

    Quando o foco estiver no painel de código, verifique se você está olhando para o arquivo de cabeçalho  **. h** e, se não clicar com o botão direito do mouse na barra de navegação estrutural e selecionar o arquivo correto (**MyInterfaceController. h**)

    ![Selecionar MyInterfaceController](troubleshooting-images/add-8.png)

10. Agora você pode criar saídas e ações por **Ctrl + arraste** do storyboard para o arquivo de cabeçalho **. h** .

    ![Criando saídas e ações](troubleshooting-images/add-9.png)

    Ao liberar o recurso arrastar, você será solicitado a selecionar se deseja criar uma tomada ou uma ação e escolher seu nome:

    ![A caixa de diálogo tomada e ação](troubleshooting-images/add-a.png)

11. Depois que as alterações do storyboard forem salvas e o Xcode estiver fechado, volte para Visual Studio para Mac. Ele detectará as alterações do arquivo de cabeçalho e adicionará automaticamente o código ao arquivo **. designer.cs** :

    ```csharp
    [Register ("MyInterfaceController")]
    partial class MyInterfaceController
    {
        [Outlet]
        WatchKit.WKInterfaceButton myButton { get; set; }

        void ReleaseDesignerOutlets ()
        {
            if (myButton != null) {
                myButton.Dispose ();
                myButton = null;
            }
        }
    }
    ```

Agora você pode referenciar o controle (ou implementar a ação) em C#!

<a name="command_line"></a>

## <a name="launching-the-watch-app-from-the-command-line"></a>Iniciando o aplicativo Watch na linha de comando

> [!IMPORTANT]
> Você pode iniciar o aplicativo Watch no modo de aplicativo normal por padrão e também em modos de **visão geral** ou de **notificação** usando [parâmetros de execução personalizados](~/ios/watchos/get-started/installation.md#custommodes) no Visual Studio para Mac e no Visual Studio.

Você também pode usar a linha de comando para controlar o simulador de iOS. A ferramenta de linha de comando usada para iniciar aplicativos de inspeção é **mTouch**.

Aqui está um exemplo completo (executado como uma única linha no terminal):

```bash
/Library/Frameworks/Xamarin.iOS.framework/Versions/Current/bin/mtouch --sdkroot=/Applications/Xcode.app/Contents/Developer/ --device=:v2:runtime=com.apple.CoreSimulator.SimRuntime.iOS-8-2,devicetype=com.apple.CoreSimulator.SimDeviceType.iPhone-6
--launchsimwatch=/path/to/watchkitproject/watchsample/bin/iPhoneSimulator/Debug/watchsample.app
```

O parâmetro que você precisa atualizar para refletir seu aplicativo é `launchsimwatch` :

### <a name="--launchsimwatch"></a>--launchsimwatch

O caminho completo para o pacote do aplicativo principal *para o aplicativo IOS que contém o aplicativo e a extensão Watch*.

> [!NOTE]
> O caminho que você precisa fornecer é para o *arquivo. app do aplicativo do iPhone*, ou seja, aquele que será implantado no simulador do IOS e que contém a extensão de inspeção e o aplicativo de inspeção.

Exemplo:

```bash
--launchsimwatch=/path/to/watchkitproject/watchsample/bin/iPhoneSimulator/Debug/watchsample.app
```

## <a name="notification-mode"></a>Modo de notificação

Para testar o modo de [ **notificação** ](~/ios/watchos/platform/notifications.md)do aplicativo, defina o `watchlaunchmode` parâmetro como `Notification` e forneça um caminho para um arquivo JSON que contenha uma carga de notificação de teste.

O parâmetro payload é *necessário* para o modo de notificação.

Por exemplo, adicione estes argumentos ao comando mTouch:

```bash
--watchlaunchmode=Notification --watchnotificationpayload=/path/to/file.json
```

## <a name="other-arguments"></a>Outros argumentos

Os argumentos restantes são explicados abaixo:

### <a name="--sdkroot"></a>--sdkroot

Obrigatórios. Especifica o caminho para o Xcode (6,2 ou posterior).

Exemplo:

```bash
 --sdkroot /Applications/Xcode.app/Contents/Developer/
```

### <a name="--device"></a>--dispositivo

O dispositivo simulador a ser executado. Isso pode ser especificado de duas maneiras, usando o UDID de um dispositivo específico ou usando uma combinação de tempo de execução e tipo de dispositivo.

Os valores exatos variam entre os computadores e podem ser consultados usando a ferramenta **simctl** da Apple:

```bash
/Applications/Xcode.app/Contents/Developer/usr/bin/simctl list
```

**UDID**

Exemplo:

```bash
--device=:v2:udid=AAAAAAA-BBBB-CCCC-DDDD-EEEEEEEEEEEE
```

**Tempo de execução e tipo de dispositivo**

Exemplo:

```bash
--device=:v2:runtime=com.apple.CoreSimulator.SimRuntime.iOS-8-2,devicetype=com.apple.CoreSimulator.SimDeviceType.iPhone-6
```

## <a name="related-links"></a>Links Relacionados

- [WatchKitCatalog (exemplo)](/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [WatchTables (exemplo)](/samples/xamarin/ios-samples/watchos-watchtables)