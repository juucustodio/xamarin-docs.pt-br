---
title: Alterações adicionais do tvOS 10 frameworks
description: Este documento descreve pequenas alterações e aprimoramentos feitos em estruturas existentes no iOS 10. Ele aborda as atualizações para AVFoundation, AVKit, dados principais, gráficos principais, base, GameKit, GameplayKit e muito mais.
ms.prod: xamarin
ms.assetid: F771640A-F92E-4954-82D5-2D720434971E
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 410b66e91168ed07da6f102d96b965f9e6a184f7
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84571630"
---
# <a name="additional-tvos-10-frameworks-changes"></a>Alterações adicionais do tvOS 10 frameworks

Além das principais alterações no tvOS, a Apple fez modificações e melhorias em várias estruturas existentes no tvOS 10.

<a name="AV-Foundation-Framework"></a>

## <a name="avfoundation-framework-additions"></a>Adições da estrutura AVFoundation

A estrutura AVFoundation inclui os seguintes aprimoramentos:

- No tvOS 10, o aplicativo não implementa mais comportamentos de [AVPlayerItem](https://developer.apple.com/reference/avfoundation/avplayeritem) diferentes com base no tipo de conteúdo. Basta definir a `Rate` propriedade e AVFoundation determinará quando o conteúdo suficiente está disponível para reprodução sem interrupções.
- A nova `AVPlayerLooper` classe torna mais fácil fazer um loop de uma determinada mídia durante a reprodução.

<a name="AVKit-Framework-Enhancements"></a>

## <a name="avkit-framework-enhancements"></a>Aprimoramentos do AVKit Framework

A estrutura AVKit inclui os seguintes aprimoramentos:

- O aplicativo agora tem controle sobre o comportamento de ignorar o [AVPlayerViewController](https://developer.apple.com/reference/avkit/avplayerviewcontroller) para que um gesto de ignorar possa mover para o próximo item na playlist ou avançar dentro do item atual.

<a name="Core-Data-Enhancements"></a>

## <a name="core-data-enhancements"></a>Principais aprimoramentos de dados

o tvOS 10 inclui os seguintes aprimoramentos para a estrutura de dados principal:

- Os objetos raiz [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) dão suporte à falha e busca simultâneas sem serialização.
- A classe [NSPersistentStoreCoordinator](https://developer.apple.com/reference/coredata/nspersistentstorecoordinator) mantém um pool de armazenamentos de dados do SQLite.
- Os objetos [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) com armazenamentos de dados SQLite no modo de diário Wal dão suporte ao novo recurso de geração de consulta em que os contextos de objeto gerenciado (MOC) podem ser fixados em versões de banco de dados específicas para futuras transações de busca e falha.
- Usando o alto nível `NSPersistenceContainer` para fazer referência a `NSPersistentStoreCoordinator` , [NSManagedObjectModel](https://developer.apple.com/reference/coredata/nsmanagedobjectmodel) e outros recursos principais de configuração de dados.
- Vários novos métodos de conveniência foram adicionados para `NSManagedObject` tornar mais fácil executar buscas e criar subclasses.

Para obter mais informações, consulte a [referência da estrutura de dados principal](https://developer.apple.com/reference/coredata)da Apple.

<a name="Core-Graphics-Enhancements"></a>

## <a name="core-graphics-enhancements"></a>Principais aprimoramentos de gráficos

o tvOS 10 inclui os seguintes aprimoramentos na estrutura de gráficos principal:

- A nova classe CGColorConverterRef pode ser usada para executar uma série de conversões de cor.

<a name="Core-Image-Enhancements"></a>

## <a name="core-image-enhancements"></a>Aprimoramentos da imagem principal

o tvOS 10 faz os seguintes aprimoramentos na estrutura de imagem principal:

- O `ImageWithExtent` método da classe [CIFilter](https://developer.apple.com/reference/coreimage/cifilter) pode ser usado para inserir o processamento personalizado na operação de filtro. A imagem principal invocará o retorno de chamada fornecido entre os filtros ao processar uma imagem para saída ou exibição.
- O aplicativo agora pode processar imagens em um espaço de cores fora do espaço de cores de trabalho do contexto de imagem principal, convertendo dentro e fora do espaço de cores antes e depois do processamento.
- Vários aprimoramentos de desempenho de renderização foram feitos para `UIImage` renderização (quando apoiado por armazenamentos de imagem de imagem de núcleo) em `UIImageView` objetos.
- `UIImage`objetos marcados com largura ampla serão renderizados como uma cor de ampla gama em `UIImageView` objetos em dispositivos IOS que dão suporte a cores amplas.
- O código do kernel da imagem de núcleo agora pode solicitar formatos de saída de pixel específicos.

Além disso, os seguintes novos filtros de imagem de núcleo foram adicionados:

- `CINinePartTiled`
- `CINinePartStretched`
- `CIHueSaturationValueGradient`
- `CIEdgePreserveUpsampleFilter`
- `CIClamp`

<a name="Foundation-Enhancements"></a>

## <a name="foundation-enhancements"></a>Aprimoramentos do Foundation

Os aprimoramentos a seguir foram feitos na estrutura de base para o tvOS 10:

- Use a nova classe [NSDateInterval](https://developer.apple.com/reference/foundation/nsdateinterval) para fazer cálculos de intervalo de data e hora, como durações, para comparar intervalos e testar as interseções de intervalo.
- Várias novas propriedades foram adicionadas à classe [NSLocal](https://developer.apple.com/reference/foundation/nslocale) para adquirir informações locais e os formatos de exibição disponíveis.
- Use a nova classe [NSMeasurement](https://developer.apple.com/reference/foundation/nsmeasurement) para converter entre diferentes unidades de medida (unidade ea) ou executar cálculos em valores em diferentes UOMs.
- Use a nova classe [NSMeasurementFormatter](https://developer.apple.com/reference/foundation/nsmeasurementformatter) para formatar as medidas localizadas para exibição para o usuário final.
- Use as novas classes [NSUnit](https://developer.apple.com/reference/foundation/nsunit) e [NSDimension](https://developer.apple.com/reference/foundation/nsdimension) para representar UOMs específicos.

<a name="GameKit-Enhancements"></a>

## <a name="gamekit-enhancements"></a>Aprimoramentos do GameKit

Os aprimoramentos a seguir foram feitos na estrutura GameKit no tvOS 10:

- Um novo tipo de conta somente iCloud foi implementado pela classe [GKCloudPlayer](https://developer.apple.com/reference/gamekit/gkcloudplayer) .
- A nova classe [GKGameSession](https://developer.apple.com/reference/gamekit/gkgamesession) fornece uma solução generalizada para gerenciar o armazenamento de dados persistente em Game Center. `GKGameSession`mantém uma lista de players e o aplicativo é o formulário responsável que implementa como e quando a data do participante é armazenada, recuperada ou trocada entre os jogadores. Em muitas instâncias, as sessões de jogos podem substituir as correspondências existentes, correspondências em tempo real ou métodos de salvamento de jogos persistentes.

<a name="GameplayKit-Enhancements"></a>

## <a name="gameplaykit-enhancements"></a>Aprimoramentos do GameplayKit

Os aprimoramentos a seguir foram feitos na estrutura GameplayKit no tvOS 10:

- A geração de ruído de procedimento foi adicionada e pode ser usada para aprimorar o realm nas texturas de aparência natural, adicionar Realm aos movimentos da câmera e ajudar a gerar mundos avançados de jogos.
- Use o particionamento espacial para particionar os dados do mundo do jogo para uma pesquisa eficiente.
- Um novo[GKMonteCarloStrategist](https://developer.apple.com/reference/gameplaykit/gkmontecarlostrategist)(Monte Carlo estrategista) foi adicionado para uma computação de movimentação exaustivamente possível.
- Uma nova API de árvore de decisão foi adicionada ([GKDecisionTree](https://developer.apple.com/reference/gameplaykit/gkdecisiontree) e [GKDecisionNode](https://developer.apple.com/reference/gameplaykit/gkdecisionnode)) para aprimorar o ia de criação de jogos.
- o suporte a 3D foi adicionado ao agente existente e aos comportamentos de localização de caminho usando as novas classes [GKAgent3D](https://developer.apple.com/reference/gameplaykit/gkagent3d) e [GKGraphNode3D](https://developer.apple.com/reference/gameplaykit/gkgraphnode3d) .
- Use a nova classe [GKMeshGraph](https://developer.apple.com/reference/gameplaykit/gkmeshgraph) para fornecer caminhos de aparência natural de alto desempenho.
- As novas classes [GKScene](https://developer.apple.com/reference/gameplaykit/gkscene) e [GKSKNodeComponent](https://developer.apple.com/reference/gameplaykit/gksknodecomponent) tornam a combinação de GameplayKit e SpriteKit mais fácil do que nunca.

<a name="Metal-Enhancements"></a>

## <a name="metal-enhancements"></a>Aprimoramentos de metal

Os aprimoramentos a seguir foram feitos na estrutura de metal no tvOS 10:

- os aplicativos e jogos 3D agora podem usar o _mosaico_ para renderizar com eficiência cenas e geometria complexas por meio da GPU.
- Use a especialização de função para criar uma coleção altamente otimizada de funções de combinação de material e leve para uma cena.
- Forneça um controle refinado de alocação de recursos para otimizar o desempenho de aplicativos baseados em metal usando heaps de recursos e destinos de renderização sem memória.

Para saber mais, confira o [Guia de programação de metal](https://developer.apple.com/library/prerelease/content/documentation/Miscellaneous/Conceptual/MetalProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014221)da Apple.

<a name="Metal-Performance-Shaders-Enhancements"></a>

## <a name="metal-performance-shaders-enhancements"></a>Aprimoramentos de sombreadores de desempenho de metal

Os aprimoramentos a seguir foram feitos na estrutura de sombreadores de desempenho de metal no tvOS 10:

- Muitos kernels novos foram adicionados à estrutura de sombreadores de desempenho de metal para permitir que o aplicativo Tire proveito de computações de dados paralelos de alta otimização, como conversões de espaço de cores e operações de rede neural.

<a name="ModelIO-Enhancements"></a>

## <a name="modelio-enhancements"></a>Aprimoramentos do ModelIO

Os aprimoramentos a seguir foram feitos na estrutura ModelIO no tvOS 10:

- Agora há suporte para o formato de arquivo USD.
- Use a nova `MDLMaterialPropertyGraph` classe para dar suporte facilmente a alterações de tempo de execução para modelos.
- O suporte ao campo de distância assinada foi adicionado à classe [MDLVoxelArray](https://developer.apple.com/reference/modelio/mdlvoxelarray) .
- Use a nova `MDLLightProbeIrradianceDataSource` classe para auxiliar no posicionamento da investigação leve.

<a name="SceneKit-Enhancements"></a>

## <a name="scenekit-enhancements"></a>Aprimoramentos do SceneKit

Os aprimoramentos a seguir foram feitos na estrutura SceneKit no tvOS 10:

- O SceneKit agora inclui um novo sistema de processamento com base em física (PBR) para resultados mais Realos com a criação de ativos mais simples.
- Use o novo modelo de sombreamento [SCNLightingModelPhysicallyBased](https://developer.apple.com/reference/scenekit/scnlightingmodelphysicallybased) para obter uma ampla gama de efeitos de sombreamento realista, ao mesmo tempo que exige apenas três propriedades fundamentais ( `Diffuse` `Metalness` e `Roughness` ).
- Como o sombreamento do PBR funciona melhor com iluminação baseada em ambiente, use a `LightingEnvironment` propriedade para atribuir a iluminação baseada em imagem à cena inteira de Tan.
- Use a `IESProfileURL` propriedade para importar os acessórios de luz do mundo real que definem a base de iluminação em valores reais, como intensidade (em lumens) e temperatura de cor (em graus Kelvin).
- A classe [SCNCamera](https://developer.apple.com/reference/scenekit/scncamera) pode fornecer maior Realm usando os recursos e efeitos HDR. Use a exposição adaptativa para criar efeitos automáticos ou use Vignetting, a borda da cor e a redução da cor para adicionar efeitos filmatics ao jogo.
- Os recursos de câmera de PBR e HDR fornecem resultados melhores do que as técnicas de renderização tradicionais e, como resultado, o SceneKit agora executa todos os cálculos de cor em um espaço de cores linear (usando a gama de cores P3 em monitores de dispositivos de cores largos).
- A cor SceneKit Now corresponde a todas as cores lendo as informações do perfil de cor.
- SceneKit interpreta valores de componente de cor em um espaço de cores RGB linear para todos os tipos de sombreador.
- Como o SceneKit lê e ajusta as informações de perfil de cor em imagens de textura, use catálogos de ativos para todas as imagens para garantir que essas informações sejam fornecidas.
- A renderização de espaço de cores linear e a grande cor podem ser desabilitadas especificando as `SCNDisableLinearSpaceRendering` `SCNDisableWideGamut` chaves e no aplicativo `Info.plist` .
- Crie Primates Polygon arbitrários (carregados a partir de arquivos ou gerados programaticamente) para especificar Geometry com a nova classe [SCNGeometryPrimitiveTypePolygon](https://developer.apple.com/documentation/scenekit/scngeometryprimitivetype/scngeometryprimitivetypepolygon) .

<a name="SpriteKit-Enhancements"></a>

## <a name="spritekit-enhancements"></a>Aprimoramentos do SpriteKit

Os aprimoramentos a seguir foram feitos na estrutura SpriteKit no tvOS 10:

- Tilemaps agora suportam as formas de peças quadradas, hexágonos e para 2D, 2,5 d e rolagem para jogos usando as `SKTileMapMode` `SKTileGroup` `SKTileGroupRule` classes, e `SKTileSet` .
- Use a nova `SKWarpGeometry` classe para alongar ou distorcer a renderização [SKSpriteNode](https://developer.apple.com/reference/spritekit/skspritenode) ou [SKEffectNode](https://developer.apple.com/reference/spritekit/skeffectnode) . A nova classe [SKAction](https://developer.apple.com/reference/spritekit/skaction) pode ser usada para animar transições entre efeitos de distorção.
- Os sombreadores personalizados podem fornecer atributos ( `SKAttribute` ) que podem ser configurados separadamente por cada nó que usa o sombreador fornecendo um valor de atributo ( `SKAttributeValue` ).
- A classe [SKView](https://developer.apple.com/reference/spritekit/skview) fornece vários novos métodos para fornecer um controle refinado sobre quando e como uma cena é renderizada.

<a name="UIKit-Enhancements"></a>

## <a name="uikit-enhancements"></a>Aprimoramentos do UIKit

Os aprimoramentos a seguir foram feitos na estrutura UIKit no tvOS 10:

- A API de foco foi aprimorada para dar suporte ao foco de item que não é de exibição, além de `UIViews` . Os itens que dão suporte ao foco _devem_ implementar a `IUIFocusItem` interface.
- A nova `UIGraphicsRender` classe fornece um método orientado a objeto de criação de bitmaps ou PDFs de renderização UIKit ou gráficos de núcleo e substitui o método preterido `UIGraphicsBeginImageContext` .
- A `UIUserInterfaceStyle` classe foi adicionada para determinar qual tema da interface do usuário (escuro ou claro) está ativo no momento.
- O novo suporte à animação com interrupções, com base em objeto e totalmente interativo, foi adicionado e a van está vinculada a gestos. Pleas consulte referência de [protocolo UIViewAnimating](https://developer.apple.com/reference/uikit/uiviewanimating)da Apple [, referência de classe UIViewPropertyAnimator](https://developer.apple.com/reference/uikit/uiviewpropertyanimator), referência de [protocolo UITimingCurveProvider](https://developer.apple.com/reference/uikit/uitimingcurveprovider), [referência de classe UICubicTimingParameters](https://developer.apple.com/reference/uikit/uicubictimingparameters) e [referência de classe UISpringTimingParameter](https://developer.apple.com/reference/uikit/uispringtimingparameters) para obter mais informações.
- O novo `UIPreviewInteraction` e `UIPreviewInteractionDelegate` o permitem que o aplicativo forneça uma interface personalizada para operações de Peek e pop.
- A nova `UIAccessibilityCustomRotor` classe permite que o aplicativo forneça funcionalidade personalizada e específica ao contexto para tecnologias assistenciais, como voz.
- Use os `UIAccessibilityIsAssistiveTouchRunning` `UIAccessibilityAssistiveTouchStatusDidChangeNotification` símbolos e para determinar se o AssistiveTouch está habilitado.
- Use os `UIAccessibilityHearingDevicePairedEar` `UIAccessibilityHearingDevicePairedEarDidChangeNotification` símbolos e para obter o status de quaisquer auxílios de audição acessórios MFI emparelhados.
- A nova API [UIPasteboard](https://developer.apple.com/reference/uikit/uipasteboard) fornece novas opções (como limitações de tempo de vida) e irá declarar automaticamente os tipos de conteúdo compatíveis para tipos de classe comuns.
- Para dar suporte ao tipo dinâmico em rótulos, campos de texto e caixas de texto, use o novo `PreferredFontForTextStyle` método da `UIFont` classe.
- Para decidir se um elemento deve atualizar a fonte quando os dispositivos `UIContentSizeCategory` forem alterados, use a `AdjustsFontForContentSizeCategory` Propriedade do `UIContentSizeCategoryAdjusting` delegado.
- O aplicativo agora pode controlar a aparência da notificação para itens da barra de guias, como texto e cor do plano de fundo.
- O controle de atualização agora tem suporte em todas as subclasses View e Scroll View (como `UICollectionView` ).
- O `OpenURL` método da `UIApplication` classe é chamado de forma assíncrona agora dá suporte a um manipulador de conclusão que é chamado após a conclusão da abertura.
- Inicie o compartilhamento de CloudKit e modifique suas propriedades usando as novas `UICloudSharingController` `UICloudSharingControllerDelegate` classes e.
- Aproveite as células de pré-busca para melhorar a experiência de rolagem do `UICollectionViews` com o novo `UICollectionViewDataSourcePrefetching` delegado.

## <a name="related-links"></a>Links relacionados

- [Exemplos do tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [O que há de novo no tvOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)
