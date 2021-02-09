---
title: Criar um Xamarin.Forms início rápido do aplicativo
description: Este artigo explica como criar um Xamarin.Forms aplicativo de Shell, o que reduz a complexidade do desenvolvimento de aplicativos móveis fornecendo os recursos fundamentais que a maioria dos aplicativos móveis exigem.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: 359F5012-4409-42A7-BEDF-C1DB9AF86DED
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.custom: contperf-fy21q3
ms.date: 01/26/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 4d21daa89322021e80a46753c864da3cb671b2d6
ms.sourcegitcommit: 1f391667869a4541dd9b42d78862dc01d69ed160
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/08/2021
ms.locfileid: "99974376"
---
# <a name="create-a-xamarinforms-application-quickstart"></a>Criar um Xamarin.Forms início rápido do aplicativo

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/getstarted-notes-app/)

Neste guia de início rápido, você aprenderá a:

- Crie um Xamarin.Forms aplicativo de Shell.
- Defina a interface do usuário para uma página usando XAML (linguagem de marcação de aplicativo extensível) e interaja com elementos XAML do código.
- Descreva a hierarquia visual de um aplicativo de shell por meio da subclasse da `Shell` classe.

O guia de início rápido explica como criar um aplicativo de Shell de plataforma cruzada Xamarin.Forms , que permite que você insira uma anotação e persista-a no armazenamento do dispositivo. O aplicativo final é mostrado abaixo:

