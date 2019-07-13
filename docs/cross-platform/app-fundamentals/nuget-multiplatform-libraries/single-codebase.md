---
title: Criando uma nova biblioteca multiplataforma para NuGet
description: Este documento descreve como criar uma biblioteca multiplataforma para uso com o NuGet. Essa técnica é adequada para a lógica de negócios e algoritmos que podem ser expressos inteiramente na biblioteca de classes de Base do .NET e, portanto, serão executado em todas as plataformas de destino sem código específico da plataforma.
ms.prod: xamarin
ms.assetid: E7B55354-9BBE-4122-BCE3-3506B79090DD
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 6371c2af15eab9c5124212eefd9cf70d07b945d4
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67864727"
---
# <a name="creating-a-new-multiplatform-library-for-nuget"></a>Criando uma nova biblioteca multiplataforma para NuGet

Criando um projeto de biblioteca multiplataforma que usa o PCL ou .NET Standard significa que o NuGet resultante pode ser adicionado a qualquer projeto do .NET que suporta o perfil de destino, incluindo projetos do ASP.NET ou aplicativos da área de trabalho usando o WinForms, WPF ou UWP.

A biblioteca só pode conter código com suporte pelo perfil selecionado de PCL ou .NET Standard, bem como outros NuGets que são adicionados.
Isso é adequado para a lógica de negócios e algoritmos que podem ser expressos inteiramente na base class library do .NET.

Um único assembly é criado e compilado em um pacote do NuGet.

Se você precisar da funcionalidade de específico da plataforma, mais tarde [podem ser adicionados a projetos específicos da plataforma](#add-platforms).

## <a name="steps-to-create-a-multiplatform-library-nuget"></a>Etapas para criar um NuGet biblioteca multiplataforma

1. Selecione **arquivo > nova solução** (ou clique com botão direito de uma solução existente e escolha **Adicionar > Novo projeto**).

2. Escolher **biblioteca multiplataforma** da **multiplataforma > biblioteca** seção:

   [![](single-codebase-images/mulitplatform-library-sml.png "Configurar a biblioteca multiplataforma para uma única base de código")](single-codebase-images/mulitplatform-library.png#lightbox)

3. Insira um **nome** e **descrição**e escolha **único para todas as plataformas**:

   [![](single-codebase-images/single-configure-sml.png "Configurar a biblioteca multiplataforma para uma única base de código")](single-codebase-images/single-configure.png#lightbox)

4. Conclua o assistente. Um projeto de biblioteca único é criado na solução.

5. Clique com botão direito no novo projeto de biblioteca e, em seguida, selecione **opções**. O **Build > geral** seção permite que o **estrutura de destino** a ser definido – escolha um perfil de PCL portátil do .NET ou uma versão do .NET Standard:

   [![](single-codebase-images/single-choose-type-sml.png "Escolha o PCL ou .NET Standard para o tipo de biblioteca")](single-codebase-images/single-choose-type.png#lightbox)

6. Além disso, no **opções de projeto** janela, abra o **pacote do NuGet > metadados** seção e insira o [metadados necessários](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md) (bem como quaisquer metadados opcionais):

   [![](single-codebase-images/single-metadata-sml.png "Insira os metadados necessários")](single-codebase-images/single-metadata.png#lightbox)

7. Clique com botão direito no projeto de biblioteca e escolha **criar pacote do NuGet** (ou compilar ou implantar a solução) e o **. nupkg** serão salvas no arquivo de pacote do NuGet a **/bin/** pasta (Debug ou Release, dependendo da configuração):

   ![](single-codebase-images/create-nuget-package.png "O arquivo de pacote do NuGet será salvo na pasta bin Debug ou Release, dependendo da configuração")


## <a name="verifying-the-output"></a>Verificando a saída

Pacotes do NuGet também são arquivos ZIP, portanto, é possível inspecionar a estrutura interna do pacote gerado.

Esta captura de tela mostra o conteúdo de um NuGet baseado em PCL – um único assembly PCL é incluído:

![](single-codebase-images/nuget-output.png "Arquivos contidos no pacote NuGet")

<a name="add-platforms" />

## <a name="adding-platform-specific-code"></a>Adicionar código específico da plataforma

Projetos baseados em PCL e projetos baseados em .NET Standard não podem conter referências específicas da plataforma (por exemplo, iOS ou Android funcionalidade).

Se um projeto PCL existente ou o projeto do .NET Standard precisa ser expandido para incluir o código específico da plataforma, isso pode ser feito clicando com botão direito no projeto e selecionando **Adicionar > Adicionar a implementação de plataforma...** :

[![](single-codebase-images/add-later-sml.png "Adicionar menu de implementação de plataforma")](single-codebase-images/add-later.png#lightbox)

Um ou mais projetos de plataforma podem ser adicionados à solução e a biblioteca PCL ou .NET Standard existente, opcionalmente, pode ser convertida em um projeto compartilhado:

[![](single-codebase-images/add-later-platforms-sml.png "Adicionar opções de plataformas como iOS, Android e projeto compartilhado")](single-codebase-images/add-later-platforms-sml.png#lightbox)

Depois de converter um projeto compartilhado, visite o **opções de projeto > pacote do NuGet > Assemblies de referência**
[seção](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/platform-specific.md) e garanta que nenhum perfil selecionado (para que o NuGet continua a ser compatível com projetos que foi usada anteriormente na).


## <a name="related-links"></a>Links relacionados

- [Guia de metadados](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)
