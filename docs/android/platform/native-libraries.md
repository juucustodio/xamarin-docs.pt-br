---
title: Usar bibliotecas nativas
ms.prod: xamarin
ms.assetid: 7AA6CEC8-C09E-BBDA-FDD6-E40559143548
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: 1b0771a0ccc2597ebd800468b82044e4020d9d94
ms.sourcegitcommit: 3489c281c9eb5ada2cddf32d73370943342a1082
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2019
ms.locfileid: "58854607"
---
# <a name="using-native-libraries"></a>Usar bibliotecas nativas

Xamarin. Android dá suporte ao uso de bibliotecas nativas por meio do mecanismo de PInvoke padrão. Você também pode agrupar as bibliotecas nativas adicionais que não fazem parte do sistema operacional em seu Apk.

Para implantar uma biblioteca nativa com um aplicativo xamarin. Android, adicione a biblioteca de binária ao projeto e defina suas **ação de compilação** à **AndroidNativeLibrary**.

Para implantar uma biblioteca nativa com um projeto de biblioteca do xamarin. Android, adicione a biblioteca de binária ao projeto e defina suas **ação de compilação** à **EmbeddedNativeLibrary**.

Observe que, como Android oferece suporte a vários aplicativos ABIs (Interfaces binárias), xamarin. Android precisa saber qual ABI a biblioteca nativa é compilada para.
Há duas maneiras de fazer isso:

1.  Caminho "detecção" de
1.  Usando um `AndroidNativeLibrary/Abi` elemento dentro do arquivo de projeto


Com a detecção de caminho, o nome do diretório pai da biblioteca nativa é usado para especificar a ABI usada como destino pela biblioteca. Portanto, se você adicionar `lib/armeabi/libfoo.so` ao projeto, em seguida, a ABI será "detectada" como `armeabi`.

Como alternativa, você pode editar seu arquivo de projeto para especificar explicitamente a ABI, use:

```xml
<ItemGroup>
    <AndroidNativeLibrary Include="path/to/libfoo.so">
        <Abi>armeabi</Abi>
    </AndroidNativeLibrary>
</ItemGroup>
```

Para obter mais informações sobre como usar bibliotecas nativas, consulte [interoperabilidade com bibliotecas nativas](https://www.mono-project.com/docs/advanced/pinvoke/).

## <a name="debugging-native-code-with-visual-studio"></a>Depurando código nativo com o Visual Studio

Se você estiver usando *Visual Studio de 2019* ou *Visual Studio 2017*, você não precisa modificar os arquivos de projeto, conforme descrito acima.
Você pode compilar e depurar C++ dentro de sua solução xamarin. Android, adicionando uma referência de projeto para um C++ **dinâmico biblioteca compartilhada (Android)** projeto.

Para depurar código C++ nativo em seu projeto, siga estas etapas:

1. Clique duas vezes no projeto **propriedades** e selecione o **opções do Android** página.
2. Role para baixo até **opções de depuração**.
3. No **depurador** menu suspenso, selecione **C++** (em vez do padrão **.Net (Xamarin)**).

Visual Studio C++ os desenvolvedores podem ver o [SanAngeles_NativeDebug](https://developer.xamarin.com/samples/monodroid/SanAngeles_NDK/) exemplo tentar depurar C++ de 2019 do Visual Studio ou Visual Studio 2017 com Xamarin; e consulte a nossa [postagem do blog](https://blog.xamarin.com/build-and-debug-c-libraries-in-xamarin-android-apps-with-visual-studio-2015/) para obter mais informações informações.



## <a name="related-links"></a>Links relacionados

- [SanAngeles_NativeDebug (amostra)](https://developer.xamarin.com/samples/monodroid/SanAngeles_NDK/)
- [Desenvolver aplicativos nativos do Xamarin Android](https://blogs.msdn.microsoft.com/vcblog/2015/02/23/developing-xamarin-android-native-applications/)
