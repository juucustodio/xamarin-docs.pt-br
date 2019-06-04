---
title: Shell do Xamarin.Forms
description: Este guia explica como usar o Shell do Xamarin.Forms, o que reduz a complexidade dos aplicativos Xamarin.Forms fornecendo os recursos fundamentais que a maioria dos aplicativos exige.
ms.prod: xamarin
ms.assetid: 85B322AA-808F-41B6-953A-5877264AE643
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/28/2019
ms.openlocfilehash: a988a99e20af76d071f55c4cd2c97b135ad077f8
ms.sourcegitcommit: 10b4ccbfcf182be940899c00fc0fecae1e199c5b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2019
ms.locfileid: "66252283"
---
# <a name="xamarinforms-shell"></a>Shell do Xamarin.Forms

## <a name="introductionintroductionmd"></a>[Introdução](introduction.md)

O Shell do Xamarin.Forms reduz a complexidade do desenvolvimento de aplicativos móveis, fornecendo os recursos fundamentais que a maioria dos aplicativos móveis exige. Isso inclui uma experiência de usuário de navegação comum, um esquema de navegação baseado em URI e um manipulador de pesquisa integrado.

## <a name="create-a-xamarinforms-shell-applicationcreatemd"></a>[Criar um Aplicativo do Shell do Xamarin.Forms](create.md)

O processo para a criação de um aplicativo do Shell do Xamarin.Forms é criar um arquivo XAML que seja uma subclasse da classe `Shell`, definir a propriedade `MainPage` da classe `App` do aplicativo para o objeto `Shell` na subclasse e descrever a hierarquia visual do aplicativo na classe `Shell` na subclasse.

## <a name="flyoutflyoutmd"></a>[Submenu](flyout.md)

O submenu é o menu raiz de um aplicativo Shell e é acessível por meio de um ícone ou passando o dedo na lateral da tela. O submenu consiste em um cabeçalho opcional, itens de submenu e itens de menu opcionais.

## <a name="tabstabsmd"></a>[Guias](tabs.md)

Depois de um submenu, o próximo nível de navegação em um aplicativo Shell é a barra de guias inferior. Como alternativa, o padrão de navegação de um aplicativo pode começar com as guias inferiores e sem o uso de um submenu. Em ambos os casos, quando uma guia inferior contiver mais de uma página, as páginas poderão ser navegadas pelas guias superiores.

## <a name="page-configurationconfigurationmd"></a>[Configuração de página](configuration.md)

A classe do `Shell` define as propriedades anexadas que podem ser usadas para configurar a aparência das páginas em aplicativos do Shell do Xamarin.Forms. Isso inclui a configuração das cores da página, a desabilitação da barra de navegação, a desabilitação da barra de guias e a exibição dos modos de exibição na barra de navegação.

## <a name="navigationnavigationmd"></a>[Navegação](navigation.md)

Os aplicativos do Shell podem usar um esquema de navegação baseado em URI que usa rotas para navegar para qualquer página no aplicativo sem precisar seguir uma hierarquia de navegação definida.

## <a name="searchsearchmd"></a>[Pesquisar](search.md)

Os aplicativos do Shell podem usar a funcionalidade de pesquisa integrada fornecida por uma caixa de pesquisa que pode ser adicionada à parte superior de cada página.

## <a name="custom-rendererscustomrenderersmd"></a>[Renderizadores personalizados](customrenderers.md)

Os aplicativos do Shell são altamente personalizáveis pelas propriedades e pelos métodos que as várias classes de Shell expõem. No entanto, também será possível criar um renderizador personalizado do Shell quando personalizações específicas de plataforma mais sofisticadas forem necessárias.
