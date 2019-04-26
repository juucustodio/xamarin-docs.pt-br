---
title: Instalando a incorporação do .NET
description: Este documento descreve como instalar a incorporação do .NET. Ele discute como executar a ferramenta manualmente, como associações de gerar automaticamente, como usar destinos personalizados do MSBuild e etapas necessárias de pós-compilação.
ms.prod: xamarin
ms.assetid: 47106AF3-AC6E-4A0E-B30B-9F73C116DDB3
author: chamons
ms.author: chhamo
ms.date: 04/18/2018
ms.openlocfilehash: 2a572748c21d2a640add3346d1162f4b6bdc8e99
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61076561"
---
# <a name="installing-net-embedding"></a>Instalando a incorporação do .NET

## <a name="installing-net-embedding-from-nuget"></a>Instalando o .NET incorporação do NuGet

Escolha **Adicionar > Adicionar pacotes NuGet...**  e instale **Embeddinator 4000** do Gerenciador de pacotes NuGet:

![Gerenciador de pacotes NuGet](images/visualstudionuget.png)

Isso instalará **Embeddinator 4000.exe** e **objcgen** para o **pacotes/Embeddinator-4000/tools** directory.

Em geral, a versão mais recente do Embeddinator-4000 deve ser selecionada para download. Suporte de Objective-C requer 0.4 ou posterior.

## <a name="running-manually"></a>Execução manual

Agora que o NuGet está instalado, você pode executar a ferramenta manualmente.

- Abra um Terminal (macOS) ou o Prompt de comando (Windows)
- Altere o diretório para sua solução raiz
- As ferramentas é instalada em:
    - **./packages/Embeddinator-4000.[VERSION]/tools/objcgen** (Objective-C)
    - **./packages/Embeddinator-4000.[VERSION]/tools/Embeddinator-4000.exe** (Java/C)
- No macOS **objcgen** podem ser executados diretamente.
- No Windows, **Embeddinator 4000.exe** podem ser executados diretamente.
- No macOS **Embeddinator 4000.exe** precisa ser executado com **mono**:
    - `mono ./packages/Embeddinator-4000.[VERSION]/tools/Embeddinator-4000.exe`

Cada invocação de comando será necessário um número de parâmetros listados na documentação específica da plataforma.

## <a name="automatic-binding-generation"></a>Geração de associação automática

Há duas abordagens para execução automática de incorporação de .NET parte do processo de compilação.

- Destinos personalizados do MSBuild
- Etapas pós-compilação

Embora este documento descreverá ambos, usar um destino MSBuild personalizado é preferencial. Etapas de compilação de POST não será necessariamente executado ao compilar na linha de comando.

### <a name="custom-msbuild-targets"></a>Destinos personalizados do MSBuild

Para personalizar sua compilação com destinos do MSbuild, primeiro crie uma **Embeddinator 4000.targets** arquivo ao lado de seu csproj semelhante à seguinte:

```xml
<Project DefaultTargets="Build" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
    <Target Name="RunEmbeddinator" AfterTargets="AfterBuild" Inputs="$(OutputPath)/$(AssemblyName).dll" Outputs="$(IntermediateOutputPath)/Embeddinator/$(AssemblyName).framework/$(AssemblyName)">
        <Exec Command="" />
    </Target>
</Project>
```

Aqui, o texto do comando deve ser preenchido com uma das invocações de incorporação de .NET listadas na documentação específica da plataforma.

Para usar esse destino:

- Feche o seu projeto no Visual Studio 2017 ou Visual Studio para Mac
- Abra a biblioteca csproj em um editor de texto
- Adicione esta linha na parte inferior, acima o último `</Project>` linha:

```xml
 <Import Project="Embeddinator-4000.targets" />
```

- Reabrir o projeto

### <a name="post-build-steps"></a>Etapas pós-compilação

As etapas para adicionar uma etapa de pós-compilação para executar a inserção de .NET variam dependendo do IDE:

#### <a name="visual-studio-for-mac"></a>Visual Studio para Mac

No Visual Studio para Mac, acesse **opções de projeto > Build > comandos personalizados** e adicione um **depois de criar** etapa.

Configure o comando listado na documentação específica da plataforma.

> [!NOTE]
> Certifique-se de usar o número da versão instalada do NuGet

Se você pretende fazer desenvolvimento contínuo sobre o C# projeto, você também pode adicionar um comando personalizado para limpar o **saída** diretório antes de executar a incorporação do .NET:

```shell
rm -Rf '${SolutionDir}/output/'
```

![Ação de compilação personalizada](images/visualstudiocustombuild.png)

> [!NOTE]
> A associação gerada será colocada no diretório indicado pela `--outdir` ou `--out` parâmetro. O nome de associação gerada pode variar conforme alguns plataformas têm limitações nos nomes de pacote.

#### <a name="visual-studio-2017"></a>Visual Studio 2017

Essencialmente, definiremos a mesma coisa, mas os menus no Visual Studio 2017 são um pouco diferentes. Os comandos do shell também são ligeiramente diferentes.

Vá para **opções de projeto > eventos de Build** e digite o comando listado na documentação específica da plataforma para o **linha de comando do evento de pós-compilação** caixa. Por exemplo:

![Inserindo em Windows do .NET](images/visualstudiowindows.png)
