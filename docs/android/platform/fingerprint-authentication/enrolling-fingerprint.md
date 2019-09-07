---
title: Registrar uma impressão digital
description: Como configurar um bloqueio de tela e registrar uma impressão digital em um dispositivo Android ou emulador.
ms.prod: xamarin
ms.assetid: 52092F63-00EE-4F8B-A49F-65C9CCBA7EF2
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: f52be16a81f3c8047997e1f4a88e13f6b940db14
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70756413"
---
# <a name="enrolling-a-fingerprint"></a>Registrar uma impressão digital

## <a name="enrolling-a-fingerprint-overview"></a>Registrando uma visão geral da impressão digital

Só é possível que um aplicativo Android Aproveite a autenticação de impressão digital se o dispositivo já tiver sido configurado com autenticação de impressão digital. Este guia explicará como registrar uma impressão digital em um dispositivo Android ou emulador. Os emuladores não têm o hardware real para executar uma verificação de impressão digital, mas é possível simular uma verificação de impressão digital com a ajuda do Android Debug Bridge (descrito abaixo).  Este guia explicará como habilitar o bloqueio de tela em um dispositivo Android e registrar uma impressão digital para autenticação.

## <a name="requirements"></a>Requisitos

Para registrar uma impressão digital, você deve ter um dispositivo Android ou um emulador executando a API de nível 23 (Android 6,0).

O uso do Android Debug Bridge (ADB) requer familiaridade com o prompt de comando, e o executável do **ADB** deve estar no caminho do seu Bash, PowerShell ou ambiente de prompt de comando.

## <a name="configuring-a-screen-lock-and-enrolling-a-fingerprint"></a>Configurando um bloqueio de tela e registrando uma impressão digital 

Para configurar um bloqueio de tela, execute as seguintes etapas:

1. Vá para **configurações > segurança**e selecione **bloqueio de tela**:

    ![Local da seleção de bloqueio de tela na tela de segurança](enrolling-fingerprint-images/testing-01.png)

2. A próxima tela exibida permitirá que você selecione e configure um dos métodos de segurança de bloqueio de tela: 

    ![Selecione o dedo, o padrão, o PIN ou a senha](enrolling-fingerprint-images/testing-02.png)

   Selecione e conclua um dos métodos de bloqueio de tela disponíveis.

3. Quando o screenlock estiver configurado, retorne para a página **configurações > segurança** e selecione **impressão digital**:

    ![Local da seleção de impressão digital na tela de segurança](enrolling-fingerprint-images/testing-03.png)

4. A partir daí, siga a sequência para adicionar uma impressão digital ao dispositivo:

    [![Sequência de capturas de tela para adicionar uma impressão digital ao dispositivo](enrolling-fingerprint-images/testing-04-sml.png)](enrolling-fingerprint-images/testing-04.png#lightbox)

5. Na tela final, você será solicitado a inserir o dedo no scanner de impressão digital: 

    ![Tela que solicita que você coloque o dedo no sensor](enrolling-fingerprint-images/testing-05.png)

    Se você estiver usando um dispositivo Android, conclua o processo tocando um dedo no scanner. 

### <a name="simulating-a-fingerprint-scan-on-the-emulator"></a>Simulando uma verificação de impressão digital no emulador

Em um emulador do Android, é possível simular uma verificação de impressão digital usando o Android Debug Bridge. No OS X, inicie uma sessão de terminal enquanto estiver no Windows, inicie um prompt de comando ou `adb`uma sessão do PowerShell e execute:

```shell
$ adb -e emu finger touch 1
```

O valor de **1** é a _ID\_do dedo_ para o dedo que foi "examinado". É um inteiro exclusivo que você atribui para cada impressão digital virtual. No futuro, quando o aplicativo estiver em execução, você poderá executar esse mesmo comando ADB sempre que o emulador solicitar uma impressão digital, você poderá executar `adb` o comando e passá-lo a _ID do dedo\__ para simular a verificação de impressão digital.

Depois que a verificação de impressão digital for concluída, o Android o notificará de que a impressão digital foi adicionada:  

![Exibição de impressão digital adicionada à tela!](enrolling-fingerprint-images/testing-06.png)

## <a name="summary"></a>Resumo 

Este guia abordou como configurar um bloqueio de tela e registrar uma impressão digital em um dispositivo Android ou em um emulador do Android. 
