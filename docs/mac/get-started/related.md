---
title: Xamarin.Mac – documentação relacionada
description: 'Este documento fornece links para a documentação relevante para desenvolvedores Xamarin.Mac: documentação do Xamarin.iOS, Mac Dev Center da Apple e vários guias que descrevem como criar interfaces do usuário com Xamarin.Mac.'
ms.prod: xamarin
ms.assetid: 0a282c58-1c37-4f73-8440-85de2daf454a
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 12/02/2016
ms.openlocfilehash: ae1ac9b40a0e6a62076a6143e64fa8beb82df6c5
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50107307"
---
# <a name="xamarinmac-related-documentation"></a>Xamarin.Mac – documentação relacionada

Além de seção Mac [developer.xamarin.com](~/mac/get-started/index.md) há três fontes excelentes de documentação que podem ser de ajuda com perguntas sobre o Xamarin.Mac:

- [**Documentação do Xamarin.IOS**](~/ios/get-started/index.md) – Para muitas APIs (principalmente fora do AppKit/UIKit), há apenas pequenas diferenças entre as versões de iOS e macOS. Em alguns casos em que determinada API de iOS tem um nome de `UIFoo`, uma API semelhante chamada `NSFoo` pode ser encontrada no macOS. Esses exemplos geralmente estarão em C#.

- **Centro de desenvolvimento de Mac [da Apple](https://developer.apple.com/devcenter/mac/)** – Muitas vezes um exemplo de quais APIs chamar em Objective-C pode ser convertido em C# de uma maneira direta. Consulte [Noções básicas de APIs do Mac](~/mac/app-fundamentals/mac-apis.md) para obter detalhes de como fazer isso.

- [**Estouro de pilha**](http://stackoverflow.com/) – Um excelente recurso para perguntas simples como ["Como expandir automaticamente todos os nós em uma NSOutlineView"](http://stackoverflow.com/questions/519751/nsoutlineview-auto-expand-all-nodes). Esses exemplos geralmente estarão em Objective-C e precisam ser convertidos em C#, mas há um subconjunto de respostas em C#.

## <a name="user-interface"></a>Interface do Usuário

Ao trabalhar com C# e .NET em um aplicativo Xamarin.Mac, o desenvolvedor tem acesso aos mesmos controles de interface do usuário que um desenvolvedor que trabalha em *Objective-C* e *Xcode*. Como o Xamarin.Mac se integra diretamente com Xcode, o desenvolvedor pode usar o _Interface Builder_ do Xcode para criar e manter interfaces de usuário de um aplicativo (ou, opcionalmente, criá-los diretamente em código C#).

Os guias listados abaixo oferecem informações detalhadas sobre como trabalhar com elementos do macOS em um aplicativo Xamarin.Mac:

- [Windows](~/mac/user-interface/window.md)
- [Caixas de diálogo](~/mac/user-interface/dialog.md)
- [Alertas](~/mac/user-interface/alert.md)
- [Menus](~/mac/user-interface/menu.md)
- [Barras de ferramentas](~/mac/user-interface/toolbar.md)
- [Modos de exibição de tabela](~/mac/user-interface/table-view.md)
- [Modos de exibição de estrutura de tópicos](~/mac/user-interface/outline-view.md)
- [Listas de origem](~/mac/user-interface/source-list.md)
