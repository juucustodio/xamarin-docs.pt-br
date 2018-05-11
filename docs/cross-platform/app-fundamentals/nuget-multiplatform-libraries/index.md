---
title: Projetos do NuGet (Nugetizer 3000)
description: Crie automaticamente os pacotes do NuGet para compartilhar código entre plataformas, usando o 'Nugetizer 3000'!
ms.prod: xamarin
ms.assetid: F0A5A9BB-86CD-44C9-8EE8-74D1E5E74A30
author: asb3993
ms.author: amburns
ms.date: 11/22/2017
ms.openlocfilehash: 60c78385d0ffd2fbbdd7af5a4ef4af63e30feb9f
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2018
---
# <a name="nuget-projects-nugetizer-3000"></a>Projetos do NuGet (Nugetizer 3000)

_Crie automaticamente os pacotes do NuGet para compartilhar código entre plataformas, usando o 'Nugetizer 3000'!_

É possível criar automaticamente os pacotes do NuGet para compartilhar código entre plataformas, utilizando o _Nugetizer 3000_. Isso faz com que é possível criar pacotes do NuGet de projetos de biblioteca existente ou criando um novo **o projeto de biblioteca Multiplatform**.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

A 3000 Nugetizer é incluído com o Visual Studio para Mac 6.2.

[![](images/mulitplatform-library-sml.png "Criar nova janela Multiplatform biblioteca")](images/mulitplatform-library.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Para usar a 3000 Nugetizer no Visual Studio, veja [Baixe e execute o instalador do VSIX](http://bit.ly/nugetizer-2017).

-----

## <a name="building-nuget-packages"></a>Pacotes do NuGet construção

Uma vez configurado, cada build do projeto gera um pacote NuGet concluído, o que pode ser usado para compartilhar código internamente com outros aplicativos ou carregado [NuGet.org](https://www.nuget.org).

Há três cenários para usar esse recurso:

- [Projetos de biblioteca existentes](existing-library.md)

  Crie um pacote de NuGet de projetos existentes de PCL (ou .NET padrão).

- [Criar um novo projeto de biblioteca em várias plataformas](single-codebase.md)

  Crie uma nova biblioteca para compartilhar código comum por meio do NuGet, usando um PCL ou .NET padrão.

- [Criar novos projetos de biblioteca específica de plataforma](platform-specific.md)

  Crie uma nova biblioteca e NuGet que inclui o código específico de plataforma iOS e Android e usa um projeto compartilhado para conter o código comum e projetos específicos de plataforma para oferecer suporte a funcionalidades específicas de iOS ou Android.

Consulte o [metadados guia](metadata.md) para obter detalhes sobre os metadados necessários e opcionais que devem ser adicionados a qualquer pacote do NuGet.


## <a name="further-nuget-information"></a>Informações adicionais do NuGet

Leia mais sobre [criação manual de NuGets para Xamarin](~/cross-platform/app-fundamentals/nuget-manual.md) e como [incluem um pacote do NuGet em um aplicativo](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough).

Da Microsoft [NuGet documentação](https://docs.microsoft.com/nuget/) contém as informações mais detalhadas sobre o **nupkg** formato e o uso de pacotes do NuGet no Visual Studio.

A discussão de design para projetos de pacote do NuGet (também conhecido como NuGetizer 3000) está disponível na [repositório NuGet GitHub](https://github.com/NuGet/Home/wiki/NuGetizer-3000).


## <a name="related-links"></a>Links relacionados

- [Casos de uso NuGetizer 3000](https://github.com/NuGet/Home/wiki/NuGetizer-Core-Scenarios)
- [Criar manualmente os pacotes do NuGet para Xamarin](~/cross-platform/app-fundamentals/nuget-manual.md)
- [Documentação do NuGet](https://docs.microsoft.com/nuget/)
- [Notas de Versão](https://developer.xamarin.com/releases/studio/xamarin.studio_6.2/xamarin.studio_6.2/#NuGetizer_3000)
