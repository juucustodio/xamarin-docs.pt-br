---
title: Orientação de autenticação de impressões digitais
ms.prod: xamarin
ms.assetid: B40332CC-8123-4150-B47E-996214388842
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: e955d4f96724bd5682e7d0e6db2c36fa1b7810f4
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027426"
---
# <a name="fingerprint-authentication-guidance"></a>Orientação de autenticação de impressões digitais

## <a name="fingerprint-authentication-guidance"></a>Orientação de autenticação de impressões digitais

Agora que vimos os conceitos e APIs em torno da autenticação de impressão digital do Android 6.0, vamos discutir alguns conselhos gerais para o uso das APIs de impressão digital.

1. **Use as APIs** &ndash; de compatibilidade v4 da Biblioteca de Suporte do Android Isso simplificará o código do aplicativo removendo a verificação da API do código e permitirá que um aplicativo direcione a maioria dos dispositivos possíveis.
2. **Fornecer alternativas à autenticação** &ndash; de impressão digital A autenticação de impressão digital é uma ótima e rápida maneira de um aplicativo autenticar um usuário, no entanto, não se pode presumir que ele sempre funcionará ou estará disponível. É possível que o scanner de impressões digitais possa falhar, a lente talvez esteja suja, o usuário pode não ter configurado o dispositivo para usar autenticação de impressão digital, ou as impressões digitais desapareceram desde então. Também é possível que o usuário não deseje usar autenticação de impressão digital com seu aplicativo. Por essas razões, um aplicativo para Android deve fornecer um processo de autenticação alternativo, como nome de usuário e senha.
3. **Use o ícone** &ndash; de impressão digital do Google Todos os aplicativos devem usar o mesmo ícone de impressão digital fornecido pelo Google. O uso de um ícone padrão facilita que os usuários de Android reconheçam onde em aplicativos a autenticação de impressão digital é usada: 
    
    ![Ícone de impressão digital do Android](summary-images/ic-fp-40px.png)
    
4. **Notifique o Usuário** &ndash; Um aplicativo deve exibir algum tipo de notificação ao usuário de que o scanner de impressões digitais está ativo e aguardando um toque ou deslize. 

## <a name="summary"></a>Resumo

A autenticação de impressões digitais é uma ótima maneira de permitir que um aplicativo Xamarin.Android verifique rapidamente os usuários, facilitando a interação dos usuários com recursos sensíveis, como compras no aplicativo. Este guia discutiu os conceitos e códigos necessários para incorporar as API de impressão digital do Android 6.0 no seu aplicativo Xamarin.Android.

Primeiro discutimos a própria API `FingerprintManager` da `FingerprintManagerCompat`impressão digital, (e ). Examinamos como `FingerprintManager.AuthenticationCallbacks` a classe abstrata deve ser estendida por um aplicativo e usada como intermediário entre o hardware de impressão digital e o próprio aplicativo. Em seguida, examinamos como verificar a integridade dos `Cipher` resultados do scanner de impressões digitais usando um objeto Java. Finalmente, tocamos um pouco nos testes, descrevendo como registrar uma impressão digital em um dispositivo e usando **um adb** para simular um deslize de impressão digital em um emulador. 

Se você ainda não fez isso, você deve olhar para a [aplicação de amostra](https://github.com/xamarin/monodroid-samples/tree/master/FingerprintGuide) que acompanha este guia. A [Amostra de Diálogo de Impressão Digital](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-fingerprintdialog) foi portada de Java para Xamarin.Android e fornece outro exemplo sobre como adicionar autenticação de impressão digital a um aplicativo Android.

## <a name="related-links"></a>Links relacionados

- [Aplicativo de amostra de guia de impressão digital](https://github.com/xamarin/monodroid-samples/tree/master/FingerprintGuide)
- [Amostra de diálogo de impressão digital](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-fingerprintdialog)
- [Ícone de impressão digital](https://raw.githubusercontent.com/xamarin/monodroid-samples/master/FingerprintGuide/FingerprintSampleApp/Resources/drawable-hdpi/ic_fp_40px.png)
