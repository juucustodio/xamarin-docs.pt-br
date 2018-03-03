---
title: Controles personalizados no Designer de Xamarin para iOS
description: "O Designer de Xamarin para iOS oferece suporte a controles de renderização personalizada criada em seu projeto ou referenciado de fontes externas, como o armazenamento do componente Xamarin."
ms.topic: article
ms.prod: xamarin
ms.assetid: D8F07D63-B006-4050-9D1B-AC6FCDA71B99
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: 83ec11ab6a17717dd9556122745afc8d87959186
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="custom-controls-in-the-xamarin-designer-for-ios"></a>Controles personalizados no Designer de Xamarin para iOS

_O Designer de Xamarin para iOS oferece suporte a controles de renderização personalizada criada em seu projeto ou referenciado de fontes externas, como o armazenamento do componente Xamarin._

O Designer de Xamarin para iOS é uma ferramenta poderosa para visualizar a interface do usuário do aplicativo e fornece suporte para a maioria das exibições de iOS e controladores de exibição de edição WYSIWYG. Seu aplicativo também pode conter controles personalizados que estendem aqueles criados no iOS. Se esses controles personalizados são gravados com algumas diretrizes em mente, eles também podem ser renderizados pelo iOS Designer, fornecendo uma experiência de edição ainda melhores. Este documento aborda as diretrizes.

## <a name="requirements"></a>Requisitos

Um controle que atende aos seguintes requisitos será renderizado na superfície de design:

