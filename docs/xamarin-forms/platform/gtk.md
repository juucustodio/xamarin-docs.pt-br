---
title: 'Instalação da plataforma GTK #'
description: 'Xamarin. Forms agora tem suporte para a plataforma GTK # visualização'
ms.prod: xamarin
ms.assetid: 3417FB95-3E4B-47DA-85D0-F34832747236
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/10/2018
ms.openlocfilehash: 7f68b7c8affc11b50bdb4a2fc9589f8dcbfb45ec
ms.sourcegitcommit: 7a89735aed9ddf89c855fd33928915d72da40c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36209184"
---
# <a name="gtk-platform-setup"></a>Instalação da plataforma GTK #

![Visualizar](~/media/shared/preview.png)

Xamarin. Forms agora tem suporte para aplicativos GTK # visualização. GTK # é um kit de ferramentas de interface gráfica do usuário que vincula o Kit de ferramentas GTK + e uma variedade de bibliotecas GNOME, permitindo que o desenvolvimento de aplicativos de gráficos GNONE totalmente nativos usando Mono e .NET. Este artigo demonstra como adicionar um projeto GTK # a uma solução xamarin. Forms.

Antes de iniciar, crie uma nova solução xamarin. Forms ou usar uma solução existente do xamarin. Forms, por exemplo, [ **GameOfLife**](https://developer.xamarin.com/samples/xamarin-forms/BoxView/GameOfLife/).

> [!NOTE]
> Enquanto este artigo se concentra na adição de um aplicativo GTK # para uma solução de xamarin. Forms em VS2017 e o Visual Studio para Mac, ele também pode ser executado no [MonoDevelop](http://www.monodevelop.com/) para Linux.

## <a name="adding-a-gtk-app"></a>Adicionar um aplicativo GTK #

GTK # macOS e Linux é instalado como parte do [Mono](http://www.mono-project.com/download/stable/). GTK # para .NET pode ser instalado no Windows com o [GTK # instalador](http://www.mono-project.com/download/stable/#download-win).

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Siga estas instruções para adicionar um aplicativo GTK # que será executado na área de trabalho do Windows:

1. No Visual Studio de 2017, clique no nome da solução em **Solution Explorer** e escolha **Adicionar > Novo projeto...** .

2. No **novo projeto** janela, selecione esquerda **Visual C#** e **área de trabalho clássica do Windows**. Na lista de tipos de projeto, escolha **biblioteca de classes (.NET Framework)** e certifique-se de que o **Framework** suspenso é definido como um mínimo de 4.7 do .NET Framework.

3. Digite um nome para o projeto com uma **GTK** extensão, por exemplo **GameOfLife.GTK**. Clique o **procurar** botão, selecione a pasta que contém a plataforma de outros projetos e pressione **Selecionar pasta**. Isso colocará o projeto GTK no mesmo diretório que os outros projetos na solução.

    ![Adicionar um novo projeto GTK](gtk-images/win/add-new-project.png "adicionar um novo projeto GTK")

    Pressione a **Okey** botão para criar o projeto.

4. No **Solution Explorer**, clique com botão direito no novo projeto GTK e selecione **gerenciar pacotes NuGet**. Selecione o **procurar** guia e procure **xamarin. Forms** 3.0 ou superior.

    ![Selecione o pacote NuGet do xamarin. Forms](gtk-images/win/select-forms-nuget-package.png ", selecione o pacote NuGet do xamarin. Forms")

    Selecione o pacote e clique no **instalar** botão.

5. Procurar o **Xamarin.Forms.Platform.GTK** pacote 3.0 ou superior.

    ![Selecione o pacote Xamarin.Forms.Platform.GTK NuGet](gtk-images/win/select-forms-platform-nuget-package.png ", selecione o pacote do Xamarin.Forms.Platform.GTK NuGet")

    Selecione o pacote e clique no **instalar** botão.

6. No **Solution Explorer**, com o botão direito no nome da solução e selecione **gerenciar pacotes NuGet para solução**. Selecione o **atualização** guia e o **xamarin. Forms** pacote. Selecione todos os projetos e atualizá-los para a mesma versão do xamarin. Forms usado pelo projeto GTK.

7. No **Solution Explorer**, clique duas vezes em **referências** no projeto GTK. No **Gerenciador de referências** caixa de diálogo, selecione **projetos** à esquerda e verifique a caixa de seleção adjacente ao projeto compartilhado ou padrão do .NET:

    ![Referência ao projeto compartilhado](gtk-images/win/reference-shared-project.png "referência ao projeto compartilhado")

8. No **Gerenciador de referências** caixa de diálogo, pressione a **procurar** botão e navegue até o **C:\Program Files (x86)\GtkSharp\2.12\lib** pasta e selecione o  **ATK sharp.dll**, **gdk sharp.dll**, **glade sharp.dll**, **glib sharp.dll**, **gtk-dotnet.dll**, **sharp.dll gtk** arquivos.

    ![Fazer referência as bibliotecas GTK #](gtk-images/win/reference-gtk-libraries.png "fazem referência as bibliotecas GTK #")

    Pressione a **Okey** botão para adicionar as referências.

9. No projeto GTK, renomeie **Class1.cs** para **Program.cs**.

10. No projeto GTK, edite o **Program.cs** arquivos de forma que ele se parece com o código a seguir:

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

    Esse código inicializa GTK # e xamarin. Forms, cria uma janela de aplicativo e executa o aplicativo.

11. No **Solution Explorer**, clique com botão direito no projeto GTK e selecione **propriedades**.

12. No **propriedades** janela, selecione o **aplicativo** guia e altere o **tipo de saída** lista suspensa para **aplicativo do Windows**.

    ![Alterar o tipo de saída do projeto](gtk-images/win/change-project-output-type.png "alterar o tipo de saída do projeto")

13. No **Solution Explorer**, o projeto e selecione **definir como projeto de inicialização**. Pressione F5 para executar o programa com o depurador do Visual Studio, na área de trabalho do Windows:

    ![GTK # jogo da vida útil](gtk-images/win/gtk-gameoflife.png "GTK # jogo da vida útil")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Siga estas instruções para adicionar um aplicativo GTK # que será executado na área de trabalho de Mac:

1. No Visual Studio para Mac, clique com botão direito na solução xamarin. Forms e escolha **Adicionar > Adicionar novo projeto...** .

2. No **novo projeto** janela escolher **outros > .NET > Gtk # 2.0 projeto** e pressione **próximo**.

3. Digite um nome para o projeto com uma **GTK** extensão, por exemplo **GameOfLife.GTK**e pressione **criar**.

4. No **solução preenchimento**, com o botão direito em **pacotes > Adicionar pacotes de...**  para o GTK do projeto e adicionar o pacote de NuGet de pré-lançamento do xamarin. Forms 3.0 ou superior.

    ![Selecione o pacote NuGet do xamarin. Forms](gtk-images/mac/select-forms-nuget-package.png ", selecione o pacote NuGet do xamarin. Forms")

5. No **solução preenchimento**, com o botão direito em **pacotes > Adicionar pacotes de...**  para o GTK do projeto e adicionar o pacote de NuGet de pré-lançamento Xamarin.Forms.Platform.GTK 3.0 ou superior.

    ![Selecione o pacote Xamarin.Forms.Platform.GTK NuGet](gtk-images/mac/select-forms-platform-nuget-package.png ", selecione o pacote do Xamarin.Forms.Platform.GTK NuGet")

6. Atualize os outros projetos de plataforma para usar a mesma versão do xamarin. Forms usada pelo projeto GTK.

7. No **solução preenchimento**, com o botão direito em **referências > Editar referências...**  para o GTK do projeto e adicione uma referência ao projeto xamarin. Forms (.NET padrão ou projeto compartilhado).

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

    Esse código inicializa GTK # e xamarin. Forms, cria uma janela de aplicativo e executa o aplicativo.

9. No **solução preenchimento**, com o botão direito no projeto GTK e selecione **definir como projeto de inicialização**.

10. No Visual Studio para barra de ferramentas do Mac, pressione a **iniciar** botão (o botão triangular que se parece com um botão Play) para iniciar o aplicativo.

    ![GTK # jogo da vida útil](gtk-images/mac/gtk-gameoflife.png "GTK # jogo da vida útil")

-----

## <a name="next-steps"></a>Próximas etapas

### <a name="platform-specifics"></a>Especificações da plataforma

Você pode determinar qual plataforma está em execução no seu aplicativo xamarin. Forms de código ou XAML. Isso permite que você altere as características de programa quando ele é executado em GTK #. No código, comparar o valor de `Device.RuntimePlatform` com o `Device.GTK` constante (que é igual a cadeia de caracteres "GTK"). Se houver uma correspondência, o aplicativo está em execução GTK #.

Em XAML, você pode usar o `OnPlatform` marca para selecionar um valor de propriedade específico da plataforma:

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

Você pode definir o ícone do aplicativo durante a inicialização:

```csharp
window.SetApplicationIcon("icon.png");
```

### <a name="themes"></a>Temas

Há uma ampla variedade de temas disponíveis para GTK #, e eles podem ser usados de um aplicativo xamarin. Forms:

```csharp
GtkThemes.Init ();
GtkThemes.LoadCustomTheme ("Themes/gtkrc");
```

### <a name="native-forms"></a>Formulários nativo

Formulários nativo permite xamarin. Forms [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-derivado páginas a serem consumidos por projetos nativos, inclusive GTK # projetos. Isso pode ser feito por meio da criação de uma instância do [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-derivado de página e convertê-lo para o nativo GTK # tipo usando o `CreateContainer` método de extensão:

```csharp
var settingsView = new SettingsView().CreateContainer();
vbox.PackEnd(settingsView, true, true, 0);
```

Para obter mais informações sobre os formulários nativo, consulte [formulários nativo](~/xamarin-forms/platform/native-forms.md).

## <a name="issues"></a>Problemas

Esta é uma visualização, portanto você deve esperar que nem tudo é preparado para produção. O status atual de implementação, consulte [Status](https://github.com/jsuarezruiz/forms-gtk-progress/blob/master/Status.md)e para os problemas conhecidos atuais, consulte [problemas conhecidos & pendentes](https://github.com/jsuarezruiz/forms-gtk-progress/blob/master/Issues-Pending.md).
