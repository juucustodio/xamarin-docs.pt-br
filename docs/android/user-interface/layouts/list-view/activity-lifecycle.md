---
title: ListView e o ciclo de vida de atividade
ms.prod: xamarin
ms.assetid: 40840D03-6074-30A2-74DA-3664703E3367
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: b2328759b3158920bc8683ec14c2aebefd7a04ae
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50117714"
---
# <a name="listview-and-the-activity-lifecycle"></a>ListView e o ciclo de vida de atividade

As atividades percorrem determinados estados conforme seu aplicativo é executado, por exemplo, iniciar, em execução, em pausa e que está sendo interrompido. Para obter mais informações e diretrizes específicas sobre o tratamento de transições de estado, consulte a [Tutorial de ciclo de vida de atividade](~/android/app-fundamentals/activity-lifecycle/index.md).
É importante entender o ciclo de vida de atividade e coloque seu `ListView` código nos locais corretos.

Todos os exemplos neste documento executam tarefas de configuração na atividade de `OnCreate` método e (quando necessário) execute 'subdivisão' no `OnDestroy`. Os exemplos geralmente usam pequenos conjuntos de dados que não são alterados, para que recarregar os dados com mais frequência é desnecessária.

No entanto, se seus dados está sendo alterado com frequência ou se usar muita memória pode ser apropriado usar os métodos de ciclo de vida diferente para popular e atualizar seu `ListView`. Por exemplo, se os dados subjacentes são alterados constantemente (ou podem ser afetados pelas atualizações em outras atividades) e em seguida, criar o adaptador no `OnStart` ou `OnResume` garantirá que os dados mais recentes são exibidos sempre que a atividade é mostrada.

Se o adaptador usa recursos como memória ou um cursor gerenciado, lembre-se liberar esses recursos no método complementar para onde eles foram instanciados (por exemplo. os objetos criados no `OnStart` podem ser descartados no `OnStop`).


## <a name="configuration-changes"></a>Alterações de configuração

É importante lembrar-se de que a configuração é alterado &ndash; visibilidade de teclado e de rotação de tela especialmente &ndash; pode fazer com que a atividade atual ser destruído e criado novamente (a menos que você especificar usando outra forma o `ConfigurationChanges` atributo). Isso significa que em condições normais, girar um dispositivo fará com que um `ListView` e `Adapter` ser recriada e (a menos que você tenha escrito o código `OnPause` e `OnResume`) a seleção de linha e posição de rolagem estados serão perdidas.

O seguinte atributo impediria uma atividade que está sendo destruída e recriada como resultado das alterações de configuração:

```csharp
[Activity(ConfigurationChanges="keyboardHidden|orientation")]
```

A atividade deve substituir, em seguida, `OnConfigurationChanged` responder a essas alterações de forma apropriada. Para obter mais detalhes sobre como lidar com alterações de configuração, consulte a documentação.

