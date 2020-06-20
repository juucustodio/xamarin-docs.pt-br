---
title: O que é Xamarin.Forms ?
description: Este artigo apresenta as Xamarin.Forms bibliotecas relacionadas e.
ms.prod: xamarin
ms.assetid: C1E24DB9-3099-4F79-BB88-10AABF7D4614
author: profexorgeek
ms.author: jusjohns
ms.date: 05/28/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: eb76da9be7fcb227c465c0a046b967b2f70b1cfb
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84198313"
---
# <a name="what-is-xamarinforms"></a>O que é Xamarin.Forms ?

[![Capturas de tela do aplicativo de exemplo Xamarin.Forms no Ios e no Android](what-is-xamarin-forms-images/xamarin-forms-app-cropped.png)](what-is-xamarin-forms-images/xamarin-forms-app.png#lightbox)

Xamarin.Formsé uma estrutura de interface do usuário de código-fonte aberto. Xamarin.Formspermite que os desenvolvedores criem aplicativos Xamarin. Android, Xamarin. iOS e Windows a partir de uma única base de código compartilhada.

Xamarin.Formspermite que os desenvolvedores criem interfaces de usuário em XAML com code-behind em C#. Essas interfaces são renderizadas como controles nativos de alto desempenho em cada plataforma.

## <a name="who-xamarinforms-is-for"></a>Quem Xamarin.Forms é para

Xamarin.Formso é para os desenvolvedores com os seguintes objetivos:

- Compartilhe o layout e o design da interface do usuário entre plataformas.
- Compartilhe código, teste e lógica de negócios entre plataformas.
- Escreva aplicativos multiplataforma em C# no Visual Studio.

## <a name="how-xamarinforms-works"></a>Como o Xamarin.Forms funciona

![Xamarin.Formsdiagrama de arquitetura](what-is-xamarin-forms-images/xamarin-forms-architecture.png)

Xamarin.Formsfornece uma API consistente para criar elementos de interface do usuário entre plataformas. Essa API pode ser implementada em XAML ou C# e é compatível com DataBinding para padrões como MVVM (Model-View-ViewModel).

Em tempo de execução, o Xamarin.Forms utiliza renderizadores de plataforma para converter os elementos da interface do usuário de plataforma cruzada em controles nativos em xamarin. Android, xamarin. Ios e UWP. Isso permite que os desenvolvedores obtenham aparência, sensação e desempenho nativos, enquanto aproveitam os benefícios do compartilhamento de código entre plataformas.

Xamarin.Formsos aplicativos normalmente consistem em uma biblioteca de .NET Standard compartilhada e projetos de plataforma individuais. A biblioteca compartilhada contém exibições XAML ou C# e qualquer lógica de negócios, como serviços, modelos ou outro código. Os projetos de plataforma contêm qualquer lógica específica da plataforma ou pacotes exigidos pelo aplicativo.

Xamarin.Formsusa a plataforma Xamarin para executar aplicativos .NET nativamente entre plataformas. Para obter mais informações sobre a plataforma Xamarin, consulte [o que é o xamarin?](~/get-started/what-is-xamarin.md).

## <a name="additional-functionality"></a>Funcionalidade adicional

Xamarin.Formso tem um grande ecossistema de bibliotecas que adicionam uma funcionalidade diversificada aos aplicativos. Esta seção descreve algumas dessas funcionalidades adicionais.

### Xamarin.Essentials

Xamarin.Essentialsé uma biblioteca que fornece APIs de plataforma cruzada para recursos de dispositivo nativo. Como o Xamarin, Xamarin.Essentials é uma abstração que simplifica o processo de acesso a utilitários nativos. Alguns exemplos de utilitários fornecidos pelo Xamarin.Essentials incluem:

- Informações do dispositivo
- Sistema de arquivos
- Acelerômetro
- Discagem telefônica
- Conversão de texto em fala
- Bloqueio de tela

Para obter mais informações, consulte [Xamarin.Essentials](~/essentials/index.md).

### <a name="shell"></a>Shell

Xamarin.FormsO Shell reduz a complexidade do desenvolvimento de aplicativos móveis fornecendo os recursos fundamentais que a maioria dos aplicativos exigem. Alguns exemplos de recursos fornecidos pelo Shell incluem:

- Experiência de navegação do Shell
- Esquema de navegação baseado em URI
- Manipulador de pesquisa integrado

Para obter mais informações, consulte [ Xamarin.Forms shell](~/xamarin-forms/app-fundamentals/shell/index.md)

### <a name="platform-specifics"></a>Elementos específicos da plataforma

Xamarin.Formsfornece uma API comum que processa controles nativos entre plataformas, mas uma plataforma específica pode ter uma funcionalidade que não existe em outras plataformas. Por exemplo, a plataforma Android tem funcionalidade nativa para a rolagem rápida em um `ListView`, mas o iOS não tem. Xamarin.Formsas especificações de plataforma permitem utilizar a funcionalidade que só está disponível em uma plataforma específica sem a criação de efeitos ou renderizadores personalizados.

Xamarin.Formsinclui soluções predefinidas para uma variedade de funcionalidades específicas da plataforma. Para obter mais informações, consulte:

- [Xamarin.Formsespecificações da plataforma](~/xamarin-forms/platform/platform-specifics/index.md)
- [Especificações da plataforma Android](~/xamarin-forms/platform/android/index.md)
- [especificações da plataforma iOS](~/xamarin-forms/platform/ios/index.md)
- [Elementos específicos da plataforma do Windows](~/xamarin-forms/platform/windows/index.md)

### <a name="material-visual"></a>Visual material

Xamarin.FormsO Visual material é usado para aplicar regras de design de material a Xamarin.Forms aplicativos. Xamarin.FormsO Visual material utiliza a propriedade visual para aplicar seletivamente renderizadores personalizados à interface do usuário, resultando em um aplicativo com uma aparência consistente no iOS e no Android.

Para obter mais informações, consulte [ Xamarin.Forms material Visual](~/xamarin-forms/user-interface/visual/material-visual.md)

## <a name="related-links"></a>Links relacionados

- [Introdução aoXamarin.Forms](~/xamarin-forms/index.yml)
- [Xamarin.Essentials](~/essentials/index.md)
- [Xamarin.FormsShell](~/xamarin-forms/app-fundamentals/shell/index.md)
- [Xamarin.FormsVisual do material](~/xamarin-forms/user-interface/visual/material-visual.md)
