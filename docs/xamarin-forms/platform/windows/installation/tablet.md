---
title: Adicionar um aplicativo do Windows
ms.prod: xamarin
ms.assetid: ADF99B78-F1BC-48DF-9128-01B93C4411C1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/16/2016
ms.openlocfilehash: 0d2ef44896c9352776443c2fec318d40d27d7539
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="adding-a-windows-app"></a>Adicionar um aplicativo do Windows


1. **com o botão direito na solução > Adicionar > Novo projeto...**  e adicione um **aplicativo em branco (Windows)**

 ![](tablet-images/add-wu.png "Adicionar caixa de diálogo Novo projeto")

2. **Clique com botão direito no projeto > Gerenciar pacotes NuGet...**  e adicione o **xamarin. Forms** pacote.

3. **Clique com botão direito no projeto > Adicionar > referência** e criar uma referência de projeto para o projeto de aplicativo xamarin. Forms compartilhado.

  ![](tablet-images/addref.png "Caixa de diálogo Gerenciador de referências")

4. Editar **App.xaml.cs** para incluir o `Init()` chamada do método dentro de `OnLaunched` método em torno da linha 65

```csharp
// add this line
Xamarin.Forms.Forms.Init (e); // requires LaunchActivatedEventArgs
// above this existing line
if (e.PreviousExecutionState == ApplicationExecutionState.Terminated) {}
```

 5 . Editar **MainPage. XAML** -altere o elemento raiz `<Page` para `<forms:WindowsPage` *e* definir o `xmlns:forms` que ele usa:

```xaml
<forms:WindowsPage
...
   xmlns:forms="using:Xamarin.Forms.Platform.WinRT"
...
</forms:WindowsPage>
```


 6 . Editar **MainPage.xaml.cs** para remover o `: Page` especificador de herança para o nome da classe.

```csharp
public sealed partial class MainPage  // REMOVE ": Page"
```

 7 . Ainda no **MainPage.xaml.cs**, adicionar o `LoadApplication` chamar o `MainPage` construtor (em torno da linha 28) para iniciar o aplicativo xamarin. Forms:

```csharp
// below this existing line
this.InitializeComponent();
// add this line
LoadApplication(new YOUR_NAMESPACE.App());
```

8 . Clique duas vezes em **Package. appxmanifest** para definir esses recursos que costumam ser necessários:

  Conjunto de recursos:

  * Internet (Client)
  * Local

9 . Finalmente, adicione qualquer recurso local (por exemplo. arquivos de imagem) dos projetos existentes de plataforma que são necessários.

