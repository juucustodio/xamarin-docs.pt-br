---
title: Armazenamento de dados e recursos
description: Este artigo aborda o trabalho com recursos e armazenamento de dados persistentes em um aplicativo Xamarin.tvOS.
ms.prod: xamarin
ms.assetid: C56B5046-D2C0-4B63-9CE0-ADAA0EFD368A
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: b4d96ef50498b454da583a955169b9d51c29dd01
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="resources-and-data-storage"></a>Armazenamento de dados e recursos

_Este artigo aborda o trabalho com recursos e armazenamento de dados persistentes em um aplicativo Xamarin.tvOS._

<a name="tvOS-Resource-Limitations" />

## <a name="tvos-resource-limitations"></a>tvOS limitações de recursos

Ao contrário de dispositivos iOS, o novo Apple TV fornece armazenamento persistente, local extremamente limitado para tvOS aplicativos ou dados. Para obter itens muito pequenos (por exemplo, as preferências do usuário), seu aplicativo tvOS ainda tem acesso ao `NSUserDefaults` com um [limite de 500 KB de dados](https://forums.developer.apple.com/message/50696#50696). No entanto, se seu aplicativo Xamarin.tvOS precisa manter grandes quantidades de informação, ele deve armazenar e recuperar dados de [iCloud](#iCloud-Data-Storage).

Além disso, tvOS limita o tamanho de um aplicativo da Apple TV para 200MB. Se seu aplicativo requer recursos além desse tamanho, eles precisam ser empacotados e carregados usando [recursos sob demanda](#On-Demand-Resources) (até um adicional de 2 GB). Devido a essas limitações, é importante que seu tempo corretamente o download de ativos adicionais para fornecer a melhor experiência para usuários de seu aplicativo. Para obter mais informações, consulte da Apple [guia de recursos sob demanda](https://developer.apple.com/library/prerelease/tvos/documentation/FileManagement/Conceptual/On_Demand_Resources_Guide/index.html#//apple_ref/doc/uid/TP40015083).

<a name="Non-Persistent-Downloads" />

## <a name="non-persistent-downloads"></a>Downloads não persistente

Cada aplicativo tvOS é fornecido um diretório de cache temporário que seus recursos adicionais e ativos são baixados. Este diretório será preservado desde que o aplicativo ainda está em execução. No entanto, como Apple TV precisa liberar espaço para outros aplicativos ou o uso do sistema, esse cache pode ser excluído.

Como resultado, seu aplicativo não pode confiar no conteúdo baixado anteriormente estavam disponíveis na próxima vez que ele é iniciado. Seu aplicativo Xamarin.tvOS sempre deve verificar a existência dos recursos exigidos e baixá-los conforme necessário.

> [!IMPORTANT]
> Enquanto você tem a capacidade de fazer o download de outros recursos conforme necessário e ativos, Apple previne consumindo todo o espaço no cache do aplicativo, pois isso pode levar a resultados imprevisíveis.




<a name="Managing-Resources" />

## <a name="managing-resources"></a>Gerenciamento de recursos

Como mencionado anteriormente, devido a limitação de armazenamento não persistente das informações disponíveis para os aplicativos tvOS, essas restrições exigem um planejamento cuidadoso criar uma excelente experiência de usuário para seu aplicativo Xamarin.tvOS.

<a name="iCloud-Data-Storage" />

### <a name="icloud-data-storage"></a>Armazenamento de dados do iCloud

Como o armazenamento da Apple TV é limitado, não só é persistente, local de armazenamento muito limitado, seu aplicativo não é nenhuma garantia de que todas as informações baixado anteriormente estarão disponíveis na próxima vez que ele é executado.

Como resultado, seu aplicativo Xamarin.tvOS deve armazenar os dados de usuários em um repositório de dados no iCloud. Apple fornece duas opções de armazenamento de dados com base em iCloud para seus aplicativos tvOS:

- **Armazenamento de chave-valor (KVS) do iCloud** - para pequenos pedaços de informações (menos de 1 MB) que seu aplicativo pode exigir (como as preferências do usuário), você pode usar o iCloud KVS armazenamento. iCloud KVS dados são sincronizados automaticamente para a nuvem e todos os dispositivos do usuário executando o mesmo aplicativo. Para obter mais informações, consulte o [armazenamento de chave-valor](~/ios/data-cloud/introduction-to-icloud.md) seção do nosso [introdução com o iCloud](~/ios/data-cloud/introduction-to-icloud.md) documento ou da Apple [criando para dados de chave-valor no iCloud](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/iCloudDesignGuide/Chapters/DesigningForKey-ValueDataIniCloud.html#//apple_ref/doc/uid/TP40012094-CH7) documentação.
- **CloudKit** - o para o armazenamento das maiores informações (maiores que 1 MB), use o Framework de CloudKit da Apple. Ao contrário do iCloud armazenamento KVS, CloudKit dados podem ser compartilhados entre todos os usuários do seu aplicativo (bem como sendo particular a um único usuário). Formulário obter mais informações, consulte nosso [Introdução ao CloudKit](~/ios/data-cloud/intro-to-cloudkit.md) da Apple ou a documentação [início rápido do CloudKit](https://developer.apple.com/library/prerelease/tvos/documentation/DataManagement/Conceptual/CloudKitQuickStart/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014987).

<a name="On-Demand-Resources" />

### <a name="on-demand-resources"></a>Recursos sob demanda

Recursos sob demanda fornecem o conteúdo do aplicativo e ativos (separados do pacote de aplicativo), que são hospedados na loja de aplicativos e baixados conforme exigido pelo seu aplicativo. Até um adicional de 2 GB de dados podem ser atendida a usar os recursos sob demanda. Elas permitem que o download do aplicativo inicial a ser menores (tvOS aplicativos são limitados a um máximo de 200MB), enquanto ainda fornecem recursos avançados conforme necessário.

Quando um aplicativo tvOS solicita recursos sob demanda, o sistema gerenciará automaticamente a baixar e o armazenamento desse conteúdo para o diretório de cache do aplicativo. Seu aplicativo deve esperar para este conteúdo a ser baixado e disponibilizada antes de continuar.

Esses recursos podem continuar a ser armazenado em cache na Apple TV ao longo de várias inicializações de seu aplicativo, assim acelerando ciclo de inicialização. No entanto, seu aplicativo não pode depender de qualquer conteúdo baixado anteriormente estavam disponíveis na próxima vez que ele é iniciado. Consulte o [Downloads não persistente](#Non-Persistent-Downloads) seção acima para obter mais detalhes.

Use o Xcode para criar pacotes de conteúdo relacionado (como todos os ativos de jogo nível 2) associados com uma marca de recurso oferecem. Mais tarde seu aplicativo irá solicitar recursos sob demanda, especificando essa marca de recurso. Seu aplicativo deve apresentar uma interface do usuário para o usuário informando que o conteúdo está sendo descarregado. Para obter mais informações, consulte da Apple [guia de recursos sob demanda](https://developer.apple.com/library/prerelease/tvos/documentation/FileManagement/Conceptual/On_Demand_Resources_Guide/index.html#//apple_ref/doc/uid/TP40015083).

> [!IMPORTANT]
> Tome cuidado para encontrar o equilíbrio correto entre o número de vezes que o aplicativo deve baixar os recursos sob demanda e o tamanho dos downloads individuais. Usuário pode se tornar frustrado com seu aplicativo se jogos é interrompido constantemente para baixar o conteúdo novo ou se um único download leva muito tempo.




<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo abordou as limitações de armazenamento de tamanho, recursos e dados colocadas em um aplicativo Xamarin.tvOS pelo sistema tvOS. Ele tem apresentado opções para contornar essas limitações e sugestões para criar uma excelente experiência de usuário para seu aplicativo.



## <a name="related-links"></a>Links relacionados

- [Amostras do tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS guias de Interface Humana](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guia de programação de aplicativo para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
