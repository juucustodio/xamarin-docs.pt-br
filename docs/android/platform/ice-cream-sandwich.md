---
title: Recursos do sanduíche de sorvete
description: Este artigo descreve vários dos novos recursos disponíveis para desenvolvedores de aplicativos com a API do Android 4-Ice de sorvete. Ele abrange várias novas tecnologias de interface do usuário e, em seguida, examina uma variedade de novos recursos que o Android 4 oferece para o compartilhamento de dados entre aplicativos e entre dispositivos.
ms.prod: xamarin
ms.assetid: 78E18A62-C12F-A699-37FA-44B9F6B44273
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/09/2018
ms.openlocfilehash: 4fbbe1bec317e66166d5218ef0ed54247aa4f6dd
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2020
ms.locfileid: "78292380"
---
# <a name="ice-cream-sandwich-features"></a>Recursos do sanduíche de sorvete

_Este artigo descreve vários dos novos recursos disponíveis para desenvolvedores de aplicativos com a API do Android 4-Ice de sorvete. Ele abrange várias novas tecnologias de interface do usuário e, em seguida, examina uma variedade de novos recursos que o Android 4 oferece para o compartilhamento de dados entre aplicativos e entre dispositivos._

## <a name="overview"></a>Visão geral

O Android OS versão 4,0 (API nível 14) representa um grande retrabalho do sistema operacional Android e inclui várias alterações e atualizações importantes, incluindo:

- **Interface do usuário atualizada** – vários novos recursos de IU oferecem aos desenvolvedores mais capacidade e flexibilidade quando criam interfaces de usuário do aplicativo. Esses novos recursos incluem: `GridLayout`, `PopupMenu`, `Switch` widget e `TextureView`.
- **Melhor aceleração de hardware** – a renderização 2D agora ocorre na GPU para todos os controles do Android. Além disso, a aceleração de hardware está ativada, por padrão, em todos os aplicativos desenvolvidos para Android 4,0.
- **Novas APIs de dados** – há um novo acesso a dados que não foram oficialmente disponibilizados anteriormente, como dados de calendário e o perfil de usuário do proprietário do dispositivo.
- O **compartilhamento de dados do aplicativo** – o compartilhamento de dados entre aplicativos e dispositivos agora é mais fácil do que nunca por meio de tecnologias como a `ShareActionProvider`, o que facilita a criação de uma ação de compartilhamento de uma barra de ação e a transmissão do *Android* para a *NFC (comunicação a curta distância)* , o que o torna um ajuste para compartilhar dados entre dispositivos de perto uns dos outros.

Neste artigo, vamos explorar esses recursos e outras alterações que foram feitas na API do Android 4,0, e explicaremos como usar cada recurso com o Xamarin. Android.

## <a name="user-interface-features"></a>Recursos da interface do usuário

Uma variedade de novas tecnologias de interface do usuário estão disponíveis com o Android 4, incluindo:

- **[GridLayout](~/android/user-interface/layouts/grid-layout.md)** – dá suporte ao layout de grade 2D de controles.
- **[Alternar widget](~/android/user-interface/controls/switch.md)** – permite alternar entre ativar ou desativar.
- **[TextureView](~/android/user-interface/controls/texture-view.md)** – habilita o conteúdo de vídeo e OpenGL em uma exibição.
- **[Barra de navegação](~/android/user-interface/controls/navigation-bar.md)** – contém botões virtuais para voltar, iniciar e realizar várias tarefas.

Além disso, outros elementos da interface do usuário foram aprimorados, como o `<a href"/guides/android/user_interface/popup_menus">PopupMenu</a>`, que agora é mais fácil de trabalhar e guias, que têm uma aparência mais elegante.

## <a name="sharing-features"></a>Recursos de compartilhamento

O Android 4 inclui várias novas tecnologias que nos permitem compartilhar dados entre dispositivos e entre aplicativos. Ele também fornece acesso a vários tipos de dados que não estavam disponíveis anteriormente, como informações de calendário e o perfil de usuário do proprietário do dispositivo. Nesta seção, examinaremos uma variedade de recursos oferecidos pelo Android 4 que abordam essas áreas, incluindo:

- **[Transmissão do Android](~/android/platform/android-beam.md)** – permite o compartilhamento de dados via NFC.
- **[ShareActionProvider](~/android/user-interface/controls/action-bar.md)** – cria um provedor que permite aos desenvolvedores especificar ações de compartilhamento na barra de ação.
- **[Perfil do usuário](~/android/user-interface/user-profile.md)** – fornece acesso a dados de perfil do proprietário do dispositivo.
- **[API de calendário](~/android/user-interface/controls/calendar.md)** – fornece acesso a dados de calendário do provedor de calendário.

## <a name="x86-emulators"></a>Emuladores x86

O ICS ainda não dá suporte ao desenvolvimento com um emulador x86. os emuladores x86 só têm suporte com o Android 2.3.3, nível de API 10. Consulte [Configurando o emulador x86](~/android/get-started/installation/android-emulator/index.md) para obter mais informações.

## <a name="summary"></a>Resumo

Este artigo abordou uma variedade das novas tecnologias que agora estão disponíveis com o Android 4. Examinamos novos recursos de interface do usuário, como o widget *GridLayout*, *PopupMenu*e *switch* . Também vimos alguns dos novos suporte para controlar a interface do usuário do sistema, bem como trabalhar com o *TextureView*. Em seguida, discutimos uma variedade de novas tecnologias de compartilhamento. Abordamos como o *feixe do Android* permite que você compartilhe informações em dispositivos que usam a *NFC*, DISCUTIu a nova *API de calendário*e também mostrou como usar o *ShareActionProvider*interno.
Por fim, examinamos como usar o provedor *ContactsContract* para acessar os dados de perfil do usuário.

## <a name="related-links"></a>Links relacionados

- [TextureViewDemo (exemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/textureviewdemo)
- [CalendarDemo (exemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/calendardemo)
- [Tutorial de layout de guia](~/android/user-interface/layouts/tab-layout/index.md)
- [Sanduíche de sorvete](https://developer.android.com/about/versions/android-4.0-highlights.html)
- [Plataforma Android 4,0](https://developer.android.com/about/versions/android-4.0.html)
