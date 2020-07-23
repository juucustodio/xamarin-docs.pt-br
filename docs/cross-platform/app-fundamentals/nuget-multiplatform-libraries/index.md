---
title: Projetos de biblioteca multiplataforma do NuGet (também conhecido como Nugetizer 3000)
description: Este documento descreve como usar a ferramenta Nugetizer 3000 para criar automaticamente pacotes NuGet para compartilhar código entre plataformas.
ms.prod: xamarin
ms.assetid: F0A5A9BB-86CD-44C9-8EE8-74D1E5E74A30
author: davidortinau
ms.author: daortin
ms.date: 07/25/2018
ms.openlocfilehash: ac8e4b2eef0905ba50cb24b9eb035d50a05dd84a
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86932231"
---
# <a name="nuget-multiplatform-library-projects-nugetizer-3000"></a>Projetos de biblioteca multiplataforma do NuGet (Nugetizer 3000)

_Crie pacotes NuGet automaticamente para compartilhar código entre plataformas usando o ' Nugetizer 3000 '!_

É possível criar automaticamente pacotes NuGet para compartilhar código entre plataformas usando o _Nugetizer 3000_. Isso torna possível criar pacotes NuGet a partir de projetos de biblioteca existentes ou criando um novo **projeto de biblioteca multiplataforma**.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

O Nugetizer 3000 está incluído com Visual Studio para Mac &ndash; procurar a **biblioteca >** tipo de projeto de biblioteca Mulitplatform no **arquivo > nova** janela:

[![Criar nova janela de biblioteca multiplataforma](images/mulitplatform-library-sml.png)](images/mulitplatform-library.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Para usar o Nugetizer 3000 no Visual Studio, [Baixe e execute o instalador do VSIX](https://bit.ly/nugetizer-2017).

-----

## <a name="building-nuget-packages"></a>Criando pacotes NuGet

Uma vez configurado, cada compilação do projeto gera um pacote NuGet completo, que pode ser usado para compartilhar código internamente com outros aplicativos ou carregado no [NuGet.org](https://www.nuget.org).

Há três cenários para usar esse recurso:

- [Projetos de biblioteca existentes](existing-library.md)

  Crie um pacote NuGet a partir de projetos PCL (ou .NET Standard) existentes.

- [Criando um novo projeto de biblioteca multiplataforma](single-codebase.md)

  Crie uma nova biblioteca para compartilhar código comum por meio do NuGet usando um PCL ou .NET Standard.

- [Criando novos projetos de biblioteca específicos da plataforma](platform-specific.md)

  Crie uma nova biblioteca e NuGet que inclua o código específico da plataforma para iOS e Android e use um projeto compartilhado para conter o código comum e os projetos específicos da plataforma para dar suporte à funcionalidade específica para iOS ou Android.

Consulte o [Guia de metadados](metadata.md) para obter detalhes sobre os metadados necessários e opcionais que devem ser adicionados a qualquer pacote NuGet.

## <a name="further-nuget-information"></a>Informações adicionais do NuGet

Leia mais sobre como [criar manualmente o NuGets para Xamarin](~/cross-platform/app-fundamentals/nuget-manual.md) e como [incluir um pacote NuGet em um aplicativo](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough).

A [documentação do NuGet](https://docs.microsoft.com/nuget/) da Microsoft contém informações mais detalhadas sobre o formato **. nupkg** e o uso de pacotes NuGet no Visual Studio.

A discussão de design para projetos de pacote NuGet (também conhecido como NuGetizer 3000) está disponível no [repositório GitHub do NuGet](https://github.com/NuGet/Home/wiki/NuGetizer-3000).

## <a name="related-links"></a>Links Relacionados

- [Casos de uso de NuGetizer-3000](https://github.com/NuGet/Home/wiki/NuGetizer-Core-Scenarios)
- [Criar manualmente pacotes NuGet para o Xamarin](~/cross-platform/app-fundamentals/nuget-manual.md)
- [Documentação do NuGet](https://docs.microsoft.com/nuget/)
