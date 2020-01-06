---
title: Parte 3-Configurando uma solução de plataforma cruzada do Xamarin
description: Este documento descreve como configurar uma solução de plataforma cruzada no Xamarin. Ele aborda várias estratégias de compartilhamento de código, como projetos compartilhados e .NET Standard.
ms.prod: xamarin
ms.assetid: 4139A6C2-D477-C563-C1AB-98CCD0D10A93
author: davidortinau
ms.author: daortin
ms.date: 03/27/2017
ms.openlocfilehash: e7cde22115830a845ed82aa907195521f36b6866
ms.sourcegitcommit: d8af612b6b3218fea396d2f180e92071c4d4bf92
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2020
ms.locfileid: "75663267"
---
# <a name="part-3---setting-up-a-xamarin-cross-platform-solution"></a>Parte 3-Configurando uma solução de plataforma cruzada do Xamarin

Independentemente de quais plataformas estão sendo usadas, os projetos do Xamarin usam o mesmo formato de arquivo de solução (o formato de arquivo **. sln** do Visual Studio). As soluções podem ser compartilhadas entre ambientes de desenvolvimento, mesmo quando projetos individuais não podem ser carregados (como um projeto do Windows no Visual Studio para Mac).

Ao criar um novo aplicativo de plataforma cruzada, a primeira etapa é criar uma solução em branco. Esta seção explica o que acontece em seguida: Configurando os projetos para criar aplicativos móveis de plataforma cruzada.

## <a name="sharing-code"></a>Compartilhando código

Consulte o documento [Opções de compartilhamento de código](~/cross-platform/app-fundamentals/code-sharing.md) para obter uma descrição detalhada de como implementar o compartilhamento de código entre plataformas.

### <a name="net-standard"></a>.NET Standard

Os projetos de [.net Standard](~/cross-platform/app-fundamentals/net-standard.md) fornecem uma maneira fácil de compartilhar código entre plataformas, produzindo assemblies que podem ser usados em todas as plataformas do Windows, Xamarin (Ios, Android, Mac) e Linux.
Essa é a maneira recomendada para compartilhar código para soluções Xamarin.

### <a name="other-options"></a>Outras opções

Historicamente, o Xamarin usava [PCLs (bibliotecas de classes portáteis)](~/cross-platform/app-fundamentals/pcl.md)e [projetos compartilhados](~/cross-platform/app-fundamentals/shared-projects.md). Nenhum deles é recomendado para novos projetos; e você deve considerar a migração de aplicativos existentes para usar .NET Standard.

## <a name="populating-the-solution"></a>Populando a solução

Independentemente de qual método é usado para compartilhar código, a estrutura geral da solução deve implementar uma arquitetura em camadas que incentiva o compartilhamento de código.
A abordagem do Xamarin é agrupar o código em dois tipos de projeto:

- **Projeto de núcleo (ou "compartilhado")** – escreva o código reutilizável em um único lugar, para ser compartilhado entre diferentes plataformas. Use os princípios de encapsulamento para ocultar detalhes de implementação sempre que possível.
- **Projetos de aplicativos específicos da plataforma** – consuma o código reutilizável com o mínimo de acoplamento possível. Os recursos específicos da plataforma são adicionados nesse nível, criados em componentes expostos no projeto principal.

### <a name="core-project"></a>Projeto principal

Os projetos principais que compartilham o código devem ser .NET Standard e apenas os assemblies de referência que estão disponíveis em todas as plataformas – por ex. os namespaces comuns do Common Framework, como `System`, `System.Core` e `System.Xml`.

Os projetos principais devem implementar o máximo possível de funcionalidade que não seja de interface do usuário, o que pode incluir as seguintes camadas:

- **Camada de dados** – código que cuida do armazenamento de dados físicos, por exemplo. [SQLite-net](https://www.nuget.org/packages/sqlite-net-pcl/) ou até mesmo arquivos XML. As classes da camada de dados normalmente são usadas apenas pela camada de acesso a dados.
- **Camada de acesso a dados** – define uma API que dá suporte às operações de dados necessárias para a funcionalidade do aplicativo, como métodos para acessar listas de dados, itens de dados individuais e também criar, editar e excluí-los.
- **Camada de acesso ao serviço** – uma camada opcional para fornecer serviços de nuvem para o aplicativo. Contém o código que acessa recursos de rede remota (serviços Web, downloads de imagens, etc.) e, possivelmente, Caching dos resultados.
- **Camada de negócios** – definição das classes de modelo e das classes de fachada ou gerente que expõem a funcionalidade para os aplicativos específicos da plataforma.

### <a name="platform-specific-application-projects"></a>Projetos de aplicativos específicos da plataforma

Os projetos específicos da plataforma devem referenciar os assemblies necessários para associar ao SDK de cada plataforma (Xamarin. iOS, Xamarin. Android, Xamarin. Mac ou Windows), bem como ao projeto .NET Standard.

Os projetos específicos da plataforma devem implementar:

- **Camada de aplicativo** – funcionalidade específica da plataforma e vinculação/conversão entre os objetos da camada de negócios e a interface do usuário.
- **Camada de interface do usuário** – telas, controles de interface do usuário personalizados, apresentação da lógica de validação.

## <a name="project-references"></a>Referências de projeto

As referências de projeto refletem as dependências de um projeto. Os projetos principais limitam suas referências a assemblies comuns para que o código seja fácil de compartilhar.
Os projetos de aplicativos específicos da plataforma fazem referência ao projeto .NET Standard, além de quaisquer outros assemblies específicos da plataforma de que precisam para aproveitar a plataforma de destino.

Exemplos específicos de como os projetos devem ser estruturados são fornecidos nos estudos de caso.

## <a name="adding-files"></a>Adicionando arquivos

### <a name="build-action"></a>Compilar ação

É importante definir a ação de compilação correta para determinados tipos de arquivo. Esta lista mostra a ação de Build para alguns tipos de arquivo comuns:

- **Todos C# os arquivos** – ação de compilação: compilar
- **Imagens no Xamarin. iOS & Windows** – ação de compilação: conteúdo
- **Arquivos XIB e storyboard no Xamarin. Ios** – ação de compilação: InterfaceDefinition
- **Imagens e layouts XML no Android** – ação de compilação: AndroidResource
- **Arquivos XAML em projetos do Windows** – ação de compilação: página
- **Arquivos XAML do Xamarin. Forms** – ação de compilação: EmbeddedResource

Geralmente, o IDE detectará o tipo de arquivo e sugerirá a ação de compilação correta.

### <a name="case-sensitivity"></a>Diferenciação de maiúsculas e minúsculas

Finalmente, lembre-se de que algumas plataformas têm sistemas de arquivos que diferenciam maiúsculas de minúsculas (por exemplo
iOS e Android), portanto, use um padrão de nomenclatura de arquivo consistente e certifique-se de que os nomes de arquivo usados no código correspondam exatamente ao sistema de arquivos. Isso é especialmente importante para imagens e outros recursos que você referencia no código.
