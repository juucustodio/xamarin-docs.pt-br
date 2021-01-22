---
title: Opções de layout no Xamarin. iOS
description: Este documento descreve diferentes maneiras de dispor as interfaces do usuário no Xamarin. iOS. Ele aborda o autodimensionamento e o layout automático.
ms.prod: xamarin
ms.assetid: D8180FEC-F300-42C0-B029-66803E0C1A5F
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: e320e5e3ad7942f371e01baaade753a2bb35f76e
ms.sourcegitcommit: 513feb0e07558766e3de4a898e53d56b27c20559
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98697651"
---
# <a name="layout-options-in-xamarinios"></a>Opções de layout no Xamarin. iOS

Há dois mecanismos diferentes para controlar o layout quando uma exibição é redimensionada ou girada:

- **Dimensionamento** automático – o Inspetor de autodimensionamento no designer fornece uma maneira de definir as `AutoresizingMask` Propriedades. Isso permitirá que um controle seja ancorado às bordas de seu contêiner e/ou corrija seu tamanho. O dimensionamento automático funciona em todas as versões do iOS. Isso é descrito mais detalhadamente abaixo
- **Layout automático** – um recurso, introduzido no Ios 6, que permite um controle refinado sobre as relações dos controles da interface do usuário. Ele permitirá o controle das posições de elementos em relação a outros elementos na superfície de design. Este tópico é abordado em mais detalhes no  [layout automático com o guia de designer do Xamarin Ios](~/ios/user-interface/designer/designer-auto-layout.md) .

## <a name="autosizing"></a>Dimensionamento automático

Quando um usuário redimensiona uma janela, como quando o dispositivo é girado e a orientação é alterada, o sistema redimensiona automaticamente as exibições dentro dessa janela de acordo com suas regras de dimensionamento automático. Essas regras podem ser definidas em C# usando a `AutoresizingMask` propriedade de `UIView` ou no **painel de propriedades** do designer do IOS, conforme ilustrado abaixo:

 [![Captura de tela mostra a Painel de Propriedades do designer I S.](layout-options-images/image41.png)](layout-options-images/image41.png#lightbox)

Quando um controle é selecionado, isso permite que você especifique manualmente o local e as dimensões do controle, bem como a escolha do comportamento de **dimensionamento** automático. Conforme ilustrado na captura de tela abaixo, podemos usar as molas e Struts no controle de dimensionamento automático para definir a relação da exibição selecionada para o pai:

 [![Captura de tela mostra o controle de dimensionamento automático na Painel de Propriedades do designer I S.](layout-options-images/image42.png)](layout-options-images/image42.png#lightbox)

O ajuste de uma *mola* fará com que a exibição seja redimensionada com base na largura ou na altura de sua exibição pai. O ajuste de um *Strut* fará com que a exibição mantenha uma distância constante entre si mesma e sua exibição pai, nessa borda específica.

Essas configurações também podem ser definidas no código:

```csharp
textfield1.Frame = new RectangleF(15, 277, 79, 27);
textfield1.AutoresizingMask = UIViewAutoresizing.FlexibleRightMargin | UIViewAutoresizing.FlexibleBottomMargin;
```

Para testar as configurações de dimensionamento automático, habilite diferentes **orientações de dispositivo com suporte** nas opções do projeto:

 [![Configurações de dimensionamento automático](layout-options-images/image43a.png)](layout-options-images/image43a.png#lightbox)

No code-behind, podemos usar o código a seguir, que faz com que os dois controles de texto sejam redimensionados horizontalmente:

```csharp
textview1.AutoresizingMask = UIViewAutoresizing.FlexibleWidth;
textfield1.AutoresizingMask = UIViewAutoresizing.FlexibleWidth;
imageview1.AutoresizingMask = UIViewAutoresizing.FlexibleTopMargin | UIViewAutoresizing.FlexibleLeftMargin;
```

Também podemos ajustar os controles usando o designer. A seleção dos Struts como exibido abaixo fará com que a imagem permaneça alinhada à direita sem ser recortada na parte inferior da exibição:

 [![Captura de tela mostra o controle de dimensionamento automático com esquerda e inferior selecionados. ](layout-options-images/autoresize.png)](layout-options-images/autoresize.png#lightbox)

Essas capturas de tela mostram como os controles redimensionam ou se reposicionam quando a tela é girada:

 [![Captura de tela mostrar um dispositivo móvel exibido em retrato e paisagem com texto e gráfico ajustados.](layout-options-images/image44a.png)](layout-options-images/image44a.png#lightbox)

Observe que a exibição de texto e o campo de texto são esticados para manter as mesmas margens esquerda e direita, devido à `FlexibleWidth` configuração. A imagem tem a margem superior e esquerda flexível, o que significa que preserva as margens inferior e direita – mantendo a imagem na exibição quando a tela é girada. Layouts complexos normalmente exigem uma combinação dessas configurações em todos os controles visíveis para manter a interface do usuário consistente e impedir que os controles se sobreponham quando os limites da exibição são alterados (devido a rotação ou outro evento de redimensionamento).

## <a name="related-links"></a>Links Relacionados

- [Controles (exemplo)](/samples/xamarin/ios-samples/controls)