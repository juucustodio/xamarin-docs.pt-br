---
title: Resumo do capítulo 1. Como o Xamarin.Forms se encaixa?
description: 'Criando aplicativos móveis com Xamarin.Forms : Resumo do capítulo 1. Como o Xamarin.Forms se encaixa?'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F3F864FF-EE70-49D0-90D1-388889037625
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1af4b990d388436c04a2feb0abe3d4e3a8bc6756
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93370449"
---
# <a name="summary-of-chapter-1-how-does-no-locxamarinforms-fit-in"></a>Resumo do capítulo 1. Como o Xamarin.Forms se encaixa?

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter01)

> [!NOTE]
> Este livro foi publicado na Primavera de 2016 e não foi atualizado desde então. Há muito no livro que permanece valioso, mas alguns materiais estão desatualizados e alguns tópicos não estão mais totalmente corretos ou completos.

Um dos trabalhos mais desagradáveis na programação é portar uma base de código de uma plataforma para outra, especialmente se essa plataforma envolver uma linguagem de programação diferente. Há uma tentação ao portar o código para refatorá-lo também, mas se ambas as plataformas precisarem ser mantidas em paralelo, as diferenças entre as duas bases de código tornarão a manutenção futura mais difícil.

## <a name="cross-platform-mobile-development"></a>Desenvolvimento móvel multiplataforma

Esse problema é comum ao direcionar plataformas móveis. Atualmente, existem duas principais plataformas móveis, a família Apple de iPhones e iPads executando o sistema operacional iOS e o sistema operacional Android que é executado em vários telefones e tablets. Outra plataforma significativa é a Plataforma Universal do Windows da Microsoft (UWP), que permite que um único programa direcione o Windows 10.

Um fornecedor de software que deseja direcionar essas plataformas deve lidar com paradigmas de interface do usuário diferentes, três ambientes de desenvolvimento diferentes, três interfaces de programação diferentes e, &mdash; talvez, mais inadequadamente &mdash; três linguagens de programação diferentes: Objective-C para iPhone e iPad, Java para Android e C# para Windows.

## <a name="the-c-and-net-solution"></a>A solução C# e .NET

Embora o Objective-C, o Java e o C# sejam todos derivados da linguagem de programação C, eles evoluíram por caminhos muito diferentes. O C# é a mais recente dessas linguagens e vem de maneira muito útil. Além disso, o C# está fortemente associado a uma infra-estrutura de programação inteira chamada .NET, que oferece suporte a matemática, depuração, reflexo, coleções, globalização, e/s de arquivo, rede, segurança, Threading, serviços Web, manipulação de dados e leitura e gravação de XML e JSON.

Atualmente, o Xamarin fornece ferramentas para direcionar as APIs nativas de Mac, iOS e Android usando C# e .NET. Essas ferramentas são chamadas de Xamarin. Mac, Xamarin. iOS e Xamarin. Android, coletivamente conhecidos como a plataforma Xamarin. Essas são bibliotecas e associações que expressam as APIs nativas dessas plataformas com linguagens .NET.

Os desenvolvedores podem usar a plataforma Xamarin para escrever aplicativos em C# direcionados para Mac, iOS ou Android. Mas, ao direcionar mais de uma plataforma, faz muito sentido compartilhar parte do código entre as plataformas de destino. Isso envolve a separação do programa em código dependente de plataforma (geralmente envolvendo a interface do usuário) e o código independente de plataforma, que geralmente requer apenas o .NET Framework base. Esse código independente de plataforma pode residir em uma PCL (biblioteca de classes portátil) ou em um projeto compartilhado, geralmente chamado de projeto de ativo compartilhado ou SAP.

> [!NOTE]
> As bibliotecas de classes portáteis foram substituídas por bibliotecas de .NET Standard. Todo o código de exemplo do livro foi convertido para usar bibliotecas do .NET Standard.

## <a name="introducing-no-locxamarinforms"></a>Apresentando Xamarin.Forms

Ao direcionar várias plataformas móveis, Xamarin.Forms permite ainda mais o compartilhamento de código. Um único programa escrito para Xamarin.Forms pode direcionar estas plataformas:

- iOS para programas que são executados no iPhone, iPad e iPod Touch
- Android para programas que são executados em telefones e tablets Android
- o Plataforma Universal do Windows para o Windows 10 de destino

