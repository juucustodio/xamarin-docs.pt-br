---
title: ListView do Xamarin. Android e o ciclo de vida da atividade
ms.prod: xamarin
ms.assetid: 40840D03-6074-30A2-74DA-3664703E3367
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 7c6e395a353dcfd737ad244df9d169edc5b08f1c
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68510302"
---
# <a name="xamarinandroid-listview-and-the-activity-lifecycle"></a>ListView do Xamarin. Android e o ciclo de vida da atividade

As atividades passam por determinados Estados à medida que seu aplicativo é executado, como inicialização, execução, pausa e parada. Para obter mais informações e diretrizes específicas sobre como lidar com transições de estado, consulte o [tutorial ciclo de vida da atividade](~/android/app-fundamentals/activity-lifecycle/index.md).
É importante entender o ciclo de vida da atividade e posicionar seu `ListView` código nos locais corretos.

Todos os exemplos neste documento executam "tarefas de instalação" no método da `OnCreate` atividade e (quando necessário) executam "desmontagem" em. `OnDestroy` Os exemplos geralmente usam pequenos conjuntos de dados que não são alterados, portanto, carregar novamente os dados com mais frequência é desnecessário.

No entanto, se os dados forem alterados com frequência ou usar muita memória, talvez seja apropriado usar diferentes métodos de ciclo de vida para popular `ListView`e atualizar seu. Por exemplo, se os dados subjacentes estiverem constantemente mudando (ou podem ser afetados por atualizações em outras atividades), criar o adaptador `OnStart` no `OnResume` ou garantirá que os dados mais recentes sejam exibidos cada vez que a atividade for mostrada.

Se o adaptador usar recursos como memória ou um cursor gerenciado, lembre-se de liberar esses recursos no método complementar para o local em que foram instanciados (por exemplo, os objetos criados `OnStart` no podem ser descartados em `OnStop`).


## <a name="configuration-changes"></a>Alterações de configuração

É importante lembrar que as alterações &ndash; de configuração especialmente a rotação de tela e a visibilidade &ndash; do teclado podem fazer com que a atividade atual seja destruída e recriada (a `ConfigurationChanges` menos que você especifique o contrário usando o atributo). Isso significa que, em condições normais, girar um dispositivo fará com `ListView` que `Adapter` um e seja recriado e (a menos que você tenha escrito `OnPause` código `OnResume`em e) os Estados de posição de rolagem e de seleção de linha serão perdidos.

O atributo a seguir impediria que uma atividade fosse destruída e recriada como resultado de alterações de configuração:

```csharp
[Activity(ConfigurationChanges="keyboardHidden|orientation")]
```

A atividade deve então ser `OnConfigurationChanged` substituída para responder a essas alterações adequadamente. Para obter mais detalhes sobre como lidar com alterações de configuração, consulte a documentação.

