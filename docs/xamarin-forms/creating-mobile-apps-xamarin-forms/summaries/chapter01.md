---
title: "Resumo do capítulo 1. Como xamarin. Forms se encaixa?"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F3F864FF-EE70-49D0-90D1-388889037625
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: e1d246ea65e88bde07c3d527c18b7850aa09e08b
ms.sourcegitcommit: 8e722d72c5d1384889f70adb26c5675544897b1f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/15/2018
---
# <a name="summary-of-chapter-1-how-does-xamarinforms-fit-in"></a>Resumo do capítulo 1. Como xamarin. Forms se encaixa?

Um dos trabalhos mais desagradáveis na programação é portar o código de base de uma plataforma para outra, principalmente se essa plataforma envolve uma linguagem de programação diferente. Há uma tentativa ao portar o código para refatorá-lo bem, mas se ambas as plataformas devem ser mantidas em paralelo, em seguida, as diferenças entre as duas bases de código dificultará manutenção futura.

## <a name="cross-platform-mobile-development"></a>Desenvolvimento móvel em plataforma cruzada

Esse problema é comum quando direcionando plataformas móveis. No momento, houver duas principais plataformas móveis, a família de Apple de iPhones e iPads que executam o sistema operacional iOS e o sistema operacional Android que é executado em uma variedade de telefones e tablets. Outra plataforma significativa é Windows UWP da Microsoft (plataforma Universal), que permite que um único programa de destino do Windows 10 e Windows 10 Mobile.

Um fornecedor de software que deseja essas três plataformas de destino deve lidar com paradigmas de interface de usuário diferente, três ambientes de desenvolvimento diferentes, três interfaces de programação diferente e&mdash;talvez mais se&mdash; três linguagens de programação diferentes: Objective-C para o iPhone e iPad, Java para o Android e c# para o Windows.

## <a name="the-c-and-net-solution"></a>A solução c# e .NET

Embora Objective-C, Java e c# são derivados da linguagem de programação C, eles foram desenvolvidos pelos caminhos muito diferentes. C# é o mais recente desses idiomas e tem sido amadurecendo de maneiras muito útil. Além disso, c# é estreitamente relacionados com uma infra-estrutura de programação chamada .NET, que oferece suporte para matemática, depuração, reflexão, coleções, globalização, e/s de arquivo, rede, segurança, threading, serviços web, manipulação de dados e XML e JSON, leitura e gravação.

Xamarin atualmente fornece ferramentas para direcionar o Mac, iOS e Android APIs com c# e o .NET native. Essas ferramentas são chamadas Xamarin.Mac, xamarin e xamarin, coletivamente conhecido como a plataforma do Xamarin. Essas são as associações que expressa as APIs nativas dessas plataformas com linguagens .NET e bibliotecas.

Os desenvolvedores podem usar a plataforma Xamarin para escrever aplicativos em c# que destino Mac, iOS ou Android. Mas quando mais de uma plataforma de direcionamento faz muito sentido para compartilhar a parte do código entre as plataformas de destino. Isso envolve separar o programa em código dependente de plataforma (geralmente envolvendo a interface do usuário) e o código independente de plataforma, o que geralmente exige apenas o base do .NET framework. Esse código independente de plataforma pode residir em uma biblioteca de classe portátil (PCL) ou um projeto compartilhado, geralmente chamado de um projeto de ativo compartilhado ou SAP.

## <a name="introducing-xamarinforms"></a>Introdução ao xamarin. Forms

Ao direcionar várias plataformas móveis, xamarin. Forms permite compartilhamento de código ainda mais. Um único programa gravado para xamarin. Forms pode direcionar cinco plataformas distintas:

- iOS para programas que são executados no iPhone, iPad e iPod touch
- Android para programas que são executados em telefones e tablets Android
- a plataforma Universal do Windows para Windows 10 de destino e Windows 10 Mobile
- o tempo de execução do Windows API do Windows 8.1
- o tempo de execução do Windows API do Windows Phone 8.1

