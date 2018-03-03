---
title: "Alterações de estruturas adicionais tvOS 10"
description: "Este artigo aborda as alterações adicionais, secundárias ou melhorias em estruturas existentes para tvOS 10."
ms.topic: article
ms.prod: xamarin
ms.assetid: F771640A-F92E-4954-82D5-2D720434971E
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 004abfffd3a100b7a25a9647fe233fd676f61143
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="additional-tvos-10-frameworks-changes"></a>Alterações de estruturas adicionais tvOS 10

_Este artigo aborda as alterações adicionais, secundárias ou melhorias em estruturas existentes para tvOS 10._


Além das alterações principais para tvOS, Apple fez melhorias para várias estruturas existentes e modificações tvOS 10.

<a name="AV-Foundation-Framework" />

## <a name="av-foundation-framework-additions"></a>Adições de Framework AV Foundation

A estrutura AVFoundation inclui os seguintes aprimoramentos:

 - Em tvOS 10, o aplicativo não implementa diferentes [AVPlayerItem](https://developer.apple.com/reference/avfoundation/avplayeritem) comportamentos com base no tipo de conteúdo. Basta definir o `Rate` propriedade e AVFoundation determinará quando conteúdo suficiente está disponível para a reprodução sem atrasando.
 - O novo `AVPlayerLooper` classe torna mais fácil repetir um determinado item de mídia durante a reprodução.

<a name="AVKit-Framework-Enhancements" />

## <a name="avkit-framework-enhancements"></a>Aprimoramentos do AVKit Framework

A estrutura AVKit inclui os seguintes aprimoramentos:

 - O aplicativo agora tem controle sobre o comportamento ignorando do [AVPlayerViewController](https://developer.apple.com/reference/avkit/avplayerviewcontroller) para um gesto ignorando pode mudar para o próximo item na lista de reprodução ou antecipada dentro do item atual.

<a name="Core-Data-Enhancements" />

## <a name="core-data-enhancements"></a>Principais aprimoramentos de dados

tvOS 10 inclui os seguintes aprimoramentos para a estrutura de dados principais:

 - Raiz [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) simultâneos com falha e buscar sem serialização dá suporte a objetos.
 - O [NSPersistentStoreCoordinator](https://developer.apple.com/reference/coredata/nspersistentstorecoordinator) classe mantém um pool de armazenamentos de dados SQLite.
 - O [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) objetos com repositórios de dados SQLite no suporte a WAL diário modo a nova geração de consulta de recursos onde contextos de objeto gerenciado (MOC) podem ser fixados em versões de banco de dados específico para buscar futuras e transações com falha.
 - Usando o alto nível `NSPersistenceContainer` a referência a `NSPersistentStoreCoordinator`, [NSManagedObjectModel](https://developer.apple.com/reference/coredata/nsmanagedobjectmodel) e outros recursos de configuração de dados principal.
 - Foram adicionados vários novos métodos de conveniência para `NSManagedObject` tornando mais fácil executar buscas e criar subclasses.

Para obter mais informações, consulte da Apple [referência de estrutura de dados principal](https://developer.apple.com/reference/coredata).

<a name="Core-Graphics-Enhancements" />

## <a name="core-graphics-enhancements"></a>Principais aprimoramentos de gráficos

tvOS 10 inclui os seguintes aprimoramentos para a estrutura do gráfico principal:

 - O novo [CGColorConverterRef](https://developer.apple.com/reference/coregraphics/cgcolorconverterref) classe pode ser usada para executar uma série de conversões de cores.

<a name="Core-Image-Enhancements" />

## <a name="core-image-enhancements"></a>Principais aprimoramentos de imagem

tvOS 10 faz com que os seguintes aprimoramentos para a estrutura de imagem principal:

 - O `ImageWithExtent` método o [CIFilter](https://developer.apple.com/reference/coreimage/cifilter) classe pode ser usada para inserir o processamento personalizado na operação de filtro. Imagem principal será invocar o retorno de chamada especificado entre os filtros ao processar uma imagem de saída ou exibir.
 - O aplicativo agora pode processar imagens em um espaço de cores fora do espaço de cor de trabalho do contexto de imagem principal convertendo dentro e fora do espaço de cor antes e após o processamento.
 - Foram feitas várias melhorias de desempenho de renderização para `UIImage` processamento (quando feito por armazenamentos de imagem de imagem principal) em `UIImageView` objetos. 
 - `UIImage` objetos marcados toda a gama de cores será renderizado como toda a gama de cores no `UIImageView` objetos em dispositivos iOS que dão suporte a cores de largura.
 - Código de kernel de imagem principal agora pode solicitar os formatos de saída específicas.

Além disso, os seguintes novos filtros de imagem principal foram adicionados:

 - `CINinePartTiled`
 - `CINinePartStretched`
 - `CIHueSaturationValueGradient`
 - `CIEdgePreserveUpsampleFilter`
 - `CIClamp`

<a name="Foundation-Enhancements" />

## <a name="foundation-enhancements"></a>Aprimoramentos de base

As seguintes melhorias foram feitas para a estrutura de base para tvOS 10:

 - Use a nova [NSDateInterval](https://developer.apple.com/reference/foundation/nsdateinterval) classe para fazer cálculos de intervalo de data e hora como durações, para comparar os intervalos e testando interseções de intervalo.
 - Várias novas propriedades foram adicionadas para o [NSLocal](https://developer.apple.com/reference/foundation/nslocale) classe para adquirir informações locais e os formatos de exibição disponíveis.
 - Use a nova [NSMeasuerment](https://developer.apple.com/reference/foundation/nsmeasurement) classe para converter entre diferentes unidades de medida (unidade de medida) ou executar cálculos nos valores de UOMs diferentes.
 - Use a nova [NSMeasurementFormatter](https://developer.apple.com/reference/foundation/nsmeasurementformatter) classe Formatar medidas localizadas para a exibição para o usuário final.
 - Use a nova [NSUnit](https://developer.apple.com/reference/foundation/nsunit) e [NSDimension](https://developer.apple.com/reference/foundation/nsdimension) classes para representar UOMs específicos.

<a name="GameKit-Enhancements" />

## <a name="gamekit-enhancements"></a>Aprimoramentos de GameKit

As seguintes melhorias foram feitas para a estrutura de GameKit tvOS 10:

 - Um novo tipo de conta somente iCloud foi implementado pelo [GKCloudPlayer](https://developer.apple.com/reference/gamekit/gkcloudplayer) classe.
 - O novo [GKGameSession](https://developer.apple.com/reference/gamekit/gkgamesession) classe fornece uma solução generalizada para gerenciamento de armazenamento de dados persistentes no Game Center. `GKGameSession` mantém uma lista de participantes e o aplicativo é responsável formulário implementando como e quando a data de participante é armazenada, recuperadas ou trocadas entre os participantes. Em muitas instâncias sessões jogo pode substituir correspondências existentes com base por sua vez, correspondências em tempo real ou jogo persistente salvar métodos.

<a name="GameplayKit-Enhancements" />

## <a name="gameplaykit-enhancements"></a>Aprimoramentos de GameplayKit

As seguintes melhorias foram feitas para a estrutura de GameplayKit tvOS 10:

 - Geração de ruído procedimento foi adicionada e pode ser usada para aprimorar o realismo em natural de aparência, adicionar realismo movimentos de câmera e ajudar a gerar mundos jogos avançados.
 - Use o particionamento espacial para particionar os dados de jogos do mundo para pesquisa eficiente.
 - Estrategista de Monte Carlo novo ([GKMonteCarloStrategist](https://developer.apple.com/reference/gameplaykit/gkmontecarlostrategist)) foi adicionado para a computação de movimentação possíveis completa.
 - Foi adicionada uma nova API de árvore de decisão ([GKDecisionTree](https://developer.apple.com/reference/gameplaykit/gkdecisiontree) e [GKDecisionNode](https://developer.apple.com/reference/gameplaykit/gkdecisionnode)) para aprimorar a construção de jogo AI.
 - Foi adicionado suporte 3D para o agente existente e comportamentos de localização do caminho usando o novo [GKAgent3D](https://developer.apple.com/reference/gameplaykit/gkagent3d) e [GKGraphNode3D](https://developer.apple.com/reference/gameplaykit/gkgraphnode3d) classes.
 - Use a nova [GKMeshGraph](https://developer.apple.com/reference/gameplaykit/gkmeshgraph) classe para fornecer caminhos de alto desempenho, procurando natural.
 - O novo [GKScene](https://developer.apple.com/reference/gameplaykit/gkscene) e [GKSKNodeComponent](https://developer.apple.com/reference/gameplaykit/gksknodecomponent) criar classes combinando GameplayKit e SpriteKit mais fácil.

<a name="Metal-Enhancements" />

## <a name="metal-enhancements"></a>Aprimoramentos de metal

As seguintes melhorias foram feitas à estrutura de Metal no tvOS 10:

 - Aplicativos 3D e jogos agora podem usar _mosaico_ para processar com eficiência cenas complexas e geometry por meio da GPU.
 - Use a função especialização para criar uma coleção altamente otimizado de material e funções de combinação de luz para uma cena.
 - Fornece controle refinado de alocação de recursos para otimizar o desempenho do sistema operacional com base em aplicativos que usam o recurso de Heaps e destinos Memoryless renderizar.

Para obter mais informações, consulte da Apple [guia de programação de Metal](https://developer.apple.com/library/prerelease/content/documentation/Miscellaneous/Conceptual/MetalProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014221).

<a name="Metal-Performance-Shaders-Enhancements" />

## <a name="metal-performance-shaders-enhancements"></a>Aprimoramentos de sombreadores de desempenho de sistema operacional

As seguintes melhorias foram feitas à estrutura de sombreadores de desempenho do sistema operacional no tvOS 10:

 - Muitos kernels novos foram adicionados para a estrutura de sombreadores de desempenho do sistema operacional para permitir que o aplicativo tirar proveito dos cálculos altamente otimizada e paralelo de dados, como operações de rede neural e conversões de espaço de cor.

<a name="ModelIO-Enhancements" />

## <a name="modelio-enhancements"></a>Aprimoramentos de ModelIO

As seguintes melhorias foram feitas para a estrutura de ModelIO tvOS 10:

 - Agora há suporte para o formato de arquivo USD.
 - Use a nova `MDLMaterialPropertyGraph` classe para suportar facilmente o tempo de execução é alterado para modelos.
 - Assinado distância campo foi adicionado suporte para o [MDLVoxelArray](https://developer.apple.com/reference/modelio/mdlvoxelarray) classe.
 - Use a nova `MDLLightProbeIrradianceDataSource` classe para auxiliar na investigação de luz de posicionamento.

<a name="SceneKit-Enhancements" />

## <a name="scenekit-enhancements"></a>Aprimoramentos de SceneKit

As seguintes melhorias foram feitas para a estrutura de SceneKit tvOS 10:

 - SceneKit agora inclui um novo sistema fisicamente com base em processamento (PBR) para obter resultados mais realistas com mais simples de criação de ativo.
 - Use a nova [SCNLightingModelPhysicallyBased](https://developer.apple.com/reference/scenekit/scnlightingmodelphysicallybased) sombreamento de modelo ao produto de uma ampla gama de efeitos de sombreamento realístico, exigindo somente três propriedades fundamentais (`Diffuse`, `Metalness` e `Roughness`).
 - Desde PBR sombreamento funciona melhor com iluminação baseado no ambiente, use o `LightingEnvironment` propriedade para atribuir a iluminação baseada em imagem para tan toda cena.
 - Use o `IESProfileURL` propriedade importar fixações da iluminação reais que definem iluminação com base nos valores reais, como a intensidade (em lumens) e cor de temperatura (em graus Kelvin).
 - O [SCNCamera](https://developer.apple.com/reference/scenekit/scncamera) classe pode fornecer maior realismo usando recursos HDR e efeitos. Use exposição adaptável para criar efeitos automática ou use vinheta, margem das cores e cor de classificação para adicionar efeitos filmatic ao jogo.
 - Recursos de câmera PBR e HDR fornecem resultados melhores que as técnicas tradicionais de renderização e, como resultado, SceneKit executa todos os cálculos de cor em um espaço de cor linear (usando o P3 gama de cores exibe cores de todo o dispositivo).
 - Cor do SceneKit agora corresponde a todas as cores ao ler as informações de perfil de cor.
 - SceneKit interpretará valores de componente de cor em um espaço de cor RGB linear para todos os tipos de sombreador.
 - Como SceneKit lê e ajuste para obter informações de perfil de cor nas imagens de textura, use catálogos ativos para todas as imagens para garantir que essas informações são fornecidas.
 - Ambos linear renderização de espaço de cores e toda a cor pode ser desabilitado especificando o `SCNDisableLinearSpaceRendering` e `SCNDisableWideGamut` chaves no aplicativo do `Info.plist`.
 - Criar primates arbitrária polígono (ou carregado de arquivos gerados por meio de programação) para especificar a geometria com o novo [SCNGeometryPrimitiveTypePolygon](https://developer.apple.com/reference/scenekit/1772322-scenekit_enumerations/scngeometryprimitivetype/scngeometryprimitivetypepolygon) classe.

<a name="SpriteKit-Enhancements" />

## <a name="spritekit-enhancements"></a>Aprimoramentos de SpriteKit

As seguintes melhorias foram feitas para a estrutura de SpriteKit tvOS 10:

 - Tilemaps agora dão suporte ao bloco quadrado, hexagonal e isométrica formas 2D, 2,5 D e jogos rolagem lado usando o `SKTileMapMode`, `SKTileGroup`, `SKTileGroupRule` e `SKTileSet` classes.
 - Use a nova `SKWarpGeometry` classe para alongar ou distorcer [SKSpriteNode](https://developer.apple.com/reference/spritekit/skspritenode) ou [SKEffectNode](https://developer.apple.com/reference/spritekit/skeffectnode) renderização. O novo [SKAction](https://developer.apple.com/reference/spritekit/skaction) classe pode ser usada para animar transições entre os efeitos de distorção.
 - Sombreadores personalizados podem fornecer atributos (`SKAttribute`) que pode ser configurado separadamente, cada nó que usa o sombreador fornecendo um valor de atributo (`SKAttributeValue`).
 - O [SKView](https://developer.apple.com/reference/spritekit/skview) classe fornece vários novos métodos para fornecer controle refinado sobre quando e como uma cena é renderizada.

<a name="UIKit-Enhancements" />

## <a name="uikit-enhancements"></a>Aprimoramentos de UIKit

As seguintes melhorias foram feitas para a estrutura de UIKit tvOS 10:

 - O foco API foi aprimorado para o foco de exibição não item, além do suporte para `UIViews`. Itens que oferecem suporte a foco _deve_ implementar o `IUIFocusItem` interface.
 - O novo `UIGraphicsRender` classe fornece um método orientado a objeto de criação de bitmaps ou PDFs de renderização UIKit ou principais gráficos e substitui preterido `UIGraphicsBeginImageContext` método.
 - O `UIUserInterfaceStyle` classe foi adicionada para determinar o tema de interface do usuário (claro ou escuro) está ativo no momento.
 - Novo suporte de animação totalmente interativo, com base em objeto, passível de interrupção foi adicionado e van ser vinculado a gestos. Do pleas consulte Apple [referência de protocolo UIViewAnimating](https://developer.apple.com/reference/uikit/uiviewanimating), [referência de classe UIViewPropertyAnimator](https://developer.apple.com/reference/uikit/uiviewpropertyanimator), [referência de protocolo UITimingCurveProvider](https://developer.apple.com/reference/uikit/uitimingcurveprovider), [Referência de classe UICubicTimingParameters](https://developer.apple.com/reference/uikit/uicubictimingparameters) e [UISpringTimingParameter Referência de classe](https://developer.apple.com/reference/uikit/uispringtimingparameters) para obter mais informações.
 - O novo `UIPreviewInteraction` e `UIPreviewInteractionDelegate` permitem que o aplicativo do aplicativo fornecer uma interface personalizada para as operações peek e pop.
 - O novo `UIAccessibilityCustomRotor` classe permite que o aplicativo forneça funcionalidade personalizada, específicas de contexto, como voz sobre tecnologias de assistência.
 - Use o `UIAccessibilityIsAssistiveTouchRunning` e `UIAccessibilityAssistiveTouchStatusDidChangeNotification` símbolos para determinar se AssistiveTouch está habilitado.
 - Use o `UIAccessibilityHearingDevicePairedEar` e `UIAccessibilityHearingDevicePairedEarDidChangeNotification` símbolos para obter o status de qualquer par MFi auxílios de audição.
 - O novo [UIPasteboard](https://developer.apple.com/reference/uikit/uipasteboard) API fornece novas opções (como as limitações de tempo de vida) e automaticamente irá declarar tipos de conteúdo compatíveis para tipos comuns de classe.
 - Para dar suporte a tipo dinâmico em rótulos, campos de texto e caixas de texto usam o novo `PreferredFontForTextStyle` método o `UIFont` classe.
 - Para decidir se um elemento atualize fonte quando os dispositivos `UIContentSizeCategory` alterações, use o `AdjustsFontForContentSizeCategory` propriedade o `UIContentSizeCategoryAdjusting` delegar.
 - O aplicativo agora pode controlar a aparência de notificação para itens da barra de guia, como cor de plano de fundo e texto.
 - O controle de atualização agora tem suporte em todas as exibições de rolagem e rolagem subclasses (como `UICollectionView`).
 - O `OpenURL` método o `UIApplication` classe é chamada de forma assíncrona agora dá suporte a um processador de conclusão é chamado após a abertura.
 - Iniciar o compartilhamento de CloudKit e modificar suas propriedades usando a nova `UICloudSharingController` e `UICloudSharingControllerDelegate` classes.
 - Tirar proveito das células de pré-busca para melhorar a experiência de rolagem de `UICollectionViews` com o novo `UICollectionViewDataSourcePrefetching` delegate.



## <a name="related-links"></a>Links relacionados

- [Exemplos de tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [O que há de novo no tvOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)
