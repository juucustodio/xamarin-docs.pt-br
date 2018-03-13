---
title: "Registrar uma impressão digital"
description: "Como definir até um bloqueio de tela e registrar uma impressão digital em um dispositivo Android ou o emulador."
ms.topic: article
ms.prod: xamarin
ms.assetid: 52092F63-00EE-4F8B-A49F-65C9CCBA7EF2
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 20e6d693f2a3eba54afaf1d3c7054ad75d7a7610
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="enrolling-a-fingerprint"></a>Registrar uma impressão digital

## <a name="enrolling-a-fingerprint-overview"></a>Registrar uma visão geral de impressão digital

Só é possível para um aplicativo do Android utilizar autenticação de impressões digitais, se o dispositivo já tiver sido configurado com autenticação de impressões digitais. Este guia sobre como registrar uma impressão digital em um dispositivo Android ou o emulador. Emuladores não tem o hardware real para executar uma verificação de impressão digital, mas é possível simular uma verificação de impressão digital com a Ajuda da ponte de depuração Android (descritas abaixo).  Este guia sobre como habilitar o bloqueio de tela em um dispositivo Android e registrar uma impressão digital para autenticação.

## <a name="requirements"></a>Requisitos

Para registrar uma impressão digital, você deve ter um dispositivo Android ou um emulador em execução API nível 23 (Android 6.0).

O uso do Android depurar ponte (ADB) requer familiaridade com o prompt de comando e o **adb** executável deve estar no caminho do seu Bash, o PowerShell, ou ambiente de Prompt de comando.

## <a name="configuring-a-screen-lock-and-enrolling-a-fingerprint"></a>Configurando um bloqueio de tela e registrando uma impressão digital 

Para configurar um bloqueio de tela, execute as seguintes etapas:

1. Vá para **Configurações > segurança**e selecione **bloqueio de tela**:

    ![Local da seleção de bloqueio de tela na tela de segurança](enrolling-fingerprint-images/testing-01.png)

2. A próxima tela exibida permitirá que você selecione e configure um dos métodos de segurança de bloqueio de tela: 

    ![Passe o dedo para selecionar, padrão, PIN ou senha](enrolling-fingerprint-images/testing-02.png)

   Selecione e execute um dos métodos de bloqueio de tela disponíveis.

3. Depois que o screenlock estiver configurada, retornar para o **Configurações > segurança** página e selecione **impressão digital**:

    ![Local da seleção de impressão digital na tela de segurança](enrolling-fingerprint-images/testing-03.png)

4. A partir daí, execute a sequência para adicionar uma impressão digital para o dispositivo:

    [![Sequência de capturas de tela para adicionar uma impressão digital para o dispositivo](enrolling-fingerprint-images/testing-04-sml.png)](enrolling-fingerprint-images/testing-04.png#lightbox)

5. Na tela final, você será solicitado a colocar o dedo sobre o scanner de impressão digital: 

    ![Tela que solicita que você coloque o dedo no sensor do](enrolling-fingerprint-images/testing-05.png)

    Se você estiver usando um dispositivo Android, conclua o processo com o toque com um clique para o scanner. 
    
    
### <a name="simulating-a-fingerprint-scan-on-the-emulator"></a>Simulando uma verificação de impressão digital no emulador

Em um emulador Android, é possível simular uma verificação de impressão digital usando a ponte de depuração do Android. No início de X do sistema operacional uma sessão de Terminal em Windows inicie um prompt de comando ou uma sessão do Powershell e execute `adb`:

```shell
$ adb -e emu finger touch 1
```

O valor de **1** é o _dedo\_id_ para o dedo foi "verificado". É um inteiro exclusivo que você atribui cada impressão digital virtual. No futuro, quando o aplicativo está em execução você pode executar esse mesmo ADB comando cada vez que o emulador solicita a você uma impressão digital, é possível executar o `adb` de comando e passá-lo a _dedo\_id_ para simular a verificação de impressão digital .

Após a conclusão da leitura da impressão digital, o Android notificará que foi adicionada a impressão digital:  

![Tela exibindo a impressão digital adicionado!](enrolling-fingerprint-images/testing-06.png)

## <a name="summary"></a>Resumo 

Este guia abordada como um bloqueio de tela de instalação e registrar uma impressão digital em um dispositivo Android ou em um emulador Android. 

