---
Título: "configurar projetos do Windows" Descrição: " Xamarin.Forms soluções mais antigas (ou aquelas criadas no MacOS) não terão projetos de plataforma universal do Windows e, portanto, este artigo explica como adicionar um novo projeto UWP a uma Xamarin.Forms solução existente."
MS. Prod: xamarin MS. AssetID: A0774D2E-6994-4D91-84E8-DAB66FC92320 MS. Technology: xamarin-Forms autor: davidbritch MS. Author: dabritch MS. Date: 04/10/2018 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="setup-windows-projects"></a>Configurar projetos do Windows

_Adicionando novos projetos do Windows a uma Xamarin.Forms solução existente_

Xamarin.FormsAs soluções mais antigas (ou aquelas criadas no MacOS) não terão projetos de aplicativo plataforma universal do Windows (UWP). Portanto, você precisará adicionar manualmente um projeto UWP para compilar um aplicativo do Windows 10 (UWP).

## <a name="add-a-universal-windows-platform-app"></a>Adicionar um aplicativo Plataforma Universal do Windows

O **Visual Studio 2019** no **Windows 10** é recomendado para criar aplicativos UWP. Para obter mais informações sobre o Plataforma Universal do Windows, consulte [introdução à plataforma universal do Windows](/windows/uwp/get-started/universal-application-platform-guide/).

O UWP está disponível no Xamarin.Forms 2,1 e posterior e no Xamarin.Forms . Há suporte para mapas no Xamarin.Forms 2,2 e posterior.

Confira a seção de <a href="#troubleshooting">solução de problemas</a> para obter dicas úteis.

Siga estas instruções para adicionar um aplicativo UWP que será executado em telefones, tablets e desktops do Windows 10:

 uma. Clique com o botão direito do mouse na solução e selecione **adicionar > novo projeto...** e adicione um projeto de **aplicativo em branco (universal do Windows)** :

  ![](universal-images/add-wu.png "Add New Project Dialog")

 2. Na caixa de diálogo **novo projeto plataforma universal do Windows** , selecione as versões mínimas e de destino do Windows 10 nas quais o aplicativo será executado:

  ![](universal-images/target-version.png "New Universal Windows Platform Project Dialog")

 Beta. Clique com o botão direito do mouse no projeto UWP e selecione **gerenciar pacotes NuGet...** e adicione o **Xamarin.Forms** pacote. Certifique-se de que os outros projetos na solução também sejam atualizados para a mesma versão do Xamarin.Forms pacote.

 quatro. Verifique se o novo projeto UWP será criado na janela **Build > Configuration Manager** (isso provavelmente não terá acontecido por padrão). Marque as caixas de **criação** e **implantação** para o projeto universal:

  [![](universal-images/configuration-sml.png "Configuration Manager Window")](universal-images/configuration.png#lightbox "Configuration Manager Window")

 05. Clique com o botão direito do mouse no projeto e selecione **adicionar > referência** e crie uma referência ao Xamarin.Forms projeto de aplicativo (.net Standard ou projeto compartilhado).

  ![](universal-images/addref-sml.png "Reference Manager Dialog")

 152. No projeto UWP, edite **app.XAML.cs** para incluir a `Init` chamada de método dentro do `OnLaunched` método em torno da linha 52:

```csharp
// under this line
rootFrame.NavigationFailed += OnNavigationFailed;
// add this line
Xamarin.Forms.Forms.Init (e); // requires the `e` parameter
```

 7. No projeto UWP, edite **MainPage. XAML** removendo o `Grid` contido dentro do `Page` elemento.

 8. Em **MainPage. XAML**, adicione uma nova `xmlns` entrada para `Xamarin.Forms.Platform.UWP` :

```csharp
xmlns:forms="using:Xamarin.Forms.Platform.UWP"
```

 99. Em **MainPage. XAML**, altere o `<Page` elemento raiz para `<forms:WindowsPage` :

```xaml
<forms:WindowsPage
...
   xmlns:forms="using:Xamarin.Forms.Platform.UWP"
...
</forms:WindowsPage>
```

 254. No projeto UWP, edite **MainPage.XAML.cs** para remover o `: Page` especificador de herança para o nome da classe (já que agora ele será herdado de `WindowsPage` devido à alteração feita na etapa anterior):

```csharp
public sealed partial class MainPage  // REMOVE ": Page"
```

 11. No **MainPage.XAML.cs**, adicione a `LoadApplication` chamada no `MainPage` construtor para iniciar o Xamarin.Forms aplicativo:

```csharp
// below this existing line
this.InitializeComponent();
// add this line
LoadApplication(new YOUR_NAMESPACE.App());
```

> [!NOTE]
> O argumento para o `LoadApplication` método é a `Xamarin.Forms.Application` instância definida em seu projeto do .net Standard.

<!--
11 . Double-click **Package.appxmanifest** to set these capabilities
  that are often required:

  Capabilities set:

  * Internet (Client)
  * Location
-->

12. Adicione todos os recursos locais (por exemplo, arquivos de imagem) dos projetos de plataforma existentes que são necessários.

## <a name="troubleshooting"></a>Solução de problemas

### <a name="target-invocation-exception-when-using-compile-with-net-native-tool-chain"></a>"Exceção de invocação de destino" ao usar "compilar com .NET Native cadeia de ferramentas"

Se seu aplicativo UWP estiver referenciando vários assemblies (por exemplo, bibliotecas de controle de terceiros ou seu próprio aplicativo for dividido em várias bibliotecas), Xamarin.Forms talvez não consiga carregar objetos desses assemblies (como renderizadores personalizados).

Isso pode ocorrer ao usar a **cadeia de ferramentas compilar com .net Native** , que é uma opção para aplicativos UWP nas **propriedades > compilação > janela geral** para o projeto.

Você pode corrigir isso usando uma sobrecarga específica de UWP da `Forms.Init` chamada em **app.XAML.cs** , conforme mostrado no código abaixo (você deve substituir `ClassInOtherAssembly` por uma classe real que seu código referencia):

```csharp
// You'll need to add `using System.Reflection;`
List<Assembly> assembliesToInclude = new List<Assembly>();

// Now, add in all the assemblies your app uses
assembliesToInclude.Add(typeof (ClassInOtherAssembly).GetTypeInfo().Assembly);

// Also do this for all your other 3rd party libraries
Xamarin.Forms.Forms.Init(e, assembliesToInclude);
// replaces Xamarin.Forms.Forms.Init(e);
```

Adicione uma entrada para cada assembly que você adicionou como uma referência no Gerenciador de Soluções, seja por meio de uma referência direta ou de um NuGet.

#### <a name="dependency-services-and-net-native-compilation"></a>Serviços de dependência e compilação de .NET Native

Compilações de versão usando .NET Native a compilação pode falhar ao resolver os serviços de dependência que são definidos fora do executável do aplicativo principal (como em um projeto ou biblioteca separada).

Use o `DependencyService.Register<T>()` método para registrar manualmente as classes de serviço de dependência. Com base no exemplo acima, adicione o método Register da seguinte maneira:

```csharp
Xamarin.Forms.Forms.Init(e, assembliesToInclude);
Xamarin.Forms.DependencyService.Register<ClassInOtherAssembly>(); // add this
```
