---
title: "Alterações de loja de aplicativos"
description: Explorando os novos recursos do iOS 11
ms.topic: article
ms.prod: xamarin
ms.assetid: 4A7A03FD-B4F2-4969-8676-A17260730FD6
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/13/2016
ms.openlocfilehash: d75c1393f2b5701226433235010a41da9c1aeb03
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="app-store-changes"></a>Alterações de loja de aplicativos

_Explorando os novos recursos do iOS 11_

IOS App Store teve uma reestruturação completa, que não só permite aos usuários navegar com eficiência o armazenamento, mas também permite que você, como desenvolvedor, promover o seu aplicativo aos usuários. Esses promoções incluem as atualizações para compras no aplicativo e a página do produto. iOS 11 também adiciona atualizações sobre como se comunicar com os usuários, como adicionar o ícone do aplicativo e a versão do seu aplicativo para o público.

![Novo layout de armazenamento de aplicativo](app-store-changes-images/image3.jpg)

Loja de aplicativos reprojetada tem as seguintes seções:

- **Hoje** – essa guia inclui aplicativos que são "Escolha do editor" ou um aplicativo. Para promover seu aplicativo aqui preencher informações sobre o [promover](https://developer.apple.com//contact/app-store/promote/) página.
- **Jogos** – todos os aplicativos que é definido como o **jogo** categoria na iTunes Connect pode ser encontrada nesta guia.
- **Aplicativos** – este guia apresenta todos os outros aplicativos. Os usuários podem procurar por aplicativos em destaque, categorias, superior paga/livre.
- **Atualizações** – este aplicativo exibe as atualizações para seus aplicativos. Mesmo se você liberar um aplicativo por meio de [fases versão](#Phased_Release), os usuários ainda podem ir para este guia e baixar a versão mais recente.
- **Pesquisa** – esta guia permite que os usuários pesquisem em seu aplicativo.

## <a name="store-icon"></a>Ícone de armazenamento

Ícones de armazenamento (ou marketing ícones) não são gerenciados no iTunes conectar e em vez disso, devem estar contidos como um [catálogo de ativos](~/ios/app-fundamentals/images-icons/app-icons.md) em binário seu aplicativo, como ícones de aplicativo. Um ícone de repositório de 1024 x 1024 no formato PNG deve ser incluído em um catálogo de ativos para envio bem-sucedido de aplicativos do iOS 11.

Diminuir o aplicativo garante que esse catálogo de ativos adicionais não aumenta o tamanho do aplicativo.


## <a name="in-app-purchases-promoted-in-the-app-store"></a>Compras no aplicativo promovido na loja de aplicativos

Apple fez compras no aplicativo mais detectáveis na loja de aplicativos. Agora você pode adicionar até 20 _App Store promovido_ compras no aplicativo que agora podem ser encontradas na página de aplicativos, na página de produto do seu aplicativo, ou por meio de pesquisa.

Para que suas compras no aplicativo exibidas na loja de aplicativos, você deve incluir os seguintes dados:

- **Imagem** – você precisa fornecer uma imagem especialmente projetada para o ícone que descreve o que faz a compra no aplicativo. Essa imagem deve ser diferente do que o ícone do aplicativo e não pode ser uma captura de tela.
- **Nome** – o nome só pode ser um máximo de 30 caracteres.
- **Descrição** – a descrição pode ter somente um máximo de 45 caracteres.

Qualquer promoções de compra no aplicativo estão sujeitos a uma revisão de armazenamento do aplicativo antes de ser publicado.

Para tornar suas compras no aplicativo disponíveis para promover, abra o aplicativo e navegue até **recursos > compras no aplicativo**. Vá para o **promoção de repositório do aplicativo (opcional)** seção e adicionar uma imagem de 1024 x 1024 e **salvar**, conforme ilustrado na imagem a seguir:

![Seção de promoção de loja de aplicativos em iTune conectar](app-store-changes-images/image4.png)

Você também precisará adicionar o `ShouldAddStorePayment` método para o `SKPaymentTransactionObserver` protocolo em seu aplicativo.

Para obter mais informações sobre promoções de compra no aplicativo, consulte da Apple [promover o no aplicativo compras](https://developer.apple.com/app-store/promoting-in-app-purchases/) página.

## <a name="redesigned-product-page"></a>Página de produto redesenhado

As seguintes alterações foram feitas para a página de produto:

- Títulos agora estão definidos para um máximo de 30 caracteres
- Foi adicionado um subtítulo
    - Ele deve ser um breve resumo que complementa o título.
    - Subtítulo deve ter um máximo de 30 caracteres
- Visualizações de aplicativo
    - Agora você pode ter três vídeos ou capturas de tela.
    - Reprodução de vídeos automática quando um usuário acessa a página.
    - Para obter mais informações, consulte da Apple [aplicativo visualização](https://developer.apple.com/app-store/app-previews/) página.
- Texto promocional
    - Esse novo recurso fornece 170 caracteres de texto, permitindo que você descreve as informações de alteração com frequência sobre seu aplicativo.
    - Ele pode ser atualizado a qualquer momento sem a necessidade de enviar uma nova versão do aplicativo.

## <a name="customer-communication"></a>Comunicação do cliente

Em 10.3, Apple lançou uma nova maneira para os desenvolvedores a se comunicar diretamente com os usuários do aplicativo por meio da capacidade de responder a revisões. Você pode acessar essa nova funcionalidade no iTunes conectar-se ao procurar por **aplicativo > atividade > classificações e comentários de**, conforme ilustrado na imagem a seguir:

![Caixa de diálogo mostrando a área para inserir a resposta para comentar](app-store-changes-images/image5.png)

Há algumas coisas a serem consideradas ao responder a usuários:

- Você somente poderá responder uma vez, mas as duas partes podem editar seus comentários quanto desejarem.
- Os usuários obterão uma notificação ao responder a um comentário.
- Conecte-se um novo suporte de cliente função foi criada no iTunes. Os usuários com esta função ou uma função de administrador podem responder aos comentários.

Para obter mais informações, consulte da Apple [responder a revisões](https://developer.apple.com/app-store/responding-to-reviews/) página.


## <a name="phased-release"></a>Versão em fases

Com o iOS 11, a Apple implementou a opção de versões em fases para atualizações para seu aplicativo. Você pode habilitar as versões em fases permitir que a atualização de aplicativo gradualmente seja liberado para clientes, garantindo que a demanda não sobrecarrega o ambiente de produção.

Versões em fases estão habilitados no iTunes conectar. Clique no seu aplicativo na barra lateral, role até a **fases de versão para atualizações automáticas** seção na parte inferior e selecione **fases de atualização de versão com o uso de período de 7 dias versão**:

![Mostrando opção fases versão para atualizações automáticas](app-store-changes-images/image6.png)

A atualização está disponível imediatamente para download na guia atualizações da App Store. Versões em fases só estão disponíveis para os usuários que têm downloads automáticos selecionados.


## <a name="related-links"></a>Links relacionados

- [O que há de novo no iOS 11 (Apple)](https://developer.apple.com/ios/)
- [Página de produto atualizados App Store (Apple)](https://developer.apple.com/app-store/product-page/)
- [Atualizar seu aplicativo para iOS 11 (WWDC) (vídeo)](https://developer.apple.com/videos/play/wwdc2017/204/)