> [!NOTE]
> Xamarin.Forms Não dá mais suporte a Windows 8.1, Windows Phone 8,1 ou Windows 10 Mobile, mas Xamarin.Forms os aplicativos são executados na área de trabalho do Windows 10. Também há suporte de visualização para as plataformas [Mac](~/xamarin-forms/platform/other/mac.md), [WPF](~/xamarin-forms/platform/other/wpf.md), [GTK #](~/xamarin-forms/platform/other/gtk.md)e [tizen](~/xamarin-forms/platform/other/tizen.md) .

A maior parte de um Xamarin.Forms programa existe em uma biblioteca ou em um SAP. Cada uma das plataformas consiste em um stub de aplicativo pequeno que chama esse código compartilhado.

As Xamarin.Forms APIs são mapeadas para controles nativos em cada plataforma, de forma que cada plataforma mantenha sua aparência característica:

[![Captura de tela tripla de compartilhamento de visuais de plataforma](images/ch01fg03-small.png "::: no-Loc (Xamarin. Forms)::: Controls em cada plataforma")](images/ch01fg03-large.png#lightbox "::: no-Loc (Xamarin. Forms)::: Controls em cada plataforma")

As capturas de tela da esquerda para a direita mostram um iPhone e um telefone Android:

Em cada tela, a página contém um Xamarin.Forms [`Label`](xref:Xamarin.Forms.Label) para exibir o texto, um [`Button`](xref:Xamarin.Forms.Button) para iniciar ações, um [`Switch`](xref:Xamarin.Forms.Switch) para escolher um valor de ativar/desativar e um [`Slider`](xref:Xamarin.Forms.Slider) para especificar um valor dentro de um intervalo contínuo. Todas as quatro exibições são filhas de um [`StackLayout`](xref:Xamarin.Forms.StackLayout) em um [`ContentPage`](xref:Xamarin.Forms.ContentPage) .

Também anexado à página é uma Xamarin.Forms barra de ferramentas que consiste em vários [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) objetos. Eles são visíveis como ícones na parte superior das telas do iOS e do Android e na parte inferior da tela do Windows 10 Mobile.

Xamarin.Forms também dá suporte a XAML, o Extensible Application Markup Language desenvolvido na Microsoft para várias plataformas de aplicativos. Todos os visuais do programa mostrado acima são definidos em XAML, conforme demonstrado no exemplo de [**PlatformVisuals**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter01/PlatformVisuals) .

Um Xamarin.Forms programa pode determinar em qual plataforma ele está sendo executado e executar código diferente adequadamente. De forma mais eficiente, os desenvolvedores podem escrever código personalizado para as várias plataformas e executar esse código de um Xamarin.Forms programa de maneira independente da plataforma. Os desenvolvedores também podem criar controles adicionais escrevendo renderizadores para cada plataforma.

Embora Xamarin.Forms seja uma boa solução para aplicativos de linha de negócios, ou para criar protótipos ou fazer uma demonstração rápida de prova de conceito, é menos ideal para aplicativos que exigem gráficos vetoriais ou interação de toque complexa.

## <a name="your-development-environment"></a>Seu ambiente de desenvolvimento

Seu ambiente de desenvolvimento depende de quais plataformas você deseja direcionar e quais computadores você deseja usar.

Se quiser direcionar para o iOS, você precisará de um Mac com Xcode e a plataforma Xamarin instalada. O suporte ao Android também requer a instalação do Java e dos SDKs necessários. Em seguida, você pode direcionar o iOS e o Android usando Visual Studio para Mac.

A instalação do Visual Studio permite que no PC você direcione para iOS, Android e todas as plataformas Windows. No entanto, o direcionamento ao iOS do Visual Studio ainda requer um Mac com o Xcode e a plataforma Xamarin instalada.

Você pode testar programas em um dispositivo real conectado por USB ao computador ou em um simulador.

## <a name="installation"></a>Instalação

Antes de criar e criar um Xamarin.Forms aplicativo, você deve tentar criar e compilar separadamente um aplicativo IOS, um aplicativo Android e um aplicativo UWP, dependendo das plataformas que você deseja direcionar e de seu ambiente de desenvolvimento.

Os sites do Xamarin e da Microsoft contêm informações sobre como fazer isso:

- [Introdução ao iOS](~/ios/get-started/index.md)
- [Introdução ao Android](~/android/get-started/index.md)
- [Central de Desenvolvimento do Windows](https://dev.windows.com)

Depois de criar e executar projetos para essas plataformas individuais, você não deve ter problemas ao criar e executar um Xamarin.Forms aplicativo.

## <a name="related-links"></a>Links relacionados

- [Capítulo 1 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch01-Apr2016.pdf)
- [Exemplo do capítulo 1](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter01)
