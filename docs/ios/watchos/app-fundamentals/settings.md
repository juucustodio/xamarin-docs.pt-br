---
title: "Trabalhando com as configurações"
ms.topic: article
ms.prod: xamarin
ms.assetid: 4B2EB192-F0A2-4010-B141-0431520594C0
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 3ff8800f4e8690069f5394193d11552d917baffe
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="working-with-settings"></a>Trabalhando com as configurações

Aplicativos do Apple Watch podem usar a mesma funcionalidade de configurações como aplicativos iOS - a interface do usuário de configurações é exibida no **Apple Watch** aplicativo iPhone, mas os valores são acessíveis no seu aplicativo de iPhone e também a extensão de inspeção.

![](settings-images/intro.png "Aplicativos do Apple Watch podem usar a mesma funcionalidade de configurações como aplicativos do iOS")

As configurações serão armazenadas em um local de arquivo compartilhado que seja acessível para o aplicativo do iOS e a extensão do aplicativo watch, definido por um **aplicativo grupo**. Você deve [configurar um grupo de aplicativo](~/ios/watchos/app-fundamentals/app-groups.md) antes de adicionar as configurações usando as instruções a seguir.

## <a name="add-settings-in-a-watch-solution"></a>Adicionar configurações em uma solução de inspeção

No **aplicativo iPhone** em sua solução (*não* o aplicativo watch ou a extensão):

1. Clique com botão direito **Adicionar > novo arquivo...**  e escolha **Settings. Bundle** (não é possível editar o nome de **novo arquivo** caixa de diálogo):

   [ ![](settings-images/settings-add-sml.png "Adicionar um novo pacote de configurações")](settings-images/settings-add.png)

2. Altere o nome para **configurações Watch.bundle** (selecione e digite **comando + R** renomear):

   ![](settings-images/settings-rename.png "Renomeie o pacote")

3. Adicione uma nova chave `ApplicationGroupContainerIdentifier` para o **root. plist** com o valor definido para o grupo de aplicativo que você configurou, (por exemplo. `group.com.xamarin.WatchSettings` no exemplo):

   [ ![](settings-images/settings-appgroup-sml.png "Adicionar uma chave ApplicationGroupContainerIdentifier para o root. plist")](settings-images/settings-appgroup.png)

4. Editar o **Settings-Watch.bundle/Root.plist** para conter as opções que você deseja usar - o arquivo de modelo contém um grupo.
  campo de texto, switch de alternância e slider por padrão (que você pode excluir e substituir suas próprias configurações):

  [ ![](settings-images/rootplist-sml.png "Editar o Settings-Watch.bundle/Root.plist")](settings-images/rootplist.png)


## <a name="use-settings-in-the-watch-app"></a>Usar configurações no aplicativo de inspeção

Para acessar os valores selecionados pelo usuário, crie um `NSUserDefaults` instância usando o grupo de aplicativo e especificar `NSUserDefaultsType.SuiteName`:

```csharp
NSUserDefaults shared = new NSUserDefaults(
    "group.com.xamarin.WatchSettings",
    NSUserDefaultsType.SuiteName);

var isEnabled = shared.BoolForKey ("enabled_preference");
var userName = shared.StringForKey ("name_preference");
```

## <a name="apple-watch-app"></a>Aplicativo do Apple Watch

[ ![](settings-images/settings-app-sml.png "O novo aplicativo Apple Watch no iPhone")](settings-images/settings-app.png)

Usuários irão interagir com as configurações através do novo **Apple Watch** aplicativo em seu iPhone. Este aplicativo permite que o usuário Mostrar/ocultar aplicativos na inspeção e também editar as configurações expostas usando o **Watch.bundle configurações**.

![](settings-images/applewatch-1.png "Exemplo de configurações do aplicativo") ![ ] (settings-images/applewatch-2.png "exemplo de configurações de aplicativo")



## <a name="related-links"></a>Links relacionados

- [Doc de configurações da Apple](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/Settings.html#//apple_ref/doc/uid/TP40014969-CH22-SW1)
