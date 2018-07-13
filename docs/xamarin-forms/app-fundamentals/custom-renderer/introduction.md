---
title: Introdução ao renderizadores personalizados
description: Este artigo fornece uma introdução aos renderizadores personalizados e descreve o processo para criar um renderizador personalizado.
ms.prod: xamarin
ms.assetid: 264314BE-1C5C-4727-A14E-F6F98151CDBD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/19/2016
ms.openlocfilehash: 180a196e0b95854815c8a74ef1d2df63407dd04f
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38997998"
---
# <a name="introduction-to-custom-renderers"></a>Introdução ao renderizadores personalizados

_Renderizadores personalizados fornecem uma abordagem eficiente para personalizar a aparência e comportamento de controles do xamarin. Forms. Eles podem ser usados para estilo pequenas alterações ou layout de específico da plataforma sofisticada e personalização de comportamento. Este artigo fornece uma introdução aos renderizadores personalizados e descreve o processo para criar um renderizador personalizado._

Xamarin. Forms [páginas, Layouts e controles](~/xamarin-forms/user-interface/controls/index.md) apresentar uma API comum para descrever interfaces de usuário móvel de plataforma cruzada. Cada página, layout e controle é renderizado diferentemente em cada plataforma, usando um `Renderer` classe que por sua vez cria um controle nativo (correspondente à representação do xamarin. Forms), organiza sua disposição na tela e adiciona o comportamento especificado no código compartilhado.

Os desenvolvedores podem implementar suas próprias classes `Renderer` personalizadas para personalizar a aparência e/ou o comportamento de um controle. Renderizadores personalizados para um determinado tipo podem ser adicionados ao projeto de um aplicativo para personalizar o controle em um só lugar, permitindo que o comportamento padrão em outras plataformas; ou diferentes renderizadores personalizados podem ser adicionados a cada projeto de aplicativo para criar uma aparência diferente no iOS, Android e Universal Windows Platform (UWP). No entanto, implementando uma classe de renderizador personalizado para executar uma personalização de controle simples geralmente é uma resposta detalhada. Efeitos simplificam esse processo e normalmente são usados para estilo pequenas alterações. Para obter mais informações, veja [Efeitos](~/xamarin-forms/app-fundamentals/effects/index.md).

## <a name="examining-why-custom-renderers-are-necessary"></a>Examinando renderizadores de personalizados por que serão necessárias

Alterando a aparência de um controle xamarin. Forms, sem usar um renderizador personalizado, é um processo de duas etapas que envolve a criação de um controle personalizado por meio de subclasses e, em seguida, consumindo o controle personalizado no lugar do controle original. O exemplo de código a seguir mostra um exemplo de criação de subclasses de `Entry` controle:

```csharp
public class MyEntry : Entry
{
  public MyEntry ()
  {
    BackgroundColor = Color.Gray;
  }
}
```

O `MyEntry` controle é um `Entry` controlam onde o `BackgroundColor` é definido como cinza e podem ser referenciadas em Xaml, declarando um namespace para seu local e usando o prefixo de namespace no elemento de controle. O seguinte exemplo de código mostra como o `MyEntry` controle personalizado pode ser consumido por um `ContentPage`:

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

O `local` prefixo de namespace pode ser qualquer coisa. No entanto, o `namespace` e `assembly` valores devem corresponder aos detalhes de controle personalizado. Depois que o namespace é declarado, o prefixo é usado para referenciar o controle personalizado.

> [!NOTE]
> Definindo o `xmlns` é muito mais simples em projetos de biblioteca .NET Standard que projetos compartilhados. Uma biblioteca .NET Standard é compilada em um assembly e, portanto, é fácil determinar o que o `assembly=CustomRenderer` valor deve ser. Ao usar projetos compartilhados, todos os ativos compartilhados (incluindo o XAML) são compilados em cada um dos projetos de referência, o que significa que, se o iOS, Android e UWP projetos têm suas próprias *nomes de assembly* é impossível escrever o `xmlns` declaração porque o valor deve ser diferente para cada aplicativo. Controles personalizados em XAML para projetos compartilhados exigirá a cada projeto de aplicativo a ser configurado com o mesmo nome de assembly.

