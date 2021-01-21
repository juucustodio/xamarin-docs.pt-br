---
title: Criando uma nova biblioteca multiplataforma para NuGet
description: Este documento descreve como criar uma biblioteca multiplataforma para uso com o NuGet. Essa técnica é adequada para algoritmos e lógica de negócios que podem ser expressas inteiramente na biblioteca de classes base .NET e, portanto, serão executadas em todas as plataformas de destino sem código específico à plataforma.
ms.prod: xamarin
ms.assetid: E7B55354-9BBE-4122-BCE3-3506B79090DD
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: ca874149874c420801c839fcde7afac11f46c5e1
ms.sourcegitcommit: e27e29c14b783263e063baaa65d4eecb8dd31f57
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98628911"
---
# <a name="creating-a-new-multiplatform-library-for-nuget"></a>Criando uma nova biblioteca multiplataforma para NuGet

Criar um projeto de biblioteca multiplataforma que usa PCL ou .NET Standard significa que o NuGet resultante pode ser adicionado a qualquer projeto .NET que ofereça suporte ao perfil de destino, incluindo projetos ASP.NET ou aplicativos de área de trabalho usando WinForms, WPF ou UWP.

A biblioteca pode conter apenas o código suportado pelo perfil PCL ou .NET Standard selecionado, bem como qualquer outro NuGets que seja adicionado.
Isso é adequado à lógica de negócios e aos algoritmos que podem ser expressos inteiramente na biblioteca de classes base do .NET.

Um único assembly é criado e compilado em um pacote NuGet.

Se você precisar mais tarde da funcionalidade específica da plataforma, os [projetos específicos da plataforma poderão ser adicionados](#add-platforms).

## <a name="steps-to-create-a-multiplatform-library-nuget"></a>Etapas para criar um NuGet de biblioteca multiplataforma

1. Selecione **arquivo > nova solução** (ou clique com o botão direito do mouse em uma solução existente e escolha **Adicionar > novo projeto**).

2. Escolha **biblioteca multiplataforma** na seção **biblioteca de > de várias plataformas** :

   [![Captura de tela mostra escolher um modelo com a biblioteca multiplataforma selecionada.](single-codebase-images/mulitplatform-library-sml.png)](single-codebase-images/mulitplatform-library.png#lightbox)

3. Insira um **nome** e uma **Descrição** e escolha **único para todas as plataformas**:

   [![Captura de tela mostra valores inseridos para nome, descrição e implementação.](single-codebase-images/single-configure-sml.png)](single-codebase-images/single-configure.png#lightbox)

4. Conclua o assistente. Um único projeto de biblioteca é criado na solução.

5. Clique com o botão direito do mouse no novo projeto de biblioteca e selecione **Opções**. A seção **Build > geral** permite que a **estrutura de destino** seja definida – escolha um perfil PCL portátil do .net ou uma versão .net Standard:

   [![Escolher PCL ou .NET Standard para o tipo de biblioteca](single-codebase-images/single-choose-type-sml.png)](single-codebase-images/single-choose-type.png#lightbox)

6. Também na janela **Opções do projeto** , abra a seção de **metadados > pacote NuGet** e insira os [metadados necessários](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md) (bem como os metadados opcionais):

   [![Inserir metadados necessários](single-codebase-images/single-metadata-sml.png)](single-codebase-images/single-metadata.png#lightbox)

7. Clique com o botão direito do mouse no projeto de biblioteca e escolha **criar pacote NuGet** (ou compilar ou implantar a solução) e o arquivo de pacote NuGet **. nupkg** será salvo na pasta **/bin/** (debug ou Release, dependendo da configuração):

   ![O arquivo de pacote NuGet será salvo na pasta bin Debug ou Release, dependendo da configuração](single-codebase-images/create-nuget-package.png)

## <a name="verifying-the-output"></a>Verificando a saída

Os pacotes NuGet também são arquivos ZIP, portanto, é possível inspecionar a estrutura interna do pacote gerado.

Esta captura de tela mostra o conteúdo de um NuGet baseado em PCL – apenas um único assembly PCL está incluído:

![Arquivos contidos no pacote NuGet](single-codebase-images/nuget-output.png)

<a name="add-platforms"></a>

## <a name="adding-platform-specific-code"></a>Adicionando Platform-Specific código

Projetos baseados em PCL e projetos baseados em .NET Standard não podem conter referências específicas à plataforma (como a funcionalidade iOS ou Android).

Se um projeto PCL ou .NET Standard projeto existente precisar ser expandido para incluir o código específico da plataforma, isso poderá ser feito clicando com o botão direito do mouse no projeto e selecionando **adicionar > adicionar a implementação da plataforma...**:

[![Adicionar menu de implementação de plataforma](single-codebase-images/add-later-sml.png)](single-codebase-images/add-later.png#lightbox)

Um ou mais projetos de plataforma podem ser adicionados à solução, e a biblioteca PCL ou .NET Standard existente pode, opcionalmente, ser convertida em um projeto compartilhado:

[![Adicionar opções de plataforma, como iOS, Android e projeto compartilhado](single-codebase-images/add-later-platforms-sml.png)](single-codebase-images/add-later-platforms-sml.png#lightbox)

Depois de converter para um projeto compartilhado, visite a seção **Opções do projeto > pacote NuGet > assemblies de referência** 
 [](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/platform-specific.md) e verifique se todos os perfis necessários estão selecionados (para que o NuGet continue a ser compatível com os projetos em que ele foi usado anteriormente).

## <a name="related-links"></a>Links Relacionados

- [Guia de metadados](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)
