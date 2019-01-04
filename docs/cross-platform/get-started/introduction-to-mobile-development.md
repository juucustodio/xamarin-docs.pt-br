---
title: Introdução ao desenvolvimento móvel
description: Este documento oferece uma introdução ao desenvolvimento móvel, abordando o Xamarin, como ele funciona e os aplicativos que ele produz.
ms.prod: xamarin
ms.assetid: 33C83E13-F3E5-17B4-6512-207F3D3C5AB6
author: asb3993
ms.author: amburns
ms.date: 03/28/2017
ms.openlocfilehash: cefcc7084b2abab4af61f07ef1f33a4f4c363f69
ms.sourcegitcommit: 6e84adf7358dc05f4d888ab2674de70d88214090
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/31/2018
ms.locfileid: "53815198"
---
# <a name="introduction-to-mobile-development"></a>Introdução ao desenvolvimento móvel

Criar aplicativos móveis pode ser tão fácil quanto abrir o IDE, reunir alguns elementos, fazer alguns testes rápidos e enviar para uma App Store – tudo em uma tarde. Ou pode ser um processo extremamente complexo que envolve um design inicial rigoroso, teste de usabilidade, teste de garantia de qualidade em milhares de dispositivos, um ciclo de vida completo beta e, em seguida, implantação de várias maneiras diferentes.

Este documento destina-se a apresentar a plataforma do Xamarin. Para saber mais sobre o *processo* de compilar aplicativos móveis do design ao teste, consulte o documento [Introduction to the Mobile Software Development Lifecycle](~/cross-platform/get-started/introduction-to-mobile-sdlc.md) (Introdução ao ciclo de vida de desenvolvimento de software móvel).

