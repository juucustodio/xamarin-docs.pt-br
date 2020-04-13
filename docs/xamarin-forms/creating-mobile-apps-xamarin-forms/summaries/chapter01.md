---
title: Resumo do Capítulo 1. Como o Xamarin.Forms se encaixa?
description: 'Criando aplicativos móveis com Xamarin.Forms: Resumo do Capítulo 1. Como o Xamarin.Forms se encaixa?'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F3F864FF-EE70-49D0-90D1-388889037625
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: 6dfa473bdfb4c1dd88ca833dbf5011a0bbdec42a
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73032891"
---
# <a name="summary-of-chapter-1-how-does-xamarinforms-fit-in"></a>Resumo do Capítulo 1. Como o Xamarin.Forms se encaixa?

[![Baixar](~/media/shared/download.png) amostra Baixar a amostra](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter01)

> [!NOTE]
> As notas nesta página indicam áreas onde xamarin.forms divergiu do material apresentado no livro.

Um dos trabalhos mais desagradáveis na programação é portar uma base de código de uma plataforma para outra, particularmente se essa plataforma envolve uma linguagem de programação diferente. Há uma tentação ao portar o código para refatorá-lo também, mas se ambas as plataformas devem ser mantidas em paralelo, então as diferenças entre as duas bases de código dificultarão a manutenção futura.

## <a name="cross-platform-mobile-development"></a>Desenvolvimento móvel multiplataforma

Esse problema é comum ao mirar plataformas móveis. Atualmente, existem duas grandes plataformas móveis, a família Apple de iPhones e iPads rodando o sistema operacional iOS, e o sistema operacional Android que funciona em uma variedade de telefones e tablets. Outra plataforma significativa é a Universal Windows Platform (UWP) da Microsoft, que permite que um único programa tenha como alvo ambos o Windows 10.

Um fornecedor de software que deseja atingir essas plataformas deve lidar com diferentes paradigmas de interface&mdash;do usuário, três ambientes de desenvolvimento diferentes, três interfaces de programação diferentes e, talvez, mais desajeitadamente&mdash;três linguagens de programação diferentes: Objective-C para o iPhone e iPad, Java para Android e C# para Windows.

## <a name="the-c-and-net-solution"></a>A solução C# e .NET

Embora Objective-C, Java e C# sejam todos derivados da linguagem de programação C, eles evoluíram por caminhos muito diferentes. C# é a mais recente dessas línguas e vem amadurecendo de maneiras muito úteis. Além disso, o C# está intimamente associado a toda uma infra-estrutura de programação chamada .NET, que fornece suporte para matemática, depuração, reflexão, coleções, globalização, I/O de arquivos, rede, segurança, threading, serviços web, manipulação de dados e leitura e escrita XML e JSON.

Atualmente, a Xamarin fornece ferramentas para direcionar as APIs nativas do Mac, iOS e Android usando C# e .NET. Essas ferramentas são chamadas de Xamarin.Mac, Xamarin.iOS e Xamarin.Android, coletivamente conhecida como a plataforma Xamarin. São bibliotecas e vinculações que expressam as APIs nativas dessas plataformas com expressões .NET.

Os desenvolvedores podem usar a plataforma Xamarin para escrever aplicativos em C# que visam Mac, iOS ou Android. Mas ao mirar mais de uma plataforma, faz muito sentido compartilhar parte do código entre as plataformas-alvo. Isso envolve separar o programa em código dependente da plataforma (geralmente envolvendo a interface do usuário) e código independente da plataforma, que geralmente requer apenas a estrutura base .NET. Esse código independente da plataforma pode residir em uma Biblioteca de Classes Portáteis (PCL) ou em um projeto compartilhado, muitas vezes chamado de Projeto de Ativos Compartilhados ou SAP.

> [!NOTE]
> Bibliotecas de classe portáteis foram substituídas por bibliotecas padrão .NET. Todo o código de amostra do livro foi convertido para usar bibliotecas padrão .NET.

## <a name="introducing-xamarinforms"></a>Introdução de Xamarin.Forms

Ao direcionar várias plataformas móveis, o Xamarin.Forms permite ainda mais compartilhamento de código. Um único programa escrito para Xamarin.Forms pode direcionar essas plataformas:

- iOS para programas que são executados no iPhone, iPad e iPod touch
- Android para programas que são executados em celulares e tablets Android
- a Plataforma Universal Windows para atingir o Windows 10

