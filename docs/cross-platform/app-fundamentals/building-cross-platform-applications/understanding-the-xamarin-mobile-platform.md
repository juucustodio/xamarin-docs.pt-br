---
title: Parte 1 – noções básicas sobre a plataforma do Xamarin Mobile
description: Este documento descreve a plataforma Xamarin em um alto nível, examinando o processo de compilação, acesso ao SDK da plataforma, compartilhamento de código, criação de interface do usuário, designers visuais e muito mais.
ms.prod: xamarin
ms.assetid: FBCEF258-D3D8-A420-79ED-3AAB4A7308E4
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: e10e9f5330de3226fb0f08051ab135ea58900fe7
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016857"
---
# <a name="part-1--understanding-the-xamarin-mobile-platform"></a>Parte 1 – noções básicas sobre a plataforma do Xamarin Mobile

A plataforma Xamarin consiste em vários elementos que permitem desenvolver aplicativos para iOS e Android:

- idioma – permite que você use uma sintaxe familiar e recursos sofisticados, como genéricos, LINQ e a biblioteca de tarefas paralelas. **C#**
- **Mono .NET Framework** – fornece uma implementação de plataforma cruzada dos recursos abrangentes do .NET Framework da Microsoft.
- **Compilador** – dependendo da plataforma, produz um aplicativo nativo (por exemplo, iOS) ou um aplicativo .NET integrado e tempo de execução (por exemplo, Android). O compilador também executa muitas otimizações para implantação móvel, como a vinculação de código não utilizado.
- **Ferramentas IDE** – o Visual Studio no Mac e no Windows permite que você crie, compile e implante projetos do Xamarin.

Além disso, como a linguagem subjacente é C# com o .NET Framework, os projetos podem ser estruturados para compartilhar código que também pode ser implantado em Windows Phone.

## <a name="under-the-hood"></a>Nos bastidores

Embora o Xamarin permita que você escreva aplicativos C#e compartilhe o mesmo código em várias plataformas, a implementação real em cada sistema é muito diferente.

## <a name="compilation"></a>Compilação

A C# fonte deixa seu caminho em um aplicativo nativo de maneiras muito diferentes em cada plataforma:

- **Ios** – C# é compilado na AOT (ahead-of-Time) para a linguagem do assembly ARM. O .NET Framework está incluído, com classes não utilizadas sendo removidas durante a vinculação para reduzir o tamanho do aplicativo. A Apple não permite a geração de código em tempo de execução no iOS, portanto alguns recursos de linguagem não estão disponíveis (consulte [limitações do Xamarin. Ios](~/ios/internals/limitations.md) ).
- **Android** – C# é compilado para Il e empacotado com MonoVM + JIT'ing. As classes não utilizadas na estrutura são eliminadas durante a vinculação. O aplicativo é executado lado a lado com Java/arte (tempo de execução do Android) e interage com os tipos nativos por meio de JNI (Confira [limitações do Xamarin. Android](~/android/internals/limitations.md) ).
- O Windows C# – é compilado para Il e executado pelo tempo de execução interno e não requer ferramentas do Xamarin. A criação de aplicativos do Windows seguindo as diretrizes do Xamarin torna mais simples reutilizar o código no iOS e no Android.
  Observe que o Plataforma Universal do Windows também tem uma opção **.net Native** que se comporta da mesma forma que a compilação AOT do Xamarin. Ios.

A documentação do vinculador para [xamarin. Ios](~/ios/deploy-test/linker.md) e [xamarin. Android](~/android/deploy-test/linker.md) fornece mais informações sobre essa parte do processo de compilação.

Tempo de execução ' Compilation ' – gerando código dinamicamente com `System.Reflection.Emit` – deve ser evitado.

O kernel da Apple impede a geração de código dinâmico em dispositivos iOS, portanto, emitir código imediatamente não funcionará no Xamarin. iOS. Da mesma forma, os recursos de tempo de execução de linguagem dinâmica não podem ser usados com as ferramentas do Xamarin.

Alguns recursos de reflexão funcionam (por exemplo, O MonoTouch. Dialog o usa para a API de reflexão), apenas para a geração de código.

## <a name="platform-sdk-access"></a>Acesso ao SDK da plataforma

