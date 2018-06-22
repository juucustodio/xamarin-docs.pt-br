---
title: Guia de autenticação de impressão digital
ms.prod: xamarin
ms.assetid: B40332CC-8123-4150-B47E-996214388842
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 2b66c3660f6d8af9217089a7615784957fcc6ed7
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
ms.locfileid: "30763300"
---
# <a name="fingerprint-authentication-guidance"></a>Guia de autenticação de impressão digital

## <a name="fingerprint-authentication-guidance"></a>Guia de autenticação de impressão digital

Agora que já vimos os conceitos e autenticação de impressão digital APIs ao redor Android 6.0, vamos discutir algumas dicas gerais para o uso das APIs de impressão digital.

1. **Usar as APIs de compatibilidade de biblioteca de suporte Android v4** &ndash; isso simplificará o código do aplicativo, removendo a verificação de API do código e permitir que um aplicativo para o máximo possível de dispositivos de destino.
2. **Fornecem alternativas para autenticação de impressões digitais** &ndash; autenticação de impressão digital é uma ótima maneira rápida para um aplicativo autenticar um usuário, no entanto, ele não é possível supor que ele sempre funcionará ou estar disponível. É possível que o scanner de impressão digital pode falhar, talvez a Lente ser sujo, o usuário não pode ter configurado o dispositivo para usar a autenticação de impressão digital ou as impressões digitais já passaram ausentes. Também é possível que o usuário não poderá usar a autenticação de impressão digital com seu aplicativo. Por esses motivos, um aplicativo do Android deve fornecer um processo de autenticação alternativo, como nome de usuário e senha.
3. **Use o ícone de impressão digital do Google** &ndash; todos os aplicativos devem usar o mesmo ícone de impressão digital fornecido pelo Google. O uso de um ícone padrão torna mais fácil para os usuários do Android reconhecer onde em aplicativos em autenticação de impressão digital é usada: 
    
    ![Ícone de impressão digital Android](summary-images/ic-fp-40px.png)
    
4. **Notificar o usuário** &ndash; um aplicativo deve exibir algum tipo de notificação para o usuário que o scanner de impressão digital está ativo e aguardando um toque ou passe o dedo. 

## <a name="summary"></a>Resumo

Autenticação de impressão digital é uma ótima maneira de permitir que um aplicativo xamarin verificar rapidamente se os usuários, tornando mais fácil para os usuários interajam com recursos confidenciais, como compras no aplicativo. Este guia discutidos os conceitos e o código que é necessário para incorporar a impressão digital de Android 6.0 da API em seu aplicativo xamarin.

Primeiro discutimos a impressão digital da API do, `FingerprintManager` (e `FingerprintManagerCompat`). Examinamos como o `FingerprintManager.AuthenticationCallbacks` classe abstrata deve ser estendida por um aplicativo e usada como um intermediário entre o hardware de impressão digital e o aplicativo em si. Em seguida, examinamos como verificar a integridade dos resultados de scanner de impressão digital usando um Java `Cipher` objeto. Por fim, abordamos um pouco sobre testes que descreve como registrar uma impressão digital em um dispositivo e usando **adb** para simular um passe o dedo para impressão digital em um emulador. 

Se você ainda não fez isso, você deve examinar o [aplicativo de exemplo](https://github.com/xamarin/monodroid-samples/tree/master/FingerprintGuide) que acompanha este guia. O [exemplo de caixa de diálogo de impressão digital](https://developer.xamarin.com/samples/monodroid/android-m/FingerprintDialog/) portado do Java para xamarin e oferece outro exemplo de como adicionar autenticação de impressão digital para um aplicativo do Android.



## <a name="related-links"></a>Links relacionados

- [Aplicativo de exemplo do guia de impressão digital](https://github.com/xamarin/monodroid-samples/tree/master/FingerprintGuide)
- [Exemplo de caixa de diálogo de impressão digital](https://developer.xamarin.com/samples/monodroid/android-m/FingerprintDialog/)
- [Ícone de impressão digital](https://developer.android.comhttps://developer.xamarin.com/samples/FingerprintDialog/res/drawable-hdpi/ic_fp_40px.html)
