---
title: Como copiar arquivos de saída de IPA à pasta-depósito TFS?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: B0F1E09E-7315-45BA-B7FF-44D2063EE19C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 087a20ea3b573595e6cbd2b40d77de649676391e
ms.sourcegitcommit: dc882e9631b4ed52596b944a6fbbdde309346943
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/26/2018
ms.locfileid: "31883703"
---
# <a name="how-can-i-copy-ipa-output-files-to-the-tfs-drop-folder"></a>Como copiar arquivos de saída de IPA à pasta-depósito TFS?

Abra o `.csproj` de arquivos para o projeto de aplicativo do iOS em um editor de texto e, em seguida, adicione as seguintes linhas ao final (imediatamente antes do fechamento `</Project>` marca):

```xml
<PropertyGroup>
    <CreateIpaDependsOn>
        $(CreateIpaDependsOn);
        CopyIpa
    </CreateIpaDependsOn>
</PropertyGroup>

<Target Name="CopyIpa"
    Condition="'$(OutputType)' == 'Exe'
        And '$(ComputedPlatform)' == 'iPhone'
        And '$(BuildIpa)' == 'true'
        And '$(TF_BUILD)' == 'true'">
    <Copy
        SourceFiles="$(OutputPath)$(IpaPackageName)"
        DestinationFolder="$(TF_BUILD_BINARIESDIRECTORY)"
    />
</Target>
```

## <a name="notes"></a>Observações

-   Isso é a mesma técnica geral discutida em [pode alterar o caminho de saída do arquivo IPA?](~/ios/troubleshooting/questions/ipa-output-path.md). Os dois pontos importantes são definir `$(TF_BUILD_BINARIESDIRECTORY)` como a pasta de destino e para adicionar uma condição adicional para `CopyIpa` só será executada para compilações do TFS.

-   Para obter uma descrição de `TF_BUILD_BINARIESDIRECTORY` consulte [ https://msdn.microsoft.com/library/hh850448.aspx ](https://msdn.microsoft.com/library/hh850448.aspx).

## <a name="additional-references"></a>Referências adicionais

- [Documentação sobre a instalação do TFS para uso com o Xamarin](https://docs.microsoft.com/vsts/tfvc/overview)
- [Tarefa de compilação do TFS: xamarin](https://docs.microsoft.com/vsts/build-release/tasks/build/xamarin-android)
- [Tarefa de compilação do TFS: xamarin](https://docs.microsoft.com/vsts/build-release/tasks/build/xamarin-ios)

### <a name="next-steps"></a>Próximas etapas
Este documento aborda o comportamento atual a partir de Xamarin 3.11.666 para Visual Studio e host de build xamarin 8.10.3 no Mac. Para obter mais assistência, entre em contato conosco ou se esse problema permanece mesmo após utilizando as informações acima, consulte [quais opções de suporte estão disponíveis para Xamarin?](~/cross-platform/troubleshooting/support-options.md) para obter informações sobre opções de contato, sugestões, bem como a registrar um bug de novo, se necessário. 



