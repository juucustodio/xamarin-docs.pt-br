---
title: Criando novos projetos de biblioteca específicos da plataforma para NuGet
description: Este documento descreve como criar um único pacote NuGet que contém o código específico da plataforma para várias plataformas.
ms.prod: xamarin
ms.assetid: D8BC4906-805F-4AFB-8D1A-88B7BF87E17F
author: conceptdev
ms.author: crdun
ms.date: 03/23/2017
ms.openlocfilehash: d3f756b1a551c7b6bcbe48129235d537312edff6
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70282144"
---
# <a name="creating-new-platform-specific-library-projects-for-nuget"></a>Criando novos projetos de biblioteca específicos da plataforma para NuGet

Os projetos de biblioteca multiplataforma que visam plataformas específicas, como iOS e Android, funcionam melhor com projetos compartilhados.

O NuGet pode conter código específico para iOS e Android, bem como o código .NET comum para ambos.

Vários assemblies são criados e incorporados em um único pacote NuGet. Os padrões do NuGet garantem que o pacote possa ser adicionado a todos os tipos de projeto com suporte, como o Xamarin. iOS e os projetos Android.

## <a name="steps-to-create-a-cross-platform-library-nuget"></a>Etapas para criar um NuGet de biblioteca de plataforma cruzada

1. Selecione **arquivo > nova solução** (ou clique com o botão direito do mouse em uma solução existente e escolha **Adicionar > novo projeto**).

2. Escolha **biblioteca multiplataforma** na seção **biblioteca de > de várias plataformas** :

    [![](platform-specific-images/mulitplatform-library-sml.png "Configurar a biblioteca de várias plataformas para uma única base de código")](platform-specific-images/multiplatform-library.png#lightbox)

3. Insira um **nome** e uma **Descrição**e escolha a **plataforma específica**:

    [![](platform-specific-images/specific-configure-sml.png "Configurar a biblioteca específica da plataforma para iOS e Android")](platform-specific-images/specific-configure.png#lightbox)

4. Conclua o assistente. Os projetos a seguir são adicionados à solução:

    - **Projeto Android** – o código específico do Android pode, opcionalmente, ser adicionado a este projeto.
    - **projeto do IOS** – o código específico do IOS pode opcionalmente ser adicionado a este projeto.
    - **Projeto NuGet** – nenhum código é adicionado a este projeto. Ele faz referência a outros projetos e contém a configuração de metadados para a saída do pacote NuGet.
    - **Projeto compartilhado** – o código comum deve ser adicionado a este projeto, incluindo o código específico da `#if` plataforma dentro de diretivas do compilador.

5. Clique com o botão direito do mouse no projeto NuGet e escolha **Opções**e abra a seção **pacote NuGet > metadados** e insira os [metadados necessários](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md) (bem como quaisquer metadados opcionais):

    [![](platform-specific-images/specific-metadata-sml.png "Inserir metadados necessários")](platform-specific-images/specific-metadata.png#lightbox)

6. Também na janela **Opções do projeto** , abra a seção **assemblies de referência** e escolha a quais perfis PCL a biblioteca compartilhada dará suporte por meio de "Bait e switch":

    ![](platform-specific-images/specific-reference-assemblies.png "Também na janela Opções do projeto, abra a seção assemblies de referência e escolha a quais perfis PCL a biblioteca compartilhada dará suporte por meio do Bait e do comutador")

    > [!NOTE]
    > "Bait e switch" significa que os assemblies PCL conterão apenas a API exposta pela biblioteca (ele não pode conter o código específico da plataforma). Quando o NuGet é adicionado a um projeto Xamarin, as bibliotecas compartilhadas serão compiladas no PCL, mas os assemblies específicos da plataforma conterão o código que é realmente usado pelo projeto iOS ou Android.

7. Clique com o botão direito do mouse no projeto e escolha **criar pacote NuGet** (ou compilar ou implantar a solução) e o arquivo de pacote NuGet **. nupkg** será salvo na pasta **/bin/** (debug ou Release, dependendo da configuração).

    ![](platform-specific-images/create-nuget-package.png "O arquivo do pacote NuGet será salvo na pasta bin Debug ou Release, dependendo da configuração")


## <a name="verifying-the-output"></a>Verificando a saída

Os pacotes NuGet também são arquivos ZIP, portanto, é possível inspecionar a estrutura interna do pacote gerado.

Esta captura de tela mostra o conteúdo de um NuGet específico da plataforma que dá suporte a iOS e Android e tinha dois assemblies de referência selecionados:

![](platform-specific-images/nuget-output.png "Arquivos contidos no pacote NuGet")


## <a name="related-links"></a>Links relacionados

- [Guia de metadados](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)
