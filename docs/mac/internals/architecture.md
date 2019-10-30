---
title: Arquitetura do Xamarin. Mac
description: Este guia explora o Xamarin. Mac e sua relação com o Objective-C em um nível baixo. Ele explica conceitos como compilação, seletores, registradores, inicialização de aplicativo e o gerador.
ms.prod: xamarin
ms.assetid: 74D1FF57-4F2A-4646-8669-003DE99671D4
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 04/12/2017
ms.openlocfilehash: 51900adb1dd15675e584671f3b06ad6d7572f47d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73017564"
---
# <a name="xamarinmac-architecture"></a>Arquitetura do Xamarin. Mac

_Este guia explora o Xamarin. Mac e sua relação com o Objective-C em um nível baixo. Ele explica conceitos como compilação, seletores, registradores, inicialização de aplicativo e o gerador._

## <a name="overview"></a>Visão Geral

Os aplicativos Xamarin. Mac são executados no ambiente de execução mono e usam o compilador do Xamarin para compilar a IL (linguagem intermediária), que é então JIT (just-in-time) compilado para código nativo em tempo de execução. Isso é executado lado a lado com o tempo de execução Objective-C. Ambos os ambientes de tempo de execução são executados sobre um kernel do tipo UNIX, especificamente XNU, e expõem várias APIs ao código do usuário, permitindo que os desenvolvedores acessem o sistema nativo ou gerenciado subjacente.

O diagrama a seguir mostra uma visão geral básica dessa arquitetura:

