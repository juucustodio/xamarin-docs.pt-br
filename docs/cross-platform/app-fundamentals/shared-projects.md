---
title: Usar compartilhada projetos para compartilhar o código
description: Projetos compartilhados permitem que você escreva código comum que é referenciado por um número de projetos de aplicativo diferente. O código é compilado como parte de cada projeto de referência e pode incluir diretivas de compilador para ajudar a incorporar funcionalidade específica da plataforma para a base de código compartilhado.
ms.prod: xamarin
ms.assetid: 191c71fb-44a4-4e6c-af4b-7b1107dce6af
author: conceptdev
ms.author: crdun
ms.date: 07/18/2018
ms.openlocfilehash: 61096635cd94d0fdd0abe6fda59c4efa41eeceb1
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61192784"
---
# <a name="shared-projects-code-sharing"></a>Compartilhamento de código de projetos de compartilhado

_Projetos compartilhados permitem que você escreva código comum que é referenciado por um número de projetos de aplicativo diferente. O código é compilado como parte de cada projeto de referência e pode incluir diretivas de compilador para ajudar a incorporar funcionalidade específica da plataforma para a base de código compartilhado._

Projetos compartilhados (também chamados de projetos de ativos compartilhados) permitem que você escreva código que é compartilhado entre vários projetos de destino, incluindo aplicativos Xamarin.

Eles oferecem suporte a diretivas de compilador para que você possa incluir condicionalmente o código específico da plataforma para ser compilado em um subconjunto dos projetos que fazem referência a projeto compartilhado. Também há suporte IDE para ajudar a gerenciar as diretivas de compilador e visualizar a aparência do código em cada aplicativo.

Se você tiver usado a vinculação de arquivos no passado para compartilhar código entre projetos, projetos compartilhados funciona de maneira semelhante, mas com suporte mais aprimorado do IDE.

## <a name="what-is-a-shared-project"></a>O que é um projeto compartilhado?

Ao contrário da maioria dos outros tipos de projeto um projeto compartilhado não tem nenhuma saída (na forma DLL), em vez disso, o código é compilado em cada projeto que faz referência a ele. Isso é ilustrado no diagrama a seguir, é conceitualmente de todo o conteúdo do projeto compartilhado "copiado para" cada projeto de referência e compilados como se ele fosse uma parte deles.

![](shared-projects-images/sharedassetproject.png "Arquitetura de projeto compartilhada")

O código em um projeto compartilhado pode conter as diretivas de compilador que habilita ou desabilita as seções de código, dependendo de qual aplicativo o projeto está usando o código, que é sugerido pelas caixas coloridas plataforma no diagrama.

Um projeto compartilhado não obter compilado por conta própria, ele existe apenas como um agrupamento de arquivos de código fonte que pode ser incluído em outros projetos. Quando referenciado por outro projeto, o código é efetivamente compilado como *parte* desse projeto. Projetos compartilhados não podem fazer referência a qualquer outro tipo de projeto (incluindo outros projetos compartilhados).

