---
title: Recursos de plataforma do Windows
ms.topic: article
ms.prod: xamarin
ms.assetid: F6EA9E49-FB3E-442F-AF13-B7AD0C80D11F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/20/2017
ms.openlocfilehash: 4385534a6e2ecfc9c908648fa267a543c2313ce0
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="windows-platform-features"></a>Recursos de plataforma do Windows

Desenvolvendo aplicativos xamarin. Forms plataformas do Windows requer o Visual Studio. O [página requisitos](~/xamarin-forms/get-started/installation.md) contém mais informações sobre os pré-requisitos.

![](images/allhanselman.png "Xamarin. Forms aplicativos em execução no Windows")

## <a name="platform-support"></a>Suporte de plataforma

Por padrão, os modelos de xamarin. Forms disponíveis no Visual Studio contêm um projeto do Windows:

* **Aplicativos de plataforma universal do Windows** -xamarin. Forms aplicativos também podem ser otimizados para Windows 10. Universais aplicativos (UWP) podem executar no telefone, tablet e dispositivos de área de trabalho.

Se você tiver instalado as opções corretas de desenvolvimento no Visual Studio, também é possível [adicionar](installation/index.md) esses tipos para dar suporte a versões mais antigas do Windows de projeto:

* **Windows 8.1** - você pode implantar aplicativos xamarin. Forms para tablets e fatores forma da área de trabalho como um aplicativo do Windows 8.1 projeto usando controles WinRT.
* **Windows Phone 8.1** -xamarin. Forms oferece suporte para a plataforma Windows Phone 8.1 usando WinRT. A aparência de aplicativos usando o suporte do Windows Phone 8.1 podem ser diferente para seus aplicativos do Windows Phone xamarin. Forms anteriores que foram baseados no Silverlight.


> [!NOTE]
> **Observação:** suporte de xamarin. Forms 1. x e 2. x _Windows Phone 8 Silverlight_ desenvolvimento de aplicativos, porém este tipo de projeto foi preterido.


## <a name="getting-started"></a>Guia de Introdução

Vá para **arquivo > Novo > projeto** no Visual Studio e escolha uma da **entre plataformas > aplicativo em branco (xamarin. Forms)** modelos para começar.

Soluções antigas do xamarin. Forms, ou aqueles criados em macOS, não terá todos os projetos do Windows listados acima (mas eles precisam ser adicionados manualmente).
Se você deseja direcionar a plataforma do Windows não estiver em sua solução, visite o [instruções de instalação](installation/index.md) para adicionar os Windows desejados projeto tipo/s.


## <a name="samples"></a>Exemplos

[Todos os exemplos](https://github.com/xamarin/xamarin-forms-book-preview-2) para o catálogo de Charles Petzold [ *criação de aplicativos móveis com o xamarin. Forms* ](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md) incluem Windows Phone 8.1, Windows 8.1 e projetos de plataforma Universal do Windows (para Windows 10).

O ["Scott Hanselman" aplicativo de demonstração](https://github.com/jamesmontemagno/Hanselman.Forms) está disponível separadamente e também inclui projetos Apple Watch e desgaste Android (usando xamarin e xamarin, respectivamente, xamarin. Forms não executar nessas plataformas).


## <a name="related-links"></a>Links relacionados

- [Projetos de instalação do Windows](~/xamarin-forms/platform/windows/installation/index.md)
