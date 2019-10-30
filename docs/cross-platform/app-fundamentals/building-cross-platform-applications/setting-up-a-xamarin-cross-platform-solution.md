---
title: Parte 3-Configurando uma solução de plataforma cruzada do Xamarin
description: Este documento descreve como configurar uma solução de plataforma cruzada no Xamarin. Ele aborda várias estratégias de compartilhamento de código, como projetos compartilhados e .NET Standard.
ms.prod: xamarin
ms.assetid: 4139A6C2-D477-C563-C1AB-98CCD0D10A93
author: davidortinau
ms.author: daortin
ms.date: 03/27/2017
ms.openlocfilehash: 843887282c9a5af671d46699ae2f601fd32902e0
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016882"
---
# <a name="part-3---setting-up-a-xamarin-cross-platform-solution"></a>Parte 3-Configurando uma solução de plataforma cruzada do Xamarin

Independentemente de quais plataformas estão sendo usadas, os projetos do Xamarin usam o mesmo formato de arquivo de solução (o formato de arquivo **. sln** do Visual Studio). As soluções podem ser compartilhadas entre ambientes de desenvolvimento, mesmo quando projetos individuais não podem ser carregados (como um projeto do Windows no Visual Studio para Mac).

Ao criar um novo aplicativo de plataforma cruzada, a primeira etapa é criar uma solução em branco. Esta seção explica o que acontece em seguida: Configurando os projetos para criar aplicativos móveis de plataforma cruzada.

 <a name="Sharing_Code" />

## <a name="sharing-code"></a>Compartilhando código

Consulte o documento [Opções de compartilhamento de código](~/cross-platform/app-fundamentals/code-sharing.md) para obter uma descrição detalhada de como implementar o compartilhamento de código entre plataformas.

 <a name="Shared_Asset_Projects" />

### <a name="shared-projects"></a>Projetos compartilhados

A abordagem mais simples para compartilhar arquivos de código é usar um [projeto compartilhado](~/cross-platform/app-fundamentals/shared-projects.md).

Esse método permite que você compartilhe o mesmo código em diferentes projetos de plataforma e use diretivas de compilador para incluir caminhos de código diferentes de plataforma específica.

 <a name="Portable_Class_Libraries" />

### <a name="portable-class-libraries-pcl"></a>PCL (Bibliotecas de classe portáteis)

Historicamente, um arquivo de projeto .NET (e o assembly resultante) foi direcionado a uma versão específica do Framework. Isso impede que o projeto ou o assembly seja compartilhado por diferentes estruturas.

Uma PCL (biblioteca de classes portátil) é um tipo especial de projeto que pode ser usado em plataformas de CLI diferentes, como Xamarin. iOS e Xamarin. Android, bem como WPF, Plataforma Universal do Windows e Xbox. A biblioteca só pode utilizar um subconjunto do .NET Framework completo, limitado pelas plataformas que estão sendo direcionadas.

