---
title: "Opções de layout"
ms.topic: article
ms.prod: xamarin
ms.assetid: D8180FEC-F300-42C0-B029-66803E0C1A5F
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 1e3139eb4c94264c91307f6f8a69b183f3bf7fa6
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="layout-options"></a>Opções de layout

Existem dois mecanismos diferentes para controlar o layout quando uma exibição é redimensionada ou girada:

-  **Dimensionando** – dimensionando o Inspetor no designer fornece uma maneira de definir o `AutoresizingMask` propriedades. Isso permitirá que um controle ancorado às bordas de seu contêiner e/ou corrigir seu tamanho. Dimensionando funciona em todas as versões do iOS. Isso é descrito em mais detalhes abaixo
-  **AutoLayout** – um recurso, introduzido no iOS6 que permite um controle refinado sobre as relações entre os controles de interface do usuário. Isso permitirá que o controle das posições de elementos em relação a outros elementos na superfície de design. Este tópico é abordado mais detalhadamente o [Layout automático com o Designer do Xamarin iOS](~/ios/user-interface/designer/designer-auto-layout.md) guia.


## <a name="autosizing"></a>Dimensionamento automático

Quando um usuário redimensionar uma janela, como quando o dispositivo for girado e as alterações de orientação, o sistema será redimensionado automaticamente as exibições dentro dessa janela de acordo com suas regras de dimensionamento automático. Essas regras podem ser definidas em c# usando o `AutoresizingMask` propriedade do `UIView` ou no **propriedades de preenchimento** do iOS Designer, conforme ilustrado abaixo:

 [ ![](layout-options-images/image41.png "O Visual Studio para Mac Designer")](layout-options-images/image41.png)

Quando um controle é selecionado, isso permite que você especifique manualmente a localização e as dimensões do controle, bem como escolher **dimensionando** comportamento. Conforme ilustrado na captura de tela abaixo, podemos usar o Molas e struts no controle de dimensionamento automático para definir a relação do modo de exibição selecionado para seu pai:

 [ ![](layout-options-images/image42.png "O Visual Studio para Mac Designer")](layout-options-images/image42.png)

Ajustando um *spring* fará com que o modo de exibição redimensionar com base na largura ou altura de exibição de seu pai. Ajustando um *mostre* que a exibição a manter uma distância constante entre ele mesmo e sua exibição pai, em que borda específica.

Essas configurações também podem ser definidas no código:

```csharp
textfield1.Frame = new RectangleF(15, 277, 79, 27);
textfield1.AutoresizingMask = UIViewAutoresizing.FlexibleRightMargin | UIViewAutoresizing.FlexibleBottomMargin;
```


Para testar as configurações de dimensionamento automático, habilitar diferentes **orientações com suporte de dispositivo** nas opções do projeto:

 [ ![](layout-options-images/image43a.png "Configurações de dimensionamento automático")](layout-options-images/image43a.png)

No code-behind, podemos usar o código a seguir, que faz com que os controles de texto de duas Redimensionar horizontalmente:

```csharp
textview1.AutoresizingMask = UIViewAutoresizing.FlexibleWidth;
textfield1.AutoresizingMask = UIViewAutoresizing.FlexibleWidth;
imageview1.AutoresizingMask = UIViewAutoresizing.FlexibleTopMargin | UIViewAutoresizing.FlexibleLeftMargin;
```


Podemos também pode ajustar os controles usando o Designer. Selecionar os struts conforme apresentado abaixo fará com que a imagem permaneça alinhado à direita sem ser cortada da parte inferior do modo de exibição:

 [ ![](layout-options-images/autoresize.png "Definição de auto-rotação")](layout-options-images/autoresize.png)

Essas capturas de tela mostram como os controles de redimensionar ou reposicioná-las quando a tela for girada:

 [ ![](layout-options-images/image44a.png "Definição de auto-rotação")](layout-options-images/image44a.png)

Observe que a exibição de texto e o campo de texto tanto alongam para manter o mesmo à esquerda e direita as margens, devido ao `FlexibleWidth` configuração. A imagem tem a superior e esquerda margem flexível, que significa que preserva a parte inferior e direita as margens – mantendo a imagem na exibição quando a tela for girada. Layouts complexos normalmente exigem uma combinação dessas configurações em cada controle visível para manter a interface do usuário consistente e para impedir a sobreposição quando alteram os limites do modo de exibição (devido a rotação ou outro evento de redimensionamento) de controles.





## <a name="related-links"></a>Links relacionados

- [Controles (exemplo)](https://developer.xamarin.com/samples/Controls/)
