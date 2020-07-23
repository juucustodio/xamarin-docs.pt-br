---
title: Trabalhando com configurações de watchOS no Xamarin
description: Este documento descreve como trabalhar com as configurações do watchOS no Xamarin. Ele aborda a adição de configurações a uma solução de aplicativo de inspeção, usando essas configurações no aplicativo e o aplicativo Apple Watch no iPhone.
ms.prod: xamarin
ms.assetid: 4B2EB192-F0A2-4010-B141-0431520594C0
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: c5ad40c375320ed21acb3f0a75c5c66f2efc7824
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86938617"
---
# <a name="working-with-watchos-settings-in-xamarin"></a>Trabalhando com configurações de watchOS no Xamarin

Apple Watch aplicativos podem usar a mesma funcionalidade de configurações que aplicativos iOS – a interface do usuário de configurações é exibida no aplicativo **Apple Watch** iPhone, mas os valores são acessíveis em seu aplicativo do iPhone e também na extensão Watch.

![Apple Watch aplicativos podem usar a mesma funcionalidade de configurações que aplicativos iOS](settings-images/intro.png)

As configurações serão armazenadas em um local de arquivo compartilhado que seja acessível para o aplicativo iOS e para a extensão de aplicativo de inspeção, definida por um **grupo de aplicativos**. Você deve [configurar um grupo de aplicativos](~/ios/watchos/app-fundamentals/app-groups.md) antes de adicionar as configurações usando as instruções abaixo.

## <a name="add-settings-in-a-watch-solution"></a>Adicionar configurações em uma solução de inspeção

No **aplicativo do iPhone** em sua solução (*não* o aplicativo de inspeção ou extensão):

1. Clique com o botão direito do mouse em **adicionar > novo arquivo...** e escolha **configurações. pacote** (você não pode editar o nome na caixa de diálogo **novo arquivo** ):

   [![Adicionar um novo pacote de configurações](settings-images/settings-add-sml.png)](settings-images/settings-add.png#lightbox)

2. Altere o nome para **Settings-Watch. Bundle** (selecione e digite **Command + R** para renomear):

   ![Renomear o pacote](settings-images/settings-rename.png)

3. Adicione uma nova chave `ApplicationGroupContainerIdentifier` ao **root. plist** com o valor definido para o grupo de aplicativos que você configurou (por exemplo, `group.com.xamarin.WatchSettings`no exemplo):

   [![Adicionar uma chave ApplicationGroupContainerIdentifier ao root. plist](settings-images/settings-appgroup-sml.png)](settings-images/settings-appgroup.png#lightbox)

4. Edite o **Settings-Watch. Bundle/raiz. plist** para conter as opções que você deseja usar – o arquivo de modelo contém um grupo.
  TextField, toggle switch e Slider por padrão (que você pode excluir e substituir por suas próprias configurações):

  [![Editar o Settings-Watch. Bundle/raiz. plist](settings-images/rootplist-sml.png)](settings-images/rootplist.png#lightbox)

## <a name="use-settings-in-the-watch-app"></a>Usar as configurações no aplicativo Watch

Para acessar os valores selecionados pelo usuário, crie uma `NSUserDefaults` instância usando o grupo de aplicativos e especificando `NSUserDefaultsType.SuiteName` :

```csharp
NSUserDefaults shared = new NSUserDefaults(
    "group.com.xamarin.WatchSettings",
    NSUserDefaultsType.SuiteName);

var isEnabled = shared.BoolForKey ("enabled_preference");
var userName = shared.StringForKey ("name_preference");
```

## <a name="apple-watch-app"></a>Apple Watch aplicativo

[![O novo aplicativo Apple Watch no iPhone](settings-images/settings-app-sml.png)](settings-images/settings-app.png#lightbox)

Os usuários irão interagir com as configurações por meio do novo aplicativo **Apple Watch** em seu iPhone. Esse aplicativo permite que o usuário mostre/oculte aplicativos no Watch e também edite as configurações expostas usando o **Settings-Watch. Bundle**.

![Exemplo de configurações do aplicativo](settings-images/applewatch-1.png) ![Exemplo de configurações do aplicativo](settings-images/applewatch-2.png)

## <a name="related-links"></a>Links Relacionados

- [Doc de configurações da Apple](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/Settings.html#//apple_ref/doc/uid/TP40014969-CH22-SW1)
