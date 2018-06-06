---
title: Estruturas inseridas no xamarin
description: Este documento descreve como compartilhar código com estruturas inseridas em um aplicativo xamarin. Isso pode ser feito com a ferramenta de mtouch ou referências nativo.
ms.prod: xamarin
ms.assetid: F8C61020-4106-46F1-AECB-B56C909F42CB
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: e42f0940fe3fc132c9d381907aad5afbe474c4ad
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34787286"
---
# <a name="embedded-frameworks-in-xamarinios"></a>Estruturas inseridas no xamarin

_Este documento descreve como os desenvolvedores de aplicativos podem incorporar estruturas de usuário em seus aplicativos._

Com o iOS 8.0 Apple tornou possível criar uma estrutura incorporada para compartilhar código entre as extensões de aplicativo e o aplicativo principal no Xcode.

Xamarin 9.0 adiciona suporte para o uso dessas estruturas inseridas (criadas com o Xcode) em aplicativos xamarin. *Ele irá **não** ser possível criar estruturas inseridas de qualquer tipo de projetos de xamarin, consomem apenas as estruturas existentes nativo (Objective-C).*

Há duas maneiras para consumir estruturas em xamarin:

- Passar a estrutura para a ferramenta mtouch, adicionando o seguinte para os argumentos adicionais mtouch do projeto **iOS compilação** opções:

  ```csharp
  --framework:/Path/To/My.Framework
  ```

  Isso deve ser definida para cada configuração de projeto.

- Adicione referências nativo no menu de contexto

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Clique com botão direito no projeto e navegue para adicionar referências nativo

![](embedded-frameworks-images/xam-native-refs.png "Selecione Adicionar referências de nativo no Visual Studio para Mac")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Clique com botão direito no projeto e navegue para adicionar referências nativo

![](embedded-frameworks-images/vs-native-refs.png "Selecione Adicionar referências de nativo no Visual Studio")

-----

  Isso funciona para todas as configurações.

Em versões futuras do Visual Studio para Mac e as ferramentas Xamarin para Visual Studio será possível consumir estruturas de dentro do IDE (sem editar manualmente os arquivos de projeto).

Alguns projetos de exemplo podem ser encontrados em [github](https://github.com/rolfbjarne/embedded-frameworks)

## <a name="limitations"></a>Limitações

- Estruturas inseridas só têm suporte em [unificada](~/cross-platform/macios/unified/index.md) projetos.
- Estruturas inseridas têm suporte apenas em projetos com um destino de implantação pelo menos o iOS 8.0.
- Se uma extensão requer uma estrutura inserida, o aplicativo de contêiner também deve ter uma referência para a estrutura, caso contrário que o framework não será incluído no pacote de aplicativo.

## <a name="the-mono-runtime"></a>O tempo de execução Mono

Internamente, xamarin tira proveito desse recurso para vincular com o tempo de execução Mono como uma estrutura, em vez de vincular o tempo de execução Mono estaticamente para cada extensão e o aplicativo de contêiner.

Isso é feito automaticamente se o aplicativo de contêiner é um aplicativo unificada, ela contém extensões e a implantação de destino é o iOS 8.0 ou superior.

Aplicativos sem extensões ainda serão vinculado com o tempo de execução Mono estaticamente, porque não há uma penalidade de tamanho para o uso de uma estrutura se houver apenas um aplicativo que fazem referência a ele.

Esse comportamento pode ser substituído pelo desenvolvedor do aplicativo, adicionando o seguinte como um argumento mtouch adicionais no iOS do projeto opções de compilação:

- `--mono:static`: Links com o tempo de execução Mono estaticamente.
- `--mono:framework`: Links com o tempo de execução Mono como uma estrutura.

Um cenário para vincular com o tempo de execução Mono como uma estrutura mesmo para aplicativos sem extensões é diminuir o tamanho do executável, para solucionar quaisquer restrições de tamanho que Apple impõe no executável. Para referência, o tempo de execução Mono adiciona aproximadamente 1,7 MB por arquitetura (como de xamarin 8.12, porém seu varia entre versões e até mesmo entre aplicativos). A estrutura Mono adiciona aproximadamente 2.3MB por arquitetura, o que significa que, para um aplicativo de arquitetura única sem qualquer extensão, tornando o link do aplicativo com o tempo de execução Mono resultante como uma estrutura de reduzir o executável por ~1.7MB, mas adicionar um framework ~2.3MB, em uma ~0.6MB maior aplicativo juntas.

