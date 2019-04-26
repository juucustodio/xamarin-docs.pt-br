---
title: DevOps com Xamarin
ms.assetid: ff978cc2-5a25-46d6-921b-e51adaa65992
author: conceptdev
ms.author: crdun
manager: crdun
ms.workload:
- xamarin
ms.date: 10/23/2018
ms.openlocfilehash: de7cb0d3cce97f251fe6d9625fb1373e6aac7a67
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61293681"
---
# <a name="devops-with-xamarin"></a>DevOps com Xamarin

O Xamarin permite criar aplicativos móveis de plataforma cruzada direcionados para Android, iOS e Windows usando C#, .NET e Visual Studio. O Xamarin permite que uma grande parte do código seja compartilhada entre plataformas, com apenas um pequeno percentual precisando ser específico da plataforma.

Desenvolver aplicativos para plataformas modernas envolve muito mais atividades do que apenas escrever código. Essas atividades, conhecidas como DevOps (desenvolvimento + operações), abrangem o ciclo de vida completo do aplicativo e incluem trabalhos de planejamento e acompanhando, elaboração e implementação de código, gerenciamento de um repositório de código-fonte, execução de builds, gerenciamento de integrações e implantações contínuas, testes (incluindo testes de unidade e testes de IU), execução de várias formas de diagnóstico em ambientes de desenvolvimento e produção e monitoramento de desempenho do aplicativo e dos comportamentos do usuário em tempo real por meio de telemetria e análise.

O Visual Studio, com o Azure DevOps Services e o Team Foundation Server, oferece uma variedade de funcionalidades de DevOps. Muitos deles são totalmente aplicáveis a projetos de plataforma cruzada. Isso vale principalmente para os aplicativos Xamarin, porque eles são criados com C# e .NET, que são usados para a criação de algumas ferramentas de DevOps. Outras ferramentas exigem uma integração forte com os ambientes de build e de tempo de execução. Uma vez que os aplicativos Xamarin são executados em plataformas não Windows e usam a implementação Mono do .NET, o Xamarin fornece ferramentas especializadas para determinadas necessidades.

As tabelas a seguir identificam quais recursos de DevOps no Visual Studio devem funcionar bem com um projeto do Xamarin e quais têm limitações. Consulte a documentação vinculada para obter detalhes sobre os recursos em si.

## <a name="agile-tools"></a>Ferramentas agile

Link de referência: **[Sobre as ferramentas do Agile e gerenciamento de projeto Agile](/azure/devops/boards/backlogs/overview?view=vsts)**

Comentário Geral: todos os recursos de planejamento e acompanhamento são independentes do tipo de projeto e de linguagens de codificação.

|Recurso|Tem suporte com o Xamarin|Comentários Adicionais|
|-------------|----------------------------|-------------------------|
|Gerenciar listas de pendências e sprints|Sim||
|Acompanhamento de trabalho|Sim||
|Colaboração da sala da equipe|Sim||
|Quadros kanban|Sim||
|Relatar e visualizar o progresso|Sim||

## <a name="modeling"></a>Modelagem

Link de referência: **[Analisar e modelar a arquitetura](/visualstudio/modeling/analyze-and-model-your-architecture)**

Recursos de design são independentes da linguagem de codificação ou funcionam com linguagens .NET como C#. Consulte [Funções de arquitetura e diagramas de modelagem no desenvolvimento de software](/visualstudio/modeling/scenario-change-your-design-using-visualization-and-modeling#ModelingDiagramsTools) para aspectos relacionados a código.

|Recurso|Tem suporte com o Xamarin|Comentários Adicionais|
|-------------|----------------------------|-------------------------|
|Diagramas de sequência|Sim||
|Grafos de dependência|Sim||
|Hierarquia de chamadas|Sim||
|Designer de Classe|Sim||
|Gerenciador de arquitetura|Sim||
|Diagramas UML (caso de uso, atividade, classe, componente, sequência e DSL)|Sim||
|Diagramas de camada|Sim||
|Validação da camada|Sim||

## <a name="code"></a>Código

|Recurso|Tem suporte com o Xamarin|Comentários Adicionais|
|-------------|----------------------------|-------------------------|
|[Usar o TFVC (Controle de Versão do Team Foundation)](/azure/devops/repos/tfvc/overview?view=vsts) ou o Azure Repos|Sim||
|[Introdução ao GIT no Azure Repos](/azure/devops/repos/git/gitquickstart?view=vsts&tabs=visual-studio)|Sim||
|[Melhorar a qualidade do código](/visualstudio/test/improve-code-quality)|Sim||
|[Localizar alterações de código e outros históricos](/visualstudio/ide/find-code-changes-and-other-history-with-codelens)|Sim|Exceto entre os limites específicos da plataforma em que a implementação não é resolvida até o tempo de execução.|
|[Usar mapas de códigos para depurar aplicativos](/visualstudio/modeling/use-code-maps-to-debug-your-applications)|Sim||

