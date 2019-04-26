---
title: Atualizando aplicativos existentes do Mac
description: Este documento descreve as etapas que devem ser seguidas para atualizar um aplicativo xamarin. MAC da API clássica para a API unificada.
ms.prod: xamarin
ms.assetid: 26673CC5-C1E5-4BAC-BEF4-9A386B296FD5
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 5e6034b079bba5e884872e4f2096d677fd3641d0
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61213478"
---
# <a name="updating-existing-mac-apps"></a>Atualizando aplicativos existentes do Mac

Atualizando um aplicativo existente para usar a API unificada requer alterações no arquivo de projeto em si, bem como para os namespaces e as APIs usadas no código do aplicativo.

## <a name="the-road-to-64-bits"></a>O caminho para a 64 Bits

As novas APIs de Unificação são necessários para dar suporte a arquiteturas de dispositivo de 64 bits de um aplicativo xamarin. Mac. A partir de 1º de fevereiro de 2015 a Apple requer que todos os novos envios de aplicativo para a Mac App Store dá suporte a arquiteturas de 64 bits.

O Xamarin fornece ferramentas para Visual Studio para Mac e Visual Studio automatizar o processo de migração da API clássica para a API unificada, ou você pode converter os arquivos de projeto manualmente. Enquanto o usando as ferramentas automático é altamente recomendável, este artigo abordará os dois métodos.

### <a name="before-you-start"></a>Antes de começar...

Antes de atualizar seu código existente para a API unificada, é altamente recomendável que você elimine todos *avisos de compilação*. Muitas *avisos* na API do clássico se tornará erros depois de migrar para a Unificação. Corrigi-los antes de começar é mais fácil, porque as mensagens do compilador da API clássica geralmente fornecem dicas sobre o que atualizar.

## <a name="automated-updating"></a>Automatizada de atualização

Depois que foram corrigidos os avisos, selecione um projeto existente do Mac no Visual Studio para Mac ou Visual Studio e escolha **migrar para a API unificada do xamarin. Mac** da **projeto** menu. Por exemplo:

![](updating-mac-apps-images/beta-tool1.png "Escolha as migrações para a API unificada do xamarin. Mac no menu projeto")

Será necessário concordar com este aviso antes de ser executado a migração automatizada (Obviamente, certifique-se você tem controle de origem/backups antes de embarcar nesse adventure):

![](updating-mac-apps-images/migrate01.png "Aceitar esse aviso antes da migração automatizada será executado")

Há dois tipos de estrutura de destino com suporte que podem ser selecionados ao usar a API unificada em um aplicativo xamarin. Mac:

- **Estrutura do xamarin. Mac Mobile** -este é o mesmo .NET framework ajustado usado por xamarin. IOS e xamarin. Android que dão suporte a um subconjunto de todo **Desktop** framework. Esta é a estrutura de recomendado porque fornece binários médios menores devido ao comportamento de vinculação superior.
- **Xamarin. Mac .NET 4.5 Framework** -essa estrutura é mais uma vez, um subconjunto do **Desktop** framework. No entanto, ele corta muito menos completo **área de trabalho** framework que o **Mobile** framework e deve _"apenas funcionam"_ com a maioria dos pacotes do NuGet ou 3ª bibliotecas de terceiros. Isso permite que o desenvolvedor consumir standard **Desktop** assemblies enquanto ainda usando uma estrutura com suporte, mas essa opção produz pacotes de aplicativos maiores. Esta é a estrutura recomendada onde 3ª assemblies do .NET de terceiros estão sendo usados, não são compatíveis com o **estrutura do xamarin. Mac Mobile**. Para obter uma lista de assemblies com suporte, consulte nosso [Assemblies](~/cross-platform/internals/available-assemblies.md) documentação.

Para obter informações detalhadas sobre estruturas de destino e as implicações da seleção de um destino específico para seu aplicativo xamarin. Mac, consulte nosso [estruturas de destino](~/mac/platform/target-framework.md) documentação. 

A ferramenta basicamente automatiza todas as etapas descritas a **atualização manualmente** seção apresentada abaixo e é o método sugerido de conversão de um projeto existente do xamarin. Mac para a API unificada.

## <a name="steps-to-update-manually"></a>Etapas para atualizar manualmente

Novamente, depois que foram corrigidos os avisos, siga estas etapas para atualizar manualmente os aplicativos xamarin. Mac para usar a nova API unificada:

### <a name="1-update-project-type--build-target"></a>1. Tipo de projeto de atualização & destino de compilação

Alterar o tipo de projeto em seu **csproj** arquivos de `42C0BBD9-55CE-4FC1-8D90-A7348ABAFB23` para `A3F8F2AB-B479-4A4A-A458-A89E7DC349F1`. Editar o **csproj** em um editor de texto, substituindo o primeiro item no `<ProjectTypeGuids>` elemento, conforme mostrado:

![](updating-mac-apps-images/csproj.png "Edite o arquivo csproj em um editor de texto, substituindo o primeiro item no elemento ProjectTypeGuids, conforme mostrado")

Alterar o **importação** elemento que contém `Xamarin.Mac.targets` para `Xamarin.Mac.CSharp.targets` conforme mostrado:

![](updating-mac-apps-images/csproj2.png "Altere o elemento de importação que contém Xamarin.Mac.targets para Xamarin.Mac.CSharp.targets, conforme mostrado")

Adicione as seguintes linhas de código após o `<AssemblyName>` elemento:

```xml
<TargetFrameworkVersion>v2.0</TargetFrameworkVersion>
<TargetFrameworkIdentifier>Xamarin.Mac</TargetFrameworkIdentifier>

```

