---
title: "Restrições de Layout através de programação"
description: "Este guia apresenta restrições de Layout automático de trabalhar com iOS em código c# em vez de criá-las no Designer de iOS."
ms.topic: article
ms.prod: xamarin
ms.assetid: 119C8365-B470-4CD4-85F7-086F0A46DCBB
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: 7819201e76e268ea84bf2cc5d49a5a07b20a04e3
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="programmatic-layout-constraints"></a>Restrições de Layout através de programação

_Este guia apresenta restrições de Layout automático de trabalhar com iOS em código c# em vez de criá-las no Designer de iOS._

Layout automático (também chamado de "adaptável layout") é uma abordagem de design responsivo. Ao contrário do sistema de layout de transição, onde o local de cada elemento é codificado para um ponto na tela, o Layout automático é sobre *relações* -as posições de elementos em relação a outros elementos na superfície de design. A essência do Layout automático é a ideia de restrições ou regras que definem o posicionamento de um elemento ou um conjunto de elementos no contexto de outros elementos na tela. Como os elementos não estão vinculados a uma posição específica na tela, restrições ajudam a criar um layout adaptável que parece bom em diferentes tamanhos de telas e orientações de dispositivo.

Normalmente ao trabalhar com Layout automático no iOS, você usará o Designer do iOS graficamente colocar as restrições de Layout de seus itens de interface do usuário. No entanto, pode haver ocasiões em que você precisa para criar e aplicar restrições de código c#. Por exemplo, ao usar dinamicamente criados elementos de interface do usuário adicionados a um `UIView`.

Este guia mostrará como criar e trabalhar com restrições de código c# em vez de criá-las graficamente no Designer de iOS.

<a name="Creating-Constraints-Programmatically" />

## <a name="creating-constraints-programmatically"></a>Criar restrições de forma programática

Como mencionado acima, normalmente você estará trabalhando com restrições de Layout automático no Designer de iOS. Para os horários em que você precisa criar as restrições de forma programática, você tem três opções à sua escolha:

* [Âncoras de layout](#Layout-Anchors) -essa API fornece acesso às propriedades de âncora (como `TopAnchor`, `BottomAnchor` ou `HeightAnchor`) dos itens de interface do usuário seja restritos.
* [Restrições de layout](#Layout-Constraints) -você pode criar restrições diretamente usando o `NSLayoutConstraint` classe.
* [Formatação de linguagem do Visual](#Visual-Format-Language) -fornece uma arte ASCII como o método para definir as restrições.

As seções a seguir apresentará cada opção detalhadamente.

<a name="Layout-Anchors" />

### <a name="layout-anchors"></a>Âncoras de layout

Usando o `NSLayoutAnchor` classe, você tem uma interface fluente para criar restrições com base nas propriedades dos itens de interface do usuário seja restritos âncora. Por exemplo, os guias de layout de partes superior e inferior de um modo de exibição do controlador expõe o `TopAnchor`, `BottomAnchor` e `HeightAnchor` ancorar propriedades enquanto uma exibição expõe as propriedades de borda, centro, tamanho e da linha de base.

> [!IMPORTANT]
> **Observação:** além do conjunto padrão de propriedades de ancoragem, modos de exibição do iOS também incluem o `LayoutMarginsGuides` e `ReadableContentGuide` propriedades. Essas propriedades expõem `UILayoutGuide` objetos para trabalhar com margens do modo de exibição e legível conteúdo guias respectivamente.

Âncoras de layout fornecem vários métodos para criação de restrições em um formato de fácil leitura, compact:

- **ConstraintEqualTo** -define uma relação onde `first attribute = second attribute + [constant]` com um fornecido opcionalmente `constant` valor de deslocamento.
- **ConstraintGreaterThanOrEqualTo** -define uma relação onde `first attribute >= second attribute + [constant]` com um fornecido opcionalmente `constant` valor de deslocamento.
- **ConstraintLessThanOrEqualTo** -define uma relação onde `first attribute <= second attribute + [constant]` com um fornecido opcionalmente `constant` valor de deslocamento.

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

Uma restrição típica de Layout pode ser expresso apenas como uma expressão linear. Veja o exemplo a seguir:

[![](programmatic-layout-constraints-images/graph01.png "Uma restrição de Layout, expresso como uma expressão linear")](programmatic-layout-constraints-images/graph01.png#lightbox)

Que será convertido para a seguinte linha de código em c# usando âncoras de Layout:

```csharp
PurpleView.LeadingAnchor.ConstraintEqualTo (OrangeView.TrailingAnchor, 10).Active = true; 
```

Onde as partes do código c# correspondem para as partes da equação de determinado da seguinte maneira:

<table width="100%" border="1">
<tr>
<td width="50%"><b>Equação</b></td><td><b>Código</b></td>
</tr>
<tr>
<td width="50%">Item 1</td><td>PurpleView</td>
</tr>
<tr>
<td width="50%">Atributo 1</td><td>LeadingAnchor</td>
</tr>
<tr>
<td width="50%">Relationship</td><td>ConstraintEqualTo</td>
</tr>
<tr>
<td width="50%">Multiplicador</td><td>O padrão é 1.0 assim não especificado</td>
</tr>
<tr>
<td width="50%">Item 2</td><td>OrangeView</td>
</tr>
<tr>
<td width="50%">Atributo 2</td><td>TrailingAnchor</td>
</tr>
<tr>
<td width="50%">Constante</td><td>10.0</td>
</tr>
</table>

Além de fornecer apenas os parâmetros que são necessárias para resolver uma equação de restrição de layout fornecido, cada um dos métodos Layout âncora impor a segurança de tipos dos parâmetros passados a eles. Restrição horizontal assim como âncoras `LeadingAnchor` ou `TrailingAnchor` só pode ser usado com outros âncora horizontal tipos e multiplicadores são fornecidas apenas para restrições de tamanho.

<a name="Layout-Constraints" />

### <a name="layout-constraints"></a>Restrições de layout

Você pode adicionar restrições de Layout automático manualmente criando diretamente um `NSLayoutConstraint` no código c#. Ao contrário de usar âncoras de Layout, você deve especificar um valor para cada parâmetro, mesmo se ele não tem efeito na restrição que está sendo definido. Como resultado, você acabará produzir uma quantidade considerável de difíceis de ler, código padronizado. Por exemplo:

```csharp
//// Pin the leading edge of the view to the margin
NSLayoutConstraint.Create (OrangeView, NSLayoutAttribute.Leading, NSLayoutRelation.Equal, View, NSLayoutAttribute.LeadingMargin, 1.0f, 0.0f).Active = true;

//// Pin the trailing edge of the view to the margin
NSLayoutConstraint.Create (OrangeView, NSLayoutAttribute.Trailing, NSLayoutRelation.Equal, View, NSLayoutAttribute.TrailingMargin, 1.0f, 0.0f).Active = true;

//// Give the view a 1:2 aspect ratio
NSLayoutConstraint.Create (OrangeView, NSLayoutAttribute.Height, NSLayoutRelation.Equal, OrangeView, NSLayoutAttribute.Width, 2.0f, 0.0f).Active = true;
```

Onde o `NSLayoutAttribute` enum define o valor de margens do modo de exibição e correspondem do `LayoutMarginsGuide` propriedades, como `Left`, `Right`, `Top` e `Bottom` e o `NSLayoutRelation` enum define a relação que será criada entre os determinados atributos como `Equal`, `LessThanOrEqual` ou `GreaterThanOrEqual`.

Ao contrário de com a API de âncora de Layout, o `NSLayoutConstraint` métodos de criação não realçar os aspectos importantes de uma restrição específica e não houver nenhuma compilação verificações executadas na restrição de tempo. Como resultado, é fácil criar uma restrição inválida que gera uma exceção em tempo de execução.

<a name="Visual-Format-Language" />

### <a name="visual-format-language"></a>Idioma de formato Visual

O idioma de formato Visual permite que você defina restrições usando arte ASCII como cadeias de caracteres que fornecem uma representação visual da restrição que está sendo criada. Isso tem as seguintes vantagens e desvantagens:

- O idioma de formato Visual impõe a criação de restrições válidas apenas.
 - Layout automático gera restrições para o console usando a linguagem Visual de formato para as mensagens de depuração serão parecida com o código usado para criar a restrição.
 - O idioma de formato Visual permite que você crie várias restrições ao mesmo tempo com uma expressão muito compact.
 - Como não há nenhuma validação do lado do compilação das cadeias de caracteres do idioma de formato Visual, problemas só podem ser descobertos no tempo de execução.
 - Como o idioma de formato Visual enfatiza a visualização sobre integridade de que alguns tipos de restrição não podem ser criados com ele (como taxas).

Ao usar a linguagem Visual de formato para criar uma restrição, você executar as etapas a seguir:

1. Criar um `NSDictionary` que contém os objetos de exibição e guias de Layout e uma chave de cadeia de caracteres que será usada ao definir os formatos.
2. Opcionalmente, crie um `NSDictionary` que define um conjunto de chaves e valores (`NSNumber`) usado como o valor constante para a restrição.
3. Crie a cadeia de caracteres de formato para o layout de uma única coluna ou linha dos itens.
4. Chamar o `FromVisualFormat` método o `NSLayoutConstraint` classe para gerar as restrições.
5. Chamar o `ActivateConstraints` método o `NSLayoutConstraint` classe para ativar e aplicar as restrições.

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

Como o idioma de formato Visual sempre cria zero restrições ponto anexadas para as margens da exibição pai ao usar o espaçamento padrão, esse código gera resultados idênticos para os exemplos apresentados a seguir.

Para designs de interface do usuário mais complexos, como várias exibições filho em uma única linha, a linguagem Visual de formato Especifica o espaçamento horizontal e o alinhamento vertical. Como no exemplo acima em que ele especifica o `AlignAllTop` `NSLayoutFormatOptions` alinha todos os modos de exibição em uma linha ou coluna para suas partes superiores.

Consulte da Apple [Visual apêndice de idioma de formato](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/AutolayoutPG/VisualFormatLanguage.html#//apple_ref/doc/uid/TP40010853-CH27-SW1) para obter alguns exemplos de especificação de restrições comuns e a gramática de cadeia de caracteres de formato Visual.

<a name="Summary" />

## <a name="summary"></a>Resumo

Este guia apresentados criando e trabalhando com restrições de Layout automático no c# em vez de criá-las graficamente no Designer de iOS. Primeiro, ele visto usando âncoras de Layout (`NSLayoutAnchor`) para lidar com Layout automático. Em seguida, ele mostrou como trabalhar com restrições de Layout (`NSLayoutConstraint`). Por fim, apresentados usando a linguagem Visual de formato para Layout automático.

## <a name="related-links"></a>Links relacionados

- [Introdução ao Storyboards](~/ios/user-interface/storyboards/index.md)
- [iOS projetáveis controles passo a passo](~/ios/user-interface/designer/ios-designable-controls-walkthrough.md)
- [Layout automático com o Designer de Xamarin para iOS](~/ios/user-interface/designer/designer-auto-layout.md#modifying-in-code)
- [Apple - programaticamente a criação de restrições](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/AutolayoutPG/ProgrammaticallyCreatingConstraints.html#//apple_ref/doc/uid/TP40010853-CH16-SW1)
- [Apple - formato Visual idioma Apêndice](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/AutolayoutPG/VisualFormatLanguage.html#//apple_ref/doc/uid/TP40010853-CH27-SW1)
