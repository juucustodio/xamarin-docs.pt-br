---
title: "Guia de Introdução com as pastas de trabalho do Xamarin SDK"
ms.topic: article
ms.prod: xamarin
ms.assetid: FAED4445-9F37-46D8-B408-E694060969B9
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.openlocfilehash: 53bc3d1a897eb007cd4c2971b0e3aa3fa01e1238
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2018
---
# <a name="getting-started-with-the-xamarin-workbooks-sdk"></a>Guia de Introdução com as pastas de trabalho do Xamarin SDK

Este documento fornece um guia rápido de Introdução ao desenvolvimento integrações para pastas de trabalho do Xamarin. Isso funcionará com as pastas de trabalho do Xamarin estável, mas **carregar integrações por meio de pacotes do NuGet só tem suporte em pastas de trabalho 1.3**, no canal alfa no momento da gravação.

# <a name="general-overview"></a>Visão geral

Integrações de pastas de trabalho do Xamarin são pequenas bibliotecas que usam o [ `Xamarin.Workbooks.Integrations` NuGet] [ nuget] SDK para integrar com o Xamarin pastas de trabalho e o Inspetor de agentes para fornecer experiências aprimoradas.

Há 3 etapas principais de Introdução ao desenvolvimento de uma integração —, o descreveremos aqui.

## <a name="creating-the-integration-project"></a>Criando o projeto de integração

