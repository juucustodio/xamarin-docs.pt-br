---
title: Introdução com o SDK do Xamarin Workbooks
description: Este documento descreve como começar a usar o SDK do Xamarin Workbooks, que pode ser usado para desenvolver integrações para Xamarin Workbooks.
ms.prod: xamarin
ms.assetid: FAED4445-9F37-46D8-B408-E694060969B9
author: conceptdev
ms.author: crdun
ms.date: 03/30/2017
ms.openlocfilehash: 8e3dc65f9f615ff893f3526d53d99da25045c794
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70283960"
---
# <a name="getting-started-with-the-xamarin-workbooks-sdk"></a>Introdução com o SDK do Xamarin Workbooks

Este documento fornece um guia rápido de introdução ao desenvolvimento de integrações para Xamarin Workbooks. Grande parte disso funcionará com o Xamarin Workbooks estável, mas o **carregamento de integrações por meio de pacotes NuGet só tem suporte nas pastas de trabalho 1,3**, no canal alfa no momento da gravação.

## <a name="general-overview"></a>Visão geral

As integrações de Xamarin Workbooks são bibliotecas pequenas que usam o SDK do [ `Xamarin.Workbooks.Integrations` NuGet][nuget] para integrar com os agentes de Xamarin Workbooks e de inspetor para fornecer experiências aprimoradas.

Há três etapas principais para a introdução ao desenvolvimento de uma integração — descreveremos estas aqui.

## <a name="creating-the-integration-project"></a>Criando o projeto de integração

