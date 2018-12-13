---
title: Introdução a renderizadores personalizados
description: Este artigo fornece uma introdução aos renderizadores personalizados e descreve o processo de criação de um renderizador personalizado.
ms.prod: xamarin
ms.assetid: 264314BE-1C5C-4727-A14E-F6F98151CDBD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/19/2016
ms.openlocfilehash: 2b2b5726f4ca28ae37f027a700abdd688aa0b1d7
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108425"
---
# <a name="introduction-to-custom-renderers"></a>Introdução a renderizadores personalizados

_Renderizadores personalizados fornecem uma abordagem eficiente para personalizar a aparência e o comportamento de controles do Xamarin.Forms. Eles podem ser usados para pequenas alterações de estilo ou personalização sofisticada de comportamento e de layout específico da plataforma. Este artigo fornece uma introdução aos renderizadores personalizados e descreve o processo de criação de um renderizador personalizado._

[Páginas, Layouts e Controles](~/xamarin-forms/user-interface/controls/index.md) do Xamarin.Forms apresenta uma API comum para descrever interfaces do usuário móveis multiplataforma. Cada página, layout e controle é renderizado de maneira diferente em cada plataforma usando uma classe `Renderer`, que por sua vez cria um controle nativo (correspondente à representação no Xamarin.Forms), organiza sua disposição na tela e adiciona o comportamento especificado no código compartilhado.

Os desenvolvedores podem implementar suas próprias classes `Renderer` personalizadas para personalizar a aparência e/ou o comportamento de um controle. Renderizadores personalizados de um determinado tipo podem ser adicionados ao projeto de um aplicativo para personalizar o controle em um lugar enquanto permite o comportamento padrão em outras plataformas; ou renderizadores personalizados diferentes podem ser adicionados a cada projeto de aplicativo para criar uma aparência diferente no iOS, no Android e na UWP (Plataforma Universal do Windows). No entanto, implementar uma classe de renderizador personalizado para executar uma personalização de controle simples geralmente gera uma resposta pesada. Os efeitos simplificam esse processo e normalmente são usados para pequenas alterações de estilo. Para obter mais informações, veja [Efeitos](~/xamarin-forms/app-fundamentals/effects/index.md).

## <a name="examining-why-custom-renderers-are-necessary"></a>Examinando por que renderizadores personalizados são necessários

Alterar a aparência de um controle do Xamarin.Forms sem usar um renderizador personalizado é um processo de duas etapas que envolve criar um controle personalizado por meio de subclasses e, em seguida, consumir o controle personalizado no lugar do controle original. O código a seguir mostra um exemplo da criação de subclasses do controle `Entry`:

```csharp
public class MyEntry : Entry
{
  public MyEntry ()
  {
    BackgroundColor = Color.Gray;
  }
}
```

O controle `MyEntry` é um controle `Entry` em que o `BackgroundColor` é definido como cinza, que pode ser referenciado no XAML declarando um namespace para sua localização e usando o prefixo do namespace no elemento de controle. O seguinte exemplo de código mostra como o controle personalizado `MyEntry` pode ser consumido por um `ContentPage`:

```xaml
<ContentPage
    ...
    xmlns:local="clr-namespace:CustomRenderer;assembly=CustomRenderer"
    ...>
    ...
    <local:MyEntry Text="In Shared Code" />
    ...
</ContentPage>
```

O prefixo do namespace `local` pode ser qualquer coisa. No entanto, os valores de `namespace` e `assembly` devem corresponder aos detalhes do controle personalizado. Quando o namespace é declarado, o prefixo é usado para referenciar o controle personalizado.

