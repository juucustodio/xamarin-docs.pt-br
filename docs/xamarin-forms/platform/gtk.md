---
title: 'Instalação de plataforma do GTK #'
description: 'Xamarin. Forms agora tem suporte de visualização para a plataforma do GTK #'
ms.prod: xamarin
ms.assetid: 3417FB95-3E4B-47DA-85D0-F34832747236
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/10/2018
ms.openlocfilehash: 132f31f4c9426dfceeef38f464231d1545f4db50
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50115166"
---
# <a name="gtk-platform-setup"></a>Instalação de plataforma do GTK #

![Visualizar](~/media/shared/preview.png)

Xamarin. Forms agora tem suporte para aplicativos do GTK # visualização. O GTK # é um kit de ferramentas de interface gráfica do usuário que vincula o Kit de ferramentas GTK + e uma variedade de bibliotecas GNOME, permitindo o desenvolvimento de GNOME totalmente nativo aplicativos de gráficos usando o Mono e .NET. Este artigo demonstra como adicionar um projeto GTK # a uma solução do xamarin. Forms.

Antes de começar, crie uma nova solução xamarin. Forms ou usar uma solução existente do xamarin. Forms, por exemplo, [ **GameOfLife**](https://developer.xamarin.com/samples/xamarin-forms/BoxView/GameOfLife/).

> [!NOTE]
> Embora este artigo se concentra na adição de um aplicativo do GTK # a uma solução do xamarin. Forms no VS2017 e o Visual Studio para Mac, ele também pode ser executado no [MonoDevelop](http://www.monodevelop.com/) para Linux.

## <a name="adding-a-gtk-app"></a>Adicionar um aplicativo do GTK #

O GTK # para macOS e Linux é instalado como parte do [Mono](http://www.mono-project.com/download/stable/). O GTK # para .NET pode ser instalado no Windows com o [instalador do GTK #](http://www.mono-project.com/download/stable/#download-win).

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Siga estas instruções para adicionar um aplicativo do GTK # que será executado na área de trabalho do Windows:

1. No Visual Studio 2017, clique com botão direito no nome da solução no **Gerenciador de soluções** e escolha **Adicionar > Novo projeto...** .

2. No **novo projeto** janela, à esquerda, selecione **Visual c#** e **área de trabalho clássica do Windows**. Na lista de tipos de projeto, escolha **biblioteca de classes (.NET Framework)** e certifique-se de que o **Framework** suspensa é definida como um mínimo de .NET Framework 4.7.

3. Digite um nome para o projeto com um **GTK** extensão, por exemplo **GameOfLife.GTK**. Clique no **navegue** botão, selecione a pasta que contém a plataforma de outra projetos e pressione **Selecionar pasta**. Isso colocará o projeto GTK no mesmo diretório que os outros projetos na solução.

    ![Adicionar um novo projeto GTK](gtk-images/win/add-new-project.png "adicionar um novo projeto GTK")

    Pressione a **Okey** botão para criar o projeto.

4. No **Gerenciador de soluções**, com o botão direito do mouse no projeto GTK novo e selecione **Manage NuGet Packages**. Selecione o **navegue** guia e pesquise **xamarin. Forms** 3.0 ou superior.

    ![Selecione o pacote NuGet do xamarin. Forms](gtk-images/win/select-forms-nuget-package.png ", selecione o pacote do NuGet xamarin. Forms")

    Selecione o pacote e clique no **instalar** botão.

5. Agora pesquise a **Xamarin.Forms.Platform.GTK** pacote 3.0 ou superior.

    ![Selecione o pacote Xamarin.Forms.Platform.GTK NuGet](gtk-images/win/select-forms-platform-nuget-package.png "selecione o pacote Xamarin.Forms.Platform.GTK NuGet")

    Selecione o pacote e clique no **instalar** botão.

6. No **Gerenciador de soluções**, clique com botão direito no nome da solução e selecione **gerenciar pacotes NuGet para solução**. Selecione o **atualização** guia e o **xamarin. Forms** pacote. Selecione todos os projetos e atualizá-los para a mesma versão do xamarin. Forms usado pelo projeto GTK.

7. No **Gerenciador de soluções**, clique duas vezes em **referências** no projeto GTK. No **Gerenciador de referências** caixa de diálogo, selecione **projetos** na esquerda e verifique a caixa de seleção adjacente ao projeto .NET Standard ou compartilhada:

    ![Referência ao projeto compartilhado](gtk-images/win/reference-shared-project.png "referência ao projeto compartilhado")

8. No **Gerenciador de referências** caixa de diálogo, pressione a **procurar** botão e navegue até o **C:\Program Files (x86)\GtkSharp\2.12\lib** pasta e selecione o  **ATK sharp.dll**, **gdk sharp.dll**, **glade sharp.dll**, **glib sharp.dll**, **gtk-dotnet.dll**, **sharp.dll gtk** arquivos.

    ![Fazer referência a bibliotecas do GTK #](gtk-images/win/reference-gtk-libraries.png "referencie as bibliotecas GTK #")

    Pressione a **Okey** botão para adicionar as referências.

9. No projeto GTK, renomeie **Class1.cs** à **Program.cs**.

10. No projeto GTK, edite o **Program.cs** arquivo para que ele fique parecido com o código a seguir:

    ```csharp
    using System;
    using Xamarin.Forms;
    using Xamarin.Forms.Platform.GTK;

    namespace GameOfLife.GTK
    {
        class MainClass
        {
            [STAThread]
            public static void Main(string[] args)
            {
                Gtk.Application.Init();
                Forms.Init();

                var app = new App();
                var window = new FormsWindow();
                window.LoadApplication(app);
                window.SetApplicationTitle("Game of Life");
                window.Show();

                Gtk.Application.Run();
            }
        }
    }
    ```

    Esse código inicializa o GTK # e xamarin. Forms, cria uma janela do aplicativo e executa o aplicativo.

11. No **Gerenciador de soluções**, com o botão direito do mouse no projeto GTK e selecione **propriedades**.

12. No **propriedades** janela, selecione a **aplicativo** guia e altere a **tipo de saída** lista suspensa para **aplicativo Windows**.

    ![Alterar o tipo de saída do projeto](gtk-images/win/change-project-output-type.png "alterar o tipo de saída do projeto")

13. No **Gerenciador de soluções**, o projeto WPF com o botão direito e selecione **definir como projeto de inicialização**. Pressione F5 para executar o programa com o depurador do Visual Studio, na área de trabalho do Windows:

    ![O GTK # jogo da vida útil](gtk-images/win/gtk-gameoflife.png "GTK # jogo da vida útil")

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Siga estas instruções para adicionar um aplicativo do GTK # que será executado na área de trabalho Mac:

1. No Visual Studio para Mac, clique com botão direito na solução do xamarin. Forms e escolha **Adicionar > Adicionar novo projeto...** .

2. No **novo projeto** janela escolher **outros > .NET > Gtk # 2.0 projeto** e pressione **próxima**.

3. Digite um nome para o projeto com um **GTK** extensão, por exemplo **GameOfLife.GTK**e pressione **criar**.

4. No **painel de soluções**, clique duas vezes em **pacotes > Adicionar pacotes...**  para o GTK de projeto e adicionar o pacote de NuGet de pré-lançamento do xamarin. Forms 3.0 ou superior.

    ![Selecione o pacote NuGet do xamarin. Forms](gtk-images/mac/select-forms-nuget-package.png ", selecione o pacote do NuGet xamarin. Forms")

5. No **painel de soluções**, clique duas vezes em **pacotes > Adicionar pacotes...**  para o GTK de projeto e adicione o pacote do NuGet de pré-lançamento Xamarin.Forms.Platform.GTK 3.0 ou superior.

    ![Selecione o pacote Xamarin.Forms.Platform.GTK NuGet](gtk-images/mac/select-forms-platform-nuget-package.png "selecione o pacote Xamarin.Forms.Platform.GTK NuGet")

6. Atualize os outros projetos de plataforma para usar a mesma versão do xamarin. Forms como usado pelo projeto GTK.

7. No **painel de soluções**, clique duas vezes em **referências > Editar referências...**  para o GTK de projeto e adicione uma referência ao projeto xamarin. Forms (.NET Standard ou projeto compartilhado).

    ![Referência ao projeto compartilhado](gtk-images/mac/reference-shared-project.png "referência ao projeto compartilhado")

8. Editar o **Program.cs** arquivo do projeto GTK para que ele se parece com o código a seguir:

    ```csharp
    using System;
    using Xamarin.Forms;
    using Xamarin.Forms.Platform.GTK;

    namespace GameOfLife.GTK
    {
        class MainClass
        {
            [STAThread]
            public static void Main(string[] args)
            {
                Gtk.Application.Init();
                Forms.Init();

                var app = new App();
                var window = new FormsWindow();
                window.LoadApplication(app);
                window.SetApplicationTitle("Game of Life");
                window.Show();

                Gtk.Application.Run();
            }
        }
    }
    ```

    Esse código inicializa o GTK # e xamarin. Forms, cria uma janela do aplicativo e executa o aplicativo.

9. No **painel de soluções**, clique com botão direito no projeto GTK e selecione **definir como projeto de inicialização**.

10. No Visual Studio para a barra de ferramentas do Mac, pressione a **iniciar** botão (o botão triangular que se parece com o botão Reproduzir) para iniciar o aplicativo.

    ![O GTK # jogo da vida útil](gtk-images/mac/gtk-gameoflife.png "GTK # jogo da vida útil")

-----

## <a name="next-steps"></a>Próximas etapas

### <a name="platform-specifics"></a>Especificidades da plataforma

Você pode determinar em qual plataforma está em execução no seu aplicativo xamarin. Forms de XAML ou código. Isso permite que você altere as características do programa quando ele é executado em GTK #. No código, comparar o valor de `Device.RuntimePlatform` com o `Device.GTK` constante (que é igual a cadeia de caracteres "GTK"). Se houver uma correspondência, o aplicativo está em execução em GTK #.

No XAML, você pode usar o `OnPlatform` marca para selecionar um valor de propriedade específico da plataforma:

```xaml
<Button.TextColor>
    <OnPlatform x:TypeArguments="Color">
        <On Platform="iOS" Value="White" />
        <On Platform="macOS" Value="White" />
        <On Platform="Android" Value="Black" />
        <On Platform="GTK" Value="Blue" />
    </OnPlatform>
</Button.TextColor>
```

### <a name="application-icon"></a>Ícone do Aplicativo

Você pode definir o ícone do aplicativo na inicialização:

```csharp
window.SetApplicationIcon("icon.png");
```

### <a name="themes"></a>Temas

Há uma grande variedade de temas disponíveis para GTK #, e eles podem ser usados em um aplicativo xamarin. Forms:

```csharp
GtkThemes.Init ();
GtkThemes.LoadCustomTheme ("Themes/gtkrc");
```

### <a name="native-forms"></a>Formulários nativos

Formulários nativos permite que o xamarin. Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-derivado de páginas a ser consumido por projetos nativos, incluindo projetos GTK #. Isso pode ser feito criando uma instância das [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-derivado de página e convertê-lo para o nativo GTK # tipo usando o `CreateContainer` método de extensão:

```csharp
var settingsView = new SettingsView().CreateContainer();
vbox.PackEnd(settingsView, true, true, 0);
```

Para obter mais informações sobre formulários nativos, consulte [formulários nativos](~/xamarin-forms/platform/native-forms.md).

## <a name="issues"></a>Problemas

Esta é uma visualização, portanto, você deve esperar que nem tudo está pronto para produção. O status atual de implementação, consulte [Status](https://github.com/jsuarezruiz/forms-gtk-progress/blob/master/Status.md)e para os problemas conhecidos atuais, consulte [problemas conhecidos e pendentes](https://github.com/jsuarezruiz/forms-gtk-progress/blob/master/Issues-Pending.md).
