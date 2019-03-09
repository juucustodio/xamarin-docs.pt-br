---
title: Parte 2 – Arquitetura
description: Este documento descreve os padrões de arquitetura úteis para a criação de aplicativos de plataforma cruzada. Ele aborda as camadas de aplicativo típicas (camada de dados, camada de acesso a dados, etc.) e padrões de software móvel comuns (MVVM, MVC, etc.)
ms.prod: xamarin
ms.assetid: 2176DB2D-E84A-3757-CFAB-04A586068D50
author: asb3993
ms.author: amburns
ms.date: 03/27/2017
ms.openlocfilehash: cfb2bddceea7717ac87bd7a78fd9cd45e8b93144
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2019
ms.locfileid: "57670435"
---
# <a name="part-2---architecture"></a>Parte 2 – Arquitetura

É a filosofia principal da criação de aplicativos de plataforma cruzada criar uma arquitetura que se presta a uma maximização de compartilhamento entre as plataformas de código. Cumprir os seguintes princípios de programação orientada a objeto o ajuda a criar um aplicativo bem arquitetado:

-   **Encapsulamento** – garantindo que classes e camadas arquitetônicas até mesmo apenas expõem uma API mínimo executa seus necessárias funções e oculta os detalhes de implementação. Em um nível de classe, isso significa que os objetos se comportam como 'caixas pretas' e que consome o código não precisa saber como eles realizam suas tarefas. No nível de arquitetura, significa que a implementação de padrões como fachada que estimulam a uma API simplificada que orquestra as interações mais complexas em nome o código nas camadas mais abstratas. Isso significa que o código de interface do usuário (por exemplo) deve apenas ser responsável por exibir telas e aceitar a entrada do usuário; e nunca interagir diretamente com o banco de dados. Da mesma forma o código de acesso a dados deve apenas ler e gravar no banco de dados, mas nunca interagem diretamente com botões ou rótulos.
-   **Separação de responsabilidades** – Certifique-se de que cada componente (ambos em arquitetura e nível de classe) tem uma finalidade clara e bem definida. Cada componente deve executar apenas suas tarefas definidas e expor essa funcionalidade por meio de uma API que é acessível a outras classes que precisam usá-lo.
-   **Polimorfismo** – programação para uma interface (ou classe abstrata) que dá suporte a vários meios de implementações do código principal pode ser gravado e compartilhado entre plataformas, enquanto ainda interagindo com recursos específicos à plataforma.


O resultado natural é um aplicativo modelado de mundo real ou entidades abstratas com camadas lógicas separadas. Separar o código em camadas tornar aplicativos mais fáceis de entender, testar e manter. É recomendável que o código em cada camada seja fisicamente separado (seja em projetos separados, mesmo para aplicativos muito grandes ou de diretórios), bem como separados logicamente (usando namespaces).

 <a name="Typical_Application_Layers" />


## <a name="typical-application-layers"></a>Camadas de aplicativo típicas

Em todo este documento e os estudos de caso, nos referimos a camadas de aplicativo de seis a seguir:

-   **Camada de dados** – persistência de dados não volátil, provavelmente será um banco de dados SQLite, mas poderia ser implementado com arquivos XML ou qualquer outro mecanismo adequado.
-   **Camada de acesso a dados** – Wrapper em torno da camada de dados que fornece Create, Read, Update, acesso de exclusão (CRUD) nos dados sem expor os detalhes de implementação para o chamador. Por exemplo, a DAL pode conter instruções SQL para consultar ou atualizar os dados, mas o código de referência não precisaria saber disso.
-   **Camada de negócios** – (às vezes chamado de camada de lógica comercial ou BLL) contém definições de entidade de negócios (o modelo) e a lógica de negócios. Candidato para o padrão de fachada de negócios.
-   **Camada de acesso a serviço** – usado para acessar os serviços na nuvem: dos serviços web complexo (REST, JSON, WCF) a simples recuperação de dados e imagens de servidores remotos. Encapsula o comportamento de rede e fornece uma API simples para serem consumidos por camadas de aplicativo e da interface do usuário.
-   **Camada de aplicativo** – código que normalmente é específico da plataforma (geralmente não é compartilhado entre plataformas) ou de código que é específico ao aplicativo (não reutilizável em geral). Um bom teste para verificar se é necessário colocar o código na camada de aplicativo versus a camada de interface do usuário é (a) determinar se a classe tem os controles de exibição real ou (b) se ele pode ser compartilhado entre vários dispositivos ou telas (por exemplo iPhone e iPad).
-   **Camada de interface do usuário** – a camada de voltadas ao usuário, contém telas, widgets e os controladores que gerenciá-los.


