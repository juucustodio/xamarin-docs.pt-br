---
title: Olá, iOS Multitela – Guia de Início Rápido
description: Este documento demonstra como expandir o aplicativo de exemplo Phoneword para adicionar uma segunda tela, descrevendo o padrão modelo-exibição-controlador, a navegação do iOS e outros conceitos de desenvolvimento fundamentais do iOS ao longo da leitura.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: d72e6230-c9ee-4bee-90ec-877d256821aa
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 10/05/2018
ms.openlocfilehash: b937cc653275f509b7f759af5c3d995e84381cb2
ms.sourcegitcommit: 952db1983c0bc373844c5fbe9d185e04a87d8fb4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "86997339"
---
# <a name="hello-ios-multiscreen--quickstart"></a>Olá, iOS Multitela – Guia de Início Rápido

Esta parte do passo a passo adicionará uma segunda tela ao aplicativo Phoneword, que exibe um histórico dos números de telefone chamados com o aplicativo. O aplicativo final terá uma segunda tela exibindo o histórico de chamadas, como ilustrado pela seguinte captura de tela:

[![O aplicativo final terá uma segunda tela que exibe o histórico de chamadas, conforme ilustrado nesta captura](hello-ios-multiscreen-quickstart-images/00.png)](hello-ios-multiscreen-quickstart-images/00.png#lightbox)

O [Aprofundamento relacionado](~/ios/get-started/hello-ios-multiscreen/hello-ios-multiscreen-deepdive.md) examinará o aplicativo compilado e discutirá a arquitetura, a navegação e outros novos conceitos de iOS que encontrarmos ao longo do caminho.

## <a name="requirements"></a>Requisitos

Esta guia retoma no ponto em que o documento Hello, iOS parou e exige a conclusão do [Início rápido do Hello, iOS](~/ios/get-started/hello-ios/index.md). Baixe a versão completa do aplicativo Phoneword do [exemplo Hello, iOS](https://docs.microsoft.com/samples/xamarin/ios-samples/hello-ios).

::: zone pivot="macos"

## <a name="walkthrough-on-macos"></a>Passo a passo no macOS

Este passo a passo adicionará uma tela de Histórico de Chamadas ao nosso aplicativo **Phoneword**.

1. Abra o aplicativo **Phoneword** no Visual Studio para Mac. Se necessário, o aplicativo Phoneword concluído do guia de [instruções de saudação do IOS](~/ios/get-started/hello-ios/index.md) pode ser baixado [aqui](https://docs.microsoft.com/samples/xamarin/ios-samples/hello-ios).

2. Abra o arquivo **Main.storyboard** do **Painel de Solução**:

    ![O Main.storyboard no Designer de iOS](hello-ios-multiscreen-quickstart-images/02new.png)

3. Arraste um **controlador de navegação** da **caixa de ferramentas** para a superfície de design (talvez seja necessário reduzir tudo isso na superfície de Design!):

    ![Arraste um controlador de navegação da caixa de ferramentas para a superfície de design](hello-ios-multiscreen-quickstart-images/03new.png)

4. Arraste o **transição sem origem** (essa é a seta cinza à esquerda do controlador de exibição único) para o **controlador de navegação** para alterar o ponto de partida do aplicativo:

    ![Arraste o transição sem origem para o controlador de navegação para alterar o ponto inicial do aplicativo](hello-ios-multiscreen-quickstart-images/04new.png)

5. Selecione o **controlador de exibição raiz** existente clicando na barra inferior e pressione **delete** para removê-lo da superfície de design.
Em seguida, mova a cena **Phoneword** ao lado do **controlador de navegação**:

    ![Mover a cena Phoneword ao lado do controlador de navegação](hello-ios-multiscreen-quickstart-images/05new.png)

6. Defina o **ViewController** como o **controlador de exibição raiz**do controlador de navegação. Pressione a tecla **Ctrl** e clique dentro do **controlador de navegação**. Deve aparecer uma linha azul. Em seguida, ainda mantendo pressionada a tecla **Ctrl** , arraste do **controlador de navegação** para a cena e a versão do **Phoneword** . Isso se chama _arrastar com Ctrl_:

    ![Arraste do controlador de navegação até a cena e a versão do Phoneword](hello-ios-multiscreen-quickstart-images/06.png)

7. No pop-over, vamos definir as relações como **Raiz**:

    ![Configuração da relação como raiz](hello-ios-multiscreen-quickstart-images/07new.png)

    O **ViewController** agora é o **controlador de exibição raiz do controlador de navegação:**

    ![O ViewController agora é o controlador de exibição raiz dos controladores de navegação](hello-ios-multiscreen-quickstart-images/08.png)

8. Na tela do **Phoneword**, clique duas vezes na barra de **Título** e altere o **Título** para **Phoneword**:

    ![Altere o título para Phoneword](hello-ios-multiscreen-quickstart-images/09.png)

9. Arraste um **botão** da **Caixa de Ferramentas** e coloque-o sob o **Botão Chamar**. Arraste as alças para deixar o novo **Botão** com a mesma largura do **Botão Chamar**:

    ![Deixe o novo botão com a mesma largura que o botão Chamar](hello-ios-multiscreen-quickstart-images/10new.png)

10. No **painel de propriedades**, altere o **nome** do botão para **CallHistoryButton** e altere o **título** para histórico de **chamadas**:

    ![Altere o Nome do botão para CallHistoryButton e altere o título para Histórico de chamadas](hello-ios-multiscreen-quickstart-images/11new.png)

11. Crie a tela **Histórico de Chamadas**. Na **caixa de ferramentas**, arraste um controlador de **exibição de tabela** para a superfície de design:

    ![Arraste um controlador de exibição de tabela para a superfície de design](hello-ios-multiscreen-quickstart-images/12new.png)

12. Em seguida, selecione o **controlador de exibição de tabela** clicando na barra preta na parte inferior da cena. No **painel de propriedades**, altere a classe **do controlador de exibição de tabela** para `CallHistoryController` e pressione **Enter**:

    ![Altere a classe de controladores de exibição de tabela para CallHistoryController](hello-ios-multiscreen-quickstart-images/13new.png)

    O Designer do iOS gerará uma classe de suporte personalizada chamada `CallHistoryController` para gerenciar a hierarquia de exibição de conteúdo dessa tela. O arquivo **CallHistoryController.cs** aparecerá no **Painel de Solução**:

    ![O arquivo CallHistoryController.cs no Painel de Soluções](hello-ios-multiscreen-quickstart-images/14new.png)

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

14. Crie um _transição_ (transição) entre a cena **Phoneword** e a cena de **histórico de chamadas** .
  Na **cena Phoneword**, selecione o **botão histórico de chamadas** e Ctrl-arraste do **botão** para a cena **histórico de chamadas** :

    ![Ctrl-arraste do botão para a cena do histórico de chamadas](hello-ios-multiscreen-quickstart-images/15.png)

    Na **ação transição** popover, selecione **Mostrar**

    O Designer do iOS adicionará um Segue entre as duas cenas:

    ![O transição entre as duas cenas](hello-ios-multiscreen-quickstart-images/17new.png)

15. Adicione um **título** ao **controlador de exibição de tabela** selecionando a barra preta na parte inferior da cena e alterando o título do controlador de **exibição** para o **histórico de chamadas** no **painel de propriedades**:

    ![Alterar o título do controlador de exibição para o histórico de chamadas no Painel de Propriedades](hello-ios-multiscreen-quickstart-images/18new.png)

16. Quando o aplicativo for executado, o **Botão Histórico de Chamadas** abrirá a tela **Histórico de Chamadas**, mas a exibição de tabela estará vazia porque não há nenhum código para controlar e exibir os números de telefone.

    Esse aplicativo armazenará os números de telefone como uma lista de cadeias de caracteres.

    Adicione uma `using` diretiva para `System.Collections.Generic` na parte superior de **ViewController**:

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

    - A variável `translatedNumber` passou do método `ViewDidLoad` para uma _variável no nível de classe_.
    - O código **CallButton** foi modificado para adicionar números discados à lista de números de telefone chamando `PhoneNumbers.Add(translatedNumber)`.
    - O método `PrepareForSegue` foi adicionado.

    Salve e compile o aplicativo para garantir que não haja erros.

18. Pressione o botão **Iniciar** para iniciar o aplicativo dentro do **simulador de Ios**:

    ![Pressione o botão Iniciar para iniciar o aplicativo dentro do simulador de iOS](hello-ios-multiscreen-quickstart-images/19.png)

Parabéns por concluir seu primeiro aplicativo Xamarin.iOS multitela!

::: zone-end
::: zone pivot="windows"

## <a name="walkthrough-on-windows"></a>Passo a passo no Windows

Este passo a passo adicionará uma tela de Histórico de Chamadas ao nosso aplicativo **Phoneword**.

1. Abra o aplicativo **Phoneword** no Visual Studio. Se necessário, baixe o [aplicativo Phoneword completo](https://docs.microsoft.com/samples/xamarin/ios-samples/hello-ios) do guia [Passo a passo do Hello, iOS](~/ios/get-started/hello-ios/index.md). Lembre-se de que é necessário conectar-se a um [Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md) para usar o Designer do iOS e o Simulador de iOS.

2. Comece editando a interface do usuário. Abra o arquivo **Main.Storyboard** no **Gerenciador de Soluções**, certificando-se de que **Exibir como** esteja definido como _iPhone 6_:

    ![O Main.storyboard no Designer de iOS](hello-ios-multiscreen-quickstart-images/image1.png)

3. Arraste um **controlador de navegação** da **caixa de ferramentas** para a superfície de design:

    ![Arraste um controlador de navegação da caixa de ferramentas para a superfície de design](hello-ios-multiscreen-quickstart-images/image2.png)

4. Arraste o **transição sem origem** (essa é a seta cinza à esquerda da cena **Phoneword** ) da cena **Phoneword** para o **controlador de navegação** para alterar o ponto de partida do aplicativo:

    ![Arraste o transição sem origem para o controlador de navegação para alterar o ponto inicial do aplicativo](hello-ios-multiscreen-quickstart-images/image3.png)

5. Selecione o **controlador de exibição raiz** clicando na barra preta e pressione **delete** para removê-lo da superfície de design.
  Em seguida, mova a cena **Phoneword** ao lado do **controlador de navegação**:

    ![Mover a cena Phoneword ao lado do controlador de navegação](hello-ios-multiscreen-quickstart-images/image4.png)

6. Defina o **ViewController** como o controlador de exibição raiz do controlador de navegação. Pressione a tecla **Ctrl** e clique dentro do **Controlador de Navegação**. Deve aparecer uma linha azul. Em seguida, ainda mantendo pressionada a tecla **Ctrl** , arraste do **controlador de navegação** para a cena e a versão do **Phoneword** . Isso se chama _arrastar com Ctrl_:

    ![Arraste do controlador de navegação até a cena e a versão do Phoneword](hello-ios-multiscreen-quickstart-images/image5.png)

7. No pop-over, vamos definir as relações como **Raiz**:

    ![Defina a relação como raiz](hello-ios-multiscreen-quickstart-images/image6.png)

    O **ViewController** agora é o **controlador de exibição raiz do controlador de navegação.**

8. Na tela do **Phoneword**, clique duas vezes na barra de **Título** e altere o **Título** para **Phoneword**:

    ![Altere o título para Phoneword](hello-ios-multiscreen-quickstart-images/image7.png)

9. Arraste um **botão** da **Caixa de Ferramentas** e coloque-o sob o **Botão Chamar**. Arraste as alças para deixar o novo **Botão** com a mesma largura do **Botão Chamar**:

    ![Deixe o novo botão com a mesma largura que o botão Chamar](hello-ios-multiscreen-quickstart-images/image8.png)

10. No **Gerenciador de Propriedades**, altere o **Nome** do **Botão** para `CallHistoryButton` e altere o **Título** para **Histórico de Chamadas**:

    ![Altere o nome do botão para CallHistoryButton e o título para chamar o histórico](hello-ios-multiscreen-quickstart-images/image9.png)

11. Crie a tela **Histórico de Chamadas**. Na **caixa de ferramentas**, arraste um controlador de **exibição de tabela** para a superfície de design:

    ![Arraste um controlador de exibição de tabela para a superfície de design](hello-ios-multiscreen-quickstart-images/image10.png)

12. Selecione o **controlador de exibição de tabela** clicando na barra preta na parte inferior da cena. No **Gerenciador de propriedades**, altere a classe **do controlador de exibição de tabela** para `CallHistoryController` e pressione **Enter**:

    ![Altere a classe de controladores de exibição de tabela para CallHistoryController](hello-ios-multiscreen-quickstart-images/image11.png)

    O Designer do iOS gerará uma classe de suporte personalizada chamada `CallHistoryController` para gerenciar a hierarquia de exibição de conteúdo dessa tela. O arquivo **CallHistoryController.cs** aparecerá no **Gerenciador de Soluções**:

    ![O arquivo CallHistoryController.cs no Gerenciador de Soluções](hello-ios-multiscreen-quickstart-images/image12.png)

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

14. Crie um _transição_ (transição) entre a cena **Phoneword** e a cena de **histórico de chamadas** .
  Na **cena Phoneword**, selecione o **botão histórico de chamadas** e **Ctrl-arraste** do **botão** para a cena **histórico de chamadas** :

    ![Ctrl-arraste do botão para a cena do histórico de chamadas](hello-ios-multiscreen-quickstart-images/image13.png)

    No pop-over **Segue de Ação**, selecione **Mostrar**:

    ![Selecione Mostrar com o tipo de segue](hello-ios-multiscreen-quickstart-images/image14.png)

    O Designer do iOS adicionará um segue entre as duas cenas:

    ![O transição entre as duas cenas](hello-ios-multiscreen-quickstart-images/image15.png)

15. Adicione um **título** ao **controlador de exibição de tabela** selecionando a barra preta na parte inferior da cena e alterando o controlador de **exibição > título** para **chamar o histórico** no **Gerenciador de propriedades**:

    ![Alterar o título do controlador de exibição para o histórico de chamadas](hello-ios-multiscreen-quickstart-images/image16.png)

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

    ![A primeira tela do aplicativo de exemplo](hello-ios-multiscreen-quickstart-images/19.png)

Parabéns por concluir seu primeiro aplicativo Xamarin.iOS multitela!

::: zone-end

Agora, o aplicativo pode lidar com navegação usando tanto segues de storyboard quanto código. Agora é hora de examinar as ferramentas e as habilidades que acabamos de aprender no [Aprofundamento na multitela do Hello, iOS](~/ios/get-started/hello-ios-multiscreen/hello-ios-multiscreen-deepdive.md).

## <a name="related-links"></a>Links Relacionados

- [Hello, iOS (exemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/hello-ios)
- [Diretrizes da interface humana do iOS](https://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/Introduction/Introduction.html)
- [Portal de Provisionamento do iOS](https://developer.apple.com/ios/manage/overview/index.action)
