---
title: Opções de compartilhamento de código
description: 'Este documento compara os diferentes métodos de compartilhamento de código entre projetos de plataforma cruzada: projetos compartilhados, bibliotecas de classes portáteis e .NET padrão, incluindo as vantagens e desvantagens de cada um.'
ms.prod: xamarin
ms.assetid: B73675D2-09A3-14C1-E41E-20352B819B53
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 7531a8a21b6895de6113f5edd85d09a5e150877e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="sharing-code-options"></a>Opções de compartilhamento de código

_Este documento compara os diferentes métodos de compartilhamento de código entre projetos de plataforma cruzada: projetos compartilhados, bibliotecas de classes portáteis e .NET padrão, incluindo as vantagens e desvantagens de cada um._

Há três métodos alternativos para compartilhar código entre aplicativos de plataforma cruzada:

-   [**Projetos compartilhados** ](#Shared_Projects) – o tipo de projeto de ativo compartilhado usado para organizar seu código-fonte e usar diretivas de compilador #if conforme necessário para gerenciar os requisitos de plataforma específica.
-   [**Bibliotecas de classes portáteis** ](#Portable_Class_Libraries) – criar direcionar uma biblioteca de classe portátil (PCL) as plataformas que você deseja dar suporte e usar Interfaces para fornecer a funcionalidade específica de plataforma.
-   [**Bibliotecas padrão do .NET** ](#Net_Standard) – projetos .NET padrão funcionam da mesma forma para PCLs, exigir o uso de Interfaces para inserir a funcionalidade específica de plataforma.

A meta de uma estratégia de compartilhamento de código é dar suporte a arquitetura mostrada neste diagrama, em que uma única base de código pode ser utilizado pelas várias plataformas.

 ![](code-sharing-images/conceptualarchitecture.png "Arquitetura de aplicativos de código compartilhado")

Este artigo compara os três métodos para ajudar a escolher o tipo de projeto certo para seus aplicativos.

<a name="Shared_Projects" />

## <a name="shared-projects"></a>Projetos compartilhados

A abordagem mais simples para o compartilhamento de arquivos de código é usar um [projeto compartilhado](~/cross-platform/app-fundamentals/shared-projects.md).

Esta captura de tela mostra um arquivo de solução que contém três projetos de aplicativo (para o Android, iOS e Windows Phone), com um **compartilhado** projeto que contém comuns c# arquivos código-fonte:

 ![](code-sharing-images/sharedsolution.png "Solução de projeto compartilhado")

A arquitetura conceitual é mostrada no diagrama a seguir, onde cada projeto inclui todos os arquivos de origem compartilhada:

 ![](code-sharing-images/sharedassetproject.png "Diagrama de projeto de compartilhado")


### <a name="example"></a>Exemplo

Um aplicativo de plataforma cruzada que dá suporte a iOS, Android e Windows Phone exigiria um projeto de aplicativo para cada plataforma. O código comum reside no projeto compartilhado.

Uma solução de exemplo conterá as seguintes pastas e projetos (nomes de projeto foi escolhido para expressividade, seus projetos não precisará seguir estas diretrizes de nomenclatura):

-   **Compartilhado** – projeto compartilhado que contém o código comum a todos os projetos.
-   **AppAndroid** – projeto de aplicativo xamarin.
-   **AppiOS** – projeto de aplicativo xamarin.
-   **AppWinPhone** – projeto de aplicativo do Windows Phone.


Dessa forma os projetos de três aplicativos estão compartilhando o mesmo código-fonte (os arquivos c# no compartilhado). As edições no código compartilhado serão compartilhadas entre todos os três projetos.


### <a name="benefits"></a>Benefícios

-  Permite que você compartilhe código em vários projetos.
-  Código compartilhado pode ser ramificado com base na plataforma usando diretivas de compilador (por exemplo. usando `#if __ANDROID__` , conforme discutido no [criando aplicativos de plataforma cruzada](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md) documento).
-  Projetos de aplicativo podem incluir referências de específico de plataforma que pode utilizar o código compartilhado (como o uso de `Community.CsharpSqlite.WP7` no exemplo Tasky para Windows Phone).



### <a name="disadvantages"></a>Desvantagens

-  Ao contrário da maioria dos outros tipos de projeto, um projeto compartilhado não tem nenhum assembly de 'produzidos'. Durante a compilação, os arquivos são tratados como parte do projeto de referência e compilados no assembly. Se você quiser compartilhar seu código como um assembly, em seguida, bibliotecas de classes portáteis ou .NET padrão são a melhor solução.
-  Refatorações que afetam o código dentro de diretivas de compilador 'inactive' não atualizará o código.


 <a name="Shared_Remarks" />

### <a name="remarks"></a>Comentários

Uma boa solução para desenvolvedores de aplicativos, escrevendo código que é destinado somente para o compartilhamento em seu aplicativo (e não distribuir para outros desenvolvedores).

 <a name="Portable_Class_Libraries" />


## <a name="portable-class-libraries"></a>Bibliotecas de Classes Portáteis


Bibliotecas de classes portáteis são [discutidos em detalhes aqui](~/cross-platform/app-fundamentals/pcl.md).

 ![](code-sharing-images/portableclasslibrary.png "Diagrama de biblioteca de classes portátil")


### <a name="benefits"></a>Benefícios

-  Permite que você compartilhe código em vários projetos.
-  Operações de refatoração sempre atualizam afetadas todas as referências.


### <a name="disadvantages"></a>Desvantagens

-  Não é possível usar diretivas de compilador.
-  Somente um subconjunto do .NET framework está disponível para uso, determinado pelo perfil selecionado (consulte a [Introdução ao PCL](~/cross-platform/app-fundamentals/pcl.md) para obter mais informações).


### <a name="remarks"></a>Comentários

Uma boa solução se você planeja compartilhar o assembly resultante com outros desenvolvedores.



<a name="Net_Standard" />

## <a name="net-standard-libraries"></a>Bibliotecas padrão do .NET

.NET padrão é [discutidos em detalhes aqui](~/cross-platform/app-fundamentals/net-standard.md).

![](code-sharing-images/netstandard.png "Diagrama de .NET padrão")

### <a name="benefits"></a>Benefícios

-  Permite que você compartilhe código em vários projetos.
-  Operações de refatoração sempre atualizam afetadas todas as referências.
-  Uma área de superfície maior do .NET biblioteca BCL (Base Class) está disponível que os perfis PCL.

### <a name="disadvantages"></a>Desvantagens

 -  Não é possível usar diretivas de compilador.

### <a name="remarks"></a>Comentários

.NET padrão é semelhante a PCL, mas com um modelo mais simples para suporte de plataforma e um número maior de classes de BCL.



## <a name="summary"></a>Resumo

O código de compartilhamento estratégia que você escolher será determinado pelas plataformas de que destino. Escolha um método que funciona melhor para seu projeto.

PCL ou .NET padrão são boas opções para a criação de bibliotecas de código compartilhável (especialmente publicação NuGet). Projetos compartilhados funcionam bem para os desenvolvedores de aplicativos que planeja usar muitas funcionalidades específicas de plataforma em seus aplicativos entre platforma.


## <a name="related-links"></a>Links relacionados

- [Aplicativos de plataforma cruzada construção (documento principal)](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)
- [Bibliotecas de classes portáteis](~/cross-platform/app-fundamentals/pcl.md)
- [Projetos compartilhados](~/cross-platform/app-fundamentals/shared-projects.md)
- [.NET Standard](~/cross-platform/app-fundamentals/net-standard.md)
- [Estudo de caso: Tasky](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md)
- [Exemplo tasky (github)](https://github.com/xamarin/mobile-samples/tree/master/Tasky)
- [Exemplo tasky usando PCL (github)](https://github.com/xamarin/mobile-samples/tree/master/TaskyPortable)
