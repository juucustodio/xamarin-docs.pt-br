---
title: Parte 2 – Arquitetura
description: Este documento descreve os padrões de arquitetura úteis para a criação de aplicativos de plataforma cruzada. Ele aborda camadas típicas de aplicativos (camada de dados, camada de acesso a dados, etc.) e padrões comuns de software móvel (MVVM, MVC, etc.)
ms.prod: xamarin
ms.assetid: 2176DB2D-E84A-3757-CFAB-04A586068D50
author: asb3993
ms.author: amburns
ms.date: 03/27/2017
ms.openlocfilehash: 23758e9794904e60b0ba09fe740574da8e7b830c
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69526646"
---
# <a name="part-2---architecture"></a>Parte 2 – Arquitetura

Uma filosofia importante da criação de aplicativos de plataforma cruzada é criar uma arquitetura que se presta a uma maximização do compartilhamento de código entre plataformas. Obedecer aos seguintes princípios de programação orientada a objeto ajuda a criar um aplicativo bem projetado:

- **Encapsulamento** – garantindo que as classes e até mesmo as camadas de arquitetura exponham apenas uma API mínima que executa suas funções necessárias e oculta os detalhes da implementação. Em um nível de classe, isso significa que os objetos se comportam como ' caixas pretas ' e que o código de consumo não precisa saber como eles realizam suas tarefas. Em um nível de arquitetura, isso significa implementar padrões como a fachada que incentiva uma API simplificada que orquestra interações mais complexas em nome do código em camadas mais abstratas. Isso significa que o código da interface do usuário (por exemplo) só deve ser responsável por exibir telas e aceitar a entrada do usuário; e nunca interagir diretamente com o banco de dados. Da mesma forma, o código de acesso a dados só deve ler e gravar no banco de dado, mas nunca interagir diretamente com botões ou rótulos.
- **Separação de responsabilidades** – garanta que cada componente (em arquitetura e nível de classe) tenha uma finalidade clara e bem definida. Cada componente deve executar apenas suas tarefas definidas e expor essa funcionalidade por meio de uma API que seja acessível para as outras classes que precisam usá-la.
- **Polimorfismo** – a programação para uma interface (ou classe abstrata) que dá suporte a várias implementações significa que o código principal pode ser escrito e compartilhado entre plataformas, enquanto ainda interage com recursos específicos da plataforma.


O resultado natural é um aplicativo modelado após entidades do mundo real ou abstratas com camadas lógicas separadas. Separar o código em camadas torna os aplicativos mais fáceis de entender, testar e manter. É recomendável que o código em cada camada seja fisicamente separado (em diretórios ou até mesmo projetos separados para aplicativos muito grandes), bem como separados logicamente (usando namespaces).

 <a name="Typical_Application_Layers" />


## <a name="typical-application-layers"></a>Camadas de aplicativo típicas

Ao longo deste documento e dos estudos de caso, nos referimos às seis camadas de aplicativo a seguir:

- **Camada de dados** – persistência de dados não volátil, provavelmente é um banco de dados SQLite, mas pode ser implementada com arquivos XML ou qualquer outro mecanismo adequado.
- **Camada de acesso a dados** – wrapper em volta da camada de dados que fornece acesso de criação, leitura, atualização, exclusão (CRUD) aos dados sem expor detalhes de implementação para o chamador. Por exemplo, a DAL pode conter instruções SQL para consultar ou atualizar os dados, mas o código de referência não precisa saber isso.
- **Camada de negócios** – (às vezes chamada de camada de lógica de negócios ou BLL) contém definições de entidade de negócios (o modelo) e lógica de negócios. Padrão de fachada do candidato para empresas.
- **Camada de acesso de serviço** – usada para acessar serviços na nuvem: de serviços da Web complexos (REST, JSON, WCF) até a recuperação simples de dados e imagens de servidores remotos. Encapsula o comportamento de rede e fornece uma API simples a ser consumida pelas camadas de aplicativo e de interface do usuário.
- **Camada de aplicativo** – código que normalmente é específico da plataforma (geralmente não compartilhada entre plataformas) ou código que é específico para o aplicativo (geralmente não reutilizável). Um bom teste de colocação do código na camada de aplicativo versus a camada da interface do usuário é (a) para determinar se a classe tem algum controle de exibição real ou (b) se ele pode ser compartilhado entre várias telas ou dispositivos (por exemplo, iPhone e iPad).
- **Camada de interface do usuário (IU)** – a camada voltada para o usuário, contém telas, widgets e controladores que os gerenciam.


