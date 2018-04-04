---
title: Onde os componentes são armazenados no meu computador?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5EBB49EE-39E5-428B-866F-9FC1BB215B31
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.openlocfilehash: f0dad6e6219d373eaa9f8410aea7d96c81eceb6b
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="where-are-the-components-stored-on-my-machine"></a>Onde os componentes são armazenados no meu computador?

Sempre que você instala um componente Xamarin em um projeto de aplicativo, ele é colocado em dois locais:

1. Em uma pasta de componentes no nível raiz de sua pasta de solução. Se você remover o componente de todos os projetos na solução, ele será obter removido desta pasta também.

2. Uma cópia também é armazenada nos seguintes locais:
    - Windows: `%LocalAppData%\Xamarin\Cache\Components`
    - Mac: `~/Library/Caches/Xamarin/Components`

Para remover completamente um componente do sistema, exclua-de suas soluções de projetos/e da pasta cache acima.
