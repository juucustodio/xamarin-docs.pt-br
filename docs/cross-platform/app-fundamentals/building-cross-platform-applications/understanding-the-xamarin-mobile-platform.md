---
title: Parte 1 – Noções básicas sobre a plataforma Xamarin Mobile
description: Este documento descreve a plataforma Xamarin em um alto nível, observando o processo de compilação, acesso SDK de plataforma, compartilhamento de código, criação de interface do usuário, designers visuais e muito mais.
ms.prod: xamarin
ms.assetid: FBCEF258-D3D8-A420-79ED-3AAB4A7308E4
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: c7c0f582ac4a7dc8571fbc607dba9b0ad97d49e1
ms.sourcegitcommit: 58d8bbc19ead3eb535fb8248710d93ba0892e05d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67674840"
---
# <a name="part-1--understanding-the-xamarin-mobile-platform"></a>Parte 1 – Noções básicas sobre a plataforma Xamarin Mobile

A plataforma Xamarin consiste em um número de elementos que permitem que você desenvolva aplicativos para iOS e Android:

-   **C#linguagem** – permite que você use uma sintaxe familiar e recursos sofisticados, como genéricos, LINQ e a biblioteca paralela de tarefas.
-   **Mono .NET framework** – fornece uma implementação de plataforma cruzada dos recursos abrangentes do .NET framework da Microsoft.
-   **Compilador** – dependendo da plataforma, produz um aplicativo nativo (por exemplo. iOS) ou um aplicativo integrado do .NET e o tempo de execução (por exemplo. Android). O compilador também faz muitas otimizações para implantação móvel como vinculação do código não utilizado ausente.
-   **Ferramentas IDE** – o Visual Studio no Mac e Windows permite que você criar, compilar e implantar projetos do Xamarin.

Além disso, como a linguagem subjacente é C# com o .NET framework, os projetos podem ser estruturados para compartilhar o código que pode ser implantado para Windows Phone.

## <a name="under-the-hood"></a>Nos bastidores

Embora o Xamarin permite que você escreva aplicativos em C#e compartilhar o mesmo código em várias plataformas, a implementação real em cada sistema é muito diferente.

## <a name="compilation"></a>Compilação

O C# código-fonte faz seu caminho em um aplicativo nativo de maneiras muito diferentes em cada plataforma:

-   **iOS** – C# é ahead of time (AOT) compilado em linguagem de assembly do ARM. O .NET framework está incluído, as classes não utilizadas são eliminadas durante a vinculação para reduzir o tamanho do aplicativo. A Apple não permite a geração de código em tempo de execução no iOS, portanto, alguns recursos de linguagem não estão disponíveis (consulte [xamarin. IOS limitações](~/ios/internals/limitations.md) ).
-   **Android** – C# é compilada para IL e empacotados com o MonoVM + JIT'ing. Classes não utilizadas do framework são eliminadas, durante a vinculação. O aplicativo é executado side-by-side com Java/arte (tempo de execução do Android) e interage com os tipos nativos por meio de JNI (consulte [xamarin. Android limitações](~/android/internals/limitations.md) ).
-   **Windows** – C# é compilada para IL e executados pelo tempo de execução interno e não requer ferramentas Xamarin. Criação de aplicativos do Windows diretrizes do seguinte Xamarin torna mais simples de reutilizar o código no iOS e Android.
  Observe que a plataforma Universal do Windows também tem um **.NET Native** opção que se comporta da mesma forma, a compilação de AOT dos xamarin. IOS.


A documentação do vinculador [xamarin. IOS](~/ios/deploy-test/linker.md) e [xamarin. Android](~/android/deploy-test/linker.md) fornece mais informações sobre essa parte do processo de compilação.

Tempo de execução 'compilação' – geração de código dinâmica com `System.Reflection.Emit` – deve ser evitado.

Kernel da Apple impede a geração de código dinâmico em dispositivos iOS, portanto a emissão de código em interrupções não funcionará no xamarin. IOS. Da mesma forma, os recursos do Dynamic Language Runtime não podem ser usados com as ferramentas Xamarin.

Fazer o trabalho (por exemplo, alguns recursos de reflexão MonoTouch usado para a API de reflexão), mas não a geração de código.

