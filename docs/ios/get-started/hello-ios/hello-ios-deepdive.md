---
title: Hello, iOS – Aprofundamento
description: Este documento faz uma análise mais detalhada do aplicativo de exemplo Olá, iOS, considerando sua arquitetura, a interface do usuário, a hierarquia de exibição de conteúdo, os testes, a implantação e muito mais.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: 61ba3a7e-fe11-4439-8bc8-9809512b8eff
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 10/05/2018
ms.openlocfilehash: 4b9043d70bb7460abf62c964da8041f345cd1be6
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435612"
---
# <a name="hello-ios--deep-dive"></a>Hello, iOS – Aprofundamento

O passo a passo de início rápido, introduziu os procedimentos para compilar e executar um aplicativo básico do Xamarin.iOS. Agora é hora de desenvolver uma compreensão mais profunda de como funcionam os aplicativos iOS para que você possa criar programas mais sofisticados. Este guia examina as etapas no passo a passo Hello, iOS para habilitar a compreensão dos conceitos fundamentais do desenvolvimento de aplicativos iOS.

Este guia ajudará você a desenvolver as habilidades e o conhecimento necessário para criar um aplicativo iOS de uma única tela. Depois que trabalhar com ele, você deverá compreender as diferentes partes de um aplicativo Xamarin.iOS e como elas se encaixam.

::: zone pivot="macos"

## <a name="introduction-to-visual-studio-for-mac"></a>Introdução ao Visual Studio para Mac

O Visual Studio para Mac é um IDE gratuito de software livre que combina funcionalidades do Visual Studio e do XCode. Ele conta com um designer visual totalmente integrado, um editor de texto complementado com ferramentas de refatoração, um navegador de assembly, integração de código-fonte e muito mais. Este guia apresenta alguns recursos básicos do Visual Studio para Mac, mas, se você for novo no Visual Studio para Mac, confira a documentação [Visual Studio para Mac](/visualstudio/mac/).

Visual Studio para Mac segue a prática do Visual Studio de organizar o código em *soluções* e *projetos*. Uma solução é um contêiner que pode conter um ou mais projetos. Um projeto pode ser um aplicativo (por exemplo, iOS ou Android), uma biblioteca com suporte, um aplicativo de teste e muito mais. No aplicativo Phoneword, um novo projeto de iPhone foi adicionado usando o modelo de **Aplicativo de exibição única**. A solução inicial tinha esta aparência:

![Uma captura de tela da solução inicial](hello-ios-deepdive-images/image30.png)

::: zone-end
::: zone pivot="windows"

## <a name="introduction-to-visual-studio"></a>Introdução ao Visual Studio

O Visual Studio é um IDE avançado da Microsoft. Ele conta com um designer visual totalmente integrado, um editor de texto complementado com ferramentas de refatoração, um navegador de assembly, integração de código-fonte e muito mais. Este guia apresenta algumas funcionalidades básicas do Visual Studio com as Ferramentas de Xamarin para Visual Studio.

O Visual Studio organiza o código em soluções e projetos. Uma solução é um contêiner que pode conter um ou mais projetos. Um projeto pode ser um aplicativo (por exemplo, iOS ou Android), uma biblioteca com suporte, um aplicativo de teste e muito mais. No aplicativo Phoneword, um novo projeto de iPhone foi adicionado usando o modelo de **Aplicativo de exibição única**. A solução inicial tinha esta aparência:

![Uma captura de tela da solução inicial](hello-ios-deepdive-images/vs-image30.png)

::: zone-end

## <a name="anatomy-of-a-xamarinios-application"></a>Anatomia de um aplicativo Xamarin.iOS

::: zone pivot="macos"

À esquerda está o **painel de solução**, que contém a estrutura de diretório e todos os arquivos associados à solução:

![O Painel de soluções, que contém a estrutura de diretórios e todos os arquivos associados à solução](hello-ios-deepdive-images/image31.png)

::: zone-end
::: zone pivot="windows"

À direita está o **painel solução**, que contém a estrutura de diretório e todos os arquivos associados à solução:

![O Painel da solução, que contém a estrutura de diretórios e todos os arquivos associados à solução](hello-ios-deepdive-images/vs-image31.png)

::: zone-end

Nas instruções [Hello, Ios](~/ios/get-started/hello-ios/hello-ios-quickstart.md) , você criou uma solução chamada **Phoneword** e colocou um projeto do IOS- **Phoneword_iOS** -dentro dele. Os itens dentro do projeto incluem:

