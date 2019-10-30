---
title: Trabalhando com o layout watchOS no Xamarin
description: Este documento descreve como criar um layout watchOS usando o Xamarin. Ele aborda os controladores de interface, grupos, separadores e controles de conteúdo.
ms.prod: xamarin
ms.assetid: BEDB62A1-2249-4459-986F-413A41E63DF0
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 568d1e354d0ee840aeed980d6e8cc6b83068a1c8
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73001538"
---
# <a name="working-with-watchos-layout-in-xamarin"></a>Trabalhando com o layout watchOS no Xamarin

A criação de layouts para os [tamanhos de tela](~/ios/watchos/app-fundamentals/screen-sizes.md) de Apple Watch apresenta desafios exclusivos.

## <a name="design-tips"></a>Dicas de design

O ponto principal é: tornar sua interface do usuário legível e utilizável em uma tela pequena de inspeção, com um grande dedo. Não se enquadrar na interceptação de design para o **simulador de Ios** (que parece muito grande) e um **ponteiro do mouse** (que funciona com pequenos destinos de toque)!

- Usar um plano de fundo preto – cria a ilusão de uma tela maior com o painel preto da inspeção.

- Não preencha o layout da tela-o painel forma um preenchimento Visual natural.

- Concentre-se na legibilidade. Use cores e tamanhos de fonte criteriosamente para garantir que o texto seja legível. Use os estilos de texto internos para obter suporte automático de tipo dinâmico.

![](layout-images/type.png "Example of Dynamic Type support")

- Concentre-se em tamanhos de destino de toque. As linhas da tabela Buttons/tappable com rótulos de texto devem abranger a tela inteira. A Apple diz "Nunca coloque mais de três itens lado a lado" e, se você usar ícones e não rótulos de texto.

- Use o [controle de`Menu`](~/ios/watchos/user-interface/menu.md) para expor a funcionalidade usada com menos frequência para manter seu design de aplicativo claro e conciso.

## <a name="implementation"></a>Implementação

O Watch kit inclui os seguintes controles para ajudá-lo a criar layouts de aplicativo atraentes de inspeção:

### <a name="interface-controller"></a>Controlador de interface

A `WKInterfaceController` é a classe base de todos os seus bastidores.

A superfície de design do controlador de interface se comporta como um **grupo**vertical: você pode arrastar outros controles para o controlador de interface e eles serão automaticamente dispostos um acima do outro:

![](layout-images/controller-scene.png "Controls are automatically laid-out one above the other")

Você pode definir as propriedades **Position** e **size** em cada controle para controlar sua aparência:

![](layout-images/positionsize-attributes.png "Set the Position and Size properties on each control")

Quando o tamanho é definido como **relativo ao contêiner** , você pode fornecer um valor proporcional e um ajuste de deslocamento. Esta captura de tela mostra um botão que foi definido para usar 80% da largura da tela de inspeção (**0,8**):

![](layout-images/button-attributes.png "Provide a proportional value and an offset adjustment")

### <a name="group"></a>Grupo

`WKInterfaceGroup` é um contêiner de layout simples que pode ser configurado para empilhar controles vertical ou horizontalmente. Ele inclui o espaçamento entre cada controle por padrão, mas você pode modificar o espaçamento (e as indefinições) no Inspetor de **atributos** .

![](layout-images/group-attributes.png "Modify the spacing and insets in the Attributes inspector")

Os grupos podem ser dimensionados e posicionados em relação aos controles ao seu respeito, e os grupos podem ser aninhados para criar layouts complexos.

![](layout-images/group-scene.png "Groups can be nested to create complex layouts")

### <a name="separator"></a>Separador

O controle Separator destina-se a ajudar a fornecer diretrizes visuais em seu layout. Use separadores (ou cores ou imagens de plano de fundo) para ajudar o usuário a entender qual conteúdo está relacionado na tela.

![](layout-images/separator-scene.png "Example of Separator usage")

Observe que os separadores azuis e verdes que não usam a largura total da tela foram configurados com os tamanhos **fixo** ou **relativo aos contêineres** .

### <a name="content-controls"></a>Controles de conteúdo

Nenhum layout seria completo sem o `Label`, `Image`, `Button`, `Switch`, `Slider`, `Map`e [outros controles](~/ios/watchos/user-interface/index.md).
Eles podem ser posicionados em seus layouts usando **grupos** ou as configurações de posição e tamanho em cada controle.

## <a name="related-links"></a>Links relacionados

- [WatchKitCatalog (exemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [Referência de layout da Apple](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/Layout.html)
- [Referência de tipografia de & de cor da Apple](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/ColorandTypography.html)
