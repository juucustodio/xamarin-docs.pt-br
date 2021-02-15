---
title: Olá, iOS – Guia de Início Rápido
description: Este passo a passo demonstra como criar um aplicativo Xamarin.iOS simples chamado Olá, iOS. Dessa forma, ele apresenta as ferramentas fundamentais e os conceitos que devem ser compreendidos para criar aplicativos Xamarin.iOS.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: D3868F3A-4EED-BDDF-45AA-665102C39634
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 10/05/2018
ms.openlocfilehash: da84a7faf2e08f35d364d7301102027342792f61
ms.sourcegitcommit: 01ccefd54c0ced724784dbe1aec9ecfc9b00e633
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92630251"
---
# <a name="hello-ios--quickstart"></a>Olá, iOS – Guia de Início Rápido

Este guia descreve como criar um aplicativo que converte um número de telefone alfanumérico inserido pelo usuário em numérico e, em seguida, chama esse número. O aplicativo final tem esta aparência:

 [![O aplicativo de início rápido Hello. iOS](hello-ios-quickstart-images/image1.png)](hello-ios-quickstart-images/image1.png#lightbox)

## <a name="requirements"></a>Requisitos

O desenvolvimento do iOS com o Xamarin requer:

- Um Mac executando o macOS High Sierra (10.13) ou superior.
- A versão mais recente do SDK do iOS e do Xcode instalada da [App Store](https://itunes.apple.com/us/app/xcode/id497799835?mt=12).

::: zone pivot="macos"

O Xamarin.iOS funciona com a seguinte configuração:

- Versão mais recente do Visual Studio para Mac que atenda às especificações acima.

O [guia de Instalação do Mac do Xamarin.iOS](~/ios/get-started/installation/mac.md) está disponível para instruções de instalação passo a passo

::: zone-end
::: zone pivot="windows"

O Xamarin.iOS funciona com a seguinte configuração:

- A versão mais recente do Visual Studio 2019 ou Visual Studio 2017 Community, Professional ou Enterprise no Windows 10, emparelhada com um host de build do Mac que atenda às especificações acima.

O [Guia de instalação do Xamarin.iOS no Windows](~/ios/get-started/installation/windows/index.md) está disponível para fornecer instruções de instalação passo a passo.

::: zone-end

Antes de começar, baixe o [Conjunto de ícones do Aplicativo Xamarin](https://github.com/xamarin/ios-samples/blob/master/Hello_iOS/Resources/XamarinAppIconsandLaunchImages.zip?raw=true).

::: zone pivot="macos"

## <a name="visual-studio-for-mac-walkthrough"></a>Passo a passo do Visual Studio para Mac

Este passo a passo descreve como criar um aplicativo chamado Phoneword que converte um número de telefone alfanumérico em um número de telefone numérico.

1. Inicie o Visual Studio para Mac na pasta **Aplicativos** ou no **Destaque** :

    ![A tela de Inicialização](hello-ios-quickstart-images/image2new.png)

    Na Tela de Inicialização, clique em **Novo Projeto...** para criar uma nova solução do Xamarin.iOS:

    ![Solução de iOS](hello-ios-quickstart-images/image3new.png)

2. Na **caixa de diálogo Nova Solução** , escolha o modelo **iOS > Aplicativo > Aplicativo de Exibição Única** , garantindo que C# esteja selecionado. Clique em **Avançar** :

    ![Escolha o aplicativo de exibição única](hello-ios-quickstart-images/image4new.png)

3. Configure o aplicativo. Dê a ele o **Nome** `Phoneword_iOS` e deixe o resto como padrão. Clique em **Avançar** :

    ![Insira o nome do aplicativo](hello-ios-quickstart-images/image5new.png)

4. Deixe o Projeto e o Nome da Solução como está. Escolha o local do projeto aqui ou mantenha-o como padrão:

    ![Escolha o local do projeto](hello-ios-quickstart-images/image6new.png)

5. Clique em **Criar** para criar a **Solução** .

6. Abra o arquivo **Main.storyboard** clicando duas vezes no **Painel de Solução** . Certifique-se de abrir o arquivo usando o Visual Studio iOS designer (clique com o botão direito do mouse no storyboard e selecione **abrir com > o designer do IOS** ). Isso oferece uma maneira de criar visualmente uma interface do usuário:

    ![O Designer de iOS](hello-ios-quickstart-images/image7new.png)

    Observe que as _classes de tamanho_ são habilitadas por padrão. Consulte o guia [Storyboards Unificados](~/ios/user-interface/storyboards/unified-storyboards.md) para saber mais sobre elas.

7. No **Painel de Caixa de Ferramentas** , digite "rótulo" na barra de pesquisa e arraste um **Rótulo** para a superfície de design (a área no centro):

    ![Arraste um Rótulo para a área da superfície de design no centro](hello-ios-quickstart-images/image8new.png)

    > [!NOTE]
    > Você pode abrir o painel **Propriedades** ou a **Caixa de Ferramentas** a qualquer momento navegando para **Exibir > Painéis** .

8. Pegue as alças dos *Controles de Arraste* (os círculos ao redor do controle) e torne o rótulo mais largo:

    ![Torne o rótulo mais largo](hello-ios-quickstart-images/image9.png)

9. Com o **Rótulo** selecionado na superfície de design, use o **Painel de Propriedades** para alterar a propriedade **Texto** do **Rótulo** para “Inserir um Phoneword:”

    ![Defina o rótulo para Inserir um Phoneword](hello-ios-quickstart-images/image10.png)

10. Pesquise “campo de texto” dentro da Caixa de Ferramentas e arraste um **Campo de Texto** da **Caixa de Ferramentas** para a superfície de design e coloque-o no **Rótulo** . Ajuste a largura até que o **campo de texto** tenha a mesma largura que o **rótulo** :

    ![Deixe o Campo de texto com a mesma largura que o Rótulo](hello-ios-quickstart-images/image12new.png)

11. Com o **Campo de Texto** selecionado na superfície de design, no **Campo de Texto** , altere a propriedade **Nome** na seção Identidade do **Painel de Propriedades** para `PhoneNumberText` e altere a propriedade **Texto** para "1-855-XAMARIN":

    ![Altere a propriedade Título para 1-855-XAMARIN](hello-ios-quickstart-images/image13new.png)

12. Arraste um **Botão** da **Caixa de Ferramentas** para a superfície de design e coloque-o no **Campo de Texto** . Ajuste a largura para que o **botão** seja tão largo quanto o **campo de texto** e o **rótulo** :

    ![Ajuste a largura para que o botão tenha a mesma largura que o Campo de texto e o Rótulo](hello-ios-quickstart-images/image14new.png)

13. Com o **Botão** selecionado na superfície de design, altere a propriedade **Nome** na seção **Identidade** do **Painel de Propriedades** para `TranslateButton`. Altere a propriedade **title** para "translate":

    ![Altere a propriedade Título para Traduzir](hello-ios-quickstart-images/image15new.png)

14. Repita as duas etapas acima e arraste um **Botão** de **Caixa de Ferramentas** para a superfície de design e coloque-o sob o primeiro **Botão** . Ajuste a largura para que o **botão** seja tão largo quanto o primeiro **botão** :

    ![Ajuste a largura para que o botão tenha a mesma largura que o primeiro botão](hello-ios-quickstart-images/image16new.png)

15. Com o segundo **Botão** selecionado na superfície de design, altere a propriedade **Nome** na seção **Identidade** do **Painel de Propriedades** para `CallButton`. Altere a propriedade **title** para "Call":

    ![Altere a propriedade Título para Chamar](hello-ios-quickstart-images/image17new.png)

    Salve as alterações navegando até **Arquivo > Salvar** ou pressionando **⌘ + s** .

16. Alguma lógica precisa ser adicionada ao aplicativo para converter números de telefone de alfanuméricos para numéricos. Adicione um novo arquivo ao projeto clicando com o botão direito do mouse no projeto **Phoneword_iOS** no **Painel de Soluções** e escolhendo **Adicionar > Novo Arquivo...** ou pressionando **⌘ + n** :

    ![Adicione um novo arquivo ao projeto](hello-ios-quickstart-images/image18.png)

17. Na caixa de diálogo **Novo Arquivo** , selecione **Geral > Classe Vazia** e nomeie o novo arquivo `PhoneTranslator`:

    ![Selecione a Classe vazia e dê um nome ao novo arquivo PhoneTranslator](hello-ios-quickstart-images/image19.png)

18. Isso cria uma nova classe C# vazia para nós. Remova todo o código de modelo e substitua-o pelo código a seguir:

    ```csharp
    using System.Text;
    using System;

    namespace Phoneword_iOS
    {
        public static class PhoneTranslator
        {
            public static string ToNumber(string raw)
            {
                if (string.IsNullOrWhiteSpace(raw)) {
                    return "";
                } else {
                    raw = raw.ToUpperInvariant();
                }

                var newNumber = new StringBuilder();
                foreach (var c in raw)
                {
                    if (" -0123456789".Contains(c)) {
                        newNumber.Append(c);
                    } else {
                        var result = TranslateToNumber(c);
                        if (result != null) {
                            newNumber.Append(result);
                        }
                    }
                    // otherwise we've skipped a non-numeric char
                }
                return newNumber.ToString();
            }

            static bool Contains (this string keyString, char c)
            {
                return keyString.IndexOf(c) >= 0;
            }

            static int? TranslateToNumber(char c)
            {
                if ("ABC".Contains(c)) {
                    return 2;
                } else if ("DEF".Contains(c)) {
                    return 3;
                } else if ("GHI".Contains(c)) {
                    return 4;
                } else if ("JKL".Contains(c)) {
                    return 5;
                } else if ("MNO".Contains(c)) {
                    return 6;
                } else if ("PQRS".Contains(c)) {
                    return 7;
                } else if ("TUV".Contains(c)) {
                    return 8;
                } else if ("WXYZ".Contains(c)) {
                    return 9;
                }
                return null;
            }
        }
    }
    ```

    Salve o arquivo **PhoneTranslator.cs** e feche-o.

19. Adicione código para conectar a interface do usuário. Para fazer isso, clique duas vezes em **ViewController.cs** no **Painel de Solução** para abri-lo:

    ![Adicione um código para conectar a interface do usuário](hello-ios-quickstart-images/image20new.png)

20. Comece conectando `TranslateButton`. Na classe **ViewController** , localize o método `ViewDidLoad` e adicione o seguinte código sob a chamada `base.ViewDidLoad()`:

    ```csharp
    string translatedNumber = "";

    TranslateButton.TouchUpInside += (object sender, EventArgs e) => {
        // Convert the phone number with text to a number
        // using PhoneTranslator.cs
        translatedNumber = PhoneTranslator.ToNumber(
            PhoneNumberText.Text);

        // Dismiss the keyboard if text field was tapped
        PhoneNumberText.ResignFirstResponder ();

        if (translatedNumber == "") {
            CallButton.SetTitle ("Call ", UIControlState.Normal);
            CallButton.Enabled = false;
        } else {
            CallButton.SetTitle ("Call " + translatedNumber,
                UIControlState.Normal);
            CallButton.Enabled = true;
        }
    };
    ```

    Incluir `using Phoneword_iOS;` se o namespace do arquivo for diferente.

21. Adicione código para responder quando o usuário pressiona o segundo botão, que se chama `CallButton`. Coloque o código a seguir abaixo do código para `TranslateButton` e adicione `using Foundation;` à parte superior do arquivo:

    ```csharp
        CallButton.TouchUpInside += (object sender, EventArgs e) => {
            // Use URL handler with tel: prefix to invoke Apple's Phone app...
            var url = new NSUrl ("tel:" + translatedNumber);

            // ...otherwise show an alert dialog
            if (!UIApplication.SharedApplication.OpenUrl (url)) {
                var alert = UIAlertController.Create ("Not supported", "Scheme 'tel:' is not supported on this device", UIAlertControllerStyle.Alert);
                alert.AddAction (UIAlertAction.Create ("Ok", UIAlertActionStyle.Default, null));
                PresentViewController (alert, true, null);
            }
        };
    ```

22. Salve as alterações e, em seguida, compile o aplicativo escolhendo **compilar > compilar tudo** ou pressionar **⌘ + B** .  Se o aplicativo for compilado, uma mensagem de êxito aparecerá na parte superior do IDE:

    ![Será exibida uma mensagem de êxito na parte superior do IDE](hello-ios-quickstart-images/image21.png)

    Se houver erros, repita as etapas anteriores e corrija-os até que o aplicativo seja compilado com êxito.

23. Por fim, teste o aplicativo no **Simulador do iOS** . No canto superior esquerdo do IDE, escolha **Depurar** na primeira lista suspensa e **iPhone XR iOS 12.0** (ou outro simulador disponível) na segunda lista suspensa e pressione **Iniciar** (o botão triangular que lembra um botão Reproduzir):

    ![Selecione um simulador e pressione iniciar](hello-ios-quickstart-images/image27.png)

    > [!NOTE]
    > No momento, devido a um requisito da Apple, pode ser necessário ter um certificado de desenvolvimento ou *identidade de assinatura* para poder compilar código para o dispositivo ou simulador. Siga as etapas no [guia Provisionamento de Dispositivo](~/ios/get-started/installation/device-provisioning/manual-provisioning.md) para configurar isso.

24. Isso inicializará o aplicativo no Simulador do iOS:

    ![O aplicativo em execução dentro do Simulador de iOS](hello-ios-quickstart-images/image28.png)

    Não há suporte para chamadas telefônicas no Simulador de iOS; você verá uma caixa de diálogo de alerta quanto tentar fazer uma chamada:

    ![A caixa de diálogo de alerta ao tentar fazer uma chamada](hello-ios-quickstart-images/image29.png)

::: zone-end
::: zone pivot="windows"

## <a name="visual-studio-walkthrough"></a>Passo a passo do Visual Studio

Este passo a passo descreve como criar um aplicativo chamado Phoneword que converte um número de telefone alfanumérico em um número de telefone numérico.

> [!NOTE]
> Este passo a passo usa o Visual Studio Enterprise 2017 em uma máquina virtual do Windows 10. Sua instalação pode diferir desta, desde que atenda aos requisitos acima, mas lembre-se de que algumas capturas de tela podem ser diferentes para sua instalação.

> [!NOTE]
> Antes de continuar com este passo a passo, você já deve estar conectado ao Mac do Visual Studio. Isso ocorre porque o Xamarin.iOS depende das ferramentas da Apple para compilar e iniciar o iOS Designer e os aplicativos. Para obter a configuração, siga as etapas do guia [Emparelhar com Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

1. Inicialize o Visual Studio no menu **Iniciar** :

    ![A Tela inicial](hello-ios-quickstart-images/image001-.png)

    Crie uma solução Xamarin.iOS selecionando **Arquivo > Novo > Projeto... > Visual C# > iPhone e iPad > Aplicativo iOS (Xamarin)** :

    ![Selecionar tipo de projeto do aplicativo iOS (Xamarin)](hello-ios-quickstart-images/image002.w157.png "Selecionar tipo de projeto do aplicativo iOS (Xamarin)")

    Na próxima caixa de diálogo que aparece, selecione o modelo **Aplicativo de Modo de Exibição Único** e pressione **OK** para criar o projeto:

    ![Selecionar modelo de projeto de exibição única](hello-ios-quickstart-images/image002-2.w157.png "Selecionar modelo de projeto de exibição única")

1. Confirme se o ícone do Xamarin Mac Agent na barra de ferramentas está verde.

    ![Confirme se o ícone do Xamarin Mac Agent na barra de ferramentas está verde](hello-ios-quickstart-images/vs-image4.png)

    Caso contrário, significa que não há nenhuma conexão com o host do build do Mac, siga as etapas no [guia de configuração](~/ios/get-started/installation/windows/connecting-to-mac/index.md) para se conectar.

1. Abra o arquivo **Main.storyboard** no iOS Designer clicando duas vezes no **Gerenciador de Soluções** :

    ![O Designer de iOS](hello-ios-quickstart-images/vs-image7.png)

1. Abra a guia **Caixa de Ferramentas** , digite "rótulo" na barra de pesquisa e arraste um **Rótulo** para a superfície de design (a área no centro):

    ![Arraste um Rótulo para a área da superfície de design no centro](hello-ios-quickstart-images/vs-image8.png)

1. Em seguida, pegue as alças dos *Controles de Arraste* e torne o rótulo mais largo:

    ![Torne o rótulo mais largo](hello-ios-quickstart-images/vs-image9.png)

1. Com o **Rótulo** selecionado na superfície de design, use as **Janelas de Propriedades** para alterar a propriedade **Texto** do **Rótulo** para “Inserir um Phoneword:”

    ![Altere a propriedade Texto do rótulo para `Inserir um Phoneword`](hello-ios-quickstart-images/vs-image10.png)

    > [!NOTE]
    > Você pode abrir **Propriedades** ou a **Caixa de Ferramentas** a qualquer momento navegando para o menu **Exibir** .

1. Pesquise “campo de texto” dentro da Caixa de Ferramentas e arraste um **Campo de Texto** da **Caixa de Ferramentas** para a superfície de design e coloque-o no **Rótulo** . Ajuste a largura até que o **campo de texto** tenha a mesma largura que o **rótulo** :

    ![Ajuste a largura até o Campo de texto ter a mesma largura que o Rótulo](hello-ios-quickstart-images/vs-image12.png)

1. Com o **Campo de Texto** selecionado na superfície de design, no **Campo de Texto** , altere a propriedade **Nome** na seção Identidade de **Propriedades** para `PhoneNumberText` e altere a propriedade **Texto** para “1-855-XAMARIN”:

    ![Altere a propriedade Texto para 1-855-XAMARIN](hello-ios-quickstart-images/vs-image13.png)

1. Arraste um **Botão** da **Caixa de Ferramentas** para a superfície de design e coloque-o no **Campo de Texto** . Ajuste a largura para que o **botão** seja tão largo quanto o **campo de texto** e o **rótulo** :

    ![Ajuste a largura para que o botão tenha a mesma largura que o Campo de texto e o Rótulo](hello-ios-quickstart-images/vs-image14.png)

1. Com o **Botão** selecionado na superfície de design, altere a propriedade **Nome** na seção **Identidade** de **Propriedades** para `TranslateButton`. Altere a propriedade **title** para "translate":

    ![Altere a propriedade Título para Traduzir](hello-ios-quickstart-images/vs-image15.png)

1. Repita as duas etapas anteriores e arraste um **Botão** de **Caixa de Ferramentas** para a superfície de design e coloque-o sob o primeiro **Botão** . Ajuste a largura para que o **botão** seja tão largo quanto o primeiro **botão** :

    ![Ajuste a largura para que o botão tenha a mesma largura que o primeiro botão](hello-ios-quickstart-images/vs-image16.png)

1. Com o segundo **Botão** selecionado na superfície de design, altere a propriedade **Nome** na seção **Identidade** de **Propriedades** para `CallButton`. Altere a propriedade **title** para "Call":

    ![Altere a propriedade Título para Chamar](hello-ios-quickstart-images/vs-image17.png)

    Salve as alterações navegando até **Arquivo > Salvar Tudo** ou pressionando **Ctrl + s** .

1. Adicione algum código para converter números de telefone de alfanuméricos para numéricos. Para fazer isso, primeiro adicione um novo arquivo ao Projeto clicando com o botão direito do mouse no Projeto **Phoneword** no **Gerenciador de Soluções** e escolhendo **Adicionar > Novo Item…** ou pressionando **Ctrl + Shift + A** :

    ![Adicione algum código para converter números de telefone de alfanuméricos em numéricos](hello-ios-quickstart-images/vs-image18.png)

1. Na caixa de diálogo **Adicionar Novo Item** (clique com o botão direito do mouse no projeto e escolha Adicionar > Novo Item...), selecione **Apple > Classe** e o nome do novo arquivo `PhoneTranslator`:

    ![Adicione uma nova classe chamada PhoneTranslator](hello-ios-quickstart-images/vs-image19.w157.png)

    > [!IMPORTANT]
    > Selecione o modelo "class" que tem C# no ícone. Caso contrário, você não poderá fazer referência a essa nova classe.

1. Isso cria uma nova classe C#. Remova todo o código de modelo e substitua-o pelo código a seguir:

    ```csharp
    using System.Text;
    using System;

    namespace Phoneword
    {
        public static class PhoneTranslator
        {
            public static string ToNumber(string raw)
            {
                if (string.IsNullOrWhiteSpace(raw)) {
                    return "";
                } else {
                    raw = raw.ToUpperInvariant();
                }

                var newNumber = new StringBuilder();
                foreach (var c in raw)
                {
                    if (" -0123456789".Contains(c)) {
                        newNumber.Append(c);
                    } else {
                        var result = TranslateToNumber(c);
                        if (result != null) {
                            newNumber.Append(result);
                        }
                    }
                    // otherwise we've skipped a non-numeric char
                }
                return newNumber.ToString();
            }

            static bool Contains (this string keyString, char c)
            {
                return keyString.IndexOf(c) >= 0;
            }

            static int? TranslateToNumber(char c)
            {
                if ("ABC".Contains(c)) {
                    return 2;
                } else if ("DEF".Contains(c)) {
                    return 3;
                } else if ("GHI".Contains(c)) {
                    return 4;
                } else if ("JKL".Contains(c)) {
                    return 5;
                } else if ("MNO".Contains(c)) {
                    return 6;
                } else if ("PQRS".Contains(c)) {
                    return 7;
                } else if ("TUV".Contains(c)) {
                    return 8;
                } else if ("WXYZ".Contains(c)) {
                    return 9;
                }
                return null;
            }
        }
    }
    ```

    Salve o arquivo **PhoneTranslator.cs** e feche-o.

1. Clique duas vezes em **ViewController.cs** no **Gerenciador de Soluções** para abri-lo de modo que a lógica pode ser adicionada a interações de alças com botões:

    ![Lógica adicionada para manipular as interações com os botões](hello-ios-quickstart-images/vs-image20.png)

1. Comece conectando `TranslateButton`. Na classe **ViewController** , localize o método `ViewDidLoad`. Adicione o seguinte código de botão dentro de `ViewDidLoad`, abaixo da chamada `base.ViewDidLoad()`:

    ```csharp
    string translatedNumber = "";

    TranslateButton.TouchUpInside += (object sender, EventArgs e) => {

        // Convert the phone number with text to a number
        // using PhoneTranslator.cs
        translatedNumber = PhoneTranslator.ToNumber(PhoneNumberText.Text);

        // Dismiss the keyboard if text field was tapped
        PhoneNumberText.ResignFirstResponder ();

        if (translatedNumber == "") {
            CallButton.SetTitle ("Call", UIControlState.Normal);
            CallButton.Enabled = false;
            }
        else {
            CallButton.SetTitle ("Call " + translatedNumber, UIControlState.Normal);
            CallButton.Enabled = true;
            }
    };
    ```

    Incluir `using Phoneword;` se o namespace do arquivo for diferente.

1. Adicione código para responder quando o usuário pressiona o segundo botão, que se chama `CallButton`. Coloque o código a seguir abaixo do código para `TranslateButton` e adicione `using Foundation;` à parte superior do arquivo:

    ```csharp
    CallButton.TouchUpInside += (object sender, EventArgs e) => {
        var url = new NSUrl ("tel:" + translatedNumber);

            // Use URL handler with tel: prefix to invoke Apple's Phone app,
            // otherwise show an alert dialog

        if (!UIApplication.SharedApplication.OpenUrl (url)) {
                        var alert = UIAlertController.Create ("Not supported", "Scheme 'tel:' is not supported on this device", UIAlertControllerStyle.Alert);
                        alert.AddAction (UIAlertAction.Create ("Ok", UIAlertActionStyle.Default, null));
                        PresentViewController (alert, true, null);
                    }
    };
    ```

1. Salve as alterações e, em seguida, compile o aplicativo escolhendo **compilar > criar solução** ou pressionar **Ctrl + Shift + B** .  Se o aplicativo for compilado, uma mensagem de êxito aparecerá na parte inferior do IDE:

    ![Será exibida uma mensagem de êxito na parte inferior do IDE](hello-ios-quickstart-images/vs-image21.png)

    Se houver erros, repita as etapas anteriores e corrija-os até que o aplicativo seja compilado com êxito.

1. Por fim, teste o aplicativo no **Simulador do iOS Remoto** . Na barra de ferramentas de IDE, escolha **Depurar** e **iPhone 8 Plus iOS x.x** no menu suspenso e pressione **Iniciar** (o triângulo verde parecido com o botão Executar):

    ![Pressione Iniciar](hello-ios-quickstart-images/vs-image27.png)

1. Isso inicializará o aplicativo no Simulador do iOS:

    ![O aplicativo em execução dentro do Simulador de iOS](hello-ios-quickstart-images/vs-image28.png)

    Não há suporte para chamadas telefônicas no Simulador de iOS; uma caixa de diálogo de alerta será exibida ao tentar fazer uma chamada:

    ![Uma caixa de diálogo de alerta será exibida ao tentar fazer uma chamada](hello-ios-quickstart-images/vs-image29.png)

::: zone-end

Parabéns por concluir seu primeiro aplicativo Xamarin.iOS!

Agora é hora de examinar as ferramentas e as habilidades mostradas neste guia no [Aprofundamento no Hello, iOS](~/ios/get-started/hello-ios/hello-ios-deepdive.md).

## <a name="related-links"></a>Links Relacionados

- [Ícones de aplicativo e imagens de inicialização do Xamarin (exemplo)](https://github.com/xamarin/ios-samples/blob/master/Hello_iOS/Resources/XamarinAppIconsandLaunchImages.zip?raw=true)
- [Hello, iOS (exemplo)](/samples/xamarin/ios-samples/hello-ios)
- [Diretrizes da interface humana do iOS](https://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/Introduction/Introduction.html)
- [Portal de Provisionamento do iOS](https://developer.apple.com/ios/manage/overview/index.action)
