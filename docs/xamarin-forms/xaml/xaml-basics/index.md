---
title: Noções básicas de XAML de xamarin. Forms
description: Este guia explica como começar com o XAML de plataforma cruzada para dispositivos móveis. XAML permite que os desenvolvedores definam interfaces do usuário em aplicativos xamarin. Forms usando marcação em vez de código.
ms.prod: xamarin
ms.assetid: 67CC2CD6-D10A-4B14-9696-1D3A410EFFBF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/25/2017
ms.openlocfilehash: bd6013a0fc1d4f3dfc2d863860052310fed55e4d
ms.sourcegitcommit: 817d26585093cd180a36b28179eb354b0eb900b3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55292136"
---
# <a name="xamarinforms-xaml-basics"></a>Noções básicas de XAML de xamarin. Forms

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/XamlSamples/)

XAML (eXtensible Application Markup Language) permite que os desenvolvedores definam interfaces do usuário em aplicativos Xamarin.Forms usando marcação em vez do código. XAML nunca é necessária em um programa do xamarin. Forms, mas geralmente é mais sucinta e coerente visualmente mais que o código equivalente e potencialmente editável. XAML é especialmente adequado para uso com a arquitetura de aplicativo popular do MVVM (Model-View-ViewModel): XAML define o modo de exibição que esteja vinculado ao código do ViewModel por meio de ligações de dados com base em XAML.

## <a name="xaml-basics-contents"></a>Conteúdo de Noções básicas XAML

