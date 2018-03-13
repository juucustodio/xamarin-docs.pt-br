---
title: Atualizando aplicativos existentes do Mac
description: Siga estas etapas para atualizar um aplicativo Xamarin.Mac existente para usar a API unificada.
ms.topic: article
ms.prod: xamarin
ms.assetid: 26673CC5-C1E5-4BAC-BEF4-9A386B296FD5
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 35665731fb0b8b669a850c06929dd951589e6bf6
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="updating-existing-mac-apps"></a>Atualizando aplicativos existentes do Mac

_Siga estas etapas para atualizar um aplicativo Xamarin.Mac existente para usar a API unificada._

Atualizar um aplicativo existente para usar a API unificada requer alterações no próprio arquivo do projeto, bem como para os namespaces e as APIs usadas no código do aplicativo.

## <a name="the-road-to-64-bits"></a>O caminho para 64 Bits

As novas APIs unificado são necessários para dar suporte a arquiteturas de dispositivo de 64 bits de um aplicativo Xamarin.Mac. A partir de 1º de fevereiro de 2015 Apple requer que todos os envios de aplicativo novo para Mac App Store dá suporte a arquiteturas de 64 bits.

Xamarin fornece ferramentas para Visual Studio para Mac e o Visual Studio automatizar o processo de migração da API clássica para a API unificada ou você pode converter os arquivos de projeto manualmente. Enquanto o usando as ferramentas automática é altamente recomendável, este artigo aborda os dois métodos.

### <a name="before-you-start"></a>Antes de começar...

Antes de atualizar seu código existente para a API unificada, é altamente recomendável que você elimine todas *avisos de compilação*. Muitos *avisos* na API do clássico se tornarão erros quando você migrar para unificada. É mais fácil corrigi-los antes de iniciar, porque as mensagens do compilador da API clássica geralmente fornecem dicas sobre o que a atualização.

## <a name="automated-updating"></a>Automatizada de atualização

Depois que os avisos foram corrigidos, selecione um projeto existente do Mac no Visual Studio para Mac ou o Visual Studio e escolha **migrar para a API de unificado de Xamarin.Mac** do **projeto** menu. Por exemplo:

![](updating-mac-apps-images/beta-tool1.png "Escolher migrar Xamarin.Mac Unified API no menu projeto")

Você precisará concordar com este aviso antes da execução de uma migração automatizada (Obviamente, você deve garantir você tem controle de origem/backups antes de embarcar neste adventure):

![](updating-mac-apps-images/migrate01.png "Concordar com este aviso antes da execução de uma migração automatizada")

Há dois tipos de estrutura de destino com suporte que podem ser selecionados ao usar a API unificada em um aplicativo de Xamarin.Mac:

- **Xamarin.Mac móvel Framework** -este é o mesmo ajustado .NET framework usada por xamarin e um subconjunto do total de suporte de xamarin **Desktop** framework. Esta é a estrutura de recomendado porque fornece menores binários médios devido ao comportamento de vinculação superior.
- **Xamarin.Mac .NET 4.5 Framework** -essa estrutura novamente, é um subconjunto do **Desktop** framework. No entanto, organizando muito menos completa **Desktop** framework que o **Mobile** framework e deve _"funcionando"_ com a maioria dos pacotes do NuGet ou 3º bibliotecas de terceiros. Isso permite que o desenvolvedor consumir padrão **Desktop** assemblies enquanto ainda usando uma estrutura com suporte, mas essa opção produz maior pacotes de aplicativos. Esta é a estrutura recomendada onde assemblies de .NET 3ª parte estão sendo usados e que não são compatíveis com o **Xamarin.Mac Mobile Framework**. Para obter uma lista de assemblies com suporte, consulte nosso [Assemblies](~/cross-platform/internals/available-assemblies.md) documentação.

Para obter informações detalhadas sobre a estrutura de destino e as implicações de selecionar um destino específico para seu aplicativo Xamarin.Mac, consulte nosso [estrutura de destino](~/mac/platform/target-framework.md) documentação. 

A ferramenta basicamente automatiza todas as etapas descritas no **atualização manualmente** seção apresentados a seguir e é o método sugerido de converter um projeto existente do Xamarin.Mac para a API unificada.

## <a name="steps-to-update-manually"></a>Etapas para atualizar manualmente

Novamente, depois que os avisos foram corrigidos, siga estas etapas para atualizar manualmente Xamarin.Mac aplicativos para usar a nova API unificada:

### <a name="1-update-project-type--build-target"></a>1. Tipo de projeto de atualização e o destino de compilação

Alterar o tipo de projeto em seu **csproj** arquivos de `42C0BBD9-55CE-4FC1-8D90-A7348ABAFB23` para `A3F8F2AB-B479-4A4A-A458-A89E7DC349F1`. Editar o **csproj** arquivo em um editor de texto, substituindo o item primeiro o `<ProjectTypeGuids>` elemento conforme mostrado:

![](updating-mac-apps-images/csproj.png "Edite o arquivo csproj em um editor de texto, substituindo o primeiro item no elemento ProjectTypeGuids, conforme mostrado")

Alterar o **importação** elemento que contém `Xamarin.Mac.targets` para `Xamarin.Mac.CSharp.targets` conforme mostrado:

![](updating-mac-apps-images/csproj2.png "Altere o elemento de importação que contém Xamarin.Mac.targets para Xamarin.Mac.CSharp.targets, conforme mostrado")

Adicione as seguintes linhas de código após a `<AssemblyName>` elemento:

```xml
<TargetFrameworkVersion>v2.0</TargetFrameworkVersion>
<TargetFrameworkIdentifier>Xamarin.Mac</TargetFrameworkIdentifier>

```

