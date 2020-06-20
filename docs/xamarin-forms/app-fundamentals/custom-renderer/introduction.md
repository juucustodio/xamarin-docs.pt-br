---
title: Introdução a renderizadores personalizados
description: Este artigo fornece uma introdução aos renderizadores personalizados e descreve o processo de criação de um renderizador personalizado.
ms.prod: xamarin
ms.assetid: 264314BE-1C5C-4727-A14E-F6F98151CDBD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/19/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e2bed4d5e8f89efa2997fb085278c4b549870245
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84135315"
---
# <a name="introduction-to-custom-renderers"></a>Introdução a renderizadores personalizados

_Os renderizadores personalizados fornecem uma abordagem poderosa para personalizar a aparência e o comportamento dos Xamarin.Forms controles. Eles podem ser usados para alterações de estilo pequeno ou para personalização sofisticada de comportamento e layout específico da plataforma. Este artigo fornece uma introdução aos renderizadores personalizados e descreve o processo de criação de um renderizador personalizado._

Xamarin.Forms[Páginas, layouts e controles](~/xamarin-forms/user-interface/controls/index.md) apresentam uma API comum para descrever interfaces de usuário móvel de plataforma cruzada. Cada página, layout e controle são renderizados de forma diferente em cada plataforma, usando uma `Renderer` classe que, por sua vez, cria um controle nativo (correspondente à Xamarin.Forms representação), organiza-o na tela e adiciona o comportamento especificado no código compartilhado.

Os desenvolvedores podem implementar suas próprias classes `Renderer` personalizadas para personalizar a aparência e/ou o comportamento de um controle. Renderizadores personalizados de um determinado tipo podem ser adicionados ao projeto de um aplicativo para personalizar o controle em um lugar enquanto permite o comportamento padrão em outras plataformas; ou renderizadores personalizados diferentes podem ser adicionados a cada projeto de aplicativo para criar uma aparência diferente no iOS, no Android e na UWP (Plataforma Universal do Windows). No entanto, implementar uma classe de renderizador personalizado para executar uma personalização de controle simples geralmente gera uma resposta pesada. Os efeitos simplificam esse processo e normalmente são usados para pequenas alterações de estilo. Para obter mais informações, veja [Efeitos](~/xamarin-forms/app-fundamentals/effects/index.md).

## <a name="examining-why-custom-renderers-are-necessary"></a>Examinando por que renderizadores personalizados são necessários

Alterar a aparência de um Xamarin.Forms controle, sem usar um processador personalizado, é um processo de duas etapas que envolve a criação de um controle personalizado por meio de subclasses e o consumo do controle personalizado no lugar do controle original. O código a seguir mostra um exemplo da criação de subclasses do controle `Entry`:

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

![](introduction-images/screenshots.png "MyEntry Custom Control on each Platform")

A alteração da cor da tela de fundo do controle em cada plataforma foi feira usando apenas subclasses do controle. No entanto, há limites para o que é possível realizar com essa técnica, pois não é possível tirar proveito dos aprimoramentos e personalizações específicos da plataforma. Quando são necessários, renderizadores personalizados devem ser implementados.

## <a name="creating-a-custom-renderer-class"></a>Criando uma classe de renderizador personalizado

O processo para criar uma classe de renderizador personalizado é a seguinte:

1. Criar uma subclasse da classe do renderizador que renderiza o controle nativo.
1. Substitua o método que renderiza o controle nativo e escreva a lógica para personalizá-lo. Geralmente, o `OnElementChanged` método é usado para renderizar o controle nativo, que é chamado quando o Xamarin.Forms controle correspondente é criado.
1. Adicione um `ExportRenderer` atributo à classe de processador personalizado para especificar que ele será usado para renderizar o Xamarin.Forms controle. Esse atributo é usado para registrar o renderizador personalizado com Xamarin.Forms .

> [!NOTE]
> Para a maioria dos Xamarin.Forms elementos, é opcional fornecer um renderizador personalizado em cada projeto de plataforma. Se um renderizador personalizado não estiver registrado, será usado o renderizador padrão da classe base do controle. No entanto, são necessários renderizadores personalizados em cada projeto da plataforma durante a renderização de um elemento [View](xref:Xamarin.Forms.View) ou [ViewCell](xref:Xamarin.Forms.ViewCell).

Os tópicos desta série fornecerão demonstrações e explicações desse processo para diferentes Xamarin.Forms elementos.

## <a name="troubleshooting"></a>Solução de problemas

Se um controle personalizado estiver contido em um projeto de biblioteca .NET Standard que foi adicionado à solução (ou seja, não a biblioteca de .NET Standard criada pelo modelo de projeto de aplicativo Visual Studio para Mac/Visual Studio Xamarin.Forms ), poderá ocorrer uma exceção no Ios ao tentar acessar o controle personalizado. Se esse problema ocorrer, ele poderá ser resolvido criando uma referência ao controle personalizado usando a classe `AppDelegate`:

```csharp
var temp = new ClassInPCL(); // in AppDelegate, but temp not used anywhere
```

Isso força o compilador a reconhecer o tipo `ClassInPCL` resolvendo-o. Como alternativa, o atributo `Preserve` pode ser adicionado à classe `AppDelegate` para alcançar o mesmo resultado:

```csharp
[assembly: Preserve (typeof (ClassInPCL))]
```

Isso cria uma referência ao tipo `ClassInPCL`, indicando que ele é necessário no runtime. Para obter mais informações, confira [Preservando o código](~/ios/deploy-test/linker.md).

## <a name="summary"></a>Resumo

Este artigo forneceu uma introdução aos renderizadores personalizados e descreveu o processo de criação de um renderizador personalizado. Os renderizadores personalizados fornecem uma abordagem poderosa para personalizar a aparência e o comportamento dos Xamarin.Forms controles. Eles podem ser usados para pequenas alterações de estilo ou personalização sofisticada de comportamento e de layout específico da plataforma.

## <a name="related-links"></a>Links relacionados

- [Effect](~/xamarin-forms/app-fundamentals/effects/index.md)
