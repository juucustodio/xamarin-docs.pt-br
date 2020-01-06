---
title: Carregar na Mac App Store
description: Este documento descreve como usar o iTunes Connect para fazer upload de um aplicativo do Xamarin.Mac na Mac App Store. Ele aborda as informações exigidas pelo iTunes Connect para concluir o processo.
ms.prod: xamarin
ms.assetid: 30cd0e47-1b2e-47ef-93f6-4bed20b15c03
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: e2b25468255ff84a3fe79ed4fea913e04bf88687
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75489356"
---
# <a name="upload-to-mac-app-store"></a>Carregar na Mac App Store

_Este guia ensina como fazer upload de aplicativos Xamarin.Mac para publicação na Mac App Store._

Os aplicativos são enviados para aprovação da Mac App Store através do [iTunes Connect](https://itunesconnect.apple.com/). Você também precisará da ferramenta [**transportadora**](https://apps.apple.com/us/app/transporter/id1450874784?mt=12) da loja de aplicativos.

1. Escolha um **Aplicativo macOS** para criar: 

    [![](uploading-images/image65.png "iTunes Connect")](uploading-images/image65.png#lightbox)

2. Insira o nome do aplicativo e outros detalhes. O desenvolvedor pode escolher apenas de uma **ID do pacote** já existente, que foi criada anteriormente: 

    [![](uploading-images/image66.png "Selecting the bundle ID")](uploading-images/image66.png#lightbox)

3. Selecione a data de disponibilidade e o preço. Independentemente da data de disponibilidade selecionada pelo desenvolvedor, o aplicativo só ficará disponível para venda depois que for aprovado. Esse valor poderá ser definido no futuro, se o desenvolvedor desejar ter mais controle sobre a data de disponibilidade real: 

    [![](uploading-images/image67.png "Setting the available date and price")](uploading-images/image67.png#lightbox)

4. Insira as informações do aplicativo, incluindo a categoria da App Store a que ele pertence: 

    [![](uploading-images/image68.png "Entering the app information")](uploading-images/image68.png#lightbox) 

    Selecione as classificações que se aplicam: 

    [![](uploading-images/image69.png "Setting the app ratings")](uploading-images/image69.png#lightbox) 

    URLs de contato, descrição e palavras-chave: 

    [![](uploading-images/image70.png "Editing the Description, keywords and contact URLs")](uploading-images/image70.png#lightbox) 

    Informações de contato e avisos para os revisores da App Store: 

    [![](uploading-images/image71.png "Editing the contact information and advice for the App Store reviewers")](uploading-images/image71.png#lightbox) 

    E finalmente, capturas de tela: 

    [![](uploading-images/image72.png "Adding the required screenshots")](uploading-images/image72.png#lightbox) 

    As capturas de tela devem ser no formato JPG, TIF ou PNG, com 1280 x 800, 1440 x 900, 2880 x 1800 ou 2560 x 1600 pixels de tamanho. Pressione **Salvar** para concluir.

5. As informações do aplicativo são mostradas para análise. Clique em **Exibir detalhes** para alterar o status: 

    [![](uploading-images/image73.png "Viewing the app details")](uploading-images/image73.png#lightbox)

6. Na exibição de detalhes, clique em Pronto para Carregar Binário para enviar o arquivo do pacote de aplicativos: 

    [![](uploading-images/image74.png "Selecting Ready to Upload Binary")](uploading-images/image74.png#lightbox)

7. Responda à pergunta de criptografia: 

    [![](uploading-images/image75.png "Answering the cryptography question")](uploading-images/image75.png#lightbox)

8. O site informará quando estiver pronto para aceitar o arquivo do pacote de aplicativos: 

    [![](uploading-images/image76.png "The acceptance notification")](uploading-images/image76.png#lightbox)

9. Inicie o **transportador** e faça logon com sua ID da Apple e escolha **Adicionar aplicativo**:

    [![](uploading-images/transporter01-sml.png "The Application Loader interface")](uploading-images/transporter01.png#lightbox)

    Siga as instruções para carregar o pacote do aplicativo no iTunes Connect.

    > [!NOTE]
    > O [**transportador**](https://apps.apple.com/us/app/transporter/id1450874784?mt=12) substitui a ferramenta **carregador de aplicativos** usada com o Xcode 10 e anterior.
    > O carregador de aplicativos não está mais disponível no Xcode 11 ou mais recente.

Quando o aplicativo for aprovado, estará disponível para download ou compra na Mac App Store.

## <a name="related-links"></a>Links relacionados

- [Instalação](~//mac/get-started/installation.md)
- [Amostra do Hello, Mac](~/mac/get-started/hello-mac.md)
- [Distribua aplicativos na Mac App Store](https://developer.apple.com/devcenter/mac/checklist/)
- [Guia de ferramentas: assinatura de código do aplicativo](https://developer.apple.com/library/mac/#documentation/ToolsLanguages/Conceptual/OSXWorkflowGuide/CodeSigning/CodeSigning.html)
- [ID de Desenvolvedor e GateKeeper](https://developer.apple.com/resources/developer-id/)
