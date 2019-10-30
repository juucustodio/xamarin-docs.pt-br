---
title: ListView do Xamarin. Android e o ciclo de vida da atividade
ms.prod: xamarin
ms.assetid: 40840D03-6074-30A2-74DA-3664703E3367
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/06/2018
ms.openlocfilehash: a4ebda89ad3bdcb663dc9d7cd513e45760163c34
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028942"
---
# <a name="xamarinandroid-listview-and-the-activity-lifecycle"></a>ListView do Xamarin. Android e o ciclo de vida da atividade

As atividades passam por determinados Estados à medida que seu aplicativo é executado, como inicialização, execução, pausa e parada. Para obter mais informações e diretrizes específicas sobre como lidar com transições de estado, consulte o [tutorial ciclo de vida da atividade](~/android/app-fundamentals/activity-lifecycle/index.md).
É importante entender o ciclo de vida da atividade e posicionar seu código de `ListView` nos locais corretos.

Todos os exemplos neste documento executam "tarefas de instalação" no método de `OnCreate` da atividade e (quando necessário) executam "desmontagem" em `OnDestroy`. Os exemplos geralmente usam pequenos conjuntos de dados que não são alterados, portanto, carregar novamente os dados com mais frequência é desnecessário.

No entanto, se os dados forem alterados com frequência ou usar muita memória, talvez seja apropriado usar diferentes métodos de ciclo de vida para popular e atualizar sua `ListView`. Por exemplo, se os dados subjacentes estiverem constantemente mudando (ou podem ser afetados por atualizações em outras atividades), a criação do adaptador em `OnStart` ou `OnResume` garantirá que os dados mais recentes sejam exibidos sempre que a atividade for mostrada.

Se o adaptador usar recursos como memória ou um cursor gerenciado, lembre-se de liberar esses recursos no método complementar para o local em que foram instanciados (por exemplo, os objetos criados no `OnStart` podem ser descartados em `OnStop`).

## <a name="configuration-changes"></a>Alterações de configuração

É importante lembrar-se de que as alterações de configuração &ndash; especialmente a rotação de tela e a visibilidade do teclado &ndash; podem fazer com que a atividade atual seja destruída e recriada (a menos que você especifique de outra forma usando o atributo `ConfigurationChanges`). Isso significa que, em condições normais, girar um dispositivo fará com que um `ListView` e `Adapter` seja recriado e (a menos que você tenha escrito código em `OnPause` e `OnResume`) os Estados de posição de rolagem e de seleção de linha serão perdidos.

O atributo a seguir impediria que uma atividade fosse destruída e recriada como resultado de alterações de configuração:

```csharp
[Activity(ConfigurationChanges="keyboardHidden|orientation")]
```

A atividade deve então substituir `OnConfigurationChanged` para responder a essas alterações adequadamente. Para obter mais detalhes sobre como lidar com alterações de configuração, consulte a documentação.
