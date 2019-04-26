---
title: Tópicos de integração avançada
description: Este documento descreve tópicos avançados relacionados às integrações de pastas de trabalho do Xamarin. Ele aborda o pacote Xamarin.Workbook.Integrations NuGet e a exposição de API dentro de uma pasta de trabalho do Xamarin.
ms.prod: xamarin
ms.assetid: 002CE0B1-96CC-4AD7-97B7-43B233EF57A6
author: lobrien
ms.author: laobri
ms.date: 03/30/2017
ms.openlocfilehash: 56ee709b78b8587c2717dc9d25a6357041812d23
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61382239"
---
# <a name="advanced-integration-topics"></a>Tópicos de integração avançada

Assemblies de integração devem fazer referência a [ `Xamarin.Workbooks.Integrations` NuGet][nuget]. Confira nossos [documentação de início rápido](~/tools/workbooks/sdk/index.md) para obter mais informações sobre como começar com o pacote do NuGet.

Integrações de cliente também têm suporte e são iniciadas, colocando os arquivos JavaScript ou CSS com o mesmo nome do conjunto de módulos de integração do agente no mesmo diretório. Por exemplo, se o assembly de agente de integração (que faz referência do NuGet) é denominado `SampleExternalIntegration.dll`, em seguida, `SampleExternalIntegration.js` e `SampleExternalIntegration.css` serão integrados ao cliente também se eles existirem. Integrações de cliente são opcionais.

A integração de externa pode ser empacotada como um NuGet, fornecida e referenciada diretamente dentro do aplicativo que hospeda o agente, ou simplesmente colocados lado a lado um `.workbook` arquivo que deseja consumi-lo.

Integrações externas (cliente e agente) em pacotes do NuGet serão carregadas automaticamente quando o pacote é referenciado, de acordo com a documentação de início rápido, enquanto os assemblies de integração fornecidos junto com a pasta de trabalho serão necessário fazer referência a ele, então:

```csharp
#r "SampleExternalIntegration.dll"
```

Ao fazer referência a uma integração dessa forma, ele não será carregado pelo cliente imediatamente&mdash;você precisará chamar alguns códigos com a integração para que ele seja carregado. Podemos sobre esse bug no futuro.

O `Xamarin.Interactive` PCL fornece algumas APIs de integração importantes. Cada integração forneça pelo menos um ponto de entrada de integração:

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

Neste ponto, depois que o assembly de integração é referenciado, o cliente implicitamente carregará arquivos de integração do JavaScript e CSS.

## <a name="apis"></a>APIs

Como com qualquer assembly que é referenciado por uma pasta de trabalho ou ao vivo inspecionar a sessão, qualquer uma das suas APIs públicas são acessíveis para a sessão. Portanto, é importante ter uma superfície de API adequada e segura para usuários explorarem.

O assembly de integração é efetivamente uma ponte entre um aplicativo ou o SDK de interesse e a sessão. Ele pode fornecer novas APIs que fazem sentido especificamente no contexto de uma pasta de trabalho ou ao vivo inspecionar a sessão, ou não fornecer nenhuma API público e simplesmente executar tarefas de "de bastidores", como transferir um objeto [representações](~/tools/workbooks/sdk/representations.md).

> [!NOTE]
> As APIs que devem ser públicos, mas não devem ser apresentadas por meio do IntelliSense podem ser marcadas com o habitual `[EditorBrowsable (EditorBrowsableState.Never)]` atributo.

[nuget]: https://nuget.org/packages/Xamarin.Workbooks.Integration
