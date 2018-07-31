---
title: Onde os componentes são armazenados no meu computador?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5EBB49EE-39E5-428B-866F-9FC1BB215B31
author: asb3993
ms.author: amburns
ms.date: 05/08/2018
ms.openlocfilehash: 4152c8ef7eeba3748d9244e27e48f3f9a2c0019b
ms.sourcegitcommit: aa9b9b203ab4cd6a6b4fd51e27d865e2abf582c1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2018
ms.locfileid: "39350713"
---
# <a name="where-are-the-components-stored-on-my-machine"></a>Onde os componentes são armazenados no meu computador?

Sempre que você instala um componente do Xamarin em um projeto de aplicativo, ele obtém colocado em dois locais:

1. Em uma pasta de componentes no nível raiz da pasta da solução. Se você remover o componente de todos os projetos na solução, ele será obter removido desta pasta também.

2. Uma cópia também é armazenada nos seguintes locais:
    - Windows: `%LocalAppData%\Xamarin\Cache\Components`
    - Mac: `~/Library/Caches/Xamarin/Components`

Portanto, para remover um componente completamente do seu sistema, excluí-lo de seus projetos/soluções e da pasta de cache acima.
