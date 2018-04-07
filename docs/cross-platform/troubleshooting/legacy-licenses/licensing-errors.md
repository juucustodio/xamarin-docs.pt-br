---
title: Alguns erros de licenciamento específicos
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: D26BDF2D-923B-4BC1-841A-74583155DB71
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.openlocfilehash: 8592fe5381c974e999477d0ef6ca6ebdd8b38cc4
ms.sourcegitcommit: 6f7033a598407b3e77914a85a3f650544a4b6339
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/07/2018
---
# <a name="some-specific-licensing-errors"></a>Alguns erros de licenciamento específicos

> [!IMPORTANT]
> As informações a seguir não se aplica aos usuários do MSDN, porque eles são problemas específicos para licenças de Xamarin herdadas. Se você for um usuário do MSDN e vir erros semelhantes aos abaixo, tente [atualização para a versão mais recente do Xamarin](https://developer.xamarin.com/recipes/cross-platform/ide/change_updates_channel/) & consulte [guia Opções de licenciamento](~/cross-platform/get-started/requirements.md).



## <a name="invalid-license"></a>"Licença inválida"

> Licença inválida. Reative o xamarin (XA9999) não é possível determinar a edição de licença. (XA9010)

Essas mensagens podem aparecer em algumas circunstâncias.

-   A licença atual no disco pode estar fora de sincronia com as informações do usuário atual no computador. [Atualizando os arquivos de licença](~/cross-platform/troubleshooting/legacy-licenses/resync-licenses.md) para resolver esse problema em muitos casos.

-   O computador pode ter 2 ativações duplicadas conflitantes. Esse tipo de licença não é usado pelo Xamarin. Se você tiver problemas que parecem estar relacionados a esse erro, verifique [suporte por Email](https://www.xamarin.com/support).

-   A conta Xamarin ainda não pode ser convidada para uma licença do Xamarin. Consulte também: [equipe de gerenciamento de licenças](~/cross-platform/troubleshooting/legacy-licenses/team-management.md).

## <a name="failed-to-load-android-entitlements"></a>"Falha ao carregar direitos Android"

> Erro Mono.VisualStudio.Shell.ShellPackage: 0: Falha ao carregar direitos Android: licença inválida. Reativar, xamarin (XA9999) Mono.VisualStudio.Shell.ShellPackage erro: 0: Falha na atualização da licença: licença inválida. Por favor, reativar xamarin (XA9999)

Esta é uma versão mais antiga do problema "Licença inválida" acima. Se as mesmas etapas de solução de problemas.

## <a name="this-version-was-released-after-your-subscription-expired"></a>"Essa versão foi liberado após sua assinatura expirou"

> Erro XA9000: Esta versão foi liberada após sua assinatura expirou (11/11/2014 5:11: 41H). (XA9000) (Mobile.Android.Utilities) erro XA9010: não é possível determinar a edição de licença. (XA9010) (Mobile.Android.Utilities)

Geralmente, essas mensagens aparecem em duas situações:

-   As licenças no disco estão desatualizadas. [Atualizando os arquivos de licença](~/cross-platform/troubleshooting/legacy-licenses/resync-licenses.md) para resolver esse problema em muitos casos.

-   A assinatura de Xamarin não está mais ativa e a versão atual instalada do Xamarin é mais recente que a data de validade da assinatura. Nesse caso a correção correta é [fazer o downgrade](http://kb.xamarin.com/customer/portal/articles/1699777) para uma versão do Xamarin que foi lançado antes que a assinatura expirou. Fique à vontade para enviar um email para [ hello@xamarin.com ](mailto:hello@xamarin.com) para solicitar a versão mais recente para a sua assinatura válida. Se você vir o erro após o downgrade, certifique-se de que tente atualizar os arquivos de licença muito.

## <a name="additional-references"></a>Referências adicionais

-   [Como atualizar licenças](~/cross-platform/troubleshooting/legacy-licenses/resync-licenses.md)
-   [Como fazer o downgrade do Xamarin](http://kb.xamarin.com/customer/portal/articles/1699777-downgrading)
-   [Lista de códigos de erro do xamarin](~/android/troubleshooting/errors.md)
-   [Lista de códigos de erro MonoTouch (xamarin)](~/ios/troubleshooting/mtouch-errors.md)

### <a name="next-steps"></a>Próximas etapas
Para obter mais assistência, entre em contato conosco ou se esse problema permanece mesmo após utilizando as informações acima, consulte [quais opções de suporte estão disponíveis para Xamarin?](~/cross-platform/troubleshooting/support-options.md) para obter informações sobre opções de contato, sugestões, bem como a registrar um bug de novo, se necessário.
