---
title: Xamarin.Forms Introdução ao shell
description: Xamarin.Forms O Shell fornece os recursos fundamentais que a maioria dos aplicativos exigem, incluindo uma experiência de usuário de navegação comum, um esquema de navegação baseado em URI e um manipulador de pesquisa integrado.
ms.prod: xamarin
ms.assetid: 4604DCB5-83DA-458A-8B02-6508A740BE0E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 61e48328aea01347678540cde5141a29387e92f5
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563166"
---
# <a name="no-locxamarinforms-shell-introduction"></a>Xamarin.Forms Introdução ao shell

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

Xamarin.Forms O Shell reduz a complexidade do desenvolvimento de aplicativos móveis fornecendo os recursos fundamentais que a maioria dos aplicativos móveis exigem, incluindo:

- Um único lugar para descrever a hierarquia visual do aplicativo.
- Uma experiência de navegação comum para o usuário.
- Um esquema de navegação baseada em URI que permite a navegação para qualquer página no aplicativo.
- Um manipulador de pesquisa integrado.

Além disso, o aplicativo Shell tem a vantagem de aumentar a velocidade de renderização e reduzir o consumo de memória.

> [!IMPORTANT]
> Os aplicativos existentes podem adotar o Shell e beneficiar-se imediatamente das melhorias de navegação, desempenho e extensibilidade.

## <a name="platform-support"></a>Suporte a plataforma

Xamarin.Forms O Shell está totalmente disponível no iOS e no Android, mas apenas parcialmente disponível no Plataforma Universal do Windows (UWP). Além disso, o Shell é experimental no momento na UWP e só pode ser usado adicionando a linha de código a seguir à classe `App` em seu projeto UWP, antes de chamar `Forms.Init`:

```csharp
global::Xamarin.Forms.Forms.SetFlags("Shell_UWP_Experimental");
```

Para obter informações sobre como adicionar um projeto UWP a uma Xamarin.Forms solução, consulte [Configurar projetos do Windows](~/xamarin-forms/platform/windows/installation/index.md).

## <a name="shell-navigation-experience"></a>Experiência de navegação do Shell

O Shell fornece uma experiência de navegação obstinada, com base em guias e submenus. O nível superior da navegação em um aplicativo do Shell será um submenu ou uma barra de guias inferior, dependendo dos requisitos de navegação do aplicativo. O exemplo a seguir mostra um aplicativo em que o nível superior da navegação é um submenu:

[![Captura de tela de um submenu do Shell no iOS e no Android](introduction-images/flyout.png "Submenu do Shell")](introduction-images/flyout-large.png#lightbox "Submenu do Shell")

Selecionar um item de submenu faz com que a guia inferior que representa o item seja selecionada e exibida:

[![Captura de tela de guias inferiores do Shell no iOS e no Android](introduction-images/monkeys.png "Guias inferiores do Shell")](introduction-images/monkeys-large.png#lightbox "Guias inferiores do Shell")

> [!NOTE]
> Quando o submenu está fechado, a barra de guias inferior pode ser considerada o nível principal da navegação no aplicativo.

Cada guia exibe um [`ContentPage`](xref:Xamarin.Forms.ContentPage) . No entanto, se uma guia inferior contiver mais de uma página, será possível navegar pelas páginas por meio da barra de guias superior:

[![Captura de tela de guias superiores do Shell no iOS e no Android](introduction-images/cats.png "Guias superiores do Shell")](introduction-images/cats-large.png#lightbox "Guias superiores do Shell")

Dentro de cada guia, os [`ContentPage`](xref:Xamarin.Forms.ContentPage) objetos adicionais podem ser navegados para:

[![Captura de tela do painel de navegação do Shell no iOS e no Android](introduction-images/cat-details.png "Navegação do aplicativo do Shell")](introduction-images/cat-details-large.png#lightbox "Navegação do aplicativo do Shell")

## <a name="related-links"></a>Links relacionados

- [Xaminals (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)