Consulte nossos [requisitos de sistema](~/cross-platform/get-started/requirements.md#macos-requirements) para confirmar se você pode instalar o Xamarin.

## <a name="introduction-to-xamarin"></a>Introdução ao Xamarin

Ao considerar como compilar aplicativos iOS e Android, muitas pessoas pensam que as linguagens nativas, Objective-C, Swift e Java, são a única opção. No entanto, nos últimos anos, surgiu todo um novo ecossistema de plataformas para criar aplicativos móveis.

O Xamarin é exclusivo nesse espaço ao oferecer uma única linguagem (C#), biblioteca de classes e tempo de execução que funciona em todas as três plataformas móveis, iOS, Android e Windows Phone (a linguagem nativa do Windows Phone já é C#), enquanto ainda compila aplicativos nativos (não interpretada) que têm um desempenho alto o bastante mesmo para jogos exigentes.

Cada uma dessas plataformas tem um conjunto de recursos diferente e cada um varia em sua capacidade de escrever aplicativos nativos – ou seja, aplicativos compilados para código nativo e que interoperam de modo fluente com o subsistema Java subjacente. Por exemplo, algumas plataformas só permitem que aplicativos sejam compilados em HTML e JavaScript, enquanto outras são de nível muito baixo e só permitem código C/C++. Algumas plataformas nem sequer utilizam o kit de ferramentas de controle nativo.

O Xamarin é o único que combina toda a potência das plataformas nativas e adiciona vários recursos eficientes próprios, incluindo:

1.   **Associação completa para os SDKs subjacentes** – Xamarin contém associações para praticamente toda plataforma subjacente SDKs no iOS e Android. Além disso, essas associações são fortemente tipadas, o que significa que são fáceis de navegar e usar e oferecem uma verificação de tipo de tempo de build e durante o desenvolvimento. Isso resulta em menos erros de tempo de execução e aplicativos de qualidade mais alta.
1.   **Objective-C, Java, C e C++ Interop** – o Xamarin oferece recursos para invocar diretamente bibliotecas Objective-C, Java, C e C++, permitindo que você use uma ampla gama de código de terceiros já criado. Isso permite aproveitar as bibliotecas do iOS e Android existentes escritas em Objective-C, Java ou C/C++. Além disso, o Xamarin oferece projetos de associação que permitem associar facilmente bibliotecas Objective-C e Java nativas usando uma sintaxe declarativa.
1.   **Construções de Linguagem Moderna** – os aplicativos Xamarin são escritos em C#, uma linguagem moderna que inclui aprimoramentos significativos com relação a Objective-C e Java, como *Recursos de Linguagem Dinâmica*, *Construções Funcionais* como *Lambdas*, *LINQ*, recursos de *Programação Paralela*, *Genéricos* sofisticados e muito mais.
1.   **Incrível BCL (Biblioteca de Classes Base)** – aplicativos Xamarin usam a BCL do .NET, uma grande coleção de classes com recursos abrangente e simplificados, como suporte eficiente a XML, banco de dados, serialização, E/S, cadeia de caracteres e rede, para citar apenas alguns. Além disso, o código C# existente pode ser compilado para uso em aplicativos, o que dá acesso a milhares e milhares de bibliotecas que permitem executar ações que ainda não são cobertas pela BCL.
1.   **IDE (Ambiente de Desenvolvimento Integrado) Moderno** – o Xamarin usa o Visual Studio para Mac no Mac OS X e Visual Studio no Windows. Ambos são IDEs modernos que incluem recursos como preenchimento automático de código, um sofisticado sistema de gerenciamento de Projeto e Solução, uma biblioteca de modelos de projeto abrangente, controle do código-fonte integrado e muitos outros.
1.   **Suporte para Plataforma Cruzada Móvel** – o Xamarin oferece suporte sofisticado para plataforma cruzada para as três principais plataformas móveis: iOS, Android e Windows Phone. Os aplicativos podem ser escritos para compartilhar até 90% de seu código e nossa biblioteca Xamarin.Mobile oferece uma API unificada para acessar recursos comuns em todas as três plataformas. Isso pode reduzir significativamente os custos de desenvolvimento e o tempo para colocação no mercado para desenvolvedores móveis almejam as três plataformas móveis mais populares.


Devido ao conjunto de recursos eficiente e abrangente do Xamarin, ele fecha uma lacuna para desenvolvedores de aplicativos que desejam usar uma linguagem e plataforma modernas para desenvolver aplicativos móveis de plataforma cruzada.


> [!NOTE]
> Esta série de Introdução concentra-se em começar a compilar aplicativos iOS e Android. A Microsoft oferece informações sobre o [desenvolvimento da UWP (Plataforma Universal do Windows)](https://docs.microsoft.com/windows/uwp/develop/) para tablets e desktops. Para saber mais sobre o desenvolvimento multiplataforma com o Xamarin (incluindo aplicativos UWP para Windows), leia o [guia Criando aplicativos multiplataforma](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md).



## <a name="how-does-xamarin-work"></a>Como funciona o Xamarin?

O Xamarin oferece dois produtos comerciais: Xamarin.iOS e Xamarin.Android. Ambos são criados sobre *Mono*, uma versão do código-fonte de software livre do .NET Framework com base nos padrões publicados ECMA .NET. Mono já existe há quase tanto tempo quanto o próprio .NET Framework e é executado em quase todas as plataformas imagináveis, incluindo Linux, Unix, FreeBSD e Mac OS X.

No iOS, o Compilador *AOT* (*Ahead Of Time*) Xamarin compila aplicativos Xamarin.iOS diretamente para o código do assembly nativo do ARM. No Android, o compilador do Xamarin compila para *IL* (*Linguagem Intermediária*), que então é compilada *JIT* (*Just-in-Time*) para o assembly nativo quando o aplicativo é iniciado.

Em ambos os casos, os aplicativos Xamarin utilizam um tempo de execução que processa automaticamente elementos como alocação de memória, coleta de lixo, interoperabilidade de plataforma subjacente, etc.



### <a name="xamariniosdll-and-monoandroiddll"></a>Xamarin.iOS.dll e Mono.Android.dll

Aplicativos Xamarin são compilados em relação a um subconjunto BCL do .NET conhecido como Perfil Móvel do Xamarin. Este perfil foi criado especificamente para aplicativos móveis e empacotado no MonoTouch.dll e no Mono.Android.dll (para iOS e Android, respectivamente). Isso é muito semelhante ao modo como os aplicativos Silverlight (e Moonlight) são compilados com relação ao Perfil .NET do Moonlight/Silverlight. Na verdade, o perfil Móvel do Xamarin é equivalente ao perfil do Silverlight 4.0, com muitas classes BCL adicionadas de volta.

Para obter uma lista completa de classes e assemblies disponíveis, consulte a [Lista de Assemblies do Xamarin.iOS](~/cross-platform/internals/available-assemblies.md?context=xamarin/ios) e a [Lista de Assemblies do Xamarin.Android](~/cross-platform/internals/available-assemblies.md?context=xamarin/android)

Além da BCL, essas .dlls incluem invólucros para praticamente todo o SDK iOS e Android, o que permite que as APIs do SDK subjacentes sejam invocadas diretamente do C#.



### <a name="application-output"></a>Saída do aplicativo

Quando os aplicativos Xamarin são compilados, o resultado é um Pacote de Aplicativo, seja um arquivo .app em iOS ou um arquivo .apk no Android. Não é possível distinguir esses arquivos dos pacotes de aplicativos compilados com os IDEs padrão da plataforma e são implantáveis exatamente da mesma maneira.



## <a name="getting-started"></a>Guia de Introdução

Agora que você aprendeu um pouco sobre como o Xamarin funciona, é hora de ir mais fundo!

A próxima etapa é começar a compilar o aplicativo usando um destes guias:

* [**Hello, iOS**](~/ios/get-started/hello-ios/index.md)

![](introduction-to-mobile-development-images/ios.png "Hello, iOS")


* [**Hello, Android**](~/android/get-started/hello-android/index.md)

![](introduction-to-mobile-development-images/android.png "Hello, Android")


* [**Introdução ao Xamarin.Forms**](~/xamarin-forms/get-started/introduction-to-xamarin-forms.md)





## <a name="summary"></a>Resumo

Este documento simplesmente apresentou a plataforma Xamarin. A diversão de verdade começa quando seu primeiro aplicativo começa a funcionar. Confira os guias [Hello, iOS](~/ios/get-started/hello-ios/index.md), [Hello, Android](~/android/get-started/hello-android/index.md) e [Introdução ao Xamarin](~/xamarin-forms/get-started/introduction-to-xamarin-forms.md) para começar.


## <a name="related-links"></a>Links relacionados

- [Hello, iOS](~/ios/get-started/hello-ios/index.md)
- [Hello, Android](~/android/get-started/hello-android/index.md)
