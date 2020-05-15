---
title: Reutilizar páginas Xamarin. Forms em uma extensão do iOS
description: Este documento descreve as extensões do iOS e como usar páginas Xamarin. Forms nelas. Ele contém instruções sobre como criar uma extensão, integrar o Xamarin. Forms e renderizar um ContentPage simples de forma nativa em um projeto de extensão do iOS.
ms.prod: xamarin
ms.assetid: 50076FFD-3EF6-41CC-BC7E-210DE3958F5B
ms.technology: xamarin-ios
author: alexeystrakh
ms.author: alstrakh
ms.date: 05/13/2020
ms.openlocfilehash: 4006bb3ef82264b5a7a6d764719bee81a8ce78a1
ms.sourcegitcommit: 5bc37b384706bfbf5bf8e5b1288a34ddd0f3303b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/15/2020
ms.locfileid: "83418010"
---
# <a name="reuse-xamarinforms-pages-in-an-ios-extension"></a>Reutilizar páginas Xamarin. Forms em uma extensão do iOS

as extensões do iOS permitem que você personalize o comportamento do sistema existente adicionando funcionalidade extra a [predefinidas por pontos de extensão do IOS e MacOS](https://developer.apple.com/library/archive/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214-CH20-SW2), como ações de contexto personalizadas, AutoPreenchimento de senha, filtros de chamadas de entrada, modificadores de conteúdo de notificação e muito mais. O Xamarin. iOS dá suporte a extensões e [este guia](https://docs.microsoft.com/xamarin/ios/platform/extensions) irá orientá-lo na criação de uma extensão do IOS usando as ferramentas do Xamarin.

As extensões são distribuídas como parte de um aplicativo de contêiner e ativadas de um ponto de extensão específico em um aplicativo host. O aplicativo de contêiner é geralmente um aplicativo iOS simples, que fornece a um usuário informações sobre a extensão, como ativá-la e usá-la. Há três abordagens principais para compartilhar código entre uma extensão e um aplicativo de contêiner:

1. Projeto iOS comum.

    Você pode colocar todo o código compartilhado entre o contêiner e a extensão em uma biblioteca do iOS compartilhada e fazer referência à biblioteca de ambos os projetos. Normalmente, a biblioteca compartilhada contém UIViewControllers nativos e deve ser uma biblioteca do Xamarin. iOS.

1. Links de arquivo.

    Em alguns casos, o aplicativo de contêiner fornece a maior parte da funcionalidade, enquanto a extensão precisa renderizar um único `UIViewController` . Com poucos arquivos para compartilhar, é comum adicionar um link de arquivo ao aplicativo de extensão do arquivo localizado no aplicativo de contêiner.

1. Projeto do Xamarin. Forms comum.

    Se as páginas de aplicativo já estiverem compartilhadas com outra plataforma, como o Android, usando o Xamarin. Forms Framework, a abordagem comum é reimplementar as páginas necessárias nativamente no projeto de extensão, pois a extensão do iOS funciona com as páginas UIViewControllers nativas e não Xamarin. Forms. Você precisa executar etapas adicionais para usar o Xamarin. Forms na extensão do iOS, que são explicadas abaixo.

## <a name="xamarinforms-in-an-ios-extension-project"></a>Xamarin. Forms em um projeto de extensão do iOS

A capacidade de usar o Xamarin. Forms em um projeto nativo é fornecida por meio de [formulários nativos](~/xamarin-forms/platform/native-forms.md). Ele permite que `ContentPage` páginas derivadas sejam adicionadas diretamente a projetos nativos do Xamarin. Ios. O `CreateViewController` método de extensão converte uma instância de uma página Xamarin. Forms em um nativo `UIViewController` , que pode ser usado ou modificado como um controlador regular. Como uma extensão do iOS é um tipo especial de um projeto nativo do iOS, você também pode usar **formulários nativos** aqui.

> [!IMPORTANT]
> Há muitas [limitações conhecidas](https://docs.microsoft.com/xamarin/ios/platform/extensions#limitations) para as extensões do Ios. Embora seja possível usar o Xamarin. Forms em uma extensão do iOS, você deve fazer isso com muito cuidado, monitorar o uso da memória e o tempo de inicialização. Caso contrário, a extensão poderia ser encerrada pelo iOS sem qualquer maneira de lidar com isso normalmente.

## <a name="walkthrough"></a>Passo a passo

Neste tutorial, você vai criar um aplicativo Xamarin. Forms, uma extensão Xamarin. iOS e reutilizar o código compartilhado no projeto de extensão:

1. Abra Visual Studio para Mac, crie um novo projeto Xamarin. Forms usando o modelo de **aplicativo de formulários em branco** e nomeie-o **FormsShareExtension**:

    ![Criar projeto](extensions-xf-images/1.walkthrough-createproject.png)

1. Em **FormsShareExtension/MainPage. XAML**, substitua o conteúdo pelo seguinte layout:

    ```xaml
    <?xml version="1.0" encoding="utf-8" ?>
    <ContentPage
        x:Class="FormsShareExtension.MainPage"
        xmlns="http://xamarin.com/schemas/2014/forms"
        xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
        xmlns:d="http://xamarin.com/schemas/2014/forms/design"
        xmlns:local="clr-namespace:FormsShareExtension"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        x:DataType="local:MainPageViewModel"
        BackgroundColor="Orange"
        mc:Ignorable="d">
        <ContentPage.BindingContext>
            <local:MainPageViewModel Message="Hello from Xamarin.Forms!" />
        </ContentPage.BindingContext>
        <StackLayout HorizontalOptions="Center" VerticalOptions="Center">
            <Label
                Margin="20"
                Text="{Binding Message}"
                VerticalOptions="CenterAndExpand" />
            <Button Command="{Binding DoCommand}" Text="Do the job!" />
        </StackLayout>
    </ContentPage>
    ```

1. Adicione uma nova classe chamada **MainPageViewMode** ao projeto **FormsShareExtension** e substitua o conteúdo da classe pelo código a seguir:

    ```csharp
    using System;
    using System.ComponentModel;
    using System.Windows.Input;
    using Xamarin.Forms;

    namespace FormsShareExtension
    {
        public class MainPageViewModel : INotifyPropertyChanged
        {
            public event PropertyChangedEventHandler PropertyChanged;

            private string _message;
            public string Message
            {
                get { return _message; }
                set
                {
                    if (_message != value)
                    {
                        _message = value;
                        PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(nameof(Message)));
                    }
                }
            }

            private ICommand _doCommand;
            public ICommand DoCommand
            {
                get { return _doCommand; }
                set
                {
                    if(_doCommand != value)
                    {
                        _doCommand = value;
                        PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(nameof(DoCommand)));
                    }
                }
            }

            public MainPageViewModel()
            {
                DoCommand = new Command(OnDoCommandExecuted);
            }

            private void OnDoCommandExecuted(object state)
            {
                Message = $"Job {Environment.TickCount} has been completed!";
            }
        }
    }
    ```

    O código é compartilhado entre todas as plataformas e também será usado por uma extensão do iOS.

1. No painel de solução, clique com o botão direito do mouse na solução, selecione **adicionar > novo projeto > extensão de > do iOS > extensão de ação**, nomeie-o como **myaction** e pressione **criar**:

    ![Criar extensão](extensions-xf-images/2.walkthrough-createextension.png)

1. Para usar o Xamarin. Forms na extensão do iOS e o código compartilhado, você precisa adicionar as referências necessárias:

    - Clique com o botão direito do mouse na extensão do iOS, selecione **referências > adicionar projetos > de referência > FormsShareExtension** e pressione **OK**.

    - Clique com o botão direito do mouse na extensão do iOS, selecione **pacotes > gerenciar pacotes NuGet... > Xamarin. Forms** e pressione **Adicionar pacote**.

1. Expanda o projeto de extensão e modifique um ponto de entrada para inicializar Xamarin. Forms e criar páginas. Por requisitos do iOS, uma extensão deve definir o ponto de entrada no **info. plist** como `NSExtensionMainStoryboard` ou `NSExtensionPrincipalClass` . Quando o ponto de entrada é ativado, nesse caso é o `ActionViewController.ViewDidLoad` método, você pode criar uma instância de uma página Xamarin. Forms e mostrá-la a um usuário. Portanto, abra o ponto de entrada e substitua o `ViewDidLoad` método pela seguinte implementação:

    ```csharp
    public override void ViewDidLoad()
    {
        base.ViewDidLoad();

        // Initialize Xamarin.Forms framework
        global::Xamarin.Forms.Forms.Init();
        // Create an instance of XF page with associated View Model
        var xfPage = new MainPage();
        var viewModel = (MainPageViewModel)xfPage.BindingContext;
        viewModel.Message = "Welcome to XF Page created from an iOS Extension";
        // Override the behavior to complete the execution of the Extension when a user press the button
        viewModel.DoCommand = new Command(() => DoneClicked(this));
        // Convert XF page to a native UIViewController which can be consumed by the iOS Extension
        var newController = xfPage.CreateViewController();
        // Present new view controller as a regular view controller
        this.PresentModalViewController(newController, false);
    }
    ```

    O `MainPage` é instanciado usando um construtor padrão e antes de ser usado na extensão, converta-o em um nativo `UIViewController` usando o `CreateViewController` método de extensão. 
    
    Compilar e executar o aplicativo:

    ![Criar extensão](extensions-xf-images/3.walkthrough-runapp.png)

    Para ativar a extensão, navegue até o navegador Safari, digite qualquer endereço da Web, por exemplo, [Microsoft.com](https://microsoft.com), pressione navegar e, em seguida, pressione o ícone **compartilhar** na parte inferior da página para ver as extensões de ação disponíveis. Na lista de extensões disponíveis, selecione a extensão **myaction** tocando nela:

    ![Criar extensão](extensions-xf-images/4.walkthrough-run1.png) ![Criar extensão](extensions-xf-images/5.walkthrough-run2.png) ![Criar extensão](extensions-xf-images/6.walkthrough-run3.png)

    A extensão é ativada e a página Xamarin. Forms é exibida para o usuário. Todas as associações e comandos funcionam como no aplicativo de contêiner.

1. O controlador de exibição de ponto de entrada original é visível porque é criado e ativado pelo iOS. Para corrigir isso, altere o estilo de apresentação modal para `UIModalPresentationStyle.FullScreen` para o novo controlador adicionando o código a seguir logo antes da `PresentModalViewController` chamada:

    ```csharp
    newController.ModalPresentationStyle = UIModalPresentationStyle.FullScreen;
    ```

    Compile e execute no simulador de iOS ou em um dispositivo:

    ![Xamarin. Forms na extensão do iOS](extensions-xf-images/7.walkthrough-result.png)

    > [!IMPORTANT]
    > Para o Build do dispositivo, certifique-se de usar as configurações de compilação apropriadas e a configuração de **versão** , conforme [descrito aqui](~/iOS/platform/extensions.md#debug-and-release-versions-of-extensions).

## <a name="related-links"></a>Links relacionados

- [Extensões do iOS no Xamarin. iOS](~/iOS/platform/extensions.md)
- [Xamarin. Forms em projetos nativos do Xamarin](~/xamarin-forms/platform/native-forms.md)
- [Otimizar a eficiência e o desempenho de uma extensão de aplicativo iOS](https://developer.apple.com/library/archive/documentation/General/Conceptual/ExtensibilityPG/ExtensionCreation.html#//apple_ref/doc/uid/TP40014214-CH5-SW7)
- [Exemplo de código fonte](https://github.com/xamcat/xamarin-forms-ios-extension)