Bibliotecas de integração melhor são desenvolvidas como bibliotecas de várias plataformas. Como você deseja fornecer a melhor integração em todos os agentes disponíveis, passados e futuros, convém escolher um conjunto em larga escala com suporte de bibliotecas. É recomendável usar o modelo "Biblioteca portátil" para o mais amplo suporte:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Biblioteca portátil modelo Visual Studio para Mac](images/xamarin-studio-pcl.png)](images/xamarin-studio-pcl.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Biblioteca portátil modelo Visual Studio](images/visual-studio-pcl.png)](images/visual-studio-pcl.png#lightbox)

No Visual Studio, você desejará certificar-se de que você selecione as seguintes plataformas de destino para a sua biblioteca portátil:

[![Biblioteca portátil plataformas Visual Studio](images/visual-studio-pcl-platforms.png)](images/visual-studio-pcl-platforms.png#lightbox)

-----

Depois de criar o projeto de biblioteca, adicione uma referência ao nosso `Xamarin.Workbooks.Integration` NuGet biblioteca via o NuGet Package Manager.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![NuGet Visual Studio para Mac](images/xamarin-studio-nuget.png)](images/xamarin-studio-nuget.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![NuGet Visual Studio](images/visual-studio-nuget.png)](images/visual-studio-nuget.png#lightbox)

-----

Você vai querer excluir a classe vazia que é criada como parte do projeto — você não precisará-lo para isso. Depois de fazer essas etapas, você estará pronto para começar a integração.

## <a name="building-an-integration"></a>Uma integração

Criaremos uma integração simple. Realmente adoramos a cor verde, portanto, adicionaremos a cor verde como uma representação para cada objeto. Primeiro, crie uma nova classe chamada `SampleIntegration`e torná-lo a implementar nosso [ `IAgentIntegration` ] [ integration-type] interface:

```csharp
using Xamarin.Interactive;

public class SampleIntegration : IAgentIntegration
{
    public void IntegrateWith (IAgent agent)
    {
    }
}
```

O que queremos fazer é adicionar um [representação](~/tools/workbooks/sdk/representations.md) para cada objeto que é uma cor verde. Faremos isso usando um provedor de representação. Provedores herdam o [ `RepresentationProvider` ] [ reppr] classe — para nossa, precisamos substituir [ `ProvideRepresentations` ] [ prrep]:

```csharp
using Xamarin.Interactive.Representations;

class SampleRepresentationProvider : RepresentationProvider
{
    public override IEnumerable<object> ProvideRepresentations (object obj)
    {
        // This corresponds to Pantone 2250 XGC, our favorite color.
        yield return new Color (0.0, 0.702, 0.4314);
    }
}
```

Estamos retornando um [ `Color` ] [ color], um predefinidos em tipo de representação no nosso SDK.
Você observará que o tipo de retorno aqui é um `IEnumerable<object>` &mdash;um provedor de representação pode retornar várias representações de um objeto! Todos os provedores de representação são chamados para cada objeto, portanto, é importante não fazer suposições sobre quais objetos estão sendo passados para você.

A etapa final é registrar o nosso provedor com o agente e informar onde encontrar o nosso tipo de integração de pastas de trabalho. Para registrar o provedor, adicione este código para o `IntegrateWith` método o `SampleIntegration` classe criado anteriormente:

```csharp
agent.RepresentationManager.AddProvider (new SampleRepresentationProvider ());
```

Definindo o tipo de integração é feito por meio de um atributo de assembly. Você pode colocar isso em seu AssemblyInfo.cs ou na mesma classe como tipo de integração para conveniência:

```csharp
[assembly: AgentIntegration (typeof (SampleIntegration))]
````

Durante o desenvolvimento, você pode achar mais conveniente usar [ `AddProvider` sobrecargas] [ addprovider] em `RepresentationManager` que permitem que você registrar um retorno de chamada simple para fornecer representações dentro de uma pasta de trabalho e, em seguida, mova esse código em seu `RepresentationProvider` implementação quando tiver terminado. Um exemplo de processamento de um [ `OxyPlot` ] [ oxyplot] `PlotModel` pode ter esta aparência:

```csharp
InteractiveAgent.RepresentationManager.AddProvider<PlotModel> (
  plotModel => Image (new SvgExporter {
      Width = 300,
      Height = 250
    }.ExportToString (plotModel)));
```

> [!NOTE]
> Essas APIs oferecem uma maneira rápida de colocar em funcionamento, mas não recomendamos um inteiro de integração de envio somente de usá-los&mdash;eles fornecem muito pouco controle sobre como seus tipos são processados pelo cliente.

Com a representação registrada, a integração está pronta para ser entregue!

## <a name="shipping-your-integration"></a>A integração de envio

Para enviar sua integração, você precisará adicioná-lo a um pacote do NuGet.
Podem ser fornecidas com o NuGet da sua biblioteca existente, ou se você estiver criando um novo pacote, você pode usar esse arquivo. NuSpec de modelo como ponto de partida.
Você precisará preencher as seções relevantes para a integração. A parte mais importante é que todos os arquivos para a integração devem estar em um `xamarin.interactive` diretório na raiz do pacote. Isso nos permite localizar facilmente todos os arquivos relevantes para a integração, independentemente de você usa um pacote existente ou crie um novo.

```xml
<?xml version="1.0"?>
<package xmlns="http://schemas.microsoft.com/packaging/2010/07/nuspec.xsd">
    <metadata>
      <id>$YourNuGetPackage$</id>
      <version>$YourVersion$</version>
      <authors>$YourNameHere$</authors>
      <projectUrl>$YourProjectPage$</projectUrl>
      <description>A short description of your library.</description>
    </metadata>
    <files>
      <file src="Path\To\Your\Integration.dll" target="xamarin.interactive" />
    </files>
</package>
```

Depois de criar o arquivo. NuSpec, você pode empacotar o NuGet da seguinte forma:

```csharp
nuget pack MyIntegration.nuspec
```

e, em seguida, publicá-lo para [NuGet][nugetorg]. Depois disso, você poderá referenciá-lo de qualquer pasta de trabalho e ver em ação. Captura de tela abaixo, nós reunimos a integração de exemplo que é criado neste documento e instalado o pacote do NuGet em uma pasta de trabalho:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Pasta de trabalho com a integração](images/mac-workbooks-integrated.png)](images/mac-workbooks-integrated.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Pasta de trabalho com a integração](images/windows-workbooks-integrated.png)](images/windows-workbooks-integrated.png#lightbox)

-----

Observe que você não verá quaisquer `#r` diretivas ou nada para inicializar a integração — pastas de trabalho foi resolvido tudo isso para você em segundo plano!

## <a name="next-steps"></a>Próximas etapas

Check-out de nossa documentação para obter mais informações sobre as partes de movimentação que compõem o SDK, e nosso [integrações de exemplo](~/tools/workbooks/samples/index.md) para operações adicionais que você pode fazer com sua integração, como fornecer JavaScript personalizado que é executado em o cliente de pastas de trabalho.

[integration-type]: https://developer.xamarin.com/api/type/Xamarin.Interactive.IAgentIntegration/
[repman-api]: https://developer.xamarin.com/api/type/Xamarin.Interactive.Representations.IRepresentationManager/
[color]: https://developer.xamarin.com/api/type/Xamarin.Interactive.Representations.Color/
[xir]: https://developer.xamarin.com/api/namespace/Xamarin.Interactive.Representations/
[reppr]: https://developer.xamarin.com/api/type/Xamarin.Interactive.Representations.RepresentationProvider/
[prrep]: https://developer.xamarin.com/api/member/Xamarin.Interactive.Representations.RepresentationProvider.ProvideRepresentations/p/System.Object/
[nugetorg]: https://nuget.org
[nuget]: https://nuget.org/packages/Xamarin.Workbooks.Integration
[addprovider]: https://developer.xamarin.com/api/member/Xamarin.Interactive.Representations.IRepresentationManager.AddProvider/
[oxyplot]: http://www.oxyplot.org/
