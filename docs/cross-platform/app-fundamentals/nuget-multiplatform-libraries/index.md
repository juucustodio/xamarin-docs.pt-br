---
title: Projetos de biblioteca multiplataforma do NuGet (também conhecido como Nugetizer 3000)
description: Este documento descreve como usar a ferramenta Nugetizer 3000 para automaticamente criar pacotes do NuGet para compartilhar código entre plataformas.
ms.prod: xamarin
ms.assetid: F0A5A9BB-86CD-44C9-8EE8-74D1E5E74A30
author: conceptdev
ms.author: crdun
ms.date: 07/25/2018
ms.openlocfilehash: 1d48bc28aa4477361ca8057fda91ee3258f36a73
ms.sourcegitcommit: 46bb04016d3c35d91ff434b38474e0cb8197961b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2018
ms.locfileid: "39270422"
---
# <a name="nuget-multiplatform-library-projects-nugetizer-3000"></a>Projetos de biblioteca multiplataforma do NuGet (Nugetizer 3000)

_Crie automaticamente os pacotes do NuGet para compartilhar código entre plataformas usando 'Nugetizer 3000'!_

É possível criar automaticamente os pacotes do NuGet para compartilhar código entre plataformas usando o _Nugetizer 3000_. Isso faz com que seja possível criar pacotes do NuGet de projetos de biblioteca existente ou criando um novo **projeto de biblioteca multiplataforma**.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Nugetizer 3000 está incluído no Visual Studio para Mac &ndash; procure o **biblioteca > biblioteca Mulitplatform** tipo no projeto de **arquivo > novo** janela:

[![](images/mulitplatform-library-sml.png "Criar nova janela de biblioteca multiplataforma")](images/mulitplatform-library.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Para usar o Nugetizer 3000 no Visual Studio, por favor [Baixe e execute o instalador do VSIX](http://bit.ly/nugetizer-2017).

-----

## <a name="building-nuget-packages"></a>Compilando pacotes do NuGet

Uma vez configurado, cada build do projeto gera um pacote completo do NuGet, que pode ser usado para compartilhar código internamente com outros aplicativos ou carregado [NuGet.org](https://www.nuget.org).

Há três cenários para usar esse recurso:

- [Projetos de biblioteca existentes](existing-library.md)

  Crie um pacote NuGet de projetos existentes de PCL (ou .NET Standard).

- [Criar um novo projeto de biblioteca multiplataforma](single-codebase.md)

  Crie uma nova biblioteca para compartilhar código comum por meio do NuGet, usando uma PCL ou .NET Standard.

- [Criação de novos projetos de biblioteca específica da plataforma](platform-specific.md)

  Crie uma nova biblioteca e o NuGet que inclui o código específico da plataforma para iOS e Android e, em seguida, usa um projeto compartilhado que contém o código comum e projetos específicos da plataforma para dar suporte a funcionalidades específicas de iOS ou Android.

Consulte a [guia de metadados](metadata.md) para obter detalhes sobre os metadados necessários e opcionais que devem ser adicionados a qualquer pacote do NuGet.

## <a name="further-nuget-information"></a>Informações adicionais do NuGet

Leia mais sobre [criação manual de NuGets para Xamarin](~/cross-platform/app-fundamentals/nuget-manual.md) e como [incluem um pacote do NuGet em um aplicativo](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough).

Da Microsoft [documentação do NuGet](https://docs.microsoft.com/nuget/) contém informações mais detalhadas sobre o **. nupkg** formato e o uso de pacotes NuGet no Visual Studio.

A discussão de design para projetos de pacote do NuGet (também conhecido como NuGetizer 3000) está disponível na [repositório GitHub do NuGet](https://github.com/NuGet/Home/wiki/NuGetizer-3000).

## <a name="related-links"></a>Links relacionados

- [Casos de uso NuGetizer 3000](https://github.com/NuGet/Home/wiki/NuGetizer-Core-Scenarios)
- [Criar manualmente os pacotes do NuGet para Xamarin](~/cross-platform/app-fundamentals/nuget-manual.md)
- [Documentação do NuGet](https://docs.microsoft.com/nuget/)
- [Notas de Versão](https://developer.xamarin.com/releases/studio/xamarin.studio_6.2/xamarin.studio_6.2/#NuGetizer_3000)
