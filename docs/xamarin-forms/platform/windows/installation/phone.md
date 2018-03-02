---
title: Adicionar um aplicativo do Windows Phone
ms.topic: article
ms.prod: xamarin
ms.assetid: B598FA9D-6818-4CC9-8191-838C156DB9DA
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/16/2016
ms.openlocfilehash: bb3b7e101dad98a76ac6b8f55d190514d1bd599d
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2018
---
# <a name="adding-a-windows-phone-app"></a>Adicionar um aplicativo do Windows Phone


Em primeiro lugar, se você usou o modelo xamarin. Forms PCL, [atualizar o perfil](~/xamarin-forms/platform/windows/installation/index.md), em seguida, siga as instruções abaixo:

1. **com o botão direito na solução > Adicionar > Novo projeto...**  e adicione um **aplicativo em branco (Windows Phone)**

  ![](phone-images/add-wp81.png "Adicionar caixa de diálogo Novo projeto")

2. **Clique com botão direito no projeto recém-criado > Gerenciar pacotes NuGet...**  e adicione o **xamarin. Forms** pacote.

3. **Clique com botão direito no projeto > Adicionar > referência** e criar uma referência de projeto para o projeto de aplicativo xamarin. Forms compartilhado.

  ![](phone-images/addref.png "Caixa de diálogo Gerenciador de referências")

4. Editar **App.xaml.cs** para incluir o `Init()` da chamada do método, além de `OnLaunched` método em torno da linha 67:

```csharp
// add this line
Xamarin.Forms.Forms.Init (e); // requires LaunchActivatedEventArgs
// above this existing line
if (e.PreviousExecutionState == ApplicationExecutionState.Terminated) {}
```

 5 . Editar **MainPage. XAML** -altere o elemento raiz `<Page` para `<forms:WindowsPhonePage` *e* definir o `xmlns:forms` que ele usa:

```xaml
<forms:WindowsPhonePage
...
   xmlns:forms="using:Xamarin.Forms.Platform.WinRT"
...
</forms:WindowsPhonePage>
```

 6 . Editar **MainPage.xaml.cs** para remover o `: PhonePage` especificador de herança para o nome da classe.

```csharp
public sealed partial class MainPage  // REMOVE ": PhonePage"
```

 7 . Ainda no **MainPage.xaml.cs**, adicionar o `LoadApplication` chamar o `MainPage` construtor (em torno da linha 28) para iniciar o aplicativo xamarin. Forms:

```csharp
// below this existing line
this.InitializeComponent();
// add this line
LoadApplication(new YOUR_NAMESPACE.App());
```

8 . Clique duas vezes em **Package. appxmanifest** para definir esses recursos que costumam ser necessários:

  * Internet (Cliente e Servidor) &

9 . Finalmente, adicione qualquer recurso local (por exemplo. arquivos de imagem) dos projetos existentes de plataforma que são necessários.