O Xamarin torna os recursos fornecidos pelo SDK específico da plataforma facilmente acessíveis com uma C# sintaxe familiar:

- **Ios** – Xamarin. Ios expõe as estruturas do SDK do Cocoatouch da Apple como namespaces dos C#quais você pode fazer referência. Por exemplo, a estrutura UIKit que contém todos os controles de interface do usuário pode ser incluída com uma simples instrução `using UIKit;`.
- **Android** – o Xamarin. Android expõe os SDK do Android do Google como namespaces, para que você possa fazer referência a qualquer parte do SDK com suporte com uma instrução using, como `using Android.Views;` para acessar os controles da interface do usuário.
- **Windows** – os aplicativos do Windows são criados usando o Visual Studio no Windows. Os tipos de projeto incluem Windows Forms, WPF, WinRT e Plataforma Universal do Windows (UWP).

## <a name="seamless-integration-for-developers"></a>Integração direta para desenvolvedores

A beleza do Xamarin é que, apesar das diferenças nos bastidores, o Xamarin. iOS e o Xamarin. Android (juntamente com SDKs do Windows da Microsoft) oferecem uma experiência C# simples para escrever código que pode ser reutilizado em todas as três plataformas.

A lógica de negócios, o uso do banco de dados, o acesso à rede e outras funções comuns podem ser gravados uma vez e reutilizados em cada plataforma, fornecendo uma base para interfaces de usuário específicas da plataforma que parecem e executam como aplicativos nativos.

## <a name="integrated-development-environment-ide-availability"></a>Disponibilidade do ambiente de desenvolvimento integrado (IDE)

O desenvolvimento do Xamarin pode ser feito no Visual Studio no Mac ou no Windows. O IDE escolhido será determinado pelas plataformas que você deseja direcionar.

Como os aplicativos do Windows só podem ser desenvolvidos no Windows, para compilar para iOS, Android _e_ Windows, é necessário ter o Visual Studio para Windows. No entanto, é possível compartilhar projetos e arquivos entre computadores Windows e Mac, portanto, aplicativos iOS e Android podem ser criados em um Mac e um código compartilhado pode ser adicionado posteriormente a um projeto do Windows.

Os requisitos de desenvolvimento para cada plataforma são discutidos em mais detalhes no guia de [requisitos](~/cross-platform/get-started/requirements.md) .

### <a name="ios"></a>iOS

O desenvolvimento de aplicativos iOS requer um computador Mac, executando o macOS. Você também pode usar o Visual Studio para escrever e implantar aplicativos iOS com o Xamarin no Visual Studio. No entanto, um Mac ainda é necessário para fins de criação e licenciamento.

O Xcode IDE da Apple deve ser instalado para fornecer o compilador e o simulador para teste. Você pode testar seus próprios dispositivos gratuitamente, mas para compilar aplicativos para distribuição ( [por](~/ios/get-started/installation/device-provisioning/free-provisioning.md)exemplo, a loja de aplicativos) você deve ingressar no programa de desenvolvedor da Apple (US $ $99 USD por ano). Cada vez que você envia ou atualiza um aplicativo, ele deve ser revisado e aprovado pela Apple antes de ser disponibilizado para os clientes baixarem.

O código é escrito com o IDE do Visual Studio e os layouts de tela podem ser criados programaticamente ou editados com o iOS designer do Xamarin em qualquer IDE.

Consulte o [Guia de instalação do Xamarin. Ios](~/ios/get-started/installation/index.md) para obter instruções detalhadas sobre como configurar.

### <a name="android"></a>Android

O desenvolvimento de aplicativos Android requer que os SDKs Java e Android sejam instalados. Eles fornecem o compilador, o emulador e outras ferramentas necessárias para compilar, implantar e testar. Java, as ferramentas do SDK do Android e do Xamarin da Google podem ser instaladas e executadas no Windows e no macOS. As seguintes configurações são recomendadas:

- Windows 10 com Visual Studio 2019
- macOS Mojave (10.11 +) com o Visual Studio 2019 para Mac

O Xamarin fornece um instalador unificado que irá configurar seu sistema com as ferramentas Java, Android e Xamarin de pré-requisito (incluindo um designer visual para layouts de tela). Consulte o [Guia de instalação do Xamarin. Android](~/android/get-started/installation/index.md) para obter instruções detalhadas.

