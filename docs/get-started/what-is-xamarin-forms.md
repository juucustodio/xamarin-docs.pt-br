---
title: O que é o Xamarin.Forms?
description: Este artigo apresenta o Xamarin. Forms e as bibliotecas relacionadas.
ms.prod: xamarin
ms.assetid: C1E24DB9-3099-4F79-BB88-10AABF7D4614
author: profexorgeek
ms.author: jusjohns
ms.date: 09/18/2019
ms.openlocfilehash: 8bd530e743330ab8058f13cb7ba09f95a30ee886
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71256577"
---
# <a name="what-is-xamarinforms"></a>O que é o Xamarin.Forms?

[![Capturas de tela do aplicativo de exemplo Xamarin. Forms no iOS e no Android](what-is-xamarin-forms-images/xamarin-forms-app-cropped.png)](what-is-xamarin-forms-images/xamarin-forms-app.png#lightbox)

O Xamarin. Forms é uma estrutura de interface do usuário de código aberto. O Xamarin. Forms permite que os desenvolvedores compilem aplicativos Android, iOS e Windows a partir de uma única base de código compartilhada.

O Xamarin. Forms permite que os desenvolvedores criem interfaces de usuário em XAML com C#code-behind no. Essas interfaces são renderizadas como controles nativos de alto desempenho em cada plataforma.

## <a name="who-xamarinforms-is-for"></a>Quem é o Xamarin. Forms para

O Xamarin. Forms é para os desenvolvedores com os seguintes objetivos:

- Compartilhe o layout e o design da interface do usuário entre plataformas.
- Compartilhe código, teste e lógica de negócios entre plataformas.
- Escreva aplicativos de plataforma cruzada C# no Visual Studio.

## <a name="how-xamarinforms-works"></a>Como o Xamarin. Forms funciona

![Diagrama da arquitetura do Xamarin. Forms](what-is-xamarin-forms-images/xamarin-forms-architecture.png)

O Xamarin. Forms fornece uma API consistente para a criação de elementos de interface do usuário entre plataformas. Essa API pode ser implementada em XAML ou C# e dá suporte a DataBinding para padrões como Model-View-ViewModel (MVVM).

Em tempo de execução, o Xamarin. Forms utiliza renderizadores de plataforma para converter os elementos da interface do usuário de plataforma cruzada em controles nativos no Android, iOS e UWP. O permite aos desenvolvedores obter aparência, sensação e desempenho nativos e, ao mesmo tempo, perceber os benefícios do compartilhamento de código entre plataformas.

Os aplicativos Xamarin. Forms normalmente consistem em uma biblioteca de .NET Standard compartilhada e projetos de plataforma individuais. A biblioteca compartilhada contém o XAML ou C# exibições e qualquer lógica de negócios, como serviços, modelos ou outro código. Os projetos de plataforma contêm qualquer lógica específica da plataforma ou pacotes exigidos pelo aplicativo.

O xamarin. Forms usa o Xamarin para executar aplicativos .NET nativamente entre plataformas. Para obter mais informações sobre o Xamarin, consulte [o que é o xamarin?](~/get-started/what-is-xamarin.md).

## <a name="additional-tools"></a>Ferramentas adicionais

O Xamarin. Forms tem um grande ecossistema de pacotes NuGet que adicionam uma funcionalidade diversificada aos aplicativos. Esta seção descreve alguns pacotes NuGet usados com frequência.

### <a name="xamarinessentials"></a>Xamarin.Essentials

O Xamarin. Essentials é uma biblioteca que fornece APIs de plataforma cruzada para recursos de dispositivo nativo. Assim como o Xamarin, o Xamarin. Essentials é uma abstração que simplifica o processo de acesso a utilitários nativos. Alguns exemplos de utilitários fornecidos pelo Xamarin. Essentials incluem:

- Informações do dispositivo
- Sistema de arquivos
- Acelerômetro
- Discagem telefônica
- Conversão de texto em fala
- Bloqueio de tela

Para obter mais informações, consulte [Xamarin. Essentials](~/essentials/index.md).

### <a name="shell"></a>Shell

O Shell do Xamarin. Forms reduz a complexidade do desenvolvimento de aplicativos móveis fornecendo os recursos fundamentais que a maioria dos aplicativos exige. Alguns exemplos de recursos fornecidos pelo shell incluem:

- Experiência de navegação comum
- Esquema de navegação baseado em URI
- Manipulador de pesquisa integrado

Para obter mais informações, consulte [shell do Xamarin. Forms](~/xamarin-forms/app-fundamentals/shell/index.md)

### <a name="platform-specifics"></a>Especificações da plataforma

O Xamarin. Forms fornece uma API comum que renderiza controles nativos entre plataformas, mas uma plataforma específica pode ter uma funcionalidade que não existe em outras plataformas. Por exemplo, a plataforma Android tem funcionalidade nativa para a rolagem rápida em `ListView` um mas o IOS não. As especificações da plataforma Xamarin. Forms permitem utilizar a funcionalidade que está disponível apenas em uma plataforma específica sem a criação de efeitos ou renderizadores personalizados.

O Xamarin. Forms inclui soluções predefinidas para uma variedade de funcionalidades específicas da plataforma. Para obter mais informações, consulte:

- [Especificações da plataforma Xamarin. Forms](~/xamarin-forms/platform/platform-specifics/index.md)
- [Especificações da plataforma Android](~/xamarin-forms/platform/android/index.md)
- [especificações da plataforma iOS](~/xamarin-forms/platform/ios/index.md)
- [Especificações da plataforma Windows](~/xamarin-forms/platform/windows/index.md)

### <a name="material-visual"></a>Visual material

O Visual material xamarin. Forms é usado para aplicar regras de design de material a aplicativos Xamarin. Forms. O Visual material Xamarin. Forms utiliza a propriedade visual para aplicar seletivamente renderizadores personalizados à interface do usuário, resultando em um aplicativo com uma aparência consistente no iOS e no Android.

Para obter mais informações, consulte [Visual material do Xamarin. Forms](~/xamarin-forms/user-interface/visual/material-visual.md)

## <a name="related-links"></a>Links relacionados

- [Introdução ao Xamarin. Forms](~/xamarin-forms/index.yml)
- [Xamarin.Essentials](~/essentials/index.md)
- [Shell do Xamarin.Forms](~/xamarin-forms/app-fundamentals/shell/index.md)
- [Visual de material do Xamarin. Forms](~/xamarin-forms/user-interface/visual/material-visual.md)
