---
title: "Introdução aos efeitos"
description: "Efeitos de permitir que os controles nativos em cada plataforma para personalização e são geralmente usados para o estilo de pequenas alterações. Este artigo fornece uma introdução aos efeitos, descreve o limite entre renderizadores personalizados e efeitos e descreve a classe PlatformEffect."
ms.topic: article
ms.prod: xamarin
ms.assetid: 30CB8615-8F39-4762-BDB7-333D2B57D112
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: b66f47ecb8f955f6558df6fff18af92a7a8b97cf
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="introduction-to-effects"></a>Introdução aos efeitos

_Efeitos de permitir que os controles nativos em cada plataforma para personalização e são geralmente usados para o estilo de pequenas alterações. Este artigo fornece uma introdução aos efeitos, descreve o limite entre renderizadores personalizados e efeitos e descreve a classe PlatformEffect._

Xamarin. Forms [páginas, Layouts e controles](~/xamarin-forms/user-interface/controls/index.md) apresenta uma API para descrever as interfaces de usuário móvel de plataforma cruzada. Cada página, o layout e o controle é renderizado diferente em cada plataforma usando um `Renderer` classe que por sua vez, cria um controle nativo (correspondente à representação xamarin. Forms), organiza na tela e adiciona o comportamento especificado em compartilhado código.

Os desenvolvedores podem implementar suas próprias classes `Renderer` personalizadas para personalizar a aparência e/ou o comportamento de um controle. No entanto, implementando uma classe de renderizador personalizado para executar uma personalização de controle simples geralmente é uma resposta ativado. Efeitos simplificam esse processo, permitindo que os controles nativos em cada plataforma para ser mais facilmente personalizado.

Efeitos são criados em projetos de plataforma específica por subclassificação o `PlatformEffect` controle e, em seguida, os efeitos são consumidos por anexá-los a um controle apropriado em um projeto de biblioteca de classe portátil xamarin. Forms (PCL) ou biblioteca compartilhada.

## <a name="why-use-an-effect-over-a-custom-renderer"></a>Por que usar um efeito em um renderizador personalizado?

Efeitos de simplificam a personalização de um controle, são reutilizáveis e podem ser parametrizados para aumentar ainda mais a reutilização.

Tudo o que pode ser obtido com um efeito também pode ser obtido com um renderizador personalizado. No entanto, renderizadores personalizados oferecem mais flexibilidade e personalização de efeitos. As diretrizes a seguir listam as circunstâncias em que você escolha um efeito em um renderizador personalizado:

- Recomenda-se um efeito ao alterar as propriedades de um controle específico de plataforma, obterá o resultado desejado.
- Um renderizador personalizado é necessário quando é necessário para substituir os métodos de um controle específico de plataforma.
- Um renderizador personalizado é necessário quando é necessário para substituir o controle específico de plataforma que implementa um controle xamarin. Forms.

## <a name="subclassing-the-platformeffect-class"></a>Subclasses da classe PlatformEffect

A tabela a seguir lista o namespace para o `PlatformEffect` classe em cada plataforma e os tipos de suas propriedades:

<table>
 <thead>
   <tr>
     <td><strong>Plataforma</strong></td>
     <td><strong>Namespace</strong></td>
     <td><strong>contêiner</strong></td>
     <td><strong>Controle</strong></td>
   </tr>
 </thead>
 <tbody>
   <tr>
     <td>iOS</a></td>
     <td>Xamarin.Forms.Platform.iOS</td>
     <td>UIView</td>
     <td>UIView</td>
   </tr>
   <tr>
     <td>Android</a></td>
     <td>Xamarin.Forms.Platform.Android</td>
     <td>ViewGroup</td>
     <td>Exibir</td>
   </tr>
   <tr>
     <td>Windows Phone 8.1</a></td>
     <td>Xamarin.Forms.Platform.WinRT</td>
     <td>FrameworkElement</td>
     <td>FrameworkElement</td>
   </tr>
   <tr>
     <td>UWP (Plataforma Universal do Windows)</a></td>
     <td>Xamarin.Forms.Platform.UWP</td>
     <td>FrameworkElement</td>
     <td>FrameworkElement</td>
   </tr>
 </tbody>
</table>

Cada plataforma específica `PlatformEffect` classe expõe as propriedades a seguir:

- `Container` – referencia o controle específico da plataforma que está sendo usado para implementar o layout.
- `Control` – referencia o controle específico da plataforma que está sendo usado para implementar o controle xamarin. Forms.
- `Element` – referencia o controle xamarin. Forms que está sendo processado.

Efeitos não tem informações sobre o contêiner, controle ou elemento que eles estão anexados a porque eles podem ser anexados a qualquer elemento de tipo. Portanto, quando um efeito é anexado a um elemento que não oferece suporte para ele deve degradar de forma suave ou lançar uma exceção. No entanto, o `Container`, `Control`, e `Element` propriedades podem ser convertidas para o tipo de implementação. Para obter mais informações sobre esses tipos consulte [Classes de Base de processador e controles nativos](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

Cada plataforma específica `PlatformEffect` classe expõe os métodos a seguir, que devem ser substituídos para implementar um efeito:

- [`OnAttached`](https://developer.xamarin.com/api/member/Xamarin.Forms.Effect.OnAttached()/) – chamado quando um efeito é anexado a um controle xamarin. Forms. Uma versão substituída deste método na classe cada efeito platfom específicos, é o local para executar a personalização do controle, juntamente com tratamento de exceção no caso do efeito não pode ser aplicado ao controle xamarin. Forms especificado.
- [`OnDetached`](https://developer.xamarin.com/api/member/Xamarin.Forms.Effect.OnDetached()/) – chamado quando um efeito é desanexado de um controle xamarin. Forms. Uma versão substituída deste método, cada classe de efeito específico da plataforma, é o local para realizar qualquer limpeza efeito como eliminação registrar um manipulador de eventos.

Além disso, o `PlatformEffect` expõe o [ `OnElementPropertyChanged` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformEffect%3CTContainer,TControl%3E.OnElementPropertyChanged/p/System.ComponentModel.PropertyChangedEventArgs/) método, que também pode ser substituído. Este método é chamado quando uma propriedade do elemento é alterado. Uma versão substituída deste método, cada classe de efeito específico da plataforma, é o local para responder às alterações de propriedade ligável no controle xamarin. Forms. Uma verificação para a propriedade que é alterada sempre deve ser feita, como essa substituição pode ser chamada várias vezes.


## <a name="related-links"></a>Links relacionados

- [Processadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
