---
title: Alterações adicionais em estruturas do iOS 10
description: Este documento descreve pequenas alterações e aprimoramentos feitos em estruturas existentes no iOS 10 e discute como fazer uso dessas atualizações no Xamarin. iOS.
ms.prod: xamarin
ms.assetid: 0E2217F1-FC96-4D0A-ABAB-D40AD8F96502
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/29/2017
ms.openlocfilehash: 5aad72de5d894a83d734cd53fce3ac060125d740
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656939"
---
# <a name="additional-ios-10-frameworks-changes"></a>Alterações adicionais em estruturas do iOS 10

_Este artigo aborda as alterações adicionais, secundárias ou aprimoramentos nas estruturas existentes do iOS 10._

## <a name="av-foundation-framework-additions"></a>Adições de estrutura do AV Foundation

A estrutura AVFoundation inclui os seguintes aprimoramentos:

- No iOS 10, o desenvolvedor não precisa mais implementar comportamentos de [AVPlayerItem](xref:AVFoundation.AVPlayerItem) diferentes com base no tipo de conteúdo. Basta definir a `Rate` Propriedade e AVFoundation determinará quando o conteúdo suficiente está disponível para reprodução sem interrupções.
- A nova classe [AVCapturePhotoOutput](xref:AVFoundation.AVCaptureFileOutput) substitui a `AVCaptureStillImageOutput` classe preterida e fornece um método unificado para lidar com todos os fluxos de trabalho de fotografia, fornecendo controle sofisticado e monitoramento do processo de captura e suporte para novos recursos como fotos ao vivo e o formato de captura bruta.
- A nova `AVPlayerLooper` classe torna mais fácil fazer um loop de uma determinada mídia durante a reprodução.
- A `AVAssetDownloadURLSession` classe permite o download e a reprodução posterior de fluxos de HLS criptografados Fairplay.
- Por padrão, a classe [AVCaptureSession](xref:AVFoundation.AVCaptureSession) dá suporte automaticamente à captura de grande cor e de grande gama quando o hardware do dispositivo dá suporte a ela. Consulte a [referência de compatibilidade de dispositivo IOS](https://developer.apple.com/library/prerelease/content/documentation/DeviceInformation/Reference/iOSDeviceCompatibility/Introduction/Introduction.html#//apple_ref/doc/uid/TP40013599) da Apple para obter mais detalhes.

## <a name="avkit-additions"></a>Adições de AVKit

A estrutura AVKit agora inclui a nova `UpdatesNowPlayingInfoCenter` propriedade para indicar quando o centro de informações em execução deve ser atualizado.

## <a name="core-data-enhancements"></a>Principais aprimoramentos de dados

o iOS 10 inclui os seguintes aprimoramentos para a estrutura de dados principal:

- Os objetos [NSManagedObjectContext](xref:CoreData.NSManagedObjectContext) com armazenamentos de dados SQLite no modo de diário Wal dão suporte ao novo recurso de geração de consulta em que os contextos de objeto gerenciado (MOC) podem ser fixados em versões de banco de dados específicas para futuras transações de busca e falha.
- Os objetos raiz [NSManagedObjectContext](xref:CoreData.NSManagedObjectContext) dão suporte à falha e busca simultâneas sem serialização.
- A classe [NSPersistentStoreCoordinator](xref:CoreData.NSPersistentStoreCoordinator) mantém um pool de armazenamentos de dados do SQLite.
- Vários novos métodos de conveniência foram adicionados para `NSManagedObject` tornar mais fácil executar buscas e criar subclasses.
- Usando o alto nível `NSPersistenceContainer` para fazer referência a, [NSManagedObjectModel](xref:CoreData.NSManagedObjectModel) e outros recursos principais de configuração de `NSPersistentStoreCoordinator`dados.

Para obter mais informações, consulte a [referência da estrutura de dados principal](https://developer.apple.com/reference/coredata)da Apple.

## <a name="core-image-enhancements"></a>Aprimoramentos da imagem principal

o iOS 10 faz os seguintes aprimoramentos para a estrutura de imagem principal:

- Agora, o desenvolvedor pode processar imagens em um espaço de cores fora do espaço de cores de trabalho do contexto de imagem principal, convertendo e reduzindo o espaço de cores antes e depois do processamento.
- Para dispositivos iOS que usam CPUs A8 ou A9, agora há suporte para o formato de imagem RAW. A imagem básica agora oferece suporte para decodificação de imagens BRUTAs da câmera iSight interna ou de uma câmera de terceiros. Use os `FilterWithImageData` métodos `FilterWithImageURL` ou da classe [CIFilter](xref:CoreImage.CIFilter) para processar imagens brutas.
- Vários aprimoramentos de desempenho de renderização foram feitos `UIImage` para renderização (quando apoiado por armazenamentos de imagem de `UIImageView` imagem de núcleo) em objetos. 
- `UIImage`objetos marcados com largura ampla serão renderizados como uma cor de ampla `UIImageView` gama em objetos em dispositivos IOS que dão suporte a cores amplas.
- O código do kernel da imagem de núcleo agora pode solicitar formatos de saída de pixel específicos.
- O `ImageWithExtent` método da classe [CIFilter](xref:CoreImage.CIFilter) pode ser usado para inserir o processamento personalizado na operação de filtro. A imagem principal invocará o retorno de chamada fornecido entre os filtros ao processar uma imagem para saída ou exibição.

Além disso, os seguintes novos filtros de imagem de núcleo foram adicionados:

- `CINinePartTiled`
- `CINinePartStretched`
- `CIHueSaturationValueGradient`
- `CIEdgePreserveUpsampleFilter`
- `CIClamp`

## <a name="core-motion-additions"></a>Adições de movimento de núcleo

Novo no iOS 10, a estrutura de animação principal inclui eventos pedometer que permitem que um aplicativo receba notificações rápidas e em tempo real do usuário, pausando e retomando o rastreamento durante a execução. Use o [CMPedometer](xref:CoreMotion.CMPedometer) para se registrar em eventos de pedometer de primeiro plano ou em segundo plano.

## <a name="foundation-enhancements"></a>Aprimoramentos do Foundation

Os aprimoramentos a seguir foram feitos na estrutura de base para iOS 10:

- Use a nova classe [NSMeasurementFormatter](https://developer.apple.com/reference/foundation/nsmeasurementformatter) para formatar as medidas localizadas para exibição para o usuário final.
- Use a nova classe [NSDateInterval](https://developer.apple.com/reference/foundation/nsdateinterval) para fazer cálculos de intervalo de data e hora, como durações, para comparar intervalos e testar as interseções de intervalo.
- Use a nova classe [NSMeasurement](https://developer.apple.com/reference/foundation/nsmeasurement) para converter entre diferentes unidades de medida (unidade ea) ou executar cálculos em valores em diferentes UOMs.

- Use as novas classes [NSUnit](https://developer.apple.com/reference/foundation/nsunit) e [NSDimension](https://developer.apple.com/reference/foundation/nsdimension) para representar UOMs específicos.
- Várias novas propriedades foram adicionadas à classe [NSLocal](https://developer.apple.com/reference/foundation/nslocale) para adquirir informações locais e os formatos de exibição disponíveis.

## <a name="gamekit-enhancements"></a>Aprimoramentos do GameKit

Os aprimoramentos a seguir foram feitos na estrutura GameKit no iOS 10:

- O **aplicativo Game Center** foi preterido e removido do Ios. Se o aplicativo usar GameKit, ele _deverá_ apresentar sua própria interface para exibir recursos de GameKit, como placares, etc. 
- Um novo tipo de conta somente iCloud foi implementado pela classe [GKCloudPlayer](https://developer.apple.com/reference/gamekit/gkcloudplayer) .
- A nova classe [GKGameSession](https://developer.apple.com/reference/gamekit/gkgamesession) fornece uma solução generalizada para gerenciar o armazenamento de dados persistente em Game Center. `GKGameSession`mantém uma lista de players e o aplicativo é responsável por implementar como e quando a data do participante é armazenada, recuperada ou trocada entre os jogadores. Em muitas instâncias, as sessões de jogos podem substituir as correspondências existentes, correspondências em tempo real ou métodos de salvamento de jogos persistentes.

## <a name="gameplaykit-enhancements"></a>Aprimoramentos do GameplayKit

Os aprimoramentos a seguir foram feitos na estrutura GameplayKit no iOS 10:

- Use a nova classe [GKMeshGraph](https://developer.apple.com/reference/gameplaykit/gkmeshgraph) para fornecer caminhos de aparência natural de alto desempenho.
- A geração de ruído de procedimento foi adicionada e pode ser usada para aprimorar o realm nas texturas de aparência natural, adicionar Realm aos movimentos da câmera e ajudar a gerar mundos avançados de jogos.
- Use o particionamento espacial para particionar os dados do mundo do jogo para uma pesquisa eficiente.
- Um novo[GKMonteCarloStrategist](https://developer.apple.com/reference/gameplaykit/gkmontecarlostrategist)(Monte Carlo estrategista) foi adicionado para uma computação de movimentação exaustivamente possível.
- o suporte a 3D foi adicionado ao agente existente e aos comportamentos de localização de caminho usando as novas classes [GKAgent3D](https://developer.apple.com/reference/gameplaykit/gkagent3d) e [GKGraphNode3D](https://developer.apple.com/reference/gameplaykit/gkgraphnode3d) .
- As novas classes [GKScene](https://developer.apple.com/reference/gameplaykit/gkscene) e [GKSKNodeComponent](https://developer.apple.com/reference/gameplaykit/gksknodecomponent) tornam a combinação de GameplayKit e SpriteKit mais fácil do que nunca.
- Uma nova API de árvore de decisão foi adicionada ([GKDecisionTree](https://developer.apple.com/reference/gameplaykit/gkdecisiontree) e [GKDecisionNode](https://developer.apple.com/reference/gameplaykit/gkdecisionnode)) para aprimorar o ia de criação de jogos.

## <a name="healthkit-enhancements"></a>Aprimoramentos do HealthKit

Os aprimoramentos a seguir foram feitos na estrutura HealthKit no iOS 10:

- Foram adicionadas novas chaves de metadados para os tipos meteorológicos `HKWeatherConditionClear` ( `HKWeatherConditionCloudy`como e) e os tipos de `HKWorkoutActivityTypeFlexibility` treinamento `HKWorkoutActivityTypeWheelchairRunPace`(como e) foram adicionados.
- A nova `HKCDADocument` classe foi adicionada para representar um documento formatado da CDA (arquitetura de documento clínico).
- Use a nova classe [HKWorkoutConfiguration](https://developer.apple.com/reference/healthkit/hkworkoutconfiguration) para especificar o `ActivityType` e `LocationType` de um treinamento.
- O novo [HKWheelchairUseObject](https://developer.apple.com/reference/healthkit/hkwheelchairuseobject) e o `WheelchairUse` método da classe [HKHealthStore](https://developer.apple.com/reference/healthkit/hkhealthstore) foram adicionados para trabalhar com dados de integridade relacionados a cadeira de rodas.

## <a name="homekit-enhancements"></a>Aprimoramentos do HomeKit

Os aprimoramentos a seguir foram feitos na estrutura HomeKit no iOS 10:

- Novos serviços e características foram adicionados.
- Um iPad pode ser configurado para atuar como um hub HomeKit para fornecer acesso de acessório remoto, executar gatilhos de automação e habilitar permissões de usuário compartilhado.
- Foi adicionado suporte para acessórios de câmera e doorbell.
- Foram fornecidos mais contexto e configurações para acessórios.

Consulte nossa [introdução à](~/ios/platform/homekit.md) documentação do homekit para obter mais informações.

## <a name="metal-enhancements"></a>Aprimoramentos de metal

Os aprimoramentos a seguir foram feitos na estrutura de metal no iOS 10:

- os aplicativos e jogos 3D agora podem usar o mosaico para renderizar com eficiência cenas e geometria complexas por meio da GPU.
- Forneça um controle refinado de alocação de recursos para otimizar o desempenho de aplicativos baseados em metal usando heaps de recursos e destinos de renderização sem memória.
- Use a especialização de função para criar uma coleção altamente otimizada de funções de combinação de material e leve para uma cena.

Para saber mais, confira o [Guia de programação de metal](https://developer.apple.com/library/prerelease/content/documentation/Miscellaneous/Conceptual/MetalProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014221)da Apple.

## <a name="modelio-enhancements"></a>Aprimoramentos do ModelIO

Os aprimoramentos a seguir foram feitos na estrutura ModelIO no iOS 10:

- Agora há suporte para o formato de arquivo USD.
- O suporte ao campo de distância assinada foi adicionado à classe [MDLVoxelArray](https://developer.apple.com/reference/modelio/mdlvoxelarray) .
- Use a nova `MDLLightProbeIrradianceDataSource` classe para auxiliar no posicionamento da investigação leve.
- Use a nova `MDLMaterialPropertyGraph` classe para dar suporte facilmente a alterações de tempo de execução para modelos.

## <a name="photos-enhancements"></a>Aprimoramentos de fotos

Os aprimoramentos a seguir foram feitos na estrutura de fotos no iOS 10:

- Use as classes [CIImageProcessorInput](https://developer.apple.com/reference/coreimage/ciimageprocessorinput) e [CIImageProcessorOutput](https://developer.apple.com/reference/coreimage/ciimageprocessoroutput) para aproveitar o novo recurso de processador de imagem de núcleo para executar edições.
- A edição de fotos ao vivo agora está disponível para aplicativos que dão suporte à estrutura de fotos e às extensões de edição de fotos (para uso dentro dos aplicativos de fotos e câmera).
- Use a nova classe [PHLivePhotoEditingContext](https://developer.apple.com/reference/photos/phlivephotoeditingcontext) para aplicar edições ao vídeo e ainda ao conteúdo de fotos ao vivo.

## <a name="replaykit-enhancements"></a>Aprimoramentos do ReplayKit

Os aprimoramentos a seguir foram feitos na estrutura ReplayKit no iOS 10:

- Use as classes [RPScreenRecorder](https://developer.apple.com/reference/replaykit/rpscreenrecorder), [RPBroadcastActivityViewController](https://developer.apple.com/reference/replaykit/rpbroadcastactivityviewcontroller) e [RPBroadcastController](https://developer.apple.com/reference/replaykit/rpbroadcastcontroller) para dar suporte à transmissão de mídias gravadas por meio de sites de terceiros.
- A interface do usuário de difusão e as extensões de Broadcast Upload são necessárias para dar suporte a serviços de difusão de terceiros ReplayKit no aplicativo.

## <a name="scenekit-enhancements"></a>Aprimoramentos do SceneKit

Os aprimoramentos a seguir foram feitos na estrutura SceneKit no iOS 10:

- A classe [SCNCamera](xref:SceneKit.SCNCamera) pode fornecer maior Realm usando os recursos e efeitos HDR. Use a exposição adaptativa para criar efeitos automáticos ou use Vignetting, a borda da cor e a redução da cor para adicionar efeitos fillmatics ao jogo.
- O SceneKit agora inclui um novo sistema de processamento com base em física (PBR) para resultados mais Realos com a criação de ativos mais simples.
- Use o novo modelo de sombreamento [SCNLightingModelPhysicallyBased](https://developer.apple.com/reference/scenekit/scnlightingmodelphysicallybased) para obter uma ampla gama de efeitos de sombreamento realista, ao mesmo tempo que exige`Diffuse`apenas `Metalness` três `Roughness`propriedades fundamentais (e).
- Como o sombreamento do PBR funciona melhor com iluminação baseada em ambiente, `LightingEnvironment` use a propriedade para atribuir iluminação baseada em imagem a uma cena inteira.
- Use a `IESProfileURL` propriedade para importar os acessórios de luz do mundo real que definem a iluminação com base em valores reais, como intensidade (em lumens) e temperatura de cor (em graus Kelvin).
- Os recursos de câmera de PBR e HDR fornecem resultados melhores do que as técnicas de renderização tradicionais e, como resultado, o SceneKit agora executa todos os cálculos de cor em um espaço de cores linear (usando a gama de cores P3 em monitores de dispositivos de cores largos).
- A cor SceneKit Now corresponde a todas as cores lendo as informações do perfil de cor.
- SceneKit interpreta valores de componente de cor em um espaço de cores RGB linear para todos os tipos de sombreador.
- A renderização de espaço de cores linear e a grande cor podem ser desabilitadas `SCNDisableLinearSpaceRendering` especificando `SCNDisableWideGamut` as `Info.plist`chaves e no aplicativo.
- Crie Primates Polygon arbitrários (carregados a partir de arquivos ou gerados programaticamente) para especificar Geometry com a nova classe [SCNGeometryPrimitiveTypePolygon](https://developer.apple.com/reference/scenekit/1772322-scenekit_enumerations/scngeometryprimitivetype/scngeometryprimitivetypepolygon) .
- Como o SceneKit lê e ajusta as informações de perfil de cor em imagens de textura, use catálogos de ativos para todas as imagens para garantir que essas informações sejam fornecidas.

## <a name="spritekit-enhancements"></a>Aprimoramentos do SpriteKit

Os aprimoramentos a seguir foram feitos na estrutura SpriteKit no iOS 10:

- Os sombreadores personalizados podem fornecer atributos`SKAttribute`() que podem ser configurados separadamente por cada nó que usa o sombreador fornecendo um valor`SKAttributeValue`de atributo ().
- Tilemaps agora suportam as formas de peças quadradas, hexágonos e para 2D, 2,5 d e rolagem `SKTileMapMode`para jogos `SKTileGroupRule` usando `SKTileSet` as classes, `SKTileGroup`e.
- Use a nova `SKWarpGeometry` classe para alongar ou distorcer a renderização [SKSpriteNode](xref:SpriteKit.SKSpriteNode) ou [SKEffectNode](xref:SpriteKit.SKEffectNode) . A nova classe [SKAction](xref:SpriteKit.SKAction) pode ser usada para animar transições entre efeitos de distorção.
- A classe [SKView](xref:SpriteKit.SKView) fornece vários novos métodos para fornecer um controle refinado sobre quando e como uma cena é renderizada.

## <a name="scrollview-enhancements"></a>Aprimoramentos do ScrollView

Os aprimoramentos a seguir foram feitos no controle ScrollView no iOS 10,3:

- `UIScrollView`Agora inclua a `IndexDisplayMode` propriedade para controlar como o índice é mostrado enquanto o usuário está rolando `UIScrollViewIndexDisplayMode` como:
    - `Automatic`-A exibição do índice é controlada pelo sistema operacional.
    - `AlwaysHidden`-A exibição do índice está sempre oculta.

Consulte o [exemplo de iOSTenThree](https://docs.microsoft.com/samples/xamarin/ios-samples/ios10-iostenthree) para uso.

## <a name="uikit-enhancements"></a>Aprimoramentos do UIKit

Os aprimoramentos a seguir foram feitos na estrutura UIKit no iOS 10:

- A nova API [UIPasteboard](xref:UIKit.UIPasteboard) fornece novas opções (como limitações de tempo de vida) e irá declarar automaticamente os tipos de conteúdo compatíveis para tipos de classe comuns.
- Novo suporte à animação com interrupções, com base em objeto e totalmente interativo, foi adicionado e pode ser vinculado a gestos. Consulte a referência de [protocolo UIViewAnimating](https://developer.apple.com/reference/uikit/uiviewanimating)da Apple [, referência de classe UIViewPropertyAnimator](https://developer.apple.com/reference/uikit/uiviewpropertyanimator), referência de [protocolo UITimingCurveProvider](https://developer.apple.com/reference/uikit/uitimingcurveprovider), [referência de classe UICubicTimingParameters](https://developer.apple.com/reference/uikit/uicubictimingparameters) e [ Referência de classe UISpringTimingParameter](https://developer.apple.com/reference/uikit/uispringtimingparameters) para obter mais informações.
- O novo `UIPreviewInteraction` e `UIPreviewInteractionDelegate` o permitem que o aplicativo de desenvolvedor forneça uma interface personalizada para operações de Peek e pop.
- A nova `UIAccessibilityCustomRotor` classe permite que o aplicativo forneça funcionalidade personalizada e específica ao contexto para tecnologias assistenciais, como voz.
- Use os `UIAccessibilityIsAssistiveTouchRunning` símbolos `UIAccessibilityAssistiveTouchStatusDidChangeNotification` e para determinar se o AssistiveTouch está habilitado.
- Use os `UIAccessibilityHearingDevicePairedEar` símbolos `UIAccessibilityHearingDevicePairedEarDidChangeNotification` e para obter o status de quaisquer auxílios de audição acessórios MFI emparelhados.
- Para dar suporte ao tipo dinâmico em rótulos, campos `PreferredFontForTextStyle` `UIFont` de texto e caixas de texto, use o novo método da classe.
- Para decidir se um elemento deve atualizar sua fonte quando o dispositivo `UIContentSizeCategory` for alterado, use a `AdjustsFontForContentSizeCategory` Propriedade do `UIContentSizeCategoryAdjusting` delegado.
- O `OpenURL` método`UIApplication` da classe é chamado de forma assíncrona e agora dá suporte a um manipulador de conclusão que é chamado depois que a ação abrir é concluída.
- Inicie o compartilhamento de CloudKit e modifique suas propriedades usando `UICloudSharingController` as `UICloudSharingControllerDelegate` novas classes e.
- Aproveite as células de pré-busca para melhorar a experiência de rolagem do `UICollectionViews` com o novo `UICollectionViewDataSourcePrefetching` delegado.
- O desenvolvedor agora pode controlar a aparência da notificação para itens da barra de guias (como texto e cor do plano de fundo).
- O controle de atualização agora tem suporte em todas as subclasses View e Scroll View ( `UICollectionView`como).

## <a name="webkit-enhancements"></a>Aprimoramentos de WebKit

Os aprimoramentos a seguir foram feitos na estrutura WebKit no iOS 10:

- O `WKWebView` suporte de Peek e pop foi adicionado à classe. Use o `ShouldPreviewElement` método para determinar se uma determinada exibição da Web deve exibir uma visualização.


## <a name="related-links"></a>Links relacionados

- [Amostras do iOS 10](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS10)
- [O que há de novo no iOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewIniOS/Articles/iOS10.html#//apple_ref/doc/uid/TP40017084-SW1)
