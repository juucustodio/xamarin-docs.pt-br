---
title: "Introdução aos renderizadores personalizados"
description: "Processadores personalizados fornecem uma abordagem avançada para personalizar a aparência e comportamento de controles xamarin. Forms. Eles podem ser usados para estilo pequenas alterações ou layout de específico de plataforma sofisticada e personalização de comportamento. Este artigo fornece uma introdução aos renderizadores personalizados e descreve o processo de criação de um renderizador personalizado."
ms.topic: article
ms.prod: xamarin
ms.assetid: 264314BE-1C5C-4727-A14E-F6F98151CDBD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/19/2016
ms.openlocfilehash: a9908429994f4575a9e41936d500bfd8906a843b
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="introduction-to-custom-renderers"></a>Introdução aos renderizadores personalizados

_Processadores personalizados fornecem uma abordagem avançada para personalizar a aparência e comportamento de controles xamarin. Forms. Eles podem ser usados para estilo pequenas alterações ou layout de específico de plataforma sofisticada e personalização de comportamento. Este artigo fornece uma introdução aos renderizadores personalizados e descreve o processo de criação de um renderizador personalizado._

Xamarin. Forms [páginas, Layouts e controles](~/xamarin-forms/user-interface/controls/index.md) apresentar uma API para descrever as interfaces de usuário móvel de plataforma cruzada. Cada página, o layout e o controle é renderizado diferente em cada plataforma, usando um `Renderer` classe que por sua vez, cria um controle nativo (correspondente à representação xamarin. Forms), organiza na tela e adiciona o comportamento especificado do código compartilhado.

Os desenvolvedores podem implementar suas próprias classes `Renderer` personalizadas para personalizar a aparência e/ou o comportamento de um controle. Processadores personalizados para um determinado tipo podem ser adicionados ao projeto de um aplicativo para personalizar o controle em um local enquanto permite que o comportamento padrão em outras plataformas; ou diferentes em renderizadores personalizados podem ser adicionados para cada projeto de aplicativo para criar uma aparência diferente no iOS, Android e Windows Phone. No entanto, implementando uma classe de renderizador personalizado para executar uma personalização de controle simples geralmente é uma resposta ativado. Efeitos simplificam esse processo e geralmente são usados para o estilo de pequenas alterações. Para obter mais informações, veja [Efeitos](~/xamarin-forms/app-fundamentals/effects/index.md).

## <a name="examining-why-custom-renderers-are-necessary"></a>Examinando renderizadores de personalizado por que são necessárias

Alterando a aparência de um controle xamarin. Forms, sem usar um renderizador personalizado, é um processo de duas etapas que envolve a criação de um controle personalizado por meio de subclasses e, em seguida, consumindo o controle personalizado no lugar do controle original. O exemplo de código a seguir mostra um exemplo de subclassificação o `Entry` controle:

```csharp
public class MyEntry : Entry
{
  public MyEntry ()
  {
    BackgroundColor = Color.Gray;
  }
}
```

O `MyEntry` controle é um `Entry` controlam onde o `BackgroundColor` é definido como cinza e pode ser referenciado em Xaml declarando um namespace para seu local e usando o prefixo de namespace no elemento de controle. O seguinte exemplo de código mostra como o `MyEntry` controle personalizado pode ser consumido por um `ContentPage`:

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

O `local` o prefixo do namespace pode ser qualquer coisa. No entanto, o `namespace` e `assembly` valores devem coincidir com os detalhes do controle personalizado. Depois que o namespace for declarado, o prefixo é usado para referenciar o controle personalizado.

