---
title: Criando um NuGet de projetos de biblioteca existentes
description: Este documento descreve como criar um pacote NuGet de um projeto de biblioteca existente, permitindo que o código seja compartilhado com outros desenvolvedores.
ms.prod: xamarin
ms.assetid: EDAC3E5E-DB7D-40A9-AE28-45C52ADA854E
author: davidortinau
ms.author: daortin
ms.date: 04/20/2017
ms.openlocfilehash: 058904236e100a670e8a38417dcaec805178d6d2
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86936782"
---
# <a name="creating-a-nuget-from-existing-library-projects"></a>Criando um NuGet de projetos de biblioteca existentes

As bibliotecas PCL ou .NET Standard existentes podem ser transformadas em NuGets por meio da janela **Opções do projeto** :

1. Clique com o botão direito do mouse no projeto de biblioteca no **painel de soluções** e escolha **Opções**.

2. Vá para a seção **pacote NuGet > metadados** e insira todas as [informações necessárias](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md) na guia **geral** :

   [![Inserir metadados necessários](existing-library-images/existing-metadata-sml.png)](existing-library-images/existing-metadata.png#lightbox)

3. Opcionalmente, [Adicione metadados adicionais](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md) na guia **detalhes** .

4. Depois que os metadados são configurados, você pode clicar com o botão direito do mouse no projeto e escolher **criar pacote NuGet** e o arquivo de pacote NuGet **. nupkg** será salvo na pasta **/bin/** (debug ou Release, dependendo da configuração).

   ![Escolha Criar pacote NuGet no menu do botão direito do mouse](existing-library-images/create-nuget-package.png)

5. Para criar o pacote NuGet em _cada_ compilação ou implantação, vá para o **pacote NuGet >** seção de compilação e tique **criar um pacote NuGet ao compilar o projeto**:

    [![Tique para criar um pacote NuGet](existing-library-images/existing-tickbox-sml.png)](existing-library-images/existing-tickbox.png#lightbox)

> [!NOTE]
> A criação do pacote NuGet pode retardar o processo de compilação. Se essa caixa não estiver marcada, você ainda poderá gerar um pacote NuGet manualmente a qualquer momento no menu de contexto do projeto (mostrado na etapa 4 acima).

## <a name="verifying-the-output"></a>Verificando a saída

Os pacotes NuGet também são arquivos ZIP, portanto, é possível inspecionar a estrutura interna do pacote gerado.

Esta captura de tela mostra o conteúdo de um NuGet baseado em PCL – apenas um único assembly PCL está incluído:

![Arquivos contidos no pacote NuGet](existing-library-images/nuget-output.png)

## <a name="related-links"></a>Links Relacionados

- [Guia de metadados](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)
