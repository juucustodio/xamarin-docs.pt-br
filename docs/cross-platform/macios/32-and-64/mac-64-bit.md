---
title: Atualizando aplicativos Xamarin.Mac unificado para 64 bits
description: Este guia descreve como atualizar seus aplicativos Xamarin.Mac ao destino de 64 bits
ms.prod: xamarin
ms.assetid: C3810A74-539C-4FFB-B47F-68CA5F7BCDAD
author: bradumbaugh
ms.author: brumbaug
ms.date: 02/22/2018
ms.openlocfilehash: 558edbdee5adfe57205c7f76b35a0538c78b927f
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2018
---
# <a name="updating-xamarinmac-unified-applications-to-64-bit"></a>Atualizando aplicativos Xamarin.Mac unificado para 64 bits

A partir de janeiro de 2018, Apple requer que novos [64 bits de destino de envios de Mac App Store](https://developer.apple.com/news/?id=06282017a). Aplicativos já disponíveis na loja de aplicativos Mac devem ser atualizados para o destino de 64 bits por de 2018 de junho.

O **arquivo** > **novo** Xamarin.Mac modelo de projeto cria aplicativos de 64 bits por padrão, para que todos os aplicativos criados recentemente já são compatíveis de 64 bits e não exigirá qualquer alteração.

## <a name="targeting-64-bit"></a>Direcionamento de 64 bits

1. Abra o **opções de projeto** janela estiver seu aplicativo Xamarin.Mac:

   ![O menu contextual para o projeto](mac-64-bit-images/1-contextual_menu-vsmac.png "o menu contextual do projeto")

2. Selecione **Mac criar** e defina **arquiteturas compatíveis** para **x86\_64**:

   [![Definindo as arquiteturas com suporte para x86_64](mac-64-bit-images/2-project_options-vsmac.png "definindo as arquiteturas com suporte para x86_64")](mac-64-bit-images/2-project_options-vsmac-large.png#lightbox)

3. Se seu aplicativo tiver quaisquer dependências externas como referência nativa ou projetos de associação, atualizá-los para o destino de 64 bits.

### <a name="errors"></a>Erros

A primeira vez que você compila ou executar o aplicativo com o suporte de 64 bits, você poderá encontrar erros de link de problemas clang ou tempo de execução. Esses erros podem ocorrer se o terceiro dependências — por exemplo, referências nativo em sua Xamarin.Mac ou projetos de associações ou estruturas de todo o sistema manualmente carregado — não foram atualizados para 64 bits.

> [!TIP]
> Converter o projeto para 64 bits é uma alteração importante e indiretamente pode revelar vários erros de programação. Em particular, ele pode alterar o tamanho e alinhamento de estruturas de dados, o que afetaria assinaturas p/invoke e vinculadas em seu projeto de código nativo. Revise os avisos de compilação fornecidos e testar seu aplicativo throughly posteriormente para detectar possíveis problemas.

#### <a name="example-error-resulting-from-a-dynamically-linked-third-party-dependency-that-does-not-target-64-bit"></a>Erro de exemplo resultante de uma dependência de terceiros vinculados dinamicamente que não tem como destino 64 bits:

```console
ld : warning : ignoring file PATH/ThirdPartyLibrary.framework/ThirdPartyLibrary, 
file was built for i386 which is not the architecture being linked (x86_64): 
PATH/ThirdPartyLibrary.framework/ThirdPartyLibrary 
```

Esse erro pode ser seguido em tempo de execução por `dlopen` retornando `IntPtr.Zero` em vez de um identificador esperado.

#### <a name="example-error-resulting-from-a-statically-linked-third-party-dependency-that-does-not-target-64-bit"></a>Erro de exemplo resultante de uma dependência de terceiros estaticamente vinculada que não tem como destino 64 bits:

```console
Undefined symbols for architecture x86_64:
  "_LibraryFunction", referenced from:
     -u command line option
ld: symbol(s) not found for architecture x86_64 
```

Para compilar e executar com êxito, atualize essas dependências para 64 bits e recompile seu aplicativo.

