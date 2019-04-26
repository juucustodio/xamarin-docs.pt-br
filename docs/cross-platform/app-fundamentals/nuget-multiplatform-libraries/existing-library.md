---
title: Criando um NuGet de projetos de biblioteca existentes
description: Este documento descreve como criar um pacote do NuGet de um projeto de biblioteca existente, permitindo que o código ser compartilhado com outros desenvolvedores.
ms.prod: xamarin
ms.assetid: EDAC3E5E-DB7D-40A9-AE28-45C52ADA854E
author: asb3993
ms.author: amburns
ms.date: 04/20/2017
ms.openlocfilehash: 7f407b22d1793d585ae40aeae8c2d9b7616784e6
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61267775"
---
# <a name="creating-a-nuget-from-existing-library-projects"></a>Criando um NuGet de projetos de biblioteca existentes

Bibliotecas PCL existente ou .NET Standard podem ser transformadas em NuGets por meio de **opções de projeto** janela:

1. Clique com botão direito no projeto de biblioteca na **painel de soluções** e escolha **opções**.

2. Vá para o **pacote do NuGet > metadados** seção e insira todas a [as informações necessárias](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md) no **geral** guia:

  [![](existing-library-images/existing-metadata-sml.png "Insira os metadados necessários")](existing-library-images/existing-metadata.png#lightbox)

3. Opcionalmente, [adicionar metadados adicionais](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md) na **detalhes** guia.

4. Depois de configurar os metadados, você pode clique com botão direito no projeto e escolha **criar pacote do NuGet** e o **. nupkg** serão salvas no arquivo de pacote do NuGet a **/bin/** pasta (Debug ou Release, dependendo da configuração).

  ![](existing-library-images/create-nuget-package.png "Escolha Criar pacote do NuGet no menu de atalho")

5. Para criar o pacote do NuGet na _cada_ compilar ou implantar, vá para o **pacote do NuGet > compilar** seção e escala **criar um pacote NuGet ao compilar o projeto**:

    [![](existing-library-images/existing-tickbox-sml.png "Escala para criar um pacote do NuGet")](existing-library-images/existing-tickbox.png#lightbox)

> [!NOTE]
> Pacote de criação do NuGet pode reduzir a velocidade o processo de compilação. Se essa caixa não está marcada, ainda será possível gerar um pacote NuGet manualmente a qualquer momento no menu de contexto de projeto (mostrado na etapa 4 acima).

## <a name="verifying-the-output"></a>Verificando a saída

Pacotes do NuGet também são arquivos ZIP, portanto, é possível inspecionar a estrutura interna do pacote gerado.

Esta captura de tela mostra o conteúdo de um NuGet baseado em PCL – um único assembly PCL é incluído:

![](existing-library-images/nuget-output.png "Arquivos contidos no pacote NuGet")


## <a name="related-links"></a>Links relacionados

- [Guia de metadados](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)
