---
title: Alterações na loja de aplicativos no iOS 11
description: Este documento explora as alterações na loja de aplicativos no iOS 11. Ele aborda o ícone de armazenamento de um aplicativo, promoveu compras no aplicativo, a página do produto reprojetado, a comunicação do cliente e as versões em fases.
ms.prod: xamarin
ms.assetid: 4A7A03FD-B4F2-4969-8676-A17260730FD6
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/13/2016
ms.openlocfilehash: 2aee2a8dc37d6a1a73569322a97cdd0aebfbfa1c
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84574204"
---
# <a name="app-store-changes-in-ios-11"></a>Alterações na loja de aplicativos no iOS 11

A iOS App Store teve uma reformulação completa, o que permite que os usuários naveguem com eficiência na loja, mas também permite que você, como desenvolvedor, promova seu aplicativo para os usuários. Essas promoções incluem atualizações para compras e atualizações no aplicativo na página do produto. o iOS 11 também adiciona atualizações sobre como se comunicar com os usuários, como adicionar o ícone do aplicativo e como liberar seu aplicativo para o público.

![Novo layout da loja de aplicativos](app-store-changes-images/image3.jpg)

A loja de aplicativos remodelada tem as seguintes seções:

- **Hoje** – essa guia inclui aplicativos que são uma "escolha do editor" ou aplicativo em destaque. Para promover seu aplicativo, preencha as informações na página [promover](https://developer.apple.com//contact/app-store/promote/) .
- **Jogos** – qualquer aplicativo definido como a categoria de **jogo** no iTunes Connect pode ser encontrado nessa guia.
- **Aplicativos** – essa guia apresenta todos os outros aplicativos. Os usuários podem navegar por aplicativos em destaque, categorias, principais pagas/gratuitos.
- **Atualizações** – este aplicativo exibe as atualizações para seus aplicativos. Mesmo que você libere um aplicativo por meio da [versão em fases](#Phased_Release), os usuários ainda poderão ir para essa guia e baixar a versão mais recente.
- **Pesquisa** – esta guia permite que os usuários pesquisem seu aplicativo.

## <a name="store-icon"></a>Ícone de armazenamento

Os ícones da loja (ou ícones de marketing) não são mais gerenciados no iTunes Connect e, em vez disso, devem estar contidos como um [Catálogo de ativos](~/ios/app-fundamentals/images-icons/app-icons.md) em seu binário de aplicativo, semelhante aos ícones do aplicativo. Um ícone de armazenamento 1024 x 1024 no formato PNG deve ser incluído em um catálogo de ativos para envio bem-sucedido de aplicativos do iOS 11.

O estreitamento do aplicativo garante que esse catálogo de ativos adicional não aumente o tamanho do aplicativo.

## <a name="in-app-purchases-promoted-in-the-app-store"></a>Compras no aplicativo promovidas na loja de aplicativos

A Apple fez compras no aplicativo mais detectáveis na loja de aplicativos. Agora você pode adicionar até 20 compras de aplicativo _promovidas_ no aplicativo que agora podem ser encontradas na página de aplicativos, na página do produto do seu aplicativo ou pesquisando.

Para que suas compras no aplicativo sejam exibidas na loja de aplicativos, você deve incluir os seguintes dados:

- **Imagem** – você precisa fornecer uma imagem especialmente projetada para o ícone que descreve o que a compra no aplicativo faz. Essa imagem deve ser diferente do ícone do aplicativo e não pode ser uma captura de tela.
- **Nome** – o nome pode ter apenas um máximo de 30 caracteres.
- **Descrição** – a descrição pode ter apenas um máximo de 45 caracteres.

Todas as promoções de compra no aplicativo estão sujeitas a uma revisão da loja de aplicativos antes que possam ser publicadas.

Para disponibilizar suas compras no aplicativo para promover, abra seu aplicativo e navegue até **recursos > compras no aplicativo**. Vá para a seção **promoção da loja de aplicativos (opcional)** e adicione uma imagem 1024 x 1024 e **salve**, conforme ilustrado na imagem a seguir:

![Seção promoção da loja de aplicativos no iTune Connect](app-store-changes-images/image4.png)

Você também precisa adicionar o `ShouldAddStorePayment` método ao `SKPaymentTransactionObserver` protocolo em seu aplicativo.

Para obter mais informações sobre promoções de compra no aplicativo, consulte [a página promovendo compras no aplicativo](https://developer.apple.com/app-store/promoting-in-app-purchases/) da Apple.

## <a name="redesigned-product-page"></a>Página do produto reprojetado

As seguintes alterações foram feitas na página do produto:

- Os títulos agora estão definidos para um máximo de 30 caracteres
- Um subtítulo foi adicionado
  - Deve ser um breve resumo que complementa o título.
  - O subtítulo deve ter um máximo de 30 caracteres
- Visualizações de aplicativo
  - Agora você pode ter três vídeos ou capturas de tela.
  - Reprodução de vídeos quando um usuário visita a página.
  - Para obter mais informações, consulte a página de [visualização do aplicativo](https://developer.apple.com/app-store/app-previews/) da Apple.
- Texto Promocional
  - Esse novo recurso fornece 170 caracteres de texto, permitindo que você descreva com frequência as informações que mudam sobre seu aplicativo.
  - Ele pode ser atualizado a qualquer momento sem a necessidade de enviar uma nova versão do aplicativo.

## <a name="customer-communication"></a>Comunicação do cliente

Em 10,3, a Apple lançou uma nova maneira para os desenvolvedores se comunicarem diretamente com os usuários do aplicativo por meio da capacidade de responder às revisões. Você pode acessar essa nova funcionalidade no iTunes Connect navegando até o **aplicativo > atividade > classificações e revisões**, conforme ilustrado na imagem a seguir:

![Caixa de diálogo mostrando área para inserir resposta para comentário](app-store-changes-images/image5.png)

Há algumas coisas a serem consideradas ao responder aos usuários:

- Você só pode responder uma vez, mas ambas as partes podem editar seus comentários tanto quanto desejarem.
- Os usuários recebem uma notificação quando você responde a um comentário.
- Uma nova função de suporte ao cliente foi criada no iTunes Connect. Os usuários com essa função ou uma função de administrador podem responder a comentários.

Para obter mais informações, consulte [a página respondendo às revisões](https://developer.apple.com/app-store/responding-to-reviews/) da Apple.

<a name="Phased_Release"></a>

## <a name="phased-release"></a>Versão em fases

Com o iOS 11, a Apple implementou a opção de lançamentos em fases para atualizações em seu aplicativo. Você pode habilitar as versões em fases para permitir que a atualização do aplicativo seja liberada gradualmente para os clientes, garantindo que a demanda não sobrecarrega seu ambiente de produção.

As versões em fases são habilitadas no iTunes Connect. Clique em seu aplicativo na barra lateral, role até a **versão em fases para atualizações automáticas** seção na parte inferior e selecione **liberar atualização no período de 7 dias usando a versão em fases**:

![Opção mostrando a versão em fases para atualizações automáticas](app-store-changes-images/image6.png)

Sua atualização está disponível imediatamente para download na guia Atualizações da loja de aplicativos. As versões em fases só estão disponíveis para usuários que têm Downloads automáticos selecionados.

## <a name="related-links"></a>Links relacionados

- [O que há de novo no iOS 11 (Apple)](https://developer.apple.com/ios/)
- [Página de produto da App Store atualizada (Apple)](https://developer.apple.com/app-store/product-page/)
- [Atualizando seu aplicativo para iOS 11 (WWDC) (vídeo)](https://developer.apple.com/videos/play/wwdc2017/204/)
