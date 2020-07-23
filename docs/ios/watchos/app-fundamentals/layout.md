---
title: Trabalhando com o layout watchOS no Xamarin
description: Este documento descreve como criar um layout watchOS usando o Xamarin. Ele aborda os controladores de interface, grupos, separadores e controles de conteúdo.
ms.prod: xamarin
ms.assetid: BEDB62A1-2249-4459-986F-413A41E63DF0
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 345c05a439423474644ac64ef86f9adc580ab0b1
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86937716"
---
# <a name="working-with-watchos-layout-in-xamarin"></a>Trabalhando com o layout watchOS no Xamarin

A criação de layouts para os [tamanhos de tela](~/ios/watchos/app-fundamentals/screen-sizes.md) de Apple Watch apresenta desafios exclusivos.

## <a name="design-tips"></a>Dicas de design

O ponto principal é: tornar sua interface do usuário legível e utilizável em uma tela pequena de inspeção, com um grande dedo. Não se enquadrar na interceptação de design para o **simulador de Ios** (que parece muito grande) e um **ponteiro do mouse** (que funciona com pequenos destinos de toque)!

- Usar um plano de fundo preto – cria a ilusão de uma tela maior com o painel preto da inspeção.

- Não preencha o layout da tela-o painel forma um preenchimento Visual natural.

- Concentre-se na legibilidade. Use cores e tamanhos de fonte criteriosamente para garantir que o texto seja legível. Use os estilos de texto internos para obter suporte automático de tipo dinâmico.

![Exemplo de suporte de tipo dinâmico](layout-images/type.png)

- Concentre-se em tamanhos de destino de toque. As linhas da tabela Buttons/tappable com rótulos de texto devem abranger a tela inteira. A Apple diz "Nunca coloque mais de três itens lado a lado" e, se você usar ícones e não rótulos de texto.

- Use o [ `Menu` controle](~/ios/watchos/user-interface/menu.md) para expor a funcionalidade usada com menos frequência para manter seu design de aplicativo claro e conciso.

## <a name="implementation"></a>Implementação

O Watch kit inclui os seguintes controles para ajudá-lo a criar layouts de aplicativo atraentes de inspeção:

### <a name="interface-controller"></a>Controlador de interface

O `WKInterfaceController` é a classe base de todos os seus bastidores.

A superfície de design do controlador de interface se comporta como um **grupo**vertical: você pode arrastar outros controles para o controlador de interface e eles serão automaticamente dispostos um acima do outro:

![Os controles são automaticamente dispostos um acima do outro](layout-images/controller-scene.png)

Você pode definir as propriedades **Position** e **size** em cada controle para controlar sua aparência:

![Definir as propriedades de posição e de tamanho em cada controle](layout-images/positionsize-attributes.png)

Quando o tamanho é definido como **relativo ao contêiner** , você pode fornecer um valor proporcional e um ajuste de deslocamento. Esta captura de tela mostra um botão que foi definido para usar 80% da largura da tela de inspeção (**0,8**):

![Fornecer um valor proporcional e um ajuste de deslocamento](layout-images/button-attributes.png)

### <a name="group"></a>Agrupar

`WKInterfaceGroup`é um contêiner de layout simples que pode ser configurado para empilhar controles vertical ou horizontalmente. Ele inclui o espaçamento entre cada controle por padrão, mas você pode modificar o espaçamento (e as indefinições) no Inspetor de **atributos** .

![Modificar o espaçamento e as indefinições no Inspetor de atributos](layout-images/group-attributes.png)

Os grupos podem ser dimensionados e posicionados em relação aos controles ao seu respeito, e os grupos podem ser aninhados para criar layouts complexos.

![Os grupos podem ser aninhados para criar layouts complexos](layout-images/group-scene.png)

### <a name="separator"></a>Separador

O controle Separator destina-se a ajudar a fornecer diretrizes visuais em seu layout. Use separadores (ou cores ou imagens de plano de fundo) para ajudar o usuário a entender qual conteúdo está relacionado na tela.

![Exemplo de uso de separador](layout-images/separator-scene.png)

Observe que os separadores azuis e verdes que não usam a largura total da tela foram configurados com os tamanhos **fixo** ou **relativo aos contêineres** .

### <a name="content-controls"></a>Controles de conteúdo

Nenhum layout seria completo sem os `Label` controles, `Image` ,,,, `Button` `Switch` `Slider` `Map` e [outros](~/ios/watchos/user-interface/index.md).
Eles podem ser posicionados em seus layouts usando **grupos** ou as configurações de posição e tamanho em cada controle.

## <a name="related-links"></a>Links Relacionados

- [WatchKitCatalog (exemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [Referência de layout da Apple](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/Layout.html)
- [Referência de tipografia de & de cor da Apple](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/ColorandTypography.html)
