---
title: Arquitetura do xamarin. Mac
description: Este guia explora o xamarin. Mac e sua relação com Objective-C em um nível baixo. Ele explica os conceitos, como a compilação, seletores, registradores, inicialização do aplicativo e o gerador.
ms.prod: xamarin
ms.assetid: 74D1FF57-4F2A-4646-8669-003DE99671D4
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 04/12/2017
ms.openlocfilehash: 1ea38b527acaa89b9f25690de4e55664a7afd9e8
ms.sourcegitcommit: d09391c315336d36496880ef465a72b8974f2ac7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2018
ms.locfileid: "51579824"
---
# <a name="xamarinmac-architecture"></a>Arquitetura do xamarin. Mac

_Este guia explora o xamarin. Mac e sua relação com Objective-C em um nível baixo. Ele explica os conceitos, como a compilação, seletores, registradores, inicialização do aplicativo e o gerador._

## <a name="overview"></a>Visão geral

Aplicativos xamarin. Mac executado dentro do ambiente de execução Mono e usam o compilador do Xamarin para compilar para baixo para IL (linguagem intermediária), que é, em seguida, Just-in-Time (JIT) compilado para código nativo em tempo de execução. Isso é executado lado a lado com o tempo de execução do Objective-C. Ambos os ambientes de tempo de execução executados sobre um kernel do UNIX-like, especificamente XNU e expõem várias APIs ao código do usuário, permitindo que os desenvolvedores acessem o sistema subjacente nativo ou gerenciado.

O diagrama a seguir mostra uma visão geral básica dessa arquitetura:

[![Diagrama que mostra uma visão geral básica da arquitetura](architecture-images/mac-arch.png "diagrama que mostra uma visão geral básica da arquitetura")](architecture-images/mac-arch-large.png#lightbox)

### <a name="native-and-managed-code"></a>Código nativo e gerenciado

Ao desenvolver para o Xamarin, os termos *nativos* e *gerenciados* código geralmente são usados. Código gerenciado é o código que tem sua execução gerenciada pelo .NET Framework Common Language Runtime ou, no caso do Xamarin: o tempo de execução Mono.

Código nativo é o código que será executado nativamente na plataforma específica (por exemplo, Objective-C ou até mesmo código compilado de AOT, em um chip ARM). Este guia explora como o código gerenciado é compilado para código nativo e explica como funciona um aplicativo xamarin. Mac, fazendo uso completo de APIs de Mac da Apple com o uso de associações, enquanto também tem acesso ao. BCL da rede e uma linguagem sofisticada, como C#.

## <a name="requirements"></a>Requisitos

O listado a seguir é necessário para desenvolver um aplicativo do macOS X com Xamarin.Mac:

- Um Mac executando macOS Sierra (10.12) ou maior.
- A versão mais recente do Xcode (instalado a partir de [App Store](https://itunes.apple.com/us/app/xcode/id497799835?mt=12))
- A versão mais recente do xamarin. Mac e Visual Studio para Mac

Aplicativos Mac em execução criados com Xamarin.Mac têm os seguintes requisitos do sistema:

- Um Mac executando o Mac OS X 10.7 ou posterior.

## <a name="compilation"></a>Compilação

Quando você compilar qualquer aplicativo da plataforma Xamarin, o Mono C# (ou F#) compilador será executado e compilará seu C# e F# código em Microsoft Intermediate Language (MSIL ou IL). Xamarin. Mac, em seguida, usa uma *apenas no tempo (JIT)* compilador em tempo de execução para compilar o código nativo, permitindo que a execução na arquitetura correta conforme necessário.

Isso é diferente de xamarin. IOS que usa a compilação AOT. Ao usar o compilador AOT, todos os assemblies e todos os métodos dentro deles são compilados no momento da compilação. Com o JIT, a compilação acontece sob demanda somente para os métodos que são executados.

Com aplicativos xamarin. Mac, o Mono normalmente é inserido em lote de aplicativo (e conhecida como **Embedded Mono**). Ao usar a API clássica do xamarin. Mac, o aplicativo poderá usar **sistema Mono**, no entanto, isso não é suportado na API unificada. Sistema Mono refere-se em Mono que foi instalado no sistema operacional. Na inicialização do aplicativo, o aplicativo xamarin. Mac usará.

## <a name="selectors"></a>Seletores

Com o Xamarin, temos dois ecossistemas separadas, .NET e da Apple, o que precisamos para trazer juntos para parecer como simplificada quanto possível, para garantir que o objetivo final é uma experiência do usuário uniforme. Temos visto na seção acima como dois tempos de execução se comunicam e você deve ter ouvido muito bem do termo 'associações' que permite que as APIs de Mac nativo a ser usado no Xamarin. Associações são explicadas em detalhes na [documentação de associação do Objective-C](~/mac/platform/binding.md), portanto, por enquanto, vamos explorar como o xamarin. Mac funciona nos bastidores.

Primeiro, deve haver uma maneira de expor Objective-C para C#, que é feito por meio de seletores. Um seletor é uma mensagem que é enviada para um objeto ou classe. Com o Objective-C, isso é feito por meio de [objc_msgSend](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ObjCRuntimeRef/index.html) funções. Para obter mais informações sobre como usar seletores, consulte o iOS [seletores de Objective-C](~/ios/internals/objective-c-selectors.md) guia. Também deve haver uma maneira de expor o código gerenciado para Objective-C, que é mais complicado devido ao fato de que Objective-C não sabe nada sobre o código gerenciado. Para contornar isso, usamos uma [registrador](~/mac/internals/registrar.md). Isso é explicado mais detalhadamente na próxima seção.

## <a name="registrar"></a>Registrador

Conforme mencionado acima, o registrador é código que expõe o código gerenciado para Objective-C. Ele faz isso criando uma lista de todas as classes gerenciadas que deriva de NSObject:

- Para todas as classes que não são quebra automática de uma classe existente do Objective-C, ele cria uma nova classe de Objective-C com membros de Objective-C, espelhamento de todos os membros gerenciados que têm um `[Export]` atributo.
- Em implementações para cada membro de Objective-C, o código é adicionado automaticamente ao chamar o membro gerenciado espelhado.

O pseudocódigo a seguir mostra um exemplo de como isso é feito:

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

O código gerenciado pode conter os atributos `[Register]` e `[Export]`, que usa o registrador de saber que o objeto precisa ser exposta para Objective-C. O atributo [registrar] é usado para especificar o nome da classe Objective-C gerada caso o nome padrão gerado não é adequado. Todas as classes derivadas de NSObject são automaticamente registradas com Objective-C. O atributo obrigatório do [exportação] contém uma cadeia de caracteres, que é o seletor usado na classe gerada Objective-C.

Há dois tipos de registradores usados no xamarin. Mac – dinâmica e estática:

- Registradores dinâmicos – este é o registrador de padrão para todos os builds do xamarin. Mac. O registrador dinâmico faz o registro de todos os tipos em seu assembly em tempo de execução. Ele faz isso usando as funções fornecidas pelo tempo de execução do Objective-C API. O registrador dinâmico, portanto, tem uma inicialização mais lenta, mas um mais rápido do tempo de compilação. Funções nativas (normalmente em C), chamadas trampolines, são usadas como implementações de método ao usar os registradores dinâmicos. Eles variam entre arquiteturas diferentes.
- Registradores estáticos – o registrador estático gera código Objective-C durante a compilação, o que, em seguida, é compilada em uma biblioteca estática e vinculada no executável. Isso permite uma inicialização mais rápida, mas leva mais tempo durante o tempo de compilação.

## <a name="application-launch"></a>Inicialização do aplicativo

Lógica de inicialização do xamarin. Mac serão diferentes dependendo se inseridos ou sistema Mono é usado. Para exibir o código e as etapas para a inicialização do aplicativo xamarin. Mac, consulte a [cabeçalho de inicialização](https://github.com/xamarin/xamarin-macios/blob/master/runtime/xamarin/launch.h) arquivo no repositório público macios xamarin.

## <a name="generator"></a>Gerador

Xamarin. Mac contém definições para todas as APIs de Mac. Você pode procurar por meio de qualquer um na [repositório do github MaciOS](https://github.com/xamarin/xamarin-macios/tree/master/src). Essas definições contêm interfaces com atributos, bem como quaisquer métodos necessários e propriedades. Por exemplo, o código a seguir é usado para definir um NSBox na [AppKit namespace](https://github.com/xamarin/xamarin-macios/blob/master/src/appkit.cs#L1465-L1526). Observe que se trata de uma interface com um número de métodos e propriedades:

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

O gerador, chamado `bmac` no xamarin. Mac, usa esses arquivos de definição e usa as ferramentas do .NET para compilá-los em um assembly temporário. No entanto, esse assembly temporário não é utilizável para chamar o código Objective-C. O gerador, em seguida, lê o assembly temporário e gera C# código que pode ser usado em tempo de execução. Isso é por que, por exemplo, se você adicionar um atributo aleatório ao seu arquivo de definição de. cs, ele não aparecerá no código emitido. O gerador não o conhece e, portanto, `bmac` não sabe para procurar o assembly temporário de saída.

Depois que o xamarin tiver sido criado, o empacotador `mmp`, empacotará todos os componentes juntos.

Em um alto nível, ele realiza isso executando as seguintes tarefas:

- Crie uma estrutura de pacote do aplicativo.
- Copiar em seus assemblies gerenciados.
- Se a vinculação estiver habilitada, execute o vinculador gerenciado para otimizar seus assemblies, removendo as partes não usadas.
- Crie um aplicativo de iniciador, o código de iniciador falou sobre juntamente com o código do registrador em caso de modo estático de vinculação.

Isso é, em seguida, executar como parte do usuário criar processo que compila o código do usuário em um assembly que referenciam xamarin e execuções `mmp` para torná-lo em um pacote

Para obter mais informações sobre o vinculador e como elas são usadas, consulte o iOS [vinculador](~/ios/deploy-test/linker.md) guia.

## <a name="summary"></a>Resumo

Este guia examinou a compilação de aplicativos do xamarin. Mac e xamarin. Mac explorado e sua relação com Objective-C.
