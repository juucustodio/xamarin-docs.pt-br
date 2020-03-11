---
title: Resumo do capítulo 1. Como xamarin. Forms se encaixa?
description: 'Criação de aplicativos móveis com xamarin. Forms: Resumo do capítulo 1. Como xamarin. Forms se encaixa?'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F3F864FF-EE70-49D0-90D1-388889037625
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: 6dfa473bdfb4c1dd88ca833dbf5011a0bbdec42a
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2020
ms.locfileid: "73032891"
---
# <a name="summary-of-chapter-1-how-does-xamarinforms-fit-in"></a>Resumo do capítulo 1. Como xamarin. Forms se encaixa?

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter01)

> [!NOTE]
> Observações sobre essa página indicam áreas onde o xamarin. Forms foi bifurcado do material apresentado no livro.

Um dos trabalhos mais desagradáveis na programação é portar um código de base de uma plataforma para outra, especialmente se essa plataforma envolve uma linguagem de programação diferente. Há uma tentação ao portar o código para refatorá-lo bem, mas se ambas as plataformas devem ser mantidas em paralelo, em seguida, as diferenças entre as duas bases de código dificultará manutenção futura.

## <a name="cross-platform-mobile-development"></a>Desenvolvimento móvel multiplataforma

Esse problema é comum ao direcionar para plataformas móveis. Atualmente, existem duas principais plataformas móveis, a família de Apple de iPhones e iPads que executam o sistema operacional iOS e o sistema operacional Android que é executado em uma variedade de telefones e tablets. Outra plataforma significativa é a Plataforma Universal do Windows da Microsoft (UWP), que permite que um único programa direcione o Windows 10.

Um fornecedor de software que deseja direcionar essas plataformas deve lidar com paradigmas de interface do usuário diferentes, três ambientes de desenvolvimento diferentes, três interfaces de programação diferentes e&mdash;talvez, mais inadequadamente,&mdash;três linguagens de programação diferentes: Objective-C para iPhone C# e iPad, Java para Android e para Windows.

## <a name="the-c-and-net-solution"></a>A solução c# e .NET

Embora Objective-C, Java e c# são derivados da linguagem de programação C, eles foram desenvolvidos por caminhos muito diferentes. C# é a mais recente dessas linguagens e tem sido amadurecimento de maneiras muito útil. Além disso, o c# é estreitamente relacionados com uma infra-estrutura de programação toda chamada .NET, que fornece suporte para matemática, depuração, reflexão, coleções, globalização, e/s de arquivo, rede, segurança, threading, serviços web, manipulação de dados e XML e JSON de leitura e gravação.

Atualmente, Xamarin fornece ferramentas para direcionar o Mac, iOS e APIs do Android usando c# e .NET native. Essas ferramentas são chamadas de xamarin. Mac, xamarin. IOS e xamarin. Android, coletivamente conhecido como a plataforma Xamarin. Essas são as bibliotecas e as ligações que expressam as APIs nativas dessas plataformas com linguagens .NET.

Os desenvolvedores podem usar a plataforma Xamarin para escrever aplicativos em c# destinados ao Mac, iOS ou Android. Mas quando mais de uma plataforma de direcionamento, faz muito sentido para compartilhar parte do código entre as plataformas de destino. Isso envolve a separação de programa em código dependente de plataforma (geralmente envolvendo a interface do usuário) e o código independente de plataforma, o que geralmente requer apenas o base do .NET framework. Esse código independente de plataforma ou pode residir em uma biblioteca de classe portátil (PCL) ou um projeto compartilhado, geralmente chamado de um projeto de ativo compartilhado ou SAP.

> [!NOTE]
> Bibliotecas de classes portáteis foram substituídas por bibliotecas .NET Standard. O código de exemplo do livro foi convertido para usar bibliotecas padrão do .NET.

## <a name="introducing-xamarinforms"></a>Introdução ao xamarin. Forms

Ao direcionar várias plataformas móveis, xamarin. Forms permite o compartilhamento de código ainda mais. Um único programa escrito para Xamarin. Forms pode direcionar estas plataformas:

- iOS para programas que são executados no iPhone, iPad e iPod touch
- Android para programas que são executados em telefones e tablets Android
- o Plataforma Universal do Windows para o Windows 10 de destino

