---
title: Conceitos básicos de aplicativo do xamarin. Forms
description: Explorar os conceitos básicos do desenvolvimento de aplicativos xamarin. Forms, incluindo todos os os conceitos principais necessárias, por meio de toques finais, como a localização e acessibilidade.
ms.prod: xamarin
ms.assetid: 7B516BBC-F7E1-4387-9779-7754E2E69723
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/28/2017
ms.openlocfilehash: 515dbd2683619cfcfb7a6c8ecac6bc147265ef7d
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995606"
---
# <a name="xamarinforms-application-fundamentals"></a>Conceitos básicos de aplicativo do xamarin. Forms

## <a name="accessibilityaccessibilityindexmd"></a>[Acessibilidade](accessibility/index.md)

Dicas para incorporar recursos acessíveis (como dar suporte às ferramentas de leitura de tela) com xamarin. Forms.

## <a name="app-classapplication-classmd"></a>[Classe de aplicativo](application-class.md)

O `Application` classe é o ponto de partida para xamarin. Forms – todo aplicativo precisa implementar uma subclasse `App` para definir a página inicial. Ele também fornece o `Properties` coleta para armazenamento de dados simples. Ele pode ser definido em c# ou XAML.

## <a name="app-lifecycleapp-lifecyclemd"></a>[Ciclo de vida do aplicativo](app-lifecycle.md)

O `Application` classe `OnStart`, `OnSleep`, e `OnResume` métodos, bem como eventos de navegação modal, permitem que você manipule eventos de ciclo de vida do aplicativo com o código personalizado.

## <a name="behaviorsbehaviorsindexmd"></a>[Comportamentos](behaviors/index.md)

Controles de interface do usuário podem ser estendidos facilmente sem a criação de subclasses usando comportamentos para adicionar funcionalidade.

## <a name="custom-rendererscustom-rendererindexmd"></a>[Renderizadores personalizados](custom-renderer/index.md)

Renderizadores personalizados permitem que os desenvolvedores 'override' o processamento padrão de controles do xamarin. Forms para personalizar a aparência e comportamento em cada plataforma (usando SDKs nativos se desejado).

## <a name="data-bindingdata-bindingindexmd"></a>[Associação de dados](data-binding/index.md)

Associação de dados vincula as propriedades de dois objetos, permitindo que as alterações em uma propriedade sejam refletidas automaticamente na outra propriedade. Associação de dados é parte integrante do Model-View-ViewModel ([MVVM](~/xamarin-forms/enterprise-application-patterns/mvvm.md)) arquitetura do aplicativo.

## <a name="dependency-servicedependency-serviceindexmd"></a>[Serviço de dependência](dependency-service/index.md)

O `DependencyService` fornece um localizador de simple para que você pode codificar para interfaces em seu código compartilhado e fornecer implementações específicas à plataforma que são resolvidas automaticamente, tornando mais fácil fazer referência a funcionalidade específica da plataforma no xamarin. Forms.

## <a name="effectseffectsindexmd"></a>[Efeitos](effects/index.md)

O Effects permite que os controles nativos em cada plataforma sejam personalizados e são geralmente usados para estilo pequenas alterações.

## <a name="filesfilesmd"></a>[Arquivos](files.md)

Arquivo tratamento com xamarin. Forms pode ser obtido usando o código em uma biblioteca .NET Standard, ou usando recursos inseridos.

## <a name="gesturesgesturesindexmd"></a>[Gestos](gestures/index.md)

O xamarin. Forms [ `GestureRecognizer` ](xref:Xamarin.Forms.GestureRecognizer) classe dá suporte a toque, aperto e gestos de pan em controles de interface do usuário.

## <a name="localizationlocalizationindexmd"></a>[Localização](localization/index.md)

A estrutura de localização interna do .NET pode ser usada para compilar aplicativos multilíngues de plataforma cruzada com xamarin. Forms.

## <a name="local-databasesdatabasesmd"></a>[Bancos de dados locais](databases.md)

Xamarin. Forms dá suporte a aplicativos controlados por banco de dados usando o mecanismo de banco de dados SQLite, que torna possível carregar e salvar objetos no código compartilhado.

## <a name="messaging-centermessaging-centermd"></a>[Centro de mensagens](messaging-center.md)

Xamarin. Forms `MessagingCenter` permite exibir modelos e outros componentes para se comunicar com sem precisar saber nada sobre uns aos outros, além de um contrato de mensagem simple.

## <a name="navigationnavigationindexmd"></a>[Navegação](navigation/index.md)

Xamarin. Forms fornece uma série de experiências de navegação de página diferente, dependendo do `Page` digite que está sendo usado.

## <a name="templatestemplatesindexmd"></a>[Modelos](templates/index.md)

Modelos de controle fornecem a capacidade de tema com facilidade e re-theme páginas do aplicativo em tempo de execução, enquanto modelos de dados fornecem a capacidade de definir a apresentação dos dados em controles com suporte.

## <a name="triggerstriggersmd"></a>[Gatilhos](triggers.md)

Atualize controles respondendo a alterações de propriedade e eventos no XAML.


## <a name="related-links"></a>Links relacionados

- [Introdução ao Xamarin.Forms](~/xamarin-forms/get-started/introduction-to-xamarin-forms.md)
