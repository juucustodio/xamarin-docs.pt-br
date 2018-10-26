---
title: Alterações de estruturas adicionais do watchOS 3
description: Este documento descreve várias alterações de estrutura introduzidas com o watchOS 3 e como trabalhar com eles no Xamarin. Dados básicos, animação de núcleo, Foundation, HealthKit, HomeKit, PassKit e UIKit são discutidos.
ms.prod: xamarin
ms.assetid: FE93796E-F699-4B14-B37D-D39F9D48E81E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 745c39dab1f73870ce036791434ed9a0b05d681b
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50122615"
---
# <a name="additional-watchos-3-frameworks-changes"></a>Alterações de estruturas adicionais do watchOS 3

_Este artigo aborda as alterações adicionais, secundárias ou aprimoramentos para as estruturas existentes para watchOS 3._

Além das principais alterações para o iOS, o Apple fez modificações e melhorias nas várias estruturas existentes no watchOS 3.


## <a name="core-data"></a>Dados básicos

A estrutura de dados principal para watch OS 3 têm feitas as seguintes melhorias:

- Raiz [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) simultâneo com falha e buscando sem serialização dá suporte a objetos.
- O [NSPersistentStoreCoordinator](https://developer.apple.com/reference/coredata/nspersistentstorecoordinator) classe mantém um pool de armazenamentos de dados SQLite.
- O [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) objetos com armazenamentos de dados SQLite no suporte a nova geração de consulta WAL diário modo em que contextos de objeto gerenciado (MOC) podem ser fixados em versões de banco de dados específico para buscar futuras de recursos e transações com falha.
- Usando o alto nível `NSPersistenceContainer` a referência a `NSPersistentStoreCoordinator`, [NSManagedObjectModel](https://developer.apple.com/reference/coredata/nsmanagedobjectmodel) e outros recursos de configuração de dados principal.
- Vários métodos de conveniência novos foram adicionados ao `NSManagedObject` tornando mais fácil executar buscas e criar subclasses.

Para obter mais informações, consulte da Apple [referência de estrutura de dados de núcleo](https://developer.apple.com/reference/coredata).


## <a name="core-motion"></a>Animação de núcleo

A estrutura de animação de núcleo para watch OS 3 têm feitas as seguintes melhorias:

- O novo evento de movimento do dispositivo usa o acelerômetro e o giroscópio para fornecer atualizações de movimento e a orientação. o aplicativo pode se registrar para isso atualizadas (com taxas de até 100Hz).
- O novo evento Pedometer rápidos e habilita notificações em tempo real quando o usuário pausa e retoma a execução. Use o [CMPedometer](https://developer.apple.com/reference/coremotion/cmpedometer) para registrar eventos de pedometer primeiro ou segundo plano.


## <a name="foundation"></a>Foundation

Fez as seguintes melhorias para a estrutura de base para inspeção 3 do sistema operacional:

- Use a nova [NSDateInterval](https://developer.apple.com/reference/foundation/nsdateinterval) classe para fazer cálculos de intervalo de data e hora como durações, para comparar os intervalos e teste para interseções de intervalo.
- Várias novas propriedades foram adicionadas para o [NSLocal](https://developer.apple.com/reference/foundation/nslocale) classe para adquirir informações locais e os formatos de exibição disponíveis.
- Use a nova [NSMeasuerment](https://developer.apple.com/reference/foundation/nsmeasurement) classe para converter entre diferentes unidades de medida (unidade de medida) ou executar cálculos nos valores de UOMs diferentes.
- Use a nova [NSMeasurementFormatter](https://developer.apple.com/reference/foundation/nsmeasurementformatter) classe Formatar medidas localizadas para a exibição para o usuário final.
- Use a nova [NSUnit](https://developer.apple.com/reference/foundation/nsunit) e [NSDimension](https://developer.apple.com/reference/foundation/nsdimension) classes para representar UOMs específicos.


## <a name="healthkit"></a>HealthKit

Fez as seguintes melhorias para a estrutura de HealthKit para inspeção 3 do sistema operacional:

- Use a nova [HKWorkoutConfiguration](https://developer.apple.com/reference/healthkit/hkworkoutconfiguration) classe para especificar o `ActivityType` e `LocationType` de uma ginástica.
- O novo [HKWheelchairUseObject](https://developer.apple.com/reference/healthkit/hkwheelchairuseobject) e o `WheelchairUse` método da [HKHealthStore](https://developer.apple.com/reference/healthkit/hkhealthstore) classe foram adicionadas para trabalhar com cadeiras de rodas nos dados de integridade relacionados.
- Foram adicionadas novas chaves de metadados para tipos de clima (como `HKWeatherConditionClear` e `HKWeatherConditionCloudy`) e tipos de treinamento (como `HKWorkoutActivityTypeFlexibility` e `HKWorkoutActivityTypeWheelchairRunPace`) foram adicionadas.


## <a name="homekit"></a>HomeKit

Fez as seguintes melhorias para a estrutura HomeKit para inspeção 3 do sistema operacional:

- Adicionada a capacidade de exibir e interagir com o HomeKit conectados IP câmeras.
- Adicionados vários novos serviços e características.
- Adicionadas mais contexto e a configuração de Acessórios de serviços principais e os serviços de link.


## <a name="passkit"></a>PassKit

Fez as seguintes melhorias ao PassKit framework para inspeção 3 do sistema operacional:

- Expande o framework para dar suporte a pagamentos seguros no aplicativo do Apple Watch de bens físicos e serviços.
- As classes a seguir agora estão disponíveis: [PKPayment](https://developer.apple.com/reference/passkit/pkpayment), [PKPaymentMethod](https://developer.apple.com/reference/passkit/pkpaymentmethod), [PKPaymentRequest](https://developer.apple.com/reference/passkit/pkpaymentrequest) e [PKPaymentToken](https://developer.apple.com/reference/passkit/pkpaymenttoken)


## <a name="uikit"></a>UIKit

Fez as seguintes melhorias para a estrutura UIKit para inspeção 3 do sistema operacional:

- Para dar suporte a tipo dinâmico em rótulos, campos de texto e caixas de texto usam a nova `PreferredFontForTextStyle` método da `UIFont` classe.
- O `ColorWithDisplayP3` método foi adicionado para dar suporte a cores de largura.


## <a name="related-links"></a>Links relacionados

- [Guia de Introdução (amostra)](https://developer.xamarin.com/samples/monotouch/WatchKit/)
- [O que há de novo no watchOS 3](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewInwatchOS/Articles/watchOS3.html#//apple_ref/doc/uid/TP40017085-SW1)
