---
title: Guia de Introdução com as pastas de trabalho do Xamarin do SDK
description: Este documento descreve como começar com o SDK de pastas de trabalho do Xamarin, que pode ser usado para desenvolver integrações para pastas de trabalho do Xamarin.
ms.prod: xamarin
ms.assetid: FAED4445-9F37-46D8-B408-E694060969B9
author: lobrien
ms.author: laobri
ms.date: 03/30/2017
ms.openlocfilehash: 5800e98acbff147735ae4a6125979a4b47be2367
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50115361"
---
# <a name="getting-started-with-the-xamarin-workbooks-sdk"></a>Guia de Introdução com as pastas de trabalho do Xamarin do SDK

Este documento fornece um guia rápido de Introdução ao desenvolvimento de integrações para o Xamarin Workbooks. A maior parte dessa funcionará com o Xamarin Workbooks estável, mas **carregar integrações por meio de pacotes do NuGet é compatível apenas com pastas de trabalho 1.3**, no canal alfa no momento da gravação.

## <a name="general-overview"></a>Visão geral

Integrações de Xamarin Workbooks são pequenas bibliotecas que usam o [ `Xamarin.Workbooks.Integrations` NuGet] [ nuget] SDK para integrar com o Xamarin Workbooks e Inspector agentes para fornecer experiências aprimoradas.

Há 3 etapas principais para introdução ao desenvolvimento de uma integração — vamos descrevê-los aqui.

## <a name="creating-the-integration-project"></a>Criando o projeto de integração

