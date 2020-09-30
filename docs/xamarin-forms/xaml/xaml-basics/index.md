---
title: Xamarin.Forms Noções básicas do XAML
description: Este guia explica como começar a usar o XAML de plataforma cruzada para dispositivos móveis. O XAML permite aos desenvolvedores definir interfaces de usuário em Xamarin.Forms aplicativos usando marcação em vez de código.
ms.prod: xamarin
ms.custom: video
ms.assetid: 67CC2CD6-D10A-4B14-9696-1D3A410EFFBF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/25/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 3d2620014c9554712c746b98de3b9a0eeaa20864
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562750"
---
# <a name="no-locxamarinforms-xaml-basics"></a>Xamarin.Forms Noções básicas do XAML

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)

A linguagem XAML é uma linguagem baseada em XML criada pela Microsoft como uma alternativa ao código de programação para instanciar e inicializar objetos e organizar esses objetos em hierarquias pai-filho. O XAML foi adaptado para várias tecnologias no .NET Framework, mas encontrou seu maior utilitário na definição do layout das interfaces do usuário dentro do Windows Presentation Foundation (WPF), do Silverlight, do Windows Runtime e do UWP (Plataforma Universal do Windows).

O XAML permite aos desenvolvedores definir interfaces de usuário em Xamarin.Forms aplicativos usando marcação em vez de código. O XAML nunca é necessário em um Xamarin.Forms programa, mas geralmente é mais sucinto e mais visualmente coerente do que o código equivalente e potencialmente passível de ferramenta. O XAML é adequado para uso com a arquitetura de aplicativo MVVM (Model-View-ViewModel) popular: XAML define a exibição que está vinculada ao código ViewModel por meio de associações de dados baseadas em XAML.

Em um arquivo XAML, o Xamarin.Forms desenvolvedor pode definir as interfaces do usuário usando todos os Xamarin.Forms modos de exibição, layouts e páginas, bem como classes personalizadas. O arquivo XAML pode ser compilado ou inserido no executável. De qualquer forma, as informações XAML são analisadas no momento da compilação para localizar objetos nomeados e, novamente, em tempo de execução para instanciar e inicializar objetos e para estabelecer links entre esses objetos e o código de programação.

O XAML tem várias vantagens sobre o código equivalente:

- Em geral, o XAML é mais sucinto e legível que o código equivalente.
- A hierarquia pai-filho inerente ao XML permite que o XAML seja imitado com maior clareza visual da hierarquia pai-filho dos objetos da interface do usuário.
- O XAML pode ser facilmente escrito por programadores, mas também se presta para ser editável e gerado por ferramentas de Design Visual.

Também há desvantagens, principalmente relacionadas a limitações que são intrínsecas a linguagens de marcação:

- O XAML não pode conter código. Todos os manipuladores de eventos devem ser definidos em um arquivo de código.
- O XAML não pode conter loops para processamento repetitivo. (No entanto, vários Xamarin.Forms objetos visuais — mais notavelmente  [`ListView`](xref:Xamarin.Forms.ListView) — podem gerar vários filhos com base nos objetos em sua `ItemsSource` coleção.)
- O XAML não pode conter processamento condicional (no entanto, uma vinculação de dados pode fazer referência a um conversor de associação baseado em código que efetivamente permite algum processamento condicional.)
- O XAML geralmente não pode instanciar classes que não definem um construtor sem parâmetros. (No entanto, às vezes há uma maneira de resolver essa restrição.)
- O XAML geralmente não pode chamar métodos. (Novamente, essa restrição pode, às vezes, ser superada.)

Ainda não há um designer visual para gerar XAML em Xamarin.Forms aplicativos. Todo o XAML deve ser escrito à mão, mas há um [Visualizador XAML](~/xamarin-forms/xaml/xaml-previewer/index.md). Os programadores novos para XAML podem querer compilar e executar com frequência seus aplicativos, especialmente depois de qualquer coisa que possa não estar correta. Até mesmo os desenvolvedores com muita experiência no XAML sabem que a experimentação é gratificante.

O XAML é basicamente XML, mas XAML tem alguns recursos de sintaxe exclusivos. O mais importante é:

- Elementos de propriedade
- Propriedades anexadas
- Extensões de marcação

Esses recursos *não* são extensões XML. O XAML é totalmente legal XML. Mas esses recursos de sintaxe XAML usam XML de maneiras exclusivas. Eles são discutidos em detalhes nos artigos abaixo, que terminam com uma introdução ao uso de XAML para implementar o MVVM.

## <a name="requirements"></a>Requisitos

Este artigo pressupõe familiaridade com o Xamarin.Forms . Este artigo também pressupõe alguma familiaridade com XML, incluindo a compreensão do uso de declarações de namespace XML e o *elemento*, a *marca*e o *atributo*dos termos.

Quando estiver familiarizado com o Xamarin.Forms e o XML, comece a ler a [parte 1. Introdução com XAML](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md).

## <a name="related-links"></a>Links relacionados

- [XamlSamples](/samples/xamarin/xamarin-forms-samples/xamlsamples)
- [Criando o livro de aplicativos móveis](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md)
- [Xamarin.Forms Amostras](/samples/browse/?products=xamarin&term=Xamarin.Forms)

## <a name="related-video"></a>Vídeo relacionado

> [!Video https://channel9.msdn.com/Series/Xamarin-101/XamarinForms-UI-with-XAML-5-of-11/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]