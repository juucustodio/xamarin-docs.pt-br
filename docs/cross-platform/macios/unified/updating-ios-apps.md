---
title: Atualizando aplicativos do iOS existente
description: Siga estas etapas para atualizar um aplicativo xamarin existente para usar a API unificada.
ms.topic: article
ms.prod: xamarin
ms.assetid: 303C36A8-CBF4-48C0-9412-387E95024CAB
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: d5c8828557d5c5d3d3212378f94e7e0eb0902f61
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="updating-existing-ios-apps"></a>Atualizando aplicativos do iOS existente

_Siga estas etapas para atualizar um aplicativo xamarin existente para usar a API unificada._

Atualizar um aplicativo existente para usar a API unificada requer alterações no próprio arquivo do projeto, bem como para os namespaces e as APIs usadas no código do aplicativo.

# <a name="the-road-to-64-bits"></a>O caminho para 64 Bits

As novas APIs unificado são necessários para dar suporte a arquiteturas de dispositivo de 64 bits de um aplicativo móvel do xamarin. A partir de 1º de fevereiro de 2015 Apple requer que todos os envios de aplicativo novo para o iTunes App Store dá suporte a arquiteturas de 64 bits.

Xamarin fornece ferramentas para Visual Studio para Mac e o Visual Studio automatizar o processo de migração da API clássica para a API unificada ou você pode converter os arquivos de projeto manualmente. Enquanto o usando as ferramentas automática é altamente recomendável, este artigo aborda os dois métodos.

## <a name="before-you-start"></a>Antes de começar...

Antes de atualizar seu código existente para a API unificada, é altamente recomendável que você elimine todas *avisos de compilação*. Muitos *avisos* na API do clássico se tornarão erros quando você migrar para unificada. É mais fácil corrigi-los antes de iniciar, porque as mensagens do compilador da API clássica geralmente fornecem dicas sobre o que a atualização.


# <a name="automated-updating"></a>Automatizada de atualização

Depois que os avisos foram corrigidos, selecione um projeto existente do iOS no Visual Studio para Mac ou o Visual Studio e escolha **migrar para o API unificada do xamarin** do **projeto** menu. Por exemplo:

![](updating-ios-apps-images/beta-tool1.png "Escolher migrar xamarin Unified API no menu projeto")

Você precisará concordar com este aviso antes da execução de uma migração automatizada (Obviamente, você deve garantir você tem controle de origem/backups antes de embarcar neste adventure):

![](updating-ios-apps-images/beta-tool2.png "Concordar com este aviso antes da execução de uma migração automatizada")

A ferramenta basicamente automatiza todas as etapas descritas no **atualização manualmente** seção apresentados a seguir e é o método sugerido de converter um projeto existente do xamarin para a API unificada.


# <a name="steps-to-update-manually"></a>Etapas para atualizar manualmente

Novamente, depois que os avisos foram corrigidos, siga estas etapas para atualizar manualmente a aplicativos xamarin para usar a nova API unificada:

## <a name="1-update-project-type--build-target"></a>1. Tipo de projeto de atualização e o destino de compilação

Alterar o tipo de projeto em seu **csproj** arquivos de `6BC8ED88-2882-458C-8E55-DFD12B67127B` para `FEACFBD2-3405-455C-9665-78FE426C6842`. Editar o **csproj** arquivo em um editor de texto, substituindo o item primeiro o `<ProjectTypeGuids>` elemento conforme mostrado:

![](updating-ios-apps-images/csproj.png "Edite o arquivo csproj em um editor de texto, substituindo o primeiro item no elemento ProjectTypeGuids, conforme mostrado")

Alterar o **importação** elemento que contém `Xamarin.MonoTouch.CSharp.targets` para `Xamarin.iOS.CSharp.targets` conforme mostrado:

![](updating-ios-apps-images/csproj2.png "Altere o elemento de importação que contém Xamarin.MonoTouch.CSharp.targets para Xamarin.iOS.CSharp.targets, conforme mostrado")


## <a name="2-update-project-references"></a>2. Atualizar referências de projeto

Expanda o projeto de aplicativo iOS **referências** nó. Mostrarão inicialmente um * quebrada - **monotouch** referência semelhante à captura de tela (como acabamos de alterar o tipo de projeto):

![](updating-ios-apps-images/references.png "Ele mostrarão inicialmente uma referência monotouch dividido semelhante a esta captura de tela porque o tipo de projeto foi alterado")

Com o botão direito no projeto de aplicativo do iOS para **Editar referências**, em seguida, clique no **monotouch** referência e excluí-lo usando o botão "X" vermelho.

![](updating-ios-apps-images/references-delete-monotouch-sml.png "Clique com botão direito no projeto de aplicativo iOS para referências de editar, clique na referência monotouch e excluí-lo usando o vermelho botão X")

Agora, role até o final da lista de referências e escala de **xamarin** assembly.

![](updating-ios-apps-images/references-add-xamarinios-sml.png "Agora, role até o final da lista de referências e escala assembly xamarin")

