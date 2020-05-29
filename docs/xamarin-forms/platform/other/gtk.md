---
title: "configuração de plataforma GTK #" Descrição: " Xamarin.Forms agora tem suporte de visualização para a plataforma GTK #" MS. Prod: xamarin MS. AssetID: 3417FB95-3E4B-47DA-85D0-F34832747236 MS. Technology: xamarin-Forms autor: davidbritch MS. Author: dabritch MS. Date: 04/10/2018 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="gtk-platform-setup"></a>Configuração da plataforma GTK #

![Versão Prévia](~/media/shared/preview.png)

Xamarin.FormsAgora tem suporte de visualização para aplicativos GTK #. O GTK # é um kit de ferramentas de interface gráfica do usuário que vincula o GTK + Toolkit e uma variedade de bibliotecas GNOME, permitindo o desenvolvimento de aplicativos gráficos GNOME totalmente nativos usando mono e .NET. Este artigo demonstra como adicionar um projeto GTK # a uma Xamarin.Forms solução.

> [!IMPORTANT]
> Xamarin.Formso suporte para GTK # é fornecido pela Comunidade. Para obter mais informações, consulte [ Xamarin.Forms suporte a plataformas](https://github.com/xamarin/Xamarin.Forms/wiki/Platform-Support).

Antes de começar, crie uma nova Xamarin.Forms solução ou use uma solução existente Xamarin.Forms , por exemplo, [**GameOfLife**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-gameoflife).

> [!NOTE]
> Embora este artigo se concentre em Adicionar um aplicativo GTK # a uma Xamarin.Forms solução no VS2017 e Visual Studio para Mac, ele também pode ser executado em [MonoDevelop](https://www.monodevelop.com/) para Linux.

## <a name="adding-a-gtk-app"></a>Adicionando um aplicativo GTK #

O GTK # para macOS e Linux é instalado como parte do [mono](https://www.mono-project.com/download/stable/). O GTK # para .NET pode ser instalado no Windows com o [instalador do GTK #](https://www.mono-project.com/download/stable/#download-win).

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Siga estas instruções para adicionar um aplicativo GTK # que será executado na área de trabalho do Windows:

1. No Visual Studio 2019, clique com o botão direito do mouse no nome da solução em **Gerenciador de soluções** e escolha **Adicionar > novo projeto...**.

2. Na janela **novo projeto** , à esquerda, selecione **Visual C#** e **área de trabalho clássica do Windows**. Na lista de tipos de projeto, escolha **biblioteca de classes (.NET Framework)** e verifique se a lista suspensa **estrutura** está definida como um mínimo de .NET Framework 4,7.

3. Digite um nome para o projeto com uma extensão **GTK** , por exemplo, **GameOfLife. GTK**. Clique no botão **procurar** , selecione a pasta que contém os outros projetos da plataforma e pressione **Selecionar pasta**. Isso colocará o projeto do GTK no mesmo diretório que os outros projetos na solução.

    ![Adicionar um novo projeto GTK](gtk-images/win/add-new-project.png "Adicionar um novo projeto GTK")

    Pressione o botão **OK** para criar o projeto.

4. Na **Gerenciador de soluções**, clique com o botão direito do mouse no novo projeto GTK e selecione **gerenciar pacotes NuGet**. Selecione a guia **procurar** e procure por **Xamarin.Forms** 3,0 ou superior.

    ![Selecione o Xamarin.Forms pacote NuGet](gtk-images/win/select-forms-nuget-package.png "Selecione o [! Parar. Pacote NuGet não LOC (Xamarin. Forms)]")

    Selecione o pacote e clique no botão **instalar** .

5. Agora, pesquise por ** Xamarin.Forms . Pacote do Platform. GTK** 3,0 ou superior.

    ![Selecione o Xamarin.Forms . Pacote NuGet do Platform. GTK](gtk-images/win/select-forms-platform-nuget-package.png "Selecione o [! Parar. Não-LOC (Xamarin. Forms)]. Pacote NuGet do Platform. GTK")

    Selecione o pacote e clique no botão **instalar** .

6. Na **Gerenciador de soluções**, clique com o botão direito do mouse no nome da solução e selecione **gerenciar pacotes NuGet para solução**. Selecione a guia **Atualizar** e o **Xamarin.Forms** pacote. Selecione todos os projetos e atualize-os para a mesma Xamarin.Forms versão usada pelo projeto GTK.

7. Na **Gerenciador de soluções**, clique com o botão direito do mouse em **referências** no projeto GTK. Na caixa de diálogo **Gerenciador de referências** , selecione **projetos** à esquerda e marque a caixa de seleção adjacente ao .net Standard ou ao projeto compartilhado:

    ![Referenciar o projeto compartilhado](gtk-images/win/reference-shared-project.png "Referenciar o projeto compartilhado")

