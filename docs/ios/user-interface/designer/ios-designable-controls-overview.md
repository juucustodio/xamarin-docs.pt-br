---
title: Controles personalizados na Xamarin Designer para iOS
description: O Xamarin Designer para iOS dá suporte à renderização de controles personalizados criados em seu projeto ou referenciados de fontes externas, como a loja de componentes do Xamarin.
ms.prod: xamarin
ms.assetid: D8F07D63-B006-4050-9D1B-AC6FCDA71B99
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/22/2017
ms.openlocfilehash: 51afbdf79248af6f76426dd0e0c862e506a0a22f
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70768775"
---
# <a name="custom-controls-in-the-xamarin-designer-for-ios"></a>Controles personalizados na Xamarin Designer para iOS

_O Xamarin Designer para iOS dá suporte à renderização de controles personalizados criados em seu projeto ou referenciados de fontes externas, como a loja de componentes do Xamarin._

A Xamarin Designer para iOS é uma ferramenta poderosa para visualizar a interface do usuário de um aplicativo e fornece suporte à edição WYSIWYG para a maioria dos modos de exibição e controladores de exibições do iOS. Seu aplicativo também pode conter controles personalizados que estendem aqueles incorporados ao iOS. Se esses controles personalizados forem escritos com algumas diretrizes em mente, eles também poderão ser renderizados pelo designer do iOS, oferecendo uma experiência de edição ainda mais rica. Este documento dá uma olhada nessas diretrizes.

## <a name="requirements"></a>Requisitos

Um controle que atende a todos os requisitos a seguir será renderizado na superfície de design:

1. É uma subclasse direta ou indireta de [UIView](xref:UIKit.UIView) ou [UIViewController](xref:UIKit.UIViewController). Outras subclasses [NSObject](xref:Foundation.NSObject) serão exibidas como um ícone na superfície de design.
2. Ele tem um [registrattribute](xref:Foundation.RegisterAttribute) para expô-lo ao Objective-C.
3. Ele tem [o Construtor IntPtr necessário](~/ios/internals/api-design/index.md).
4. Ele implementa a interface [IComponent](xref:System.ComponentModel.IComponent) ou tem um [DesignTimeVisibleAttribute](xref:System.ComponentModel.DesignTimeVisibleAttribute) definido como true.

Os controles definidos no código que atendem aos requisitos acima aparecerão no designer quando o projeto recipiente for compilado para o simulador. Por padrão, todos os controles personalizados serão exibidos na seção **componentes personalizados** da **caixa de ferramentas**. No entanto, o [CategoryAttribute](xref:System.ComponentModel.CategoryAttribute) pode ser aplicado à classe do controle personalizado para especificar uma seção diferente.

O designer não dá suporte ao carregamento de bibliotecas de Objective-C de terceiros.

## <a name="custom-properties"></a>Propriedades personalizadas

Uma propriedade declarada por um controle personalizado aparecerá no painel de propriedades se as seguintes condições forem atendidas:

1. A propriedade tem um getter e setter públicos.
1. A propriedade tem um [ExportAttribute](xref:Foundation.ExportAttribute) , bem como um [BrowsableAttribute](xref:System.ComponentModel.BrowsableAttribute) definido como true.
1. O tipo de propriedade é um tipo numérico, tipo de enumeração, Cadeia de caracteres, bool, [SizeF](xref:System.Drawing.SizeF), [UIColor](xref:UIKit.UIColor)ou [UIImage](xref:UIKit.UIImage). Essa lista de tipos com suporte pode ser expandida no futuro.

A propriedade também pode ser decorada com um [DisplayNameAttribute](xref:System.ComponentModel.DisplayNameAttribute) para especificar o rótulo que é exibido para ele no painel de propriedades.

## <a name="initialization"></a>Inicialização

Para `UIViewController` subclasses, você deve usar o método [ViewDidLoad](xref:UIKit.UIViewController.ViewDidLoad) para o código que depende de exibições criadas no designer.

Para `UIView` o e `NSObject` outras subclasses, o método [AwakeFromNib](xref:Foundation.NSObject.AwakeFromNib) é o local recomendado para executar a inicialização do seu controle personalizado depois que ele é carregado a partir do arquivo de layout. Isso ocorre porque qualquer propriedade personalizada definida no painel de propriedades não será definida quando o construtor do controle for executado, mas será definido antes `AwakeFromNib` de ser chamado:

```csharp
[Register ("CustomView"), DesignTimeVisible (true)]
public class CustomView : UIView {

    public CustomView (IntPtr handle) : base (handle) { }

    public override void AwakeFromNib ()
    {
        // Initialize the view here.
    }
}
```

Se o controle também for projetado para ser criado diretamente do código, talvez você queira criar um método que tenha um código de inicialização comum, como este:

```csharp
[Register ("CustomView"), DesignTimeVisible (true)]
public class CustomView : UIView {

    public CustomView (IntPtr handle) : base (handle) { }

    public CustomView ()
    {
        // Called when created from code.
        Initialize ();
    }

    public override void AwakeFromNib ()
    {
        // Called when loaded from xib or storyboard.
        Initialize ();
    }

    void Initialize ()
    {
        // Common initialization code here.
    }
}
```

