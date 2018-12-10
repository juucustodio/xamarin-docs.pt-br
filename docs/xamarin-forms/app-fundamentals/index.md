---
title: Conceitos básicos de aplicativo Xamarin.Forms
description: Explorar os conceitos básicos do desenvolvimento de aplicativos Xamarin.Forms, incluindo todos os conceitos principais necessários e passando para os toques finais, como a localização e a acessibilidade.
ms.prod: xamarin
ms.assetid: 7B516BBC-F7E1-4387-9779-7754E2E69723
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/03/2018
ms.openlocfilehash: 015a7dfa3d55b411e6626ae2ac70fd2930661e65
ms.sourcegitcommit: 01f93a34b466f8d4043cef68fab9b35cd8decee6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/05/2018
ms.locfileid: "52898986"
---
# <a name="xamarinforms-application-fundamentals"></a>Conceitos básicos de aplicativo Xamarin.Forms

## <a name="accessibilityaccessibilityindexmd"></a>[Acessibilidade](accessibility/index.md)

Dicas para incorporar recursos acessíveis (como dar suporte às ferramentas de leitura de tela) com o Xamarin.Forms.

## <a name="app-classapplication-classmd"></a>[Classe de aplicativo](application-class.md)

A classe `Application` é o ponto de partida para o Xamarin.Forms – todo aplicativo precisa implementar uma subclasse `App` para definir a página inicial. Ela também fornece a coleta `Properties` para armazenamento de dados simples. Ela pode ser definida em C# ou em XAML.

## <a name="app-lifecycleapp-lifecyclemd"></a>[Ciclo de vida do aplicativo](app-lifecycle.md)

Os métodos `OnStart`, `OnSleep` e `OnResume` da classe `Application`, bem como eventos de navegação modal, permitem que você manipule eventos de ciclo de vida do aplicativo com código personalizado.

## <a name="behaviorsbehaviorsindexmd"></a>[Comportamentos](behaviors/index.md)

Controles de interface do usuário podem ser estendidos facilmente sem a criação de subclasses pelo uso de comportamentos para adicionar funcionalidade.

## <a name="custom-rendererscustom-rendererindexmd"></a>[Renderizadores personalizados](custom-renderer/index.md)

Renderizadores personalizados permitem que os desenvolvedores 'substituam' a renderização padrão de controles do Xamarin.Forms para personalizar sua aparência e comportamento em cada plataforma (usando SDKs nativos, se desejado).

## <a name="data-bindingdata-bindingindexmd"></a>[Associação de dados](data-binding/index.md)

A associação de dados associa as propriedades de dois objetos, permitindo que as alterações em uma propriedade sejam automaticamente refletidas na outra. A associação de dados é parte integrante da arquitetura do aplicativo [MVVM](~/xamarin-forms/enterprise-application-patterns/mvvm.md) (Model-View-ViewModel).

## <a name="dependency-servicedependency-serviceindexmd"></a>[Serviço de dependência](dependency-service/index.md)

O `DependencyService` fornece um localizador simples para que você possa codificar para interfaces em seu código compartilhado e fornecer implementações específicas a uma plataforma que são resolvidas automaticamente, tornando mais fácil fazer referência a funcionalidades específicas a uma plataforma no Xamarin.Forms.

## <a name="effectseffectsindexmd"></a>[Efeitos](effects/index.md)

Efeitos permitem que os controles nativos em cada plataforma sejam personalizados e são geralmente usados para pequenas alterações de estilo.

## <a name="filesfilesmd"></a>[Arquivos](files.md)

O tratamento de arquivos com o Xamarin.Forms pode ser alcançado usando código em uma biblioteca .NET Standard ou usando recursos inseridos.

## <a name="gesturesgesturesindexmd"></a>[Gestos](gestures/index.md)

A classe [`GestureRecognizer`](xref:Xamarin.Forms.GestureRecognizer) do Xamarin.Forms dá suporte a gestos de toque, pinçagem e panorâmica em controles da interface do usuário.

## <a name="localizationlocalizationindexmd"></a>[Localização](localization/index.md)

A estrutura de localização interna do .NET pode ser usada para criar aplicativos multilíngues multiplataforma com o Xamarin.Forms.

## <a name="local-databasesdatabasesmd"></a>[Bancos de dados locais](databases.md)

O Xamarin.Forms dá suporte a aplicativos controlados por banco de dados usando o mecanismo de banco de dados SQLite, que torna possível carregar e salvar objetos em código compartilhado.

## <a name="messaging-centermessaging-centermd"></a>[Centro de mensagens](messaging-center.md)

O `MessagingCenter` do Xamarin.Forms permite exibir modelos e outros componentes com os quais se comunicar sem que eles precisem saber nada uns sobre os outros, além de um contrato de mensagem simples.

## <a name="navigationnavigationindexmd"></a>[Navegação](navigation/index.md)

o Xamarin.Forms oferece uma série de experiências de navegação de página diferentes, dependendo do tipo do `Page` sendo usado.

## <a name="shellshellmd"></a>[Shell](shell.md)

O Shell do Xamarin.Forms é um contêiner para aplicativos, que fornece os recursos fundamentais da interface do usuário que a maioria dos aplicativos exige, permitindo que você se concentre na carga de trabalho principal do aplicativo.

## <a name="templatestemplatesindexmd"></a>[Modelos](templates/index.md)

Modelos de controle fornecem a capacidade de definir e redefinir temas de páginas de aplicativo em tempo de execução com facilidade, enquanto modelos de dados fornecem a capacidade de definir a apresentação de dados em controles compatíveis.

## <a name="triggerstriggersmd"></a>[Gatilhos](triggers.md)

Atualize os controles respondendo a alterações de propriedade e eventos no XAML.


## <a name="related-links"></a>Links relacionados

- [Introdução ao Xamarin.Forms](~/xamarin-forms/get-started/introduction-to-xamarin-forms.md)
