---
title: Criar um aplicativo Xamarin.Forms de página única
description: Este artigo explica como criar um aplicativo Xamarin. Forms de plataforma cruzada de única página, que permite que você insira uma anotação e persista-a no armazenamento do dispositivo.
zone_pivot_groups: platform-dev16
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: E8CF05B1-54B9-428B-8518-D068837BD61E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/01/2019
ms.openlocfilehash: c1d7aa1535fe979df222aaedc6ba2cf3bae0d51c
ms.sourcegitcommit: bb6a103de620386187a08151a4cdc29a4dc53a10
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2019
ms.locfileid: "71679982"
---
# <a name="create-a-single-page-xamarinforms-application"></a>Criar um aplicativo Xamarin. Forms de página única

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-singlepage/)

Neste guia de início rápido, você aprenderá a:

- Crie um aplicativo Xamarin. Forms de plataforma cruzada.
- Defina a interface do usuário para uma página usando XAML (eXtensible Application Markup Language).
- Interaja com elementos XAML da interface do usuário do código.

O guia de início rápido explica como criar um aplicativo Xamarin. Forms de plataforma cruzada, que permite que você insira uma anotação e persista-a no armazenamento do dispositivo. O aplicativo final é mostrado abaixo:

[![](single-page-images/screenshots-sml.png "Aplicativo do Notes")](single-page-images/screenshots.png#lightbox "Aplicativo do Notes")

::: zone pivot="windows"

### <a name="prerequisites"></a>Pré-requisitos

- Visual Studio 2019 (versão mais recente), com o **desenvolvimento móvel com** carga de trabalho do .net instalada.
- Conhecimento de C#.
- adicional Um Mac emparelhado para compilar o aplicativo no iOS.

Para obter mais informações sobre esses pré-requisitos, consulte [instalando o Xamarin](~/get-started/installation/index.md). Para obter informações sobre como conectar o Visual Studio 2019 a um host de build Mac, confira [Emparelhar com o Mac para desenvolvimento no Xamarin.iOS](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

## <a name="get-started-with-visual-studio-2019"></a>Introdução ao Visual Studio 2019

1. Inicie o Visual Studio 2019 e, na janela iniciar, clique em **criar um novo projeto** para criar um novo projeto:

    ![](single-page-images/vs/new-solution-2019.png "Novo Projeto")

2. Na janela **criar um novo projeto** , selecione **móvel** na lista suspensa **tipo de projeto** , selecione o modelo **aplicativo móvel (Xamarin. Forms)** e clique no botão **Avançar** :

    ![](single-page-images/vs/new-project-2019.png "Modelos de Projeto de Multiplataforma")

3. Na janela **configurar seu novo projeto** , defina o **nome do projeto** como **anotações**, escolha um local adequado para o projeto e clique no botão **criar** :

    ![](single-page-images/vs/configure-project.png "Configurar seu projeto")

    > [!IMPORTANT]
    > Os snippets C# e XAML neste início rápido requerem que a solução seja nomeada como **Notas**. O uso de um nome diferente resultará em erros de build ao copiar o código deste início rápido para a solução.

4. Na caixa de diálogo **novo aplicativo de plataforma cruzada** , clique em **aplicativo em branco**e clique no botão **OK** :

    ![](single-page-images/vs/new-app-2019.png "Novo Aplicativo de Multiplataforma")

    Para obter mais informações sobre a biblioteca .NET Standard criada, confira [Anatomia de um aplicativo Xamarin.Forms](deepdive.md#anatomy-of-a-xamarinforms-application) em [Aprofundamento do Início Rápido do Xamarin.Forms](deepdive.md).

5. No **Gerenciador de Soluções**, no projeto **Notas**, clique duas vezes em **MainPage.xaml** para abri-lo:

    ![](single-page-images/vs/open-mainpage-xaml-2019.png "Abrir MainPage.xaml")

6. Em **MainPage.xaml**, remova todo o código de modelo e substitua-o pelo código a seguir:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.MainPage">
        <StackLayout Margin="10,35,10,10">
            <Label Text="Notes"
                   HorizontalOptions="Center"
                   FontAttributes="Bold" />
            <Editor x:Name="editor"
                    Placeholder="Enter your note"
                    HeightRequest="100" />
            <Grid>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>
                <Button Text="Save"
                        Clicked="OnSaveButtonClicked" />
                <Button Grid.Column="1"
                        Text="Delete"
                        Clicked="OnDeleteButtonClicked"/>
            </Grid>
        </StackLayout>
    </ContentPage>
    ```

    Esse código define declarativamente a interface do usuário para a página, que consiste em [`Label`](xref:Xamarin.Forms.Label) um para exibir texto, [`Editor`](xref:Xamarin.Forms.Editor) uma entrada de texto e duas [`Button`](xref:Xamarin.Forms.Button) instâncias que direcionam o aplicativo para salvar ou excluir um arquivo. As duas instâncias de `Button` são dispostas horizontalmente em uma [`Grid`](xref:Xamarin.Forms.Grid), com o `Label`, o `Editor` e a `Grid` dispostos verticalmente em um [`StackLayout`](xref:Xamarin.Forms.StackLayout). Para obter mais informações sobre como criar a interface do usuário, consulte [interface do usuário](deepdive.md#user-interface) no guia de [início rápido do Xamarin. Forms](deepdive.md).

    Salve as alterações em **MainPage.xaml** ao pressionar **CTRL+S** e feche o arquivo.

7. No **Gerenciador de Soluções**, no projeto **Notas**, expanda **MainPage.xaml** e clique duas vezes em **MainPage.xaml.cs** para abri-lo:

    ![](single-page-images/vs/open-mainpage-codebehind-2019.png "Abrir MainPage.xaml.cs")

8. Em **MainPage.xaml.cs**, remova todo o código de modelo e substitua-o pelo código a seguir:

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;

    namespace Notes
    {
        public partial class MainPage : ContentPage
        {
            string _fileName = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "notes.txt");

            public MainPage()
            {
                InitializeComponent();

                if (File.Exists(_fileName))
                {
                    editor.Text = File.ReadAllText(_fileName);
                }
            }

            void OnSaveButtonClicked(object sender, EventArgs e)
            {
                File.WriteAllText(_fileName, editor.Text);
            }

            void OnDeleteButtonClicked(object sender, EventArgs e)
            {
                if (File.Exists(_fileName))
                {
                    File.Delete(_fileName);
                }
                editor.Text = string.Empty;
            }
        }
    }
    ```

    Esse código define um campo `_fileName`, que referencia um arquivo chamado `notes.txt` que armazenará os dados de notas na pasta de dados local do aplicativo. Quando o construtor de página for executado o arquivo será lido, caso exista, e exibido no [`Editor`](xref:Xamarin.Forms.Editor). Quando o [`Button`](xref:Xamarin.Forms.Button) **Salvar** for pressionado, o manipulador de eventos `OnSaveButtonClicked` será executado, salvando no arquivo o conteúdo do `Editor`. Quando o `Button` **Excluir** for pressionado, o manipulador de eventos `OnDeleteButtonClicked` será executado, excluindo o arquivo, caso ele exista, e removendo todo o texto do `Editor`. Para obter mais informações sobre a interação do usuário, consulte [respondendo à interação do usuário](deepdive.md#responding-to-user-interaction) no guia de [início rápido do Xamarin. Forms](deepdive.md).

    Salve as alterações em **MainPage.xaml.cs** ao pressionar **CTRL+S** e feche o arquivo.

### <a name="building-the-quickstart"></a>Criando o Início Rápido

1. No Visual Studio, selecione o item de menu **Criar > Criar Solução** (ou pressione F6). A solução será criada e uma mensagem de êxito será exibida na barra de status do Visual Studio:

      ![](single-page-images/vs/build-succeeded.png "Build concluído com êxito")

    Se houver erros, repita as etapas anteriores e corrija-os até que a solução seja compilada com êxito.

2. Na barra de ferramentas do Visual Studio, pressione o botão **Iniciar** (o botão triangular que se parece com o botão Reproduzir) para iniciar o aplicativo em seu emulador Android escolhido:

    ![](single-page-images/vs/android-start.png "Barra de ferramentas do Visual Studio para Android")

    [![](single-page-images/vs/notes-android.png "Observações no Android Emulator")](single-page-images/vs/notes-android-large.png#lightbox "Notes in the Android Simulator")

    Insira uma nota e pressione o botão **Salvar**.

    Para obter mais informações sobre como o aplicativo é iniciado em cada plataforma, consulte [iniciando o aplicativo em cada plataforma](deepdive.md#launching-the-application-on-each-platform) no guia de [início rápido do Xamarin. Forms](deepdive.md).

    > [!NOTE]
    > As etapas a seguir deverão ser realizadas somente se você tiver um [Mac emparelhado](~/ios/get-started/installation/windows/connecting-to-mac/index.md) que atenda aos requisitos do sistema para o desenvolvimento no Xamarin.Forms.

3. Na barra de ferramentas do Visual Studio, clique com o botão direito do mouse no projeto **Notes.iOS** e selecione **Definir como projeto de inicialização**.

      ![](single-page-images/vs/set-as-startup-project-ios.png "Definir o iOS como projeto de inicialização")

4. Na barra de ferramentas do Visual Studio, pressione o botão **Iniciar** (o botão triangular que se parece com o botão Reproduzir) para iniciar o aplicativo em seu [Simulador remoto do iOS](~/tools/ios-simulator/index.md) escolhido:

    ![](single-page-images/vs/ios-start.png "Barra de ferramentas do iOS do Visual Studio")

    [![](single-page-images/vs/notes-ios.png "Observações no simulador de iOS")](single-page-images/vs/notes-ios-large.png#lightbox "Observações no simulador de iOS")

    Insira uma nota e pressione o botão **Salvar**.

    Para obter mais informações sobre como o aplicativo é iniciado em cada plataforma, consulte [iniciando o aplicativo em cada plataforma](deepdive.md#launching-the-application-on-each-platform) no guia de [início rápido do Xamarin. Forms](deepdive.md).

::: zone-end
::: zone pivot="win-vs2017"

### <a name="prerequisites"></a>Pré-requisitos

- Visual Studio 2017, com o **desenvolvimento móvel com** carga de trabalho do .net instalado.
- Conhecimento de C#.
- adicional Um Mac emparelhado para compilar o aplicativo no iOS.

Para obter mais informações sobre esses pré-requisitos, consulte [instalando o Xamarin](~/get-started/installation/index.md). Para obter informações sobre como conectar o Visual Studio 2019 a um host de build Mac, confira [Emparelhar com o Mac para desenvolvimento no Xamarin.iOS](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

## <a name="get-started-with-visual-studio-2017"></a>Introdução ao Visual Studio 2017

1. Inicie o Visual Studio 2017 e, na página Iniciar, clique em **criar novo projeto...** para criar um novo projeto:

    ![](single-page-images/vs/new-solution.png "Novo Projeto")

2. Na caixa de diálogo **Novo Projeto**, clique em **Multiplataforma**, selecione o modelo **Aplicativo Móvel (Xamarin.Forms)** , defina o Nome como **Notas**, escolha um local adequado para o projeto e clique no botão **OK**:

    ![](single-page-images/vs/new-project.png "Modelos de Projeto de Multiplataforma")

    > [!IMPORTANT]
    > Os snippets C# e XAML neste início rápido requerem que a solução seja nomeada como **Notas**. O uso de um nome diferente resultará em erros de build ao copiar o código deste início rápido para a solução.

3. Na caixa de diálogo **Novo Aplicativo de Plataforma Cruzada**, clique em **Aplicativo em Branco**, selecione **.NET Standard** como a estratégia de compartilhamento de código e clique no botão **OK**:

    ![](single-page-images/vs/new-app.png "Novo Aplicativo de Multiplataforma")

    Para obter mais informações sobre a biblioteca .NET Standard criada, confira [Anatomia de um aplicativo Xamarin.Forms](deepdive.md#anatomy-of-a-xamarinforms-application) em [Aprofundamento do Início Rápido do Xamarin.Forms](deepdive.md).

4. No **Gerenciador de Soluções**, no projeto **Notas**, clique duas vezes em **MainPage.xaml** para abri-lo:

    ![](single-page-images/vs/open-mainpage-xaml.png "Abrir MainPage.xaml")

5. Em **MainPage.xaml**, remova todo o código de modelo e substitua-o pelo código a seguir:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.MainPage">
        <StackLayout Margin="10,35,10,10">
            <Label Text="Notes"
                   HorizontalOptions="Center"
                   FontAttributes="Bold" />
            <Editor x:Name="editor"
                    Placeholder="Enter your note"
                    HeightRequest="100" />
            <Grid>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>
                <Button Text="Save"
                        Clicked="OnSaveButtonClicked" />
                <Button Grid.Column="1"
                        Text="Delete"
                        Clicked="OnDeleteButtonClicked"/>
            </Grid>
        </StackLayout>
    </ContentPage>
    ```

    Esse código define declarativamente a interface do usuário para a página, que consiste em [`Label`](xref:Xamarin.Forms.Label) um para exibir texto, [`Editor`](xref:Xamarin.Forms.Editor) uma entrada de texto e duas [`Button`](xref:Xamarin.Forms.Button) instâncias que direcionam o aplicativo para salvar ou excluir um arquivo. As duas instâncias de `Button` são dispostas horizontalmente em uma [`Grid`](xref:Xamarin.Forms.Grid), com o `Label`, o `Editor` e a `Grid` dispostos verticalmente em um [`StackLayout`](xref:Xamarin.Forms.StackLayout). Para obter mais informações sobre como criar a interface do usuário, consulte [interface do usuário](deepdive.md#user-interface) no guia de [início rápido do Xamarin. Forms](deepdive.md).

    Salve as alterações em **MainPage.xaml** ao pressionar **CTRL+S** e feche o arquivo.

6. No **Gerenciador de Soluções**, no projeto **Notas**, expanda **MainPage.xaml** e clique duas vezes em **MainPage.xaml.cs** para abri-lo:

    ![](single-page-images/vs/open-mainpage-codebehind.png "Abrir MainPage.xaml.cs")

7. Em **MainPage.xaml.cs**, remova todo o código de modelo e substitua-o pelo código a seguir:

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;

    namespace Notes
    {
        public partial class MainPage : ContentPage
        {
            string _fileName = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "notes.txt");

            public MainPage()
            {
                InitializeComponent();

                if (File.Exists(_fileName))
                {
                    editor.Text = File.ReadAllText(_fileName);
                }
            }

            void OnSaveButtonClicked(object sender, EventArgs e)
            {
                File.WriteAllText(_fileName, editor.Text);
            }

            void OnDeleteButtonClicked(object sender, EventArgs e)
            {
                if (File.Exists(_fileName))
                {
                    File.Delete(_fileName);
                }
                editor.Text = string.Empty;
            }
        }
    }
    ```

    Esse código define um campo `_fileName`, que referencia um arquivo chamado `notes.txt` que armazenará os dados de notas na pasta de dados local do aplicativo. Quando o construtor de página for executado o arquivo será lido, caso exista, e exibido no [`Editor`](xref:Xamarin.Forms.Editor). Quando o [`Button`](xref:Xamarin.Forms.Button) **Salvar** for pressionado, o manipulador de eventos `OnSaveButtonClicked` será executado, salvando no arquivo o conteúdo do `Editor`. Quando o `Button` **Excluir** for pressionado, o manipulador de eventos `OnDeleteButtonClicked` será executado, excluindo o arquivo, caso ele exista, e removendo todo o texto do `Editor`. Para obter mais informações sobre a interação do usuário, consulte [respondendo à interação do usuário](deepdive.md#responding-to-user-interaction) no guia de [início rápido do Xamarin. Forms](deepdive.md).

    Salve as alterações em **MainPage.xaml.cs** ao pressionar **CTRL+S** e feche o arquivo.

### <a name="building-the-quickstart"></a>Criando o Início Rápido

1. No Visual Studio, selecione o item de menu **Criar > Criar Solução** (ou pressione F6). A solução será criada e uma mensagem de êxito será exibida na barra de status do Visual Studio:

      ![](single-page-images/vs/build-succeeded.png "Build concluído com êxito")

    Se houver erros, repita as etapas anteriores e corrija-os até que a solução seja compilada com êxito.

2. Na barra de ferramentas do Visual Studio, pressione o botão **Iniciar** (o botão triangular que se parece com o botão Reproduzir) para iniciar o aplicativo em seu emulador Android escolhido:

    ![](single-page-images/vs/android-start.png "Barra de ferramentas do Visual Studio para Android")

    [![](single-page-images/vs/notes-android.png "Observações no Android Emulator")](single-page-images/vs/notes-android-large.png#lightbox "Notes in the Android Simulator")

    Insira uma nota e pressione o botão **Salvar**.

    Para obter mais informações sobre como o aplicativo é iniciado em cada plataforma, consulte [iniciando o aplicativo em cada plataforma](deepdive.md#launching-the-application-on-each-platform) no guia de [início rápido do Xamarin. Forms](deepdive.md).

    > [!NOTE]
    > As etapas a seguir deverão ser realizadas somente se você tiver um [Mac emparelhado](~/ios/get-started/installation/windows/connecting-to-mac/index.md) que atenda aos requisitos do sistema para o desenvolvimento no Xamarin.Forms.

3. Na barra de ferramentas do Visual Studio, clique com o botão direito do mouse no projeto **Notes.iOS** e selecione **Definir como projeto de inicialização**.

      ![](single-page-images/vs/set-as-startup-project-ios.png "Definir o iOS como projeto de inicialização")

4. Na barra de ferramentas do Visual Studio, pressione o botão **Iniciar** (o botão triangular que se parece com o botão Reproduzir) para iniciar o aplicativo em seu [Simulador remoto do iOS](~/tools/ios-simulator/index.md) escolhido:

    ![](single-page-images/vs/ios-start.png "Barra de ferramentas do iOS do Visual Studio")

    [![](single-page-images/vs/notes-ios.png "Observações no simulador de iOS")](single-page-images/vs/notes-ios-large.png#lightbox "Observações no simulador de iOS")

    Insira uma nota e pressione o botão **Salvar**.

    Para obter mais informações sobre como o aplicativo é iniciado em cada plataforma, consulte [iniciando o aplicativo em cada plataforma](deepdive.md#launching-the-application-on-each-platform) no guia de [início rápido do Xamarin. Forms](deepdive.md).

::: zone-end
::: zone pivot="macos"

### <a name="prerequisites"></a>Pré-requisitos

- Visual Studio para Mac (versão mais recente), com o suporte à plataforma iOS e Android instalado.
- Xcode (versão mais recente).
- Conhecimento de C#.

Para obter mais informações sobre esses pré-requisitos, consulte [instalando o Xamarin](~/get-started/installation/index.md).

## <a name="get-started-with-visual-studio-for-mac"></a>Introdução ao Visual Studio para Mac

1. Inicie o Visual Studio para Mac e, na janela iniciar, clique em **novo** para criar um novo projeto:

    ![](single-page-images/vsmac/new-project.png "Nova Solução")

2. Na caixa de diálogo **Escolher um modelo para seu novo projeto**, clique em **Multiplataforma > Aplicativo**, selecione o modelo do **Aplicativo de Formulários em Branco** e clique no botão **Avançar**:

    ![](single-page-images/vsmac/choose-template.png "Escolher um modelo")

3. Na caixa de diálogo **Configurar seu aplicativo de formulários em branco**, nomeie o novo aplicativo como **Notas**, selecione o botão de opção **Usar .NET Standard** e clique no botão **Avançar**:    

    ![](single-page-images/vsmac/configure-app.png "Configurar o Aplicativo Forms")

4. Na caixa de diálogo **Configurar seu novo aplicativo de formulários em branco**, deixe os nomes do Projeto e da Solução definidos como **Notas**, escolha um local adequado para o projeto e clique no botão **Criar** para criá-lo:

    ![](single-page-images/vsmac/configure-project.png "Configurar o Projeto Forms")

    > [!IMPORTANT]
    > Os snippets C# e XAML neste início rápido requerem que a solução e o projeto sejam nomeados como **Notas**. O uso de um nome diferente resultará em erros de build ao copiar o código deste início rápido para o projeto.

    Para obter mais informações sobre a biblioteca .NET Standard criada, confira [Anatomia de um aplicativo Xamarin.Forms](deepdive.md#anatomy-of-a-xamarinforms-application) em [Aprofundamento do Início Rápido do Xamarin.Forms](deepdive.md).

5. No **Painel de Soluções**, no projeto **Notas**, clique duas vezes em **MainPage.xaml** para abri-lo:

    ![](single-page-images/vsmac/mainpage-xaml.png "MainPage.xaml")

6. Em **MainPage.xaml**, remova todo o código de modelo e substitua-o pelo código a seguir:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.MainPage">
        <StackLayout Margin="10,35,10,10">
            <Label Text="Notes"
                   HorizontalOptions="Center"
                   FontAttributes="Bold" />
            <Editor x:Name="editor"
                    Placeholder="Enter your note"
                    HeightRequest="100" />
            <Grid>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>
                <Button Text="Save"
                        Clicked="OnSaveButtonClicked" />
                <Button Grid.Column="1"
                        Text="Delete"
                        Clicked="OnDeleteButtonClicked"/>
            </Grid>
        </StackLayout>
    </ContentPage>
    ```

    Esse código define declarativamente a interface do usuário para a página, que consiste em [`Label`](xref:Xamarin.Forms.Label) um para exibir texto, [`Editor`](xref:Xamarin.Forms.Editor) uma entrada de texto e duas [`Button`](xref:Xamarin.Forms.Button) instâncias que direcionam o aplicativo para salvar ou excluir um arquivo. As duas instâncias de `Button` são dispostas horizontalmente em uma [`Grid`](xref:Xamarin.Forms.Grid), com o `Label`, o `Editor` e a `Grid` dispostos verticalmente em um [`StackLayout`](xref:Xamarin.Forms.StackLayout). Para obter mais informações sobre como criar a interface do usuário, consulte [interface do usuário](deepdive.md#user-interface) no guia de [início rápido do Xamarin. Forms](deepdive.md).

    Salve as alterações em **MainPage.xaml** ao escolher **Arquivo > Salvar** (ou ao pressionar **&#8984; + S**) e feche o arquivo.

7. No **Painel de Soluções**, no projeto **Notas**, expanda **MainPage.xaml** e clique duas vezes em **MainPage.xaml.cs** para abri-lo:

    ![](single-page-images/vsmac/mainpage-xaml-cs.png "MainPage.xaml.cs")

8. Em **MainPage.xaml.cs**, remova todo o código de modelo e substitua-o pelo código a seguir:

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;

    namespace Notes
    {
        public partial class MainPage : ContentPage
        {
            string _fileName = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "notes.txt");

            public MainPage()
            {
                InitializeComponent();

                if (File.Exists(_fileName))
                {
                    editor.Text = File.ReadAllText(_fileName);
                }
            }

            void OnSaveButtonClicked(object sender, EventArgs e)
            {
                File.WriteAllText(_fileName, editor.Text);
            }

            void OnDeleteButtonClicked(object sender, EventArgs e)
            {
                if (File.Exists(_fileName))
                {
                    File.Delete(_fileName);
                }
                editor.Text = string.Empty;
            }
        }
    }
    ```

    Esse código define um campo `_fileName`, que referencia um arquivo chamado `notes.txt` que armazenará os dados de notas na pasta de dados local do aplicativo. Quando o construtor de página for executado o arquivo será lido, caso exista, e exibido no [`Editor`](xref:Xamarin.Forms.Editor). Quando o [`Button`](xref:Xamarin.Forms.Button) **Salvar** for pressionado, o manipulador de eventos `OnSaveButtonClicked` será executado, salvando no arquivo o conteúdo do `Editor`. Quando o `Button` **Excluir** for pressionado, o manipulador de eventos `OnDeleteButtonClicked` será executado, excluindo o arquivo, caso ele exista, e removendo todo o texto do `Editor`. Para obter mais informações sobre a interação do usuário, consulte [respondendo à interação do usuário](deepdive.md#responding-to-user-interaction) no guia de [início rápido do Xamarin. Forms](deepdive.md).

    Salve as alterações em **MainPage.xaml.cs** ao escolher **Arquivo > Salvar** (ou ao pressionar **&#8984; + S**) e feche o arquivo.

### <a name="building-the-quickstart"></a>Criando o Início Rápido

1. No Visual Studio para Mac, selecione o item de menu **Compilar > Compilar Tudo** (ou pressione **&#8984; + B**). Os projetos serão criados e uma mensagem de êxito será exibida na barra de ferramentas do Visual Studio para Mac.

      ![](single-page-images/vsmac/build-successful.png "Build bem-sucedido")

    Se houver erros, repita as etapas anteriores e corrija-os até que os projetos sejam compilados com êxito.

2. No **painel de soluções**, selecione o projeto **Notes. Ios** , clique com o botão direito do mouse e selecione **definir como projeto de inicialização**:

      ![](single-page-images/vsmac/set-startup-project-ios.png "Definir o iOS como projeto de inicialização")

3. Na barra de ferramentas do Visual Studio para Mac, pressione o botão **Iniciar** (o botão triangular que se parece com o botão Reproduzir) para iniciar o aplicativo no Simulador do iOS escolhido:

      ![](single-page-images/vsmac/start.png "Barra de ferramentas do Visual Studio para Mac")

      [![](single-page-images/vsmac/notes-ios.png "Observações no simulador de iOS")](single-page-images/vsmac/notes-ios-large.png#lightbox "Observações no simulador de iOS")

    Insira uma nota e pressione o botão **Salvar**.

    Para obter mais informações sobre como o aplicativo é iniciado em cada plataforma, consulte [iniciando o aplicativo em cada plataforma](deepdive.md#launching-the-application-on-each-platform) no guia de [início rápido do Xamarin. Forms](deepdive.md).

4. No **painel de soluções**, selecione o projeto **Notes. Droid** , clique com o botão direito do mouse e selecione **definir como projeto de inicialização**:

      ![](single-page-images/vsmac/set-startup-project-android.png "Definir Android como projeto de inicialização")

5. Na barra de ferramentas do Visual Studio para Mac, pressione o botão **Iniciar** (o botão triangular que se parece com o botão Reproduzir) para iniciar o aplicativo em seu emulador Android escolhido:

      [![](single-page-images/vsmac/notes-android.png "Observações no Android Emulator")](single-page-images/vsmac/notes-android-large.png#lightbox "Notes in the Android Simulator")

    Insira uma nota e pressione o botão **Salvar**.

    Para obter mais informações sobre como o aplicativo é iniciado em cada plataforma, consulte [iniciando o aplicativo em cada plataforma](deepdive.md#launching-the-application-on-each-platform) no guia de [início rápido do Xamarin. Forms](deepdive.md).

::: zone-end

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você aprendeu a:

- Crie um aplicativo Xamarin. Forms de plataforma cruzada.
- Defina a interface do usuário para uma página usando XAML (eXtensible Application Markup Language).
- Interaja com elementos XAML da interface do usuário do código.

Para transformar esse aplicativo de página única em um aplicativo de várias páginas, vá para o próximo início rápido.

> [!div class="nextstepaction"]
> [Avançar](multi-page.md)

## <a name="related-links"></a>Links relacionados

- [Notas (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-singlepage/)
- [Detalhamento do guia de início rápido do Xamarin. Forms](deepdive.md)