> [!NOTE]
> Definir o `xmlns` é muito mais simples em projetos de biblioteca do .NET Standard do que em projetos compartilhados. Uma biblioteca do .NET Standard é compilada em um assembly e, portanto, é fácil determinar qual deveria ser o valor de `assembly=CustomRenderer`. Ao usar Projetos compartilhados, todos os ativos compartilhados (incluindo o XAML) são compilados em cada um dos projetos de referência, o que significa que, se os projetos do iOS, do Android e da UWP tiverem seus próprios *nomes de assembly*, será impossível escrever a declaração `xmlns` porque o valor deverá ser diferente para cada aplicativo. Controles personalizados em XAML para Projetos compartilhados exigirão que cada projeto de aplicativo seja configurado com o mesmo nome de assembly.

O controle personalizado `MyEntry` é renderizado em cada plataforma, com uma tela de fundo cinza, conforme mostrado nas capturas de tela seguir:

![](introduction-images/screenshots.png "Controle personalizado MyEntry em cada plataforma")

A alteração da cor da tela de fundo do controle em cada plataforma foi feira usando apenas subclasses do controle. No entanto, há limites para o que é possível realizar com essa técnica, pois não é possível tirar proveito dos aprimoramentos e personalizações específicos da plataforma. Quando são necessários, renderizadores personalizados devem ser implementados.

## <a name="creating-a-custom-renderer-class"></a>Criando uma classe de renderizador personalizado

O processo para criar uma classe de renderizador personalizado é a seguinte:

1. Criar uma subclasse da classe do renderizador que renderiza o controle nativo.
1. Substitua o método que renderiza o controle nativo e escreva a lógica para personalizá-lo. Frequentemente, o método `OnElementChanged` é usado para renderizar o controle nativo, que é chamado quando um controle do Xamarin.Forms correspondente é criado.
1. Adicionar um atributo `ExportRenderer` à classe do renderizador personalizado para especificar que ele será usado para renderizar o controle do Xamarin.Forms. Esse atributo é usado para registrar o renderizador personalizado no Xamarin.Forms.

> [!NOTE]
> Para a maioria dos elementos do Xamarin.Forms, o fornecimento de um renderizador personalizado em cada projeto de plataforma é opcional. Se um renderizador personalizado não estiver registrado, será usado o renderizador padrão da classe base do controle. No entanto, são necessários renderizadores personalizados em cada projeto da plataforma durante a renderização de um elemento [View](xref:Xamarin.Forms.View) ou [ViewCell](xref:Xamarin.Forms.ViewCell).

Os tópicos desta série fornecem demonstrações e explicações sobre esse processo para diferentes elementos do Xamarin.Forms.

## <a name="troubleshooting"></a>Solução de problemas

Se um controle personalizado estiver em um projeto de biblioteca do .NET Standard que foi adicionado à solução (ou seja, não na biblioteca do .NET Standard criada pelo modelo de projeto de aplicativo do Xamarin.Forms para Visual Studio para Mac/Visual Studio), poderá ocorrer uma exceção no iOS ao tentar acessar o controle personalizado. Se esse problema ocorrer, ele poderá ser resolvido criando uma referência ao controle personalizado usando a classe `AppDelegate`:

```csharp
var temp = new ClassInPCL(); // in AppDelegate, but temp not used anywhere
```

Isso força o compilador a reconhecer o tipo `ClassInPCL` resolvendo-o. Como alternativa, o atributo `Preserve` pode ser adicionado à classe `AppDelegate` para alcançar o mesmo resultado:

```csharp
[assembly: Preserve (typeof (ClassInPCL))]
```

Isso cria uma referência ao tipo `ClassInPCL`, indicando que ele é necessário no tempo de execução. Para obter mais informações, confira [Preservando o código](~/ios/deploy-test/linker.md).

## <a name="summary"></a>Resumo

Este artigo forneceu uma introdução aos renderizadores personalizados e descreveu o processo de criação de um renderizador personalizado. Renderizadores personalizados fornecem uma abordagem eficiente para personalizar a aparência e o comportamento de controles do Xamarin.Forms. Eles podem ser usados para pequenas alterações de estilo ou personalização sofisticada de comportamento e de layout específico da plataforma.


## <a name="related-links"></a>Links relacionados

- [Efeitos](~/xamarin-forms/app-fundamentals/effects/index.md)
