---
title: Usar projetos compartilhados para compartilhar código
description: Projetos compartilhados permitem que você escreva código comum que é referenciado por vários projetos de aplicativo diferentes. O código é compilado como parte de cada projeto de referência e pode incluir diretivas de compilador para ajudar a incorporar a funcionalidade específica da plataforma à base de código compartilhada.
ms.prod: xamarin
ms.assetid: 191c71fb-44a4-4e6c-af4b-7b1107dce6af
author: davidortinau
ms.author: daortin
ms.date: 07/18/2018
ms.openlocfilehash: b8be06b9da4561ffc0d628b53f8daf639b5e3179
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84571085"
---
# <a name="shared-projects-code-sharing"></a>Compartilhamento de código de projetos compartilhados

_Projetos compartilhados permitem que você escreva código comum que é referenciado por vários projetos de aplicativo diferentes. O código é compilado como parte de cada projeto de referência e pode incluir diretivas de compilador para ajudar a incorporar a funcionalidade específica da plataforma à base de código compartilhada._

Projetos compartilhados (às vezes chamados de projetos de ativos compartilhados) permitem que você escreva o código que é compartilhado entre vários projetos de destino, incluindo aplicativos Xamarin.

Eles dão suporte a diretivas de compilador para que você possa incluir condicionalmente o código específico da plataforma a ser compilado em um subconjunto dos projetos que fazem referência ao projeto compartilhado. Também há suporte para IDE para ajudar a gerenciar as diretivas do compilador e visualizar como o código será examinado em cada aplicativo.

Se você usou a vinculação de arquivos no passado para compartilhar código entre projetos, projetos compartilhados funciona de forma semelhante, mas com suporte a IDE muito aprimorado.

## <a name="what-is-a-shared-project"></a>O que é um projeto compartilhado?

Ao contrário da maioria dos outros tipos de projeto, um projeto compartilhado não tem nenhuma saída (no formato de DLL), em vez disso, o código é compilado em cada projeto que faz referência a ele. Isso é ilustrado no diagrama abaixo – conceitualmente, todo o conteúdo do projeto compartilhado é "copiado para" cada projeto de referência e compilado como se fosse parte dele.

![](shared-projects-images/sharedassetproject.png "Shared Project architecture")

O código em um projeto compartilhado pode conter diretivas de compilador que habilitarão ou desabilitarão seções de código, dependendo de qual projeto de aplicativo está usando o código, que é sugerido pelas caixas de plataforma colorida no diagrama.

Um projeto compartilhado não é compilado por conta própria, ele existe puramente como um agrupamento de arquivos de código-fonte que podem ser incluídos em outros projetos. Quando referenciado por outro projeto, o código é efetivamente compilado como *parte* desse projeto. Projetos compartilhados não podem fazer referência a nenhum outro tipo de projeto (incluindo outros projetos compartilhados).