## <a name="build"></a>Build

Link de referência: **[Azure Pipelines](/azure/devops/pipelines/index?view=vsts)**

|Recurso|Tem suporte com o Xamarin|Comentários Adicionais|
|-------------|----------------------------|-------------------------|
|Servidor TFS local|Sim|Computadores de build devem ter Xamarin instalado e podem ser vinculados a um computador OSX para compilar para iOS. Confira [Usar TFVC](/azure/devops/repos/tfvc/overview?view=vsts)|
|Servidor de build local vinculado ao Azure Pipelines|Sim|Consulte [Build and release agents](/azure/devops/pipelines/agents/agents?view=vsts) (Agentes de build e de versão) para obter instruções.|
|Serviço de controlador hospedado do Azure Pipelines|Sim|Consulte [Compilar seu aplicativo Xamarin](/azure/devops/pipelines/languages/xamarin?view=vsts&tabs=vsts).|
|Compilar definições com pré e pós-scripts|Sim||
|Integração contínua incluindo check-ins restritos|Sim|Check-ins restritos somente para TFVC, uma vez que Git funciona em um modelo de solicitação pull, em vez de check-ins.|

## <a name="test"></a>Teste

|Recurso|Tem suporte com o Xamarin|Comentários Adicionais|
|-------------|----------------------------|-------------------------|
|Planejando testes, criando casos de teste e organizando conjuntos de testes|Sim||
|Teste manual|Sim||
|Gerenciador de Teste (testes de gravação e reprodução)|Sim|Somente dispositivos Windows e emuladores Android do Visual Studio.|
|Cobertura de código|N/D||
|[Efetuar teste de unidade em seu código](/visualstudio/test/unit-test-your-code/)|Sim|Para os destinos Android e Windows, as ferramentas internas do MSTest podem ser usadas. Para executar testes de unidade em Windows, Android e iOS, o Xamarin recomenda NUnit. Confira [Usar o TFVC](/azure/devops/repos/tfvc/overview?view=vsts).|
|[Usar a automação de interface do usuário para testar seu código](/visualstudio/test/use-ui-automation-to-test-your-code/)|Somente Windows|O gravador de teste da interface do usuário do Visual Studio é somente Windows. Para todas as plataformas, confira [Xamarin.UITest](/appcenter/test-cloud/uitest/).|

## <a name="improve-code-quality"></a>Melhorar a qualidade do código

Link de referência: **[Melhorar a qualidade do código](/visualstudio/test/improve-code-quality)**

|Recurso|Tem suporte com o Xamarin|Comentários Adicionais|
|-------------|----------------------------|-------------------------|
|[Analisar a qualidade do código gerenciado](/visualstudio/code-quality/analyzing-managed-code-quality-by-using-code-analysis)|Sim||
|[Localizar código duplicado usando detecção de clone de código](https://msdn.microsoft.com/library/hh205279.aspx)|Sim||
|[Medir complexidade e facilidade de manutenção do código gerenciado](/visualstudio/code-quality/measuring-complexity-and-maintainability-of-managed-code)|Sim||
|[Gerenciador de Desempenho](/visualstudio/profiling/performance-explorer)|Não|Use o [Xamarin Profiler](/xamarin/tools/profiler/) por meio do Visual Studio para Mac em vez disso. Observe que o Xamarin Profiler está atualmente em visualização e ainda não funciona para destinos do Windows.|
|[Analisar problemas de memória do .NET Framework](https://msdn.microsoft.com/library/dn342825.aspx)|Não|Ferramentas do Visual Studio não têm ganchos na estrutura Mono para a criação de perfil.|

## <a name="release-management"></a>Gerenciamento de liberações

Link de referência: **[Compilar e liberar em Pipelines do Azure e o TFS](/azure/devops/pipelines/overview?view=vsts)**

|Recurso|Tem suporte com o Xamarin|Comentários Adicionais|
|-------------|----------------------------|-------------------------|
|Gerenciar processos de versão|Sim||
|Implantação para servidores para carregamento lateral por meio de scripts|Sim||
|Carregar para a loja de aplicativos|Parcial|Estão disponíveis extensões que podem automatizar esse processo para algumas lojas de aplicativos.  Consulte [Extensões para o Azure DevOps Services](https://marketplace.visualstudio.com/VSTS); por exemplo, a [extensão para Google Play](https://marketplace.visualstudio.com/items?itemName=ms-vsclient.google-play).|

## <a name="monitor-with-hockeyapp"></a>Monitorar com HockeyApp

Link de referência: **[Monitoramento com o HockeyApp](https://www.hockeyapp.net/features/)**

|Recurso|Tem suporte com o Xamarin|Comentários Adicionais|
|-------------|----------------------------|-------------------------|
|Análise de falhas, telemetria e distribuição beta|Sim||
