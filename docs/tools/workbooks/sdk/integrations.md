---
title: Tópicos de integração avançada
description: Este documento descreve tópicos avançados relacionados a integrações do Xamarin Workbooks. Ele aborda o pacote do NuGet. Workbook. Integrations e a exposição da API em uma pasta de trabalho do Xamarin.
ms.prod: xamarin
ms.assetid: 002CE0B1-96CC-4AD7-97B7-43B233EF57A6
author: davidortinau
ms.author: daortin
ms.date: 03/30/2017
ms.openlocfilehash: 159fecbe1385c091c56a6ececb61bf7d020dfc1b
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029610"
---
# <a name="advanced-integration-topics"></a>Tópicos de integração avançada

Os assemblies de integração devem referenciar o [`Xamarin.Workbooks.Integrations` NuGet][nuget]. Confira nossa [documentação de início rápido](~/tools/workbooks/sdk/index.md) para obter mais informações sobre a introdução ao pacote NuGet.

As integrações do cliente também têm suporte e são iniciadas colocando arquivos JavaScript ou CSS com o mesmo nome que o assembly de integração do agente no mesmo diretório. Por exemplo, se o assembly de integração do agente (que faz referência ao NuGet) for nomeado `SampleExternalIntegration.dll`, `SampleExternalIntegration.js` e `SampleExternalIntegration.css` serão integrados ao cliente também se existirem. As integrações do cliente são opcionais.

A integração externa em si pode ser empacotada como um NuGet, fornecido e referenciado diretamente dentro do aplicativo que está hospedando o agente ou simplesmente colocado junto com um arquivo `.workbook` que deseja consumi-lo.

As integrações externas (agente e cliente) em pacotes NuGet serão carregadas automaticamente quando o pacote for referenciado, de acordo com a documentação de início rápido, enquanto os assemblies de integração fornecidos junto com a pasta de trabalho precisarão fazer referência a ela da seguinte forma:

```csharp
#r "SampleExternalIntegration.dll"
```

Ao fazer referência a uma integração dessa forma, ele não será carregado pelo cliente imediatamente&mdash;você precisará chamar algum código da integração para que ele seja carregado. Abordaremos esse bug no futuro.

O `Xamarin.Interactive` PCL fornece algumas APIs de integração importantes. Cada integração deve, pelo menos, fornecer um ponto de entrada de integração:

```csharp
using Xamarin.Interactive;

[assembly: AgentIntegration (typeof (AgentIntegration))]

class AgentIntegration : IAgentIntegration
{
    const string TAG = nameof (AgentIntegration);

    public void IntegrateWith (IAgent agent)
    {
        // hook into IAgent APIs
    }
}
```

Neste ponto, depois que o assembly de integração for referenciado, o cliente carregará implicitamente os arquivos de integração JavaScript e CSS.

## <a name="apis"></a>APIs

Assim como acontece com qualquer assembly que é referenciado por uma pasta de trabalho ou pela sessão de inspeção ao vivo, qualquer uma de suas APIs públicas é acessível para a sessão. Portanto, é importante ter uma superfície de API segura e sensata para que os usuários explorem.

O assembly de integração é efetivamente uma ponte entre um aplicativo ou um SDK de interesse e a sessão. Ele pode fornecer novas APIs que fazem sentido especificamente no contexto de uma pasta de trabalho ou de uma sessão de inspeção ao vivo, ou não fornecem APIs públicas e simplesmente executam tarefas "por trás das cenas", como a concessão de [representações](~/tools/workbooks/sdk/representations.md)de objeto.

> [!NOTE]
> As APIs que devem ser públicas, mas não devem ser apresentadas por meio do IntelliSense, podem ser marcadas com o atributo de `[EditorBrowsable (EditorBrowsableState.Never)]` usual.

[nuget]: https://nuget.org/packages/Xamarin.Workbooks.Integration
