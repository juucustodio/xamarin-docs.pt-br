---
title: Parte 2 - arquitetura
description: Este documento descreve os padrões de arquitetura úteis para a criação de aplicativos de plataforma cruzada. Ele discute camadas de aplicativo típico (camada de dados, camada de acesso a dados, etc.) e padrões comuns de software móvel (MVVM, MVC, etc.)
ms.prod: xamarin
ms.assetid: 2176DB2D-E84A-3757-CFAB-04A586068D50
author: asb3993
ms.author: amburns
ms.date: 03/27/2017
ms.openlocfilehash: ebcfe8880a826c552d55e7a5567271a5a764fa1d
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34780868"
---
# <a name="part-2---architecture"></a>Parte 2 - arquitetura

É um princípio chave da criação de aplicativos de plataforma cruzada criar uma arquitetura que presta a uma maximização de código compartilhamento entre plataformas. Cumprir os seguintes princípios de programação orientada a objeto o ajuda a cria um aplicativo bem projetado:

-   **Encapsulamento** – garantindo que classes e camadas arquitetônicas mesmo apenas exponham uma API mínima que executa seus necessário funções e oculta os detalhes de implementação. Em um nível de classe, isso significa que os objetos se comportam como 'caixas pretas' e que código de consumo não precisa saber como eles realizar suas tarefas. No nível de arquitetura, isso significa implementar os padrões como fachada que incentivem uma API simplificada que orquestra as interações mais complexas em nome de código em camadas mais abstratas. Isso significa que o código de interface do usuário (por exemplo) deve ser apenas responsável por exibindo as telas e aceitar a entrada do usuário; e nunca interagir diretamente com o banco de dados. Da mesma forma o código de acesso a dados deve apenas de leitura e gravação no banco de dados, mas nunca interagir diretamente com os botões ou rótulos.
-   **Separação de responsabilidades** – Certifique-se de que cada componente (na arquitetura e nível de classe) tem uma finalidade clara e bem definida. Cada componente deve executar somente as tarefas definidas e expor essa funcionalidade por meio de uma API que é acessível a outras classes que precisam usá-lo.
-   **Polimorfismo** – programação para uma interface (ou classe abstrata) que oferece suporte a vários meios de implementações do código principal pode ser gravado e compartilhado entre plataformas, enquanto ainda interagir com recursos específicos de plataforma.


O resultado natural é um aplicativo modelado reais ou entidades abstratas com camadas lógicas separadas. Separação de código em camadas tornar aplicativos mais fáceis de entender, testar e manter. É recomendável que o código em cada camada seja fisicamente separado (em diretórios ou projetos separados até mesmo para aplicativos grandes), bem como logicamente separado (usando namespaces).

 <a name="Typical_Application_Layers" />


## <a name="typical-application-layers"></a>Camadas de aplicativo típico

Em todo este documento e estudos de caso, nós nos referimos a camadas de seis aplicativo a seguir:

-   **Camada de dados** – persistência de dados não volátil, provavelmente será um banco de dados SQLite, mas pode ser implementado com arquivos XML ou qualquer outro mecanismo adequado.
-   **Camada de acesso a dados** – Wrapper em torno da camada de dados que fornece a criar, ler, atualizar, acesso de exclusão (CRUD) nos dados sem expor os detalhes de implementação para o chamador. Por exemplo, a DAL pode conter instruções SQL para consultar ou atualizar os dados, mas o código de referência não precisa saber isso.
-   **Camada de negócios** – (às vezes chamado de camada de lógica comercial ou BLL) contém definições de entidade de negócios (o modelo) e a lógica de negócios. Candidato para o padrão de fachada de negócios.
-   **Camada de acesso a serviço** – usado para acessar os serviços na nuvem: dos serviços web complexos (REST, JSON, WCF) a recuperação simple de dados e imagens de servidores remotos. Encapsula o comportamento de rede e fornece uma API simples para ser consumido pelas camadas de aplicativo e a interface do usuário.
-   **Camada de aplicativo** – código que geralmente é específico da plataforma (geralmente não são compartilhado entre plataformas) ou de código que é específico para o aplicativo (não geralmente reutilizável). É um bom teste se inserir o código na camada de aplicativo versus a camada de interface do usuário (a) determinar se a classe tem controles de exibição real ou (b) se ele pode ser compartilhado entre vários dispositivos ou telas (por exemplo. iPhone e iPad).
-   **Camada de interface do usuário** – a camada de voltado ao usuário, contém telas, widgets e os controladores que gerenciá-los.


