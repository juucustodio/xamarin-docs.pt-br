---
title: Visão geral do código do compartilhamento
description: 'Este documento compara os diferentes métodos de compartilhar código entre projetos de plataforma cruzada: Compartilhado projetos, bibliotecas de classes portáteis e .NET Standard, incluindo as vantagens e desvantagens de cada um.'
ms.prod: xamarin
ms.assetid: B73675D2-09A3-14C1-E41E-20352B819B53
author: conceptdev
ms.author: crdun
ms.date: 08/06/2018
ms.openlocfilehash: 98b5786ae4f071b4d8e8f854561db97aee037fdc
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61227932"
---
# <a name="sharing-code-overview"></a>Visão geral do código do compartilhamento

_Este documento compara os diferentes métodos de compartilhar código entre projetos de plataforma cruzada: .NET Standard, projetos compartilhados e bibliotecas de classes portáteis, incluindo as vantagens e desvantagens de cada um._

Há três métodos para compartilhar código entre aplicativos de plataforma cruzada:

- [**.NET standard Libraries** ](#Net_Standard) – projetos do .NET Standard pode implementar código sejam compartilhadas entre várias plataformas e pode acessar um grande número de APIs do .NET (dependendo da versão). .NET standard 1.0-1.6 implementam conjuntos de cada vez maiores de APIs, enquanto o .NET Standard 2.0 fornece a melhor cobertura da BCL do .NET (incluindo as APIs do .NET disponíveis nos aplicativos do Xamarin).
- [**Projetos compartilhados** ](#Shared_Projects) – Use o tipo de projeto de ativo compartilhado para organizar seu código-fonte e usar `#if` diretivas de compilador conforme necessário para gerenciar os requisitos específicos da plataforma.
- [**Bibliotecas de classes portáteis** ](#Portable_Class_Libraries) (preterido) – bibliotecas de classes portáteis (PCLs) pode direcionar várias plataformas com uma superfície de API comum e usar Interfaces para fornecer a funcionalidade específica da plataforma. PCLs são preteridas nas versões mais recentes do Visual Studio &ndash; usar em vez disso, o .NET Standard.

O objetivo de uma estratégia de compartilhamento de código é dar suporte a arquitetura mostrada neste diagrama, em que uma única base de código pode ser utilizado pelas várias plataformas.

 ![Compartilhado a arquitetura de aplicativos de código](code-sharing-images/conceptualarchitecture.png "compartilhado a arquitetura de aplicativos de código")

Este artigo compara os métodos disponíveis para ajudá-lo a escolher o tipo de projeto certo para seus aplicativos.

<a name="Net_Standard" />

## <a name="net-standard-libraries"></a>.NET Standard Libraries

[.NET standard](~/cross-platform/app-fundamentals/net-standard.md) bibliotecas fornecem um conjunto bem definido de bibliotecas de classes base que pode ser referenciado em diferentes tipos de projeto, incluindo projetos de plataforma cruzada como xamarin. Android e xamarin. IOS. .NET standard 2.0 é recomendada para compatibilidade máxima com o código existente do .NET Framework.

![Diagrama do .NET standard](code-sharing-images/netstandard.png "diagrama .NET Standard")

### <a name="benefits"></a>Benefícios

- Permite que você compartilhe código entre vários projetos.
- Operações de refatoração sempre atualizam referências de todos os afetados.
- Uma área de superfície maior do .NET Base classe Library (BCL) está disponível que os perfis PCL. Em particular, o .NET Standard 2.0 tem quase a mesma superfície de API do .NET Framework e é recomendado para novos aplicativos e portabilidade PCLs existentes.

### <a name="disadvantages"></a>Desvantagens

- Não é possível usar diretivas de compilador, como `#if __IOS__`.

### <a name="remarks"></a>Comentários

O .NET standard é [semelhante à PCL](https://docs.microsoft.com/dotnet/standard/net-standard#comparison-to-portable-class-libraries), mas com um modelo mais simples para suporte de plataforma e um número maior de classes da BCL.

<a name="Shared_Projects" />

## <a name="shared-projects"></a>Projetos compartilhados

[Projetos compartilhados](~/cross-platform/app-fundamentals/shared-projects.md) contêm arquivos de código e ativos que são incluídos em qualquer projeto que faz referência a eles. Projetos de compartilhamento não produzem saída compilada por conta própria.

Esta captura de tela mostra um arquivo de solução contendo três projetos de aplicativo (para Android, iOS e Windows), com um **compartilhado** projeto que contém a comum c# arquivos de código fonte:

![Solução de projeto compartilhado](code-sharing-images/sharedsolution.png "solução de projeto compartilhado")

A arquitetura conceitual é mostrada no diagrama a seguir, onde cada projeto inclui todos os arquivos de código-fonte compartilhado:

![Diagrama de projeto compartilhado](code-sharing-images/sharedassetproject.png "diagrama de projeto compartilhado")

### <a name="example"></a>Exemplo

Um aplicativo de plataforma cruzada que oferece suporte a iOS, Android e Windows exigiria um projeto de aplicativo para cada plataforma. O código comum reside no projeto compartilhado.

Um exemplo de solução conteria as pastas e os projetos (nomes de projeto foi escolhido para expressividade, seus projetos não precisa seguir essas diretrizes de nomenclatura) a seguir:

- **Compartilhado** – projeto compartilhado que contém o código comum a todos os projetos.
- **AppAndroid** – projeto de aplicativo do xamarin. Android.
- **AppiOS** – projeto de aplicativo do xamarin. IOS.
- **AppWindows** – projeto de aplicativo do Windows.

Dessa forma, os projetos de aplicativo de três estão compartilhando o mesmo código-fonte (arquivos do c# no compartilhado). Todas as edições no código compartilhado serão compartilhadas entre todos os três projetos.

### <a name="benefits"></a>Benefícios

- Permite que você compartilhe código entre vários projetos.
- Código compartilhado pode ser ramificado com base na plataforma usando diretivas de compilador (por exemplo. usando o `#if __ANDROID__` , conforme discutido em de [criando aplicativos de plataforma cruzada](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md) documento).
- Projetos de aplicativos podem incluir referências específicas da plataforma que o código compartilhado pode utilizar (como o uso de `Community.CsharpSqlite.WP7` na amostra Tasky para Windows Phone).

### <a name="disadvantages"></a>Desvantagens

- Refatorações que afetam o código dentro de diretivas de compilador 'inactive' não atualizará o código dentro dessas diretivas.
- Ao contrário da maioria dos outros tipos de projeto, um projeto compartilhado não tem nenhum assembly de 'output'. Durante a compilação, os arquivos são tratados como parte do projeto de referência e compilados no assembly. Se você quiser compartilhar seu código como um assembly, em seguida, .NET Standard ou bibliotecas de classes portáteis são uma solução melhor.

<a name="Shared_Remarks" />

### <a name="remarks"></a>Comentários

Uma boa solução para os desenvolvedores de aplicativos que escrever um código que é destinado apenas para o compartilhamento no seu aplicativo (e não para distribuição a outros desenvolvedores).

<a name="Portable_Class_Libraries" />

## <a name="portable-class-libraries"></a>Bibliotecas de Classes Portáteis

> [!TIP]
> Bibliotecas do .NET standard 2.0 são recomendadas para bibliotecas de classes portáteis.

São bibliotecas de classes portáteis [abordado em detalhes aqui](~/cross-platform/app-fundamentals/pcl.md).

![Diagrama de biblioteca de classes portátil](code-sharing-images/portableclasslibrary.png "diagrama da biblioteca de classes portátil")

### <a name="benefits"></a>Benefícios

- Permite que você compartilhe código entre vários projetos.
- Operações de refatoração sempre atualizam referências de todos os afetados.

### <a name="disadvantages"></a>Desvantagens

- Bibliotecas do .NET Standard preterida nas versões mais recentes do Visual Studio, são recomendadas em vez disso. Consulte este [explicação das diferenças](https://docs.microsoft.com/dotnet/standard/net-standard#comparison-to-portable-class-libraries) entre PCL e .NET Standard.
- Não é possível usar diretivas de compilador.
- Somente um subconjunto do .NET framework está disponível para uso, determinadas pelo perfil selecionado (consulte a [Introdução à PCL](~/cross-platform/app-fundamentals/pcl.md) para obter mais informações).

### <a name="remarks"></a>Comentários

Modelo PCL é considerado preterida nas versões mais recentes do Visual Studio.

## <a name="summary"></a>Resumo

O escolha da estratégia de compartilhamento de código será determinado pelas plataformas de que destino. Escolha um método que funciona melhor para seu projeto.

.NET standard é a melhor opção para a criação de bibliotecas de código compartilhável (especialmente a publicação no NuGet). Projetos compartilhados funcionam bem para os desenvolvedores de aplicativos que planeja usar muita funcionalidade específica da plataforma em seus aplicativos de plataforma cruzada.

Embora projetos PCL continuem a ter suporte no Visual Studio, .NET Standard é recomendado para novos projetos.

## <a name="related-links"></a>Links relacionados

- [Aplicativos de plataforma cruzada da construção (documento principal)](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)
- [Bibliotecas de classes portáteis](~/cross-platform/app-fundamentals/pcl.md)
- [Projetos compartilhados](~/cross-platform/app-fundamentals/shared-projects.md)
- [.NET Standard](~/cross-platform/app-fundamentals/net-standard.md)
- [Estudo de caso: Tasky](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md)
- [Exemplo tasky (github)](https://github.com/xamarin/mobile-samples/tree/master/Tasky)
- [Exemplo de tasky usando PCL (github)](https://github.com/xamarin/mobile-samples/tree/master/TaskyPortable)
