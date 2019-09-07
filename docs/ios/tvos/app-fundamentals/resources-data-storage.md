---
title: Recursos de tvOS e armazenamento de dados no Xamarin
description: Este artigo descreve como trabalhar com o armazenamento de dados de recursos e persistentes em um aplicativo tvOS criado com o Xamarin. Ele aborda o armazenamento de dados do iCloud e recursos sob demanda.
ms.prod: xamarin
ms.assetid: C56B5046-D2C0-4B63-9CE0-ADAA0EFD368A
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/16/2017
ms.openlocfilehash: 4b50ba09488f1f052f3e41302a42c691089727ff
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70769645"
---
# <a name="tvos-resources-and-data-storage-in-xamarin"></a>Recursos de tvOS e armazenamento de dados no Xamarin

_Este artigo aborda como trabalhar com recursos e armazenamento de dados persistentes em um aplicativo Xamarin. tvOS._

<a name="tvOS-Resource-Limitations" />

## <a name="tvos-resource-limitations"></a>Limitações de recursos do tvOS

Ao contrário dos dispositivos iOS, a nova Apple TV fornece armazenamento local persistente extremamente limitado para aplicativos ou dados tvOS. Para itens muito pequenos (como preferências do usuário), seu aplicativo tvOS ainda tem acesso a `NSUserDefaults` com um [limite de 500 KB de dados](https://forums.developer.apple.com/message/50696#50696). No entanto, se seu aplicativo Xamarin. tvOS precisar manter maiores quantidades de informações, ele deverá armazenar e recuperar esses dados do [icloud](#iCloud-Data-Storage).

Além disso, o tvOS limita o tamanho de um aplicativo da Apple TV para 200 MB. Se seu aplicativo exigir recursos além desse tamanho, eles precisarão ser empacotados e carregados usando [recursos sob demanda](#On-Demand-Resources) (até um 2 GB adicional). Devido a essas limitações, é essencial que você tenha tempo de download de ativos adicionais corretamente para fornecer a melhor experiência para os usuários do seu aplicativo. Para obter mais informações, consulte o [Guia de recursos sob demanda](https://developer.apple.com/library/prerelease/tvos/documentation/FileManagement/Conceptual/On_Demand_Resources_Guide/index.html#//apple_ref/doc/uid/TP40015083)da Apple.

<a name="Non-Persistent-Downloads" />

## <a name="non-persistent-downloads"></a>Downloads não persistentes

Cada aplicativo tvOS é fornecido como um diretório de cache temporário no qual seus recursos adicionais e ativos são baixados. Esse diretório será preservado, desde que o aplicativo ainda esteja em execução. No entanto, como a Apple TV precisa liberar espaço para outros aplicativos ou uso do sistema, esse cache pode ser excluído.

Como resultado, seu aplicativo não poderá contar com o conteúdo baixado anteriormente que está disponível na próxima vez em que for iniciado. Seu aplicativo Xamarin. tvOS sempre deve verificar a existência de recursos necessários e baixá-los conforme necessário.

> [!IMPORTANT]
> Embora você tenha a capacidade de baixar outros ativos e recursos conforme necessário, a Apple avisa sobre o consumo de todo o espaço no cache do aplicativo, pois pode levar a resultados imprevisíveis.

<a name="Managing-Resources" />

## <a name="managing-resources"></a>Gerenciando recursos

Conforme mencionado acima, devido ao armazenamento limitado e não persistente de informações disponíveis para aplicativos tvOS, essas restrições exigem um planejamento cuidadoso para criar uma excelente experiência do usuário para seu aplicativo Xamarin. tvOS.

<a name="iCloud-Data-Storage" />

### <a name="icloud-data-storage"></a>Armazenamento de dados do iCloud

Como o armazenamento na Apple TV é limitado, não apenas há um armazenamento local persistente muito limitado, seu aplicativo não tem nenhuma garantia de que todas as informações baixadas anteriormente estarão disponíveis na próxima vez em que for executado.

Como resultado, seu aplicativo Xamarin. tvOS deve armazenar qualquer dado do usuário em um armazenamento de dados do iCloud. A Apple fornece duas opções de armazenamento de dados baseadas no iCloud para seus aplicativos tvOS:

- setor de **armazenamento de chave-valor (KVS) do icloud** – para pequenas informações (menos de 1 MB) que seu aplicativo pode exigir (como preferências do usuário), você pode usar o armazenamento de KVS do icloud. os dados do iCloud KVS são sincronizados automaticamente com a nuvem e todos os dispositivos do usuário que executam o mesmo aplicativo. Para obter mais informações, consulte a seção [armazenamento de chave-valor](~/ios/data-cloud/introduction-to-icloud.md) de nosso documento [introdução ao icloud](~/ios/data-cloud/introduction-to-icloud.md) ou [design da Apple para dados de chave-valor na](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/iCloudDesignGuide/Chapters/DesigningForKey-ValueDataIniCloud.html#//apple_ref/doc/uid/TP40012094-CH7) documentação do icloud.
- **CloudKit** – para o armazenamento de informações maiores (mais de 1MB), use a estrutura CloudKit da Apple. Ao contrário do armazenamento KVS do iCloud, os dados do CloudKit podem ser compartilhados entre todos os usuários do seu aplicativo (bem como serem privados a um único usuário). Saiba mais informações, consulte nossa [introdução à](~/ios/data-cloud/intro-to-cloudkit.md) documentação do CloudKit ou ao CloudKit da Apple [início rápido](https://developer.apple.com/library/prerelease/tvos/documentation/DataManagement/Conceptual/CloudKitQuickStart/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014987).

> [!IMPORTANT]
> A Apple [fornece ferramentas](https://developer.apple.com/support/allowing-users-to-manage-data/) para ajudar os desenvolvedores a lidar adequadamente com o GDPR (Regulamento Geral sobre a Proteção de Dados) da União Europeia.

<a name="On-Demand-Resources" />

### <a name="on-demand-resources"></a>Recursos sob demanda

Os recursos sob demanda fornecem conteúdo do aplicativo e ativos (separados do pacote de aplicativos), que são hospedados na loja de aplicativos e baixados conforme exigido pelo seu aplicativo. Até um 2 GB de dados adicionais podem ser servidos usando recursos sob demanda. Eles permitem que o download inicial do aplicativo seja menor (os aplicativos tvOS são limitados a um máximo de 200 MB), enquanto ainda fornecem ativos avançados, conforme necessário.

Quando um aplicativo tvOS solicita recursos sob demanda, o sistema gerencia automaticamente o download e o armazenamento desse conteúdo no diretório de cache do aplicativo. Seu aplicativo deve aguardar que este conteúdo seja baixado e disponibilizado antes que possa continuar.

Esses recursos podem continuar sendo armazenados em cache na Apple TV durante várias inicializações do seu aplicativo, acelerando o ciclo de inicialização. No entanto, seu aplicativo não pode depender de qualquer conteúdo baixado anteriormente que esteja disponível na próxima vez em que for iniciado. Consulte a seção de [downloads não persistentes](#Non-Persistent-Downloads) acima para obter mais detalhes.

Use o Xcode para criar pacotes de conteúdo relacionado (como todos os ativos para o nível 2 do jogo) associados a uma marca de recurso de criação. Mais tarde, seu aplicativo solicitará recursos sob demanda especificando esta marca de recurso. Seu aplicativo deve apresentar uma interface do usuário para que ele informando que o conteúdo está sendo baixado. Para obter mais informações, consulte o [Guia de recursos sob demanda](https://developer.apple.com/library/prerelease/tvos/documentation/FileManagement/Conceptual/On_Demand_Resources_Guide/index.html#//apple_ref/doc/uid/TP40015083)da Apple.

> [!IMPORTANT]
> Deve-se tomar cuidado para atingir o equilíbrio certo entre o número de vezes que o aplicativo precisa baixar recursos sob demanda e o tamanho dos downloads individuais. O usuário poderá se sentir frustrado com seu aplicativo se o jogo for interrompido constantemente para baixar novo conteúdo ou se um único download levar muito tempo.

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo abordou as limitações de tamanho, de recursos e de armazenamento de dados colocadas em um aplicativo Xamarin. tvOS pelo sistema tvOS. Ele apresentou opções para contornar essas limitações e sugestões para criar uma excelente experiência de usuário para seu aplicativo.

## <a name="related-links"></a>Links relacionados

- [Amostras do tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guias de interface humana do tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guia de programação de aplicativo para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
