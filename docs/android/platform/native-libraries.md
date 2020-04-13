---
title: Usar bibliotecas nativas
ms.prod: xamarin
ms.assetid: 7AA6CEC8-C09E-BBDA-FDD6-E40559143548
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/09/2018
ms.openlocfilehash: b7d69e99327aa3d3e3e1f5e5dbc61697d1fb9b71
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "75489161"
---
# <a name="using-native-libraries"></a>Usar bibliotecas nativas

Xamarin.Android suporta o uso de bibliotecas nativas através do mecanismo Padrão PInvoke. Você também pode empacotar bibliotecas nativas adicionais que não fazem parte do Sistema Operacional em seu .apk.

Para implantar uma biblioteca nativa com um aplicativo Xamarin.Android, adicione o binário da biblioteca ao projeto e defina sua **Build Action** para **AndroidNativeLibrary**.

Para implantar uma biblioteca nativa com um projeto de biblioteca Xamarin.Android, adicione o binário da biblioteca ao projeto e defina sua **Build Action** **como EmbeddedNativeLibrary**.

Observe que, uma vez que o Android suporta várias ABIs Application Binary Interfaces (ABIs), o Xamarin.Android deve saber para qual ABI a biblioteca nativa foi construída.
Há duas maneiras de fazer isso:

1. Caminho "farejando"
1. Usando um `AndroidNativeLibrary/Abi` elemento dentro do arquivo do projeto

Com a detecção de caminho, o nome do diretório pai da biblioteca nativa é usado para especificar a ABI usada como destino pela biblioteca. Assim, se `lib/armeabi/libfoo.so` você adicionar ao projeto, então o ABI `armeabi`será "farejado" como .

Alternativamente, você pode editar seu arquivo de projeto para especificar explicitamente o ABI para usar:

```xml
<ItemGroup>
    <AndroidNativeLibrary Include="path/to/libfoo.so">
        <Abi>armeabi</Abi>
    </AndroidNativeLibrary>
</ItemGroup>
```

Para obter mais informações sobre o uso de bibliotecas nativas, consulte [Interop com bibliotecas nativas](https://www.mono-project.com/docs/advanced/pinvoke/).

## <a name="debugging-native-code-with-visual-studio"></a>Depuração de código nativo com visual studio

Se você estiver usando *o Visual Studio 2019* ou *o Visual Studio 2017,* você não precisa modificar seus arquivos de projeto como descrito acima.
Você pode construir e depurar C++ dentro da sua solução Xamarin.Android adicionando uma referência de projeto a um projeto C++ **Dynamic Shared Library (Android).**

Para depurar o código C++ nativo em seu projeto, siga estas etapas:

1. Clique duas vezes em **Propriedades do** projeto e selecione a página Opções do **Android.**
2. Desça até **as opções de depuração**.
3. No menu **suspenso Debugger,** selecione **C++** (em vez do padrão **.NET (Xamarin).**

Os desenvolvedores do Visual Studio C++ podem ver a amostra [SanAngeles_NativeDebug](https://docs.microsoft.com/samples/xamarin/monodroid-samples/sanangeles-ndk) para tentar depurar C++ do Visual Studio 2019 ou visual Studio 2017 com Xamarin; e consulte nosso [blog para](https://blog.xamarin.com/build-and-debug-c-libraries-in-xamarin-android-apps-with-visual-studio-2015/) obter mais informações.

## <a name="related-links"></a>Links relacionados

- [SanAngeles_NativeDebug (amostra)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/sanangeles-ndk)
- [Desenvolvendo aplicativos nativos do Xamarin Android](https://blogs.msdn.microsoft.com/vcblog/2015/02/23/developing-xamarin-android-native-applications/)
