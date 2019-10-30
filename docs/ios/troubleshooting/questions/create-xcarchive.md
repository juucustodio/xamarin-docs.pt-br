---
title: É possível criar um arquivo. xcarchive do Visual Studio?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 417D84FB-1BA9-4DB9-A683-66E960BA3D0D
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/03/2018
ms.openlocfilehash: 27697223735c4074dd508d3f603ef6aa4e47b1be
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031169"
---
# <a name="is-it-possible-to-create-a-xcarchive-archive-from-visual-studio"></a>É possível criar um arquivo. xcarchive do Visual Studio?

## <a name="for-xamarin-4"></a>Para o Xamarin 4

A partir do Xamarin 4. x, agora é possível criar uma `.xcarchive` do Windows, definindo a propriedade `ArchiveOnBuild` como `true`. Por exemplo, usando `MSBuild` na linha de comando:

```bash
msbuild /p:Configuration=Release /p:ServerAddress=10.211.55.2 /p:ServerUser=xamUser /p:Platform=iPhone /p:ArchiveOnBuild=true /t:"Build" MyProject.csproj
```

O `.xcarchive` será colocado no diretório `$HOME/Library/Developer/Xcode/Archives` no host de Build do Mac que o Xcode e o Xamarin Studio Search exibam os arquivos criados anteriormente.

Consulte esta [postagem de fóruns do Xamarin](https://forums.xamarin.com/discussion/comment/156635/#Comment_156635) para ver algumas observações adicionais sobre a propriedade `ArchiveOnBuild`. Consulte a documentação sobre o [Xamarin. Ios linha de comando Builds no Windows](~/ios/get-started/installation/windows/connecting-to-mac/index.md) para obter detalhes adicionais sobre as propriedades `ServerAddress` e `ServerUser`.

* * *

## <a name="for-xamarin-3-and-earlier"></a>Para o Xamarin 3 e anterior

A extensão do Xamarin 3. x do Visual Studio não fornece um mecanismo para produzir arquivos de `.xcarchive`. Dito isso, a lógica usada para criar `.xcarchive` arquivos em Xamarin Studio no Mac [é descrita aqui](https://bugzilla.xamarin.com/show_bug.cgi?id=35#c5), portanto, você provavelmente poderá criar seu próprio `.xcarchive` manualmente, se desejar.

Mas vale a pena observar que você não precisa de um `.xcarchive` para enviar à loja de aplicativos. Você pode enviar um arquivo IPA desde que ele seja assinado com um perfil de distribuição da loja de aplicativos (não um perfil de distribuição ad hoc).

Na verdade, você pode até apenas compactar o pacote de `.app` (que é assinado com um perfil de distribuição da loja de aplicativos) e enviar esse arquivo de `.zip` para a loja de aplicativos.

Em ambos os casos, você pode usar o aplicativo carregador de aplicativos para enviar o aplicativo (em vez de Xcode).
