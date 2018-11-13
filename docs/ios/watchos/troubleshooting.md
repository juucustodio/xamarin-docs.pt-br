---
title: watchOS solução de problemas
description: Este documento aborda problemas conhecidos e soluções alternativas para o desenvolvimento de watchOS com Xamarin. Ele descreve imagens com problemas, adicionar manualmente os arquivos do controlador de interface, iniciar um aplicativo de inspeção da linha de comando e muito mais.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 27C31DB8-451E-4888-BBC1-CE0DFC2F9DEC
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 7bfb0cad8bbc025bc5dba39abe3699ae77ee83ba
ms.sourcegitcommit: 7eed80186e23e6aff3ddbbf7ce5cd1fa20af1365
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/11/2018
ms.locfileid: "51526475"
---
# <a name="watchos-troubleshooting"></a>watchOS solução de problemas

Esta página contém informações adicionais e soluções alternativas para recursos ainda em desenvolvimento. Algumas dessas soluções alternativas se aplicam somente a nossas versões de visualização.

- [Problemas conhecidos](#knownissues)

- [Removendo o canal alfa de imagens de ícone](#noalpha)

- [Adicionar manualmente os arquivos do controlador de Interface](#add) para Xcode Interface Builder.

- [Iniciando o WatchApp da linha de comando](#command_line).

<a name="knownissues" />

## <a name="known-issues"></a>Problemas Conhecidos

### <a name="general"></a>Geral

<a name="deploy" />

- Versões anteriores do Visual Studio para Mac mostram incorretamente uma da **AppleCompanionSettings** ícones como sendo pixels de 88 x 88; o que resulta em uma **ausente o ícone de erro** se você tentar enviar para a App Store.
    Esse ícone deve ser 87 x 87 pixels (unidades de 29 para **@3x** telas de Retina). Você não pode corrigir isso no Visual Studio para Mac - editar o ativo de imagem no Xcode ou editar manualmente o **Contents.json** arquivo (para corresponder à [Este exemplo](https://github.com/xamarin/monotouch-samples/blob/master/WatchKit/WatchKitCatalog/WatchApp/Resources/Images.xcassets/AppIcons.appiconset/Contents.json#L126-L132)).

- Se o projeto de extensão de inspeção **Info. plist > ID do pacote de WKApp** não está [definidas corretamente](~/ios/watchos/get-started/project-references.md) para coincidir com o aplicativo de inspeção **ID do pacote**, o depurador não conseguirão se conectar e o Visual Studio para Mac deverão aguardar com a mensagem *"Aguardando o depurador conectar-se"*.

- Há suporte para depuração no **notificações** modo, mas pode não ser confiável. Tentar novamente, às vezes, funcionará. Confirme se o aplicativo de inspeção **Info. plist** `WKCompanionAppBundleIdentifier` é definido para corresponder ao identificador de pacote do aplicativo iOS pai/contêiner (ie. aquele que é executado no iPhone).

- Designer do iOS não mostra as setas de ponto de entrada para controladores de interface rapidamente ou notificação.

- Não é possível adicionar dois `WKNotificationControllers` a um storyboard.
    Solução alternativa: A `notificationCategory` elemento no XML de storyboard é sempre inserido com o mesmo `id`. Para contornar esse problema, você pode adicionar dois (ou mais) controladores de notificação, abra o arquivo de storyboard em um editor de texto e, em seguida, altere manualmente o `id` elemento a ser exclusivo.

    [![](troubleshooting-images/duplicate-id-sml.png "Abrindo o storyboard do arquivo em um editor de texto e alterar manualmente o elemento de id para serem exclusivos")](troubleshooting-images/duplicate-id.png#lightbox)

- Você verá um erro "o aplicativo não foi compilado" ao tentar iniciar o aplicativo. Isso ocorre depois que um **Clean** quando o projeto de inicialização é definido como o projeto de extensão de inspeção.
    A correção é selecionar **compilar > recompilar todos os** e, em seguida, iniciar o aplicativo novamente.

### <a name="visual-studio"></a>Visual Studio

O Designer do iOS dão suporte para o Kit de inspeção *requer* a solução estar configurado corretamente. Se as referências do projeto não estiverem definidas (consulte [como definir referências](~/ios/watchos/get-started/project-references.md)) e em seguida, a superfície de design não funcionará corretamente.

<a name="noalpha" />

## <a name="removing-the-alpha-channel-from-icon-images"></a>Removendo o canal alfa de imagens de ícone

Ícones não devem conter um canal alfa (o canal alfa define áreas transparentes de uma imagem), caso contrário, o aplicativo será rejeitado durante o envio da App Store com um erro semelhante a esta:

```csharp
Invalid Icon - The watch application '...watchkitextension.appex/WatchApp.app'
contains an icon file '...watchkitextension.appex/WatchApp.app/Icon-27.5@2x.png'
with an alpha channel. Icons should not have an alpha channel.
```

É fácil de remover o canal alfa no Mac OS X usando o **visualização** aplicativo:

1. Abra a imagem do ícone **versão prévia** e, em seguida, escolha **arquivo > Exportar**.

2. Caixa de diálogo exibida incluirá um **Alpha** caixa de seleção se houver um canal alfa.

    ![](troubleshooting-images/remove-alpha-sml.png "Caixa de diálogo exibida incluirá uma caixa de seleção de alfa, se houver um canal alfa")

3. *Untick* as **alfa** caixa de seleção e **salvar** o arquivo para o local correto.

4. A imagem do ícone agora deve passar verificações de validação da Apple.


<a name="add" />

## <a name="manually-adding-interface-controller-files"></a>Adicionar manualmente os arquivos do controlador de Interface

> [!IMPORTANT]
> Suporte de WatchKit do Xamarin inclui desenvolver watch storyboards no designer de iOS (no Visual Studio para Mac e Visual Studio), que não exige que as etapas descritas abaixo. Simplesmente fornecer um controlador de interface de um nome de classe no Visual Studio para preencher as propriedades do Mac e o C# arquivos de código serão criados automaticamente.


*Se* você estiver usando o Interface Builder do Xcode, siga estas etapas para criar novos controladores de interface para o aplicativo de inspeção e habilitar a sincronização com o Xcode para que as saídas e ações estão disponíveis no C#:

1. Abra o aplicativo de inspeção **Interface.storyboard** na **Xcode Interface Builder**.
    
    ![](troubleshooting-images/add-6.png "Abrindo o storyboard no Interface Builder do Xcode")

2. Arraste um novo `InterfaceController` até o storyboard:

    ![](troubleshooting-images/add-1.png "A InterfaceController")

3. Agora você pode arrastar controles para o controlador de interface (por exemplo. rótulos e botões), mas você não pode criar saídas ou ações ainda, porque não há nenhuma **. h** arquivo de cabeçalho. As etapas a seguir fará com que o necessário **. h** arquivo de cabeçalho a ser criado.

    ![](troubleshooting-images/add-2.png "Um botão no layout")

4. Feche o storyboard e retorne ao Visual Studio para Mac. Criar um novo C# arquivo **MyInterfaceController.cs** (ou qualquer nome desejado) na **Assista a extensão do aplicativo** projeto (não o aplicativo de inspeção em si, em que o storyboard é). Adicione o código a seguir (Atualizando o namespace, nome de classe e o nome do construtor):

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

5. Criar outro novo C# arquivo **MyInterfaceController.designer.cs** no **Assista a extensão do aplicativo** do projeto e adicione o código a seguir. Certifique-se de atualizar o namespace, o nome de classe e o `Register` atributo:

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
    
    Dica: Você pode (opcionalmente) transformar este arquivo em um nó filho do primeiro arquivo por arrastando-a para o outro C# arquivo no Visual Studio para Mac painel de soluções. Em seguida, ele será exibido como este:
    
    ![](troubleshooting-images/add-5.png "O painel de soluções")

6. Selecione **compilar > compilar tudo** para que a sincronização do Xcode reconhecerá a nova classe (por meio de `Register` atributo) que usamos.

7. Abra novamente o storyboard clicando duas vezes no arquivo de storyboard do aplicativo de inspeção e selecionando **abrir com > Interface Builder do Xcode**:

    ![](troubleshooting-images/add-6.png "Abrindo o storyboard no construtor de Interface")

8. Selecione o novo controlador de interface e dê a ele o nome da classe definido acima, por exemplo. `MyInterfaceController`.
Se algo funcionou corretamente, ele deve aparecer automaticamente na **classe:** lista suspensa e você pode selecioná-lo a partir daí.

    ![](troubleshooting-images/add-4.png "Definindo uma classe personalizada")

9. Escolha o **Editor assistente** exibir no Xcode (o ícone com dois círculos sobrepostos) para que você possa ver o storyboard e a código do lado a lado:

    ![](troubleshooting-images/add-7.png "O item de barra de ferramentas do Editor Assistente")

    Quando o foco estiver no painel de código, verifique se você examinar a **. h** arquivo de cabeçalho e, se não com o botão direito na barra de trilha e selecione o arquivo correto (**MyInterfaceController.h**)

    ![](troubleshooting-images/add-8.png "Selecione MyInterfaceController")

10. Agora você pode criar saídas e ações por **Ctrl + arrastar** usando o storyboard para o **. h** arquivo de cabeçalho.

    ![](troubleshooting-images/add-9.png "Criando saídas e ações")

    Quando você soltar a operação de arrastar, você será solicitado a selecionar se deseja criar uma saída ou uma ação e escolha seu nome:

    ![](troubleshooting-images/add-a.png "A saída e uma caixa de diálogo de ação")

11. Depois de salvar as alterações de storyboard e o Xcode está fechado, retorne ao Visual Studio para Mac. Ele detectará as alterações no arquivo de cabeçalho e adicionar automaticamente o código para o **. designer.cs** arquivo:


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


Agora você pode fazer referência ao controle (ou implementar a ação) em C#!


<a name="command_line" />

## <a name="launching-the-watch-app-from-the-command-line"></a>Iniciar o aplicativo de inspeção da linha de comando

> [!IMPORTANT]
> Você pode iniciar o aplicativo Watch no modo de aplicativo normal por padrão e também no **Glance** ou **notificação** modos usando [parâmetros de execução personalizado](~/ios/watchos/get-started/installation.md#custommodes) no Visual Studio para Mac e Visual Studio.


Você também pode usar a linha de comando para controlar o simulador de iOS. É a ferramenta de linha de comando usada para iniciar aplicativos do watch **mtouch**.

Aqui está um exemplo completo (executado como uma única linha no terminal):

```bash
/Library/Frameworks/Xamarin.iOS.framework/Versions/Current/bin/mtouch --sdkroot=/Applications/Xcode.app/Contents/Developer/ --device=:v2:runtime=com.apple.CoreSimulator.SimRuntime.iOS-8-2,devicetype=com.apple.CoreSimulator.SimDeviceType.iPhone-6
--launchsimwatch=/path/to/watchkitproject/watchsample/bin/iPhoneSimulator/Debug/watchsample.app
```

O parâmetro precisa ser atualizado para refletir seu aplicativo é `launchsimwatch`:

### <a name="--launchsimwatch"></a>--launchsimwatch

O caminho completo para o pacote de aplicativo principal *para o aplicativo iOS que contém o aplicativo de inspeção e a extensão*.

> [!NOTE]
> O caminho que você precisa fornecer é para o *arquivo. App do aplicativo de iPhone*, ou seja, aquele que será implantado para o simulador de iOS e que contém a extensão de inspeção e o aplicativo de inspeção.

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

O dispositivo do simulador para executar. Isso pode ser especificado de duas maneiras: usando o udid de um dispositivo específico, ou usando uma combinação de tipo de dispositivo e o tempo de execução.

Os valores exatos varia entre as máquinas e pode ser consultado por meio da Apple **simctl** ferramenta:

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

- [WatchKitCatalog (amostra)](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [WatchTables (amostra)](https://developer.xamarin.com/samples/monotouch/watchOS/WatchTables/)