- **Referências** -contém os assemblies necessários para compilar e executar o aplicativo. Expanda o diretório para ver referências a assemblies do .NET como [System](/dotnet/api/system), System.Core e [System.Xml](/dotnet/api/system.xml), bem como uma referência ao assembly Xamarin.iOS.
- **Pacotes** – o diretório de pacotes contém pacotes do NuGet prontos.
- **Recursos** – a pasta de recursos armazena outras mídias.
- **Main.cs** – isso contém o ponto de entrada principal do aplicativo. Para iniciar o aplicativo, o nome da classe principal do aplicativo, a `AppDelegate`, é passado.
- **AppDelegate.cs** – esse arquivo contém a classe principal do aplicativo e é responsável por criar a janela, criar a interface do usuário e escutar eventos do sistema operacional.
- **Main.Storyboard** – o storyboard contém o design visual da interface do usuário do aplicativo. Arquivos de storyboard abertos em um editor gráfico chamado Designer iOS.
- **ViewController.cs** – o controlador de exibição alimenta a tela (exibição) que um usuário vê e toca. O controlador de exibição é responsável por lidar com as interações entre o usuário e a exibição.
- **ViewController.designer.cs** – o `designer.cs` é um arquivo gerado automaticamente que serve como a cola entre os controles na exibição e suas representações de código no controlador de exibição. Como esse é um arquivo de conexão interna, o IDE substituirá todas as alterações manuais e, na maioria das vezes, esse arquivo poderá ser ignorado. Para obter mais informações sobre a relação entre o Designer visual e o código subjacente, consulte o guia [Introdução ao Designer do iOS](~/ios/user-interface/designer/introduction.md).
- **Info.plist** – **Info.plist** é onde as propriedades do aplicativo, como o nome do aplicativo, ícones, imagens de inicialização, entre outros, são definidos. Esse é um arquivo poderoso e uma introdução completa sobre ele está disponível no guia [Como trabalhar com listas de propriedades](~/ios/app-fundamentals/property-lists.md).
- **Entitlements.plist** – a lista de propriedades de direito nos permite especificar as *funcionalidades* do aplicativo (também chamadas de tecnologias de repositório de aplicativo) como iCloud, PassKit e muito mais. Mais informações sobre **Entitlements.plist** podem ser encontradas no guia [Como trabalhar com listas de propriedades](~/ios/app-fundamentals/property-lists.md). Para obter uma introdução geral a direitos, consulte o guia [Provisionamento de dispositivos](~/ios/get-started/installation/device-provisioning/index.md).

## <a name="architecture-and-app-fundamentals"></a>Conceitos básicos de aplicativo e arquitetura

Antes que um aplicativo iOS possa carregar uma interface do usuário, duas coisas precisam estar prontas. Primeiro, o aplicativo precisa definir um *ponto de entrada* – o primeiro código que é executado quando o processo do aplicativo é carregado na memória. Em segundo lugar, é necessário definir uma classe para manipular eventos em todo o aplicativo e interagir com o sistema operacional.

Esta seção estuda as relações ilustradas no diagrama a seguir:

