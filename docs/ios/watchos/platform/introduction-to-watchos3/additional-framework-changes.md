---
title: "Alterações de estruturas adicionais watchOS 3"
description: "Este artigo aborda as alterações adicionais, secundárias ou melhorias em estruturas existentes para watchOS 3."
ms.topic: article
ms.prod: xamarin
ms.assetid: FE93796E-F699-4B14-B37D-D39F9D48E81E
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: e2d14bcedd6ff5570d77da8c9b54a74b5ce0752a
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="additional-watchos-3-frameworks-changes"></a>Alterações de estruturas adicionais watchOS 3

_Este artigo aborda as alterações adicionais, secundárias ou melhorias em estruturas existentes para watchOS 3._

Além das alterações principais para iOS, Apple fez melhorias para várias estruturas existentes e modificações watchOS 3.


## <a name="core-data"></a>Dados de núcleo

Os aprimoramentos a seguir fez para a estrutura de dados principal para inspecionar OS 3:

- Raiz [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) simultâneos com falha e buscar sem serialização dá suporte a objetos.
- O [NSPersistentStoreCoordinator](https://developer.apple.com/reference/coredata/nspersistentstorecoordinator) classe mantém um pool de armazenamentos de dados SQLite.
- O [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) objetos com repositórios de dados SQLite no suporte a WAL diário modo a nova geração de consulta de recursos onde contextos de objeto gerenciado (MOC) podem ser fixados em versões de banco de dados específico para buscar futuras e transações com falha.
- Usando o alto nível `NSPersistenceContainer` a referência a `NSPersistentStoreCoordinator`, [NSManagedObjectModel](https://developer.apple.com/reference/coredata/nsmanagedobjectmodel) e outros recursos de configuração de dados principal.
- Foram adicionados vários novos métodos de conveniência para `NSManagedObject` tornando mais fácil executar buscas e criar subclasses.

Para obter mais informações, consulte da Apple [referência de estrutura de dados principal](https://developer.apple.com/reference/coredata).


## <a name="core-motion"></a>Movimento de núcleo

Os aprimoramentos a seguir fez para o framework Core movimento para inspecionar OS 3:

- O novo evento de movimento do dispositivo usa o acelerômetro e giroscópio para fornecer atualizações de movimento e orientação. o aplicativo do aplicativo pode registrar para essa atualização (em taxas de até 100Hz).
- O novo evento Pedometer rápida, habilita notificações em tempo real quando o usuário pausa e continua em execução. Use o [CMPedometer](https://developer.apple.com/reference/coremotion/cmpedometer) para registrar eventos de pedometer primeiro ou segundo plano.


## <a name="foundation"></a>Foundation

Os aprimoramentos a seguir fez para a estrutura de base para inspecionar 3 do sistema operacional:

- Use a nova [NSDateInterval](https://developer.apple.com/reference/foundation/nsdateinterval) classe para fazer cálculos de intervalo de data e hora como durações, para comparar os intervalos e testando interseções de intervalo.
- Várias novas propriedades foram adicionadas para o [NSLocal](https://developer.apple.com/reference/foundation/nslocale) classe para adquirir informações locais e os formatos de exibição disponíveis.
- Use a nova [NSMeasuerment](https://developer.apple.com/reference/foundation/nsmeasurement) classe para converter entre diferentes unidades de medida (unidade de medida) ou executar cálculos nos valores de UOMs diferentes.
- Use a nova [NSMeasurementFormatter](https://developer.apple.com/reference/foundation/nsmeasurementformatter) classe Formatar medidas localizadas para a exibição para o usuário final.
- Use a nova [NSUnit](https://developer.apple.com/reference/foundation/nsunit) e [NSDimension](https://developer.apple.com/reference/foundation/nsdimension) classes para representar UOMs específicos.


## <a name="healthkit"></a>HealthKit

Os aprimoramentos a seguir fez para o framework HealthKit para inspecionar 3 do sistema operacional:

- Use a nova [HKWorkoutConfiguration](https://developer.apple.com/reference/healthkit/hkworkoutconfiguration) classe para especificar o `ActivityType` e `LocationType` de um treinamento.
- O novo [HKWheelchairUseObject](https://developer.apple.com/reference/healthkit/hkwheelchairuseobject) e o `WheelchairUse` método o [HKHealthStore](https://developer.apple.com/reference/healthkit/hkhealthstore) classe foram adicionados para trabalhar com cadeira de rodas integridade de dados relacionados.
- Foram adicionadas novas chaves de metadados para tipos de clima (como `HKWeatherConditionClear` e `HKWeatherConditionCloudy`) e tipos de treinamento (como `HKWorkoutActivityTypeFlexibility` e `HKWorkoutActivityTypeWheelchairRunPace`) foram adicionadas.


## <a name="homekit"></a>HomeKit

Os aprimoramentos a seguir fez para o framework HomeKit para inspecionar 3 do sistema operacional:

- Adicionada a capacidade de exibir e interagir com HomeKit conectado IP câmeras.
- Adicionar vários novos serviços e características.
- Adicionar mais contexto e a configuração de Acessórios de serviços principais e os serviços de link.


## <a name="passkit"></a>PassKit

Os aprimoramentos a seguir fez para o framework PassKit para inspecionar 3 do sistema operacional:

- Expande a estrutura para dar suporte ao pagamentos seguros no aplicativo do Apple Watch de bens físicos e serviços.
- As classes a seguir agora estão disponíveis: [PKPayment](https://developer.apple.com/reference/passkit/pkpayment), [PKPaymentMethod](https://developer.apple.com/reference/passkit/pkpaymentmethod), [PKPaymentRequest](https://developer.apple.com/reference/passkit/pkpaymentrequest) e [PKPaymentToken](https://developer.apple.com/reference/passkit/pkpaymenttoken)


## <a name="uikit"></a>UIKit

Os aprimoramentos a seguir fez para o framework UIKit para inspecionar 3 do sistema operacional:

- Para dar suporte a tipo dinâmico em rótulos, campos de texto e caixas de texto usam o novo `PreferredFontForTextStyle` método o `UIFont` classe.
- O `ColorWithDisplayP3` método foi adicionado para oferecer suporte a cores de largura.


## <a name="related-links"></a>Links relacionados

- [Guia de Introdução (exemplo)](https://developer.xamarin.com/samples/monotouch/WatchKit/)
- [O que há de novo no watchOS 3](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewInwatchOS/Articles/watchOS3.html#//apple_ref/doc/uid/TP40017085-SW1)
