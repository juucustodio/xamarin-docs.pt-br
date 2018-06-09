---
title: Noções básicas de XAML xamarin. Forms
description: Este guia explica como começar com XAML de plataforma cruzada para dispositivos móveis. XAML permite aos desenvolvedores a definição de interfaces de usuário em aplicativos xamarin. Forms usando marcação em vez do código.
ms.prod: xamarin
ms.assetid: 67CC2CD6-D10A-4B14-9696-1D3A410EFFBF
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 10/25/2017
ms.openlocfilehash: 627267b95bb2d810a60f84c51e38bf5387fe1f99
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35245957"
---
# <a name="xamarinforms-xaml-basics"></a>Noções básicas de XAML xamarin. Forms

XAML (eXtensible Application Markup Language) permite que os desenvolvedores definam interfaces do usuário em aplicativos Xamarin.Forms usando marcação em vez do código. XAML nunca é necessária em um programa xamarin. Forms, mas geralmente é mais sucinta e visualmente mais coerente de código equivalentes e potencialmente editável. XAML é especialmente adequado para uso com a arquitetura do aplicativo MVVM (Model-View-ViewModel) popular: XAML define o modo de exibição que está vinculado ao ViewModel código por meio de ligações de dados baseados em XAML.

## <a name="xaml-basics-contents"></a>Conteúdo de Noções básicas XAML

* [Visão geral](#Overview)
* [Parte 1. Introdução ao XAML](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
* [Parte 2. Sintaxe essencial de XAML](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
* [Parte 3. Extensões de Marcação XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
* [Parte 4. Conceitos básicos da associação de dados](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
* [Parte 5. De associação de dados para o modelo MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)

Além desses artigos Noções básicas de XAML, você pode baixar capítulos do livro [criação de aplicativos móveis com o xamarin. Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md):

[![](images/cover-sml.png "Capa do livro")](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md)

XAML tópicos são abordados mais detalhadamente nos capítulos muitas do catálogo, incluindo:

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

XAML é uma linguagem baseada em XML criada pela Microsoft, como uma alternativa para o código de programação para criar uma instância e inicializando objetos e organizar os objetos em hierarquias pai-filho. XAML tem foi adaptado para várias tecnologias dentro do .NET framework, mas encontrou sua maior utilidade para definir o layout das interfaces de usuário no Windows Presentation Foundation (WPF), Silverlight, o tempo de execução do Windows e o Universal do Windows UWP (plataforma).

XAML também faz parte do xamarin. Forms, a plataforma cruzada nativamente com base em interface de programação para iOS, Android e UWP dispositivos móveis. Dentro do arquivo XAML, o desenvolvedor do xamarin. Forms pode definir usando todo o xamarin. Forms exibições, layouts e páginas, como classes personalizadas bem como as interfaces de usuário. O arquivo XAML pode ser compilado ou incorporado no executável. De qualquer forma, as informações de XAML são analisadas em tempo de compilação para localizar objetos nomeados e novamente em tempo de execução para instanciar e inicializar objetos e para estabelecer links entre esses objetos e o código de programação.

XAML tem várias vantagens sobre código equivalente:

-  XAML é geralmente mais sucinta e legível que código equivalente.
-  A hierarquia pai-filho inerente XML permite que o XAML simular com maior clareza visual a hierarquia pai-filho de objetos de interface do usuário.
-  XAML pode ser facilmente manual por programadores, mas serve também para ser compatível com ferramentas e gerados por ferramentas de design visual.

Naturalmente, também há desvantagens, relacionadas principalmente às limitações são intrínsecas para linguagens de marcação:

-  XAML não pode conter código. Todos os manipuladores de eventos devem ser definidos em um arquivo de código.
-  XAML não pode conter loops para processamento repetitivo. (No entanto, vários objetos de visual xamarin. Forms — especialmente [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) — pode gerar vários filhos com base nos objetos no seu `ItemsSource` coleção.)
-  XAML não pode conter processamento condicional (no entanto, uma associação de dados pode fazer referência a um conversor de associação baseada em código que permite efetivamente que algum processamento condicional.)
-  XAML geralmente não é possível instanciar classes que não definem um construtor sem parâmetros. (No entanto, há algumas vezes, uma maneira para contornar essa restrição.)
-  XAML geralmente não é possível chamar métodos. (Novamente, essa restrição pode às vezes ser superada.)

Não há ainda um designer visual para gerar o XAML em aplicativos xamarin. Forms. Todos os XAML deve ser manual, mas não há um [XAML visualizador](~/xamarin-forms/xaml/xaml-previewer.md). Programadores novos para XAML talvez queira frequentemente compilar e executar seus aplicativos, principalmente após qualquer coisa que não podem ser logicamente corretas. Os desenvolvedores até mesmo com muita experiência em XAML sabem que experimentação é gratificante.

XAML é, basicamente, XML, mas não tem alguns recursos de sintaxe XAML. São os mais importantes:

- Elementos de propriedade
- Propriedades anexadas
- Extensões de marcação

Esses recursos são *não* extensões XML. XAML é XML inteiramente legal. Mas esses recursos de sintaxe XAML usam XML de maneiras exclusivas. Eles serão discutidos em detalhes nos artigos a seguir, que terminam com uma introdução ao uso de XAML para implementar MVVM.

## <a name="requirements"></a>Requisitos

Este artigo pressupõe que esteja familiarizado com o xamarin. Forms. Lendo [uma introdução ao xamarin. Forms](~/xamarin-forms/get-started/introduction-to-xamarin-forms.md) é altamente recomendável.

Este artigo também pressupõe familiaridade com o XML, inclusive a entender o uso de declarações de namespace XML e os termos de *elemento*, *marca*, e *atributo*.

Quando você estiver familiarizado com o xamarin. Forms e XML, comece a ler [parte 1. Guia de Introdução com XAML](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md).



## <a name="related-links"></a>Links relacionados

- [XamlSamples](https://developer.xamarin.com/samples/xamarin-forms/XamlSamples/)
- [Uma introdução ao xamarin. Forms](~/xamarin-forms/get-started/introduction-to-xamarin-forms.md)
- [Criar o catálogo de aplicativos móveis](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md)
- [Amostras do Xamarin.Forms](https://developer.xamarin.com/samples/xamarin-forms/all/)
