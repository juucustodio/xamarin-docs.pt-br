---
title: Tópicos de integração avançada
ms.prod: xamarin
ms.assetid: 002CE0B1-96CC-4AD7-97B7-43B233EF57A6
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 03/30/2017
ms.openlocfilehash: 8ab0bb71d4c79895eca94899c3f277b466fe0eb1
ms.sourcegitcommit: 775a7d1cbf04090eb75d0f822df57b8d8cff0c63
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2018
---
# <a name="external-integrations"></a>Integrações externas

Assemblies de integração devem fazer referência a [ `Xamarin.Workbooks.Integrations` NuGet][nuget]. Confira nosso [documentação de início rápido](~/tools/workbooks/sdk/index.md) para obter mais informações sobre como começar com o pacote do NuGet.

Integrações do cliente também têm suporte e são iniciadas por colocar arquivos CSS ou JavaScript com o mesmo nome do conjunto de módulos de integração do agente no mesmo diretório. Por exemplo, se o conjunto de integração do agente (que referencia o NuGet) é nomeado `SampleExternalIntegration.dll`, em seguida, `SampleExternalIntegration.js` e `SampleExternalIntegration.css` serão integrados ao cliente também se eles existirem. Integrações do cliente são opcionais.

A própria integração externa pode ser empacotada como um NuGet, fornecida e referenciada diretamente dentro do aplicativo que hospeda o agente ou simplesmente posicionados junto com um `.workbook` arquivo que deseja consumi-lo.

Integrações externas (cliente e agente) em pacotes do NuGet serão carregadas automaticamente quando o pacote é referenciado, de acordo com a documentação de início rápido, enquanto os assemblies de integração enviados junto com a pasta de trabalho serão necessário fazer referência a ele como assim:

```csharp
#r "SampleExternalIntegration.dll"
```

Ao fazer referência a uma integração dessa forma, ele não será carregado pelo cliente imediatamente&mdash;você precisará chamar um código de integração para que ele seja carregado. Podemos será abordar esse bug no futuro.

O `Xamarin.Interactive` PCL fornece integração importante algumas APIs. Cada integração deve fornecer pelo menos um ponto de entrada de integração:

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

Neste ponto, depois que o assembly de integração é referenciado, o cliente implicitamente carregar arquivos de integração do JavaScript e CSS.

## <a name="apis"></a>APIs

Como com qualquer assembly que é referenciado por uma pasta de trabalho ou ao vivo inspecionar sessão, qualquer uma das suas APIs públicas são acessíveis para a sessão. Portanto, é importante ter uma superfície de API sensata e segura para usuários explorarem.

O assembly de integração é efetivamente uma ponte entre um aplicativo ou o SDK de interesse e a sessão. Ele pode fornecer novas APIs que fazem sentido especificamente no contexto de uma pasta de trabalho ou ao vivo inspecionar a sessão, ou não fornecer nenhum APIs públicas e simplesmente executar tarefas de "nos bastidores" como objeto de resposta [representações](~/tools/workbooks/sdk/representations.md).

> [!NOTE]
> APIs que devem ser públicos, mas não devem ser apresentados por meio do IntelliSense pode ser marcado com o habitual `[EditorBrowsable (EditorBrowsableState.Never)]` atributo.

[nuget]: https://nuget.org/packages/Xamarin.Workbooks.Integration