Bibliotecas de integração melhor são desenvolvidas como bibliotecas multiplataforma. Como você deseja fornecer a melhor integração em todos os agentes disponíveis, passados e futuros, convém escolher um conjunto em larga escala com suporte de bibliotecas. Recomendamos usar o modelo "Biblioteca portátil" para o mais amplo suporte:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![Biblioteca portátil modelo do Visual Studio para Mac](images/xamarin-studio-pcl.png)](images/xamarin-studio-pcl.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Biblioteca portátil modelo Visual Studio](images/visual-studio-pcl.png)](images/visual-studio-pcl.png#lightbox)

No Visual Studio, você desejará certificar-se de que selecionar as seguintes plataformas de destino para a sua biblioteca portátil:

[![Biblioteca portátil plataformas Visual Studio](images/visual-studio-pcl-platforms.png)](images/visual-studio-pcl-platforms.png#lightbox)

-----

Depois de criar o projeto de biblioteca, adicione uma referência ao nosso `Xamarin.Workbooks.Integration` biblioteca de NuGet por meio do Gerenciador de pacotes NuGet.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![NuGet Visual Studio para Mac](images/xamarin-studio-nuget.png)](images/xamarin-studio-nuget.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![NuGet Visual Studio](images/visual-studio-nuget.png)](images/visual-studio-nuget.png#lightbox)

-----

Você desejará excluir a classe vazia que é criada como parte do projeto — você não precisá-lo para isso. Depois de concluir essas etapas, você está pronto para começar a criar sua integração.

## <a name="building-an-integration"></a>Criação de uma integração

Vamos criar uma integração simple. Realmente adoramos a cor verde, portanto, vamos adicionar a cor verde como uma representação para cada objeto. Primeiro, crie uma nova classe chamada `SampleIntegration`e torná-lo a implementar nosso [ `IAgentIntegration` ] [ integration-type] interface:

```csharp
using Xamarin.Interactive;

public class SampleIntegration : IAgentIntegration
{
    public void IntegrateWith (IAgent agent)
    {
    }
}
```

O que queremos fazer é adicionar um [representação](~/tools/workbooks/sdk/representations.md) para cada objeto que é uma cor verde. Faremos isso usando um provedor de representação. Provedores de herdam de [ `RepresentationProvider` ] [ reppr] classe — para nossa, precisamos substituir [ `ProvideRepresentations` ] [ prrep]:

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

Estamos retornando uma [ `Color` ] [ color], um pré-criados em tipo de representação no nosso SDK.
Você observará que o tipo de retorno aqui é um `IEnumerable<object>` &mdash;um provedor de representação pode retornar várias representações para um objeto! Todos os provedores de representação são chamados para cada objeto, portanto, é importante não fazer nenhuma suposição sobre quais objetos estão sendo passados para você.

A etapa final é realmente registrar nosso provedor com o agente e informar onde encontrar nosso tipo de integração de pastas de trabalho. Para registrar o provedor, adicione este código para o `IntegrateWith` método no `SampleIntegration` classe criada anteriormente:

```csharp
agent.RepresentationManager.AddProvider (new SampleRepresentationProvider ());
```

O tipo de integração de configuração é feito por meio de um atributo de todo o assembly. Você pode colocar isso em seu AssemblyInfo.cs ou na mesma classe como seu tipo de integração para sua conveniência:

```csharp
[assembly: AgentIntegration (typeof (SampleIntegration))]
````

Durante o desenvolvimento, talvez seja mais conveniente usar [ `AddProvider` sobrecargas] [ addprovider] em `RepresentationManager` que permitem que você registre um retorno de chamada simple para fornecer representações dentro de uma pasta de trabalho e, em seguida, mova esse código em seu `RepresentationProvider` depois que você terminou de implementação. Um exemplo para renderização de um [ `OxyPlot` ] [ oxyplot] `PlotModel` teria esta aparência:

```csharp
InteractiveAgent.RepresentationManager.AddProvider<PlotModel> (
  plotModel => Image (new SvgExporter {
      Width = 300,
      Height = 250
    }.ExportToString (plotModel)));
```

> [!NOTE]
> Essas APIs oferecem uma maneira rápida de começar a trabalhar, mas não recomendamos um inteiro de integração de envio somente de usá-los&mdash;eles fornecem muito pouco controle sobre como seus tipos são processados pelo cliente.

Com a representação de registrado, a integração está pronta para ser entregue!

## <a name="shipping-your-integration"></a>A integração de envio

Para enviar sua integração, você precisará adicioná-lo a um pacote do NuGet.
Ele pode ser enviada com o NuGet da biblioteca existente, ou se você estiver criando um novo pacote, você pode usar esse arquivo. NuSpec de modelo como um ponto de partida.
Você precisará preencher as seções relevantes ao seu processo de integração. A parte mais importante é que todos os arquivos para a integração devem estar em um `xamarin.interactive` diretório na raiz do pacote. Isso nos permite localizar facilmente todos os arquivos relevantes para sua integração, independentemente de você usa um pacote existente ou crie um novo.

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

Depois de criar o arquivo. NuSpec, você pode empacotar seu NuGet da seguinte forma:

```csharp
nuget pack MyIntegration.nuspec
```

e, em seguida, publicá-lo para [NuGet][nugetorg]. Quando estiver lá, você poderá referenciá-la em qualquer pasta de trabalho e vê-lo em ação. Captura de tela abaixo, nós reunimos a integração de exemplo que compilamos neste documento e instalado o pacote do NuGet em uma pasta de trabalho:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![Pasta de trabalho com a integração](images/mac-workbooks-integrated.png)](images/mac-workbooks-integrated.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Pasta de trabalho com a integração](images/windows-workbooks-integrated.png)](images/windows-workbooks-integrated.png#lightbox)

-----

Observe que você não verá qualquer `#r` diretivas ou qualquer coisa para inicializar a integração — pastas de trabalho cuidou de tudo isso para você em segundo plano!

## <a name="next-steps"></a>Próximas etapas

Fazer check-out de outras documentações para obter mais informações sobre as partes móveis que compõem o SDK, e nossos [integrações de exemplo](~/tools/workbooks/samples/index.md) para coisas adicionais, você pode fazer na sua integração, como fornecer JavaScript personalizado que é executado em o cliente de pastas de trabalho.

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
