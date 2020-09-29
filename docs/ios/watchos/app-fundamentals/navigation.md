---
title: Trabalhando com a navegação do watchOS no Xamarin
description: Este documento descreve como trabalhar com navegação em um aplicativo watchOS. Ele aborda interfaces restritas, navegação hierárquica e interfaces baseadas em página.
ms.prod: xamarin
ms.assetid: 71A64C10-75C8-4159-A547-6A704F3B5C2E
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: a44d68426ff03ba0b6ab41f57e339caebce62c39
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436716"
---
# <a name="working-with-watchos-navigation-in-xamarin"></a>Trabalhando com a navegação do watchOS no Xamarin

A opção de navegação mais simples disponível no Watch é um [popup modal](#modal) simples que aparece na parte superior da cena atual.

Para aplicativos de inspeção de várias cenas, há dois paradigmas de navegação disponíveis:

- [Navegação hierárquica](#Hierarchical_Navigation)
- [Interfaces baseadas em página](#Page-Based_Interfaces)

<a name="modal"></a>

## <a name="modal-interfaces"></a>Interfaces modais

Use o `PresentController` método para abrir um controlador de interface de forma modal. O controlador de interface já deve estar definido na **interface. Storyboard**.

```csharp
PresentController ("pageController","some context info");
```

Os controladores apresentados de moda restrita usam a tela inteira (cobrindo a cena anterior). Por padrão, o título é definido como **Cancelar** e tocar nele irá ignorar o controlador.

Para fechar programaticamente o controlador apresentado de modo restrito, chame `DismissController` .

```csharp
DismissController();
```

As telas modais podem ser uma única cena ou usar um layout baseado em página.

<a name="Hierarchical_Navigation"></a>

## <a name="hierarchical-navigation"></a>Navegação hierárquica

Apresenta cenas como uma pilha que pode ser navegada de volta, de forma semelhante à maneira como `UINavigationController` funciona no Ios. Os bastidores podem ser enviados para a pilha de navegação e desativados (de forma programática ou por seleção de usuário).

![Os bastidores podem ser enviados para a pilha de navegação](navigation-images/hierarchy-1.png) ![Os bastidores podem ser retirados da pilha de navegação](navigation-images/hierarchy-2.png)

Assim como no iOS, um dedo para a borda esquerda navega de volta para o controlador pai em uma pilha de navegação hierárquica.

Os exemplos de [WatchKitCatalog](/samples/xamarin/ios-samples/watchos-watchkitcatalog) e [WatchTables](/samples/xamarin/ios-samples/watchos-watchtables) incluem navegação hierárquica.

### <a name="pushing-and-popping-in-code"></a>Enviando e exibindo código

O kit de inspeção não exige um "controlador de navegação" mais preciso para ser criado, como o iOS, basta enviar por push um controlador usando o `PushController` método e uma pilha de navegação será criada automaticamente.

```csharp
PushController("secondPageController","some context info");
```

A tela do observador incluirá um botão **voltar** no canto superior esquerdo, mas você também poderá remover programaticamente uma cena da pilha de navegação usando `PopController` .

```csharp
PopController();
```

Assim como no iOS, também é possível retornar à raiz da pilha de navegação usando `PopToRootController` .

```csharp
PopToRootController();
```

### <a name="using-segues"></a>Usando continuações

Continuações pode ser criado entre cenas no storyboard para definir a navegação hierárquica. Para obter o contexto para a cena de destino, o sistema operacional chama `GetContextForSegue` para inicializar o novo controlador de interface.

```csharp
public override NSObject GetContextForSegue (string segueIdentifier)
{
  if (segueIdentifier == "mySegue") {
    return new NSString("some context info");
  }
  return base.GetContextForSegue (segueIdentifier);
}
```

<a name="Page-Based_Interfaces"></a>

## <a name="page-based-interfaces"></a>Interfaces baseadas em página

As interfaces baseadas em página passem da esquerda para a direita, de forma semelhante à maneira como `UIPageViewController` funciona no Ios. Os pontos de indicador são exibidos na parte inferior da tela para mostrar qual página está sendo exibida no momento.

![Primeira página de exemplo](navigation-images/paged-1.png) ![Segunda página de exemplo](navigation-images/paged-2.png) ![Exemplo da quinta página](navigation-images/paged-5.png)

Para fazer uma interface baseada em página a interface do usuário principal para seu aplicativo Watch, use `ReloadRootControllers` com uma matriz de controladores de interface e contextos:

```csharp
var controllerNames = new [] { "pageController", "pageController", "pageController", "pageController", "pageController" };
var contexts = new [] { "First", "Second", "Third", "Fourth", "Fifth" };
ReloadRootControllers (controllerNames, contexts);
```

Você também pode apresentar um controlador baseado em página que não é a raiz usando `PresentController` uma das outras cenas em um aplicativo.

```csharp
var controllerNames = new [] { "pageController", "pageController", "pageController", "pageController", "pageController" };
var contexts = new [] { "First", "Second", "Third", "Fourth", "Fifth" };
PresentController (controllerNames, contexts);
```

## <a name="related-links"></a>Links Relacionados

- [WatchKitCatalog (exemplo)](/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [WatchTables (exemplo)](https://developer.xamarin.com//samples/monotouch/watchOS/WatchTables/)