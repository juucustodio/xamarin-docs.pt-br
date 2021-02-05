---
title: Criar um aplicativo de página única Xamarin.Forms
description: Este artigo explica como criar um aplicativo de plataforma cruzada de página única Xamarin.Forms , que permite que você insira uma anotação e persista-a no armazenamento do dispositivo.
zone_pivot_groups: platform-dev16
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: E8CF05B1-54B9-428B-8518-D068837BD61E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/01/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b550aed2dd2038a48fa4eafcf1ed1d874a08eb03
ms.sourcegitcommit: 10c7dd16fe78226053d1d036492b6c9102fc421b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "93375519"
---
# <a name="create-a-single-page-xamarinforms-application"></a>Criar um aplicativo de página única Xamarin.Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/getstarted-notes-singlepage/)

Neste guia de início rápido, você aprenderá a:

- Crie um aplicativo de plataforma cruzada Xamarin.Forms .
- Definir a interface do usuário para uma página usando a linguagem XAML.
- Interagir com elementos da interface do usuário XAML do código.

O guia de início rápido explica como criar um aplicativo de plataforma cruzada Xamarin.Forms , que permite que você insira uma anotação e persista-a no armazenamento do dispositivo. O aplicativo final é mostrado abaixo:

[![Aplicativo do Notes](single-page-images/screenshots-sml.png)](single-page-images/screenshots.png#lightbox "Aplicativo do Notes")

::: zone pivot="windows"

### <a name="prerequisites"></a>Pré-requisitos

- Visual Studio 2019 (versão mais recente), com a carga de trabalho de **Desenvolvimento móvel com .NET** instalada.
- Conhecimento de C#.
- (opcional) Um Mac emparelhado para compilar o aplicativo no iOS.

Para obter mais informações sobre esses pré-requisitos, confira [Como instalar o Xamarin](~/get-started/installation/index.md). Para obter informações sobre como conectar o Visual Studio 2019 a um host de build Mac, confira [Emparelhar com o Mac para desenvolvimento no Xamarin.iOS](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

## <a name="get-started-with-visual-studio-2019"></a>Introdução ao Visual Studio 2019

1. Inicie o Visual Studio 2019 e, na janela inicial, clique em **Criar um projeto** para criar um projeto:

    ![Novo Projeto](single-page-images/vs/new-solution-2019.png)

2. Na janela **criar um novo projeto** , selecione **móvel** na lista suspensa **tipo de projeto** , selecione o modelo **aplicativo móvel ( Xamarin.Forms )** e clique no botão **Avançar** :

    ![Modelos de Projeto Multiplataforma](single-page-images/vs/new-project-2019.png)

3. Na janela **Configurar seu novo projeto**, defina o **Nome do projeto** como **Notes**, escolha uma localização adequada para o projeto e clique no botão **Criar**:

    ![Configurar seu projeto](single-page-images/vs/configure-project.png)

    > [!IMPORTANT]
    > Os snippets C# e XAML neste início rápido requerem que a solução seja nomeada como **Notas**. O uso de um nome diferente resultará em erros de build ao copiar o código deste início rápido para a solução.

4. Na caixa de diálogo **Novo Aplicativo Multiplataforma**, clique em **Aplicativo em Branco** e clique no botão **OK**:

    ![Novo Aplicativo Multiplataforma](single-page-images/vs/new-app-2019.png)

    Para obter mais informações sobre a biblioteca de .NET Standard que é criada, consulte a [anatomia de um Xamarin.Forms aplicativo](deepdive.md#anatomy-of-a-xamarinforms-application) no guia de [ Xamarin.Forms início rápido](deepdive.md)aprofundado.

5. No **Gerenciador de Soluções**, no projeto **Notas**, clique duas vezes em **MainPage.xaml** para abri-lo:

    ![Abra MainPage.xaml](single-page-images/vs/open-mainpage-xaml-2019.png)

6. Em **MainPage. XAML**, remova todo o código do modelo e substitua-o pelo seguinte código:

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

    Esse código define declarativamente a interface do usuário para a página, que consiste em um [`Label`](xref:Xamarin.Forms.Label) para exibir texto, uma [`Editor`](xref:Xamarin.Forms.Editor) entrada de texto e duas [`Button`](xref:Xamarin.Forms.Button) instâncias que direcionam o aplicativo para salvar ou excluir um arquivo. As duas `Button` instâncias são dispostas horizontalmente em um [`Grid`](xref:Xamarin.Forms.Grid) , com o `Label` , `Editor` e `Grid` que estão sendo dispostos verticalmente em um [`StackLayout`](xref:Xamarin.Forms.StackLayout) . Para obter mais informações sobre como criar a interface do usuário, consulte [interface do usuário](deepdive.md#user-interface) no guia de [ Xamarin.Forms início rápido](deepdive.md)aprofundamento.

    Salve as alterações em **MainPage.xaml** ao pressionar **CTRL+S** e feche o arquivo.

7. No **Gerenciador de Soluções**, no projeto **Notas**, expanda **MainPage.xaml** e clique duas vezes em **MainPage.xaml.cs** para abri-lo:

    ![Abra MainPage.xaml.cs](single-page-images/vs/open-mainpage-codebehind-2019.png)

8. No **MainPage.XAML.cs**, remova todo o código do modelo e substitua-o pelo código a seguir:

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

    Esse código define um campo `_fileName`, que referencia um arquivo chamado `notes.txt` que armazenará os dados de notas na pasta de dados local do aplicativo. Quando o construtor da página é executado, o arquivo é lido, se existir, e exibido no [`Editor`](xref:Xamarin.Forms.Editor) . Quando o **salvamento** [`Button`](xref:Xamarin.Forms.Button) é pressionado `OnSaveButtonClicked` , o manipulador de eventos é executado, o que salva o conteúdo do no `Editor` arquivo. Quando o **Excluir** `Button` for pressionado, o manipulador de eventos `OnDeleteButtonClicked` será executado, excluindo o arquivo, caso ele exista, e removendo todo o texto do `Editor`. Para obter mais informações sobre a interação do usuário, consulte [respondendo à interação do usuário](deepdive.md#responding-to-user-interaction) no guia de [ Xamarin.Forms início rápido](deepdive.md)aprofundamento.

    Salve as alterações em **MainPage.xaml.cs** ao pressionar **CTRL+S** e feche o arquivo.

### <a name="building-the-quickstart"></a>Criando o Início Rápido

1. No Visual Studio, selecione o item de menu **Criar > Criar Solução** (ou pressione F6). A solução será criada e uma mensagem de êxito será exibida na barra de status do Visual Studio:

      ![Build bem-sucedido](single-page-images/vs/build-succeeded.png)

    Se houver erros, repita as etapas anteriores e corrija-os até que a solução seja compilada com êxito.

2. Na barra de ferramentas do Visual Studio, pressione o botão **Iniciar** (o botão triangular que se parece com o botão Reproduzir) para iniciar o aplicativo em seu emulador Android escolhido:

    ![Barra de ferramentas do Visual Studio para Android](single-page-images/vs/android-start.png)

    [![Observações no Android Emulator](single-page-images/vs/notes-android.png)](single-page-images/vs/notes-android-large.png#lightbox "Observações no Simulador de Android")

    Insira uma nota e pressione o botão **Salvar**.

    Para obter mais informações sobre como o aplicativo é iniciado em cada plataforma, consulte [iniciando o aplicativo em cada plataforma](deepdive.md#launching-the-application-on-each-platform) no guia de [ Xamarin.Forms início rápido](deepdive.md)aprofundamento.

    > [!NOTE]
    > As etapas a seguir só devem ser executadas se você tiver um [Mac emparelhado](~/ios/get-started/installation/windows/connecting-to-mac/index.md) que atenda aos requisitos de sistema para Xamarin.Forms desenvolvimento.

3. Na barra de ferramentas do Visual Studio, clique com o botão direito do mouse no projeto **Notes.iOS** e selecione **Definir como projeto de inicialização**.

      ![Definir o iOS como Projeto Inicial](single-page-images/vs/set-as-startup-project-ios.png)

4. Na barra de ferramentas do Visual Studio, pressione o botão **Iniciar** (o botão triangular que se parece com o botão Reproduzir) para iniciar o aplicativo em seu [Simulador remoto do iOS](~/tools/ios-simulator/index.md) escolhido:

    ![Barra de ferramentas do Visual Studio para iOS](single-page-images/vs/ios-start.png)

    [![Observações no Simulador do iOS](single-page-images/vs/notes-ios.png)](single-page-images/vs/notes-ios-large.png#lightbox "Observações no Simulador do iOS")

    Insira uma nota e pressione o botão **Salvar**.

    Para obter mais informações sobre como o aplicativo é iniciado em cada plataforma, consulte [iniciando o aplicativo em cada plataforma](deepdive.md#launching-the-application-on-each-platform) no guia de [ Xamarin.Forms início rápido](deepdive.md)aprofundamento.

::: zone-end
::: zone pivot="win-vs2017"

### <a name="prerequisites"></a>Pré-requisitos

- Visual Studio 2017, com a carga de trabalho de **Desenvolvimento móvel com .NET** instalada.
- Conhecimento de C#.
- (opcional) Um Mac emparelhado para compilar o aplicativo no iOS.

Para obter mais informações sobre esses pré-requisitos, confira [Como instalar o Xamarin](~/get-started/installation/index.md). Para obter informações sobre como conectar o Visual Studio 2019 a um host de build Mac, confira [Emparelhar com o Mac para desenvolvimento no Xamarin.iOS](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

## <a name="get-started-with-visual-studio-2017"></a>Introdução ao Visual Studio 2017

1. Inicialize o Visual Studio 2017 e, na página inicial, clique em **Criar projeto…** para criar um novo projeto:

    ![Novo Projeto](single-page-images/vs/new-solution.png)

2. Na caixa de diálogo **novo projeto** , clique em **plataforma cruzada**, selecione o modelo **aplicativo móvel ( Xamarin.Forms )** , defina o nome como **notas**, escolha um local adequado para o projeto e clique no botão **OK** :

    ![Modelos de Projeto Multiplataforma](single-page-images/vs/new-project.png)

    > [!IMPORTANT]
    > Os snippets C# e XAML neste início rápido requerem que a solução seja nomeada como **Notas**. O uso de um nome diferente resultará em erros de build ao copiar o código deste início rápido para a solução.

3. Na caixa de diálogo **Novo Aplicativo de Plataforma Cruzada**, clique em **Aplicativo em Branco**, selecione **.NET Standard** como a estratégia de compartilhamento de código e clique no botão **OK**:

    ![Novo Aplicativo Multiplataforma](single-page-images/vs/new-app.png)

    Para obter mais informações sobre a biblioteca de .NET Standard que é criada, consulte a [anatomia de um Xamarin.Forms aplicativo](deepdive.md#anatomy-of-a-xamarinforms-application) no guia de [ Xamarin.Forms início rápido](deepdive.md)aprofundado.

4. No **Gerenciador de Soluções**, no projeto **Notas**, clique duas vezes em **MainPage.xaml** para abri-lo:

    ![Abra MainPage.xaml](single-page-images/vs/open-mainpage-xaml.png)

5. Em **MainPage. XAML**, remova todo o código do modelo e substitua-o pelo seguinte código:

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

    Esse código define declarativamente a interface do usuário para a página, que consiste em um [`Label`](xref:Xamarin.Forms.Label) para exibir texto, uma [`Editor`](xref:Xamarin.Forms.Editor) entrada de texto e duas [`Button`](xref:Xamarin.Forms.Button) instâncias que direcionam o aplicativo para salvar ou excluir um arquivo. As duas `Button` instâncias são dispostas horizontalmente em um [`Grid`](xref:Xamarin.Forms.Grid) , com o `Label` , `Editor` e `Grid` que estão sendo dispostos verticalmente em um [`StackLayout`](xref:Xamarin.Forms.StackLayout) . Para obter mais informações sobre como criar a interface do usuário, consulte [interface do usuário](deepdive.md#user-interface) no guia de [ Xamarin.Forms início rápido](deepdive.md)aprofundamento.

    Salve as alterações em **MainPage.xaml** ao pressionar **CTRL+S** e feche o arquivo.

6. No **Gerenciador de Soluções**, no projeto **Notas**, expanda **MainPage.xaml** e clique duas vezes em **MainPage.xaml.cs** para abri-lo:

    ![Abra MainPage.xaml.cs](single-page-images/vs/open-mainpage-codebehind.png)

7. No **MainPage.XAML.cs**, remova todo o código do modelo e substitua-o pelo código a seguir:

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

    Esse código define um campo `_fileName`, que referencia um arquivo chamado `notes.txt` que armazenará os dados de notas na pasta de dados local do aplicativo. Quando o construtor da página é executado, o arquivo é lido, se existir, e exibido no [`Editor`](xref:Xamarin.Forms.Editor) . Quando o **salvamento** [`Button`](xref:Xamarin.Forms.Button) é pressionado `OnSaveButtonClicked` , o manipulador de eventos é executado, o que salva o conteúdo do no `Editor` arquivo. Quando o **Excluir** `Button` for pressionado, o manipulador de eventos `OnDeleteButtonClicked` será executado, excluindo o arquivo, caso ele exista, e removendo todo o texto do `Editor`. Para obter mais informações sobre a interação do usuário, consulte [respondendo à interação do usuário](deepdive.md#responding-to-user-interaction) no guia de [ Xamarin.Forms início rápido](deepdive.md)aprofundamento.

    Salve as alterações em **MainPage.xaml.cs** ao pressionar **CTRL+S** e feche o arquivo.

### <a name="building-the-quickstart"></a>Criando o Início Rápido

1. No Visual Studio, selecione o item de menu **Criar > Criar Solução** (ou pressione F6). A solução será criada e uma mensagem de êxito será exibida na barra de status do Visual Studio:

      ![Build bem-sucedido](single-page-images/vs/build-succeeded.png)

    Se houver erros, repita as etapas anteriores e corrija-os até que a solução seja compilada com êxito.

2. Na barra de ferramentas do Visual Studio, pressione o botão **Iniciar** (o botão triangular que se parece com o botão Reproduzir) para iniciar o aplicativo em seu emulador Android escolhido:

    ![Barra de ferramentas do Visual Studio para Android](single-page-images/vs/android-start.png)

    [![Observações no Android Emulator](single-page-images/vs/notes-android.png)](single-page-images/vs/notes-android-large.png#lightbox "Observações no Simulador de Android")

    Insira uma nota e pressione o botão **Salvar**.

    Para obter mais informações sobre como o aplicativo é iniciado em cada plataforma, consulte [iniciando o aplicativo em cada plataforma](deepdive.md#launching-the-application-on-each-platform) no guia de [ Xamarin.Forms início rápido](deepdive.md)aprofundamento.

    > [!NOTE]
    > As etapas a seguir só devem ser executadas se você tiver um [Mac emparelhado](~/ios/get-started/installation/windows/connecting-to-mac/index.md) que atenda aos requisitos de sistema para Xamarin.Forms desenvolvimento.

3. Na barra de ferramentas do Visual Studio, clique com o botão direito do mouse no projeto **Notes.iOS** e selecione **Definir como projeto de inicialização**.

      ![Definir o iOS como Projeto Inicial](single-page-images/vs/set-as-startup-project-ios.png)

4. Na barra de ferramentas do Visual Studio, pressione o botão **Iniciar** (o botão triangular que se parece com o botão Reproduzir) para iniciar o aplicativo em seu [Simulador remoto do iOS](~/tools/ios-simulator/index.md) escolhido:

    ![Barra de ferramentas do Visual Studio para iOS](single-page-images/vs/ios-start.png)

    [![Observações no Simulador do iOS](single-page-images/vs/notes-ios.png)](single-page-images/vs/notes-ios-large.png#lightbox "Observações no Simulador do iOS")

    Insira uma nota e pressione o botão **Salvar**.

    Para obter mais informações sobre como o aplicativo é iniciado em cada plataforma, consulte [iniciando o aplicativo em cada plataforma](deepdive.md#launching-the-application-on-each-platform) no guia de [ Xamarin.Forms início rápido](deepdive.md)aprofundamento.

::: zone-end
::: zone pivot="macos"

### <a name="prerequisites"></a>Pré-requisitos

- Visual Studio para Mac (versão mais recente), compatível com a plataforma iOS e Android instalada.
- Xcode (versão mais recente).
- Conhecimento de C#.

Para obter mais informações sobre esses pré-requisitos, confira [Como instalar o Xamarin](~/get-started/installation/index.md).

## <a name="get-started-with-visual-studio-for-mac"></a>Introdução ao Visual Studio para Mac

1. Inicialize o Visual Studio para Mac e, na janela inicial, clique em **Novo** para criar um novo projeto:

    ![Nova solução](single-page-images/vsmac/new-project.png)

2. Na caixa de diálogo **Escolher um modelo para seu novo projeto**, clique em **Multiplataforma > Aplicativo**, selecione o modelo do **Aplicativo de Formulários em Branco** e clique no botão **Avançar**:

    ![Escolher um modelo](single-page-images/vsmac/choose-template.png)

3. Na caixa de diálogo **Configurar seu aplicativo de formulários em branco**, nomeie o novo aplicativo como **Notas**, selecione o botão de opção **Usar .NET Standard** e clique no botão **Avançar**:    

    ![Configurar o Aplicativo Forms](single-page-images/vsmac/configure-app.png)

4. Na caixa de diálogo **Configurar seu novo aplicativo de formulários em branco**, deixe os nomes do Projeto e da Solução definidos como **Notas**, escolha um local adequado para o projeto e clique no botão **Criar** para criá-lo:

    ![Configurar o Projeto Forms](single-page-images/vsmac/configure-project.png)

    > [!IMPORTANT]
    > Os snippets C# e XAML neste início rápido requerem que a solução e o projeto sejam nomeados como **Notas**. O uso de um nome diferente resultará em erros de build ao copiar o código deste início rápido para o projeto.

    Para obter mais informações sobre a biblioteca de .NET Standard que é criada, consulte a [anatomia de um Xamarin.Forms aplicativo](deepdive.md#anatomy-of-a-xamarinforms-application) no guia de [ Xamarin.Forms início rápido](deepdive.md)aprofundado.

5. No **Painel de Soluções**, no projeto **Notas**, clique duas vezes em **MainPage.xaml** para abri-lo:

    ![MainPage.xaml](single-page-images/vsmac/mainpage-xaml.png)

6. Em **MainPage. XAML**, remova todo o código do modelo e substitua-o pelo seguinte código:

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

    Esse código define declarativamente a interface do usuário para a página, que consiste em um [`Label`](xref:Xamarin.Forms.Label) para exibir texto, uma [`Editor`](xref:Xamarin.Forms.Editor) entrada de texto e duas [`Button`](xref:Xamarin.Forms.Button) instâncias que direcionam o aplicativo para salvar ou excluir um arquivo. As duas `Button` instâncias são dispostas horizontalmente em um [`Grid`](xref:Xamarin.Forms.Grid) , com o `Label` , `Editor` e `Grid` que estão sendo dispostos verticalmente em um [`StackLayout`](xref:Xamarin.Forms.StackLayout) . Para obter mais informações sobre como criar a interface do usuário, consulte [interface do usuário](deepdive.md#user-interface) no guia de [ Xamarin.Forms início rápido](deepdive.md)aprofundamento.

    Salve as alterações em **MainPage.xaml** ao escolher **Arquivo > Salvar** (ou ao pressionar **& #8984; + S**) e feche o arquivo.

7. No **Painel de Soluções**, no projeto **Notas**, expanda **MainPage.xaml** e clique duas vezes em **MainPage.xaml.cs** para abri-lo:

    ![MainPage.xaml.cs](single-page-images/vsmac/mainpage-xaml-cs.png)

8. No **MainPage.XAML.cs**, remova todo o código do modelo e substitua-o pelo código a seguir:

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

    Esse código define um campo `_fileName`, que referencia um arquivo chamado `notes.txt` que armazenará os dados de notas na pasta de dados local do aplicativo. Quando o construtor da página é executado, o arquivo é lido, se existir, e exibido no [`Editor`](xref:Xamarin.Forms.Editor) . Quando o **salvamento** [`Button`](xref:Xamarin.Forms.Button) é pressionado `OnSaveButtonClicked` , o manipulador de eventos é executado, o que salva o conteúdo do no `Editor` arquivo. Quando o **Excluir** `Button` for pressionado, o manipulador de eventos `OnDeleteButtonClicked` será executado, excluindo o arquivo, caso ele exista, e removendo todo o texto do `Editor`. Para obter mais informações sobre a interação do usuário, consulte [respondendo à interação do usuário](deepdive.md#responding-to-user-interaction) no guia de [ Xamarin.Forms início rápido](deepdive.md)aprofundamento.

    Salve as alterações em **MainPage.xaml.cs** ao escolher **Arquivo > Salvar** (ou ao pressionar **& #8984; + S**) e feche o arquivo.

### <a name="building-the-quickstart"></a>Criando o Início Rápido

1. No Visual Studio para Mac, selecione o item de menu **Compilar > Compilar Tudo** (ou pressione **&#8984; + B**). Os projetos serão criados e uma mensagem de êxito será exibida na barra de ferramentas do Visual Studio para Mac.

      ![Build bem-sucedido](single-page-images/vsmac/build-successful.png)

    Se houver erros, repita as etapas anteriores e corrija-os até que os projetos sejam compilados com êxito.

2. No **Painel de Soluções**, selecione o projeto **Notes.iOS**, clique com o botão direito do mouse e selecione **Definir como projeto de inicialização**:

      ![Definir o iOS como Projeto Inicial](single-page-images/vsmac/set-startup-project-ios.png)

3. Na barra de ferramentas do Visual Studio para Mac, pressione o botão **Iniciar** (o botão triangular que se parece com o botão Reproduzir) para iniciar o aplicativo no Simulador do iOS escolhido:

      ![Barra de ferramentas do Visual Studio para Mac](single-page-images/vsmac/start.png)

      [![Observações no Simulador do iOS](single-page-images/vsmac/notes-ios.png)](single-page-images/vsmac/notes-ios-large.png#lightbox "Observações no Simulador do iOS")

    Insira uma nota e pressione o botão **Salvar**.

    Para obter mais informações sobre como o aplicativo é iniciado em cada plataforma, consulte [iniciando o aplicativo em cada plataforma](deepdive.md#launching-the-application-on-each-platform) no guia de [ Xamarin.Forms início rápido](deepdive.md)aprofundamento.

4. No **Painel de Soluções**, selecione o projeto **Notes.Droid**, clique com o botão direito do mouse e selecione **Definir como projeto de inicialização**:

      ![Definir Android como projeto de inicialização](single-page-images/vsmac/set-startup-project-android.png)

5. Na barra de ferramentas do Visual Studio para Mac, pressione o botão **Iniciar** (o botão triangular que se parece com o botão Reproduzir) para iniciar o aplicativo em seu emulador Android escolhido:

      [![Observações no Android Emulator](single-page-images/vsmac/notes-android.png)](single-page-images/vsmac/notes-android-large.png#lightbox "Observações no Simulador de Android")

    Insira uma nota e pressione o botão **Salvar**.

    Para obter mais informações sobre como o aplicativo é iniciado em cada plataforma, consulte [iniciando o aplicativo em cada plataforma](deepdive.md#launching-the-application-on-each-platform) no guia de [ Xamarin.Forms início rápido](deepdive.md)aprofundamento.

::: zone-end

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você aprendeu a:

- Crie um aplicativo de plataforma cruzada Xamarin.Forms .
- Definir a interface do usuário para uma página usando a linguagem XAML.
- Interagir com elementos da interface do usuário XAML do código.

Para transformar esse aplicativo de página única em um aplicativo de várias páginas, vá para o próximo início rápido.

> [!div class="nextstepaction"]
> [Próximo](multi-page.md)

## <a name="related-links"></a>Links relacionados

- [Notas (exemplo)](/samples/xamarin/xamarin-forms-samples/getstarted-notes-singlepage/)
- [Xamarin.Forms Aprofundamento do guia de início rápido](deepdive.md)