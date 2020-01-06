---
title: O que é Xamarin. Formas?
description: Este artigo apresenta Xamarin. Formulários e bibliotecas relacionadas.
ms.prod: xamarin
ms.assetid: C1E24DB9-3099-4F79-BB88-10AABF7D4614
author: profexorgeek
ms.author: jusjohns
ms.date: 09/18/2019
no-loc:
- Xamarin
- Xamarin.Forms
- Xamarin.Android
- Xamarin.Essentials
- Xamarin.iOS
- Xamarin.Mac
ms.openlocfilehash: c217acdc3bd5c007822cc29fc730df99e373ba01
ms.sourcegitcommit: 6f09bc2b760e76a61a854f55d6a87c4f421ac6c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/02/2020
ms.locfileid: "75607835"
---
# <a name="what-is-opno-locxamarinforms"></a>O que é Xamarin. Formas?

[![capturas de tela do exemplo [! Parar. Não-LOC (Xamarin)]. Aplicativo de formulários no iOS e Android](what-is-xamarin-forms-images/xamarin-forms-app-cropped.png)](what-is-xamarin-forms-images/xamarin-forms-app.png#lightbox)

Xamarin. O Forms é uma estrutura de interface do usuário de código-fonte aberto. Xamarin. Os formulários permitem que os desenvolvedores compilem aplicativos Android, iOS e Windows de uma única base de código compartilhada.

Xamarin. Os formulários permitem que os desenvolvedores criem interfaces de usuário em XAML com C#code-behind no. Essas interfaces são renderizadas como controles nativos de alto desempenho em cada plataforma.

## <a name="who-opno-locxamarinforms-is-for"></a>Quem Xamarin. Formulários é para

Xamarin. Os formulários são para desenvolvedores com os seguintes objetivos:

- Compartilhe o layout e o design da interface do usuário entre plataformas.
- Compartilhe código, teste e lógica de negócios entre plataformas.
- Escreva aplicativos de plataforma cruzada C# no Visual Studio.

## <a name="how-opno-locxamarinforms-works"></a>Como Xamarin. Formulários funcionam

![[! Parar. Não-LOC (Xamarin)]. Diagrama de arquitetura de formulários](what-is-xamarin-forms-images/xamarin-forms-architecture.png)

Xamarin. O Forms fornece uma API consistente para a criação de elementos de interface do usuário entre plataformas. Essa API pode ser implementada em XAML ou C# e dá suporte a DataBinding para padrões como Model-View-ViewModel (MVVM).

Em tempo de execução, Xamarin. Os formulários utilizam renderizadores de plataforma para converter os elementos da interface do usuário de plataforma cruzada em controles nativos no Android, iOS e UWP. O permite aos desenvolvedores obter aparência, sensação e desempenho nativos e, ao mesmo tempo, perceber os benefícios do compartilhamento de código entre plataformas.

Xamarin. Os aplicativos de formulários normalmente consistem em uma biblioteca de .NET Standard compartilhada e projetos de plataforma individuais. A biblioteca compartilhada contém o XAML ou C# exibições e qualquer lógica de negócios, como serviços, modelos ou outro código. Os projetos de plataforma contêm qualquer lógica específica da plataforma ou pacotes exigidos pelo aplicativo.

Xamarin. Os formulários usam Xamarin para executar aplicativos .NET nativamente entre plataformas. Para obter mais informações sobre Xamarin, consulte [o que é Xamarin?](~/get-started/what-is-xamarin.md).

## <a name="additional-tools"></a>Ferramentas adicionais

Xamarin. Os formulários têm um grande ecossistema de pacotes NuGet que adicionam uma funcionalidade diversificada aos aplicativos. Esta seção descreve alguns pacotes NuGet usados com frequência.

### <a name="opno-locxamarinessentials"></a>Xamarin. Essenciais

Xamarin. O Essentials é uma biblioteca que fornece APIs de plataforma cruzada para recursos de dispositivo nativo. Como Xamarin si mesmo, Xamarin. O Essentials é uma abstração que simplifica o processo de acesso a utilitários nativos. Alguns exemplos de utilitários fornecidos pelo Xamarin. O Essentials inclui:

- Informações do dispositivo
- Sistema de arquivos
- Acelerômetro
- Discagem telefônica
- Conversão de texto em fala
- Bloqueio de tela

Para obter mais informações, consulte [Xamarin. Conceitos básicos](~/essentials/index.md).

### <a name="shell"></a>Shell

Xamarin. O Shell de formulários reduz a complexidade do desenvolvimento de aplicativos móveis fornecendo os recursos fundamentais que a maioria dos aplicativos exigem. Alguns exemplos de recursos fornecidos pelo shell incluem:

- Experiência de navegação comum
- Esquema de navegação baseado em URI
- Manipulador de pesquisa integrado

Para obter mais informações, consulte [Xamarin. Shell de formulários](~/xamarin-forms/app-fundamentals/shell/index.md)

### <a name="platform-specifics"></a>Especificações da plataforma

Xamarin. O Forms fornece uma API comum que processa controles nativos entre plataformas, mas uma plataforma específica pode ter funcionalidade que não existe em outras plataformas. Por exemplo, a plataforma Android tem funcionalidade nativa para a rolagem rápida em um `ListView` mas o iOS não. Xamarin. As especificações de plataforma de formulários permitem utilizar a funcionalidade que só está disponível em uma plataforma específica sem a criação de efeitos ou renderizadores personalizados.

Xamarin. Os formulários incluem soluções predefinidas para uma variedade de funcionalidades específicas da plataforma. Para obter mais informações, consulte .

- [Xamarin. Especificações de plataforma de formulários](~/xamarin-forms/platform/platform-specifics/index.md)
- [Especificações da plataforma Android](~/xamarin-forms/platform/android/index.md)
- [especificações da plataforma iOS](~/xamarin-forms/platform/ios/index.md)
- [Especificações da plataforma Windows](~/xamarin-forms/platform/windows/index.md)

### <a name="material-visual"></a>Visual material

Xamarin. O Visual do material de formulário é usado para aplicar regras de design de material a Xamarin. Aplicativos de formulários. Xamarin. O Visual de material de formulário utiliza a propriedade visual para aplicar seletivamente renderizadores personalizados à interface do usuário, resultando em um aplicativo com uma aparência consistente no iOS e no Android.

Para obter mais informações, consulte [Xamarin. Visual de material de formulários](~/xamarin-forms/user-interface/visual/material-visual.md)

## <a name="related-links"></a>Links relacionados

- [Introdução ao Xamarin. Formas](~/xamarin-forms/index.yml)
- [Xamarin. Essenciais](~/essentials/index.md)
- [Xamarin. Shell de formulários](~/xamarin-forms/app-fundamentals/shell/index.md)
- [Xamarin. Visual de material de formulários](~/xamarin-forms/user-interface/visual/material-visual.md)