Um aplicativo não pode conter todas as camadas necessariamente – por exemplo, a camada de acesso de serviço não seria existir em um aplicativo que não acessam recursos de rede. Um aplicativo muito simples pode mesclar a camada de dados e a camada de acesso a dados porque as operações são extremamente básicas.

 <a name="Common_Mobile_Software_Patterns" />


## <a name="common-mobile-software-patterns"></a>Common Mobile Software Patterns

Padrões são uma maneira estabelecida para capturar recorrentes soluções para problemas comuns. Há alguns padrões principais que são úteis para entender na criação de aplicativos móveis sustentável compreensível.

-   **Modelo, exibição, ViewModel (MVVM)** – padrão do Model-View-ViewModel é popular com estruturas que dão suporte à vinculação de dados, como o xamarin. Forms. Que foi popularizada por SDKs habilitado para XAML, como Windows Presentation Foundation (WPF) e Silverlight; em que o ViewModel atua como intermediário entre os dados (modelo) e a interface do usuário (visualização) por meio de associação de dados e comandos.
-   **Modelo, exibição, controlador (MVC)** – um padrão comum e frequentemente mal compreendido, o MVC é mais frequentemente usado ao criar Interfaces do usuário e fornece uma separação entre a definição real da tela de interface do usuário (visualização), o mecanismo por trás dele que manipula interação (controlador) e os dados que preenche (modelo). O modelo é, na verdade, uma parte opcional completamente e, portanto, o núcleo de Noções básicas sobre esse padrão se encontra no modo de exibição e controlador. O MVC é uma abordagem popular para aplicativos iOS.
-   **Fachada de negócios** – também conhecido como padrão de Gerenciador, fornece um ponto simplificado da entrada de trabalho complexos. Por exemplo, em um aplicativo de acompanhamento de tarefa, você pode ter um `TaskManager` classe com métodos como `GetAllTasks()` , `GetTask(taskID)` , `SaveTask (task)` , etc. O `TaskManager` classe fornece uma fachada para o funcionamento interno de realmente salvar/recuperar objetos de tarefas.
-   **Singleton** – padrão Singleton o fornece uma maneira em que apenas uma única instância de um objeto específico nunca pode existir. Por exemplo, ao usar o SQLite em aplicativos móveis, você deseja apenas uma instância do banco de dados. Usando o padrão Singleton é uma maneira simples de garantir isso.
-   **Provedor** – um padrão de inventado pela Microsoft (indiscutivelmente semelhante à estratégia ou básica de injeção de dependência) a fim de incentivar a reutilização de código entre aplicativos do Silverlight, WPF e WinForms. Código compartilhado pode ser escrito em relação a uma interface ou classe abstrata e implementações concretas de específico da plataforma são escritas e passadas quando o código é usado.
-   **Async** – não deve ser confundido com a palavra-chave Async, o padrão é usado quando o trabalho de longa execução precisa para ser executado sem manter a interface do usuário ou o processamento atual de assíncrono. Em sua forma mais simples, o padrão assíncrono simplesmente descreve que tarefas de longa execução devem ser inicializadas em outro thread (ou semelhante abstração de thread, como uma tarefa) enquanto o thread atual continuará a processar e aguarda uma resposta do processo em segundo plano e, em seguida, atualiza a interface do usuário quando dados de e/ou estado é retornado.


Cada um dos padrões será examinada com mais detalhes, conforme o uso prático é ilustrado nos estudos de caso. Wikipedia tem descrições mais detalhadas do [MVVM](https://en.wikipedia.org/wiki/Model–view–viewmodel), [MVC](https://en.wikipedia.org/wiki/Model–view–controller), [Facade](https://en.wikipedia.org/wiki/Facade_pattern), [Singleton](https://en.wikipedia.org/wiki/Singleton_pattern), [estratégia](https://en.wikipedia.org/wiki/Strategy_pattern)e [provedor](https://en.wikipedia.org/wiki/Provider_model) padrões (e dos [padrões de Design](https://en.wikipedia.org/wiki/Design_Patterns) geralmente).
