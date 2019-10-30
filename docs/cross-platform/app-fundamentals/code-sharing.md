---
title: Visão geral do código de compartilhamento
description: 'Este documento compara os diferentes métodos de compartilhamento de código entre projetos de plataforma cruzada: projetos compartilhados, bibliotecas de classes portáteis e .NET Standard, incluindo os benefícios e as desvantagens de cada um.'
ms.prod: xamarin
ms.assetid: B73675D2-09A3-14C1-E41E-20352B819B53
author: davidortinau
ms.author: daortin
ms.date: 08/06/2018
ms.openlocfilehash: 78b849434a087cf7951fe36345688251885ea00b
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016908"
---
# <a name="sharing-code-overview"></a>Visão geral do código de compartilhamento

_Este documento compara os diferentes métodos de compartilhamento de código entre projetos de plataforma cruzada: .NET Standard, projetos compartilhados e bibliotecas de classes portáteis, incluindo os benefícios e as desvantagens de cada um._

Há três métodos para compartilhar código entre aplicativos de plataforma cruzada:

- [**Bibliotecas de .net Standard**](#Net_Standard) – .net Standard projetos podem implementar o código a ser compartilhado entre várias plataformas e pode acessar um grande número de APIs do .net (dependendo da versão). .NET Standard 1,0-1,6 implementar conjuntos de APIs de forma progressiva maior, enquanto .NET Standard 2,0 fornece a melhor cobertura do .NET BCL (incluindo as APIs .NET disponíveis em aplicativos Xamarin).
- [**Projetos compartilhados**](#Shared_Projects) – use o tipo de projeto de ativo compartilhado para organizar seu código-fonte e use `#if` diretivas de compilador conforme necessário para gerenciar requisitos específicos da plataforma.
- [**Bibliotecas de classes portáteis**](#Portable_Class_Libraries) (preteridas) – PCLs (bibliotecas de classes portáteis) podem direcionar várias plataformas com uma superfície de API comum e usar interfaces para fornecer funcionalidade específica da plataforma. Os PCLs são preteridos nas versões mais recentes do Visual Studio &ndash; usar .NET Standard em vez disso.

O objetivo de uma estratégia de compartilhamento de código é dar suporte à arquitetura mostrada neste diagrama, em que uma única codebase pode ser utilizada por várias plataformas.

 ![Arquitetura de aplicativo de código compartilhado](code-sharing-images/conceptualarchitecture.png "Arquitetura de aplicativo de código compartilhado")

Este artigo compara os métodos disponíveis para ajudá-lo a escolher o tipo de projeto certo para seus aplicativos.

<a name="Net_Standard" />

## <a name="net-standard-libraries"></a>Bibliotecas de .NET Standard

As bibliotecas de [.net Standard](~/cross-platform/app-fundamentals/net-standard.md) fornecem um conjunto bem definido de bibliotecas de classes base que podem ser referenciadas em diferentes tipos de projeto, incluindo projetos de plataforma cruzada, como Xamarin. Android e Xamarin. Ios. .NET Standard 2,0 é recomendado para compatibilidade máxima com o código de .NET Framework existente.

![Diagrama de .NET Standard](code-sharing-images/netstandard.png "Diagrama de .NET Standard")

### <a name="benefits"></a>Benefícios

- Permite que você compartilhe código entre vários projetos.
- As operações de refatoração sempre atualizam todas as referências afetadas.
- Uma área de superfície maior da BCL (biblioteca de classes base) do .NET está disponível para perfis PCL. Em particular, .NET Standard 2,0 tem quase a mesma superfície de API que a .NET Framework e é recomendada para novos aplicativos e portar PCLs existentes.

### <a name="disadvantages"></a>Desvantagens

- Não é possível usar diretivas de compilador como `#if __IOS__`.

### <a name="remarks"></a>Comentários

.NET Standard é [semelhante a PCL](https://docs.microsoft.com/dotnet/standard/net-standard#comparison-to-portable-class-libraries), mas com um modelo mais simples para suporte de plataforma e um número maior de classes da BCL.

<a name="Shared_Projects" />

## <a name="shared-projects"></a>Projetos compartilhados

[Projetos compartilhados](~/cross-platform/app-fundamentals/shared-projects.md) contêm arquivos de código e ativos que são incluídos em qualquer projeto que os referencia. Os projetos de compartilhamento não produzem a saída compilada por conta própria.

Esta captura de tela mostra um arquivo de solução que contém três projetos de aplicativo (para Android, iOS e Windows ), com um projeto C# compartilhado que contém arquivos de código-fonte comuns:

![Solução de projeto compartilhado](code-sharing-images/sharedsolution.png "Solução de projeto compartilhado")

A arquitetura conceitual é mostrada no diagrama a seguir, onde cada projeto inclui todos os arquivos de origem compartilhados:

![Diagrama de projeto compartilhado](code-sharing-images/sharedassetproject.png "Diagrama de projeto compartilhado")

### <a name="example"></a>Exemplo

Um aplicativo de plataforma cruzada que dá suporte a iOS, Android e Windows exigiria um projeto de aplicativo para cada plataforma. O código comum reside no projeto compartilhado.

Uma solução de exemplo conteria as seguintes pastas e projetos (nomes de projeto foram escolhidos para fins expressivos, seus projetos não precisam seguir essas diretrizes de nomenclatura):

- **Compartilhado** – projeto compartilhado que contém o código comum a todos os projetos.
- **AppAndroid** – projeto de aplicativo Xamarin. Android.
- **AppiOS** – projeto de aplicativo Xamarin. Ios.
- **AppWindows** – projeto de aplicativo do Windows.

Dessa forma, os três projetos de aplicativo estão compartilhando o mesmo código C# -fonte (os arquivos em compartilhados). Todas as edições no código compartilhado serão compartilhadas entre todos os três projetos.

### <a name="benefits"></a>Benefícios

- Permite que você compartilhe código entre vários projetos.
- O código compartilhado pode ser ramificado com base na plataforma usando as diretivas do compilador (por exemplo, usando `#if __ANDROID__`, conforme discutido no documento [criando aplicativos da plataforma cruzada](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md) ).
- Os projetos de aplicativo podem incluir referências específicas à plataforma que o código compartilhado pode utilizar (como usar `Community.CsharpSqlite.WP7` no exemplo de tarefa para Windows Phone).

### <a name="disadvantages"></a>Desvantagens

- Refatoração que afetam o código dentro de diretivas de compilador ' inativas ' não atualizarão o código dentro dessas diretivas.
- Ao contrário da maioria dos outros tipos de projeto, um projeto compartilhado não tem nenhum assembly de ' saída '. Durante a compilação, os arquivos são tratados como parte do projeto de referência e compilados nesse assembly. Se você deseja compartilhar seu código como um assembly, .NET Standard ou bibliotecas de classe portátil são uma solução melhor.

<a name="Shared_Remarks" />

### <a name="remarks"></a>Comentários

Uma boa solução para desenvolvedores de aplicativos que escrevem código destinado apenas ao compartilhamento em seus aplicativos (e não a distribuição a outros desenvolvedores).

<a name="Portable_Class_Libraries" />

## <a name="portable-class-libraries"></a>Bibliotecas de Classes Portáteis

> [!TIP]
> .NET Standard bibliotecas 2,0 são recomendadas em relação às bibliotecas de classes portáteis.

As bibliotecas de classes portáteis são [discutidas em detalhes aqui](~/cross-platform/app-fundamentals/pcl.md).

![Diagrama de biblioteca de classes portátil](code-sharing-images/portableclasslibrary.png "Diagrama de biblioteca de classes portátil")

### <a name="benefits"></a>Benefícios

- Permite que você compartilhe código entre vários projetos.
- As operações de refatoração sempre atualizam todas as referências afetadas.

### <a name="disadvantages"></a>Desvantagens

- Preteridas nas versões mais recentes do Visual Studio, em vez disso, as bibliotecas de .NET Standard são recomendadas. Consulte esta [explicação das diferenças entre o](https://docs.microsoft.com/dotnet/standard/net-standard#comparison-to-portable-class-libraries) PCL e o .net Standard.
- Não é possível usar diretivas de compilador.
- Somente um subconjunto do .NET Framework está disponível para uso, determinado pelo perfil selecionado (consulte a [introdução ao PCL](~/cross-platform/app-fundamentals/pcl.md) para obter mais informações).

### <a name="remarks"></a>Comentários

O modelo PCL é considerado preterido nas versões mais recentes do Visual Studio.

## <a name="summary"></a>Resumo

A estratégia de compartilhamento de código que você escolher será orientada pelas plataformas de destino. Escolha um método que funcione melhor para seu projeto.

.NET Standard é a melhor opção para criar bibliotecas de código compartilháveis (especialmente a publicação no NuGet). Projetos compartilhados funcionam bem para desenvolvedores de aplicativos que planejam usar muitas funcionalidades específicas da plataforma em seus aplicativos de plataforma cruzada.

Embora os projetos PCL continuem a ter suporte no Visual Studio, .NET Standard é recomendada para novos projetos.

## <a name="related-links"></a>Links relacionados

- [Criando aplicativos de plataforma cruzada (documento principal)](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)
- [Bibliotecas de classes portáteis](~/cross-platform/app-fundamentals/pcl.md)
- [Projetos compartilhados](~/cross-platform/app-fundamentals/shared-projects.md)
- [.NET Standard](~/cross-platform/app-fundamentals/net-standard.md)
- [Estudo de caso: Tasky](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md)
- [Exemplo de tarefa (github)](https://github.com/xamarin/mobile-samples/tree/master/Tasky)
- [Exemplo de tarefa usando PCL (github)](https://github.com/xamarin/mobile-samples/tree/master/TaskyPortable)
