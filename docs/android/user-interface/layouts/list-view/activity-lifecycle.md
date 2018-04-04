---
title: ListView e o ciclo de vida da atividade
ms.prod: xamarin
ms.assetid: 40840D03-6074-30A2-74DA-3664703E3367
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 6e15fb8796ae6a616c5eae44059caae3d9478aef
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="listview-and-the-activity-lifecycle"></a>ListView e o ciclo de vida da atividade

Atividades passam por determinados estados como a execução do seu aplicativo, como iniciar, em execução, sendo pausada e que está sendo interrompido. Para obter mais informações e diretrizes específicas sobre como tratar as transições de estado, consulte o [Tutorial de ciclo de vida da atividade](~/android/app-fundamentals/activity-lifecycle/index.md).
É importante entender o ciclo de vida da atividade e coloque seu `ListView` código nos locais corretos.

Todos os exemplos neste documento executam tarefas de configuração na atividade de `OnCreate` método e (quando necessário) executar 'desmontagem' em `OnDestroy`. Os exemplos usam geralmente pequenos conjuntos de dados que não são alterados, para que recarregar os dados com mais frequência é desnecessária.

No entanto, se seus dados é que mudam frequentemente ou usam muita memória pode ser apropriado usar métodos diferentes de ciclo de vida para popular e atualizar seu `ListView`. Por exemplo, se os dados subjacentes são alterados constantemente (ou podem ser afetados pelas atualizações em outras atividades), em seguida, criar o adaptador em `OnStart` ou `OnResume` garantirá que os dados mais recentes são exibidos sempre que a atividade é mostrada.

Se o adaptador usa recursos como memória ou um cursor gerenciado, lembre-se liberar os recursos no método complementar para o qual foram instanciadas (por exemplo. objetos criados em `OnStart` pode ser descartado na `OnStop`).


## <a name="configuration-changes"></a>Alterações de configuração

É importante lembrar-se de que a configuração altera &ndash; tela especialmente visibilidade rotação e teclado &ndash; pode fazer com que a atividade atual a ser destruído e criado novamente (a menos que especificado de outra forma usando o `ConfigurationChanges` atributo). Isso significa que em condições normais, girar um dispositivo fará com que uma `ListView` e `Adapter` ser recriada e (a menos que você escreveu o código `OnPause` e `OnResume`) a seleção de linha e posição de rolagem estados serão perdidas.

O seguinte atributo impediria uma atividade do que está sendo destruído e recriado como resultado das alterações de configuração:

```csharp
[Activity(ConfigurationChanges="keyboardHidden|orientation")]
```

A atividade deve substituir `OnConfigurationChanged` para responder a essas alterações adequadamente. Para obter mais detalhes sobre como lidar com as alterações de configuração, consulte a documentação.

