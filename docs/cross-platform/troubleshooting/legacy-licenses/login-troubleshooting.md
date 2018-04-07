---
title: Por que não é possível efetuar login Xamarin no Visual Studio ou o Visual Studio para Mac?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 6EF2B553-5DF9-41CC-B68F-77A7F64572FA
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.openlocfilehash: cb750a7c282ecab6e2193bb554e470086868e018
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="why-cant-i-log-into-xamarin-in-visual-studio-or-visual-studio-for-mac"></a>Por que não é possível efetuar login Xamarin no Visual Studio ou o Visual Studio para Mac?

> [!IMPORTANT]
> Este guia não se aplicam à maioria dos usuários do MSDN porque eles não são necessários para o proprietário ou de log em contas do Xamarin usando o [Xamarin componentes armazenar](https://components.xamarin.com/) ou [Visual Studio para Mac (Mac)](~/cross-platform/get-started/requirements.md). Detentores de licença do MSDN devem se referir a esse [guia Opções de licenciamento](~/cross-platform/get-started/requirements.md) em vez disso.



## <a name="overview"></a>Visão geral
Há algumas causas comuns que podem impedir o login em sua conta do Xamarin no IDE. Problemas conhecidos e correções são descritas abaixo.

### <a name="finding-the-login-screen"></a>Localizando a tela de Login

Para referência, as telas de logon são encontradas aqui:

- Visual Studio para Mac
   - Canto superior direito da tela de boas-vinda
   - **O Visual Studio para Mac > conta** (Mac)
   - **Ferramentas > conta** (Windows)
- Visual Studio
   - **Ferramentas > conta Xamarin**

## <a name="the-ide-is-connecting-but-the-account-screen-isnt-showing-correct-login-information"></a>O IDE está se conectando, mas a tela de conta não está mostrando informações de logon correto

Normalmente, esse problema é resolvido por uma ressincronização de licença manual.
Siga as instruções neste artigo: [como fazer manualmente resyncronize Xamarin licenças?](~/cross-platform/troubleshooting/legacy-licenses/resync-licenses.md)

## <a name="invalid-account-information"></a>Informações de conta inválida

Se você for para o site do Xamarin [página de logon](https://store.xamarin.com/Login?from=%2faccount%2f), você pode tentar o registro em log com suas credenciais da conta atual.
A página também contém links para redefinir sua senha e criar uma nova conta.

## <a name="account-is-valid-but-the-ide-cant-connect"></a>Conta é válida, mas o IDE não pode se conectar

Isso geralmente é causado quando o firewall ou outras configurações de segurança bloqueiam o IDE de acessar os pontos de extremidade necessários.
Os servidores de ativação devem acessar o seguinte:

> activation.xamarin.com store.xamarin.com auth.xamarin.com

No entanto, vários outros pontos de extremidade são importantes para os processos de desenvolvimento geral, como atualizações, obtendo pacotes NuGet, etc. Por isso, é recomendado para certificar-se *todos os* dos pontos de extremidade são adicionadas a [Xamarin guia de configuração de firewall](~/cross-platform/get-started/installation/firewall.md).

### <a name="ios-in-xamarin-studio-windows"></a>iOS no Xamarin Studio Windows
Não há suporte para o desenvolvimento do iOS no Xamarin Studio para Windows. Ao acessar a tela de login, a licença do iOS não será exibida.

Em vez disso, logon por meio do Xamarin Studio (Mac) ou o Visual Studio com uma licença herdada. Observe que os usuários do MSDN no Windows não precisar fazer logon no.

## <a name="additional-support"></a>Suporte adicional

Se os cenários acima não descrever a sua situação ou resolver o problema, consulte estes [opções de suporte](https://www.xamarin.com/support).
