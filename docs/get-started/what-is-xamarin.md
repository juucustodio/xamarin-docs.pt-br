---
title: O que é o Xamarin?
description: Este artigo apresenta Xamarin e bibliotecas relacionadas.
ms.prod: xamarin
ms.assetid: 33C83E13-F3E5-17B4-6512-207F3D3C5AB6
ms.custom: video
author: profexorgeek
ms.author: jusjohns
ms.date: 09/16/2019
no-loc:
- Xamarin
- Xamarin.Forms
- Xamarin.Android
- Xamarin.Essentials
- Xamarin.iOS
- Xamarin.Mac
ms.openlocfilehash: d4abb59cac117314239c669df454a786a3720ff5
ms.sourcegitcommit: 6f09bc2b760e76a61a854f55d6a87c4f421ac6c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/02/2020
ms.locfileid: "75607874"
---
# <a name="what-is-opno-locxamarin"></a>O que é o Xamarin?

[![capturas de tela do exemplo [! Parar. Aplicativo não LOC (Xamarin)] no iOS e Android](what-is-xamarin-images/xamarin-app-cropped.png)](what-is-xamarin-images/xamarin-app.png#lightbox)

Xamarin é uma plataforma de software livre para a criação de aplicativos modernos e de alto desempenho para iOS, Android e Windows com o .NET. Xamarin é uma camada de abstração que gerencia a comunicação de código compartilhado com o código de plataforma subjacente. o Xamarin é executado em um ambiente gerenciado que fornece conveniências, como alocação de memória e coleta de lixo.

Xamarin permite que os desenvolvedores compartilhem uma média de 90% de seu aplicativo entre plataformas. Esse padrão permite que os desenvolvedores gravem toda a lógica de negócios em uma única linguagem (ou reutilizem o código do aplicativo existente), mas alcancem o desempenho nativo, examinem e sintam-se em cada plataforma.

Xamarin aplicativos podem ser gravados em PC ou Mac e compilados em pacotes de aplicativos nativos, como um arquivo **. apk** no Android ou um arquivo **. ipa** no Ios.

> [!NOTE]
> A compilação e a implantação de aplicativos para iOS atualmente exigem um computador MacOS. Para obter mais informações sobre requisitos de desenvolvimento, consulte [requisitos do sistema](~/cross-platform/get-started/requirements.md#macos-requirements).

## <a name="who-opno-locxamarin-is-for"></a>Quem Xamarin é para

Xamarin é para os desenvolvedores com os seguintes objetivos:

- Compartilhe código, teste e lógica de negócios entre plataformas.
- Escreva aplicativos de plataforma cruzada C# no Visual Studio.

## <a name="how-opno-locxamarin-works"></a>Como Xamarin funciona

![Diagrama de [! Parar. Arquitetura não-LOC (Xamarin)]](what-is-xamarin-images/xamarin-architecture.png)

O diagrama mostra a arquitetura geral de um aplicativo Xamarin de plataforma cruzada. Xamarin permite que você crie uma interface do usuário nativa em cada plataforma e grave C# lógica de negócios no que é compartilhada entre plataformas. Na maioria dos casos, 80% do código do aplicativo é compartilhável usando Xamarin.

Xamarin se baseia no **mono**, uma versão de código aberto do .NET Framework com base nos padrões de ECMA do .net. O mono existe por tanto tempo quanto o .NET Framework em si e é executado na maioria das plataformas, incluindo Linux, UNIX, FreeBSD e macOS. O ambiente de execução mono manipula automaticamente tarefas como alocação de memória, coleta de lixo e interoperabilidade com plataformas subjacentes.

Para obter mais informações sobre a arquitetura específica da plataforma, consulte [Xamarin. Android](#xamarinandroid) e [Xamarin. Ios](#xamarinios).

### <a name="added-features"></a>Recursos adicionados

Xamarin combina as capacidades de plataformas nativas e adiciona vários recursos, incluindo:

1. **Ligação completa para os SDKs subjacentes** – Xamarin contém associações para quase todos os SDKs de plataforma subjacentes no Ios e no Android. Além disso, essas associações são fortemente tipadas, o que significa que são fáceis de navegar e usar e oferecem uma verificação de tipo de tempo de build e durante o desenvolvimento. As associações fortemente tipadas levam a menos erros de tempo de execução e aplicativos de maior qualidade.
1. **Objective-C, Java, C e C++ Interop** – Xamarin fornece recursos para invocar diretamente o Objective-c, Java, c C++ e bibliotecas, oferecendo a você o poder de usar uma ampla matriz de código de terceiros. Essa funcionalidade permite que você use bibliotecas iOS e Android existentes escritas em Objective-C, Java ou CC++/. Além disso, o Xamarin oferece projetos de associação que permitem associar bibliotecas de Objective-C e Java usando uma sintaxe declarativa.
1. **Construções de linguagem modernas** – Xamarin aplicativos são escritos em C#, uma linguagem moderna que inclui melhorias significativas em relação ao Objective-C e Java, como recursos de linguagem dinâmica, construções funcionais, como lambdas, LINQ, programação paralela, genéricos e muito mais.
1. **BCL (biblioteca de classes base) robusta** – Xamarin aplicativos usam a BCL do .net, uma grande coleção de classes que têm recursos abrangentes e simplificados, como XML poderoso, banco de dados, serialização, e/s, Cadeia de caracteres e suporte de rede e muito mais. O C# código existente pode ser compilado para uso em um aplicativo, que fornece acesso a milhares de bibliotecas que adicionam funcionalidade além da BCL.
1. **IDE (ambiente de desenvolvimento integrado) moderno** – o Xamarin usa o Visual Studio, um IDE moderno que inclui recursos como a conclusão automática de código, um sistema sofisticado de gerenciamento de projetos e soluções, uma biblioteca de modelos de projetos abrangente, um controle do código-fonte integrado e muito mais.
1. **Suporte de plataforma cruzada móvel** – o Xamarin oferece suporte sofisticado entre plataformas para as três principais plataformas do IOS, Android e Windows. Os aplicativos podem ser gravados para compartilhar até 90% de seu código e Xamarin. O Essentials oferece uma API unificada para acessar recursos comuns em todas as três plataformas. O código compartilhado pode reduzir significativamente os custos de desenvolvimento e o tempo de colocação no mercado para desenvolvedores móveis.

### <a name="opno-locxamarinandroid"></a>Xamarin. Android

[![[! Parar. Não-LOC (Xamarin)]. Diagrama de arquitetura do Android](what-is-xamarin-images/android-architecture-cropped.png)](what-is-xamarin-images/android-architecture.png#lightbox)

Xamarin. Os aplicativos Android são C# compilados da **Il (linguagem intermediária)** , que é **JIT (just-in-time)** compilada para um assembly nativo quando o aplicativo é iniciado. Xamarin. Os aplicativos Android são executados no ambiente de execução mono, lado a lado com a máquina virtual de tempo de execução do Android (arte). Xamarin fornece associações .NET para os namespaces Android. * e Java. *. O ambiente de execução mono chama esses namespaces por meio de **MCW (wrappers callable) gerenciados** e fornece **ACW (Android callable wrappers)** para a arte, permitindo que ambos os ambientes invoquem o código entre si.

Para obter mais informações, consulte [Xamarin. Arquitetura do Android](~/android/internals/architecture.md).

### <a name="opno-locxamarinios"></a>Xamarin. iOS

[![[! Parar. Não-LOC (Xamarin)]. diagrama de arquitetura do iOS](what-is-xamarin-images/ios-architecture-cropped.png)](what-is-xamarin-images/ios-architecture.png#lightbox)

os aplicativos Xamarin. iOS são totalmente em **tempo de vida (AOT)** compilados C# no código do assembly do ARM nativo. Xamarin usa **seletores** para expor Objective-c a C# Managed e **registradores** para expor C# código gerenciado para Objective-c. Os seletores e registradores coletivamente são chamados de "associações" e permitem o Objective C# -C e se comunicam.

Para obter mais informações, consulte a [arquiteturaXamarin. Ios](~/ios/internals/architecture.md).

### <a name="opno-locxamarinessentials"></a>Xamarin. Essenciais

Xamarin. O Essentials é uma biblioteca que fornece APIs de plataforma cruzada para recursos de dispositivo nativo. Como Xamarin si mesmo, Xamarin. O Essentials é uma abstração que simplifica o processo de acesso à funcionalidade nativa. Alguns exemplos de funcionalidade fornecida pelo Xamarin. O Essentials inclui:

- Informações do dispositivo
- Sistema de arquivos
- Acelerômetro
- Discagem telefônica
- Conversão de texto em fala
- Bloqueio de tela

Para obter mais informações, consulte [Xamarin. Conceitos básicos](~/essentials/index.md).

### <a name="opno-locxamarinforms"></a>Xamarin. Formas

Xamarin. O Forms é uma estrutura de interface do usuário de código-fonte aberto. Xamarin. Os formulários permitem que os desenvolvedores criem aplicativos iOS, Android e Windows a partir de uma única base de código compartilhada. Xamarin. Os formulários permitem que os desenvolvedores criem interfaces de usuário em XAML com C#code-behind no. Essas interfaces de usuário são renderizadas como controles nativos de alto desempenho em cada plataforma. Alguns exemplos de recursos fornecidos pelo Xamarin. Os formulários incluem:

- Idioma da interface do usuário XAML
- Associação de dados
- Gestos
- Efeitos
- {1&gt;Estilo&lt;1}

Para obter mais informações, consulte [Xamarin. Formulários](~/xamarin-forms/index.yml).

## <a name="get-started"></a>Introdução

Os guias a seguir ajudarão você a criar seu primeiro aplicativo usando Xamarin:

- [Introdução ao Xamarin. Formas](~/xamarin-forms/index.yml)
- [Introdução ao Xamarin. Android](~/android/index.yml)
- [Introdução ao Xamarin. iOS](~/ios/index.yml)
- [Introdução ao Xamarin. Mac](~/mac/index.yml)

## <a name="related-video"></a>Vídeo relacionado

> [!Video https://channel9.msdn.com/Series/Xamarin-101/What-is-Xamarin-1-of-11/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
