---
title: Sorvete Sandwich recursos
description: "Este artigo descreve vários dos novos recursos disponíveis para desenvolvedores de aplicativos com a API do Android 4 - Sandwich sorvete. Ele abrange várias novas tecnologias de interface de usuário e, em seguida, examina uma variedade de novos recursos que o Android 4 oferece para compartilhar dados entre aplicativos e dispositivos."
ms.topic: article
ms.prod: xamarin
ms.assetid: 78E18A62-C12F-A699-37FA-44B9F6B44273
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.openlocfilehash: 9726ec83cd38dd2f237152f0f8e7373f509a3e01
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="ice-cream-sandwich-features"></a>Sorvete Sandwich recursos

_Este artigo descreve vários dos novos recursos disponíveis para desenvolvedores de aplicativos com a API do Android 4 - Sandwich sorvete. Ele abrange várias novas tecnologias de interface de usuário e, em seguida, examina uma variedade de novos recursos que o Android 4 oferece para compartilhar dados entre aplicativos e dispositivos._

## <a name="overview"></a>Visão geral

Sistema operacional Android versão 4.0 (API nível 14) representa um retrabalhando principais do sistema de operacional Android e inclui uma série de importantes alterações e atualizações, incluindo:

-   **A Interface do usuário atualizada** – vários novos recursos de interface de usuário permitem que os desenvolvedores mais potência e flexibilidade ao criar usuário do aplicativo interfaces. Esses novos recursos incluem: `GridLayout` , `PopupMenu` , `Switch` widget, e `TextureView` . 
-   **Melhor aceleração de Hardware** – 2D ocorra na GPU para Android todos os controles de renderização agora. Além disso, a aceleração de hardware está ativada, por padrão, em todos os aplicativos desenvolvidos para o Android 4.0. 
-   **Novas APIs de dados** – há novo acesso a dados que não estavam anteriormente oficialmente acessíveis, como dados de calendário e o perfil de usuário do proprietário do dispositivo. 
-   **Compartilhamento de dados do aplicativo** – compartilhar dados entre aplicativos e dispositivos agora é mais fácil do que nunca por meio de tecnologias como o `ShareActionProvider` , que simplifica a criação de uma ação de compartilhamento de uma barra de ação, e *emitir Android* para *perto de comunicações de distância (NFC)* , que torna fácil compartilhar dados entre dispositivos nas proximidades uns aos outros. 


Neste artigo, vamos explorar esses recursos e outras alterações que foram feitas para a API do Android 4.0 e vamos explicar como usar cada recurso com xamarin.

## <a name="user-interface-features"></a>Recursos de Interface do usuário

Uma variedade de novas tecnologias de interface do usuário estão disponíveis com o Android 4, incluindo:

-   **[GridLayout](~/android/user-interface/layouts/grid-layout.md)**  – layout de grade 2D dá suporte a controles. 
-   **[Alternar o widget](~/android/user-interface/controls/switch.md)**  – permite a alternância entre ON ou OFF. 
-   **[TextureView](~/android/user-interface/controls/texture-view.md)**  – permite que o vídeo e conteúdo de OpenGL dentro de uma exibição. 
-   **[Barra de navegação](~/android/user-interface/controls/navigation-bar.md)**  – contém botões virtual de volta, inicial e multitarefa. 


Além disso, outros elementos de interface do usuário foram aprimorados, como o `<a href"/guides/android/user_interface/popup_menus">PopupMenu</a>`, que agora é mais fácil trabalhar com e guias, que tem uma aparência mais refinada.

## <a name="sharing-features"></a>Compartilhamento de recursos

Android 4 inclui várias novas tecnologias que vamos compartilhar dados entre dispositivos e aplicativos. Ele também fornece acesso a vários tipos de dados que não estavam disponíveis anteriormente, como informações de calendário e perfil de usuário do proprietário do dispositivo. Nesta seção, que vamos examinar uma variedade de recursos oferecidos pelo Android 4 endereço essas áreas como:

-  **[Emitir Android](~/android/platform/android-beam.md)**  – permite que dados de compartilhamento via NFC.
-   **[ShareActionProvider](~/android/user-interface/controls/action-bar.md)**  – cria um provedor que permite aos desenvolvedores especificar ações de compartilhamento na barra de ação. 
-   **[Perfil de usuário](~/android/user-interface/user-profile.md)**  – fornece acesso aos dados de perfil do proprietário do dispositivo. 
-   **[API de calendário](~/android/user-interface/controls/calendar.md)**  – fornece acesso aos dados de calendário do provedor de calendário. 

## <a name="x86-emulators"></a>x86 emuladores

ICS ainda não dá suporte a desenvolvimento com x86 emulador. x86 emuladores só são suportados com 2.3.3 Android, o nível de API 10. Consulte [Configurando x86 emulador](~/android/get-started/installation/android-emulator/index.md) para obter mais informações.

## <a name="summary"></a>Resumo

Este artigo abordados uma variedade de novas tecnologias que agora estão disponíveis com o Android 4. Analisamos novos recursos de interface do usuário, como o *GridLayout*, *PopupMenu*, e *Switch* widget. Também vimos alguns o novo suporte para controlar o interface do usuário, sistema, bem como trabalhar com o *TextureView*. Em seguida, discutimos uma variedade de novas tecnologias de compartilhamento. É coberto como *emitir Android* vamos compartilhar informações entre os dispositivos que usam *NFC*, discutido a nova *calendário API*e também mostrou como usar interna em  *ShareActionProvider*.
Por fim, examinamos como usar o *ContactsContract* provedor para acessar dados de perfil de usuário.



## <a name="related-links"></a>Links relacionados

- [Exemplos de sorvete Sandwich](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/ICS_Samples/)
- [TextureViewDemo (exemplo)](https://developer.xamarin.com/samples/monodroid/TextureViewDemo/)
- [CalendarDemo (exemplo)](https://developer.xamarin.com/samples/monodroid/CalendarDemo/)
- [Guia Layout Tutorial](~/android/user-interface/layouts/tab-layout/index.md)
- [Sorvete Sandwich](http://developer.android.com/about/versions/android-4.0-highlights.html)
- [Plataforma 4.0 Android](http://developer.android.com/about/versions/android-4.0.html)
