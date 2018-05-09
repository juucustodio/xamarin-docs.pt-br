---
title: Conceitos básicos de aplicativo
description: Explorando os conceitos básicos do desenvolvimento do xamarin. Forms
ms.prod: xamarin
ms.assetid: 7B516BBC-F7E1-4387-9779-7754E2E69723
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/28/2017
ms.openlocfilehash: 2bd050e6eba33b543c52a80bb42b184200164c14
ms.sourcegitcommit: e16517edcf471b53b4e347cd3fd82e485923d482
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
---
# <a name="application-fundamentals"></a>Conceitos básicos de aplicativo

## <a name="accessibilityaccessibilityindexmd"></a>[Acessibilidade](accessibility/index.md)

Dicas para incorporar recursos acessíveis (como ferramentas de leitura de tela de suporte) com xamarin. Forms.

## <a name="app-classapplication-classmd"></a>[Classe de aplicativo](application-class.md)

O `Application` classe é o ponto de partida para xamarin. Forms – cada aplicativo precisa implementar uma subclasse `App` para definir a página inicial. Ele também fornece o `Properties` coleta para armazenamento de dados simples. Ela pode ser definida em c# ou XAML.

## <a name="app-lifecycleapp-lifecyclemd"></a>[Ciclo de vida do aplicativo](app-lifecycle.md)

O `Application` classe `OnStart`, `OnSleep`, e `OnResume` métodos, bem como dos eventos de navegação modais permitem manipular eventos de ciclo de vida do aplicativo com o código personalizado.

## <a name="behaviorsbehaviorsindexmd"></a>[Comportamentos](behaviors/index.md)

Controles de interface do usuário podem ser facilmente estendidos sem subclassificação usando comportamentos para adicionar funcionalidade.

## <a name="custom-rendererscustom-rendererindexmd"></a>[Renderizadores personalizados](custom-renderer/index.md)

Renderiza personalizadas permitem que os desenvolvedores 'override' o processamento padrão de controles xamarin. Forms para personalizar sua aparência e comportamento em cada plataforma (usando SDKs nativo, se desejado).

## <a name="data-bindingdata-bindingindexmd"></a>[Associação de dados](data-binding/index.md)

Associação de dados vincula as propriedades de dois objetos, permitindo que as alterações em uma propriedade sejam refletidas automaticamente na outra propriedade. Associação de dados é uma parte integrante do Model-View-ViewModel ([MVVM](~/xamarin-forms/enterprise-application-patterns/mvvm.md)) arquitetura do aplicativo.

## <a name="dependency-servicedependency-serviceindexmd"></a>[Serviço de dependência](dependency-service/index.md)

O `DependencyService` fornece um localizador de simple para que você pode codificar para interfaces no seu código compartilhado e fornecer implementações de específico de plataforma que são resolvidas automaticamente, tornando mais fácil fazer referência a funcionalidade específica de plataforma em xamarin. Forms.

## <a name="effectseffectsindexmd"></a>[Efeitos](effects/index.md)

Efeitos de permitir que os controles nativos em cada plataforma para personalização e são geralmente usados para o estilo de pequenas alterações.

## <a name="gesturesgesturesindexmd"></a>[Gestos](gestures/index.md)

O xamarin. Forms [ `GestureRecognizer` ](https://developer.xamarin.com/api/type/Xamarin.Forms.GestureRecognizer/) classe oferece suporte a gestos de panorâmica, aperto e toque em controles de interface do usuário.

## <a name="localizationlocalizationindexmd"></a>[Localização](localization/index.md)

A estrutura interna de localização do .NET pode ser usada para criar aplicativos multilíngues de plataforma cruzada com xamarin. Forms.

## <a name="local-databasesdatabasesmd"></a>[Bancos de dados locais](databases.md)

Xamarin. Forms oferece suporte a aplicativos controlados por banco de dados usando o mecanismo de banco de dados SQLite, o que torna possível carregar e salvar objetos no código compartilhado.

## <a name="messaging-centermessaging-centermd"></a>[Centro de mensagens](messaging-center.md)

Xamarin. Forms `MessagingCenter` permite exibir modelos e outros componentes para se comunicar com sem precisar saber nada sobre uns aos outros, além de um contrato de mensagem simple.

## <a name="navigationnavigationindexmd"></a>[Navegação](navigation/index.md)

Xamarin. Forms fornece um número de experiências de navegação de página diferente, dependendo do `Page` tipo que está sendo usado.

## <a name="templatestemplatesindexmd"></a>[Modelos](templates/index.md)

Modelos de controle fornecem a capacidade facilmente tema e re-tema páginas de aplicativo em tempo de execução, enquanto os modelos de dados fornecem a capacidade de definir a apresentação dos dados em controles com suporte.

## <a name="triggerstriggersmd"></a>[Gatilhos](triggers.md)

Atualize controles respondendo a eventos em XAML e alterações de propriedade.


## <a name="related-links"></a>Links relacionados

- [Introdução ao Xamarin.Forms](~/xamarin-forms/get-started/introduction-to-xamarin-forms.md)
