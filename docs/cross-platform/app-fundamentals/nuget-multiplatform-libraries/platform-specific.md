---
title: Criando novos projetos de biblioteca específicos da plataforma para NuGet
description: Este documento descreve como criar um único pacote NuGet que contém o código específico da plataforma para várias plataformas.
ms.prod: xamarin
ms.assetid: D8BC4906-805F-4AFB-8D1A-88B7BF87E17F
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: b1c32d76f4f25298a8f0643e2e29707df7775736
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86939214"
---
# <a name="creating-new-platform-specific-library-projects-for-nuget"></a>Criando novos projetos de biblioteca específicos da plataforma para NuGet

Os projetos de biblioteca multiplataforma que visam plataformas específicas, como iOS e Android, funcionam melhor com projetos compartilhados.

O NuGet pode conter código específico para iOS e Android, bem como o código .NET comum para ambos.

Vários assemblies são criados e incorporados em um único pacote NuGet. Os padrões do NuGet garantem que o pacote possa ser adicionado a todos os tipos de projeto com suporte, como o Xamarin. iOS e os projetos Android.

## <a name="steps-to-create-a-cross-platform-library-nuget"></a>Etapas para criar um NuGet de biblioteca de plataforma cruzada

1. Selecione **arquivo > nova solução** (ou clique com o botão direito do mouse em uma solução existente e escolha **Adicionar > novo projeto**).

2. Escolha **biblioteca multiplataforma** na seção **biblioteca de > de várias plataformas** :

    [![Configurar a biblioteca de várias plataformas para uma única base de código](platform-specific-images/mulitplatform-library-sml.png)](platform-specific-images/multiplatform-library.png#lightbox)

3. Insira um **nome** e uma **Descrição**e escolha a **plataforma específica**:

    [![Configurar a biblioteca específica da plataforma para iOS e Android](platform-specific-images/specific-configure-sml.png)](platform-specific-images/specific-configure.png#lightbox)

4. Conclua o assistente. Os projetos a seguir são adicionados à solução:

    - **Projeto Android** – o código específico do Android pode, opcionalmente, ser adicionado a este projeto.
    - **projeto do IOS** – o código específico do IOS pode opcionalmente ser adicionado a este projeto.
    - **Projeto NuGet** – nenhum código é adicionado a este projeto. Ele faz referência a outros projetos e contém a configuração de metadados para a saída do pacote NuGet.
    - **Projeto compartilhado** – o código comum deve ser adicionado a este projeto, incluindo o código específico da plataforma dentro de `#if` diretivas do compilador.

5. Clique com o botão direito do mouse no projeto NuGet e escolha **Opções**e abra a seção **pacote NuGet > metadados** e insira os [metadados necessários](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md) (bem como quaisquer metadados opcionais):

    [![Inserir metadados necessários](platform-specific-images/specific-metadata-sml.png)](platform-specific-images/specific-metadata.png#lightbox)

6. Também na janela **Opções do projeto** , abra a seção **assemblies de referência** e escolha a quais perfis PCL a biblioteca compartilhada dará suporte por meio de "Bait e switch":

    ![Também na janela Opções do projeto, abra a seção assemblies de referência e escolha a quais perfis PCL a biblioteca compartilhada dará suporte por meio do Bait e do comutador](platform-specific-images/specific-reference-assemblies.png)

    > [!NOTE]
    > "Bait e switch" significa que os assemblies PCL conterão apenas a API exposta pela biblioteca (ele não pode conter o código específico da plataforma). Quando o NuGet é adicionado a um projeto Xamarin, as bibliotecas compartilhadas serão compiladas no PCL, mas os assemblies específicos da plataforma conterão o código que é realmente usado pelo projeto iOS ou Android.

7. Clique com o botão direito do mouse no projeto e escolha **criar pacote NuGet** (ou compilar ou implantar a solução) e o arquivo de pacote NuGet **. nupkg** será salvo na pasta **/bin/** (debug ou Release, dependendo da configuração).

    ![O arquivo do pacote NuGet será salvo na pasta bin Debug ou Release, dependendo da configuração](platform-specific-images/create-nuget-package.png)

## <a name="verifying-the-output"></a>Verificando a saída

Os pacotes NuGet também são arquivos ZIP, portanto, é possível inspecionar a estrutura interna do pacote gerado.

Esta captura de tela mostra o conteúdo de um NuGet específico da plataforma que dá suporte a iOS e Android e tinha dois assemblies de referência selecionados:

![Arquivos contidos no pacote NuGet](platform-specific-images/nuget-output.png)

## <a name="related-links"></a>Links Relacionados

- [Guia de metadados](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)
