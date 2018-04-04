---
title: Projetos compartilhados
description: Projetos compartilhados permitem que você escreva código comum que é referenciado por um número de projetos de aplicativo diferente. O código é compilado como parte de cada projeto de referência e pode incluir diretivas de compilador para ajudar a incorporar a funcionalidade específica de plataforma para a base de código compartilhado.
ms.prod: xamarin
ms.assetid: 191c71fb-44a4-4e6c-af4b-7b1107dce6af
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: a7d5a25c3c6f2889bde0ff4aeaf85053bcc796fb
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="shared-projects"></a>Projetos compartilhados

_Projetos compartilhados permitem que você escreva código comum que é referenciado por um número de projetos de aplicativo diferente. O código é compilado como parte de cada projeto de referência e pode incluir diretivas de compilador para ajudar a incorporar a funcionalidade específica de plataforma para a base de código compartilhado._

Projetos compartilhados (também chamados de projetos ativos compartilhados) permitem a você escrever código que é compartilhado entre vários projetos de destino, incluindo aplicativos Xamarin.

Oferece suporte a diretivas de compilador para que você pode incluir condicionalmente código específico da plataforma para ser compilada em um subconjunto dos projetos que fazem referência do projeto compartilhado. Também há suporte IDE para ajudar a gerenciar as diretivas de compilador e visualizar a aparência do código em cada aplicativo.

Se você tiver usado a vinculação de arquivo no passado para compartilhar código entre projetos, projetos compartilhados funciona de maneira semelhante, mas com suporte IDE muito melhorado.



## <a name="what-is-a-shared-project"></a>O que é um projeto compartilhado?

Ao contrário da maioria dos outros tipos de projeto um projeto compartilhado não tem nenhuma saída (na forma DLL), em vez disso, o código é compilado em cada projeto que faz referência a ele. Isso é ilustrado no diagrama a seguir - conceitualmente todo o conteúdo do projeto compartilhado é "copiado em" cada projeto de referência e compilados como se fosse uma parte deles.

 ![](shared-projects-images/sharedassetproject.png "Arquitetura de projeto compartilhada")

O código em um projeto compartilhado pode conter diretivas de compilador que habilita ou desabilita a seções de códigos, dependendo de qual aplicativo o projeto está usando o código, que é sugerido pelas caixas coloridas plataforma no diagrama.

Um projeto compartilhado não obter compilado por conta própria, ele existe apenas como um grupo de arquivos de código fonte que pode ser incluído em outros projetos. Quando referenciada por outro projeto, o código é compilado efetivamente como *parte* desse projeto. Projetos compartilhados não podem fazer referência a qualquer outro tipo de projeto (incluindo outros projetos compartilhados).

