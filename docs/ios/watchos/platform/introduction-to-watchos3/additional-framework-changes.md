---
title: Alterações adicionais de estruturas watchOS 3
description: Este documento descreve várias alterações de estrutura introduzidas com o watchOS 3 e como trabalhar com elas no Xamarin. Os dados principais, o Motion Core, o Foundation, o HealthKit, o HomeKit, o PassKit e o UIKit são discutidos.
ms.prod: xamarin
ms.assetid: FE93796E-F699-4B14-B37D-D39F9D48E81E
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/17/2017
ms.openlocfilehash: 34f192938ac583e39232312377142015aa6d3811
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70287559"
---
# <a name="additional-watchos-3-frameworks-changes"></a>Alterações adicionais de estruturas watchOS 3

_Este artigo aborda as alterações adicionais e secundárias ou aprimoramentos nas estruturas existentes do watchOS 3._

Além das principais alterações no iOS, a Apple fez modificações e melhorias em várias estruturas existentes no watchOS 3.


## <a name="core-data"></a>Dados principais

Os aprimoramentos a seguir foram feitos na estrutura de dados principal para o sistema operacional de inspeção 3:

- Os objetos raiz [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) dão suporte à falha e busca simultâneas sem serialização.
- A classe [NSPersistentStoreCoordinator](https://developer.apple.com/reference/coredata/nspersistentstorecoordinator) mantém um pool de armazenamentos de dados do SQLite.
- Os objetos [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) com armazenamentos de dados SQLite no modo de diário Wal dão suporte ao novo recurso de geração de consulta em que os contextos de objeto gerenciado (MOC) podem ser fixados em versões de banco de dados específicas para futuras transações de busca e falha.
- Usando o alto nível `NSPersistenceContainer` para fazer referência a, [NSManagedObjectModel](https://developer.apple.com/reference/coredata/nsmanagedobjectmodel) e outros recursos principais de configuração de `NSPersistentStoreCoordinator`dados.
- Vários novos métodos de conveniência foram adicionados para `NSManagedObject` tornar mais fácil executar buscas e criar subclasses.

Para obter mais informações, consulte a [referência da estrutura de dados principal](https://developer.apple.com/reference/coredata)da Apple.


## <a name="core-motion"></a>Movimento principal

Os aprimoramentos a seguir foram feitos na estrutura de movimento principal para o sistema operacional de inspeção 3:

- O novo evento de movimento do dispositivo usa o acelerômetro e o giroscópio para fornecer atualizações de movimento e de orientação. O aplicativo pode se registrar para essa atualização (em taxas de até 100Hz).
- O novo evento pedometer permite notificações rápidas e em tempo real quando o usuário pausa e retoma a execução. Use o [CMPedometer](https://developer.apple.com/reference/coremotion/cmpedometer) para se registrar em eventos de pedometer de primeiro plano ou em segundo plano.


## <a name="foundation"></a>Fundamental

Os aprimoramentos a seguir foram feitos na estrutura de base para o sistema operacional de inspeção 3:

- Use a nova classe [NSDateInterval](https://developer.apple.com/reference/foundation/nsdateinterval) para fazer cálculos de intervalo de data e hora, como durações, para comparar intervalos e testar as interseções de intervalo.
- Várias novas propriedades foram adicionadas à classe [NSLocal](https://developer.apple.com/reference/foundation/nslocale) para adquirir informações locais e os formatos de exibição disponíveis.
- Use a nova classe [NSMeasurement](https://developer.apple.com/reference/foundation/nsmeasurement) para converter entre diferentes unidades de medida (unidade ea) ou executar cálculos em valores em diferentes UOMs.
- Use a nova classe [NSMeasurementFormatter](https://developer.apple.com/reference/foundation/nsmeasurementformatter) para formatar as medidas localizadas para exibição para o usuário final.
- Use as novas classes [NSUnit](https://developer.apple.com/reference/foundation/nsunit) e [NSDimension](https://developer.apple.com/reference/foundation/nsdimension) para representar UOMs específicos.


## <a name="healthkit"></a>HealthKit

Os seguintes aprimoramentos foram feitos na estrutura HealthKit para o sistema operacional de observação 3:

- Use a nova classe [HKWorkoutConfiguration](https://developer.apple.com/reference/healthkit/hkworkoutconfiguration) para especificar o `ActivityType` e `LocationType` de um treinamento.
- O novo [HKWheelchairUseObject](https://developer.apple.com/reference/healthkit/hkwheelchairuseobject) e o `WheelchairUse` método da classe [HKHealthStore](https://developer.apple.com/reference/healthkit/hkhealthstore) foram adicionados para trabalhar com dados de integridade relacionados a cadeira de rodas.
- Foram adicionadas novas chaves de metadados para os tipos meteorológicos `HKWeatherConditionClear` ( `HKWeatherConditionCloudy`como e) e os tipos de `HKWorkoutActivityTypeFlexibility` treinamento `HKWorkoutActivityTypeWheelchairRunPace`(como e) foram adicionados.


## <a name="homekit"></a>HomeKit

Os seguintes aprimoramentos foram feitos na estrutura HomeKit para o sistema operacional de observação 3:

- Adicionada a capacidade de exibir e interagir com câmeras de IP conectadas do HomeKit.
- Foram adicionados vários novos serviços e características.
- Adição de mais contexto e configuração dos acessórios de serviços primários e serviços de link.


## <a name="passkit"></a>PassKit

Os seguintes aprimoramentos foram feitos na estrutura PassKit para o sistema operacional de observação 3:

- Expande a estrutura para dar suporte a pagamentos seguros e no aplicativo na Apple Watch de bens e serviços físicos.
- As seguintes classes agora estão disponíveis: [PKPayment](https://developer.apple.com/reference/passkit/pkpayment), [PKPaymentMethod](https://developer.apple.com/reference/passkit/pkpaymentmethod), [PKPaymentRequest](https://developer.apple.com/reference/passkit/pkpaymentrequest) e [PKPaymentToken](https://developer.apple.com/reference/passkit/pkpaymenttoken)


## <a name="uikit"></a>UIKit

Os seguintes aprimoramentos foram feitos na estrutura UIKit para o sistema operacional de observação 3:

- Para dar suporte ao tipo dinâmico em rótulos, campos `PreferredFontForTextStyle` `UIFont` de texto e caixas de texto, use o novo método da classe.
- O `ColorWithDisplayP3` método foi adicionado para dar suporte à cor ampla.


## <a name="related-links"></a>Links relacionados

- [exemplos de watchOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS%20watchos)
- [O que há de novo no watchOS 3](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewInwatchOS/Articles/watchOS3.html#//apple_ref/doc/uid/TP40017085-SW1)
