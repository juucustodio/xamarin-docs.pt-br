---
title: Instalando a incorporação de .NET
description: Este documento descreve como instalar a incorporação de .NET. Ele discute como executar a ferramenta manualmente, como associações de gerar automaticamente, como usar personalizados destinos do MSBuild e etapas necessárias de pós-compilação.
ms.prod: xamarin
ms.assetid: 47106AF3-AC6E-4A0E-B30B-9F73C116DDB3
author: chamons
ms.author: chhamo
ms.date: 4/18/2018
ms.openlocfilehash: 057a1f3f662b2dbe2f8aee277505e1d6e8798084
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34793789"
---
# <a name="installing-net-embedding"></a>Instalando a incorporação de .NET

## <a name="installing-net-embedding-from-nuget"></a>Instalando o .NET incorporação do NuGet

Escolha **Adicionar > adicione pacotes NuGet...**  e instalar **Embeddinator 4000** do NuGet package manager:

![Gerenciador de pacotes do NuGet](images/visualstudionuget.png)

Isso irá instalar **Embeddinator 4000.exe** e **objcgen** para o **pacotes/Embeddinator-4000/ferramentas** directory.

Em geral, a versão mais recente do Embeddinator-4000 deve ser selecionada para download. Suporte de Objective-C requer 0,4 ou posterior.

## <a name="running-manually"></a>Executando manualmente

Agora que o NuGet está instalado, você pode executar a ferramenta manualmente.

- Abra um Prompt de comando (Windows) ou de Terminal (macOS)
- Altere o diretório para a raiz da solução
- A ferramenta é instalada em:
    - **. / Embeddinator/pacotes-4000. [Versão] / ferramentas/objcgen** (Objective-C)
    - **. / Embeddinator/pacotes-4000. [VERSION]/tools/Embeddinator-4000.exe** (Java/C) 
- Em macOS, **objcgen** podem ser executados diretamente. 
- No Windows, **Embeddinator 4000.exe** podem ser executados diretamente.
- Em macOS, **Embeddinator 4000.exe** precisa ser executado com **mono**: 
    - `mono ./packages/Embeddinator-4000.[VERSION]/tools/Embeddinator-4000.exe`

Cada invocação de comando será necessário um número de parâmetros listados na documentação da plataforma específica.

## <a name="automatic-binding-generation"></a>Geração automática de associação

Há duas abordagens para execução automática de .NET inserir parte do processo de compilação.

- Destinos do MSBuild personalizados
- Etapas de pós-compilação

Embora este documento descreverá ambos, usar um destino personalizado do MSBuild é preferido. Etapas de compilação de postagem não será necessariamente executado ao compilar na linha de comando.

### <a name="custom-msbuild-targets"></a>Destinos do MSBuild personalizados

Para personalizar sua compilação com destinos do MSbuild, primeiro crie um **Embeddinator 4000.targets** arquivo próximo seu csproj semelhante à seguinte:

```xml
<Project DefaultTargets="Build" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
    <Target Name="RunEmbeddinator" AfterTargets="AfterBuild" Inputs="$(OutputPath)/$(AssemblyName).dll" Outputs="$(IntermediateOutputPath)/Embeddinator/$(AssemblyName).framework/$(AssemblyName)">
        <Exec Command="" />
    </Target>
</Project>
```

Aqui, o texto do comando deve ser preenchido com uma das invocações de .NET inserindo listadas na documentação específica de plataforma.

Para usar este destino:

- Feche o seu projeto no Visual Studio de 2017 ou o Visual Studio para Mac
- Abra a biblioteca csproj em um editor de texto
- Adicione esta linha na parte inferior, acima o último `</Project>` linha:

```xml
 <Import Project="Embeddinator-4000.targets" />
```

- Abra seu projeto

### <a name="post-build-steps"></a>Etapas de pós-compilação

As etapas para adicionar uma etapa de pós-compilação para executar a inserção de .NET variam dependendo do IDE:

#### <a name="visual-studio-for-mac"></a>Visual Studio para Mac

No Visual Studio para Mac, vá para **opções de projeto > compilar > comandos do personalizada** e adicione um **depois de criar** etapa.

Configure o comando listado na documentação da plataforma específica.

> [!NOTE]
> Certifique-se de usar o número da versão instalada do NuGet

Se você pretende fazer desenvolvimento contínuo do projeto c#, você também pode adicionar um comando personalizado para limpar o **saída** diretório antes de executar a inserção do .NET:

```shell
rm -Rf '${SolutionDir}/output/'
```

![Ação de compilação personalizada](images/visualstudiocustombuild.png)

> [!NOTE]
> A associação gerada será colocada no diretório indicado pelo `--outdir` ou `--out` parâmetro. O nome da associação gerado pode variar conforme algumas plataformas têm limitações nos nomes de pacote.

#### <a name="visual-studio-2017"></a>Visual Studio 2017

Essencialmente definiremos o mesmo significado, mas os menus no Visual Studio de 2017 são um pouco diferentes. Os comandos do shell também são um pouco diferentes.

Vá para **opções de projeto > eventos de Build** e digite o comando listado na documentação específica de plataforma para o **linha de comando do evento de pós-compilação** caixa. Por exemplo:

![Inserindo em Windows de .NET](images/visualstudiowindows.png)
