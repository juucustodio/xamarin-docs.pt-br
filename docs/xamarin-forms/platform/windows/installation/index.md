---
title: Projetos de instalação do Windows
description: Soluções antigas do xamarin. Forms (ou aquelas criadas no macOS) não terá os projetos de plataforma Universal do Windows e, portanto, este artigo explica como adicionar um novo projeto UWP a uma solução existente do xamarin. Forms.
ms.prod: xamarin
ms.assetid: A0774D2E-6994-4D91-84E8-DAB66FC92320
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/10/2018
ms.openlocfilehash: b0f06cf15d3a3ec7eae4742d5d037e233be46d08
ms.sourcegitcommit: 3489c281c9eb5ada2cddf32d73370943342a1082
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2019
ms.locfileid: "58855179"
---
# <a name="setup-windows-projects"></a>Projetos de instalação do Windows

_Adição de novos projetos do Windows a uma solução existente do xamarin. Forms_

Soluções antigas do xamarin. Forms (ou aquelas criadas no macOS) não terá a projetos de aplicativo da plataforma Universal do Windows (UWP). Portanto, você precisará adicionar manualmente um projeto UWP para criar um aplicativo do Windows 10 (UWP).

## <a name="add-a-universal-windows-platform-app"></a>Adicionar um Universal Windows Platform app

**Visual Studio de 2019** na **Windows 10** é recomendado para criar aplicativos da UWP. Para obter mais informações sobre a plataforma Universal do Windows, consulte [Introdução à plataforma Windows Universal](/windows/uwp/get-started/universal-application-platform-guide/).

UWP está disponível no xamarin. Forms 2.1 e posterior e Xamarin.Forms.Maps tem suporte no xamarin. Forms 2.2 e posterior.

Verifique as <a href="#troubleshooting">solução de problemas</a> seção para obter dicas úteis.

Siga estas instruções para adicionar um aplicativo UWP que será executado em telefones, tablets e desktops Windows 10:

 1 . Clique com botão direito na solução e selecione **Adicionar > Novo projeto...**  e adicione uma **aplicativo em branco (Universal Windows)** projeto:

  ![](universal-images/add-wu.png "Adicionar caixa de diálogo Novo projeto")

 2 . No **novo projeto da plataforma Windows Universal** caixa de diálogo, selecione as versões mínima e de destino do Windows 10 que o aplicativo será executado em:

  ![](universal-images/target-version.png "Novo diálogo de projeto de plataforma Universal do Windows")

 3 . Clique com botão direito no projeto UWP e selecione **gerenciar pacotes NuGet...**  e adicione o **xamarin. Forms** pacote. Certifique-se de que os outros projetos na solução também são atualizados para a mesma versão do pacote do xamarin. Forms.

 4 . Verifique se o novo projeto UWP será compilado na **compilar > Configuration Manager** janela (isso provavelmente não ter ocorrido por padrão). Escala de **construir** e **implantar** caixas para o projeto Universal:

  [![](universal-images/configuration-sml.png "Janela do Gerenciador de configuração")](universal-images/configuration.png#lightbox "janela do Gerenciador de configuração")

 5 . Clique com botão direito no projeto e selecione **Add > referência** e criar uma referência ao projeto de aplicativo xamarin. Forms (.NET Standard ou projeto compartilhado).

  ![](universal-images/addref-sml.png "Caixa de diálogo Gerenciador de referências")

 6 . No projeto UWP, edite **App.xaml.cs** para incluir o `Init` chamada do método dentro de `OnLaunched` método perto da linha 52:

```csharp
// under this line
rootFrame.NavigationFailed += OnNavigationFailed;
// add this line
Xamarin.Forms.Forms.Init (e); // requires the `e` parameter
```

 7 . No projeto UWP, edite **MainPage. XAML** removendo a `Grid` contidos o `Page` elemento.

 8 . Na **MainPage. XAML**, adicione uma nova `xmlns` entrada de `Xamarin.Forms.Platform.UWP`:

```csharp
xmlns:forms="using:Xamarin.Forms.Platform.UWP"
```

 9 . Na **MainPage. XAML**, alterar a raiz `<Page` elemento `<forms:WindowsPage`:

```xaml
<forms:WindowsPage
...
   xmlns:forms="using:Xamarin.Forms.Platform.UWP"
...
</forms:WindowsPage>
```

 10 . No projeto UWP, edite **MainPage.xaml.cs** para remover o `: Page` especificador de herança para o nome da classe (uma vez que ele agora herda `WindowsPage` devido à alteração feita na etapa anterior):

```csharp
public sealed partial class MainPage  // REMOVE ": Page"
```

 11 . No **MainPage.xaml.cs**, adicione o `LoadApplication` chamar o `MainPage` construtor para iniciar o aplicativo xamarin. Forms:

```csharp
// below this existing line
this.InitializeComponent();
// add this line
LoadApplication(new YOUR_NAMESPACE.App());
```

<!--
11 . Double-click **Package.appxmanifest** to set these capabilities
  that are often required:

  Capabilities set:

  * Internet (Client)
  * Location
-->

12 . Adicione qualquer recurso local (por exemplo. arquivos de imagem) de projetos de plataforma existentes que são necessários.

## <a name="troubleshooting"></a>Solução de problemas

<a name="target-invocation-exception" />

### <a name="target-invocation-exception-when-using-compile-with-net-native-tool-chain"></a>"Exceção de invocação de destino" ao usar "Compilar com cadeia de ferramentas nativas do .NET"

Se seu aplicativo da UWP está fazendo referência a vários assemblies (por exemplo bibliotecas de controle de terceiros, ou seu próprio aplicativo é dividido em várias bibliotecas), xamarin. Forms talvez não consiga carregar objetos desses assemblies (como renderizadores personalizados).

Isso pode ocorrer ao usar o **compilar com cadeia de ferramentas .NET Native** que é uma opção para aplicativos UWP na **Propriedades > Build > geral** janela para o projeto.

Você pode corrigir isso usando uma sobrecarga específica de UWP do `Forms.Init` chamar **App.xaml.cs** conforme mostrado no código a seguir (você deve substituir `ClassInOtherAssembly` com uma classe real do seu código faz referência):

```csharp
// You'll need to add `using System.Reflection;`
List<Assembly> assembliesToInclude = new List<Assembly>();

// Now, add in all the assemblies your app uses
assembliesToInclude.Add(typeof (ClassInOtherAssembly).GetTypeInfo().Assembly);

// Also do this for all your other 3rd party libraries
Xamarin.Forms.Forms.Init(e, assembliesToInclude);
// replaces Xamarin.Forms.Forms.Init(e);
```

Adicione uma entrada para cada assembly que você adicionou como uma referência no Gerenciador de soluções, por meio de uma referência direta ou um NuGet.

#### <a name="dependency-services-and-net-native-compilation"></a>Compilação nativa de serviços de dependência e .NET

Builds de versão usando a compilação do .NET nativo podem fazer para resolver os serviços de dependência que são definidos fora o executável de aplicativo principal (como em um projeto separado ou biblioteca).

Use o `DependencyService.Register<T>()` método para registrar manualmente as classes de serviço de dependência. Com base no exemplo acima, adicione o método register como este:

```csharp
Xamarin.Forms.Forms.Init(e, assembliesToInclude);
Xamarin.Forms.DependencyService.Register<ClassInOtherAssembly>(); // add this
```
