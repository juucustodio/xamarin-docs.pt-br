---
title: 'Erro de compilação: Não é possível codificar o deslocamento X na realocação de dispersão resultante'
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 84158C42-FE79-415A-A44A-D48C9E5E6760
ms.technology: xamarin-ios
author: chamons
ms.author: chhamo
ms.date: 08/07/2019
ms.openlocfilehash: df974649c8c9f1d1fb4c13d6d801eb0f6a3d1e77
ms.sourcegitcommit: 2e5a6b8bcd1a073b54604f51538fd108e1c2a8e5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68876225"
---
# <a name="compile-error-can-not-encode-offset-x-in-resulting-scattered-relocation"></a>Erro de compilação: Não é possível codificar o deslocamento X na realocação de dispersão resultante

```
1>C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\MSBuild\Xamarin\iOS\Xamarin.iOS.Common.targets(804,3): error GDC116A36: can not encode offset ‘0x1155504’ in resulting scattered relocation.
1> .long mono_aot_Xamarin_iOS_got - . + 12 (TaskId:141)
1> ^ (TaskId:141)
1>C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\MSBuild\Xamarin\iOS\Xamarin.iOS.Common.targets(804,3): error GDC116A36: can not encode offset ‘0x11555B8’ in resulting scattered relocation.
1> .long mono_aot_Xamarin_iOS_got - . + 16 (TaskId:141)
```

Esse problema ocorre durante a compilação de arquiteturas de 32 bits, como ARMv7, quando o binário final é muito grande para o ferramentas nativo.

Para resolver isso:

- Descartar compilação para essa arquitetura no painel de Build do iOS de opções de projeto.
- Habilitar vinculação ou remover manualmente o código para reduzir o tamanho final do executável
