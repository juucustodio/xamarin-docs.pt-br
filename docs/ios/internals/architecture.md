---
title: Arquitetura do aplicativo iOS
description: Este documento descreve o Xamarin. iOS em um nível baixo, discutindo como o código nativo e o gerenciado interagem, compilação AOT, seletores, registradores, inicialização de aplicativos e o gerador.
ms.prod: xamarin
ms.assetid: F40F2275-17DA-4B4D-9678-618FF25C6803
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 832071023bfe2ea9d947946ff2b70428d93fc866
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86937523"
---
# <a name="ios-app-architecture"></a>Arquitetura do aplicativo iOS

Os aplicativos Xamarin. iOS são executados no ambiente de execução mono e usam a compilação completa antecipada de tempo (AOT) para compilar o código C# na linguagem de assembly do ARM. Isso é executado lado a lado com o [tempo de execução Objective-C](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ObjCRuntimeRef/). Ambos os ambientes de tempo de execução são executados sobre um kernel do tipo UNIX, especificamente [XNU](https://en.wikipedia.org/wiki/XNU), e expõem várias APIs ao código do usuário, permitindo que os desenvolvedores acessem o sistema nativo ou gerenciado subjacente.

O diagrama a seguir mostra uma visão geral básica dessa arquitetura:

[![Este diagrama mostra uma visão geral básica da arquitetura de compilação da AOT (antecipação de tempo)](architecture-images/ios-arch-small.png)](architecture-images/ios-arch.png#lightbox)

## <a name="native-and-managed-code-an-explanation"></a>Código nativo e gerenciado: uma explicação

Ao desenvolver para o Xamarin, os termos *nativo e código gerenciado* são frequentemente usados. [Código gerenciado](https://blogs.msdn.microsoft.com/brada/2004/01/09/what-is-managed-code/) é o código que tem sua execução gerenciada pelo [.NET Framework Common Language Runtime](https://msdn.microsoft.com/library/8bs2ecf4(v=vs.110).aspx)ou no caso do Xamarin: o tempo de execução do mono. É isso que chamamos de linguagem intermediária.

Código nativo é o código que será executado nativamente na plataforma específica (por exemplo, Objective-C ou mesmo código compilado por AOT, em um chip ARM). Este guia explora como a AOT compila seu código gerenciado para código nativo e explica como um aplicativo Xamarin. iOS funciona, fazendo uso completo das APIs do iOS da Apple por meio do uso de associações, enquanto também tem acesso ao. A BCL da rede e uma linguagem sofisticada, como o C#.

## <a name="aot"></a>AOT

Quando você compila qualquer aplicativo da plataforma Xamarin, o compilador mono C# (ou F #) será executado e compilará seu código C# e F # na MSIL (Microsoft Intermediate Language). Se você estiver executando um Xamarin. Android, um aplicativo Xamarin. Mac ou até mesmo um aplicativo Xamarin. iOS no simulador, o [CLR (Common Language Runtime) do .net](https://msdn.microsoft.com/library/8bs2ecf4(v=vs.110).aspx) compilará o MSIL usando um compilador JIT (just in time). Em tempo de execução, isso é compilado em um código nativo, que pode ser executado na arquitetura correta para seu aplicativo.

No entanto, há uma restrição de segurança no iOS, definida pela Apple, que não permite a execução de código gerado dinamicamente em um dispositivo.
Para garantir que possamos aderir a esses protocolos de segurança, o Xamarin. iOS usa um compilador de AOT (antecipadamente tempo) para compilar o código gerenciado. Isso produz um binário iOS nativo, opcionalmente otimizado com LLVM para dispositivos, que pode ser implantado no processador baseado em ARM da Apple. Um diagrama aproximado de como isso se ajusta é ilustrado abaixo:

[![Um diagrama aproximado de como isso se encaixa](architecture-images/aot.png)](architecture-images/aot-large.png#lightbox)

O uso da AOT tem várias limitações, que são detalhadas no guia de [limitações](~/ios/internals/limitations.md) . Ele também fornece uma série de melhorias no JIT por meio de uma redução no tempo de inicialização e várias otimizações de desempenho

Agora que exploramos como o código é compilado da origem para o código nativo, vamos dar uma olhada nos bastidores para ver como o Xamarin. iOS nos permite escrever aplicativos iOS totalmente nativos

## <a name="selectors"></a>Seletores

Com o Xamarin, temos dois ecossistemas separados, .NET e Apple, que precisamos reunir para parecer o mais eficiente possível, para garantir que o objetivo final seja uma experiência de usuário tranqüila. Vimos na seção acima como os dois tempos de execução se comunicam e você pode muito bem ter ouvido falar do termo ' Bindings ', que permite que as APIs nativas do iOS sejam usadas no Xamarin. As associações são explicadas em detalhes em nossa documentação de [vinculação objetiva-C](~/cross-platform/macios/binding/overview.md) , portanto, por enquanto, vamos explorar como o Ios funciona nos bastidores.

Primeiro, deve haver uma maneira de expor Objective-C para C#, que é feito por meio de seletores. Um seletor é uma mensagem que é enviada a um objeto ou classe. Com o Objective-C, isso é feito por meio das funções de [objc_msgSend](~/cross-platform/macios/binding/overview.md) .
Para obter mais informações sobre como usar seletores, consulte o guia de [seletores de Objective-C](~/ios/internals/objective-c-selectors.md) . Também deve haver uma maneira de expor código gerenciado para Objective-C, o que é mais complicado devido ao fato de que Objective-C não sabe nada sobre o código gerenciado. Para contornar isso, usamos *registradores*. Eles são explicados com mais detalhes na próxima seção.

## <a name="registrars"></a>Registradores

Conforme mencionado acima, o registrador é um código que expõe o código gerenciado para Objective-C. Ele faz isso criando uma lista de todas as classes gerenciadas que derivam de NSObject:

- Para todas as classes que não estão encapsulando uma classe Objective-C existente, ela cria uma nova classe Objective-C com membros Objective-C espelhando todos os membros gerenciados que têm um `Export` atributo [].

- Nas implementações para cada membro Objective-C, o código é adicionado automaticamente para chamar o membro gerenciado espelhado.

O pseudocódigo abaixo mostra um exemplo de como isso é feito:

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

O código gerenciado pode conter os atributos `[Register]` e `[Export]` , que o registrador usa para saber que o objeto precisa ser exposto a Objective-C.
O `[Register]` atributo é usado para especificar o nome da classe Objective-C gerada, caso o nome padrão gerado não seja adequado. Todas as classes derivadas de NSObject são automaticamente registradas com Objective-C.
O `[Export]` Atributo Required contém uma cadeia de caracteres, que é o seletor usado na classe Objective-C gerada.

Há dois tipos de registradores usados no Xamarin. iOS – dinâmico e estático:

- **Registradores dinâmicos** – o registrador dinâmico faz o registro de todos os tipos em seu assembly em tempo de execução. Ele faz isso usando funções fornecidas pela [API de tempo de execução do Objective-C](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ObjCRuntimeRef/). O registrador dinâmico, portanto, tem uma inicialização mais lenta, mas um tempo de compilação mais rápido. Isso é padrão para o simulador de iOS. Funções nativas (geralmente em C), chamadas trampolines, são usadas como implementações de método ao usar os registradores dinâmicos. Elas variam entre diferentes arquiteturas.

- **Registradores estáticos** – o registrador estático gera código Objective-C durante a compilação, que é então compilado em uma biblioteca estática e vinculado ao executável. Isso permite uma inicialização mais rápida, mas leva mais tempo durante o tempo de compilação. Isso é usado por padrão para compilações de dispositivo. O registrador estático também pode ser usado com o simulador de iOS passando `--registrar:static` como um `mtouch` atributo nas opções de Build do projeto, conforme mostrado abaixo:

    [![Configurando argumentos mTouch adicionais](architecture-images/image1.png)](architecture-images/image1.png#lightbox)

Para obter mais informações sobre as especificidades do sistema de registro do tipo iOS usado pelo Xamarin. iOS, consulte o guia do [registrador de tipos](~/ios/internals/registrar.md) .

## <a name="application-launch"></a>Inicialização do aplicativo

O ponto de entrada de todos os executáveis do Xamarin. iOS é fornecido por uma função chamada `xamarin_main` , que inicializa o mono.

Dependendo do tipo de projeto, o seguinte é feito:

- Para aplicativos iOS e tvOS regulares, o método Main gerenciado, fornecido pelo aplicativo Xamarin, é chamado. Em seguida, esse método Main gerenciado chama `UIApplication.Main` , que é o ponto de entrada para Objective-C. UIApplication. Main é a associação para o método de Objective-C `UIApplicationMain` .
- Para extensões, a função nativa –  `NSExtensionMain` ou ( `NSExtensionmain` para extensões WatchOS) – fornecida pelas bibliotecas da Apple é chamada. Como esses projetos são bibliotecas de classes e não projetos executáveis, não há nenhum método principal gerenciado a ser executado.

Toda essa sequência de inicialização é compilada em uma biblioteca estática, que é então vinculada ao seu executável final para que seu aplicativo saiba como tirar o fundo.

Neste ponto, nosso aplicativo foi inicializado, o mono está em execução, estamos em código gerenciado e sabemos como chamar o código nativo e ser chamado de volta. A próxima coisa que precisamos fazer é realmente começar a adicionar controles e tornar o aplicativo interativo.

## <a name="generator"></a>Gerador

O Xamarin. iOS contém definições para cada API única do iOS. Você pode navegar por qualquer um deles no [repositório GitHub MaciOS](https://github.com/xamarin/xamarin-macios/tree/master/src). Essas definições contêm interfaces com atributos, bem como quaisquer métodos e propriedades necessários. Por exemplo, o código a seguir é usado para definir um UIToolbar no [namespace](https://github.com/xamarin/xamarin-macios/blob/master/src/uikit.cs#L11277-L11327)UIKit. Observe que se trata de uma interface com vários métodos e propriedades:

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

O gerador, chamado [`btouch`](https://github.com/xamarin/xamarin-macios/blob/master/src/btouch.cs) no Xamarin. Ios, pega esses arquivos de definição e usa as ferramentas .net para [compilá-los em um assembly temporário](https://github.com/xamarin/xamarin-macios/blob/master/src/btouch.cs#L318). No entanto, esse assembly temporário não é utilizável para chamar o código Objective-C. Em seguida, o gerador lê o assembly temporário e gera código C# que pode ser usado em tempo de execução.
É por isso que, por exemplo, se você adicionar um atributo aleatório ao arquivo Definition. cs, ele não aparecerá no código de saída. O gerador não sabe disso e, portanto, `btouch` não sabe procurar por ele no assembly temporário para gerar o resultado.

Depois que o Xamarin.iOS.dll tiver sido criado, o mTouch agrupará todos os componentes juntos.

Em um alto nível, ele consegue isso executando as seguintes tarefas:

- Crie uma estrutura de pacote de aplicativo.
- Copie em seus assemblies gerenciados.
- Se a vinculação estiver habilitada, execute o vinculador gerenciado para otimizar seus assemblies copiando partes não utilizadas.
- Compilação AOT.
- Crie um executável nativo, que gera uma série de bibliotecas estáticas (uma para cada assembly) vinculadas ao executável nativo, para que o executável nativo seja composto pelo código do inicializador, pelo código do registrador (se estático) e por todas as saídas do compilador AOT

Para obter informações mais detalhadas sobre o vinculador e como ele é usado, consulte o guia do [vinculador](~/ios/deploy-test/linker.md) .

## <a name="summary"></a>Resumo

Este guia examinou a compilação de AOT de aplicativos Xamarin. iOS e explorou o Xamarin. iOS e sua relação com Objective-C em profundidade.

## <a name="related-links"></a>Links Relacionados

- [Limitações](~/ios/internals/limitations.md)
- [Associação do Objective-C](~/cross-platform/macios/binding/overview.md)
- [Seletores de Objective-C](~/ios/internals/objective-c-selectors.md)
- [Registrador de tipos](~/ios/internals/registrar.md)
- [Vinculador](~/ios/deploy-test/linker.md)
