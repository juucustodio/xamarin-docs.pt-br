---
title: Pastas de trabalho interativas
description: Este documento descreve como usar Xamarin Workbooks para criar documentos em tempo real C# contendo código para experimentar, ensinar, treinar ou explorar.
ms.prod: xamarin
ms.assetid: B79E5DE9-5389-4691-9AA3-FF4336CE294E
author: conceptdev
ms.author: crdun
ms.date: 03/30/2017
ms.openlocfilehash: 7b3c356460d9427821843dc084b3f306c026ffa0
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70293081"
---
# <a name="interactive-workbooks"></a>Pastas de trabalho interativas

Você pode usar pastas de trabalho como um aplicativo autônomo, separado do IDE.

Para começar a criar uma nova pasta de trabalho, execute o aplicativo de pastas de trabalho. Se ainda não tiver instalado isso, visite a página [instalação](~/tools/workbooks/install.md#install) . Você será solicitado a criar uma pasta de trabalho na plataforma de sua escolha, que se conectará automaticamente a um aplicativo do Agent, permitindo que você visualize o documento em tempo real.

Se o aplicativo de pastas de trabalho já estiver em execução, você poderá criar um novo documento navegando até **arquivo > novo**.

As pastas de trabalho podem ser salvas e abertas novamente mais tarde no aplicativo. Você pode compartilhá-los com outras pessoas para demonstrar ideias, explorar novas APIs ou ensinar novos conceitos.

## <a name="code-editing"></a>Edição de código

A janela de edição de código fornece a conclusão de código, a coloração de sintaxe, o diagnóstico ao vivo embutido e o suporte a instruções de várias linhas.

[![](workbook-images/inspector-0.6.0-repl-small.png "A janela de edição de código fornece a conclusão de código, a coloração de sintaxe, o diagnóstico ao vivo embutido e o suporte a instruções de várias linhas")](workbook-images/inspector-0.6.0-repl.png#lightbox)

Xamarin Workbooks são salvas em `.workbook` um arquivo, que é um arquivo CommonMark com alguns metadados na parte superior (consulte os [tipos de arquivo de pastas de trabalho](#workbooks-files-types) para obter mais detalhes sobre como as pastas de trabalho podem ser salvas).

### <a name="nuget-package-support"></a>Suporte ao pacote NuGet

Muitos pacotes NuGet populares têm suporte diretamente no Xamarin Workbooks. Você pode procurar pacotes navegando até o **arquivo > adicionar pacote**. A adição de um pacote trará `#r` automaticamente instruções que fazem referência a assemblies de pacote, permitindo que você os use imediatamente.

Quando você salva uma pasta de trabalho com referências de pacote, essas referências também são salvas. Se você compartilhar a pasta de trabalho com outra pessoa, ela baixará automaticamente os pacotes referenciados.

Há algumas limitações conhecidas com o suporte a pacotes NuGet em pastas de trabalho:

- As bibliotecas nativas têm suporte apenas no iOS e somente quando vinculadas à biblioteca gerenciada.
- Os pacotes que dependem `.targets` de arquivos ou scripts do PowerShell provavelmente não funcionarão conforme o esperado.
- Para remover ou modificar uma dependência de pacote, edite o manifesto da pasta de trabalho com um editor de texto. O gerenciamento de pacotes adequado está a caminho.

### <a name="xamarinforms-support"></a>Suporte para Xamarin. Forms

Se você referenciar o pacote NuGet do Xamarin. Forms em sua pasta de trabalho, o aplicativo de pasta de trabalho alterará sua exibição principal para o Xamarin. Forms baseado. Você pode acessá- `Xamarin.Forms.Application.Current.MainPage`lo por meio do.

A guia Inspetor de exibição também tem suporte especial para mostrar a hierarquia de exibição Xamarin. Forms para ajudá-lo a entender seus layouts.

## <a name="rich-text-editing"></a>Edição de Rich Text

Você pode editar o texto em todo o seu código usando o editor de Rich Text incluído, conforme ilustrado abaixo:

![](workbook-images/inspector-0.6.2-editing.gif "Editar o texto em volta do código usando o editor de Rich Text interno")

### <a name="markdown-authoring"></a>Redução da criação

Às vezes, os autores da pasta de trabalho podem achar mais fácil editar diretamente a "origem" do CommonMark da pasta de trabalho com seu editor favorito.

Lembre-se de que, se você editar e salvar sua pasta de trabalho no cliente de pastas de trabalho, seu texto CommonMark poderá ser reformatado.

Observe que, devido à extensão CommonMark que usamos para habilitar metadados YAML em arquivos de pasta de `---` trabalho, é reservada para essa finalidade. Se você quiser criar [quebras de temática](http://spec.commonmark.org/0.27/#thematic-break) no texto, deverá usar `***` ou `___` em vez disso. Essas interrupções devem ser evitadas nas pastas de trabalho 1,2 e anteriores devido a um bug durante o salvamento.

### <a name="improvements-in-workbooks-13"></a>Melhorias nas pastas de trabalho 1,3

Também estendemos ligeiramente a sintaxe de redução de aspas de blocos para melhorar a apresentação. Ao adicionar um Emoji como o primeiro caractere em sua citação de bloco, você pode influenciar a cor do plano de fundo da citação:

- `> [!NOTE]`
    > será renderizado como uma anotação com um plano de fundo azul
- `> [!IMPORTANT]`
    > será renderizado como um aviso com um plano de fundo amarelo
- `> [!WARNING]`
    > será renderizado como um problema com um plano de fundo vermelho

Você também pode vincular a cabeçalhos no documento da pasta de trabalho. Geramos âncoras para cada cabeçalho, com a ID de âncora sendo o texto do cabeçalho, processado da seguinte maneira:

1. O cabeçalho está em letras minúsculas.
1. Todos os caracteres, exceto os alfanuméricos e traços, são removidos.
1. Todos os espaços são substituídos por traços.

Isso significa que um cabeçalho como "cabeçalho importante" Obtém uma ID `important-header` e que pode ser vinculado ao inserir um link para `#important-header` na pasta de trabalho.

## <a name="document-structure"></a>Estrutura do documento

### <a name="cell"></a>CÉL

Uma unidade discreta de conteúdo, que representa o código executável ou a redução. Uma célula de código é composta de até quatro subcomponentes:

- Editor
  - Buffer
- Diagnóstico do compilador
- Saída do console
- Resultados da execução

### <a name="editor"></a>Editor

O componente de texto interativo de uma célula. Para células de código, esse é o editor de código real com realce de sintaxe, etc. Para as células de redução, este é um editor de conteúdo de Rich Text com uma barra de ferramentas de criação e formatação contextual.

### <a name="buffer"></a>Buffer

O conteúdo de texto real de um editor.

### <a name="compiler-diagnostics"></a>Diagnóstico do compilador

Qualquer diagnóstico produzido durante a compilação do código, será renderizado somente quando a execução explícita for solicitada. Exibido imediatamente abaixo do editor de célula.

### <a name="console-output"></a>Saída do console

Qualquer saída gravada no erro padrão ou padrão durante a execução da célula. O padrão de saída será renderizado em texto preto e o erro padrão será renderizado em texto vermelho.

### <a name="execution-results"></a>Resultados da execução

Representações avançadas e potencialmente interativas dos resultados de uma célula serão renderizados após a compilação bem-sucedida, desde que um resultado seja realmente produzido pela execução. As exceções são consideradas resultados neste contexto, pois elas são produzidas como resultado da execução da compilação.

## <a name="workbooks-files-types"></a>Tipos de arquivos de pastas de trabalho

### <a name="plain-files"></a>Arquivos simples

Por padrão, uma pasta de trabalho é salva como `.workbook` um arquivo de texto sem formatação que contém texto formatado em CommonMark.

### <a name="packages"></a>Pacotes

Um pacote de pasta de trabalho é um diretório que é `.workbook` nomeado com a extensão.
No localizador do Mac e no menu Xamarin Workbooks abrir caixa de diálogo e arquivos recentes, esse diretório será reconhecido como se fosse um arquivo.

O diretório deve conter um `index.workbook` arquivo, que é a pasta de trabalho de texto sem formatação real que será carregada no Xamarin Workbooks. O diretório também pode conter recursos exigidos `index.workbook`pelo, incluindo imagens ou outros arquivos.

Se um arquivo de `.workbook` texto sem formatação que faz referência a recursos de seu mesmo diretório for aberto nas pastas de trabalho 0.99.3 ou posterior, quando ele for salvo, ele `.workbook` será convertido em um pacote. Isso é verdadeiro no Mac e no Windows.

> [!NOTE]
> Os usuários do Windows abrirão o `package.workbook\index.workbook` arquivo diretamente, mas o pacote se comportará do mesmo modo que no Mac.

### <a name="archives"></a>Zip

Os pacotes de pastas de trabalho, sendo diretórios, podem ser difíceis de distribuir facilmente pela Internet. A solução é arquivamento de pastas de trabalho. Um arquivo de pasta de trabalho é um pacote de pasta de trabalho compactado zip, chamado com a `.workbook` extensão.

A partir das pastas de trabalho 1,1, ao salvar um pacote de pasta de trabalho, a caixa de diálogo Salvar oferece a opção de salvar como um arquivo morto. As pastas de trabalho 1,0 não tinham uma maneira interna de criar ou salvar arquivos mortos.

Nas pastas de trabalho 1,0, quando um arquivo de pasta de trabalho foi aberto, ele foi convertido de forma transparente em um pacote de pasta de trabalho e o arquivo zip foi perdido. Nas pastas de trabalho 1,1, o arquivo zip permanece. Quando o usuário salva o arquivo, ele é substituído por um novo arquivo zip.

Você pode criar um arquivo morto de pasta de trabalho manualmente clicando com o botão direito do mouse em um pacote de pasta de trabalho e selecionando **compactar** no Mac ou **Enviar para > pasta compactada (zipada)** no Windows. Em seguida, renomeie o arquivo `.workbook` zip para ter uma extensão de arquivo. Isso só funciona com pacotes de pasta de trabalho, não com arquivos de pasta de trabalho simples.

## <a name="related-links"></a>Links relacionados

- [Bem-vindo às pastas de trabalho](https://developer.xamarin.com/workbooks/workbooks/getting-started/welcome.workbook)