[![Diagrama mostrando uma visão geral básica da arquitetura](architecture-images/mac-arch.png "Diagrama mostrando uma visão geral básica da arquitetura")](architecture-images/mac-arch-large.png#lightbox)

### <a name="native-and-managed-code"></a>Código nativo e gerenciado

Ao desenvolver para o Xamarin, os termos *nativos* e código *gerenciado* são frequentemente usados. Código gerenciado é o código que tem sua execução gerenciada pelo .NET Framework Common Language Runtime ou no caso do Xamarin: o tempo de execução do mono.

Código nativo é o código que será executado nativamente na plataforma específica (por exemplo, Objective-C ou mesmo código compilado por AOT, em um chip ARM). Este guia explora como o código gerenciado é compilado para código nativo e explica como um aplicativo Xamarin. Mac funciona, fazendo uso completo das APIs do Mac da Apple por meio do uso de associações, enquanto também tem acesso ao. A BCL da rede e uma linguagem sofisticada C#, como.

## <a name="requirements"></a>Requisitos

O listado a seguir é necessário para desenvolver um aplicativo do macOS X com Xamarin.Mac:

- Um Mac que executa o macOS Sierra (10,12) ou superior.
- A versão mais recente do Xcode (instalada na [loja de aplicativos](https://itunes.apple.com/us/app/xcode/id497799835?mt=12))
- A versão mais recente do Xamarin. Mac e do Visual Studio para Mac

Aplicativos Mac em execução criados com Xamarin.Mac têm os seguintes requisitos do sistema:

- Um Mac que executa o Mac OS X 10,7 ou superior.

## <a name="compilation"></a>Compilação

Quando você compila qualquer aplicativo Xamarin Platform, o compilador C# mono ( F#ou) será executado e compilará seu C# código F# e o codificará no Microsoft Intermediate Language (MSIL ou Il). O Xamarin. Mac usa um compilador *JIT (just in time)* em tempo de execução para compilar código nativo, permitindo a execução na arquitetura correta, conforme necessário.

Isso é diferente do Xamarin. iOS, que usa a compilação AOT. Ao usar o compilador de AOT, todos os assemblies e todos os métodos dentro deles são compilados no momento da compilação. Com o JIT, a compilação ocorre sob demanda apenas para os métodos que são executados.

Com os aplicativos Xamarin. Mac, o mono normalmente é inserido no pacote do aplicativo (e conhecido como **mono incorporado**). Ao usar a API do Xamarin. Mac clássica, o aplicativo poderia, em vez disso, usar o **mono do sistema**. no entanto, não há suporte para isso no API unificada. O sistema mono refere-se ao mono que foi instalado no sistema operacional. Na inicialização do aplicativo, o aplicativo Xamarin. Mac usará isso.

## <a name="selectors"></a>Seletores

Com o Xamarin, temos dois ecossistemas separados, .NET e Apple, que precisamos reunir para parecer o mais eficiente possível, para garantir que o objetivo final seja uma experiência de usuário tranqüila. Vimos na seção acima como os dois tempos de execução se comunicam, e você pode ter ouvido muito bem o termo ' associações ', que permite que as APIs do Mac nativo sejam usadas no Xamarin. As associações são explicadas em detalhes na [documentação de associação do Objective-C](~/mac/platform/binding.md), portanto, por enquanto, vamos explorar como o Xamarin. Mac funciona nos bastidores.

Primeiro, deve haver uma maneira de expor Objective-C para C#, que é feito por meio de seletores. Um seletor é uma mensagem que é enviada a um objeto ou classe. Com o Objective-C, isso é feito por meio das funções [objc_msgSend](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ObjCRuntimeRef/index.html) . Para obter mais informações sobre como usar seletores, consulte o guia de [seletores do IOS Objective-C](~/ios/internals/objective-c-selectors.md) . Também deve haver uma maneira de expor código gerenciado para Objective-C, o que é mais complicado devido ao fato de que Objective-C não sabe nada sobre o código gerenciado. Para contornar isso, usamos um [registrador](~/mac/internals/registrar.md). Isso é explicado mais detalhadamente na próxima seção.

## <a name="registrar"></a>Registrador

Conforme mencionado acima, o registrador é um código que expõe o código gerenciado para Objective-C. Ele faz isso criando uma lista de todas as classes gerenciadas que derivam de NSObject:

- Para todas as classes que não estão encapsulando uma classe Objective-C existente, ela cria uma nova classe Objective-C com membros Objective-C espelhando todos os membros gerenciados que têm um atributo `[Export]`.
- Nas implementações para cada membro Objective-C, o código é adicionado automaticamente para chamar o membro gerenciado espelhado.

O pseudocódigo abaixo mostra um exemplo de como isso é feito:

**C#(código gerenciado):**

```csharp
class MyViewController : UIViewController{
    [Export ("myFunc")]
    public void MyFunc ()
    {
    }
 }
 ```

**Objective-C (código nativo):**

```objc
@interface MyViewController : UIViewController
 - (void)myFunc;
@end 

@implementation MyViewController
- (void)myFunc {
    // Code to call the managed C# MyFunc method in MyViewController
}
@end
```

O código gerenciado pode conter os atributos, `[Register]` e `[Export]`, que o registrador usa para saber que o objeto precisa ser exposto a Objective-C. O atributo [Register] é usado para especificar o nome da classe Objective-C gerada, caso o nome padrão gerado não seja adequado. Todas as classes derivadas de NSObject são automaticamente registradas com Objective-C. O atributo [Export] necessário contém uma cadeia de caracteres, que é o seletor usado na classe Objective-C gerada.

Há dois tipos de registradores usados no Xamarin. Mac – dinâmico e estático:

- Registradores dinâmicos – este é o registrador padrão para todas as compilações do Xamarin. Mac. O registrador dinâmico faz o registro de todos os tipos em seu assembly em tempo de execução. Ele faz isso usando funções fornecidas pela API de tempo de execução do Objective-C. O registrador dinâmico, portanto, tem uma inicialização mais lenta, mas um tempo de compilação mais rápido. Funções nativas (geralmente em C), chamadas trampolines, são usadas como implementações de método ao usar os registradores dinâmicos. Elas variam entre diferentes arquiteturas.
- Registradores estáticos – o registrador estático gera código Objective-C durante a compilação, que é então compilado em uma biblioteca estática e vinculado ao executável. Isso permite uma inicialização mais rápida, mas leva mais tempo durante o tempo de compilação.

## <a name="application-launch"></a>Inicialização do aplicativo

A lógica de inicialização do Xamarin. Mac será diferente, dependendo se o mono incorporado ou o sistema é usado. Para exibir o código e as etapas de inicialização do aplicativo Xamarin. Mac, consulte o arquivo de [cabeçalho de inicialização](https://github.com/xamarin/xamarin-macios/blob/master/runtime/xamarin/launch.h) no repositório público Xamarin-macios.

## <a name="generator"></a>Gerador

O Xamarin. Mac contém definições para cada API do Mac. Você pode navegar por qualquer um deles no [repositório GitHub MaciOS](https://github.com/xamarin/xamarin-macios/tree/master/src). Essas definições contêm interfaces com atributos, bem como quaisquer métodos e propriedades necessários. Por exemplo, o código a seguir é usado para definir um NSBox no [namespace AppKit](https://github.com/xamarin/xamarin-macios/blob/master/src/appkit.cs#L1465-L1526). Observe que se trata de uma interface com vários métodos e propriedades:

```csharp
[BaseType (typeof (NSView))]
public interface NSBox {

    …

    [Export ("borderRect")]
    CGRect BorderRect { get; }

    [Export ("titleRect")]
    CGRect TitleRect { get; }

    [Export ("titleCell")]
    NSObject TitleCell { get; }

    [Export ("sizeToFit")]
    void SizeToFit ();

    [Export ("contentViewMargins")]
    CGSize ContentViewMargins { get; set; }

    [Export ("setFrameFromContentFrame:")]
    void SetFrameFromContentFrame (CGRect contentFrame);

    …

}
```

O gerador, chamado `bmac` no Xamarin. Mac, pega esses arquivos de definição e usa as ferramentas .NET para compilá-los em um assembly temporário. No entanto, esse assembly temporário não é utilizável para chamar o código Objective-C. Em seguida, o gerador lê o assembly temporário C# e gera o código que pode ser usado em tempo de execução. É por isso que, por exemplo, se você adicionar um atributo aleatório ao arquivo Definition. cs, ele não aparecerá no código de saída. O gerador não sabe disso e, portanto, `bmac` não sabe procurar por ele no assembly temporário para gerar o resultado.

Depois que o Xamarin. Mac. dll tiver sido criado, o packager, `mmp`, agrupará todos os componentes juntos.

Em um alto nível, ele consegue isso executando as seguintes tarefas:

- Crie uma estrutura de pacote de aplicativo.
- Copie em seus assemblies gerenciados.
- Se a vinculação estiver habilitada, execute o vinculador gerenciado para otimizar seus assemblies removendo partes não utilizadas.
- Criar um aplicativo iniciador, a vinculação no código do iniciador falou junto com o código do registrador se estiver no modo estático.

Isso é executado como parte do processo de compilação do usuário que compila o código do usuário em um assembly que referencia o Xamarin. Mac. dll e executa `mmp` para torná-lo um pacote

Para obter informações mais detalhadas sobre o vinculador e como ele é usado, consulte o guia do [vinculador](~/ios/deploy-test/linker.md) do Ios.

## <a name="summary"></a>Resumo

Este guia examinou a compilação de aplicativos Xamarin. Mac e explorou o Xamarin. Mac e sua relação com o Objective-C.
