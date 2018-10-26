---
title: Estruturas incorporadas no xamarin. IOS
description: Este documento descreve como compartilhar código com estruturas incorporadas em um aplicativo xamarin. IOS. Isso pode ser feito com a ferramenta mtouch ou referências nativas.
ms.prod: xamarin
ms.assetid: F8C61020-4106-46F1-AECB-B56C909F42CB
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/05/2018
ms.openlocfilehash: b59fd7c1a9e5f528878b90e1a76fabe5a79bab81
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108230"
---
# <a name="embedded-frameworks-in-xamarinios"></a>Estruturas incorporadas no xamarin. IOS

_Este documento descreve como os desenvolvedores de aplicativos podem incorporar estruturas de usuário em seus aplicativos._

Com o iOS 8.0 Apple agora é possível criar uma estrutura inserida para compartilhar código entre extensões de aplicativo e o aplicativo principal no Xcode.

Xamarin. IOS 9.0 adiciona suporte para consumir essas estruturas incorporadas (criadas com o Xcode) em aplicativos xamarin. IOS. *Ele irá **não** ser possível criar estruturas incorporadas de qualquer tipo de projetos xamarin. IOS, apenas consumir as estruturas existentes nativas (Objective-C).*

Há duas maneiras de consumir estruturas no xamarin. IOS:

- Passe a estrutura para a ferramenta mtouch, adicionando o seguinte para os argumentos adicionais do mtouch do projeto **Build do iOS** opções:

  ```csharp
  --framework:/Path/To/My.Framework
  ```

  Isso deve ser definido para cada configuração de projeto.

- Adicionar referências nativas no menu de contexto

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Clique com botão direito no projeto e navegue para adicionar referências nativas

![](embedded-frameworks-images/xam-native-refs.png "Selecione Adicionar referências de nativos no Visual Studio para Mac")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Clique com botão direito no projeto e navegue para adicionar referências nativas

![](embedded-frameworks-images/vs-native-refs.png "Selecione Adicionar referências de nativos no Visual Studio")

-----

  Isso funcionará para todas as configurações.

Em versões futuras do Visual Studio para Mac e as ferramentas do Xamarin para Visual Studio, será possível consumir as estruturas de dentro do IDE (sem editar manualmente os arquivos de projeto).

Alguns projetos de exemplo podem ser encontrados no [github](https://github.com/rolfbjarne/embedded-frameworks)

## <a name="limitations"></a>Limitações

- Somente há suporte para estruturas incorporadas em [unificado](~/cross-platform/macios/unified/index.md) projetos.
- Estruturas incorporadas têm suporte apenas em projetos com um destino de implantação pelo menos o iOS 8.0.
- Se uma extensão requer uma estrutura inserida, o aplicativo de contêiner também deve ter uma referência para a estrutura, caso contrário, que a estrutura não será incluída no pacote de aplicativo.

## <a name="the-mono-runtime"></a>Tempo de execução Mono

Internamente, xamarin. IOS tira proveito desse recurso para vincular com o tempo de execução Mono, como uma estrutura, em vez de vincular o tempo de execução Mono estaticamente em cada extensão e o aplicativo de contêiner.

Isso é feito automaticamente se o aplicativo de contêiner é um aplicativo unificado, ele contém extensões e a implantação de destino é o iOS 8.0 ou superior.

Aplicativos sem extensões ainda serão vinculado com o tempo de execução Mono estaticamente, porque há uma penalidade de tamanho para usar uma estrutura, se houver apenas um aplicativo fazendo referência a ela.

Esse comportamento pode ser substituído pelo desenvolvedor do aplicativo, adicionando o seguinte como um argumento de adicionais do mtouch em Opções de Build do iOS do projeto:

- `--mono:static`: Vincula estaticamente com tempo de execução Mono.
- `--mono:framework`: Links com o tempo de execução Mono, como uma estrutura.

Um cenário para vinculação com o tempo de execução Mono, como uma estrutura, mesmo para aplicativos sem extensões é diminuir o tamanho do executável, para superar a quaisquer restrições de tamanho que Apple impõe sobre o executável. Para referência, o tempo de execução Mono adiciona aproximadamente de 1,7 MB por arquitetura (como do xamarin. IOS 8.12, porém seu varia entre as versões e até mesmo entre os aplicativos). A estrutura Mono adiciona aproximadamente 2.3MB por arquitetura, o que significa que, para um aplicativo single-arquitetura sem qualquer extensão, tornando o link de aplicativo com o tempo de execução Mono resultante como uma estrutura de reduzir o executável por ~1.7MB, mas adicione uma estrutura ~2.3MB, em uma ~0.6MB maior aplicativo juntas.