Exemplo:

![](updating-mac-apps-images/csproj3.png "Adicione estas linhas de código após o elemento < AssemblyName >")

### <a name="2-update-project-references"></a>2. Atualizar referências de projeto

Expanda o projeto de aplicativo de Mac **referências** nó. Mostrarão inicialmente um * quebrada - **XamMac** referência semelhante à captura de tela (como acabamos de alterar o tipo de projeto):

![](updating-mac-apps-images/references.png "Ele mostrarão inicialmente uma referência XamMac dividido semelhante a esta captura de tela")

Clique o **ícone de engrenagem** ao lado de **XamMac** entrada e selecione **excluir** para remover a referência.

Em seguida, clique duas vezes no **referências** pasta o **Solution Explorer** e selecione **Editar referências**. Role até a parte inferior da lista de referências e marque além **Xamarin.Mac**.

![](updating-mac-apps-images/references2.png "Role até a parte inferior da lista de referências e marque além Xamarin.Mac")

Pressione **Okey** para salvar alterações de referências de projeto.

### <a name="3-remove-monomac-from-namespaces"></a>3. Remover MonoMac de Namespaces

Remover o **MonoMac** prefixo de namespaces no `using` instruções ou sempre que um nome de classe foi totalmente qualificado (por exemplo. `MonoMac.AppKit` torna-se apenas `AppKit`).

### <a name="4-remap-types"></a>4. Remapear tipos

[Tipos nativos](~/cross-platform/macios/nativetypes.md) foram introduzidas qual substituir alguns tipos que foram usados anteriormente, como instâncias de `System.Drawing.RectangleF` com `CoreGraphics.CGRect` (por exemplo). A lista completa de tipos pode ser encontrada no [tipos nativos](~/cross-platform/macios/nativetypes.md) página.

### <a name="5-fix-method-overrides"></a>5. Corrija as substituições de método

Alguns `AppKit` métodos tiveram sua assinatura alterada para usar o novo [tipos nativos](~/cross-platform/macios/nativetypes.md) (como `nint`). Se esses métodos de substituição de subclasses personalizadas as assinaturas não irão corresponder e resultarão em erros. Corrigi essas substituições de método alterando a subclasse de acordo com a nova assinatura usando tipos nativos. 

## <a name="considerations"></a>Considerações

As seguintes considerações devem ser levadas em conta ao converter um projeto existente do Xamarin.Mac da API clássica para a nova API unificada se esse aplicativo depende de um ou mais componentes ou pacote do NuGet. 

### <a name="components"></a>Componentes

Qualquer componente que você incluiu em seu aplicativo também precisam ser atualizados para a API unificada ou você receberá um conflito ao tentar compilar. Para qualquer componente incluído, substituir a versão atual com uma nova versão do armazenamento de componente Xamarin que oferece suporte a API unificada e faça uma compilação limpa. Qualquer componente que ainda não foi convertido pelo autor, exibirá um aviso somente no repositório de componente de 32 bits.

### <a name="nuget-support"></a>Suporte do NuGet

Enquanto estamos contribuiu com alterações ao NuGet para funcionar com o suporte da API unificada, não houve uma nova versão do NuGet, portanto, estamos avaliando como obter NuGet reconheça as novas APIs. 

Até lá, assim como os componentes, você precisará alternar qualquer pacote do NuGet incluir em seu projeto para uma versão que oferece suporte a APIs unificado e faça uma compilação limpa posteriormente.

> [!IMPORTANT]
> **Observação:** se você tiver um erro no formato _"Erro 3 não pode incluir 'monomac.dll' e 'Xamarin.Mac.dll' no mesmo projeto Xamarin.Mac - 'Xamarin.Mac.dll' é referenciado explicitamente, enquanto a 'monomac.dll' é referenciado por ' xxx, Versão = 0.0.000, Culture = neutral, PublicKeyToken = null'"_ depois de converter seu aplicativo para as APIs unificado, normalmente ele é devido à necessidade de um componente ou um pacote do NuGet no projeto que não foi atualizado para a API unificada. Você precisará remover o componente/NuGet existente, atualize para uma versão que oferece suporte a APIs unificado e faça uma compilação limpa.

## <a name="enabling-64-bit-builds-of-xamarinmac-apps"></a>Habilitação de 64 bits compilações de Xamarin.Mac aplicativos

Para um aplicativo móvel de Xamarin.Mac foi convertido para a API unificada, o desenvolvedor precisa ainda permitem a criação do aplicativo para computadores de 64 bits de opções do aplicativo. Consulte o **habilitando 64 Bit compilações de Xamarin.Mac aplicativos de** do [considerações sobre a plataforma de 32/64 bits](~/cross-platform/macios/32-and-64/index.md) compilações do documento para obter instruções detalhadas sobre como habilitar 64 bits.
    
## <a name="finishing-up"></a>Concluindo a

Se você optar por usar o método manual ou automática para converter seu aplicativo Xamarin.Mac de clássica para as APIs unificado, ou não existem várias instâncias que serão mais, requerem intervenção manual. Confira nosso [dicas para atualizar o código para a API unificada](~/cross-platform/macios/unified/updating-tips.md) documentos para problemas conhecidos e soluções alternativas.

## <a name="related-links"></a>Links relacionados

- [Dicas para atualizar o código para a API unificada](~/cross-platform/macios/unified/updating-tips.md)
- [Trabalhar com tipos nativos em aplicativos de plataforma cruzada](~/cross-platform/macios/native-types-cross-platform.md)
- [Diferenças de API unificada do vs clássico](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