Você pode ler mais sobre o suporte do Xamarin [para bibliotecas de classes portáteis](~/cross-platform/app-fundamentals/pcl.md) e seguir as instruções para ver como o [exemplo TaskyPortable](https://github.com/xamarin/mobile-samples/tree/master/TaskyPortable) funciona.

### <a name="net-standard"></a>.NET Standard

Introduzido em 2016, os projetos [.net Standard](~/cross-platform/app-fundamentals/net-standard.md) fornecem uma maneira fácil de compartilhar código entre plataformas, produzindo assemblies que podem ser usados em todas as plataformas Windows, Xamarin (Ios, Android, Mac) e Linux.

.NET Standard bibliotecas podem ser criadas e usadas como PCLs, exceto que as APIs disponíveis em cada versão (de 1,0 a 1,6) são descobertas mais facilmente e cada versão é compatível com versões anteriores com números de versão menores.

 <a name="Populating_the_Solution" />

## <a name="populating-the-solution"></a>Populando a solução

Independentemente de qual método é usado para compartilhar código, a estrutura geral da solução deve implementar uma arquitetura em camadas que incentiva o compartilhamento de código.
A abordagem do Xamarin é agrupar o código em dois tipos de projeto:

- **Projeto principal** – escreva código reutilizável em um só lugar, para ser compartilhado entre diferentes plataformas. Use os princípios de encapsulamento para ocultar detalhes de implementação sempre que possível.
- **Projetos de aplicativos específicos da plataforma** – consuma o código reutilizável com o mínimo de acoplamento possível. Os recursos específicos da plataforma são adicionados nesse nível, criados em componentes expostos no projeto principal.

 <a name="Core_Project" />

### <a name="core-project"></a>Projeto principal

Projetos de código compartilhado só devem referenciar assemblies que estejam disponíveis em todas as plataformas – por ex. os namespaces comuns do Common Framework, como `System`, `System.Core` e `System.Xml`.

Projetos compartilhados devem implementar o máximo possível de funcionalidades que não sejam da interface do usuário, o que pode incluir as seguintes camadas:

- **Camada de dados** – código que cuida do armazenamento de dados físicos, por exemplo.  [SQLite-net](https://github.com/praeclarum/sqlite-net), um banco de dados alternativo como [realm.Io](https://realm.io/products/realm-mobile-database/) ou até mesmo arquivos XML. As classes da camada de dados normalmente são usadas apenas pela camada de acesso a dados.
- **Camada de acesso a dados** – define uma API que dá suporte às operações de dados necessárias para a funcionalidade do aplicativo, como métodos para acessar listas de dados, itens de dados individuais e também criar, editar e excluí-los.
- **Camada de acesso ao serviço** – uma camada opcional para fornecer serviços de nuvem para o aplicativo. Contém o código que acessa recursos de rede remota (serviços Web, downloads de imagens, etc.) e, possivelmente, Caching dos resultados.
- **Camada de negócios** – definição das classes de modelo e das classes de fachada ou gerente que expõem a funcionalidade para os aplicativos específicos da plataforma.

 <a name="Platform-Specific_Application_Projects" />

### <a name="platform-specific-application-projects"></a>Projetos de aplicativos específicos da plataforma

Os projetos específicos da plataforma devem referenciar os assemblies necessários para ligar ao SDK de cada plataforma (Xamarin. iOS, Xamarin. Android, Xamarin. Mac ou Windows), bem como o projeto de código compartilhado básico.

Os projetos específicos da plataforma devem implementar:

- **Camada de aplicativo** – funcionalidade específica da plataforma e vinculação/conversão entre os objetos da camada de negócios e a interface do usuário.
- **Camada de interface do usuário** – telas, controles de interface do usuário personalizados, apresentação da lógica de validação.

<a name="Example" />

### <a name="example"></a>Exemplo

A arquitetura do aplicativo é ilustrada neste diagrama:

 [![](setting-up-a-xamarin-cross-platform-solution-images/conceptualarchitecture.png "The application architecture is illustrated in this diagram")](setting-up-a-xamarin-cross-platform-solution-images/conceptualarchitecture.png#lightbox)

Esta captura de tela mostra uma configuração de solução com os projetos de projeto de núcleo compartilhado, iOS e aplicativos Android. O projeto compartilhado contém código relacionado a cada uma das camadas de arquitetura (código comercial, de serviço, de dados e de acesso a dados):

 ![](setting-up-a-xamarin-cross-platform-solution-images/core-solution-example.png "The Shared Project contains code relating to each of the architectural layers (Business, Service, Data and Data Access code)")

 <a name="Project_References" />

## <a name="project-references"></a>Referências de Projeto

As referências de projeto refletem as dependências de um projeto. Os projetos principais limitam suas referências a assemblies comuns para que o código seja fácil de compartilhar.
Os projetos de aplicativos específicos da plataforma fazem referência ao código compartilhado, além de quaisquer outros assemblies específicos da plataforma necessários para aproveitar a plataforma de destino.

O aplicativo projeta cada projeto compartilhado de referência e contém o código de interface do usuário necessário para apresentar a funcionalidade para o usuário, conforme mostrado nestas capturas de tela:

![](setting-up-a-xamarin-cross-platform-solution-images/solution-android.png "O aplicativo projeta cada projeto compartilhado de referência") ![](setting-up-a-xamarin-cross-platform-solution-images/solution-ios.png "O aplicativo projeta cada projeto compartilhado de referência")

Exemplos específicos de como os projetos devem ser estruturados são fornecidos nos estudos de caso.

 <a name="Adding_Files" />

## <a name="adding-files"></a>Adicionando arquivos

 <a name="Build_Action" />

### <a name="build-action"></a>Ação de compilação

É importante definir a ação de compilação correta para determinados tipos de arquivo. Esta lista mostra a ação de Build para alguns tipos de arquivo comuns:

- **Todos C# os arquivos** – ação de compilação: compilar
- **Imagens no Xamarin. iOS & Windows** – ação de compilação: conteúdo
- **Arquivos XIB e storyboard no Xamarin. Ios** – ação de compilação: InterfaceDefinition
- **Imagens e layouts AXML no Android** – ação de compilação: AndroidResource
- **Arquivos XAML em projetos do Windows** – ação de compilação: página
- **Arquivos XAML do Xamarin. Forms** – ação de compilação: EmbeddedResource

Geralmente, o IDE detectará o tipo de arquivo e sugerirá a ação de compilação correta.

 <a name="Case_Sensitivity" />

### <a name="case-sensitivity"></a>Diferenciação de maiúsculas e minúsculas

Finalmente, lembre-se de que algumas plataformas têm sistemas de arquivos que diferenciam maiúsculas de minúsculas (por exemplo
iOS e Android), portanto, use um padrão de nomenclatura de arquivo consistente e certifique-se de que os nomes de arquivo usados no código correspondam exatamente ao sistema de arquivos. Isso é especialmente importante para imagens e outros recursos que você referencia no código.