1.  É uma subclasse direta ou indireta de [UIView](https://developer.xamarin.com/api/type/UIKit.UIView/) ou [UIViewController](https://developer.xamarin.com/api/type/UIKit.UIView/Controller). Outros [NSObject](https://developer.xamarin.com/api/type/Foundation.NSObject/) subclasses aparecerá como um ícone na superfície de design.
2.  Ele tem um [RegisterAttribute](https://developer.xamarin.com/api/type/Foundation.RegisterAttribute/) para expô-lo para o objetivo-C.
3.  Ele tem [o construtor IntPtr necessário](~/ios/internals/api-design/index.md).
4.  Ele implementa tanto o [IComponent](https://developer.xamarin.com/api/type/System.ComponentModel.IComponent/) interface ou tem um [DesignTimeVisibleAttribute](https://developer.xamarin.com/api/type/System.ComponentModel.DesignTimeVisibleAttribute/) definido como True.

Controles definidos no código que atendem aos requisitos acima serão exibido no designer quando seu projeto é compilado para o simulador. Por padrão, todos os controles personalizados aparecerá no **componentes personalizados** seção o **caixa de ferramentas**. No entanto, a [CategoryAttribute](https://developer.xamarin.com/api/type/System.ComponentModel.CategoryAttribute/) pode ser aplicado à classe do controle personalizado para especificar uma seção diferente.

O designer não oferece suporte ao carregar bibliotecas de Objective-C de terceiros.

## <a name="custom-properties"></a>Propriedades personalizadas

Uma propriedade declarada por um controle personalizado aparecerá no painel de propriedade se as seguintes condições forem atendidas:

1.  A propriedade tem um getter público e um setter.
1.  A propriedade tem um [ExportAttribute](https://developer.xamarin.com/api/type/Foundation.ExportAttribute/) , bem como a [BrowsableAttribute](https://developer.xamarin.com/api/type/System.ComponentModel.BrowsableAttribute/) definido como True.
1.  O tipo de propriedade é um tipo numérico, o tipo de enumeração, a cadeia de caracteres, o bool, [SizeF](https://developer.xamarin.com/api/type/System.Drawing.SizeF/), [UIColor](https://developer.xamarin.com/api/type/UIKit.UIColor/), ou [UIImage](https://developer.xamarin.com/api/type/UIKit.UIImage/). Esta lista de tipos com suporte pode ser expandida no futuro.


A propriedade também pode ser decorada com um [DisplayNameAttribute](https://developer.xamarin.com/api/type/System.ComponentModel.DisplayNameAttribute/) para especificar o rótulo que é exibido para ele no painel de propriedade.

## <a name="initialization"></a>Inicialização

Para `UIViewController` subclasses, você deve usar o [ViewDidLoad](https://developer.xamarin.com/api/member/UIKit.UIViewController.ViewDidLoad/) método do código que depende de modos de exibição criados no designer.

Para `UIView` e outros `NSObject` subclasses de [AwakeFromNib](https://developer.xamarin.com/api/member/Foundation.NSObject.AwakeFromNib/) método é o local recomendado para executar a inicialização do seu controle personalizado após o carregamento do arquivo de layout. Isso ocorre porque as propriedades personalizadas definidas no painel de propriedade não serão definidas quando o construtor do controle é executado, mas eles serão definidos antes de `AwakeFromNib` é chamado:


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

Se o controle também foi projetado para ser criado diretamente no código, convém criar um método que tem o código de inicialização comuns, como este:

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

## <a name="property-initialization-and-awakefromnib"></a>AwakeFromNib e inicialização de propriedade

Tome cuidado sobre quando e onde inicializar projetáveis propriedades em um componente personalizado para não substituir os valores que foram definidos dentro do Designer do iOS. Por exemplo, execute o seguinte código:

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

O `CustomView` componente expõe um `Counter` propriedade que pode ser definida pelo desenvolvedor de dentro do Designer do iOS. No entanto, independentemente de qual é o valor é definido dentro do designer, o valor da `Counter` propriedade sempre será zero (0). Eis o porquê:

-  Uma instância do `CustomControl` é inflada do arquivo de Storyboard.
-  Quaisquer propriedades modificadas no designer de iOS são definidas (como definir o valor da `Counter` para dois (2), por exemplo).
-  O `AwakeFromNib` método é executado e é feita uma chamada para o componente `Initialize` método.
-  Dentro de `Initialize` o valor de `Counter` propriedade está sendo redefinida para zero (0).


Para corrigir a situação acima, iniciar o `Counter` propriedade em outro lugar (como construtor o componente do) ou não substituir o `AwakeFromNib` método e chamada `Initialize` se sem inicialização adicional fora o que exige que o componente no momento está sendo tratado por seus construtores.

## <a name="design-mode"></a>Modo de design

Na superfície de design, um controle personalizado deve aderir às algumas restrições:

-  Recursos do pacote de aplicativo não estão disponíveis no modo de design. As imagens estão disponíveis quando carregado por meio de [UIImage métodos](https://developer.xamarin.com/api/type/UIKit.UIImage/%2fM) .
-  Operações assíncronas, como solicitações da web, não devem ser executadas no modo de design. A superfície de design não dá suporte a animação ou qualquer outra atualização assíncrona para interface do usuário do controle.


Um controle personalizado pode implementar [IComponent](https://developer.xamarin.com/api/type/System.ComponentModel.IComponent/) e usar o [DesignMode](https://developer.xamarin.com/api/property/System.ComponentModel.ISite.DesignMode/) propriedade para verificar se ele está na superfície de design. Neste exemplo, o rótulo será exibido "Modo de Design" na superfície de design e "Runtime" em tempo de execução:

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
        if (Site != null &amp;&amp; Site.DesignMode)
            Text = "Design Mode";
        else
            Text = "Runtime";
    }
}
```

Você sempre deve verificar o `Site` propriedade `null` antes de tentar acessar qualquer um dos seus membros. Se `Site` é `null`, é seguro assumir o controle não está em execução no designer.
No modo de design, `Site` será definido depois que o construtor do controle foi executado e antes de `AwakeFromNib` é chamado.

## <a name="debugging"></a>Depuração

Um controle que atenda aos requisitos acima será exibido na caixa de ferramentas e renderizado na superfície.
Se um controle não é processado, verificar erros no controle ou uma de suas dependências.

A superfície de design geralmente pode capturar exceções geradas por controles individuais enquanto continua a processar outros controles. O controle com falha é substituído por um espaço reservado para vermelho, e você pode exibir o rastreamento de exceção clicando no ícone de exclamação:

 ![](ios-designable-controls-overview-images/exception-box.png "Um controle com falha como espaço reservado vermelho e os detalhes da exceção")

Se os símbolos de depuração estão disponíveis para o controle, o rastreamento terá números de linha e de nomes de arquivo. Uma linha em que o rastreamento de pilha de clicando duas vezes saltará para essa linha no código-fonte.

Se o designer não pode isolar o controle com falha, uma mensagem de aviso será exibida na parte superior da superfície de design:

 ![](ios-designable-controls-overview-images/info-bar.png "Uma mensagem de aviso na parte superior da superfície de design")

Processamento completo será retomado quando o controle com falha é corrigido ou removido da superfície de design.

## <a name="summary"></a>Resumo

Este artigo introduziu a criação e aplicação de controles personalizados no designer de iOS. Primeiro, ele descrito requisitos controles devem atender para ser renderizado na superfície de design e expor propriedades personalizadas no painel de propriedade. Em seguida, examinamos o code-behind - inicialização do controle e a propriedade DesignMode. Por fim, ele descrito o que acontece quando as exceções são geradas e como resolver o problema.