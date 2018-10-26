---
title: Trabalhando com o watchOS configurações no Xamarin
description: Este documento descreve como trabalhar com as configurações de watchOS no Xamarin. Ele aborda as configurações adicionando a uma solução de aplicativo de inspeção, usando essas configurações no aplicativo e o aplicativo de Apple Watch no iPhone.
ms.prod: xamarin
ms.assetid: 4B2EB192-F0A2-4010-B141-0431520594C0
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 36164e1e9f92b5a5520d10f769f3953cfa2ceb85
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50106345"
---
# <a name="working-with-watchos-settings-in-xamarin"></a>Trabalhando com o watchOS configurações no Xamarin

Aplicativos do Apple Watch podem usar a mesma funcionalidade de configurações como aplicativos do iOS: a interface do usuário de configurações é exibida na **Apple Watch** aplicativo iPhone, mas os valores são acessíveis no seu aplicativo de iPhone e também a extensão de inspeção.

![](settings-images/intro.png "Aplicativos do Apple Watch podem usar a mesma funcionalidade de configurações como aplicativos do iOS")

As configurações serão armazenadas em um local de arquivo compartilhado que seja acessível para o aplicativo do iOS e a extensão do aplicativo watch, definido por uma **grupo de aplicativos**. Você deve [configurar um grupo de aplicativos](~/ios/watchos/app-fundamentals/app-groups.md) antes de adicionar as configurações usando as instruções abaixo.

## <a name="add-settings-in-a-watch-solution"></a>Adicionar configurações em uma solução de inspeção

No **aplicativo de iPhone** em sua solução (*não* o aplicativo de inspeção ou extensão):

1. Clique com botão direito **Adicionar > novo arquivo...**  e escolha **Settings. Bundle** (você não pode editar o nome no **novo arquivo** caixa de diálogo):

   [![](settings-images/settings-add-sml.png "Adicionar um novo pacote de configurações")](settings-images/settings-add.png#lightbox)

2. Altere o nome para **configurações Watch.bundle** (selecione e digite **Command + R** renomear):

   ![](settings-images/settings-rename.png "Renomeie o pacote")

3. Adicione uma nova chave `ApplicationGroupContainerIdentifier` para o **root. plist** com o valor definido para o grupo de aplicativos que você configurou, (por exemplo. `group.com.xamarin.WatchSettings` no exemplo):

   [ ![](settings-images/settings-appgroup-sml.png "Adicionar uma chave de ApplicationGroupContainerIdentifier para o root. plist")](settings-images/settings-appgroup.png#lightbox)

4. Editar o **Settings-Watch.bundle/Root.plist** para conter as opções que você deseja usar - o arquivo de modelo contém um grupo.
  campo de texto, switch de alternância e slider por padrão (que você pode excluir e substituir por suas próprias configurações):

  [![](settings-images/rootplist-sml.png "Editar o Settings-Watch.bundle/Root.plist")](settings-images/rootplist.png#lightbox)


## <a name="use-settings-in-the-watch-app"></a>Usar configurações no aplicativo de inspeção

Para acessar os valores selecionados pelo usuário, crie uma `NSUserDefaults` da instância usando o grupo de aplicativos e especificando `NSUserDefaultsType.SuiteName`:

```csharp
NSUserDefaults shared = new NSUserDefaults(
    "group.com.xamarin.WatchSettings",
    NSUserDefaultsType.SuiteName);

var isEnabled = shared.BoolForKey ("enabled_preference");
var userName = shared.StringForKey ("name_preference");
```

## <a name="apple-watch-app"></a>Aplicativo de Apple Watch

[![](settings-images/settings-app-sml.png "O novo aplicativo de Apple Watch no iPhone")](settings-images/settings-app.png#lightbox)

Os usuários interagirão com as configurações por meio do novo **Apple Watch** aplicativo em seu iPhone. Esse aplicativo permite que o usuário Mostrar/ocultar aplicativos na inspeção e também editar as configurações expostas usando o **Watch.bundle configurações**.

![](settings-images/applewatch-1.png "Exemplo de configurações do aplicativo") ![](settings-images/applewatch-2.png "exemplo de configurações de aplicativo")



## <a name="related-links"></a>Links relacionados

- [Documento de configurações da Apple](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/Settings.html#//apple_ref/doc/uid/TP40014969-CH22-SW1)