Exemplo:

![](updating-mac-apps-images/csproj3.png "Adicione estas linhas de código após o elemento < AssemblyName >")

### <a name="2-update-project-references"></a>2. Atualizar referências de projeto

Expanda o projeto de aplicativo do Mac **referências** nó. Mostrarão inicialmente um * quebrada - **XamMac** referência semelhante a esta captura de tela (como acabamos de alterar o tipo de projeto):

![](updating-mac-apps-images/references.png "Ele mostrarão inicialmente uma referência XamMac quebrado semelhante a esta captura de tela")

Clique o **ícone de engrenagem** ao lado de **XamMac** entrada e selecione **excluir** para remover a referência desfeita.

Em seguida, clique duas vezes no **referências** pasta o **Gerenciador de soluções** e selecione **Editar referências**. Role até a parte inferior da lista de referências e colocar uma marca de seleção além **xamarin. Mac**.

![](updating-mac-apps-images/references2.png "Role até a parte inferior da lista de referências e colocar uma marca de seleção, além de xamarin. Mac")

Pressione **Okey** para salvar alterações de referências de projeto.

### <a name="3-remove-monomac-from-namespaces"></a>3. Remover o MonoMac de Namespaces

Remover o **MonoMac** prefixo de namespaces no `using` instruções ou onde quer que um nome de classe foi totalmente qualificado (por exemplo. `MonoMac.AppKit` torna-se apenas `AppKit`).

### <a name="4-remap-types"></a>4. Remapear tipos

[Tipos nativos](~/cross-platform/macios/nativetypes.md) ter sido introduzido que substitua alguns tipos que foram usados anteriormente, como instâncias do `System.Drawing.RectangleF` com `CoreGraphics.CGRect` (por exemplo). A lista completa de tipos pode ser encontrada na [tipos nativos](~/cross-platform/macios/nativetypes.md) página.

### <a name="5-fix-method-overrides"></a>5. Corrija as substituições de método

Alguns `AppKit` métodos tiveram sua assinatura alterada para usar o novo [tipos nativos](~/cross-platform/macios/nativetypes.md) (como `nint`). Se a subclasses personalizadas substituem esses métodos, as assinaturas não corresponderão mais e resultarão em erros. Corrigi essas substituições de método alterando a subclasse de acordo com a nova assinatura, usando tipos nativos. 

## <a name="considerations"></a>Considerações

As seguintes considerações devem ser levadas em conta ao converter um projeto existente do xamarin. MAC da API clássica para a nova API unificada se baseia-se de que o aplicativo em um ou mais componente ou pacote do NuGet. 

### <a name="components"></a>Componentes

Qualquer componente que você incluiu em seu aplicativo também precisam ser atualizados para a API unificada, ou você receberá um conflito ao tentar compilar. Para qualquer componente incluído, substituir a versão atual com uma nova versão de Store de componente do Xamarin que dá suporte a API unificada e fazer uma compilação limpa. Qualquer componente que ainda não foi convertido pelo autor, exibirá um aviso apenas no repositório de componente de 32 bits.

### <a name="nuget-support"></a>Suporte do NuGet

Enquanto estamos contribuiu com as alterações no NuGet para trabalhar com o suporte de API unificada, não houve uma nova versão do NuGet, portanto, estamos avaliando como obter o NuGet para reconhecer as novas APIs. 

Até esse momento, assim como os componentes, você precisará alternar qualquer pacote NuGet que você incluiu em seu projeto para uma versão que dá suporte a APIs unificadas e fazer uma compilação limpa posteriormente.

> [!IMPORTANT]
> Se você tiver um erro no formulário _"Erro 3 não pode incluir 'monomac.dll' e 'Xamarin' no mesmo projeto xamarin. Mac – 'Xamarin' é referenciada explicitamente, enquanto 'monomac.dll' é referenciado por ' xxx, versão = 0.0.000, Culture = neutral, PublicKeyToken = null'"_ após converter seu aplicativo para as APIs unificadas, ela normalmente é devido a ter um componente ou o pacote do NuGet no projeto que não foi atualizado para a API unificada. Você precisará remover o componente/NuGet existente, atualize para uma versão que dá suporte a APIs unificadas e fazer uma compilação limpa.

## <a name="enabling-64-bit-builds-of-xamarinmac-apps"></a>Habilitação de 64 bits compilações de aplicativos xamarin. Mac

Para um aplicativo móvel de xamarin. MAC que foi convertido para a API unificada, o desenvolvedor precisa ainda permitem a criação do aplicativo para computadores de 64 bits de opções do aplicativo. Consulte a **habilitando bits de 64 compilações de aplicativos xamarin. Mac** da [considerações sobre plataformas de 32/64 bits](~/cross-platform/macios/32-and-64/index.md) compilações de documento para obter instruções detalhadas sobre como habilitar a 64 bits.
    
## <a name="finishing-up"></a>Finalizando

Se você optar por usar o método manual ou automático para converter seu aplicativo xamarin. MAC do clássico para as APIs de Unificação, há várias instâncias que serão ainda mais, requerem intervenção manual. Consulte nosso [dicas para atualizar o código para a API unificada](~/cross-platform/macios/unified/updating-tips.md) de documento para os problemas conhecidos e soluções alternativas.

## <a name="related-links"></a>Links relacionados

- [Dicas para atualizar o código para a API unificada](~/cross-platform/macios/unified/updating-tips.md)
- [Trabalhar com tipos nativos em aplicativos de plataforma cruzada](~/cross-platform/macios/native-types-cross-platform.md)
- [Diferenças de API unificada do vs clássico](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
