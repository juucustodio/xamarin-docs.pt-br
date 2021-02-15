---
title: O que é o Xamarin?
description: Este artigo apresenta a plataforma Xamarin e as bibliotecas relacionadas.
ms.prod: xamarin
ms.assetid: 33C83E13-F3E5-17B4-6512-207F3D3C5AB6
ms.custom: video
author: profexorgeek
ms.author: jusjohns
ms.date: 05/28/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8c89a71de12fcd21ee907b6478d7a616f0d5337f
ms.sourcegitcommit: 1f391667869a4541dd9b42d78862dc01d69ed160
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/08/2021
ms.locfileid: "99818053"
---
# <a name="what-is-xamarin"></a>O que é o Xamarin?

[![Capturas de tela do aplicativo Xamarin de exemplo no iOS e no Android](what-is-xamarin-images/xamarin-app-cropped.png)](what-is-xamarin-images/xamarin-app.png#lightbox)

O Xamarin é uma plataforma de software livre para a criação de aplicativos modernos e de alto desempenho para iOS, Android e Windows com o .NET. O Xamarin é uma camada de abstração que gerencia a comunicação de código compartilhado com o código de plataforma subjacente. O Xamarin é executado em um ambiente gerenciado que fornece conveniências, como alocação de memória e coleta de lixo.

O Xamarin permite que os desenvolvedores compartilhem uma média de 90% de seu aplicativo entre plataformas. Esse padrão permite que os desenvolvedores gravem toda a lógica de negócios em uma única linguagem (ou reutilizem o código do aplicativo existente), mas alcancem o desempenho e a aparência nativos em cada plataforma.

Os aplicativos Xamarin podem ser escritos em PC ou Mac e compilados em pacotes de aplicativos nativos, como um arquivo **.apk** no Android ou um arquivo **.ipa** no iOS.

> [!NOTE]
> Compilar e implantar aplicativos para iOS atualmente requer um computador MacOS. Para obter mais informações sobre requisitos de desenvolvimento, confira [requisitos do sistema](~/cross-platform/get-started/requirements.md#macos-requirements).

## <a name="who-xamarin-is-for"></a>Para quem é o Xamarin

O Xamarin é para os desenvolvedores com os objetivos a seguir:

- Compartilhe código, teste e lógica de negócios entre plataformas.
- Escreva aplicativos multiplataforma em C# no Visual Studio.

## <a name="how-xamarin-works"></a>Como o Xamarin funciona

![Diagrama da arquitetura do Xamarin](what-is-xamarin-images/xamarin-architecture.png)

O diagrama mostra a arquitetura geral de um aplicativo Xamarin multiplataforma. O Xamarin permite que você crie uma interface do usuário nativa em cada plataforma e escreva lógica de negócios, em C#, que é compartilhada entre plataformas. Na maioria dos casos, 80% do código do aplicativo são compartilháveis usando o Xamarin.

O Xamarin é criado com base no .NET, que manipula automaticamente tarefas como alocação de memória, coleta de lixo e interoperabilidade com plataformas subjacentes.

Para obter mais informações sobre a arquitetura específica da plataforma, confira [Xamarin.Android](#xamarinandroid) e [Xamarin.iOS](#xamarinios).

### <a name="added-features"></a>Recursos adicionados

O Xamarin combina as capacidades das plataformas nativas, ao mesmo tempo que adiciona recursos que incluem:

1. **Associação completa para os SDKs subjacentes** – o Xamarin contém associações para praticamente todos os SDKs de plataforma subjacente no iOS e Android. Além disso, essas associações são fortemente tipadas, o que significa que são fáceis de navegar e usar e oferecem uma verificação de tipo de tempo de build e durante o desenvolvimento. As associações fortemente tipadas levam a menos erros de runtime e aplicativos de maior qualidade.
1. **Objective-C, Java, C e C++ Interop** – o Xamarin oferece recursos para invocar diretamente bibliotecas Objective-C, Java, C e C++, permitindo que você use uma ampla gama de código de terceiros. Essa funcionalidade permite que você use as bibliotecas do iOS e Android existentes escritas em Objective-C, Java ou C/C++. Além disso, o Xamarin oferece projetos de associação que permitem associar bibliotecas Objective-C e Java nativas usando uma sintaxe declarativa.
1. **Construções de Linguagem Moderna** – os aplicativos Xamarin são escritos em C#, uma linguagem moderna que inclui aprimoramentos significativos com relação a Objective-C e Java, como *Recursos de Linguagem Dinâmica, constructos funcionais como lambdas, LINQ, programação paralela, genéricos e muito mais.
1. **BCL (Biblioteca de Classes Base) robusta** – os aplicativos Xamarin usam a BCL do .NET, uma grande coleção de classes com recursos abrangentes e simplificados, como suporte eficiente a XML, Banco de Dados, Serialização, E/S, Cadeia de Caracteres e Rede, e muito mais. O código C# existente pode ser compilado para uso em um aplicativo, que fornece acesso a milhares de bibliotecas que adicionam funcionalidade além da BCL.
1. **IDE (ambiente de desenvolvimento integrado) moderno** – o Xamarin usa o Visual Studio, um IDE moderno que inclui recursos como preenchimento automático de código, um sistema sofisticado de gerenciamento de projetos e soluções, uma biblioteca de modelos de projetos abrangente, um controle do código-fonte integrado e muito mais.
1. **Suporte multiplataforma móvel**: o Xamarin oferece suporte sofisticado multiplataforma para as três principais plataformas: iOS, Android e Windows. Os aplicativos podem ser gravados para compartilhar até 90% de seu código e Xamarin.Essentials oferecem uma API unificada para acessar recursos comuns em todas as três plataformas. O código compartilhado pode reduzir significativamente os custos de desenvolvimento e o tempo de colocação no mercado para desenvolvedores móveis.

### <a name="xamarinandroid"></a>Xamarin.Android

[![Diagrama de arquitetura do Xamarin. Android](what-is-xamarin-images/android-architecture-cropped.png)](what-is-xamarin-images/android-architecture.png#lightbox)

Os aplicativos Xamarin.Android compilam de C# para **IL (linguagem intermediária)** que, em seguida, passa por compilação **JIT (Just-In-Time)** em um assembly nativo quando o aplicativo é iniciado. Os aplicativos Xamarin.Android são executados no ambiente de execução Mono, lado a lado com a máquina virtual ART (Runtime do Android). O Xamarin fornece associações .NET para os namespaces Android.* e Java.*. O ambiente de execução Mono chama esses namespaces por meio de **MCW (Wrappers Chamáveis Gerenciados)** e fornece **ACW (Wrappers Chamáveis Android)** para o ART, permitindo que ambos os ambientes invoquem o código entre si.

Para obter mais informações, confira a [Arquitetura do Xamarin.Android](~/android/internals/architecture.md).

### <a name="xamarinios"></a>Xamarin.iOS

[![Diagrama de arquitetura do Xamarin. iOS](what-is-xamarin-images/ios-architecture-cropped.png)](what-is-xamarin-images/ios-architecture.png#lightbox)

Os aplicativos Xamarin.iOS são totalmente compilados **AOT (Ahead-of-Time)** de C# para o código do assembly ARM nativo. O Xamarin usa **Seletores** para expor Objective-C para C# gerenciado e **Registradores** para expor código C# gerenciado para Objective-C. Os seletores e registradores coletivamente são chamados de "associações" e permitem a comunicação de Objective-C e C#.

Para obter mais informações, confira a [Arquitetura do Xamarin.iOS](~/ios/internals/architecture.md).

### Xamarin.Essentials

Xamarin.Essentials é uma biblioteca que fornece APIs de plataforma cruzada para recursos de dispositivo nativo. Como o Xamarin, Xamarin.Essentials é uma abstração que simplifica o processo de acesso à funcionalidade nativa. Alguns exemplos de funcionalidade fornecida pelo Xamarin.Essentials incluem:

- Informações do dispositivo
- Sistema de arquivos
- Acelerômetro
- Discagem telefônica
- Conversão de texto em fala
- Bloqueio de tela

Para obter mais informações, confira [Xamarin.Essentials](~/essentials/index.md).

### Xamarin.Forms

Xamarin.Forms é uma estrutura de interface do usuário de código-fonte aberto. Xamarin.Forms permite que os desenvolvedores criem aplicativos Xamarin. iOS, Xamarin. Android e Windows a partir de uma única base de código compartilhada. Xamarin.Forms permite que os desenvolvedores criem interfaces de usuário em XAML com code-behind em C#. Essas interfaces do usuário são renderizadas como controles nativos de alto desempenho em cada plataforma. Alguns exemplos de recursos fornecidos pelo Xamarin.Forms incluem:

- Idioma da interface do usuário XAML
- Associação de dados
- Gestos
- Efeitos
- Estilo

Para obter mais informações, confira [Xamarin.Forms](~/xamarin-forms/index.yml).

## <a name="get-started"></a>Introdução

Os guias a seguir ajudarão você a criar seu primeiro aplicativo usando o Xamarin:

- [Introdução ao Xamarin.Forms](~/xamarin-forms/index.yml)
- [Introdução ao Xamarin.Android](~/android/index.yml)
- [Introdução ao Xamarin.iOS](~/ios/index.yml)
- [Introdução ao Xamarin.Mac](~/mac/index.yml)

## <a name="related-video"></a>Vídeo relacionado

> [!Video https://channel9.msdn.com/Series/Xamarin-101/What-is-Xamarin-1-of-11/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
