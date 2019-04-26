---
title: Alterações adicionais de estrutura do MacOS Serra
description: Este documento descreve algumas pequenas alterações e aprimoramentos para as estruturas existentes introduzidos no macOS Sierra. Ele examina as alterações do framework Accelerate, AppKit, AVFoundation, dados principais, imagem principal, Foundation e muito mais.
ms.prod: xamarin
ms.assetid: CA701269-D11E-4DE3-89C1-58EF8993A482
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 6ed827c018931e5b79887dc355f136e2a84623d6
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61031621"
---
# <a name="additional-macos-sierra-framework-changes"></a>Alterações adicionais de estrutura do MacOS Serra

<a name="Accelerate-Framework-Enhancements" />

## <a name="accelerate-framework-enhancements"></a>Acelere os aprimoramentos do Framework

O seguinte aperfeiçoamento foram feitas para o Framework acelerar para macOS Sierra:

- Adicionado em quadratura (cálculo integral).
- Adicionado funções básicas para a criação de redes neurais.
- Funções de predicado geométricas adicionadas para testar a coisas como a interseção de dois objetos geométricos.

<a name="AppKit-Framework-Enhancements" />

## <a name="appkit-framework-enhancements"></a>Aprimoramentos do AppKit Framework

O seguinte aperfeiçoamento foram feitas para a estrutura de AppKit para macOS Sierra:

