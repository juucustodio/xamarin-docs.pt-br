---
title: Arquitetura de aplicativo do iOS
description: Este documento descreve o xamarin. IOS em um nível inferior, a discussão sobre como código nativo e gerenciado interagir, compilação AOT, seletores, registradores, inicialização do aplicativo e o gerador.
ms.prod: xamarin
ms.assetid: F40F2275-17DA-4B4D-9678-618FF25C6803
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: d08f7e3a35cd82f4262ef1f145d4b4648f7baef8
ms.sourcegitcommit: 7eed80186e23e6aff3ddbbf7ce5cd1fa20af1365
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/11/2018
ms.locfileid: "51526657"
---
# <a name="ios-app-architecture"></a>Arquitetura de aplicativo do iOS

Aplicativos xamarin. IOS executado dentro do ambiente de execução Mono e usar a compilação de em frente da hora (AOT) completo para compilar C# código em linguagem de assembly do ARM. Isso é executado lado a lado com o [tempo de execução do Objective-C](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ObjCRuntimeRef/). Ambos os ambientes de tempo de execução são executados sobre um kernel UNIX-like, especificamente [XNU](https://en.wikipedia.org/wiki/XNU)e expor várias APIs ao código do usuário, permitindo que os desenvolvedores acessem o sistema subjacente nativo ou gerenciado.

O diagrama a seguir mostra uma visão geral básica dessa arquitetura:

