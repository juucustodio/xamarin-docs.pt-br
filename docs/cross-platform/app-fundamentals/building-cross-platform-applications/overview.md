---
title: Criando visão geral de aplicativos de plataforma cruzada
description: Este documento fornece uma visão geral de alto nível da criação de aplicativos de plataforma cruzada. Ele aborda o valor de C#, padrões de design, como MVC/MVVM e UIs nativas.
ms.prod: xamarin
ms.assetid: E442EEFB-FA9C-40E9-9668-5A3F915C8400
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: bdf641ca15919958627850e000c117eeedd12de9
ms.sourcegitcommit: 836d54779190b1bef1b43bc0c2016c9b3034bfda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93281242"
---
# <a name="building-cross-platform-applications-overview"></a>Criando visão geral de aplicativos de plataforma cruzada

Este guia apresenta a plataforma Xamarin e como arquitetar um aplicativo de plataforma cruzada para maximizar o uso de código e fornecer uma experiência nativa de alta qualidade em todas as principais plataformas móveis: iOS, Android e Windows Phone.

A abordagem usada neste documento geralmente se aplica a aplicativos de produtividade e aplicativos de jogos, mas o foco está na produtividade e no utilitário (aplicativos sem jogo). Consulte [Ferramentas do Visual Studio para Unity](/visualstudio/cross-platform/visual-studio-tools-for-unity) para obter diretrizes de desenvolvimento de jogos entre plataformas.

A frase "Write-Once, executar em qualquer lugar" é geralmente usada para enalteça as virtudes de uma única base de código que é executada sem modificações em várias plataformas. Embora ele tenha o benefício de reutilização de código, essa abordagem geralmente leva a aplicativos que têm um conjunto de recursos de denominador mais baixo-comum e uma interface de usuário de aparência genérica que não se encaixa perfeitamente em nenhuma das plataformas de destino.

O Xamarin não é apenas uma plataforma de "gravação única, executada em qualquer lugar", porque um de seus pontos fortes é a capacidade de implementar interfaces de usuário nativas especificamente para cada plataforma. No entanto, com um design elaborado, ainda é possível compartilhar a maior parte do código de interface que não seja do usuário e obter o melhor dos dois mundos: escrever seu código de armazenamento de dados e de lógica de negócios uma vez e apresentar interfaces de usuário nativas em cada plataforma. Este documento discute uma abordagem arquitetônica geral para atingir essa meta.

Aqui está um resumo dos principais pontos para criar aplicativos de plataforma cruzada do Xamarin:

- **Use c#** – escreva seus aplicativos em c#. O código existente escrito em C# pode ser portado para iOS e Android usando o Xamarin com muita facilidade e, obviamente, usado em aplicativos do Windows.
- **Utilizar padrões de design do modelo MVC ou MVVM** – desenvolva a interface do usuário do aplicativo usando o padrão Model/View/Controller. Projete seu aplicativo usando uma abordagem de modelo/exibição/controlador ou uma abordagem de Model/View/ViewModel em que há uma separação clara entre o "modelo" e o restante. Determine quais partes do seu aplicativo usarão elementos nativos da interface do usuário de cada plataforma (iOS, Android, Windows, Mac) e use-o como uma diretriz para dividir seu aplicativo em dois componentes: "Core" e "user-interface".
- **Criar UIs nativas** -cada aplicativo específico do sistema operacional fornece uma camada de interface de usuário diferente (implementada em C# com a ajuda de ferramentas de design de interface do usuário nativas):

1. No iOS, use as APIs do UIKit para criar aplicativos de aparência nativa, utilizando opcionalmente o iOS designer do Xamarin para criar sua interface do usuário visualmente.
1. No Android, use Android. views para criar aplicativos de aparência nativa, tirando proveito do designer de interface do usuário do Xamarin.
1. No Windows, você usará o XAML para a camada de apresentação, criado no designer de interface do usuário do Visual Studio ou do Blend.
1. No Mac, você usará storyboards para a camada de apresentação, criada no Xcode.

Os projetos do Xamarin. Forms têm suporte em todas as plataformas e permitem que você crie interfaces do usuário que podem ser compartilhadas entre plataformas usando o XAML do Xamarin. Forms.

A quantidade de reutilização de código dependerá, em grande parte, da quantidade de código mantida no núcleo compartilhado e da quantidade de código específica da interface do usuário. O código principal é qualquer coisa que não interaja diretamente com o usuário, mas, em vez disso, fornece serviços para partes do aplicativo que coletarão e exibirão essas informações.

Para aumentar a quantidade de reutilização de código, você pode adotar componentes de plataforma cruzada que fornecem serviços comuns em todos esses sistemas, como:

1. [SQLite-net](https://www.nuget.org/packages/sqlite-net-pcl/) para o armazenamento local do SQL,
1. [Plugins do Xamarin](https://xamarin.com/plugins) para acessar recursos específicos do dispositivo, incluindo câmera, contatos e geolocalização,
1. [Pacotes NuGet](https://nuget.org) que são compatíveis com projetos do Xamarin, como [JSON.net](https://www.nuget.org/packages/Newtonsoft.Json/),
1. Usando recursos do .NET Framework para rede, serviços Web, e/s e muito mais.

Alguns desses componentes são implementados no estudo de caso de *tarefa* .

 <a name="Separate_Reusable_Code_into_a_Core_Library"></a>

## <a name="separate-reusable-code-into-a-core-library"></a>Separar código reutilizável em uma biblioteca principal

Seguindo o princípio de separação de responsabilidade, colocando em camadas a arquitetura do aplicativo e, em seguida, movendo a funcionalidade principal que é independente da plataforma em uma biblioteca principal reutilizável, você pode maximizar o compartilhamento de código entre plataformas, como a figura abaixo ilustra:

 ![Seguindo o princípio de separação de responsabilidade, colocando em camadas a arquitetura do aplicativo e, em seguida, movendo a funcionalidade principal que é independente da plataforma em uma biblioteca principal reutilizável, você pode maximizar o compartilhamento de código entre plataformas](overview-images/layers2.png)

 <a name="Case_Studies"></a>

## <a name="case-studies"></a>Estudos de Caso

Há um estudo de caso que acompanha este documento – *taskal pro*. Cada estudo de caso aborda a implementação dos conceitos descritos neste documento em um exemplo do mundo real. O código é de código-fonte aberto e está disponível no [GitHub](https://github.com/xamarin/mobile-samples/).
