---
title: Carregar na Mac App Store
description: Este documento descreve como usar o iTunes Connect para fazer upload de um aplicativo do Xamarin.Mac na Mac App Store. Ele aborda as informações exigidas pelo iTunes Connect para concluir o processo.
ms.prod: xamarin
ms.assetid: 30cd0e47-1b2e-47ef-93f6-4bed20b15c03
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 03/14/2017
ms.openlocfilehash: b0bb58fee1c9eaeee391b3a838b3fa2921e97a57
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70284263"
---
# <a name="upload-to-mac-app-store"></a>Carregar na Mac App Store

_Este guia ensina como fazer upload de aplicativos Xamarin.Mac para publicação na Mac App Store._

Os aplicativos são enviados para aprovação da Mac App Store através do [iTunes Connect](http://itunesconnect.apple.com/).

1. Escolha um **Aplicativo macOS** para criar: 

    [![](uploading-images/image65.png "iTunes Connect")](uploading-images/image65.png#lightbox)

2. Insira o nome do aplicativo e outros detalhes. O desenvolvedor pode escolher apenas de uma **ID do pacote** já existente, que foi criada anteriormente: 

    [![](uploading-images/image66.png "Selecionando a ID do pacote")](uploading-images/image66.png#lightbox)

3. Selecione a data de disponibilidade e o preço. Independentemente da data de disponibilidade selecionada pelo desenvolvedor, o aplicativo só ficará disponível para venda depois que for aprovado. Esse valor poderá ser definido no futuro, se o desenvolvedor desejar ter mais controle sobre a data de disponibilidade real: 

    [![](uploading-images/image67.png "Configurando o preço e data disponíveis")](uploading-images/image67.png#lightbox)

4. Insira as informações do aplicativo, incluindo a categoria da App Store a que ele pertence: 

    [![](uploading-images/image68.png "Inserindo as informações do aplicativo")](uploading-images/image68.png#lightbox) 

    Selecione as classificações que se aplicam: 

    [![](uploading-images/image69.png "Configurando as classificações do aplicativo")](uploading-images/image69.png#lightbox) 

    URLs de contato, descrição e palavras-chave: 

    [![](uploading-images/image70.png "Editando a descrição, as palavras-chave e as URLs de contato")](uploading-images/image70.png#lightbox) 

    Informações de contato e avisos para os revisores da App Store: 

    [![](uploading-images/image71.png "Editando as informações de contato e os conselhos para os revisores da App Store")](uploading-images/image71.png#lightbox) 

    E finalmente, capturas de tela: 

    [![](uploading-images/image72.png "Adicionando as capturas de tela necessárias")](uploading-images/image72.png#lightbox) 

    As capturas de tela devem ser no formato JPG, TIF ou PNG, com 1280 x 800, 1440 x 900, 2880 x 1800 ou 2560 x 1600 pixels de tamanho. Pressione **Salvar** para concluir.

5. As informações do aplicativo são mostradas para análise. Clique em **Exibir detalhes** para alterar o status: 

    [![](uploading-images/image73.png "Exibindo os detalhes do aplicativo")](uploading-images/image73.png#lightbox)

6. Na exibição de detalhes, clique em Pronto para Carregar Binário para enviar o arquivo do pacote de aplicativos: 

    [![](uploading-images/image74.png "Selecionando Pronto para Carregar Binário")](uploading-images/image74.png#lightbox)

7. Responda à pergunta de criptografia: 

    [![](uploading-images/image75.png "Respondendo à pergunta de criptografia")](uploading-images/image75.png#lightbox)

8. O site informará quando estiver pronto para aceitar o arquivo do pacote de aplicativos: 

    [![](uploading-images/image76.png "A notificação de aceitação")](uploading-images/image76.png#lightbox)

9. Inicie o Application Loader e certifique-se de estar conectado com a ID da Apple.
Escolha **Entregar seu aplicativo** para continuar: 

    [![](uploading-images/image77.png "A Interface do carregador de aplicativos")](uploading-images/image77.png#lightbox)

10. Selecione na lista de aplicativos com status **Pronto para Carregar Binário** e clique em **Avançar**: 

    [![](uploading-images/image78.png "Selecionando o aplicativo a ser carregado")](uploading-images/image78.png#lightbox)

11. Examine os metadados do aplicativo e clique em **Escolher...** para localizar o arquivo do pacote: 

    [![](uploading-images/image79.png "Examinando os metadados do aplicativo")](uploading-images/image79.png#lightbox)

12. Localize o arquivo do pacote que foi compilado no Visual Studio para Mac usando a configuração de build da App Store: 

    [![](uploading-images/image80.png "Selecionando o arquivo a ser carregado")](uploading-images/image80.png#lightbox)

13. Pressione **Enviar**: 

    [![](uploading-images/image81.png "Enviando o aplicativo")](uploading-images/image81.png#lightbox)

14. O pacote será validado e quaisquer erros serão informados. Corrija esses erros e carregue novamente. Quando o upload for concluído com êxito, o aplicativo será automaticamente enviado para análise pela equipe do App Store: 

    [![](uploading-images/image82.png "Um exemplo de erros de upload")](uploading-images/image82.png#lightbox)

Quando o aplicativo for aprovado, estará disponível para download ou compra na Mac App Store.

## <a name="related-links"></a>Links relacionados

- [Instalação](~//mac/get-started/installation.md)
- [Amostra do Hello, Mac](~//mac/get-started/hello-mac.md)
- [Distribua aplicativos na Mac App Store](https://developer.apple.com/devcenter/mac/checklist/)
- [Guia de ferramentas: Assinando o código do seu aplicativo](https://developer.apple.com/library/mac/#documentation/ToolsLanguages/Conceptual/OSXWorkflowGuide/CodeSigning/CodeSigning.html)
- [ID de Desenvolvedor e GateKeeper](https://developer.apple.com/resources/developer-id/)
