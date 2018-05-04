---
title: RelativeLayout
description: Use RelativeLayout para criar interfaces do usuário que são dimensionados para caber qualquer tela.
ms.prod: xamarin
ms.assetid: 2530BCB8-01B8-4C4F-BF14-CA53659F1B5A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/25/2015
ms.openlocfilehash: 7c12319d9e688504c87368be37edf93e7294df30
ms.sourcegitcommit: 4b0582a0f06598f3ff8ad5b817946459fed3c42a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/03/2018
---
# <a name="relativelayout"></a>RelativeLayout

`RelativeLayout` é usado para a posição e modos de exibição de tamanho relativo propriedades dos modos de exibição de layout ou irmão. Ao contrário de `AbsoluteLayout`, `RelativeLayout` não tem o conceito da âncora de movimentação e não tem recursos para posicionar elementos em relação a margem inferior ou direita do layout. `RelativeLayout` suporte a elementos de posicionamento fora de seus próprios limites.

[![](relative-layout-images/layouts-sml.png "Layouts de xamarin. Forms")](relative-layout-images/layouts.png#lightbox "xamarin. Forms Layouts")

## <a name="purpose"></a>Finalidade

`RelativeLayout` pode ser usado para posicionar os modos de exibição na tela em relação o layout geral ou a outros modos de exibição.

![](relative-layout-images/flag.png "Exploração de RelativeLayout")

## <a name="usage"></a>Uso

### <a name="understanding-constraints"></a>Noções básicas sobre restrições

Posicionar e dimensionar um modo de exibição dentro de um `RelativeLayout` é feito com restrições. Uma expressão de restrição pode incluir as seguintes informações:

- **Tipo** &ndash; se a restrição é em relação ao pai ou para outro modo.
- **Propriedade** &ndash; cuja propriedade a ser usado como base para a restrição.
- **Fator de** &ndash; o fator a ser aplicado ao valor da propriedade.
- **Constante** &ndash; o valor a ser usado como um deslocamento do valor.
- **ElementName** &ndash; o nome da exibição que a restrição é relativo.

Em XAML, as restrições são expressas como `ConstraintExpression`s. Considere o exemplo a seguir:

```xaml
<BoxView Color="Green" WidthRequest="50" HeightRequest="50"
    RelativeLayout.XConstraint =
      "{ConstraintExpression Type=RelativeToParent,
                             Property=Width,
                             Factor=0.5,
                             Constant=-100}"
    RelativeLayout.YConstraint =
      "{ConstraintExpression Type=RelativeToParent,
                             Property=Height,
                             Factor=0.5,
                             Constant=-100}" />
```

No c#, as restrições são expressas de maneira um pouco diferente, usando funções em vez de expressões na exibição. Restrições são especificadas como argumentos para o layout `Add` método:

```csharp
layout.Children.Add(box, Constraint.RelativeToParent((parent) =>
    {
      return (.5 * parent.Width) - 100;
    }),
    Constraint.RelativeToParent((parent) =>
    {
        return (.5 * parent.Height) - 100;
    }),
    Constraint.Constant(50), Constraint.Constant(50));
```

Observe os seguintes aspectos do layout acima:

- O `x` e `y` restrições são especificadas com seus próprios restrições.
- No c#, relativas restrições são definidas como funções. Conceitos como `Factor` não existe, mas pode ser implementado manualmente.
- A caixa `x` coordenada é definida como metade da largura do pai, -100.
- A caixa `y` coordenada é definida como metade da altura do pai, -100.

> [!NOTE]
> Por causa da forma que as restrições são definidas, é possível fazer layouts mais complexos em c# que pode ser especificado com XAML.

Ambos os exemplos anteriores definem restrições como `RelativeToParent` &ndash; ou seja, seus valores são em relação ao elemento pai. Também é possível definir restrições como em relação a outro modo de exibição. Isso permite mais intuitivos (para o desenvolvedor) layouts e pode tornar mais aparente a intenção do seu código de layout.

Considere um layout em que um elemento precisa ser menor que outro de 20 pixels. Se ambos os elementos são definidos com valores constantes, menor poderia ter seu `Y` uma restrição definida como uma constante que é maior que 20 pixels, o `Y` restrição do elemento superior. Essa abordagem se enquadra curta se o elemento superior é posicionado usando uma proporção, para que o tamanho de pixel não é conhecido. Nesse caso, é mais robusto restringir o elemento com base na posição do outro elemento:

```xaml
<RelativeLayout>
    <BoxView Color="Red" x:Name="redBox"
        RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent,
            Property=Height,Factor=.15,Constant=0}"
        RelativeLayout.WidthConstraint="{ConstraintExpression
            Type=RelativeToParent,Property=Width,Factor=1,Constant=0}"
        RelativeLayout.HeightConstraint="{ConstraintExpression
            Type=RelativeToParent,Property=Height,Factor=.8,Constant=0}" />
    <BoxView Color="Blue"
        RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToView,
            ElementName=redBox,Property=Y,Factor=1,Constant=20}"
        RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToView,
            ElementName=redBox,Property=X,Factor=1,Constant=20}"
        RelativeLayout.WidthConstraint="{ConstraintExpression
            Type=RelativeToParent,Property=Width,Factor=.5,Constant=0}"
        RelativeLayout.HeightConstraint="{ConstraintExpression
            Type=RelativeToParent,Property=Height,Factor=.5,Constant=0}" />
</RelativeLayout>
```

Para realizar o mesmo layout em c#:

```csharp
layout.Children.Add (redBox, Constraint.RelativeToParent ((parent) => {
        return parent.X;
    }), Constraint.RelativeToParent ((parent) => {
        return parent.Y * .15;
    }), Constraint.RelativeToParent((parent) => {
        return parent.Width;
    }), Constraint.RelativeToParent((parent) => {
        return parent.Height * .8;
    }));
layout.Children.Add (blueBox, Constraint.RelativeToView (redBox, (Parent, sibling) => {
        return sibling.X + 20;
    }), Constraint.RelativeToView (blueBox, (parent, sibling) => {
        return sibling.Y + 20;
    }), Constraint.RelativeToParent((parent) => {
        return parent.Width * .5;
    }), Constraint.RelativeToParent((parent) => {
        return parent.Height * .5;
    }));
```

Isso produz a seguinte saída, com a posição da caixa azul determinada _relativo_ para a posição da caixa:

![](relative-layout-images/red-blue-box.png "RelativeLayout com BoxViews azul e vermelho")

### <a name="sizing"></a>Dimensionamento

Modos de exibição disposto por `RelativeLayout` tem duas opções para especificar seu tamanho:

- `HeightRequest & WidthRequest`
- `RelativeLayout.WidthConstraint` & `RelativeLayout.HeightConstraint`

`HeightRequest` e `WidthRequest` especificar a pretendido altura e largura da exibição, mas pode ser substituído pelo layouts conforme necessário. `WidthConstraint` e `HeightConstraint` oferece suporte à definição de altura e largura como um valor relativo para o layout ou outra exibição propriedades ou como um valor constante.

## <a name="exploring-a-complex-layout"></a>Explorar um Layout complexo
Cada um dos layouts têm vantagens e desvantagens para a criação de layouts específicos. Em toda esta série de artigos de layout, um aplicativo de exemplo foi criado com o mesmo layout de página implementado usando três layouts diferentes.

Considere o XAML a seguir:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="TheBusinessTumble.RelativeLayoutPage"
BackgroundColor="Maroon"
Title="RelativeLayout">
    <ContentPage.Content>
    <ScrollView>
      <RelativeLayout>
        <BoxView Color="Gray" HeightRequest="100"
            RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=1}" />
        <Button BorderRadius="35" x:Name="imageCircleBack"
            BackgroundColor="Maroon" HeightRequest="70" WidthRequest="70" RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent,Property=Width, Factor=.5, Constant = -35}" RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Factor=0, Property=Y, Constant=70}" />
        <Button BorderRadius="30" BackgroundColor="Red" HeightRequest="60"
            WidthRequest="60" RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToView, ElementName=imageCircleBack, Property=X, Factor=1,Constant=5}" RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Factor=0, Property=Y, Constant=75}" />
        <Label Text="User Name" FontAttributes="Bold" FontSize="26"
            HorizontalTextAlignment="Center" RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Y, Factor=0, Constant=140}" RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=1}" />
        <Entry Text="Bio + Hashtags" TextColor="White" BackgroundColor="Maroon"
            RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Y, Factor=0, Constant=180}" RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=1}" />
        <RelativeLayout BackgroundColor="White" RelativeLayout.YConstraint="
            {ConstraintExpression Type=RelativeToParent, Property=Y, Factor=0, Constant=220}" HeightRequest="60" RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=1}" >
            <BoxView BackgroundColor="Black" WidthRequest="50"
                HeightRequest="50" RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Y, Factor=0, Constant=5}" RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=X, Factor=0, Constant=5}" />
            <BoxView BackgroundColor="Maroon" WidthRequest="50"
                HeightRequest="50" RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Y, Factor=0, Constant=5}" RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=0.5, Constant=}" />
            <Label FontSize="14" TextColor="Black" Text="Accent Color"
                RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Y, Factor=0, Constant=20}" RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=X, Factor=0, Constant=60}" />
            <Label FontSize="14" TextColor="Black" Text="Primary Color"
                RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Y, Factor=0, Constant=20}" RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=0.5, Constant=55}" />
        </RelativeLayout>
        <RelativeLayout Padding="5,0,0,0">
          <Label FontSize="14" Text="Age:" TextColor="White"
            RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0,Constant=305}"
            RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=0, Constant=10}"
            RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width,Factor=.25,Constant=0}"
            RelativeLayout.HeightConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0,Constant=50}" />
          <Entry Text="35" TextColor="White" BackgroundColor="Maroon"
            RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0,Constant=280}"
            RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=0.3, Constant=0}"
            RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width,Factor=0.75,Constant=0}"
            RelativeLayout.HeightConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0,Constant=50}" />
        </RelativeLayout>
        <RelativeLayout  Padding="5,0,0,0">
          <Label FontSize="14" Text="Interests:" TextColor="White"
            RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0,Constant=345}"
            RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=0, Constant=10}"
            RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width,Factor=.25,Constant=0}"
            RelativeLayout.HeightConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0,Constant=50}" />
          <Entry Text="Xamarin.Forms" TextColor="White" BackgroundColor="Maroon"
            RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0,Constant=320}"
            RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=0.3, Constant=0}"
            RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width,Factor=0.75,Constant=0}"
            RelativeLayout.HeightConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0,Constant=50}" />
        </RelativeLayout>
        <RelativeLayout  Padding="5,0,0,0">
          <Label FontSize="14" Text="Ask me about:" TextColor="White"
            LineBreakMode="WordWrap"
            RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0,Constant=395}"
            RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=0, Constant=10}"
            RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width,Factor=.25,Constant=0}"
            RelativeLayout.HeightConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0,Constant=50}" />
          <Entry Text="Xamarin, C#, .NET, Mono" TextColor="White"
            BackgroundColor="Maroon"
            RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0,Constant=370}"
            RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=0.3, Constant=0}"
            RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width,Factor=0.75,Constant=0}"
            RelativeLayout.HeightConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0,Constant=50}" />
        </RelativeLayout>
      </RelativeLayout>
    </ScrollView>
  </ContentPage.Content>
</ContentPage>
```

O código acima resulta no layout a seguir:

![](relative-layout-images/relative.png "RelativeLayout complexo")

Observe que `RelativeLayouts`s estiverem aninhados, como em alguns casos aninhamento layouts pode ser mais fácil do que apresentar todos os elementos do layout do mesmo. Além disso, observe que alguns elementos são `RelativeToView`, pois o que permite layout mais fácil e intuitivo quando as relações entre os modos de exibição de guia de posicionamento.


## <a name="related-links"></a>Links relacionados

- [Layout (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/)
- [Exemplo de BusinessTumble (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/BusinessTumble/)