8. Na caixa de diálogo **Gerenciador de referências** , pressione o botão **procurar** e navegue até a pasta **C:\Program Files (x86) \GtkSharp\2.12\lib** e selecione os arquivos **ATK-Sharp. dll**, **GDK-Sharp. dll**, **Glade-Sharp.** dll, **GLib-Sharp**. dll, GTK-dotnet. **dll**, GTK-Sharp **.** dll.

    ![Referenciar as bibliotecas do GTK #](gtk-images/win/reference-gtk-libraries.png "Referenciar as bibliotecas do GTK #")

    Pressione o botão **OK** para adicionar as referências.

9. No projeto do GTK, renomeie **Class1.cs** para **Program.cs**.

10. No projeto do GTK, edite o arquivo **Program.cs** para que ele se assemelhe ao seguinte código:

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

    Esse código inicializa o GTK # e Xamarin.Forms o cria uma janela de aplicativo e executa o aplicativo.

11. Na **Gerenciador de soluções**, clique com o botão direito do mouse no projeto GTK e selecione **Propriedades**.

12. Na janela **Propriedades** , selecione a guia **aplicativo** e altere a lista suspensa **tipo de saída** para aplicativo do **Windows**.

    ![Alterar o tipo de saída do projeto](gtk-images/win/change-project-output-type.png "Alterar o tipo de saída do projeto")

13. Na **Gerenciador de soluções**, clique com o botão direito do mouse no projeto GTK e selecione **definir como projeto de inicialização**. Pressione F5 para executar o programa com o depurador do Visual Studio na área de trabalho do Windows:

    ![Jogo de vida do GTK #](gtk-images/win/gtk-gameoflife.png "Jogo de vida do GTK #")

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Siga estas instruções para adicionar um aplicativo GTK # que será executado no Mac desktop:

1. Em Visual Studio para Mac, clique com o botão direito do mouse na Xamarin.Forms solução e escolha **Adicionar > adicionar novo projeto...**.

2. Na janela **novo projeto** , escolha **outro projeto > .net > GTK # 2,0** e pressione **Avançar**.

3. Digite um nome para o projeto com uma extensão **GTK** , por exemplo, **GameOfLife. GTK**, e pressione **criar**.

4. Na **painel de soluções**, clique com o botão direito do mouse em **pacotes > adicionar pacotes...** para o projeto GTK e adicione o Xamarin.Forms pacote NuGet de pré-lançamento 3,0 ou superior.

    ![Selecione o Xamarin.Forms pacote NuGet](gtk-images/mac/select-forms-nuget-package.png "Selecione o [! Parar. Pacote NuGet não LOC (Xamarin. Forms)]")

5. Na **painel de soluções**, clique com o botão direito do mouse em **pacotes > adicionar pacotes...** para o projeto GTK e adicione o Xamarin.Forms . Platform. GTK 3,0 pacote NuGet de pré-lançamento ou superior.

    ![Selecione o Xamarin.Forms . Pacote NuGet do Platform. GTK](gtk-images/mac/select-forms-platform-nuget-package.png "Selecione o [! Parar. Não-LOC (Xamarin. Forms)]. Pacote NuGet do Platform. GTK")

6. Atualize os outros projetos da plataforma para usar a mesma Xamarin.Forms versão usada pelo projeto GTK.

7. Na **painel de soluções**, clique com o botão direito do mouse em **referências > editar referências...** para o projeto GTK e adicione uma referência ao Xamarin.Forms projeto (.net Standard ou projeto compartilhado).

    ![Referenciar o projeto compartilhado](gtk-images/mac/reference-shared-project.png "Referenciar o projeto compartilhado")

8. Edite o arquivo **Program.cs** do projeto GTK para que seja semelhante ao seguinte código:

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

    Esse código inicializa o GTK # e Xamarin.Forms o cria uma janela de aplicativo e executa o aplicativo.

9. Na **painel de soluções**, clique com o botão direito do mouse no projeto GTK e selecione **definir como projeto de inicialização**.

10. Na barra de ferramentas Visual Studio para Mac, pressione o botão **Iniciar** (o botão triangular que se assemelha a um botão de reprodução) para iniciar o aplicativo.

    ![Jogo de vida do GTK #](gtk-images/mac/gtk-gameoflife.png "Jogo de vida do GTK #")

-----

## <a name="next-steps"></a>Próximas etapas

### <a name="platform-specifics"></a>Especificações da plataforma

Você pode determinar em qual plataforma seu Xamarin.Forms aplicativo está em execução por meio de XAML ou código. Isso permite que você altere as características do programa quando ele está em execução no GTK #. No código, compare o valor de `Device.RuntimePlatform` com a `Device.GTK` constante (que é igual à cadeia de caracteres "GTK"). Se houver uma correspondência, o aplicativo será executado em GTK #.

Em XAML, você pode usar a `OnPlatform` marca para selecionar um valor de propriedade específico para a plataforma:

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

Há uma ampla variedade de temas disponíveis para o GTK # e eles podem ser usados em um Xamarin.Forms aplicativo:

```csharp
GtkThemes.Init ();
GtkThemes.LoadCustomTheme ("Themes/gtkrc");
```

### <a name="native-forms"></a>Formulários nativos

Formulários nativos permitem que Xamarin.Forms [`ContentPage`](xref:Xamarin.Forms.ContentPage) páginas derivadas sejam consumidas por projetos nativos, incluindo projetos GTK #. Isso pode ser feito criando uma instância da [`ContentPage`](xref:Xamarin.Forms.ContentPage) página derivada e convertendo-a para o tipo nativo do GTK # usando o `CreateContainer` método de extensão:

```csharp
var settingsView = new SettingsView().CreateContainer();
vbox.PackEnd(settingsView, true, true, 0);
```

Para obter mais informações sobre formulários nativos, consulte [formulários nativos](~/xamarin-forms/platform/native-forms.md).

## <a name="issues"></a>Problemas

Esta é uma visualização, portanto, você deve esperar que nem tudo está pronto para produção. Para obter o status de implementação atual, consulte [status](https://github.com/jsuarezruiz/forms-gtk-progress/blob/master/Status.md)e para obter os problemas conhecidos atuais, consulte [pendentes & problemas conhecidos](https://github.com/jsuarezruiz/forms-gtk-progress/blob/master/Issues-Pending.md).