- Várias melhorias para `NSCollectionView` , como:
    - **Seções recolhidas** -permite que o usuário recolher uma seção de exibição de coleção em uma única linha horizontal.
    - **Flutuante cabeçalhos** -cabeçalhos e rodapés podem agora ser flutuante (em um layout de fluxo) usando a mesma API [UICollectionView](https://developer.apple.com/reference/uikit/uicollectionview) no iOS.
    - **Modos de exibição de tela de fundo rolável** -um plano de fundo de modos de exibição de coleção agora pode ser definido para rolar juntamente com o conteúdo.
- A passagem de layout de exibição adiada foi otimizada e estendida.
- A API de arrastar e soltar agora inclui a nova `NSFilePromiseProvider` e `NSFilePromiseReceiver` classes para dar suporte a arrastar pássaros.
- Vários construtores de conveniência para controles existentes foram adicionados:
    -  `NSButton` inclui novos construtores para criar botões de ação, caixas de seleção e botões de opção.
    -  `NSTextField` inclui novos construtores para criar o encapsulamento e não quebra automática de rótulos, rótulos atribuídos e campos editáveis de texto.
    -  `NSSegmentedControl` inclui novos construtores para criar controles segmentados de um grupo de rótulos ou imagens.
    -  `NSSlider` inclui novos construtores para criar os controles deslizantes lineares horizontais.
    -  `NSImageView` inclui novos construtores para a criação de modos de exibição de imagem não editável de uma determinada `NSImage`.
-  O novo `NSGridView` foi adicionado ao layout automático em tamanho de uma coleção de exibições de sub em uma grade com variável de linhas e colunas que podem ser ocultadas ou exibidas dinamicamente.

<a name="AVFoundation-Framework-Enhancements" />

## <a name="avfoundation-framework-enhancements"></a>Aprimoramentos do Framework de AVFoundation

O seguinte aperfeiçoamento foram feitas para a estrutura de AVFoundation para macOS Sierra:

- No macOS, o aplicativo não tem que implementar diferentes [AVPlayerItem](https://developer.apple.com/reference/avfoundation/avplayeritem) comportamentos com base no tipo de conteúdo. Basta definir o `Rate` propriedade e AVFoundation determinará quando conteúdo suficiente está disponível para a reprodução sem atraso das.
- O novo `AVPlayerLooper` classe torna mais fácil executar uma determinada parte da mídia de um loop durante a reprodução.
- O `AVAssetDownloadURLSession` classe permite o download e a reprodução do FairPlay posterior criptografados streams HLS.

<a name="Core-Data-Framework-Enhancements" />

## <a name="core-data-framework-enhancements"></a>Principais aprimoramentos de estrutura de dados

O seguinte aperfeiçoamento foram feitas para a estrutura de dados principal para o macOS Sierra:

- Raiz [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) simultâneo com falha e buscando sem serialização dá suporte a objetos.
- O [NSPersistentStoreCoordinator](https://developer.apple.com/reference/coredata/nspersistentstorecoordinator) classe mantém um pool de armazenamentos de dados SQLite.
- O [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) objetos com armazenamentos de dados SQLite no suporte a nova geração de consulta WAL diário modo em que contextos de objeto gerenciado (MOC) podem ser fixados em versões de banco de dados específico para buscar futuras de recursos e transações com falha.
- Usando o alto nível `NSPersistenceContainer` a referência a `NSPersistentStoreCoordinator`, [NSManagedObjectModel](https://developer.apple.com/reference/coredata/nsmanagedobjectmodel) e outros recursos de configuração de dados principal.
- Vários métodos de conveniência novos foram adicionados ao `NSManagedObject` tornando mais fácil executar buscas e criar subclasses.

Para obter mais informações, consulte da Apple [referência de estrutura de dados de núcleo](https://developer.apple.com/reference/coredata).

<a name="Core-Image-Framework-Enhancements" />

## <a name="core-image-framework-enhancements"></a>Principais aprimoramentos do Framework de imagem

O seguinte aperfeiçoamento foram feitas para a estrutura de imagem Core para macOS Sierra:

- O `ImageWithExtent` método da [CIFilter](https://developer.apple.com/reference/coreimage/cifilter) classe pode ser usada para inserir o processamento personalizado para a operação de filtro. Imagem principal invoque o retorno de chamada fornecido entre os filtros ao processar uma imagem de saída ou a exibir.
- O aplicativo agora pode processar imagens em um espaço de cores fora do espaço de cor de trabalho do contexto de núcleo de imagem, convertendo para dentro e fora do espaço de cor antes e após o processamento.
- O kernel de imagem Core agora pode solicitar um formato de saída do pixel específico.
- Os novos filtros de imagem a seguir foram adicionados: `CINinePartTitled`, `CINinePartStretched`, `CIHueSaturationValueGradient`, `CIEdgePreserveUpsampleFilter` e `CIClamp`.

<a name="Foundation-Framework-Enhancements" />

## <a name="foundation-framework-enhancements"></a>Aprimoramentos do Foundation do Framework

O seguinte aperfeiçoamento foram feitas para a estrutura de base para o macOS Sierra:

- Use o [NSDimentions](https://developer.apple.com/reference/foundation/nsdimension) API para que representa, convertendo e exibindo muitas das unidades físicas mais comuns, como em massa, comprimento, a velocidade, a duração e a temperatura.
- Use o [NSISO8601DateFormatter](https://developer.apple.com/reference/foundation/nsiso8601dateformatter) datas formatadas de classe para análise e geração de ISO 8601.
- Use a nova [NSDateInterval](https://developer.apple.com/reference/foundation/nsdateinterval) classe para fazer cálculos de intervalo de data e hora como durações, para comparar os intervalos e teste para interseções de intervalo.
- Use o [NSPersonNameComponentsFormatter](https://developer.apple.com/reference/foundation/nspersonnamecomponentsformatter) classe para analisar os elementos de nome de uma pessoa de uma cadeia de caracteres.
- Use a nova [NSURLSessionTaskMetrics](https://developer.apple.com/reference/foundation/nsurlsessiontaskmetrics) classe para obter as métricas para uma URL de sessão de rede.

Para obter mais informações, consulte da Apple [Foundation notas de versão v10.12 dos X e iOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/Miscellaneous/RN-Foundation-OSX10.12/index.html).

<a name="GameKit-Framework-Enhancements" />

## <a name="gamekit-framework-enhancements"></a>Aprimoramentos do Framework de GameKit

O seguinte aperfeiçoamento foram feitas para a estrutura de GameKit para macOS Sierra:

- O **Game Center aplicativo** foi preterido e removido do macOS. Se o aplicativo usa GameKit, ele _deve_ apresentar sua própria interface para exibir os recursos de GameKit como placares de líderes, etc. 
- Um novo tipo de conta somente para iCloud foi implementado pelo [GKCloudPlayer](https://developer.apple.com/reference/gamekit/gkcloudplayer) classe.
- O novo [GKGameSession](https://developer.apple.com/reference/gamekit/gkgamesession) classe fornece uma solução generalizada para gerenciar o armazenamento de dados persistentes no Game Center. `GKGameSession` mantém uma lista de jogadores e o aplicativo é responsável formulário implementando como e quando a data de participante é armazenada, recuperado ou trocadas entre os participantes. Em muitos casos as sessões de jogos pode substituir correspondências existentes baseada em turno, correspondências em tempo real ou jogo persistente salvar métodos.

<a name="GamePlayKit-Framework-Enhancements" />

## <a name="gameplaykit-framework-enhancements"></a>Aprimoramentos do Framework de GamePlayKit

O seguinte aperfeiçoamento foram feitas para a estrutura de GamePlayKit para macOS Sierra:

- Geração de ruído procedural foi adicionada e pode ser usada para aprimorar o realismo aparência natural das texturas, adicione realismo para movimentos de câmera e ajudar a gerar mundos de jogos avançados.
- Use o particionamento espacial para particionar os dados do mundo do jogo para a pesquisa eficiente.
- Estrategista de Monte Carlo nova ([GKMonteCarloStrategist](https://developer.apple.com/reference/gameplaykit/gkmontecarlostrategist)) foi adicionado para a computação de mudança completa de possíveis.
- Foi adicionada uma nova API de árvore de decisão ([GKDecisionTree](https://developer.apple.com/reference/gameplaykit/gkdecisiontree) e [GKDecisionNode](https://developer.apple.com/reference/gameplaykit/gkdecisionnode)) para aprimorar o AI de jogo de construção.
- Foi adicionado suporte 3D para o agente existente e os comportamentos de localização de caminho usando a nova [GKAgent3D](https://developer.apple.com/reference/gameplaykit/gkagent3d) e [GKGraphNode3D](https://developer.apple.com/reference/gameplaykit/gkgraphnode3d) classes.
- Use a nova [GKMeshGraph](https://developer.apple.com/reference/gameplaykit/gkmeshgraph) classe para fornecer caminhos de alto desempenho e a aparência natural.
- O novo [GKScene](https://developer.apple.com/reference/gameplaykit/gkscene) e [GKSKNodeComponent](https://developer.apple.com/reference/gameplaykit/gksknodecomponent) make classes combinando GameplayKit e SpriteKit mais fácil do que nunca.

<a name="Metal-Framework-Enhancements" />

## <a name="metal-framework-enhancements"></a>Aprimoramentos do Framework de metal

O seguinte aperfeiçoamento foram feitas para a estrutura de sistema operacional para o macOS Sierra:

- Aplicativos 3D e jogos agora podem usar _mosaico_ para processar com eficiência cenas complexas e geometry por meio da GPU.
- Use a especialização de função para criar uma coleção altamente otimizado de materiais e funções de combinação de luz para uma cena.
- Fornece controle refinado de alocação de recursos para otimizar o desempenho do sistema operacional com base em aplicativos usando o recurso de Heaps e Memoryless destinos de renderização.

Para obter mais informações, consulte da Apple [guia de programação de Metal](https://developer.apple.com/library/prerelease/content/documentation/Miscellaneous/Conceptual/MetalProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014221).

<a name="ModelIO-Framework-Enhancements" />

## <a name="model-io-framework-enhancements"></a>Aprimoramentos do modelo da estrutura de e/s

O seguinte aperfeiçoamento foram feitas para a estrutura de e/s de modelo para o macOS Sierra:

- Agora há suporte para o formato de arquivo USD.
- Usar o novo `MDLMaterialPropertyGraph` classe para suportar facilmente o tempo de execução é alterado para modelos.
- Assinado campo distância foi adicionado suporte para o [MDLVoxelArray](https://developer.apple.com/reference/modelio/mdlvoxelarray) classe.
- Usar o novo `MDLLightProbeIrradianceDataSource` classe para auxiliar na investigação de luz de posicionamento.

<a name="Photos-Framework-Enhancements" />

## <a name="photos-framework-enhancements"></a>Aprimoramentos do Framework de fotos

O seguinte aperfeiçoamento foram feitas para a estrutura de fotos para o macOS Sierra:

- Edição de fotos ao vivo agora está disponível para aplicativos que dão suporte a estrutura de fotos e extensões de edição de fotos (para uso dentro de fotos e câmera aplicativos).
- Use a nova [PHLivePhotoEditingContext](https://developer.apple.com/reference/photos/phlivephotoeditingcontext) classe ao aplicar edições para o vídeo e ainda o conteúdo de fotos ao vivo.
- Use o [CIImageProcessorInput](https://developer.apple.com/reference/coreimage/ciimageprocessorinput) e [CIImageProcessorOutput](https://developer.apple.com/reference/coreimage/ciimageprocessoroutput) classes para tirar proveito do novo recurso de processador de imagem principal para realizar edições.
- Para dar suporte a Live fotos, o [PHLivePhoto](https://developer.apple.com/reference/photos/phlivephoto) e [PHLivePhotoView](https://developer.apple.com/reference/photosui/phlivephotoview) classes foram portadas do iOS para macOS.

<a name="SceneKit-Framework-Enhancements" />

## <a name="scenekit-framework-enhancements"></a>Aprimoramentos do Framework do SceneKit

O seguinte aperfeiçoamento foram feitas para a estrutura de SceneKit para macOS Sierra:

- Agora inclui um novo sistema fisicamente com base em processamento (PBR) para obter resultados mais realistas com a criação de ativo mais simples.
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

<a name="Security-Framework-Enhancements" />

## <a name="security-framework-enhancements"></a>Aprimoramentos da estrutura de segurança

O seguinte aperfeiçoamento foram feitas para a estrutura de segurança para o macOS Sierra:

- O `SecKey` interface foi modernizado e unificado em todas as plataformas (iOS, tvOS, watchOS e macOS).

<a name="SpriteKit-Framework-Enhancements" />

## <a name="spritekit-framework-enhancements"></a>Aprimoramentos do Framework de SpriteKit

O seguinte aperfeiçoamento foram feitas para a estrutura de SpriteKit para macOS Sierra:

- Tilemaps agora dão suporte a formas de bloco quadrado, hexagonal e isométrica para 2D, 2.5D e rolagem de lado jogos usando o `SKTileMapMode`, `SKTileGroup`, `SKTileGroupRule` e `SKTileSet` classes.
- Use a nova `SKWarpGeometry` classe para alongar ou distorcer [SKSpriteNode](https://developer.apple.com/reference/spritekit/skspritenode) ou [SKEffectNode](https://developer.apple.com/reference/spritekit/skeffectnode) renderização. O novo [SKAction](https://developer.apple.com/reference/spritekit/skaction) classe pode ser usada para animar as transições entre os efeitos de warp.
- Sombreadores personalizados podem fornecer atributos (`SKAttribute`) que pode ser configurada separadamente por cada nó que usa o sombreador fornecendo um valor de atributo (`SKAttributeValue`).
- O [SKView](https://developer.apple.com/reference/spritekit/skview) classe fornece vários novos métodos para fornecer controle refinado sobre quando e como uma cena é renderizada.

<a name="New-Frameworks" />

## <a name="new-frameworks"></a>Novas estruturas

As estruturas a seguir foram adicionadas ao macOS Sierra:

- **Estrutura intenções** -essa estrutura permite que o aplicativo examinar as interações (como ações de usuário ou local) e agir com base nessas informações.
- **Estrutura SafariServices** -essa estrutura permite que o aplicativo desenvolver extensões de aplicativo para o Safari (como bloqueadores de conteúdo) para macOS e iOS.

## <a name="related-links"></a>Links relacionados

- [Amostras de Mac](https://developer.xamarin.com/samples/mac/)
- [O que há de novo nos X 10.12](https://developer.apple.com/library/prerelease/content/releasenotes/MacOSX/WhatsNewInOSX/Articles/OSXv10.html#//apple_ref/doc/uid/TP40017145-SW1)
