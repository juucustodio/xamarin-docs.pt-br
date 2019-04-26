---
title: Alterações de Store de aplicativo no iOS 11
description: Este documento explora as alterações para a Store do aplicativo no iOS 11. Ele aborda o ícone do aplicativo da loja, promovidas compras de no aplicativo, a página de produto redesenhado, comunicação de cliente e versões em fases.
ms.prod: xamarin
ms.assetid: 4A7A03FD-B4F2-4969-8676-A17260730FD6
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/13/2016
ms.openlocfilehash: 022d6b5c3f85863352dd1343752e934240b357aa
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61229557"
---
# <a name="app-store-changes-in-ios-11"></a>Alterações de Store de aplicativo no iOS 11

No iOS App Store teve uma reformulação completa, que não só permite aos usuários navegar com eficiência o armazenamento, mas também permite que você, como desenvolvedor, para promover seu aplicativo aos usuários. Esses promoções incluem as atualizações para compras no aplicativo e a página do produto. iOS 11 também adiciona as atualizações relacionadas a como se comunicar com os usuários, como adicionar o ícone do aplicativo e como liberar seu aplicativo para o público.

![Novo layout de armazenamento de aplicativo](app-store-changes-images/image3.jpg)

Loja de aplicativos reprojetada tem as seguintes seções:

