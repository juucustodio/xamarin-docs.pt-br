---
title: Controle de Menu (Force Touch) no Xamarin do watchOS
description: Este documento descreve como usar o gesto de toque do watchOS força no Xamarin. Ele discute como responder a um toque de força, como adicionar um menu e alterar os itens de menu.
ms.prod: xamarin
ms.assetid: 5A7F83FB-9BC4-4812-92C5-CEC8DAE8211E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 7696c820ab6fdf19bdef46db31061fb5914e6cf4
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60880666"
---
# <a name="watchos-menu-control-force-touch-in-xamarin"></a>Controle de Menu (Force Touch) no Xamarin do watchOS

Assista que Kit fornece um gesto de tocar força que dispara um menu quando implementado em uma tela de aplicativo de inspeção.

![](menu-images/menu.png "Apple Watch mostrando um menu")
<!-- watch image courtesy of http://infinitapps.com/bezel/ -->

## <a name="responding-to-force-touch"></a>Responder ao toque de força

Se um `Menu` foi implementada para um controlador de interface, quando um usuário executa um toque de forçar o menu será exibido. Se nenhum menu foi implementada, a tela é animada rapidamente uma nenhuma outra ação ocorre.

Toques de força não estão associados a qualquer elemento específico na tela; menu apenas uma pode ser anexado a um controlador de interface e ela será exibida, independentemente de onde o pressionamento de força de toque ocorre na tela.

Entre quatro e um menu de opções podem ser apresentadas.


## <a name="adding-a-menu"></a>Adicionar um Menu

Um `Menu` devem ser adicionados a um `InterfaceController` no storyboard em tempo de design. Quando um controle de menu é arrastado para um controlador de interface não há nenhuma indicação visual sobre a visualização de storyboard, mas o **menus** aparece no **Document Outline** painel:

![](menu-images/menu-action.png "Editando um menu no tempo de design")

Até o menu de quatro itens podem ser adicionados ao controle de menu. Eles podem ser configurados na **propriedades** pad. Os seguintes atributos podem ser definidos:

- Título, e
- Imagem personalizada, ou
- Uma imagem do sistema: Aceitar, adicionar, bloco, recusar, Info, talvez, mais, mudo, Pause, Play, repetir, retomar, compartilhamento, em ordem aleatória, alto-falante, Lixeira.

Criar uma `Action` selecionando o **eventos** seção os **propriedades** pad e digitando o nome para o método de ação. Um método parcial será criado no código, que pode ser implementado na classe do controlador de interface, como este:

```csharp
partial void MenuItemTapped ()
{
    Console.WriteLine ("A menu item was tapped.");
}
```

### <a name="custom-images"></a>Imagens personalizadas

Semelhante às imagens de guia no iOS, imagens de item de menu exigem um opaco padrão com um canal alfa que permite que o plano de fundo Mostrar por meio do.

Você deve adicionar as imagens usadas para o menu para o projeto de aplicativo de inspeção (não no projeto de extensão watch aplicativo) para melhor desempenho.


## <a name="changing-the-menu-items"></a>Alterando os itens de Menu

<!--
### Design Time Items

Menu items added the storyboard can be shown and hidden programmatically.
-->

### <a name="adding-at-runtime"></a>Adicionando em tempo de execução

Você não pode fazer com que um `Menu` a ser adicionado a um controlador de interface em tempo de execução, embora a coleção de `MenuItem`s *pode* ser alterada programaticamente.
Use o `AddMenuItem` método conforme mostrado:

```csharp
AddMenuItem (WKMenuItemIcon.Accept, "Yes", new ObjCRuntime.Selector ("tapped"));
```

A API de Kit de inspeção do xamarin. IOS atualmente requer uma `selector` para o `AdMenuItem` método, que deve ser declarado como este:

```csharp
[Export("tapped")]
void MenuItemTapped ()
{
    Console.WriteLine ("The dynamically added 'Yes' menu item was tapped.");
}
```

### <a name="removing-at-runtime"></a>Removendo no tempo de execução

O `ClearAllMenuItems` método pode ser chamado para remover todos os *adicionados programaticamente* itens de menu.

Itens de menu configurados no storyboard não podem ser desmarcados.



## <a name="related-links"></a>Links relacionados

- [WatchKitCatalog (amostra)](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [Documento do Menu da Apple](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/Menus.html)
