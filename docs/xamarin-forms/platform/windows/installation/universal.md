---
title: Adicionar um aplicativo UWP (plataforma) Universal do Windows
description: "Este artigo explica como adicionar um projeto de aplicativo UWP para uma solução de xamarin. Forms que foi criada no Visual Studio para Mac."
ms.topic: article
ms.prod: xamarin
ms.assetid: 34AAA045-64B8-4FDE-BB49-3FF0B4FFA17C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/16/2016
ms.openlocfilehash: 4a8d2934c4fbdc5b748014cb7dc9a121ade8c37e
ms.sourcegitcommit: 8e722d72c5d1384889f70adb26c5675544897b1f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/15/2018
---
# <a name="adding-a-universal-windows-platform-uwp-app"></a>Adicionar um aplicativo UWP (plataforma) Universal do Windows

_Este artigo explica como adicionar um projeto de aplicativo UWP para uma solução de xamarin. Forms que foi criada no Visual Studio para Mac._

Você deve estar executando **2017 do Visual Studio** na **Windows 10** para criar aplicativos UWP. Para obter mais informações sobre a plataforma Universal do Windows, consulte [gratuito para a plataforma Universal do Windows](/windows/uwp/get-started/universal-application-platform-guide/).

UWP está disponível em xamarin. Forms 2.1 e posterior, e Xamarin.Forms.Maps é suportado no xamarin. Forms 2.2 e posterior.

Verifique o <a href="#troubleshooting">de solução de problemas</a> seção para obter dicas úteis.

Siga estas instruções para adicionar um aplicativo UWP que será executado em telefones, tablets e desktops Windows 10:

 1 . Com o botão direito na solução e selecione **Adicionar > Novo projeto...**  e adicione um **(Universal do Windows) do aplicativo em branco** projeto:

  ![](universal-images/add-wu.png "Adicionar caixa de diálogo Novo projeto")

 2 . No **novo projeto de plataforma Universal do Windows** caixa de diálogo, selecione as versões de mínimo e o destino do Windows 10 que o aplicativo será executado em:

  ![](universal-images/target-version.png "Novo diálogo do projeto de plataforma Universal do Windows")

 3 . Clique com botão direito no projeto UWP e selecione **gerenciar pacotes NuGet...**  e adicione o **xamarin. Forms** pacote. Certifique-se de que os outros projetos na solução também são atualizados para a mesma versão do pacote xamarin. Forms.

 4 . Verifique se o novo projeto UWP será criado **compilação > do Configuration Manager** janela (isso provavelmente não ter ocorrido por padrão). Escala de **criar** e **implantar** caixas para o projeto Universal:

  [![](universal-images/configuration-sml.png "Janela do Gerenciador de configuração")](universal-images/configuration.png#lightbox "janela do Gerenciador de configuração")

 5 . Clique com botão direito no projeto e selecione **Adicionar > referência** e criar uma referência ao projeto de aplicativo xamarin. Forms (PCL, .NET padrão ou projeto compartilhado).

  ![](universal-images/addref-sml.png "Caixa de diálogo Gerenciador de referências")

 6 . No projeto UWP, editar **App.xaml.cs** para incluir o `Init` chamada do método dentro de `OnLaunched` método em torno da linha 52:

```csharp
// under this line
rootFrame.NavigationFailed += OnNavigationFailed;
// add this line
Xamarin.Forms.Forms.Init (e); // requires the `e` parameter
```

 7 . No projeto UWP, editar **MainPage. XAML** , removendo o `Grid` contidos o `Page` elemento.

 8 . Em **MainPage. XAML**, adicione um novo `xmlns` entrada para `Xamarin.Forms.Platform.UWP`:

```csharp
xmlns:forms="using:Xamarin.Forms.Platform.UWP"
```

 9 . Em **MainPage. XAML**, alterar a raiz `<Page` elemento `<forms:WindowsPage`:

```xaml
<forms:WindowsPage
...
   xmlns:forms="using:Xamarin.Forms.Platform.UWP"
...
</forms:WindowsPage>
```

 10 . No projeto UWP, editar **MainPage.xaml.cs** para remover o `: Page` especificador de herança para o nome da classe (desde que ele será herdado do `WindowsPage` devido à alteração feita na etapa anterior):

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

12 . Adicionar qualquer recurso local (por exemplo. arquivos de imagem) dos projetos existentes de plataforma que são necessários.

<a name="troubleshooting" />

## <a name="troubleshooting"></a>Solução de problemas

<a name="target-invocation-exception" />

### <a name="target-invocation-exception-when-using-compile-with-net-native-tool-chain"></a>"Exceção de invocação de destino" ao usar "Compilar com cadeia de ferramentas nativa do .NET"

Se seu aplicativo UWP está fazendo referência a vários assemblies (por exemplo controle bibliotecas de terceiros, ou seu próprio aplicativo é dividido em várias bibliotecas), xamarin. Forms pode ser não é possível carregar objetos esses assemblies (como processadores personalizados).

Isso pode ocorrer ao usar o **compilar com cadeia de ferramentas .NET nativo** que é uma opção para aplicativos UWP o **Propriedades > compilar > geral** janela para o projeto.

Você pode corrigir isso usando uma sobrecarga específica de UWP do `Forms.Init` chamada em **App.xaml.cs** conforme mostrado no código a seguir (você deve substituir `ClassInOtherAssembly` com uma classe real do seu código referencia):

```csharp
// You'll need to add `using System.Reflection;`
List<Assembly> assembliesToInclude = new List<Assembly>();

// Now, add in all the assemblies your app uses
assembliesToInclude.Add(typeof (ClassInOtherAssembly).GetTypeInfo().Assembly);

// Also do this for all your other 3rd party libraries
Xamarin.Forms.Forms.Init(e, assembliesToInclude);
// replaces Xamarin.Forms.Forms.Init(e);
```

Adicione uma referência para cada assembly referenciado pelo aplicativo.

#### <a name="dependency-services-and-net-native-compilation"></a>Serviços de dependência e .NET compilação nativa

Compilações de versão usando a compilação nativa do .NET podem falhar resolver os serviços de dependência definidas fora do executável de aplicativo principal (como em uma biblioteca ou um projeto separado).

Use o `DependencyService.Register<T>()` método para registrar manualmente as classes de serviço de dependência. Com base no exemplo acima, adicione o método register assim:

```csharp
Xamarin.Forms.Forms.Init(e, assembliesToInclude);
Xamarin.Forms.DependencyService.Register<ClassInOtherAssembly>(); // add this
```
