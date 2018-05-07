---
title: Quais opções de suporte estão disponíveis para Xamarin?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: E4EF7E0F-ED86-44C9-9F98-38ECF881C7E2
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.openlocfilehash: 47480adfdff5ee3282c1c7fa422260184f91c071
ms.sourcegitcommit: c9ebf456e1c6924956bedb13f4ea78ff09f7b1a0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2018
---
# <a name="what-support-options-are-available-for-xamarin"></a>Quais opções de suporte estão disponíveis para Xamarin?

## <a name="summary-of-support-options"></a>Resumo das opções de suporte

Conforme mostrado no [página de suporte de Xamarin](https://www.xamarin.com/support), várias opções estão disponíveis.  Aqui estão algumas recomendações iniciais rápidas [inspirada estouro de pilha](http://stackoverflow.com/help/product-support) sobre como escolher a melhor opção:

|   |   |
|---|---|
|"O que esse erro significa?" ou "Como fazer...?"|[Estouro de pilha](http://stackoverflow.com/questions/ask?tags=xamarin) sob o *xamarin* marca|
|Acredito que esse problema é causado por uma falha no código-fonte Xamarin.|[Xamarin Bugzilla](https://bugzilla.xamarin.com/page.cgi?id=bug-writing.html)|
|Tenho uma ideia, uma solicitação de recurso ou uma nova solicitação de documentação.|[Página Xamarin UserVoice](https://xamarin.uservoice.com)|
|Quais recursos, estruturas ou ferramentas de outros usuários recomendamos para...? (e outras discussões abertas)|[Fóruns do Xamarin](https://forums.xamarin.com)|
|Tenho uma pergunta sobre assinaturas, licenciamento e preços.|Email ou ligue usando as informações de contato sobre o [perguntas Frequentes](https://www.xamarin.com/faq)|

A equipe de suporte de Xamarin ajuda a monitorar a cada uma dessas opções.  Recomendamos que todos os usuários para contribuir com suas perguntas ponderadas e relatórios para tirar proveito do e ajudar a aumentar a comunidade de desenvolvedores do Xamarin experiente.  Em busca de defeitos suspeitos, certifique-se de enviar relatórios de erros quando possível para garantir que os problemas são controlados formalmente pela equipe de engenharia do Xamarin.

<a name="Visual_Studio_email_support_incidents_for_Xamarin_topics"/>

### <a name="visual-studio-email-support-incidents-for-xamarin-topics"></a>Incidentes de suporte de email do Visual Studio para tópicos de Xamarin

Recursos do SDK do Xamarin e IDE estão qualificados para incidentes de suporte técnico como parte do [benefícios de assinante do Visual Studio](https://msdn.microsoft.com/subscriptions/bb266240) e [Microsoft Premier Support](https://www.microsoft.com/microsoftservices/support.aspx).  Isso corresponde às políticas de outras plataformas de desenvolvimento no Visual Studio como UWP e ASP.NET.  Xamarin incidentes enviada que assim será atribuído à equipe de suporte de Xamarin.  Consulte o [perguntas frequentes sobre o suporte da Microsoft](https://support.microsoft.com/gp/offerprophone) para obter mais informações sobre as políticas para esses incidentes.

### <a name="information-for-users-migrating-from-expired-xamarin-subscriptions-to-visual-studio-subscriptions"></a>Informações para usuários que migram de assinaturas expiradas do Xamarin para assinaturas do Visual Studio

Xamarin é [agora uma parte integrante de assinaturas do Visual Studio](https://blog.xamarin.com/xamarin-for-all/) e segue os benefícios de assinante do Visual Studio standard.  Consulte a seção anterior [ *incidentes de suporte do Visual Studio* ](#Visual_Studio_email_support_incidents_for_Xamarin_topics) para obter mais alguns detalhes.

## <a name="detailed-recommendations"></a>Recomendações detalhadas

### <a name="what-does-this-error-mean-or-how-do-i--"></a>"O que esse erro significa?" ou "Como fazer...?"

[Postar uma pergunta sobre estouro de pilha](http://stackoverflow.com/questions/ask?tags=xamarin) para obter ajuda da comunidade sobre erros comuns ou implementação complicada e questões de uso da API.  Certifique-se de incluir o _xamarin_ marca na pergunta.  Consulte também: [como solicitar a uma boa pergunta?](http://stackoverflow.com/help/how-to-ask)

#### <a name="additional-resources"></a>Recursos adicionais

-   Verificar [Central de desenvolvedores do Xamarin](/index.md) para documentação, manuais e exemplos.
-   Verificar [documentação de estouro de pilha](http://stackoverflow.com/documentation) para explicações e código de exemplo contribuído pela comunidade.
-   Considere [Xamarin do parceiro de consultoria](https://www.xamarin.com/consulting-partners) serviços para obter assistência escalado com a implementação do recurso, associações para revisão de Android, iOS ou Mac bibliotecas, de terceiros de código para obter assistência de arquitetura, solução de problemas detalhada, criação de perfil de memória e desempenho ou otimização de código existente.

### <a name="what-does-this-installation-error-mean"></a>O que faz isso _instalação_ média de erro?

[Postar uma pergunta sobre estouro de pilha](http://stackoverflow.com/questions/ask?tags=xamarin+installation) que inclui tanto o _xamarin_ e _instalação_ marcas.  Como de costume, certifique-se de verificar brevemente dúvidas anterior que pode ter resolvido o problema antes.  Consulte também: [como solicitar a uma boa pergunta?](http://stackoverflow.com/help/how-to-ask)

(Observação: essa recomendação ainda está em evolução para garantir que os novos usuários tenham o melhor acesso para assistência para armadilhas comuns durante a instalação inicial.  Fique atento para atualizações.)

#### <a name="additional-resources"></a>Recursos adicionais

-   Verifique o [guias de instalação](~/cross-platform/get-started/installation/index.md) e [requisitos de sistema](~/cross-platform/get-started/requirements.md) para certificar-se de que todos os pacotes corretos estão instalados e compatíveis.

### <a name="i-believe-this-problem-is-caused-by-a-defect-in-the-xamarin-source-code"></a>Acredito que esse problema é causado por uma falha no código-fonte Xamarin.

[Registre um bug no Bugzilla do Xamarin](https://bugzilla.xamarin.com/page.cgi?id=bug-writing.html) para a equipe de engenharia do Xamarin pode investigar.  Consulte também: [quando e como eu deve arquivar um relatório de bugs?](~/cross-platform/troubleshooting/questions/howto-file-bug.md)

(*Para problemas após atualiza estável*: não hesitar em um relatório de erros rápida de arquivos, se você encontrar um erro, falha de instalação ou outro comportamento inesperado após a atualização para uma nova versão estável.  Esses relatórios são muito importantes.  Of Course, não se esqueça de verificar o [notas de versão](https://developer.xamarin.com/releases/) rapidamente no caso do problema que você está vendo pode ser uma alteração intencional ou um problema conhecido.)

#### <a name="additional-resources"></a>Recursos adicionais

-   Se um problema tiver resolvido localmente, criando uma solução personalizada com base em [projetos de desenvolvimento de código aberto do Xamarin](http://open.xamarin.com/), considere enviando uma solicitação de recepção para obter as alterações incorporadas Xamarin!

### <a name="what-does-this-error-in-a-preview-product-mean"></a>O que faz esse erro um _produto visualização_ significa?

Por favor, [arquivar um bug no Bugzilla do Xamarin](https://bugzilla.xamarin.com/page.cgi?id=bug-writing.html) para erros inesperados ou comportamentos, você pode encontrar em um produto de visualização.  Seus comentários ajudará os engenheiros Xamarin aprimorar futuras versões da visualização.

Para discussão de comunidade sobre problemas conhecidos e soluções alternativas, você poderá postar uma pergunta no Fórum de visualização correspondente:

-   [Xamarin Live Player](https://forums.xamarin.com/categories/live-player)
-   Para visualizações de associações do SDK para novas versões do iOS, Android e macOS SDKs, localize o lançamento de versão de visualização correspondente no [xamarin](http://forums.xamarin.com/categories/android), [xamarin](http://forums.xamarin.com/categories/ios), ou [Xamarin.Mac ](http://forums.xamarin.com/categories/mac) Fórum.
-   Para outros recursos de visualização, navegue para um anúncio de versão correspondentes no [pré-lançamento & Betas](http://forums.xamarin.com/categories/xamarin-prerelease) Fórum.  Ou, se não houver nenhuma correspondência comunicado, fique à vontade para aumentar sua pergunta em seu próprio thread no Fórum.

### <a name="i-have-an-idea-feature-request-or-documentation-request"></a>Tenho uma ideia, a solicitação de recurso ou a solicitação de documentação.

[Compartilhar sua ideia na página do UserVoice do Xamarin](https://xamarin.uservoice.com) para a comunidade pode vote nela e a equipe de Xamarin pode trabalhar em adicioná-lo para o roteiro.

### <a name="i-have-a-question-about-subscriptions-licensing-or-pricing"></a>Tenho uma pergunta sobre assinaturas, licenciamento e preços.

Verifique o [perguntas frequentes sobre](https://www.xamarin.com/faq)e, em seguida, use uma das opções de contato listadas nessa página para qualquer perguntas de acompanhamento.

### <a name="how-do-i-get-involved-in-xamarins-open-source-development-projects"></a>Como obter envolvido em projetos de desenvolvimento de software livre do Xamarin?

Vá para [open.xamarin.com](http://open.xamarin.com/) para começar.

### <a name="i-found-a-mistake-or-missing-information-in-the-guides-or-recipes-on-the-xamarin-developer-centerindexmd"></a>Encontrei um erro ou não possui informações na guias ou receitas no [Central de desenvolvedores do Xamarin](/index.md).

Use o _tenho um problema_ botão para enviar seus comentários diretamente na página em que você observou que o problema.

[<img src="support-options-images/feedback.png" style="width: 152px; height: 90px;">](support-options-images/feedback.png)

### <a name="what-resources-frameworks-or-tools-do-other-users-recommend-for--"></a>Quais recursos, estruturas ou ferramentas de outros usuários recomendamos para...?

[Postar uma pergunta nos fóruns do](https://forums.xamarin.com/) para discutir esses tópicos mais amplos que podem ser [fora do local no estouro de pilha](http://stackoverflow.com/help/dont-ask).

### <a name="why-do-you--"></a>Por que você...?

[Postar uma pergunta nos fóruns do](https://forums.xamarin.com/) para iniciar uma conversa com membros da comunidade e a equipe do Xamarin.

### <a name="when-will-you--"></a>Quando você vai...?

-   Verifique o [versão Blog](http://releases.xamarin.com/) para linhas de tempo de ativo atual [Xamarin plataforma](https://www.xamarin.com/platform) versões.
-   [Compartilhar sua solicitação na página de UserVoice do Xamarin](https://xamarin.uservoice.com) para a comunidade pode vote nela e a equipe de Xamarin pode trabalhar em adicioná-lo para o roteiro.

