---
title: Trabalhando com o watchOS Layout no Xamarin
description: Este documento descreve como criar um layout de watchOS usando o Xamarin. Ele aborda os controladores de interface, grupos, separadores e controles de conteúdo.
ms.prod: xamarin
ms.assetid: BEDB62A1-2249-4459-986F-413A41E63DF0
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: a714b8bd1984f22a138d09912a0a7fe1a51e857c
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68646816"
---
# <a name="working-with-watchos-layout-in-xamarin"></a>Trabalhando com o watchOS Layout no Xamarin

Design de layouts para o Apple Watch [tamanhos de tela](~/ios/watchos/app-fundamentals/screen-sizes.md) apresenta desafios únicos.

## <a name="design-tips"></a>Dicas de design

O ponto principal é: tornar sua interface do usuário, legível e pode ser utilizada em uma tela pequena de inspeção, com um dedo grande. Não cair na armadilha do design para o **simulador de iOS** (que aparece muito grande) e uma **ponteiro do mouse** (que funciona com os destinos de toque minúsculo)!

- Usar um plano de fundo preto, ele cria a ilusão de uma tela maior com o painel frontal de preto a inspeção.

- Não preencher em torno de seu layout de tela – o painel frontal do forms um preenchimento visual natural.

- Se concentrar na legibilidade. Usar tamanhos de fontes e cores com cuidado para garantir que o texto é legível. Use os estilos de texto interna para obter suporte automático de tipo dinâmico.

![](layout-images/type.png "Exemplo de suporte de tipo dinâmico")

- Se concentrar em tamanhos de destino de toque. Botões/tappable linhas da tabela com rótulos de texto devem ocupar a tela inteira. Apple diz "nunca colocar mais de três itens lado a lado", e se você usar ícones e não os rótulos de texto.

- Use o [ `Menu` controle](~/ios/watchos/user-interface/menu.md) à funcionalidade de expor com menos frequência usada para manter o design de seu aplicativo claro e conciso.


## <a name="implementation"></a>Implementação

Assista o que Kit inclui os seguintes controles para ajudar você a criar layouts de aplicativos de inspeção atraente:

### <a name="interface-controller"></a>Controlador de interface

O `WKInterfaceController` é a classe base todas suas cenas.

A superfície de design para o controlador de interface se comporta como uma vertical **grupo**: você pode arrastar outros controles para o controlador de interface e eles serão apresentados horizontalmente automaticamente um acima do outro:

![](layout-images/controller-scene.png "Os controles são apresentados automaticamente-out um acima do outro")

Você pode definir as **posição** e **tamanho** propriedades em cada controle para controlar sua aparência:

![](layout-images/positionsize-attributes.png "Definir as propriedades de posição e o tamanho em cada controle")

Quando o tamanho é definido como **relativo ao contêiner** você pode fornecer um valor proporcional e um deslocamento de ajuste. Esta captura de tela mostra um botão que foi definido para usar a 80% da largura da tela de inspeção (**0.8**):

![](layout-images/button-attributes.png "Forneça um valor proporcional e um deslocamento de ajuste")


### <a name="group"></a>Grupo

`WKInterfaceGroup` é um contêiner de layout simples que pode ser configurado para a pilha controla vertical ou horizontalmente. Ele inclui o espaçamento entre cada controle por padrão, mas você pode modificar o espaçamento (e inserções) na **atributos** Inspetor.

![](layout-images/group-attributes.png "Modificar o espaçamento e inserções no Inspetor de atributos")

Grupos podem em si ser dimensionados e posicionados em relação os controles ao redor deles, e os grupos podem ser aninhados para criar layouts complexos.

![](layout-images/group-scene.png "Grupos podem ser aninhados para criar layouts complexos")


### <a name="separator"></a>Separador

O controle de separador destina-se para ajudar a fornecer orientação visual em seu layout. Use separadores (ou cores de plano de fundo ou imagens) para ajudar o usuário a entender o conteúdo que está relacionado na tela.

![](layout-images/separator-scene.png "Exemplo de uso do separador")

Observe que os separadores de azuis e verdes que não usam a largura total da tela foram configurados com um **Fixed** ou **relativo ao contêiner** tamanhos.

### <a name="content-controls"></a>Controles de conteúdo

Não há nenhum layout estaria completa sem o `Label`, `Image`, `Button`, `Switch`, `Slider`, `Map`, e [outros controles](~/ios/watchos/user-interface/index.md).
Eles podem ser posicionados em seus layouts usando **grupos** ou as configurações de posição e o tamanho em cada controle.



## <a name="related-links"></a>Links relacionados

- [WatchKitCatalog (amostra)](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [Referência do Layout da Apple](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/Layout.html)
- [Fazer referência a tipografia & Cor da Apple](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/ColorandTypography.html)
