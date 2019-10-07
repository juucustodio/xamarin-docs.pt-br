---
title: Configuração da plataforma WPF
description: O Xamarin. Forms agora tem suporte de visualização para a plataforma WPF
ms.prod: xamarin
ms.assetid: 650723F2-4279-4B7B-B0A1-D7F8FF26BF1E
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 04/05/2018
ms.openlocfilehash: 38d9b42b3a29ea46d05a1d1cc4e38641d2445786
ms.sourcegitcommit: 4cf434b126eb7df6b2fd9bb1d71613bf2b6aac0e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/07/2019
ms.locfileid: "71997178"
---
# <a name="wpf-platform-setup"></a>Configuração da plataforma WPF

![Visualizar](~/media/shared/preview.png)

O Xamarin. Forms agora tem suporte de visualização para o Windows Presentation Foundation (WPF). Este artigo demonstra como adicionar um projeto do WPF a uma solução Xamarin. Forms.

> [!IMPORTANT]
> O suporte para o Xamarin. Forms para WPF é fornecido pela Comunidade. Para obter mais informações, consulte [suporte à plataforma Xamarin. Forms](https://github.com/xamarin/Xamarin.Forms/wiki/Platform-Support).

Antes de começar, crie uma nova solução Xamarin. Forms no Visual Studio 2019 ou use uma solução Xamarin. Forms existente, por exemplo, [**BoxViewClock**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-boxviewclock). Você só pode adicionar aplicativos do WPF a uma solução Xamarin. Forms no Windows.

## <a name="add-a-wpf-project-to-a-xamarinforms-app-with-xamarinuniversity"></a>Adicionar um projeto do WPF a um aplicativo Xamarin. Forms com Xamarin. University

> [!VIDEO https://youtube.com/embed/Fy9N6OSxK64]

**Vídeo de suporte do Xamarin. Forms 3,0 WPF**

## <a name="adding-a-wpf-app"></a>Adicionando um aplicativo WPF

Siga estas instruções para adicionar um aplicativo do WPF que será executado no Windows 7, 8 e 10 desktops:

1. No Visual Studio 2019, clique com o botão direito do mouse no nome da solução na **Gerenciador de soluções** e escolha **Adicionar > novo projeto...** .

2. Na janela **novo projeto** , à esquerda, selecione **Visual C#**  e **área de trabalho clássica do Windows**. Na lista de tipos de projeto, escolha **aplicativo WPF (.NET Framework)** .

3. Digite um nome para o projeto com uma extensão do **WPF** , por exemplo, **BoxViewClock. WPF**. Clique no botão **procurar** , selecione a pasta **BoxViewClock** e pressione **Selecionar pasta**. Isso colocará o projeto do WPF no mesmo diretório que os outros projetos na solução.

    ![Adicionar um novo projeto do WPF](wpf-images/add-new-project.png "Adicionar um novo projeto do WPF")

    Pressione OK para criar o projeto.

4. Na **Gerenciador de soluções**, clique com o botão direito do mouse no novo projeto **BoxViewClock. WPF** e selecione **gerenciar pacotes NuGet**. Selecione a guia **procurar** , clique na caixa de seleção **incluir pré-lançamento** e procure **Xamarin. Forms**.

    ![Selecione o pacote NuGet](wpf-images/select-nuget-package.png "Selecione o pacote NuGet")

    Selecione esse pacote e clique no botão **instalar** .

5. Agora, pesquise o pacote **Xamarin. Forms. Platform. WPF** e instale-o também. Verifique se o pacote é da Microsoft!

6. Clique com o botão direito do mouse no nome da solução na **Gerenciador de soluções** e selecione **gerenciar pacotes NuGet para solução**. Selecione a guia **Atualizar** e o pacote **Xamarin. Forms** . Selecione todos os projetos e atualize-os para a mesma versão do Xamarin. Forms:

    ![Atualizar o pacote NuGet](wpf-images/update-nuget-package.png "atualizar o pacote NuGet")

7. No projeto do WPF, clique com o botão direito do mouse em **referências**. Na caixa de diálogo **Gerenciador de referências** , selecione **projetos** à esquerda e marque a caixa de seleção adjacente ao projeto **BoxViewClock** :

    ![Referenciar o projeto compartilhado](wpf-images/reference-shared-project.png "referenciar o projeto compartilhado")

8. Edite o arquivo **MainWindow. XAML** do projeto do WPF. Na marca `Window`, adicione uma declaração de namespace XML para o assembly e o namespace do **Xamarin. Forms. Platform. WPF** :

    ```xaml
    xmlns:wpf="clr-namespace:Xamarin.Forms.Platform.WPF;assembly=Xamarin.Forms.Platform.WPF"
    ```

    Agora, altere a marca `Window` para `wpf:FormsApplicationPage`. Altere a configuração `Title` para o nome do seu aplicativo, por exemplo, **BoxViewClock**. O arquivo XAML concluído deve ter a seguinte aparência:

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

9. Edite o arquivo **MainWindow.XAML.cs** do projeto do WPF. Adicione duas novas diretivas `using`:

    ```csharp
    using Xamarin.Forms;
    using Xamarin.Forms.Platform.WPF;
    ```

    Altere a classe base de `MainWindow` de `Window` para `FormsApplicationPage`. Seguindo a chamada `InitializeComponent`, adicione as duas instruções a seguir:

    ```csharp
    Forms.Init();
    LoadApplication(new BoxViewClock.App());
    ```

    Exceto para comentários e diretivas `using` não utilizadas, o arquivo **MainWindows.XAML.cs** completo deve ter a seguinte aparência:

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

10. Clique com o botão direito do mouse no projeto do WPF no **Gerenciador de soluções** e selecione **definir como projeto de inicialização**. Pressione F5 para executar o programa com o depurador do Visual Studio na área de trabalho do Windows:

    Relógio WPF ![BoxView Clock]WPF(wpf-images/wpf-boxviewclock.png "BoxView" )

## <a name="next-steps"></a>Próximas etapas

### <a name="platform-specifics"></a>Especificações da plataforma

Você pode determinar em qual plataforma seu aplicativo Xamarin. Forms está em execução a partir do código ou XAML. Isso permite que você altere as características do programa quando ele está em execução no WPF. No código, compare o valor de `Device.RuntimePlatform` com a constante `Device.WPF` (que é igual à cadeia de caracteres "WPF"). Se houver uma correspondência, o aplicativo será executado no WPF.

Em XAML, você pode usar a marca `OnPlatform` para selecionar um valor de propriedade específico para a plataforma:

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

Você pode ajustar o tamanho inicial da janela no arquivo **MainWindow. XAML** do WPF:

```xaml
Title="BoxViewClock" Height="450" Width="800"
```

## <a name="issues"></a>Problemas

Esta é uma visualização, portanto, você deve esperar que nem tudo está pronto para produção. Nem todos os pacotes NuGet do Xamarin. Forms estão prontos para o WPF e alguns recursos podem não estar totalmente funcionando.