Observe que os projetos de aplicativo do Android não podem referenciar outros projetos de aplicativo do Android - por exemplo, um projeto de teste de unidade para Android não pode fazer referência a um projeto de aplicativo do Android. Para obter mais informações sobre essa limitação, consulte este [Fórum de discussão](http://forums.xamarin.com/discussion/comment/98092/).

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

## <a name="visual-studio-for-mac-walkthrough"></a>Passo a passo do Visual Studio para Mac

Esta seção explica como criar e usar um projeto compartilhado usando o Visual Studio para Mac. Consulte o para [exemplo de projeto compartilhado](#Shared_Project_Example) seção para obter um exemplo completo.

## <a name="creating-a-shared-project"></a>Criando um projeto compartilhado

Para criar um novo projeto compartilhado, navegue até **arquivo > nova solução...**  (ou uma solução existente de clique do botão direito e escolhendo **Adicionar > Adicionar novo projeto...** ):

[![Novo projeto compartilhado](shared-projects-images/xs-newsolution-sml.png "nova solução")](shared-projects-images/xs-newsolution.png#lightbox)

Na próxima tela, escolha o nome do projeto e clique em **criar**.

Um novo projeto compartilhado é mostrado abaixo, observe que há não são nenhuma referência ou nós de componente; elas não têm suporte para projetos compartilhados.

![Vazio de projeto compartilhados](shared-projects-images/xs-empty.png "vazio de projeto compartilhados")

Para um projeto compartilhado ser útil, ele deve ser referenciado pelo menos um projeto capaz de compilação (por exemplo, um iOS ou Android aplicativo ou biblioteca ou um projeto PCL). Um projeto compartilhado não é compilado ao não tem nada a fazer referência a ele, portanto, sintaxe (ou qualquer outro) erros não serão realçados até que ele foi referenciado por algo.

Adicionando uma referência a um projeto compartilhado é feito da mesma forma que a referência de um projeto de biblioteca regular. Esta captura de tela mostra um projeto xamarin. IOS fazendo referência a um projeto compartilhado.

![](shared-projects-images/xs-reference.png "Referência de projeto a projeto compartilhado")

Depois que o projeto compartilhado é referenciado por outra biblioteca ou aplicativo, você pode compilar a solução e exibir quaisquer erros no código. Quando o projeto compartilhado é referenciado pela _dois ou mais_ outros projetos, é exibido um menu no canto superior esquerdo do editor de código fonte que mostra escolhe quais projetos fazem referência a esse arquivo.

## <a name="shared-project-options"></a>Opções de projeto de compartilhado

Quando você clique duas vezes em um projeto compartilhado e escolha **opções** há menos configurações do que outros tipos de projeto. Como projetos compartilhados não são compilados (por conta própria), é possível definir opções de compilador ou de saída, as configurações de projeto, assinatura de assembly ou comandos personalizados. O código em um projeto compartilhado efetivamente herda esses valores de tudo o que está fazendo referência a eles.



O **opções** tela é mostrada abaixo - projeto **nome** e o **Default Namespace** são as apenas duas configurações que você geralmente será alterado.


![](shared-projects-images/xs-sharedprojectoptions.png "Opções de projeto de compartilhado")



# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)



## <a name="visual-studio-walkthrough"></a>Passo a passo do Visual Studio


Esta seção explica como criar e usar um projeto compartilhado usando o Visual Studio. Consulte o para [exemplo de projeto compartilhado](#Shared_Project_Example) seção para uma implementação completa.

### <a name="creating-a-shared-project"></a>Criando um projeto compartilhado

Para criar um novo projeto compartilhado, navegue até **arquivo > nova solução...**  e escolha um nome para o projeto e solução.

![](shared-projects-images/vs-newsolution.png "Nova Solução")

Você também pode adicionar um novo projeto compartilhado para uma solução clicando duas vezes no arquivo de solução e escolha **Adicionar > Novo projeto...** . Um novo projeto compartilhado será como mostrado abaixo (depois da adição de um arquivo de classe) – Observe que nenhuma referência ou nós de componente; elas não têm suporte para projetos compartilhados.

![](shared-projects-images/vs-empty.png "Projeto vazio de compartilhado")

Para um projeto compartilhado ser útil, ele deve ser referenciado pelo menos um projeto capaz de compilação (por exemplo, um iOS ou Android aplicativo ou biblioteca ou um projeto PCL). Um projeto compartilhado não é compilado ao não tem nada a fazer referência a ele, portanto, sintaxe (ou qualquer outro) erros não serão realçados até que ele foi referenciado por algo.

Adicionando uma referência a um projeto compartilhado é feito da mesma forma que a referência de um projeto de biblioteca regular. Esta captura de tela mostra um projeto xamarin. IOS fazendo referência a um projeto compartilhado.

![](shared-projects-images/vs-reference.png "Referência de projeto a projeto compartilhado")

Depois que o projeto compartilhado é referenciado por outra biblioteca ou aplicativo, você pode compilar a solução e exibir quaisquer erros no código. Quando o projeto compartilhado é referenciado pela _dois ou mais_ outros projetos, é exibido um menu no canto superior esquerdo do editor de código fonte para ver quais projetos de referenciar o arquivo de código atual.


### <a name="shared-project-properties"></a>Propriedades de projeto compartilhado


Quando você seleciona um projeto compartilhado há menos configurações no painel de propriedades que outros tipos de projeto. Como projetos compartilhados não são compilados (por conta própria), é possível definir opções de compilador ou de saída, as configurações de projeto, assinatura de assembly ou comandos personalizados. O código em um projeto compartilhado efetivamente herda esses valores de tudo o que está fazendo referência a eles.

O **propriedades** painel é mostrada abaixo - os **Namespace raiz** é a única configuração que podem ser alteradas.

![](shared-projects-images/vs-sharedprojectproperties.png "Propriedades de projeto compartilhado")

-----

<a name="Shared_Project_Example"/>

## <a name="shared-project-example"></a>Exemplo de projeto compartilhado

O [Tasky](https://github.com/xamarin/mobile-samples/tree/master/Tasky) usa um projeto compartilhado para conter o código comum usado pelos tanto o iOS, Android e Windows Phone, aplicativos de exemplo. Tanto a `SQLite.cs` e `TaskRepository.cs` utilise de arquivos de código-fonte (por exemplo, as diretivas de compilador `#if __ANDROID__`) para produzir uma saída diferente para cada um dos aplicativos que fazem referência a eles.

A estrutura de solução completa é mostrada abaixo (no Visual Studio para Mac e Visual Studio, respectivamente):

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![](shared-projects-images/xs-examplesolution.png "O Visual Studio para solução de Mac")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![](shared-projects-images/vs-examplesolution.png "Solução do Visual Studio")

-----

O projeto do Windows Phone pode ser acessado de dentro do Visual Studio para Mac, mesmo que não há suporte para esse tipo de projeto para compilação no Visual Studio para Mac.

Os aplicativos em execução são mostrados abaixo:

![](shared-projects-images/example.png "exemplos do iOS, Android, Windows Phone")

## <a name="summary"></a>Resumo

Este documento é descrito como projetos compartilhados funcionam, como eles podem ser criados e usados no Visual Studio para Mac e Visual Studio e introduziu um aplicativo de exemplo simples que demonstra um projeto compartilhado em ação.

## <a name="related-links"></a>Links relacionados

- [Aplicativo de exemplo tasky](https://github.com/xamarin/mobile-samples/tree/master/Tasky)
- [Bibliotecas de classes portáteis (amostra)](~/cross-platform/app-fundamentals/pcl.md)
- [Opções de código (exemplo) de compartilhamento](~/cross-platform/app-fundamentals/code-sharing.md)