> [!NOTE]
> O Xamarin.Forms não suporta mais o Windows 8.1, o Windows Phone 8.1 ou o Windows 10 Mobile, mas os aplicativos Xamarin.Forms são executados na área de trabalho do Windows 10. Há também suporte de pré-visualização para as plataformas [Mac,](~/xamarin-forms/platform/other/mac.md) [WPF,](~/xamarin-forms/platform/other/wpf.md) [GTK#](~/xamarin-forms/platform/other/gtk.md)e [Tizen.](~/xamarin-forms/platform/other/tizen.md)

A maior parte de um programa Xamarin.Forms existe em uma biblioteca ou em um SAP. Cada uma das plataformas consiste em um pequeno stub de aplicativo que chama para este código compartilhado.

O mapa Xamarin.Forms APIs para controles nativos em cada plataforma, para que cada plataforma mantenha sua aparência e sensação características:

[![Captura de tela tripla do compartilhamento de visuais da plataforma](images/ch01fg03-small.png "Xamarin.Forms Controls em cada plataforma")](images/ch01fg03-large.png#lightbox "Xamarin.Forms Controls em cada plataforma")

As capturas de tela da esquerda para a direita mostram um iPhone e um telefone Android:

Em cada tela, a página contém [`Label`](xref:Xamarin.Forms.Label) um Xamarin.Forms para exibir texto, [`Button`](xref:Xamarin.Forms.Button) [`Switch`](xref:Xamarin.Forms.Switch) um para iniciar ações, um [`Slider`](xref:Xamarin.Forms.Slider) para escolher um valor on/off e um para especificar um valor dentro de um intervalo contínuo. Todas essas quatro visões são [`StackLayout`](xref:Xamarin.Forms.StackLayout) crianças de um em um [`ContentPage`](xref:Xamarin.Forms.ContentPage).

Também anexada à página está uma barra de ferramentas [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) Xamarin.Forms composta por vários objetos. Estes são visíveis como ícones na parte superior das telas iOS e Android, e na parte inferior da tela do Windows 10 Mobile.

O Xamarin.Forms também suporta o XAML, o Linguagem de Marcação de Aplicativos Extensíveis desenvolvido na Microsoft para várias plataformas de aplicativos. Todos os visuais do programa mostrado acima são definidos em XAML, conforme demonstrado na amostra [**PlatformVisuals.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter01/PlatformVisuals)

Um programa Xamarin.Forms pode determinar em qual plataforma está sendo executado e executar códigos diferentes de acordo. Mais poderosamente, os desenvolvedores podem escrever código personalizado para as várias plataformas e executar esse código a partir de um programa Xamarin.Forms de forma independente da plataforma. Os desenvolvedores também podem criar controles adicionais escrevendo renderizadores para cada plataforma.

Embora o Xamarin.Forms seja uma boa solução para aplicativos de linha de negócios, ou para prototipagem, ou para fazer uma demonstração rápida de prova de conceito, é menos ideal para aplicativos que requerem gráficos vetoriais ou interação de toque complexo.

## <a name="your-development-environment"></a>Seu ambiente de desenvolvimento

Seu ambiente de desenvolvimento depende de quais plataformas você deseja segmentar e quais máquinas você deseja usar.

Se você quiser segmentar o iOS, você precisará de um Mac com Xcode e a plataforma Xamarin instalada. O suporte ao Android também requer a instalação do Java e dos SDKs necessários. Em seguida, você pode segmentar tanto o iOS quanto o Android usando o Visual Studio para Mac.

A instalação do Visual Studio permite que no PC você tenha como alvo iOS, Android e todas as plataformas windows. No entanto, o direcionamento do iOS do Visual Studio ainda requer um Mac com Xcode e a plataforma Xamarin instalada.

Você pode testar programas em um dispositivo real conectado por USB ao computador, ou em um simulador.

## <a name="installation"></a>Instalação

Antes de criar e criar um aplicativo Xamarin.Forms, você deve tentar criar e criar separadamente um aplicativo para iOS, um aplicativo Android e um aplicativo UWP, dependendo das plataformas que você deseja segmentar e do seu ambiente de desenvolvimento.

Os sites da Xamarin e da Microsoft contêm informações sobre como fazer isso:

- [Introdução ao iOS](~/ios/get-started/index.md)
- [Introdução ao Android](~/android/get-started/index.md)
- [Central de Desenvolvimento do Windows](https://dev.windows.com)

Uma vez que você pode criar e executar projetos para essas plataformas individuais, você não deve ter problemas em criar e executar um aplicativo Xamarin.Forms.

## <a name="related-links"></a>Links relacionados

- [Texto completo do capítulo 1 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch01-Apr2016.pdf)
- [Amostra do capítulo 1](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter01)
