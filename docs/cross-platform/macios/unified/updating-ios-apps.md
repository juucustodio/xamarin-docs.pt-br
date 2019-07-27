---
title: Atualizando aplicativos iOS existentes
description: Este documento descreve as etapas que devem ser seguidas para atualizar um aplicativo Xamarin. iOS da API Clássica para a API Unificada.
ms.prod: xamarin
ms.assetid: 303C36A8-CBF4-48C0-9412-387E95024CAB
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: b0999ff6fc3b3042827f11ae1e127ef7bb9fedfe
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68509611"
---
# <a name="updating-existing-ios-apps"></a>Atualizando aplicativos iOS existentes

_Siga estas etapas para atualizar um aplicativo Xamarin. iOS existente para usar o API Unificada._

Atualizar um aplicativo existente para usar a API Unificada requer alterações no próprio arquivo do projeto, bem como nos namespaces e APIs usados no código do aplicativo.

## <a name="the-road-to-64-bits"></a>A estrada para 64 bits

As novas APIs unificadas são necessárias para dar suporte a arquiteturas de dispositivo de 64 bits de um aplicativo móvel Xamarin. iOS. A partir de 1º de fevereiro, a Apple 2015 requer que todos os novos envios de aplicativos para a iTunes App Store ofereçam suporte a arquiteturas de 64 bits.

O Xamarin fornece ferramentas para o Visual Studio para Mac e o Visual Studio para automatizar o processo de migração do API Clássica para o API Unificada ou você pode converter os arquivos de projeto manualmente. Embora o uso das ferramentas automáticas seja altamente sugerido, este artigo abordará os dois métodos.

### <a name="before-you-start"></a>Antes de começar...

Antes de atualizar seu código existente para o API Unificada, é altamente recomendável que você elimine todos os *avisos de compilação*. Muitos *avisos* na API clássica se tornarão erros depois que você migrar para o Unified. Corrigi-los antes de começar é mais fácil porque as mensagens do compilador da API Clássica geralmente fornecem dicas sobre o que atualizar.

## <a name="automated-updating"></a>Atualização automatizada

Depois que os avisos tiverem sido corrigidos, selecione um projeto iOS existente no Visual Studio para Mac ou no Visual Studio e escolha **migrar para o Xamarin. iOS API unificada** no menu **projeto** . Por exemplo:

![](updating-ios-apps-images/beta-tool1.png "Escolha migrar para o Xamarin. iOS API Unificada no menu do projeto")

Você precisará concordar com este aviso antes que a migração automatizada seja executada (obviamente, você deve garantir que tenha backup/controle do código-fonte antes de embarcar nesta aventura):

![](updating-ios-apps-images/beta-tool2.png "Concordar com este aviso antes que a migração automatizada seja executada")

Basicamente, a ferramenta automatiza todas as etapas descritas na seção **atualização manual** apresentada abaixo e é o método sugerido para converter um projeto Xamarin. Ios existente no API unificada.

## <a name="steps-to-update-manually"></a>Etapas para atualizar manualmente

Novamente, depois que os avisos forem corrigidos, siga estas etapas para atualizar manualmente os aplicativos Xamarin. iOS para usar o novo API Unificada:

### <a name="1-update-project-type--build-target"></a>1. Atualizar o tipo de projeto & destino da compilação

Altere o tipo de projeto em  seus arquivos csproj `6BC8ED88-2882-458C-8E55-DFD12B67127B` de `FEACFBD2-3405-455C-9665-78FE426C6842`para. Edite o arquivo **csproj** em um editor de texto, substituindo o primeiro `<ProjectTypeGuids>` item no elemento, conforme mostrado:

![](updating-ios-apps-images/csproj.png "Edite o arquivo csproj em um editor de texto, substituindo o primeiro item no elemento ProjectTypeGuids, conforme mostrado")

Altere o elemento de **importação** que `Xamarin.MonoTouch.CSharp.targets` contém `Xamarin.iOS.CSharp.targets` para, conforme mostrado:

![](updating-ios-apps-images/csproj2.png "Altere o elemento Import que contém Xamarin. MonoTouch. CSharp. targets para Xamarin. iOS. CSharp. targets, conforme mostrado")

### <a name="2-update-project-references"></a>2. Atualizar referências do projeto

Expanda o nó **referências** do projeto de aplicativo Ios. Inicialmente, ele mostrará uma referência **MonoTouch** * quebrada semelhante a esta captura de tela (porque acabamos de alterar o tipo de projeto):

![](updating-ios-apps-images/references.png "Inicialmente, ele mostrará uma referência MonoTouch quebrada semelhante a esta captura de tela, pois o tipo de projeto foi alterado")

Clique com o botão direito do mouse no projeto de aplicativo iOS para **Editar referências**e  clique na referência do MonoTouch e exclua-a usando o botão vermelho "X".

![](updating-ios-apps-images/references-delete-monotouch-sml.png "Clique com o botão direito do mouse no projeto de aplicativo iOS para editar referências e, em seguida, clique na referência do MonoTouch e exclua-a usando o botão X vermelho")

