---
title: Alterações adicionais de estrutura do MacOS Serra
description: Este documento descreve pequenas alterações e aprimoramentos em estruturas existentes introduzidas no macOS Sierra. Ele examina as alterações na estrutura de aceleração, AppKit, AVFoundation, dados principais, imagem principal, base e muito mais.
ms.prod: xamarin
ms.assetid: CA701269-D11E-4DE3-89C1-58EF8993A482
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 03/14/2017
ms.openlocfilehash: 1de3a0a67fb6abbd4423435b38d7712f583ab74d
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70284448"
---
# <a name="additional-macos-sierra-framework-changes"></a>Alterações adicionais de estrutura do MacOS Serra

<a name="Accelerate-Framework-Enhancements" />

## <a name="accelerate-framework-enhancements"></a>Acelere os aprimoramentos de estrutura

O aprimoramento a seguir foi feito na estrutura de aceleração para macOS Sierra:

- Adição de Quadrature (integral cálculo).
- Funções básicas adicionadas para construir redes neurais.
- Adicionadas funções de predicado geométrico para testar coisas como a interseção de dois objetos geométricos.

<a name="AppKit-Framework-Enhancements" />

## <a name="appkit-framework-enhancements"></a>Aprimoramentos do AppKit Framework

O aprimoramento a seguir foi feito para a estrutura AppKit para macOS Sierra:

- Vários aprimoramentos para `NSCollectionView` , como:
  - **Seções recolhíveis** – permite que o usuário recolha uma seção de exibição de coleção em uma única linha horizontal.
  - **Cabeçalhos flutuantes** -cabeçalhos e rodapés agora podem ser flutuados (em um layout de fluxo) usando a mesma API que [UICollectionView](https://developer.apple.com/reference/uikit/uicollectionview) no Ios.
  - **Exibições em segundo plano roláveis** – um plano de fundo de exibições de coleção agora pode ser definido para rolar junto com o conteúdo.
- A passagem de layout de exibição adiada foi otimizada e estendida.
- A API do tipo "arrastar e soltar" agora inclui `NSFilePromiseProvider` as `NSFilePromiseReceiver` novas classes e para dar suporte ao arrastar pássaros.
- Vários construtores de conveniência foram adicionados aos controles existentes:
  - `NSButton`inclui novos construtores para a criação de botões de push, caixas de seleção e botões de opção.
  - `NSTextField`inclui novos construtores para a criação de rótulos de quebra automática e sem encapsulamento, rótulos atribuídos e campos de texto editáveis.
  - `NSSegmentedControl`inclui novos construtores para a criação de controles segmentados de um grupo de rótulos ou imagens.
  - `NSSlider`inclui novos construtores para a criação de controles deslizantes lineares horizontais.
  - `NSImageView`inclui novos construtores para a criação de exibições de imagem não editáveis `NSImage`de um determinado.
- O novo `NSGridView` foi adicionado para fazer o layout automático de uma coleção de subexibições em uma grade com linhas e colunas de tamanho variável que podem ser ocultadas ou exibidas dinamicamente.

<a name="AVFoundation-Framework-Enhancements" />

## <a name="avfoundation-framework-enhancements"></a>Aprimoramentos do AVFoundation Framework

O aprimoramento a seguir foi feito para a estrutura AVFoundation para macOS Sierra:

- No macOS, o aplicativo não precisa mais implementar comportamentos de [AVPlayerItem](https://developer.apple.com/reference/avfoundation/avplayeritem) diferentes com base no tipo de conteúdo. Basta definir a `Rate` Propriedade e AVFoundation determinará quando o conteúdo suficiente está disponível para reprodução sem interrupções.
- A nova `AVPlayerLooper` classe torna mais fácil fazer um loop de uma determinada mídia durante a reprodução.
- A `AVAssetDownloadURLSession` classe permite o download e a reprodução posterior de fluxos de HLS criptografados Fairplay.

<a name="Core-Data-Framework-Enhancements" />

## <a name="core-data-framework-enhancements"></a>Aprimoramentos da estrutura de dados principais

Os seguintes aprimoramentos foram feitos na estrutura de dados principal para macOS Sierra:

- Os objetos raiz [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) dão suporte à falha e busca simultâneas sem serialização.
- A classe [NSPersistentStoreCoordinator](https://developer.apple.com/reference/coredata/nspersistentstorecoordinator) mantém um pool de armazenamentos de dados do SQLite.
- Os objetos [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) com armazenamentos de dados SQLite no modo de diário Wal dão suporte ao novo recurso de geração de consulta em que os contextos de objeto gerenciado (MOC) podem ser fixados em versões de banco de dados específicas para futuras transações de busca e falha.
- Usando o alto nível `NSPersistenceContainer` para fazer referência a, [NSManagedObjectModel](https://developer.apple.com/reference/coredata/nsmanagedobjectmodel) e outros recursos principais de configuração de `NSPersistentStoreCoordinator`dados.
- Vários novos métodos de conveniência foram adicionados para `NSManagedObject` tornar mais fácil executar buscas e criar subclasses.

Para obter mais informações, consulte a [referência da estrutura de dados principal](https://developer.apple.com/reference/coredata)da Apple.

<a name="Core-Image-Framework-Enhancements" />

## <a name="core-image-framework-enhancements"></a>Aprimoramentos da estrutura da imagem principal

O aprimoramento a seguir foi feito na estrutura de imagem principal para macOS Sierra:

- O `ImageWithExtent` método da classe [CIFilter](https://developer.apple.com/reference/coreimage/cifilter) pode ser usado para inserir o processamento personalizado na operação de filtro. A imagem principal invocará o retorno de chamada fornecido entre os filtros ao processar uma imagem para saída ou exibição.
- O aplicativo agora pode processar imagens em um espaço de cores fora do espaço de cores de trabalho do contexto de imagem principal, convertendo dentro e fora do espaço de cores antes e depois do processamento.
- O kernel de imagem principal agora pode solicitar um formato de saída de pixel específico.
- Os seguintes novos filtros de imagem foram adicionados: `CINinePartTitled`, `CINinePartStretched`, `CIHueSaturationValueGradient` `CIEdgePreserveUpsampleFilter` e `CIClamp`.

<a name="Foundation-Framework-Enhancements" />

## <a name="foundation-framework-enhancements"></a>Aprimoramentos do Foundation Framework

O aprimoramento a seguir foi feito para o Foundation Framework para macOS Sierra:

- Use a API [NSDimentions](https://developer.apple.com/reference/foundation/nsdimension) para representar, converter e exibir muitas das unidades físicas mais comuns, como massa, comprimento, velocidade, duração e temperatura.
- Use a classe [NSISO8601DateFormatter](https://developer.apple.com/reference/foundation/nsiso8601dateformatter) para analisar e gerar datas formatadas ISO 8601.
- Use a nova classe [NSDateInterval](https://developer.apple.com/reference/foundation/nsdateinterval) para fazer cálculos de intervalo de data e hora, como durações, para comparar intervalos e testar as interseções de intervalo.
- Use a classe [NSPersonNameComponentsFormatter](https://developer.apple.com/reference/foundation/nspersonnamecomponentsformatter) para analisar os elementos do nome de uma pessoa a partir de uma cadeia de caracteres.
- Use a nova classe [NSURLSessionTaskMetrics](https://developer.apple.com/reference/foundation/nsurlsessiontaskmetrics) para obter métricas para uma sessão de rede de URL.

Para obter mais informações, consulte as [notas de versão de fundação da Apple para os X v 10.12 e Ios 10](https://developer.apple.com/library/prerelease/content/releasenotes/Miscellaneous/RN-Foundation-OSX10.12/index.html).

<a name="GameKit-Framework-Enhancements" />

## <a name="gamekit-framework-enhancements"></a>Aprimoramentos do GameKit Framework

O aprimoramento a seguir foi feito para a estrutura GameKit para macOS Sierra:

- O **aplicativo Game Center** foi preterido e removido do MacOS. Se o aplicativo usar GameKit, ele _deverá_ apresentar sua própria interface para exibir recursos de GameKit, como placares, etc. 
- Um novo tipo de conta somente iCloud foi implementado pela classe [GKCloudPlayer](https://developer.apple.com/reference/gamekit/gkcloudplayer) .
- A nova classe [GKGameSession](https://developer.apple.com/reference/gamekit/gkgamesession) fornece uma solução generalizada para gerenciar o armazenamento de dados persistente em Game Center. `GKGameSession`mantém uma lista de players e o aplicativo é o formulário responsável que implementa como e quando a data do participante é armazenada, recuperada ou trocada entre os jogadores. Em muitas instâncias, as sessões de jogos podem substituir as correspondências existentes, correspondências em tempo real ou métodos de salvamento de jogos persistentes.

<a name="GamePlayKit-Framework-Enhancements" />

## <a name="gameplaykit-framework-enhancements"></a>Aprimoramentos do GamePlayKit Framework

O aprimoramento a seguir foi feito para a estrutura GamePlayKit para macOS Sierra:

- A geração de ruído de procedimento foi adicionada e pode ser usada para aprimorar o realm nas texturas de aparência natural, adicionar Realm aos movimentos da câmera e ajudar a gerar mundos avançados de jogos.
- Use o particionamento espacial para particionar os dados do mundo do jogo para uma pesquisa eficiente.
- Um novo[GKMonteCarloStrategist](https://developer.apple.com/reference/gameplaykit/gkmontecarlostrategist)(Monte Carlo estrategista) foi adicionado para uma computação de movimentação exaustivamente possível.
- Uma nova API de árvore de decisão foi adicionada ([GKDecisionTree](https://developer.apple.com/reference/gameplaykit/gkdecisiontree) e [GKDecisionNode](https://developer.apple.com/reference/gameplaykit/gkdecisionnode)) para aprimorar o ia de criação de jogos.
- o suporte a 3D foi adicionado ao agente existente e aos comportamentos de localização de caminho usando as novas classes [GKAgent3D](https://developer.apple.com/reference/gameplaykit/gkagent3d) e [GKGraphNode3D](https://developer.apple.com/reference/gameplaykit/gkgraphnode3d) .
- Use a nova classe [GKMeshGraph](https://developer.apple.com/reference/gameplaykit/gkmeshgraph) para fornecer caminhos de aparência natural de alto desempenho.
- As novas classes [GKScene](https://developer.apple.com/reference/gameplaykit/gkscene) e [GKSKNodeComponent](https://developer.apple.com/reference/gameplaykit/gksknodecomponent) tornam a combinação de GameplayKit e SpriteKit mais fácil do que nunca.

<a name="Metal-Framework-Enhancements" />

## <a name="metal-framework-enhancements"></a>Aprimoramentos da estrutura de metal

O aprimoramento a seguir foi feito na estrutura de metal para macOS Sierra:

- os aplicativos e jogos 3D agora podem usar o _mosaico_ para renderizar com eficiência cenas e geometria complexas por meio da GPU.
- Use a especialização de função para criar uma coleção altamente otimizada de funções de combinação de material e leve para uma cena.
- Forneça um controle refinado de alocação de recursos para otimizar o desempenho de aplicativos baseados em metal usando heaps de recursos e destinos de renderização sem memória.

Para saber mais, confira o [Guia de programação de metal](https://developer.apple.com/library/prerelease/content/documentation/Miscellaneous/Conceptual/MetalProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014221)da Apple.

<a name="ModelIO-Framework-Enhancements" />

## <a name="model-io-framework-enhancements"></a>Aprimoramentos de estrutura de e/s de modelo

O aprimoramento a seguir foi feito para a estrutura de e/s de modelo para macOS Sierra:

- Agora há suporte para o formato de arquivo USD.
- Use a nova `MDLMaterialPropertyGraph` classe para dar suporte facilmente a alterações de tempo de execução para modelos.
- O suporte ao campo de distância assinada foi adicionado à classe [MDLVoxelArray](https://developer.apple.com/reference/modelio/mdlvoxelarray) .
- Use a nova `MDLLightProbeIrradianceDataSource` classe para auxiliar no posicionamento da investigação leve.

<a name="Photos-Framework-Enhancements" />

## <a name="photos-framework-enhancements"></a>Aprimoramentos da estrutura de fotos

Os aprimoramentos a seguir foram feitos na estrutura de fotos para macOS Sierra:

- A edição de fotos ao vivo agora está disponível para aplicativos que dão suporte à estrutura de fotos e às extensões de edição de fotos (para uso dentro dos aplicativos de fotos e câmera).
- Use a nova classe [PHLivePhotoEditingContext](https://developer.apple.com/reference/photos/phlivephotoeditingcontext) para aplicar edições ao vídeo e ainda ao conteúdo de fotos ao vivo.
- Use as classes [CIImageProcessorInput](https://developer.apple.com/reference/coreimage/ciimageprocessorinput) e [CIImageProcessorOutput](https://developer.apple.com/reference/coreimage/ciimageprocessoroutput) para aproveitar o novo recurso de processador de imagem de núcleo para executar edições.
- Para dar suporte a fotos ao vivo, as classes [PHLivePhoto](https://developer.apple.com/reference/photos/phlivephoto) e [PHLivePhotoView](https://developer.apple.com/reference/photosui/phlivephotoview) foram transportadas do IOS para o MacOS.

<a name="SceneKit-Framework-Enhancements" />

## <a name="scenekit-framework-enhancements"></a>Aprimoramentos do SceneKit Framework

O aprimoramento a seguir foi feito para a estrutura SceneKit para macOS Sierra:

- Agora inclui um novo sistema PBR (renderização com base física) para resultados mais realistas com a criação de ativos mais simples.
- Use o novo modelo de sombreamento [SCNLightingModelPhysicallyBased](https://developer.apple.com/reference/scenekit/scnlightingmodelphysicallybased) para obter uma ampla gama de efeitos de sombreamento realista, ao mesmo tempo que exige`Diffuse`apenas `Metalness` três `Roughness`propriedades fundamentais (e).
- Como o sombreamento do PBR funciona melhor com iluminação baseada em ambiente, `LightingEnvironment` use a propriedade para atribuir a iluminação baseada em imagem à cena inteira de Tan.
- Use a `IESProfileURL` propriedade para importar os acessórios de luz do mundo real que definem a base de iluminação em valores reais, como intensidade (em lumens) e temperatura de cor (em graus Kelvin).
- A classe [SCNCamera](https://developer.apple.com/reference/scenekit/scncamera) pode fornecer maior Realm usando os recursos e efeitos HDR. Use a exposição adaptativa para criar efeitos automáticos ou use Vignetting, a borda da cor e a redução da cor para adicionar efeitos filmatics ao jogo.
- Os recursos de câmera de PBR e HDR fornecem resultados melhores do que as técnicas de renderização tradicionais e, como resultado, o SceneKit agora executa todos os cálculos de cor em um espaço de cores linear (usando a gama de cores P3 em monitores de dispositivos de cores largos).
- A cor SceneKit Now corresponde a todas as cores lendo as informações do perfil de cor.
- SceneKit interpreta valores de componente de cor em um espaço de cores RGB linear para todos os tipos de sombreador.
- Como o SceneKit lê e ajusta as informações de perfil de cor em imagens de textura, use catálogos de ativos para todas as imagens para garantir que essas informações sejam fornecidas.
- A renderização de espaço de cores linear e a grande cor podem ser desabilitadas `SCNDisableLinearSpaceRendering` especificando `SCNDisableWideGamut` as `Info.plist`chaves e no aplicativo.
- Crie Primates Polygon arbitrários (carregados a partir de arquivos ou gerados programaticamente) para especificar Geometry com a nova classe [SCNGeometryPrimitiveTypePolygon](https://developer.apple.com/reference/scenekit/1772322-scenekit_enumerations/scngeometryprimitivetype/scngeometryprimitivetypepolygon) .

<a name="Security-Framework-Enhancements" />

## <a name="security-framework-enhancements"></a>Aprimoramentos da estrutura de segurança

O aprimoramento a seguir foi feito na estrutura de segurança para macOS Sierra:

- A `SecKey` interface foi modernizada e unificada em todas as plataformas (Ios, tvOS, watchOS e MacOS).

<a name="SpriteKit-Framework-Enhancements" />

## <a name="spritekit-framework-enhancements"></a>Aprimoramentos do SpriteKit Framework

O aprimoramento a seguir foi feito para a estrutura SpriteKit para macOS Sierra:

- Tilemaps agora suportam as formas de peças quadradas, hexágonos e para 2D, 2,5 d e rolagem `SKTileMapMode`para jogos `SKTileGroupRule` usando `SKTileSet` as classes, `SKTileGroup`e.
- Use a nova `SKWarpGeometry` classe para alongar ou distorcer a renderização [SKSpriteNode](https://developer.apple.com/reference/spritekit/skspritenode) ou [SKEffectNode](https://developer.apple.com/reference/spritekit/skeffectnode) . A nova classe [SKAction](https://developer.apple.com/reference/spritekit/skaction) pode ser usada para animar transições entre efeitos de distorção.
- Os sombreadores personalizados podem fornecer atributos`SKAttribute`() que podem ser configurados separadamente por cada nó que usa o sombreador fornecendo um valor`SKAttributeValue`de atributo ().
- A classe [SKView](https://developer.apple.com/reference/spritekit/skview) fornece vários novos métodos para fornecer um controle refinado sobre quando e como uma cena é renderizada.

<a name="New-Frameworks" />

## <a name="new-frameworks"></a>Novas estruturas

As seguintes estruturas foram adicionadas ao macOS Sierra:

- **Estrutura de tentativas** – essa estrutura permite que o aplicativo examine as interações (como local ou ações do usuário) e tome medidas com base nessas informações.
- **Safariservices Framework** – essa estrutura permite que o aplicativo desenvolva extensões de aplicativo para o Safari (como bloqueadores de conteúdo) para MacOS e Ios.

## <a name="related-links"></a>Links relacionados

- [Amostras de Mac](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Mac)
- [O que há de novo no OS X 10,12](https://developer.apple.com/library/prerelease/content/releasenotes/MacOSX/WhatsNewInOSX/Articles/OSXv10.html#//apple_ref/doc/uid/TP40017145-SW1)