Um aplicativo pode não conter necessariamente todas as camadas – por exemplo, a camada de acesso ao serviço não existirá em um aplicativo que não acesse recursos de rede. Um aplicativo muito simples pode mesclar a camada de dados e a camada de acesso a dados porque as operações são extremamente básicas.

 <a name="Common_Mobile_Software_Patterns" />


## <a name="common-mobile-software-patterns"></a>Padrões comuns de software móvel

Padrões são uma maneira estabelecida de capturar soluções recorrentes para problemas comuns. Há alguns padrões importantes que são úteis para entender na criação de aplicativos móveis que podem ser mantidos/compreensíveis.

- **Model, View, ViewModel (MVVM)** – o padrão Model-View-ViewModel é popular com estruturas que dão suporte à vinculação de dados, como Xamarin. Forms. Ele foi popular por SDKs habilitados para XAML como o Windows Presentation Foundation (WPF) e o Silverlight; em que o ViewModel atua como uma passagem entre os dados (modelo) e a interface do usuário (exibição) por meio de vinculação de dados e comandos.
- **Modelo, exibição, controlador (MVC)** – um padrão comum e geralmente mal compreendido, o MVC é usado com mais frequência ao criar interfaces do usuário e fornece uma separação entre a definição real de uma tela de IU (exibição), o mecanismo por trás dela que lida com a interação ( Controlador) e os dados que o popula (modelo). O modelo é, na verdade, uma parte totalmente opcional e, portanto, o núcleo da compreensão desse padrão está na exibição e no controlador. O MVC é uma abordagem popular para aplicativos iOS.
- **Fachada de negócios** – também conhecido como padrão de gerente, fornece um ponto de entrada simplificado para trabalho complexo. Por exemplo, em um aplicativo de rastreamento de tarefas, você pode `TaskManager` ter uma classe com métodos `GetAllTasks()` como `GetTask(taskID)` , `SaveTask (task)` ,, etc. A `TaskManager` classe fornece uma fachada para os trabalhos internos de realmente salvar/recuperar objetos de tarefas.
- **Singleton** – o padrão singleton fornece uma maneira na qual apenas uma única instância de um objeto específico pode existir. Por exemplo, ao usar o SQLite em aplicativos móveis, você sempre desejará uma instância do banco de dados. Usar o padrão singleton é uma maneira simples de garantir isso.
- **Provedor** – um padrão cunhado pela Microsoft (possivelmente semelhante à estratégia ou injeção de dependência básica) para incentivar o reuso de código nos aplicativos Silverlight, WPF e WinForms. O código compartilhado pode ser escrito em uma interface ou classe abstrata, e implementações concretas específicas da plataforma são escritas e passadas quando o código é usado.
- **Async** – não deve ser confundido com a palavra-chave Async, o padrão assíncrono é usado quando o trabalho de execução longa precisa ser executado sem manter a interface do usuário ou o processamento atual. Em sua forma mais simples, o padrão assíncrono simplesmente descreve que tarefas de longa execução devem ser iniciadas em outro thread (ou abstração de thread semelhante, como uma tarefa), enquanto o thread atual continua a processar e escuta uma resposta do processo em segundo plano e, em seguida, atualiza a interface do usuário quando os dados e o estado são retornados.


Cada um dos padrões será examinado em mais detalhes, pois seu uso prático é ilustrado nos estudos de caso. A Wikipédia tem descrições mais detalhadas dos padrões [MVVM](https://en.wikipedia.org/wiki/Model–view–viewmodel), [MVC](https://en.wikipedia.org/wiki/Model–view–controller), [fachada](https://en.wikipedia.org/wiki/Facade_pattern), [singleton](https://en.wikipedia.org/wiki/Singleton_pattern), [estratégia](https://en.wikipedia.org/wiki/Strategy_pattern) e [provedor](https://en.wikipedia.org/wiki/Provider_model) (e de [padrões de design](https://en.wikipedia.org/wiki/Design_Patterns) geralmente).
