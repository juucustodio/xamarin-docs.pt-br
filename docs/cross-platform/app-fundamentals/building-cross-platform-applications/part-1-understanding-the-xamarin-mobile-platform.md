---
title: "Parte 1 – Noções básicas sobre a plataforma Xamarin"
ms.topic: article
ms.prod: xamarin
ms.assetid: FBCEF258-D3D8-A420-79ED-3AAB4A7308E4
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 8b3908bcbf13063daced19c9d85b95bcd0a69c37
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="part-1--understanding-the-xamarin-mobile-platform"></a>Parte 1 – Noções básicas sobre a plataforma Xamarin

A plataforma de Xamarin consiste em um número de elementos que permitem que você desenvolva aplicativos para iOS e Android:

-   **Linguagem c#** – permite que você use uma sintaxe familiar e sofisticados recursos genéricos, como LINQ e a biblioteca paralela de tarefas.
-   **.NET framework mono** – fornece uma implementação de plataforma cruzada dos recursos abrangentes .NET framework do Microsoft.
-   **Compilador** – dependendo da plataforma, produz um aplicativo nativo (por exemplo. iOS) ou um aplicativo .NET integrado e o tempo de execução (por exemplo. Android). O compilador também executa muitas otimizações para implantação móvel como vinculação ausente código não usado.
-   **Ferramentas IDE** – o Visual Studio no Mac e Windows permite que você criar, construir e implantar projetos do Xamarin.


Além disso, como a linguagem subjacente é c# com o .NET framework, os projetos podem ser estruturados para compartilhar código também pode ser implantado para Windows Phone.

 <a name="Under_the_Hood" />


## <a name="under-the-hood"></a>Nos bastidores

Embora Xamarin permite escrever aplicativos em c# e compartilhar o mesmo código em várias plataformas, a implementação real em cada sistema é muito diferente.

 <a name="Compilation" />


## <a name="compilation"></a>Compilação

A origem c# faz seu caminho em um aplicativo nativo de maneiras muito diferentes em cada plataforma:

-   **iOS** – c# está à frente de tempo (AOT) compilado para a linguagem de assembly do ARM. O .NET framework é incluído, com classes não utilizados que estão sendo eliminados durante a vinculação para reduzir o tamanho do aplicativo. Apple não permite a geração de código em tempo de execução no iOS, portanto, alguns recursos de idioma não estão disponíveis (consulte [xamarin limitações](~/ios/internals/limitations.md) ).
-   **Android** – c# é compilado para IL e empacotado com MonoVM + JIT'ing. Classes do framework não utilizados são eliminados durante a vinculação. O aplicativo executa-lado a lado com Java/arte (tempo de execução Android) e interage com os tipos nativos via JNI (consulte [xamarin limitações](~/android/internals/limitations.md) ).
-   **Windows** – c# é compilado em IL e executado pelo tempo de execução interno e não requer ferramentas Xamarin. Diretrizes do seguinte Xamarin criando aplicativos do Windows torna muito mais simples de reutilizar o código no iOS e Android.
  Observe que a plataforma Universal do Windows também tem um **.NET Native** opção que se comporta de maneira semelhante à compilação de AOT dos xamarin.


A documentação de vinculador de [xamarin](~/ios/deploy-test/linker.md) e [xamarin](~/android/deploy-test/linker.md) fornece mais informações sobre essa parte do processo de compilação.

Tempo de execução 'compilação –' Gerar código dinamicamente com `System.Reflection.Emit` – deve ser evitado.

Núcleo da Apple evita a geração de código dinâmico em dispositivos iOS, portanto emitir código em interrupções não funciona em xamarin. Da mesma forma, os recursos do Dynamic Language Runtime não podem ser usados com as ferramentas Xamarin.

Alguns recursos de reflexão funcionam (por exemplo. MonoTouch.Dialog usa a API de reflexão), mas não a geração de código.

 <a name="Platform_SDK_Access" />


## <a name="platform-sdk-access"></a>Acesso SDK de plataforma

Xamarin faz com que os recursos fornecidos pelo SDK específico da plataforma facilmente acessível com a sintaxe familiar do c#:

