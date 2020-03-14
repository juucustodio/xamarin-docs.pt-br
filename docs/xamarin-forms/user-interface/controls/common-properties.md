---
title: Propriedades, métodos e eventos de controle comum do Xamarin. Forms
description: Este artigo descreve Propriedades, métodos e eventos comuns definidos na classe Visualelement, que são comumente usados na derivação de classes.
ms.prod: xamarin
ms.assetId: 85A0CCF5-C1D8-40BB-927F-A4D944E5534D
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 08/21/2019
ms.openlocfilehash: 6d10e665c6461655440ddfb2c524cb56a14337f6
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79304208"
---
# <a name="xamarinforms-common-control-properties-methods-and-events"></a>Propriedades, métodos e eventos de controle comum do Xamarin. Forms

A classe `VisualElement` Xamarin. Forms é a classe base para a maioria dos controles usados em um aplicativo Xamarin. Forms. A classe `VisualElement` define muitas [Propriedades](#properties), [métodos](#methods)e [eventos](#events) que são usados na derivação de classes.

## <a name="properties"></a>{1&gt;Propriedades&lt;1}

As propriedades a seguir estão disponíveis em instâncias de `VisualElement`. Para obter uma lista completa, consulte as [Propriedades da API do visualelement](xref:Xamarin.Forms.VisualElement#properties).

### [`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX)

A propriedade `AnchorX` é um valor de `double` que define o ponto central no eixo X para transformações, como escala e rotação. O valor padrão é 0,5.

### [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY)

A propriedade `AnchorY` é um valor de `double` que define o ponto central no eixo X para transformações, como escala e rotação. O valor padrão é 0,5.

### [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor)

A propriedade `BackgroundColor` é uma `Color` que determina a cor do plano de fundo do controle. Se a definição for desdefinida, o plano de fundo será o padrão `Color` objeto, que é renderizado como transparente.

### [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors)

A propriedade `Behaviors` é uma `List` de objetos `Behavior`. Os comportamentos permitem que você anexe funcionalidade reutilizável a elementos adicionando-os à lista de `Behaviors`. Para obter mais informações sobre a classe `Behavior`, consulte [comportamentos do Xamarin. Forms](~/xamarin-forms/app-fundamentals/behaviors/index.md).

### [`Bounds`](xref:Xamarin.Forms.VisualElement.Bounds)

A propriedade `Bounds` é um objeto de `Rectangle` somente leitura que representa o espaço ocupado pelo controle. O valor da propriedade `Bounds` é atribuído durante o ciclo de layout. O `Rectangle` `struct` contém propriedades e métodos úteis para testar a interseção e o confinamento de retângulos. Para obter mais informações, consulte a [API do retângulo do Xamarin. Forms](xref:Xamarin.Forms.Rectangle).

### [`Effects`](xref:Xamarin.Forms.Element.Effects)

A propriedade `Effects` é uma `List` de objetos `Effect`, herdada da classe `Element`(xref: Xamarin. Forms. Element). Os efeitos permitem que os controles nativos sejam personalizados e normalmente são usados para alterações de estilo pequeno. Para obter mais informações sobre a classe `Effect`, consulte [efeitos do Xamarin. Forms](~/xamarin-forms/app-fundamentals/effects/index.md).

### [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection)

A propriedade `FlowDirection` é um valor de enumeração `FlowDirection`. A direção do fluxo pode ser definida como `MatchParent`, `LeftToRight`ou `RightToLeft` e determina a ordem e a direção do layout. A propriedade `FlowDirection` normalmente é usada para dar suporte a idiomas que são lidos da direita para a esquerda.

### [`Height`](xref:Xamarin.Forms.VisualElement.Height)

A propriedade `Height` é um valor de `double` somente leitura que descreve a altura renderizada do controle. A propriedade `Height` é calculada durante o ciclo de layout e não pode ser definida diretamente. A altura de um controle pode ser solicitada usando a [Propriedade HeightRequest](#heightrequest).

### [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest)

A propriedade `HeightRequest` é um valor de `double` que determina a altura desejada do controle. A altura absoluta do controle pode não corresponder ao valor solicitado. Para obter mais informações, consulte [Propriedades da solicitação](#request-properties).

### [`InputTransparent`](xref:Xamarin.Forms.VisualElement.InputTransparent)

A propriedade `InputTransparent` é uma `bool` que determina se o controle recebe entrada do usuário. O valor padrão é `false`, garantindo que o elemento receba a entrada. Essa propriedade é transferida para elementos filho quando ele é definido. Definir a propriedade `InputTransparent` como `true` em uma classe de layout fará com que todos os elementos dentro do layout não recebam entrada.

### [`IsEnabled`](xref:Xamarin.Forms.VisualElement.IsEnabled)

A propriedade `IsEnabled` é um valor de `bool` que determina se o controle reage à entrada do usuário. O valor padrão é `true`. Definir essa propriedade como false impedirá que o controle aceite a entrada do usuário.

### [`IsFocused`](xref:Xamarin.Forms.VisualElement.IsFocused)

A propriedade `IsFocused` é um valor `bool` que descreve se o controle é atualmente o objeto focalizado. Chamar o método [`Focus`](#focus) no controle resultará na definição do valor de `IsFocused` como true. Chamar o método [`Unfocus`](#unfocus) definirá essa propriedade como false.

### [`IsTabStop`](xref:Xamarin.Forms.VisualElement.IsTabStop)

A propriedade `IsTabStop` é um valor de `bool` que define se o controle recebe o foco quando o usuário está avançando pelos controles com a tecla Tab. Se essa propriedade for false, a propriedade `TabIndex` não terá nenhum efeito.

### [`IsVisible`](xref:Xamarin.Forms.VisualElement.IsVisible)

A propriedade `IsVisible` é um valor de `bool` que determina se o controle é renderizado. Os controles com a propriedade `IsVisible` definida como false não serão exibidos, não serão considerados cálculos de espaço durante o ciclo de layout e não poderão aceitar a entrada do usuário.

### [`MinimumHeightRequest`](xref:Xamarin.Forms.VisualElement.MinimumHeightRequest)

A propriedade `MinimumHeightRequest` é um valor de `double` que determina como o estouro é manipulado quando dois elementos estão competindo por um espaço limitado. Definir a propriedade `MinimumHeightRequest` permite que o processo de layout dimensione o elemento para baixo até a dimensão mínima solicitada. Se nenhum `MinimumHeightRequest` for especificado, o valor padrão será-1 e o processo de layout considerará o `HeightRequest` como o valor mínimo. Isso significa que elementos sem valor de `MinimumHeightRequest` não terão altura escalonável.

Para obter mais informações, consulte [mínimo de propriedades de solicitação](#minimum-request-properties).

### [`MinimumWidthRequest`](xref:Xamarin.Forms.VisualElement.MinimumWidthRequest)

A propriedade `MinimumWidthRequest` é um valor de `double` que determina como o estouro é manipulado quando dois elementos estão competindo por um espaço limitado. Definir a propriedade `MinimumWidthRequest` permite que o processo de layout dimensione o elemento para baixo até a dimensão mínima solicitada. Se nenhum `MinimumWidthRequest` for especificado, o valor padrão será-1 e o processo de layout considerará o `WidthRequest` como o valor mínimo. Isso significa que elementos sem valor de `MinimumWidthRequest` não terão largura escalonável.

Para obter mais informações, consulte [mínimo de propriedades de solicitação](#minimum-request-properties).

### [`Opacity`](xref:Xamarin.Forms.VisualElement.Opacity)

A propriedade `Opacity` é um valor de `double` de zero a um que determina a opacidade do controle durante a renderização. O valor padrão dessa propriedade é 1,0. Os valores fora do intervalo de 0 a 1 serão clamped. A propriedade `Opacity` só será aplicada se a propriedade [`IsVisible`](#isvisible) for `true`. A opacidade é aplicada iterativamente. Portanto, se um controle pai tiver 0,5 de opacidade e seu filho tiver uma opacidade de 0,5, o filho será renderizado com um valor de opacidade de 0,25 efetivo. A definição da propriedade `Opacity` de um controle de entrada como 0 tem um comportamento indefinido.

### [`Parent`](xref:Xamarin.Forms.Element.Parent)

A propriedade `Parent` é herdada da classe `Element`. Essa propriedade é um objeto `Element` que é o pai do controle. A propriedade `Parent` normalmente é definida automaticamente em um elemento quando é adicionada como um filho de outro elemento.

### [`Resources`](xref:Xamarin.Forms.VisualElement.Resources)

A propriedade `Resources` é uma instância de `ResourceDictionary` que é populada com pares de chave/valor que normalmente são populados em tempo de execução do XAML. Esse dicionário permite que os desenvolvedores de aplicativos reutilizem objetos definidos em XAML no tempo de compilação e no tempo de execução. As chaves no dicionário são populadas a partir do atributo `x:Key` da marca XAML. O objeto criado a partir de XAML é inserido no `ResourceDictionary` para a chave especificada. Depois de inicializado.

Para obter mais informações, consulte [dicionários de recursos](~/xamarin-forms/xaml/resource-dictionaries.md).

### [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation)

A propriedade `Rotation` é um valor de `double` entre zero e 360 que define a rotação sobre o eixo Z em graus. O valor padrão dessa propriedade é 0. A rotação é aplicada em relação aos valores de [`AnchorX`](#anchorx) e [`AnchorY`](#anchory) .

### [`RotationX`](xref:Xamarin.Forms.VisualElement.RotationX)

A propriedade `RotationX` é um valor de `double` entre zero e 360 que define a rotação sobre o eixo X em graus. O valor padrão dessa propriedade é 0. A rotação é aplicada em relação aos valores de [`AnchorX`](#anchorx) e [`AnchorY`](#anchory) .

### [`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY)

A propriedade `RotationY` é um valor de `double` entre zero e 360 que define a rotação sobre o eixo Y em graus. O valor padrão dessa propriedade é 0. A rotação é aplicada em relação aos valores de [`AnchorX`](#anchorx) e [`AnchorY`](#anchory) .

### [`Scale`](xref:Xamarin.Forms.VisualElement.Scale)

A propriedade `Scale` é um valor de `double` que define a escala do controle. O valor padrão dessa propriedade é 1,0. A escala é aplicada em relação aos valores de [`AnchorX`](#anchorx) e [`AnchorY`](#anchory) .

### [`ScaleX`](xref:Xamarin.Forms.VisualElement.ScaleX)

A propriedade `ScaleX` é um valor de `double` que define a escala do controle ao longo do eixo X. O valor padrão dessa propriedade é 1,0. A propriedade `ScaleX` é aplicada relativa ao valor de [`AnchorX`](#anchorx) .

### [`ScaleY`](xref:Xamarin.Forms.VisualElement.ScaleY)

A propriedade `ScaleY` é um valor de `double` que define a escala do controle ao longo do eixo Y. O valor padrão dessa propriedade é 1,0. A propriedade `ScaleY` é aplicada relativa ao valor de [`AnchorY`](#anchory) .

### [`Style`](xref:Xamarin.Forms.NavigableElement.Style)

A propriedade `Style` é herdada da classe `NavigableElement`. Essa propriedade é uma instância da classe `Style`. A classe `Style` contém disparadores, setters e comportamentos que definem a aparência e o comportamento de elementos visuais. Para obter mais informações, consulte [estilos XAML do Xamarin. Forms](~/xamarin-forms/user-interface/styles/xaml/index.md).

### [`StyleClass`](xref:Xamarin.Forms.NavigableElement.StyleClass)

A propriedade `StyleClass` é uma lista de objetos `string` que representam os nomes das classes `Style`. Essa propriedade é herdada da classe `NavigableElement`. A propriedade `StyleClass` permite que vários atributos de estilo sejam aplicados a uma instância de `VisualElement`. Para obter mais informações, consulte [classes de estilo Xamarin. Forms](~/xamarin-forms/user-interface/styles/xaml/style-class.md).

### [`TabIndex`](xref:Xamarin.Forms.VisualElement.TabIndex)

A propriedade `TabIndex` é um valor de `int` que define a ordem de controle ao avançar pelos controles com a tecla Tab. A propriedade `TabIndex` é a implementação da propriedade definida na interface `ITabStopElement`, que a classe `VisualElement` implementa.

### [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX)

A propriedade `TranslationX` é um valor de `double` que define a conversão Delta a ser aplicada no eixo X. A conversão é aplicada após o layout e é normalmente usada para aplicar animações. Convertendo um elemento fora dos limites de seu contêiner pai meus impedem que as entradas funcionem.

Para obter mais informações, consulte [animação no Xamarin. Forms](~/xamarin-forms/user-interface/animation/index.md).

### [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY)

A propriedade `TranslationY` é um valor de `double` que define a conversão Delta a ser aplicada no eixo Y. A conversão é aplicada após o layout e é normalmente usada para aplicar animações. Convertendo um elemento fora dos limites de seu contêiner pai meus impedem que as entradas funcionem.

Para obter mais informações, consulte [animação no Xamarin. Forms](~/xamarin-forms/user-interface/animation/index.md).

### [`Triggers`](xref:Xamarin.Forms.VisualElement.Triggers)

A propriedade `Triggers` é uma `List` somente leitura de objetos `TriggerBase`. Os gatilhos permitem que os desenvolvedores de aplicativos expressem ações em XAML que alteram a aparência visual de controles em resposta a alterações de evento ou propriedade. Para obter mais informações, consulte [gatilhos do Xamarin. Forms](~/xamarin-forms/app-fundamentals/triggers.md).

### [`Visual`](xref:Xamarin.Forms.VisualElement.Visual)

A propriedade `Visual` é uma instância de `IVisual` que permite que os renderizadores sejam criados e aplicados seletivamente às instâncias de `VisualElement`. A propriedade `Visual` é definida para corresponder ao seu pai, de modo que definir um processador em um componente também se aplicará a qualquer filho desse componente. Se nenhum renderizador personalizado for definido em um controle ou seus ancestrais, o renderizador padrão do Xamarin. Forms será usado. Para obter mais informações, consulte [Visual do Xamarin. Forms](~/xamarin-forms/user-interface/visual/index.md).

### [`Width`](xref:Xamarin.Forms.VisualElement.Width)

A propriedade `Width` é um valor de `double` somente leitura que descreve a largura renderizada do controle. A propriedade `Width` é calculada durante o ciclo de layout e não pode ser definida diretamente. A largura de um controle pode ser solicitada usando a [Propriedade WidthRequest](#widthrequest).

### [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest)

A propriedade `WidthRequest` é um valor de `double` que determina a largura desejada do controle. A largura absoluta do controle pode não corresponder ao valor solicitado. Para obter mais informações, consulte [Propriedades da solicitação](#request-properties).

### [`X`](xref:Xamarin.Forms.VisualElement.X)

A propriedade `X` é um valor de `double` somente leitura que descreve a posição X atual do controle.

### [`Y`](xref:Xamarin.Forms.VisualElement.Y)

A propriedade `Y` é um valor de `double` somente leitura que descreve a posição Y atual do controle.

## <a name="methods"></a>{1&gt;Métodos&lt;1}

Os métodos a seguir estão disponíveis na classe `VisualElement`. Para obter uma lista completa, consulte [métodos de API do visualelement](xref:Xamarin.Forms.VisualElement#methods).

### [`FindByName`](xref:Xamarin.Forms.Element.FindByName*)

O método `FindByName` é herdado da classe `Element` e tem a seguinte assinatura:

```csharp
public object FindByName (string name)
```

Esse método pesquisa todos os elementos filho para o argumento de `name` fornecido e retorna o elemento que tem o nome especificado. Se nenhuma correspondência for encontrada, `null` será retornado.

### [`Focus`](xref:Xamarin.Forms.VisualElement.Focus)

O método `Focus` tenta definir o foco no elemento. Esse método tem a seguinte assinatura:

```csharp
public bool Focus ()
```

O método `Focus` retornará `true` se o foco do teclado tiver sido definido com êxito e `false` se a chamada do método não resultar em uma alteração de foco. O elemento deve ser capaz de receber o foco para que esse método funcione. Chamar o método `Focus` em elementos que estão fora da tela ou não é realizado tem comportamento indefinido.

### [`Unfocus`](xref:Xamarin.Forms.VisualElement.Unfocus)

O método `Unfocus` tenta remover o foco no elemento. Esse método tem a seguinte assinatura:

```csharp
public void Unfocus ()
```

O elemento já deve ter foco para que esse método funcione.

## <a name="events"></a>Eventos

Os eventos a seguir estão disponíveis na classe `VisualElement`. Para obter uma lista completa, consulte [eventos de visualelement do Xamarin. Forms](xref:Xamarin.Forms.VisualElement#events).

### [`Focused`](xref:Xamarin.Forms.VisualElement.Focused)

O evento `Focused` é gerado sempre que a instância de `VisualElement` recebe o foco. Esse evento não é bolha por meio da pilha Xamarin. Forms, ele é recebido diretamente do controle nativo. Esse evento é emitido pelo setter da propriedade [`IsFocused`](#isfocused) .

### [`SizeChanged`](xref:Xamarin.Forms.VisualElement.SizeChanged)

O evento `SizeChanged` é gerado sempre que a instância `VisualElement` `Height` ou `Width` as propriedades são alteradas. Se os desenvolvedores quiserem responder diretamente à alteração de tamanho, em vez de responder ao evento de pós-alteração, eles deverão implementar o método virtual [`OnSizeAllocated`](xref:Xamarin.Forms.VisualElement.OnSizeAllocated*) em vez disso.

### [`Unfocused`](xref:Xamarin.Forms.VisualElement.Unfocused)

O evento `Unfocused` é gerado sempre que a instância `VisualElement` perde o foco. Esse evento não é bolha por meio da pilha Xamarin. Forms, ele é recebido diretamente do controle nativo. Esse evento é emitido pelo setter da propriedade [`IsFocused`](#isfocused) .

## <a name="units-of-measurement"></a>Unidades de medida

As plataformas Android, iOS e UWP têm unidades de medida diferentes que podem variar entre os dispositivos. O Xamarin. Forms usa uma unidade de medida independente de plataforma que normaliza as unidades entre dispositivos e plataformas. Há 160 unidades por polegada ou 64 unidades por centímetro, no Xamarin. Forms.

## <a name="request-properties"></a>Propriedades de solicitação

Propriedades cujos nomes contêm "solicitação" definem um valor desejado, que pode não corresponder ao valor real renderizado. Por exemplo, `HeightRequest` pode ser definido como 150, mas se o layout só permitir espaço para 100 unidades, o `Height` renderizado do controle será apenas 100. O tamanho renderizado é afetado pelo espaço disponível e pelos componentes contidos.

## <a name="minimum-request-properties"></a>Propriedades de solicitação mínimas

As propriedades de solicitação mínimas incluem `MinimumHeightRequest` e `MinimumWidthRequest`e destinam-se a permitir um controle mais preciso sobre como os elementos manipulam o estouro em relação uns com os outros. No entanto, o comportamento de layout relacionado a essas propriedades tem algumas considerações importantes.

### <a name="unspecified-minimum-property-values"></a>Valores mínimos de propriedade não especificados

Se um valor mínimo não for definido, a propriedade Minimum definirá como padrão-1. O processo de layout ignora esse valor e considera o valor absoluto como o mínimo. A consequência prática desse comportamento é que um elemento sem nenhum valor mínimo especificado **não será** reduzido. Um elemento com um valor mínimo especificado **será** reduzido.

O XAML a seguir mostra dois elementos `BoxView` em um `StackLayout`horizontal:

```xaml
<StackLayout Orientation="Horizontal">
    <BoxView HeightRequest="100" BackgroundColor="Purple" WidthRequest="500"></BoxView>
    <BoxView HeightRequest="100" BackgroundColor="Green" WidthRequest="500" MinimumWidthRequest="250"></BoxView>
</StackLayout>
```

A primeira instância de `BoxView` solicita uma largura de 500 e não especifica uma largura mínima. A segunda instância de `BoxView` solicita uma largura de 500 e uma largura mínima de 250. Se o elemento `StackLayout` pai não for grande o suficiente para conter os dois componentes na largura solicitada, a primeira instância `BoxView` será considerada pelo processo de layout para ter uma largura mínima de 500 porque nenhum outro mínimo válido será especificado. A segunda instância de `BoxView` tem permissão para reduzir para 250 e será reduzida para se ajustar até que sua largura atinja 250 unidades.

Se o comportamento desejado for para a primeira instância de `BoxView` reduzir verticalmente sem largura mínima, o `MinimumWidthRequest` deverá ser definido como um valor válido, como 0.

### <a name="minimum-and-absolute-property-values"></a>Valores de propriedade mínimo e absoluto

O comportamento é indefinido quando o valor mínimo é maior que o valor absoluto. Por exemplo, se `MinimumWidthRequest` for definido como 100, a propriedade `WidthRequest` nunca deverá exceder 100. Ao especificar um valor mínimo de propriedade, você sempre deve especificar um valor absoluto para garantir que o valor absoluto seja maior que o valor mínimo.

### <a name="minimum-properties-within-a-grid"></a>Propriedades mínimas em uma grade

os layouts de `Grid` têm seu próprio sistema para o dimensionamento relativo de linhas e colunas. O uso de `MinimumWidthRequest` ou `MinimumHeightRequest` dentro de um layout de `Grid` não terá efeito. Para obter mais informações, consulte a [grade Xamarin. Forms](~/xamarin-forms/user-interface/layouts/grid.md).

## <a name="related-links"></a>Links relacionados

* [Documentação da API do visualelement](xref:Xamarin.Forms.VisualElement)
