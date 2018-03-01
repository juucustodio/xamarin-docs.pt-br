---
title: Exibindo imagens
ms.topic: article
ms.prod: xamarin
ms.assetid: 67CA8DB6-769D-42BB-A137-3AF933789FE1
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 71f3774c12add26e818b0859cf90c17ab6358538
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="displaying-images"></a>Exibindo imagens

Adicionando imagens ao seu aplicativo requer duas etapas: primeiro, adicionar imagens ao projeto. em seguida, adicione controles e código para exibi-los em uma tela. Consulte o [trabalhando com imagens](~/ios/app-fundamentals/images-icons/index.md) do artigo para obter mais cobertura da imagem tratamento em xamarin.

## <a name="adding-images-to-your-app"></a>Adicionando imagens ao seu aplicativo

Imagens podem ser adicionadas a qualquer pasta em que o Visual Studio para solução de Mac e se o **ação de compilação** é definido como **conteúdo** , em seguida, o arquivo será incluído com o seu aplicativo e pode ser exibido.

O Visual Studio para Mac também oferece suporte a um diretório especial chamado de recursos que também podem conter arquivos de imagem. Os arquivos na pasta de recursos devem ter o **ação de compilação** definida como **BundleResource**.

Esta captura de tela mostra a **ação de compilação** opções que aparecem quando um arquivo é clicado:

 [ ![](image-images/image30a.png "Menu de ação de compilação")](image-images/image30a.png)

O Visual Studio para Mac normalmente escolherá corretas **ação de compilação** automaticamente, mas você deve estar atento essas configurações, especialmente se você mover arquivos em seu projeto.

### <a name="adding-an-image-file"></a>Adicionando um arquivo de imagem

Para adicionar um arquivo de imagem ao seu projeto, primeiro clique com o botão direito e escolha **adicionar arquivos...**

 [ ![](image-images/image31a.png "Adicionar arquivos … menu")](image-images/image31a.png)

Selecione a imagem (ou imagens) que deseja incluir na caixa de diálogo de arquivo padrão. Ação de compilação padrão para imagens poderão ser **BundleResource** – não substituir esse valor, a menos que você tenha um motivo específico.

 [ ![](image-images/image32a.png "Adicionar caixa de diálogo de arquivos")](image-images/image32a.png)

A imagem será adicionada ao seu projeto e disponível para ser carregado e exibido no código. Esta captura de tela mostra uma imagem adicionada a um projeto de aplicativo do iOS:

 [ ![](image-images/image33a.png "Imagem no projeto")](image-images/image33a.png)

### <a name="what-is-the-resources-directory"></a>O que é o diretório de recursos?

Arquivos colocados no diretório de recursos são tratados Diferentemente de arquivos regulares – o conteúdo da pasta de recursos é copiado para a raiz do aplicativo e pode ser referenciado a partir daí, em seu código. Isso pode ser útil por vários motivos:

-  Armazenar as imagens definidas nas propriedades do aplicativo, como as imagens de inicialização padrão e os ícones de aplicativo.
-  Armazenar outras imagens e arquivos separadamente do código, portanto eles são mais fáceis de gerenciar (subdiretórios são preservados quando o conteúdo do diretório de recursos é copiado).


O diretório de recursos é especialmente útil em um projeto de biblioteca, desde que o código pode assumir que essas imagens serão copiadas para a raiz do aplicativo consome, tornando mais fácil escrever bibliotecas de código compartilhado que exigem a imagem, som, vídeo, XML ou outro arquivos.



O diretório de recursos deve ser chamado para, e todos os arquivos devem ter a ação de compilação definido como **BundleResource**

## <a name="displaying-the-image"></a>Exibindo a imagem

Para exibir uma imagem usando o designer, uma exibição da imagem deve ser usada como um contêiner e podem exibir uma única imagem ou uma animação de imagens. O **imagem** ícone da caixa de ferramentas é mostrado abaixo:

 [ ![](image-images/image35a.png "ImageView na caixa de ferramentas")](image-images/image35.png)

Arraste o **exibição da imagem** do **Toobox** no controlador de exibição. Em seguida em * * imagem > imagem * * na lista suspensa fornecerá uma lista de todos os arquivos de imagem disponível no seu projeto. Selecione um dos seguintes para adicioná-lo ao modo de exibição de imagem.

 [ ![](image-images/image36a.png "ImageView na caixa de ferramentas")](image-images/image36.png)

### <a name="displaying-the-image-programmatically"></a>Exibindo a imagem de forma programática

Como blocks.jpg está localizado na raiz do diretório de recursos, elas estarão disponível em tempo de execução na raiz do pacote do aplicativo. Para exibir essa imagem em um ImageView controle use o seguinte código:

```csharp
imageview1.Image = UIImage.FromBundle ("SF Monkey.png");
```

Se tivesse colocamos a imagem no `/Resources/Pics/blocks.jpg` , em seguida, o código inclui a pasta imagens no caminho:

```csharp
imageview1.Image = UIImage.FromBundle ("Pics/SF Monkey.png");
```

Arquivo de recurso referencia nunca precisa incluir o `Resources` pasta.


## <a name="related-links"></a>Links relacionados

- [Controles (exemplo)](https://developer.xamarin.com/samples/Controls/)
