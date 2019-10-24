---
title: Introdução ao Xamarin.Forms
description: O Shell do Xamarin.Forms fornece os recursos fundamentais necessários para a maioria dos aplicativos, incluindo uma experiência de navegação comum para o usuário, um esquema de navegação baseada em URI e um manipulador de pesquisa integrado.
ms.prod: xamarin
ms.assetid: 4604DCB5-83DA-458A-8B02-6508A740BE0E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2019
ms.openlocfilehash: 3e63a580bbdb1c220d44b100725cdc8ce387b405
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72696513"
---
# <a name="xamarinforms-shell-introduction"></a>Introdução ao Xamarin.Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

O Shell do Xamarin.Forms reduz a complexidade do desenvolvimento de aplicativos móveis, fornecendo os recursos fundamentais que a maioria dos aplicativos móveis exige, incluindo:

- Um único lugar para descrever a hierarquia visual do aplicativo.
- Uma experiência de navegação comum para o usuário.
- Um esquema de navegação baseada em URI que permite a navegação para qualquer página no aplicativo.
- Um manipulador de pesquisa integrado.

Além disso, o aplicativo Shell tem a vantagem de aumentar a velocidade de renderização e reduzir o consumo de memória.

> [!IMPORTANT]
> Os aplicativos existentes podem adotar o Shell e beneficiar-se imediatamente dos aprimoramentos de navegação, desempenho e extensibilidade.

## <a name="platform-support"></a>Suporte de plataforma

O Shell do Xamarin. Forms está totalmente disponível no iOS e no Android, mas apenas parcialmente disponível no Plataforma Universal do Windows (UWP). Além disso, o Shell é experimental no momento em UWP e só pode ser usado adicionando a linha de código a seguir à classe `App` em seu projeto UWP, antes de chamar `Forms.Init`:

```csharp
global::Xamarin.Forms.Forms.SetFlags("Shell_UWP_Experimental");
```

## <a name="shell-navigation-experience"></a>Experiência de navegação do Shell

O Shell fornece uma experiência de navegação obstinada, com base em guias e submenus. O nível superior da navegação em um aplicativo do Shell será um submenu ou uma barra de guias inferior, dependendo dos requisitos de navegação do aplicativo. O exemplo a seguir mostra um aplicativo em que o nível superior da navegação é um submenu:

[![Captura de tela de um submenu do Shell, no iOS e no Android](introduction-images/flyout.png "Submenu do Shell")](introduction-images/flyout-large.png#lightbox "Submenu do Shell")

Selecionar um item de submenu faz com que a guia inferior que representa o item seja selecionada e exibida:

[![Captura de tela das guias inferiores do Shell, no iOS e no Android](introduction-images/monkeys.png "Guias inferiores do Shell")](introduction-images/monkeys-large.png#lightbox "Guias inferiores do Shell")

> [!NOTE]
> Quando o submenu está fechado, a barra de guias inferior pode ser considerada o nível principal da navegação no aplicativo.

Cada guia exibe um [`ContentPage`](xref:Xamarin.Forms.ContentPage). No entanto, se uma guia inferior contiver mais de uma página, será possível navegar pelas páginas por meio da barra de guias superior:

[![Captura de tela das guias superiores do Shell, no iOS e no Android](introduction-images/cats.png "Guias superiores do Shell")](introduction-images/cats-large.png#lightbox "Guias superiores do Shell")

Dentro de cada guia, é possível navegar para objetos [`ContentPage`](xref:Xamarin.Forms.ContentPage) adicionais:

[![Captura de tela da navegação de página do Shell, no iOS e no Android](introduction-images/cat-details.png "Navegação de aplicativo de Shell")](introduction-images/cat-details-large.png#lightbox "Navegação de aplicativo de Shell")

## <a name="related-links"></a>Links relacionados

- [Xaminals (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
