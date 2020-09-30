---
title: Configuração da plataforma WPF
description: Xamarin.Forms tem suporte de visualização para a plataforma WPF.
ms.prod: xamarin
ms.assetid: 650723F2-4279-4B7B-B0A1-D7F8FF26BF1E
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 05/20/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 17db86eb6e6c767498f1d8b550b923377b905364
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91557433"
---
# <a name="wpf-platform-setup"></a>Configuração da plataforma WPF

![Versão Prévia](~/media/shared/preview.png)

Xamarin.Forms tem suporte de visualização para o Windows Presentation Foundation (WPF), em .NET Framework e no .NET Core 3. Este artigo demonstra como adicionar um projeto do WPF que se destina .NET Framework a uma Xamarin.Forms solução.

> [!IMPORTANT]
> Xamarin.Forms o suporte para o WPF é fornecido pela Comunidade. Para obter mais informações, consulte [ Xamarin.Forms suporte a plataformas](https://github.com/xamarin/Xamarin.Forms/wiki/Platform-Support).

Antes de começar, crie uma nova Xamarin.Forms solução no Visual Studio 2019 ou use uma solução existente Xamarin.Forms , por exemplo, [**BoxViewClock**](/samples/xamarin/xamarin-forms-samples/boxview-boxviewclock). Você só pode adicionar aplicativos WPF a uma Xamarin.Forms solução no Windows.

## <a name="add-a-wpf-application"></a>Adicionar um aplicativo WPF

Siga estas instruções para adicionar um aplicativo WPF que será executado no Windows 7, 8 e 10 desktops:

1. No Visual Studio 2019, clique com o botão direito do mouse no nome da solução na **Gerenciador de soluções** e escolha **Adicionar > novo projeto...**.

2. Na janela **Adicionar um novo projeto** , selecione **C#** na lista suspensa **idiomas** , selecione **janelas** na lista suspensa **plataformas** e selecione **área de trabalho** na lista suspensa **tipo de projeto** . Na lista de tipos de projeto, escolha **aplicativo WPF (.NET Framework)**:

    ![Adicionar um novo projeto do WPF](wpf-images/add-project.png "Adicionar um novo projeto do WPF")

    Pressione o botão **Avançar** .

    > [!NOTE]
    > Xamarin.Forms 4,7 inclui suporte para aplicativos do WPF que são executados no .NET Core 3.

3. Na janela **configurar seu novo projeto** , digite um nome para o projeto com uma extensão do **WPF** , por exemplo, **BoxViewClock. WPF**. Clique no botão **procurar** , selecione a pasta **BoxViewClock** e pressione **Selecionar pasta** para colocar o projeto do WPF no mesmo diretório que os outros projetos na solução:

    ![Adicionar um novo projeto do WPF](wpf-images/configure-project.png "Adicionar um novo projeto do WPF")

    Pressione o botão **criar** para criar o projeto.

4. Na **Gerenciador de soluções**, clique com o botão direito do mouse no novo projeto **BoxViewClock. WPF** e selecione **gerenciar pacotes NuGet...**. Selecione a guia **procurar** e procure ** Xamarin.Forms . Platform. WPF**:

    ![Selecione o pacote NuGet](wpf-images/select-nuget-package.png "Selecione o pacote NuGet")

    Selecione o pacote e clique no botão **instalar** .

5. Clique com o botão direito do mouse no nome da solução na **Gerenciador de soluções** e selecione **gerenciar pacotes NuGet para solução...**. Selecione a guia **atualizações** e, em seguida, selecione o **Xamarin.Forms** pacote. Selecione todos os projetos e atualize-os para a mesma Xamarin.Forms versão:

    ![Atualizar o pacote NuGet](wpf-images/update-nuget-package.png "Atualizar o pacote NuGet")

6. No projeto do WPF, clique com o botão direito do mouse em **referências** e selecione **Adicionar referência...**. Na caixa de diálogo **Gerenciador de referências** , selecione **projetos** à esquerda e marque a caixa de seleção adjacente ao projeto **BoxViewClock** :

    ![Referenciar o projeto compartilhado](wpf-images/reference-shared-project.png "Referenciar o projeto compartilhado")

    Pressione o botão **OK** .

7. Edite o arquivo **MainWindow. XAML** do projeto do WPF. Na `Window` marca, adicione uma declaração de namespace XML para o ** Xamarin.Forms . **Assembly e namespace do Platform. WPF:

    ```xaml
    xmlns:wpf="clr-namespace:Xamarin.Forms.Platform.WPF;assembly=Xamarin.Forms.Platform.WPF"
    ```

    Agora, altere a `Window` marca para `wpf:FormsApplicationPage` . Altere a `Title` configuração para o nome do seu aplicativo, por exemplo, **BoxViewClock**. O arquivo XAML concluído deve ter a seguinte aparência:

    ```xaml
    <wpf:FormsApplicationPage x:Class="BoxViewClock.WPF.MainWindow"
            xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
            xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
            xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
            xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
            xmlns:local="clr-namespace:BoxViewClock.WPF"
            xmlns:wpf="clr-namespace:Xamarin.Forms.Platform.WPF;assembly=Xamarin.Forms.Platform.WPF"            
            mc:Ignorable="d"
            Title="BoxViewClock" Height="450" Width="800">
        <Grid>

        </Grid>
    </wpf:FormsApplicationPage>
    ```

8. Edite o arquivo **MainWindow.XAML.cs** do projeto do WPF. Adicione duas novas `using` diretivas:

    ```csharp
    using Xamarin.Forms;
    using Xamarin.Forms.Platform.WPF;
    ```

    Altere a classe base de `MainWindow` de `Window` para `FormsApplicationPage` . Após a `InitializeComponent` chamada, adicione as duas instruções a seguir:

    ```csharp
    Forms.Init();
    LoadApplication(new BoxViewClock.App());
    ```

    Exceto para comentários e diretivas não usadas `using` , o arquivo **MainWindows.XAML.cs** completo deve ser assim:

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

9. Clique com o botão direito do mouse no projeto do WPF no **Gerenciador de soluções** e selecione **definir como projeto de inicialização**. Pressione F5 para executar o programa com o depurador do Visual Studio na área de trabalho do Windows:

    ![Relógio do WPF BoxView](wpf-images/wpf-boxviewclock.png "Relógio do WPF BoxView" )

## <a name="platform-specifics"></a>Especificações da plataforma

Você pode determinar em qual plataforma seu Xamarin.Forms aplicativo está em execução a partir do código ou XAML. Isso permite que você altere as características do programa quando ele está em execução no WPF. No código, compare o valor de `Device.RuntimePlatform` com a `Device.WPF` constante (que é igual à cadeia de caracteres "WPF"). Se houver uma correspondência, o aplicativo será executado no WPF.

Em XAML, você pode usar a `OnPlatform` marca para selecionar um valor de propriedade específico para a plataforma:

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

## <a name="window-size"></a>Tamanho da janela

Você pode ajustar o tamanho inicial da janela no arquivo **MainWindow. XAML** do WPF:

```xaml
Title="BoxViewClock" Height="450" Width="800"
```

## <a name="issues"></a>Problemas

Esta é uma visualização, portanto, você deve esperar que nem tudo está pronto para produção. Nem todos os pacotes NuGet do Xamarin.Forms estão prontos para o WPF, e alguns recursos podem não estar totalmente funcionando.

## <a name="related-video"></a>Vídeo relacionado

> [!VIDEO https://youtube.com/embed/Fy9N6OSxK64]

**Xamarin.Forms vídeo de suporte do WPF 3,0**