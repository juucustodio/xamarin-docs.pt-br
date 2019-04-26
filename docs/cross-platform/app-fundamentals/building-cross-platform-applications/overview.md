---
title: Criação cruzada visão geral dos aplicativos de plataforma
description: Este documento fornece uma visão geral da criação de aplicativos de plataforma cruzada. Ele aborda o valor de C#, padrões, como MVC/MVVM e interfaces do usuário nativas de design.
ms.prod: xamarin
ms.assetid: E442EEFB-FA9C-40E9-9668-5A3F915C8400
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 1eb308e0095c29d8ab0d0bdf1f74b807fd2ab97f
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61275588"
---
# <a name="building-cross-platform-applications-overview"></a>Criação cruzada visão geral dos aplicativos de plataforma

Este guia apresenta a plataforma Xamarin e como projetar um aplicativo de plataforma cruzada para maximizar a reutilização de código e fornecer uma experiência nativa de alta qualidade em todas as principais plataformas móveis: iOS, Android e Windows Phone.

A abordagem usada neste documento é geralmente aplicável aos aplicativos de produtividade e aplicativos de jogos, no entanto, o foco está na produtividade e o utilitário (aplicativos não é de jogo). Consulte a [Introdução ao MonoGame documento](~/graphics-games/monogame/introduction/index.md) ou Confira [ferramentas do Visual Studio para Unity](https://docs.microsoft.com/visualstudio/cross-platform/visual-studio-tools-for-unity) para obter diretrizes de desenvolvimento de jogos de plataforma cruzada.

A frase "gravar-executadas uma vez, em qualquer lugar" é frequentemente usado para exaltam as virtudes de uma única base de código que é executado sem modificação em várias plataformas. Enquanto ele tem a vantagem de reutilização de código, essa abordagem geralmente leva a aplicativos que têm um conjunto de recursos menor denominador comum e uma interface de usuário atraentes genérico que não se ajusta muito bem em qualquer uma das plataformas de destino.

Xamarin não é apenas uma "write-executadas uma vez, em qualquer lugar" plataforma, como um de seus pontos fortes é a capacidade de implementar interfaces do usuário nativas especificamente para cada plataforma. No entanto, com design elaborado, ainda é possível compartilhar a maioria dos não-usuário código de interface e obter o melhor dos dois mundos: escrever seu código de lógica de negócios e de armazenamento de dados uma vez e apresentar as interfaces do usuário nativas em cada plataforma. Este documento aborda uma abordagem geral da arquitetura para atingir esse objetivo.

Aqui está um resumo dos principais pontos para a criação de aplicativos de plataforma cruzada do Xamarin:

-   **Use C#**  -escrever seus aplicativos no C#. Um código escrito C# pode ser transportado para o iOS e Android usando o Xamarin com muita facilidade e, obviamente, é usado em aplicativos do Windows.
-   **Utilizar padrões de design MVC ou MVVM** -desenvolver a Interface do usuário do seu aplicativo usando o padrão de modelo/exibição/controlador. Projetar seu aplicativo usando uma abordagem de modelo/exibição/controlador ou uma abordagem de modelo/exibição/ViewModel em que há uma separação clara entre o "modelo" e o restante. Determine quais partes do seu aplicativo irá usar elementos de interface de usuário nativo de cada plataforma (iOS, Android, Windows, Mac) e usar isso como uma diretriz para dividir seu aplicativo em dois componentes: "Core" e "Interface do usuário".
-   **Criar interfaces do usuário nativas** -cada aplicativo específicas do sistema operacional fornece uma camada de interface do usuário diferentes (implementado no C# ferramentas de design com a Ajuda da interface do usuário nativa):

1.  No iOS, use as APIs de UIKit para criar aplicativos de aparência nativa, utilizando, opcionalmente, o designer do iOS do Xamarin para criar sua interface do usuário visualmente.
1.  No Android, use Android.Views para criar aplicativos de aparência nativa, tirando proveito do designer de interface do usuário do Xamarin.
1.  No Windows você usará XAML para a camada de apresentação, criada no designer de interface do usuário do Visual Studio ou do Blend.
1.  No Mac, você irá usar Storyboards para a camada de apresentação, criada no Xcode.

Projetos xamarin. Forms têm suporte em todas as plataformas e permitem que você crie interfaces do usuário que podem ser compartilhados entre plataformas usando xamarin. Forms XAML. 

A quantidade de reutilização de código depende em grande parte quanto de código é mantida no núcleo compartilhado e a quantidade de código é a interface do usuário específico. O código básico é que qualquer coisa que não interage diretamente com o usuário, mas em vez disso, fornece serviços para partes do aplicativo que irá coletar e exibir essas informações.

Para aumentar a quantidade de reutilização de código, você pode adotar os componentes de plataforma cruzada que fornecem serviços comuns entre todos esses sistemas, como:

1.   [SQLite-net](https://www.nuget.org/packages/sqlite-net-pcl/) para o armazenamento local do SQL,
1.   [Xamarin Plugins](https://xamarin.com/plugins) para acessar recursos específicos do dispositivo entre a câmera, contatos e localização geográfica,
1.   [Pacotes do NuGet](https://nuget.org) que são compatíveis com projetos do Xamarin, como [Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/),
1.  Usando recursos do .NET framework para a rede, serviços web, e/s e muito mais.


Alguns desses componentes são implementados na *Tasky* estudo de caso.

 <a name="Separate_Reusable_Code_into_a_Core_Library" />


## <a name="separate-reusable-code-into-a-core-library"></a>Separar o código reutilizável em uma biblioteca principal

Seguindo o princípio de separação de responsabilidades, disposição em camadas de arquitetura de seu aplicativo e, em seguida, movendo funcionalidade básica que é independente de plataforma em uma biblioteca reutilizável principal, você pode maximizar o compartilhamento entre plataformas, como a figura a seguir de código ilustra:

 ![](overview-images/layers2.png "Seguindo o princípio de separação de responsabilidades, disposição em camadas de arquitetura de seu aplicativo e, em seguida, movendo funcionalidade básica que é independente de plataforma em uma biblioteca reutilizável principal, você pode maximizar o compartilhamento entre as plataformas de código")

 <a name="Case_Studies" />


## <a name="case-studies"></a>Estudos de caso

Há um estudo de caso que acompanha este documento – *Tasky Pro*. Cada estudo de caso aborda a implementação dos conceitos descritos neste documento em um exemplo do mundo real. O código é um software livre e está disponível no [github](https://github.com/xamarin/mobile-samples/).
