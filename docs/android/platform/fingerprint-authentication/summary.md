---
title: Diretrizes de autenticação por impressão digital
ms.prod: xamarin
ms.assetid: B40332CC-8123-4150-B47E-996214388842
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 3baaaef22916354a6fab28b0b0c6358c9bc25c91
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50114906"
---
# <a name="fingerprint-authentication-guidance"></a>Diretrizes de autenticação por impressão digital

## <a name="fingerprint-authentication-guidance"></a>Diretrizes de autenticação por impressão digital

Agora que temos visto os conceitos e as APIs em torno do Android 6.0 autenticação de impressão digital, vamos discutir algumas dicas gerais para o uso das APIs de impressão digital.

1. **Use as APIs de compatibilidade de biblioteca de suporte ao Android v4** &ndash; isso simplificará o código do aplicativo, removendo a verificação de API do código e permitir que um aplicativo para o máximo possível de dispositivos de destino.
2. **Fornecer alternativas para autenticação por impressão digital** &ndash; autenticação por impressão digital é uma maneira excelente e rápida para um aplicativo autenticar um usuário, no entanto, ele não é possível supor que ele sempre funcionará ou estar disponível. É possível que o scanner de impressão digital pode falhar, talvez a Lente ser sujo, o usuário pode não ter configurado o dispositivo para usar a autenticação por impressão digital ou as impressões digitais já estão ausentes. Também é possível que o usuário não poderá usar a autenticação por impressão digital com o aplicativo. Por esses motivos, um aplicativo do Android deve fornecer um processo de autenticação alternativo, como nome de usuário e senha.
3. **Use o ícone de impressão digital do Google** &ndash; todos os aplicativos devem usar o mesmo ícone de impressão digital fornecido pelo Google. O uso de um ícone padrão torna mais fácil para os usuários do Android reconhecer onde em aplicativos de autenticação por impressão digital é usada: 
    
    ![Ícone de impressão digital de Android](summary-images/ic-fp-40px.png)
    
4. **Notificar o usuário** &ndash; um aplicativo deve exibir algum tipo de notificação para o usuário que o scanner de impressão digital está ativo e aguardando um toque ou passe o dedo. 

## <a name="summary"></a>Resumo

Autenticação por impressão digital é uma ótima maneira de permitir que o aplicativo xamarin. Android verificar rapidamente se os usuários, tornando mais fácil para os usuários interajam com recursos confidenciais, como compras no aplicativo. Este guia abordou os conceitos e o código que é necessário para incorporar a impressão digital do Android 6.0 de API em seu aplicativo xamarin. Android.

Pela primeira vez que a impressão digital da API do por conta própria, que abordamos `FingerprintManager` (e `FingerprintManagerCompat`). Examinamos como o `FingerprintManager.AuthenticationCallbacks` classe abstrata deve ser estendido por um aplicativo e usado como um intermediário entre o hardware de impressão digital e o aplicativo em si. Em seguida, examinamos como verificar a integridade dos resultados de scanner de impressão digital usando um Java `Cipher` objeto. Por fim, abordamos um pouco sobre testes, que descreve como registrar uma impressão digital em um dispositivo e usando **adb** para simular um dedo da impressão digital em um emulador. 

Se você ainda não fez isso, você deve examinar a [aplicativo de exemplo](https://github.com/xamarin/monodroid-samples/tree/master/FingerprintGuide) que acompanha este guia. O [exemplo de caixa de diálogo de impressão digital](https://developer.xamarin.com/samples/monodroid/android-m/FingerprintDialog/) foi movido do Java para o xamarin. Android e oferece outro exemplo de como adicionar autenticação por impressão digital para um aplicativo do Android.



## <a name="related-links"></a>Links relacionados

- [Aplicativo de exemplo do guia de impressão digital](https://github.com/xamarin/monodroid-samples/tree/master/FingerprintGuide)
- [Exemplo de caixa de diálogo de impressão digital](https://developer.xamarin.com/samples/monodroid/android-m/FingerprintDialog/)
- [Ícone de impressão digital](https://developer.android.com https://developer.xamarin.com/samples/FingerprintDialog/res/drawable-hdpi/ic_fp_40px.html)