Observe que os projetos de aplicativo do Android não podem fazer referência a outros projetos de aplicativo do Android – por exemplo, um projeto de teste de unidade Android não pode fazer referência a um projeto de aplicativo do Android. Para obter mais informações sobre essa limitação, consulte [discussão de fórum](http://forums.xamarin.com/discussion/comment/98092/).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)



## <a name="visual-studio-for-mac-walkthrough"></a>Passo a passo do Visual Studio para Mac


Esta seção explica como criar e usar um projeto compartilhado usando o Visual Studio para Mac. Consulte o para [exemplo de projeto compartilhado](#Shared_Project_Example) seção para obter um exemplo completo.


## <a name="creating-a-shared-project"></a>Criando um projeto compartilhado


Para criar um novo projeto compartilhado navegue até **arquivo > nova solução...**  e escolha um nome.


![](shared-projects-images/xs-newsolution.png "Nova Solução")


Você também pode adicionar um novo projeto compartilhado para uma solução clicando duas vezes no arquivo de solução e escolha **Adicionar > Adicionar novo projeto...** . Uma nova aparência do projeto compartilhado conforme mostrado abaixo - Observe existem referências ou nós de componente. eles não têm suporte para projetos compartilhados.


![](shared-projects-images/xs-empty.png "Projeto compartilhado vazio")


Para um projeto compartilhado ser útil, ele precisa ser referenciada pelo menos um projeto capaz de compilação (por exemplo, um iOS ou aplicativo do Android ou biblioteca ou um projeto PCL). Um projeto compartilhado não obter compilado quando não tem nenhuma referência a ele, sintaxe caso (ou qualquer outro) erros não serão realçados até que ele foi referenciado por algo.



Adicionar uma referência a um projeto compartilhado é feito da mesma forma que a referência a um projeto de biblioteca comum. Esta captura de tela mostra um projeto xamarin fazendo referência a um projeto compartilhado.


![](shared-projects-images/xs-reference.png "Referência ao projeto compartilhado")


Depois que o projeto compartilhado é referenciado por outro aplicativo ou biblioteca, você pode compilar a solução e exibir quaisquer erros no código. Quando o projeto compartilhado é referenciado por _dois ou mais_ outros projetos, é exibido um menu no canto superior esquerdo do editor de código fonte que mostra escolha quais projetos referenciam este arquivo.



## <a name="shared-project-options"></a>Opções de projeto de compartilhado


Quando você com o botão direito em um projeto compartilhado e escolha **opções** há menos configurações do que outros tipos de projeto. Como projetos compartilhados não são compilados (por conta própria), você não pode definir opções de saída ou do compilador, configurações de projeto, assinatura de assembly ou comandos personalizados. O código em um projeto compartilhado efetivamente herda esses valores de tudo o que está fazendo referência a eles.



O **opções** tela é mostrada abaixo - projeto **nome** e **Namespace padrão** são as configurações apenas dois geralmente será alterada.


![](shared-projects-images/xs-sharedprojectoptions.png "Opções de projeto de compartilhado")



# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)



## <a name="visual-studio-walkthrough"></a>Passo a passo do Visual Studio


Esta seção explica como criar e usar um projeto compartilhado usando o Visual Studio. Consulte o para [exemplo de projeto compartilhado](#Shared_Project_Example) seção para uma implementação completa.


### <a name="creating-a-shared-project"></a>Criando um projeto compartilhado


Para criar um novo projeto compartilhado navegue até **arquivo > nova solução...**  e escolha um nome para o projeto e solução.


![](shared-projects-images/vs-newsolution.png "Nova Solução")


Você também pode adicionar um novo projeto compartilhado para uma solução clicando duas vezes no arquivo de solução e escolha **Adicionar > Novo projeto...** . Um novo projeto compartilhado parece conforme mostrado abaixo (depois da adição de um arquivo de classe) - Observe que existem referências ou nós de componente. eles não têm suporte para projetos compartilhados.


![](shared-projects-images/vs-empty.png "Projeto compartilhado vazio")


Para um projeto compartilhado ser útil, ele precisa ser referenciada pelo menos um projeto capaz de compilação (por exemplo, um iOS ou aplicativo do Android ou biblioteca ou um projeto PCL). Um projeto compartilhado não obter compilado quando não tem nenhuma referência a ele, sintaxe caso (ou qualquer outro) erros não serão realçados até que ele foi referenciado por algo.



Adicionar uma referência a um projeto compartilhado é feito da mesma forma que a referência a um projeto de biblioteca comum. Esta captura de tela mostra um projeto xamarin fazendo referência a um projeto compartilhado.


![](shared-projects-images/vs-reference.png "Referência ao projeto compartilhado")


Depois que o projeto compartilhado é referenciado por outro aplicativo ou biblioteca, você pode compilar a solução e exibir quaisquer erros no código. Quando o projeto compartilhado é referenciado por _dois ou mais_ outros projetos, é exibido um menu no canto superior esquerdo do editor de código fonte para ver quais projetos referenciam o arquivo de código atual.


### <a name="shared-project-properties"></a>Propriedades de projeto compartilhado


Quando você seleciona um projeto compartilhado há menos configurações no painel Propriedades que outros tipos de projeto. Como projetos compartilhados não são compilados (por conta própria), você não pode definir opções de saída ou do compilador, configurações de projeto, assinatura de assembly ou comandos personalizados. O código em um projeto compartilhado efetivamente herda esses valores de tudo o que está fazendo referência a eles.



O **propriedades** painel é mostrado abaixo - o **Namespace raiz** é a única configuração que você pode alterar.


![](shared-projects-images/vs-sharedprojectproperties.png "Propriedades de projeto compartilhado")



-----

<a name="Shared_Project_Example"/>

## <a name="shared-project-example"></a>Exemplo de projeto compartilhado

O [Tasky](https://github.com/xamarin/mobile-samples/tree/master/Tasky) usa um projeto compartilhado para conter o código comum usado pelo iOS, Android e Windows Phone, aplicativos de exemplo. Tanto o `SQLite.cs` e `TaskRepository.cs` arquivos de código fonte utilise diretivas de compilador (por exemplo. `#if __ANDROID__`) para produzir uma saída diferente para cada um dos aplicativos que fazem referência a eles.

A estrutura de solução completa é mostrada abaixo (no Visual Studio para Mac e Visual Studio respectivamente):

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

 ![](shared-projects-images/xs-examplesolution.png "O Visual Studio para solução de Mac")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

 ![](shared-projects-images/vs-examplesolution.png "Solução do Visual Studio")

-----

O projeto Windows Phone pode ser navegado a partir do Visual Studio para Mac, mesmo que não há suporte para esse tipo de projeto de compilação no Visual Studio para Mac.

Os aplicativos em execução são mostrados abaixo.

 ![](shared-projects-images/example.png "exemplos do iOS, Android, Windows Phone")



## <a name="summary"></a>Resumo

Este documento é descrito como projetos compartilhados funcionam, como eles podem ser criados e usados no Visual Studio para Mac e o Visual Studio e introduziu um aplicativo de exemplo simples que demonstra um projeto compartilhado em ação.

## <a name="related-links"></a>Links relacionados

- [Aplicativo de exemplo tasky](https://github.com/xamarin/mobile-samples/tree/master/Tasky)
- [Bibliotecas de classes portáteis (exemplo)](~/cross-platform/app-fundamentals/pcl.md)
- [Compartilhamento de opções de código (exemplo)](~/cross-platform/app-fundamentals/code-sharing.md)