## <a name="property-initialization-and-awakefromnib"></a>Inicialização de propriedade e AwakeFromNib

Deve-se tomar cuidado quando e onde inicializar as propriedades designáveis em um componente personalizado para não substituir valores que foram definidos no designer do iOS. Por exemplo, use o seguinte código:

```csharp
[Register ("CustomView"), DesignTimeVisible (true)]
public class CustomView : UIView {

    [Export ("Counter"), Browsable (true)]
    public int Counter {get; set;}

    public CustomView (IntPtr handle) : base (handle) { }

    public CustomView ()
    {
        // Called when created from code.
        Initialize ();
    }

    public override void AwakeFromNib ()
    {
        // Called when loaded from xib or storyboard.
        Initialize ();
    }

    void Initialize ()
    {
        // Common initialization code here.
        Counter = 0;
    }
}
```

O `CustomView` componente expõe uma `Counter` propriedade que pode ser definida pelo desenvolvedor dentro do designer do Ios. No entanto, independentemente do valor definido dentro do designer, o valor da `Counter` propriedade sempre será zero (0). Eis o porquê:

- Uma instância do `CustomControl` é inplanada do arquivo de storyboard.
- Todas as propriedades modificadas no designer do IOS são definidas (como definir o valor de `Counter` para dois (2), por exemplo).
- O `AwakeFromNib` método é executado e uma chamada é feita ao método do `Initialize` componente.
- Dentro `Initialize` do valor `Counter` da propriedade está sendo redefinida como zero (0).

Para corrigir a situação acima, inicialize a `Counter` Propriedade em outro lugar (como no construtor do componente) ou não substitua o `AwakeFromNib` método e chame `Initialize` se o componente não exigir nenhuma inicialização adicional fora do que está sendo manipulado atualmente por seus construtores.

## <a name="design-mode"></a>Modo de design

Na superfície de design, um controle personalizado deve aderir a algumas restrições:

- Os recursos do pacote de aplicativos não estão disponíveis no modo de design. As imagens estão disponíveis quando carregadas por meio de [métodos UIImage](xref:UIKit.UIImage) .
- Operações assíncronas, como solicitações da Web, não devem ser executadas no modo de design. A superfície de design não dá suporte à animação ou a qualquer outra atualização assíncrona na interface do usuário do controle.

Um controle personalizado pode implementar o [IComponent](xref:System.ComponentModel.IComponent) e usar a propriedade [DesignMode](xref:System.ComponentModel.ISite.DesignMode) para verificar se ele está na superfície de design. Neste exemplo, o rótulo exibirá "modo de design" na superfície de design e "tempo de execução" em tempo de execução:

```csharp
[Register ("DesignerAwareLabel")]
public class DesignerAwareLabel : UILabel, IComponent {

    #region IComponent implementation

    public ISite Site { get; set; }
    public event EventHandler Disposed;

    #endregion

    public DesignerAwareLabel (IntPtr handle) : base (handle) { }

    public override void AwakeFromNib ()
    {
        if (Site != null && Site.DesignMode)
            Text = "Design Mode";
        else
            Text = "Runtime";
    }
}
```

Você sempre deve verificar a `Site` propriedade para `null` antes de tentar acessar qualquer um de seus membros. Se `Site` for`null`, é seguro assumir que o controle não está em execução no designer.
No modo de design `Site` , será definido depois que o construtor do controle tiver sido executado `AwakeFromNib` e antes de ser chamado.

## <a name="debugging"></a>Depuração

Um controle que atenda aos requisitos acima será exibido na caixa de ferramentas e renderizado na superfície.
Se um controle não for renderizado, verifique se há bugs no controle ou em uma de suas dependências.

A superfície de design geralmente pode capturar exceções geradas por controles individuais enquanto continua a renderizar outros controles. O controle com falha é substituído por um espaço reservado vermelho e você pode exibir o rastreamento de exceção clicando no ícone de exclamação:

 ![](ios-designable-controls-overview-images/exception-box.png "Um controle com falha como espaço reservado vermelho e os detalhes da exceção")

Se os símbolos de depuração estiverem disponíveis para o controle, o rastreamento terá nomes de arquivo e números de linha.
Clicar duas vezes em uma linha no rastreamento de pilha vai para essa linha no código-fonte.

Se o designer não puder isolar o controle com falha, uma mensagem de aviso será exibida na parte superior da superfície de design:

 ![](ios-designable-controls-overview-images/info-bar.png "Uma mensagem de aviso na parte superior da superfície de design")

A renderização completa será retomada quando o controle com falha for corrigido ou removido da superfície de design.

## <a name="summary"></a>Resumo

Este artigo apresentou a criação e a aplicação de controles personalizados no designer do iOS. Primeiro, ele descreveu os requisitos que os controles devem atender para serem renderizados na superfície de design e expõem propriedades personalizadas no painel de propriedades. Em seguida, examinamos o código por trás da inicialização do controle e a propriedade DesignMode. Por fim, ele descreveu o que acontece quando as exceções são geradas e como resolver isso.
