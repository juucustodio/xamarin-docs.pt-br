---
title: Multitela Hello, iOS
description: "Neste guia de duas partes, podemos expandir o aplicativo Phoneword criado no guia do Hello, iOS para processar uma segunda tela. Ao longo do caminho, apresentaremos o padrão de design Modelo-Exibição-Controlador, implementaremos nossa primeira navegação de iOS e desenvolveremos um entendimento mais profundo da estrutura e da funcionalidade do aplicativo iOS."
ms.topic: article
ms.prod: xamarin
ms.assetid: c866e5f4-8154-4342-876e-efa0693d66f5
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 12/02/2016
ms.openlocfilehash: 8e36548e0d9926a28c133f8f1dc688fcbfa9f78e
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="hello-ios-multiscreen-deep-dive"></a>Aprofundamento nas multitelas do Hello, iOS

No passo a passo de Início Rápido, compilamos e executamos nosso primeiro aplicativo Xamarin.iOS multitela. Agora é hora de desenvolver uma compreensão mais profunda da arquitetura e da navegação do iOS.

Neste guia, apresentamos o padrão de *MVC (Modelo, Exibição, Controlador)* e sua função na arquitetura e na navegação do iOS.
Então nos aprofundamos no Controlador de Navegação e aprendemos a usá-lo para proporcionar uma experiência de navegação familiar em iOS.

<a name="Model_View_Controller" />

## <a name="model-view-controller-mvc"></a>MVC (Modelo – Exibição – Controlador)

No tutorial [Hello, iOS](~/ios/get-started/hello-ios/index.md), aprendemos que aplicativos iOS têm apenas uma *Janela* para a qual controladores de exibição são responsáveis por carregar suas *Hierarquias de Exibição de Conteúdo*. No segundo passo a passo do Phoneword, adicionamos uma segunda tela ao nosso aplicativo e enviamos alguns dados (uma lista de números de telefone) entre as duas telas, conforme ilustra o diagrama a seguir:

 [ ![](hello-ios-multiscreen-deepdive-images/08.png "Este diagrama ilustra a passagem de dados entre duas telas")](hello-ios-multiscreen-deepdive-images/08.png)

Em nosso exemplo, os dados foram coletados na primeira tela, passados do primeiro Controlador de Exibição para o segundo e exibidos pela segunda tela. Essa separação de telas, Controladores de Exibição e dados segue o padrão *MVC (Modelo, Exibição, Controlador)*. Nas próximas seções, discutimos os benefícios do padrão, seus componentes e como os utilizamos em nosso aplicativo Phoneword.

### <a name="benefits-of-the-mvc-pattern"></a>Benefícios do padrão MVC

Modelo-Exibição-Controlador é um *padrão de design* – uma solução de arquitetura reutilizável para um problema ou caso de uso comum no código. MVC é uma arquitetura de aplicativos com uma *GUI (Interface Gráfica do Usuário)*. Ele atribui aos objetos no aplicativo uma entre três funções: *Modelo* (lógica de dados ou aplicativo), *Exibição* (interface do usuário) e *Controlador* (code-behind). O diagrama a seguir ilustra as relações entre as três partes do padrão MVC e o usuário:

 [ ![](hello-ios-multiscreen-deepdive-images/00.png "Este diagrama ilustra as relações entre as três partes do padrão MVC e o usuário")](hello-ios-multiscreen-deepdive-images/00.png)

O padrão MVC é útil porque fornece uma separação lógica entre diferentes partes de um aplicativo de GUI e facilita a reutilização de códigos e exibições. Vamos avançar e dar uma olhada em cada uma das três funções em mais detalhes.

> [!NOTE]
> **Observação**: o padrão MVC padrão é análogo, de modo amplo, à estrutura de páginas ASP.NET ou aplicativos WPF. Nesses exemplos, a exibição é o componente de fato responsável por descrever a interface do usuário e corresponde à página ASPX (HTML) no ASP.NET ou XAML em um aplicativo WPF. O Controlador é o componente responsável por gerenciar a exibição, que corresponde ao code-behind em ASP.NET ou WPF.