As bibliotecas de integração são mais bem desenvolvidas como bibliotecas multiplataforma. Como você deseja fornecer a melhor integração em todos os agentes disponíveis, no passado e no futuro, convém escolher um conjunto de bibliotecas com suporte amplo. É recomendável usar o modelo "biblioteca portátil" para obter o suporte mais amplo:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![Modelo de biblioteca portátil Visual Studio para Mac](images/xamarin-studio-pcl.png)](images/xamarin-studio-pcl.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Modelo de biblioteca portátil Visual Studio](images/visual-studio-pcl.png)](images/visual-studio-pcl.png#lightbox)

No Visual Studio, você deve ter certeza de selecionar as seguintes plataformas de destino para sua biblioteca portátil:

[![Plataformas de biblioteca portátil Visual Studio](images/visual-studio-pcl-platforms.png)](images/visual-studio-pcl-platforms.png#lightbox)

-----

Depois de criar o projeto de biblioteca, adicione uma referência à `Xamarin.Workbooks.Integration` nossa biblioteca do NuGet por meio do Gerenciador de pacotes NuGet.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![Visual Studio para Mac NuGet](images/xamarin-studio-nuget.png)](images/xamarin-studio-nuget.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![NuGet Visual Studio](images/visual-studio-nuget.png)](images/visual-studio-nuget.png#lightbox)

-----

Você desejará excluir a classe vazia que é criada para você como parte do projeto — você não precisará dela para isso. Depois de concluir essas etapas, você estará pronto para começar a criar sua integração.

## <a name="building-an-integration"></a>Criando uma integração

Criaremos uma integração simples. Na verdade, adoramos a cor verde, então vamos adicionar a cor verde como uma representação a cada objeto. Primeiro, crie uma nova classe chamada `SampleIntegration`e faça com que ela implemente nossa `IAgentIntegration` interface:

```csharp
using Xamarin.Interactive;

public class SampleIntegration : IAgentIntegration
{
    public void IntegrateWith (IAgent agent)
    {
    }
}
```

O que queremos fazer é adicionar uma [representação](~/tools/workbooks/sdk/representations.md) para cada objeto que seja uma cor verde. Faremos isso usando um provedor de representação. Os `RepresentationProvider` provedores herdam da classe — para nós, precisamos apenas substituir `ProvideRepresentations`:

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

Estamos retornando `Color`um tipo de representação predefinida em nosso SDK.
Você observará que o tipo de retorno aqui é `IEnumerable<object>`um &mdash;provedor de representação que pode retornar muitas representações para um objeto! Todos os provedores de representação são chamados para cada objeto, portanto, é importante não fazer suposições sobre quais objetos estão sendo passados para você.

A etapa final é realmente registrar nosso provedor com o agente e dizer às pastas de trabalho onde encontrar nosso tipo de integração. Para registrar o provedor, adicione este código ao `IntegrateWith` método `SampleIntegration` na classe que criamos anteriormente:

```csharp
agent.RepresentationManager.AddProvider (new SampleRepresentationProvider ());
```

A configuração do tipo de integração é feita por meio de um atributo de todo o assembly. Você pode colocar isso em seu AssemblyInfo.cs ou na mesma classe que seu tipo de integração para sua conveniência:

```csharp
[assembly: AgentIntegration (typeof (SampleIntegration))]
````

Durante o desenvolvimento, você pode achar mais conveniente usar `AddProvider` sobrecargas no `RepresentationManager` que permitem registrar um retorno de chamada simples para fornecer representações dentro de uma pasta de trabalho e, em seguida, mover esse `RepresentationProvider` código para sua implementação uma vez Você terminou. Um exemplo para renderizar [`OxyPlot`][oxyplot] um `PlotModel` pode ser assim:

```csharp
InteractiveAgent.RepresentationManager.AddProvider<PlotModel> (
  plotModel => Image (new SvgExporter {
      Width = 300,
      Height = 250
    }.ExportToString (plotModel)));
```

> [!NOTE]
> Essas APIs oferecem uma maneira rápida de colocar em funcionamento, mas não recomendamos enviar uma integração inteira apenas usando-as&mdash;, elas fornecem muito pouco controle sobre como os tipos são processados pelo cliente.

Com a representação registrada, sua integração está pronta para ser remetida!

## <a name="shipping-your-integration"></a>Enviando sua integração

Para enviar sua integração, você precisará adicioná-la a um pacote NuGet.
Você pode enviá-lo com o NuGet da biblioteca existente ou, se estiver criando um novo pacote, poderá usar esse arquivo template. nuspec como um ponto de partida.
Você precisará preencher as seções relevantes para sua integração. A parte mais importante é que todos os arquivos para sua integração devem estar em um `xamarin.interactive` diretório na raiz do pacote. Isso nos permite encontrar facilmente todos os arquivos relevantes para sua integração, independentemente de você usar um pacote existente ou criar um novo.

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

Depois de criar o arquivo. nuspec, você pode empacotar o NuGet da seguinte forma:

```csharp
nuget pack MyIntegration.nuspec
```

e, em seguida, publicá-lo no [NuGet][nugetorg]. Quando estiver lá, você poderá fazer referência a ela em qualquer pasta de trabalho e vê-la em ação. Na captura de tela abaixo, empacotamos a integração de exemplo que criamos neste documento e instalamos o pacote NuGet em uma pasta de trabalho:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![Pasta de trabalho com integração](images/mac-workbooks-integrated.png)](images/mac-workbooks-integrated.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Pasta de trabalho com integração](images/windows-workbooks-integrated.png)](images/windows-workbooks-integrated.png#lightbox)

-----

Observe que você não vê nenhuma `#r` diretivas ou nada para inicializar a integração — as pastas de trabalho cuidam de tudo isso para você em segundo plano!

## <a name="next-steps"></a>Próximas etapas

Confira nossa outra documentação para obter mais informações sobre as partes móveis que compõem o SDK e nossas [integrações de exemplo](~/tools/workbooks/samples/index.md) para tarefas adicionais que você pode fazer com sua integração, como fornecer JavaScript personalizado que é executado no cliente de pastas de trabalho.

[nugetorg]: https://nuget.org
[nuget]: https://nuget.org/packages/Xamarin.Workbooks.Integration
[oxyplot]: http://www.oxyplot.org/
