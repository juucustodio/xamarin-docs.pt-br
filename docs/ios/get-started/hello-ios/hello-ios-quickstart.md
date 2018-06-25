---
title: Olá, iOS – Guia de Início Rápido
description: Este passo a passo demonstra como criar um aplicativo Xamarin.iOS simples chamado Olá, iOS. Dessa forma, ele apresenta as ferramentas fundamentais e os conceitos que devem ser compreendidos para criar aplicativos Xamarin.iOS.
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: D3868F3A-4EED-BDDF-45AA-665102C39634
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 05/02/2017
ms.openlocfilehash: d9f5894f03bbfaa2145aec462dbd0ede7774354a
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34785980"
---
# <a name="hello-ios--quickstart"></a>Olá, iOS – Guia de Início Rápido

Este guia descreve como criar um aplicativo que converte um número de telefone alfanumérico inserido pelo usuário em numérico e, em seguida, chama esse número. O aplicativo final tem esta aparência:

 [![](hello-ios-quickstart-images/image1.png "O aplicativo de Início Rápido Hello.iOS")](hello-ios-quickstart-images/image1.png#lightbox)

<a name="Requirements" />

## <a name="requirements"></a>Requisitos

O desenvolvimento do iOS com o Xamarin requer:

-  Um Mac executando o macOS Sierra (10.12) ou superior.
-  A versão mais recente do SDK do iOS e do Xcode instalada da [App Store](https://itunes.apple.com/us/app/xcode/id497799835?mt=12).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

O Xamarin.iOS funciona com as seguintes configurações:

-  Versão mais recente do Visual Studio para Mac que atenda às especificações acima.

O [guia de Instalação do Mac do Xamarin.iOS](~/ios/get-started/installation/mac.md) está disponível para instruções de instalação passo a passo

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

O Xamarin.iOS funciona com as seguintes configurações:

-  Versão mais recente do Visual Studio 2017 Community, Professional ou Enterprise no Windows 7 ou superior, emparelhada com um host de build do Mac que atenda às especificações acima.

O [Guia de instalação do Xamarin.iOS no Windows](~/ios/get-started/installation/windows/index.md) está disponível para fornecer instruções de instalação passo a passo.

-----

Antes de começar, baixe o [Conjunto de ícones do Aplicativo Xamarin](https://github.com/xamarin/ios-samples/blob/master/Hello_iOS/Resources/XamarinAppIconsandLaunchImages.zip?raw=true).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

## <a name="visual-studio-for-mac-walkthrough"></a>Passo a passo do Visual Studio para Mac

Este passo a passo descreve como criar um aplicativo chamado Phoneword que converte um número de telefone alfanumérico em um número de telefone numérico.

1. Inicie o Visual Studio para Mac na pasta **Aplicativos** ou **Destaque** para exibir a tela Inicialização:

  ![](hello-ios-quickstart-images/image2new.png "A tela de Inicialização")

Na Tela de Inicialização, clique em **Novo Projeto...** para criar uma nova solução do Xamarin.iOS:

![](hello-ios-quickstart-images/image3new.png "Solução de iOS")

2. Na **caixa de diálogo Nova Solução**, escolha o modelo **iOS > Aplicativo > Aplicativo de Exibição Única**, garantindo que C# esteja selecionado. Clique em **Avançar**:

  ![](hello-ios-quickstart-images/image4new.png "Escolha o aplicativo de exibição única")

3. Configure o aplicativo. Dê a ele o **Nome** `Phoneword_iOS` e deixe o resto como padrão. Clique em **Avançar**:

  ![](hello-ios-quickstart-images/image5new.png "Insira o nome do aplicativo")

4. Deixe o Projeto e o Nome da Solução como está. Escolha o local do projeto aqui ou mantenha-o como padrão:

  ![](hello-ios-quickstart-images/image6new.png "Escolha o local do projeto")

5. Clique em **Criar** para criar a **Solução**.

6. Abra o arquivo **Main.storyboard** clicando duas vezes no **Painel de Solução**. Isso oferece uma maneira de criar visualmente uma interface do usuário:

  ![](hello-ios-quickstart-images/image7new.png "O Designer de iOS")

  Observe que as _classes de tamanho_ estão habilitadas por padrão. Consulte o guia [Storyboards Unificados](~/ios/user-interface/storyboards/unified-storyboards.md) para saber mais sobre elas.

8. No **Painel de Caixa de Ferramentas**, digite "rótulo" na barra de pesquisa e arraste um **Rótulo** para a superfície de design (a área no centro):

  ![](hello-ios-quickstart-images/image8new.png "Arraste um Rótulo para a área da superfície de design no centro")

  > [!NOTE]
  > Você pode abrir o painel **Propriedades** ou a **Caixa de Ferramentas** a qualquer momento navegando para **Exibir > Painéis**.

9. Pegue as alças dos *Controles de Arraste* (os círculos ao redor do controle) e torne o rótulo mais largo:

  ![](hello-ios-quickstart-images/image9.png "Torne o rótulo mais largo")


10. Com o **Rótulo** selecionado na superfície de design, use o **Painel de Propriedades** para alterar a propriedade **Texto** do **Rótulo** para “Inserir um Phoneword:”

  ![](hello-ios-quickstart-images/image10.png "Defina o rótulo para Inserir um Phoneword")

11. Pesquise “campo de texto” dentro da Caixa de Ferramentas e arraste um **Campo de Texto** da **Caixa de Ferramentas** para a superfície de design e coloque-o no **Rótulo**. Ajuste a largura até o **Campo de Texto** ter a mesma largura que o **Rótulo**:

  ![](hello-ios-quickstart-images/image12new.png "Deixe o Campo de texto com a mesma largura que o Rótulo")


12. Com o **Campo de Texto** selecionado na superfície de design, no **Campo de Texto**, altere a propriedade **Nome** na seção Identidade do **Painel de Propriedades** para `PhoneNumberText` e altere a propriedade **Texto** para "1-855-XAMARIN":

  ![](hello-ios-quickstart-images/image13new.png "Altere a propriedade Título para 1-855-XAMARIN")


13. Arraste um **Botão** da **Caixa de Ferramentas** para a superfície de design e coloque-o no **Campo de Texto**. Ajuste a largura de modo que o **botão** seja tão largo quanto o **Campo de Texto** e o **Rótulo**:

  ![](hello-ios-quickstart-images/image14new.png "Ajuste a largura para que o botão tenha a mesma largura que o Campo de texto e o Rótulo")


14. Com o **Botão** selecionado na superfície de design, altere a propriedade **Nome** na seção **Identidade** do **Painel de Propriedades** para `TranslateButton`. Altere a propriedade **Título** “Traduzir”:

  ![](hello-ios-quickstart-images/image15new.png "Altere a propriedade Título para Traduzir")


15. Repita as duas etapas acima e arraste um **Botão** de **Caixa de Ferramentas** para a superfície de design e coloque-o sob o primeiro **Botão**. Ajuste a largura para que o **Botão** tenha a mesma largura que o primeiro **Botão**:

  ![](hello-ios-quickstart-images/image16new.png "Ajuste a largura para que o botão tenha a mesma largura que o primeiro botão")


16. Com o segundo **Botão** selecionado na superfície de design, altere a propriedade **Nome** na seção **Identidade** do **Painel de Propriedades** para `CallButton`. Alterar a propriedade **Título** para “Chamada”:

  ![](hello-ios-quickstart-images/image17new.png "Altere a propriedade Título para Chamar")

  Salve as alterações navegando até **Arquivo > Salvar** ou pressionando **⌘ + s**.


17. Alguma lógica precisa ser adicionada ao aplicativo para converter números de telefone de alfanuméricos para numéricos. Adicione um novo arquivo ao projeto clicando com o botão direito do mouse no projeto **Phoneword_iOS** no **Painel de Soluções** e escolhendo **Adicionar > Novo Arquivo...** ou pressionando **⌘ + n**:

  ![](hello-ios-quickstart-images/image18.png "Adicione um novo arquivo ao projeto")


18. Na caixa de diálogo **Novo Arquivo**, selecione **Geral > Classe Vazia** e nomeie o novo arquivo `PhoneTranslator`:

  ![](hello-ios-quickstart-images/image19.png "Selecione a Classe vazia e dê um nome ao novo arquivo PhoneTranslator")


19. Isso cria uma nova classe C# vazia para nós. Remova todo o código de modelo e substitua-o pelo código a seguir:

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

20. Adicione código para conectar a interface do usuário. Para fazer isso, clique duas vezes em **ViewController.cs** no **Painel de Solução** para abri-lo:

  ![](hello-ios-quickstart-images/image20new.png "Adicione um código para conectar a interface do usuário")


21. Comece conectando `TranslateButton`. Na classe **ViewController**, localize o método `ViewDidLoad` e adicione o seguinte código sob a chamada `base.ViewDidLoad()`:

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

22. Adicione código para responder quando o usuário pressiona o segundo botão, que se chama `CallButton`. Coloque o código a seguir abaixo do código para `TranslateButton` e adicione `using Foundation;` à parte superior do arquivo:

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


23. Salve as alterações e compile o aplicativo escolhendo **Compilar > Compilar Tudo** ou pressionando **⌘ + B**.  Se o aplicativo for compilado, uma mensagem de êxito será exibida na parte superior do IDE:

  ![](hello-ios-quickstart-images/image21.png "Será exibida uma mensagem de êxito na parte superior do IDE")

  Se houver erros, repita as etapas anteriores e corrija-os até que o aplicativo seja compilado com êxito.

27. Por fim, teste o aplicativo no **Simulador do iOS**. No canto superior esquerdo do IDE, escolha **Depurar** na primeira lista suspensa e **iPhone 8 Plus iOS x.x** na segunda lista suspensa, então pressione **Iniciar** (o botão triangular que lembra um botão Executar):

  ![](hello-ios-quickstart-images/image27new.png "Pressione Iniciar")

  > [!NOTE]
  > No momento, devido a um requisito da Apple, pode ser necessário ter um certificado de desenvolvimento ou *identidade de assinatura* para poder compilar código para o dispositivo ou simulador. Siga as etapas no [guia Provisionamento de Dispositivo](~/ios/get-started/installation/device-provisioning/manual-provisioning.md) para configurar isso.

28. Isso inicializará o aplicativo no Simulador do iOS:

  ![](hello-ios-quickstart-images/image28.png "O aplicativo em execução dentro do Simulador de iOS")

  Não há suporte para chamadas telefônicas no Simulador de iOS; você verá uma caixa de diálogo de alerta quanto tentar fazer uma chamada:

  ![](hello-ios-quickstart-images/image29.png "A caixa de diálogo de alerta ao tentar fazer uma chamada")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

## <a name="visual-studio-walkthrough"></a>Passo a passo do Visual Studio

Este passo a passo descreve como criar um aplicativo chamado Phoneword que converte um número de telefone alfanumérico em um número de telefone numérico.

**Observação**: este passo a passo usa o Visual Studio Enterprise 2017 em uma máquina virtual do Windows 10. Sua instalação pode diferir desta, desde que atenda aos requisitos acima, mas lembre-se de que algumas capturas de tela podem ser diferentes para sua instalação.

> [!NOTE]
> Antes de continuar com este passo a passo, você já deve estar conectado ao Mac do Visual Studio. Isso ocorre porque o Xamarin.iOS depende das ferramentas da Apple para compilar e iniciar o iOS Designer e os aplicativos. Para obter a configuração, siga as etapas do guia [Emparelhar com Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

1. Inicialize o Visual Studio no menu **Iniciar**:

  ![](hello-ios-quickstart-images/image001-.png "A Tela inicial")

  Crie uma solução Xamarin.iOS selecionando **Arquivo > Novo > Projeto... > Visual C# > iPhone e iPad > Aplicativo iOS (Xamari**:

  ![Selecione o tipo de projeto do aplicativo iOS (Xamarin)](hello-ios-quickstart-images/image002.w157.png "Select iOS App (Xamarin) project type")

  Na próxima caixa de diálogo que aparece, selecione o modelo **Aplicativo de Modo de Exibição Único** e pressione **OK** para criar o projeto:

  ![Selecione o modelo de projeto de Modo de Exibição Único](hello-ios-quickstart-images/image002-2.w157.png "Select Single View project template")

1. Confirme se o ícone do Xamarin Mac Agent na barra de ferramentas está verde.

    ![Confirme se o ícone do Xamarin Mac Agent na barra de ferramentas está verde](hello-ios-quickstart-images/vs-image4.png)

    Caso contrário, significa que não há nenhuma conexão com o host do build do Mac, siga as etapas no [guia de configuração](~/ios/get-started/installation/windows/connecting-to-mac/index.md) para se conectar.

1. Abra o arquivo **Main.storyboard** no iOS Designer clicando duas vezes no **Gerenciador de Soluções**:

  ![](hello-ios-quickstart-images/vs-image7.png "O Designer de iOS")

1. Abra a guia **Caixa de Ferramentas**, digite "rótulo" na barra de pesquisa e arraste um **Rótulo** para a superfície de design (a área no centro):

  ![](hello-ios-quickstart-images/vs-image8.png "Arraste um Rótulo para a área da superfície de design no centro")

1. Em seguida, pegue as alças dos *Controles de Arraste* e torne o rótulo mais largo:

  ![](hello-ios-quickstart-images/vs-image9.png "Torne o rótulo mais largo")

1. Com o **Rótulo** selecionado na superfície de design, use as **Janelas de Propriedades** para alterar a propriedade **Texto** do **Rótulo** para “Inserir um Phoneword:”

  ![](hello-ios-quickstart-images/vs-image10.png "Altere a propriedade Texto do rótulo para `Inserir um Phoneword`")

  > [!NOTE]
  > Você pode abrir **Propriedades** ou a **Caixa de Ferramentas** a qualquer momento navegando para o menu **Exibir**.

1. Pesquise “campo de texto” dentro da Caixa de Ferramentas e arraste um **Campo de Texto** da **Caixa de Ferramentas** para a superfície de design e coloque-o no **Rótulo**. Ajuste a largura até o **Campo de Texto** ter a mesma largura que o **Rótulo**:

  ![](hello-ios-quickstart-images/vs-image12.png "Ajuste a largura até o Campo de texto ter a mesma largura que o Rótulo")

10. Com o **Campo de Texto** selecionado na superfície de design, no **Campo de Texto**, altere a propriedade **Nome** na seção Identidade de **Propriedades** para `PhoneNumberText` e altere a propriedade **Texto** para “1-855-XAMARIN”:

  ![](hello-ios-quickstart-images/vs-image13.png "Altere a propriedade Texto para 1-855-XAMARIN")


11. Arraste um **Botão** da **Caixa de Ferramentas** para a superfície de design e coloque-o no **Campo de Texto**. Ajuste a largura de modo que o **botão** seja tão largo quanto o **Campo de Texto** e o **Rótulo**:

  ![](hello-ios-quickstart-images/vs-image14.png "Ajuste a largura para que o botão tenha a mesma largura que o Campo de texto e o Rótulo")


12. Com o **Botão** selecionado na superfície de design, altere a propriedade **Nome** na seção **Identidade** de **Propriedades** para `TranslateButton`. Altere a propriedade **Título** “Traduzir”:

  ![](hello-ios-quickstart-images/vs-image15.png "Altere a propriedade Título para Traduzir")


13. Repita as duas etapas anteriores e arraste um **Botão** de **Caixa de Ferramentas** para a superfície de design e coloque-o sob o primeiro **Botão**. Ajuste a largura para que o **Botão** tenha a mesma largura que o primeiro **Botão**:

  ![](hello-ios-quickstart-images/vs-image16.png "Ajuste a largura para que o botão tenha a mesma largura que o primeiro botão")


14. Com o segundo **Botão** selecionado na superfície de design, altere a propriedade **Nome** na seção **Identidade** de **Propriedades** para `CallButton`. Alterar a propriedade **Título** para “Chamada”:

  ![](hello-ios-quickstart-images/vs-image17.png "Altere a propriedade Título para Chamar")

  Salve as alterações navegando até **Arquivo > Salvar Tudo** ou pressionando **Ctrl + s**.

15. Adicione algum código para converter números de telefone de alfanuméricos para numéricos. Para fazer isso, primeiro adicione um novo arquivo ao Projeto clicando com o botão direito do mouse no Projeto **Phoneword** no **Gerenciador de Soluções** e escolhendo **Adicionar > Novo Item…** ou pressionando **Ctrl + Shift + A**:

  ![](hello-ios-quickstart-images/vs-image18.png "Adicione algum código para converter números de telefone de alfanuméricos em numéricos")


16. Na caixa de diálogo **Adicionar Novo Item** (clique com o botão direito do mouse no projeto e escolha Adicionar > Novo Item...), selecione **Apple > Classe** e o nome do novo arquivo `PhoneTranslator`:

  ![](hello-ios-quickstart-images/vs-image19.w157.png "Adicione uma nova classe chamada PhoneTranslator")

  > [!IMPORTANT]
  > Selecione o modelo "class" que tem C# no ícone. Caso contrário, você não poderá fazer referência a essa nova classe.


17. Isso cria uma nova classe C#. Remova todo o código de modelo e substitua-o pelo código a seguir:

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

18. Clique duas vezes em **ViewController.cs** no **Gerenciador de Soluções** para abri-lo de modo que a lógica pode ser adicionada a interações de alças com botões:

  ![](hello-ios-quickstart-images/vs-image20.png "Lógica adicionada para manipular as interações com os botões")


19. Comece conectando `TranslateButton`. Na classe **ViewController**, localize o método `ViewDidLoad`. Adicione o seguinte código de botão dentro de `ViewDidLoad`, abaixo da chamada `base.ViewDidLoad()`:

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

20. Adicione código para responder quando o usuário pressiona o segundo botão, que se chama `CallButton`. Coloque o código a seguir abaixo do código para `TranslateButton` e adicione `using Foundation;` à parte superior do arquivo:

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


21. Salve as alterações e, em seguida, compile o aplicativo escolhendo **Compilar > Compilar Solução** ou pressionando **Ctrl + Shift + B**.  Se o aplicativo for compilado, uma mensagem de êxito será exibida na parte inferior do IDE:

  ![](hello-ios-quickstart-images/vs-image21.png "Será exibida uma mensagem de êxito na parte inferior do IDE")

  Se houver erros, repita as etapas anteriores e corrija-os até que o aplicativo seja compilado com êxito.

22. Por fim, teste o aplicativo no **Simulador do iOS Remoto**. Na barra de ferramentas de IDE, escolha **Depurar** e **iPhone 8 Plus iOS x.x** no menu suspenso e pressione **Iniciar** (o triângulo verde parecido com o botão Executar):

  ![](hello-ios-quickstart-images/vs-image27.png "Pressione Iniciar")

23. Isso inicializará o aplicativo no Simulador do iOS:

  ![](hello-ios-quickstart-images/vs-image28.png "O aplicativo em execução dentro do Simulador de iOS")

  Não há suporte para chamadas telefônicas no Simulador de iOS; uma caixa de diálogo de alerta será exibida ao tentar fazer uma chamada:

  ![](hello-ios-quickstart-images/vs-image29.png "Uma caixa de diálogo de alerta será exibida ao tentar fazer uma chamada")

-----

Parabéns por concluir seu primeiro aplicativo Xamarin.iOS!

Agora é hora de examinar as ferramentas e as habilidades mostradas neste guia no [Aprofundamento no Hello, iOS](~/ios/get-started/hello-ios/hello-ios-deepdive.md).


## <a name="related-links"></a>Links relacionados

- [Ícones de aplicativo e imagens de inicialização do Xamarin (amostra)](https://github.com/xamarin/ios-samples/blob/master/Hello_iOS/Resources/XamarinAppIconsandLaunchImages.zip?raw=true)
- [Hello, iOS (amostra)](https://developer.xamarin.com/samples/monotouch/Hello_iOS/)
- [Diretrizes da interface humana do iOS](http://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/Introduction/Introduction.html)
- [Portal de provisionamento do iOS](https://developer.apple.com/ios/manage/overview/index.action)