Você pode criar e testar aplicativos em um dispositivo real sem qualquer licença do Google, no entanto, para distribuir seu aplicativo por meio de uma loja (como Google Play, Amazon ou Barnes &amp; Noble), uma taxa de registro pode ser paga para o operador. Google Play publicará seu aplicativo instantaneamente, enquanto os outros armazenamentos têm um processo de aprovação semelhante ao da Apple.

### <a name="windows"></a>Windows

Os aplicativos do Windows (WinForms, WPF ou UWP) são criados com o Visual Studio. Eles não usam o Xamarin diretamente. No entanto, C# o código pode ser compartilhado entre o Windows, o Ios e o Android.
Visite o [centro de desenvolvimento](https://developer.microsoft.com/) da Microsoft para saber mais sobre as ferramentas necessárias para o desenvolvimento do Windows.

## <a name="creating-the-user-interface-ui"></a>Criando a interface do usuário (IU)

Um dos principais benefícios do uso do Xamarin é que a interface do usuário do aplicativo usa controles nativos em cada plataforma, criando aplicativos que são indistinguíveis de um aplicativo escrito em Objective-C ou Java (para iOS e Android, respectivamente).

Ao criar telas em seu aplicativo, você pode dispor os controles no código ou criar telas completas usando as ferramentas de design disponíveis para cada plataforma.

### <a name="create-controls-programmatically"></a>Criar controles programaticamente

Cada plataforma permite que controles de interface do usuário sejam adicionados a uma tela usando código. Isso pode ser muito demorado, pois pode ser difícil visualizar o design concluído ao embutir coordenadas de pixels para posições de controle e tamanhos.

No entanto, criar controles de forma programática tem benefícios, especialmente em iOS para criar exibições que redimensionam ou renderizam de forma diferente nos tamanhos de tela iPhone e iPad.

### <a name="visual-designer"></a>Designer Visual

Cada plataforma tem um método diferente para dispor visualmente das telas:

- **Ios** – o Ios designer do Xamarin facilita a criação de exibições usando a funcionalidade e os campos de Propriedade do tipo "arrastar e soltar". Coletivamente, essas exibições compõem um storyboard e podem ser acessadas no **.** Arquivo de Storyboard que está incluído no seu projeto.
- **Android** – o Xamarin fornece um designer de interface do usuário do tipo "arrastar e soltar" do Android para Visual Studio. Os layouts de tela do Android são salvos como **. AXML** arquivos ao usar as ferramentas do Xamarin.
- **Windows** – a Microsoft fornece um designer de interface do usuário de arrastar e soltar no Visual Studio e no Blend. Os layouts de tela são armazenados como. Arquivos XAML.

Essas capturas de tela mostram os designers de telas visuais disponíveis em cada plataforma:

 [![](understanding-the-xamarin-mobile-platform-images/designer-all1.png "These screenshots show the visual screen designers available on each platform")](understanding-the-xamarin-mobile-platform-images/designer-all1.png#lightbox)

Em todos os casos, os elementos que você cria visualmente podem ser referenciados em seu código.

### <a name="user-interface-considerations"></a>Considerações sobre interface do usuário

Um dos principais benefícios de usar o Xamarin para criar aplicativos de plataforma cruzada é que eles podem tirar proveito dos kits de conhecimento da interface do usuário nativa para apresentar uma interface familiar para os usuários. A interface do usuário também será executada tão rápido quanto qualquer outro aplicativo nativo.

Algumas metáforas de interface do usuário funcionam em várias plataformas (por exemplo, todas as três plataformas usam um controle de lista de rolagem semelhante), mas, para que seu aplicativo fique à direita, a interface do usuário deve tirar proveito dos elementos da interface específica da plataforma, quando apropriado. Exemplos de metáforas de interface do usuário específicas da plataforma incluem:

- **Ios** – navegação hierárquica com botão de reversão suave, guias na parte inferior da tela.
- **Android** – hardware/sistema – botão voltar do software, menu Ação, guias na parte superior da tela.
- **Windows** – os aplicativos do Windows podem ser executados em desktops, tablets (como Microsoft Surface) e telefones. Os dispositivos Windows 10 podem ter o botão voltar do hardware e os blocos dinâmicos, por exemplo.

É recomendável que você leia as diretrizes de design relevantes para as plataformas para as quais você está se concentrando:

- **Ios** – [diretrizes de interface humana da Apple](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/MobileHIG/index.html)
- **Android** – [diretrizes de interface do usuário do Google](https://developer.android.com/guide/practices/ui_guidelines/index.html)
- **Windows** – [diretrizes de design da experiência do usuário para Windows](https://developer.microsoft.com/windows/design)

## <a name="library-and-code-re-use"></a>Reutilização de biblioteca e código

A plataforma Xamarin permite reutilização de código existente C# em todas as plataformas, bem como a integração de bibliotecas escritas nativamente para cada plataforma.

### <a name="c-source-and-libraries"></a>C#Origem e bibliotecas

Como os produtos Xamarin C# usam o .NET Framework, muitos códigos-fonte existentes (tanto de código-fonte aberto quanto de projetos internos) podem ser reutilizados em projetos Xamarin. Ios ou Xamarin. Android. Geralmente, a fonte pode ser simplesmente adicionada a uma solução Xamarin e funcionará imediatamente. Se um recurso do .NET Framework sem suporte tiver sido usado, alguns ajustes poderão ser necessários.

Exemplos de C# origem que podem ser usados em Xamarin. Ios ou Xamarin. Android incluem: SQLite-net, NEWTONSOFT. JSON e SharpZipLib.

### <a name="objective-c-bindings--binding-projects"></a>Associações Objective-C + projetos de associação

O Xamarin fornece uma ferramenta chamada *btouch* que ajuda a criar associações que permitem que bibliotecas de Objective-C sejam usadas em projetos do Xamarin. Ios. Consulte a [documentação sobre tipos Objective-C de associação](~/cross-platform/macios/binding/binding-types-reference.md) para obter detalhes sobre como isso é feito.

Exemplos de bibliotecas Objective-C que podem ser usadas no Xamarin. iOS incluem: verificação de código de barras RedLaser, Google Analytics e integração com o PayPal. As associações do Xamarin. iOS de código aberto estão disponíveis no [GitHub](https://github.com/mono/monotouch-bindings).

### <a name="jar-bindings--binding-projects"></a>Associações. jar + projetos de associação

O xamarin dá suporte ao uso de bibliotecas Java existentes no Xamarin. Android. Consulte a [documentação vinculando uma biblioteca Java](~/android/platform/binding-java-library/index.md) para obter detalhes sobre como usar um. Arquivo JAR do Xamarin. Android.

As associações do Xamarin. Android de código aberto estão disponíveis no [GitHub](https://github.com/mono/monodroid-bindings).

### <a name="c-via-pinvoke"></a>C via PInvoke

A tecnologia de "invocação de plataforma" (P/Invoke)C#permite que o código gerenciado () Chame métodos em bibliotecas nativas, bem como suporte para bibliotecas nativas para retornar ao código gerenciado.

Por exemplo, a biblioteca [SQLite-net](https://github.com/praeclarum/sqlite-net) usa instruções como esta:

```csharp
[DllImport("sqlite3", EntryPoint = "sqlite3_open", CallingConvention=CallingConvention.Cdecl)]
public static extern Result Open (string filename, out IntPtr db);
```

Isso é associado à implementação do SQLite nativo em C-Language no iOS e no Android.
Os desenvolvedores familiarizados com uma API C existente podem construir um C# conjunto de classes para mapear para a API nativa e utilizar o código de plataforma existente. Há documentação para [vincular bibliotecas nativas](~/ios/platform/native-interop.md) no Xamarin. Ios, os princípios semelhantes se aplicam ao Xamarin. Android.

### <a name="c-via-cppsharp"></a>C++via CppSharp

Miguel explica CXXI (agora chamado de [CppSharp](https://github.com/mono/CppSharp)) em seu [blog](https://tirania.org/blog/archive/2011/Dec-19.html). Uma alternativa para associar a uma C++ biblioteca diretamente é criar um invólucro C e associá-lo por meio de P/Invoke.
