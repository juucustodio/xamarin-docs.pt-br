---
title: Restrições de Layout programáticas no xamarin. IOS
description: Este guia apresenta ao trabalhar com iOS restrições de Layout automático em C# código em vez de criá-los no Designer do iOS.
ms.prod: xamarin
ms.assetid: 119C8365-B470-4CD4-85F7-086F0A46DCBB
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: 3d8e69af7f790415343abf464ea2bb22e879e025
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50106955"
---
# <a name="programmatic-layout-constraints-in-xamarinios"></a>Restrições de Layout programáticas no xamarin. IOS

_Este guia apresenta ao trabalhar com iOS restrições de Layout automático em C# código em vez de criá-los no Designer do iOS._

Layout automático (também chamado de "layout adaptável") é uma abordagem de design responsivo. Ao contrário do sistema de layout de transição, onde o local de cada elemento é embutido em código para um ponto na tela, o Layout automático é sobre *relações* -as posições de elementos em relação a outros elementos na superfície de design. No coração do Layout automático é a ideia de restrições ou as regras que definem o posicionamento de um elemento ou um conjunto de elementos no contexto de outros elementos na tela. Porque os elementos não estão vinculados a uma determinada posição na tela, restrições de ajudar a criar um layout adaptável que tenha aparência satisfatório em diferentes tamanhos de tela e orientações do dispositivo.

Normalmente ao trabalhar com Layout automático no iOS, você usará o Designer do iOS para colocar graficamente as restrições de Layout de seus itens de interface do usuário. No entanto, pode haver ocasiões em que você precisa para criar e aplicar restrições em C# código. Por exemplo, ao usar dinamicamente criada elementos de interface do usuário adicionados a um `UIView`.

Este guia mostrará como criar e trabalhar com restrições, usando C# código em vez de criá-las graficamente no Designer do iOS.

<a name="Creating-Constraints-Programmatically" />

## <a name="creating-constraints-programmatically"></a>Criando restrições de forma programática

Como mencionado acima, normalmente você estará trabalhando com restrições de Layout automático no Designer do iOS. Para os momentos em que você precisa criar suas restrições de forma programática, você tem três opções à sua escolha:

* [Layout âncoras](#Layout-Anchors) -esta API fornece acesso às propriedades de âncora (como `TopAnchor`, `BottomAnchor` ou `HeightAnchor`) dos itens de interface do usuário que está sendo restringidos.
* [Restrições de layout](#Layout-Constraints) -você pode criar restrições diretamente usando o `NSLayoutConstraint` classe.
* [Linguagem Visual formatação](#Visual-Format-Language) -fornece uma arte ASCII como método para definir suas restrições.

As seções a seguir apresentará cada opção em detalhes.

<a name="Layout-Anchors" />

### <a name="layout-anchors"></a>Âncoras de layout

Usando o `NSLayoutAnchor` classe, você tem uma interface fluente para a criação de restrições com base nas propriedades dos itens de interface do usuário que está sendo restritas âncora. Por exemplo, os guias de layout de partes superior e inferior de um controlador de exibição expõe o `TopAnchor`, `BottomAnchor` e `HeightAnchor` ancorar propriedades enquanto um modo de exibição expõe as propriedades de borda, centro, tamanho e da linha de base.

> [!IMPORTANT]
> Além do conjunto padrão de propriedades de âncora, modos de exibição do iOS também incluem o `LayoutMarginsGuides` e `ReadableContentGuide` propriedades. Essas propriedades expõem `UILayoutGuide` objetos para trabalhar com margens da exibição e legível guias de conteúdo, respectivamente.

Âncoras de layout fornecem vários métodos para criação de restrições em um formato compacto, de fácil leitura:

- **ConstraintEqualTo** -define uma relação em que `first attribute = second attribute + [constant]` com um fornecido opcionalmente `constant` valor de deslocamento.
- **ConstraintGreaterThanOrEqualTo** -define uma relação em que `first attribute >= second attribute + [constant]` com um fornecido opcionalmente `constant` valor de deslocamento.
- **ConstraintLessThanOrEqualTo** -define uma relação em que `first attribute <= second attribute + [constant]` com um fornecido opcionalmente `constant` valor de deslocamento.

Por exemplo:

```csharp
// Get the parent view's layout
var margins = View.LayoutMarginsGuide;

// Pin the leading edge of the view to the margin
OrangeView.LeadingAnchor.ConstraintEqualTo (margins.LeadingAnchor).Active = true;

// Pin the trailing edge of the view to the margin
OrangeView.TrailingAnchor.ConstraintEqualTo (margins.TrailingAnchor).Active = true;

// Give the view a 1:2 aspect ratio
OrangeView.HeightAnchor.ConstraintEqualTo (OrangeView.WidthAnchor, 2.0f);
```

Uma restrição de Layout típico pode ser expresso simplesmente como uma expressão linear. Veja o exemplo a seguir:

[![](programmatic-layout-constraints-images/graph01.png "Uma restrição de Layout, expresso como uma expressão linear")](programmatic-layout-constraints-images/graph01.png#lightbox)

Qual seria convertida para a seguinte linha de C# o código usando as âncoras de Layout:

```csharp
PurpleView.LeadingAnchor.ConstraintEqualTo (OrangeView.TrailingAnchor, 10).Active = true; 
```

Em que as partes do C# código correspondem às partes da equação determinadas da seguinte maneira:

|Equação|Código|
|---|---|
|Item 1|PurpleView|
|Atributo 1|LeadingAnchor|
|Relationship|ConstraintEqualTo|
|Multiplicador|O padrão é 1.0 até não especificado|
|Item 2|OrangeView|
|Atributo 2|TrailingAnchor|
|Constante|10.0|

Além de fornecer apenas os parâmetros que são necessárias para resolver uma equação de restrição de layout fornecido, cada um dos métodos de âncora de Layout impor a segurança de tipos dos parâmetros passados a eles. Restrição então horizontal como âncoras `LeadingAnchor` ou `TrailingAnchor` só pode ser usado com outra âncora horizontal tipos e multiplicadores são fornecidas apenas para restrições de tamanho.

<a name="Layout-Constraints" />

### <a name="layout-constraints"></a>Restrições de layout

Você pode adicionar restrições de Layout automático manualmente criando diretamente um `NSLayoutConstraint` em C# código. Ao contrário de usar as âncoras de Layout, você deve especificar um valor para cada parâmetro, mesmo se ele não terá efeito na restrição que está sendo definido. Como resultado, você acabará produzindo uma quantidade considerável de difícil de ler o código clichê. Por exemplo:

```csharp
//// Pin the leading edge of the view to the margin
NSLayoutConstraint.Create (OrangeView, NSLayoutAttribute.Leading, NSLayoutRelation.Equal, View, NSLayoutAttribute.LeadingMargin, 1.0f, 0.0f).Active = true;

//// Pin the trailing edge of the view to the margin
NSLayoutConstraint.Create (OrangeView, NSLayoutAttribute.Trailing, NSLayoutRelation.Equal, View, NSLayoutAttribute.TrailingMargin, 1.0f, 0.0f).Active = true;

//// Give the view a 1:2 aspect ratio
NSLayoutConstraint.Create (OrangeView, NSLayoutAttribute.Height, NSLayoutRelation.Equal, OrangeView, NSLayoutAttribute.Width, 2.0f, 0.0f).Active = true;
```

Em que o `NSLayoutAttribute` enum define o valor de margens da exibição e correspondem à `LayoutMarginsGuide` propriedades, como `Left`, `Right`, `Top` e `Bottom` e o `NSLayoutRelation` enum define a relação que será criado entre os determinados atributos como `Equal`, `LessThanOrEqual` ou `GreaterThanOrEqual`.

Ao contrário de com a API de âncora de Layout, o `NSLayoutConstraint` métodos de criação não realçar os aspectos importantes de uma restrição específica e não houver nenhuma compilação verificações executadas na restrição de tempo. Como resultado, é fácil construir uma restrição inválida que gerará uma exceção em tempo de execução.

<a name="Visual-Format-Language" />

### <a name="visual-format-language"></a>Idioma do formato Visual

O idioma do formato Visual permite que você defina restrições usando arte ASCII, como cadeias de caracteres que fornecem uma representação visual da restrição que está sendo criada. Isso tem as seguintes vantagens e desvantagens:

- A linguagem Visual do formato impõe a criação de restrições válidas somente.
 - Layout automático gera restrições no console usando o idioma do formato Visual, portanto, as mensagens de depuração serão parecida com o código usado para criar a restrição.
 - O idioma do formato Visual permite que você crie várias restrições ao mesmo tempo com uma expressão muito compacta.
 - Como não há nenhuma validação de compilação lado das cadeias de caracteres de idioma de formato Visual, problemas só podem ser detectados em tempo de execução.
 - Uma vez que o idioma do formato Visual enfatiza a visualização sobre integridade de que alguns tipos de restrição não podem ser criados com ele (como taxas).

Ao usar o idioma de formato Visual para criar uma restrição, você executar as seguintes etapas:

1. Criar um `NSDictionary` que contém os objetos de exibição e guias de Layout e uma chave de cadeia de caracteres que será usada ao definir os formatos.
2. Opcionalmente, crie uma `NSDictionary` que define um conjunto de chaves e valores (`NSNumber`) usado como o valor da constante para a restrição.
3. Crie a cadeia de caracteres de formato para o layout de uma única coluna ou linha de itens.
4. Chame o `FromVisualFormat` método da `NSLayoutConstraint` classe para gerar as restrições.
5. Chame o `ActivateConstraints` método da `NSLayoutConstraint` classe para ativar e aplicar as restrições.

Por exemplo, para criar uma restrição à direita e um líderes no idioma de formato Visual, você pode usar o seguinte:

```csharp
// Get views being constrained
var views = new NSMutableDictionary (); 
views.Add (new NSString ("orangeView"), OrangeView);

// Define format and assemble constraints
var format = "|-[orangeView]-|";
var constraints = NSLayoutConstraint.FromVisualFormat (format, NSLayoutFormatOptions.AlignAllTop, null, views);

// Apply constraints
NSLayoutConstraint.ActivateConstraints (constraints);
```

Como a linguagem Visual do formato cria sempre zero restrições de ponto anexadas às margens do modo de exibição pai ao usar o espaçamento padrão, esse código produz resultados idênticos para os exemplos apresentados acima.

Para designs de interface do usuário mais complexas, como vários modos de exibição filho em uma única linha, a linguagem Visual do formato Especifica o espaçamento horizontal e o alinhamento vertical. Como no exemplo acima em que ele especifica o `AlignAllTop` `NSLayoutFormatOptions` alinha todas as exibições em uma linha ou coluna para suas partes superiores.

Consulte da Apple [apêndice de idioma de formato Visual](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/AutolayoutPG/VisualFormatLanguage.html#//apple_ref/doc/uid/TP40010853-CH27-SW1) para obter alguns exemplos de especificação de restrições comuns e a gramática de cadeia de caracteres de formato Visual.

<a name="Summary" />

## <a name="summary"></a>Resumo

Este guia apresentado criando e trabalhando com restrições de Layout automático em C# em vez de criá-las graficamente no Designer do iOS. Primeiro, ele examinou usando as âncoras de Layout (`NSLayoutAnchor`) para lidar com Layout automático. Em seguida, ele mostrou como trabalhar com restrições de Layout (`NSLayoutConstraint`). Por fim, apresentados usando a linguagem Visual do formato para Layout automático.

## <a name="related-links"></a>Links relacionados

- [Introdução ao Storyboards](~/ios/user-interface/storyboards/index.md)
- [iOS projetáveis passo a passo de controles](~/ios/user-interface/designer/ios-designable-controls-walkthrough.md)
- [Layout automático com o Designer do Xamarin para iOS](~/ios/user-interface/designer/designer-auto-layout.md#modifying-in-code)
- [Apple – criando restrições de forma programática](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/AutolayoutPG/ProgrammaticallyCreatingConstraints.html#//apple_ref/doc/uid/TP40010853-CH16-SW1)
- [Apple – apêndice de idioma do formato Visual](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/AutolayoutPG/VisualFormatLanguage.html#//apple_ref/doc/uid/TP40010853-CH27-SW1)