O `MyEntry` controle personalizado é renderizado em cada plataforma, com um plano de fundo cinza, conforme mostrado nas capturas de tela seguir:

![](introduction-images/screenshots.png "Controle personalizado MyEntry em cada plataforma")

Alterar a cor do plano de fundo do controle em cada plataforma foi cumprida puramente por meio de subclasses de controle. No entanto, essa técnica é limitada no que ele pode obter como não é possível tirar proveito dos aprimoramentos específicos de plataforma e as personalizações. Quando eles são necessários, renderizadores personalizados devem ser implementados.

## <a name="creating-a-custom-renderer-class"></a>Criando uma classe de renderizador personalizado

O processo para a criação de uma classe de renderizador personalizado é da seguinte maneira:

1. Crie uma subclasse da classe de renderizador que renderiza o controle nativo.
1. Substitua o método que renderiza o controle nativo e escrever lógica para personalizar o controle. Muitas vezes, o `OnElementChanged` método é usado para renderizar o controle nativo, que é chamado quando o controle correspondente do xamarin. Forms é criado.
1. Adicionar um `ExportRenderer` atributo à classe de renderizador personalizado para especificar que será usada para renderizar o controle do xamarin. Forms. Este atributo é usado para registrar o renderizador personalizado com o xamarin. Forms.

> [!NOTE]
> Para a maioria dos elementos de xamarin. Forms, é opcional fornecer um renderizador personalizado em cada projeto de plataforma. Se um renderizador personalizado não estiver registrado, o renderizador padrão para a classe base do controle será usado. No entanto, os renderizadores personalizados são necessárias em cada projeto da plataforma durante a renderização de um [modo de exibição](xref:Xamarin.Forms.View) ou [ViewCell](xref:Xamarin.Forms.ViewCell) elemento.

Os tópicos desta série fornecerão demonstrações e explicações sobre esse processo para diferentes elementos de xamarin. Forms.

## <a name="troubleshooting"></a>Solução de problemas

Se um controle personalizado estiver contido em um projeto de biblioteca .NET Standard que foi adicionado à solução (ou seja, não o .NET Standard library criado pelo Visual Studio para modelo de projeto de aplicativo do Mac/Visual Studio xamarin. Forms), uma exceção pode ocorrer no iOS quando tentativa de acessar o controle personalizado. Se esse problema ocorre, ele pode ser resolvido com a criação de uma referência para o controle personalizado a partir de `AppDelegate` classe:

```csharp
var temp = new ClassInPCL(); // in AppDelegate, but temp not used anywhere
```

Isso força o compilador reconheça o `ClassInPCL` tipo por resolvê-lo. Como alternativa, o `Preserve` atributo pode ser adicionado para o `AppDelegate` classe para alcançar o mesmo resultado:

```csharp
[assembly: Preserve (typeof (ClassInPCL))]
```

Isso cria uma referência para o `ClassInPCL` tipo, que indica que ele é necessário no tempo de execução. Para obter mais informações, consulte [preservação de código](~/ios/deploy-test/linker.md).

## <a name="summary"></a>Resumo

Este artigo forneceu uma introdução aos renderizadores personalizados e descreveu o processo para criar um renderizador personalizado. Renderizadores personalizados fornecem uma abordagem eficiente para personalizar a aparência e comportamento de controles do xamarin. Forms. Eles podem ser usados para estilo pequenas alterações ou layout de específico da plataforma sofisticada e personalização de comportamento.


## <a name="related-links"></a>Links relacionados

- [Efeitos](~/xamarin-forms/app-fundamentals/effects/index.md)
