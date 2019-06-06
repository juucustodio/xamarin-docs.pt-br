---
title: Trabalhando com o watchOS navegação no Xamarin
description: Este documento descreve como trabalhar com navegação em um aplicativo do watchOS. Ele aborda as interfaces modais, navegação hierárquica e interfaces com base na página.
ms.prod: xamarin
ms.assetid: 71A64C10-75C8-4159-A547-6A704F3B5C2E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 264bdcc9a7a0d73ebe3385246ee6dfae3d9a850f
ms.sourcegitcommit: d3f48bfe72bfe03aca247d47bc64bfbfad1d8071
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66740988"
---
# <a name="working-with-watchos-navigation-in-xamarin"></a>Trabalhando com o watchOS navegação no Xamarin

A opção mais simples navegação disponível no relógio é um simples [popup modal](#modal) que aparece na parte superior a cena atual.

Para aplicativos do watch de cena com vários lá são dois paradigmas de navegação disponíveis:

- [Navegação hierárquica](#Hierarchical_Navigation)
- [Interfaces com base na página](#Page-Based_Interfaces)

<a name="modal"/>

## <a name="modal-interfaces"></a>Interfaces modais

Use o `PresentController` método para abrir um controlador de interface de forma restrita. O controlador de interface já deve ser definido na **Interface.storyboard**.

```csharp
PresentController ("pageController","some context info");
```

Controladores modalmente apresentado usam a tela inteira (que abrangem a cena anterior). Por padrão, o título é definido como **Cancelar** e tocando nele irá ignorar o controlador.

Para fechar o controlador modalmente apresentadas de forma programática, chamada `DismissController`.

```csharp
DismissController();
```

Telas modais podem ser uma única cena ou use um layout de página.

<a name="Hierarchical_Navigation"/>

## <a name="hierarchical-navigation"></a>Navegação hierárquica

Apresenta as cenas como uma pilha que pode ser navegado por meio, semelhante à forma como `UINavigationController` funciona no iOS. Segundo plano pode ser enviado para a pilha de navegação e retirados (programaticamente ou por seleção do usuário).

![](navigation-images/hierarchy-1.png "Segundo plano pode ser enviado para a pilha de navegação") ![](navigation-images/hierarchy-2.png "segundo plano pode ser exibido da pilha de navegação")

Assim como acontece com iOS, um left-edge-passar o dedo navega de volta para o controlador pai em uma pilha de Navegação hierárquica.

Os dois os [WatchKitCatalog](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/) e [WatchTables](https://developer.xamarin.com/samples/monotouch/watchOS/WatchTables/) os exemplos incluem Navegação hierárquica.

### <a name="pushing-and-popping-in-code"></a>Enviar por push e pop em código

Assista Kit não exige um excesso arqueada "controlador de navegação" a ser criado como faz o iOS - simplesmente enviar por push usando um controlador de `PushController` método e uma pilha de navegação serão criados automaticamente.

```csharp
PushController("secondPageController","some context info");
```

Tela do watch incluirá uma **volta** botão na parte superior esquerda, mas você pode remover também programaticamente uma cena da pilha de navegação usando `PopController`.

```csharp
PopController();
```

Como com o iOS, também é possível retornar para a raiz da pilha de navegação usando `PopToRootController`.

```csharp
PopToRootController();
```

### <a name="using-segues"></a>Usando Segues

Segues pode ser criada entre as cenas no storyboard para definir a navegação hierárquica. Para obter o contexto para a cena de destino, as chamadas do sistema operacional `GetContextForSegue` para inicializar o novo controlador de interface.

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

## <a name="page-based-interfaces"></a>Interfaces com base na página

Interfaces com base na página passe o dedo para esquerda para a direita, semelhante à forma `UIPageViewController` funciona no iOS. Pontos de indicador são exibidos na parte inferior da tela para mostrar qual página está sendo exibida.

![](navigation-images/paged-1.png "Exemplo de primeira página") ![](navigation-images/paged-2.png "página segundo exemplo") ![](navigation-images/paged-5.png "quinta página de exemplo")


Para tornar uma interface baseada em página de interface do usuário principal para o aplicativo de inspeção, use `ReloadRootControllers` com uma matriz de controladores de interface e contextos:

```csharp
var controllerNames = new [] { "pageController", "pageController", "pageController", "pageController", "pageController" };
var contexts = new [] { "First", "Second", "Third", "Fourth", "Fifth" };
ReloadRootControllers (controllerNames, contexts);
```

Você também pode apresentar um controlador baseado em página que não é a raiz usando `PresentController` de um dos outros plano em um aplicativo.

```csharp
var controllerNames = new [] { "pageController", "pageController", "pageController", "pageController", "pageController" };
var contexts = new [] { "First", "Second", "Third", "Fourth", "Fifth" };
PresentController (controllerNames, contexts);
```



## <a name="related-links"></a>Links relacionados

- [WatchKitCatalog (amostra)](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [WatchTables (amostra)](https://developer.xamarin.com//samples/monotouch/watchOS/WatchTables/)