* [Visão geral](#Overview)
* [Parte 1. Introdução ao XAML](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
* [Parte 2. Sintaxe essencial de XAML](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
* [Parte 3. Extensões de Marcação XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
* [Parte 4. Conceitos básicos da associação de dados](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
* [Parte 5. De associação de dados a MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)

Além desses artigos Noções básicas de XAML, você pode baixar capítulos do livro [criação de aplicativos móveis com xamarin. Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md):

[![](images/cover-sml.png "Capa do livro")](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md)

Tópicos XAML são abordados mais detalhadamente nos vários capítulos do livro, incluindo:

<table style="border:0px; box-shadow:0 0px 0px" cellpadding="0" cellspacing="2" border="0" width="85%">
<tr style="background:#ecf0f1">
  <td style="border:0px;">
    <h4>Capítulo 7. XAML vs. Código</h4>
  </td>
  <td style="border:0px;" align="right"><a href="https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch07-Apr2016.pdf">Baixar PDF</a> </td>
  <td style="border:0px;" align="right"><a href="~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter07.md">Resumo</a></td>
</tr>
<tr style="background:#f8f9fa">
  <td style="border:0px;">
    <h4>Capítulo 8. Código e XAML em harmonia</h4>
  </td>
  <td style="border:0px;" align="right"><a href="https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch08-Apr2016.pdf">Baixar PDF</a> </td>
  <td style="border:0px;" align="right"><a href="~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter08.md">Resumo</a></td>
</tr>
<tr style="background:#f8f9fa">
  <td style="border:0px;">
    <h4>Capítulo 10. Extensões de marcação XAML</h4>
  </td>
  <td style="border:0px;" align="right"><a href="https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch10-Apr2016.pdf">Baixar PDF</a> </td>
  <td style="border:0px;" align="right"><a href="~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter10.md">Resumo</a></td>
</tr>
<tr style="background:#f8f9fa">
  <td style="border:0px;">
    <h4>Capítulo 18. MVVM</h4>
  </td>
  <td style="border:0px;" align="right"><a href="https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch18-Apr2016.pdf">Baixar PDF</a> </td>
  <td style="border:0px;" align="right"><a href="~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter18.md">Resumo</a></td></tr>
</table>

Esses capítulos podem ser [baixado gratuitamente](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md).

<a name="Overview" />

## <a name="overview"></a>Visão geral

XAML é uma linguagem baseada em XML criada pela Microsoft como uma alternativa ao código de programação para instanciar e inicializando objetos e organizar os objetos em hierarquias pai-filho. XAML tem foram adaptados para várias tecnologias dentro do .NET framework, mas encontrou sua utilidade maior na definição do layout de interfaces do usuário dentro do Windows Presentation Foundation (WPF), Silverlight, o tempo de execução do Windows e o Windows Universal UWP (plataforma).

XAML também é parte do xamarin. Forms, a plataforma cruzada nativamente com base em interface de programação para iOS, Android e UWP dispositivos móveis. Dentro do arquivo XAML, o desenvolvedor de xamarin. Forms pode definir interfaces do usuário usando todos os o xamarin. Forms exibições, layouts e páginas, como classes de cliente. O arquivo XAML pode ser compilado ou incorporado no executável. De qualquer forma, as informações de XAML são analisadas no momento da compilação para localizar objetos nomeados e novamente em tempo de execução para instanciar e inicializar objetos e para estabelecer links entre esses objetos e o código de programação.

XAML tem várias vantagens sobre o código equivalente:

-  XAML geralmente é mais sucinta e legíveis que o código equivalente.
-  A hierarquia pai-filho inerente no XML permite que o XAML simular com maior clareza visual a hierarquia pai-filho de objetos de interface do usuário.
-  XAML pode ser facilmente manuscritas por programadores, mas também serve para ser compatível com ferramentas e geradas por ferramentas de design visual.

Obviamente, também há desvantagens, relacionadas principalmente às limitações que são intrínsecas para linguagens de marcação:

-  XAML não pode conter código. Todos os manipuladores de eventos devem ser definidos em um arquivo de código.
-  XAML não pode conter loops para processamento repetitivo. (No entanto, vários objetos visuais do xamarin. Forms — mais notoriamente [ `ListView` ](xref:Xamarin.Forms.ListView) — pode gerar vários filhos com base nos objetos no seu `ItemsSource` coleção.)
-  XAML não pode conter processamento condicional (no entanto, uma associação de dados pode fazer referência a um conversor de associação baseada em código que permite efetivamente que algum processamento condicional.)
-  XAML geralmente não é possível instanciar classes que definem um construtor sem parâmetros. (No entanto, há algumas vezes, uma solução alternativa para essa restrição.)
-  XAML geralmente não é possível chamar métodos. (Novamente, essa restrição pode, às vezes, ser superada.)

Não há ainda um designer visual para gerar o XAML em aplicativos xamarin. Forms. Todos os XAML deve ser escrito manualmente, mas há uma [XAML pré-visualizador](~/xamarin-forms/xaml/xaml-previewer.md). Os programadores novo XAML talvez queira frequentemente compilar e executar seus aplicativos, especialmente após qualquer coisa que podem não estar corretas, obviamente. Os desenvolvedores até mesmo com muita experiência em XAML sabem que experimentação é gratificante.

XAML é, basicamente, XML, mas a XAML tem alguns recursos exclusivos de sintaxe. Os mais importantes são:

- Elementos de propriedade
- Propriedades anexadas
- Extensões de marcação

Esses recursos estão *não* extensões XML. XAML é XML inteiramente legal. Mas esses recursos de sintaxe XAML usam XML de maneiras exclusivas. Eles serão discutidos em detalhes nos artigos a seguir, que terminam com uma introdução ao uso do XAML para implementar o MVVM.

## <a name="requirements"></a>Requisitos

Este artigo pressupõe que esteja familiarizado com o xamarin. Forms. Este artigo também pressupõe alguma familiaridade com XML, inclusive a entender o uso de declarações de namespace XML e os termos *elemento*, *marca*, e *atributo*.

Quando você estiver familiarizado com o xamarin. Forms e XML, começar a ler [parte 1. Introdução ao XAML](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md).

## <a name="related-links"></a>Links relacionados

- [XamlSamples](https://developer.xamarin.com/samples/xamarin-forms/XamlSamples/)
- [Criar o catálogo de aplicativos móveis](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md)
- [Amostras do Xamarin.Forms](https://developer.xamarin.com/samples/xamarin-forms/all/)
