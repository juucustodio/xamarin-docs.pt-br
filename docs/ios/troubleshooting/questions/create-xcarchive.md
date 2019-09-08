---
title: É possível criar um arquivo. xcarchive do Visual Studio?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 417D84FB-1BA9-4DB9-A683-66E960BA3D0D
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 04/03/2018
ms.openlocfilehash: 1b078b8cb4d1129127997e9fabdd0b128e09c90f
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70769361"
---
# <a name="is-it-possible-to-create-a-xcarchive-archive-from-visual-studio"></a>É possível criar um arquivo. xcarchive do Visual Studio?

## <a name="for-xamarin-4"></a>Para o Xamarin 4

A partir do Xamarin 4. x, agora é possível criar um `.xcarchive` do Windows definindo a `ArchiveOnBuild` Propriedade como `true`. Por exemplo, usando `MSBuild` na linha de comando:

```bash
msbuild /p:Configuration=Release /p:ServerAddress=10.211.55.2 /p:ServerUser=xamUser /p:Platform=iPhone /p:ArchiveOnBuild=true /t:"Build" MyProject.csproj
```

O `.xcarchive` será colocado `$HOME/Library/Developer/Xcode/Archives` no diretório no host de Build do Mac que o Xcode e o Xamarin Studio Search para exibir os arquivos criados anteriormente.

Consulte esta [postagem de fóruns do Xamarin](https://forums.xamarin.com/discussion/comment/156635/#Comment_156635) para ver algumas breves observações adicionais sobre a `ArchiveOnBuild` propriedade. Consulte a documentação sobre o [Xamarin. Ios linha de comando Builds no Windows](~/ios/get-started/installation/windows/connecting-to-mac/index.md) para obter `ServerAddress` detalhes `ServerUser` adicionais sobre as propriedades e.

* * *

## <a name="for-xamarin-3-and-earlier"></a>Para o Xamarin 3 e anterior

A extensão do Xamarin 3. x do Visual Studio não fornece um mecanismo para `.xcarchive` produzir arquivos mortos. Dito isso, a lógica usada para criar `.xcarchive` arquivos mortos em Xamarin Studio no Mac [é descrita aqui](https://bugzilla.xamarin.com/show_bug.cgi?id=35#c5), portanto, você provavelmente pode criar `.xcarchive` seu próprio manualmente, se desejar.

Mas vale a pena observar que você não precisa de um `.xcarchive` para enviar para a loja de aplicativos. Você pode enviar um arquivo IPA desde que ele seja assinado com um perfil de distribuição da loja de aplicativos (não um perfil de distribuição ad hoc).

Na verdade, você pode até apenas compactar o `.app` pacote (que é assinado com um perfil de distribuição da loja de aplicativos) e `.zip` enviar esse arquivo para a loja de aplicativos.

Em ambos os casos, você pode usar o aplicativo carregador de aplicativos para enviar o aplicativo (em vez de Xcode).