## <a name="platform-sdk-access"></a>Acesso do SDK de plataforma

Xamarin torna os recursos fornecidos pelo SDK específica da plataforma facilmente acessível com familiar C# sintaxe:

-   **iOS** – xamarin. IOS expõe estruturas de CocoaTouch SDK da Apple, como namespaces que você pode fazer referência de C#. Por exemplo, a estrutura UIKit que contém todos os controles da interface do usuário pode ser incluída com um simples `using UIKit;` instrução.
-   **Android** – xamarin. Android expõe o SDK do Android do Google como namespaces, portanto, você pode fazer referência a qualquer parte do SDK com suporte com o uso de uma instrução, como `using Android.Views;` para acessar os controles de interface do usuário.
-   **Windows** – aplicativos do Windows são criados usando o Visual Studio no Windows. Tipos de projeto incluem o Windows Forms, WPF, WinRT e Universal Windows Platform (UWP).

## <a name="seamless-integration-for-developers"></a>Integração perfeita para desenvolvedores

A beleza do Xamarin é que, apesar das diferenças nos bastidores, xamarin. IOS e xamarin. Android (juntamente com os SDKs do Windows da Microsoft) oferecem uma experiência perfeita para gravação C# código que pode ser reutilizado em todas as três plataformas.

Lógica de negócios, o uso do banco de dados, o acesso à rede e outras funções comuns podem ser gravadas uma vez e reutilizadas em cada plataforma, proporcionando uma base para interfaces de usuário específicas à plataforma que aparência e desempenho de aplicativos nativos.

## <a name="integrated-development-environment-ide-availability"></a>Disponibilidade do desenvolvimento integrado (IDE) do ambiente

Desenvolvimento do Xamarin pode ser feito no Visual Studio no Mac ou Windows. O IDE que você escolha será determinada pelas plataformas que você deseja direcionar.

Porque os aplicativos do Windows só podem ser desenvolvidos no Windows, para compilar para iOS, Android, _e_ Windows exige o Visual Studio para Windows. No entanto é possível compartilhar projetos e arquivos entre computadores Windows e Mac, para que aplicativos iOS e Android podem ser criados em um Mac e código compartilhado posteriormente poderia ser adicionado a um projeto do Windows.

Os requisitos de desenvolvimento para cada plataforma são discutidos mais detalhadamente os [requisito](~/cross-platform/get-started/requirements.md) guia.

### <a name="ios"></a>iOS

Desenvolvimento de aplicativos do iOS requer um computador Mac, que executam o macOS. Você também pode usar o Visual Studio para escrever e implantar aplicativos iOS com o Xamarin no Visual Studio. No entanto, um Mac ainda é necessário para fins de licenciamento e de compilação.

IDE do Xcode da Apple deve ser instalado para fornecer o compilador e o simulador para teste. Você pode testar em seus próprios dispositivos [gratuitamente](~/ios/get-started/installation/device-provisioning/free-provisioning.md), mas a criação de aplicativos para distribuição (por exemplo. a Store do aplicativo), você deve associar o programa de desenvolvedores da Apple (US $ 99 por ano). Sempre que você envia ou atualiza um aplicativo, ele deve ser revisado e aprovado pela Apple antes que ele esteja disponível para download.

Código é escrito com o Visual Studio IDE e layouts de tela podem ser criados programaticamente ou editados com o Designer em IDE do iOS do Xamarin.

Consulte a [guia de instalação do xamarin. IOS](~/ios/get-started/installation/index.md) para obter instruções detalhadas sobre como configurá-lo.

### <a name="android"></a>Android

Desenvolvimento de aplicativos Android requer o Java e os SDKs do Android para ser instalado. Eles fornecem o compilador, o emulador e outras ferramentas necessárias para a criação, implantação e teste. Ferramentas Java, o SDK do Android do Google e do Xamarin podem todos ser instaladas e executadas no Windows e macOS. As configurações a seguir são recomendadas:

- Windows 10 com o Visual Studio de 2019
- macOS Mojave (10.11 e posteriores) com o Visual Studio de 2019 para Mac