[ ![ Observações](app-images/screenshots1-sml.png)](app-images/screenshots1.png#lightbox)sobre o aplicativo observações 
 [ ![ sobre a página](app-images/screenshots2-sml.png)](app-images/screenshots2.png#lightbox)

::: zone pivot="windows"

### <a name="prerequisites"></a>Pré-requisitos

- Visual Studio 2019 (versão mais recente), com a carga de trabalho de **Desenvolvimento móvel com .NET** instalada.
- Conhecimento de C#.
- (opcional) Um Mac emparelhado para compilar o aplicativo no iOS.

Para obter mais informações sobre esses pré-requisitos, confira [Como instalar o Xamarin](~/get-started/installation/index.md). Para obter informações sobre como conectar o Visual Studio 2019 a um host de build Mac, confira [Emparelhar com o Mac para desenvolvimento no Xamarin.iOS](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

## <a name="get-started-with-visual-studio-2019"></a>Introdução ao Visual Studio 2019

1. Inicie o Visual Studio 2019 e, na janela inicial, clique em **Criar um projeto** para criar um projeto:

    ![Nova solução](app-images/vs/new-solution.png)

2. Na janela **criar um novo projeto** , selecione **móvel** na lista suspensa **tipo de projeto** , selecione o modelo **aplicativo móvel ( Xamarin.Forms )** e clique no botão **Avançar** :

    ![Escolher um modelo](app-images/vs/new-project.png)

3. Na janela **Configurar seu novo projeto**, defina o **Nome do projeto** como **Notes**, escolha uma localização adequada para o projeto e clique no botão **Criar**:   

    ![Configurar o aplicativo de Shell](app-images/vs/configure-project.png)

    > [!IMPORTANT]
    > Os snippets C# e XAML neste início rápido requerem que a solução e o projeto sejam nomeados como **Notas**. O uso de um nome diferente resultará em erros de build ao copiar o código deste início rápido para o projeto.

4. Na caixa de diálogo **novo aplicativo móvel** , selecione o modelo com **guias** e clique no botão **criar** :

    ![Criar o aplicativo de Shell](app-images/vs/create-project.png)

    Quando o projeto tiver sido criado, feche o arquivo **GettingStarted.txt** .

    Para obter mais informações sobre a biblioteca de .NET Standard que é criada, consulte a [anatomia de um Xamarin.Forms aplicativo de shell](deepdive.md#anatomy-of-a-xamarinforms-application) no [ Xamarin.Forms início rápido do Shell para aprofundar](deepdive.md)-se.

5. No **Gerenciador de soluções**, no projeto do **Notes** , exclua as seguintes pastas (e seu conteúdo):

    - **Modelos**
    - **Serviços**
    - **ViewModels**
    - **Exibições**

6. No **Gerenciador de soluções**, no projeto do **Notes** , exclua **GettingStarted.txt**.

7. No **Gerenciador de soluções**, no projeto do **Notes** , adicione uma nova pasta chamada **exibições**.

8. Em **Gerenciador de soluções**, no projeto do **Notes** , selecione a pasta **exibições** , clique com o botão direito do mouse e selecione **Adicionar > novo item...**. Na caixa de diálogo **Adicionar novo item** , selecione **itens do Visual C# > Xamarin.Forms página > conteúdo**, nomeie o novo arquivo **NotesPage** e clique no botão **Adicionar** :

    ![Adicionar NotesPage](app-images/vs/add-notespage.png)

    Isso adicionará uma nova página chamada **NotesPage** à pasta **views** . Esta página será a página principal no aplicativo.

9. No **Gerenciador de soluções**, no projeto do **Notes** , clique duas vezes em **NotesPage. XAML** para abri-lo:

    ![Abra NotesPage. XAML](app-images/vs/open-notespage-xaml.png)

10. Em **NotesPage.xaml**, remova todo o código de modelo e substitua-o pelo código a seguir:

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.Views.NotesPage"
                 Title="Notes">
        <!-- Layout children vertically -->
        <StackLayout Margin="20">
            <Editor x:Name="editor"
                    Placeholder="Enter your note"
                    HeightRequest="100" />
            <!-- Layout children in two columns -->
            <Grid ColumnDefinitions="*,*">
                <Button Text="Save"
                        Clicked="OnSaveButtonClicked" />
                <Button Grid.Column="1"
                        Text="Delete"
                        Clicked="OnDeleteButtonClicked"/>
            </Grid>
        </StackLayout>
    </ContentPage>
    ```

    Esse código declarativamente define a interface do usuário para a página, que consiste em uma [`Editor`](xref:Xamarin.Forms.Editor) entrada de texto for e dois [`Button`](xref:Xamarin.Forms.Button) objetos que direcionam o aplicativo para salvar ou excluir um arquivo. Os dois `Button` objetos são dispostos horizontalmente em um [`Grid`](xref:Xamarin.Forms.Grid) , com o `Editor` e `Grid` que estão sendo dispostos verticalmente em um [`StackLayout`](xref:Xamarin.Forms.StackLayout) . Para obter mais informações sobre como criar a interface do usuário, consulte [interface do usuário](deepdive.md#user-interface) no guia de [ Xamarin.Forms início rápido do Shell-aprofundamento](deepdive.md).

    Salve as alterações em **NotesPage. XAML** pressionando **Ctrl + S**.  

12. No **Gerenciador de soluções**, no projeto do **Notes** , clique duas vezes em **NotesPage.XAML.cs** para abri-lo:

    ![Abrir NotesPage.xaml.cs](app-images/vs/open-notespage-codebehind.png)

12. Em **NotesPage.xaml.cs**, remova todo o código de modelo e substitua-o pelo código a seguir:

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;

    namespace Notes.Views
    {
        public partial class NotesPage : ContentPage
        {
            string _fileName = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "notes.txt");

            public NotesPage()
            {
                InitializeComponent();

                // Read the file.
                if (File.Exists(_fileName))
                {
                    editor.Text = File.ReadAllText(_fileName);
                }
            }

            void OnSaveButtonClicked(object sender, EventArgs e)
            {
                // Save the file.
                File.WriteAllText(_fileName, editor.Text);
            }

            void OnDeleteButtonClicked(object sender, EventArgs e)
            {
                // Delete the file.
                if (File.Exists(_fileName))
                {
                    File.Delete(_fileName);
                }
                editor.Text = string.Empty;
            }
        }
    }
    ```

    Esse código define um campo `_fileName`, que referencia um arquivo chamado `notes.txt` que armazenará os dados de notas na pasta de dados local do aplicativo. Quando o construtor da página é executado, o arquivo é lido, se existir, e exibido no [`Editor`](xref:Xamarin.Forms.Editor) . Quando o **salvamento** [`Button`](xref:Xamarin.Forms.Button) é pressionado `OnSaveButtonClicked` , o manipulador de eventos é executado, o que salva o conteúdo do no `Editor` arquivo. Quando o **Excluir** `Button` for pressionado, o manipulador de eventos `OnDeleteButtonClicked` será executado, excluindo o arquivo, caso ele exista, e removendo todo o texto do `Editor`. Para obter mais informações sobre a interação do usuário, consulte [respondendo à interação do usuário](deepdive.md#responding-to-user-interaction) no [ Xamarin.Forms início rápido do Shell](deepdive.md).

    Salve as alterações em **NotesPage.XAML.cs** pressionando **Ctrl + S**.    

13. Em **Gerenciador de soluções**, no projeto do **Notes** , selecione a pasta **exibições** , clique com o botão direito do mouse e selecione **Adicionar > novo item...**. Na caixa de diálogo **Adicionar novo item** , selecione **itens do Visual C# > Xamarin.Forms página > conteúdo**, nomeie o novo arquivo **AboutPage** e clique no botão **Adicionar** :

    ![Adicionar AboutPage](app-images/vs/add-aboutpage.png)

    Isso adicionará uma nova página chamada **AboutPage** à pasta **views** .

14. No **Gerenciador de soluções**, no projeto do **Notes** , clique duas vezes em **AboutPage. XAML** para abri-lo:

    ![Abra AboutPage. XAML](app-images/vs/open-aboutpage-xaml.png)

15. No **AboutPage. XAML**, remova todo o código do modelo e substitua-o pelo seguinte código:

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.Views.AboutPage"
                 Title="About">
        <!-- Layout children in two rows -->
        <Grid RowDefinitions="Auto,*">
            <Image Source="xamarin_logo.png"
                   BackgroundColor="{OnPlatform iOS=LightSlateGray, Android=#2196F3}"
                   VerticalOptions="Center"
                   HeightRequest="64" />
            <!-- Layout children vertically -->
            <StackLayout Grid.Row="1"
                         Margin="20"
                         Spacing="20">
                <Label FontSize="22">
                    <Label.FormattedText>
                        <FormattedString>
                            <FormattedString.Spans>
                                <Span Text="Notes"
                                      FontAttributes="Bold"
                                      FontSize="22" />
                                <Span Text=" v1.0" />
                            </FormattedString.Spans>
                        </FormattedString>
                    </Label.FormattedText>
                </Label>
                <Label Text="This app is written in XAML and C# with the Xamarin Platform." />
                <Button Text="Learn more"
                        Clicked="OnButtonClicked" />
            </StackLayout>
        </Grid>
    </ContentPage>
    ```

    Esse código declarativamente define a interface do usuário para a página, que consiste em um [`Image`](xref:Xamarin.Forms.Image) , dois [`Label`](xref:Xamarin.Forms.Label) objetos que exibem texto e um [`Button`](xref:Xamarin.Forms.Button) . Os dois `Label` objetos e `Button` são dispostos verticalmente em um [`StackLayout`](xref:Xamarin.Forms.StackLayout) , com o `Image` e `StackLayout` que estão sendo dispostos verticalmente em um [`Grid`](xref:Xamarin.Forms.Grid) . Para obter mais informações sobre como criar a interface do usuário, consulte [interface do usuário](deepdive.md#user-interface) no guia de [ Xamarin.Forms início rápido do Shell-aprofundamento](deepdive.md).

    Salve as alterações em **AboutPage. XAML** pressionando **Ctrl + S**.    

16. No **Gerenciador de soluções**, no projeto do **Notes** , clique duas vezes em **AboutPage.XAML.cs** para abri-lo:

    ![Abrir AboutPage.xaml.cs](app-images/vs/open-aboutpage-codebehind.png)

17. No **AboutPage.XAML.cs**, remova todo o código do modelo e substitua-o pelo código a seguir:

    ```csharp
    using System;
    using Xamarin.Essentials;
    using Xamarin.Forms;

    namespace Notes.Views
    {
        public partial class AboutPage : ContentPage
        {
            public AboutPage()
            {
                InitializeComponent();
            }

            async void OnButtonClicked(object sender, EventArgs e)
            {
                // Launch the specified URL in the system browser.
                await Launcher.OpenAsync("https://aka.ms/xamarin-quickstart");
            }
        }
    }
    ```

    Esse código define o `OnButtonClicked` manipulador de eventos, que é executado quando o **saiba mais** [`Button`](xref:Xamarin.Forms.Button) é pressionado. Quando o botão é pressionado, um navegador da Web é iniciado e a página representada pelo argumento URI para o `OpenAsync` método é exibida. Para obter mais informações sobre a interação do usuário, consulte [respondendo à interação do usuário](deepdive.md#responding-to-user-interaction) no [ Xamarin.Forms início rápido do Shell](deepdive.md).

    Salve as alterações em **AboutPage.XAML.cs** pressionando **Ctrl + S**.

18. No **Gerenciador de soluções**, no projeto do **Notes** , clique duas vezes em **AppShell. XAML** para abri-lo:

    ![Abra AppShell. XAML](app-images/vs/open-appshell-xaml.png)

19. No **AppShell. XAML**, remova todo o código do modelo e substitua-o pelo seguinte código:

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <Shell xmlns="http://xamarin.com/schemas/2014/forms"
           xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
           xmlns:views="clr-namespace:Notes.Views"
           x:Class="Notes.AppShell">
        <!-- Display a bottom tab bar containing two tabs -->   
        <TabBar>
            <ShellContent Title="Notes"
                          Icon="icon_feed.png"
                          ContentTemplate="{DataTemplate views:NotesPage}" />
            <ShellContent Title="About"
                          Icon="icon_about.png"
                          ContentTemplate="{DataTemplate views:AboutPage}" />
        </TabBar>
    </Shell>
    ```    

    Esse código define declarativamente a hierarquia visual do aplicativo, que consiste em um `TabBar` contendo dois `ShellContent` objetos. Esses objetos não representam nenhum elemento de interface do usuário, mas sim a organização da hierarquia visual do aplicativo. O Shell usará esses objetos e produzirá a interface do usuário para o conteúdo. Para obter mais informações sobre como criar a interface do usuário, consulte [interface do usuário](deepdive.md#user-interface) no guia de [ Xamarin.Forms início rápido do Shell-aprofundamento](deepdive.md).

    Salve as alterações em **AppShell. XAML** pressionando **Ctrl + S**.    

20. No **Gerenciador de soluções**, no projeto do **Notes** , expanda **AppShell. XAML** e clique duas vezes em **AppShell.XAML.cs** para abri-lo:

    ![Abrir AppShell.xaml.cs](app-images/vs/open-appshell-codebehind.png)

21. No **AppShell.XAML.cs**, remova todo o código do modelo e substitua-o pelo código a seguir:

    ```csharp
    using Xamarin.Forms;

    namespace Notes
    {
        public partial class AppShell : Shell
        {
            public AppShell()
            {
                InitializeComponent();
            }
        }
    }
    ```

    Salve as alterações em **AppShell.XAML.cs** pressionando **Ctrl + S**.

22. No **Gerenciador de soluções**, no projeto do **Notes** , clique duas vezes em **app. XAML** para abri-lo:

    ![Abrir app. XAML](app-images/vs/open-app-xaml.png)

23. Em **app. XAML**, remova todo o código do modelo e substitua-o pelo seguinte código:

    ```xaml
    <?xml version="1.0" encoding="utf-8" ?>
    <Application xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.App">

    </Application>
    ```

    Esse código declarativamente define uma `App` classe, que é responsável por instanciar o aplicativo.

    Salve as alterações em **app. XAML** pressionando **Ctrl + S**.    

24. No **Gerenciador de soluções**, no projeto do **Notes** , expanda **app. XAML** e clique duas vezes em **app.XAML.cs** para abri-lo:

    ![Abrir App.xaml.cs](app-images/vs/open-app-codebehind.png)

25. No **app.XAML.cs**, remova todo o código do modelo e substitua-o pelo código a seguir:

    ```csharp
    using Xamarin.Forms;

    namespace Notes
    {
        public partial class App : Application
        {

            public App()
            {
                InitializeComponent();
                MainPage = new AppShell();
            }

            protected override void OnStart()
            {
            }

            protected override void OnSleep()
            {
            }

            protected override void OnResume()
            {
            }
        }
    }
    ```

    Esse código define o code-behind para a `App` classe, que é responsável por instanciar o aplicativo. Ele inicializa a [`MainPage`](xref:Xamarin.Forms.Application.MainPage) propriedade para o objeto de subclasse `Shell` .

    Salve as alterações em **app.XAML.cs** pressionando **Ctrl + S**.    

### <a name="building-the-quickstart"></a>Criando o Início Rápido

1. No Visual Studio, selecione o item de menu **Criar > Criar Solução** (ou pressione F6). A solução será criada e uma mensagem de êxito será exibida na barra de status do Visual Studio:

      ![Build bem-sucedido](app-images/vs/build-successful.png)

    Se houver erros, repita as etapas anteriores e corrija-os até que os projetos sejam compilados com êxito.

2. Na barra de ferramentas do Visual Studio, pressione o botão **Iniciar** (o botão triangular que se parece com o botão Reproduzir) para iniciar o aplicativo em seu emulador Android escolhido:

      ![Barra de ferramentas do Visual Studio para Android](app-images/vs/android-start.png)

      ![Observações no Android Emulator](app-images/vs/notes1-android.png)

    Insira uma nota e pressione o botão **Salvar**. Em seguida, feche o aplicativo e inicie-o novamente para garantir que a observação inserida seja recarregada.

    Pressione o ícone da guia **sobre** para navegar até `AboutPage` :

      ![Observações sobre a página no Android Emulator](app-images/vs/notes2-android.png)

    Pressione o botão **saiba mais** para iniciar a página da Web de guias de início rápido.

    Para obter mais informações sobre como o aplicativo é iniciado em cada plataforma, consulte [iniciando o aplicativo em cada plataforma](deepdive.md#launch-the-application-on-each-platform) no guia de [ Xamarin.Forms início rápido](deepdive.md)aprofundamento.

    > [!NOTE]
    > As etapas a seguir só devem ser executadas se você tiver um [Mac emparelhado](~/ios/get-started/installation/windows/connecting-to-mac/index.md) que atenda aos requisitos de sistema para Xamarin.Forms desenvolvimento.    

3. Na barra de ferramentas do Visual Studio, clique com o botão direito do mouse no projeto **Notes.iOS** e selecione **Definir como projeto de inicialização**.

      ![Definir Notes. iOS como projeto de inicialização](app-images/vs/set-startup-project-ios.png)

4. Na barra de ferramentas do Visual Studio, pressione o botão **Iniciar** (o botão triangular que se parece com o botão Reproduzir) para iniciar o aplicativo em seu [Simulador remoto do iOS](~/tools/ios-simulator/index.md) escolhido:

      ![Barra de ferramentas do Visual Studio para iOS](app-images/vs/ios-start.png)

      [![Observações no Simulador do iOS](app-images/vs/notes1-ios.png)](app-images/vs/notes1-ios-large.png#lightbox)

    Insira uma nota e pressione o botão **Salvar**. Em seguida, feche o aplicativo e inicie-o novamente para garantir que a observação inserida seja recarregada.

    Pressione o ícone da guia **sobre** para navegar até `AboutPage` :

      [![Observações sobre a página no simulador de iOS](app-images/vs/notes2-ios.png)](app-images/vs/notes2-ios-large.png#lightbox)

    Pressione o botão **saiba mais** para iniciar a página da Web de guias de início rápido.

    Para obter mais informações sobre como o aplicativo é iniciado em cada plataforma, consulte [iniciando o aplicativo em cada plataforma](deepdive.md#launch-the-application-on-each-platform) no guia de [ Xamarin.Forms início rápido](deepdive.md)aprofundamento.

::: zone-end
::: zone pivot="macos"

### <a name="prerequisites"></a>Pré-requisitos

- Visual Studio para Mac (versão mais recente), compatível com a plataforma iOS e Android instalada.
- Xcode (versão mais recente).
- Conhecimento de C#.

Para obter mais informações sobre esses pré-requisitos, confira [Como instalar o Xamarin](~/get-started/installation/index.md).

## <a name="get-started-with-visual-studio-for-mac"></a>Introdução ao Visual Studio para Mac

1. Inicialize o Visual Studio para Mac e, na janela inicial, clique em **Novo** para criar um novo projeto:

    ![Nova solução](app-images/vsmac/new-project.png)

2. Na caixa de diálogo **escolher um modelo para o novo projeto** , clique em **multiplataforma > aplicativo**, selecione o modelo **aplicativo do Shell Forms** e clique no botão **Avançar** :

    ![Escolher um modelo](app-images/vsmac/choose-template.png)

3. Na caixa de diálogo **configurar seu aplicativo Shell Forms** , nomeie as **notas** do novo aplicativo e clique no botão **Avançar** :    

    ![Configurar o aplicativo de Shell](app-images/vsmac/configure-app.png)

4. Na caixa de diálogo **configurar seu novo aplicativo do Shell Forms** , deixe os nomes da solução e do projeto definidos como **observações**, escolha um local adequado para o projeto e clique no botão **criar** para criar o projeto:

    ![Configurar o projeto do Shell](app-images/vsmac/configure-project.png)

    > [!IMPORTANT]
    > Os snippets C# e XAML neste início rápido requerem que a solução e o projeto sejam nomeados como **Notas**. O uso de um nome diferente resultará em erros de build ao copiar o código deste início rápido para o projeto.

    Para obter mais informações sobre a biblioteca de .NET Standard que é criada, consulte a [anatomia de um Xamarin.Forms aplicativo de shell](deepdive.md#anatomy-of-a-xamarinforms-application) no [ Xamarin.Forms início rápido do Shell para aprofundar](deepdive.md)-se.

5. No **painel de soluções**, no projeto do **Notes** , exclua as seguintes pastas (e seu conteúdo):

    - **Modelos**
    - **Serviços**
    - **ViewModels**
    - **Exibições**

6. No **painel de soluções**, no projeto do **Notes** , exclua **GettingStarted.txt**.

7. No **painel de soluções**, no projeto do **Notes** , adicione uma nova pasta chamada **exibições**.

8. No **painel de soluções**, no projeto do **Notes** , selecione a pasta **exibições** , clique com o botão direito do mouse e selecione **Adicionar > novo arquivo...**. Na caixa de diálogo **novo arquivo** , selecione **Forms > Forms Contentpage XAML**, nomeie o novo arquivo **NotesPage** e clique no botão **novo** :

    ![Adicionar NotesPage](app-images/vsmac/add-notespage.png)

    Isso adicionará uma nova página chamada **NotesPage** à pasta **views** . Esta página será a página principal no aplicativo.

9. No **painel de soluções**, no projeto do **Notes** , clique duas vezes em **NotesPage. XAML** para abri-lo:

    ![Abra NotesPage. XAML](app-images/vsmac/open-notespage-xaml.png)

10. Em **NotesPage.xaml**, remova todo o código de modelo e substitua-o pelo código a seguir:

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.Views.NotesPage"
                 Title="Notes">
        <!-- Layout children vertically -->
        <StackLayout Margin="20">
            <Editor x:Name="editor"
                    Placeholder="Enter your note"
                    HeightRequest="100" />
            <!-- Layout children in two columns -->
            <Grid ColumnDefinitions="*,*">
                <Button Text="Save"
                        Clicked="OnSaveButtonClicked" />
                <Button Grid.Column="1"
                        Text="Delete"
                        Clicked="OnDeleteButtonClicked"/>
            </Grid>
        </StackLayout>
    </ContentPage>
    ```

    Esse código declarativamente define a interface do usuário para a página, que consiste em uma [`Editor`](xref:Xamarin.Forms.Editor) entrada de texto for e dois [`Button`](xref:Xamarin.Forms.Button) objetos que direcionam o aplicativo para salvar ou excluir um arquivo. Os dois `Button` objetos são dispostos horizontalmente em um [`Grid`](xref:Xamarin.Forms.Grid) , com o `Editor` e `Grid` que estão sendo dispostos verticalmente em um [`StackLayout`](xref:Xamarin.Forms.StackLayout) . Para obter mais informações sobre como criar a interface do usuário, consulte [interface do usuário](deepdive.md#user-interface) no guia de [ Xamarin.Forms início rápido do Shell-aprofundamento](deepdive.md).

    Salve as alterações em **NotesPage. XAML** escolhendo **arquivo > salvar** (ou pressionando **&#8984; + S**).

12. No **painel de soluções**, no projeto do **Notes** , clique duas vezes em **NotesPage.XAML.cs** para abri-lo:

    ![Abrir NotesPage.xaml.cs](app-images/vsmac/open-notespage-codebehind.png)

12. Em **NotesPage.xaml.cs**, remova todo o código de modelo e substitua-o pelo código a seguir:

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;

    namespace Notes.Views
    {
        public partial class NotesPage : ContentPage
        {
            string _fileName = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "notes.txt");

            public NotesPage()
            {
                InitializeComponent();

                // Read the file.
                if (File.Exists(_fileName))
                {
                    editor.Text = File.ReadAllText(_fileName);
                }
            }

            void OnSaveButtonClicked(object sender, EventArgs e)
            {
                // Save the file.
                File.WriteAllText(_fileName, editor.Text);
            }

            void OnDeleteButtonClicked(object sender, EventArgs e)
            {
                // Delete the file.
                if (File.Exists(_fileName))
                {
                    File.Delete(_fileName);
                }
                editor.Text = string.Empty;
            }
        }
    }
    ```

    Esse código define um campo `_fileName`, que referencia um arquivo chamado `notes.txt` que armazenará os dados de notas na pasta de dados local do aplicativo. Quando o construtor da página é executado, o arquivo é lido, se existir, e exibido no [`Editor`](xref:Xamarin.Forms.Editor) . Quando o **salvamento** [`Button`](xref:Xamarin.Forms.Button) é pressionado `OnSaveButtonClicked` , o manipulador de eventos é executado, o que salva o conteúdo do no `Editor` arquivo. Quando o **Excluir** `Button` for pressionado, o manipulador de eventos `OnDeleteButtonClicked` será executado, excluindo o arquivo, caso ele exista, e removendo todo o texto do `Editor`. Para obter mais informações sobre a interação do usuário, consulte [respondendo à interação do usuário](deepdive.md#responding-to-user-interaction) no [ Xamarin.Forms início rápido do Shell](deepdive.md).

    Salve as alterações no **NotesPage.XAML.cs** escolhendo **arquivo > salvar** (ou pressionando **&#8984; + S**).

13. No **painel de soluções**, no projeto do **Notes** , selecione a pasta **exibições** , clique com o botão direito do mouse e selecione **Adicionar > novo arquivo...**. Na caixa de diálogo **novo arquivo** , selecione **Forms > Forms Contentpage XAML**, nomeie o novo arquivo **AboutPage** e clique no botão **novo** :

    ![Adicionar AboutPage](app-images/vsmac/add-aboutpage.png)

14. No **painel de soluções**, no projeto do **Notes** , clique duas vezes em **AboutPage. XAML** para abri-lo:

    ![Abra AboutPage. XAML](app-images/vsmac/open-aboutpage-xaml.png)

    Isso adicionará uma nova página chamada **AboutPage** à pasta **views** .

15. No **AboutPage. XAML**, remova todo o código do modelo e substitua-o pelo seguinte código:

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.Views.AboutPage"
                 Title="About">
        <!-- Layout children in two rows -->
        <Grid RowDefinitions="Auto,*">
            <Image Source="xamarin_logo.png"
                   BackgroundColor="{OnPlatform iOS=LightSlateGray, Android=#2196F3}"
                   VerticalOptions="Center"
                   HeightRequest="64" />
            <!-- Layout children vertically -->
            <StackLayout Grid.Row="1"
                         Margin="20"
                         Spacing="20">
                <Label FontSize="22">
                    <Label.FormattedText>
                        <FormattedString>
                            <FormattedString.Spans>
                                <Span Text="Notes"
                                      FontAttributes="Bold"
                                      FontSize="22" />
                                <Span Text=" v1.0" />
                            </FormattedString.Spans>
                        </FormattedString>
                    </Label.FormattedText>
                </Label>
                <Label Text="This app is written in XAML and C# with the Xamarin Platform." />
                <Button Text="Learn more"
                        Clicked="OnButtonClicked" />
            </StackLayout>
        </Grid>
    </ContentPage>
    ```

    Esse código declarativamente define a interface do usuário para a página, que consiste em um [`Image`](xref:Xamarin.Forms.Image) , dois [`Label`](xref:Xamarin.Forms.Label) objetos que exibem texto e um [`Button`](xref:Xamarin.Forms.Button) . Os dois `Label` objetos e `Button` são dispostos verticalmente em um [`StackLayout`](xref:Xamarin.Forms.StackLayout) , com o `Image` e `StackLayout` que estão sendo dispostos verticalmente em um [`Grid`](xref:Xamarin.Forms.Grid) . Para obter mais informações sobre como criar a interface do usuário, consulte [interface do usuário](deepdive.md#user-interface) no guia de [ Xamarin.Forms início rápido do Shell-aprofundamento](deepdive.md).

    Salve as alterações em **AboutPage. XAML** escolhendo **arquivo > salvar** (ou pressionando **&#8984; + S**).

16. No **painel de soluções**, no projeto do **Notes** , clique duas vezes em **AboutPage.XAML.cs** para abri-lo:

    ![Abrir AboutPage.xaml.cs](app-images/vsmac/open-aboutpage-codebehind.png)

17. No **AboutPage.XAML.cs**, remova todo o código do modelo e substitua-o pelo código a seguir:

    ```csharp
    using System;
    using Xamarin.Essentials;
    using Xamarin.Forms;

    namespace Notes.Views
    {
        public partial class AboutPage : ContentPage
        {
            public AboutPage()
            {
                InitializeComponent();
            }

            async void OnButtonClicked(object sender, EventArgs e)
            {
                // Launch the specified URL in the system browser.
                await Launcher.OpenAsync("https://aka.ms/xamarin-quickstart");
            }
        }
    }
    ```

    Esse código define o `OnButtonClicked` manipulador de eventos, que é executado quando o **saiba mais** [`Button`](xref:Xamarin.Forms.Button) é pressionado. Quando o botão é pressionado, um navegador da Web é iniciado e a página representada pelo argumento URI para o `OpenAsync` método é exibida. Para obter mais informações sobre a interação do usuário, consulte [respondendo à interação do usuário](deepdive.md#responding-to-user-interaction) no [ Xamarin.Forms início rápido do Shell](deepdive.md).

    Salve as alterações no **AboutPage.XAML.cs** escolhendo **arquivo > salvar** (ou pressionando **&#8984; + S**).

18. No **painel de soluções**, no projeto do **Notes** , clique duas vezes em **AppShell. XAML** para abri-lo:

    ![Abra AppShell. XAML](app-images/vsmac/open-appshell-xaml.png)

19. No **AppShell. XAML**, remova todo o código do modelo e substitua-o pelo seguinte código:

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <Shell xmlns="http://xamarin.com/schemas/2014/forms"
           xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
           xmlns:views="clr-namespace:Notes.Views"
           x:Class="Notes.AppShell">
        <!-- Display a bottom tab bar containing two tabs -->
        <TabBar>
            <ShellContent Title="Notes"
                          Icon="icon_feed.png"
                          ContentTemplate="{DataTemplate views:NotesPage}" />
            <ShellContent Title="About"
                          Icon="icon_about.png"
                          ContentTemplate="{DataTemplate views:AboutPage}" />
        </TabBar>
    </Shell>
    ```    

    Esse código define declarativamente a hierarquia visual do aplicativo, que consiste em um `TabBar` contendo dois `ShellContent` objetos. Esses objetos não representam nenhum elemento de interface do usuário, mas sim a organização da hierarquia visual do aplicativo. O Shell usará esses objetos e produzirá a interface do usuário para o conteúdo. Para obter mais informações sobre como criar a interface do usuário, consulte [interface do usuário](deepdive.md#user-interface) no guia de [ Xamarin.Forms início rápido do Shell-aprofundamento](deepdive.md).

    Salve as alterações em **AppShell. XAML** escolhendo **arquivo > salvar** (ou pressionando **&#8984; + S**).

20. No **painel de soluções**, no projeto do **Notes** , expanda **AppShell. XAML** e clique duas vezes em **AppShell.XAML.cs** para abri-lo:

    ![Abrir AppShell.xaml.cs](app-images/vsmac/open-appshell-codebehind.png)

21. No **AppShell.XAML.cs**, remova todo o código do modelo e substitua-o pelo código a seguir:

    ```csharp
    using Xamarin.Forms;

    namespace Notes
    {
        public partial class AppShell : Shell
        {
            public AppShell()
            {
                InitializeComponent();
            }
        }
    }
    ```

    Salve as alterações no **AppShell.XAML.cs** escolhendo **arquivo > salvar** (ou pressionando **&#8984; + S**).

22. No **painel de soluções**, no projeto do **Notes** , clique duas vezes em **app. XAML** para abri-lo:

    ![Abrir app. XAML](app-images/vsmac/open-app-xaml.png)

23. Em **app. XAML**, remova todo o código do modelo e substitua-o pelo seguinte código:

    ```xaml
    <?xml version="1.0" encoding="utf-8" ?>
    <Application xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.App">

    </Application>
    ```

    Esse código declarativamente define uma `App` classe, que é responsável por instanciar o aplicativo.

    Salve as alterações em **app. XAML** escolhendo **arquivo > salvar** (ou pressionando **&#8984; + S**).

24. No **painel de soluções**, no projeto do **Notes** , expanda **app. XAML** e clique duas vezes em **app.XAML.cs** para abri-lo:

    ![Abrir App.xaml.cs](app-images/vsmac/open-app-codebehind.png)

25. No **app.XAML.cs**, remova todo o código do modelo e substitua-o pelo código a seguir:

    ```csharp
    using Xamarin.Forms;

    namespace Notes
    {
        public partial class App : Application
        {

            public App()
            {
                InitializeComponent();
                MainPage = new AppShell();
            }

            protected override void OnStart()
            {
            }

            protected override void OnSleep()
            {
            }

            protected override void OnResume()
            {
            }
        }
    }
    ```

    Esse código define o code-behind para a `App` classe, que é responsável por instanciar o aplicativo. Ele inicializa a [`MainPage`](xref:Xamarin.Forms.Application.MainPage) propriedade para o objeto de subclasse `Shell` .

    Salve as alterações no **app.XAML.cs** escolhendo **arquivo > salvar** (ou pressionando **&#8984; + S**).

### <a name="building-the-quickstart"></a>Criando o Início Rápido

1. No Visual Studio para Mac, selecione o item de menu **Compilar > Compilar Tudo** (ou pressione **&#8984; + B**). Os projetos serão criados e uma mensagem de êxito aparecerá na barra de ferramentas Visual Studio para Mac:

      ![Build bem-sucedido](app-images/vsmac/build-successful.png)

    Se houver erros, repita as etapas anteriores e corrija-os até que os projetos sejam compilados com êxito.

2. No **Painel de Soluções**, selecione o projeto **Notes.iOS**, clique com o botão direito do mouse e selecione **Definir como projeto de inicialização**:

      ![Definir o iOS como Projeto Inicial](app-images/vsmac/set-startup-project-ios.png)

3. Na barra de ferramentas do Visual Studio para Mac, pressione o botão **Iniciar** (o botão triangular que se parece com o botão Reproduzir) para iniciar o aplicativo no Simulador do iOS escolhido:

      ![Barra de ferramentas do Visual Studio para Mac](app-images/vsmac/start.png)

      ![Observações no Simulador do iOS](app-images/vsmac/notes1-ios.png)

    Insira uma nota e pressione o botão **Salvar**. Em seguida, feche o aplicativo e inicie-o novamente para garantir que a observação inserida seja recarregada.

    Pressione o ícone da guia **sobre** para navegar até `AboutPage` :

      ![Observações sobre a página no simulador de iOS](app-images/vsmac/notes2-ios.png)

    Pressione o botão **saiba mais** para iniciar a página da Web de guias de início rápido.

    Para obter mais informações sobre como o aplicativo é iniciado em cada plataforma, consulte [iniciando o aplicativo em cada plataforma](deepdive.md#launch-the-application-on-each-platform) no guia de [ Xamarin.Forms início rápido](deepdive.md)aprofundamento.

4. No **Painel de Soluções**, selecione o projeto **Notes.Droid**, clique com o botão direito do mouse e selecione **Definir como projeto de inicialização**:

      ![Definir Android como projeto de inicialização](app-images/vsmac/set-startup-project-android.png)

5. Na barra de ferramentas do Visual Studio para Mac, pressione o botão **Iniciar** (o botão triangular que se parece com o botão Reproduzir) para iniciar o aplicativo em seu emulador Android escolhido:

      ![Observações no Android Emulator](app-images/vsmac/notes1-android.png)

    Insira uma nota e pressione o botão **Salvar**. Em seguida, feche o aplicativo e inicie-o novamente para garantir que a observação inserida seja recarregada.

    Pressione o ícone da guia **sobre** para navegar até `AboutPage` :

      ![Observações sobre a página no Android Emulator](app-images/vsmac/notes2-android.png)

    Pressione o botão **saiba mais** para iniciar a página da Web de guias de início rápido.

    Para obter mais informações sobre como o aplicativo é iniciado em cada plataforma, consulte [iniciando o aplicativo em cada plataforma](deepdive.md#launch-the-application-on-each-platform) no guia de [ Xamarin.Forms início rápido](deepdive.md)aprofundamento.

::: zone-end

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você aprendeu a:

- Crie um Xamarin.Forms aplicativo de Shell.
- Defina a interface do usuário para uma página usando XAML (linguagem de marcação de aplicativo extensível) e interaja com elementos XAML do código.
- Descreva a hierarquia visual de um aplicativo de shell por meio da subclasse da `Shell` classe.

Continue para o próximo início rápido para adicionar outras páginas a este Xamarin.Forms aplicativo de Shell.

> [!div class="nextstepaction"]
> [Próximo](navigation.md)

## <a name="related-links"></a>Links relacionados

- [Notas (exemplo)](/samples/xamarin/xamarin-forms-samples/getstarted-notes-app/)
- [Xamarin.Forms Aprofundamento do início rápido do Shell](deepdive.md)
