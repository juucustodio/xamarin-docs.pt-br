---
title: Parte 3 - configuração de uma solução de plataforma cruzada do Xamarin
description: Este documento descreve como configurar uma solução de plataforma cruzada no Xamarin. Ele aborda várias estratégias, como projetos compartilhados e .NET Standard de compartilhamento de código.
ms.prod: xamarin
ms.assetid: 4139A6C2-D477-C563-C1AB-98CCD0D10A93
author: asb3993
ms.author: amburns
ms.date: 03/27/2017
ms.openlocfilehash: 2117eb91f25f2fb890b419fa7c4235b8f646729d
ms.sourcegitcommit: 58d8bbc19ead3eb535fb8248710d93ba0892e05d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67675020"
---
# <a name="part-3---setting-up-a-xamarin-cross-platform-solution"></a>Parte 3 - configuração de uma solução de plataforma cruzada do Xamarin

Independentemente de quais plataformas estão sendo usadas, todos os projetos do Xamarin usam o mesmo formato de arquivo de solução (o Visual Studio **. sln** formato de arquivo). As soluções podem ser compartilhadas entre ambientes de desenvolvimento, mesmo quando projetos individuais não podem ser carregados (por exemplo, um projeto do Windows no Visual Studio para Mac).



Ao criar um novo aplicativo de plataforma cruzada, a primeira etapa é criar uma solução em branco. Esta seção explica o que acontece em seguida: como configurar projetos para compilar aplicativos móveis de plataforma cruzada.

 <a name="Sharing_Code" />


## <a name="sharing-code"></a>Compartilhando código

Consulte a [opções de compartilhamento de código](~/cross-platform/app-fundamentals/code-sharing.md) documento para obter uma descrição detalhada de como implementar o compartilhamento de código entre plataformas.

 <a name="Shared_Asset_Projects" />


### <a name="shared-projects"></a>Projetos compartilhados

A abordagem mais simples ao compartilhamento de arquivos de código está usando um [projeto compartilhado](~/cross-platform/app-fundamentals/shared-projects.md).

Esse método permite que você compartilhar o mesmo código em projetos de plataforma diferente e usar diretivas de compilador para incluir caminhos de código diferente, específica de plataforma.

 <a name="Portable_Class_Libraries" />


### <a name="portable-class-libraries-pcl"></a>PCL (Bibliotecas de classe portáteis)

Historicamente um arquivo de projeto do .NET (e o assembly resultante) tem sido direcionados a uma versão específica do framework. Isso impede que o projeto ou assembly que está sendo compartilhado por estruturas diferentes.

Uma biblioteca de classe portátil (PCL) é um tipo especial de projeto que pode ser usado por plataformas heterogêneas a CLI, como xamarin. IOS e xamarin. Android, bem como WPF, plataforma Universal do Windows e Xbox. A biblioteca só pode usar um subconjunto do .NET framework completo, limitado pelas plataformas de destino.

