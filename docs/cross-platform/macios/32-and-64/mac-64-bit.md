---
title: Atualizando aplicativos unificados do Xamarin. Mac para 64 bits
description: Este guia descreve como atualizar seus aplicativos Xamarin. Mac para o destino de 64 bits. Ele também fornece exemplos dos tipos de erros que podem ser encontrados ao fazer essa alteração.
ms.prod: xamarin
ms.assetid: C3810A74-539C-4FFB-B47F-68CA5F7BCDAD
author: davidortinau
ms.author: daortin
ms.date: 02/22/2018
ms.openlocfilehash: 1ae807869e44ad035d2681cf777af0e66ced8255
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016316"
---
# <a name="updating-xamarinmac-unified-applications-to-64-bit"></a>Atualizando aplicativos unificados do Xamarin. Mac para 64 bits

A partir de janeiro de 2018, a Apple requer que novas [submissas do Mac App Store tenham o destino de 64 bits](https://developer.apple.com/news/?id=06282017a). Os aplicativos já disponíveis na Mac App Store devem ser atualizados para o destino de 64 bits até junho de 2018.

O **arquivo**  > **novo** modelo de projeto Xamarin. Mac cria aplicativos de 64 bits por padrão, portanto, todos os aplicativos criados recentemente já são compatíveis com 64 bits e não exigirão nenhuma alteração.

## <a name="targeting-64-bit"></a>Direcionamento de 64 bits

1. Abra a janela **Opções de projeto** para seu aplicativo Xamarin. Mac:

   ![O menu contextual do projeto](mac-64-bit-images/1-contextual_menu-vsmac.png "O menu contextual do projeto")

2. Selecione **criação de Mac** e defina as **arquiteturas com suporte** para **x86\_64**:

   [![Definindo as arquiteturas com suporte para o x86_64](mac-64-bit-images/2-project_options-vsmac.png "Definindo as arquiteturas com suporte para o x86_64")](mac-64-bit-images/2-project_options-vsmac-large.png#lightbox)

3. Se seu aplicativo tiver quaisquer dependências externas, como referências nativas ou projetos de associação, atualize-as para o destino de 64 bits.

### <a name="errors"></a>Erros

Na primeira vez que você criar ou executar seu aplicativo com suporte de 64 bits, você poderá encontrar erros de link de Clang ou problemas de tempo de execução. Esses erros podem ocorrer se dependências de terceiros — por exemplo, referências nativas em seus projetos Xamarin. Mac ou bindings, ou estruturas de todo o sistema carregadas manualmente, não foram atualizadas para 64 bits.

> [!TIP]
> Converter seu projeto para 64 bits é uma alteração importante e pode revelar indiretamente vários erros de programação. Em particular, ele pode alterar o tamanho e o alinhamento das estruturas de dados, o que afetaria as assinaturas p/Invoke e o código nativo vinculado em seu projeto. Considere a análise de quaisquer avisos de compilação fornecidos e teste o aplicativo detalhadamente mais tarde para detectar possíveis problemas.

#### <a name="example-error-resulting-from-a-dynamically-linked-third-party-dependency-that-does-not-target-64-bit"></a>Erro de exemplo resultante de uma dependência de terceiros vinculada dinamicamente que não tem como alvo 64 bits:

```console
ld : warning : ignoring file PATH/ThirdPartyLibrary.framework/ThirdPartyLibrary, 
file was built for i386 which is not the architecture being linked (x86_64): 
PATH/ThirdPartyLibrary.framework/ThirdPartyLibrary 
```

Esse erro pode ser seguido em tempo de execução por `dlopen` retornando `IntPtr.Zero` em vez de um identificador esperado.

#### <a name="example-error-resulting-from-a-statically-linked-third-party-dependency-that-does-not-target-64-bit"></a>Erro de exemplo resultante de uma dependência de terceiros vinculada estaticamente que não tem como alvo 64 bits:

```console
Undefined symbols for architecture x86_64:
  "_LibraryFunction", referenced from:
     -u command line option
ld: symbol(s) not found for architecture x86_64 
```

Para compilar e executar com êxito, atualize essas dependências para 64 bits e recompile seu aplicativo.
