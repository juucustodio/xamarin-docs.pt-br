---
title: Olá, iOS Multitela – Guia de Início Rápido
description: Este documento demonstra como expandir o aplicativo de exemplo Phoneword para adicionar uma segunda tela, descrevendo o padrão modelo-exibição-controlador, a navegação do iOS e outros conceitos de desenvolvimento fundamentais do iOS ao longo da leitura.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: d72e6230-c9ee-4bee-90ec-877d256821aa
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 10/05/2018
ms.openlocfilehash: 849c60507fe0ff7b8bf1743be5bbf89ca94b9d6f
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67865570"
---
# <a name="hello-ios-multiscreen--quickstart"></a>Olá, iOS Multitela – Guia de Início Rápido

Esta parte do passo a passo adicionará uma segunda tela ao aplicativo Phoneword que exibirá um histórico dos números de telefone chamados com o aplicativo. O aplicativo final terá uma segunda tela exibindo o histórico de chamadas, como ilustrado pela seguinte captura de tela:

[![](hello-ios-multiscreen-quickstart-images/00.png "O aplicativo final terá uma segunda tela que exibirá o histórico de chamadas, conforme ilustrado por esta captura de tela")](hello-ios-multiscreen-quickstart-images/00.png#lightbox)

O [Aprofundamento relacionado](~/ios/get-started/hello-ios-multiscreen/hello-ios-multiscreen-deepdive.md) examinará o aplicativo compilado e discutirá a arquitetura, a navegação e outros novos conceitos de iOS que encontrarmos ao longo do caminho.

## <a name="requirements"></a>Requisitos

Esta guia retoma no ponto em que o documento Hello, iOS parou e exige a conclusão do [Início rápido do Hello, iOS](~/ios/get-started/hello-ios/index.md). A versão completa do aplicativo Phoneword pode ser baixada do [exemplo Hello, iOS](https://developer.xamarin.com/samples/monotouch/Hello_iOS/).

::: zone pivot="macos"

## <a name="walkthrough-on-macos"></a>Passo a passo no macOS

Este passo a passo adicionará uma tela de Histórico de Chamadas ao nosso aplicativo **Phoneword**.

1. Abra o aplicativo **Phoneword** no Visual Studio para Mac. Se necessário, o aplicativo concluído Phoneword do guia de [Passo a passo do Hello, iOS](~/ios/get-started/hello-ios/index.md) pode ser baixado [aqui](https://developer.xamarin.com/samples/monotouch/Hello_iOS/).

2. Abra o arquivo **Main.storyboard** do **Painel de Solução**:

    ![](hello-ios-multiscreen-quickstart-images/02new.png "O Main.storyboard no Designer de iOS")

3. Arraste um **controlador de navegação** da **Caixa de Ferramentas** para a superfície de design (talvez seja necessário reduzir o zoom para encaixar tudo na superfície de design!):

    ![](hello-ios-multiscreen-quickstart-images/03new.png "Arraste um controlador de navegação da Caixa de Ferramentas para a superfície de design")

4. Arraste o **segue sem origem** (a seta cinza à esquerda do controlador de exibição simples) para o **controlador de navegação** para alterar o ponto de partida do aplicativo:

    ![](hello-ios-multiscreen-quickstart-images/04new.png "Arraste o Segue sem origem para o controlador de navegação para alterar o ponto inicial do aplicativo")

5. Selecione o **controlador de exibição de raiz** existente clicando na barra inferior e pressione **Excluir** para removê-lo da superfície de design.
Em seguida, mova a cena **Phoneword** ao lado do **controlador de navegação**:

    ![](hello-ios-multiscreen-quickstart-images/05new.png "Mova a cena do Phoneword do lado do controlador de navegação")

6. Defina o **ViewController** como o **Controlador de exibição raiz** do controlador de navegação. Pressione a tecla **Ctrl** e clique dentro do **controlador de navegação**. Deve aparecer uma linha azul. Em seguida, ainda mantendo pressionada a tecla **Ctrl**, arraste do **controlador de navegação** para a cena **Phoneword** e solte. Isso se chama _arrastar com Ctrl_:

    ![](hello-ios-multiscreen-quickstart-images/06.png "Arraste do controlador de navegação até a cena do Phoneword e solte")

7. No pop-over, vamos definir as relações como **Raiz**:

    ![](hello-ios-multiscreen-quickstart-images/07new.png "Configuração da relação como raiz")

    Agora, o **ViewController** é o **controlador de exibição raiz do controlador de navegação:**

    ![](hello-ios-multiscreen-quickstart-images/08.png "Agora, o ViewController é o controlador de exibição raiz do controlador de navegação")

8. Na tela do **Phoneword**, clique duas vezes na barra de **Título** e altere o **Título** para **Phoneword**:

    ![](hello-ios-multiscreen-quickstart-images/09.png "Altere o título para 'Phoneword'")

9. Arraste um **botão** da **Caixa de Ferramentas** e coloque-o sob o **Botão Chamar**. Arraste as alças para deixar o novo **Botão** com a mesma largura do **Botão Chamar**:

    ![](hello-ios-multiscreen-quickstart-images/10new.png "Deixe o novo botão com a mesma largura que o botão Chamar")

10. No **Painel de Propriedades**, altere o **Nome** do botão para **CallHistoryButton** e altere o **Título** para **Histórico de Chamadas**:

    ![](hello-ios-multiscreen-quickstart-images/11new.png "Altere o Nome do botão para CallHistoryButton e altere o título para Histórico de chamadas")

11. Crie a tela **Histórico de Chamadas**. Na **Caixa de Ferramentas**, arraste um **controlador de exibição de tabela** para a superfície de design:

    ![](hello-ios-multiscreen-quickstart-images/12new.png "Arraste o controlador de exibição de tabela para a superfície de design")

12. Em seguida, selecione o **controlador de exibição de tabela** clicando na barra preta na parte inferior da cena. No **Painel de Propriedades**, altere a classe do **controlador de exibição de tabela** para `CallHistoryController` e pressione **Enter**:

    ![](hello-ios-multiscreen-quickstart-images/13new.png "Altere a classe do controlador de exibição de tabela para CallHistoryController")

    O Designer do iOS gerará uma classe de suporte personalizada chamada `CallHistoryController` para gerenciar a hierarquia de exibição de conteúdo dessa tela. O arquivo **CallHistoryController.cs** aparecerá no **Painel de Solução**:

    ![](hello-ios-multiscreen-quickstart-images/14new.png "O arquivo CallHistoryController.cs no Painel de Soluções")

13. Clique duas vezes no arquivo **CallHistoryController.cs** para abri-lo e substitua os conteúdos pelo seguinte código:
    
    ```csharp
    using System;
    using Foundation;
    using UIKit;
    using System.Collections.Generic;

    namespace Phoneword_iOS
    {
        public partial class CallHistoryController : UITableViewController
        {
            public List<string> PhoneNumbers { get; set; }

            static NSString callHistoryCellId = new NSString ("CallHistoryCell");

            public CallHistoryController (IntPtr handle) : base (handle)
            {
                TableView.RegisterClassForCellReuse (typeof(UITableViewCell), callHistoryCellId);
                TableView.Source = new CallHistoryDataSource (this);
                PhoneNumbers = new List<string> ();
            }

            class CallHistoryDataSource : UITableViewSource
            {
                CallHistoryController controller;

                public CallHistoryDataSource (CallHistoryController controller)
                {
                    this.controller = controller;
                }

                public override nint RowsInSection (UITableView tableView, nint section)
                {
                    return controller.PhoneNumbers.Count;
                }

                public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
                {
                    var cell = tableView.DequeueReusableCell (CallHistoryController.callHistoryCellId);

                    int row = indexPath.Row;
                    cell.TextLabel.Text = controller.PhoneNumbers [row];
                    return cell;
                }
            }
        }
    }
    ```

    Salve o aplicativo (**⌘ + s**) e compile-o (**⌘ + b**) para garantir que não haja erros.

14. Crie um _segue_ (transição) entre uma cena do **Phoneword** e a cena do **Histórico de Chamadas**.
  Na **Cena do Phoneword**, selecione o **Botão Histórico de Chamadas** e pressione Ctrl e arraste do **Botão** para a cena **Histórico de Chamadas**:

    ![](hello-ios-multiscreen-quickstart-images/15.png "Pressione Ctrl e arraste do botão até a cena de Histórico de Chamadas")

    No pop-over **Segue de Ação**, selecione **Mostrar**

    O Designer do iOS adicionará um Segue entre as duas cenas:

    ![](hello-ios-multiscreen-quickstart-images/17new.png "O Segue entre as duas cenas")

15. Adicione um **Título** ao **controlador de exibição de tabela** selecionando a barra preta na parte inferior da cena e alterando o **Título do controlador de exibição** para **Histórico de Chamadas** no **Painel de Propriedades**:

    ![](hello-ios-multiscreen-quickstart-images/18new.png "Altere o título do controlador de exibição para Histórico de Chamadas no Painel de Propriedades")

16. Quando o aplicativo for executado, o **Botão Histórico de Chamadas** abrirá a tela **Histórico de Chamadas**, mas a exibição de tabela estará vazia porque não há nenhum código para controlar e exibir os números de telefone.

    Esse aplicativo armazenará os números de telefone como uma lista de cadeias de caracteres.

    Adicione uma política `using` para `System.Collections.Generic` na parte superior do **ViewController**:

    ```csharp
    using System.Collections.Generic;
    ```

17. Modifique a classe `ViewController` com o código a seguir:

    ```csharp
    using System;
    using System.Collections.Generic;
    using Foundation;
    using UIKit;

    namespace Phoneword_iOS
    {
      public partial class ViewController : UIViewController
      {
        string translatedNumber = "";

        public List<string> PhoneNumbers { get; set; }

        protected ViewController(IntPtr handle) : base(handle)
        {
          //initialize list of phone numbers called for Call History screen
          PhoneNumbers = new List<string>();
        }

        public override void ViewDidLoad()
        {
          base.ViewDidLoad();
          // Perform any additional setup after loading the view, typically from a nib.

          TranslateButton.TouchUpInside += (object sender, EventArgs e) => {
            // Convert the phone number with text to a number
            // using PhoneTranslator.cs
            translatedNumber = PhoneTranslator.ToNumber(
              PhoneNumberText.Text);

            // Dismiss the keyboard if text field was tapped
            PhoneNumberText.ResignFirstResponder();

            if (translatedNumber == "")
            {
              CallButton.SetTitle("Call ", UIControlState.Normal);
              CallButton.Enabled = false;
            }
            else
            {
              CallButton.SetTitle("Call " + translatedNumber,
                UIControlState.Normal);
              CallButton.Enabled = true;
            }
          };

          CallButton.TouchUpInside += (object sender, EventArgs e) => {

            //Store the phone number that we're dialing in PhoneNumbers
            PhoneNumbers.Add(translatedNumber);

            // Use URL handler with tel: prefix to invoke Apple's Phone app...
            var url = new NSUrl("tel:" + translatedNumber);

            // otherwise show an alert dialog
            if (!UIApplication.SharedApplication.OpenUrl(url))
            {
              var alert = UIAlertController.Create("Not supported", "Scheme 'tel:' is not supported on this device", UIAlertControllerStyle.Alert);
              alert.AddAction(UIAlertAction.Create("Ok", UIAlertActionStyle.Default, null));
              PresentViewController(alert, true, null);
            }
          };
        }

        public override void PrepareForSegue(UIStoryboardSegue segue, NSObject sender)
        {
          base.PrepareForSegue(segue, sender);

          // set the view controller that’s powering the screen we’re
          // transitioning to

          var callHistoryController = segue.DestinationViewController as CallHistoryController;

          //set the table view controller’s list of phone numbers to the
          // list of dialed phone numbers

          if (callHistoryController != null)
          {
            callHistoryController.PhoneNumbers = PhoneNumbers;
          }
        }
      }
    }
    ```

    Há alguns pontos a observar aqui:

    - A variável `translatedNumber` foi movida do método `ViewDidLoad` para uma _variável no nível de classe_.
    - O código **CallButton** foi modificado para adicionar números discados à lista de números de telefone chamando `PhoneNumbers.Add(translatedNumber)`.
    - O método `PrepareForSegue` foi adicionado.

    Salve e compile o aplicativo para garantir que não haja erros.

18. Pressione o botão **Iniciar** para inicializar o aplicativo dentro do **Simulador de iOS**:

    ![](hello-ios-multiscreen-quickstart-images/19.png "Pressione o botão Iniciar para inicializar o aplicativo dentro do Simulador de iOS")

Parabéns por concluir seu primeiro aplicativo Xamarin.iOS multitela!

::: zone-end
::: zone pivot="windows"

## <a name="walkthrough-on-windows"></a>Passo a passo no Windows

Este passo a passo adicionará uma tela de Histórico de Chamadas ao nosso aplicativo **Phoneword**.

1. Abra o aplicativo **Phoneword** no Visual Studio. Se necessário, baixe o [aplicativo Phoneword completo](https://developer.xamarin.com/samples/monotouch/Hello_iOS/) do guia [Passo a passo do Hello, iOS](~/ios/get-started/hello-ios/index.md). Lembre-se de que é necessário conectar-se a um [Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md) para usar o Designer do iOS e o Simulador de iOS.

2. Comece editando a interface do usuário. Abra o arquivo **Main.Storyboard** no **Gerenciador de Soluções**, certificando-se de que **Exibir como** esteja definido como _iPhone 6_:

    ![](hello-ios-multiscreen-quickstart-images/image1.png "O Main.storyboard no Designer de iOS")

3. Arraste um **controlador de navegação** da **Caixa de Ferramentas** para a superfície de design:

    ![](hello-ios-multiscreen-quickstart-images/image2.png "Arraste um controlador de navegação da Caixa de Ferramentas para a superfície de design")

4. Arraste o **Segue sem origem** (a seta cinza à esquerda da cena **Phoneword**) da cena **Phoneword** para o **controlador de navegação** para alterar o ponto de partida do aplicativo:

    ![](hello-ios-multiscreen-quickstart-images/image3.png "Arraste o Segue sem origem para o controlador de navegação para alterar o ponto inicial do aplicativo")

5. Selecione o **Controlador de exibição raiz** clicando na barra preta e pressione **Excluir** para removê-lo da superfície de design.
  Em seguida, mova a cena **Phoneword** ao lado do **controlador de navegação**:

    ![](hello-ios-multiscreen-quickstart-images/image4.png "Mova a cena do Phoneword do lado do controlador de navegação")

6. Defina o **ViewController** como o controlador de exibição raiz do controlador de navegação. Pressione a tecla **Ctrl** e clique dentro do **Controlador de Navegação**. Deve aparecer uma linha azul. Em seguida, ainda mantendo pressionada a tecla **Ctrl**, arraste do **Controlador de Navegação** para a cena **Phoneword** e solte. Isso se chama _arrastar com Ctrl_:

    ![](hello-ios-multiscreen-quickstart-images/image5.png "Arraste do controlador de navegação até a cena do Phoneword e solte")

7. No pop-over, vamos definir as relações como **Raiz**:

    ![](hello-ios-multiscreen-quickstart-images/image6.png "Defina a relação como raiz")

    O **ViewController** agora é nosso **controlador de exibição raiz do controlador de navegação.**

8. Na tela do **Phoneword**, clique duas vezes na barra de **Título** e altere o **Título** para **Phoneword**:

    ![](hello-ios-multiscreen-quickstart-images/image7.png "Altere o título para Phoneword")

9. Arraste um **botão** da **Caixa de Ferramentas** e coloque-o sob o **Botão Chamar**. Arraste as alças para deixar o novo **Botão** com a mesma largura do **Botão Chamar**:

    ![](hello-ios-multiscreen-quickstart-images/image8.png "Deixe o novo botão com a mesma largura que o botão Chamar")

10. No **Gerenciador de Propriedades**, altere o **Nome** do **Botão** para `CallHistoryButton` e altere o **Título** para **Histórico de Chamadas**:

    ![](hello-ios-multiscreen-quickstart-images/image9.png "Altere o Nome do botão para 'CallHistoryButton' e o título para 'Histórico de chamadas'")

11. Crie a tela **Histórico de Chamadas**. Na **Caixa de Ferramentas**, arraste um **controlador de exibição de tabela** para a superfície de design:

    ![](hello-ios-multiscreen-quickstart-images/image10.png "Arraste o controlador de exibição de tabela para a superfície de design")

12. Selecione o **controlador de exibição de tabela** clicando na barra preta na parte inferior da cena. No **Gerenciador de Propriedades**, altere a classe **controlador de exibição de tabela** para `CallHistoryController` e pressione **Enter**:

    ![](hello-ios-multiscreen-quickstart-images/image11.png "Altere a classe do controlador de exibição de tabela para CallHistoryController")

    O Designer do iOS gerará uma classe de suporte personalizada chamada `CallHistoryController` para gerenciar a hierarquia de exibição de conteúdo dessa tela. O arquivo **CallHistoryController.cs** aparecerá no **Gerenciador de Soluções**:

    ![](hello-ios-multiscreen-quickstart-images/image12.png "O arquivo CallHistoryController.cs no Gerenciador de Soluções")

13. Clique duas vezes no arquivo **CallHistoryController.cs** para abri-lo e substitua os conteúdos pelo seguinte código:

    ```csharp
    using System;
    using Foundation;
    using UIKit;
    using System.Collections.Generic;

    namespace Phoneword
    {
        public partial class CallHistoryController : UITableViewController
        {
            public List<String> PhoneNumbers { get; set; }

            static NSString callHistoryCellId = new NSString ("CallHistoryCell");

            public CallHistoryController (IntPtr handle) : base (handle)
            {
                TableView.RegisterClassForCellReuse (typeof(UITableViewCell), callHistoryCellId);
                TableView.Source = new CallHistoryDataSource (this);
                PhoneNumbers = new List<string> ();
            }

            class CallHistoryDataSource : UITableViewSource
            {
                CallHistoryController controller;

                public CallHistoryDataSource (CallHistoryController controller)
                {
                    this.controller = controller;
                }

                // Returns the number of rows in each section of the table
                public override nint RowsInSection (UITableView tableView, nint section)
                {
                    return controller.PhoneNumbers.Count;
                }

                public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
                {
                    var cell = tableView.DequeueReusableCell (CallHistoryController.callHistoryCellId);

                    int row = indexPath.Row;
                    cell.TextLabel.Text = controller.PhoneNumbers [row];
                    return cell;
                }
            }
        }
    }
    ```

    Salve o aplicativo e compile-o para garantir que não haja erros. Não tem problema ignorar os avisos de build por enquanto.

14. Crie um _segue_ (transição) entre uma cena do **Phoneword** e a cena do **Histórico de Chamadas**.
  Na **Cena do Phoneword**, selecione o **Botão Histórico de Chamadas** e pressione **Ctrl e arraste** do **Botão** para a cena **Histórico de Chamadas**:

    ![](hello-ios-multiscreen-quickstart-images/image13.png "Pressione Ctrl e arraste do botão até a cena de Histórico de Chamadas")

    No pop-over **Segue de Ação**, selecione **Mostrar**:

    ![](hello-ios-multiscreen-quickstart-images/image14.png "Selecione Mostrar com o tipo de segue")

    O Designer do iOS adicionará um segue entre as duas cenas:

    ![](hello-ios-multiscreen-quickstart-images/image15.png "O Segue entre as duas cenas")

15. Adicione um **Título** ao **controlador de exibição de tabela** selecionando a barra preta na parte inferior da cena e alterando o **controlador de exibição > Título** para **Histórico de Chamadas** no **Gerenciador de Propriedades**:

    ![](hello-ios-multiscreen-quickstart-images/image16.png "Altere o título do controlador de exibição para Histórico de Chamadas")

16. Quando o aplicativo for executado, o **Botão Histórico de Chamadas** abrirá a tela **Histórico de Chamadas**, mas a exibição de tabela estará vazia porque não há nenhum código para controlar e exibir os números de telefone.

    Esse aplicativo armazenará os números de telefone como uma lista de cadeias de caracteres.

    Adicione uma política `using` para `System.Collections.Generic` na parte superior do **ViewController**:

    ```csharp
    using System.Collections.Generic;
    ```

17. Modifique a classe `ViewController` com o código a seguir:

    ```csharp
    using System;
    using System.Collections.Generic;
    using Foundation;
    using UIKit;

    namespace Phoneword_iOS
    {
      public partial class ViewController : UIViewController
      {
        string translatedNumber = "";

        public List<string> PhoneNumbers { get; set; }

        protected ViewController(IntPtr handle) : base(handle)
        {
          //initialize list of phone numbers called for Call History screen
          PhoneNumbers = new List<string>();
        }

        public override void ViewDidLoad()
        {
          base.ViewDidLoad();
          // Perform any additional setup after loading the view, typically from a nib.

          TranslateButton.TouchUpInside += (object sender, EventArgs e) => {
            // Convert the phone number with text to a number
            // using PhoneTranslator.cs
            translatedNumber = PhoneTranslator.ToNumber(
              PhoneNumberText.Text);

            // Dismiss the keyboard if text field was tapped
            PhoneNumberText.ResignFirstResponder();

            if (translatedNumber == "")
            {
              CallButton.SetTitle("Call ", UIControlState.Normal);
              CallButton.Enabled = false;
            }
            else
            {
              CallButton.SetTitle("Call " + translatedNumber,
                UIControlState.Normal);
              CallButton.Enabled = true;
            }
          };

          CallButton.TouchUpInside += (object sender, EventArgs e) => {

            //Store the phone number that we're dialing in PhoneNumbers
            PhoneNumbers.Add(translatedNumber);

            // Use URL handler with tel: prefix to invoke Apple's Phone app...
            var url = new NSUrl("tel:" + translatedNumber);

            // otherwise show an alert dialog
            if (!UIApplication.SharedApplication.OpenUrl(url))
            {
              var alert = UIAlertController.Create("Not supported", "Scheme 'tel:' is not supported on this device", UIAlertControllerStyle.Alert);
              alert.AddAction(UIAlertAction.Create("Ok", UIAlertActionStyle.Default, null));
              PresentViewController(alert, true, null);
            }
          };
        }

        public override void PrepareForSegue(UIStoryboardSegue segue, NSObject sender)
        {
          base.PrepareForSegue(segue, sender);

          // set the view controller that’s powering the screen we’re
          // transitioning to

          var callHistoryController = segue.DestinationViewController as CallHistoryController;

          //set the table view controller’s list of phone numbers to the
          // list of dialed phone numbers

          if (callHistoryController != null)
          {
            callHistoryController.PhoneNumbers = PhoneNumbers;
          }
        }
      }
    }
    ```

    Há alguns pontos a observar aqui
    - A variável `translatedNumber` foi movida do método `ViewDidLoad` para uma _variável no nível de classe_.
    - O código **CallButton** foi modificado para adicionar números discados à lista de números de telefone chamando `PhoneNumbers.Add(translatedNumber)`
    - O método `PrepareForSegue` foi adicionado

    Salve e compile o aplicativo para garantir que não haja erros.

    Salve e compile o aplicativo para garantir que não haja erros.

18. Pressione o botão **Iniciar** para inicializar o aplicativo dentro do **Simulador de iOS**:

    ![](hello-ios-multiscreen-quickstart-images/19.png "A primeira tela do aplicativo de exemplo")

Parabéns por concluir seu primeiro aplicativo Xamarin.iOS multitela!

::: zone-end

Agora, o aplicativo pode lidar com navegação usando tanto segues de storyboard quanto código. Agora é hora de examinar as ferramentas e as habilidades que acabamos de aprender no [Aprofundamento na multitela do Hello, iOS](~/ios/get-started/hello-ios-multiscreen/hello-ios-multiscreen-deepdive.md).

## <a name="related-links"></a>Links relacionados

- [Hello, iOS (amostra)](https://developer.xamarin.com/samples/monotouch/Hello_iOS/)
- [Diretrizes da interface humana do iOS](https://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/Introduction/Introduction.html)
- [Portal de provisionamento do iOS](https://developer.apple.com/ios/manage/overview/index.action)
