---
title: "Referência de Info.plist"
ms.topic: article
ms.prod: xamarin
ms.assetid: 944DFDB5-ADBA-4D6E-984C-5AEC19A1CC57
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 01/18/2017
ms.openlocfilehash: 7732419af22ab8bd37cbfbf828dc59e48841217f
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="infoplist-reference"></a>Referência de Info.plist

Para obter mais informações sobre como trabalhar com chaves Info.Plist, veja o guia [Trabalhando com segurança e privacidade](~/ios/app-fundamentals/security-privacy.md). 

## <a name="location"></a>Local 

O acesso ao local do usuário também requer modificações em Info.plist. As seguintes chaves relacionadas a dados locais devem ser definidas: 

* **NSLocationWhenInUseUsageDescription** – para acessar o local do usuário enquanto eles estiverem interagindo com seu aplicativo. 
* **NSLocationAlwaysUsageDescription** – para quando o aplicativo acessa o local do usuário em segundo plano.

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
