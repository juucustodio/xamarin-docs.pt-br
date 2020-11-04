---
title: Usando o Xcode para criar interfaces do projeto
description: A maneira recomendada de criar interfaces de usuário do iOS agora é diretamente usando o Xcode em um Mac.
ms.prod: xamarin
ms.assetid: af9f95db-5cd6-475d-867d-f73e1574e8fc
ms.technology: xamarin-ios
author: joshspicer
ms.author: jospicer
ms.date: 10/27/2020
ms.openlocfilehash: b6fc5c89e9221bc84b364290a275fbd6d35c12c4
ms.sourcegitcommit: d2aa3a8bf9a60b6708db55b10b0c6893c06d3256
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93343265"
---
# <a name="using-xcode-to-design-user-interfaces"></a>Usando o Xcode para criar interfaces do usuário

_A maneira recomendada para editar arquivos. Storyboard e. NIB é editando-os no Xcode Interface Builder em um Mac._

## <a name="how-to-open-a-storyboard"></a>Como abrir um storyboard 

Abra um arquivo de interface do usuário do iOS no Visual Studio para Mac clicando com o botão direito do mouse em um arquivo de storyboard e selecionando o **Xcode Interface Builder** :

[![Selecionar Interface Builder](images/select-interface-builder.png)](images/select-interface-builder.png#lightbox)

Em seguida, você deve ver o lançamento da janela do Xcode. Todas as edições salvas aqui serão refletidas no seu projeto do Visual Studio.

[![Janela do Xcode](images/xcode.png)](images/xcode.png#lightbox)

Mais informações sobre o Interface Builder do Xcode podem ser encontradas [aqui](https://developer.apple.com/xcode/interface-builder/).

## <a name="known-problems"></a>Problemas conhecidos

Problemas conhecidos são discutidos nesta seção.

### <a name="visual-studio-could-not-communicate-with-xcode"></a>"O Visual Studio não pôde se comunicar com o Xcode"

No macOS Catalina ou superior, você pode encontrar o erro abaixo:

[![Não consegue comunicar erro](images/could-not-communicate.png)](images/could-not-communicate.png#lightbox)

Primeiro, verifique se as preferências do sistema do Mac em **segurança & privacidade > automação** de que o Visual Studio está listado e se o **Xcode** está marcado.

[![segurança do macOS](images/macos-security.png)](images/macos-security.png#lightbox)

Se o **Xcode** estiver marcado e a mensagem de erro ainda aparecer, talvez seja necessário redefinir as permissões de privacidade do Visual Studio para Mac.

Isso pode ser feito iniciando uma janela de terminal e emitindo o comando:

```bash
sudo tccutil reset All "com.microsoft.visual-studio"
```

Para garantir que a alteração acima entra em vigor, redefina o PRAM de seu Mac. As instruções sobre como fazer isso podem ser encontradas [aqui](https://support.apple.com/HT204063).
