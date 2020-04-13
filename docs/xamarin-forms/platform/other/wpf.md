---
title: Configuração da plataforma WPF
description: Xamarin.Forms agora tem suporte de pré-visualização para a plataforma WPF
ms.prod: xamarin
ms.assetid: 650723F2-4279-4B7B-B0A1-D7F8FF26BF1E
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 04/09/2020
ms.openlocfilehash: c9ec9fec2391d7d7a24f97f2ec20208a7d69dbc1
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80992331"
---
# <a name="wpf-platform-setup"></a>Configuração da plataforma WPF

![Visualização](~/media/shared/preview.png)

Xamarin.Forms agora tem suporte de pré-visualização para o Windows Presentation Foundation (WPF). Este artigo demonstra como adicionar um projeto WPF a uma solução Xamarin.Forms.

> [!IMPORTANT]
> O suporte ao Xamarin.Forms para OWPF é fornecido pela comunidade. Para obter mais informações, consulte [Xamarin.Forms Platform Support](https://github.com/xamarin/Xamarin.Forms/wiki/Platform-Support).

Antes de começar, crie uma nova solução Xamarin.Forms no Visual Studio 2019 ou use uma solução Xamarin.Forms existente, por exemplo, [**BoxViewClock**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-boxviewclock). Você só pode adicionar aplicativos WPF a uma solução Xamarin.Forms no Windows.

## <a name="add-a-wpf-application"></a>Adicionar um aplicativo WPF

Siga estas instruções para adicionar um aplicativo WPF que será executado nos desktops Windows 7, 8 e 10:

1. No Visual Studio 2019, clique com o botão direito do mouse sobre o nome da solução no **Solution Explorer** e escolha Adicionar > **Novo Projeto...**.

2. Na Nova janela **de projeto,** selecione **C#** nas **linguagens,** selecione **Windows** nas **plataformas** que dado e selecione **Desktop** no **tipo Projeto** que dado. Na lista de tipos de projetos, escolha **wpf app (.NET Framework)**:

    ![Adicionar um novo projeto WPF](wpf-images/add-project.png "Adicionar um novo projeto WPF")

    Pressione o **botão Seguinte.**

3. Na Configuração da nova janela **de projeto, digite** um nome para o projeto com uma extensão **WPF,** por exemplo, **BoxViewClock.WPF**. Clique no botão **Procurar,** selecione a pasta **BoxViewClock** e **pressione Select Folder** para colocar o projeto WPF no mesmo diretório que os outros projetos na solução:

    ![Adicionar um novo projeto WPF](wpf-images/configure-project.png "Adicionar um novo projeto WPF")

    Pressione o botão **Criar** para criar o projeto.

4. No **Solution Explorer,** clique com o botão direito do mouse no novo projeto **BoxViewClock.WPF** e selecione **Gerenciar pacotes NuGet...**. Selecione a guia **Procurar** e procure **xamarin.forms.Platform.WPF**:

    ![Selecione o pacote NuGet](wpf-images/select-nuget-package.png "Selecione o pacote NuGet")

    Selecione o pacote e clique no botão **Instalar.**

5. Clique com o botão direito do mouse no nome da solução no **Solution Explorer** e selecione **Gerenciar pacotes NuGet para solução...**. Selecione a guia **Atualizações** e selecione o pacote **Xamarin.Forms.** Selecione todos os projetos e atualize-os para a mesma versão xamarin.Forms:

    ![Atualize o pacote NuGet](wpf-images/update-nuget-package.png "Atualize o pacote NuGet")

6. No projeto WPF, clique com o botão direito do mouse em **Referências** e **selecione Adicionar referência...**. Na caixa de diálogo **Gerenciador de** referência, selecione **Projetos** à esquerda e marque a caixa de seleção adjacente ao projeto **BoxViewClock:**

    ![Referenciar o projeto compartilhado](wpf-images/reference-shared-project.png "Referenciar o projeto compartilhado")

    Pressione o botão **OK.**

7. Editar o arquivo **MainWindow.xaml** do projeto WPF. Na `Window` tag, adicione uma declaração de namespace XML para o **conjunto Xamarin.Forms.Platform.WPF** e namespace:

    ```xaml
    xmlns:wpf="clr-namespace:Xamarin.Forms.Platform.WPF;assembly=Xamarin.Forms.Platform.WPF"
    ```

    Agora mude `Window` a `wpf:FormsApplicationPage`tag para . Alterar `Title` a configuração para o nome do seu aplicativo, por exemplo, **BoxViewClock**. O arquivo XAML concluído deve ficar assim:

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

8. Edite o arquivo **MainWindow.xaml.cs** do projeto WPF. Adicione duas `using` novas diretivas:

    ```csharp
    using Xamarin.Forms;
    using Xamarin.Forms.Platform.WPF;
    ```

    Mude a classe `MainWindow` `Window` base `FormsApplicationPage`de para . Após `InitializeComponent` a chamada, adicione as duas seguintes instruções:

    ```csharp
    Forms.Init();
    LoadApplication(new BoxViewClock.App());
    ```

    Exceto por comentários `using` e diretivas não utilizadas, o arquivo **MainWindows.xaml.cs** completo deve ser assim:

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

9. Clique com o botão direito do mouse no projeto WPF no **Solution Explorer** e selecione Set como Projeto **de Inicialização**. Pressione F5 para executar o programa com o depurador visual studio na área de trabalho do Windows:

    ![Relógio WPF BoxView](wpf-images/wpf-boxviewclock.png "Relógio WPF BoxView" )

## <a name="platform-specifics"></a>Especificidades da plataforma

Você pode determinar em qual plataforma seu aplicativo Xamarin.Forms está sendo executado a partir de código ou XAML. Isso permite que você altere as características do programa quando ele está sendo executado no WPF. Em código, compare `Device.RuntimePlatform` o `Device.WPF` valor da constante (que é igual à string "WPF"). Se houver uma correspondência, o aplicativo está sendo executado no WPF.

No XAML, você `OnPlatform` pode usar a tag para selecionar um valor de propriedade específico para a plataforma:

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

Você pode ajustar o tamanho inicial da janela no arquivo WPF **MainWindow.xaml:**

```xaml
Title="BoxViewClock" Height="450" Width="800"
```

## <a name="issues"></a>Problemas

Esta é uma prévia, então você deve esperar que nem tudo esteja pronto para a produção. Nem todos os pacotes NuGet para Xamarin.Forms estão prontos para WPF, e alguns recursos podem não estar funcionando totalmente.

## <a name="related-video"></a>Vídeo relacionado

> [!VIDEO https://youtube.com/embed/Fy9N6OSxK64]

**Xamarin.Forms 3.0 WPF suporta vídeo**
