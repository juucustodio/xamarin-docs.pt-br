---
title: Introdução ao Xamarin.Forms
description: O Shell do Xamarin.Forms fornece os recursos fundamentais necessários para a maioria dos aplicativos, incluindo uma experiência de navegação comum para o usuário, um esquema de navegação baseada em URI e um manipulador de pesquisa integrado.
ms.prod: xamarin
ms.assetid: 4604DCB5-83DA-458A-8B02-6508A740BE0E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/24/2019
ms.openlocfilehash: 38553b3b30388bc64fd97a7ac96a671279d20bc5
ms.sourcegitcommit: b986460787677cf8c2fc7cc8c03f4bc60c592120
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/24/2019
ms.locfileid: "66213337"
---
# <a name="xamarinforms-shell"></a>Shell do Xamarin.Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/Xaminals/)

O Shell do Xamarin.Forms reduz a complexidade do desenvolvimento de aplicativos móveis, fornecendo os recursos fundamentais que a maioria dos aplicativos móveis exige, incluindo:

- Um único lugar para descrever a hierarquia visual do aplicativo.
- Uma experiência de navegação comum para o usuário.
- Um esquema de navegação baseada em URI que permite a navegação para qualquer página no aplicativo.
- Um manipulador de pesquisa integrado.

Além disso, o aplicativo Shell tem a vantagem de aumentar a velocidade de renderização e reduzir o consumo de memória.

> [!IMPORTANT]
> O Shell do Xamarin.Forms só está disponível no iOS e no Android. Os aplicativos iOS e Android existentes podem adotar o Shell e beneficiar-se imediatamente das melhorias de navegação, desempenho e extensibilidade.

## <a name="shell-navigation-experience"></a>Experiência de navegação do Shell

O Shell fornece uma experiência de navegação obstinada, com base em guias e submenus. O nível superior da navegação em um aplicativo do Shell será um submenu ou uma barra de guias inferior, dependendo dos requisitos de navegação do aplicativo. O exemplo a seguir mostra um aplicativo em que o nível superior da navegação é um submenu:

[![Captura de tela de um submenu do Shell no iOS e no Android](introduction-images/flyout.png "Submenu Shell")](introduction-images/flyout-large.png#lightbox "Submenu Shell")

Selecionar um item de submenu faz com que a guia inferior que representa o item seja selecionada e exibida:

[![Captura de tela das guias inferiores no Shell no iOS e no Android](introduction-images/monkeys.png "Guias inferiores do Shell")](introduction-images/monkeys-large.png#lightbox "Guias inferiores do Shell")

> [!NOTE]
> Quando o submenu está fechado, a barra de guias inferior pode ser considerada o nível principal da navegação no aplicativo.

Cada guia exibe um [`ContentPage`](xref:Xamarin.Forms.ContentPage). No entanto, se uma guia inferior contiver mais de uma página, será possível navegar pelas páginas por meio da barra de guias superior:

[![Captura de tela das guias superiores no Shell no iOS e Android](introduction-images/cats.png "Guias superiores no Shell")](introduction-images/cats-large.png#lightbox "Guias superiores no Shell")

Dentro de cada guia, é possível navegar para objetos [`ContentPage`](xref:Xamarin.Forms.ContentPage) adicionais:

[![Captura de tela da navegação de página no Shell no iOS e Android](introduction-images/cat-details.png "Navegação no aplicativo Shell")](introduction-images/cat-details-large.png#lightbox "Navegação no aplicativo Shell")

## <a name="related-links"></a>Links relacionados

- [Xaminals (exemplo)](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/Xaminals/)
