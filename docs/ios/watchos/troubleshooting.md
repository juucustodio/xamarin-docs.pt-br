---
title: watchOS solução de problemas
description: Este documento aborda problemas conhecidos e soluções alternativas para o desenvolvimento de watchOS com Xamarin. Ele descreve imagens com problemas, adicionar manualmente os arquivos do controlador de interface, iniciar um aplicativo de inspeção de linha de comando e muito mais.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 27C31DB8-451E-4888-BBC1-CE0DFC2F9DEC
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 4e84028336669738c40da9e37cd22f32ba11dfc1
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34791759"
---
# <a name="watchos-troubleshooting"></a>watchOS solução de problemas

Esta página contém informações adicionais e soluções alternativas para os recursos ainda em desenvolvimento. Algumas dessas soluções se aplicam apenas a nossas versões de visualização.

- [Problemas conhecidos](#knownissues)

- [Remover o canal alfa do ícone imagens](#noalpha)

- [Adicionar manualmente os arquivos do controlador de Interface](#add) para o construtor da Interface do Xcode.

- [Iniciando o WatchApp da linha de comando](#command_line).

<a name="knownissues" />

## <a name="known-issues"></a>Problemas Conhecidos

### <a name="general"></a>Geral

<a name="deploy" />

- Versões anteriores do Visual Studio para Mac incorretamente mostram uma da o **AppleCompanionSettings** ícones como sendo 88 x 88 pixels; o que resulta em uma **ausente o ícone de erro** se você tentar enviar para o aplicativo Repositório.
    Esse ícone deve ser 87 x 87 pixels (29 unidades para **@3x** telas de Retina). Você não pode corrigir isso no Visual Studio para Mac - editar o ativo de imagem no Xcode ou editar manualmente o **Contents.json** arquivo (para corresponder [Este exemplo](https://github.com/xamarin/monotouch-samples/blob/master/WatchKit/WatchKitCatalog/WatchApp/Resources/Images.xcassets/AppIcons.appiconset/Contents.json#L126-L132)).

- Se o projeto de extensão de inspeção **Info. plist > ID do pacote WKApp** não é [definida corretamente](~/ios/watchos/get-started/project-references.md) para coincidir com o aplicativo de inspeção **ID do pacote**, o depurador não poderá se conectar e Visual Studio para Mac aguardará com a mensagem *"Aguardando o depurador conectar-se"*.

- Há suporte para depuração em **notificações** modo, mas pode não ser confiáveis. Repetir algumas vezes funcionará. Confirme se o aplicativo de inspeção **Info. plist** `WKCompanionAppBundleIdentifier` é definido para corresponder o identificador de pacote do aplicativo de contêiner de pai/iOS (ie. aquele que é executado no iPhone).

- iOS Designer não Mostrar setas de ponto de entrada para os controladores de interface de visão ou notificação.

- Não é possível adicionar dois `WKNotificationControllers` para um storyboard.
    Solução alternativa: A `notificationCategory` elemento no storyboard XML sempre é inserido com o mesmo `id`. Para contornar esse problema, você pode adicionar duas (ou mais) controladores de notificação, abra o arquivo de storyboard em um editor de texto e alterar manualmente o `id` elemento seja exclusivo.

    [![](troubleshooting-images/duplicate-id-sml.png "Abrindo o storyboard do arquivo em um editor de texto e altere manualmente o elemento de id para ser exclusivo")](troubleshooting-images/duplicate-id.png#lightbox)

- Você pode ver um erro "o aplicativo não foi construído" ao tentar iniciar o aplicativo. Isso ocorre após um **limpar** quando o projeto de inicialização está definido como o projeto de extensão de inspeção.
    A correção é selecionar **Build > recriar todos** e, em seguida, inicie o aplicativo novamente.

### <a name="visual-studio"></a>Visual Studio

Suporte as iOS Designer para inspecionar Kit *requer* a solução deve ser configurado corretamente. Se as referências do projeto não estão definidas (consulte [como definir referências](~/ios/watchos/get-started/project-references.md)), em seguida, a superfície de design não funcionará corretamente.

<a name="noalpha" />

## <a name="removing-the-alpha-channel-from-icon-images"></a>Remover o canal alfa do ícone imagens

Ícones não devem conter um canal alfa (o canal alfa define áreas transparentes de uma imagem), caso contrário, o aplicativo será rejeitado durante o envio da loja de aplicativos com um erro semelhante a este:

```csharp
Invalid Icon - The watch application '...watchkitextension.appex/WatchApp.app'
contains an icon file '...watchkitextension.appex/WatchApp.app/Icon-27.5@2x.png'
with an alpha channel. Icons should not have an alpha channel.
```

É fácil de remover o canal alfa no Mac OS X usando o **visualização** aplicativo:

1. Abrir a imagem do ícone no **visualização** e, em seguida, escolha **arquivo > Exportar**.

2. Caixa de diálogo exibida incluirá um **Alpha** caixa de seleção se houver um canal alfa.

    ![](troubleshooting-images/remove-alpha-sml.png "A caixa de diálogo que aparece inclui uma caixa de seleção alfa se houver um canal alfa")

3. *Untick* o **Alpha** caixa de seleção e **salvar** o arquivo para o local correto.

4. A imagem do ícone agora deve passar verificações de validação da Apple.


<a name="add" />

## <a name="manually-adding-interface-controller-files"></a>Adicionar manualmente os arquivos do controlador de Interface

> [!IMPORTANT]
> Suporte a WatchKit do Xamarin inclui criando storyboards inspecionar no designer de iOS (no Visual Studio para Mac e Visual Studio), que não requer as etapas descritas abaixo. Simplesmente dar a um controlador de interface de um nome de classe no Visual Studio para o painel de propriedades de Mac e os arquivos de código c# serão criados automaticamente.


*Se* você estiver usando o construtor de Interface do Xcode, siga estas etapas para criar novos controladores de interface para seu aplicativo de inspeção e habilitar a sincronização com o Xcode para que as saídas e as ações estão disponíveis em c#:

1. Abra o aplicativo de inspeção **Interface.storyboard** na **Xcode Interface construtor**.
    
    ![](troubleshooting-images/add-6.png "Abrindo o storyboard no Xcode Interface Builder")

2. Arraste um novo `InterfaceController` para o storyboard:

    ![](troubleshooting-images/add-1.png "A InterfaceController")

3. Agora você pode arrastar controles para o controlador de interface (por exemplo. botões e rótulos), mas você não pode criar saídas ou ações ainda, porque não há nenhum **. h** arquivo de cabeçalho. As etapas a seguir fará com que o necessário **. h** arquivo de cabeçalho a ser criado.

    ![](troubleshooting-images/add-2.png "Um botão no layout")

4. Feche o storyboard e retornar ao Visual Studio para Mac. Criar um novo arquivo c# **MyInterfaceController.cs** (ou qualquer nome desejado) no **Assista a extensão do aplicativo** projeto (não o aplicativo de inspeção em si, onde é o storyboard). Adicione o seguinte código (Atualizando o namespace, nome da classe e o nome do construtor):

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

5. Criar outro novo arquivo c# **MyInterfaceController.designer.cs** no **Assista a extensão do aplicativo** do projeto e adicione o código abaixo. Certifique-se de atualizar o namespace, o nome da classe e o `Register` atributo:

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
    
    Dica: (Opcionalmente) que este arquivo um nó filho do primeiro arquivo arrastando-o para o outro arquivo c# no Visual Studio para Mac solução teclado. Em seguida, ele será exibido como este:
    
    ![](troubleshooting-images/add-5.png "O preenchimento de solução")

6. Selecione **criar > compilar todos os** para que a sincronização do Xcode reconhecerá a nova classe (por meio de `Register` atributo) que foram usados.

7. Abra novamente o storyboard clicando duas vezes no arquivo de storyboard do aplicativo de inspeção e selecionando **abrir com > Xcode Interface Builder**:

    ![](troubleshooting-images/add-6.png "Abrindo o storyboard no construtor de Interface")

8. Selecione o novo controlador de interface e dê a ele o nome da classe definido acima, por exemplo. `MyInterfaceController`.
Se tudo funcionou corretamente, ele deve aparecer em automaticamente o **classe:** lista suspensa e selecione-a de lá.

    ![](troubleshooting-images/add-4.png "Definir uma classe personalizada")

9. Escolha o **Assistente Editor** exibir no Xcode (o ícone com dois círculos sobrepostos) para que você possa ver o storyboard e a código lado a lado:

    ![](troubleshooting-images/add-7.png "O item de barra de ferramentas do Editor de Assistente")

    Quando o foco estiver no painel de código, certifique-se que você examine o **. h** arquivo de cabeçalho e se não com o botão direito na barra de navegação de trilha e selecione o arquivo correto (**MyInterfaceController.h**)

    ![](troubleshooting-images/add-8.png "Selecione MyInterfaceController")

10. Agora você pode criar saídas e as ações por **Ctrl + arrastar** do storyboard para o **. h** arquivo de cabeçalho.

    ![](troubleshooting-images/add-9.png "Criando ações e saídas")

    Quando você solta a operação de arrastar, você será solicitado a selecionar se deseja criar uma tomada ou uma ação e escolha o seu nome:

    ![](troubleshooting-images/add-a.png "A loja e uma caixa de diálogo de ação")

11. Depois que as alterações de storyboard são salvas e Xcode é fechado, retorne ao Visual Studio para Mac. Ele detectará as alterações de arquivo de cabeçalho e adiciona automaticamente o código para o **. designer.cs** arquivo:


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


Agora você pode fazer referência ao controle (ou implementar a ação) em c#!


<a name="command_line" />

## <a name="launching-the-watch-app-from-the-command-line"></a>Iniciando o aplicativo de inspeção da linha de comando

> [!IMPORTANT]
> Você pode iniciar o aplicativo de inspeção em modo normal do aplicativo por padrão e também em **visão** ou **notificação** modos usando [parâmetros de execução personalizado](~/ios/watchos/get-started/installation.md#custommodes) no Visual Studio para Mac e O Visual Studio.


Você também pode usar a linha de comando para controlar o simulador de iOS. É a ferramenta de linha de comando usada para iniciar aplicativos inspecionar **mtouch**.

Aqui está um exemplo completo (executado como uma única linha no terminal):

```bash
/Library/Frameworks/Xamarin.iOS.framework/Versions/Current/bin/mtouch --sdkroot=/Applications/Xcode.app/Contents/Developer/ --device=:v2:runtime=com.apple.CoreSimulator.SimRuntime.iOS-8-2,devicetype=com.apple.CoreSimulator.SimDeviceType.iPhone-6
--launchsimwatch=/path/to/watchkitproject/watchsample/bin/iPhoneSimulator/Debug/watchsample.app
```

O parâmetro é necessário atualizar para refletir seu aplicativo é `launchsimwatch`:

### <a name="--launchsimwatch"></a>--launchsimwatch

O caminho completo para o pacote de aplicativo principal *para o aplicativo do iOS que contém o aplicativo de inspeção e a extensão*.

> [!NOTE]
> É o caminho que você precisa fornecer para o *arquivo de App de aplicativo do iPhone*, ou seja, aquele que será implantado para o simulador de iOS e que contém a extensão de inspeção e o aplicativo watch.

Exemplo:

```bash
--launchsimwatch=/path/to/watchkitproject/watchsample/bin/iPhoneSimulator/Debug/watchsample.app
```


## <a name="notification-mode"></a>Modo de notificação

Para testar o aplicativo [ **notificação** modo](~/ios/watchos/platform/notifications.md), defina o `watchlaunchmode` parâmetro `Notification` e fornecer um caminho para um arquivo JSON que contém uma carga de notificação de teste.

O parâmetro de carga é *necessária* para o modo de notificação.

Por exemplo, adicione esses argumentos para o comando mtouch:

```bash
--watchlaunchmode=Notification --watchnotificationpayload=/path/to/file.json
```


## <a name="other-arguments"></a>Outros argumentos

Os argumentos restantes são explicados abaixo:

### <a name="--sdkroot"></a>--sdkroot

Necessário. Especifica o caminho para o Xcode (6.2 ou posterior).

Exemplo:

```bash
 --sdkroot /Applications/Xcode.app/Contents/Developer/
```

### <a name="--device"></a>– dispositivo

O dispositivo do simulador para executar. Isso pode ser especificado de duas maneiras: usando udid de um dispositivo específico, ou usando uma combinação de tipo de tempo de execução e o dispositivo.

Os valores exatos varia entre computadores e pode ser consultado por meio da Apple **simctl** ferramenta:

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



## <a name="related-links"></a>Links relacionados

- [WatchKitCatalog (exemplo)](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [WatchTables (exemplo)](https://developer.xamarin.com/samples/monotouch/watchOS/WatchTables/)