- **Hoje em dia** – essa guia inclui aplicativos que são "opções do editor" ou um aplicativo. Para promover seu aplicativo aqui preencher informações sobre o [promover](https://developer.apple.com//contact/app-store/promote/) página.
- **Jogos** – todos os aplicativos que é definido como o **jogo** categoria no iTunes Connect pode ser encontrada sob essa guia.
- **Aplicativos** – este guia apresenta todos os outros aplicativos. Os usuários podem procurar por aplicativos em destaque, categorias, superior pago/free.
- **Atualizações** – este aplicativo exibe as atualizações para seus aplicativos. Mesmo se você liberar um aplicativo por meio [em fases de lançamento](#Phased_Release), os usuários ainda podem ir para essa guia e baixar a versão mais recente.
- **Pesquisa** – esta guia permite que os usuários pesquisem em seu aplicativo.

## <a name="store-icon"></a>Ícone de Store

Ícones de Store (ou ícones de marketing) não são mais gerenciados no iTunes Connect e, em vez disso, devem estar contidas como um [catálogo de ativos](~/ios/app-fundamentals/images-icons/app-icons.md) no seu binário de aplicativo, semelhante ao ícones do aplicativo. Um ícone de armazenamento de 1024 x 1024 no formato PNG deve ser incluído em um catálogo de ativos após o envio de aplicativos do iOS 11.

Diminuir o aplicativo torna-se de que este catálogo de ativos adicionais não aumenta o tamanho do aplicativo.


## <a name="in-app-purchases-promoted-in-the-app-store"></a>Compras no aplicativo promovidos na Store do aplicativo

Apple fez compras no aplicativo mais detectável na Store do aplicativo. Agora você pode adicionar até 20 _Store do aplicativo promovido_ compras no aplicativo que agora podem ser encontradas na página de aplicativos, na página de produto do seu aplicativo ou por meio da pesquisa.

Para ter suas compras no aplicativo exibidas na Store do aplicativo, você deve incluir os seguintes dados:

- **Imagem** – você precisará fornecer uma imagem especialmente desenvolvida para o ícone que descreve o que faz a compra no aplicativo. Essa imagem deve ser diferente do ícone do aplicativo e não pode ser uma captura de tela.
- **Nome** – o nome só pode conter um máximo de 30 caracteres.
- **Descrição** – a descrição somente pode ter um máximo de 45 caracteres.

Promoções qualquer compra no aplicativo estão sujeitos a uma revisão de repositório do aplicativo antes de ser publicado.

Para tornar suas compras no aplicativo disponíveis para promover, abra seu aplicativo e navegue até **recursos > compras no aplicativo**. Vá para o **promoção de Store de aplicativo (opcional)** seção e adicionar uma imagem de 1024 x 1024 e **salvar**, conforme ilustrado na imagem a seguir:

![Seção de promoção da App Store no iTune Connect](app-store-changes-images/image4.png)

Você também precisará adicionar o `ShouldAddStorePayment` método para o `SKPaymentTransactionObserver` protocolo em seu aplicativo.

Para obter mais informações sobre promoções de compra no aplicativo, consulte da Apple [promover sua compra de no aplicativo](https://developer.apple.com/app-store/promoting-in-app-purchases/) página.

## <a name="redesigned-product-page"></a>Página de produto redesenhado

As seguintes alterações foram feitas para a página do produto:

- Títulos agora estão definidos para um máximo de 30 caracteres
- Foi adicionado um subtítulo
    - Ele deve ser um breve resumo que complementa o título.
    - Subtítulo deve ter um máximo de 30 caracteres
- Visualizações do aplicativo
    - Agora você pode ter três vídeos ou capturas de tela.
    - Reprodução de vídeos automática quando um usuário visitar a página.
    - Para obter mais informações, consulte da Apple [versão prévia do aplicativo](https://developer.apple.com/app-store/app-previews/) página.
- Texto promocional
    - Esse novo recurso fornece 170 caracteres de texto, permitindo que você descrevem as informações que são alteradas frequentemente sobre seu aplicativo.
    - Ele pode ser atualizado a qualquer momento sem a necessidade de enviar uma nova versão do aplicativo.

## <a name="customer-communication"></a>Comunicação do cliente

Em 10.3, Apple lançou uma nova maneira para os desenvolvedores a se comunicar diretamente com usuários do aplicativo por meio da capacidade de responder a revisões. Você pode acessar essa nova funcionalidade no iTunes connect, navegando até **aplicativo > atividade > classificações e revisões**, conforme ilustrado na imagem a seguir:

![Caixa de diálogo mostrando a área para inserir a resposta para fazer um comentário](app-store-changes-images/image5.png)

Há algumas coisas a serem consideradas ao responder aos usuários:

- Você só pode responder uma vez, mas ambas as partes podem editar seus comentários, tanto quanto quiserem.
- Os usuários recebem uma notificação quando você responde a um comentário.
- Um atendimento de nova função foi criada no iTunes connect. Os usuários com essa função ou uma função de administrador podem responder a comentários.

Para obter mais informações, consulte da Apple [responder a revisões](https://developer.apple.com/app-store/responding-to-reviews/) página.

<a name="Phased_Release"/>

## <a name="phased-release"></a>Lançamento em fases

Com o iOS 11, a Apple implementou a opção de versões em fases para atualizações para seu aplicativo. Você pode habilitar as versões em fases permitir que a atualização do aplicativo seja liberado gradualmente para clientes, garantindo que a demanda não sobrecarrega o ambiente de produção.

Versões em fases estão habilitados no iTunes Connect. Clique em seu aplicativo na barra lateral, role até a **em fases de versão para atualizações automáticas** seção na parte inferior e selecione **lançamento em fases de atualização de versão ao longo do período de 7 dias usando**:

![Que mostra a opção de versão para atualizações automáticas em fases](app-store-changes-images/image6.png)

A atualização está disponível imediatamente para download na guia atualizações do Store do aplicativo. Versões em fases estão disponíveis apenas para os usuários que têm downloads automáticos selecionados.


## <a name="related-links"></a>Links relacionados

- [O que há de novo no iOS 11 (Apple)](https://developer.apple.com/ios/)
- [Página de Store de aplicativo atualizado do produto (Apple)](https://developer.apple.com/app-store/product-page/)
- [Atualizar seu aplicativo para iOS 11 (WWDC) (vídeo)](https://developer.apple.com/videos/play/wwdc2017/204/)
