---
title: Criando um NuGet de projetos de biblioteca existente
ms.topic: article
ms.prod: xamarin
ms.assetid: EDAC3E5E-DB7D-40A9-AE28-45C52ADA854E
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 04/20/2017
ms.openlocfilehash: 44a97114e7d325a1fa196d2c9828855ad1a30c94
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="creating-a-nuget-from-existing-library-projects"></a>Criando um NuGet de projetos de biblioteca existente

Bibliotecas PCL existente ou .NET padrão podem ser transformadas em NuGets por meio de **opções de projeto** janela:

1. Clique com botão direito no projeto de biblioteca no **solução preenchimento** e escolha **opções**.

2. Vá para o **pacote NuGet > metadados** seção e insira todas a [informações necessárias](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md) no **geral** guia:

  [ ![](existing-library-images/existing-metadata-sml.png "Insira os metadados necessários")](existing-library-images/existing-metadata.png)

3. Opcionalmente, [adicionar metadados adicionais](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md) no **detalhes** guia.

4. Depois que os metadados estiver configurado, você pode com o botão direito no projeto e escolha **criar pacote do NuGet** e **nupkg** arquivo de pacote do NuGet serão salvas no **/bin/** pasta (Debug ou Release, dependendo da configuração).

  ![](existing-library-images/create-nuget-package.png "Escolha Criar pacote do NuGet no menu de atalho")

5. Para criar o pacote do NuGet em _cada_ compilar ou implantar, vá para o **pacote NuGet > compilar** seção e escala **criar um pacote NuGet ao criar o projeto**:

    [ ![](existing-library-images/existing-tickbox-sml.png "Escala para criar um pacote do NuGet")](existing-library-images/existing-tickbox.png)

> [!NOTE]
> Compilando o NuGet pacote pode diminuir o processo de compilação. Se essa caixa não está marcada, ainda será possível gerar um pacote NuGet manualmente a qualquer momento no menu de contexto de projeto (mostrado na etapa 4 acima).

## <a name="verifying-the-output"></a>Verificando a saída

Pacotes do NuGet também são arquivos ZIP, portanto, é possível inspecionar a estrutura interna do pacote gerado.

Esta captura de tela mostra o conteúdo de um NuGet com base em PCL – um único assembly PCL está incluído:

![](existing-library-images/nuget-output.png "Arquivos contidos no pacote do NuGet")


## <a name="related-links"></a>Links relacionados

- [Guia de metadados](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)
