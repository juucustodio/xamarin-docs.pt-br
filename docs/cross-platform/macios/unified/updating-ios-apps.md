---
title: Atualizando aplicativos iOS existentes
description: Este documento descreve as etapas que devem ser seguidas para atualizar um aplicativo xamarin. IOS da API clássica para a API unificada.
ms.prod: xamarin
ms.assetid: 303C36A8-CBF4-48C0-9412-387E95024CAB
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 4e471aaca2a7a5f247b21dd1c1863a01b062a716
ms.sourcegitcommit: afe9d93373d66eb45d82cabefca83b5733969634
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67855749"
---
# <a name="updating-existing-ios-apps"></a>Atualizando aplicativos iOS existentes

_Siga estas etapas para atualizar um aplicativo xamarin. IOS existente para usar a API unificada._

Atualizando um aplicativo existente para usar a API unificada requer alterações no arquivo de projeto em si, bem como para os namespaces e as APIs usadas no código do aplicativo.

## <a name="the-road-to-64-bits"></a>O caminho para a 64 Bits

As novas APIs de Unificação são necessários para dar suporte a arquiteturas de dispositivo de 64 bits em um aplicativo móvel do xamarin. IOS. A partir de 1º de fevereiro de 2015 a Apple requer que todos os novos envios de aplicativo para o iTunes App Store dá suporte a arquiteturas de 64 bits.

O Xamarin fornece ferramentas para Visual Studio para Mac e Visual Studio automatizar o processo de migração da API clássica para a API unificada, ou você pode converter os arquivos de projeto manualmente. Enquanto o usando as ferramentas automático é altamente recomendável, este artigo abordará os dois métodos.

### <a name="before-you-start"></a>Antes de começar...

Antes de atualizar seu código existente para a API unificada, é altamente recomendável que você elimine todos *avisos de compilação*. Muitas *avisos* na API do clássico se tornará erros depois de migrar para a Unificação. Corrigi-los antes de começar é mais fácil, porque as mensagens do compilador da API clássica geralmente fornecem dicas sobre o que atualizar.

## <a name="automated-updating"></a>Automatizada de atualização

Depois que foram corrigidos os avisos, selecione um projeto existente do iOS no Visual Studio para Mac ou Visual Studio e escolha **migrar para a API unificada do xamarin. IOS** da **projeto** menu. Por exemplo:

![](updating-ios-apps-images/beta-tool1.png "Escolha as migrações para a API unificada do xamarin. IOS no menu projeto")

Será necessário concordar com este aviso antes de ser executado a migração automatizada (Obviamente, certifique-se você tem controle de origem/backups antes de embarcar nesse adventure):

![](updating-ios-apps-images/beta-tool2.png "Aceitar esse aviso antes da migração automatizada será executado")

A ferramenta basicamente automatiza todas as etapas descritas a **atualização manualmente** seção apresentada abaixo e é o método sugerido de conversão de um projeto existente do xamarin. IOS para a API unificada.

## <a name="steps-to-update-manually"></a>Etapas para atualizar manualmente

Novamente, depois que foram corrigidos os avisos, siga estas etapas para atualizar manualmente os aplicativos xamarin. IOS para usar a nova API unificada:

### <a name="1-update-project-type--build-target"></a>1. Tipo de projeto de atualização & destino de compilação

Alterar o tipo de projeto em seu **csproj** arquivos de `6BC8ED88-2882-458C-8E55-DFD12B67127B` para `FEACFBD2-3405-455C-9665-78FE426C6842`. Editar o **csproj** em um editor de texto, substituindo o primeiro item no `<ProjectTypeGuids>` elemento, conforme mostrado:

![](updating-ios-apps-images/csproj.png "Edite o arquivo csproj em um editor de texto, substituindo o primeiro item no elemento ProjectTypeGuids, conforme mostrado")

Alterar o **importação** elemento que contém `Xamarin.MonoTouch.CSharp.targets` para `Xamarin.iOS.CSharp.targets` conforme mostrado:

![](updating-ios-apps-images/csproj2.png "Altere o elemento de importação que contém Xamarin.MonoTouch.CSharp.targets para Xamarin.iOS.CSharp.targets, conforme mostrado")

### <a name="2-update-project-references"></a>2. Atualizar referências de projeto

Expanda o projeto de aplicativo iOS **referências** nó. Mostrarão inicialmente um * quebrada - **monotouch** referência semelhante a esta captura de tela (como acabamos de alterar o tipo de projeto):

![](updating-ios-apps-images/references.png "Ele mostrarão inicialmente uma referência monotouch quebrado semelhante a esta captura de tela porque o tipo de projeto foi alterado")

Clique com botão direito no projeto de aplicativo iOS para **Editar referências**, em seguida, clique no **monotouch** referenciar e excluí-lo usando o botão "X" vermelho.

![](updating-ios-apps-images/references-delete-monotouch-sml.png "Com o botão direito no projeto de aplicativo do iOS para editar referências, e em seguida, clique na referência monotouch e excluí-lo usando o vermelho botão X")

