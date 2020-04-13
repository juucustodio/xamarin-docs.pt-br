---
title: Introdução ao Xamarin.Forms
description: O Shell do Xamarin.Forms fornece os recursos fundamentais necessários para a maioria dos aplicativos, incluindo uma experiência de navegação comum para o usuário, um esquema de navegação baseada em URI e um manipulador de pesquisa integrado.
ms.prod: xamarin
ms.assetid: 4604DCB5-83DA-458A-8B02-6508A740BE0E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2019
ms.openlocfilehash: cb2ae3afe9db86d4db603d499ef0e75e7cbbf552
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "76940400"
---
# <a name="xamarinforms-shell-introduction"></a>Introdução ao Xamarin.Forms

[![Baixar](~/media/shared/download.png) amostra Baixar a amostra](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

O Shell do Xamarin.Forms reduz a complexidade do desenvolvimento de aplicativos móveis, fornecendo os recursos fundamentais que a maioria dos aplicativos móveis exige, incluindo:

- Um único lugar para descrever a hierarquia visual do aplicativo.
- Uma experiência de navegação comum para o usuário.
- Um esquema de navegação baseada em URI que permite a navegação para qualquer página no aplicativo.
- Um manipulador de pesquisa integrado.

Além disso, o aplicativo Shell tem a vantagem de aumentar a velocidade de renderização e reduzir o consumo de memória.

> [!IMPORTANT]
> Os aplicativos existentes podem adotar o Shell e beneficiar-se imediatamente das melhorias de navegação, desempenho e extensibilidade.

## <a name="platform-support"></a>Suporte a plataforma

O Shell do Xamarin.Forms está totalmente disponível no iOS e no Android, mas apenas parcialmente disponível na UWP (Plataforma Universal do Windows). Além disso, o Shell é experimental no momento na UWP e só pode ser usado adicionando a linha de código a seguir à classe `App` em seu projeto UWP, antes de chamar `Forms.Init`:

```csharp
global::Xamarin.Forms.Forms.SetFlags("Shell_UWP_Experimental");
```

Para obter informações sobre como adicionar um projeto UWP a uma solução Xamarin.Forms, confira [Configurar projetos do Windows](~/xamarin-forms/platform/windows/installation/index.md).

## <a name="shell-navigation-experience"></a>Experiência de navegação do Shell

O Shell fornece uma experiência de navegação obstinada, com base em guias e submenus. O nível superior da navegação em um aplicativo do Shell será um submenu ou uma barra de guias inferior, dependendo dos requisitos de navegação do aplicativo. O exemplo a seguir mostra um aplicativo em que o nível superior da navegação é um submenu:

[![Captura de tela de um submenu do Shell no iOS e no Android](introduction-images/flyout.png "Submenu do Shell")](introduction-images/flyout-large.png#lightbox "Submenu do Shell")

Selecionar um item de submenu faz com que a guia inferior que representa o item seja selecionada e exibida:

[![Captura de tela de guias inferiores do Shell no iOS e no Android](introduction-images/monkeys.png "Guias inferiores do Shell")](introduction-images/monkeys-large.png#lightbox "Guias inferiores do Shell")

> [!NOTE]
> Quando o submenu está fechado, a barra de guias inferior pode ser considerada o nível principal da navegação no aplicativo.

Cada guia [`ContentPage`](xref:Xamarin.Forms.ContentPage)exibe um . No entanto, se uma guia inferior contiver mais de uma página, será possível navegar pelas páginas por meio da barra de guias superior:

[![Captura de tela de guias superiores do Shell no iOS e no Android](introduction-images/cats.png "Guias superiores do Shell")](introduction-images/cats-large.png#lightbox "Guias superiores do Shell")

Dentro de cada [`ContentPage`](xref:Xamarin.Forms.ContentPage) guia, outros objetos podem ser navegados para:

[![Captura de tela do painel de navegação do Shell no iOS e no Android](introduction-images/cat-details.png "Navegação do aplicativo do Shell")](introduction-images/cat-details-large.png#lightbox "Navegação do aplicativo do Shell")

## <a name="related-links"></a>Links relacionados

- [Xaminals (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
