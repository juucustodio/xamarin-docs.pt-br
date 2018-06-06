---
title: Parte 3 - configuração de uma solução de plataforma cruzada do Xamarin
description: Este documento descreve como configurar uma solução de plataforma cruzada no Xamarin. Ele descreve código várias estratégias de compartilhamento, como compartilhado projetos e .NET padrão.
ms.prod: xamarin
ms.assetid: 4139A6C2-D477-C563-C1AB-98CCD0D10A93
author: asb3993
ms.author: amburns
ms.date: 03/27/2017
ms.openlocfilehash: f802e31d851915d33cb6dbf5866f8cba3ab90303
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34781266"
---
# <a name="part-3---setting-up-a-xamarin-cross-platform-solution"></a>Parte 3 - configuração de uma solução de plataforma cruzada do Xamarin

Independentemente de quais plataformas estiverem sendo usadas, projetos de Xamarin todos usam o mesmo formato de arquivo de solução (Visual Studio **. sln** formato de arquivo). As soluções podem ser compartilhadas em ambientes de desenvolvimento, mesmo quando não não possível carregar projetos individuais (por exemplo, um projeto do Windows no Visual Studio para Mac).



Ao criar um novo entre aplicativos de plataforma, a primeira etapa é criar uma solução em branco. Esta seção o que acontece em seguida: configuração de projetos para a criação de aplicativos móveis de plataforma cruzada.

 <a name="Sharing_Code" />


## <a name="sharing-code"></a>Compartilhando código

Consulte o [opções de compartilhamento de código](~/cross-platform/app-fundamentals/code-sharing.md) documento para obter uma descrição detalhada de como implementar o compartilhamento de código entre plataformas.

 <a name="Shared_Asset_Projects" />


### <a name="shared-projects"></a>Projetos compartilhados

A abordagem mais simples para o compartilhamento de arquivos de código é usar um [projeto compartilhado](~/cross-platform/app-fundamentals/shared-projects.md).

Esse método permite que você compartilhar o mesmo código em projetos de plataforma diferente e usar diretivas de compilador para incluir caminhos de código diferente, específica de plataforma.

 <a name="Portable_Class_Libraries" />


### <a name="portable-class-libraries-pcl"></a>Bibliotecas de classes portáteis (PCL)

Historicamente um arquivo de projeto do .NET (e o assembly resultante) foi direcionados para uma versão específica do framework. Isso impede que o projeto ou assembly que está sendo compartilhada pelas estruturas diferentes.

Uma biblioteca de classe portátil (PCL) é um tipo especial de projeto que pode ser usado por plataformas heterogêneas de CLI como xamarin e xamarin, bem como WPF, plataforma Universal do Windows e Xbox. A biblioteca pode utilizar apenas um subconjunto do .NET framework completo, limitado por plataformas de destino.

