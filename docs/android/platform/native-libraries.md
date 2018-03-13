---
title: Usando bibliotecas nativas
ms.topic: article
ms.prod: xamarin
ms.assetid: 7AA6CEC8-C09E-BBDA-FDD6-E40559143548
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/09/2018
ms.openlocfilehash: 7bd9a64ab7ea775688225ff5496773647174ebf8
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2018
---
# <a name="using-native-libraries"></a>Usando bibliotecas nativas

Xamarin dá suporte ao uso de bibliotecas nativas por meio do mecanismo de PInvoke padrão. Você também pode agrupar bibliotecas nativas adicionais que não fazem parte do sistema operacional em seu. Apk.

Para implantar uma biblioteca nativa com um aplicativo xamarin, adicione a biblioteca de binária para o projeto e defina seu **ação de compilação** para **AndroidNativeLibrary**.

Para implantar uma biblioteca nativa com um projeto de biblioteca de xamarin, adicione a biblioteca de binária para o projeto e defina seu **ação de compilação** para **EmbeddedNativeLibrary**.

Observe que, como Android oferece suporte a várias Interfaces de binários de aplicativo (ABIs), xamarin deve saber qual ABI a biblioteca nativa é compilada para.
Há duas maneiras de fazer isso:

1.  Caminho "detecção"
1.  Usando um `AndroidNativeLibrary/Abi` elemento dentro do arquivo de projeto


Com a detecção de caminho, o nome do diretório pai da biblioteca nativa é usado para especificar a ABI usada como destino pela biblioteca. Portanto, se você adicionar `lib/armeabi/libfoo.so` para o projeto, em seguida, ABI será ser "detectada" como `armeabi`.

Como alternativa, você pode editar o arquivo de projeto para especificar explicitamente a ABI usar:

```xml
<ItemGroup>
    <AndroidNativeLibrary Include="path/to/libfoo.so">
        <Abi>armeabi</Abi>
    </AndroidNativeLibrary>
</ItemGroup>
```

Para obter mais informações sobre como usar bibliotecas nativas, consulte [interoperabilidade com bibliotecas nativas](http://www.mono-project.com/docs/advanced/pinvoke/).

## <a name="debugging-native-code-with-visual-studio-2015"></a>Depurando código nativo com o Visual Studio 2015

Se você estiver usando *Visual Studio 2015*, você não precisa modificar os arquivos de projeto (conforme descrito acima).
Você pode criar e depurar C++ dentro de sua solução de xamarin, adicionando uma referência de projeto para um C++ **dinâmico biblioteca compartilhado (Android)** projeto.

Os desenvolvedores do Visual Studio C++ podem ver o [SanAngeles_NativeDebug](https://developer.xamarin.com/samples/monodroid/SanAngeles_NDK/) de exemplo para tentar a depuração de C++ do Visual Studio 2015 com Xamarin; e consulte a nossa [postagem de blog](https://blog.xamarin.com/build-and-debug-c-libraries-in-xamarin-android-apps-with-visual-studio-2015/) para obter mais informações.



## <a name="related-links"></a>Links relacionados

- [SanAngeles_NativeDebug (sample)](https://developer.xamarin.com/samples/monodroid/SanAngeles_NDK/)
