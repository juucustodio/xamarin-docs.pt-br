---
title: Diretrizes de autenticação de impressão digital
ms.prod: xamarin
ms.assetid: B40332CC-8123-4150-B47E-996214388842
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 08738a751fd630c6a413b1c7393f8007f5c97060
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68643555"
---
# <a name="fingerprint-authentication-guidance"></a>Diretrizes de autenticação de impressão digital

## <a name="fingerprint-authentication-guidance"></a>Diretrizes de autenticação de impressão digital

Agora que vimos os conceitos e APIs que envolvem a autenticação de impressão digital do Android 6,0, vamos discutir alguns conselhos gerais para o uso das APIs de impressão digital.

1. **Usar as APIs de compatibilidade v4 de biblioteca de suporte do Android** &ndash; Isso simplificará o código do aplicativo removendo a verificação de API do código e permitirá que um aplicativo se destine à maioria dos dispositivos possíveis.
2. **Fornecer alternativas para a autenticação de impressão digital** &ndash; A autenticação de impressão digital é uma maneira excelente e rápida para um aplicativo autenticar um usuário. no entanto, não é possível supor que ele sempre funcionará ou estará disponível. É possível que o scanner de impressão digital possa falhar, a lente talvez esteja suja, o usuário pode não ter configurado o dispositivo para usar a autenticação de impressão digital ou as impressões digitais desde que não tenham sido perdidas. Também é possível que o usuário não queira usar a autenticação de impressão digital com seu aplicativo. Por esses motivos, um aplicativo Android deve fornecer um processo de autenticação alternativo, como nome de usuário e senha.
3. **Usar o ícone de impressão digital do Google** &ndash; Todos os aplicativos devem usar o mesmo ícone de impressão digital fornecido pelo Google. O uso de um ícone padrão torna mais fácil para os usuários do Android reconhecer onde a autenticação de impressão digital em aplicativos é usada: 
    
    ![Ícone de impressão digital do Android](summary-images/ic-fp-40px.png)
    
4. **Notificar o usuário** &ndash; Um aplicativo deve exibir algum tipo de notificação para o usuário que o scanner de impressão digital está ativo e aguardando um toque ou passe o dedo. 

## <a name="summary"></a>Resumo

A autenticação de impressão digital é uma ótima maneira de permitir que um aplicativo Xamarin. Android Verifique rapidamente os usuários, facilitando para os usuários a interação com recursos confidenciais, como compras no aplicativo. Este guia abordou os conceitos e o código necessários para incorporar as APIs de impressão digital do Android 6,0 em seu aplicativo Xamarin. Android.

Primeiro, discutimos as próprias APIs de impressão `FingerprintManager` digital, `FingerprintManagerCompat`(e). Examinamos como a `FingerprintManager.AuthenticationCallbacks` classe abstrata deve ser estendida por um aplicativo e usada como um intermediário entre o hardware de impressão digital e o próprio aplicativo. Em seguida, examinamos como verificar a integridade dos resultados do scanner de impressão digital `Cipher` usando um objeto java. Por fim, fizemos um pouco sobre os testes descrevendo como registrar uma impressão digital em um dispositivo e usar o **ADB** para simular um passe de impressão digital em um emulador. 

Se ainda não tiver feito isso, você deve examinar o [aplicativo de exemplo](https://github.com/xamarin/monodroid-samples/tree/master/FingerprintGuide) que acompanha este guia. A [amostra da caixa de diálogo de impressão digital](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-fingerprintdialog) foi transportada de Java para Xamarin. Android e fornece outro exemplo de como adicionar autenticação de impressão digital a um aplicativo Android.



## <a name="related-links"></a>Links relacionados

- [Aplicativo de exemplo de guia de impressão digital](https://github.com/xamarin/monodroid-samples/tree/master/FingerprintGuide)
- [Exemplo de caixa de diálogo de impressão digital](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-fingerprintdialog)
- [Ícone de impressão digital](https://raw.githubusercontent.com/xamarin/monodroid-samples/master/FingerprintGuide/FingerprintSampleApp/Resources/drawable-hdpi/ic_fp_40px.png)