-   **iOS** – xamarin expõe estruturas de CocoaTouch SDK da Apple como namespaces que você pode fazer referência de c#. Por exemplo, a estrutura de UIKit que contém todos os controles de interface de usuário pode ser incluída com um simples `using UIKit;` instrução.
-   **Android** – xamarin expõe Android SDK do Google como namespaces, portanto você pode fazer referência a qualquer parte do SDK com suporte ao uso de uma instrução, como `using Android.Views;` para acessar os controles de interface do usuário.
-   **Windows** – aplicativos do Windows é criada usando o Visual Studio no Windows. Tipos de projeto incluem Windows Forms, WPF, WinRT e o Windows UWP (plataforma Universal).


 <a name="Seamless_Integration_for_Developers" />


## <a name="seamless-integration-for-developers"></a>Integração perfeita para desenvolvedores

A vantagem do Xamarin é que apesar das diferenças nos bastidores, xamarin e xamarin (juntamente com os SDKs do Windows da Microsoft) oferecem uma experiência perfeita para escrever código em c# que pode ser reutilizado em todas as plataformas de três.

Lógica de negócios, uso de banco de dados, acesso à rede e outras funções comuns podem ser gravadas uma vez e reutilizadas em cada plataforma, fornecendo uma base para interfaces de usuário específica de plataforma que pesquisar e executar aplicativos nativos.

 <a name="Integrated_Development_Environment_(IDE)_Availability" />


## <a name="integrated-development-environment-ide-availability"></a>Disponibilidade do desenvolvimento integrado (IDE) do ambiente

Desenvolvimento do Xamarin pode ser feito no Visual Studio no Mac ou Windows. O IDE que você escolher será determinada pelas plataformas que você deseja direcionar.

Como os aplicativos do Windows só pode ser desenvolvida no Windows, a criação para iOS, Android, _e_ Windows requer o Visual Studio para Windows. No entanto é possível compartilhar arquivos entre computadores Windows e Mac, para aplicativos iOS e Android podem ser criados em um Mac e código compartilhado posteriormente pode ser adicionado a um projeto do Windows e projetos.

Os requisitos de desenvolvimento para cada plataforma são discutidos mais detalhadamente o [requisito](~/cross-platform/get-started/requirements.md) guia.


<a name="iOS" />

### <a name="ios"></a>iOS

Desenvolvimento de aplicativos do iOS requer um computador Mac, executando macOS. Você também pode usar o Visual Studio para gravar e implantar aplicativos iOS com o Xamarin no Visual Studio. No entanto, um Mac ainda é necessária para fins de licenciamento e de compilação.

Xcode IDE da Apple deve ser instalado para fornecer o compilador e o simulador para teste. Você pode testar em seus próprios dispositivos [gratuitamente](~/ios/get-started/installation/device-provisioning/free-provisioning.md), mas para compilar aplicativos para distribuição (por exemplo. Loja de aplicativos), você deve associar o programa para desenvolvedores da Apple (99 US $ por ano). Cada vez que você enviar ou atualiza um aplicativo, ele deve ser examinado e aprovado pela Apple antes que ele é disponibilizado para os clientes baixarem.

Código foi criado com o Visual Studio IDE e layouts de tela podem ser criados programaticamente ou editados com iOS do Xamarin Designer em um IDE.

Consulte o [guia de instalação do xamarin](~/ios/get-started/installation/index.md) para obter instruções sobre como configurá-lo.

<a name="Android" />

### <a name="android"></a>Android

Desenvolvimento de aplicativo do Android requer o Java e os SDKs do Android para ser instalado. Elas fornecem o compilador, o emulador e outras ferramentas necessárias para a criação, implantação e teste. Ferramentas Java, o SDK do Android do Google e do Xamarin podem todos ser instaladas e executadas nas seguintes configurações:

-  Mac OS X El Capitan e acima 10.11 (+) com o Visual Studio para Mac
-  Windows 7 & acima com Visual Studio 2015 ou 2017


Xamarin fornece um instalador unificado que irá configurar o sistema com o Java pré-requisito, Android e Xamarin ferramentas (incluindo um designer visual para layouts de tela). Consulte o [guia de instalação do xamarin](~/android/get-started/installation/index.md) para obter instruções detalhadas.

