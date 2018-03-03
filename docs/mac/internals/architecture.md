---
title: Arquitetura de Xamarin.Mac
description: "Este guia explora Xamarin.Mac e sua relação com Objective-C em um nível baixo. Explica conceitos como a compilação, seletores, registradores, abrir o aplicativo e o gerador."
ms.topic: article
ms.prod: xamarin
ms.assetid: 74D1FF57-4F2A-4646-8669-003DE99671D4
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 04/12/2017
ms.openlocfilehash: 4e4392a52035c8285bc746da75e93fcb8e7c7695
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="xamarinmac-architecture"></a>Arquitetura de Xamarin.Mac

_Este guia explora Xamarin.Mac e sua relação com Objective-C em um nível baixo. Explica conceitos como a compilação, seletores, registradores, abrir o aplicativo e o gerador._

## <a name="overview"></a>Visão geral

Aplicativos Xamarin.Mac executado dentro do ambiente de execução Mono e usam o compilador do Xamarin para compilar para a linguagem intermediária (IL), que é, em seguida, Just-in-Time (JIT) compilado para código nativo em tempo de execução. Isso é executado lado a lado com o tempo de execução Objective-C. Ambos os ambientes de tempo de execução são executados em um kernel UNIX-like, especificamente XNU e expõem várias APIs para o código de usuário permitindo que os desenvolvedores acessar o sistema nativo ou gerenciado subjacente.

O diagrama a seguir mostra uma visão geral básica dessa arquitetura:

[![Diagrama que mostra uma visão geral da arquitetura do](architecture-images/mac-arch.png "diagrama que mostra uma visão geral básica da arquitetura")](architecture-images/mac-arch-large.png)

### <a name="native-and-managed-code"></a>Código gerenciado e nativo

Ao desenvolver para Xamarin, os termos de *nativo* e *gerenciado* código são usadas com frequência. Código gerenciado é um código que tem sua execução gerenciada pelo .NET Framework Common Language Runtime ou, no caso do Xamarin: o tempo de execução Mono.

O código nativo é o código que será executado em modo nativo na plataforma específica (por exemplo, Objective-C ou até mesmo código AOT compilado em um chip do ARM). Este guia explora como o código gerenciado é compilado em código nativo e explica como funciona um aplicativo Xamarin.Mac, fazer uso total da Apple Mac APIs com o uso de associações, enquanto também tem acesso ao. BCL do NET e uma linguagem sofisticada, como c#.

## <a name="requirements"></a>Requisitos

O listado a seguir é necessário para desenvolver um aplicativo do macOS X com Xamarin.Mac:

