---
title: watchOS controle de Menu (forçar Touch) no Xamarin
description: Este documento descreve como usar o gesto de toque de força watchOS em Xamarin. Ele discute como responder a um toque de força, como adicionar um menu e alterar os itens de menu.
ms.prod: xamarin
ms.assetid: 5A7F83FB-9BC4-4812-92C5-CEC8DAE8211E
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 4b973b925b99189416087224644c376864c56871
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34791339"
---
# <a name="watchos-menu-control-force-touch-in-xamarin"></a>watchOS controle de Menu (forçar Touch) no Xamarin

Assista que Kit fornece um gesto de tocar força que dispara um menu, quando implementada em uma tela do aplicativo de inspeção.

![](menu-images/menu.png "Apple Watch mostrando um menu")
<!-- watch image courtesy of http://infinitapps.com/bezel/ -->

## <a name="responding-to-force-touch"></a>Respondendo a força de toque

Se um `Menu` foi implementado para um controlador de interface, quando um usuário executa um toque de forçar o menu será exibido. Se nenhum menu foi implementada, a tela é animada rapidamente uma nenhuma outra ação ocorre.

Ajustes de força não estão associados um determinado elemento na tela; apenas um menu pode ser anexado a um controlador de interface e ela será exibida, independentemente de onde o pressionamento Force toque ocorre na tela.

Entre quatro e um menu de opções podem ser apresentadas.


## <a name="adding-a-menu"></a>Adicionar um Menu

Um `Menu` devem ser adicionados a um `InterfaceController` no storyboard em tempo de design. Quando um controle de menu é arrastado para um controlador de interface não há nenhuma indicação visual na visualização do storyboard, mas o **Menu** aparece no **esboço de documento** teclado:

![](menu-images/menu-action.png "Editando um menu no tempo de design")

Até quatro menu itens podem ser adicionados ao controle de menu. Eles podem ser configurados no **propriedades** preenchimento. Os seguintes atributos podem ser definidos:

- Título, e
- Imagem personalizada, ou
- Uma imagem do sistema: aceitar, adicionar, bloquear, recusar, Info, talvez, mais, mudo, pausar, reproduzir, repetir, retomar, compartilhamento, ordem aleatória, alto-falantes, Lixeira.

Criar um `Action` selecionando o **eventos** seção o **propriedades** teclado e digitando o nome do método de ação. Um método parcial será criado no código, que pode ser implementado na classe de controlador de interface, como este:

```csharp
partial void MenuItemTapped ()
{
    Console.WriteLine ("A menu item was tapped.");
}
```

### <a name="custom-images"></a>Imagens personalizadas

Semelhante às imagens de guia no iOS, imagens de item de menu exigem um opaco padrão com um canal alfa que permite que o plano de fundo Mostrar.

Você deve adicionar as imagens usadas para o menu ao projeto de aplicativo de inspeção (não no projeto de extensão inspecionar aplicativo) para melhor desempenho.


## <a name="changing-the-menu-items"></a>Alterar os itens de Menu

<!--
### Design Time Items

Menu items added the the storyboard can be shown and hidden programmatically.
-->

### <a name="adding-at-runtime"></a>Adicionando em tempo de execução

Você não pode fazer com que um `Menu` a ser adicionado a um controlador de interface em tempo de execução, embora a coleção de `MenuItem`s *pode* ser alterado por meio de programação.
Use o `AddMenuItem` método conforme mostrado:

```csharp
AddMenuItem (WKMenuItemIcon.Accept, "Yes", new ObjCRuntime.Selector ("tapped"));
```

A API do Kit de inspeção xamarin atualmente requer uma `selector` para o `AdMenuItem` método, que deve ser declarado como este:

```csharp
[Export("tapped")]
void MenuItemTapped ()
{
    Console.WriteLine ("The dynamically added 'Yes' menu item was tapped.");
}
```

### <a name="removing-at-runtime"></a>Removendo no tempo de execução

O `ClearAllMenuItems` método pode ser chamado para remover todos os *adicionado programaticamente* itens de menu.

Itens de menu configurados no storyboard não podem ser desmarcados.



## <a name="related-links"></a>Links relacionados

- [WatchKitCatalog (exemplo)](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [Doc de Menu da Apple](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/Menus.html)
