---
title: Carregar na Mac App Store
description: "Este guia ensina como fazer upload de aplicativos Xamarin.Mac para publicação na Mac App Store."
ms.topic: article
ms.prod: xamarin
ms.assetid: 30cd0e47-1b2e-47ef-93f6-4bed20b15c03
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 533031016af68b1eb95a198e28ac40f445ff1579
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2018
---
# <a name="upload-to-mac-app-store"></a>Carregar na Mac App Store

_Este guia ensina como fazer upload de aplicativos Xamarin.Mac para publicação na Mac App Store._

Os aplicativos são enviados para aprovação da Mac App Store através do [iTunes Connect](http://itunesconnect.apple.com/).

1. Escolha um **Aplicativo macOS** para criar: 

    [ ![](uploading-images/image65.png "iTunes Connect")](uploading-images/image65.png)

2. Insira o nome do aplicativo e outros detalhes. O desenvolvedor pode escolher apenas de uma **ID do pacote** já existente, que foi criada anteriormente: 

    [ ![](uploading-images/image66.png "Selecionando a ID do pacote")](uploading-images/image66.png)

3. Selecione a data de disponibilidade e o preço. Independentemente da data de disponibilidade selecionada pelo desenvolvedor, o aplicativo só ficará disponível para venda depois que for aprovado. Esse valor poderá ser definido no futuro, se o desenvolvedor desejar ter mais controle sobre a data de disponibilidade real: 

    [ ![](uploading-images/image67.png "Configurando o preço e data disponíveis")](uploading-images/image67.png)

4. Insira as informações do aplicativo, incluindo a categoria da App Store a que ele pertence: 

    [ ![](uploading-images/image68.png "Inserindo as informações do aplicativo")](uploading-images/image68.png) 

    Selecione as classificações que se aplicam: 

    [ ![](uploading-images/image69.png "Configurando as classificações do aplicativo")](uploading-images/image69.png) 

    URLs de contato, descrição e palavras-chave: 

    [ ![](uploading-images/image70.png "Editando a descrição, as palavras-chave e as URLs de contato")](uploading-images/image70.png) 

    Informações de contato e avisos para os revisores da App Store: 

    [ ![](uploading-images/image71.png "Editando as informações de contato e os conselhos para os revisores da App Store")](uploading-images/image71.png) 

    E finalmente, capturas de tela: 

    [ ![](uploading-images/image72.png "Adicionando as capturas de tela necessárias")](uploading-images/image72.png) 

    As capturas de tela devem ser no formato JPG, TIF ou PNG, com 1280 x 800, 1440 x 900, 2880 x 1800 ou 2560 x 1600 pixels de tamanho. Pressione **Salvar** para concluir.

5. As informações do aplicativo são mostradas para análise. Clique em **Exibir detalhes** para alterar o status: 

    [ ![](uploading-images/image73.png "Exibindo os detalhes do aplicativo")](uploading-images/image73.png)

6. Na exibição de detalhes, clique em Pronto para Carregar Binário para enviar o arquivo do pacote de aplicativos: 

    [ ![](uploading-images/image74.png "Selecionando binário pronto para carregar")](uploading-images/image74.png)

7. Responda à pergunta de criptografia: 

    [ ![](uploading-images/image75.png "Respondendo à pergunta de criptografia")](uploading-images/image75.png)

8. O site informará quando estiver pronto para aceitar o arquivo do pacote de aplicativos: 

    [ ![](uploading-images/image76.png "A notificação de aceitação")](uploading-images/image76.png)

9. Inicie o Application Loader e certifique-se de estar conectado com a ID da Apple.
Escolha **Entregar seu aplicativo** para continuar: 

    [ ![](uploading-images/image77.png "A Interface do carregador de aplicativos")](uploading-images/image77.png)

10. Selecione na lista de aplicativos com status **Pronto para Carregar Binário** e clique em **Avançar**: 

    [ ![](uploading-images/image78.png "Selecionando o aplicativo a ser carregado")](uploading-images/image78.png)

11. Examine os metadados do aplicativo e clique em **Escolher...** para localizar o arquivo do pacote: 

    [ ![](uploading-images/image79.png "Revisando os metadados do aplicativo")](uploading-images/image79.png)

12. Localize o arquivo do pacote que foi compilado no Visual Studio para Mac usando a configuração de build da App Store: 

    [ ![](uploading-images/image80.png "Selecionando o arquivo a ser carregado")](uploading-images/image80.png)

13. Pressione **Enviar**: 

    [ ![](uploading-images/image81.png "Enviando o aplicativo")](uploading-images/image81.png)

14. O pacote será validado e quaisquer erros serão informados. Corrija esses erros e carregue novamente. Quando o upload for concluído com êxito, o aplicativo será automaticamente enviado para análise pela equipe do App Store: 

    [ ![](uploading-images/image82.png "Um exemplo de erros de upload")](uploading-images/image82.png)

Quando o aplicativo for aprovado, estará disponível para download ou compra na Mac App Store.

## <a name="related-links"></a>Links relacionados

- [Instalação](~//mac/get-started/installation.md)
- [Amostra do Hello, Mac](~//mac/get-started/hello-mac.md)
- [Distribua aplicativos na Mac App Store](https://developer.apple.com/devcenter/mac/checklist/)
- [Guia de ferramentas: assinatura de código do aplicativo](https://developer.apple.com/library/mac/#documentation/ToolsLanguages/Conceptual/OSXWorkflowGuide/CodeSigning/CodeSigning.html)
- [ID de Desenvolvedor e GateKeeper](https://developer.apple.com/resources/developer-id/)
