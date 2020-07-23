---
title: Carregar na Mac App Store
description: Este documento descreve como usar o iTunes Connect para fazer upload de um aplicativo do Xamarin.Mac na Mac App Store. Ele aborda as informações exigidas pelo iTunes Connect para concluir o processo.
ms.prod: xamarin
ms.assetid: 30cd0e47-1b2e-47ef-93f6-4bed20b15c03
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 57591a6e9fcaa3c8271ab27756160ee1c46a4fa3
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86935922"
---
# <a name="upload-to-mac-app-store"></a>Carregar na Mac App Store

_Este guia ensina como fazer upload de aplicativos Xamarin.Mac para publicação na Mac App Store._

Os aplicativos são enviados para aprovação da Mac App Store através do [iTunes Connect](https://itunesconnect.apple.com/). Você também precisará da ferramenta [**Transportador**](https://apps.apple.com/us/app/transporter/id1450874784?mt=12) da App Store.

1. Escolha um **Aplicativo macOS** para criar:

    [![iTunes Connect](uploading-images/image65.png)](uploading-images/image65.png#lightbox)

2. Insira o nome do aplicativo e outros detalhes. O desenvolvedor pode escolher apenas de uma **ID do pacote** já existente, que foi criada anteriormente:

    [![Selecionar a ID do pacote](uploading-images/image66.png)](uploading-images/image66.png#lightbox)

3. Selecione a data de disponibilidade e o preço. Independentemente da data de disponibilidade selecionada pelo desenvolvedor, o aplicativo só ficará disponível para venda depois que for aprovado. Esse valor poderá ser definido no futuro, se o desenvolvedor desejar ter mais controle sobre a data de disponibilidade real:

    [![Configurar o preço e a data disponíveis](uploading-images/image67.png)](uploading-images/image67.png#lightbox)

4. Insira as informações do aplicativo, incluindo a categoria da App Store a que ele pertence:

    [![Inserir as informações do aplicativo](uploading-images/image68.png)](uploading-images/image68.png#lightbox)

    Selecione as classificações que se aplicam:

    [![Configurar as classificações do aplicativo](uploading-images/image69.png)](uploading-images/image69.png#lightbox)

    URLs de contato, descrição e palavras-chave:

    [![Editar a descrição, as palavras-chave e as URLs de contato](uploading-images/image70.png)](uploading-images/image70.png#lightbox)

    Informações de contato e avisos para os revisores da App Store:

    [![Editar as informações de contato e os conselhos para os revisores da App Store](uploading-images/image71.png)](uploading-images/image71.png#lightbox)

    E finalmente, capturas de tela:

    [![Adicionar as capturas de tela necessárias](uploading-images/image72.png)](uploading-images/image72.png#lightbox)

    As capturas de tela devem ser no formato JPG, TIF ou PNG, com 1280 x 800, 1440 x 900, 2880 x 1800 ou 2560 x 1600 pixels de tamanho. Pressione **Salvar** para concluir.

5. As informações do aplicativo são mostradas para análise. Clique em **Exibir detalhes** para alterar o status:

    [![Exibir os detalhes do aplicativo](uploading-images/image73.png)](uploading-images/image73.png#lightbox)

6. Na exibição de detalhes, clique em Pronto para Carregar Binário para enviar o arquivo do pacote de aplicativos:

    [![Selecionar Pronto para Carregar Binário](uploading-images/image74.png)](uploading-images/image74.png#lightbox)

7. Responda à pergunta de criptografia:

    [![Responder à pergunta de criptografia](uploading-images/image75.png)](uploading-images/image75.png#lightbox)

8. O site informará quando estiver pronto para aceitar o arquivo do pacote de aplicativos:

    [![A notificação de aceitação](uploading-images/image76.png)](uploading-images/image76.png#lightbox)

9. Inicie **Transportador** e faça logon com sua ID da Apple e, em seguida, escolha **ADICIONAR APLICATIVO**:

    [![A interface do Carregador de Aplicativos](uploading-images/transporter01-sml.png)](uploading-images/transporter01.png#lightbox)

    Siga as instruções para carregar o pacote do aplicativo no iTunes Connect.

    > [!NOTE]
    > O [**Transportador**](https://apps.apple.com/us/app/transporter/id1450874784?mt=12) substitui a ferramenta **Carregador de Aplicativos** que foi usada com o Xcode 10 e anterior.
    > O Carregador de Aplicativos não está mais disponível no Xcode 11 ou mais recente.

Quando o aplicativo for aprovado, estará disponível para download ou compra na Mac App Store.

## <a name="related-links"></a>Links relacionados

- [Instalação](~//mac/get-started/installation.md)
- [Olá, exemplo de Mac](~/mac/get-started/hello-mac.md)
- [Distribua aplicativos na Mac App Store](https://developer.apple.com/devcenter/mac/checklist/)
- [Guia de ferramentas: assinatura de código do aplicativo](https://developer.apple.com/library/mac/#documentation/ToolsLanguages/Conceptual/OSXWorkflowGuide/CodeSigning/CodeSigning.html)
- [ID de Desenvolvedor e GateKeeper](https://developer.apple.com/developer-id/)
