---
title: Atualizando aplicativos unificados do xamarin. Mac para 64 bits
description: Este guia descreve como atualizar seus aplicativos xamarin. Mac para o destino de 64 bits. Ele também fornece exemplos dos tipos de erros que podem ser encontrados ao fazer essa alteração.
ms.prod: xamarin
ms.assetid: C3810A74-539C-4FFB-B47F-68CA5F7BCDAD
author: conceptdev
ms.author: crdun
ms.date: 02/22/2018
ms.openlocfilehash: 9bd70fec5d6d3bbbc4855980e1542bd4e486acaa
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61266702"
---
# <a name="updating-xamarinmac-unified-applications-to-64-bit"></a>Atualizando aplicativos unificados do xamarin. Mac para 64 bits

A partir de janeiro de 2018, a Apple exige que os novos [64 bits de destino de envios de Mac App Store](https://developer.apple.com/news/?id=06282017a). Aplicativos já está disponíveis a Mac App Store devem ser atualizados para o destino de 64 bits de junho de 2018.

O **arquivo** > **New** modelo de projeto do xamarin. Mac cria aplicativos de 64 bits por padrão, portanto, todos os aplicativos criados recentemente já são compatível com 64 bits e não exigirá qualquer alteração.

## <a name="targeting-64-bit"></a>Direcionamento de 64 bits

1. Abra o **opções de projeto** janela para seu aplicativo xamarin. Mac:

   ![O menu contextual para o projeto](mac-64-bit-images/1-contextual_menu-vsmac.png "o menu contextual para o projeto")

2. Selecione **Build do Mac** e defina **arquiteturas com suporte** para **x86\_64**:

   [![Definindo as arquiteturas com suporte como x86_64](mac-64-bit-images/2-project_options-vsmac.png "definindo as arquiteturas com suporte como x86_64")](mac-64-bit-images/2-project_options-vsmac-large.png#lightbox)

3. Se seu aplicativo tem dependências externas, como referências nativas ou projetos de associação, você deve atualizá-los para 64 bits de destino.

### <a name="errors"></a>Erros

Na primeira vez que você compila ou executar seu aplicativo com o suporte de 64 bits, você poderá encontrar erros de link de problemas de clang ou tempo de execução. Esses erros podem ocorrer se o terceiro dependências — por exemplo, as referências nativas em xamarin. Mac ou projetos de associações ou estruturas de todo o sistema carregados manualmente — não foram atualizadas para 64 bits.

> [!TIP]
> A conversão de seu projeto para 64 bits é uma alteração importante e indiretamente pode revelar vários erros de programação. Em particular, ele pode alterar o tamanho e o alinhamento de estruturas de dados, o que afetaria assinaturas p/invoke e código nativo vinculado em seu projeto. Considere revisar qualquer dados de avisos de compilação e teste seu aplicativo completamente posteriormente para detectar possíveis problemas.

#### <a name="example-error-resulting-from-a-dynamically-linked-third-party-dependency-that-does-not-target-64-bit"></a>Erro de exemplo resultante de uma dependência de terceiros vinculados dinamicamente que não tem como destino 64 bits:

```console
ld : warning : ignoring file PATH/ThirdPartyLibrary.framework/ThirdPartyLibrary, 
file was built for i386 which is not the architecture being linked (x86_64): 
PATH/ThirdPartyLibrary.framework/ThirdPartyLibrary 
```

Esse erro pode ser seguido em tempo de execução pelo `dlopen` retornando `IntPtr.Zero` em vez de um identificador esperado.

#### <a name="example-error-resulting-from-a-statically-linked-third-party-dependency-that-does-not-target-64-bit"></a>Erro de exemplo resultante de uma dependência de terceiros vinculadas estaticamente que não tem como destino 64 bits:

```console
Undefined symbols for architecture x86_64:
  "_LibraryFunction", referenced from:
     -u command line option
ld: symbol(s) not found for architecture x86_64 
```

Para compilar e executar com êxito, atualize essas dependências para 64 bits e recompilar seu aplicativo.

