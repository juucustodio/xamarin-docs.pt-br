---
redirect_url: /xamarin/cross-platform/troubleshooting/questions/
title: Pode usar o Visual Studio 2017 Release Candidate com Xamarin?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 8E752F36-F73A-4EFC-9F82-4E18FDE1C9E2
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 4c93653793c7c8cce0d2257107097c89f73fc726
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2018
---
# <a name="can-i-use-visual-studio-2017-release-candidate-with-xamarin"></a>Pode usar o Visual Studio 2017 Release Candidate com Xamarin?

## <a name="can-i-use-visual-studio-2017-release-candidate-with-xamarin"></a>Pode usar o Visual Studio 2017 Release Candidate com Xamarin?

Sim. É possível aproveitar Xamarin por meio do Visual Studio 2017 Release Candidate. No entanto, observe que, no momento, instalar o Xamarin no Visual Studio 2017 RC irá desinstalar as versões anteriores do Xamarin instalado no Visual Studio 2015/2013. Xamarin para 2017 do Visual Studio não pode ser instalado ao mesmo tempo como Xamarin para Visual Studio 2015/2013 como resultado do Visual Studio de 2017 migrar de empacotamento MSI para utilização do sistema de instalação do Visual Studio.

Enquanto a equipe atualmente está observando maneiras para ignorar esse comportamento esperado, aconselhamos que os usuários escolham seu ambiente de desenvolvimento com base em suas necessidades. 

> [!IMPORTANT]
> Se você estiver criando projetos do xamarin, verifique se que o Visual Studio para Mac em seu sistema Mac emparelhado é a mesma versão do xamarin canal.

## <a name="how-do-i-install-xamarin-to-visual-studio-2017-release-candidate"></a>Como instalar o Xamarin para Visual Studio 2017 Release Candidate?

### <a name="installing-during-the-visual-studio-2017-rc-installer"></a>Instalar durante o instalador de RC do Visual Studio de 2017

* Selecione o **Xamarin** componente como parte do novo **instalador do Visual Studio**

  [![](visualstudio-2017-rc-images/install1-sml.png "Tela de instalador RC do Visual Studio de 2017")](visualstudio-2017-rc-images/install1-orig.png#lightbox)

Isso irá instalar a extensão do Visual Studio para desenvolvimento do xamarin e xamarin.

### <a name="installing-or-reinstalling-xamarin-in-an-existing-installation-of-visual-studio-2017-rc"></a>Instalar ou reinstalar Xamarin em uma instalação existente do Visual Studio 2017 RC

#### <a name="using-the-visual-studio-installer"></a>Usando o instalador do Visual Studio:

1. Procure o aplicativo instalador do Visual Studio

  [![](visualstudio-2017-rc-images/reinstall1-sml.png "Resultados da pesquisa para o aplicativo de instalador do Visual Studio")](visualstudio-2017-rc-images/reinstall1-orig.png#lightbox)

2. Selecione: um. **Desenvolvimento móvel com o .NET (visualização)** na guia de cargas de trabalho, ou

  [![](visualstudio-2017-rc-images/reinstall2-sml.png "Guia de cargas de trabalho de instalador do VS") ](visualstudio-2017-rc-images/reinstall2-orig.png#lightbox) b. **Xamarin** no **componentes individuais** guia

  [![](visualstudio-2017-rc-images/reinstall3-sml.png "Guia de componentes de instalação do VS")](visualstudio-2017-rc-images/reinstall3-orig.png#lightbox)

#### <a name="using-the-visual-studio-installer-within-visual-studio"></a>Usando o instalador do Visual Studio no Visual Studio:
1. Navegue até a página de início do Visual Studio de 2017
2. Clique em **mais modelos de projeto** sob o **novo projeto** seção

    [![](visualstudio-2017-rc-images/reinstall4-sml.png "Página inicial do Visual Studio")](visualstudio-2017-rc-images/reinstall4-orig.png#lightbox)
3. Clique em `Open Visual Studio Installer` no painel esquerdo

    [![](visualstudio-2017-rc-images/reinstall5-sml.png "Nova tela de projeto")](visualstudio-2017-rc-images/reinstall5-orig.png#lightbox)
4. Selecione:
    
    a. **Desenvolvimento móvel com o .NET (visualização)** na guia de cargas de trabalho, ou

    [![](visualstudio-2017-rc-images/reinstall2-sml.png "Guia de cargas de trabalho de instalador do VS") ](visualstudio-2017-rc-images/reinstall2-orig.png#lightbox) b. **Xamarin** no **componentes individuais** guia

    [![](visualstudio-2017-rc-images/reinstall3-sml.png "Guia de componentes de instalação do VS")](visualstudio-2017-rc-images/reinstall3-orig.png#lightbox)