Observe que os projetos de aplicativo Android não podem fazer referência a outros projetos de aplicativo Android, por exemplo, um projeto de teste de unidade Android não pode fazer referência a um projeto de aplicativo Android. Para obter mais informações sobre essa limitação, consulte esta discussão sobre o [Fórum](https://forums.xamarin.com/discussion/comment/98092/).

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

## <a name="visual-studio-for-mac-walkthrough"></a>Passo a passo do Visual Studio para Mac

Esta seção explica como criar e usar um projeto compartilhado usando Visual Studio para Mac. Consulte a seção [exemplo de projeto compartilhado](#Shared_Project_Example) para obter um exemplo completo.

## <a name="creating-a-shared-project"></a>Criando um projeto compartilhado

Para criar um novo projeto compartilhado, navegue até **arquivo > nova solução...** (ou clique com o botão direito do mouse em uma solução existente e escolha **Adicionar > adicionar novo projeto...**):

[![Novo projeto compartilhado](shared-projects-images/xs-newsolution-sml.png "Nova solução")](shared-projects-images/xs-newsolution.png#lightbox)

Na próxima tela, escolha o nome do projeto e clique em **criar**.

Um novo projeto compartilhado é mostrado abaixo-Observe que não há referências ou nós de componentes; Eles não têm suporte para projetos compartilhados.

![Projeto compartilhado vazio](shared-projects-images/xs-empty.png "Projeto compartilhado vazio")

Para que um projeto compartilhado seja útil, ele precisa ser referenciado por pelo menos um projeto que possa ser compilado (como um aplicativo ou uma biblioteca do iOS ou Android, ou um projeto PCL). Um projeto compartilhado não é compilado quando não tem nada fazendo referência a ele; portanto, a sintaxe (ou qualquer outro) erros não será realçada até que tenha sido referenciada por outra coisa.

A adição de uma referência a um projeto compartilhado é feita da mesma forma que faz referência a um projeto de biblioteca regular. Esta captura de tela mostra um projeto Xamarin. iOS que faz referência a um projeto compartilhado.

![](shared-projects-images/xs-reference.png "Project reference to Shared Project")

Depois que o projeto compartilhado é referenciado por outra biblioteca ou aplicativo, você pode criar a solução e exibir todos os erros no código. Quando o projeto compartilhado é referenciado por _dois ou mais_ projetos, um menu é exibido no canto superior esquerdo do editor de código-fonte, que mostra a escolha de quais projetos fazem referência a esse arquivo.

## <a name="shared-project-options"></a>Opções de projeto compartilhado

Quando você clica com o botão direito do mouse em um projeto compartilhado e escolhe **Opções** , há menos configurações do que outros tipos de projeto. Como os projetos compartilhados não são compilados (por conta própria), você não pode definir opções de saída ou de compilador, configurações de projeto, assinatura de assembly ou comandos personalizados. O código em um projeto compartilhado efetivamente herda esses valores de qualquer um que faça referência a eles.

A tela de **Opções** é mostrada abaixo-o **nome** do projeto e o **namespace padrão** são as duas únicas configurações que geralmente serão alteradas.

![](shared-projects-images/xs-sharedprojectoptions.png "Shared Project Options")

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

## <a name="visual-studio-walkthrough"></a>Passo a passo do Visual Studio

Esta seção explica como criar e usar um projeto compartilhado usando o Visual Studio. Consulte a seção [exemplo de projeto compartilhado](#Shared_Project_Example) para obter uma implementação completa.

### <a name="creating-a-shared-project"></a>Criando um projeto compartilhado

Para criar um novo projeto compartilhado, navegue até **arquivo**  >  **novo**  >  **projeto**.

No Visual Studio 2019, insira **compartilhado** na caixa de pesquisa na página **criar um novo projeto** . Selecione o modelo de **projeto compartilhado** e, em seguida, selecione **Avançar**. Insira um nome para o projeto e, em seguida, selecione **criar**.

No Visual Studio 2017, selecione o modelo de **projeto compartilhado** e, em seguida, escolha um nome para o projeto.

![Modelo de projeto compartilhado no Visual Studio 2017](shared-projects-images/vs-newsolution.png)

Você também pode adicionar um novo projeto compartilhado a uma solução existente clicando com o botão direito do mouse no arquivo da solução e escolhendo **adicionar > novo projeto**. Um novo projeto compartilhado é semelhante ao mostrado abaixo (depois que um arquivo de classe tiver sido adicionado). Observe que não há referências ou nós de componentes; Eles não têm suporte para projetos compartilhados.

![](shared-projects-images/vs-empty.png "Empty Shared Project")

Para que um projeto compartilhado seja útil, ele precisa ser referenciado por pelo menos um projeto que possa ser compilado (como um aplicativo ou uma biblioteca do iOS ou Android, ou um projeto PCL). Um projeto compartilhado não é compilado quando não tem nada fazendo referência a ele; portanto, a sintaxe (ou qualquer outro) erros não será realçada até que tenha sido referenciada por outra coisa.

A adição de uma referência a um projeto compartilhado é feita da mesma forma que faz referência a um projeto de biblioteca regular. Esta captura de tela mostra um projeto Xamarin. iOS que faz referência a um projeto compartilhado.

![](shared-projects-images/vs-reference.png "Project reference to Shared Project")

Depois que o projeto compartilhado é referenciado por outra biblioteca ou aplicativo, você pode criar a solução e exibir todos os erros no código. Quando o projeto compartilhado é referenciado por _dois ou mais_ projetos, um menu é exibido no canto superior esquerdo do editor de código-fonte para ver quais projetos fazem referência ao arquivo de código atual.

### <a name="shared-project-properties"></a>Propriedades do projeto compartilhado

Quando você seleciona um projeto compartilhado, há menos configurações no painel de propriedades do que outros tipos de projeto. Como os projetos compartilhados não são compilados (por conta própria), você não pode definir opções de saída ou de compilador, configurações de projeto, assinatura de assembly ou comandos personalizados. O código em um projeto compartilhado efetivamente herda esses valores de qualquer um que faça referência a eles.

O painel **Propriedades** é mostrado abaixo-o **namespace raiz** é a única configuração que você pode alterar.

![](shared-projects-images/vs-sharedprojectproperties.png "Shared Project Properties")

-----

<a name="Shared_Project_Example"></a>

## <a name="shared-project-example"></a>Exemplo de projeto compartilhado

O exemplo de [tarefa](https://github.com/xamarin/mobile-samples/tree/master/Tasky) usa um projeto compartilhado para conter o código comum usado pelos aplicativos Ios, Android e Windows Phone. Os `SQLite.cs` arquivos de `TaskRepository.cs` código-fonte e as diretivas de compilador Utilise (por exemplo, `#if __ANDROID__`) para produzir uma saída diferente para cada um dos aplicativos que fazem referência a eles.

A estrutura de solução completa é mostrada abaixo (no Visual Studio para Mac e no Visual Studio, respectivamente):

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

![](shared-projects-images/xs-examplesolution.png "Visual Studio for Mac solution")

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![](shared-projects-images/vs-examplesolution.png "Visual Studio solution")

-----

O projeto Windows Phone pode ser navegado em Visual Studio para Mac, mesmo que esse tipo de projeto não tenha suporte para compilação no Visual Studio para Mac.

Os aplicativos em execução são mostrados abaixo:

![](shared-projects-images/example.png "iOS, Android, Windows Phone examples")

## <a name="summary"></a>Resumo

Este documento descreveu como funcionam os projetos compartilhados, como eles podem ser criados e usados tanto no Visual Studio para Mac quanto no Visual Studio, e introduzimos um aplicativo de exemplo simples que demonstra um projeto compartilhado em ação.

## <a name="related-links"></a>Links relacionados

- [Aplicativo de exemplo de tarefa](https://github.com/xamarin/mobile-samples/tree/master/Tasky)
- [Bibliotecas de classes portáteis (exemplo)](~/cross-platform/app-fundamentals/pcl.md)
- [Opções de código de compartilhamento (exemplo)](~/cross-platform/app-fundamentals/code-sharing.md)
