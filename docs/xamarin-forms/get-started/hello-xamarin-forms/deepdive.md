---
title: Aprofundamento no Xamarin.Forms
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: d97aa580-1eb9-48b3-b15b-0d7421ea7ae
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/06/2018
ms.openlocfilehash: 91086186f600855f597e3cd758b9bc30b48d82fb
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="xamarinforms-deep-dive"></a>Aprofundamento no Xamarin.Forms

No [Início rápido do Xamarin.Forms](~/xamarin-forms/get-started/hello-xamarin-forms/quickstart.md), o aplicativo Phoneword foi criado. Este artigo analisa o que foi criado para entender os fundamentos de como os aplicativos Xamarin.Forms funcionam.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

## <a name="introduction-to-visual-studio"></a>Introdução ao Visual Studio

O Visual Studio é um IDE avançado da Microsoft. Ele conta com um designer visual totalmente integrado, um editor de texto complementado com ferramentas de refatoração, um navegador de assembly, integração de código-fonte e muito mais. Este artigo se concentra no uso alguns dos recursos básicos do Visual Studio com o plug-in Xamarin.

O Visual Studio organiza o código em *Soluções* e *Projetos*. Uma solução é um contêiner que pode conter um ou mais Projetos. Um Projeto pode ser um aplicativo, uma biblioteca com suporte, um aplicativo de teste e muito mais. O aplicativo Phoneword consiste em uma solução contendo quatro projetos, conforme mostrado na captura de tela a seguir.

![](deepdive-images/vs/solution.png "Gerenciador de soluções do Visual Studio")

Os projetos são:

- Phoneword – esse projeto é o projeto da biblioteca do .NET Standard que contém todos os códigos compartilhados e interfaces do usuário compartilhadas.
- Phoneword.Android – esse projeto contém o código específico do Android e é o ponto de entrada para o aplicativo Android.
- Phoneword.iOS – esse projeto contém o código específico do iOS e é o ponto de entrada para o aplicativo iOS.
- Phoneword.UWP – esse projeto contém código específico da UWP (Plataforma Universal do Windows) e é o ponto de entrada para o aplicativo UWP.

## <a name="anatomy-of-a-xamarinforms-application"></a>Anatomia de um aplicativo Xamarin.Forms

A captura de tela a seguir mostra o conteúdo do projeto de biblioteca do .NET Standard Phoneword no Visual Studio:

![](deepdive-images/vs/net-standard-project.png "Conteúdo do projeto do .NET Standard Phoneword")

O projeto tem um nó **Dependências** que contém os nós **NuGet** e **SDK**. O nó **NuGet** contém o pacote do NuGet Xamarin.Forms que foi adicionado ao projeto e o nó **SDK** contém o metapacote `NETStandard.Library` que faz referência ao conjunto completo de pacotes do NuGet que definem o .NET Standard.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

## <a name="introduction-to-visual-studio-for-mac"></a>Introdução ao Visual Studio para Mac

O Visual Studio para Mac é um IDE de software livre gratuito semelhante ao Visual Studio. Ele conta com um designer visual totalmente integrado, um editor de texto complementado com ferramentas de refatoração, um navegador de assembly, integração de código-fonte e muito mais. Para obter mais informações sobre o Visual Studio para Mac, consulte [Introdução ao Visual Studio para Mac](/visualstudio/mac/).

O Visual Studio para Mac segue a prática do Visual Studio de organizar o código em *Soluções* e *Projetos*. Uma solução é um contêiner que pode conter um ou mais Projetos. Um Projeto pode ser um aplicativo, uma biblioteca com suporte, um aplicativo de teste e muito mais. O aplicativo Phoneword consiste em uma solução contendo três projetos, conforme mostrado na seguinte captura de tela.

![](deepdive-images/xs/solution.png "Painel de soluções do Visual Studio para Mac")

Os projetos são:

