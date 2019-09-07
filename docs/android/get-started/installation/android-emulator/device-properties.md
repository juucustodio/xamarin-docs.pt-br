---
title: Editar propriedades do Dispositivo Virtual Android
description: Este artigo explica como usar o Android Device Manager para editar as propriedades de perfil de um dispositivo virtual Android.
zone_pivot_groups: platform
ms.prod: xamarin
ms.assetid: 3E33C136-8042-4184-A40C-3200D8CD99CB
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/30/2018
ms.openlocfilehash: 7a4312120ce69493074f28fb3e202f437d3e69e8
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70758030"
---
# <a name="editing-android-virtual-device-properties"></a>Editar propriedades do Dispositivo Virtual Android

_Este artigo explica como usar o Android Device Manager para editar as propriedades de perfil de um dispositivo virtual Android._

::: zone pivot="windows"

## <a name="android-device-manager-on-windows"></a>Android Device Manager no Windows

O **Android Device Manager** é compatível com a edição de propriedades de perfis individuais de dispositivos virtuais Android. As telas **Novo Dispositivo** e **Editar Dispositivo** listam as propriedades do dispositivo virtual na primeira coluna, com os valores correspondentes de cada propriedade na segunda coluna (como mostrado neste exemplo): 

[![Tela Novo Dispositivo de exemplo](device-properties-images/win/01-new-device-editor-sml.png)](device-properties-images/win/01-new-device-editor.png#lightbox)

Ao selecionar uma propriedade, uma descrição detalhada é exibida à direita. É possível modificar as *propriedades de perfil de hardware* e as *propriedades de AVD*. As propriedades de perfil de hardware (como `hw.ramSize` e `hw.accelerometer`) descrevem as características físicas do dispositivo emulado. Essas características são o tamanho da tela, a quantidade de RAM disponível e se existe um acelerômetro. As propriedades de AVD especificam a operação do AVD quando ele é executado. Por exemplo, as propriedades de AVD podem ser configuradas para especificar como o AVD usa a placa gráfica do computador de desenvolvimento para renderização.

É possível alterar as propriedades usando as seguintes diretrizes:

- Para alterar uma propriedade booliana, clique na marca de seleção à direita dela:

    ![Alterar uma propriedade booliana](device-properties-images/win/02-boolean-value.png)

- Para alterar uma propriedade *enum* (enumerada), clique na seta para baixo à direita da propriedade e escolha um novo valor.

    ![Alterar uma propriedade de enumeração](device-properties-images/win/04-enum-value.png)

- Para alterar uma propriedade de cadeia de caracteres ou inteiro, clique duas vezes na configuração da cadeia de caracteres ou inteiro atual na coluna valor e insira um novo valor.

    ![Alterar uma propriedade de inteiro](device-properties-images/win/03-integer-value.png)

::: zone-end
::: zone pivot="macos"

## <a name="android-device-manager-on-macos"></a>Android Device Manager no macOS

O **Android Device Manager** é compatível com a edição de propriedades de perfis individuais de dispositivos virtuais Android. As telas **Novo Dispositivo** e **Editar Dispositivo** listam as propriedades do dispositivo virtual na primeira coluna, com os valores correspondentes de cada propriedade na segunda coluna (como mostrado neste exemplo): 

[![Tela Novo Dispositivo de exemplo](device-properties-images/mac/01-new-device-editor-sml.png)](device-properties-images/mac/01-new-device-editor.png#lightbox)

Ao selecionar uma propriedade, uma descrição detalhada é exibida à direita. É possível modificar as *propriedades de perfil de hardware* e as *propriedades de AVD*. As propriedades de perfil de hardware (como `hw.ramSize` e `hw.accelerometer`) descrevem as características físicas do dispositivo emulado. Essas características são o tamanho da tela, a quantidade de RAM disponível e se existe um acelerômetro. As propriedades de AVD especificam a operação do AVD quando ele é executado. Por exemplo, as propriedades de AVD podem ser configuradas para especificar como o AVD usa a placa gráfica do computador de desenvolvimento para renderização.

É possível alterar as propriedades usando as seguintes diretrizes:

- Para alterar uma propriedade booliana, clique na marca de seleção à direita dela:

    ![Alterar uma propriedade booliana](device-properties-images/mac/02-boolean-value.png)

- Para alterar uma propriedade *enum* (enumerada), clique no menu suspenso à direita dela e escolha um novo valor.

    ![Alterar uma propriedade de enumeração](device-properties-images/mac/04-enum-value.png)

- Para alterar uma propriedade de cadeia de caracteres ou inteiro, clique duas vezes na configuração da cadeia de caracteres ou inteiro atual na coluna valor e insira um novo valor.

    ![Alterar uma propriedade de inteiro](device-properties-images/mac/03-integer-value.png)

::: zone-end

A tabela a seguir mostra uma explicação detalhada das propriedades listadas nas telas **Novo Dispositivo** e **Editor de Dispositivos**:

[!include[](~/android/includes/emulator-properties.md)]

Para saber mais sobre essas propriedades, consulte [Propriedades de Perfil de Hardware](https://developer.android.com/studio/run/managing-avds.html#hpproperties).
