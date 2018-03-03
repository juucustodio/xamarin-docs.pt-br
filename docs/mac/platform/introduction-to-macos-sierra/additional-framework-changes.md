---
title: "MacOS adicionais Serra Framework alterações"
description: "Este artigo aborda as alterações adicionais, secundárias ou melhorias em estruturas existentes para macOS Serra."
ms.topic: article
ms.prod: xamarin
ms.assetid: CA701269-D11E-4DE3-89C1-58EF8993A482
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: d49276d6367a52a05d486cd5cab198c666965bf7
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2018
---
# <a name="additional-macos-sierra-framework-changes"></a>MacOS adicionais Serra Framework alterações

_Este artigo aborda as alterações adicionais, secundárias ou melhorias em estruturas existentes para macOS Serra._

<a name="Accelerate-Framework-Enhancements" />

## <a name="accelerate-framework-enhancements"></a>Acelerar aprimoramentos do Framework

O aprimoramento a seguir foram feitas para a estrutura de acelerar macOS Serra:

- Adicionado em quadratura (cálculo integral).
- Funções básicas adicionadas para a criação de redes neurais.
- Funções de predicado geométricas adicionadas para testar a coisas como a interseção de dois objetos geométricos.

<a name="AppKit-Framework-Enhancements" />

## <a name="appkit-framework-enhancements"></a>Aprimoramentos do Appkit Framework

O aprimoramento a seguir foram feitas para a estrutura de AppKit macOS Serra:

- Várias melhorias para `NSCollectionView` como:
    - **Seções recolhíveis** -permite que o usuário recolher uma seção de exibição de coleção em uma única linha horizontal.
    - **Flutuante cabeçalhos** -cabeçalhos e rodapés podem agora ser flutuante (em um layout de fluxo) usando a mesma API [UICollectionView](https://developer.apple.com/reference/uikit/uicollectionview) no iOS.
    - **Modos de exibição de plano de fundo rolável** -agora é possível definir um plano de fundo de exibições de coleção para rolar juntamente com o conteúdo.
- A passagem de layout de exibição adiada foi otimizada e estendida.
- A API de arrastar e soltar agora inclui o novo `NSFilePromiseProvider` e `NSFilePromiseReceiver` classes para dar suporte a arrastar flocking.
- Foram adicionados vários construtores de conveniência para controles existentes:
    -  `NSButton` inclui novos construtores para criar botões de ação, caixas de seleção e botões de opção.
    -  `NSTextField` inclui novos construtores para criar etiquetas de encapsulamento e não encapsulamento, rótulos atribuídos e campos de texto editável.
    -  `NSSegmentedControl` inclui novos construtores para criar controles segmentados de um grupo de rótulos ou imagens.
    -  `NSSlider` inclui novos construtores para criar controles deslizantes de lineares horizontais.
    -  `NSImageView` inclui novos construtores para criar exibições de imagem não editável de uma determinada `NSImage`.
-  O novo `NSGridView` foi adicionado ao layout automático linhas e colunas que podem ser ocultadas ou exibidas dinamicamente o tamanho de uma coleção de exibições de sub em uma grade com variável.

<a name="AVFoundation-Framework-Enhancements" />

## <a name="avfoundation-framework-enhancements"></a>Aprimoramentos do AVFoundation Framework

O aprimoramento a seguir foram feitas para a estrutura de AVFoundation macOS Serra:

- Em macOS, o aplicativo não tem que implementar diferentes [AVPlayerItem](https://developer.apple.com/reference/avfoundation/avplayeritem) comportamentos com base no tipo de conteúdo. Basta definir o `Rate` propriedade e AVFoundation determinará quando conteúdo suficiente está disponível para a reprodução sem atrasando.
- O novo `AVPlayerLooper` classe torna mais fácil repetir um determinado item de mídia durante a reprodução.
- O `AVAssetDownloadURLSession` classe permite o download e reprodução posterior de FairPlay criptografado streams HLS.

<a name="Core-Data-Framework-Enhancements" />

## <a name="core-data-framework-enhancements"></a>Aprimoramentos de estrutura de dados principal

O aprimoramento a seguir foram feitas para a estrutura de dados principal para macOS Serra:

- Raiz [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) simultâneos com falha e buscar sem serialização dá suporte a objetos.
- O [NSPersistentStoreCoordinator](https://developer.apple.com/reference/coredata/nspersistentstorecoordinator) classe mantém um pool de armazenamentos de dados SQLite.
- O [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) objetos com repositórios de dados SQLite no suporte a WAL diário modo a nova geração de consulta de recursos onde contextos de objeto gerenciado (MOC) podem ser fixados em versões de banco de dados específico para buscar futuras e transações com falha.
- Usando o alto nível `NSPersistenceContainer` a referência a `NSPersistentStoreCoordinator`, [NSManagedObjectModel](https://developer.apple.com/reference/coredata/nsmanagedobjectmodel) e outros recursos de configuração de dados principal.
- Foram adicionados vários novos métodos de conveniência para `NSManagedObject` tornando mais fácil executar buscas e criar subclasses.

Para obter mais informações, consulte da Apple [referência de estrutura de dados principal](https://developer.apple.com/reference/coredata).

<a name="Core-Image-Framework-Enhancements" />

## <a name="core-image-framework-enhancements"></a>Principais aprimoramentos de Framework de imagem

O aprimoramento a seguir foram feitas para a estrutura de imagem principal para macOS Serra:

- O `ImageWithExtent` método o [CIFilter](https://developer.apple.com/reference/coreimage/cifilter) classe pode ser usada para inserir o processamento personalizado na operação de filtro. Imagem principal será invocar o retorno de chamada especificado entre os filtros ao processar uma imagem de saída ou exibir.
- O aplicativo agora pode processar imagens em um espaço de cores fora do espaço de cor de trabalho do contexto de imagem principal convertendo dentro e fora do espaço de cor antes e após o processamento.
- O núcleo de imagem principal agora pode solicitar um formato de saída de pixel específicas.
- Os novos filtros de imagem a seguir foram adicionados: `CINinePartTitled`, `CINinePartStretched`, `CIHueSaturationValueGradient`, `CIEdgePreserveUpsampleFilter` e `CIClamp`.

<a name="Foundation-Framework-Enhancements" />

## <a name="foundation-framework-enhancements"></a>Aprimoramentos de estrutura de base

O aprimoramento a seguir foram feitas para a estrutura de base para macOS Serra:

- Use o [NSDimentions](https://developer.apple.com/reference/foundation/nsdimension) API para representar, convertendo e exibindo muitas das unidades físicas mais comuns, como em massa, comprimento, a velocidade, a duração e a temperatura.
- Use o [NSISO8601DateFormatter](https://developer.apple.com/reference/foundation/nsiso8601dateformatter) classe para análise e geração de ISO 8601 formatado datas.
- Use a nova [NSDateInterval](https://developer.apple.com/reference/foundation/nsdateinterval) classe para fazer cálculos de intervalo de data e hora como durações, para comparar os intervalos e testando interseções de intervalo.
- Use o [NSPersonNameComponentsFormatter](https://developer.apple.com/reference/foundation/nspersonnamecomponentsformatter) classe para analisar os elementos do nome de uma pessoa de uma cadeia de caracteres.
- Use a nova [NSURLSessionTaskMetrics](https://developer.apple.com/reference/foundation/nsurlsessiontaskmetrics) classe para obter a métrica para uma URL de sessão de rede.

Para obter mais informações, consulte da Apple [Foundation notas de versão para OS X v10.12 e iOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/Miscellaneous/RN-Foundation-OSX10.12/index.html).

<a name="GameKit-Framework-Enhancements" />

## <a name="gamekit-framework-enhancements"></a>Aprimoramentos do GameKit Framework

O aprimoramento a seguir foram feitas para a estrutura de GameKit macOS Serra:

- O **Game Center aplicativo** foi preterido e removidos do macOS. Se o aplicativo usa GameKit, ele _deve_ apresentar sua própria interface para exibir os recursos de GameKit, como tabelas, etc. 
- Um novo tipo de conta somente iCloud foi implementado pelo [GKCloudPlayer](https://developer.apple.com/reference/gamekit/gkcloudplayer) classe.
- O novo [GKGameSession](https://developer.apple.com/reference/gamekit/gkgamesession) classe fornece uma solução generalizada para gerenciamento de armazenamento de dados persistentes no Game Center. `GKGameSession` mantém uma lista de participantes e o aplicativo é responsável formulário implementando como e quando a data de participante é armazenada, recuperadas ou trocadas entre os participantes. Em muitas instâncias sessões jogo pode substituir correspondências existentes com base por sua vez, correspondências em tempo real ou jogo persistente salvar métodos.

<a name="GamePlayKit-Framework-Enhancements" />

## <a name="gameplaykit-framework-enhancements"></a>Aprimoramentos do GamePlayKit Framework

O aprimoramento a seguir foram feitas para a estrutura de GamePlayKit macOS Serra:

- Geração de ruído procedimento foi adicionada e pode ser usada para aprimorar o realismo em natural de aparência, adicionar realismo movimentos de câmera e ajudar a gerar mundos jogos avançados.
- Use o particionamento espacial para particionar os dados de jogos do mundo para pesquisa eficiente.
- Estrategista de Monte Carlo novo ([GKMonteCarloStrategist](https://developer.apple.com/reference/gameplaykit/gkmontecarlostrategist)) foi adicionado para a computação de movimentação possíveis completa.
- Foi adicionada uma nova API de árvore de decisão ([GKDecisionTree](https://developer.apple.com/reference/gameplaykit/gkdecisiontree) e [GKDecisionNode](https://developer.apple.com/reference/gameplaykit/gkdecisionnode)) para aprimorar a construção de jogo AI.
- Foi adicionado suporte 3D para o agente existente e comportamentos de localização do caminho usando o novo [GKAgent3D](https://developer.apple.com/reference/gameplaykit/gkagent3d) e [GKGraphNode3D](https://developer.apple.com/reference/gameplaykit/gkgraphnode3d) classes.
- Use a nova [GKMeshGraph](https://developer.apple.com/reference/gameplaykit/gkmeshgraph) classe para fornecer caminhos de alto desempenho, procurando natural.
- O novo [GKScene](https://developer.apple.com/reference/gameplaykit/gkscene) e [GKSKNodeComponent](https://developer.apple.com/reference/gameplaykit/gksknodecomponent) criar classes combinando GameplayKit e SpriteKit mais fácil.

<a name="Metal-Framework-Enhancements" />

## <a name="metal-framework-enhancements"></a>Aprimoramentos do Framework metal

O aprimoramento a seguir foram feitas para a estrutura de Metal macOS Serra:

- Aplicativos 3D e jogos agora podem usar _mosaico_ para processar com eficiência cenas complexas e geometry por meio da GPU.
- Use a função especialização para criar uma coleção altamente otimizado de material e funções de combinação de luz para uma cena.
- Fornece controle refinado de alocação de recursos para otimizar o desempenho do sistema operacional com base em aplicativos que usam o recurso de Heaps e destinos Memoryless renderizar.

Para obter mais informações, consulte da Apple [guia de programação de Metal](https://developer.apple.com/library/prerelease/content/documentation/Miscellaneous/Conceptual/MetalProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014221).

<a name="ModelIO-Framework-Enhancements" />

## <a name="model-io-framework-enhancements"></a>Aprimoramentos do modelo do Framework de e/s

O aprimoramento a seguir foram feitas para a estrutura de e/s de modelo para macOS Serra:

- Agora há suporte para o formato de arquivo USD.
- Use a nova `MDLMaterialPropertyGraph` classe para suportar facilmente o tempo de execução é alterado para modelos.
- Assinado distância campo foi adicionado suporte para o [MDLVoxelArray](https://developer.apple.com/reference/modelio/mdlvoxelarray) classe.
- Use a nova `MDLLightProbeIrradianceDataSource` classe para auxiliar na investigação de luz de posicionamento.

<a name="Photos-Framework-Enhancements" />

## <a name="photos-framework-enhancements"></a>Aprimoramentos do Framework de fotos

O aprimoramento a seguir foram feitas para a estrutura de fotos macOS Serra:

- Edição de foto ao vivo está disponível para aplicativos que dão suporte a estrutura de fotos e extensões de edição de fotos (para uso dentro de fotos e câmera aplicativos).
- Use a nova [PHLivePhotoEditingContext](https://developer.apple.com/reference/photos/phlivephotoeditingcontext) classe ao aplicar edições para o vídeo e ainda conteúdo de fotos em tempo real.
- Use o [CIImageProcessorInput](https://developer.apple.com/reference/coreimage/ciimageprocessorinput) e [CIImageProcessorOutput](https://developer.apple.com/reference/coreimage/ciimageprocessoroutput) classes para tirar proveito do novo recurso de processador de imagem principal para executar edições.
- Para dar suporte ao vivo fotos, o [PHLivePhoto](https://developer.apple.com/reference/photos/phlivephoto) e [PHLivePhotoView](https://developer.apple.com/reference/photosui/phlivephotoview) classes foram adaptadas do iOS para macOS.

<a name="SceneKit-Framework-Enhancements" />

## <a name="scenekit-framework-enhancements"></a>Aprimoramentos do SceneKit Framework

O aprimoramento a seguir foram feitas para a estrutura de SceneKit macOS Serra:

- Agora inclui um novo sistema fisicamente com base em processamento (PBR) para obter resultados mais realistas com mais simples de criação de ativo.
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

<a name="Security-Framework-Enhancements" />

## <a name="security-framework-enhancements"></a>Aprimoramentos da estrutura de segurança

O aprimoramento a seguir foram feitas para a estrutura de segurança para macOS Serra:

- O `SecKey` interface foi modernizada e unificada em todas as plataformas (iOS, tvOS, watchOS e macOS).

<a name="SpriteKit-Framework-Enhancements" />

## <a name="spritekit-framework-enhancements"></a>Aprimoramentos do SpriteKit Framework

O aprimoramento a seguir foram feitas para a estrutura de SpriteKit macOS Serra:

- Tilemaps agora dão suporte ao bloco quadrado, hexagonal e isométrica formas 2D, 2,5 D e jogos rolagem lado usando o `SKTileMapMode`, `SKTileGroup`, `SKTileGroupRule` e `SKTileSet` classes.
- Use a nova `SKWarpGeometry` classe para alongar ou distorcer [SKSpriteNode](https://developer.apple.com/reference/spritekit/skspritenode) ou [SKEffectNode](https://developer.apple.com/reference/spritekit/skeffectnode) renderização. O novo [SKAction](https://developer.apple.com/reference/spritekit/skaction) classe pode ser usada para animar transições entre os efeitos de distorção.
- Sombreadores personalizados podem fornecer atributos (`SKAttribute`) que pode ser configurado separadamente, cada nó que usa o sombreador fornecendo um valor de atributo (`SKAttributeValue`).
- O [SKView](https://developer.apple.com/reference/spritekit/skview) classe fornece vários novos métodos para fornecer controle refinado sobre quando e como uma cena é renderizada.

<a name="New-Frameworks" />

## <a name="new-frameworks"></a>Novas estruturas

As estruturas a seguir foram adicionadas ao macOS Serra:

- **Estrutura de tentativas** -essa estrutura permitem que o aplicativo examinar as interações (como ações de usuário ou local) e execute a ação com base nessas informações.
- **Estrutura SafariServices** -essa estrutura permitem que o aplicativo desenvolver extensões de aplicativo para o Safari (como bloqueadores de conteúdo) para macOS e iOS.

## <a name="related-links"></a>Links relacionados

- [Exemplos de Mac](https://developer.xamarin.com/samples/mac/)
- [O que há de novo nos X 10.12](https://developer.apple.com/library/prerelease/content/releasenotes/MacOSX/WhatsNewInOSX/Articles/OSXv10.html#//apple_ref/doc/uid/TP40017145-SW1)
