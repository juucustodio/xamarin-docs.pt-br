---
title: Onde meus componentes são armazenados no meu computador?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5EBB49EE-39E5-428B-866F-9FC1BB215B31
author: conceptdev
ms.author: crdun
ms.date: 05/08/2018
ms.openlocfilehash: 7725dbff994ffcef9734ad07c6b506064d9c5b2b
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70285049"
---
# <a name="where-are-the-components-stored-on-my-machine"></a>Onde meus componentes são armazenados no meu computador?

Sempre que você instala um componente do Xamarin em um projeto de aplicativo, ele é colocado em dois locais:

1. Em uma pasta de componentes no nível raiz da pasta da solução. Se você remover o componente de todos os projetos na solução, ele também será removido dessa pasta.

2. Uma cópia também é armazenada nos seguintes locais:
    - Windows: `%LocalAppData%\Xamarin\Cache\Components`
    - Mac: `~/Library/Caches/Xamarin/Components`

Portanto, para remover completamente um componente do seu sistema, exclua-o de seus projetos/soluções e da pasta de cache acima.
