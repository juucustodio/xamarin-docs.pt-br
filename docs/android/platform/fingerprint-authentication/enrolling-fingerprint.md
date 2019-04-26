---
title: Registrar uma impressão digital
description: Como definir o backup de um bloqueio de tela e registrar uma impressão digital em um emulador ou dispositivo Android.
ms.prod: xamarin
ms.assetid: 52092F63-00EE-4F8B-A49F-65C9CCBA7EF2
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 18903a7d8f6c4033dc3ac7c4c0187a247d023bc1
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61026912"
---
# <a name="enrolling-a-fingerprint"></a>Registrar uma impressão digital

## <a name="enrolling-a-fingerprint-overview"></a>Registrar uma visão geral de impressão digital

Só é possível que um aplicativo do Android aproveitar a autenticação por impressão digital se o dispositivo já tiver sido configurado com autenticação por impressão digital. Este guia discute como registrar uma impressão digital em um emulador ou dispositivo Android. Emuladores não tem o hardware real para executar uma verificação de impressão digital, mas é possível simular uma verificação de impressão digital com a Ajuda de que o Android Debug Bridge (descritos abaixo).  Este guia discute como habilitar o bloqueio de tela em um dispositivo Android e registrar uma impressão digital para autenticação.

## <a name="requirements"></a>Requisitos

Para registrar uma impressão digital, você deve ter um dispositivo Android ou um emulador em execução API nível 23 (Android 6.0).

O uso do Android Debug Bridge (ADB) requer familiaridade com o prompt de comando e o **adb** executável deve estar no caminho do seu Bash, PowerShell, ou ambiente de Prompt de comando.

## <a name="configuring-a-screen-lock-and-enrolling-a-fingerprint"></a>Configurando um bloqueio de tela e registrar uma impressão digital 

Para configurar um bloqueio de tela, execute as seguintes etapas:

1. Vá para **Configurações > segurança**e selecione **bloqueio de tela**:

    ![Local da seleção de bloqueio de tela na tela de segurança](enrolling-fingerprint-images/testing-01.png)

2. A próxima tela exibida permitirá que você selecione e configure um dos métodos de segurança de bloqueio de tela: 

    ![Selecione passar o dedo, padrão, PIN ou senha](enrolling-fingerprint-images/testing-02.png)

   Selecione e conclua um dos métodos de bloqueio de tela disponíveis.

3. Depois que o screenlock estiver configurado, retornar para o **Configurações > segurança** página e selecione **impressão digital**:

    ![Local da seleção de impressão digital na tela de segurança](enrolling-fingerprint-images/testing-03.png)

4. A partir daí, siga a sequência para adicionar uma impressão digital para o dispositivo:

    [![Sequência de capturas de tela para adicionar uma impressão digital para o dispositivo](enrolling-fingerprint-images/testing-04-sml.png)](enrolling-fingerprint-images/testing-04.png#lightbox)

5. Na tela final, você será solicitado a colocar seu dedo no scanner de impressão digital: 

    ![Tela que solicita que você coloque o dedo no sensor do](enrolling-fingerprint-images/testing-05.png)

    Se você estiver usando um dispositivo Android, conclua o processo ao tocar em um dedo ao scanner. 
    
    
### <a name="simulating-a-fingerprint-scan-on-the-emulator"></a>Simulando uma verificação de impressão digital no emulador

Em um emulador do Android, é possível simular uma verificação de impressão digital usando o Android Debug Bridge. No início dos X uma sessão de Terminal no Windows inicie um prompt de comando ou uma sessão do Powershell e execute `adb`:

```shell
$ adb -e emu finger touch 1
```

O valor de **1** é o _dedo\_id_ para o dedo que estava "verificado". Ele é um inteiro exclusivo que você atribui cada virtual por impressão digital. No futuro, quando o aplicativo está em execução, você pode executar esse mesmo ADB comando sempre que o emulador solicita a você para uma impressão digital, você pode executar o `adb` de comando e passe-o _dedo\_id_ para simular a verificação de impressão digital .

Após a conclusão da leitura da impressão digital, o Android notificará que a impressão digital foi adicionada:  

![Tela exibindo a impressão digital adicionado!](enrolling-fingerprint-images/testing-06.png)

## <a name="summary"></a>Resumo 

Este guia abordou como um bloqueio de tela de instalação e registrar uma impressão digital em um dispositivo Android ou em um emulador do Android. 

