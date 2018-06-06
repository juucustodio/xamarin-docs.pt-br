---
title: Alterações de estruturas de iOS adicionais 10
description: Este documento descreve algumas pequenas alterações e aprimoramentos para as estruturas existentes no iOS 10 e discute como fazer usar essas atualizações no xamarin.
ms.prod: xamarin
ms.assetid: 0E2217F1-FC96-4D0A-ABAB-D40AD8F96502
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/29/2017
ms.openlocfilehash: 4b9a230157593b66446e2949e57a925d94208752
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34787553"
---
# <a name="additional-ios-10-frameworks-changes"></a>Alterações de estruturas de iOS adicionais 10

_Este artigo aborda as alterações adicionais, secundárias ou melhorias em estruturas existentes para iOS 10._

## <a name="av-foundation-framework-additions"></a>Adições de Framework AV Foundation

A estrutura AVFoundation inclui os seguintes aprimoramentos:

- No iOS 10, o desenvolvedor não precisa implementar diferentes [AVPlayerItem](https://developer.xamarin.com/api/type/AVFoundation.AVPlayerItem/) comportamentos com base no tipo de conteúdo. Basta definir o `Rate` propriedade e AVFoundation determinará quando conteúdo suficiente está disponível para a reprodução sem atrasando.
- O novo [AVCapturePhotoOutput](https://developer.xamarin.com/api/type/AVFoundation.AVCaptureFileOutput/) classe substitui preterido `AVCaptureStillImageOutput` classe e fornece um método unificado para tratar todos os fluxos de trabalho de fotografia fornecendo um controle sofisticado e monitoramento do processo de captura e suporte para novos recursos, como fotos ao vivo e o formato de captura não PROCESSADOS.
- O novo `AVPlayerLooper` classe torna mais fácil repetir um determinado item de mídia durante a reprodução.
- O `AVAssetDownloadURLSession` classe permite o download e reprodução posterior de FairPlay criptografado streams HLS.
- Por padrão, o [AVCaptureSession](https://developer.xamarin.com/api/type/AVFoundation.AVCaptureSession/) classe automaticamente oferece suporte a toda a cor, toda a gama de captura quando o dispositivo de hardware dá suporte a ele. Consulte da Apple [iOS referência de compatibilidade do dispositivo](https://developer.apple.com/library/prerelease/content/documentation/DeviceInformation/Reference/iOSDeviceCompatibility/Introduction/Introduction.html#//apple_ref/doc/uid/TP40013599) para obter mais detalhes.

## <a name="avkit-additions"></a>Adições de AVKit

A estrutura AVKit agora inclui o novo `UpdatesNowPlayingInfoCenter` propriedade para indicar quando o Centro de informações de execução agora devem ser atualizado.

## <a name="core-data-enhancements"></a>Principais aprimoramentos de dados

iOS 10 inclui os seguintes aprimoramentos para a estrutura de dados principais:

- O [NSManagedObjectContext](https://developer.xamarin.com/api/type/CoreData.NSManagedObjectContext/) objetos com repositórios de dados SQLite no suporte a WAL diário modo a nova geração de consulta de recursos onde contextos de objeto gerenciado (MOC) podem ser fixados em versões de banco de dados específico para buscar futuras e transações com falha.
- Raiz [NSManagedObjectContext](https://developer.xamarin.com/api/type/CoreData.NSManagedObjectContext/) simultâneos com falha e buscar sem serialização dá suporte a objetos.
- O [NSPersistentStoreCoordinator](https://developer.xamarin.com/api/type/CoreData.NSPersistentStoreCoordinator/) classe mantém um pool de armazenamentos de dados SQLite.
- Foram adicionados vários novos métodos de conveniência para `NSManagedObject` tornando mais fácil executar buscas e criar subclasses.
- Usando o alto nível `NSPersistenceContainer` a referência a `NSPersistentStoreCoordinator`, [NSManagedObjectModel](https://developer.xamarin.com/api/type/CoreData.NSManagedObjectModel/) e outros recursos de configuração de dados principal.

Para obter mais informações, consulte da Apple [referência de estrutura de dados principal](https://developer.apple.com/reference/coredata).

## <a name="core-image-enhancements"></a>Principais aprimoramentos de imagem

iOS 10 faz com que os seguintes aprimoramentos para a estrutura de imagem principal:

- O desenvolvedor agora pode processar imagens em um espaço de cores fora do espaço de cor de trabalho do contexto de imagem principal convertendo dentro e fora do espaço de cor antes e após o processamento.
- Para dispositivos iOS que usam o A8 ou A9 CPUs, agora há suporte para o formato de imagem não PROCESSADOS. Imagem principal agora fornece suporte para decodificação de imagens BRUTAS do câmera iSight interno ou de uma câmera parte 3. Use o `FilterWithImageData` ou `FilterWithImageURL` métodos do [CIFilter](https://developer.xamarin.com/api/type/CoreImage.CIFilter/) classe processar imagens RAW.
- Foram feitas várias melhorias de desempenho de renderização para `UIImage` processamento (quando feito por armazenamentos de imagem de imagem principal) em `UIImageView` objetos. 
- `UIImage` objetos marcados toda a gama de cores será renderizado como toda a gama de cores no `UIImageView` objetos em dispositivos iOS que dão suporte a cores de largura.
- Código de kernel de imagem principal agora pode solicitar os formatos de saída específicas.
- O `ImageWithExtent` método o [CIFilter](https://developer.xamarin.com/api/type/CoreImage.CIFilter/) classe pode ser usada para inserir o processamento personalizado na operação de filtro. Imagem principal será invocar o retorno de chamada especificado entre os filtros ao processar uma imagem de saída ou exibir.

Além disso, os seguintes novos filtros de imagem principal foram adicionados:

- `CINinePartTiled`
- `CINinePartStretched`
- `CIHueSaturationValueGradient`
- `CIEdgePreserveUpsampleFilter`
- `CIClamp`

## <a name="core-motion-additions"></a>Adições de movimento de núcleo

Novo para iOS 10, o framework Core movimento inclui eventos de pedometer que permitem que um aplicativo rápido e receber notificações em tempo real do usuário pausar e retomar controle durante a execução. Use o [CMPedometer](https://developer.xamarin.com/api/type/CoreMotion.CMPedometer/) para registrar eventos de pedometer primeiro ou segundo plano.

## <a name="foundation-enhancements"></a>Aprimoramentos de base

As seguintes melhorias foram feitas para a estrutura de base para iOS 10:

- Use a nova [NSMeasurementFormatter](https://developer.apple.com/reference/foundation/nsmeasurementformatter) classe Formatar medidas localizadas para a exibição para o usuário final.
- Use a nova [NSDateInterval](https://developer.apple.com/reference/foundation/nsdateinterval) classe para fazer cálculos de intervalo de data e hora como durações, para comparar os intervalos e testando interseções de intervalo.
- Use a nova [NSMeasurement](https://developer.apple.com/reference/foundation/nsmeasurement) classe para converter entre diferentes unidades de medida (unidade de medida) ou executar cálculos nos valores de UOMs diferentes.

- Use a nova [NSUnit](https://developer.apple.com/reference/foundation/nsunit) e [NSDimension](https://developer.apple.com/reference/foundation/nsdimension) classes para representar UOMs específicos.
- Várias novas propriedades foram adicionadas para o [NSLocal](https://developer.apple.com/reference/foundation/nslocale) classe para adquirir informações locais e os formatos de exibição disponíveis.

## <a name="gamekit-enhancements"></a>Aprimoramentos de GameKit

As seguintes melhorias foram feitas para a estrutura GameKit no iOS 10:

- O **Game Center aplicativo** foi preterido e removidos do iOS. Se o aplicativo usa GameKit, ele _deve_ apresentar sua própria interface para exibir os recursos de GameKit, como tabelas, etc. 
- Um novo tipo de conta somente iCloud foi implementado pelo [GKCloudPlayer](https://developer.apple.com/reference/gamekit/gkcloudplayer) classe.
- O novo [GKGameSession](https://developer.apple.com/reference/gamekit/gkgamesession) classe fornece uma solução generalizada para gerenciamento de armazenamento de dados persistentes no Game Center. `GKGameSession` mantém uma lista de participantes e o aplicativo é responsável por implementar como e quando a data de participante é armazenada, recuperada ou trocada entre os participantes. Em muitas instâncias sessões jogo pode substituir correspondências existentes com base por sua vez, correspondências em tempo real ou jogo persistente salvar métodos.

## <a name="gameplaykit-enhancements"></a>Aprimoramentos de GameplayKit

As seguintes melhorias foram feitas para a estrutura GameplayKit no iOS 10:

- Use a nova [GKMeshGraph](https://developer.apple.com/reference/gameplaykit/gkmeshgraph) classe para fornecer caminhos de alto desempenho, procurando natural.
- Geração de ruído procedimento foi adicionada e pode ser usada para aprimorar o realismo em natural de aparência, adicionar realismo movimentos de câmera e ajudar a gerar mundos jogos avançados.
- Use o particionamento espacial para particionar os dados de jogos do mundo para pesquisa eficiente.
- Estrategista de Monte Carlo novo ([GKMonteCarloStrategist](https://developer.apple.com/reference/gameplaykit/gkmontecarlostrategist)) foi adicionado para a computação de movimentação possíveis completa.
- Foi adicionado suporte 3D para o agente existente e comportamentos de localização do caminho usando o novo [GKAgent3D](https://developer.apple.com/reference/gameplaykit/gkagent3d) e [GKGraphNode3D](https://developer.apple.com/reference/gameplaykit/gkgraphnode3d) classes.
- O novo [GKScene](https://developer.apple.com/reference/gameplaykit/gkscene) e [GKSKNodeComponent](https://developer.apple.com/reference/gameplaykit/gksknodecomponent) criar classes combinando GameplayKit e SpriteKit mais fácil.
- Foi adicionada uma nova API de árvore de decisão ([GKDecisionTree](https://developer.apple.com/reference/gameplaykit/gkdecisiontree) e [GKDecisionNode](https://developer.apple.com/reference/gameplaykit/gkdecisionnode)) para aprimorar a construção de jogo AI.

## <a name="healthkit-enhancements"></a>Aprimoramentos de HealthKit

As seguintes melhorias foram feitas para a estrutura HealthKit no iOS 10:

- Foram adicionadas novas chaves de metadados para tipos de clima (como `HKWeatherConditionClear` e `HKWeatherConditionCloudy`) e tipos de treinamento (como `HKWorkoutActivityTypeFlexibility` e `HKWorkoutActivityTypeWheelchairRunPace`) foram adicionadas.
- O novo `HKCDADocument` classe foi adicionada representar uma arquitetura de documento clínicas (CDA) formatado no documento.
- Use a nova [HKWorkoutConfiguration](https://developer.apple.com/reference/healthkit/hkworkoutconfiguration) classe para especificar o `ActivityType` e `LocationType` de um treinamento.
- O novo [HKWheelchairUseObject](https://developer.apple.com/reference/healthkit/hkwheelchairuseobject) e o `WheelchairUse` método o [HKHealthStore](https://developer.apple.com/reference/healthkit/hkhealthstore) classe foram adicionados para trabalhar com cadeira de rodas integridade de dados relacionados.

## <a name="homekit-enhancements"></a>Aprimoramentos de HomeKit

As seguintes melhorias foram feitas para a estrutura HomeKit no iOS 10:

- Foram adicionadas novos serviços e características.
- Um iPad pode ser configurado para atuar como um HomeKit Hub para fornecer acesso remoto de acessório, executar disparadores de automação e habilitar compartilhadas permissões de usuário.
- Foi adicionado suporte para Acessórios de câmera e campainha.
- Foi fornecidas mais de contexto e configurações de Acessórios.

Confira nosso [Introdução ao HomeKit](~/ios/platform/homekit.md) documentação para obter mais informações.

## <a name="metal-enhancements"></a>Aprimoramentos de metal

As seguintes melhorias foram feitas à estrutura de Metal no iOS 10:

- Aplicativos 3D e jogos agora podem usar _mosaico_ para processar com eficiência cenas complexas e geometry por meio da GPU.
- Fornece controle refinado de alocação de recursos para otimizar o desempenho do sistema operacional com base em aplicativos que usam o recurso de Heaps e destinos Memoryless renderizar.
- Use a função especialização para criar uma coleção altamente otimizado de material e funções de combinação de luz para uma cena.

Para obter mais informações, consulte da Apple [guia de programação de Metal](https://developer.apple.com/library/prerelease/content/documentation/Miscellaneous/Conceptual/MetalProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014221).

## <a name="modelio-enhancements"></a>Aprimoramentos de ModelIO

As seguintes melhorias foram feitas para a estrutura ModelIO no iOS 10:

 - Agora há suporte para o formato de arquivo USD.
 - Assinado distância campo foi adicionado suporte para o [MDLVoxelArray](https://developer.apple.com/reference/modelio/mdlvoxelarray) classe.
 - Use a nova `MDLLightProbeIrradianceDataSource` classe para auxiliar na investigação de luz de posicionamento.
 - Use a nova `MDLMaterialPropertyGraph` classe para suportar facilmente o tempo de execução é alterado para modelos.

## <a name="photos-enhancements"></a>Aprimoramentos de fotos

As seguintes melhorias foram feitas à estrutura de fotos no iOS 10:

- Use o [CIImageProcessorInput](https://developer.apple.com/reference/coreimage/ciimageprocessorinput) e [CIImageProcessorOutput](https://developer.apple.com/reference/coreimage/ciimageprocessoroutput) classes para tirar proveito do novo recurso de processador de imagem principal para executar edições.
- Edição de foto ao vivo está disponível para aplicativos que dão suporte a estrutura de fotos e extensões de edição de fotos (para uso dentro de fotos e câmera aplicativos).
- Use a nova [PHLivePhotoEditingContext](https://developer.apple.com/reference/photos/phlivephotoeditingcontext) classe ao aplicar edições para o vídeo e ainda conteúdo de fotos em tempo real.

## <a name="replaykit-enhancements"></a>Aprimoramentos de ReplayKit

As seguintes melhorias foram feitas para a estrutura ReplayKit no iOS 10:

- Use o [RPScreenRecorder](https://developer.apple.com/reference/replaykit/rpscreenrecorder), [RPBroadcastActivityViewController](https://developer.apple.com/reference/replaykit/rpbroadcastactivityviewcontroller) e [RPBroadcastController](https://developer.apple.com/reference/replaykit/rpbroadcastcontroller) classes para dar suporte a difusão de registradas mídia por meio de 3ª parte sites.
- As extensões de difusão de UI e carregamento de transmissão são necessárias para dar suporte a serviços de transmissão ReplayKit 3ª parte no aplicativo.

## <a name="scenekit-enhancements"></a>Aprimoramentos de SceneKit

As seguintes melhorias foram feitas para a estrutura SceneKit no iOS 10:

- O [SCNCamera](https://developer.xamarin.com/api/type/SceneKit.SCNCamera/) classe pode fornecer maior realismo usando recursos HDR e efeitos. Use exposição adaptável para criar efeitos automática ou use vinheta, margem das cores e cor de classificação para adicionar efeitos fillmatic ao jogo.
- SceneKit agora inclui um novo sistema fisicamente com base em processamento (PBR) para obter resultados mais realistas com mais simples de criação de ativo.
- Use a nova [SCNLightingModelPhysicallyBased](https://developer.apple.com/reference/scenekit/scnlightingmodelphysicallybased) sombreamento de modelo ao produto de uma ampla gama de efeitos de sombreamento realístico, exigindo somente três propriedades fundamentais (`Diffuse`, `Metalness` e `Roughness`).
- Desde PBR sombreamento funciona melhor com iluminação baseado no ambiente, use o `LightingEnvironment` propriedade para atribuir a iluminação baseada em imagem para toda uma cena.
- Use o `IESProfileURL` propriedade importar fixações da iluminação reais que definem a iluminação com base em valores reais como intensidade (em lumens) e temperatura de cor (em graus Kelvin).
- Recursos de câmera PBR e HDR fornecem resultados melhores que as técnicas tradicionais de renderização e, como resultado, SceneKit executa todos os cálculos de cor em um espaço de cor linear (usando o P3 gama de cores exibe cores de todo o dispositivo).
- Cor do SceneKit agora corresponde a todas as cores ao ler as informações de perfil de cor.
- SceneKit interpretará valores de componente de cor em um espaço de cor RGB linear para todos os tipos de sombreador.
- Ambos linear renderização de espaço de cores e toda a cor pode ser desabilitado especificando o `SCNDisableLinearSpaceRendering` e `SCNDisableWideGamut` chaves no aplicativo do `Info.plist`.
- Criar primates arbitrária polígono (ou carregado de arquivos gerados por meio de programação) para especificar a geometria com o novo [SCNGeometryPrimitiveTypePolygon](https://developer.apple.com/reference/scenekit/1772322-scenekit_enumerations/scngeometryprimitivetype/scngeometryprimitivetypepolygon) classe.
- Como SceneKit lê e ajuste para obter informações de perfil de cor nas imagens de textura, use catálogos ativos para todas as imagens para garantir que essas informações são fornecidas.

## <a name="spritekit-enhancements"></a>Aprimoramentos de SpriteKit

As seguintes melhorias foram feitas para a estrutura SpriteKit no iOS 10:

- Sombreadores personalizados podem fornecer atributos (`SKAttribute`) que pode ser configurado separadamente, cada nó que usa o sombreador fornecendo um valor de atributo (`SKAttributeValue`).
- Tilemaps agora dão suporte ao bloco quadrado, hexagonal e isométrica formas 2D, 2,5 D e jogos rolagem lado usando o `SKTileMapMode`, `SKTileGroup`, `SKTileGroupRule` e `SKTileSet` classes.
- Use a nova `SKWarpGeometry` classe para alongar ou distorcer [SKSpriteNode](https://developer.xamarin.com/api/type/SpriteKit.SKSpriteNode/) ou [SKEffectNode](https://developer.xamarin.com/api/type/SpriteKit.SKEffectNode/) renderização. O novo [SKAction](https://developer.xamarin.com/api/type/SpriteKit.SKAction/) classe pode ser usada para animar transições entre os efeitos de distorção.
- O [SKView](https://developer.xamarin.com/api/type/SpriteKit.SKView/) classe fornece vários novos métodos para fornecer controle refinado sobre quando e como uma cena é renderizada.

## <a name="scrollview-enhancements"></a>Aprimoramentos de ScrollView

As seguintes melhorias foram feitas ao controle ScrollView no iOS 10.3:

- `UIScrollView` agora incluem o `IndexDisplayMode` propriedade para controlar como o índice é mostrado enquanto o usuário está rolando como um `UIScrollViewIndexDisplayMode` de:
    - `Automatic` -A exibição do índice é controlada pelo sistema operacional.
    - `AlwaysHidden` -A exibição do índice for sempre oculto.

Consulte o [iOSTenThree exemplo](https://developer.xamarin.com/samples/monotouch/iOS10/iOSTenThree) para uso.

## <a name="uikit-enhancements"></a>Aprimoramentos de UIKit

As seguintes melhorias foram feitas para a estrutura UIKit no iOS 10:

- O novo [UIPasteboard](https://developer.xamarin.com/api/type/UIKit.UIPasteboard/) API fornece novas opções (como as limitações de tempo de vida) e automaticamente irá declarar tipos de conteúdo compatíveis para tipos comuns de classe.
- Novo suporte de animação totalmente interativo, com base em objeto, passível de interrupção foi adicionado e pode ser vinculado a gestos. Do pleas consulte Apple [referência de protocolo UIViewAnimating](https://developer.apple.com/reference/uikit/uiviewanimating), [referência de classe UIViewPropertyAnimator](https://developer.apple.com/reference/uikit/uiviewpropertyanimator), [referência de protocolo UITimingCurveProvider](https://developer.apple.com/reference/uikit/uitimingcurveprovider), [Referência de classe UICubicTimingParameters](https://developer.apple.com/reference/uikit/uicubictimingparameters) e [UISpringTimingParameter Referência de classe](https://developer.apple.com/reference/uikit/uispringtimingparameters) para obter mais informações.
- O novo `UIPreviewInteraction` e `UIPreviewInteractionDelegate` permitem que o aplicativo de desenvolvedor fornecer uma interface personalizada para as operações peek e pop.
- O novo `UIAccessibilityCustomRotor` classe permite que o aplicativo forneça funcionalidade personalizada, específicas de contexto, como voz sobre tecnologias de assistência.
- Use o `UIAccessibilityIsAssistiveTouchRunning` e `UIAccessibilityAssistiveTouchStatusDidChangeNotification` símbolos para determinar se AssistiveTouch está habilitado.
- Use o `UIAccessibilityHearingDevicePairedEar` e `UIAccessibilityHearingDevicePairedEarDidChangeNotification` símbolos para obter o status de qualquer par MFi auxílios de audição.
- Para dar suporte a tipo dinâmico em rótulos, campos de texto e caixas de texto usam o novo `PreferredFontForTextStyle` método o `UIFont` classe.
- Para decidir se um elemento atualize sua fonte quando o dispositivo `UIContentSizeCategory` alterações, use o `AdjustsFontForContentSizeCategory` propriedade o `UIContentSizeCategoryAdjusting` delegar.
- O `OpenURL` método o `UIApplication` classe é chamado de forma assíncrona e agora dá suporte a um processador de conclusão é chamado após a ação abrir.
- Iniciar o compartilhamento de CloudKit e modificar suas propriedades usando a nova `UICloudSharingController` e `UICloudSharingControllerDelegate` classes.
- Tirar proveito das células de pré-busca para melhorar a experiência de rolagem de `UICollectionViews` com o novo `UICollectionViewDataSourcePrefetching` delegate.
- O desenvolvedor agora pode controlar a aparência de notificação para itens da barra de guia (como cor de texto e plano de fundo).
- Agora há suporte para o controle de atualização em todas as exibição rolagem e subclasses de modo de exibição de rolagem (como `UICollectionView`).

## <a name="webkit-enhancements"></a>Aprimoramentos de WebKit

As seguintes melhorias foram feitas para a estrutura WebKit no iOS 10:

- Espiada e suporte pop foi adicionado para o `WKWebView` classe. Use o `ShouldPreviewElement` método para determinar se uma exibição da web especificado deve exibir uma visualização.


## <a name="related-links"></a>Links relacionados

- [Exemplos de iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
- [O que há de novo no iOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewIniOS/Articles/iOS10.html#//apple_ref/doc/uid/TP40017084-SW1)
