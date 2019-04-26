---
title: Recursos do ICE Cream Sandwich
description: Este artigo descreve vários dos novos recursos disponíveis aos desenvolvedores de aplicativos com a API do Android 4 - Ice Cream Sandwich. Ele cobre várias novas tecnologias de interface de usuário e, em seguida, examina uma variedade de novos recursos que o Android 4 oferece para compartilhar dados entre aplicativos e dispositivos.
ms.prod: xamarin
ms.assetid: 78E18A62-C12F-A699-37FA-44B9F6B44273
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: 00b553ae8de0dfcd86d57d1d5e3e2a892d6b5463
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61091140"
---
# <a name="ice-cream-sandwich-features"></a>Recursos do ICE Cream Sandwich

_Este artigo descreve vários dos novos recursos disponíveis aos desenvolvedores de aplicativos com a API do Android 4 - Ice Cream Sandwich. Ele cobre várias novas tecnologias de interface de usuário e, em seguida, examina uma variedade de novos recursos que o Android 4 oferece para compartilhar dados entre aplicativos e dispositivos._

## <a name="overview"></a>Visão geral

Sistema operacional Android versão 4.0 (API nível 14) representa um retrabalho principais do sistema de operacional Android e inclui uma série de alterações importantes e atualizações, incluindo:

-   **A Interface de usuário atualizada** – vários novos recursos de interface do usuário permitem que os desenvolvedores mais energia e interfaces de flexibilidade na criação de usuário do aplicativo. Esses novos recursos incluem: `GridLayout` , `PopupMenu` , `Switch` widget, e `TextureView` . 
-   **Uma melhor aceleração de Hardware** – 2D ocorre na GPU para Android todos os controles de renderização agora. Além disso, a aceleração de hardware está ativada, por padrão, em todos os aplicativos desenvolvidos para Android 4.0. 
-   **Novas APIs de dados** – há novo acesso a dados que não estavam anteriormente oficialmente acessíveis, como dados de calendário e o perfil do usuário do proprietário do dispositivo. 
-   **Compartilhamento de dados de aplicativo** – compartilhar dados entre aplicativos e dispositivos agora é mais fácil do que nunca por meio de tecnologias, como o `ShareActionProvider` , que torna fácil criar uma ação de compartilhamento de uma barra de ação, e *Beam do Android* para *perto de comunicações do campo (NFC)* , que torna muito fácil compartilhar dados entre dispositivos nas proximidades uns aos outros. 


Neste artigo, vamos explorar esses recursos e outras alterações que foram feitas para a API do Android 4.0 e explicaremos como usar cada recurso com o xamarin. Android.

## <a name="user-interface-features"></a>Recursos de Interface do usuário

Uma variedade de novas tecnologias de interface do usuário estão disponíveis com o Android 4, incluindo:

-   **[GridLayout](~/android/user-interface/layouts/grid-layout.md)**  – layout de grade 2D dá suporte a controles. 
-   **[Alternar o widget](~/android/user-interface/controls/switch.md)**  – permite a alternância entre ON ou OFF. 
-   **[TextureView](~/android/user-interface/controls/texture-view.md)**  – permite que o vídeo e conteúdo do OpenGL dentro de um modo de exibição. 
-   **[Barra de navegação](~/android/user-interface/controls/navigation-bar.md)**  – contém virtual botões Voltar, inicial e multitarefa. 


Além disso, outros elementos de interface do usuário foram aprimorados, como o `<a href"/guides/android/user_interface/popup_menus">PopupMenu</a>`, que agora é mais fácil trabalhar com e guias, que têm uma aparência mais refinada.

## <a name="sharing-features"></a>Recursos de compartilhamento

Android 4 inclui várias novas tecnologias que nos permitem compartilhar dados entre dispositivos e aplicativos. Ele também fornece acesso a vários tipos de dados que não estavam disponíveis anteriormente, como informações de calendário e perfil de usuário do proprietário do dispositivo. Nesta seção, que vamos examinar uma variedade de recursos oferecidos pelo Android 4 que abordam essas áreas, incluindo:

-  **[Beam do Android](~/android/platform/android-beam.md)**  – dados permite compartilhamento por meio de NFC.
-   **[ShareActionProvider](~/android/user-interface/controls/action-bar.md)**  – cria um provedor que permite que os desenvolvedores especifiquem as ações de compartilhamento na barra de ação. 
-   **[Perfil de usuário](~/android/user-interface/user-profile.md)**  – fornece acesso aos dados de perfil do proprietário do dispositivo. 
-   **[API de calendário](~/android/user-interface/controls/calendar.md)**  – fornece acesso aos dados de calendário do provedor de calendário. 

## <a name="x86-emulators"></a>x86 emuladores

ICS ainda não dá suporte a desenvolvimento com x86 emulator. x86 emuladores só são compatíveis com Android 2.3.3, 10 de nível de API. Ver [Configurando x86 Emulator](~/android/get-started/installation/android-emulator/index.md) para obter mais informações.

## <a name="summary"></a>Resumo

Este artigo abordou a uma variedade de novas tecnologias que agora estão disponíveis com o Android 4. Revisamos novos recursos de interface do usuário, como o *GridLayout*, *PopupMenu*, e *Switch* widget. Também examinamos algumas do novo suporte para controlar o interface do usuário, sistema, bem como trabalhar com o *TextureView*. Em seguida, discutimos uma variedade de novas tecnologias de compartilhamento. Estamos cobertos como *Beam do Android* vamos compartilhar informações entre os dispositivos que usam *NFC*, discutido a nova *API Calendar*e também mostrou como usar o internas  *ShareActionProvider*.
Por fim, examinamos como usar o *ContactsContract* provedor para acessar dados de perfil do usuário.



## <a name="related-links"></a>Links relacionados

- [Exemplos do ICE Cream Sandwich](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/ICS_Samples/)
- [TextureViewDemo (sample)](https://developer.xamarin.com/samples/monodroid/TextureViewDemo/)
- [CalendarDemo (amostra)](https://developer.xamarin.com/samples/monodroid/CalendarDemo/)
- [Tutorial do guia Layout](~/android/user-interface/layouts/tab-layout/index.md)
- [ICE Cream Sandwich](https://developer.android.com/about/versions/android-4.0-highlights.html)
- [Plataforma 4.0 Android](https://developer.android.com/about/versions/android-4.0.html)
