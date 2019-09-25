---
title: Controle de menu watchOS (Force Touch) no Xamarin
description: Este documento descreve como usar o gesto de toque watchOS Force no Xamarin. Ele aborda como responder a um toque forçado, como adicionar um menu e alterar os itens de menu.
ms.prod: xamarin
ms.assetid: 5A7F83FB-9BC4-4812-92C5-CEC8DAE8211E
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/17/2017
ms.openlocfilehash: c37d8592b7aadc2c88c31826bc954abfa3c0836d
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2019
ms.locfileid: "70766797"
---
# <a name="watchos-menu-control-force-touch-in-xamarin"></a>Controle de menu watchOS (Force Touch) no Xamarin

O kit de inspeção fornece um gesto de Force Touch que dispara um menu quando implementado em uma tela de aplicativo de inspeção.

![](menu-images/menu.png "Apple Watch mostrando um menu")
<!-- watch image courtesy of http://infinitapps.com/bezel/ -->

## <a name="responding-to-force-touch"></a>Respondendo a Force Touch

Se um `Menu` tiver sido implementado para um controlador de interface, quando um usuário executar uma Force Touch menu será exibido. Se nenhum menu tiver sido implementado, a tela será animada rapidamente, não ocorrerá nenhuma outra ação.

Os toques de força não estão associados a nenhum elemento específico na tela; somente um menu pode ser anexado a um controlador de interface e aparecerá independentemente de onde o Force Touch pressionar ocorre na tela.

Entre uma e quatro opções de menu podem ser apresentadas.

## <a name="adding-a-menu"></a>Adicionando um menu

Um `Menu` deve ser adicionado a um `InterfaceController` no storyboard em tempo de design. Quando um controle de menu é arrastado para um controlador de interface, não há nenhuma indicação visual na visualização do storyboard, mas o **menu** é exibido no bloco de **Tópicos do documento** :

![](menu-images/menu-action.png "Editando um menu em tempo de design")

Até quatro itens de menu podem ser adicionados ao controle de menu. Eles podem ser configurados no painel de **Propriedades** . Os seguintes atributos podem ser definidos:

- Título e
- Imagem personalizada ou
- Uma imagem do sistema: Aceitar, adicionar, bloquear, recusar, informações, talvez, mais, sem áudio, pausar, reproduzir, repetir, retomar, compartilhar, embaralhar, palestrante, lixeira.

Crie um `Action` selecionando a seção **eventos** do painel de **Propriedades** e digitando o nome do método de ação. Um método parcial será criado no código, que pode ser implementado na classe do controlador de interface, desta forma:

```csharp
partial void MenuItemTapped ()
{
    Console.WriteLine ("A menu item was tapped.");
}
```

### <a name="custom-images"></a>Imagens personalizadas

Semelhante às imagens de tabulação no iOS, as imagens de item de menu exigem um padrão opaco com um canal alfa que permite que o plano de fundo apareça.

Você deve adicionar as imagens usadas para o menu ao projeto do aplicativo Watch (não o projeto de extensão do aplicativo Watch) para obter o melhor desempenho.

## <a name="changing-the-menu-items"></a>Alterando os itens de menu

<!--
### Design Time Items

Menu items added the storyboard can be shown and hidden programmatically.
-->

### <a name="adding-at-runtime"></a>Adicionando em tempo de execução

Você não pode fazer `Menu` com que um seja adicionado a um controlador de interface em tempo de execução `MenuItem`, embora a coleção de s *possa* ser alterada programaticamente.
Use o `AddMenuItem` método conforme mostrado:

```csharp
AddMenuItem (WKMenuItemIcon.Accept, "Yes", new ObjCRuntime.Selector ("tapped"));
```

A API do kit de inspeção do Xamarin. Ios `selector` atualmente requer `AdMenuItem` um para o método, que deve ser declarado da seguinte maneira:

```csharp
[Export("tapped")]
void MenuItemTapped ()
{
    Console.WriteLine ("The dynamically added 'Yes' menu item was tapped.");
}
```

### <a name="removing-at-runtime"></a>Removendo em tempo de execução

O `ClearAllMenuItems` método pode ser chamado para remover todos os itens de menu *adicionados programaticamente* .

Os itens de menu configurados no storyboard não podem ser apagados.

## <a name="related-links"></a>Links relacionados

- [WatchKitCatalog (amostra)](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [Documento de menu da Apple](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/Menus.html)
