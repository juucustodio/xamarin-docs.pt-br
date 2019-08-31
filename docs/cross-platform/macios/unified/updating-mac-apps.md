---
title: Atualizando aplicativos Mac existentes
description: Este documento descreve as etapas que devem ser seguidas para atualizar um aplicativo Xamarin. Mac da API Clássica para a API Unificada.
ms.prod: xamarin
ms.assetid: 26673CC5-C1E5-4BAC-BEF4-9A386B296FD5
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: a6d245e60a0d163c054fd74e0fb64780225da031
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/31/2019
ms.locfileid: "70197813"
---
# <a name="updating-existing-mac-apps"></a>Atualizando aplicativos Mac existentes

Atualizar um aplicativo existente para usar a API Unificada requer alterações no próprio arquivo do projeto, bem como nos namespaces e APIs usados no código do aplicativo.

## <a name="the-road-to-64-bits"></a>A estrada para 64 bits

As novas APIs unificadas são necessárias para dar suporte a arquiteturas de dispositivo de 64 bits de um aplicativo Xamarin. Mac. A partir de 1º de fevereiro, a Apple 2015 requer que todos os novos envios de aplicativos para a Mac App Store ofereçam suporte a arquiteturas de 64 bits.

O Xamarin fornece ferramentas para o Visual Studio para Mac e o Visual Studio para automatizar o processo de migração do API Clássica para o API Unificada ou você pode converter os arquivos de projeto manualmente. Embora o uso das ferramentas automáticas seja altamente sugerido, este artigo abordará os dois métodos.

### <a name="before-you-start"></a>Antes de começar...

Antes de atualizar seu código existente para o API Unificada, é altamente recomendável que você elimine todos os *avisos de compilação*. Muitos *avisos* na API clássica se tornarão erros depois que você migrar para o Unified. Corrigi-los antes de começar é mais fácil porque as mensagens do compilador da API Clássica geralmente fornecem dicas sobre o que atualizar.

## <a name="automated-updating"></a>Atualização automatizada

Depois que os avisos forem corrigidos, selecione um projeto de Mac existente no Visual Studio para Mac ou no Visual Studio e escolha **migrar para o Xamarin. Mac API unificada** no menu **projeto** . Por exemplo:

![](updating-mac-apps-images/beta-tool1.png "Escolha migrar para o Xamarin. Mac API Unificada no menu do projeto")

Você precisará concordar com este aviso antes que a migração automatizada seja executada (obviamente, você deve garantir que tenha backup/controle do código-fonte antes de embarcar nesta aventura):

![](updating-mac-apps-images/migrate01.png "Concordar com este aviso antes que a migração automatizada seja executada")

Há dois tipos de estrutura de destino com suporte que podem ser selecionados ao usar o API Unificada em um aplicativo Xamarin. Mac:

- **Xamarin. Mac Mobile Framework** -é o mesmo .NET Framework ajustado usado pelo Xamarin. Ios e Xamarin. Android que dá suporte a um subconjunto da estrutura de **área de trabalho** completa. Esta é a estrutura recomendada porque fornece binários de média menores devido ao comportamento de vinculação superior.
- **Xamarin. Mac .net 4,5 Framework** -essa estrutura é novamente, um subconjunto da estrutura da **área de trabalho** . No entanto, ele corta muito menos da estrutura de **área de trabalho** completa do que a estrutura **móvel** e deve _"simplesmente funcionar"_ com a maioria dos pacotes NuGet ou bibliotecas de terceiros. Isso permite que o desenvolvedor consuma assemblies de **Desktop** padrão enquanto ainda usa uma estrutura com suporte, mas essa opção produz pacotes de aplicativos maiores. Esta é a estrutura recomendada na qual os assemblies .NET de terceiros estão sendo usados e não são compatíveis com o **Xamarin. Mac Mobile Framework**. Para obter uma lista de assemblies com suporte, consulte a documentação de nossos [assemblies](~/cross-platform/internals/available-assemblies.md) .

Para obter informações detalhadas sobre estruturas de destino e as implicações de selecionar um destino específico para seu aplicativo Xamarin. Mac, consulte nossa documentação de [estruturas de destino](~/mac/platform/target-framework.md) . 

Basicamente, a ferramenta automatiza todas as etapas descritas na seção **atualização manual** apresentada abaixo e é o método sugerido para converter um projeto Xamarin. Mac existente no API unificada.

## <a name="steps-to-update-manually"></a>Etapas para atualizar manualmente

Novamente, depois que os avisos forem corrigidos, siga estas etapas para atualizar manualmente os aplicativos do Xamarin. Mac para usar o novo API Unificada:

### <a name="1-update-project-type--build-target"></a>1. Atualizar o tipo de projeto & destino da compilação

Altere o tipo de projeto em seus arquivos csproj `42C0BBD9-55CE-4FC1-8D90-A7348ABAFB23` de `A3F8F2AB-B479-4A4A-A458-A89E7DC349F1`para. Edite o arquivo **csproj** em um editor de texto, substituindo o primeiro `<ProjectTypeGuids>` item no elemento, conforme mostrado:

![](updating-mac-apps-images/csproj.png "Edite o arquivo csproj em um editor de texto, substituindo o primeiro item no elemento ProjectTypeGuids, conforme mostrado")

Altere o elemento de **importação** que `Xamarin.Mac.targets` contém `Xamarin.Mac.CSharp.targets` para, conforme mostrado:

![](updating-mac-apps-images/csproj2.png "Altere o elemento Import que contém Xamarin. Mac. targets para Xamarin. Mac. CSharp. targets, conforme mostrado")

Adicione as seguintes linhas de código após o `<AssemblyName>` elemento:

```xml
<TargetFrameworkVersion>v2.0</TargetFrameworkVersion>
<TargetFrameworkIdentifier>Xamarin.Mac</TargetFrameworkIdentifier>

```

Exemplo:

![Adicione estas linhas de código após o \<elemento > AssemblyName](updating-mac-apps-images/csproj3.png)

### <a name="2-update-project-references"></a>2. Atualizar referências do projeto

Expanda o nó **referências** do projeto de aplicativo Mac. Inicialmente, ele mostrará uma referência de * **XamMac** , semelhante a esta captura de tela (porque acabamos de alterar o tipo de projeto):

![](updating-mac-apps-images/references.png "Inicialmente, ele mostrará uma referência XamMac, semelhante a esta captura de tela")

Clique no **ícone de engrenagem** ao lado da entrada **XamMac** e selecione **excluir** para remover a referência quebrada.

Em seguida, clique com o botão direito do mouse na pasta **referências** no **Gerenciador de soluções** e selecione **Editar referências**. Role até a parte inferior da lista de referências e faça uma verificação Além do **Xamarin. Mac**.

![](updating-mac-apps-images/references2.png "Role até a parte inferior da lista de referências e faça uma verificação Além do Xamarin. Mac")

Pressione **OK** para salvar as alterações de referências do projeto.

### <a name="3-remove-monomac-from-namespaces"></a>3. Remover MonoMac dos namespaces

Remova o prefixo **MonoMac** dos namespaces em `using` instruções ou sempre que um ClassName tiver sido totalmente qualificado (por exemplo, `MonoMac.AppKit`se torna `AppKit`apenas).

### <a name="4-remap-types"></a>4. Tipos de remapeamento

Foram introduzidos [tipos nativos](~/cross-platform/macios/nativetypes.md) que substituem alguns tipos que foram usados anteriormente, como instâncias de `System.Drawing.RectangleF` with `CoreGraphics.CGRect` (por exemplo). A lista completa de tipos pode ser encontrada na página [tipos nativos](~/cross-platform/macios/nativetypes.md) .

### <a name="5-fix-method-overrides"></a>5. Corrigir substituições de método

Alguns `AppKit` métodos tiveram sua assinatura alterada para usar os novos [tipos nativos](~/cross-platform/macios/nativetypes.md) ( `nint`como). Se as subclasses personalizadas substituirem esses métodos, as assinaturas não corresponderão mais e resultarão em erros. Corrija essas substituições de método alterando a subclasse para corresponder à nova assinatura usando tipos nativos. 

## <a name="considerations"></a>Considerações

As considerações a seguir devem ser levadas em conta ao converter um projeto Xamarin. Mac existente do API Clássica para o novo API Unificada se esse aplicativo depender de um ou mais componentes ou pacote NuGet. 

### <a name="components"></a>Componentes

Qualquer componente que você tenha incluído em seu aplicativo também precisará ser atualizado para o API Unificada ou você receberá um conflito quando tentar compilar. Para qualquer componente incluído, substitua a versão atual por uma nova versão da loja de componentes do Xamarin que dá suporte à API Unificada e faça uma compilação limpa. Qualquer componente que ainda não tenha sido convertido pelo autor, exibirá um aviso de 32 bits apenas no repositório de componentes.

### <a name="nuget-support"></a>Suporte do NuGet

Embora tenhamos contribuído com alterações no NuGet para trabalhar com o suporte de API Unificada, não houve uma nova versão do NuGet, portanto, estamos avaliando como obter o NuGet para reconhecer as novas APIs. 

Até esse momento, assim como os componentes, você precisará alternar qualquer pacote NuGet que tenha incluído em seu projeto para uma versão que dê suporte às APIs unificadas e faça uma compilação limpa posteriormente.

> [!IMPORTANT]
> Se você tiver um erro no formato _"erro 3 não pode incluir ' monomac. dll ' e ' Xamarin. Mac. dll ' no mesmo projeto Xamarin. Mac-' xamarin. Mac. dll ' é referenciado explicitamente, enquanto ' monomac. dll ' é referenciado por ' xxx, Version = 0.0.000, Culture = neutral, PublicKeyToken = null ' "_ depois de converter seu aplicativo para as APIs unificadas, normalmente, é devido à existência de um componente ou pacote NuGet no projeto que não foi atualizado para o API unificada. Você precisará remover o componente/NuGet existente, atualizar para uma versão que ofereça suporte a APIs unificadas e fazer uma compilação limpa.

## <a name="enabling-64-bit-builds-of-xamarinmac-apps"></a>Habilitando Builds de 64 bits de aplicativos Xamarin. Mac

Para um aplicativo Xamarin. Mac Mobile que foi convertido no API Unificada, o desenvolvedor ainda precisa habilitar a criação do aplicativo para computadores de 64 bits a partir das opções do aplicativo. Consulte a seção **habilitando as compilações de 64 bits de aplicativos Xamarin. Mac** do documento [Considerações sobre plataforma de 32/64 bits](~/cross-platform/macios/32-and-64/index.md) para obter instruções detalhadas sobre como habilitar as compilações de 64 bits.

## <a name="finishing-up"></a>Concluindo

Se você optar por usar ou não o método automático ou manual para converter seu aplicativo Xamarin. Mac do clássico para as APIs unificadas, haverá várias instâncias que exigirão mais intervenção manual. Confira nossas [dicas para atualizar o código para o documento API unificada](~/cross-platform/macios/unified/updating-tips.md) para problemas conhecidos e contornar.

## <a name="related-links"></a>Links relacionados

- [Dicas para atualizar o código para a API unificada](~/cross-platform/macios/unified/updating-tips.md)
- [Trabalhar com tipos nativos em aplicativos de plataforma cruzada](~/cross-platform/macios/native-types-cross-platform.md)
- [Diferenças clássicas do vs API Unificada](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md)