Você pode ler mais sobre do Xamarin [suporte para bibliotecas de classes portáteis](~/cross-platform/app-fundamentals/pcl.md) e siga as instruções para ver como o [TaskyPortable exemplo](https://github.com/xamarin/mobile-samples/tree/master/TaskyPortable) funciona.


### <a name="net-standard"></a>.NET Standard

Introduzida no 2016, [.NET padrão](~/cross-platform/app-fundamentals/net-standard.md) projetos fornecem uma maneira fácil de compartilhar código entre plataformas, produzindo assemblies que podem ser usados em Windows, Xamarin plataformas (iOS, Android, Mac) e Linux.

As bibliotecas .NET padrão podem ser criadas e usadas como PCLs, exceto pelo fato das APIs disponíveis em cada versão (de 1.0 1.6) mais facilmente são descobertas e cada versão é compatível com versões anteriores com números de versão inferiores.



 <a name="Populating_the_Solution" />


## <a name="populating-the-solution"></a>Preenchendo a solução

Independentemente de qual método é usado para compartilhar código, a estrutura geral de solução deve implementar uma arquitetura em camadas que encoraja o compartilhamento de código.
A abordagem de Xamarin é o código de grupo em dois tipos de projeto:

-   **Projeto principal** – escrever o código pode ser reutilizado em um só lugar, para ser compartilhado entre diferentes plataformas. Use os princípios de encapsulamento para ocultar detalhes de implementação, sempre que possível.
-   **Projetos de aplicativo específico da plataforma** – consumir o código reutilizável com como acoplamento pequeno quanto possível. Recursos específicos de plataforma são adicionados neste nível, criado em componentes expostos no projeto principal.


 <a name="Core_Project" />


### <a name="core-project"></a>Projeto principal

Projetos de código compartilhado devem referenciar apenas os assemblies que estão disponíveis em todas as plataformas – ie. Os namespaces do framework comuns como `System`, `System.Core` e `System.Xml`.

Projetos compartilhados devem implementar a funcionalidade tanta sem interface do usuário, como é possível, o que pode incluir as seguintes camadas:

-   **Camada de dados** – código que cuida por exemplo, armazenamento de dados físico.  [SQLite-NET](https://github.com/praeclarum/sqlite-net), um banco de dados alternativo como [Realm.io](https://realm.io/products/realm-mobile-database/) ou até mesmo arquivos XML. As classes da camada de dados normalmente são usados somente por camada de acesso a dados.
-   **Camada de acesso a dados** – define uma API que oferece suporte às operações de dados necessários para a funcionalidade do aplicativo, como métodos para acessar a lista de dados, itens de dados individuais e também criar, editar e excluí-los.
-   **Camada de acesso a serviço** – serviços de nuvem de fornecer uma camada opcional para o aplicativo. Contém o código que acessa os recursos de rede remota (serviços web, downloads de imagens, etc.) e possivelmente cache dos resultados.
-   **Camada de negócios** – definição de classes de modelo e as classes de fachada ou gerente que expõem a funcionalidade para os aplicativos de plataforma específica.


 <a name="Platform-Specific_Application_Projects" />


### <a name="platform-specific-application-projects"></a>Projetos de aplicativo específico da plataforma

Projetos específicos de plataforma devem referenciar os assemblies necessários para associar a SDK cada plataforma (xamarin, xamarin, Xamarin.Mac ou Windows), bem como o projeto de código compartilhado Core.

Os projetos específicos de plataforma devem implementar:

-   **Camada de aplicativo** – funcionalidade específica de plataforma e associação/conversão entre os objetos da camada de negócios e a interface do usuário.
-   **Camada de Interface do usuário** – telas, controles de interface de usuário personalizada, apresentação de lógica de validação.


<a name="Example" />


### <a name="example"></a>Exemplo

Este diagrama ilustra a arquitetura do aplicativo:

 [ ![](setting-up-a-xamarin-cross-platform-solution-images/conceptualarchitecture.png "A arquitetura do aplicativo é ilustrada no diagrama")](setting-up-a-xamarin-cross-platform-solution-images/conceptualarchitecture.png#lightbox)

Esta captura de tela mostra uma configuração de solução com o projeto de núcleo compartilhado, iOS e projetos de aplicativo do Android. O projeto compartilhado contém código relacionada a cada uma das camadas de arquitetura (código de negócios, serviços, dados e acesso a dados):

 ![](setting-up-a-xamarin-cross-platform-solution-images/core-solution-example.png "O projeto compartilhado contém código relacionadas a cada uma das camadas de arquitetura (código de negócios, serviços, dados e acesso a dados)")


 <a name="Project_References" />


## <a name="project-references"></a>Referências de Projeto

Referências de projeto refletem as dependências de um projeto. Projetos de núcleo limitam suas referências a assemblies comuns para que o código é fácil compartilhar.
Projetos de aplicativo específico da plataforma referenciam o código compartilhado, além de outros assemblies de plataforma específica que precisam aproveitar a plataforma de destino.

Os projetos de aplicativos cada referenciam de projeto compartilhado e contenham o código de interface do usuário necessário para funcionalidade presente para o usuário, conforme mostrado nessas capturas de tela:

![](setting-up-a-xamarin-cross-platform-solution-images/solution-android.png "O aplicativo projeta cada referência de projeto compartilhado") ![ ] (setting-up-a-xamarin-cross-platform-solution-images/solution-ios.png "cada referência de projeto compartilhado de projetos de aplicativo")


São fornecidos exemplos específicos de como os projetos devem ser estruturados nos estudos de caso.

 <a name="Adding_Files" />


## <a name="adding-files"></a>Adicionando arquivos

 <a name="Build_Action" />


### <a name="build-action"></a>Ação de compilação

É importante definir a ação de compilação correta para determinados tipos de arquivo. Esta lista mostra a ação de compilação para alguns tipos de arquivo comuns:

-  **Todos os arquivos c#** – ação de compilação: compila
-   **Imagens em xamarin & Windows** – ação de compilação: conteúdo
-   **Arquivos de Storyboard e XIB no xamarin** – ação de compilação: InterfaceDefinition
-   **Imagens e layouts AXML no Android** – ação de compilação: AndroidResource
-  **Arquivos XAML em projetos do Windows** – ação de compilação: página
-  **Arquivos XAML xamarin. Forms** – ação de compilação: EmbeddedResource


Geralmente o IDE detectará o tipo de arquivo e sugerir a ação de compilação correta.

 <a name="Case_Sensitivity" />


### <a name="case-sensitivity"></a>Diferenciação de maiúsculas e minúsculas

Por fim, lembre-se de que algumas plataformas de sistemas de arquivos diferencia maiusculas de minúsculas (por exemplo.
iOS e Android) a usar um padrão de nomenclatura de arquivo consistente e certifique-se de que os nomes de arquivo que você usa no código correspondam exata do sistema de arquivos. Isso é especialmente importante para imagens e outros recursos que fazem referência a no código.
