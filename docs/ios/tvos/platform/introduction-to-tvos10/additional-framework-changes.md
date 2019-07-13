---
title: Alterações de estruturas adicional tvOS 10
description: Este documento descreve algumas pequenas alterações e aprimoramentos feitos para as estruturas existentes no iOS 10. Ele aborda as atualizações para AVFoundation, AVKit, dados principais, elementos gráficos principais, Foundation, GameKit, GameplayKit e muito mais.
ms.prod: xamarin
ms.assetid: F771640A-F92E-4954-82D5-2D720434971E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: ab6236198d0a5826fc613d1f3839bafdb980d235
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67865639"
---
# <a name="additional-tvos-10-frameworks-changes"></a>Alterações de estruturas adicional tvOS 10

Além das alterações principais para tvOS, o Apple fez modificações e melhorias nas várias estruturas existentes na tvOS 10.

<a name="AV-Foundation-Framework" />

## <a name="avfoundation-framework-additions"></a>Adições de Framework AVFoundation

A estrutura de AVFoundation inclui os seguintes aprimoramentos:

- TvOS 10, o aplicativo não implementa diferentes [AVPlayerItem](https://developer.apple.com/reference/avfoundation/avplayeritem) comportamentos com base no tipo de conteúdo. Basta definir o `Rate` propriedade e AVFoundation determinará quando conteúdo suficiente está disponível para a reprodução sem atraso das.
- O novo `AVPlayerLooper` classe torna mais fácil executar uma determinada parte da mídia de um loop durante a reprodução.

<a name="AVKit-Framework-Enhancements" />

## <a name="avkit-framework-enhancements"></a>Aprimoramentos do Framework de AVKit

A estrutura de AVKit inclui os seguintes aprimoramentos:

- O aplicativo agora tem controle sobre o comportamento pulando do [AVPlayerViewController](https://developer.apple.com/reference/avkit/avplayerviewcontroller) para que um gesto pulando pode mover para o próximo item na lista de reprodução ou avanço dentro do item atual.

<a name="Core-Data-Enhancements" />

## <a name="core-data-enhancements"></a>Principais aprimoramentos de dados

tvOS 10 inclui os seguintes aprimoramentos para a estrutura de dados principais:

- Raiz [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) simultâneo com falha e buscando sem serialização dá suporte a objetos.
- O [NSPersistentStoreCoordinator](https://developer.apple.com/reference/coredata/nspersistentstorecoordinator) classe mantém um pool de armazenamentos de dados SQLite.
- O [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) objetos com armazenamentos de dados SQLite no suporte a nova geração de consulta WAL diário modo em que contextos de objeto gerenciado (MOC) podem ser fixados em versões de banco de dados específico para buscar futuras de recursos e transações com falha.
- Usando o alto nível `NSPersistenceContainer` a referência a `NSPersistentStoreCoordinator`, [NSManagedObjectModel](https://developer.apple.com/reference/coredata/nsmanagedobjectmodel) e outros recursos de configuração de dados principal.
- Vários métodos de conveniência novos foram adicionados ao `NSManagedObject` tornando mais fácil executar buscas e criar subclasses.

Para obter mais informações, consulte da Apple [referência de estrutura de dados de núcleo](https://developer.apple.com/reference/coredata).

<a name="Core-Graphics-Enhancements" />

## <a name="core-graphics-enhancements"></a>Aprimoramentos de elementos gráficos principais

tvOS 10 inclui os seguintes aprimoramentos para a estrutura de elementos gráficos principais:

- O novo [CGColorConverterRef](https://developer.apple.com/reference/coregraphics/cgcolorconverterref) classe pode ser usada para executar uma série de conversões de cores.

<a name="Core-Image-Enhancements" />

## <a name="core-image-enhancements"></a>Principais aprimoramentos de imagem

tvOS 10 faz com que os seguintes aprimoramentos para a estrutura de imagem principal:

- O `ImageWithExtent` método da [CIFilter](https://developer.apple.com/reference/coreimage/cifilter) classe pode ser usada para inserir o processamento personalizado para a operação de filtro. Imagem principal invoque o retorno de chamada fornecido entre os filtros ao processar uma imagem de saída ou a exibir.
- O aplicativo agora pode processar imagens em um espaço de cores fora do espaço de cor de trabalho do contexto de núcleo de imagem, convertendo para dentro e fora do espaço de cor antes e após o processamento.
- Foram feitas várias melhorias de desempenho de renderização para `UIImage` renderizando (quando o apoio de repositórios de imagem da imagem principal) em `UIImageView` objetos. 
- `UIImage` objetos marcados toda a gama de cores será renderizado como toda a gama de cor em `UIImageView` objetos em dispositivos iOS que dão suporte a cores de largura.
- Código de kernel de imagem Core agora pode solicitar a formatos de saída do pixel específico.

Além disso, os seguintes novos filtros de imagem Core foram adicionados:

- `CINinePartTiled`
- `CINinePartStretched`
- `CIHueSaturationValueGradient`
- `CIEdgePreserveUpsampleFilter`
- `CIClamp`

<a name="Foundation-Enhancements" />

## <a name="foundation-enhancements"></a>Aprimoramentos do Foundation

As seguintes melhorias foram feitas para a estrutura de base para tvOS 10:

- Use a nova [NSDateInterval](https://developer.apple.com/reference/foundation/nsdateinterval) classe para fazer cálculos de intervalo de data e hora como durações, para comparar os intervalos e teste para interseções de intervalo.
- Várias novas propriedades foram adicionadas para o [NSLocal](https://developer.apple.com/reference/foundation/nslocale) classe para adquirir informações locais e os formatos de exibição disponíveis.
- Use a nova [NSMeasurement](https://developer.apple.com/reference/foundation/nsmeasurement) classe para converter entre diferentes unidades de medida (unidade de medida) ou executar cálculos nos valores de UOMs diferentes.
- Use a nova [NSMeasurementFormatter](https://developer.apple.com/reference/foundation/nsmeasurementformatter) classe Formatar medidas localizadas para a exibição para o usuário final.
- Use a nova [NSUnit](https://developer.apple.com/reference/foundation/nsunit) e [NSDimension](https://developer.apple.com/reference/foundation/nsdimension) classes para representar UOMs específicos.

<a name="GameKit-Enhancements" />

## <a name="gamekit-enhancements"></a>Aprimoramentos de GameKit

As seguintes melhorias foram feitas à estrutura de GameKit no tvOS 10:

- Um novo tipo de conta somente para iCloud foi implementado pelo [GKCloudPlayer](https://developer.apple.com/reference/gamekit/gkcloudplayer) classe.
- O novo [GKGameSession](https://developer.apple.com/reference/gamekit/gkgamesession) classe fornece uma solução generalizada para gerenciar o armazenamento de dados persistentes no Game Center. `GKGameSession` mantém uma lista de jogadores e o aplicativo é responsável formulário implementando como e quando a data de participante é armazenada, recuperado ou trocadas entre os participantes. Em muitos casos as sessões de jogos pode substituir correspondências existentes baseada em turno, correspondências em tempo real ou jogo persistente salvar métodos.

<a name="GameplayKit-Enhancements" />

## <a name="gameplaykit-enhancements"></a>Aprimoramentos de GameplayKit

As seguintes melhorias foram feitas à estrutura de GameplayKit no tvOS 10:

- Geração de ruído procedural foi adicionada e pode ser usada para aprimorar o realismo aparência natural das texturas, adicione realismo para movimentos de câmera e ajudar a gerar mundos de jogos avançados.
- Use o particionamento espacial para particionar os dados do mundo do jogo para a pesquisa eficiente.
- Estrategista de Monte Carlo nova ([GKMonteCarloStrategist](https://developer.apple.com/reference/gameplaykit/gkmontecarlostrategist)) foi adicionado para a computação de mudança completa de possíveis.
- Foi adicionada uma nova API de árvore de decisão ([GKDecisionTree](https://developer.apple.com/reference/gameplaykit/gkdecisiontree) e [GKDecisionNode](https://developer.apple.com/reference/gameplaykit/gkdecisionnode)) para aprimorar o AI de jogo de construção.
- Foi adicionado suporte 3D para o agente existente e os comportamentos de localização de caminho usando a nova [GKAgent3D](https://developer.apple.com/reference/gameplaykit/gkagent3d) e [GKGraphNode3D](https://developer.apple.com/reference/gameplaykit/gkgraphnode3d) classes.
- Use a nova [GKMeshGraph](https://developer.apple.com/reference/gameplaykit/gkmeshgraph) classe para fornecer caminhos de alto desempenho e a aparência natural.
- O novo [GKScene](https://developer.apple.com/reference/gameplaykit/gkscene) e [GKSKNodeComponent](https://developer.apple.com/reference/gameplaykit/gksknodecomponent) make classes combinando GameplayKit e SpriteKit mais fácil do que nunca.

<a name="Metal-Enhancements" />

## <a name="metal-enhancements"></a>Aprimoramentos de metal

As seguintes melhorias foram feitas à estrutura de Metal no tvOS 10:

- Aplicativos 3D e jogos agora podem usar _mosaico_ para processar com eficiência cenas complexas e geometry por meio da GPU.
- Use a especialização de função para criar uma coleção altamente otimizado de materiais e funções de combinação de luz para uma cena.
- Fornece controle refinado de alocação de recursos para otimizar o desempenho do sistema operacional com base em aplicativos usando o recurso de Heaps e Memoryless destinos de renderização.

Para obter mais informações, consulte da Apple [guia de programação de Metal](https://developer.apple.com/library/prerelease/content/documentation/Miscellaneous/Conceptual/MetalProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014221).

<a name="Metal-Performance-Shaders-Enhancements" />

## <a name="metal-performance-shaders-enhancements"></a>Aprimoramentos de sombreadores de desempenho de metal

As seguintes melhorias foram feitas à estrutura de sombreadores de desempenho do sistema operacional no tvOS 10:

- Muitos kernels novos foram adicionados para a estrutura de sombreadores de desempenho do sistema operacional para permitir que o aplicativo tirar proveito de computações e otimizado para alto, paralela de dados, como operações de rede neural e conversões de espaço de cores.

<a name="ModelIO-Enhancements" />

## <a name="modelio-enhancements"></a>Aprimoramentos de ModelIO

As seguintes melhorias foram feitas à estrutura de ModelIO no tvOS 10:

- Agora há suporte para o formato de arquivo USD.
- Usar o novo `MDLMaterialPropertyGraph` classe para suportar facilmente o tempo de execução é alterado para modelos.
- Assinado campo distância foi adicionado suporte para o [MDLVoxelArray](https://developer.apple.com/reference/modelio/mdlvoxelarray) classe.
- Usar o novo `MDLLightProbeIrradianceDataSource` classe para auxiliar na investigação de luz de posicionamento.

<a name="SceneKit-Enhancements" />

## <a name="scenekit-enhancements"></a>Aprimoramentos do SceneKit

As seguintes melhorias foram feitas para a estrutura de SceneKit do tvOS 10:

- SceneKit agora inclui um novo sistema fisicamente com base em processamento (PBR) para obter resultados mais realistas com a criação de ativo mais simples.
- Use a nova [SCNLightingModelPhysicallyBased](https://developer.apple.com/reference/scenekit/scnlightingmodelphysicallybased) sombreamento de modelo para o produto uma ampla variedade de efeitos de sombreamento realístico, exigindo apenas três propriedades fundamentais (`Diffuse`, `Metalness` e `Roughness`).
- Desde PBR sombreamento funciona melhor com iluminação baseada em ambiente, use o `LightingEnvironment` propriedade para atribuir a iluminação baseada na imagem para tan toda a cena.
- Use o `IESProfileURL` propriedade para importar os acessórios de iluminação do mundo real que definem a iluminação com base nos valores do mundo real, como a intensidade (em lumens) e cor de temperatura (em graus Kelvin).
- O [SCNCamera](https://developer.apple.com/reference/scenekit/scncamera) classe pode fornecer maior realismo usando recursos HDR e efeitos. Use exposição adaptável para criar efeitos automática ou use vinheta, margem das cores e cor de classificação para adicionar efeitos filmatic ao jogo.
- Recursos de câmera PBR e HDR fornecem resultados melhores que as técnicas tradicionais de renderização e, como resultado, SceneKit agora executa todos os cálculos de cor em um espaço de cor linear (usando P3 gama de cores na cor de todo o dispositivo exibe).
- Cor do SceneKit agora corresponde a todas as cores lendo as informações de perfil de cor.
- SceneKit interpretará valores de componente de cor em um espaço de cor RGB linear para todos os tipos de sombreador.
- Como SceneKit lê e ajustar para obter informações de perfil de cor nas imagens de textura, use os catálogos de ativos para todas as imagens para garantir que essas informações são fornecidas.
- Renderização de espaço de cor linear e toda a cor pode ser desabilitado especificando o `SCNDisableLinearSpaceRendering` e `SCNDisableWideGamut` chaves do aplicativo `Info.plist`.
- Compilar primates polígono arbitrário (ou carregado de arquivos ou gerados por meio de programação) para especificar a geometria com o novo [SCNGeometryPrimitiveTypePolygon](https://developer.apple.com/reference/scenekit/1772322-scenekit_enumerations/scngeometryprimitivetype/scngeometryprimitivetypepolygon) classe.

<a name="SpriteKit-Enhancements" />

## <a name="spritekit-enhancements"></a>Aprimoramentos de SpriteKit

As seguintes melhorias foram feitas para a estrutura de SpriteKit do tvOS 10:

- Tilemaps agora dão suporte a formas de bloco quadrado, hexagonal e isométrica para 2D, 2.5D e rolagem de lado jogos usando o `SKTileMapMode`, `SKTileGroup`, `SKTileGroupRule` e `SKTileSet` classes.
- Use a nova `SKWarpGeometry` classe para alongar ou distorcer [SKSpriteNode](https://developer.apple.com/reference/spritekit/skspritenode) ou [SKEffectNode](https://developer.apple.com/reference/spritekit/skeffectnode) renderização. O novo [SKAction](https://developer.apple.com/reference/spritekit/skaction) classe pode ser usada para animar as transições entre os efeitos de warp.
- Sombreadores personalizados podem fornecer atributos (`SKAttribute`) que pode ser configurada separadamente por cada nó que usa o sombreador fornecendo um valor de atributo (`SKAttributeValue`).
- O [SKView](https://developer.apple.com/reference/spritekit/skview) classe fornece vários novos métodos para fornecer controle refinado sobre quando e como uma cena é renderizada.

<a name="UIKit-Enhancements" />

## <a name="uikit-enhancements"></a>Aprimoramentos de UIKit

As seguintes melhorias foram feitas para a estrutura UIKit em tvOS 10:

- O foco de API foi aprimorado para o foco do item de não exibição, além do suporte para `UIViews`. Itens que dão suporte ao foco _devem_ implementar o `IUIFocusItem` interface.
- O novo `UIGraphicsRender` classe fornece um método de criação de bitmaps ou PDFs de renderização de UIKit ou elementos gráficos principais de orientada a objeto e substitui preteridas `UIGraphicsBeginImageContext` método.
- O `UIUserInterfaceStyle` classe foi adicionada para determinar qual tema da interface do usuário (claro ou escuro) está ativo no momento.
- Novo suporte a animação totalmente interativos, com base em objeto, passível de interrupção foi adicionado e van ser vinculado a gestos. Do pleas consulte Apple [referência de protocolo UIViewAnimating](https://developer.apple.com/reference/uikit/uiviewanimating), [referência de classe UIViewPropertyAnimator](https://developer.apple.com/reference/uikit/uiviewpropertyanimator), [referência de protocolo UITimingCurveProvider](https://developer.apple.com/reference/uikit/uitimingcurveprovider), [Referência de classe UICubicTimingParameters](https://developer.apple.com/reference/uikit/uicubictimingparameters) e [referência de classe UISpringTimingParameter](https://developer.apple.com/reference/uikit/uispringtimingparameters) para obter mais informações.
- O novo `UIPreviewInteraction` e `UIPreviewInteractionDelegate` permitir que o aplicativo fornecer uma interface personalizada para operações de inspeção e pop.
- O novo `UIAccessibilityCustomRotor` classe permite que o aplicativo fornecer funcionalidade personalizada, o contexto específico para tecnologias auxiliares, como a VoiceOver.
- Use o `UIAccessibilityIsAssistiveTouchRunning` e `UIAccessibilityAssistiveTouchStatusDidChangeNotification` símbolos para determinar se as AssistiveTouch está habilitado.
- Use o `UIAccessibilityHearingDevicePairedEar` e `UIAccessibilityHearingDevicePairedEarDidChangeNotification` símbolos para obter o status de qualquer emparelhado ajudas aguardamos seu contato de MFi.
- O novo [UIPasteboard](https://developer.apple.com/reference/uikit/uipasteboard) API fornece novas opções (como as limitações de tempo de vida) e automaticamente irá declarar tipos de conteúdo compatíveis para tipos comuns de classe.
- Para dar suporte a tipo dinâmico em rótulos, campos de texto e caixas de texto usam a nova `PreferredFontForTextStyle` método da `UIFont` classe.
- Para decidir se um elemento deve atualizá-lo fonte quando os dispositivos `UIContentSizeCategory` alterações, use o `AdjustsFontForContentSizeCategory` propriedade do `UIContentSizeCategoryAdjusting` delegar.
- O aplicativo agora pode controlar a aparência de notificação para itens da barra de guia, como cor de plano de fundo e texto.
- O controle de atualização agora tem suporte em todas as exibição de rolagem e a exibição da rolagem subclasses (como `UICollectionView`).
- O `OpenURL` método da `UIApplication` classe é chamado de forma assíncrona agora dá suporte a um manipulador de conclusão que é chamado após a abertura foi concluída.
- Iniciar o compartilhamento do CloudKit e modifique suas propriedades usando o novo `UICloudSharingController` e `UICloudSharingControllerDelegate` classes.
- Tirar proveito das células de pré-busca para melhorar a experiência de rolagem do `UICollectionViews` com o novo `UICollectionViewDataSourcePrefetching` delegar.



## <a name="related-links"></a>Links relacionados

- [Amostras do tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [O que há de novo no tvOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)
