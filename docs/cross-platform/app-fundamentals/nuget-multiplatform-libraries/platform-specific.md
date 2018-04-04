---
title: Criar novos projetos de biblioteca específica de plataforma para NuGet
ms.prod: xamarin
ms.assetid: D8BC4906-805F-4AFB-8D1A-88B7BF87E17F
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 0f244e614a40e444139d51a9466ccc7225a7fe68
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="creating-new-platform-specific-library-projects-for-nuget"></a>Criar novos projetos de biblioteca específica de plataforma para NuGet

Projetos de biblioteca em várias plataformas que se destinam a plataformas específicas, como iOS e Android, funcionam melhor com projetos compartilhados.

O NuGet pode conter tanto código específico de iOS e Android, como código .NET comum para ambos.

Vários assemblies são criados e compilados em um único pacote de NuGet. Padrões de NuGet Certifique-se de que o pacote pode ser adicionado a todos os tipos de projeto com suporte, como o Xamarin iOS e Android projetos.

## <a name="steps-to-create-a-cross-platform-library-nuget"></a>Etapas para criar um biblioteca de plataforma cruzada NuGet

1. Selecione **arquivo > nova solução** (ou uma solução existente de clique do botão direito e escolha **Adicionar > Novo projeto**).

2. Escolha **Multiplatform biblioteca** do **Multiplatform > biblioteca** seção:

  [![](platform-specific-images/mulitplatform-library-sml.png "Configurar a biblioteca de multiplataforma para uma base de código único")](platform-specific-images/multiplatform-library.png#lightbox)

3. Insira um **nome** e **descrição**e escolha **específico de plataforma**:

  [![](platform-specific-images/specific-configure-sml.png "Configurar a biblioteca específica de plataforma para iOS e Android")](platform-specific-images/specific-configure.png#lightbox)

4. Conclua o assistente. Os projetos a seguir são adicionados à solução:

  - **Projeto Android** – código específico do Android, opcionalmente, pode ser adicionado a este projeto.
  - **iOS projeto** – código específico do iOS, opcionalmente, pode ser adicionado a este projeto.
  - **Projeto NuGet** – nenhum código é adicionado a este projeto. Ele faz referência os outros projetos e contém a configuração de metadados para a saída do pacote NuGet.
  - **Projeto compartilhado** – código comum deve ser adicionado a este projeto, incluindo o código de plataforma específica dentro de `#if` diretivas de compilador.

5. Clique com botão direito no projeto NuGet e escolha **opções**, em seguida, abra o **pacote NuGet > metadados** seção e insira o [metadados necessários](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md) (como bem como quaisquer opcional metadados):

  [![](platform-specific-images/specific-metadata-sml.png "Insira os metadados necessários")](platform-specific-images/specific-metadata.png#lightbox)

6. Também no **opções de projeto** janela, abra o **Assemblies de referência** seção e escolha quais perfis PCL a biblioteca compartilhada terá suporte via "isco":

  ![](platform-specific-images/specific-reference-assemblies.png "Também na janela de opções de projeto, abra a seção de Assemblies de referência e escolher quais perfis PCL a biblioteca compartilhada terá suporte via isco")

  > [!NOTE]
> "Isco" significa que os assemblies PCL conterá apenas a API exposta pela biblioteca (ele não pode conter o código específico da plataforma). Quando o NuGet é adicionado a um projeto de Xamarin, bibliotecas compartilhadas serão compiladas em relação a PCL, mas os assemblies específicos da plataforma contém o código que é realmente usado pelo projeto Android ou iOS.

7. Com o botão direito no projeto e escolha **criar pacote do NuGet** (ou compilar ou implantar a solução) e o **nupkg** arquivo de pacote do NuGet serão salvas no **/bin/** (da pasta Depurar ou lançar, dependendo da configuração).

  ![](platform-specific-images/create-nuget-package.png "Arquivo de pacote do NuGet será salvo na pasta bin Debug ou Release, dependendo da configuração")


## <a name="verifying-the-output"></a>Verificando a saída

Pacotes do NuGet também são arquivos ZIP, portanto, é possível inspecionar a estrutura interna do pacote gerado.

Esta captura de tela mostra o conteúdo de um NuGet específico da plataforma que dá suporte a iOS e Android e tinha dois assemblies de referência selecionado:

![](platform-specific-images/nuget-output.png "Arquivos contidos no pacote do NuGet")


## <a name="related-links"></a>Links relacionados

- [Guia de metadados](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)
