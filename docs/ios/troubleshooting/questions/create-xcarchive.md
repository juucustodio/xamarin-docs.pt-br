---
title: É possível criar um arquivo. xcarchive no Visual Studio?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 417D84FB-1BA9-4DB9-A683-66E960BA3D0D
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 04/03/2018
ms.openlocfilehash: 1c20534e1d4476ce7ff85d9ffd5ae8742c445983
ms.sourcegitcommit: aa9b9b203ab4cd6a6b4fd51e27d865e2abf582c1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2018
ms.locfileid: "39350204"
---
# <a name="is-it-possible-to-create-a-xcarchive-archive-from-visual-studio"></a>É possível criar um arquivo. xcarchive no Visual Studio?

## <a name="for-xamarin-4"></a>Para Xamarin 4

A partir do Xamarin 4. x, agora é possível criar uma `.xcarchive` do Windows, definindo o `ArchiveOnBuild` propriedade `true`. Por exemplo, usando `MSBuild` na linha de comando:

```bash
msbuild /p:Configuration=Release /p:ServerAddress=10.211.55.2 /p:ServerUser=xamUser /p:Platform=iPhone /p:ArchiveOnBuild=true /t:"Build" MyProject.csproj
```

O `.xcarchive` será colocado no `$HOME/Library/Developer/Xcode/Archives` diretório no host de build do Mac Xcode e Xamarin Studio pesquisar os arquivos de exibição criada anteriormente.

Consulte este [fóruns do Xamarin lançar](https://forums.xamarin.com/discussion/comment/156635/#Comment_156635) para alguns breves observações adicionais sobre o `ArchiveOnBuild` propriedade. Consulte a documentação [compilações de linha de comando do xamarin. IOS no Windows](~/ios/get-started/installation/windows/connecting-to-mac/index.md) para obter mais detalhes sobre o `ServerAddress` e `ServerUser` propriedades.

* * *

## <a name="for-xamarin-3-and-earlier"></a>Para o Xamarin 3 e versões anteriores

A extensão do Visual Studio 3. x Xamarin não fornece um mecanismo para produzir `.xcarchive` arquiva. Dito isso, a lógica usada para criar `.xcarchive` arquiva no Xamarin Studio no Mac [descrita aqui](https://bugzilla.xamarin.com/show_bug.cgi?id=35#c5), portanto, você provavelmente pode criar seu próprio `.xcarchive` manualmente se você quisesse.

Mas vale a pena observar que não é necessário um `.xcarchive` para enviar para a App Store. Você pode enviar um arquivo de IPA desde que ele seja assinado com um perfil de distribuição de Store de aplicativo (não um perfil de distribuição Ad Hoc).

Na verdade, você pode até mesmo Compacte os `.app` pacote (que é assinado com um perfil de distribuição de Store de aplicativo) e enviar `.zip` arquivo na app Store.

Em ambos os casos, você pode usar o aplicativo carregador de aplicativos para enviar o aplicativo (em vez de Xcode).

