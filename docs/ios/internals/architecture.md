---
title: Arquitetura do iOS
description: Explorando o xamarin em um nível baixo
ms.prod: xamarin
ms.assetid: F40F2275-17DA-4B4D-9678-618FF25C6803
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 930b52e5b2a532e71594f26af79035db2cc5fb25
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="ios-architecture"></a>Arquitetura do iOS

Aplicativos xamarin executado dentro do ambiente de execução Mono e usam a compilação de em frente do tempo (AOT) completo para compilar o código c# para a linguagem de assembly do ARM. Isso é executado lado a lado com o [Objective-C Runtime](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ObjCRuntimeRef/). Ambos os ambientes de tempo de execução são executados em um kernel UNIX-like, especificamente [XNU](https://en.wikipedia.org/wiki/XNU)e expor várias APIs para o código de usuário permitindo que os desenvolvedores acessar o sistema nativo ou gerenciado subjacente.

O diagrama a seguir mostra uma visão geral básica dessa arquitetura:

[ ![](architecture-images/ios-arch-small.png "Este diagrama mostra uma visão geral básica da arquitetura de compilação em frente do tempo (AOT)")](architecture-images/ios-arch.png#lightbox)

## <a name="native-and-managed-code-an-explanation"></a>Nativo e código gerenciado: uma explicação

Ao desenvolver para Xamarin os termos *nativos e gerenciados* código são usadas com frequência. [Código gerenciado](https://blogs.msdn.microsoft.com/brada/2004/01/09/what-is-managed-code/) é o código que tem sua execução gerenciada pelo [Common Language Runtime do .NET Framework](https://msdn.microsoft.com/en-us/library/8bs2ecf4(v=vs.110).aspx), ou no caso do Xamarin: o tempo de execução Mono. Este é o que chamamos de uma linguagem intermediária.

O código nativo é o código que será executado em modo nativo na plataforma específica (por exemplo, Objective-C ou até mesmo código AOT compilado em um chip do ARM). Este guia explora como AOT compila o código gerenciado para código nativo e explica como funciona um aplicativo xamarin, fazer uso total de iOS da Apple APIs com o uso de associações, enquanto também tem acesso. BCL do NET e uma linguagem sofisticada, como c#.


## <a name="aot"></a>AOT

Quando você compila a qualquer aplicativo da plataforma de Xamarin, o compilador c# Mono (ou F #) será executado e será compilar seu código c# e F # em linguagem MSIL (Microsoft Intermediate). Se você estiver executando um xamarin, um aplicativo de Xamarin.Mac ou até mesmo um aplicativo xamarin no simulador, o [.NET Common Language Runtime (CLR)](https://msdn.microsoft.com/en-us/library/8bs2ecf4(v=vs.110).aspx) compila o MSIL usando um logo no compilador Time (JIT). Em tempo de execução que isso é compilado em código nativo, que pode executar na arquitetura correta para seu aplicativo.

No entanto, há uma restrição de segurança no iOS, definido pela Apple, que não permite a execução de código gerado dinamicamente em um dispositivo.
Para garantir o cumprimento esses protocolos de segurança, xamarin usa um compilador em frente do tempo (AOT) para compilar o código gerenciado. Isso produz um iOS nativo binário, opcionalmente otimizado com LLVM para dispositivos, que podem ser implantados em um processador baseado em ARM da Apple. Um esboço do diagrama de como isso se encaixa está ilustrada abaixo:

[![](architecture-images/aot.png "Um esboço do diagrama de como isso se encaixa em conjunto")](architecture-images/aot-large.png#lightbox)

Usar AOT tem um número de limitações que são detalhadas no [limitações](~/ios/internals/limitations.md) guia. Ele também fornece uma série de melhorias por JIT por meio de uma redução no tempo de inicialização e várias otimizações de desempenho

Agora que estamos exploraram como a compilação do código de origem para código nativo, vamos dar uma olhada nos bastidores para ver como xamarin permite escrever aplicativos do iOS totalmente nativo

## <a name="selectors"></a>Seletores

Com o Xamarin, temos duas ecossistemas separadas, .NET e Apple, que são necessárias para trazer para parecer como simplificado quanto possível, para garantir que o objetivo final é uma experiência de usuário uniforme. Vimos na seção acima como se comunicam dois tempos de execução, e você pode muito bem ter ouvido falar do termo 'ligações' que permite que o iOS nativo APIs para ser usado em Xamarin. Associações são explicadas em detalhes no nosso [Objective-C associação](~/cross-platform/macios/binding/overview.md) documentação, portanto agora vamos explorar como iOS funciona nos bastidores.

Primeiro, deve haver uma maneira de expor Objective-C para c#, que é feito por meio de seletores. Um seletor é uma mensagem que é enviada a um objeto ou classe. Com Objective-C, isso é feito por meio de [objc_msgSend](~/cross-platform/macios/binding/overview.md) funções.
Para obter mais informações sobre o uso de seletores, consulte o [seletores Objective-C](~/ios/internals/objective-c-selectors.md) guia. Também deve haver uma maneira para expor o código gerenciado para Objective-C, que é mais complicado devido ao fato de que Objective-C não sabe nada sobre o código gerenciado. Para contornar esse problema, usamos *registradores*. Essas são explicadas em mais detalhes na próxima seção.

## <a name="registrars"></a>Registradores

Conforme mencionado acima, o registrador é código que expõe o código gerenciado para o objetivo-C. Ele faz isso criando uma lista de todas as classes gerenciadas que deriva de NSObject:

- Para todas as classes que não são quebra automática de uma classe existente em Objective-C, ele cria uma nova classe de Objective-C com membros Objective-C espelhamento todos os membros gerenciados que têm um [`Export`] atributo.

- Em implementações para cada membro Objective-C, o código é adicionado automaticamente ao chamar o membro gerenciado espelhado.

O pseudocódigo a seguir mostra um exemplo de como isso é feito:

**C# (código gerenciado)**

```csharp
 class MyViewController : UIViewController{
    [Export ("myFunc")]
    public void MyFunc ()
    {
    }
 }
```

**Objective-C:**

```objectivec
@interface MyViewController : UIViewController { }

    -(void)myFunc;
@end

@implementation MyViewController {}

    -(void) myFunc
    {
        /* code to call the managed MyViewController.MyFunc method */
    }
@end

```

O código gerenciado pode conter os atributos `[Register]` e `[Export]`, que usa o registrador de saber que o objeto precisa ser exposto ao objetivo-C.
O `[Register]` atributo é usado para especificar o nome da classe gerada Objective-C, caso o nome padrão gerado não é adequado. Todas as classes derivadas de NSObject são automaticamente registradas com o objetivo-C.
Obrigatório `[Export]` atributo contém uma cadeia de caracteres, que é o seletor usado na classe Objective-C gerado.

Há dois tipos de registradores usados em xamarin – dinâmico e estático:


- **Registradores dinâmicos** – o registrador dinâmico o registro de todos os tipos em seu assembly em tempo de execução. Ele faz isso usando funções fornecidas pelo [tempo de execução do Objective-C API](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ObjCRuntimeRef/). O registro dinâmico, portanto, tem uma inicialização mais lenta, mas um rápido tempo de compilação. Esse é o padrão para o simulador de iOS. Funções nativas (normalmente em C), chamadas trampolines, são usadas como implementações de método ao usar os registradores dinâmicos. Eles variam entre arquiteturas diferentes.

- **Registradores estáticos** – o registrador estático gera código Objective-C durante a compilação, o que é compilada em uma biblioteca estática e vinculada para o executável. Isso permite uma inicialização mais rápida, mas leva mais tempo durante o tempo de compilação. Isso é usado por padrão para compilações do dispositivo. O registrador estático também pode ser usado com o simulador de iOS passando `--registrar:static` como um `mtouch` atributo nas opções de compilação do projeto, conforme mostrado abaixo:

    [![](architecture-images/image1.png "Argumentos de mtouch adicionais de configuração")](architecture-images/image1.png#lightbox)

Para obter mais informações sobre as especificações do sistema de registro do tipo usado pelo xamarin iOS, consulte o [tipo registrador](~/ios/internals/registrar.md) guia.

## <a name="application-launch"></a>Iniciar o aplicativo

O ponto de entrada de todos os executáveis do xamarin é fornecido por uma função chamada `xamarin_main`, que inicializa mono.

Dependendo do tipo de projeto, o seguinte ocorre:

- Para iOS regular e aplicativos de tvOS, o método Main gerenciado, fornecido pelo aplicativo Xamarin é chamado. Isso gerenciado método Main chama `UIApplication.Main`, que é o ponto de entrada para o objetivo-C. UIApplication.Main é a associação para Objective-C `UIApplicationMain` método.
- Para as extensões, a função nativa – `NSExtensionMain` ou (`NSExtensionmain` para extensões WatchOS) – fornecido pela Apple bibliotecas é chamado. Como esses projetos são bibliotecas de classe e projetos não executáveis, não há métodos principal gerenciada para executar.

Todos os dessa sequência de inicialização é compilado em uma biblioteca estática, o que é vinculada em seu executável final para que seu aplicativo Saiba como obter o plano.

Neste ponto nosso aplicativo tenha sido iniciado, Mono está em execução, estamos no código gerenciado e sabemos como chamar código nativo e ser chamado novamente. A próxima coisa que precisamos fazer é realmente começar a adicionar controles e tornar o aplicativo interativo.


## <a name="generator"></a>Gerador

Xamarin contém definições para cada API do iOS único. Você pode procurar por meio de qualquer um no [MaciOS do repositório github](https://github.com/xamarin/xamarin-macios/tree/master/src). Essas definições contenham interfaces com atributos, bem como métodos necessários e propriedades. Por exemplo, é o código a seguir é usado para definir um UIToolbar no UIKit [namespace](https://github.com/xamarin/xamarin-macios/blob/master/src/uikit.cs#L11277-L11327). Observe que se trata de uma interface com um número de métodos e propriedades:

```csharp
[BaseType (typeof (UIView))]
public interface UIToolbar : UIBarPositioning {
    [Export ("initWithFrame:")]
    IntPtr Constructor (CGRect frame);

    [Export ("barStyle")]
    UIBarStyle BarStyle { get; set; }

    [Export ("items", ArgumentSemantic.Copy)][NullAllowed]
    UIBarButtonItem [] Items { get; set; }

    [Export ("translucent", ArgumentSemantic.Assign)]
    bool Translucent { [Bind ("isTranslucent")] get; set; }

    // done manually so we can keep this "in sync" with 'Items' property
    //[Export ("setItems:animated:")][PostGet ("Items")]
    //void SetItems (UIBarButtonItem [] items, bool animated);

    [Since (5,0)]
    [Export ("setBackgroundImage:forToolbarPosition:barMetrics:")]
    [Appearance]
    void SetBackgroundImage ([NullAllowed] UIImage backgroundImage, UIToolbarPosition position, UIBarMetrics barMetrics);

    [Since (5,0)]
    [Export ("backgroundImageForToolbarPosition:barMetrics:")]
    [Appearance]
    UIImage GetBackgroundImage (UIToolbarPosition position, UIBarMetrics barMetrics);

    ...
}
```

O gerador, chamado [ `btouch` ](https://github.com/xamarin/xamarin-macios/blob/master/src/btouch.cs) em xamarin, usa esses arquivos de definição e usa as ferramentas do .NET para [compilá-los em um assembly temporário](https://github.com/xamarin/xamarin-macios/blob/master/src/btouch.cs#L318). No entanto, esse assembly temporário não é utilizável para chamar código Objective-C. O gerador de lê o assembly temporário e gera o código c# que pode ser usado em tempo de execução.
Isso é porque, por exemplo, se você adicionar um atributo aleatório para o arquivo de definição de. cs, ele não aparecer no código do meio. O gerador não sabe sobre ele e, portanto, `btouch` não sabe procurá-lo no conjunto temporário de saída.

Quando o Xamarin.iOS.dll tiver sido criado, mtouch vai agrupar todos os componentes em conjunto.

Em um nível alto, ele realiza isso executando as seguintes tarefas:

-   Crie uma estrutura de pacote do aplicativo.
-   Copie os assemblies gerenciados.
-   Se vinculando estiver habilitado, execute o vinculador gerenciado para otimizar seus assemblies copiando partes não utilizadas out.
-   Compilação de AOT.
-   Criar um executável nativo, o que gera uma série de bibliotecas estáticas (uma para cada assembly) que são vinculados para o executável nativo, para que o executável nativo consiste o código de iniciador, o código de registrador (se estático) e todas as saídas da AOT compilador


Para obter mais informações sobre o vinculador e como elas são usadas, consulte o [vinculador](~/ios/deploy-test/linker.md) guia.

## <a name="summary"></a>Resumo

Este guia pesquisados na compilação de AOT de aplicativos xamarin e xamarin explorado e sua relação com Objective-C em camadas.

## <a name="related-links"></a>Links relacionados

- [Limitações](~/ios/internals/limitations.md)
- [Associação do Objective-C](~/cross-platform/macios/binding/overview.md)
- [Seletores de Objective-C](~/ios/internals/objective-c-selectors.md)
- [Tipo de registro](~/ios/internals/registrar.md)
- [Vinculador](~/ios/deploy-test/linker.md)