> [!NOTE]
> **Observação**: definindo a `xmlns` é muito mais simples no PCLs que projetos compartilhados. Um PCL é compilado em um assembly, portanto, é fácil determinar o que o `assembly=CustomRenderer` o valor deve ser. Ao usar projetos compartilhados, todos os recursos compartilhados (incluindo o XAML) são compilados em cada um dos projetos de referência, o que significa que, se o iOS, Android e Windows Phone projetos têm seus próprios *nomes de assembly* é impossível para gravar o `xmlns` declaração porque o valor precisa ser diferente para cada aplicativo. Controles personalizados em XAML para projetos compartilhados exige que todos os projetos de aplicativo a ser configurado com o mesmo nome de assembly.

O `MyEntry` controle personalizado é renderizado em cada plataforma, com um plano de fundo cinza, conforme mostrado nas capturas de tela seguir:

![](introduction-images/screenshots.png "Controle personalizado MyEntry em cada plataforma")

Alterar a cor do plano de fundo do controle em cada plataforma foi realizada apenas por meio de subclassificação do controle. No entanto, essa técnica é limitada em que ele pode obter como não é possível tirar proveito dos aprimoramentos específicos de plataforma e personalizações. Quando forem necessários, renderizadores personalizados devem ser implementados.

## <a name="creating-a-custom-renderer-class"></a>Criando uma classe de renderizador personalizado

O processo para criar uma classe de renderizador personalizado é da seguinte maneira:

1. Crie uma subclasse da classe renderizador que renderiza o controle nativo.
1. Substitua o método que renderiza o controle nativo e lógica para personalizar o controle de gravação. Geralmente, o `OnElementChanged` método é usado para renderizar o controle nativo, que é chamado quando o controle xamarin. Forms correspondente é criado.
1. Adicionar um `ExportRenderer` de atributo para a classe de renderizador personalizado para especificar que será usada para renderizar o controle xamarin. Forms. Este atributo é usado para registrar o renderizador personalizado com xamarin. Forms.

> [!NOTE]
> **Observação**: para a maioria dos elementos de xamarin. Forms, é opcional fornecer um renderizador personalizado em cada projeto da plataforma. Se um renderizador personalizado não estiver registrado, será usado o renderizador padrão para a classe base do controle. No entanto, renderizadores personalizados são necessários em cada projeto de plataforma ao renderizar um [exibição](https://developer.xamarin.com/api/type/Xamarin.Forms.View/) ou [ViewCell](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/) elemento.

Os tópicos nesta série fornecerão demonstrações e explicações sobre esse processo para diferentes elementos de xamarin. Forms.

## <a name="troubleshooting"></a>Solução de problemas

Se um controle personalizado está contido em um projeto PCL foi adicionado à solução (ou seja, não o PCL criado pelo Visual Studio para modelo de projeto de aplicativo do Mac/Visual Studio xamarin. Forms), uma exceção pode ocorrer no iOS, ao tentar acessar o controle personalizado. Se ocorrer esse problema pode ser resolvido com a criação de uma referência para o controle personalizado a partir de `AppDelegate` classe:

```csharp
var temp = new ClassInPCL(); // in AppDelegate, but temp not used anywhere
```

Isso força o compilador a reconhecer o `ClassInPCL` tipo por resolvê-lo. Como alternativa, o `Preserve` atributo pode ser adicionado para o `AppDelegate` classe para alcançar o mesmo resultado:

```csharp
[assembly: Preserve (typeof (ClassInPCL))]
```

Isso cria uma referência para o `ClassInPCL` tipo, indicando que é necessário em tempo de execução. Para obter mais informações, consulte [código preservando](~/ios/deploy-test/linker.md).

## <a name="summary"></a>Resumo

Este artigo fornece uma introdução aos renderizadores personalizados e descreveu o processo para criar um renderizador personalizado. Processadores personalizados fornecem uma abordagem avançada para personalizar a aparência e comportamento de controles xamarin. Forms. Eles podem ser usados para estilo pequenas alterações ou layout de específico de plataforma sofisticada e personalização de comportamento.


## <a name="related-links"></a>Links relacionados

- [Efeitos](~/xamarin-forms/app-fundamentals/effects/index.md)
