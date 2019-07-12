---
title: Criação de novos projetos de biblioteca específica da plataforma para NuGet
description: Este documento descreve como criar um único pacote NuGet que contém o código específico da plataforma para várias plataformas.
ms.prod: xamarin
ms.assetid: D8BC4906-805F-4AFB-8D1A-88B7BF87E17F
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 4be010448d963462ccf06c263ddfad7ba1d9feae
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832035"
---
# <a name="creating-new-platform-specific-library-projects-for-nuget"></a>Criação de novos projetos de biblioteca específica da plataforma para NuGet

Projetos de biblioteca multiplataforma que se destinam a plataformas específicas, como iOS e Android, funcionam melhor com projetos compartilhados.

O NuGet pode conter tanto código específico de iOS e Android, bem como código .NET comuns a ambos.

Vários assemblies são criados e compilados em um único pacote do NuGet. Padrões de NuGet Certifique-se de que o pacote pode ser adicionado a todos os tipos de projeto com suporte, como projetos xamarin. IOS e Android.

## <a name="steps-to-create-a-cross-platform-library-nuget"></a>Etapas para criar um NuGet da biblioteca de plataforma cruzada

1. Selecione **arquivo > nova solução** (ou clique com botão direito de uma solução existente e escolha **Adicionar > Novo projeto**).

2. Escolher **biblioteca multiplataforma** da **multiplataforma > biblioteca** seção:

    [![](platform-specific-images/mulitplatform-library-sml.png "Configurar a biblioteca multiplataforma para uma única base de código")](platform-specific-images/multiplatform-library.png#lightbox)

3. Insira um **nome** e **descrição**e escolha **específicas de plataforma**:

    [![](platform-specific-images/specific-configure-sml.png "Configurar a biblioteca específica da plataforma para iOS e Android")](platform-specific-images/specific-configure.png#lightbox)

4. Conclua o assistente. Os projetos a seguir são adicionados à solução:

    - **Projeto do Android** – código específico do Android, opcionalmente, pode ser adicionado a este projeto.
    - **Projeto do iOS** – código específico do iOS, opcionalmente, pode ser adicionado a este projeto.
    - **Projeto NuGet** – nenhum código é adicionado a este projeto. Ele faz referência a outros projetos e contém a configuração de metadados para a saída do pacote NuGet.
    - **Projeto compartilhado** – código comum deve ser adicionado a este projeto, incluindo o código específico da plataforma dentro `#if` diretivas de compilador.

5. Clique com botão direito no projeto NuGet e escolha **opções**, em seguida, abra o **pacote do NuGet > metadados** seção e insira o [metadados necessários](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md) (como bem como quaisquer opcional metadados):

    [![](platform-specific-images/specific-metadata-sml.png "Insira os metadados necessários")](platform-specific-images/specific-metadata.png#lightbox)

6. Além disso, nos **opções de projeto** janela, abra o **Assemblies de referência** seção e escolha quais perfis PCL a biblioteca compartilhada dará suporte por meio de "de isca":

    ![](platform-specific-images/specific-reference-assemblies.png "Também na janela Opções do projeto, abra a seção de Assemblies de referência e a escolha de quais perfis PCL a biblioteca compartilhada dará suporte por meio de isca")

    > [!NOTE]
    > "De isca" significa que os assemblies PCL conterá somente a API exposta pela biblioteca (ele não pode conter o código específico da plataforma). Quando o NuGet é adicionado a um projeto do Xamarin, bibliotecas compartilhadas serão compiladas em relação a PCL, mas os assemblies específicos à plataforma contêm o código que é realmente usado pelo projeto do Android ou iOS.

7. Clique com botão direito no projeto e escolha **criar pacote do NuGet** (ou compilar ou implantar a solução) e o **. nupkg** serão salvas no arquivo de pacote do NuGet a **/bin/** pasta ( Depuração ou versão, dependendo da configuração).

    ![](platform-specific-images/create-nuget-package.png "Arquivo de pacote do NuGet será salvo na pasta bin Debug ou Release, dependendo da configuração")


## <a name="verifying-the-output"></a>Verificando a saída

Pacotes do NuGet também são arquivos ZIP, portanto, é possível inspecionar a estrutura interna do pacote gerado.

Esta captura de tela mostra o conteúdo de um NuGet específicos da plataforma que dá suporte a iOS e Android e tinha dois assemblies de referência selecionado:

![](platform-specific-images/nuget-output.png "Arquivos contidos no pacote NuGet")


## <a name="related-links"></a>Links relacionados

- [Guia de metadados](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)