[ ![](architecture-images/ios-arch-small.png "Este diagrama mostra uma visão geral básica da arquitetura de compilação em frente da hora (AOT)")](architecture-images/ios-arch.png#lightbox)

## <a name="native-and-managed-code-an-explanation"></a>Nativo e o código gerenciado: obter uma explicação

Durante o desenvolvimento para Xamarin os termos *nativos e gerenciados* código geralmente são usados. [O código gerenciado](https://blogs.msdn.microsoft.com/brada/2004/01/09/what-is-managed-code/) é o código que tem sua execução gerenciada pelo [Common Language Runtime do .NET Framework](https://msdn.microsoft.com/library/8bs2ecf4(v=vs.110).aspx), ou no caso do Xamarin: o tempo de execução Mono. Isso é o que chamamos de uma linguagem intermediária.

Código nativo é o código que será executado nativamente na plataforma específica (por exemplo, Objective-C ou até mesmo código compilado de AOT, em um chip ARM). Este guia explora como AOT compila seu código gerenciado para código nativo e explica como funciona um aplicativo xamarin. IOS, utilizar integralmente as APIs do iOS da Apple com o uso de associações, enquanto também tem acesso ao. BCL da rede e uma linguagem sofisticada, como C#.

## <a name="aot"></a>AOT

Quando você compilar qualquer aplicativo da plataforma Xamarin, o Mono C# (ou F#) compilador será executado e compilará seu C# e F# código em Microsoft Intermediate Language (MSIL). Se você estiver executando um xamarin. Android, um aplicativo xamarin. Mac ou até mesmo um aplicativo xamarin. IOS no simulador, o [.NET Common Language Runtime (CLR)](https://msdn.microsoft.com/library/8bs2ecf4(v=vs.110).aspx) compila o MSIL usando um Just no compilador JIT (Time). Em tempo de execução que é compilado em um código nativo, que pode executar na arquitetura correta para seu aplicativo.

No entanto, há uma restrição de segurança no iOS, definidas pela Apple, que não permite a execução do código gerado dinamicamente em um dispositivo.
Para garantir que seguimos para esses protocolos de segurança, em vez disso, o xamarin. IOS usa um compilador em frente da hora (AOT) para compilar o código gerenciado. Isso produz um binário, nativos de iOS, opcionalmente, otimizado com LLVM para dispositivos, que podem ser implantados em um processador de baseado em ARM da Apple. Um esboço do diagrama de como isso funciona em conjunto é ilustrado abaixo:

[![](architecture-images/aot.png "Um esboço do diagrama de como isso funciona em conjunto")](architecture-images/aot-large.png#lightbox)

Usar AOT tem várias limitações, que são detalhadas na [limitações](~/ios/internals/limitations.md) guia. Ele também fornece uma série de aprimoramentos por JIT por meio de uma redução no tempo de inicialização e várias otimizações de desempenho

Agora que nós já explorou como o código é compilado do código-fonte para código nativo, vamos dar uma olhada nos bastidores para ver como o xamarin. IOS permite escrever aplicativos do iOS totalmente nativos

## <a name="selectors"></a>Seletores

Com o Xamarin, temos dois ecossistemas separadas, .NET e da Apple, o que precisamos para trazer juntos para parecer como simplificada quanto possível, para garantir que o objetivo final é uma experiência do usuário uniforme. Temos visto na seção acima como dois tempos de execução se comunicam e você deve ter ouvido muito bem do termo 'associações' que permite que as APIs a ser usado no Xamarin iOS nativo. Associações são explicadas em detalhes no nosso [associação de Objective-C](~/cross-platform/macios/binding/overview.md) documentação, portanto, agora vamos explorar como iOS funciona nos bastidores.

Primeiro, deve haver uma maneira de expor Objective-C para C#, que é feito por meio de seletores. Um seletor é uma mensagem que é enviada para um objeto ou classe. Com o Objective-C, isso é feito por meio de [objc_msgSend](~/cross-platform/macios/binding/overview.md) funções.
Para obter mais informações sobre como usar seletores, consulte o [seletores de Objective-C](~/ios/internals/objective-c-selectors.md) guia. Também deve haver uma maneira de expor o código gerenciado para Objective-C, que é mais complicado devido ao fato de que Objective-C não sabe nada sobre o código gerenciado. Para contornar isso, usamos *registradores*. Eles são explicados mais detalhadamente na próxima seção.

## <a name="registrars"></a>Registradores

Conforme mencionado acima, o registrador é código que expõe o código gerenciado para Objective-C. Ele faz isso criando uma lista de todas as classes gerenciadas que deriva de NSObject:

- Para todas as classes que não são quebra automática de uma classe existente do Objective-C, ele cria uma nova classe de Objective-C com membros de Objective-C, espelhamento de todos os membros gerenciados que têm um [`Export`] atributo.

- Em implementações para cada membro de Objective-C, o código é adicionado automaticamente ao chamar o membro gerenciado espelhado.

O pseudocódigo a seguir mostra um exemplo de como isso é feito:

**C#(Código gerenciado)**

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

O código gerenciado pode conter os atributos `[Register]` e `[Export]`, que usa o registrador de saber que o objeto precisa ser exposta para Objective-C.
O `[Register]` atributo é usado para especificar o nome da classe Objective-C gerada caso o nome padrão gerado não é adequado. Todas as classes derivadas de NSObject são automaticamente registradas com Objective-C.
Necessário `[Export]` atributo contém uma cadeia de caracteres, que é o seletor usado na classe gerada Objective-C.

Há dois tipos de registradores usados no xamarin. IOS – dinâmica e estática:


- **Registradores dinâmicos** – o registrador dinâmico faz o registro de todos os tipos em seu assembly em tempo de execução. Ele faz isso usando funções oferecidas pelos [tempo de execução do Objective-C API](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ObjCRuntimeRef/). O registrador dinâmico, portanto, tem uma inicialização mais lenta, mas um mais rápido do tempo de compilação. Esse é o padrão para o simulador de iOS. Funções nativas (normalmente em C), chamadas trampolines, são usadas como implementações de método ao usar os registradores dinâmicos. Eles variam entre arquiteturas diferentes.

- **Registradores estáticos** – o registrador estático gera código Objective-C durante a compilação, o que, em seguida, é compilada em uma biblioteca estática e vinculada no executável. Isso permite uma inicialização mais rápida, mas leva mais tempo durante o tempo de compilação. Isso é usado por padrão para compilações de dispositivo. O registrador estático também pode ser usado com o simulador de iOS, passando `--registrar:static` como um `mtouch` atributo nas opções de compilação do seu projeto, conforme mostrado abaixo:

    [![](architecture-images/image1.png "Argumentos mtouch adicionais de configuração")](architecture-images/image1.png#lightbox)

Para obter mais informações sobre as especificações do sistema de registro do tipo usado pelo xamarin. IOS do iOS, consulte a [registrador tipo](~/ios/internals/registrar.md) guia.

## <a name="application-launch"></a>Inicialização do aplicativo

O ponto de entrada de todos os executáveis do xamarin. IOS é fornecido por uma função chamada `xamarin_main`, que inicializa o mono.

Dependendo do tipo de projeto, o seguinte ocorre:

- Para iOS regulares e tvOS, o método Main gerenciado, fornecido pelo aplicativo do Xamarin é chamado. Isso gerenciado método Main, em seguida, chama `UIApplication.Main`, que é o ponto de entrada para Objective-C. UIApplication.Main é a associação do Objective-C `UIApplicationMain` método.
- Para extensões, a função nativa – `NSExtensionMain` ou (`NSExtensionmain` para extensões do WatchOS) – fornecido pela Apple bibliotecas é chamado. Uma vez que esses projetos são bibliotecas de classe e projetos não executáveis, não há nenhum método Main gerenciado para executar.

Todos essa sequência de inicialização é compilado em uma biblioteca estática, que, em seguida, é vinculada ao seu arquivo executável final para que seu aplicativo Saiba como obter o plano.

Agora nosso aplicativo tenha sido iniciado, Mono está em execução, estamos em código gerenciado e sabemos como chamar código nativo e ser chamado novamente. A próxima coisa que precisamos fazer é realmente começar a adicionar controles e tornar o aplicativo interativo.


## <a name="generator"></a>Gerador

Xamarin. IOS contém definições para cada API única do iOS. Você pode procurar por meio de qualquer um na [repositório do github MaciOS](https://github.com/xamarin/xamarin-macios/tree/master/src). Essas definições contêm interfaces com atributos, bem como quaisquer métodos necessários e propriedades. Por exemplo, o código a seguir é usado para definir um UIToolbar no UIKit [namespace](https://github.com/xamarin/xamarin-macios/blob/master/src/uikit.cs#L11277-L11327). Observe que se trata de uma interface com um número de métodos e propriedades:

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

O gerador, chamado [ `btouch` ](https://github.com/xamarin/xamarin-macios/blob/master/src/btouch.cs) no xamarin. IOS, usa esses arquivos de definição e usa as ferramentas para .NET [compilá-los em um assembly temporário](https://github.com/xamarin/xamarin-macios/blob/master/src/btouch.cs#L318). No entanto, esse assembly temporário não é utilizável para chamar o código Objective-C. O gerador, em seguida, lê o assembly temporário e gera C# código que pode ser usado em tempo de execução.
Isso é por que, por exemplo, se você adicionar um atributo aleatório ao seu arquivo de definição de. cs, ele não aparecerá no código emitido. O gerador não souber sobre isso e, portanto, `btouch` não sabe para procurar o assembly temporário de saída.

Depois que o xamarin tiver sido criado, o mtouch empacotará todos os componentes juntos.

Em um alto nível, ele realiza isso executando as seguintes tarefas:

-   Crie uma estrutura de pacote do aplicativo.
-   Copiar em seus assemblies gerenciados.
-   Se a vinculação estiver habilitada, execute o vinculador gerenciado para otimizar seus assemblies, copiando as partes não usadas fora.
-   Compilação AOT.
-   Criar um executável nativo, o que gera uma série de bibliotecas estáticas (uma para cada assembly) que estão vinculadas no executável nativo, para que o executável nativo consiste em todas as saídas da AOT, o código do registrador (se estático) e o código do iniciador compilador


Para obter mais informações sobre o vinculador e como elas são usadas, consulte a [vinculador](~/ios/deploy-test/linker.md) guia.

## <a name="summary"></a>Resumo

Este guia examinou a compilação da AOT dos aplicativos do xamarin. IOS e xamarin. IOS explorado e sua relação para Objective-C em profundidade.

## <a name="related-links"></a>Links relacionados

- [Limitações](~/ios/internals/limitations.md)
- [Associação do Objective-C](~/cross-platform/macios/binding/overview.md)
- [Seletores de Objective-C](~/ios/internals/objective-c-selectors.md)
- [Registrador de tipo](~/ios/internals/registrar.md)
- [Vinculador](~/ios/deploy-test/linker.md)
