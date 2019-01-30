---
title: Alterações de estruturas adicionais do iOS 10
description: Este documento descreve algumas pequenas alterações e aprimoramentos feitos para as estruturas existentes no iOS 10 e discute como disponibilizar usar essas atualizações no xamarin. IOS.
ms.prod: xamarin
ms.assetid: 0E2217F1-FC96-4D0A-ABAB-D40AD8F96502
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/29/2017
ms.openlocfilehash: a7b029aad69e65192d48d969dba2b9bb9a0d7a50
ms.sourcegitcommit: a1a58afea68912c79d16a3f64de9a0c1feb2aeb4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55233894"
---
# <a name="additional-ios-10-frameworks-changes"></a>Alterações de estruturas adicionais do iOS 10

_Este artigo aborda as alterações adicionais, secundárias ou aprimoramentos para as estruturas existentes do iOS 10._

## <a name="av-foundation-framework-additions"></a>Adições de Framework de AV Foundation

A estrutura de AVFoundation inclui os seguintes aprimoramentos:

- No iOS 10, o desenvolvedor não precisa implementar diferentes [AVPlayerItem](https://developer.xamarin.com/api/type/AVFoundation.AVPlayerItem/) comportamentos com base no tipo de conteúdo. Basta definir o `Rate` propriedade e AVFoundation determinará quando conteúdo suficiente está disponível para a reprodução sem atraso das.
- O novo [AVCapturePhotoOutput](https://developer.xamarin.com/api/type/AVFoundation.AVCaptureFileOutput/) classe substitui preteridas `AVCaptureStillImageOutput` de classe e fornece um método unificado para tratar todos os fluxos de trabalho de fotografia, fornecendo um controle sofisticado e monitoramento do processo de captura e suporte para novos recursos, como fotos ao vivo e o formato de captura não PROCESSADOS.
- O novo `AVPlayerLooper` classe torna mais fácil executar uma determinada parte da mídia de um loop durante a reprodução.
- O `AVAssetDownloadURLSession` classe permite o download e a reprodução do FairPlay posterior criptografados streams HLS.
- Por padrão, o [AVCaptureSession](https://developer.xamarin.com/api/type/AVFoundation.AVCaptureSession/) classe dá suporte automaticamente à captura toda a cor, toda a gama de cores quando o hardware do dispositivo dá suporte a ele. Consulte da Apple [referência de compatibilidade de dispositivos do iOS](https://developer.apple.com/library/prerelease/content/documentation/DeviceInformation/Reference/iOSDeviceCompatibility/Introduction/Introduction.html#//apple_ref/doc/uid/TP40013599) para obter mais detalhes.

## <a name="avkit-additions"></a>Adições de AVKit

A estrutura de AVKit agora inclui o novo `UpdatesNowPlayingInfoCenter` propriedade para indicar quando o Centro de informações do jogo agora deve ser atualizado.

## <a name="core-data-enhancements"></a>Principais aprimoramentos de dados

iOS 10 inclui os seguintes aprimoramentos para a estrutura de dados principais:

- O [NSManagedObjectContext](https://developer.xamarin.com/api/type/CoreData.NSManagedObjectContext/) objetos com armazenamentos de dados SQLite no suporte a nova geração de consulta WAL diário modo em que contextos de objeto gerenciado (MOC) podem ser fixados em versões de banco de dados específico para buscar futuras de recursos e transações com falha.
- Raiz [NSManagedObjectContext](https://developer.xamarin.com/api/type/CoreData.NSManagedObjectContext/) simultâneo com falha e buscando sem serialização dá suporte a objetos.
- O [NSPersistentStoreCoordinator](https://developer.xamarin.com/api/type/CoreData.NSPersistentStoreCoordinator/) classe mantém um pool de armazenamentos de dados SQLite.
- Vários métodos de conveniência novos foram adicionados ao `NSManagedObject` tornando mais fácil executar buscas e criar subclasses.
- Usando o alto nível `NSPersistenceContainer` a referência a `NSPersistentStoreCoordinator`, [NSManagedObjectModel](https://developer.xamarin.com/api/type/CoreData.NSManagedObjectModel/) e outros recursos de configuração de dados principal.

Para obter mais informações, consulte da Apple [referência de estrutura de dados de núcleo](https://developer.apple.com/reference/coredata).

## <a name="core-image-enhancements"></a>Principais aprimoramentos de imagem

iOS 10 faz com que os seguintes aprimoramentos para a estrutura de imagem principal:

- O desenvolvedor agora pode processar imagens em um espaço de cores fora do espaço de cor de trabalho do contexto de núcleo de imagem, convertendo para dentro e fora do espaço de cor antes e após o processamento.
- Para dispositivos iOS que usam o A8 ou A9 CPUs, agora há suporte para o formato da imagem BRUTA. Imagem de Core agora oferece suporte para a câmera interna iSight de decodificação de imagens RAW de qualquer um ou de uma câmera 3ª de terceiros. Use o `FilterWithImageData` ou `FilterWithImageURL` métodos das [CIFilter](https://developer.xamarin.com/api/type/CoreImage.CIFilter/) classe para processar imagens RAW.
- Foram feitas várias melhorias de desempenho de renderização para `UIImage` renderizando (quando o apoio de repositórios de imagem da imagem principal) em `UIImageView` objetos. 
- `UIImage` objetos marcados toda a gama de cores será renderizado como toda a gama de cor em `UIImageView` objetos em dispositivos iOS que dão suporte a cores de largura.
- Código de kernel de imagem Core agora pode solicitar a formatos de saída do pixel específico.
- O `ImageWithExtent` método da [CIFilter](https://developer.xamarin.com/api/type/CoreImage.CIFilter/) classe pode ser usada para inserir o processamento personalizado para a operação de filtro. Imagem principal invoque o retorno de chamada fornecido entre os filtros ao processar uma imagem de saída ou a exibir.

Além disso, os seguintes novos filtros de imagem Core foram adicionados:

- `CINinePartTiled`
- `CINinePartStretched`
- `CIHueSaturationValueGradient`
- `CIEdgePreserveUpsampleFilter`
- `CIClamp`

## <a name="core-motion-additions"></a>Adições de animação de núcleo

Novo para o iOS 10, a estrutura de animação de núcleo inclui eventos pedometer que permitem que um aplicativo rápido e receba notificações em tempo real do usuário pausar e retomar o controle durante a execução. Use o [CMPedometer](https://developer.xamarin.com/api/type/CoreMotion.CMPedometer/) para registrar eventos de pedometer primeiro ou segundo plano.

## <a name="foundation-enhancements"></a>Aprimoramentos do Foundation

As seguintes melhorias foram feitas para a estrutura de base para o iOS 10:

- Use a nova [NSMeasurementFormatter](https://developer.apple.com/reference/foundation/nsmeasurementformatter) classe Formatar medidas localizadas para a exibição para o usuário final.
- Use a nova [NSDateInterval](https://developer.apple.com/reference/foundation/nsdateinterval) classe para fazer cálculos de intervalo de data e hora como durações, para comparar os intervalos e teste para interseções de intervalo.
- Use a nova [NSMeasurement](https://developer.apple.com/reference/foundation/nsmeasurement) classe para converter entre diferentes unidades de medida (unidade de medida) ou executar cálculos nos valores de UOMs diferentes.

- Use a nova [NSUnit](https://developer.apple.com/reference/foundation/nsunit) e [NSDimension](https://developer.apple.com/reference/foundation/nsdimension) classes para representar UOMs específicos.
- Várias novas propriedades foram adicionadas para o [NSLocal](https://developer.apple.com/reference/foundation/nslocale) classe para adquirir informações locais e os formatos de exibição disponíveis.

## <a name="gamekit-enhancements"></a>Aprimoramentos de GameKit

As seguintes melhorias foram feitas para a estrutura GameKit no iOS 10:

- O **Game Center aplicativo** foi preterido e removido do iOS. Se o aplicativo usa GameKit, ele _deve_ apresentar sua própria interface para exibir os recursos de GameKit como placares de líderes, etc. 
- Um novo tipo de conta somente para iCloud foi implementado pelo [GKCloudPlayer](https://developer.apple.com/reference/gamekit/gkcloudplayer) classe.
- O novo [GKGameSession](https://developer.apple.com/reference/gamekit/gkgamesession) classe fornece uma solução generalizada para gerenciar o armazenamento de dados persistentes no Game Center. `GKGameSession` mantém uma lista de jogadores e o aplicativo é responsável por implementar como e quando a data de participante é armazenada, recuperada ou trocada entre os jogadores. Em muitos casos as sessões de jogos pode substituir correspondências existentes baseada em turno, correspondências em tempo real ou jogo persistente salvar métodos.

## <a name="gameplaykit-enhancements"></a>Aprimoramentos de GameplayKit

As seguintes melhorias foram feitas para a estrutura GameplayKit no iOS 10:

- Use a nova [GKMeshGraph](https://developer.apple.com/reference/gameplaykit/gkmeshgraph) classe para fornecer caminhos de alto desempenho e a aparência natural.
- Geração de ruído procedural foi adicionada e pode ser usada para aprimorar o realismo aparência natural das texturas, adicione realismo para movimentos de câmera e ajudar a gerar mundos de jogos avançados.
- Use o particionamento espacial para particionar os dados do mundo do jogo para a pesquisa eficiente.
- Estrategista de Monte Carlo nova ([GKMonteCarloStrategist](https://developer.apple.com/reference/gameplaykit/gkmontecarlostrategist)) foi adicionado para a computação de mudança completa de possíveis.
- Foi adicionado suporte 3D para o agente existente e os comportamentos de localização de caminho usando a nova [GKAgent3D](https://developer.apple.com/reference/gameplaykit/gkagent3d) e [GKGraphNode3D](https://developer.apple.com/reference/gameplaykit/gkgraphnode3d) classes.
- O novo [GKScene](https://developer.apple.com/reference/gameplaykit/gkscene) e [GKSKNodeComponent](https://developer.apple.com/reference/gameplaykit/gksknodecomponent) make classes combinando GameplayKit e SpriteKit mais fácil do que nunca.
- Foi adicionada uma nova API de árvore de decisão ([GKDecisionTree](https://developer.apple.com/reference/gameplaykit/gkdecisiontree) e [GKDecisionNode](https://developer.apple.com/reference/gameplaykit/gkdecisionnode)) para aprimorar o AI de jogo de construção.

## <a name="healthkit-enhancements"></a>Aprimoramentos de HealthKit

As seguintes melhorias foram feitas para a estrutura HealthKit no iOS 10:

- Foram adicionadas novas chaves de metadados para tipos de clima (como `HKWeatherConditionClear` e `HKWeatherConditionCloudy`) e tipos de treinamento (como `HKWorkoutActivityTypeFlexibility` e `HKWorkoutActivityTypeWheelchairRunPace`) foram adicionadas.
- O novo `HKCDADocument` classe foi adicionada representar uma arquitetura de documento clínico (CDA) formatada no documento.
- Use a nova [HKWorkoutConfiguration](https://developer.apple.com/reference/healthkit/hkworkoutconfiguration) classe para especificar o `ActivityType` e `LocationType` de uma ginástica.
- O novo [HKWheelchairUseObject](https://developer.apple.com/reference/healthkit/hkwheelchairuseobject) e o `WheelchairUse` método da [HKHealthStore](https://developer.apple.com/reference/healthkit/hkhealthstore) classe foram adicionadas para trabalhar com cadeiras de rodas nos dados de integridade relacionados.

## <a name="homekit-enhancements"></a>Aprimoramentos do HomeKit

As seguintes melhorias foram feitas para a estrutura HomeKit no iOS 10:

- Foram adicionadas novos serviços e características.
- Um iPad pode ser configurado para atuar como compartilhado de um Hub de HomeKit para fornecer acesso remoto de acessório, executar disparadores de automação e habilitar permissões de usuário.
- Foi adicionado suporte para Acessórios de câmera e a campainha.
- Mais de contexto e as configurações foram fornecidas para Acessórios.

Consulte nosso [Introdução ao HomeKit](~/ios/platform/homekit.md) documentação para obter mais informações.

## <a name="metal-enhancements"></a>Aprimoramentos de metal

As seguintes melhorias foram feitas à estrutura de Metal no iOS 10:

- Aplicativos 3D e jogos agora podem usar _mosaico_ para processar com eficiência cenas complexas e geometry por meio da GPU.
- Fornece controle refinado de alocação de recursos para otimizar o desempenho do sistema operacional com base em aplicativos usando o recurso de Heaps e Memoryless destinos de renderização.
- Use a especialização de função para criar uma coleção altamente otimizado de materiais e funções de combinação de luz para uma cena.

Para obter mais informações, consulte da Apple [guia de programação de Metal](https://developer.apple.com/library/prerelease/content/documentation/Miscellaneous/Conceptual/MetalProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014221).

## <a name="modelio-enhancements"></a>Aprimoramentos de ModelIO

As seguintes melhorias foram feitas para a estrutura ModelIO no iOS 10:

 - Agora há suporte para o formato de arquivo USD.
 - Assinado campo distância foi adicionado suporte para o [MDLVoxelArray](https://developer.apple.com/reference/modelio/mdlvoxelarray) classe.
 - Usar o novo `MDLLightProbeIrradianceDataSource` classe para auxiliar na investigação de luz de posicionamento.
 - Usar o novo `MDLMaterialPropertyGraph` classe para suportar facilmente o tempo de execução é alterado para modelos.

## <a name="photos-enhancements"></a>Aprimoramentos de fotos

As seguintes melhorias foram feitas à estrutura de fotos no iOS 10:

- Use o [CIImageProcessorInput](https://developer.apple.com/reference/coreimage/ciimageprocessorinput) e [CIImageProcessorOutput](https://developer.apple.com/reference/coreimage/ciimageprocessoroutput) classes para tirar proveito do novo recurso de processador de imagem principal para realizar edições.
- Edição de fotos ao vivo agora está disponível para aplicativos que dão suporte a estrutura de fotos e extensões de edição de fotos (para uso dentro de fotos e câmera aplicativos).
- Use a nova [PHLivePhotoEditingContext](https://developer.apple.com/reference/photos/phlivephotoeditingcontext) classe ao aplicar edições para o vídeo e ainda o conteúdo de fotos ao vivo.

## <a name="replaykit-enhancements"></a>Aprimoramentos de ReplayKit

As seguintes melhorias foram feitas para a estrutura ReplayKit no iOS 10:

- Use o [RPScreenRecorder](https://developer.apple.com/reference/replaykit/rpscreenrecorder), [RPBroadcastActivityViewController](https://developer.apple.com/reference/replaykit/rpbroadcastactivityviewcontroller) e [RPBroadcastController](https://developer.apple.com/reference/replaykit/rpbroadcastcontroller) registradas de classes para dar suporte a difusão de mídia por meio de 3ª parte sites.
- As extensões da interface do usuário de difusão e difusão carregar são necessários para dar suporte a serviços de difusão de terceiros ReplayKit 3º no aplicativo.

## <a name="scenekit-enhancements"></a>Aprimoramentos do SceneKit

As seguintes melhorias foram feitas para a estrutura de SceneKit do iOS 10:

- O [SCNCamera](xref:SceneKit.SCNCamera) classe pode fornecer maior realismo usando recursos HDR e efeitos. Use exposição adaptável para criar efeitos automática ou use vinheta, margem das cores e cor de classificação para adicionar fillmatic efeitos no jogo.
- SceneKit agora inclui um novo sistema fisicamente com base em processamento (PBR) para obter resultados mais realistas com a criação de ativo mais simples.
- Use a nova [SCNLightingModelPhysicallyBased](https://developer.apple.com/reference/scenekit/scnlightingmodelphysicallybased) sombreamento de modelo para o produto uma ampla variedade de efeitos de sombreamento realístico, exigindo apenas três propriedades fundamentais (`Diffuse`, `Metalness` e `Roughness`).
- Desde PBR sombreamento funciona melhor com iluminação baseada em ambiente, use o `LightingEnvironment` propriedade para atribuir iluminação baseada na imagem para uma cena inteira.
- Use o `IESProfileURL` propriedade para importar os acessórios de iluminação do mundo real que definem a iluminação com base nos valores de mundo real, como a intensidade (em lumens) e a temperatura de cor (em graus Kelvin).
- Recursos de câmera PBR e HDR fornecem resultados melhores que as técnicas tradicionais de renderização e, como resultado, SceneKit agora executa todos os cálculos de cor em um espaço de cor linear (usando P3 gama de cores na cor de todo o dispositivo exibe).
- Cor do SceneKit agora corresponde a todas as cores lendo as informações de perfil de cor.
- SceneKit interpretará valores de componente de cor em um espaço de cor RGB linear para todos os tipos de sombreador.
- Renderização de espaço de cor linear e toda a cor pode ser desabilitado especificando o `SCNDisableLinearSpaceRendering` e `SCNDisableWideGamut` chaves do aplicativo `Info.plist`.
- Compilar primates polígono arbitrário (ou carregado de arquivos ou gerados por meio de programação) para especificar a geometria com o novo [SCNGeometryPrimitiveTypePolygon](https://developer.apple.com/reference/scenekit/1772322-scenekit_enumerations/scngeometryprimitivetype/scngeometryprimitivetypepolygon) classe.
- Como SceneKit lê e ajustar para obter informações de perfil de cor nas imagens de textura, use os catálogos de ativos para todas as imagens para garantir que essas informações são fornecidas.

## <a name="spritekit-enhancements"></a>Aprimoramentos de SpriteKit

As seguintes melhorias foram feitas para a estrutura de SpriteKit do iOS 10:

- Sombreadores personalizados podem fornecer atributos (`SKAttribute`) que pode ser configurada separadamente por cada nó que usa o sombreador fornecendo um valor de atributo (`SKAttributeValue`).
- Tilemaps agora dão suporte a formas de bloco quadrado, hexagonal e isométrica para 2D, 2.5D e rolagem de lado jogos usando o `SKTileMapMode`, `SKTileGroup`, `SKTileGroupRule` e `SKTileSet` classes.
- Use a nova `SKWarpGeometry` classe para alongar ou distorcer [SKSpriteNode](https://developer.xamarin.com/api/type/SpriteKit.SKSpriteNode/) ou [SKEffectNode](https://developer.xamarin.com/api/type/SpriteKit.SKEffectNode/) renderização. O novo [SKAction](https://developer.xamarin.com/api/type/SpriteKit.SKAction/) classe pode ser usada para animar as transições entre os efeitos de warp.
- O [SKView](https://developer.xamarin.com/api/type/SpriteKit.SKView/) classe fornece vários novos métodos para fornecer controle refinado sobre quando e como uma cena é renderizada.

## <a name="scrollview-enhancements"></a>Aprimoramentos de ScrollView

As seguintes melhorias foram feitas ao controle no iOS 10.3 ScrollView:

- `UIScrollView` agora incluem o `IndexDisplayMode` propriedade para controlar como o índice é mostrado enquanto o usuário está rolando como um `UIScrollViewIndexDisplayMode` de:
    - `Automatic` -A exibição do índice é controlada pelo sistema operacional.
    - `AlwaysHidden` -A exibição do índice sempre está oculto.

Consulte a [iOSTenThree exemplo](https://developer.xamarin.com/samples/monotouch/iOS10/iOSTenThree) para uso.

## <a name="uikit-enhancements"></a>Aprimoramentos de UIKit

As seguintes melhorias foram feitas para a estrutura UIKit no iOS 10:

- O novo [UIPasteboard](xref:UIKit.UIPasteboard) API fornece novas opções (como as limitações de tempo de vida) e automaticamente irá declarar tipos de conteúdo compatíveis para tipos comuns de classe.
- Novo suporte a animação totalmente interativos, com base em objeto, passível de interrupção foi adicionado e pode ser vinculado a gestos. Consulte da Apple [referência de protocolo UIViewAnimating](https://developer.apple.com/reference/uikit/uiviewanimating), [referência de classe UIViewPropertyAnimator](https://developer.apple.com/reference/uikit/uiviewpropertyanimator), [referência de protocolo UITimingCurveProvider](https://developer.apple.com/reference/uikit/uitimingcurveprovider), [Referência de classe UICubicTimingParameters](https://developer.apple.com/reference/uikit/uicubictimingparameters) e [referência de classe UISpringTimingParameter](https://developer.apple.com/reference/uikit/uispringtimingparameters) para obter mais informações.
- O novo `UIPreviewInteraction` e `UIPreviewInteractionDelegate` permitem que o aplicativo do desenvolvedor fornecer uma interface personalizada para operações de inspeção e pop.
- O novo `UIAccessibilityCustomRotor` classe permite que o aplicativo fornecer funcionalidade personalizada, o contexto específico para tecnologias auxiliares, como a VoiceOver.
- Use o `UIAccessibilityIsAssistiveTouchRunning` e `UIAccessibilityAssistiveTouchStatusDidChangeNotification` símbolos para determinar se as AssistiveTouch está habilitado.
- Use o `UIAccessibilityHearingDevicePairedEar` e `UIAccessibilityHearingDevicePairedEarDidChangeNotification` símbolos para obter o status de qualquer emparelhado ajudas aguardamos seu contato de MFi.
- Para dar suporte a tipo dinâmico em rótulos, campos de texto e caixas de texto usam a nova `PreferredFontForTextStyle` método da `UIFont` classe.
- Para decidir se um elemento deve atualizar sua fonte quando o dispositivo `UIContentSizeCategory` alterações, use o `AdjustsFontForContentSizeCategory` propriedade do `UIContentSizeCategoryAdjusting` delegar.
- O `OpenURL` método da `UIApplication` classe é chamado de forma assíncrona e agora dá suporte a um manipulador de conclusão é chamado depois que a ação de abertura foi concluída.
- Iniciar o compartilhamento do CloudKit e modifique suas propriedades usando o novo `UICloudSharingController` e `UICloudSharingControllerDelegate` classes.
- Tirar proveito das células de pré-busca para melhorar a experiência de rolagem do `UICollectionViews` com o novo `UICollectionViewDataSourcePrefetching` delegar.
- O desenvolvedor agora pode controlar a aparência de notificação para itens da barra de guia (como cor do plano de fundo e texto).
- Agora há suporte para o controle de atualização em todas as exibição de rolagem e subclasses do modo de exibição de rolagem (como `UICollectionView`).

## <a name="webkit-enhancements"></a>Aprimoramentos do WebKit

As seguintes melhorias foram feitas para a estrutura do WebKit no iOS 10:

- Espiada e suporte de pop-up foi adicionada para o `WKWebView` classe. Use o `ShouldPreviewElement` método para determinar se uma exibição da web especificado deve exibir uma visualização.


## <a name="related-links"></a>Links relacionados

- [Amostras do iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
- [O que há de novo no iOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewIniOS/Articles/iOS10.html#//apple_ref/doc/uid/TP40017084-SW1)
