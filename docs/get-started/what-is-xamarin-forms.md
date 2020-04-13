---
title: O que é o Xamarin.Forms?
description: Este artigo apresenta o Xamarin.Forms e as bibliotecas relacionadas.
ms.prod: xamarin
ms.assetid: C1E24DB9-3099-4F79-BB88-10AABF7D4614
author: profexorgeek
ms.author: jusjohns
ms.date: 09/18/2019
ms.openlocfilehash: aaceb6089a5b7e5f0551dafe9ef1fe50d01433d9
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "77374017"
---
# <a name="what-is-xamarinforms"></a>O que é o Xamarin.Forms?

[![Capturas de tela do exemplo Xamarin.Forms aplicativo no iOS e Android](what-is-xamarin-forms-images/xamarin-forms-app-cropped.png)](what-is-xamarin-forms-images/xamarin-forms-app.png#lightbox)

O Xamarin.Forms é uma estrutura de interface do usuário de software livre. O Xamarin.Forms permite que os desenvolvedores compilem aplicativos Android, iOS e Windows usando uma única base de código compartilhada.

O Xamarin.Forms permite que os desenvolvedores criem interfaces do usuário em XAML com code-behind em C#. Essas interfaces são renderizadas como controles nativos de alto desempenho em cada plataforma.

## <a name="who-xamarinforms-is-for"></a>Para quem é o Xamarin.Forms

O Xamarin.Forms é para os desenvolvedores com os objetivos a seguir:

- Compartilhe o layout e o design da interface do usuário entre plataformas.
- Compartilhe código, teste e lógica de negócios entre plataformas.
- Escreva aplicativos multiplataforma em C# no Visual Studio.

## <a name="how-xamarinforms-works"></a>Como funciona o Xamarin.Forms

![Diagrama da arquitetura do Xamarin.Forms](what-is-xamarin-forms-images/xamarin-forms-architecture.png)

O Xamarin.Forms fornece uma API consistente para a criação de elementos de interface do usuário entre plataformas. Essa API pode ser implementada em XAML ou C# e é compatível com DataBinding para padrões como MVVM (Model-View-ViewModel).

No runtime, o Xamarin.Forms utiliza renderizadores de plataforma para converter os elementos da interface do usuário multiplataforma em controles nativos no Android, iOS e UWP. Isso permite que os desenvolvedores obtenham aparência, sensação e desempenho nativos, enquanto aproveitam os benefícios do compartilhamento de código entre plataformas.

Os aplicativos Xamarin.Forms normalmente consistem em uma biblioteca .NET Standard compartilhada e projetos de plataforma individuais. A biblioteca compartilhada contém exibições XAML ou C# e qualquer lógica de negócios, como serviços, modelos ou outro código. Os projetos de plataforma contêm qualquer lógica específica da plataforma ou pacotes exigidos pelo aplicativo.

O Xamarin.Forms usa o Xamarin para executar aplicativos .NET nativamente entre plataformas. Para obter mais informações sobre o Xamarin, confira [O que é o Xamarin?](~/get-started/what-is-xamarin.md).

## <a name="additional-tools"></a>Ferramentas adicionais

O Xamarin.Forms tem um grande ecossistema de pacotes NuGet que adicionam uma funcionalidade diversificada aos aplicativos. Esta seção descreve alguns pacotes NuGet usados com frequência.

### <a name="xamarinessentials"></a>Xamarin.Essentials

O Xamarin.Essentials é uma biblioteca que fornece APIs multiplataforma para recursos de dispositivo nativo. Assim como o Xamarin, o Xamarin.Essentials é uma abstração que simplifica o processo de acesso a utilitários nativos. Alguns exemplos de utilitários fornecidos pelo Xamarin.Essentials incluem:

- Informações do dispositivo
- Sistema de arquivos
- Acelerômetro
- Discagem telefônica
- Conversão de texto em fala
- Bloqueio de tela

Para obter mais informações, confira [Xamarin.Essentials](~/essentials/index.md).

### <a name="shell"></a>Shell

O Shell do Xamarin.Forms reduz a complexidade do desenvolvimento de aplicativos móveis, fornecendo os recursos fundamentais que a maioria dos aplicativos exige. Alguns exemplos de recursos fornecidos pelo Shell incluem:

- Experiência de navegação do Shell
- Esquema de navegação baseado em URI
- Manipulador de pesquisa integrado

Para obter mais informações, confira [Shell do Xamarin.Forms](~/xamarin-forms/app-fundamentals/shell/index.md)

### <a name="platform-specifics"></a>Elementos específicos da plataforma

O Xamarin.Forms fornece uma API comum que renderiza controles nativos entre plataformas, mas uma plataforma específica pode ter uma funcionalidade que não existe em outras plataformas. Por exemplo, a plataforma Android tem funcionalidade nativa para a rolagem rápida em um `ListView`, mas o iOS não tem. As especificações da plataforma Xamarin.Forms permitem utilizar a funcionalidade disponível apenas em uma plataforma específica sem criar efeitos ou renderizadores personalizados.

O Xamarin.Forms inclui soluções predefinidas para uma variedade de funcionalidades específicas da plataforma. Para obter mais informações, consulte:

- [Elementos específicos da plataforma Xamarin.Forms](~/xamarin-forms/platform/platform-specifics/index.md)
- [Elementos específicos da plataforma do Android](~/xamarin-forms/platform/android/index.md)
- [Elementos específicos da plataforma do iOS](~/xamarin-forms/platform/ios/index.md)
- [Elementos específicos da plataforma do Windows](~/xamarin-forms/platform/windows/index.md)

### <a name="material-visual"></a>Visual material

O Visual do Material do Xamarin.Forms é usado para aplicar regras de design de material a aplicativos Xamarin.Forms. O Visual de Material do Xamarin.Forms utiliza a propriedade Visual para aplicar seletivamente renderizadores personalizados à interface do usuário, resultando em um aplicativo com uma aparência consistente no iOS e no Android.

Para obter mais informações, confira [Visual de Material do Xamarin.Forms](~/xamarin-forms/user-interface/visual/material-visual.md)

## <a name="related-links"></a>Links relacionados

- [Introdução ao Xamarin.Forms](~/xamarin-forms/index.yml)
- [Xamarin.Essentials](~/essentials/index.md)
- [Shell do Xamarin.Forms](~/xamarin-forms/app-fundamentals/shell/index.md)
- [Visual de Material do Xamarin.Forms](~/xamarin-forms/user-interface/visual/material-visual.md)