Pressione **Okey** para salvar alterações de referências de projeto.

## <a name="3-remove-monotouch-from-namespaces"></a>3. Remover MonoTouch de Namespaces

Remover o **MonoTouch** prefixo de namespaces no `using` instruções ou sempre que um nome de classe foi totalmente qualificado (por exemplo. `MonoTouch.UIKit` torna-se apenas `UIKit`).

## <a name="4-remap-types"></a>4. Remapear tipos

[Tipos nativos](~/cross-platform/macios/nativetypes.md) foram introduzidas qual substituir alguns tipos que foram usados anteriormente, como instâncias de `System.Drawing.RectangleF` com `CoreGraphics.CGRect` (por exemplo). A lista completa de tipos pode ser encontrada no [tipos nativos](~/cross-platform/macios/nativetypes.md) página.

## <a name="5-fix-method-overrides"></a>5. Corrija as substituições de método

Alguns `UIKit` métodos tiveram sua assinatura alterada para usar o novo [tipos nativos](~/cross-platform/macios/nativetypes.md) (como `nint`). Se esses métodos de substituição de subclasses personalizadas as assinaturas não irão corresponder e resultarão em erros. Corrigi essas substituições de método alterando a subclasse de acordo com a nova assinatura usando tipos nativos.

Exemplos incluem a alteração `public override int NumberOfSections (UITableView tableView)` para retornar `nint` e alterando ambos retornam o tipo e os tipos de parâmetro em `public override int RowsInSection (UITableView tableView, int section)` para `nint`.

# <a name="considerations"></a>Considerações

As seguintes considerações devem ser levadas em conta ao converter um projeto existente do xamarin da API clássica para a nova API unificada se esse aplicativo depende de um ou mais componentes ou pacote do NuGet.

## <a name="components"></a>Componentes

Qualquer componente que você incluiu em seu aplicativo também precisam ser atualizados para a API unificada ou você receberá um conflito ao tentar compilar. Para qualquer componente incluído, substituir a versão atual com uma nova versão do armazenamento de componente Xamarin que oferece suporte a API unificada e faça uma compilação limpa. Qualquer componente que ainda não foi convertido pelo autor, exibirá um aviso somente no repositório de componente de 32 bits.


## <a name="nuget-support"></a>Suporte do NuGet

Enquanto estamos contribuiu com alterações ao NuGet para funcionar com o suporte da API unificada, não houve uma nova versão do NuGet, portanto, estamos avaliando como obter NuGet reconheça as novas APIs.

Até lá, assim como os componentes, você precisará alternar qualquer pacote do NuGet incluir em seu projeto para uma versão que oferece suporte a APIs unificado e faça uma compilação limpa posteriormente.

> [!IMPORTANT]
> **Observação:** se você tiver um erro no formato _"Erro 3 não pode incluir 'monotouch.dll' e 'Xamarin.iOS.dll' no mesmo projeto xamarin - 'Xamarin.iOS.dll' é referenciado explicitamente, enquanto a 'monotouch.dll' é referenciado por ' xxx, Versão = 0.0.000, Culture = neutral, PublicKeyToken = null'"_ depois de converter seu aplicativo para as APIs unificado, normalmente ele é devido à necessidade de um componente ou um pacote do NuGet no projeto que não foi atualizado para a API unificada. Você precisará remover o componente/NuGet existente, atualize para uma versão que oferece suporte a APIs unificado e faça uma compilação limpa.




# <a name="enabling-64-bit-builds-of-xamarinios-apps"></a>Habilitação de 64 bits compilações de aplicativos xamarin

Para um aplicativo móvel de xamarin foi convertido para a API unificada, o desenvolvedor precisa ainda permitem a criação do aplicativo para computadores de 64 bits de opções do aplicativo. Consulte o **habilitando 64 Bit compilações do xamarin aplicativos de** do [considerações sobre a plataforma de 32/64 bits](~/cross-platform/macios/32-and-64.md#enable-64) compilações do documento para obter instruções detalhadas sobre como habilitar 64 bits.

# <a name="finishing-up"></a>Concluindo a

Se você optar por usar o método manual ou automática para converter seu aplicativo xamarin de clássica para as APIs unificado, ou não existem várias instâncias que serão mais, requerem intervenção manual. Confira nosso [dicas para atualizar o código para a API unificada](~/cross-platform/macios/unified/updating-tips.md) documentos para problemas conhecidos e soluções alternativas.



## <a name="related-links"></a>Links relacionados

- [Dicas para atualizar o código para a API unificada](~/cross-platform/macios/unified/updating-tips.md)
- [Trabalhando com tipos nativos em aplicativos de plataforma cruzada](~/cross-platform/macios/native-types-cross-platform.md)
- [Diferenças de API unificada do vs clássico](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
- [Migrando para a API unificada (vídeo)](http://university.xamarin.com/lightninglectures/migrating-to-the-unified-api)
