---
title: É possível criar um arquivo de .xcarchive do Visual Studio?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 417D84FB-1BA9-4DB9-A683-66E960BA3D0D
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 23af3bf277ab68ffe93df2e1ee8ee64afc01828d
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
ms.locfileid: "30776801"
---
# <a name="is-it-possible-to-create-a-xcarchive-archive-from-visual-studio"></a>É possível criar um arquivo de .xcarchive do Visual Studio?

## <a name="for-xamarin-4"></a>Para Xamarin 4

A partir do Xamarin 4. x, agora é possível criar um `.xcarchive` do Windows, definindo o `ArchiveOnBuild` propriedade `true`. Por exemplo, usando `MSBuild` na linha de comando:

```bash
msbuild /p:Configuration=Release /p:ServerAddress=10.211.55.2 /p:ServerUser=xamUser /p:Platform=iPhone /p:ArchiveOnBuild=true /t:"Build" MyProject.csproj
```

O `.xcarchive` será colocada no `$HOME/Library/Developer/Xcode/Archives` diretório no host de compilação de Mac Xcode e Xamarin Studio pesquisar os arquivos de vídeo criado anteriormente.

Consulte este [Xamarin fóruns lançar](https://forums.xamarin.com/discussion/comment/156635/#Comment_156635) para alguns breves observações adicionais sobre o `ArchiveOnBuild` propriedade. Consulte a documentação [compilações de linha de comando do xamarin no Windows](~/ios/get-started/installation/windows/connecting-to-mac/index.md) para obter detalhes adicionais sobre o `ServerAddress` e `ServerUser` propriedades.

* * *

## <a name="for-xamarin-3-and-earlier"></a>Para Xamarin 3 e anterior

A extensão do Visual Studio Xamarin 3. x não fornecem um mecanismo para produzir `.xcarchive` arquivos. Dito isso, a lógica usada para criar `.xcarchive` arquiva no Xamarin Studio no Mac [é descrita aqui](https://bugzilla.xamarin.com/show_bug.cgi?id=35#c5), portanto, você provavelmente pode criar seu próprio `.xcarchive` manualmente se você desejou.

Mas vale a pena observar que não é necessário um `.xcarchive` para enviar à loja de aplicativos. Você pode enviar um arquivo de IPA enquanto ele está assinado com um perfil de distribuição de repositório do aplicativo (não um perfil de distribuição Ad Hoc).

Na verdade, você pode até mesmo zip o `.app` pacote (que está assinado com um perfil de distribuição do aplicativo armazenar) e enviar `.zip` arquivo para a loja de aplicativos.

Em ambos os casos, você pode usar o aplicativo do carregador do aplicativo para enviar o aplicativo (em vez do Xcode).

