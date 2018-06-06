---
title: Trabalhando com watchOS navegação no Xamarin
description: Este documento descreve como trabalhar com navegação em um aplicativo watchOS. Ele discute modal, navegação hierárquica e a baseada em página interfaces.
ms.prod: xamarin
ms.assetid: 71A64C10-75C8-4159-A547-6A704F3B5C2E
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: c9bcfc388164060549ca7010d11671abfa8230ac
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34790634"
---
# <a name="working-with-watchos-navigation-in-xamarin"></a>Trabalhando com watchOS navegação no Xamarin

A opção mais simples navegação disponível no watch é um simples [pop-up modal](#modal) que aparece na parte superior da cena atual.

Para inspecionar cena vários aplicativos existe são dois paradigmas de navegação disponíveis:

- [Navegação hierárquica](#Hierarchical_Navigation)
- [Interfaces de página](#Page-Based_Interfaces)

<a name="modal"/>

## <a name="modal-interfaces"></a>Interfaces modais

Use o `PresentController` método para abrir um controlador de interface restrito. O controlador de interface já deve ser definido no **Interface.storyboard**.

```csharp
PresentController ("pageController","some context info");
```

Controladores modalmente apresentados usam a tela inteira (que abrangem a cena anterior). Por padrão, o título é definido como **Cancelar** e tocar ele irá ignorar o controlador.

Para fechar o controlador modalmente apresentados programaticamente, chame `DismissController`.

```csharp
DismissController();
```

Telas modais podem ser uma única cena ou use um layout de página.

<a name="Hierarchical_Navigation"/>

## <a name="hierarchical-navigation"></a>Navegação hierárquica

Apresenta o segundo plano como uma pilha que pode ser navegado através de, semelhante à forma como `UINavigationController` funciona em iOS. Segundo plano pode ser inserido na pilha de navegação e retirados (programaticamente ou por seleção de usuário).

![](navigation-images/hierarchy-1.png "Segundo plano pode ser enviado para a pilha de navegação") ![ ] (navigation-images/hierarchy-2.png "segundo plano pode ser exibido da pilha de navegação")

Assim como acontece com iOS, dedo esquerda-borda navega de volta para o controlador pai em uma pilha de Navegação hierárquica.

Ambos os [WatchKitCatalog](https://developer.xamarin.com/samples/WatchKitCatalog) e [WatchTables](https://developer.xamarin.com/samples/WatchTables) exemplos incluem a navegação hierárquica.

### <a name="pushing-and-popping-in-code"></a>Enviar por push e pop no código

Assista Kit não requer um excesso arqueada "controlador de navegação" a ser criado como o iOS - simplesmente push a um controlador usando o `PushController` método e uma pilha de navegação serão criados automaticamente.

```csharp
PushController("secondPageController","some context info");
```

Tela de inspecionar incluirá um **novamente** botão no canto superior esquerdo, mas você pode remover também programaticamente uma cena da pilha de navegação usando `PopController`.

```csharp
PopController();
```

Assim como com iOS, também é possível retornar para a raiz da pilha de navegação usando `PopToRootController`.

```csharp
PopToRootController();
```

### <a name="using-segues"></a>Usando Segues

Segues pode ser criada entre cenas no storyboard para definir a navegação hierárquica. Para obter o contexto da cena de destino, as chamadas do sistema operacional `GetContextForSegue` para inicializar o novo controlador de interface.

```csharp
public override NSObject GetContextForSegue (string segueIdentifier)
{
  if (segueIdentifier == "mySegue") {
    return new NSString("some context info");
  }
  return base.GetContextForSegue (segueIdentifier);
}
```
<a name="Page-Based_Interfaces"/>

## <a name="page-based-interfaces"></a>Interfaces de página

Interfaces com base em página passe à esquerda para a direita, semelhante ao modo `UIPageViewController` funciona em iOS. Pontos de indicador são exibidos na parte inferior da tela para mostrar a página é exibida no momento.

![](navigation-images/paged-1.png "Exemplo de primeira página") ![ ] (navigation-images/paged-2.png "página segundo exemplo") ![ ] (navigation-images/paged-5.png "quinta página de exemplo")


Para fazer a interface do usuário principal de seu aplicativo de inspeção de uma interface baseada em página, use `ReloadRootControllers` com uma matriz de controladores de interface e contextos:

```csharp
var controllerNames = new [] { "pageController", "pageController", "pageController", "pageController", "pageController" };
var contexts = new [] { "First", "Second", "Third", "Fourth", "Fifth" };
ReloadRootControllers (controllerNames, contexts);
```

Você também pode apresentar um controlador baseada na página que não é a raiz usando `PresentController` de um plano em um aplicativo.

```csharp
var controllerNames = new [] { "pageController", "pageController", "pageController", "pageController", "pageController" };
var contexts = new [] { "First", "Second", "Third", "Fourth", "Fifth" };
PresentController (controllerNames, contexts);
```



## <a name="related-links"></a>Links relacionados

- [WatchKitCatalog (exemplo)](https://developer.xamarin.com/samples/monotouch/WatchKit/WatchKitCatalog/)
- [WatchTables (exemplo)](https://developer.xamarin.com/samples/monotouch/WatchKit/WatchTables/)
