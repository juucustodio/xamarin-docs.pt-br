---
title: Onde meus componentes são armazenados no meu computador?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5EBB49EE-39E5-428B-866F-9FC1BB215B31
author: davidortinau
ms.author: daortin
ms.date: 05/08/2018
ms.openlocfilehash: 9447cf903c8789078e66082e720eeecfa7bb3e0d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73014233"
---
# <a name="where-are-the-components-stored-on-my-machine"></a>Onde meus componentes são armazenados no meu computador?

Sempre que você instala um componente do Xamarin em um projeto de aplicativo, ele é colocado em dois locais:

1. Em uma pasta de componentes no nível raiz da pasta da solução. Se você remover o componente de todos os projetos na solução, ele também será removido dessa pasta.

2. Uma cópia também é armazenada nos seguintes locais:
    - Windows: `%LocalAppData%\Xamarin\Cache\Components`
    - Mac: `~/Library/Caches/Xamarin/Components`

Portanto, para remover completamente um componente do seu sistema, exclua-o de seus projetos/soluções e da pasta de cache acima.