- Um macOS em execução do Mac Serra (10.12) ou superior.
- A versão mais recente do Xcode (instalado a partir de [App Store](https://itunes.apple.com/us/app/xcode/id497799835?mt=12))
- A versão mais recente do Visual Studio para Mac e Xamarin.Mac

Aplicativos Mac em execução criados com Xamarin.Mac têm os seguintes requisitos do sistema:

- Um Mac executando o Mac OS X 10.7 ou maior.

## <a name="compilation"></a>Compilação

Quando você compila a qualquer aplicativo da plataforma de Xamarin, o compilador c# Mono (ou F #) será executado e será compilar seu código c# e F # em Microsoft Intermediate Language (MSIL ou IL). Xamarin.Mac, em seguida, usa um *apenas no tempo (JIT)* compilador em tempo de execução para compilar o código nativo, permitindo que a execução na arquitetura correta conforme necessário.

Isso é diferente de xamarin que usa AOT compilação. Ao usar o compilador AOT, todos os assemblies e todos os métodos dentro deles são compilados no momento da compilação. Com JIT, compilação acontece sob demanda apenas para os métodos que são executados.

Com aplicativos de Xamarin.Mac Mono geralmente é incorporada em pacote de aplicativo (e a chamada de **Mono incorporado**). Ao usar a API Xamarin.Mac clássico, o aplicativo poderá usar **sistema Mono**, no entanto, isso não tem suporte na API unificada. Sistema Mono se refere a Mono que foi instalado no sistema operacional. Na inicialização do aplicativo, o aplicativo Xamarin.Mac usará.

## <a name="selectors"></a>Seletores

Com o Xamarin, temos duas ecossistemas separadas, .NET e Apple, que são necessárias para trazer para parecer como simplificado quanto possível, para garantir que o objetivo final é uma experiência de usuário uniforme. Vimos na seção acima como se comunicam dois tempos de execução, e você pode muito bem ter ouvido falar do termo 'ligações' que permite que as APIs nativas do Mac ser usado em Xamarin. Associações são explicadas em detalhes o [documentação de associação Objective-C](~/mac/platform/binding.md), portanto, por enquanto, vamos explorar como Xamarin.Mac funciona nos bastidores.

Primeiro, deve haver uma maneira de expor Objective-C para c#, que é feito por meio de seletores. Um seletor é uma mensagem que é enviada a um objeto ou classe. Com Objective-C, isso é feito por meio de [objc_msgSend](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ObjCRuntimeRef/index.html) funções. Para obter mais informações sobre o uso de seletores, consulte o iOS [seletores Objective-C](~/ios/internals/objective-c-selectors.md) guia. Também deve haver uma maneira para expor o código gerenciado para Objective-C, que é mais complicado devido ao fato de que Objective-C não sabe nada sobre o código gerenciado. Para contornar esse problema, usamos um [registrador](~/mac/internals/registrar.md). Isso é explicado mais detalhadamente na próxima seção.

## <a name="registrar"></a>Registrador

Conforme mencionado acima, o registrador é código que expõe o código gerenciado para o objetivo-C. Ele faz isso criando uma lista de todas as classes gerenciadas que deriva de NSObject:

- Para todas as classes que não são quebra automática de uma classe existente em Objective-C, ele cria uma nova classe de Objective-C com membros Objective-C espelhamento todos os membros gerenciados que têm um `[Export]` atributo.
- Em implementações para cada membro Objective-C, o código é adicionado automaticamente ao chamar o membro gerenciado espelhado.

O pseudocódigo a seguir mostra um exemplo de como isso é feito:

**C# (código gerenciado):**

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

O código gerenciado pode conter os atributos `[Register]` e `[Export]`, que usa o registrador de saber que o objeto precisa ser exposto ao objetivo-C. O atributo [registrar] é usado para especificar o nome da classe gerada Objective-C, caso o nome padrão gerado não é adequado. Todas as classes derivadas de NSObject são automaticamente registradas com o objetivo-C. O atributo necessário de exportação] contém uma cadeia de caracteres, que é o seletor usado na classe Objective-C gerado.

Há dois tipos de registradores usados em Xamarin.Mac – dinâmico e estático:

- Registradores dinâmicos – este é o registrador de padrão para todas as compilações Xamarin.Mac. O registro dinâmico faz o registro de todos os tipos em seu assembly em tempo de execução. Ele faz isso usando funções fornecidas pelo tempo de execução do Objective-C API. O registro dinâmico, portanto, tem uma inicialização mais lenta, mas um rápido tempo de compilação. Funções nativas (normalmente em C), chamadas trampolines, são usadas como implementações de método ao usar os registradores dinâmicos. Eles variam entre arquiteturas diferentes.
- Registradores estáticos – o registrador estático gera código Objective-C durante a compilação, o que é compilada em uma biblioteca estática e vinculada para o executável. Isso permite uma inicialização mais rápida, mas leva mais tempo durante o tempo de compilação.

## <a name="application-launch"></a>Iniciar o aplicativo

Lógica de inicialização Xamarin.Mac serão diferentes dependendo se inseridos ou sistema Mono é usado. Para exibir o código e as etapas para iniciar o aplicativo Xamarin.Mac, consulte o [cabeçalho inicialização](https://github.com/xamarin/xamarin-macios/blob/master/runtime/xamarin/launch.h) arquivo no repositório público macios xamarin.

## <a name="generator"></a>Gerador

Xamarin.Mac contém definições para cada API de Mac. Você pode procurar por meio de qualquer um no [MaciOS do repositório github](https://github.com/xamarin/xamarin-macios/tree/master/src). Essas definições contenham interfaces com atributos, bem como métodos necessários e propriedades. Por exemplo, o código a seguir é usado para definir um NSBox no [AppKit namespace](https://github.com/xamarin/xamarin-macios/blob/master/src/appkit.cs#L1465-L1526). Observe que se trata de uma interface com um número de métodos e propriedades:

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

O gerador, chamado `bmac` em Xamarin.Mac, usa esses arquivos de definição e usa ferramentas do .NET para compilá-los em um assembly temporário. No entanto, esse assembly temporário não é utilizável para chamar código Objective-C. O gerador de lê o assembly temporário e gera o código c# que pode ser usado em tempo de execução. Isso é porque, por exemplo, se você adicionar um atributo aleatório para o arquivo de definição de. cs, ele não aparecer no código do meio. O gerador não sabe sobre ele e, portanto, `bmac` não sabe procurá-lo no conjunto temporário de saída.

Quando o Xamarin.Mac.dll tiver sido criado, o Gerenciador de `mmp`, vai agrupar todos os componentes em conjunto.

Em um nível alto, ele realiza isso executando as seguintes tarefas:

- Crie uma estrutura de pacote do aplicativo.
- Copie os assemblies gerenciados.
- Se a vinculação está habilitada, execute o vinculador gerenciado para otimizar seus conjuntos, removendo as partes não usadas.
- Crie um aplicativo do iniciador, vinculando no código do iniciador descreveu junto com o código registar se estiver no modo estático.

Isso é, em seguida, executar como parte do usuário criar processo que compila o código do usuário em um assembly que referenciam Xamarin.Mac.dll e executa `mmp` para torná-lo em um pacote

Para obter mais informações sobre o vinculador e como elas são usadas, consulte o iOS [vinculador](~/ios/deploy-test/linker.md) guia.

## <a name="summary"></a>Resumo

Este guia pesquisados na compilação de aplicativos de Xamarin.Mac e Xamarin.Mac explorado e sua relação com o objetivo-C.
