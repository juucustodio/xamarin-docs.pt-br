---
title: Onde está meu Mac?
description: Instruções para configurar seu Mac para o Visual Studio compilar projetos do Xamarin.iOS
ms.prod: xamarin
ms.assetid: 4f792690-b3b1-4d56-a5e2-363b4f246059
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: f3e2988c9700549db24ad69277df9e412c99caed
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="wheres-my-mac"></a>Onde está meu Mac?

_Instruções para configurar seu Mac para o Visual Studio compilar projetos do Xamarin.iOS_

A versão atual **estável** do Xamarin para Visual Studio interage com seu Mac diferentemente das versões anteriores[^](#earlier-versions).

Para usar o Mac como um Xamarin Mac Agent, habilite o **Logon Remoto** no Mac.

1. Abra *Destaque* (`Cmd-Space`) e pesquise **Logon Remoto** e selecione o resultado **Compartilhamento**. Isso abrirá as **Preferências do Sistema** no painel **Compartilhamento**.

  ![](visual-studio-ssh-images/spotlight.png "Pesquisa do Spotlight para acesso remoto")

2. Marque a opção **Logon Remoto** na lista **Serviço** à esquerda para permitir que o Xamarin para Visual Studio se conecte ao Mac.

  ![](visual-studio-ssh-images/sharing.png "Marque a opção Acesso Remoto na Lista de serviços")

3. Verifique se o **Logon Remoto** está definido para permitir o acesso de **Todos os usuários** ou se o nome de usuário ou grupo do Mac está incluído na lista de usuários permitidos à direita.

O Mac agora deve ser detectável pelo Visual Studio se estiver na mesma rede.
Quando o Visual Studio tenta se conectar ao Mac, você é solicitado a efetuar logon no Visual Studio usando suas credenciais de usuário do Mac.

## <a name="where-can-i-find-more-information"></a>Onde encontrar mais informações?

Há uma série de guias disponíveis para ajudá-lo a configurar e entender os novos agentes, listados abaixo:

- [Conectando ao Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md)
- [Solução de problemas](~/ios/get-started/installation/windows/connecting-to-mac/troubleshooting.md)
- [Universidade de Xamarin – Xamarin Mac Agent](https://university.xamarin.com/lightninglectures/xamarin-mac-agent)

<a name="earlier-versions" />

## <a name="migrating-from-previous-versions"></a>Migrando de versões anteriores

Se você usou versões anteriores do Xamarin para Visual Studio, deverá estar familiarizado com o aplicativo **Host de build do Xamarin.iOS** que precisava ser iniciado em seu Mac e, em seguida, *emparelhado* por meio de um número PIN, com a instância do Visual Studio.

O aplicativo host de build não é mais necessário com esta versão do Xamarin para Visual Studio.
