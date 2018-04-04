---
title: Trabalhando com Layout
ms.prod: xamarin
ms.assetid: BEDB62A1-2249-4459-986F-413A41E63DF0
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 38e26544a907ffcafdec38e3e2758d9bdac7b977
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="working-with-layout"></a>Trabalhando com Layout

Criando layouts para o Apple Watch [tamanhos de tela](~/ios/watchos/app-fundamentals/screen-sizes.md) apresenta desafios exclusivos.

## <a name="design-tips"></a>Dicas de design

O ponto-chave é: tornar sua interface de usuário legível e pode ser utilizada em uma tela pequena observação, com um grande dedo. Não se enquadram em interceptação de projetar para o **simulador de iOS** (que aparece muito grande) e um **ponteiro do mouse** (que funciona com os destinos de toque pequena)!

- Usar um plano de fundo preto - cria a ilusão de uma tela maior com painel preto do relógio.

- Não preenchimento ao redor de seu layout da tela - um preenchimento visual natural de formulários do painel frontal.

- Focalizar a legibilidade. Use tamanhos de fontes e cores cuidado para garantir texto legível. Use os estilos de texto interno para obter suporte automático de tipo dinâmico.

![](layout-images/type.png "Exemplo de suporte de tipo dinâmico")

- Se concentrar em tamanhos de destino de toque. Botões/tappable linhas da tabela com rótulos de texto devem ocupar a tela inteira. Apple diz "nunca colocar mais de três itens lado a lado", e se você usar ícones e não os rótulos de texto.

- Use o [ `Menu` controle](~/ios/watchos/user-interface/menu.md) a funcionalidade de expor com menos frequência usada para manter seu design de aplicativo claro e conciso.


## <a name="implementation"></a>Implementação

Assista que Kit inclui os seguintes controles para ajudá-lo a criar inspeção atraente layouts de aplicativo:

### <a name="interface-controller"></a>Controlador de interface

O `WKInterfaceController` é a classe base todas suas cenas.

A superfície de design para o controlador de interface se comporta como uma vertical **grupo**: você pode arrastar outros controles para o controlador de interface e eles serão apresentados em automaticamente um acima do outro:

![](layout-images/controller-scene.png "Os controles são apresentados em automaticamente um acima do outro")

Você pode definir o **posição** e **tamanho** propriedades em cada controle para controlar sua aparência:

![](layout-images/positionsize-attributes.png "Definir as propriedades de posição e tamanho de cada controle")

Quando o tamanho é definido como **relativo ao contêiner** você pode fornecer um valor proporcional e um deslocamento de ajuste. Esta captura de tela mostra um botão que foi definido para usar a 80% da largura da tela de inspeção (**0,8**):

![](layout-images/button-attributes.png "Forneça um valor proporcional e um deslocamento de ajuste")


### <a name="group"></a>Grupo

`WKInterfaceGroup` é um contêiner de layout simples que pode ser configurado para pilha controla verticalmente ou horizontalmente. Ele inclui o espaçamento entre cada controle por padrão, mas você pode modificar o espaçamento (e inserções) no **atributos** Inspetor.

![](layout-images/group-attributes.png "Modificar o espaçamento e inserções no Inspetor de atributos")

Grupos podem se ser dimensionados e posicionados em relação os controles ao redor deles, e os grupos podem ser aninhados para criar layouts complexos.

![](layout-images/group-scene.png "Grupos podem ser aninhados para criar layouts complexos")


### <a name="separator"></a>Separador

O controle de separador destina-se a ajudar a fornecer orientação visual no layout. Use separadores (ou cores de plano de fundo ou imagens) para ajudar o usuário a entender o conteúdo que está relacionado na tela.

![](layout-images/separator-scene.png "Exemplo de uso do separador")

Observe que os separadores de verdes e azuis que não usam a largura total da tela foram configurados com um **fixo** ou **relativo ao contêiner** tamanhos.

### <a name="content-controls"></a>Controles de conteúdo

Nenhum layout estaria completa sem a `Label`, `Image`, `Button`, `Switch`, `Slider`, `Map`, e [outros controles](~/ios/watchos/user-interface/index.md).
Eles podem ser posicionados em seus layouts usando **grupos** ou as configurações de tamanho e posição em cada controle.



## <a name="related-links"></a>Links relacionados

- [WatchKitCatalog (exemplo)](https://developer.xamarin.com/samples/monotouch/WatchKit/WatchKitCatalog/)
- [Referência de Layout da Apple](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/Layout.html)
- [Referenciam de tipografia & Cor da Apple](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/ColorandTypography.html)