Um aplicativo não pode conter necessariamente todas as camadas – por exemplo, a camada de acesso de serviço não seria existir em um aplicativo que não acessam recursos de rede. Um aplicativo muito simples pode mesclar a camada de dados e a camada de acesso a dados porque as operações são muito básicas.

 <a name="Common_Mobile_Software_Patterns" />


## <a name="common-mobile-software-patterns"></a>Padrões comuns de Software móvel

Padrões são estabelecidos de forma recorrentes soluções para problemas comuns de captura. Há alguns padrões de chave que são úteis para entender na criação de aplicativos móveis sustentável compreensíveis.

-   **Modelo de exibição, ViewModel (MVVM)** – o Model-View-ViewModel padrão é popular com estruturas que dão suporte a associação de dados, como xamarin. Forms. Ele foi popularizado por SDKs habilitado XAML como Windows Presentation Foundation (WPF) e Silverlight. qual o ViewModel atua como um intermediário entre os dados (modelo) e a interface do usuário (visualização) por meio de associação de dados e comandos.
-   **Modelo de exibição, Controller (MVC)** – um padrão comum e geralmente mal, MVC são geralmente usado na criação de Interfaces de usuário e fornece uma separação entre a definição real da tela de interface do usuário (visualização), o mecanismo por trás dele que manipula interação (controlador) e os dados que preenche (modelo). O modelo é realmente uma parte opcional completamente e, portanto, o núcleo de compreender esse padrão está no modo de exibição e controlador. MVC é um método popular para aplicativos iOS.
-   **Fachada de negócios** — também conhecido como gerenciador padrão, fornece um ponto simplificado de entrada para trabalhos complexos. Por exemplo, em um aplicativo de controle de tarefa, você pode ter um `TaskManager` classe com métodos como `GetAllTasks()` , `GetTask(taskID)` , `SaveTask (task)` , etc. O `TaskManager` classe fornece uma fachada para o funcionamento interno de realmente salvar/recuperar objetos de tarefas.
-   **Singleton** – padrão Singleton o fornece uma forma em que apenas uma única instância de um determinado objeto pode já existir. Por exemplo, ao usar SQLite em aplicativos móveis, você deseja apenas uma instância do banco de dados. Usando o padrão de Singleton é uma maneira simples de garantir que isso.
-   **Provedor** – um padrão cunhado pela Microsoft (possivelmente é semelhante a estratégia, ou básica injeção de dependência) a fim de incentivar reutilização de código em aplicativos do Silverlight, WPF e WinForms. Código compartilhado pode ser gravado em uma interface ou classe abstrata e implementações concretas específica de plataforma são gravadas e passadas quando o código é usado.
-   **Assíncrono** – não deve ser confundido com a palavra-chave Async, o assíncrono padrão é usado quando o trabalho de longa execução precisa ser executado sem manter a interface do usuário ou processamento atual. Em sua forma mais simples, o padrão assíncrono simplesmente descreve que tarefas de longa execução devem ser inicializadas em outro thread (ou semelhante abstração de thread, como uma tarefa) ao thread atual continua a processar e aguarda uma resposta do processo em segundo plano e, em seguida, atualiza a interface do usuário quando os dados e/ou estado são retornados.


Cada um dos padrões será examinada em mais detalhes conforme ilustrado nos estudos de caso seu uso prático. Wikipédia tem descrições mais detalhadas do [MVVM](https://en.wikipedia.org/wiki/Model–view–viewmodel), [MVC](https://en.wikipedia.org/wiki/Model–view–controller), [fachada](http://en.wikipedia.org/wiki/Facade_pattern), [Singleton](http://en.wikipedia.org/wiki/Singleton_pattern), [estratégia](http://en.wikipedia.org/wiki/Strategy_pattern)e [provedor](http://en.wikipedia.org/wiki/Provider_model) padrões (e de [padrões de Design](http://en.wikipedia.org/wiki/Design_Patterns) geralmente).
