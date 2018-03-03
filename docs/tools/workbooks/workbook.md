---
title: Pastas de trabalho interativas
description: "Use pastas de trabalho para criar documentos em tempo real com o código c# para testar, ensino, treinamento ou explorar."
ms.topic: article
ms.prod: xamarin
ms.assetid: B79E5DE9-5389-4691-9AA3-FF4336CE294E
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 03/30/2017
ms.openlocfilehash: bd53074fcde25736d37544efc719ecef1110c364
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="interactive-workbooks"></a>Pastas de trabalho interativas

_Use pastas de trabalho para criar documentos em tempo real com o código c# para testar, ensino, treinamento ou explorar._

Você pode usar pastas de trabalho como um aplicativo autônomo, separado do seu IDE.

Para começar a criar uma nova pasta de trabalho, execute o aplicativo de pastas de trabalho. Se você ainda não instalou este já, visite o [instalação](~/tools/workbooks/install.md#install) página. Você precisará criar uma pasta de trabalho em sua plataforma de preferência, que se conectarão automaticamente a um aplicativo de agente, permitindo que você visualize o documento em tempo real.

Se o aplicativo de pastas de trabalho já está em execução, você pode criar um novo documento, navegando para **arquivo > novo**.

Pastas de trabalho podem ser salvo e abertas novamente mais tarde no aplicativo. Em seguida, você pode compartilhá-los com outras pessoas para demonstrar ideias, explorar novas APIs ou ensinar conceitos novos.

## <a name="code-editing"></a>Edição de código

Janela de edição de código fornece conclusão de código, a coloração de sintaxe, embutido em tempo real de diagnóstico e suporte de instrução de várias linhas.

[ ![](workbook-images/inspector-0.6.0-repl-small.png "Janela de edição de código fornece conclusão de código, a coloração de sintaxe, embutido em tempo real de diagnóstico e suporte de instrução de várias linhas")](workbook-images/inspector-0.6.0-repl.png)

Xamarin pastas de trabalho são salvos em um `.workbook` arquivo, que é um arquivo de CommonMark com alguns metadados na parte superior (consulte [tipos de arquivo de pastas de trabalho](#Workbooks_Files_Types) para obter mais detalhes sobre como as pastas de trabalho podem ser salvos).

### <a name="nuget-package-support"></a>Suporte de pacote do NuGet

Muitos pacotes do NuGet populares têm suporte diretamente em pastas de trabalho do Xamarin. Você pode procurar pacotes navegando até **arquivo > Adicionar pacote**. Adicionando um pacote automaticamente trará `#r` instruções que referenciam os assemblies do pacote, permitindo que você usá-los imediatamente.

Quando você salvar uma pasta de trabalho com referências do pacote, essas referências também são salvas. Se você compartilhar a pasta de trabalho com outra pessoa, ele baixará automaticamente os pacotes referenciados.

Há algumas limitações conhecidas com suporte de pacote do NuGet em pastas de trabalho:

  * Bibliotecas nativas têm suporte apenas no iOS e somente quando vinculado com a biblioteca gerenciada.
  * Pacotes que dependem de `.targets` arquivos ou scripts do PowerShell provavelmente não funcionará conforme o esperado.
  * Para remover ou modificar uma dependência de pacote, edite o manifesto da pasta de trabalho com um editor de texto. Gerenciamento de pacotes adequado está a caminho.

### <a name="xamarinforms-support"></a>Suporte de xamarin. Forms

Se você referenciar o pacote NuGet do xamarin. Forms em sua pasta de trabalho, o aplicativo de pasta de trabalho alterará o modo de exibição principal para ser baseados em xamarin. Forms. Você pode acessá-lo por meio de `Xamarin.Forms.Application.Current.MainPage`.

A guia do Inspetor de exibição também tem suporte especial para mostrar a hierarquia de exibição xamarin. Forms para ajudá-lo a entender os layouts.

## <a name="rich-text-editing"></a>Edição de texto avançada

Você pode editar o texto em torno de seu código usando o editor de rich text incluído, conforme ilustrado abaixo:

![](workbook-images/inspector-0.6.2-editing.gif "Editar o texto no código usando o editor de rich text internos")

### <a name="markdown-authoring"></a>Criação de markdown

Os autores de pasta de trabalho podem, às vezes, seja mais fácil editar diretamente o CommonMark "fonte" da pasta de trabalho com seu editor favorito.

Lembre-se de que se você edita e salva sua pasta de trabalho do cliente de pastas de trabalho, o texto CommonMark pode ser reformatado.

Observe que devido à extensão de CommonMark usamos para habilitar YAML metadados nos arquivos de pasta de trabalho, `---` é reservado para essa finalidade. Se você quiser criar [quebras temáticas](http://spec.commonmark.org/0.27/#thematic-break) em texto, você deve usar `***` ou `___` em vez disso. Tais quebras devem ser evitadas em pastas de trabalho 1.2 e anteriormente devido a um erro durante o salvamento.

### <a name="improvements-in-workbooks-13"></a>Aprimoramentos em pastas de trabalho 1.3

Também podemos tiver estendido a sintaxe de cotação de bloco Markdown ligeiramente para melhorar a apresentação. Adicionando um emoji como o primeiro caractere em sua cotação de bloco, você pode influenciar a cor de plano de fundo da cotação:

- `> [!NOTE]
>' será processada como uma anotação com um plano de fundo azul
- `> [!IMPORTANT]
>' será renderizado como um aviso com um plano de fundo amarelo
- `> [!WARNING]
>' será renderizado como um problema com um plano de fundo vermelho

Você também pode vincular aos cabeçalhos do documento da pasta de trabalho. Podemos gerar âncoras para cada cabeçalho, com a ID de âncora sendo o texto do cabeçalho, processado como a seguir:

1. O cabeçalho é em minúsculas.
1. Todos os caracteres, exceto alfanuméricos e traços são removidos.
1. Todos os espaços são substituídos por travessões.

Isso significa que um cabeçalho como "Cabeçalho importantes" obtém uma id de `important-header` e pode ser vinculado ao inserir um link para `#important-header` na pasta de trabalho.

## <a name="document-structure"></a>Estrutura do documento

### <a name="cell"></a>Célula

Uma unidade distinta de conteúdo, que representa o código executável ou redução. Uma célula de código é composta de até quatro subcomponentes:

- Editor
  - Buffer
- Diagnóstico do compilador
- Saída do console
- Resultados da execução

### <a name="editor"></a>Editor

O componente de texto de uma célula. Para células de código, este é o editor de código real com realce de sintaxe, etc. Para células de markdown, esse é um editor de rich text conteúdo com uma formatação sensíveis ao contexto e a barra de ferramentas de criação.

### <a name="buffer"></a>Buffer
O conteúdo de um editor de texto real.

### <a name="compiler-diagnostics"></a>Diagnóstico do compilador

Qualquer diagnóstico produzidos durante a compilação de código, processado apenas quando for solicitada a execução explícita. Exibido imediatamente abaixo do editor de célula.

### <a name="console-output"></a>Saída do console

Nenhuma saída gravada fora do padrão ou de erro padrão durante a execução da célula. Saída padrão será renderizada em texto preto e erro padrão será renderizado em texto vermelho.

### <a name="execution-results"></a>Resultados da execução

Avançadas e interativas potencialmente representações de resultados de uma célula serão renderizadas na compilação bem-sucedida, desde que um resultado realmente produzido pela execução. Exceções serão consideradas resultados nesse contexto, pois eles são gerados como resultado da execução, na verdade, a compilação.

## <a name="workbooks-files-types"></a>Tipos de arquivos de pastas de trabalho

### <a name="plain-files"></a>Arquivos simples

Por padrão, uma pasta de trabalho salva como um texto sem formatação `.workbook` arquivo que contém o texto formatado em CommonMark.

### <a name="packages"></a>Pacotes

Um pacote de pasta de trabalho é um diretório que é nomeado com o `.workbook` extensão.
Localizador do Mac e na caixa de diálogo Abrir pastas de trabalho do Xamarin e o menu de arquivos recentes, esse diretório será reconhecido como se fosse um arquivo.

O diretório deve conter um `index.workbook` arquivo, que é a pasta de trabalho de texto sem formatação real que será carregada em pastas de trabalho do Xamarin. O diretório também pode conter recursos exigidos pela `index.workbook`, incluindo imagens ou outros arquivos.

Se um texto sem formatação `.workbook` que faz referência a recursos do mesmo diretório de arquivo é aberto em pastas de trabalho 0.99.3 ou posterior, quando ele for salvo, ele será convertido em um `.workbook` pacote. Isso é verdadeiro no Mac e Windows.

> [!NOTE]
> **Observação:** os usuários do Windows abrirá o `package.workbook\index.workbook` arquivo diretamente, mas, caso contrário, o pacote comportará da mesma no Mac.

### <a name="archives"></a>Arquivos mortos

Pacotes de pasta de trabalho, sendo diretórios, podem ser difícil distribuir facilmente pela internet. A solução é arquivos de pasta de trabalho. Um arquivo de pasta de trabalho é um pacote compactado zip pasta de trabalho, chamado com o `.workbook` extensão.

A partir de pastas de trabalho 1.1, ao salvar um pacote da pasta de trabalho, a caixa de diálogo Salvar oferece a opção de salvar como arquivo morto em vez disso. Pastas de trabalho 1.0 não tinham interno como criar ou salvar arquivos.

Pastas de trabalho 1.0, quando um arquivo de pasta de trabalho foi aberto, transparente foi convertido em um pacote de pasta de trabalho, e o arquivo zip foi perdido. Em pastas de trabalho 1.1, o arquivo zip permanece. Quando o usuário salva o arquivo, ele será substituído por um novo arquivo zip.

Você pode criar um arquivo de pasta de trabalho manualmente clicando duas vezes um pacote da pasta de trabalho e selecionando **compactar** no Mac, ou **enviar para > pasta de compactadas (zipada)** no Windows. Renomeie o arquivo zip para ter um `.workbook` extensão de arquivo. Isso funciona apenas com pacotes de pasta de trabalho, arquivos de pasta de trabalho não simples.

## <a name="related-links"></a>Links relacionados

- [Bem-vindo às pastas de trabalho](https://developer.xamarin.com/workbooks/workbooks/getting-started/welcome.workbook)
