---
title: Certificados e identificadores no Xamarin.Mac
description: Esse guia lhe orienta durante a criação dos Certificados e Identificadores que serão necessários para publicar um aplicativo Xamarin.Mac.
ms.prod: xamarin
ms.assetid: 393d0066-7f6f-4ac3-a48d-4b5db65bc4cd
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 12/17/2019
ms.openlocfilehash: 2b2bfe9925a99c2ba7f1366ea28d5c72e2e1da88
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76725535"
---
# <a name="certificates-and-identifiers-in-xamarinmac"></a>Certificados e identificadores no Xamarin.Mac

_Esse guia lhe orienta durante a criação dos Certificados e Identificadores que serão necessários para publicar um aplicativo Xamarin.Mac._

## <a name="setup"></a>Configuração

Visite o [Apple Developer Member Center (Central de Associados do Desenvolvedor da Apple)](https://developer.apple.com) para configurar o Mac para desenvolvimento. Clique no link **Conta** e entre. O menu principal é mostrado abaixo:

> [!div class="mx-imgBorder"]
> [![A Central de Membros do Desenvolvedor Apple](certificates-identifiers-images/devcenter01.png)](certificates-identifiers-images/devcenter01-large.png#lightbox)

Clique no botão **Certificados, Identificadores e Perfis** (ou no botão de adição próximo ao cabeçalho **Certificados**):

> [!div class="mx-imgBorder"]
> [![Selecione Certificados, IDs e Perfis](certificates-identifiers-images/devcenter02.png)](certificates-identifiers-images/devcenter02-large.png#lightbox)

Selecione um tipo de certificado e clique em **Continuar**:

> [!div class="mx-imgBorder"]
> [![Selecionando o link de certificados](certificates-identifiers-images/devcenter03.png)](certificates-identifiers-images/devcenter03-large.png#lightbox)

Daqui, é possível baixar os **Certificados Intermediários** (Autoridade de Certificação de Relações de Desenvolvedores em todo o mundo e Autoridade de Certificação de ID de Desenvolvedor), se necessário (o último item na parte inferior da página). No entanto, eles devem ser instalados automaticamente para o desenvolvedor pelo Xcode.

O restante desta seção guia você pelas seções relevantes para desenvolvedores de Mac:

- **Register Mac App ID (Registrar a ID do aplicativo Mac)** – O desenvolvedor precisa seguir essas etapas para cada aplicativo que grava.
- **Register macOS Systems (Registrar sistemas macOS)** – Isso só é necessário ao adicionar computadores para teste.
- **Create Certificates (Criar certificados)** – necessário somente uma vez ao definir os certificados e, posteriormente, ao renová-los.
- **Create Provisioning Profile (Criar perfil de provisionamento)** – O desenvolvedor precisa seguir essas etapas para cada novo aplicativo gravado e ao adicionar novos sistemas.

## <a name="register-mac-app-id"></a>Registrar a ID do Aplicativo Mac

Você precisa registrar uma ID do Aplicativo para cada aplicativo. Siga as etapas abaixo para criar uma entrada:

1. Pressione o botão "+" (sinal de adição) ou **Registrar uma ID do Aplicativo**:

    > [!div class="mx-imgBorder"]
    > [![Introdução a IDs do aplicativo](certificates-identifiers-images/appid01.png)](certificates-identifiers-images/appid01-large.png#lightbox)

1. Escolher **IDs do Aplicativo**

    > [!div class="mx-imgBorder"]
    > [![Introdução a IDs do aplicativo](certificates-identifiers-images/appid02.png)](certificates-identifiers-images/appid02-large.png#lightbox)

1. Insira uma **Descrição** e selecione quaisquer **Serviços de Aplicativos** que o aplicativo exigir: A plataforma deve ser **macOS**. Escolha uma **Descrição** (usada somente neste portal). Insira a **ID do Pacote**, que deve corresponder ao seu **Info.plist**. Selecione as funcionalidades exigidas pelo seu aplicativo

    > [!div class="mx-imgBorder"]
    > [![Inserindo a descrição e os serviços de aplicativos](certificates-identifiers-images/appid03.png)](certificates-identifiers-images/appid03-large.png#lightbox)

    Pressione **Continuar** para examinar suas seleções.

1. Se todas as informações estiverem corretas, clique em **Registrar** para concluir a instalação:

    > [!div class="mx-imgBorder"]
    > [![Examine os dados inseridos](certificates-identifiers-images/appid04.png)](certificates-identifiers-images/appid04-large.png#lightbox)

1. Verifique as informações e clique no botão **Submit (Enviar)**:

    > [!div class="mx-imgBorder"]
    > ![Verificar as informações](certificates-identifiers-images/appid05.png)

Alguns **App Services (Serviços de Aplicativos)** poderão exigir outras configurações (por exemplo, iCloud). Se esse for o caso, selecione a nova ID do aplicativo recém-criada e clique no botão **Edit (Editar)**:

> [!div class="mx-imgBorder"]
> [![Editar a nova ID do aplicativo](certificates-identifiers-images/appid06.png)](certificates-identifiers-images/appid06-large.png#lightbox)

Para configurar os serviços do iCloud, por exemplo, clique no botão **Editar**:

> [!div class="mx-imgBorder"]
> [![Configurando os serviços de iCloud](certificates-identifiers-images/appid07.png)](certificates-identifiers-images/appid07-large.png#lightbox)

## <a name="register-macos-devices"></a>Registrar dispositivos macOS

Para criar um perfil de provisionamento para teste, o desenvolvedor precisa ter seus computadores Mac registrados. É possível registrar um máximo de 100 computadores para teste.

1. Na Central de Desenvolvedores Mac, selecione **All (Todos)** na seção **Devices (Dispositivos)** e clique no botão **+**:

    > [!div class="mx-imgBorder"]
    > [![Adicionando um novo computador](certificates-identifiers-images/device01.png)](certificates-identifiers-images/device01-large.png#lightbox)

1. Insira um **Name (Nome)** e o **UUID** do computador para adicionar e clique no botão **Continue (Continuar)**. Examine as informações e clique no botão **Register (Registrar)**:

    > [!div class="mx-imgBorder"]
    > [![Inserindo as informações do novo computador](certificates-identifiers-images/device02.png)](certificates-identifiers-images/device02-large.png#lightbox)

1. Examine e confirme os dados inseridos:

    > [!div class="mx-imgBorder"]
    > [![Inserindo as informações do novo computador](certificates-identifiers-images/device03.png)](certificates-identifiers-images/device03-large.png#lightbox)

## <a name="create-certificates"></a>Criar certificados

Use a seção Certificados para criar vários tipos diferentes de certificados que serão usados para assinar os Aplicativos Mac:

> [!div class="mx-imgBorder"]
> [![Criando um novo certificado](certificates-identifiers-images/devcenter04.png)](certificates-identifiers-images/devcenter04-large.png#lightbox)

Há cinco tipos principais de certificado relevantes para o desenvolvimento no macOS:

- **Desenvolvimento do Mac** – opcional para o desenvolvimento geral de aplicativos, mas obrigatório se o desenvolvedor planeja usar recursos, como o iCloud ou as notificações por push. O desenvolvedor precisará de um Certificado de Desenvolvimento antes que ele possa criar perfis de provisionamento que lhe permita acessar esses recursos.
- **Distribuição de Aplicativo Mac** – o desenvolvedor precisará de um certificado para seu aplicativo e outro certificado para o instalador.
- **Distribuição de Instalador Mac** – o desenvolvedor precisará de um certificado para seu aplicativo e outro certificado para o instalador.
- **Instalador da ID de Desenvolvedor** – certificados para o instalador distribuir fora da Mac App Store.
- **Aplicativo da ID de Desenvolvedor** – certificados para o aplicativo distribuir fora da Mac App Store.

As seções a seguir fornecerão exemplos da criação de alguns desses tipos de certificado.

### <a name="mac-development-certificate"></a>Certificado de desenvolvimento do Mac

Conforme mencionado anteriormente, o Certificado de Desenvolvimento do Mac não é necessário, a menos que recursos macOS, como o iCloud ou as notificações por push, estejam em uso.

Faça o seguinte para criar um novo Certificado de Desenvolvimento:

1. Selecione o botão de opção **Mac Development (Desenvolvimento do Mac)** e clique em **Continue (Continuar)**:

    > [!div class="mx-imgBorder"]
    > [![Adicionando um certificado de desenvolvimento](certificates-identifiers-images/certif02.png)](certificates-identifiers-images/certif02-large.png#lightbox)

1. Carregue uma _solicitação de assinatura de certificado_. O arquivo de solicitação de certificado (extensão `.certSigningRequest`) será salvo localmente no Mac. Clique em **Escolher arquivo** para selecionar a solicitação de certificado e, em seguida, pressione **Continuar**.

    > [!div class="mx-imgBorder"]
    > [![Carregar uma solicitação de assinatura de certificado](certificates-identifiers-images/certif03.png)](certificates-identifiers-images/certif03-large.png#lightbox)

    Siga o link [Saiba mais >](https://help.apple.com/developer-account/#/devbfa00fef7) para obter instruções sobre como usar o **Acesso ao Conjunto de Chaves** para criar um arquivo de solicitação de certificado.

1. Pressione **Baixar** para obter o arquivo de certificado e clique duas vezes para instalá-lo:

    > [!div class="mx-imgBorder"]
    > [![Baixar o arquivo de certificado](certificates-identifiers-images/certif04.png)](certificates-identifiers-images/certif04-large.png#lightbox)

Conforme mencionado anteriormente, o certificado do Desenvolvedor nem sempre é necessário, a menos que o desenvolvedor esteja implementando recursos macOS, como o iCloud e as notificações por push. Ele também é necessário para criar um **Development Provisioning Profile (Perfil de Provisionamento de Desenvolvimento)**, que será necessário para testar os aplicativos da Mac App Store.

### <a name="mac-app-store-certificates"></a>Certificados da Mac App Store

Para liberar um aplicativo na App Store, você precisará de dois certificados:

- certificado de **Distribuição de Aplicativo Mac**, que será usado para assinar o aplicativo; e
- certificado de **Distribuição do Instalador Mac**, para assinar o instalador.

> [!TIP]
> Tenha cuidado ao nomear as solicitações de certificado para essas chaves: use nomes descritivos que incluem o texto `Application` e o `Installer` para que possam ser diferenciados posteriormente.

Primeiro, crie o certificado do instalador:

1. Selecione **Distribuição do Instalador Mac** como o tipo de certificado e clique no botão **Continue (Continuar)**:

    > [!div class="mx-imgBorder"]
    > [![Criando um certificado de loja de aplicativos](certificates-identifiers-images/certif05.png)](certificates-identifiers-images/certif05-large.png#lightbox)

1. A próxima página explica como usar o **Acesso ao Conjunto de Chaves** para gerar um arquivo de solicitação de certificado. Siga as instruções:

    > [!div class="mx-imgBorder"]
    > [![Carregar uma solicitação de certificado](certificates-identifiers-images/certif06.png)](certificates-identifiers-images/certif06-large.png#lightbox)

    Siga o link [Saiba mais >](https://help.apple.com/developer-account/#/devbfa00fef7) para obter instruções sobre como usar o **Acesso ao Conjunto de Chaves** para criar um arquivo de solicitação de certificado. Lembre-se de escolher um nome de certificado que reflita o _tipo_ do certificado (de Aplicativo ou de Instalador).

1. Clique em **Download (Baixar)** para obter seu certificado e clique duas vezes para instalá-lo no **Keychain (Conjunto de Chaves)**:

    > [!div class="mx-imgBorder"]
    > [![Baixar o certificado da App Store](certificates-identifiers-images/certif07.png)](certificates-identifiers-images/certif07-large.png#lightbox)

**Siga as mesmas etapas para o certificado de Distribuição de Aplicativo Mac.**

![Certificado de Distribuição de Aplicativo Mac](certificates-identifiers-images/certif08.png)

### <a name="developer-id-certificates"></a>Certificados de ID de desenvolvedor

Para lançar por conta própria um aplicativo Xamarin.Mac (mas não lançá-lo por meio da Apple App Store), você precisará de dois certificados:

- certificado de **Instalador da ID do Desenvolvedor**, que será usado para assinar o aplicativo; e
- certificado de **Aplicativo da ID do Desenvolvedor**, para assinar o instalador.

> [!TIP]
> Tenha cuidado ao nomear as solicitações de certificado para essas chaves: use nomes descritivos que incluem o texto `Application` e o `Installer` para que possam ser diferenciados posteriormente.

Depois que você tiver criado, baixado e instalado os certificados, eles estarão visíveis em **Acesso ao Conjunto de Chaves**:

[Lista de certificados de Acesso ao Conjunto de Chaves](certificates-identifiers-images/certif09.png)

## <a name="related-links"></a>Links relacionados

- [Instalação](/visualstudio/mac/installation/)
- [Amostra do Hello, Mac](~/mac/get-started/hello-mac.md)
- [Distribua aplicativos na Mac App Store](https://developer.apple.com/devcenter/mac/checklist/)
- [ID de Desenvolvedor e GateKeeper](https://developer.apple.com/developer-id/)
