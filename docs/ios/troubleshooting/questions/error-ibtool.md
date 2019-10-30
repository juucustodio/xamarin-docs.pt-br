---
title: 'Erro de IBTool: não foi possível concluir a operação.'
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: A804EBC4-2BBF-4A98-A4E8-A455DB2E8A17
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/03/2018
ms.openlocfilehash: 4ff7b88ad63870246acbf2b29d01775f6711a8ce
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031184"
---
# <a name="ibtool-error-the-operation-couldnt-be-completed"></a>Erro de IBTool: não foi possível concluir a operação.

## <a name="fixed-in-xcode-611"></a>Corrigido no Xcode 6.1.1

A Apple [corrigiu](https://developer.apple.com/library/content/documentation/Xcode/Conceptual/RN-Xcode-Archive/Chapters/xc6_release_notes.html#//apple_ref/doc/uid/TP40016994-CH4-SW1) esse `ibtool` bug no Xcode 6.1.1, portanto, a atualização para o Xcode 6.1.1 ou superior é a correção mais fácil.

* * *

## <a name="description-of-the-problem"></a>Descrição do problema

O comando `ibtool` no Xcode 6,0 tinha um bug no OS X 10,10 Yosemite. O Xamarin. iOS usa o `ibtool` do Xcode para compilar storyboards e arquivos de `XIB`.

Mais informações sobre o bug em relação ao Xcode podem ser encontradas no seguinte Stack Overflow post: [https://stackoverflow.com/questions/25754763/cant-open-storyboard](https://stackoverflow.com/questions/25754763/cant-open-storyboard)

### <a name="error-message"></a>Mensagem de erro

> Não foi possível abrir o documento "MainStoryboard. Storyboard". Não foi possível concluir a operação. (erro com. Apple. InterfaceBuilder-1.)

## <a name="workarounds-for-xcode-60"></a>Soluções alternativas (para o Xcode 6,0)

### <a name="option-1-manage-all-uiimageviewimage-properties-in-code"></a>Opção 1: gerenciar todas as propriedades de `UIImageView.Image` no código

Em vez de definir a propriedade `Image` de um `UIImageView` no arquivo de storyboard ou `.xib`, você pode definir a propriedade em um dos métodos de substituição do ciclo de vida da exibição no controlador de exibição (por exemplo, em `ViewDidLoad()`). Consulte também [trabalhando com imagens](~/ios/app-fundamentals/images-icons/index.md) para obter dicas sobre como usar `UIImage.FromBundle()` versus `UIImage.FromFile()`.

### <a name="option-2-move-all-of-the-image-resources-to-the-top-level-resources-folder"></a>Opção 2: mover todos os recursos de imagem para a pasta de `Resources` de nível superior

Depois de mover as imagens para a pasta de `Resources` de nível superior, você precisará atualizar os arquivos de storyboard e `.xib` para usar os novos caminhos de imagem.

### <a name="option-3-set-the-logicalname-for-any-problematic-image-assets-so-they-are-copied-to-the-top-level-of-theapp-bundle"></a>Opção 3: definir o `LogicalName` para qualquer ativo de imagem problemática para que eles sejam copiados para o nível superior do pacote de`.app`

Por exemplo, digamos que o arquivo original de `.csproj` contém a seguinte entrada:

`<BundleResource Include="Resources\Images\image.png" />`

Você pode alterar esse elemento e adicionar um `LogicalName` para que a imagem seja copiada para o nível superior do pacote de `.app`:

```xml
<BundleResource Include="Resources\Images\image.png">
    <LogicalName>image.png</LogicalName>
</BundleResource>
```

Em Visual Studio para Mac o `LogicalName` também pode ser definido usando o campo `Resource ID` para a imagem em **exibir > Pads > Propriedades**. (Consulte também: [https://stackoverflow.com/questions/16938250/xamarin-studio-folder-structure-issue-in-ios-project/16951545#16951545](https://stackoverflow.com/questions/16938250/xamarin-studio-folder-structure-issue-in-ios-project/16951545#16951545))

Após essa alteração, você precisará atualizar os arquivos de storyboard e `.xib` para usar os novos caminhos de imagem de nível superior. Visual Studio para Mac atualizará automaticamente a lista de preenchimentos automáticos para a propriedade `Image` no designer do iOS. No Visual Studio, você precisará editar o caminho manualmente. O designer do iOS exibirá isso como uma imagem ausente, mas o projeto será compilado e executado corretamente.

### <a name="next-steps"></a>Próximas etapas

Para obter mais assistência, entrar em contato conosco ou, se esse problema permanecer mesmo depois de utilizar as informações acima, consulte [quais opções de suporte estão disponíveis para o Xamarin?](~/cross-platform/troubleshooting/support-options.md) para obter informações sobre opções de contato, sugestões, bem como como arquivar um novo bug, se necessário . 
