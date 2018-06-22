---
title: Onde os componentes são armazenados no meu computador?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5EBB49EE-39E5-428B-866F-9FC1BB215B31
author: asb3993
ms.author: amburns
ms.openlocfilehash: a53045a6179a26b30d824976d11fd2769a84811e
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/09/2018
ms.locfileid: "33919391"
---
# <a name="where-are-the-components-stored-on-my-machine"></a>Onde os componentes são armazenados no meu computador?

Sempre que você instala um componente Xamarin em um projeto de aplicativo, ele é colocado em dois locais:

1. Em uma pasta de componentes no nível raiz de sua pasta de solução. Se você remover o componente de todos os projetos na solução, ele será obter removido desta pasta também.

2. Uma cópia também é armazenada nos seguintes locais:
    - Windows: `%LocalAppData%\Xamarin\Cache\Components`
    - Mac: `~/Library/Caches/Xamarin/Components`

Para remover completamente um componente do sistema, exclua-de suas soluções de projetos/e da pasta cache acima.
