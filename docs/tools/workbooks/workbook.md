---
title: Pastas de trabalho interativas
description: Este documento descreve como usar o Xamarin Workbooks para criar documentos em tempo real que contém C# código para experimentar, ensinar, treinamento ou explorando.
ms.prod: xamarin
ms.assetid: B79E5DE9-5389-4691-9AA3-FF4336CE294E
author: lobrien
ms.author: laobri
ms.date: 03/30/2017
ms.openlocfilehash: 71f46535ffd0a99ad78acb8f0e3bbc5870abf33e
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50116752"
---
# <a name="interactive-workbooks"></a>Pastas de trabalho interativas

Você pode usar pastas de trabalho como um aplicativo autônomo, separado do seu IDE.

Para começar a criar uma nova pasta de trabalho, execute o aplicativo de pastas de trabalho. Se você ainda não tiver isso já instalado, visite o [instalação](~/tools/workbooks/install.md#install) página. Você será solicitado a criar uma pasta de trabalho em sua plataforma de preferência, que irá se conectar automaticamente a um aplicativo de agente, permitindo que você visualize seu documento em tempo real.

Se o aplicativo pastas de trabalho já está em execução, você pode criar um novo documento, navegando até **arquivo > novo**.

Pastas de trabalho podem ser salvo e abertas novamente mais tarde no aplicativo. Em seguida, você pode compartilhá-los com outras pessoas para demonstrar ideias, explore as novas APIs ou ensinar os novos conceitos.

## <a name="code-editing"></a>Edição de código

O janela de edição de código fornece conclusão de código, colorização de sintaxe, embutido live-diagnostics e suporte à instrução de várias linhas.

[ ![](workbook-images/inspector-0.6.0-repl-small.png "O janela de edição de código fornece conclusão de código, colorização de sintaxe, embutido live-diagnostics e suporte à instrução de várias linhas")](workbook-images/inspector-0.6.0-repl.png#lightbox)

Xamarin Workbooks são salvas em um `.workbook` arquivo, que é um arquivo CommonMark com alguns metadados na parte superior (consulte [tipos de arquivo de pastas de trabalho](#workbooks-files-types) para obter mais detalhes sobre como as pastas de trabalho podem ser salvas).

### <a name="nuget-package-support"></a>Suporte de pacote do NuGet

Muitos pacotes do NuGet populares têm suporte diretamente em pastas de trabalho do Xamarin. Você pode procurar pacotes, navegando até **arquivo > Adicionar pacote**. Adicionando um pacote levará automaticamente no `#r` instruções que referenciam os assemblies do pacote, permitindo que você usá-los imediatamente.

Quando você salva uma pasta de trabalho com referências de pacote, essas referências são salvos também. Se você compartilhar a pasta de trabalho com outra pessoa, ele baixa automaticamente os pacotes referenciados.

Há algumas limitações conhecidas com suporte de pacote do NuGet em pastas de trabalho:

- Bibliotecas nativas são suportados apenas no iOS e somente quando vinculado com a biblioteca gerenciada.
- Pacotes que dependem do `.targets` arquivos ou scripts do PowerShell provavelmente não funcionará conforme o esperado.
- Para remover ou modificar uma dependência de pacote, edite o manifesto da pasta de trabalho com um editor de texto. Gerenciamento de pacotes apropriado está sendo enviado.

### <a name="xamarinforms-support"></a>Suporte de xamarin. Forms

Se você referenciar o pacote NuGet do xamarin. Forms em sua pasta de trabalho, o aplicativo de pasta de trabalho será alterado sua exibição principal para ser baseados em xamarin. Forms. Você pode acessá-lo por meio de `Xamarin.Forms.Application.Current.MainPage`.

A guia do Inspetor de exibição também tem suporte especial para mostrar a hierarquia de exibição do xamarin. Forms para ajudá-lo a entender seus layouts.

## <a name="rich-text-editing"></a>Edição de texto avançada

Você pode editar o texto em torno de seu código usando o editor de rich text incluído, conforme ilustrado abaixo:

![](workbook-images/inspector-0.6.2-editing.gif "Editar o texto ao redor do código usando o editor de texto avançado interno")

### <a name="markdown-authoring"></a>Criação de markdown

Os autores de pasta de trabalho podem, às vezes, achar mais fácil editar diretamente o CommonMark "origem" da pasta de trabalho com seu editor favorito.

Lembre-se de que, se você, em seguida, editar e salva sua pasta de trabalho do cliente de pastas de trabalho, seu texto CommonMark pode ser reformatado.

Observe que, devido à extensão CommonMark, podemos usar habilitar YAML metadados em arquivos de pasta de trabalho, `---` é reservado para essa finalidade. Se você deseja criar [quebras temáticas](http://spec.commonmark.org/0.27/#thematic-break) em seu texto, você deve usar `***` ou `___` em vez disso. Essas quebras devem ser evitadas em pastas de trabalho 1.2 e anteriormente devido a um bug durante o salvamento.

### <a name="improvements-in-workbooks-13"></a>Melhorias em pastas de trabalho 1.3

Também ampliamos a sintaxe de aspas do bloco de Markdown ligeiramente para melhorar a apresentação. Adicionando um emoji como o primeiro caractere em seu citação em bloco, você pode influenciar a cor do plano de fundo da cotação:

- `> [!NOTE]`
    > será renderizado como uma anotação com um plano de fundo azul
- `> [!IMPORTANT]`
    > será renderizado como um aviso com um plano de fundo amarelo
- `> [!WARNING]`
    > será renderizado como um problema com um plano de fundo vermelho

Você também pode vincular aos cabeçalhos do documento de pasta de trabalho. Podemos gerar as âncoras para cada cabeçalho, com a ID da âncora sendo o texto do cabeçalho, processado da seguinte maneira:

1. O cabeçalho é em minúsculas.
1. Todos os caracteres, exceto caracteres alfanuméricos e traços são removidos.
1. Todos os espaços são substituídos por traços.

Isso significa que um cabeçalho como "Importante Header" obtém uma id de `important-header` e pode ser vinculado a inserindo um link para `#important-header` na pasta de trabalho.

## <a name="document-structure"></a>Estrutura do documento

### <a name="cell"></a>célula

Uma unidade separada de conteúdo, que representa o código executável ou markdown. Uma célula de código é composta de até quatro subcomponentes:

- Editor
  - buffer
- Diagnóstico do compilador
- Saída do console
- Resultados da execução

### <a name="editor"></a>Editor

O componente de texto interativa de uma célula. Para células de código, este é o editor de código real com realce de sintaxe, etc. Para células de markdown, este é um editor de conteúdo de rich text com uma formatação sensível ao contexto e a barra de ferramentas de criação.

### <a name="buffer"></a>buffer

O conteúdo de um editor de texto real.

### <a name="compiler-diagnostics"></a>Diagnóstico do compilador

Qualquer diagnóstico produzido durante a compilação de código, processado somente quando for solicitada a execução explícita. Exibido imediatamente abaixo do editor de célula.

### <a name="console-output"></a>Saída do console

Nenhuma saída foi gravada para saída padrão ou erro padrão durante a execução da célula. Saída padrão será renderizada em texto preto e erro padrão será renderizado em texto vermelho.

### <a name="execution-results"></a>Resultados da execução

Ricas e interativas potencialmente representações de resultados de uma célula serão renderizadas na compilação bem-sucedida, desde um resultado, na verdade, é produzido pela execução. As exceções são consideradas resultados nesse contexto, uma vez que eles são produzidos como resultado da execução, na verdade, a compilação.

## <a name="workbooks-files-types"></a>Tipos de arquivos de pastas de trabalho

### <a name="plain-files"></a>Arquivos simples

Por padrão, uma pasta de trabalho salva como um texto sem formatação `.workbook` arquivo que contém o texto formatado em CommonMark.

### <a name="packages"></a>Pacotes

Um pacote de pasta de trabalho é um diretório que é nomeado com o `.workbook` extensão.
No Finder do Mac e na caixa de diálogo Abrir pastas de trabalho do Xamarin e o menu de arquivos recentes, esse diretório será reconhecido como se fosse um arquivo.

O diretório deve conter um `index.workbook` arquivo, que é a pasta de trabalho de texto sem formatação que será carregada no Xamarin Workbooks. O diretório também pode conter recursos exigidos pelo `index.workbook`, incluindo imagens ou outros arquivos.

Se um texto sem formatação `.workbook` que faz referência a recursos do mesmo diretório de arquivo é aberto em pastas de trabalho 0.99.3 ou posterior, quando ele for salvo, ele será convertido em um `.workbook` pacote. Isso é verdadeiro no Mac e Windows.

> [!NOTE]
> Os usuários do Windows abrirá o `package.workbook\index.workbook` diretamente do arquivo, mas caso contrário, o pacote se comportará como no Mac.

### <a name="archives"></a>Arquivos mortos

Pacotes de pasta de trabalho, que está sendo diretórios, podem ser difícil distribuir facilmente pela internet. A solução é arquivos de pasta de trabalho. Um arquivo de pasta de trabalho é um pacote compactado por zip pasta de trabalho, nomeado com o `.workbook` extensão.

A partir 1.1 de pastas de trabalho, ao salvar um pacote da pasta de trabalho, a caixa de diálogo Salvar oferece a opção de salvar como arquivo morto em vez disso. Pastas de trabalho 1.0 não tinham nenhum modo interno de criar ou salvar arquivos mortos.

Em pastas de trabalho 1.0, quando um arquivo de pasta de trabalho foi aberto, transparente, ele foi convertido em um pacote da pasta de trabalho e o arquivo zip foi perdido. 1.1 de pastas de trabalho, o arquivo zip permanece. Quando o usuário salva o arquivo morto, ele será substituído por um novo arquivo zip.

Você pode criar um arquivo de pasta de trabalho manualmente clicando duas vezes um pacote da pasta de trabalho e selecionando **compactar** no Mac, ou **enviar para > pasta compactada (zipada)** no Windows. Renomeie o arquivo zip para ter um `.workbook` extensão de arquivo. Isso só funciona com os pacotes de pasta de trabalho, arquivos de pasta de trabalho não sem formatação.

## <a name="related-links"></a>Links relacionados

- [Bem-vindo às pastas de trabalho](https://developer.xamarin.com/workbooks/workbooks/getting-started/welcome.workbook)
