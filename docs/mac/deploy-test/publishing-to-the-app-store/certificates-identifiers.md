---
title: Certificados e identificadores no Xamarin.Mac
description: Este guia orienta a criação dos certificados e identificadores necessários que serão necessários para publicar um aplicativo Xamarin. Mac.
ms.prod: xamarin
ms.assetid: 393d0066-7f6f-4ac3-a48d-4b5db65bc4cd
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 12/17/2019
ms.openlocfilehash: 2ea3516c1fb89c8c9b9cc3694d7c95ccd87e9d41
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75489429"
---
# <a name="certificates-and-identifiers-in-xamarinmac"></a>Certificados e identificadores no Xamarin.Mac

_Esse guia lhe orienta durante a criação dos Certificados e Identificadores que serão necessários para publicar um aplicativo Xamarin.Mac._

## <a name="setup"></a>Configuração

Visite o [Apple Developer Member Center (Central de Associados do Desenvolvedor da Apple)](https://developer.apple.com) para configurar o Mac para desenvolvimento. Clique no link da **conta** e entre. O menu principal é mostrado abaixo:

> [!div class="mx-imgBorder"]
> [![A Central de Membros do Desenvolvedor Apple](certificates-identifiers-images/devcenter01.png)](certificates-identifiers-images/devcenter01-large.png#lightbox)

Clique no botão **certificados, identificadores & perfis** (ou no botão de adição próximo ao título **certificados** ):

> [!div class="mx-imgBorder"]
> [![selecionando certificados, IDs & perfis](certificates-identifiers-images/devcenter02.png)](certificates-identifiers-images/devcenter02-large.png#lightbox)

Selecione um tipo de certificado e clique em **continuar**:

> [!div class="mx-imgBorder"]
> [![Selecionando o link de certificados](certificates-identifiers-images/devcenter03.png)](certificates-identifiers-images/devcenter03-large.png#lightbox)

A partir daqui, você pode baixar os **certificados intermediários** (parceiros de autoridade de certificação em todo o mundo e a autoridade de certificação de ID de desenvolvedor), se necessário (último item na parte inferior da página). No entanto, eles devem ser instalados automaticamente para o desenvolvedor pelo Xcode.

O restante desta seção percorre as seções relevantes para os desenvolvedores de Mac:

- **Register Mac App ID (Registrar a ID do aplicativo Mac)** – O desenvolvedor precisa seguir essas etapas para cada aplicativo que grava.
- **Register macOS Systems (Registrar sistemas macOS)** – Isso só é necessário ao adicionar computadores para teste.
- **Criar certificados** – necessário apenas uma vez ao configurar os certificados e, posteriormente, ao renová-los.
- **Create Provisioning Profile (Criar perfil de provisionamento)** – O desenvolvedor precisa seguir essas etapas para cada novo aplicativo gravado e ao adicionar novos sistemas.

## <a name="register-mac-app-id"></a>Registrar a ID do Aplicativo Mac

Você precisa registrar uma ID de aplicativo para cada aplicativo. Siga as etapas abaixo para criar uma entrada:

1. Pressione o sinal "+" (sinal de adição) ou **registre uma ID do aplicativo**:

    > [!div class="mx-imgBorder"]
    > [![introdução às IDs de aplicativo](certificates-identifiers-images/appid01.png)](certificates-identifiers-images/appid01-large.png#lightbox)

1. Escolher **IDs de aplicativo**

    > [!div class="mx-imgBorder"]
    > [![introdução às IDs de aplicativo](certificates-identifiers-images/appid02.png)](certificates-identifiers-images/appid02-large.png#lightbox)

1. Insira uma **Descrição**e selecione os **serviços de aplicativos** que o aplicativo precisará: a. A plataforma deve ser **MacOS** a. Escolha uma **Descrição** (usada somente neste portal) a. Insira a **ID do pacote**, que deve corresponder ao seu **info. plist** a. Selecione os recursos que seu aplicativo requer

    > [!div class="mx-imgBorder"]
    > [![Inserindo a descrição e os serviços de aplicativos](certificates-identifiers-images/appid03.png)](certificates-identifiers-images/appid03-large.png#lightbox)

    Pressione **continuar** para revisar suas seleções.

1. Se as informações estiverem corretas, clique em **registrar** para concluir a instalação:

    > [!div class="mx-imgBorder"]
    > [![examinar os dados inseridos](certificates-identifiers-images/appid04.png)](certificates-identifiers-images/appid04-large.png#lightbox)

1. Verifique as informações e clique no botão **Submit (Enviar)** :

    > [!div class="mx-imgBorder"]
    > ![verificar as informações](certificates-identifiers-images/appid05.png)

Alguns **App Services (Serviços de Aplicativos)** poderão exigir outras configurações (por exemplo, iCloud). Se esse for o caso, selecione a nova ID do aplicativo recém-criada e clique no botão **Edit (Editar)** :

> [!div class="mx-imgBorder"]
> [![editar a nova ID do aplicativo](certificates-identifiers-images/appid06.png)](certificates-identifiers-images/appid06-large.png#lightbox)

Para configurar os serviços do iCloud, por exemplo, clique no botão **Editar** :

> [!div class="mx-imgBorder"]
> [![Configurando os serviços de iCloud](certificates-identifiers-images/appid07.png)](certificates-identifiers-images/appid07-large.png#lightbox)

## <a name="register-macos-devices"></a>Registrar dispositivos macOS

Para criar um perfil de provisionamento para teste, o desenvolvedor precisa ter seus computadores Mac registrados. Um máximo de 100 computadores pode ser registrado para teste.

1. Na Central de Desenvolvedores Mac, selecione **All (Todos)** na seção **Devices (Dispositivos)** e clique no botão **+** :

    > [!div class="mx-imgBorder"]
    > [![Adicionando um novo computador](certificates-identifiers-images/device01.png)](certificates-identifiers-images/device01-large.png#lightbox)

1. Insira um **Name (Nome)** e o **UUID** do computador para adicionar e clique no botão **Continue (Continuar)** . Examine as informações e clique no botão **Register (Registrar)** :

    > [!div class="mx-imgBorder"]
    > [![Inserindo as informações do novo computador](certificates-identifiers-images/device02.png)](certificates-identifiers-images/device02-large.png#lightbox)

1. Examine e confirme os dados inseridos:

    > [!div class="mx-imgBorder"]
    > [![Inserindo as informações do novo computador](certificates-identifiers-images/device03.png)](certificates-identifiers-images/device03-large.png#lightbox)

## <a name="create-certificates"></a>Criar certificados

Use a seção Certificados para criar vários tipos diferentes de certificados que serão usados para assinar os Aplicativos Mac:

> [!div class="mx-imgBorder"]
> [![Criando um novo certificado](certificates-identifiers-images/devcenter04.png)](certificates-identifiers-images/devcenter04-large.png#lightbox)

Há cinco tipos principais de certificado relevantes para o desenvolvimento do macOS:

- **Desenvolvimento de Mac** – opcional para o desenvolvimento geral de aplicativos, mas necessário se o desenvolvedor planeja usar recursos como icloud ou notificações por push. O desenvolvedor precisará de um Certificado de Desenvolvimento antes que ele possa criar perfis de provisionamento que lhe permita acessar esses recursos.
- **Distribuição de aplicativos Mac** – o desenvolvedor precisará de um certificado para seu aplicativo e outro certificado para o instalador.
- **Distribuição do instalador do Mac** – o desenvolvedor precisará de um certificado para seu aplicativo e outro certificado para o instalador.
- **Instalador da ID do desenvolvedor** – certificados para o instalador distribuir fora da Mac App Store.
- **Aplicativo de ID de desenvolvedor** – certificados para o aplicativo distribuir fora da Mac App Store.

As seções a seguir fornecerão exemplos de como criar alguns desses tipos de certificado.

### <a name="mac-development-certificate"></a>Certificado de desenvolvimento do Mac

Conforme mencionado anteriormente, o certificado de desenvolvimento do Mac não é necessário, a menos que recursos do macOS, como iCloud ou notificações por push, estejam sendo usados.

Faça o seguinte para criar um novo Certificado de Desenvolvimento:

1. Selecione o botão de opção **Mac Development (Desenvolvimento do Mac)** e clique em **Continue (Continuar)** :

    > [!div class="mx-imgBorder"]
    > [![Adicionando um certificado de desenvolvimento](certificates-identifiers-images/certif02.png)](certificates-identifiers-images/certif02-large.png#lightbox)

1. Carregar uma _solicitação de assinatura de certificado_. O arquivo de solicitação de certificado (`.certSigningRequest`de extensão) será salvo localmente no Mac. Clique em **escolher arquivo** para selecionar a solicitação de certificado e pressione **continuar**.

    > [!div class="mx-imgBorder"]
    > [![carregar um arquivo de solicitação de certificado](certificates-identifiers-images/certif03.png)](certificates-identifiers-images/certif03-large.png#lightbox)

    Siga o link [saiba mais >](https://help.apple.com/developer-account/#/devbfa00fef7) para obter instruções sobre como usar o acesso ao conjunto de **chaves** para criar um arquivo de solicitação de certificado.

1. Pressione **baixar** para obter o arquivo de certificado e clique duas vezes nele para instalar:

    > [!div class="mx-imgBorder"]
    > [![baixar o arquivo de certificado](certificates-identifiers-images/certif04.png)](certificates-identifiers-images/certif04-large.png#lightbox)

Conforme mencionado anteriormente, o certificado do Desenvolvedor nem sempre é necessário, a menos que o desenvolvedor esteja implementando recursos macOS, como o iCloud e as notificações por push. Ele também é necessário para criar um **Development Provisioning Profile (Perfil de Provisionamento de Desenvolvimento)** , que será necessário para testar os aplicativos da Mac App Store.

### <a name="mac-app-store-certificates"></a>Certificados da Mac App Store

Para liberar um aplicativo na loja de aplicativos, você precisará de dois certificados:

- Certificado de **distribuição de aplicativo Mac** que será usado para assinar o aplicativo; e 
- Certificado de **distribuição do instalador do Mac** , para assinar o instalador.

> [!TIP]
> Tenha cuidado ao nomear as solicitações de certificado para essas chaves: use nomes descritivos que incluem o texto `Application` e o `Installer` para que possam ser diferenciados posteriormente.

Primeiro, crie o certificado do instalador:

1. Selecione **distribuição do instalador do Mac** como o tipo de certificado e clique no botão **continuar** :

    > [!div class="mx-imgBorder"]
    > [![Criando um certificado de loja de aplicativos](certificates-identifiers-images/certif05.png)](certificates-identifiers-images/certif05-large.png#lightbox)

1. A próxima página explica como usar o **Acesso ao Conjunto de Chaves** para gerar um arquivo de solicitação de certificado. Siga as instruções:

    > [!div class="mx-imgBorder"]
    > [![carregar uma solicitação de certificado](certificates-identifiers-images/certif06.png)](certificates-identifiers-images/certif06-large.png#lightbox)

    Siga o link [saiba mais >](https://help.apple.com/developer-account/#/devbfa00fef7) para obter instruções sobre como usar o acesso ao conjunto de **chaves** para criar um arquivo de solicitação de certificado. Lembre-se de escolher um nome de certificado que reflita o _tipo_ de certificado (aplicativo ou instalador).

1. Clique em **Download (Baixar)** para obter seu certificado e clique duas vezes para instalá-lo no **Keychain (Conjunto de Chaves)** :

    > [!div class="mx-imgBorder"]
    > [![baixar o certificado da App Store](certificates-identifiers-images/certif07.png)](certificates-identifiers-images/certif07-large.png#lightbox)

**Siga as mesmas etapas para o certificado de distribuição de aplicativo Mac.**

![Certificado de distribuição de aplicativo Mac](certificates-identifiers-images/certif08.png)

### <a name="developer-id-certificates"></a>Certificados de ID de desenvolvedor

Para lançar por conta própria um aplicativo Xamarin. Mac (não lançado por meio da Apple App Store), você precisará de dois certificados:

- Certificado do **instalador da ID do desenvolvedor** que será usado para assinar o aplicativo; e 
- **ID do desenvolvedor** certificado de aplicativo, para assinar o instalador.

> [!TIP]
> Tenha cuidado ao nomear as solicitações de certificado para essas chaves: use nomes descritivos que incluem o texto `Application` e o `Installer` para que possam ser diferenciados posteriormente.

Depois de criar, baixar e instalar certificados, eles ficarão visíveis no acesso ao conjunto de **chaves**:

[Lista de certificados de acesso do conjunto de chaves](certificates-identifiers-images/certif09.png)

## <a name="related-links"></a>Links relacionados

- [Instalação](/visualstudio/mac/installation/)
- [Amostra do Hello, Mac](~/mac/get-started/hello-mac.md)
- [Distribua aplicativos na Mac App Store](https://developer.apple.com/devcenter/mac/checklist/)
- [ID de Desenvolvedor e GateKeeper](https://developer.apple.com/resources/developer-id/)