Os modelos de solução xamarin. Forms atuais não incluem os modelos de projetos para as plataformas Windows 8.1 e Windows Phone 8.1.

A maior parte de um programa xamarin. Forms existe em um PCL ou um SAP. Cada uma das plataformas consiste de um stub de aplicativo pequeno que chama o PCL. As APIs Xamarin.Forms mapear para controles nativos em cada plataforma, para que cada plataforma mantém sua aparência característica:

[![Captura de tela de tripla de visuais de plataforma de compartilhamento](images/ch01fg03-small.png "xamarin. Forms controles em cada plataforma")](images/ch01fg03-large.png#lightbox "xamarin. Forms controles em cada plataforma")

As capturas de tela da esquerda para a direita mostram um iPhone, um telefone Android e um telefone Windows 10 Mobile. Em cada tela, a página contém um xamarin. Forms [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) para exibir o texto, um [ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) para iniciar ações, uma [ `Switch` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Switch/) para Escolha um valor de ativar/desativar e um [ `Slider` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Slider/) para especificar um valor dentro de um intervalo contínuo. Todos os quatro dessas exibições são filhos de um [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) em uma [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/).

Também é anexado à página é uma barra de ferramentas xamarin. Forms consiste em vários [ `ToolbarItem` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ToolbarItem/) objetos. Eles são visíveis como ícones na parte superior do iOS e Android telas e, na parte inferior da tela do Windows 10 Mobile.

Xamarin. Forms também oferece suporte a XAML, Extensible Application Markup Language desenvolvido na Microsoft para várias plataformas de aplicativo. Todos os elementos visuais do programa mostrado acima são definidos em XAML, conforme demonstrado no [ **PlatformVisuals** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter01/PlatformVisuals) exemplo.

Um programa xamarin. Forms pode determinar a plataforma que ele está em execução no e executará o código diferente de acordo. Mais potente, os desenvolvedores podem escrever código personalizado para várias plataformas e executar esse código em um programa xamarin. Forms de forma independente de plataforma. Os desenvolvedores também podem criar controles adicionais escrevendo renderizadores para cada plataforma.

Ao xamarin. Forms é uma boa solução para aplicativos de linha de negócios, ou para criação de protótipos, ou fazer uma demonstração de prova de conceito rápida, é menos ideal para aplicativos que necessitam de gráficos vetoriais ou interação complexa de toque.

## <a name="your-development-environment"></a>Ambiente de desenvolvimento

Ambiente de desenvolvimento depende de quais plataformas de que destino e o que as máquinas que você deseja usar.

Se você quiser iOS de destino, será necessário um Mac com o Xcode e a plataforma Xamarin instalado. Suporte Android também requer a instalação do Java e os SDKs necessários. Em seguida, você pode direcionar iOS e Android usando o Visual Studio para Mac.

Instalando o Visual Studio permite no computador de destino todas as plataformas Windows, Android e iOS. No entanto, direcionar iOS do Visual Studio ainda requer um Mac com o Xcode e a plataforma Xamarin instalado.

Você pode testar programas em um dispositivo real conectado por USB no computador ou em um simulador.

## <a name="installation"></a>Instalação

Antes de criar e compilar um aplicativo xamarin. Forms, você deve tentar criar e compilar separadamente de um aplicativo do iOS, um aplicativo do Android e um aplicativo de UWP, dependendo das plataformas que você deseja para seu ambiente de desenvolvimento e de destino.

Os sites Xamarin e Microsoft contêm informações sobre como fazer isso:

- [Guia de Introdução do iOS](~/ios/get-started/index.md)
- [Guia de Introdução do Android](~/android/get-started/index.md)
- [Centro de desenvolvimento do Windows](http://dev.windows.com)

Depois que você pode criar e não executar projetos para essas plataformas individuais, você deve ter nenhum problema criando e executando um aplicativo xamarin. Forms.



## <a name="related-links"></a>Links relacionados

- [Texto completo do capítulo 1 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch01-Apr2016.pdf)
- [Exemplo 1 de capítulo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter01)
