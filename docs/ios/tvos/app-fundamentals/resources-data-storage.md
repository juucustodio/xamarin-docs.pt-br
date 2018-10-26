---
title: tvOS recursos e o armazenamento de dados no Xamarin
description: Este artigo descreve como trabalhar com recursos e o armazenamento de dados persistentes em um aplicativo tvOS criado com o Xamarin. Ele aborda os recursos de armazenamento e sob demanda de dados do iCloud.
ms.prod: xamarin
ms.assetid: C56B5046-D2C0-4B63-9CE0-ADAA0EFD368A
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 8f8ecc115738fb97f71b4ea6b2cdcc5c2714372d
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108178"
---
# <a name="tvos-resources-and-data-storage-in-xamarin"></a>tvOS recursos e o armazenamento de dados no Xamarin

_Este artigo aborda o trabalho com recursos e o armazenamento de dados persistentes em um aplicativo xamarin. tvos._

<a name="tvOS-Resource-Limitations" />

## <a name="tvos-resource-limitations"></a>Limitações de recursos do tvOS

Ao contrário de dispositivos iOS, o novo Apple TV fornece extremamente limitada de armazenamento persistente, local para tvOS aplicativos ou dados. Para itens muito pequenos (por exemplo, as preferências do usuário), seu aplicativo tvOS ainda tem acesso aos `NSUserDefaults` com um [limite de 500 KB de dados](https://forums.developer.apple.com/message/50696#50696). No entanto, se seu aplicativo tvos precisar manter grandes quantidades de informações, ele deve armazenar e recuperar dados de [iCloud](#iCloud-Data-Storage).

Além disso, tvOS limita o tamanho de um aplicativo de Apple TV para 200MB. Se seu aplicativo requer recursos além desse tamanho, precisarão ser empacotados e carregados usando [recursos sob demanda](#On-Demand-Resources) (até um adicional de 2 GB). Devido a essas limitações, é essencial que você tempo corretamente o download dos ativos adicionais para fornecer a melhor experiência para usuários do aplicativo. Para obter mais informações, consulte da Apple [guia de recursos sob demanda](https://developer.apple.com/library/prerelease/tvos/documentation/FileManagement/Conceptual/On_Demand_Resources_Guide/index.html#//apple_ref/doc/uid/TP40015083).

<a name="Non-Persistent-Downloads" />

## <a name="non-persistent-downloads"></a>Downloads não persistente

Cada aplicativo tvOS é fornecido um diretório de cache temporário que seus recursos adicionais e ativos serão baixados para. Esse diretório será preservado, desde que o aplicativo ainda está em execução. No entanto, como Apple TV precisa liberar espaço para outros aplicativos ou o uso do sistema, esse cache pode ser excluído.

Como resultado, seu aplicativo não pode confiar no conteúdo baixado anteriormente disponibilizados na próxima vez que ele é iniciado. Seu aplicativo tvos sempre deve verificar a existência dos recursos necessários e baixá-los conforme necessário.

> [!IMPORTANT]
> Embora você tenha a capacidade de baixar outros ativos e recursos conforme necessário, Apple previne consumindo todo o espaço no cache do seu aplicativo, que pode levar a resultados imprevisíveis.




<a name="Managing-Resources" />

## <a name="managing-resources"></a>Gerenciamento de recursos

Conforme mencionado acima, devido à limitação, o armazenamento não persistente de informações disponíveis para aplicativos do tvOS, essas restrições exigem um planejamento cuidadoso criar uma experiência excelente do usuário para seu aplicativo xamarin. tvos.

<a name="iCloud-Data-Storage" />

### <a name="icloud-data-storage"></a>Armazenamento de dados do iCloud

Como o armazenamento na Apple TV é limitado, não apenas é persistente, local de armazenamento muito limitado, seu aplicativo não tem nenhuma garantia de que qualquer informação que baixou anteriormente estarão disponível na próxima vez que ele é executado.

Como resultado, seu aplicativo tvos deve armazenar dados do usuário em um Data Store do iCloud. A Apple fornece duas opções de armazenamento de dados com base em iCloud para seus aplicativos de tvOS:

- **Armazenamento de chave-valor (KVS) do iCloud** - para pequenas partes de informações (menos de 1 MB) que seu aplicativo pode exigir (como as preferências do usuário), você pode usar o armazenamento de KVS iCloud. iCloud KVS dados sincronizados automaticamente com a nuvem e todos os dispositivos do usuário que executa o mesmo aplicativo. Para obter mais informações, consulte o [armazenamento de chave-valor](~/ios/data-cloud/introduction-to-icloud.md) seção do nosso [Introdução ao iCloud](~/ios/data-cloud/introduction-to-icloud.md) documento ou do Apple [Projetando para dados de chave-valor no iCloud](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/iCloudDesignGuide/Chapters/DesigningForKey-ValueDataIniCloud.html#//apple_ref/doc/uid/TP40012094-CH7) documentação.
- **CloudKit** - para o armazenamento das maiores informações (maiores que 1 MB), usar a estrutura do CloudKit da Apple. Ao contrário do armazenamento de KVS do iCloud, CloudKit dados podem ser compartilhados entre todos os usuários do seu aplicativo (bem como sendo privado a um único usuário). Formam a obter mais informações, consulte nosso [Introdução ao CloudKit](~/ios/data-cloud/intro-to-cloudkit.md) documentação ou da Apple [início rápido do CloudKit](https://developer.apple.com/library/prerelease/tvos/documentation/DataManagement/Conceptual/CloudKitQuickStart/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014987).

> [!IMPORTANT]
> A Apple [fornece ferramentas](https://developer.apple.com/support/allowing-users-to-manage-data/) para ajudar os desenvolvedores a lidar adequadamente com o GDPR (Regulamento Geral sobre a Proteção de Dados) da União Europeia.

<a name="On-Demand-Resources" />

### <a name="on-demand-resources"></a>Recursos sob demanda

Recursos sob demanda fornecem o conteúdo do aplicativo e ativos (separados do pacote de aplicativo), que são hospedados na App Store e baixados conforme exigido pelo seu aplicativo. Até um adicional de 2 GB de dados pode ser servido a usar os recursos sob demanda. Eles permitem que o download do aplicativo inicial a ser menores (tvOS aplicativos são limitados a um máximo de 200MB), enquanto ainda fornecem avançados ativos conforme necessário.

Quando um aplicativo tvOS solicita recursos sob demanda, o sistema irá gerenciar automaticamente o download e o armazenamento desse conteúdo para o diretório de cache do aplicativo. Seu aplicativo deve aguardar esse conteúdo a serem baixados e disponibilizados para poder continuar.

Esses recursos podem continuar a ser armazenado em cache na Apple TV ao longo de várias inicializações de seu aplicativo, ciclo de lançamento, portanto, acelerar. No entanto, seu aplicativo não pode depender de qualquer conteúdo baixado anteriormente disponibilizados na próxima vez que ele é iniciado. Consulte a [Downloads não persistente](#Non-Persistent-Downloads) seção acima para obter mais detalhes.

Usar o Xcode para criar pacotes de conteúdo relacionado (por exemplo, todos os ativos de jogo nível 2) associados com um marca de recurso determinado. Mais tarde seu aplicativo solicita recursos sob demanda, especificando a marca de recurso. Seu aplicativo deve apresentar uma interface do usuário para o usuário informando que o conteúdo está sendo baixado. Para obter mais informações, consulte da Apple [guia de recursos sob demanda](https://developer.apple.com/library/prerelease/tvos/documentation/FileManagement/Conceptual/On_Demand_Resources_Guide/index.html#//apple_ref/doc/uid/TP40015083).

> [!IMPORTANT]
> Tome cuidado para alcançar o equilíbrio entre o número de vezes que o aplicativo deve fazer o download de recursos sob demanda e o tamanho dos downloads individuais. Usuário pode se tornar frustrado com seu aplicativo se o jogo for interrompido constantemente para baixar o novo conteúdo ou se um único download leva muito tempo.




<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo abordou as limitações de armazenamento de tamanho, recursos e dados colocadas em um aplicativo xamarin. tvos pelo sistema tvOS. Ele tem apresentado opções para contornar essas limitações e sugestões para criar uma experiência excelente do usuário para seu aplicativo.



## <a name="related-links"></a>Links relacionados

- [Amostras do tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [Guias de Interface humana do tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guia de programação de aplicativo para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
