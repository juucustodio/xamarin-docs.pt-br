---
title: Referência de Info.plist para Xamarin.iOS
description: Este documento descreve vários pares de chave/valor que podem ser definidos no arquivo Info.plist de um aplicativo Xamarin.iOS. Essas chaves são necessárias se o aplicativo executa tarefas específicas, como acessar a localização, as fotos, o microfone ou a câmera.
ms.prod: xamarin
ms.assetid: 944DFDB5-ADBA-4D6E-984C-5AEC19A1CC57
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 01/18/2017
ms.openlocfilehash: 2a61a4a313fa23b5cdc629279bee39f1eed792cc
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70120496"
---
# <a name="infoplist-reference-for-xamarinios"></a>Referência de Info.plist para Xamarin.iOS

Para obter mais informações sobre como trabalhar com chaves Info.Plist, veja o guia [Trabalhando com segurança e privacidade](~/ios/app-fundamentals/security-privacy.md). 

## <a name="location"></a>Local 

O acesso ao local do usuário também requer modificações em Info.plist. As seguintes chaves relacionadas a dados locais devem ser definidas: 

- **NSLocationWhenInUseUsageDescription** – para acessar o local do usuário enquanto eles estiverem interagindo com seu aplicativo. 
- **NSLocationAlwaysUsageDescription** – para quando o aplicativo acessa o local do usuário em segundo plano.

## <a name="photos"></a>Fotos 

NSPhotoLibraryUsageDescription  

## <a name="contacts"></a>Contatos 

NSContactsUsageDescription 

## <a name="calendar-data"></a>Dados de calendário 
    
NSCalendarsUsageDescription 

## <a name="reminder-data"></a>Data de lembrete 
    
NSRemindersUsageDescription 

## <a name="bluetooth-peripherals"></a>Periféricos de Bluetooth 
    
NSBluetoothPeripheralUsageDescription 

## <a name="microphone"></a>Microphone 

NSMicrophoneUsageDescription 

## <a name="camera"></a>Câmera 
    
NSCameraUsageDescription 

## <a name="reading-healthkit"></a>Lendo HealthKit  

NSHealthShareUsageDescription 

NSHealthUpdateUsageDescription 

## <a name="background-modes"></a>Modos de segundo plano 
    
UIBackgroundModes 

## <a name="homekit"></a>HomeKit 

NSHomeKitUsageDescription 


## <a name="related-links"></a>Links relacionados

- [Guia de Referência da Apple.](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html#//apple_ref/doc/uid/TP40009252-SW10)
