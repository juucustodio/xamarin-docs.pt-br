---
title: Opções de layout no xamarin. IOS
description: Este documento descreve as diferentes maneiras de definir o layout de interfaces do usuário no xamarin. IOS. Ele aborda o dimensionamento automático e o Layout automático.
ms.prod: xamarin
ms.assetid: D8180FEC-F300-42C0-B029-66803E0C1A5F
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: b35149028763691c17fe526673d023cc9b707c28
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50116648"
---
# <a name="layout-options-in-xamarinios"></a>Opções de layout no xamarin. IOS

Há dois mecanismos diferentes para controlar o layout quando um modo de exibição é redimensionado ou girado:

-  **Dimensionando** – dimensionando o Inspetor no designer fornece uma maneira de definir o `AutoresizingMask` propriedades. Isso permitirá que um controle ancorado às bordas de seu contêiner e/ou corrigir seu tamanho. Dimensionamento automático funciona em todas as versões do iOS. Isso é descrito em mais detalhes abaixo
-  **Layout automático** – um recurso, introduzido no iOS 6, o que permite que um controle refinado sobre as relações dos controles da interface do usuário. Isso permitirá que o controle das posições dos elementos em relação a outros elementos na superfície de design. Este tópico é abordado mais detalhadamente os [Layout automático com o Designer do iOS Xamarin](~/ios/user-interface/designer/designer-auto-layout.md) guia.

## <a name="autosizing"></a>Dimensionamento automático

Quando um usuário redimensiona uma janela, como quando o dispositivo é girado e as alterações de orientação, o sistema será redimensionado automaticamente os modos de exibição dentro dessa janela de acordo com suas regras de dimensionamento automático. Essas regras podem ser definidas na C# usando o `AutoresizingMask` propriedade do `UIView` ou nos **painel de propriedades** do iOS Designer, conforme ilustrado abaixo:

 [![](layout-options-images/image41.png "O Visual Studio para Mac Designer")](layout-options-images/image41.png#lightbox)

Quando um controle for selecionado, isso permite que você especifique manualmente a localização e as dimensões do controle, bem como escolhendo **dimensionando** comportamento. Conforme ilustrado na captura de tela abaixo, podemos usar o struts e springs no controle de dimensionamento automático para definir a relação do modo de exibição selecionado ao pais:

 [![](layout-options-images/image42.png "O Visual Studio para Mac Designer")](layout-options-images/image42.png#lightbox)

Ajustando um *spring* fará com que o modo de exibição redimensionar com base na largura ou altura do modo de exibição pai. Ajustando um *mostre* que a exibição mantém uma distância constante entre si e o modo de exibição pai, na borda específica.

Essas configurações também podem ser definidas no código:

```csharp
textfield1.Frame = new RectangleF(15, 277, 79, 27);
textfield1.AutoresizingMask = UIViewAutoresizing.FlexibleRightMargin | UIViewAutoresizing.FlexibleBottomMargin;
```


Para testar as configurações de dimensionamento automático, habilitar diferentes **orientações do dispositivo com suporte** nas opções do projeto:

 [![](layout-options-images/image43a.png "Configurações de dimensionamento automático")](layout-options-images/image43a.png#lightbox)

No code-behind, podemos usar o código a seguir, que faz com que os controles de texto de dois Redimensionar horizontalmente:

```csharp
textview1.AutoresizingMask = UIViewAutoresizing.FlexibleWidth;
textfield1.AutoresizingMask = UIViewAutoresizing.FlexibleWidth;
imageview1.AutoresizingMask = UIViewAutoresizing.FlexibleTopMargin | UIViewAutoresizing.FlexibleLeftMargin;
```


Podemos também pode ajustar os controles usando o Designer. Selecionar os struts, conforme exibido abaixo fará com que a imagem permanecer alinhado à direita sem ser cortada da parte inferior do modo de exibição:

 [![](layout-options-images/autoresize.png "Definição de auto-rotação")](layout-options-images/autoresize.png#lightbox)

Essas capturas de tela mostram como os controles de redimensionar ou reposicionam quando a tela é girada:

 [![](layout-options-images/image44a.png "Definição de auto-rotação")](layout-options-images/image44a.png#lightbox)

Observe que a exibição de texto e o campo de texto tanto esticam para manter o mesmo à esquerda e direita margens, devido ao `FlexibleWidth` configuração. A imagem tem a superior e esquerda margem flexível, que significa que ele preserva a parte inferior e direita as margens – manter a imagem no modo de exibição quando a tela é girada. Layouts complexos geralmente exigem uma combinação dessas configurações em todos os controles visíveis para manter a interface do usuário consistente e impedir que os controles se sobrepondo quando os limites da exibição alteram (devido à rotação ou outro evento de redimensionamento).





## <a name="related-links"></a>Links relacionados

- [Controles (amostra)](https://developer.xamarin.com/samples/Controls/)
