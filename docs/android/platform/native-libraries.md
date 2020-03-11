---
title: Usar bibliotecas nativas
ms.prod: xamarin
ms.assetid: 7AA6CEC8-C09E-BBDA-FDD6-E40559143548
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/09/2018
ms.openlocfilehash: b7d69e99327aa3d3e3e1f5e5dbc61697d1fb9b71
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2020
ms.locfileid: "75489161"
---
# <a name="using-native-libraries"></a>Usar bibliotecas nativas

O Xamarin. Android dá suporte ao uso de bibliotecas nativas por meio do mecanismo PInvoke padrão. Você também pode agrupar bibliotecas nativas adicionais que não fazem parte do sistema operacional em seu. apk.

Para implantar uma biblioteca nativa com um aplicativo Xamarin. Android, adicione o binário da biblioteca ao projeto e defina sua **ação de compilação** como **AndroidNativeLibrary**.

Para implantar uma biblioteca nativa com um projeto de biblioteca Xamarin. Android, adicione o binário da biblioteca ao projeto e defina sua **ação de Build** como **EmbeddedNativeLibrary**.

Observe que, como o Android dá suporte a várias interfaces binárias de aplicativos (ABIs), o Xamarin. Android deve saber para qual ABI a biblioteca nativa é compilada.
Há duas maneiras de fazer isso:

1. Caminho "farejando"
1. Usando um elemento `AndroidNativeLibrary/Abi` dentro do arquivo de projeto

Com a detecção de caminho, o nome do diretório pai da biblioteca nativa é usado para especificar a ABI usada como destino pela biblioteca. Portanto, se você adicionar `lib/armeabi/libfoo.so` ao projeto, a ABI será "sniffed" como `armeabi`.

Como alternativa, você pode editar o arquivo de projeto para especificar explicitamente a ABI a ser usada:

```xml
<ItemGroup>
    <AndroidNativeLibrary Include="path/to/libfoo.so">
        <Abi>armeabi</Abi>
    </AndroidNativeLibrary>
</ItemGroup>
```

Para obter mais informações sobre como usar bibliotecas nativas, consulte [interoperabilidade com bibliotecas nativas](https://www.mono-project.com/docs/advanced/pinvoke/).

## <a name="debugging-native-code-with-visual-studio"></a>Depurando código nativo com o Visual Studio

Se você estiver usando o *visual studio 2019* ou o *Visual Studio 2017*, não precisará modificar os arquivos do projeto conforme descrito acima.
Você pode criar e depurar C++ dentro de sua solução Xamarin. Android adicionando uma referência de projeto a C++ um projeto de **biblioteca compartilhada dinâmica (Android)** .

Para depurar o C++ código nativo em seu projeto, siga estas etapas:

1. Clique duas vezes em **Propriedades** do projeto e selecione a página **Opções do Android** .
2. Role para baixo até **Opções de depuração**.
3. No menu suspenso do **depurador** , selecione **C++** (em vez do **.NET padrão (Xamarin)** ).

Os desenvolvedores C++ do Visual Studio podem ver o exemplo de [SanAngeles_NativeDebug](https://docs.microsoft.com/samples/xamarin/monodroid-samples/sanangeles-ndk) para experimentar a depuração C++ do visual studio 2019 ou do Visual Studio 2017 com o Xamarin; e consulte nossa [postagem no blog](https://blog.xamarin.com/build-and-debug-c-libraries-in-xamarin-android-apps-with-visual-studio-2015/) para obter mais informações.

## <a name="related-links"></a>Links relacionados

- [SanAngeles_NativeDebug (exemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/sanangeles-ndk)
- [Desenvolvendo aplicativos do Xamarin Android nativo](https://blogs.msdn.microsoft.com/vcblog/2015/02/23/developing-xamarin-android-native-applications/)