### <a name="model"></a>Modelo

O objeto de Modelo normalmente é uma representação específica do aplicativo de dados a serem exibidos ou inseridos na Exibição. O modelo geralmente costuma ser de modo amplo – por exemplo, em nosso aplicativo **Phoneword_iOS**, a lista de números de telefone (representados como uma lista de cadeias de caracteres) é o Modelo. Se estivéssemos compilando um aplicativo de plataforma cruzada, poderíamos escolher compartilhar o código do **PhonewordTranslator** entre nossos aplicativos Android e iOS. Podemos pensar nesse código compartilhado como o Modelo também.

O MVC é completamente independente da *persistência de dados* e do *acesso* do Modelo. Em outras palavras, o MVC não se importa com a aparência de nossos dados ou com o modo como são armazenados, somente com o modo como os dados são *representado*. Por exemplo, poderíamos escolher armazenar nossos dados em um banco de dados SQL ou mantê-los em algum mecanismo de armazenamento em nuvem ou simplesmente usar um `List<string>`. Para fins de MVC, apenas a representação de dados em si está incluída no padrão.

Em alguns casos, a parte do Modelo do MVC pode estar vazia. Por exemplo, podemos optar por adicionar algumas páginas estáticas a nosso aplicativo explicando como funciona o tradutor de telefone, por que o criamos e como entrar em contato conosco para relatar bugs. Essas telas do aplicativo ainda seriam criadas usando Exibições e Controladores, mas não teriam nenhum dado real de Modelo.

> [!NOTE]
> **Observação**: na literatura de alguns, a parte do Modelo do padrão MVC pode referir-se a todo o back-end do aplicativo e não apenas aos dados exibidos na interface do usuário. Neste guia, usamos uma interpretação moderna do Modelo, mas a distinção não é particularmente importante.


### <a name="view"></a>Exibir

Uma Exibição é o componente responsável por renderizar a interface do usuário. Em quase todas as plataformas que usam o padrão MVC, a interface do usuário é composta por uma hierarquia de exibições. Podemos pensar em uma Exibição no MVC como uma hierarquia de exibições com uma única exibição, conhecida como exibição de raiz, na parte superior da hierarquia e qualquer número de exibições filho (conhecidas como subexibições) abaixo dela. No iOS, a Hierarquia de Exibição de Conteúdo de uma tela corresponde ao componente de Exibição no MVC.

### <a name="controller"></a>Controlador

O objeto de Controlador é o componente que conecta tudo e é representado no iOS por `UIViewController`. Podemos pensar no Controlador como o código de apoio para uma tela ou um conjunto de exibições. O controlador é responsável por escutar solicitações do usuário e retornar a hierarquia de exibições apropriada. Ele escuta a solicitações da Exibição (cliques de botão, entrada de texto, etc.) e executa processamento, a modificação da Exibição e o recarregamento da Exibição adequados. O Controlador também é responsável por criar ou recuperar o Modelo de qualquer armazenamento de dados de apoio que exista no aplicativo e preencher a Exibição com seus dados.

Controladores também podem gerenciar outros Controladores. Por exemplo, um Controlador poderá carregar outro Controlador se ele precisar exibir uma tela diferente ou gerenciar uma pilha de Controladores para monitorar sua ordem e as transições entre eles. Na próxima seção, veremos um exemplo de um Controlador que gerencia outros Controladores conforme apresentamos um tipo especial de Controlador de Exibição do iOS chamado *Controlador de Navegação*.

## <a name="navigation-controller"></a>Controlador de navegação

