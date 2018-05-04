---
title: Criação de visão geral de aplicativos de plataforma cruzada /
ms.prod: xamarin
ms.assetid: E442EEFB-FA9C-40E9-9668-5A3F915C8400
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: e4a55829e35329a50b2e121311b0102328e6fea9
ms.sourcegitcommit: 4b0582a0f06598f3ff8ad5b817946459fed3c42a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/03/2018
---
# <a name="building-cross-platform-applications-overview"></a>Criação de visão geral de aplicativos de plataforma cruzada /

Este guia apresenta a plataforma Xamarin e como criar um aplicativo de plataforma cruzada para maximizar a reutilização de código e fornecer uma experiência de alta qualidade nativo em todas as principais plataformas móveis: iOS, Android e Windows Phone.

A abordagem usada neste documento é geralmente aplicável a aplicativos de produtividade e aplicativos de jogos, mas o foco está na produtividade e utilitário (jogo sem aplicativos). Consulte o [Introdução ao documento MonoGame](~/graphics-games/monogame/introduction/index.md) ou check-out [Visual Studio Tools for Unity](https://docs.microsoft.com/visualstudio/cross-platform/visual-studio-tools-for-unity) para obter diretrizes de desenvolvimento de jogos de plataforma cruzada.

A frase "de gravação-uma vez, executadas em qualquer lugar" geralmente é usada para exaltam as vantagens de uma única base de código que é executado sem modificações em várias plataformas. Enquanto ele tem a vantagem de reutilização de código, essa abordagem geralmente leva a aplicativos que têm um conjunto de recursos mais baixo do denominador comum e uma interface do usuário procurando genérico que não se ajustam bem em qualquer uma das plataformas de destino.

Xamarin não é apenas uma "gravação-uma vez, executadas em qualquer lugar" plataforma, porque uma das suas vantagens é a capacidade de implementar as interfaces de usuário nativo especificamente para cada plataforma. No entanto, com um design coerente ainda é possível compartilhar a maioria do usuário não código de interface e obter o melhor dos dois mundos: escrever seu código de lógica de negócios e armazenamento de dados uma vez e apresentar interfaces do usuário nativas em cada plataforma. Este documento descreve uma abordagem de arquitetura geral para alcançar essa meta.

Aqui está um resumo dos principais pontos para a criação de aplicativos de plataforma cruzada do Xamarin:

-   **Usar o c#** -gravar seus aplicativos em c#. Código existente escrito em c# pode ser movido para o iOS e Android usando o Xamarin facilmente e obviamente usado em aplicativos do Windows.
-   **Usar padrões de design MVC ou MVVM** -desenvolver a Interface do usuário do seu aplicativo usando o padrão de modelo/exibição/controlador. Projetar seu aplicativo usando uma abordagem de modelo/exibição/controlador ou uma abordagem de modelo/exibição/ViewModel onde há uma separação clara entre o modelo de"" e o restante. Determinar quais partes do seu aplicativo irá usar elementos de interface do usuário nativo de cada plataforma (iOS, Android, Windows, Mac) e usar isso como uma diretriz para dividir seu aplicativo em dois componentes: "Principal" e "Interface de usuário".
-   **Criar interfaces do usuário nativo** -cada aplicativo específicas do sistema operacional fornece uma camada de interface de usuário diferente (implementado no c# com a Ajuda das ferramentas de design de interface do usuário nativo):

1.  No iOS, use as APIs UIKit para criar aplicativos atraentes nativo, utilizando, opcionalmente, o designer do iOS do Xamarin para criar sua interface do usuário visualmente.
1.  No Android, use Android.Views para criar aplicativos atraentes nativo, aproveitando as vantagens do designer de interface do usuário do Xamarin.
1.  No Windows você usará XAML para a camada de apresentação, criada no designer de interface do usuário do Visual Studio ou de mesclagem.
1.  No Mac, você usará Storyboards para a camada de apresentação, criada no Xcode.

Xamarin. Forms projetos têm suporte em todas as plataformas e permitem que você crie interfaces do usuário que podem ser compartilhadas entre plataformas usando XAML xamarin. Forms. 

A quantidade de reutilização de código depende em grande parte quanto de código é mantida no núcleo compartilhado e quanto de código é a interface do usuário específico. O código básico é tudo o que não interagem diretamente com o usuário, mas em vez disso, fornece serviços para partes do aplicativo que irá coletar e exibir essas informações.

Para aumentar a quantidade de reutilização de código, você pode adotar componentes de plataforma cruzada que fornecem serviços comuns entre todos esses sistemas, como:

1.   [SQLite-net](https://www.nuget.org/packages/sqlite-net-pcl/) para armazenamento local do SQL,
1.   [Xamarin Plugins](https://xamarin.com/plugins) para acessar recursos específicos do dispositivo entre a câmera, contatos e localização geográfica,
1.   [Pacotes do NuGet](https://nuget.org) que são compatíveis com o Xamarin projetos, tais como [Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/),
1.  Usando recursos do .NET framework para a rede, serviços web, e/s e muito mais.


Alguns desses componentes são implementados o *Tasky* estudo de caso.

 <a name="Separate_Reusable_Code_into_a_Core_Library" />


## <a name="separate-reusable-code-into-a-core-library"></a>Separar o código reutilizável em uma biblioteca de núcleo

Seguindo o princípio de separação de responsabilidade dispondo em camadas de arquitetura de seu aplicativo e, em seguida, movendo funcionalidade básica que é independente de plataforma em uma biblioteca reutilizável core, você pode maximizar o compartilhamento entre plataformas, como a figura a seguir de código ilustra:

 ![](overview-images/layers2.png "Seguindo o princípio de separação de responsabilidade dispondo em camadas de arquitetura de seu aplicativo e, em seguida, movendo funcionalidade básica que é independente de plataforma em uma biblioteca reutilizável core, você pode maximizar o compartilhamento em plataformas de código")

 <a name="Case_Studies" />


## <a name="case-studies"></a>Estudos de caso

Há um estudo de caso que acompanha este documento – *Tasky Pro*. Cada estudo de caso descreve a implementação dos conceitos descritos neste documento em um exemplo do mundo real. O código é o código-fonte aberto e disponível em [github](https://github.com/xamarin/mobile-samples/).