> [!NOTE]
> Xamarin. Forms não oferece suporte a Windows 8.1, Windows Phone 8.1 ou Windows 10 Mobile, mas os aplicativos xamarin. Forms são executados no Windows 10 desktop. Também há suporte de visualização para as plataformas [Mac](~/xamarin-forms/platform/other/mac.md), [WPF](~/xamarin-forms/platform/other/wpf.md), [GTK #](~/xamarin-forms/platform/other/gtk.md)e [tizen](~/xamarin-forms/platform/other/tizen.md) .

A maior parte de um programa do xamarin. Forms existe em uma biblioteca ou um SAP. Cada uma das plataformas consiste em um stub de aplicativo pequeno que chama esse código compartilhado.

As APIs Xamarin.Forms mapeados para controles nativos em cada plataforma, para que cada plataforma mantém sua aparência característica:

[![Captura de tela tripla de compartilhamento de visuais de plataforma](images/ch01fg03-small.png "Controles Xamarin. Forms em cada plataforma")](images/ch01fg03-large.png#lightbox "Controles Xamarin. Forms em cada plataforma")

As capturas de tela da esquerda para a direita mostram um iPhone e um telefone Android:

Em cada tela, a página contém um [`Label`](xref:Xamarin.Forms.Label) Xamarin. Forms para exibir texto, um [`Button`](xref:Xamarin.Forms.Button) para ações de inicialização, um [`Switch`](xref:Xamarin.Forms.Switch) para escolher um valor de on/off e um [`Slider`](xref:Xamarin.Forms.Slider) para especificar um valor dentro de um intervalo contínuo. Todas as quatro exibições são filhas de um [`StackLayout`](xref:Xamarin.Forms.StackLayout) em um [`ContentPage`](xref:Xamarin.Forms.ContentPage).

Também anexado à página é uma barra de ferramentas Xamarin. Forms que consiste em vários objetos [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) . Eles são visíveis como ícones na parte superior do iOS e Android telas e, na parte inferior da tela do Windows 10 Mobile.

Xamarin. Forms também oferece suporte a XAML, a Extensible Application Markup Language de desenvolvidos na Microsoft para várias plataformas de aplicativo. Todos os visuais do programa mostrado acima são definidos em XAML, conforme demonstrado no exemplo de [**PlatformVisuals**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter01/PlatformVisuals) .

Um programa de xamarin. Forms pode determinar em qual plataforma na qual está sendo executado e executar um código diferente de acordo. Mais potente, os desenvolvedores podem escrever código personalizado para várias plataformas e executar esse código a partir de um programa do xamarin. Forms de maneira independente de plataforma. Os desenvolvedores também podem criar controles adicionais escrevendo renderizadores para cada plataforma.

Enquanto o xamarin. Forms é uma boa solução para aplicativos de linha de negócios ou para criação de protótipos, ou fazer uma rápida demonstração de prova de conceito, é menos ideal para aplicativos que requerem interação por toque complexos ou gráficos vetoriais.

## <a name="your-development-environment"></a>Ambiente de desenvolvimento

Ambiente de desenvolvimento depende de quais plataformas de destino e quais computadores você deseja usar.

Se você desejar direcionar ao iOS, você precisará de um Mac com Xcode e a plataforma Xamarin instalados. Suporte a Android também exige a instalação do Java e os SDKs necessários. Você pode, em seguida, direcionar o iOS e Android usando o Visual Studio para Mac.

Instalar o Visual Studio permite no PC de destino todas as plataformas Windows, Android e iOS. No entanto, direcionamento ao iOS no Visual Studio ainda requer um Mac com Xcode e a plataforma Xamarin instalados.

Você pode testar os programas em um dispositivo real conectado por USB ao computador ou em um simulador.

## <a name="installation"></a>Instalação

Antes de criar e compilar um aplicativo xamarin. Forms, você deve tentar criar e compilar separadamente de um aplicativo iOS, um aplicativo do Android e um aplicativo UWP, dependendo das plataformas que deseja para seu ambiente de desenvolvimento e de destino.

Os sites Xamarin e da Microsoft contêm informações sobre como fazer isso:

- [Introdução com iOS](~/ios/get-started/index.md)
- [Introdução com Android](~/android/get-started/index.md)
- [Central de Desenvolvimento do Windows](https://dev.windows.com)

Depois que você pode criar e executar projetos para essas plataformas individuais, você deve enfrentar nenhum problema criando e executando um aplicativo xamarin. Forms.

## <a name="related-links"></a>Links relacionados

- [Capítulo 1 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch01-Apr2016.pdf)
- [Exemplo do capítulo 1](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter01)