Agora, role até o final da lista de referências e escala para o **xamarin. IOS** assembly.

![](updating-ios-apps-images/references-add-xamarinios-sml.png "Agora, role até o final da lista de referências e escala o assembly do xamarin. IOS")

Pressione **Okey** para salvar alterações de referências de projeto.

### <a name="3-remove-monotouch-from-namespaces"></a>3. Remover MonoTouch de Namespaces

Remover o **MonoTouch** prefixo de namespaces no `using` instruções ou onde quer que um nome de classe foi totalmente qualificado (por exemplo. `MonoTouch.UIKit` torna-se apenas `UIKit`).

### <a name="4-remap-types"></a>4. Remapear tipos

[Tipos nativos](~/cross-platform/macios/nativetypes.md) ter sido introduzido que substitua alguns tipos que foram usados anteriormente, como instâncias do `System.Drawing.RectangleF` com `CoreGraphics.CGRect` (por exemplo). A lista completa de tipos pode ser encontrada na [tipos nativos](~/cross-platform/macios/nativetypes.md) página.

### <a name="5-fix-method-overrides"></a>5. Corrija as substituições de método

Alguns `UIKit` métodos tiveram sua assinatura alterada para usar o novo [tipos nativos](~/cross-platform/macios/nativetypes.md) (como `nint`). Se a subclasses personalizadas substituem esses métodos, as assinaturas não corresponderão mais e resultarão em erros. Corrigi essas substituições de método alterando a subclasse de acordo com a nova assinatura, usando tipos nativos.

Exemplos incluem a alteração `public override int NumberOfSections (UITableView tableView)` para retornar `nint` e alterando ambos retornam o tipo e tipos de parâmetros `public override int RowsInSection (UITableView tableView, int section)` para `nint`.

## <a name="considerations"></a>Considerações

As seguintes considerações devem ser levadas em conta ao converter um projeto existente do xamarin. IOS da API clássica para a nova API unificada se baseia-se de que o aplicativo em um ou mais componente ou pacote do NuGet.

### <a name="components"></a>Componentes

Qualquer componente que você incluiu em seu aplicativo também precisam ser atualizados para a API unificada, ou você receberá um conflito ao tentar compilar. Para qualquer componente incluído, substituir a versão atual com uma nova versão de Store de componente do Xamarin que dá suporte a API unificada e fazer uma compilação limpa. Qualquer componente que ainda não foi convertido pelo autor, exibirá um aviso apenas no repositório de componente de 32 bits.

### <a name="nuget-support"></a>Suporte do NuGet

Enquanto estamos contribuiu com as alterações no NuGet para trabalhar com o suporte de API unificada, não houve uma nova versão do NuGet, portanto, estamos avaliando como obter o NuGet para reconhecer as novas APIs.

Até esse momento, assim como os componentes, você precisará alternar qualquer pacote NuGet que você incluiu em seu projeto para uma versão que dá suporte a APIs unificadas e fazer uma compilação limpa posteriormente.

> [!IMPORTANT]
> Se você tiver um erro no formulário _"Erro 3 não pode incluir 'MonoTouch. dll' e 'Xamarin' no mesmo projeto xamarin. IOS – 'Xamarin' é referenciada explicitamente, enquanto 'MonoTouch. dll' é referenciado por ' xxx, versão = 0.0.000, Cultura = neutral, PublicKeyToken = null'"_ após converter seu aplicativo para as APIs unificadas, ela normalmente é devido a ter um componente ou o pacote do NuGet no projeto que não foi atualizado para a API unificada. Você precisará remover o componente/NuGet existente, atualize para uma versão que dá suporte a APIs unificadas e fazer uma compilação limpa.

## <a name="enabling-64-bit-builds-of-xamarinios-apps"></a>Habilitação de 64 bits compilações de aplicativos xamarin. IOS

Para um aplicativo móvel de xamarin. IOS que foi convertido para a API unificada, o desenvolvedor precisa ainda permitem a criação do aplicativo para computadores de 64 bits de opções do aplicativo. Consulte a **habilitando bits de 64 compilações de aplicativos xamarin. IOS** da [considerações sobre plataformas de 32/64 bits](~/cross-platform/macios/32-and-64/index.md#enable-64) compilações de documento para obter instruções detalhadas sobre como habilitar a 64 bits.

## <a name="finishing-up"></a>Finalizando

Se você optar por usar o método manual ou automático para converter seu aplicativo xamarin. IOS do clássico para as APIs de Unificação, há várias instâncias que serão ainda mais, requerem intervenção manual. Consulte nosso [dicas para atualizar o código para a API unificada](~/cross-platform/macios/unified/updating-tips.md) de documento para os problemas conhecidos e soluções alternativas.

## <a name="related-links"></a>Links relacionados

- [Dicas para atualizar o código para a API unificada](~/cross-platform/macios/unified/updating-tips.md)
- [Trabalhar com tipos nativos em aplicativos de plataforma cruzada](~/cross-platform/macios/native-types-cross-platform.md)
- [Diferenças de API unificada do vs clássico](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