Você pode criar e testar aplicativos em um dispositivo real sem qualquer licença do Google, porém para distribuir seu aplicativo por meio de um repositório (como Google Play, Amazon ou Barnes &amp; Noble) uma taxa de registro pode ser a pagar para o operador. Google Play publicará o aplicativo imediatamente, enquanto os outros armazenamentos tem um processo de aprovação semelhante da Apple.

 <a name="Windows_Phone" />


### <a name="windows"></a>Windows

Aplicativos do Windows (WinForms, WPF ou UWP) são criados com o Visual Studio. Eles não usam Xamarin diretamente. No entanto, o código c# pode ser compartilhado em Windows, iOS e Android.
Visite da Microsoft [Centro de desenvolvimento](https://developer.microsoft.com/) para saber mais sobre as ferramentas necessárias para o desenvolvimento do Windows.

 <a name="Creating_the_User_Interface_(UI)" />


## <a name="creating-the-user-interface-ui"></a>Criando a Interface do usuário (IU)

Uma vantagem importante da usando Xamarin é que a interface de usuário do aplicativo usa controles nativos em cada plataforma, criação de aplicativos que podem ser diferenciados de um aplicativo escrito em Objective-C ou Java (iOS e Android respectivamente).

Ao criar telas no seu aplicativo, você pode dispor os controles no código ou criar telas completas usando as ferramentas de design disponíveis para cada plataforma.

 <a name="Programmatically_Create_Controls" />


### <a name="create-controls-programmatically"></a>Criar controles de forma programática

Cada plataforma permite controles da interface a ser adicionado a uma tela usando código de usuário. Isso pode ser muito demorado, pois pode ser difícil visualizar o design concluído quando codificar pixel coordenadas de posições de controle e tamanhos.

Programaticamente Criando controles tem benefícios, especialmente no iOS para a criação de exibições que redimensionar ou processam de maneira diferente em tamanhos de tela de iPhone e iPad.

 <a name="Visual_Designer" />


### <a name="visual-designer"></a>Designer Visual

Cada plataforma tem um método diferente para dispor visualmente telas:

-   **iOS** – iOS do Xamarin Designer facilita a criação de exibições usando campos de propriedade e funcionalidade de arrastar e soltar. Coletivamente essas exibições constituem um Storyboard e pode ser acessadas a **. Storyboard** arquivo que está incluído no projeto.
-   **Android** – Xamarin fornece um designer de interface do usuário de arrastar e soltar Android para o Visual Studio. Layouts de tela de Android serão salvas como **. AXML** arquivos ao usar as ferramentas Xamarin.
-   **Windows** – a Microsoft fornece um designer de interface do usuário de arrastar e soltar no Visual Studio e mesclagem. Os layouts de tela são armazenados como. Arquivos XAML.


Essas capturas de tela mostram os designers visuais tela disponíveis em cada plataforma:

 [ ![](part-1-understanding-the-xamarin-mobile-platform-images/designer-all1.png "Essas capturas de tela mostram os designers visuais tela disponíveis em cada plataforma")](part-1-understanding-the-xamarin-mobile-platform-images/designer-all1.png)

Em todos os casos, os elementos que você criar visualmente podem ser referenciados em seu código.

 <a name="User_Interface_Considerations" />


### <a name="user-interface-considerations"></a>Considerações da Interface do usuário

Um benefício principal de usar o Xamarin para criar aplicativos de plataforma cruzada é que eles podem aproveitar nativo kits de ferramentas de interface do usuário para apresentar uma interface familiar para o usuário. A interface do usuário também executará assim como qualquer outro aplicativo nativo.

Alguns metáforas de interface do usuário trabalhar em várias plataformas (por exemplo, todas as três plataformas usam um controle de lista de rolagem semelhante) mas para que seu aplicativo para 'sinta-se' direito a interface do usuário deve aproveitar usuário específico da plataforma elementos de interface quando apropriado. Exemplos de metáforas de interface do usuário específica de plataforma:

-   **iOS** – Navegação hierárquica com o botão Voltar flexível, as guias na parte inferior da tela.
-   **Android** – hardware/software de sistema back botão, o menu Ação, guias na parte superior da tela.
-   **Windows** – aplicativos do Windows pode executar em telefones, tablets (como Microsoft Surface) e áreas de trabalho. Dispositivos Windows 10 podem ter hardware botões Voltar e blocos dinâmicos, por exemplo.


É recomendável que você leia as diretrizes de design relevantes para as plataformas de que destino:

-   **iOS** – [diretrizes de Interface humana da Apple](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/MobileHIG/index.html)
-   **Android** – [diretrizes de Interface de usuário do Google](http://developer.android.com/guide/practices/ui_guidelines/index.html)
-   **Windows** – [diretrizes de Design de experiência do usuário do Windows](https://developer.microsoft.com/en-us/windows/design)


 <a name="Library_and_Code_Re-use" />


## <a name="library-and-code-re-use"></a>Biblioteca e reutilização de código

A plataforma Xamarin permite a reutilização de código c# existente em todas as plataformas, bem como a integração de bibliotecas escritas em modo nativo para cada plataforma.

 <a name="C#_Source_and_Libraries" />


### <a name="c-source-and-libraries"></a>Bibliotecas e código-fonte c#

Como produtos Xamarin usam o c# e o .NET framework, muito do código-fonte existente (ambos Abrir fonte e projetos internos) pode ser reutilizado em projetos do xamarin ou xamarin. Geralmente a fonte simplesmente pode ser adicionada a uma solução Xamarin e funcionará imediatamente. Se um recurso do framework .NET sem suporte foi usado, alguns ajustes poderão ser necessárias.

Exemplos de origem c# que pode ser usado em xamarin ou xamarin: SQLite NET, newtonsoft. JSON e SharpZipLib.

 <a name="Objective-C_Bindings_+_Binding_Projects" />


### <a name="objective-c-bindings--binding-projects"></a>Associações Objective-C + associação de projetos

Xamarin fornece uma ferramenta chamada *btouch* que ajuda a criar associações que permitem que bibliotecas Objective-C a ser usada em projetos do xamarin. Consulte o [documentação de associação de tipos de Objective-C](~/cross-platform/macios/binding/binding-types-reference.md) para obter detalhes sobre como fazer isso.

Exemplos de bibliotecas Objective-C que podem ser usadas em xamarin: código de barras RedLaser varredura, integração do Google Analytics e PayPal. Associações do código-fonte aberto xamarin estão disponíveis em [github](https://github.com/mono/monotouch-bindings).

 <a name=".jar_Bindings_+_Binding_Projects" />


### <a name="jar-bindings--binding-projects"></a>associações. jar + associação de projetos

Xamarin dá suporte ao uso de bibliotecas de Java existentes no xamarin. Consulte o [associando uma documentação de biblioteca Java](~/android/platform/binding-java-library/index.md) para obter detalhes sobre como usar um. Arquivo JAR do xamarin.

Associações do código-fonte aberto xamarin estão disponíveis em [github](https://github.com/mono/monodroid-bindings).

 <a name="C_via_PInvoke" />


### <a name="c-via-pinvoke"></a>C via PInvoke

Tecnologia "Plataforma Invoke" (P/Invoke) permite que o código gerenciado (c#) para chamar métodos em bibliotecas nativas, bem como suporte para bibliotecas nativas chamar no código gerenciado.

Por exemplo, o [NET SQLite](https://github.com/praeclarum/sqlite-net) biblioteca usa instruções como este:

```csharp
[DllImport("sqlite3", EntryPoint = "sqlite3_open", CallingConvention=CallingConvention.Cdecl)]
public static extern Result Open (string filename, out IntPtr db);
```

Isso vincula na implementação de linguagem C SQLite nativo no iOS e Android.
Os desenvolvedores familiarizados com uma API C existente podem construir um conjunto de classes do c# para mapear para a API nativa e utilizar o código de plataforma existente. Há documentação para [vinculação bibliotecas nativas](~/ios/platform/native-interop.md) em xamarin, os mesmos princípios se aplicam ao xamarin.

 <a name="C++_via_Cxxi" />


### <a name="c-via-cppsharp"></a>C++ via CppSharp

Miguel explica CXXI (agora chamado [CppSharp](https://github.com/mono/CppSharp)) em sua [blog](http://tirania.org/blog/archive/2011/Dec-19.html). É uma alternativa à associação diretamente para uma biblioteca C++ criar um wrapper de C e fazer a ligação por meio de P/Invoke.

