---
title: Características do sanduíche de sorvete
description: Este artigo descreve vários dos novos recursos disponíveis para desenvolvedores de aplicativos com a API Android 4 - Ice Cream Sandwich. Ele cobre várias novas tecnologias de interface de usuário e, em seguida, examina uma variedade de novos recursos que o Android 4 oferece para compartilhar dados entre aplicativos e entre dispositivos.
ms.prod: xamarin
ms.assetid: 78E18A62-C12F-A699-37FA-44B9F6B44273
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/09/2018
ms.openlocfilehash: 4fbbe1bec317e66166d5218ef0ed54247aa4f6dd
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "78292380"
---
# <a name="ice-cream-sandwich-features"></a>Características do sanduíche de sorvete

_Este artigo descreve vários dos novos recursos disponíveis para desenvolvedores de aplicativos com a API Android 4 - Ice Cream Sandwich. Ele cobre várias novas tecnologias de interface de usuário e, em seguida, examina uma variedade de novos recursos que o Android 4 oferece para compartilhar dados entre aplicativos e entre dispositivos._

## <a name="overview"></a>Visão geral

A versão 4.0 do Sistema Operacional Android (Nível 14 da API) representa uma grande reformulação do sistema operacional Android e inclui uma série de mudanças e atualizações importantes, incluindo:

- **Interface de usuário atualizada** – Vários novos recursos de interface de usuário dão aos desenvolvedores mais poder e flexibilidade quando criam interfaces de usuário de aplicativos. Estes novos `GridLayout` recursos `PopupMenu` `Switch` incluem: , `TextureView` widget e .
- **Melhor aceleração de hardware** – a renderização 2D agora acontece na GPU para todos os controles Android. Além disso, a aceleração de hardware está em, por padrão, em todos os aplicativos desenvolvidos para android 4.0.
- **Novas APIs de dados** – Há um novo acesso a dados que não eram oficialmente acessíveis, como dados de calendário e o perfil de usuário do proprietário do dispositivo.
- **Compartilhamento de dados do aplicativo** – O compartilhamento de dados entre aplicativos `ShareActionProvider` e dispositivos agora é mais fácil do que nunca através de tecnologias como a , o que facilita a criação de uma ação de compartilhamento a partir de uma Barra de Ação, e *o Android Beam* for Near Field Communications *(NFC),* o que torna um snap para compartilhar dados entre dispositivos próximos um do outro.

Neste artigo, vamos explorar esses recursos e outras alterações que foram feitas na API do Android 4.0, e explicaremos como usar cada recurso com Xamarin.Android.

## <a name="user-interface-features"></a>Recursos da interface do usuário

Uma variedade de novas tecnologias de interface de usuário estão disponíveis com o Android 4, incluindo:

- **[GridLayout](~/android/user-interface/layouts/grid-layout.md)** – Suporta layout de grade 2D de controles.
- **[Widget switch](~/android/user-interface/controls/switch.md)** – Permite alternar entre ON ou OFF.
- **[TextureView](~/android/user-interface/controls/texture-view.md)** – Permite conteúdo de vídeo e OpenGL dentro de uma exibição.
- **[Barra de navegação](~/android/user-interface/controls/navigation-bar.md)** – Contém botões virtuais para trás, casa e multitarefa.

Além disso, outros elementos de interface do `<a href"/guides/android/user_interface/popup_menus">PopupMenu</a>`iu foram aprimorados, como o , que agora é mais fácil de trabalhar, e abas, que têm uma aparência mais polida.

## <a name="sharing-features"></a>Compartilhando recursos

O Android 4 inclui várias novas tecnologias que nos permitem compartilhar dados entre dispositivos e aplicativos. Ele também fornece acesso a vários tipos de dados que não estavam disponíveis anteriormente, como informações de calendário e o perfil de usuário do proprietário do dispositivo. Nesta seção, examinaremos uma variedade de recursos oferecidos pelo Android 4 que abordam essas áreas, incluindo:

- **[Android Beam](~/android/platform/android-beam.md)** – Permite o compartilhamento de dados via NFC.
- **[ShareActionProvider](~/android/user-interface/controls/action-bar.md)** – Cria um provedor que permite que os desenvolvedores especifiquem ações de compartilhamento da Barra de Ação.
- **[Perfil do Usuário](~/android/user-interface/user-profile.md)** – Fornece acesso aos dados do perfil do proprietário do dispositivo.
- **[API do calendário](~/android/user-interface/controls/calendar.md)** – Fornece acesso aos dados do calendário do provedor de calendário.

## <a name="x86-emulators"></a>emuladores x86

O ICS ainda não suporta o desenvolvimento com um emulador x86. os emuladores x86 só são suportados com Android 2.3.3, Nível 10 da API. Consulte [Configuração do emulador x86](~/android/get-started/installation/android-emulator/index.md) para obter mais informações.

## <a name="summary"></a>Resumo

Este artigo cobriu uma variedade de novas tecnologias que agora estão disponíveis com o Android 4. Analisamos novos recursos de interface de usuário, como o *widget GridLayout,* *PopupMenu*e *Switch.* Também analisamos alguns dos novos suportes para controlar a iu do sistema, bem como como trabalhar com o *TextureView*. Em seguida, discutimos uma variedade de novas tecnologias de compartilhamento. Nós cobrimos como o *Android Beam* permite que você compartilhe informações entre dispositivos que usam *NFC,* discutimos a nova *API do Calendário*e também mostramos como usar o *ShareActionProvider*incorporado.
Finalmente, examinamos como usar o provedor *ContactsContract* para acessar dados do perfil do usuário.

## <a name="related-links"></a>Links relacionados

- [TextureViewDemo (amostra)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/textureviewdemo)
- [Demonstração de calendário (amostra)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/calendardemo)
- [Tutorial de layout de guias](~/android/user-interface/layouts/tab-layout/index.md)
- [Sanduíche de sorvete](https://developer.android.com/about/versions/android-4.0-highlights.html)
- [Plataforma Android 4.0](https://developer.android.com/about/versions/android-4.0.html)
