---
title: Registrar uma impressão digital
description: Como configurar um bloqueio de tela e registrar uma impressão digital em um dispositivo Android ou Emulador.
ms.prod: xamarin
ms.assetid: 52092F63-00EE-4F8B-A49F-65C9CCBA7EF2
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: c0290dfa3b4aa301a07a589f78577899e8282158
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027588"
---
# <a name="enrolling-a-fingerprint"></a>Registrar uma impressão digital

## <a name="enrolling-a-fingerprint-overview"></a>Inscrevendo uma visão geral da impressão digital

Só é possível para um aplicativo Android aproveitar a autenticação de impressão digital se o dispositivo já tiver sido configurado com autenticação de impressão digital. Este guia discutirá como registrar uma impressão digital em um dispositivo Android ou Emulador. Os emuladores não possuem o hardware real para realizar uma varredura de impressões digitais, mas é possível simular uma digitalização de impressões digitais com a ajuda da Ponte de Depuração do Android (descrita abaixo).  Este guia discutirá como ativar o bloqueio de tela em um dispositivo Android e registrar uma impressão digital para autenticação.

## <a name="requirements"></a>Requisitos

Para registrar uma impressão digital, você deve ter um dispositivo Android ou um emulador executando a API nível 23 (Android 6.0).

O uso da Ponte de Depuração do Android (ADB) requer familiaridade com o prompt de comando, e o executável **adb** deve estar no path do seu ambiente Bash, PowerShell ou Command Prompt.

## <a name="configuring-a-screen-lock-and-enrolling-a-fingerprint"></a>Configurando um bloqueio de tela e registrando uma impressão digital 

Para configurar um bloqueio de tela, execute as seguintes etapas:

1. Vá para **Configurações > Segurança**e selecione **Bloqueio de tela**:

    ![Localização da seleção de bloqueio de tela na tela segurança](enrolling-fingerprint-images/testing-01.png)

2. A próxima tela exibida permitirá selecionar e configurar um dos métodos de segurança de bloqueio de tela: 

    ![Selecione Deslizar, Padrão, PIN ou Senha](enrolling-fingerprint-images/testing-02.png)

   Selecione e complete um dos métodos de bloqueio de tela disponíveis.

3. Uma vez configurado o bloqueio de tela, retorne à página **Configurações > Segurança** e selecione **Impressão Digital**:

    ![Localização da seleção de impressão digital na tela segurança](enrolling-fingerprint-images/testing-03.png)

4. A partir daí, siga a seqüência para adicionar uma impressão digital ao dispositivo:

    [![Seqüência de capturas de tela para adicionar uma impressão digital ao dispositivo](enrolling-fingerprint-images/testing-04-sml.png)](enrolling-fingerprint-images/testing-04.png#lightbox)

5. Na tela final, você é solicitado a colocar o dedo no scanner de impressões digitais: 

    ![Tela que pede para você colocar o dedo no sensor](enrolling-fingerprint-images/testing-05.png)

    Se você estiver usando um dispositivo Android, complete o processo tocando um dedo no scanner. 

### <a name="simulating-a-fingerprint-scan-on-the-emulator"></a>Simulando uma digitalização de impressões digitais no emulador

Em um emulador android, é possível simular uma digitalização de impressões digitais usando o Android Debug Bridge. No OS X inicie uma sessão de Terminal enquanto estiver `adb`no Windows inicie um prompt de comando ou uma sessão do Powershell e execute:

```shell
$ adb -e emu finger touch 1
```

O valor de **1** é o _dedo\_para_ o dedo que foi "escaneado". É um inteiro único que você atribui para cada impressão digital virtual. No futuro, quando o aplicativo estiver sendo executado, você pode executar este mesmo comando ADB `adb` cada vez que o emulador solicita uma impressão digital, você pode executar o comando e passar-lhe o _id do\_dedo_ para simular a digitalização das impressões digitais.

Depois que a digitalização de impressões digitais estiver concluída, o Android notificará você de que a impressão digital foi adicionada:  

![Tela exibindo impressão digital adicionada!](enrolling-fingerprint-images/testing-06.png)

## <a name="summary"></a>Resumo 

Este guia cobriu como configurar um bloqueio de tela e registrar uma impressão digital em um dispositivo Android ou em um emulador Android. 
