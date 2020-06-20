---
title: Xamarin.FormsShell
description: Este guia explica como usar o Xamarin.Forms shell, que reduz a complexidade dos Xamarin.Forms aplicativos, fornecendo os recursos fundamentais que a maioria dos aplicativos exige.
ms.prod: xamarin
ms.assetid: 85B322AA-808F-41B6-953A-5877264AE643
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/28/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 0acf00d85c2bfb823ec1cfba099179bb6743117c
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84138743"
---
# <a name="xamarinforms-shell"></a>Xamarin.FormsShell

## <a name="introduction"></a>[Introdução](introduction.md)

Xamarin.FormsO Shell reduz a complexidade do desenvolvimento de aplicativos móveis fornecendo os recursos fundamentais que a maioria dos aplicativos móveis exigem. Isso inclui uma experiência de usuário de navegação comum, um esquema de navegação baseado em URI e um manipulador de pesquisa integrado.

## <a name="create-a-xamarinforms-shell-applicationcreatemd"></a>[Criar um Xamarin.Forms aplicativo de Shell](create.md)

O processo de criação de um Xamarin.Forms aplicativo de Shell é criar um arquivo XAML que crie subclasses da `Shell` classe, defina a `MainPage` propriedade da classe do aplicativo `App` para o objeto de subclasse `Shell` e, em seguida, descreva a hierarquia visual do aplicativo na classe de subclasse `Shell` .

## <a name="flyout"></a>[Submenu](flyout.md)

O submenu é o menu raiz de um aplicativo Shell e é acessível por meio de um ícone ou passando o dedo na lateral da tela. O submenu consiste em um cabeçalho opcional, itens de submenu e itens de menu opcionais.

## <a name="tabs"></a>[Temas](tabs.md)

Depois de um submenu, o próximo nível de navegação em um aplicativo Shell é a barra de guias inferior. Como alternativa, o padrão de navegação de um aplicativo pode começar com as guias inferiores e sem o uso de um submenu. Em ambos os casos, quando uma guia inferior contiver mais de uma página, as páginas poderão ser navegadas pelas guias superiores.

## <a name="page-configuration"></a>[Configuração de página](configuration.md)

A `Shell` classe define as propriedades anexadas que podem ser usadas para configurar a aparência de páginas em Xamarin.Forms aplicativos Shell. Isso inclui a configuração das cores da página, a desabilitação da barra de navegação, a desabilitação da barra de guias e a exibição dos modos de exibição na barra de navegação.

## <a name="navigation"></a>[Navegação](navigation.md)

Os aplicativos do Shell podem usar um esquema de navegação baseado em URI que usa rotas para navegar para qualquer página no aplicativo sem precisar seguir uma hierarquia de navegação definida.

## <a name="search"></a>[Pesquisar](search.md)

Os aplicativos do Shell podem usar a funcionalidade de pesquisa integrada fornecida por uma caixa de pesquisa que pode ser adicionada à parte superior de cada página.

## <a name="lifecycle"></a>[Ciclo de vida](lifecycle.md)

Os aplicativos de shell respeitam o Xamarin.Forms ciclo de vida e um `Appearing` evento é gerado quando uma página está prestes a aparecer na tela e um `Disappearing` evento é gerado quando uma página está prestes a desaparecer da tela.

## <a name="custom-renderers"></a>[Renderizadores personalizados](customrenderers.md)

Os aplicativos do Shell são altamente personalizáveis pelas propriedades e pelos métodos que as várias classes de Shell expõem. No entanto, também será possível criar um renderizador personalizado do Shell quando personalizações específicas de plataforma mais sofisticadas forem necessárias.
