---
title: Instalando a inserção do .NET
description: Este documento descreve como instalar a inserção do .NET. Ele aborda como executar as ferramentas manualmente, como gerar associações automaticamente, como usar destinos do MSBuild personalizados e as etapas de pós-compilação necessárias.
ms.prod: xamarin
ms.assetid: 47106AF3-AC6E-4A0E-B30B-9F73C116DDB3
author: chamons
ms.author: chhamo
ms.date: 04/18/2018
ms.openlocfilehash: 7da163e85b04791c276f9cb14f5b21615b7909fb
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/31/2019
ms.locfileid: "70200163"
---
# <a name="installing-net-embedding"></a>Instalando a inserção do .NET

## <a name="installing-net-embedding-from-nuget"></a>Instalando a incorporação .NET do NuGet

Escolha **adicionar > adicionar pacotes NuGet...** e instale o **Embeddinator-4000** do Gerenciador de pacotes NuGet:

![Gerenciador de pacotes do NuGet](images/visualstudionuget.png)

Isso instalará o **Embeddinator-4000. exe** e o **objcgen** no diretório **Packages/Embeddinator-4000/Tools** .

Em geral, a versão mais recente do Embeddinator-4000 deve ser selecionada para download. O suporte a Objective-C requer 0,4 ou posterior.

## <a name="running-manually"></a>Executando manualmente

Agora que o NuGet está instalado, você pode executar as ferramentas manualmente.

- Abrir um terminal (macOS) ou um prompt de comando (Windows)
- Altere o diretório para a raiz da solução
- As ferramentas são instaladas em:
    - **./packages/Embeddinator-4000.[VERSION]/tools/objcgen** (Objective-C)
    - **./packages/Embeddinator-4000.[VERSION]/tools/Embeddinator-4000.exe** (Java/C)
- No macOS, o **objcgen** pode ser executado diretamente.
- No Windows, o **Embeddinator-4000. exe** pode ser executado diretamente.
- No macOS, o **Embeddinator-4000. exe** precisa ser executado com o **mono**:
    - `mono ./packages/Embeddinator-4000.[VERSION]/tools/Embeddinator-4000.exe`

Cada invocação de comando precisará de vários parâmetros listados na documentação específica da plataforma.

## <a name="automatic-binding-generation"></a>Geração de associação automática

Há duas abordagens para executar automaticamente a incorporação .NET da parte do seu processo de compilação.

- Destinos do MSBuild personalizados
- Etapas de pós-compilação

Embora este documento Descreva ambos, é preferível usar um destino do MSBuild personalizado. As etapas pós-Build não serão necessariamente executadas ao criar a partir da linha de comando.

### <a name="custom-msbuild-targets"></a>Destinos do MSBuild personalizados

Para personalizar sua compilação com destinos do MSbuild, primeiro crie um arquivo **Embeddinator-4000. targets** próximo ao seu csproj semelhante ao seguinte:

```xml
<Project DefaultTargets="Build" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
    <Target Name="RunEmbeddinator" AfterTargets="AfterBuild" Inputs="$(OutputPath)/$(AssemblyName).dll" Outputs="$(IntermediateOutputPath)/Embeddinator/$(AssemblyName).framework/$(AssemblyName)">
        <Exec Command="" />
    </Target>
</Project>
```

Aqui, o texto do comando deve ser preenchido com uma das invocações de inserção do .NET listadas na documentação específica da plataforma.

Para usar este destino:

- Feche seu projeto no Visual Studio 2017 ou Visual Studio para Mac
- Abrir a biblioteca csproj em um editor de texto
- Adicione essa linha na parte inferior, acima da linha `</Project>` final:

```xml
 <Import Project="Embeddinator-4000.targets" />
```

- Reabra seu projeto

### <a name="post-build-steps"></a>Etapas de pós-compilação

As etapas para adicionar uma etapa de pós-compilação para executar a inserção do .NET variam de acordo com o IDE:

#### <a name="visual-studio-for-mac"></a>Visual Studio para Mac

Em Visual Studio para Mac, vá para **Opções de projeto > criar > comandos personalizados** e adicione uma etapa **após a compilação** .

Configure o comando listado na documentação específica da plataforma.

> [!NOTE]
> Certifique-se de usar o número de versão que você instalou do NuGet

Se você for fazer um desenvolvimento contínuo no C# projeto, também poderá adicionar um comando personalizado para limpar o diretório de **saída** antes de executar a inserção do .net:

```shell
rm -Rf '${SolutionDir}/output/'
```

![Ação de compilação personalizada](images/visualstudiocustombuild.png)

> [!NOTE]
> A associação gerada será colocada no diretório indicado pelo `--outdir` parâmetro ou. `--out` O nome de associação gerado pode variar, pois algumas plataformas têm limitações em nomes de pacote.

#### <a name="visual-studio-2017"></a>Visual Studio 2017

Essencialmente, definiremos a mesma coisa, mas os menus do Visual Studio 2017 são um pouco diferentes. Os comandos do Shell também são ligeiramente diferentes.

Vá para **Opções de projeto > eventos de compilação** e insira o comando listado na documentação específica da plataforma na caixa de **linha de comando de evento de pós-compilação** . Por exemplo:

![Inserção .NET no Windows](images/visualstudiowindows.png)
 