O Xamarin fornece um instalador unificado que irá configurar seu sistema com o pré-requisito Java, Android e Xamarin ferramentas (incluindo um designer visual para layouts de tela). Consulte a [guia de instalação do xamarin. Android](~/android/get-started/installation/index.md) para obter instruções detalhadas.

Você pode criar e testar aplicativos em um dispositivo real sem qualquer licença do Google, porém para distribuir seu aplicativo por meio de um repositório (como Google Play, Amazon ou Barnes &amp; Noble) uma taxa de registro pode ser pagos para o operador. Google Play publicará seu aplicativo instantaneamente, enquanto os outros armazenamentos têm um processo de aprovação, semelhante da Apple.

### <a name="windows"></a>Windows

Aplicativos do Windows (WinForms, WPF ou UWP) são criados com o Visual Studio. Eles não usam Xamarin diretamente. No entanto, C# código pode ser compartilhado entre o Windows, iOS e Android.
Visite da Microsoft [Centro de desenvolvimento](https://developer.microsoft.com/) para saber mais sobre as ferramentas necessárias para o desenvolvimento do Windows.

## <a name="creating-the-user-interface-ui"></a>Criando a Interface do usuário (IU)

Dos principais benefícios de usar o Xamarin é que a interface do usuário de aplicativo usa controles nativos em cada plataforma, criação de aplicativos que são difíceis de diferenciar de um aplicativo escrito em Java ou Objective-C (para iOS e Android, respectivamente).

Ao criar telas em seu aplicativo, você pode dispor os controles no código ou criar telas completas usando as ferramentas de design disponíveis para cada plataforma.

### <a name="create-controls-programmatically"></a>Criar controles de forma programática

Cada plataforma permite que usuário controles da interface a ser adicionado a uma tela usando código. Isso pode ser muito demorado, pois pode ser difícil visualizar do design concluído quando o pixel embutir coordenadas para tamanhos e posições de controle.

Criando controles de forma programática tem benefícios, particularmente no iOS para a criação de exibições que redimensionar ou processam de forma diferente entre os tamanhos de tela do iPhone e iPad.

### <a name="visual-designer"></a>Designer Visual

Cada plataforma tem um método diferente para dispor visualmente telas:

- **iOS** – Designer do iOS do Xamarin facilita a criação de exibições usando arrastar e soltar campos de propriedade e funcionalidade. Coletivamente, essas exibições compõem um Storyboard e podem ser acessadas no **. Storyboard** arquivo que está incluído em seu projeto.
- **Android** – o Xamarin fornece um designer de interface do usuário de arrastar e soltar Android para Visual Studio. Layouts de tela do Android são salvos como **. AXML** arquivos quando usar as ferramentas Xamarin.
- **Windows** – a Microsoft fornece um designer de interface do usuário de arrastar e soltar no Visual Studio e no Blend. Os layouts de tela são armazenados como. Arquivos XAML.

Essas capturas de tela mostram os designers de tela visual disponíveis em cada plataforma:

 [![](understanding-the-xamarin-mobile-platform-images/designer-all1.png "Essas capturas de tela mostram os designers de tela visual disponíveis em cada plataforma")](understanding-the-xamarin-mobile-platform-images/designer-all1.png#lightbox)

Em todos os casos, os elementos que você crie visualmente podem ser referenciados em seu código.

### <a name="user-interface-considerations"></a>Considerações da Interface do usuário

Dos principais benefícios de usar o Xamarin para compilar aplicativos de plataforma cruzada é que eles podem tirar proveito dos kits de ferramentas de interface do usuário nativa para apresentar uma interface familiar ao usuário. A interface do usuário também executará tão rápido quanto qualquer outro aplicativo nativo.

Alguns metáforas de interface do usuário funcionam em várias plataformas (por exemplo, todas as três plataformas usam um controle de lista de rolagem semelhante), mas para que seu aplicativo para 'se sentir' a interface do usuário deve aproveitar usuário específicas à plataforma elementos de interface quando apropriado. Exemplos de metáforas de interface do usuário específico da plataforma:

- **iOS** – guias de Navegação hierárquica com o botão Voltar reversível, na parte inferior da tela.
- **Android** – hardware/software de sistema de back-botão, o menu de ação, as guias na parte superior da tela.
- **Windows** – aplicativos do Windows podem ser executados em desktops, tablets (por exemplo, o Microsoft Surface) e telefones. Dispositivos Windows 10 podem ter hardware botões Voltar e blocos dinâmicos, por exemplo.

É recomendável que você leia as diretrizes de design relevantes para as plataformas de que destino:

- **iOS** – [diretrizes de Interface humana da Apple](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/MobileHIG/index.html)
- **Android** – [as diretrizes de Interface do usuário do Google](https://developer.android.com/guide/practices/ui_guidelines/index.html)
- **Windows** – [diretrizes de Design de experiência do usuário para Windows](https://developer.microsoft.com/windows/design)

## <a name="library-and-code-re-use"></a>Biblioteca e reutilização de código

A plataforma Xamarin permite a reutilização de existente C# código em todas as plataformas, bem como a integração de bibliotecas escritas em modo nativo para cada plataforma.

### <a name="c-source-and-libraries"></a>C#Código-fonte e bibliotecas

Como usam os produtos Xamarin C# e o .NET framework, muito do código-fonte existente (ambos de código aberto projetos internos) pode ser reutilizado em projetos do xamarin. IOS ou xamarin. Android. Muitas vezes a fonte pode simplesmente ser adicionada a uma solução do Xamarin e ele funcionará imediatamente. Se um recurso do framework .NET sem suporte foi usado, alguns ajustes podem ser necessárias.

Exemplos de C# código-fonte que pode ser usado no xamarin. IOS ou xamarin. Android incluem: SQLite-NET, newtonsoft. JSON e SharpZipLib.

### <a name="objective-c-bindings--binding-projects"></a>Associações do Objective-C + projetos de associação

O Xamarin fornece uma ferramenta chamada *btouch* que ajuda a criar as associações que permitem que bibliotecas Objective-C a ser usado em projetos do xamarin. IOS. Consulte a [documentação de tipos de associação de Objective-C](~/cross-platform/macios/binding/binding-types-reference.md) para obter detalhes sobre como isso é feito.

Exemplos de bibliotecas de Objective-C que podem ser usadas no xamarin. IOS: Código de barras RedLaser varredura, integração do Google Analytics e PayPal. Associações do xamarin. IOS de software livre estão disponíveis no [github](https://github.com/mono/monotouch-bindings).

### <a name="jar-bindings--binding-projects"></a>. jar associações + projetos de associação

Xamarin dá suporte ao uso de bibliotecas Java existentes no xamarin. Android. Consulte a [associando uma documentação biblioteca Java](~/android/platform/binding-java-library/index.md) para obter detalhes sobre como usar um. Arquivo JAR do xamarin. Android.

Associações do xamarin. Android de software livre estão disponíveis no [github](https://github.com/mono/monodroid-bindings).

### <a name="c-via-pinvoke"></a>C por meio do PInvoke

A tecnologia de "Platform Invoke" (P/Invoke) permite que o código gerenciado (C#) para chamar métodos em bibliotecas nativas, bem como suporte para as bibliotecas nativas retornar a chamada para código gerenciado.

Por exemplo, o [SQLite-NET](https://github.com/praeclarum/sqlite-net) biblioteca usa instruções como esta:

```csharp
[DllImport("sqlite3", EntryPoint = "sqlite3_open", CallingConvention=CallingConvention.Cdecl)]
public static extern Result Open (string filename, out IntPtr db);
```

Isso vincula à implementação nativa da linguagem C SQLite em iOS e Android.
Os desenvolvedores familiarizados com uma API de C existente podem construir um conjunto de C# classes de mapear para a API nativa e utilizar o código de plataforma existentes. Documentação sobre [vinculação a bibliotecas nativas](~/ios/platform/native-interop.md) no xamarin. IOS, os princípios semelhantes se aplicam a xamarin. Android.

### <a name="c-via-cppsharp"></a>C++ via CppSharp

Miguel explica CXXI (agora chamado de [CppSharp](https://github.com/mono/CppSharp)) em seu [blog](https://tirania.org/blog/archive/2011/Dec-19.html). É uma alternativa à associação diretamente para uma biblioteca C++ criar um wrapper de C e fazer a ligação via P/Invoke.
