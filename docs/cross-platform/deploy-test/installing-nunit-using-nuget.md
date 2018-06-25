---
title: Instalação do NUnit 2.6.4 por meio do NuGet
description: Este documento descreve como fazer downgrade do NUnit 3.0 para o NUnit 2.6.4 usando o NuGet. Isso é necessário ao trabalhar com Xamarin.UITest, que não é compatível com o NUnit 3.x.
ms.prod: xamarin
ms.assetid: 7683F2B8-7FDF-48C4-8E7D-649D4D4E79F0
author: asb3993
ms.author: amburns
ms.date: 03/24/2017
ms.openlocfilehash: fb0ffd87a61f20744e68eafdd7eb8de691df6f75
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34781514"
---
# <a name="installing-nunit-264-using-nuget"></a>Instalação do NUnit 2.6.4 por meio do NuGet

_Este guia descreve como fazer o downgrade do NUnit 3.0 para o NUnit 2.6.4 usando o NuGet._

Os desenvolvedores que estão escrevendo testes no Visual Studio para Mac ou usando Xamarin.UITest devem usar o [NUnit 2.6.4](http://nunit.org/index.php?p=docHome&r=2.6.4), pois o NUnit 3.0 ou superior não é compatível com o Visual Studio para Mac ou o Xamarin.UITest. A tentativa de executar testes de unidade no Visual Studio para Mac ou no Xamarin.UITests com o NUnit 3.0 falhará.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Este guia abordará como instalar o NUnit 2.6.4 usando o NuGet para Visual Studio para Mac. Estas etapas também desinstalarão o NUnit 3.0, se necessário.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Este guia explicará como fazer o downgrade do NUnit 3.0 para o NUnit 2.6.4 usando o NuGet no Visual Studio 2015.

-----

## <a name="requirements"></a>Requisitos

Este guia pressupõe que haja uma solução com um projeto de aplicativo móvel e um projeto de teste.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

## <a name="installing-nunit-264-in-visual-studio-for-mac"></a>Instalação do NUnit 2.6.4 no Visual Studio para Mac

As etapas a seguir descrevem como instalar o NUnit 2.6.4.


1. **Abrir o Gerenciador de Pacotes** – Clique com o botão direito do mouse em **Pacotes** e selecione **Adicionar Pacotes** no menu pop-up:

    [![](installing-nunit-using-nuget-images/add-packages-xs.png "Clique com o botão direito do mouse em Pacotes e selecione Adicionar Pacotes no menu pop-up")](installing-nunit-using-nuget-images/add-packages-xs.png#lightbox)
    
1. **Pesquisar `NUnit version:2.6.4`** – O Visual Studio para Mac desinstalará o NUnit 3.0 (se necessário) e baixará e instalará o NUnit 2.6.4. No diálogo **Adicionar Pacotes**, insira o texto `nunit version:2.6.4` no campo **Pesquisar**, localizado no canto superior direito. Selecione **NUnit** nos resultados da pesquisa e clique no botão **Adicionar Pacote**:

    [![](installing-nunit-using-nuget-images/nunit-search-xs.png "Selecione NUnit nos resultados da pesquisa e clique no botão Adicionar Pacote")](installing-nunit-using-nuget-images/nunit-search-xs.png#lightbox)


Para confirmar se o NUnit 2.6.4 foi instalado, inspecione o número da versão do pacote NUnit no Painel de Solução:

[![](installing-nunit-using-nuget-images/nunit-2-6-4-installed.png "Inspecione o número de versão do pacote do NUnit no Painel de Soluções")](installing-nunit-using-nuget-images/nunit-2-6-4-installed.png#lightbox)

## <a name="summary"></a>Resumo

Este guia abordou como fazer o downgrade do NUnit 3.0 para o NUnit 2.6.4 no Visual Studio para Mac usando o Console do Gerenciador de Pacotes.


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

## <a name="installing-nunit-264-in-visual-studio"></a>Instalação do NUnit 2.6.4 no Visual Studio

Esta seção abordará o uso do _Console do Gerenciador de Pacotes NuGet_ no Visual Studio 2015 para desinstalar o NUnit 3.0 e instalar o NUnit 2.6.4.


1. **Iniciar o Console do Gerenciador de Pacotes NuGet** – Selecione **Ferramentas > Gerenciador de Pacotes NuGet > Console do Gerenciador de Pacotes**:

    [![](installing-nunit-using-nuget-images/package-manager-console.png "Iniciar o Console do Gerenciador de Pacotes NuGet – Selecione Ferramentas  Gerenciador de Pacotes NuGet  Console do Gerenciador de Pacotes")](installing-nunit-using-nuget-images/package-manager-console.png#lightbox)
    
1. **Verificar a versão do NUnit** – Você pode verificar opcionalmente a versão do NUnit que está instalada, executando o comando `Get-Package -Project <UITEST PROJECT>`:

    ```bash
    [PM] Get-Package -Project [TEST PROJECT NAME]
    
        Id                                  Versions                                 ProjectName
        --                                  --------                                 -----------
    NUnit                               {3.0.1}                                  [TEST PROJECT NAME]
    Xamarin.UITest                      {1.2.0}                                  [TEST PROJECT NAME]
    ```

Se a versão for o NUnit 3.0 ou superior, você deverá fazer o downgrade para o NUnit 2.6.4.

1. **Desinstalar o NUnit 3.0** – Use o commandlet `Uninstall-Package` para desinstalar o NUnit 3.0:

        <PM> Uninstall-Package NUnit -Project <TEST PROJECT NAME>
        Attempting to gather dependencies information for package 'NUnit.3.0.1' with respect to project '<TEST PROJECT NAME>', targeting '.NETFramework,Version=v4.5'
        Resolving actions to uninstall package 'NUnit.3.0.1'
        Resolved actions to uninstall package 'NUnit.3.0.1'
        Removed package 'NUnit.3.0.1' from 'packages.config'
        Successfully uninstalled 'NUnit.3.0.1' from <TEST PROJECT NAME>

1. **Instalar o NUnit 2.6.4** – Instale o Nunit 2.6.4 com o commandlet `Install-Package`, como demonstrado no trecho a seguir:

        <PM> Install-Package NUnit -Version 2.6.4 -Project <TEST PROJECT NAME>
        Attempting to gather dependencies information for package 'NUnit.2.6.4' with respect to project '<TEST PROJECT NAME>', targeting '.NETFramework,Version=v4.5'
        Attempting to resolve dependencies for package 'NUnit.2.6.4' with DependencyBehavior 'Lowest'
        Resolving actions to install package 'NUnit.2.6.4'
        Resolved actions to install package 'NUnit.2.6.4'
        Adding package 'NUnit.2.6.4' to folder 'Z:\Desktop\DowngradeNunit\packages'
        Added package 'NUnit.2.6.4' to folder 'Z:\Desktop\DowngradeNunit\packages'
        Added package 'NUnit.2.6.4' to 'packages.config'
        Successfully installed 'NUnit 2.6.4' to <TEST PROJECT NAME>
    
## <a name="summary"></a>Resumo

Este guia abordou como fazer o downgrade do NUnit 3.0 para o NUnit 2.6.4 no Visual Studio 2015 usando o Console do Gerenciador de Pacotes.

-----

## <a name="related-links"></a>Links relacionados

- [NUnit 2.6.4](http://nunit.org/index.php?p=docHome&r=2.6.4)
- [Pacote NuGet NUnit 2.6.4](https://www.nuget.org/packages/NUnit/2.6.4)