No aplicativo Phoneword, usamos um *Controlador de Navegação* para ajudar a gerenciar a navegação entre várias telas. O Controlador de Navegação é um `UIViewController` especializado representado pela classe `UINavigationController`. Em vez de gerenciar uma única Hierarquia de Exibição de Conteúdo, o Controlador de Navegação gerencia outros Controladores de Exibição, bem como sua própria Hierarquia de Exibição de Conteúdo especial na forma de uma barra de ferramentas de navegação que inclui um título, o botão de voltar e outros recursos opcionais.

O Controlador de Navegação é comum em aplicativos iOS e fornece navegação para aplicativos iOS padrão, como o aplicativo **Configurações**, conforme ilustrado pela captura de tela abaixo:

 [ ![](hello-ios-multiscreen-deepdive-images/01.png "O Controlador de Navegação fornece navegação para aplicativos iOS como o aplicativo de Configurações mostrado aqui")](hello-ios-multiscreen-deepdive-images/01.png)

O Controlador de Navegação atende a três funções principais:

-  **Fornece Ganchos para Navegação de Avanço** – o Controlador de Navegação usa uma metáfora de navegação hierárquica em que Hierarquias de modo Exibição de Conteúdo são *enviadas por push* para uma *pilha de navegação*. Você pode pensar em uma pilha de navegação como uma pilha de cartas de baralho, em que apenas a carta superior está visível, conforme ilustra o diagrama a seguir:  

    [ ![](hello-ios-multiscreen-deepdive-images/02.png "Este diagrama ilustra a navegação como uma pilha de cartas")](hello-ios-multiscreen-deepdive-images/02.png)


-  **Opcionalmente, fornece um botão Voltar** – quando fazemos o push de um novo item para a pilha de navegação, a barra de título pode exibir automaticamente um *botão Voltar* que permite ao usuário retornar na navegação. Pressionar o botão Voltar abre em *pop-up* o Controlador de Exibição atual pilha de navegação e carrega a Hierarquia de Exibição de Conteúdo anterior na Janela:  

    [ ![](hello-ios-multiscreen-deepdive-images/03.png "Este diagrama ilustra a retirada de uma carta da pilha")](hello-ios-multiscreen-deepdive-images/03.png)


-  **Fornece uma Barra de Título** – a parte superior do **Controlador de Navegação** é chamada de *Barra de Título*. Ele é responsável por exibir o título do Controlador de Exibição, conforme ilustrado pelo diagrama a seguir:  

    [ ![](hello-ios-multiscreen-deepdive-images/04.png "A Barra de Título é responsável por exibir o título do Controlador de Exibição")](hello-ios-multiscreen-deepdive-images/04.png)




### <a name="root-view-controller"></a>Controlador de exibição de raiz

Um **Controlador de Navegação** não gerencia uma Hierarquia de Exibição de Conteúdo, portanto, não tem nada para exibir por conta própria.
Em vez disso, um **Controlador de Navegação** é associado a um *Controlador de Exibição de Raiz*:

 [ ![](hello-ios-multiscreen-deepdive-images/05.png "Um Controlador de Navegação é associado a um Controlador de Exibição de Raiz")](hello-ios-multiscreen-deepdive-images/05.png)

O Controlador de Exibição de Raiz representa o primeiro Controlador de Exibição na pilha do **Controlador de Navegação** e a Hierarquia de Exibição de Conteúdo do Controlador de Exibição de Raiz é a primeira Hierarquia de Exibição de Conteúdo a ser carregada na Janela. Se desejamos colocar todo o aplicativo na pilha do Controlador de Navegação, podemos transferir o Segue Sourceless para o **Controlador de Navegação** e definir nosso Controlador de Exibição da primeira tela como o Controlador de Exibição de Raiz, como fizemos no aplicativo Phoneword:

 [ ![](hello-ios-multiscreen-deepdive-images/06.png "O Sourceless Segue define o Controlador de Exibição de primeiras telas como o Controlador de Exibição de Raiz")](hello-ios-multiscreen-deepdive-images/06.png)

