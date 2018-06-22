---
title: Instalação de plataforma do WPF
description: Xamarin. Forms agora tem suporte para a plataforma WPF visualização
ms.prod: xamarin
ms.assetid: 650723F2-4279-4B7B-B0A1-D7F8FF26BF1E
ms.technology: xamarin-forms
ms.custom: xamu-video
author: charlespetzold
ms.author: chape
ms.date: 04/05/2018
ms.openlocfilehash: 416e33f131c6e1ef144608f98964fd8372f454f8
ms.sourcegitcommit: e16517edcf471b53b4e347cd3fd82e485923d482
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
ms.locfileid: "33799341"
---
# <a name="wpf-platform-setup"></a>Instalação de plataforma do WPF

![Visualizar](~/media/shared/preview.png)

Xamarin. Forms agora tem suporte de visualização para o Windows Presentation Foundation (WPF). Este artigo demonstra como adicionar um projeto WPF a uma solução xamarin. Forms.

Antes de iniciar, crie uma nova solução do xamarin. Forms no Visual Studio de 2017 ou usar uma solução existente do xamarin. Forms, por exemplo, [ **BoxViewClock**](https://developer.xamarin.com/samples/xamarin-forms/BoxView/BoxViewClock/). Você só pode adicionar aplicativos do WPF para uma solução xamarin. Forms no Windows.

## <a name="add-a-wpf-project-to-a-xamarinforms-app-with-xamarinuniversity"></a>Adicionar um projeto WPF a um aplicativo xamarin. Forms com Xamarin.University

> [!VIDEO https://youtube.com/embed/Fy9N6OSxK64]

**Xamarin. Forms 3.0 WPF suporte, por [University Xamarin](https://university.xamarin.com/)**

## <a name="adding-a-wpf-app"></a>Adicionar um aplicativo WPF

Siga estas instruções para adicionar um aplicativo WPF que serão executados nas áreas de trabalho do Windows 7, 8 e 10:

1. No Visual Studio de 2017, clique no nome da solução no **Solution Explorer** e escolha **Adicionar > Novo projeto...** .

2. No **novo projeto** janela, selecione esquerda **Visual C#** e **área de trabalho clássica do Windows**. Na lista de tipos de projeto, escolha **aplicativo WPF (.NET Framework)**. 

3. Digite um nome para o projeto com uma **WPF** extensão, por exemplo, **BoxViewClock.WPF**. Clique o **procurar** botão, selecione o **BoxViewClock** pasta e pressione **Selecionar pasta**. Isso colocará o projeto no mesmo diretório que os outros projetos na solução.

    ![Adicionar um novo projeto WPF](wpf-images/add-new-project.png "adicionar um novo projeto do WPF")

    Pressione Okey para criar o projeto.

4. No **Solution Explorer**, à direita, clique no novo **BoxViewClock.WPF** do projeto e selecione **gerenciar pacotes NuGet**. Selecione o **procurar** , clique no **incluir pré-lançamento** caixa de seleção e procure **xamarin. Forms**.

    ![Selecione o pacote NuGet](wpf-images/select-nuget-package.png ", selecione o pacote do NuGet")

    Selecione em que o pacote e clique no **instalar** botão.

5. Procurar **Xamarin.Forms.Platform.WPF** empacotar e instalar também o que um. Verifique se o pacote é da Microsoft!

6. Clique com botão direito no nome da solução no **Solution Explorer** e selecione **gerenciar pacotes NuGet para solução**. Selecione o **atualização** guia e o **xamarin. Forms** pacote. Selecione todos os projetos e atualizá-los para a mesma versão do xamarin. Forms:

    ![Atualizar o pacote do NuGet](wpf-images/update-nuget-package.png "atualizar o pacote do NuGet") 

7. No projeto, clique duas vezes em **referências**. No **Gerenciador de referências** caixa de diálogo, selecione **projetos** à esquerda e verifique a caixa de seleção adjacente a **BoxViewClock** projeto:

    ![Referência ao projeto compartilhado](wpf-images/reference-shared-project.png "referência ao projeto compartilhado")

8. Editar o **MainWindow** arquivo do projeto WPF. No `Window` marca, adicione uma declaração de namespace XML para o **Xamarin.Forms.Platform.WPF** assembly e namespace:

    ```xaml
    xmlns:wpf="clr-namespace:Xamarin.Forms.Platform.WPF;assembly=Xamarin.Forms.Platform.WPF"
    ```

    Agora, altere o `Window` marca para `wpf:FormsApplicationPage`. Alterar o `Title` definindo como o nome do seu aplicativo, por exemplo, **BoxViewClock**. O arquivo XAML completo deve ter esta aparência:

    ```xaml
    <wpf:FormsApplicationPage x:Class="BoxViewClock.WPF.MainWindow"
            xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
            xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
            xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
            xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
            xmlns:wpf="clr-namespace:Xamarin.Forms.Platform.WPF;assembly=Xamarin.Forms.Platform.WPF"
            xmlns:local="clr-namespace:BoxViewClock.WPF"
            mc:Ignorable="d"
            Title="BoxViewClock" Height="450" Width="800">
        <Grid>
        
        </Grid>
    </wpf:FormsApplicationPage>
    ```

9. Editar o **MainWindow.xaml.cs** arquivo do projeto WPF. Adicionar dois novos `using` diretivas:

    ```csharp
    using Xamarin.Forms;
    using Xamarin.Forms.Platform.WPF;
    ```

    Alterar a classe base de `MainWindow` de `Window` para `FormsApplicationPage`. Após o `InitializeComponent` chamar, adicione as duas instruções a seguir:

    ```csharp
    Forms.Init();
    LoadApplication(new BoxViewClock.App());
    ```
    
    Com exceção de comentários e não utilizado `using` diretivas, completo **MainWindows.xaml.cs** arquivo deve ter esta aparência:

    ```csharp
    using Xamarin.Forms;
    using Xamarin.Forms.Platform.WPF;

    namespace BoxViewClock.WPF
    {
        public partial class MainWindow : FormsApplicationPage
        {
            public MainWindow()
            {
                InitializeComponent();

                Forms.Init();
                LoadApplication(new BoxViewClock.App());
            }
        }
    }
    ```

10. Clique com botão direito no projeto do WPF no **Solution Explorer** e selecione **definir como projeto de inicialização**. Pressione F5 para executar o programa com o depurador do Visual Studio, na área de trabalho do Windows:

    ![WPF BoxView relógio](wpf-images/wpf-boxviewclock.png "WPF BoxView relógio" )

## <a name="next-steps"></a>Próximas etapas

### <a name="platform-specifics"></a>Especificações da plataforma

Você pode determinar qual plataforma está em execução no seu aplicativo xamarin. Forms de código ou XAML. Isso permite que você altere as características de programa quando ele está em execução no WPF. No código, comparar o valor de `Device.RuntimePlatform` com o `Device.WPF` constante (que é igual a cadeia de caracteres "WPF"). Se houver uma correspondência, o aplicativo está em execução no WPF.

Em XAML, você pode usar o `OnPlatform` marca para selecionar um valor de propriedade específico da plataforma:

```xaml
<Button.TextColor>
    <OnPlatform x:TypeArguments="Color">
        <On Platform="iOS" Value="White" />
        <On Platform="macOS" Value="White" />
        <On Platform="Android" Value="Black" />
        <On Platform="WPF" Value="Blue" />
    </OnPlatform>
</Button.TextColor>
```

### <a name="window-size"></a>Tamanho da janela

Você pode ajustar o tamanho inicial da janela em WPF **MainWindow** arquivo:

```xaml
Title="BoxViewClock" Height="450" Width="800"
```

## <a name="issues"></a>Problemas

Esta é uma visualização, portanto você deve esperar que nem tudo é preparado para produção. Nem todos os pacotes do NuGet do xamarin. Forms estão prontos para WPF e alguns recursos podem não estar funcionando completamente.

