---
title: Criando uma nova biblioteca de multiplataforma para NuGet
ms.topic: article
ms.prod: xamarin
ms.assetid: E7B55354-9BBE-4122-BCE3-3506B79090DD
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: a56cc080ac04c45ef3f0fcc6c7c89096a08beddf
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="creating-a-new-multiplatform-library-for-nuget"></a>Criando uma nova biblioteca de multiplataforma para NuGet

Criando um projeto de biblioteca Multiplatform que usa PCL ou .NET padrão significa que o NuGet resultante pode ser adicionado a qualquer projeto .NET que suporta o perfil de destino, incluindo os projetos ASP.NET ou aplicativos de área de trabalho usando WinForms, WPF ou UWP.

A biblioteca só pode conter código com suporte o perfil selecionado de PCL ou padrão do .NET, bem como outros NuGets que são adicionados.
Isso é adequado para a lógica de negócios e algoritmos que podem ser expressos inteiramente na biblioteca de classe base do .NET.

Um único assembly é criado e compilado em um pacote do NuGet.

Se você precisar posteriormente a funcionalidade específica de plataforma, [podem ser adicionados a projetos específicos de plataforma](#add-platforms).

## <a name="steps-to-create-a-multiplatform-library-nuget"></a>Etapas para criar um biblioteca multiplataforma NuGet

1. Selecione **arquivo > nova solução** (ou uma solução existente de clique do botão direito e escolha **Adicionar > Novo projeto**).

2. Escolha **Multiplatform biblioteca** do **Multiplatform > biblioteca** seção:

  [![](single-codebase-images/mulitplatform-library-sml.png "Configurar a biblioteca de multiplataforma para uma base de código único")](single-codebase-images/mulitplatform-library.png#lightbox)

3. Insira um **nome** e **descrição**e escolha **única para todas as plataformas**:

  [![](single-codebase-images/single-configure-sml.png "Configurar a biblioteca de multiplataforma para uma base de código único")](single-codebase-images/single-configure.png#lightbox)

4. Conclua o assistente. Um projeto de biblioteca único é criado na solução.

5. Clique com botão direito no novo projeto de biblioteca e, em seguida, selecione **opções**. O **Build > geral** seção permite que o **Framework de destino** a ser definido – escolha um perfil PCL portátil do .NET ou uma versão padrão do .NET:

  [![](single-codebase-images/single-choose-type-sml.png "Escolher PCL ou .NET padrão para o tipo de biblioteca")](single-codebase-images/single-choose-type.png#lightbox)

6. Também no **opções de projeto** janela, abra o **pacote NuGet > metadados** seção e insira o [metadados necessários](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md) (bem como quaisquer metadados opcional):

  [![](single-codebase-images/single-metadata-sml.png "Insira os metadados necessários")](single-codebase-images/single-metadata.png#lightbox)

7. Clique com botão direito no projeto de biblioteca e escolha **criar pacote do NuGet** (ou compilar ou implantar a solução) e o **nupkg** arquivo de pacote do NuGet serão salvas no **/bin/** pasta (Debug ou Release, dependendo da configuração):

  ![](single-codebase-images/create-nuget-package.png "O arquivo de pacote do NuGet serão ser salvas na pasta bin, Debug ou Release, dependendo da configuração")


## <a name="verifying-the-output"></a>Verificando a saída

Pacotes do NuGet também são arquivos ZIP, portanto, é possível inspecionar a estrutura interna do pacote gerado.

Esta captura de tela mostra o conteúdo de um NuGet com base em PCL – um único assembly PCL está incluído:

![](single-codebase-images/nuget-output.png "Arquivos contidos no pacote do NuGet")

<a name="add-platforms" />

## <a name="adding-platform-specific-code"></a>Adicionando código específico da plataforma

Projetos baseados em PCL e projetos baseados em .NET padrão não podem conter referências de plataforma específica (por exemplo, iOS ou Android funcionalidade).

Se um projeto existente de PCL ou .NET padrão precisa ser expandido para incluir o código específico da plataforma, isso pode ser feito clicando duas vezes no projeto e selecionando **Adicionar > Adicionar a implementação de plataforma...** :

[![](single-codebase-images/add-later-sml.png "Adicionar menu de implementação de plataforma")](single-codebase-images/add-later.png#lightbox)

Um ou mais projetos de plataforma podem ser adicionados à solução e a biblioteca de PCL ou .NET padrão existente, opcionalmente, pode ser convertida em um projeto compartilhado:

[![](single-codebase-images/add-later-platforms-sml.png "Adicionar opções de plataformas como iOS, Android e projeto compartilhado")](single-codebase-images/add-later-platforms-sml.png#lightbox)

Depois de converter em um projeto compartilhado, visite o **opções de projeto > pacote NuGet > Assemblies de referência**
[seção](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/platform-specific.md) e certifique-se de que as necessárias perfil selecionado (para que o NuGet continua a ser compatível com projetos que ela foi usada anteriormente).


## <a name="related-links"></a>Links relacionados

- [Guia de metadados](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)
