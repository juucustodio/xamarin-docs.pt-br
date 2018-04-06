---
title: Conectado serviços no Visual Studio para Mac
description: Adicione o armazenamento de dados do Azure, autenticação e notificações por push a aplicativos móveis do Visual Studio para Mac
ms.prod: xamarin
ms.assetid: ADDFB3A5-DB6A-417C-8ACE-33D107FB75C2
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: b9aaa197ccf01c59d6e4bbb0476d10295a108f89
ms.sourcegitcommit: 66807f8927d472fbfd0ff8bc77cea9b37e7b9a4f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2018
---
# <a name="connected-services-walkthrough"></a>Instruções passo a passo de serviços conectados

O fluxo de trabalho de serviços conectados traz o fluxo de trabalho do portal do Azure para Visual Studio para Mac, para que você não precisa sair de seu projeto para adicionar serviços.

Este passo a passo mostra como adicionar um serviço de back-end do Azure, que oferece armazenamento de dados em nuvem, autenticação e notificações por push para um aplicativo de biblioteca de classe portátil xamarin. Forms (PCL) de plataforma cruzada.


1.  Iniciar clicando duas vezes no **serviços conectados** nó na solução, o que abrirá o **serviços galeria**.
  Esta é uma lista de todos os serviços disponíveis para este tipo de aplicativo. Selecione um serviço (como **móvel back-end com o serviço de aplicativo do Azure**) clicando nele.

  [![](connected-services-images/image001-sml.png "Conectado o nó de serviços no Visual Studio para Mac")](connected-services-images/image001.png#lightbox)

2. A página de detalhes do serviço tem uma descrição do serviço e as dependências serão instalados.
  Clique o **adicionar** botão para adicionar as dependências para o aplicativo:

  [![](connected-services-images/image002-sml.png "Móvel back-end com o Azure")](connected-services-images/image002.png#lightbox)

3. As dependências precisam ser adicionados para o PCL e projetos específicos de plataforma para trabalhar.
  Selecione as caixas de seleção para adicionar o serviço para cada projeto que fará referência (direta ou indiretamente):

  [![](connected-services-images/image003-sml.png "Verifique todos os projetos que devem fazer referência de serviço")](connected-services-images/image003.png#lightbox)

4. Escolha **aceitar** no **a aceitação da licença** caixas de diálogo para os pacotes do NuGet.
  Pode haver duas caixas de diálogo para aceitar, um para o MobileClient e dependências e outro para SQLiteStore, que é necessário para sincronização de dados offline:

  [![](connected-services-images/image004-sml.png "Aceite os contratos de licença")](connected-services-images/image004.png#lightbox)

  ![](connected-services-images/image005.png "Janela de aceitação da licença")

5. Depois de adicionar as dependências, você deverá fazer logon com a conta que você deseja usar para se comunicar com o Azure.
  Se você já está conectado com uma ID da Microsoft, o Visual Studio para Mac tentará buscar suas assinaturas do Azure e quaisquer serviços de aplicativo associados a eles. Se você não tem assinaturas, você pode adicionar um por se inscrever para uma avaliação gratuita ou comprar um plano de assinatura no portal do Azure.

6. Selecione um aplicativo de serviço na lista. Isso preencherá o código de modelo para o `MobileServiceClient` objeto com a URL correspondente do serviço de aplicativo no Azure:

  [![](connected-services-images/image006-sml.png "Selecione o serviço de aplicativo na lista")](connected-services-images/image006.png#lightbox)

  Se não houver nenhum serviço listado, clique no **novo** botão (consulte a etapa 9).

7. Copie o código de modelo para o `MobileServiceClient` no PCL. O aplicativo de local do arquivo no não são importantes, como há apenas uma instância dele.
  A abordagem recomendada é criar um `AzureService` classe manipula todas as interações do Azure e usa o `MobileServiceClient`:

  ![](connected-services-images/image007.png "Copie o código de configuração para o aplicativo")

8. Siga a documentação em **próximas etapas** para adicionar dados, sincronização offline, autenticação e notificações por push para seu aplicativo:

  [![](connected-services-images/image008-sml.png "Examine as próximas instruções de etapas")](connected-services-images/image008.png#lightbox)

10. Se você não tiver qualquer serviço de aplicativo existente, você pode criar novos serviços de dentro do Visual Studio para Mac.
  Clique o **novo** botão no canto inferior esquerdo da lista de serviços para abrir o **novo serviço de aplicativo** caixa de diálogo:

  [![](connected-services-images/image009-sml.png "Criar um novo serviço de aplicativo no Visual Studio para Mac")](connected-services-images/image009.png#lightbox)

Um novo serviço requer os seguintes parâmetros:

-   **Nome do serviço de aplicativo** – nome/id exclusiva para o plano
-   **Assinatura** – a assinatura que você deseja usar para pagar pelo serviço
-   **Grupo de recursos** – um modo ou organizar todos os seus recursos do Azure para um projeto. Opção de usar existente ou crie um novo. Se esse for o primeiro serviço do Azure, crie um novo.
-   **Plano de serviço** – determina o local e o custo de todos os recursos que usá-lo. Opção de usar existente ou crie um novo. Se esse for o primeiro serviço do Azure, use o padrão ou criar um novo na camada gratuita (F1).

Visite o [documentação do serviço de aplicativo do Azure](https://docs.microsoft.com/azure/app-service/) para obter mais informações


## <a name="related-links"></a>Links relacionados

- [Serviço de Aplicativo do Azure](https://docs.microsoft.com/en-us/azure/app-service/)
- [Notas de Versão](https://developer.xamarin.com/releases/studio/xamarin.studio_6.2/xamarin.studio_6.2/#Connected_Services)
