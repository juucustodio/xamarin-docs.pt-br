---
title: Criando Interfaces do usuário com o Designer do iOS
description: Este documento descreve como usar o Designer do Xamarin para iOS para criar a interface de usuário do aplicativo com arquivos. XIB e storyboards. Ele vincula a documentos que abordam a disponibilidade da ferramenta, sua funcionalidade básica, controles projetáveis e fornecem instruções passo a passo de seu uso.
ms.prod: xamarin
ms.assetid: E35EFB69-EBBA-40E3-ADBE-CB8016F17127
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/31/2018
ms.openlocfilehash: 7c6529c539ab502fb6c13226acd18a57f8f6d58e
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61378657"
---
# <a name="building-user-interfaces-with-the-ios-designer"></a>Criando Interfaces do usuário com o Designer do iOS

_O Designer do Xamarin para iOS é um designer visual para o iOS formatos de Storyboard e Interface Builder é totalmente integrado com o Visual Studio para Mac e Visual Studio. O Designer do iOS mantém total compatibilidade com os formatos de Storyboard e. XIB, para que os arquivos podem ser editados no Visual Studio para Mac ou Visual Studio, além do Interface Builder do Xcode. Além disso, o Designer de Xamarin para iOS oferece suporte a recursos avançados, como controles personalizados que processam em tempo de design no editor._

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![o iOS Designer no Visual Studio para Mac](images/designer-vsmac-sml.png "o Designer do iOS")](images/designer-vsmac.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![o iOS Designer no Visual Studio](images/designer-vs.png "o Designer do iOS")](images/designer-vs.png#lightbox)

-----

## <a name="availability"></a>Disponibilidade

O Designer de Xamarin para iOS está disponível no Visual Studio para Mac e no Visual Studio 2017 no Windows.

Esses guias pressupõe uma familiaridade com o conteúdo abordados os [xamarin. IOS Introdução orienta](~/ios/get-started/index.md).

## <a name="ios-designer-basicsintroductionmd"></a>[Noções básicas do Designer do iOS](introduction.md)

Este guia aborda os recursos do designer de iOS do Xamarin. Ele abrange conceitos básicos de designer, mostrando como usar o designer para dispor controles visualmente e como editar as propriedades.

## <a name="designable-controls-overviewios-designable-controls-overviewmd"></a>[Visão geral dos controles projetáveis](ios-designable-controls-overview.md)

Este guia analisa em detalhes em controles personalizados, como eles são criados e quais requisitos que eles devem atender para ser renderizado na superfície de design. Além disso, ele mostra como depurar problemas comuns que podem ocorrer ao usar controles projetáveis.

## <a name="walkthrough---using-custom-controls-with-ios-designerios-designable-controls-walkthroughmd"></a>[Passo a passo: usando controles personalizados com o Designer do iOS](ios-designable-controls-walkthrough.md)

Este artigo fornece instruções passo a passo que mostra como criar um controle personalizado e usá-lo no designer do iOS. Ele mostra como fazer um controle disponíveis na caixa de ferramentas do designer para que ele pode ser arrastar/solto em um modo de exibição. Além disso, ele mostra como implementar um controle para que ele renderiza adequadamente em tempo de design e tempo de execução, bem como criar propriedades que podem ser definidas em tempo de design.

## <a name="auto-layout-with-the-xamarin-ios-designerdesigner-auto-layoutmd"></a>[Layout automático com o Designer do iOS Xamarin](designer-auto-layout.md)

Este guia apresenta novas restrições fluxo de trabalho disponível no designer de iOS e iOS Layout automático.