[![As relações dos conceitos básicos de arquitetura e aplicativo estão ilustradas neste diagrama](hello-ios-deepdive-images/image32.png)](hello-ios-deepdive-images/image32.png#lightbox)

### <a name="main-method"></a>Método Principal

O ponto de entrada principal de um aplicativo iOS é a classe `Application`. A classe `Application` é definida no arquivo **Main.cs** e contém um método `Main` estático. Ele cria uma nova instância do aplicativo Xamarin.iOS e passa o nome da classe *Delegado do aplicativo* que manipulará eventos do sistema operacional. O código de modelo do método estático `Main` aparece abaixo:

```csharp
using System;
using UIKit;

namespace Phoneword_iOS
{
    public class Application
    {
        static void Main (string[] args)
        {
            UIApplication.Main (args, null, "AppDelegate");
        }
    }
}
```

### <a name="application-delegate"></a>Delegado do aplicativo

No iOS, a classe *Delegado do aplicativo* manipula eventos de sistema; essa classe reside dentro de **AppDelegate.cs**. A classe `AppDelegate` gerencia a *janela* do aplicativo. A Janela é uma única instância da classe `UIWindow`, que serve como um contêiner para a interface do usuário. Por padrão, um aplicativo obtém apenas uma janela na qual carregar seu conteúdo e a Janela é anexada a uma *tela* (instância única de `UIScreen`) que fornece o retângulo delimitador correspondente às dimensões da tela do dispositivo físico.

O *AppDelegate* também é responsável por assinar atualizações do sistema sobre eventos do aplicativo importantes, por exemplo, quando a inicialização do aplicativo é concluída ou quando a memória é insuficiente.

O código do modelo para AppDelegate é apresentado abaixo:

```csharp
using System;
using Foundation;
using UIKit;

namespace Phoneword_iOS
{

    [Register ("AppDelegate")]
    public partial class AppDelegate : UIApplicationDelegate
    {
        public override UIWindow Window {
            get;
            set;
        }

        ...
    }
}
```

Depois que o aplicativo define sua janela, ele pode começar a carregar a interface do usuário. A próxima seção explora a criação da interface do usuário.

## <a name="user-interface"></a>Interface do usuário

A interface do usuário de um aplicativo iOS é como uma vitrine – o aplicativo normalmente obtém uma Janela, mas ele pode preencher a janela com o número de objetos que precisar e as disposições e objetos podem ser alterados dependendo do que o aplicativo deseja exibir. Os objetos nesse cenário – as coisas que o usuário vê – são chamados de exibições. Para criar uma única tela em um aplicativo, as exibições são empilhadas umas sobre as outras em uma *hierarquia de exibição de conteúdo*e a hierarquia é gerenciada por um único controlador de exibição. Aplicativos com várias telas têm várias hierarquias de exibição de conteúdo, cada uma com seu próprio controlador de exibição; o aplicativo coloca as exibições na janela para criar uma hierarquia de exibição de conteúdo diferente com base na tela na qual o usuário está.

Esta seção aprofunda-se na interface do usuário, descrevendo as exibições, hierarquias exibição de conteúdo e o Designer do iOS.

### <a name="ios-designer-and-storyboards"></a>Designer do iOS e storyboards

O Designer do iOS é uma ferramenta visual para criar interfaces do usuário no Xamarin. O Designer pode ser iniciado clicando duas vezes em qualquer arquivo do storyboard (.storyboard), que será aberto em uma exibição semelhante à seguinte captura de tela:

::: zone pivot="macos"

![Interface do Designer do iOS](hello-ios-deepdive-images/image33.png)

Um *Storyboard* é um arquivo que contém os designs visuais das telas do nosso aplicativo, bem como as transições e as relações entre as telas. A representação da tela de um aplicativo em um Storyboard é chamada de _cena_. Cada cena representa um controlador de exibição e a pilha de exibições que ele gerencia (hierarquia de exibição de conteúdo). Quando um novo projeto de **aplicativo de exibição única** é criado a partir de um modelo, Visual Studio para Mac gera automaticamente um arquivo de storyboard chamado `Main.storyboard` e o preenche com uma única cena, conforme ilustrado pela captura de tela abaixo:

![O Visual Studio para Mac gera automaticamente um arquivo do Storyboard chamado Main.storyboard e o preenche com uma única cena](hello-ios-deepdive-images/image34.png)

A barra preta na parte inferior da tela do storyboard pode ser selecionada para escolher o controlador de exibição da cena. O controlador de exibição é uma instância da classe `UIViewController` que contém o código subjacente para a hierarquia de exibição de conteúdo. As propriedades nesse controlador de exibição podem ser exibidas e definidas dentro do **painel de propriedades**, conforme ilustrado pela captura de tela abaixo:

![O Painel Propriedades](hello-ios-deepdive-images/image35.png)

::: zone-end
::: zone pivot="windows"

![Interface do Designer do iOS](hello-ios-deepdive-images/vs-image33.png)

Um *Storyboard* é um arquivo que contém os designs visuais das telas do nosso aplicativo, bem como as transições e as relações entre as telas. A representação da tela de um aplicativo em um Storyboard é chamada de _cena_. Cada cena representa um controlador de exibição e a pilha de exibições que ele gerencia (hierarquia de exibição de conteúdo). Quando um novo projeto de **aplicativo de exibição única** é criado a partir de um modelo, o Visual Studio gera automaticamente um arquivo de storyboard chamado `Main.storyboard` e o preenche com uma única cena, conforme ilustrado pela captura de tela abaixo:

![O Visual Studio gera automaticamente um arquivo do Storyboard chamado Main.storyboard e o preenche com uma única cena](hello-ios-deepdive-images/vs-image34.png)

A barra na parte inferior da tela do storyboard pode ser selecionada para escolher o controlador de exibição da cena. O controlador de exibição é uma instância da classe `UIViewController` que contém o código subjacente para a hierarquia de exibição de conteúdo. As propriedades nesse controlador de exibição podem ser exibidas e definidas dentro do **painel Propriedades**, conforme ilustrado pela captura de tela abaixo:

![O Painel Propriedades](hello-ios-deepdive-images/vs-image35.png)

::: zone-end

A _Exibição_ pode ser selecionada clicando dentro da parte branca da cena. A exibição é uma instância da classe `UIView` que define uma área da tela e fornece interfaces para trabalhar com o conteúdo nessa área. A exibição padrão é uma única *exibição raiz* que preenche toda a tela do dispositivo.

À esquerda da cena há uma seta cinza com um ícone de sinalizador, conforme ilustrado pela captura de tela abaixo:

 [![Uma seta cinza com um ícone de bandeira](hello-ios-deepdive-images/image37.png)](hello-ios-deepdive-images/image37.png#lightbox)

A seta cinza representa uma transição de storyboard chamada *Segue* (pronuncia-se "seg-uêi"). Como este segue não tem nenhuma origem, ele é chamado de *Segue sem origem*. Um segue sem origem aponta para a primeira cena cujas exibições são carregadas na janela do aplicativo na inicialização do aplicativo. A cena e as exibições dentro dela serão a primeira coisa que o usuário verá quando o aplicativo for carregado.

Ao criar uma interface do usuário, modos de exibição adicionais podem ser arrastados da **caixa de ferramentas** para a exibição principal na superfície de design, conforme ilustrado pela captura de tela abaixo:

::: zone pivot="macos"

![As exibições adicionais podem ser arrastadas da caixa de ferramentas para a exibição principal na superfície de design](hello-ios-deepdive-images/image38.png)

::: zone-end
::: zone pivot="windows"

![As exibições adicionais podem ser arrastadas da caixa de ferramentas para a exibição principal na superfície de design](hello-ios-deepdive-images/vs-image38.png)

::: zone-end

Essas exibições adicionais são chamadas de *subexibições*. Juntas, a exibição raiz e as subexibições fazem parte de uma *hierarquia de exibição de conteúdo* que é gerenciada pelo `ViewController` . O contorno de todos os elementos na cena pode ser exibido examinando-o no bloco de **Tópicos do documento** :

::: zone pivot="macos"

![O painel de Estrutura de Tópicos do Documento](hello-ios-deepdive-images/image39.png)

::: zone-end
::: zone pivot="windows"

![O painel de Estrutura de Tópicos do Documento](hello-ios-deepdive-images/vs-image39.png)

::: zone-end

As subexibições são realçadas no diagrama a seguir:

::: zone pivot="macos"

![As subexibições são realçadas no diagrama](hello-ios-deepdive-images/image40.png)

::: zone-end
::: zone pivot="windows"

![As subexibições são realçadas no diagrama](hello-ios-deepdive-images/vs-image40.png)

::: zone-end

A próxima seção divide a hierarquia de exibição de conteúdo representado por essa cena.

## <a name="content-view-hierarchy"></a>Hierarquia de exibição de conteúdo

Uma _hierarquia de exibição de conteúdo_ é uma pilha de exibições e subexibições gerenciadas por um único controlador de exibição, conforme ilustrado pelo diagrama a seguir:

 [![A hierarquia de exibição de conteúdo](hello-ios-deepdive-images/image41.png)](hello-ios-deepdive-images/image41.png#lightbox)

Podemos tornar mais fácil a visualização da hierarquia de exibição de conteúdo de nosso `ViewController` alterando temporariamente a cor da tela de fundo da exibição raiz para amarelo na seção de Exibição do **Painel Propriedades**, conforme ilustrado pela captura de tela abaixo:

::: zone pivot="macos"

![Alterar a cor da tela de fundo da exibição raiz para amarelo na seção de exibição do Painel de Propriedades](hello-ios-deepdive-images/image42.png)

::: zone-end
::: zone pivot="windows"

![Alterar a cor da tela de fundo da exibição raiz para amarelo na seção de exibição do Painel de Propriedades](hello-ios-deepdive-images/vs-image42.png)

::: zone-end

O diagrama a seguir ilustra as relações entre a janela, exibições, subexibições e controlador de exibição que levam a interface do usuário para a tela do dispositivo:

[![As relações entre a janela, as exibições, as subexibições e o controlador de exibição](hello-ios-deepdive-images/image43.png)](hello-ios-deepdive-images/image43.png#lightbox)

A próxima seção discute como trabalhar com exibições no código e aprender programação voltada à interação do usuário usando os controladores de exibição e o ciclo de vida da exibição.

## <a name="view-controllers-and-the-view-lifecycle"></a>Controladores de exibição e ciclo de vida da exibição

Cada hierarquia de exibição de conteúdo tem um controlador de exibição correspondente para promover a interação do usuário. A função do controlador de exibição é gerenciar as exibições na hierarquia de exibição de conteúdo. O controlador de exibição não é parte da hierarquia de exibição de conteúdo e não é um elemento na interface. Em vez disso, ele fornece o código que promove as interações do usuário com os objetos na tela.

### <a name="view-controllers-and-storyboards"></a>Controladores de exibição e storyboards

::: zone pivot="macos"

O controlador de exibição é representado em um storyboard como uma barra na parte inferior da cena. Selecionar o controlador de exibição exibe suas propriedades no **Painel de Propriedades**:

![Ao selecionar o controlador de exibição, as propriedades são exibidas no Painel Propriedades](hello-ios-deepdive-images/image44.png)

Uma classe de controlador de exibição personalizada para a hierarquia de exibição de conteúdo representada por essa cena pode ser definida editando-se a propriedade de **classe** na seção de **identidade** do **painel de propriedades**. Por exemplo, nosso aplicativo **Phoneword** define o `ViewController` como o controlador de exibição para nossa primeira tela, conforme ilustrado pela captura de tela abaixo:

![O aplicativo Phoneword define o ViewController como o controlador de exibição](hello-ios-deepdive-images/image45new.png)

::: zone-end
::: zone pivot="windows"

O controlador de exibição é representado em um storyboard como uma barra na parte inferior da cena. A seleção do controlador de exibição traz suas propriedades no **painel Propriedades**:

![Ao selecionar o controlador de exibição, as propriedades são exibidas no Painel Propriedades](hello-ios-deepdive-images/vs-image44.png)

Uma classe de controlador de exibição personalizada para a hierarquia de exibição de conteúdo representada por essa cena pode ser definida por meio da edição da propriedade **Classe**, na seção **Identidade** do **Painel Propriedades**. Por exemplo, nosso aplicativo **Phoneword** define o `ViewController` como o controlador de exibição para nossa primeira tela, conforme ilustrado pela captura de tela abaixo:

![O aplicativo Phoneword define o ViewController como o controlador de exibição](hello-ios-deepdive-images/vs-image45.png)

::: zone-end

Isso vincula a representação do storyboard do controlador de exibição para a classe C# `ViewController`. Abra o arquivo `ViewController.cs` e observe que o controlador de exibição é uma *subclasse* de `UIViewController`, conforme ilustrado pelo código a seguir:

```csharp
public partial class ViewController : UIViewController
{
    public ViewController (IntPtr handle) : base (handle)
    {

    }
}
```

O `ViewController` agora conduz as interações da hierarquia de exibição de conteúdo associadas a esse controlador de exibição no storyboard. Em seguida, você aprenderá sobre a função do controlador de exibição de gerenciar as exibições por meio da introdução de um processo chamado ciclo de vida de exibição.

> [!NOTE]
> Para telas puramente visuais que não exigem interação do usuário, a propriedade **Classe** pode ser deixada em branco no **Painel Propriedades**. Isso define a classe subjacente do controlador de exibição como a implementação padrão de um `UIViewController`, o que é apropriado se você não planeja adicionar código personalizado.

### <a name="view-lifecycle"></a>Ciclo de vida de exibição

O controlador de exibição é responsável por carregar e descarregar hierarquias de exibição de conteúdo da janela. Quando algo importante acontece em uma exibição na hierarquia de exibição de conteúdo, o sistema operacional notifica o controlador de exibição por meio de eventos no ciclo de vida de exibição. Substituindo métodos do ciclo de vida de exibição, você pode interagir com os objetos na tela e criar uma interface do usuário dinâmica e responsiva.

Estes são os métodos de ciclo de vida básicos e suas funções:

- **ViewDidLoad** – chamado  *uma vez* na primeira vez que o controlador de exibição carrega sua hierarquia de exibição de conteúdo na memória. Esse é um bom lugar para realizar a configuração inicial, porque é quando as subexibições se tornam disponíveis no código pela primeira vez.
- **ViewWillAppear** – chamado sempre que o modo de exibição de um controlador de exibição está prestes a ser adicionado a uma hierarquia de exibição de conteúdo e aparece na tela.
- **ViewWillDisappear** – chamado sempre que o modo de exibição de um controlador de exibição está prestes a ser removido de uma hierarquia de exibição de conteúdo e desaparece da tela. Esse evento de ciclo de vida é usado para limpeza e para salvar estados.
- **ViewDidAppear** e **ViewDidDisappear** – chamados quando uma exibição é adicionada ou removida da hierarquia de exibição de conteúdo, respectivamente.

Quando o código personalizado é adicionado a qualquer estágio do ciclo de vida, a *implementação base* desse método de ciclo de vida deve ser *substituída*. Isso é realizado usando-se para derivação o método de ciclo de vida existente (que já tem algum código anexado a ele) e estendendo-o com código adicional. A implementação base é chamada de dentro do método para certificar-se de que o código original é executado antes do novo código. Um exemplo disso é demonstrado na próxima seção.

Para obter mais informações sobre como trabalhar com controladores de exibição, consulte o [Guia de programação do controlador de exibição da Apple para IOS](https://developer.apple.com/library/archive/featuredarticles/ViewControllerPGforiPhoneOS/index.html#//apple_ref/doc/uid/TP40007457-CH2-SW1) e a referência de [UIViewController](https://developer.apple.com/documentation/uikit/uiviewcontroller?language=objc).

### <a name="responding-to-user-interaction"></a>Respondendo à interação do usuário

A função mais importante do controlador de exibição é responder a interações do usuário como pressionamentos de botão, navegação e muito mais. A maneira mais simples de manipular a interação do usuário é conectar um controle para escutar a entrada do usuário e anexar um manipulador de eventos para responder à entrada. Por exemplo, um botão pode ser conectado para responder a um evento de toque, conforme demonstrado no aplicativo Phoneword.

Vamos explorar como isso funciona.
No projeto `Phoneword_iOS`, um botão chamado `TranslateButton` foi adicionado à hierarquia de exibição de conteúdo:

[![Um botão chamado TranslateButton foi adicionado à hierarquia de exibição de conteúdo](hello-ios-deepdive-images/image1.png)](hello-ios-deepdive-images/image1.png#lightbox)

Quando um **Name** é atribuído ao controle **Button** no **Painel Propriedades**, o Designer do iOS o mapeia automaticamente para um controle no **ViewController.designer.cs**, tornando o `TranslateButton` disponível dentro da classe `ViewController`. Controles serão disponibilizados primeiro no estágio `ViewDidLoad` do ciclo de vida de exibição, portanto, esse método de ciclo de vida é usado para responder ao toque do usuário:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // wire up TranslateButton here
}
```

O aplicativo Phoneword usa um evento de toque chamado `TouchUpInside` para ouvir o toque do usuário. `TouchUpInside` escuta um evento toque para cima (levantando o dedo para fora da tela) que ocorre após um toque para baixo (tocando o dedo na tela) dentro dos limites do controle. O oposto de `TouchUpInside` é o evento `TouchDown`, que é acionado quando o usuário pressiona um controle. O evento `TouchDown` captura muito ruído e não dá ao usuário nenhuma opção de cancelar a interatividade ao deslizar o dedo para fora do controle. `TouchUpInside` é a maneira mais comum de responder a um toque no **botão** e cria a experiência que o usuário espera ter ao pressionar um botão. Mais informações sobre isso estão disponíveis nas [Diretrizes de Interface Humana iOS](https://developer.apple.com/library/ios/documentation/userexperience/conceptual/MobileHIG/index.html) da Apple.

O aplicativo tratou o `TouchUpInside` evento com um lambda, mas também seria possível usar um manipulador de evento nomeado ou delegado. O código do último botão era semelhante ao seguinte:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();
    string translatedNumber = "";

    TranslateButton.TouchUpInside += (object sender, EventArgs e) => {
      translatedNumber = Core.PhonewordTranslator.ToNumber(PhoneNumberText.Text);
      PhoneNumberText.ResignFirstResponder ();

      if (translatedNumber == "") {
        CallButton.SetTitle ("Call", UIControlState.Normal);
        CallButton.Enabled = false;
      } else {
        CallButton.SetTitle ("Call " + translatedNumber, UIControlState.Normal);
        CallButton.Enabled = true;
      }
  };
}
```

## <a name="additional-concepts-introduced-in-phoneword"></a>Conceitos adicionais introduzidos no Phoneword

O aplicativo Phoneword introduziu vários conceitos não abordados neste guia. Esses conceitos incluem:

- **Alterar o texto do botão** – o aplicativo Phoneword demonstrou como alterar o texto de um **botão** chamando `SetTitle` no **botão** e passando o novo texto e o _estado do controle_ do **botão**. Por exemplo, o código a seguir altera o texto do CallButton para “Call”:

    ```csharp
    CallButton.SetTitle ("Call", UIControlState.Normal);
    ```

- **Habilitar e desabilitar botões** – **botões** podem estar em um estado `Enabled` ou `Disabled`. Um **botão** desabilitado não responderá à entrada do usuário. Por exemplo, o seguinte código desabilita o `CallButton`:

    ```csharp
    CallButton.Enabled = false;
    ```

    Para obter mais informações sobre botões, veja o guia [Botões](~/ios/user-interface/controls/buttons.md).

- **Ignorar o teclado** – quando a usuário toca no campo de texto, o iOS exibe o teclado para permitir que o usuário insira a entrada. Infelizmente, não há nenhuma funcionalidade interna para ignorar o teclado. O código a seguir é adicionado ao `TranslateButton` para ignorar o teclado quando o usuário pressiona `TranslateButton`:

    ```csharp
    PhoneNumberText.ResignFirstResponder ();
    ```

    Para ver outro exemplo de como ignorar o teclado, veja o guia [Ignorar o teclado](https://github.com/xamarin/recipes/tree/master/Recipes/ios/input/keyboards/dismiss_the_keyboard).

- **Realizar chamada telefônica com URL** – no aplicativo Phoneword, um esquema de URL da Apple é usado para inicializar o aplicativo de telefone do sistema. O esquema de URL personalizado consiste em uma "tel:" prefixo e o número de telefone traduzido, conforme ilustrado pelo código a seguir:

    ```csharp
    var url = new NSUrl ("tel:" + translatedNumber);
    if (!UIApplication.SharedApplication.OpenUrl (url))
    {
        // show alert Controller
    }
    ```

- **Mostrar um alerta** – quando um usuário tenta fazer uma chamada telefônica em um dispositivo que não dá suporte a chamadas – por exemplo, o simulador ou um iPod Touch – uma caixa de diálogo de alerta é exibida para permitir que o usuário saiba que a chamada telefônica não pode ser colocada. O código a seguir cria e popula um controlador de alerta:

    ```csharp
    if (!UIApplication.SharedApplication.OpenUrl (url)) {
                    var alert = UIAlertController.Create ("Not supported", "Scheme 'tel:' is not supported on this device", UIAlertControllerStyle.Alert);
                    alert.AddAction (UIAlertAction.Create ("Ok", UIAlertActionStyle.Default, null));
                    PresentViewController (alert, true, null);
                }
    ```

    Para obter mais informações sobre exibições de alertas do iOS, consulte a [receita do controlador de alerta](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/alertcontroller).

## <a name="testing-deployment-and-finishing-touches"></a>Testes, implantação e toques finais

Ambos o Visual Studio para Mac e o Visual Studio oferecem várias opções para testar e implantar um aplicativo. Esta seção aborda as opções de depuração, demonstra o teste de aplicativos no dispositivo e apresenta ferramentas para a criação de ícones do aplicativo personalizados e imagens de inicialização.

### <a name="debugging-tools"></a>Ferramentas de depuração

Algumas vezes, Problemas no código do aplicativo são difíceis de diagnosticar. Para ajudar a diagnosticar problemas de código complexos, você poderia [definir um ponto de interrupção](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/set_a_breakpoint), [examinar o código passo a passo](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/step_through_code) ou [enviar informações para a janela de Log](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/output_information_to_log_window).

### <a name="deploy-to-a-device"></a>Implantar em um dispositivo

O Simulador iOS é uma maneira rápida de testar um aplicativo. O Simulador tem uma série de otimizações úteis para testes, inclusive local fictício, [simulação de movimento](https://github.com/xamarin/recipes/tree/master/Recipes/ios/multitasking/test_location_changes_in_simulator) e muito mais. No entanto, os usuários não consumirão aplicativo final em um simulador. Todos os aplicativos devem ser testados em dispositivos reais antecipadamente e com frequência.

Um dispositivo leva tempo para provisionar e requer uma Conta de Desenvolvedor Apple. O guia [Provisionamento de dispositivo](~/ios/get-started/installation/device-provisioning/index.md) fornece instruções completas de como preparar um dispositivo para o desenvolvimento.

> [!NOTE]
> Atualmente, devido a um requisito da Apple, é necessário ter um certificado de desenvolvimento ou uma _identidade de assinatura_ para compilar o código para um dispositivo físico ou para o simulador. Siga as etapas no [guia Provisionamento de Dispositivo](~/ios/get-started/installation/device-provisioning/manual-provisioning.md) para configurar isso.

Depois que o dispositivo é configurado você pode implantar para ele conectando-o, alterando o destino na barra de ferramentas de build para o dispositivo iOS e pressionando **Iniciar** ( **Reproduzir**) conforme ilustrado pela captura de tela a seguir:

::: zone pivot="macos"

![Pressionar Iniciar/Reproduzir](hello-ios-deepdive-images/image46new.png)

::: zone-end
::: zone pivot="windows"

![Pressionar Iniciar/Reproduzir](hello-ios-deepdive-images/vs-image46.png)

::: zone-end

O aplicativo será implantado no dispositivo iOS:

[![O aplicativo será implantado no dispositivo iOS e executado](hello-ios-deepdive-images/image1.png)](hello-ios-deepdive-images/image1.png#lightbox)

### <a name="generate-custom-icons-and-launch-images"></a>Gerar ícones personalizados e imagens de inicialização

Nem todos têm um designer disponível para criar os ícones personalizados e iniciar imagens que um aplicativo precisa destacar. Aqui estão várias abordagens alternativas para gerar arte do aplicativo personalizado:

::: zone pivot="macos"

- [**Pixelmator**](https://www.pixelmator.com/) – uma imagem versátil editando o aplicativo para Mac que custa aproximadamente US$ 30.
- [**Fiverr**](https://www.fiverr.com/) – escolha dentre uma variedade de designers para criar um ícone definido para você, com valores partindo de US$ 5. Mesmo que os resultados com ele nem sempre sejam os esperados, trata-se de um bom recurso para quem precisa criar ícones rapidamente

::: zone-end
::: zone pivot="windows"

- Visual Studio – você pode usar isso para criar um conjunto de ícones simples para seu aplicativo diretamente no IDE.
- [**Fiverr**](https://www.fiverr.com/) – escolha dentre uma variedade de designers para criar um ícone definido para você, com valores partindo de US$ 5. Mesmo que os resultados com ele nem sempre sejam os esperados, trata-se de um bom recurso para quem precisa criar ícones rapidamente

::: zone-end

Para obter mais informações sobre requisitos e tamanhos de imagem de inicialização e de ícone, consulte o [guia Como trabalhar com imagens](~/ios/app-fundamentals/images-icons/index.md).

## <a name="summary"></a>Resumo

Parabéns! Agora você tem uma compreensão sólida dos componentes de um aplicativo Xamarin.iOS, bem como das ferramentas usadas para criá-los.
No [próximo tutorial na série Introdução](~/ios/get-started/hello-ios-multiscreen/index.md), você estenderá nosso aplicativo para lidar com várias telas. Ao longo do caminho você implementará um controlador de navegação, aprenderá sobre o Storyboard Segues e introduzirá o padrão MVC (Model, View, Controller) à medida que estende o aplicativo para gerenciar várias telas.

## <a name="related-links"></a>Links relacionados

- [Hello, iOS (exemplo)](/samples/xamarin/ios-samples/hello-ios)
- [Diretrizes da interface humana do iOS](https://developer.apple.com/design/human-interface-guidelines/ios/overview/themes/)
- [Portal de Provisionamento do iOS](https://developer.apple.com/account/#/overview)