### <a name="additional-navigation-options"></a>Opções Adicionais de Navegação

O **Controlador de Navegação** é uma maneira comum de processar a navegação no iOS, mas não é a única opção. Um [Controlador de Barra de Guia](~/ios/user-interface/controls/creating-tabbed-applications.md) pode dividir um aplicativo em diferentes áreas funcionais; um [Controlador de Exibição Dividida](https://developer.xamarin.com/recipes/ios/content_controls/split_view/use_split_view_to_show_two_controllers) pode criar Exibições Mestre/Detalhadas; e um [Controlador de Navegação do Submenu](http://components.xamarin.com/view/flyoutnavigation) cria navegação que o usuário pode acessar com um gesto de passar do lado pela lateral. Tudo isso pode ser combinado com um **Controlador de Navegação** para uma maneira intuitiva de apresentar conteúdo.

## <a name="handling-transitions"></a>Processando transições

No passo a passo do Phoneword, tratamos da transição entre os dois Controladores de Exibição de duas maneiras diferentes – primeiro com um Storyboard Segue e, em seguida, programaticamente. Vamos explorar ambas as opções mais detalhadamente.

### <a name="prepareforsegue"></a>PrepareForSegue

Quando adicionamos um Segue com uma ação **Mostrar** ao Storyboard, instruímos o iOS a fazer o push do segundo Controlador de Exibição na pilha do Controlador de Navegação:

 [ ![](hello-ios-multiscreen-deepdive-images/09.png "Definir o tipo de segue a partir de uma lista suspensa")](hello-ios-multiscreen-deepdive-images/09.png)

Adicionar um Segue ao Storyboard é suficiente para criar uma transição simples entre telas. Se quisermos transmitir dados entre Controladores de Exibição, precisamos substituir o método `PrepareForSegue` e manipular os dados nós mesmos:

```csharp
public override void PrepareForSegue (UIStoryboardSegue segue, NSObject sender)
{
    base.PrepareForSegue (segue, sender);
    ...
}
```

O iOS chama `PrepareForSegue` logo antes de a transição ocorrer e passa o Segue que criamos no Storyboard para o método.
Neste ponto, precisamos definir manualmente o Controlador de Exibição de destino do Segue. O código a seguir obtém um identificador para o Controlador de Exibição de Destino e o converte para a classe adequada, neste caso, CallHistoryController:

```csharp
CallHistoryController callHistoryContoller = segue.DestinationViewController as CallHistoryController;
```

Finalmente, passamos a lista de números de telefone (o Modelo) do `ViewController` para o `CallHistoryController` configurando a propriedade `PhoneHistory` do `CallHistoryController` para a lista de números de telefone discados:

```csharp
callHistoryContoller.PhoneNumbers = PhoneNumbers;
```

O código completo para enviar dados usando um Segue é o seguinte:

```csharp
public override void PrepareForSegue (UIStoryboardSegue segue, NSObject sender)
{
    base.PrepareForSegue (segue, sender);

    var callHistoryContoller = segue.DestinationViewController as CallHistoryController;

    if (callHistoryContoller != null) {
         callHistoryContoller.PhoneNumbers = PhoneNumbers;
    }
 }
```

### <a name="navigation-without-segues"></a>Navegação sem Segues

Fazer a transição do primeiro Controlador de Exibição para o segundo no código é o mesmo processo que o de um Segue, mas várias etapas que devem ser feitas manualmente.
Primeiro, usamos `this.NavigationController` para obter uma referência ao Controlador de Navegação em cuja pilha estamos no momento. Em seguida, usamos o método `PushViewController` do Controlador de Navegação para fazer o push manualmente do próximo Controlador de Exibição para a pilha, passando o Controlador de Exibição e uma opção para animar a transição (definimos isso como `true`).

O código a seguir processa a transição da tela Phoneword para a tela de Histórico de Chamadas:

```csharp
this.NavigationController.PushViewController (callHistory, true);
```

Antes de podermos fazer a transição para o próximo Controlador de Exibição, precisamos instanciá-lo manualmente usando o Storyboard chamando `this.Storyboard.InstantiateViewController` e passando a ID do Storyboard do `CallHistoryController`:

```csharp
CallHistoryController callHistory =
this.Storyboard.InstantiateViewController
("CallHistoryController") as CallHistoryController;
```

Por fim, passamos a lista de números de telefone (o Modelo) do `ViewController` para o `CallHistoryController` configurando a propriedade `PhoneHistory` do `CallHistoryController` para a lista de números de telefone discados, exatamente como fizemos ao tratar da transição com um Segue:

```csharp
callHistory.PhoneNumbers = PhoneNumbers;
```

O código completo para a transição programática é o seguinte:

```csharp
CallHistoryButton.TouchUpInside += (object sender, EventArgs e) => {
    // Launches a new instance of CallHistoryController
    CallHistoryController callHistory = this.Storyboard.InstantiateViewController ("CallHistoryController") as CallHistoryController;
    if (callHistory != null) {
     callHistory.PhoneNumbers = PhoneNumbers;
     this.NavigationController.PushViewController (callHistory, true);
    }
};
```

## <a name="additional-concepts-introduced-in-phoneword"></a>Conceitos adicionais introduzidos no Phoneword

O aplicativo Phoneword introduziu vários conceitos não abordados neste guia. Esses conceitos incluem:

-  **Criação automática de controladores de exibição** – quando inserimos um nome de classe para o Controlador de Exibição no **Painel de Propriedades**, o designer do iOS verifica se a classe existe e, em seguida, gera a classe de apoio do Controlador de Exibição para nós. Para saber mais sobre este e outros recursos de designer do iOS, consulte o guia de [Introdução ao iOS Designer](~/ios/user-interface/designer/introduction.md).
-  **Controlador de Exibição de Tabela** – o `CallHistoryController` é um Controlador de Exibição de Tabela. Um Controlador de Exibição de Tabela contém uma Exibição de Tabela, o layout mais comum e a ferramenta de exibição de dados no iOS. Tabelas estão além do escopo deste guia. Para saber mais sobre Controladores de Exibição de Tabela, consulte o guia [Working with Tables and Cells](~/ios/user-interface/controls/tables/index.md) (Trabalhando com Tabelas e Células).
-   **ID do Storyboard** – definir a ID do Storyboard cria uma classe de Controlador de Exibição em Objective-C, que contém o code-behind para o Controlador de Exibição no Storyboard. Usamos a ID do Storyboard para localizar a classe Objective-C e instanciar o Controlador de Exibição no Storyboard. Para saber mais sobre IDs de Storyboard, consulte o guia [Introduction to Storyboards](~/ios/user-interface/storyboards/index.md) (Introdução a Storyboards).


## <a name="summary"></a>Resumo

Parabéns, você concluiu seu primeiro aplicativo iOS multitela!

Neste guia, apresentamos o padrão MVC e o utilizamos para criar um aplicativo com várias telas. Também exploramos Controladores de Navegação e sua função na potencialização da navegação do iOS. Agora você tem a base sólida necessária para começar a desenvolver seus próprios aplicativos Xamarin.iOS.

Em seguida, vamos aprender a compilar aplicativos de plataforma cruzada com Xamarin com os guias [Introdução ao desenvolvimento móvel](~/cross-platform/get-started/introduction-to-mobile-development.md) e [Building Cross-Platform Applications](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md) (Compilando aplicativos de plataforma cruzada).


## <a name="related-links"></a>Links relacionados

- [Hello, iOS (amostra)](https://developer.xamarin.com/samples/monotouch/Hello_iOS/)
- [Diretrizes da interface humana do iOS](http://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/Introduction/Introduction.html)
- [Portal de provisionamento do iOS](https://developer.apple.com/ios/manage/overview/index.action)