- Phoneword – esse projeto é o projeto PCL (biblioteca de classes portátil) que contém todos os códigos compartilhados e interfaces do usuário compartilhadas.
- Phoneword.Droid – esse projeto contém o código específico do Android e é o ponto de entrada para aplicativos Android.
- Phoneword.iOS – esse projeto contém o código específico do iOS e é o ponto de entrada para aplicativos iOS.

## <a name="anatomy-of-a-xamarinforms-application"></a>Anatomia de um aplicativo Xamarin.Forms

A captura de tela a seguir mostra o conteúdo do projeto PCL Phoneword no Visual Studio para Mac:

![](deepdive-images/xs/pcl-project.png "Conteúdo do projeto PCL Phoneword")

O projeto consiste em três pastas:

- **Referências** – contém os assemblies necessários para compilar e executar o aplicativo. Expandir a pasta do Subconjunto Portátil .NET revela referências aos assemblies do .NET, como [System](http://msdn.microsoft.com/library/system%28v=vs.110%29.aspx), System.Core e [System.Xml](http://msdn.microsoft.com/library/system.xml%28v=vs.110%29.aspx). Expandir a pasta **Pacotes de Origem** revela referências aos assemblies Xamarin.Forms.
- **Pacotes** – o diretório Pacotes aloja pacotes [NuGet](https://www.nuget.org) que simplificam o processo de usar bibliotecas de terceiros em seu aplicativo. Esses pacotes podem ser atualizados para as versões mais recentes clicando com o botão direito do mouse na pasta e selecionando a opção de atualização no menu pop-up.
- **Propriedades** – contém **AssemblyInfo.cs**, um arquivo de metadados de assembly do .NET. É uma boa prática preencher esse arquivo com algumas informações básicas sobre seu aplicativo. Para saber mais sobre esse arquivo, confira [Classe AssemblyInfo](http://msdn.microsoft.com/library/microsoft.visualbasic.applicationservices.assemblyinfo(v=vs.110).aspx) no MSDN.

-----

O projeto também consiste em vários arquivos:

- **XAML** – a marcação XAML para a classe `App`, que define um dicionário de recursos para o aplicativo.
- **App.XAML.CS** – o code-behind para a classe `App`, que é responsável por instanciar a primeira página que será exibida pelo aplicativo em cada plataforma e por processar eventos de ciclo de vida do aplicativo.
- **IDialer.cs** – a interface `IDialer`, que especifica que o método `Dial` deve ser fornecido por qualquer classe de implementação.
- **MainPage. XAML** – a marcação XAML para a classe `MainPage`, que define a interface do usuário para a página exibida quando o aplicativo é iniciado.
- **MainPage.xaml.cs** – o code-behind para a classe `MainPage`, que contém a lógica de negócios executada quando o usuário interage com a página.
- **packages.config** – (somente Visual Studio para Mac) um arquivo XML que contém informações sobre os pacotes do NuGet usados pelo projeto para rastrear os pacotes necessários e suas respectivas versões. O Visual Studio para Mac e o Visual Studio podem ser configurados para restaurar automaticamente quaisquer pacotes do NuGet ausentes ao compartilhar o código-fonte com outros usuários. O conteúdo desse arquivo é controlado pelo gerenciador de pacotes NuGet e não deve ser editado manualmente.
- **PhoneTranslator.cs** – a lógica de negócios responsável por converter uma palavra de telefone em um número de telefone, que é invocado de **MainPage.xaml.cs**.

Para saber mais sobre a anatomia de um aplicativo Xamarin.iOS, consulte [Anatomia de um aplicativo Xamarin.iOS](~/ios/get-started/hello-ios/hello-ios-deepdive.md#anatomy). Para saber mais sobre a anatomia de um aplicativo Xamarin.Android, consulte [Anatomia de um aplicativo Xamarin.Android](~/android/get-started/hello-android/hello-android-deepdive.md#anatomy).

## <a name="architecture-and-application-fundamentals"></a>Arquitetura e conceitos básicos de aplicativo

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Um aplicativo Xamarin.Forms é arquitetado da mesma forma que um aplicativo tradicional de plataforma cruzada. O código compartilhado normalmente é colocado em uma biblioteca do .NET Standard e aplicativos específicos de plataforma consomem o código compartilhado. O diagrama a seguir mostra uma visão geral dessa relação para o aplicativo Phoneword:

![](deepdive-images/vs/architecture.png "Arquitetura do Phoneword")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Um aplicativo Xamarin.Forms é arquitetado da mesma forma que um aplicativo tradicional de plataforma cruzada. O código compartilhado normalmente é colocado em uma PCL (Biblioteca de Classes Portátil) e aplicativos específicos de plataforma consome o código compartilhado. O diagrama a seguir mostra uma visão geral dessa relação para o aplicativo Phoneword:

![](deepdive-images/xs/architecture.png "Arquitetura do Phoneword")

Para obter mais informações sobre PCLs, consulte [Introduction to Portable Class Libraries](~/cross-platform/app-fundamentals/pcl.md) (Introdução às bibliotecas de classes portáteis).

-----

Para maximizar a reutilização de código de inicialização, os aplicativos Xamarin.Forms têm uma única classe chamada `App`, que é responsável por instanciar a primeira página que será exibida pelo aplicativo em cada plataforma, conforme mostrado no exemplo de código a seguir:

```csharp
using Xamarin.Forms;
using Xamarin.Forms.Xaml;

[assembly: XamlCompilation(XamlCompilationOptions.Compile)]
namespace Phoneword
{
    public partial class App : Application
    {
        public App()
        {
            InitializeComponent();
            MainPage = new MainPage();
        }
        ...
    }
}
```

Esse código define a propriedade `MainPage` da classe `App` para uma nova instância da classe [`MainPage`](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.MainPage/). Além disso, o atributo [`XamlCompilation`](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.XamlCompilationAttribute/) ativa o compilador XAML para que o XAML seja compilado diretamente em linguagem intermediária. Para saber mais, consulte [XAML Compilation](~/xamarin-forms/xaml/xamlc.md) (Compilação de XAML).

## <a name="launching-the-application-on-each-platform"></a>Iniciar o aplicativo em cada plataforma

### <a name="ios"></a>iOS

Para inicializar a página inicial do Xamarin.Forms no iOS, o projeto Phoneword.iOS inclui a classe `AppDelegate`, que herda da classe `FormsApplicationDelegate`, conforme mostra o exemplo de código a seguir:

```csharp
namespace Phoneword.iOS
{
    [Register ("AppDelegate")]
    public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate
    {
        public override bool FinishedLaunching (UIApplication app, NSDictionary options)
        {
            global::Xamarin.Forms.Forms.Init ();
            LoadApplication (new App ());
            return base.FinishedLaunching (app, options);
        }
    }
}
```

A substituição `FinishedLaunching` inicializa a estrutura do Xamarin.Forms ao chamar o método `Init`. Isso faz a implementação específica do iOS do Xamarin.Forms ser carregada no aplicativo antes que o controlador de exibição de raiz seja definido pela chamada para o método `LoadApplication`.

### <a name="android"></a>Android

Para inicializar a página inicial do Xamarin.Forms no Android, o projeto Phoneword.Droid inclui código que cria uma `Activity` com o atributo `MainLauncher`, com a atividade herdando da classe `FormsApplicationActivity`, como mostra o código de exemplo a seguir:

```csharp
namespace Phoneword.Droid
{
    [Activity(Label = "Phoneword",
              Icon = "@drawable/icon",
              MainLauncher = true,
              ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
    public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity
    {
        internal static MainActivity Instance { get; private set; }

        protected override void OnCreate(Bundle bundle)
        {
            base.OnCreate(bundle);

            Instance = this;
            global::Xamarin.Forms.Forms.Init(this, bundle);
            LoadApplication(new App());
        }
    }
}
```

A substituição `OnCreate` inicializa a estrutura do Xamarin.Forms ao chamar o método `Init`. Isso faz a implementação específica do Android do Xamarin.Forms ser carregada no aplicativo antes de o aplicativo Xamarin.Forms ser carregado. Além disso, a classe `MainActivity` armazena uma referência a si mesma na propriedade `Instance`. A propriedade `Instance` é conhecida como o contexto local e é referenciada da classe `PhoneDialer`.

## <a name="universal-windows-platform"></a>Plataforma Universal do Windows

Em aplicativos UWP (Plataforma Universal do Windows), o método `Init` que inicializa a estrutura Xamarin.Forms é chamado da classe `App`:

```csharp
Xamarin.Forms.Forms.Init (e);

if (e.PreviousExecutionState == ApplicationExecutionState.Terminated)
{
  ...
}
```

Isso faz a implementação específica de UWP do Xamarin.Forms ser carregada no aplicativo. A página inicial do Xamarin.Forms é iniciada pela classe `MainPage`, conforme demonstrado no exemplo de código a seguir:

```csharp
namespace Phoneword.UWP
{
    public sealed partial class MainPage
    {
        public MainPage()
        {
            this.InitializeComponent();
            this.LoadApplication(new Phoneword.App());
        }
    }
}
```

O aplicativo Xamarin.Forms é carregado com o método `LoadApplication`.

> [!NOTE]
> Os aplicativos da Plataforma Universal do Windows (UWP) podem ser criados com o Xamarin.Forms, mas somente usando o Visual Studio no Windows.

## <a name="user-interface"></a>Interface do Usuário

Há quatro grupos de controle principais usados para criar a interface do usuário de um aplicativo Xamarin.Forms.

1. **Páginas** – as páginas do Xamarin.Forms representam telas de aplicativos móveis de plataforma cruzada. O aplicativo Phoneword usa a classe [`ContentPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) para exibir uma única tela. Para saber mais sobre páginas, consulte [Xamarin.Forms Pages](~/xamarin-forms/user-interface/controls/pages.md) (Páginas do Xamarin.Forms).
1. **Layouts** – os layouts do Xamarin.Forms são contêineres usados para compor exibições em estruturas lógicas. O aplicativo Phoneword usa a classe [`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) para organizar controles em uma pilha horizontal. Para saber mais sobre layouts, consulte [Xamarin.Forms Layouts](~/xamarin-forms/user-interface/controls/layouts.md) (Layouts do Xamarin.Forms).
1. **Exibições** – as exibições do Xamarin.Forms são os controles exibidos na interface do usuário, como rótulos, botões e caixas de entrada de texto. O aplicativo Phoneword usa os controles [`Label`](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/), [`Entry`](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) e [`Button`](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/). Para saber mais sobre exibições, consulte [Xamarin.Forms Views](~/xamarin-forms/user-interface/controls/views.md) (Exibições do Xamarin.Forms).
1. **Células** – as células do Xamarin.Forms são elementos especializados usados para itens em uma lista e descrevem como cada item em uma lista deve ser desenhado. O aplicativo Phoneword não usa nenhuma célula. Para saber mais sobre células, consulte [Xamarin.Forms Cells](~/xamarin-forms/user-interface/controls/cells.md) (Células do Xamarin.Forms).

No tempo de execução, cada controle será mapeado para seu equivalente nativo, que é o que será renderizado.

Quando o aplicativo Phoneword é executado em qualquer plataforma, ele exibe uma única tela que corresponde a um [`Page`](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) no Xamarin.Forms. Um `Page` representa um *ViewGroup* no Android, um *Controlador de Exibição* no iOS ou uma *Página* na Plataforma Universal do Windows. O aplicativo Phoneword também instancia um objeto [`ContentPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) que representa a classe `MainPage`, cuja marcação XAML é mostrada no exemplo de código a seguir:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                         xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                         x:Class="Phoneword.MainPage">
        ...
        <StackLayout>
            <Label Text="Enter a Phoneword:" />
            <Entry x:Name="phoneNumberText" Text="1-855-XAMARIN" />
            <Button x:Name="translateButon" Text="Translate" Clicked="OnTranslate" />
            <Button x:Name="callButton" Text="Call" IsEnabled="false" Clicked="OnCall" />
        </StackLayout>
</ContentPage>
```

A classe `MainPage` usa um controle [`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) para organizar automaticamente os controles na tela, não importa o tamanho da tela. Cada elemento filho é posicionado um depois do outro, verticalmente na ordem em que são adicionados. O controle `StackLayout` contém um controle [`Label`](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) para exibir o texto na página, um controle [`Entry`](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) para aceitar entrada textual do usuário e dois controles [`Button`](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) usados para executar código em resposta a eventos de toque.

Para saber mais sobre XAML no Xamarin.Forms, consulte [Xamarin.Forms XAML Basics](~/xamarin-forms/xaml/xaml-basics/index.md) (Noções básicas de XAML no Xamarin.Forms).

### <a name="responding-to-user-interaction"></a>Respondendo à interação do usuário

Um objeto definido em XAML pode acionar um evento que é processado no arquivo code-behind. O seguinte exemplo de código mostra o método `OnTranslate` no code-behind para a classe `MainPage`, que é executada em resposta ao disparo do evento [`Clicked`](https://developer.xamarin.com/api/event/Xamarin.Forms.Button.Clicked/) no botão *Traduzir*.

```csharp
void OnTranslate(object sender, EventArgs e)
{
    translatedNumber = Core.PhonewordTranslator.ToNumber (phoneNumberText.Text);
    if (!string.IsNullOrWhiteSpace (translatedNumber)) {
        callButton.IsEnabled = true;
        callButton.Text = "Call " + translatedNumber;
    } else {
        callButton.IsEnabled = false;
        callButton.Text = "Call";
    }
}
```

O método `OnTranslate` traduz a phoneword para seu número de telefone correspondente e, em resposta, define as propriedades no botão de chamada. O arquivo code-behind de uma classe XAML pode acessar um objeto definido em XAML usando o nome atribuído a ele com o atributo `x:Name`. O valor atribuído a esse atributo tem as mesmas regras que variáveis C#, no sentido de que deve começar com uma letra ou um sublinhado e não conter espaços.

A fiação do botão de traduzir para o método `OnTranslate` ocorre na marcação XAML para a classe `MainPage`:

```xaml
<Button x:Name="translateButon" Text="Translate" Clicked="OnTranslate" />
```

## <a name="additional-concepts-introduced-in-phoneword"></a>Conceitos adicionais introduzidos no Phoneword

O aplicativo Phoneword para Xamarin.Forms introduziu vários conceitos não abordados neste artigo. Esses conceitos incluem:

- Habilitar e desabilitar botões. Um [`Button`](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) pode ser ativado ou desativado alterando sua propriedade [`IsEnabled`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.IsEnabled/). Por exemplo, o seguinte código de exemplo desabilita o `callButton`:

    ```csharp
    callButton.IsEnabled = false;
    ```

- Exibindo uma caixa de diálogo de alerta. Quando o usuário pressiona a chamada **Botão**, o aplicativo Phoneword mostra uma *Caixa de Diálogo de Alerta* com a opção de fazer ou cancelar uma chamada. O método [`DisplayAlert`](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayAlert/p/System.String/System.String/System.String/System.String/) é usado para criar a caixa de diálogo, como mostrado no código de exemplo a seguir:

    ```csharp
    await this.DisplayAlert (
            "Dial a Number",
            "Would you like to call " + translatedNumber + "?",
            "Yes",
            "No");
    ```

- Acessando recursos nativos por meio da classe [`DependencyService`](https://developer.xamarin.com/api/type/Xamarin.Forms.DependencyService/). O aplicativo Phoneword usa a classe `DependencyService` para resolver a interface `IDialer` para implementações de discagem de telefone específicas da plataforma, como mostra o código de exemplo a seguir do projeto Phoneword:

    ```csharp
    async void OnCall (object sender, EventArgs e)
    {
        ...
        var dialer = DependencyService.Get<IDialer> ();
        ...
    }
    ```

  Para saber mais sobre a classe [`DependencyService`](https://developer.xamarin.com/api/type/Xamarin.Forms.DependencyService/), consulte [Acessando recursos nativos por meio do DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md).

- Fazendo uma chamada telefônica com uma URL. O aplicativo Phoneword usa `OpenURL` para inicializar o aplicativo de telefone do sistema. A URL consiste em um prefixo `tel:` seguido pelo número de telefone a ser chamado, como mostra o exemplo de código a seguir do projeto iOS:

    ```csharp
    return UIApplication.SharedApplication.OpenUrl (new NSUrl ("tel:" + number));
    ```

- Ajustando o layout da plataforma. A classe [`Device`](https://developer.xamarin.com/api/type/Xamarin.Forms.Device/) permite aos desenvolvedores personalizar o layout e a funcionalidade do aplicativo por plataforma, conforme mostra o exemplo de código a seguir que usa outros valores [`Padding`](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout.Padding/) nas plataformas para exibir corretamente a cada página:

    ```xaml
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms" ... >
        <ContentPage.Padding>
            <OnPlatform x:TypeArguments="Thickness">
                <On Platform="iOS" Value="20, 40, 20, 20" />
                <On Platform="Android, WinPhone, Windows" Value="20" />
            </OnPlatform>
        </ContentPage.Padding>
        ...
    </ContentPage>
    ```

  Para saber mais sobre os ajustes de plataforma, consulte [Device Class](~/xamarin-forms/platform/device.md) (Classe de Dispositivo).

## <a name="testing-and-deployment"></a>Teste e implantação

Tanto o Visual Studio para Mac quanto o Visual Studio oferecem várias opções para testar e implantar um aplicativo. Depurar aplicativos é uma parte comum do ciclo de vida de desenvolvimento de aplicativo e ajuda a diagnosticar problemas de código. Para saber mais, consulte [Definir um ponto de interrupção](https://developer.xamarin.com/recipes/cross-platform/ide/debugging/set_a_breakpoint/), [Percorrer o código](https://developer.xamarin.com/recipes/cross-platform/ide/debugging/step_through_code/) e [Enviar as informações para a janela de log](https://developer.xamarin.com/recipes/cross-platform/ide/debugging/output_information_to_log_window/).

Simuladores são um bom lugar para começar a implantar e testar um aplicativo e eles apresentam recursos úteis para testar aplicativos. No entanto, os usuários não consumirão o aplicativo final em um simulador, assim, os aplicativos devem ser testados em dispositivos reais antecipadamente e com frequência. Para saber mais sobre o provisionamento de dispositivo iOS, consulte [Provisionamento de Dispositivo](~/ios/get-started/installation/device-provisioning/index.md). Para saber mais sobre o provisionamento de dispositivo Android, consulte [Configurar o dispositivo para desenvolvimento](~/android/get-started/installation/set-up-device-for-development.md).

## <a name="summary"></a>Resumo

Este artigo examinou os conceitos básicos do desenvolvimento de aplicativos usando o Xamarin.Forms. Os tópicos abordados incluído a anatomia de um aplicativo Xamarin.Forms, arquitetura e conceitos básicos de aplicativo e a interface do usuário.

Na próxima seção deste guia, o aplicativo será estendido para incluir várias telas, a fim de explorar conceitos e arquiteturas mais avançados do Xamarin.Forms.
