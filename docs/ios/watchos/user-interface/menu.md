---
title: Controle de menu watchOS (Force Touch) no Xamarin
description: Este documento descreve como usar o gesto de toque watchOS Force no Xamarin. Ele aborda como responder a um toque forçado, como adicionar um menu e alterar os itens de menu.
ms.prod: xamarin
ms.assetid: 5A7F83FB-9BC4-4812-92C5-CEC8DAE8211E
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 5ad45e1cc17a58875037f20996463f55f2b68685
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91431549"
---
# <a name="watchos-menu-control-force-touch-in-xamarin"></a>Controle de menu watchOS (Force Touch) no Xamarin

O kit de inspeção fornece um gesto de Force Touch que dispara um menu quando implementado em uma tela de aplicativo de inspeção.

![Apple Watch mostrando um menu](menu-images/menu.png)
<!-- watch image courtesy of http://infinitapps.com/bezel/ -->

## <a name="responding-to-force-touch"></a>Respondendo a Force Touch

Se um `Menu` tiver sido implementado para um controlador de interface, quando um usuário executar uma Force Touch menu será exibido. Se nenhum menu tiver sido implementado, a tela será animada rapidamente, não ocorrerá nenhuma outra ação.

Os toques de força não estão associados a nenhum elemento específico na tela; somente um menu pode ser anexado a um controlador de interface e aparecerá independentemente de onde o Force Touch pressionar ocorre na tela.

Entre uma e quatro opções de menu podem ser apresentadas.

## <a name="adding-a-menu"></a>Adicionando um menu

Um `Menu` deve ser adicionado a um `InterfaceController` no storyboard em tempo de design. Quando um controle de menu é arrastado para um controlador de interface, não há nenhuma indicação visual na visualização do storyboard, mas o **menu** é exibido no bloco de **Tópicos do documento** :

![Editando um menu em tempo de design](menu-images/menu-action.png)

Até quatro itens de menu podem ser adicionados ao controle de menu. Eles podem ser configurados no painel de **Propriedades** . Os seguintes atributos podem ser definidos:

- Título e
- Imagem personalizada ou
- Uma imagem do sistema: aceitar, adicionar, bloquear, recusar, informações, talvez, mais, sem áudio, pausar, reproduzir, repetir, retomar, compartilhar, embaralhar, palestrante, Trash.

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

Você não pode fazer com que um seja `Menu` adicionado a um controlador de interface em tempo de execução, embora a coleção de `MenuItem` s *possa* ser alterada programaticamente.
Use o `AddMenuItem` método conforme mostrado:

```csharp
AddMenuItem (WKMenuItemIcon.Accept, "Yes", new ObjCRuntime.Selector ("tapped"));
```

A API do kit de inspeção do Xamarin. iOS atualmente requer um `selector` para o `AdMenuItem` método, que deve ser declarado da seguinte maneira:

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

## <a name="related-links"></a>Links Relacionados

- [WatchKitCatalog (exemplo)](/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [Documento de menu da Apple](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/Menus.html)