Agora, role até o final da lista de referências e marque o assembly **Xamarin. Ios** .

![](updating-ios-apps-images/references-add-xamarinios-sml.png "Agora, role até o final da lista de referências e marque o assembly Xamarin. iOS")

Pressione **OK** para salvar as alterações de referências do projeto.

### <a name="3-remove-monotouch-from-namespaces"></a>3. Remover o MonoTouch dos namespaces

Remova o  prefixo MonoTouch dos namespaces em `using` instruções ou sempre que um ClassName tiver sido totalmente qualificado (por exemplo, `MonoTouch.UIKit`se torna `UIKit`apenas).

### <a name="4-remap-types"></a>4. Tipos de remapeamento

Foram introduzidos [tipos nativos](~/cross-platform/macios/nativetypes.md) que substituem alguns tipos que foram usados anteriormente, como instâncias de `System.Drawing.RectangleF` with `CoreGraphics.CGRect` (por exemplo). A lista completa de tipos pode ser encontrada na página [tipos nativos](~/cross-platform/macios/nativetypes.md) .

### <a name="5-fix-method-overrides"></a>5. Corrigir substituições de método

Alguns `UIKit` métodos tiveram sua assinatura alterada para usar os novos [tipos nativos](~/cross-platform/macios/nativetypes.md) ( `nint`como). Se as subclasses personalizadas substituirem esses métodos, as assinaturas não corresponderão mais e resultarão em erros. Corrija essas substituições de método alterando a subclasse para corresponder à nova assinatura usando tipos nativos.

Os exemplos incluem `public override int NumberOfSections (UITableView tableView)` alterar para `nint` retornar e alterar o tipo de retorno e os `public override int RowsInSection (UITableView tableView, int section)` tipos `nint`de parâmetro no para.

## <a name="considerations"></a>Considerações

As considerações a seguir devem ser levadas em conta ao converter um projeto Xamarin. iOS existente do API Clássica para o novo API Unificada se esse aplicativo depender de um ou mais componentes ou pacote NuGet.

### <a name="components"></a>Componentes

Qualquer componente que você tenha incluído em seu aplicativo também precisará ser atualizado para o API Unificada ou você receberá um conflito quando tentar compilar. Para qualquer componente incluído, substitua a versão atual por uma nova versão da loja de componentes do Xamarin que dá suporte à API Unificada e faça uma compilação limpa. Qualquer componente que ainda não tenha sido convertido pelo autor, exibirá um aviso de 32 bits apenas no repositório de componentes.

### <a name="nuget-support"></a>Suporte do NuGet

Embora tenhamos contribuído com alterações no NuGet para trabalhar com o suporte de API Unificada, não houve uma nova versão do NuGet, portanto, estamos avaliando como obter o NuGet para reconhecer as novas APIs.

Até esse momento, assim como os componentes, você precisará alternar qualquer pacote NuGet que tenha incluído em seu projeto para uma versão que dê suporte às APIs unificadas e faça uma compilação limpa posteriormente.

> [!IMPORTANT]
> Se você tiver um erro no formato _"erro 3, não será possível incluir ' MonoTouch. dll ' e ' Xamarin. Ios. dll ' no mesmo projeto do xamarin. Ios-' xamarin. Ios. dll ' é referenciado explicitamente, enquanto ' MonoTouch. dll ' é referenciado por ' xxx, Version = 0.0.000, Culture = neutral, PublicKeyToken = null ' "_ depois de converter seu aplicativo para as APIs unificadas, normalmente, é devido a ter um componente ou pacote NuGet no projeto que não foi atualizado para o API unificada. Você precisará remover o componente/NuGet existente, atualizar para uma versão que ofereça suporte a APIs unificadas e fazer uma compilação limpa.

## <a name="enabling-64-bit-builds-of-xamarinios-apps"></a>Habilitando Builds de 64 bits de aplicativos Xamarin. iOS

Para um aplicativo móvel Xamarin. iOS que foi convertido no API Unificada, o desenvolvedor ainda precisa habilitar a criação do aplicativo para computadores de 64 bits a partir das opções do aplicativo. Consulte as **compilações de 64 bits de aplicativos Xamarin. Ios** do documento [Considerações sobre plataforma de 32/64 bits](~/cross-platform/macios/32-and-64/index.md#enable-64) para obter instruções detalhadas sobre como habilitar os builds de 64 bits.

## <a name="finishing-up"></a>Concluindo

Se você optar por usar ou não o método automático ou manual para converter seu aplicativo Xamarin. iOS do clássico para as APIs unificadas, haverá várias instâncias que exigirão mais intervenção manual. Confira nossas [dicas para atualizar o código para o documento API unificada](~/cross-platform/macios/unified/updating-tips.md) para problemas conhecidos e contornar.

## <a name="related-links"></a>Links relacionados

- [Dicas para atualizar o código para a API unificada](~/cross-platform/macios/unified/updating-tips.md)
- [Trabalhar com tipos nativos em aplicativos de plataforma cruzada](~/cross-platform/macios/native-types-cross-platform.md)
- [Diferenças clássicas do vs API Unificada](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md)
