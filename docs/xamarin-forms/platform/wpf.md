---
title: Instalação de plataforma do WPF
description: Xamarin. Forms agora tem suporte de visualização para a plataforma do WPF
ms.prod: xamarin
ms.assetid: 650723F2-4279-4B7B-B0A1-D7F8FF26BF1E
ms.technology: xamarin-forms
ms.custom: xamu-video
author: charlespetzold
ms.author: chape
ms.date: 04/05/2018
ms.openlocfilehash: 416e33f131c6e1ef144608f98964fd8372f454f8
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38831313"
---
# <a name="wpf-platform-setup"></a>Instalação de plataforma do WPF

![Visualizar](~/media/shared/preview.png)

Xamarin. Forms agora tem suporte de visualização para o Windows Presentation Foundation (WPF). Este artigo demonstra como adicionar um projeto do WPF para uma solução xamarin. Forms.

Antes de começar, crie uma nova solução xamarin. Forms no Visual Studio 2017 ou usar uma solução existente do xamarin. Forms, por exemplo, [ **BoxViewClock**](https://developer.xamarin.com/samples/xamarin-forms/BoxView/BoxViewClock/). Você só pode adicionar aplicativos do WPF a uma solução do xamarin. Forms no Windows.

## <a name="add-a-wpf-project-to-a-xamarinforms-app-with-xamarinuniversity"></a>Adicione um projeto WPF a um aplicativo xamarin. Forms com Xamarin.University

> [!VIDEO https://youtube.com/embed/Fy9N6OSxK64]

**Xamarin. Forms 3.0 WPF dão suporte, por [Xamarin University](https://university.xamarin.com/)**

## <a name="adding-a-wpf-app"></a>Adicionar um aplicativo do WPF

Siga estas instruções para adicionar um aplicativo do WPF que serão executados nas áreas de trabalho do Windows 7, 8 e 10:

1. No Visual Studio 2017, clique com botão direito no nome da solução nos **Gerenciador de soluções** e escolha **Adicionar > Novo projeto...** .

2. No **novo projeto** janela, à esquerda, selecione **Visual c#** e **área de trabalho clássica do Windows**. Na lista de tipos de projeto, escolha **aplicativo WPF (.NET Framework)**. 

3. Digite um nome para o projeto com um **WPF** extensão, por exemplo, **BoxViewClock.WPF**. Clique o **navegue** botão, selecione o **BoxViewClock** pasta e pressione **Selecionar pasta**. Isso colocará o projeto WPF no mesmo diretório que os outros projetos na solução.

    ![Adicionar um novo projeto WPF](wpf-images/add-new-project.png "adicionar um novo projeto WPF")

    Pressione Okey para criar o projeto.

4. No **Gerenciador de soluções**, à direita, clique no botão new **BoxViewClock.WPF** do projeto e selecione **Manage NuGet Packages**. Selecione o **navegue** , clique no **incluir pré-lançamento** caixa de seleção e pesquise **xamarin. Forms**.

    ![Selecione o pacote NuGet](wpf-images/select-nuget-package.png "selecione o pacote NuGet")

    Selecione em que o pacote e clique no **instalar** botão.

5. Agora pesquise **Xamarin.Forms.Platform.WPF** empacotar e instalar aqui também. Verifique se que o pacote é da Microsoft!

6. Clique com botão direito no nome da solução nos **Gerenciador de soluções** e selecione **gerenciar pacotes NuGet para solução**. Selecione o **atualização** guia e o **xamarin. Forms** pacote. Selecione todos os projetos e atualizá-los para a mesma versão do xamarin. Forms:

    ![Atualizar o pacote do NuGet](wpf-images/update-nuget-package.png "atualizar o pacote NuGet") 

7. No projeto do WPF, clique duas vezes em **referências**. No **Gerenciador de referências** caixa de diálogo, selecione **projetos** na esquerda e verifique a caixa de seleção adjacente para o **BoxViewClock** projeto:

    ![Referência ao projeto compartilhado](wpf-images/reference-shared-project.png "referência ao projeto compartilhado")

8. Editar o **MainWindow. XAML** arquivo do projeto WPF. No `Window` marca, adicione uma declaração de namespace XML para o **Xamarin.Forms.Platform.WPF** assembly e namespace:

    ```xaml
    xmlns:wpf="clr-namespace:Xamarin.Forms.Platform.WPF;assembly=Xamarin.Forms.Platform.WPF"
    ```

    Agora, altere a `Window` marca a `wpf:FormsApplicationPage`. Alterar o `Title` definindo como o nome do seu aplicativo, por exemplo, **BoxViewClock**. O arquivo XAML concluído deve ter esta aparência:

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

9. Editar o **MainWindow.xaml.cs** arquivo do projeto WPF. Adicione dois novos `using` diretivas:

    ```csharp
    using Xamarin.Forms;
    using Xamarin.Forms.Platform.WPF;
    ```

    Altere a classe base da `MainWindow` partir `Window` para `FormsApplicationPage`. Seguindo o `InitializeComponent` chamar, adicione as duas instruções a seguir:

    ```csharp
    Forms.Init();
    LoadApplication(new BoxViewClock.App());
    ```
    
    Exceto para comentários e não utilizadas `using` diretivas, completo **MainWindows.xaml.cs** arquivo deve ter esta aparência:

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

10. Clique com botão direito no projeto do WPF na **Gerenciador de soluções** e selecione **definir como projeto de inicialização**. Pressione F5 para executar o programa com o depurador do Visual Studio, na área de trabalho do Windows:

    ![O relógio do WPF BoxView](wpf-images/wpf-boxviewclock.png "BoxView relógio do WPF" )

## <a name="next-steps"></a>Próximas etapas

### <a name="platform-specifics"></a>Especificidades da plataforma

Você pode determinar em qual plataforma está em execução no seu aplicativo xamarin. Forms de código ou XAML. Isso permite que você altere as características do programa quando ele está em execução no WPF. No código, comparar o valor de `Device.RuntimePlatform` com o `Device.WPF` constante (que é igual a cadeia de caracteres "WPF"). Se houver uma correspondência, o aplicativo está em execução no WPF.

No XAML, você pode usar o `OnPlatform` marca para selecionar um valor de propriedade específico da plataforma:

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

Você pode ajustar o tamanho inicial da janela no WPF **MainWindow. XAML** arquivo:

```xaml
Title="BoxViewClock" Height="450" Width="800"
```

## <a name="issues"></a>Problemas

Esta é uma visualização, portanto, você deve esperar que nem tudo está pronto para produção. Nem todos os pacotes do NuGet para xamarin. Forms estão prontos para WPF, e alguns recursos talvez não esteja funcionando totalmente.