Você pode ler mais sobre do Xamarin [suporte para bibliotecas de classes portáteis](~/cross-platform/app-fundamentals/pcl.md) e siga as instruções para ver como o [TaskyPortable exemplo](https://github.com/xamarin/mobile-samples/tree/master/TaskyPortable) funciona.


### <a name="net-standard"></a>.NET Standard

Introduzido no 2016, [.NET Standard](~/cross-platform/app-fundamentals/net-standard.md) projetos fornecem uma maneira fácil de compartilhar código entre plataformas, produzindo assemblies que podem ser usados em Windows, Xamarin plataformas (iOS, Android, Mac) e Linux.

Bibliotecas do .NET standard podem ser criadas e usadas como PCLs, exceto que as APIs disponíveis em cada versão (de 1.0 a 1.6) são descobertas com mais facilidade e cada versão é compatível com os números de versão inferiores.



 <a name="Populating_the_Solution" />


## <a name="populating-the-solution"></a>Preenchendo a solução

Independentemente de qual método é usado para compartilhar o código, a estrutura geral da solução deve implementar uma arquitetura em camadas que incentiva o compartilhamento de código.
A abordagem do Xamarin é o código de grupo em dois tipos de projeto:

-   **Projeto Core** – escrever o código pode ser reutilizado em um só lugar, seja compartilhado entre diferentes plataformas. Use os princípios de encapsulamento para ocultar os detalhes de implementação sempre que possível.
-   **Projetos de aplicativos específicos da plataforma** – consumir o código pode ser reutilizado com como pouco acoplamento quanto possível. Recursos específicos da plataforma são adicionados nesse nível, criada nos componentes expostos no projeto Core.


 <a name="Core_Project" />


### <a name="core-project"></a>Projeto de núcleo

Projetos de código compartilhado só devem fazer referência a assemblies que estão disponíveis em todas as plataformas – ou seja. namespaces do framework comuns, como `System`, `System.Core` e `System.Xml`.

Projetos compartilhados devem implementar tanta funcionalidade sem interface do usuário, como é possível, o que pode incluir as seguintes camadas:

-   **Camada de dados** – código por exemplo, se encarrega de armazenamento de dados físico.  [SQLite-NET](https://github.com/praeclarum/sqlite-net), como um banco de dados alternativo [Realm.io](https://realm.io/products/realm-mobile-database/) ou até mesmo arquivos XML. As classes de camada de dados normalmente são usados apenas pela camada de acesso a dados.
-   **Camada de acesso a dados** – define uma API que oferece suporte às operações de dados necessários para a funcionalidade do aplicativo, como métodos para acessar listas de dados, itens de dados individuais e também criar, editar e excluí-los.
-   **Camada de acesso a serviço** – uma camada opcional para fornecer a nuvem de serviços para o aplicativo. Contém o código que acessa os recursos de rede remota (serviços web, downloads de imagens, etc.) e, possivelmente, cache dos resultados.
-   **Camada de negócios** – definição de classes de modelo e as classes de fachada ou gerente que expõem a funcionalidade para os aplicativos específicos da plataforma.


 <a name="Platform-Specific_Application_Projects" />


### <a name="platform-specific-application-projects"></a>Projetos de aplicativos específicos da plataforma

Projetos específicos da plataforma devem referenciar os assemblies necessários para associar a SDK cada plataforma (xamarin. IOS, xamarin. Android, xamarin. Mac ou Windows), bem como o projeto de código compartilhado Core.

Os projetos específicos da plataforma devem implementar:

-   **Camada de aplicativo** – funcionalidade específica da plataforma e associação/conversão entre os objetos da camada de negócios e a interface do usuário.
-   **Camada de Interface do usuário** – telas, controles de interface do usuário personalizada, apresentação da lógica de validação.


<a name="Example" />


### <a name="example"></a>Exemplo

A arquitetura do aplicativo é ilustrada neste diagrama:

 [![](setting-up-a-xamarin-cross-platform-solution-images/conceptualarchitecture.png "A arquitetura do aplicativo é ilustrada neste diagrama")](setting-up-a-xamarin-cross-platform-solution-images/conceptualarchitecture.png#lightbox)

Esta captura de tela mostra uma configuração de solução com o projeto de núcleo compartilhado, iOS e projetos de aplicativo Android. O projeto Shared contém código relacionado a cada uma das camadas de arquitetura (código de negócios, serviços, dados e acesso a dados):

 ![](setting-up-a-xamarin-cross-platform-solution-images/core-solution-example.png "O projeto Shared contém código relacionado a cada uma das camadas de arquitetura (código de negócios, serviços, dados e acesso a dados)")


 <a name="Project_References" />


## <a name="project-references"></a>Referências de Projeto

Referências de projeto refletem as dependências para um projeto. Projetos do Core limitam suas referências para assemblies common para que o código é fácil de compartilhar.
Projetos de aplicativos específicos da plataforma referenciar o código compartilhado, além de outros assemblies específicos de plataforma que eles precisam para aproveitar a plataforma de destino.

Os projetos de aplicativos cada referência projeto compartilhado e contenham o código de interface do usuário necessário para a funcionalidade presente para o usuário, conforme mostrado nessas capturas de tela:

![](setting-up-a-xamarin-cross-platform-solution-images/solution-android.png "O aplicativo projeta cada referência de projeto compartilhado") ![](setting-up-a-xamarin-cross-platform-solution-images/solution-ios.png "cada referência de projeto compartilhado de projetos de aplicativo")


Exemplos específicos de como os projetos devem ser estruturados recebem os estudos de caso.

 <a name="Adding_Files" />


## <a name="adding-files"></a>Adicionando arquivos

 <a name="Build_Action" />


### <a name="build-action"></a>Ação de build

É importante definir a ação de build correta para determinados tipos de arquivo. Esta lista mostra a ação de compilação para alguns tipos de arquivo comuns:

-  **Todos os C# arquivos** – ação de compilação: Compilar
-   **Imagens no xamarin. IOS e Windows** – ação de Build: Conteúdo
-   **Arquivos Storyboard e XIB no xamarin. IOS** – ação de compilação: InterfaceDefinition
-   **Imagens e layouts AXML no Android** – ação de compilação: AndroidResource
-  **Arquivos XAML em projetos do Windows** – ação de compilação: Página
-  **Arquivos XAML de xamarin. Forms** – ação de compilação: EmbeddedResource


Geralmente o IDE irá detectar o tipo de arquivo e sugerir a ação de compilação correta.

 <a name="Case_Sensitivity" />


### <a name="case-sensitivity"></a>Diferenciação de maiúsculas e minúsculas

Por fim, lembre-se de que algumas plataformas têm sistemas de arquivo diferencia maiusculas de minúsculas (por exemplo.
iOS e Android) portanto, não se esqueça de usar um padrão de nomenclatura de arquivo consistente e certifique-se de que os nomes de arquivo é usada no código correspondem exatamente o sistema de arquivos. Isso é especialmente importante para imagens e outros recursos que você referencia no código.
