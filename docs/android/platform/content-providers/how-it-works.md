---
title: Como os provedores de conteúdo funcionam
ms.prod: xamarin
ms.assetid: B9E2EF89-7EBE-45F5-1ED9-7D2C70BE792C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 9d8710b3b88b59871b88a1d42ec4f4bb3e515ff5
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70756513"
---
# <a name="how-content-providers-work"></a>Como os provedores de conteúdo funcionam

Há duas classes envolvidas em uma `ContentProvider` interação:

- **ContentProvider** &ndash; Implementa uma API que expõe um conjunto de dados de forma padrão. Os principais métodos são consulta, inserção, atualização e exclusão.

- **ContentResolver retornem** Um proxy estático que se comunica com um `ContentProvider` para acessar seus dados, seja de dentro do mesmo aplicativo ou de outro aplicativo. &ndash;

Um provedor de conteúdo é normalmente apoiado por um banco de dados SQLite, mas a API significa que o consumo de código não precisa saber nada sobre o SQL subjacente. As consultas são feitas por meio de um URI usando constantes para referenciar nomes de coluna (para reduzir as dependências na estrutura `ICursor` de dados subjacente) e um é retornado para que o código de consumo seja iterado.

## <a name="consuming-a-contentprovider"></a>Consumindo um ContentProvider

`ContentProviders`expor sua funcionalidade por meio de um URI que é registrado no **AndroidManifest. xml** do aplicativo que publica os dados. Há uma convenção em que o URI e as colunas de dados que são expostas devem estar disponíveis como constantes para facilitar a ligação aos dados. `ContentProviders` Tudo interno do Android fornece classes de conveniência com constantes que fazem referência à estrutura de dados [`Android.Providers`](xref:Android.Provider) no namespace.

### <a name="built-in-providers"></a>Provedores internos

O Android oferece acesso a uma ampla variedade de dados do sistema e `ContentProviders`do usuário usando:

- *Navegador* indicadores e histórico de navegador (requer permissão `READ_HISTORY_BOOKMARKS` e/ou `WRITE_HISTORY_BOOKMARKS`). &ndash;

- *CallLog* &ndash; chamadas recentes feitas ou recebidas com o dispositivo.

- *Contatos* do &ndash; informações detalhadas da lista de contatos do usuário, incluindo pessoas, telefones, fotos & grupos.

- *MediaStore* &ndash; conteúdo do dispositivo do usuário: áudio (álbuns, artistas, gêneros, listas de reprodução), imagens (incluindo miniaturas) & vídeo.

- *Configurações* do &ndash; configurações e preferências do dispositivo de todo o sistema.

- *Userdictionary* &ndash; conteúdo do dicionário definido pelo usuário usado para entrada de texto preditiva.

- *Caixa postal* &ndash; histórico de mensagens de correio de voz.

## <a name="classes-overview"></a>Visão geral de classes

As classes primárias usadas ao trabalhar com `ContentProvider` um são mostradas aqui:

[![Diagrama de classes do aplicativo provedor de conteúdo e interações de aplicativo de consumo](how-it-works-images/classdiagram1.png)](how-it-works-images/classdiagram1.png#lightbox)

Neste diagrama, o implementa `ContentProvider` as consultas e registra os URIs que outros aplicativos usam para localizar dados. O `ContentResolver` atua como um ' proxy ' para os `ContentProvider` métodos (consulta, inserção, atualização e exclusão). O `SQLiteOpenHelper` contém dados usados `ContentProvider`pelo, mas não é exposto diretamente ao consumo de aplicativos.
O `CursorAdapter` passa o cursor retornado `ContentResolver` pelo para exibir em um `ListView`. O `UriMatcher` é uma classe auxiliar que analisa URIs ao processar consultas.

A finalidade de cada classe é descrita abaixo:

- **ContentProvider** &ndash; Implemente os métodos dessa classe abstrata para expor dados. A API é disponibilizada para outras classes e aplicativos por meio do atributo URI que é adicionado à definição de classe.

- **SQLiteOpenHelper** Ajuda a implementar o repositório de armazenamento do SQLite que é `ContentProvider`exposto pelo. &ndash;

- **UriMatcher** &ndash; Use em`UriMatcher` sua`ContentProvider` implementação para ajudar a gerenciar URIs que são usados para consultar o conteúdo.

- **ContentResolver retornem** O consumo de código `ContentResolver` usa um para `ContentProvider` acessar uma instância do. &ndash; As duas classes juntos cuidam dos problemas de comunicação entre processos, permitindo que os dados sejam facilmente compartilhados entre os aplicativos. O consumo de código nunca `ContentProvider` cria uma classe explicita; em vez disso, os dados são acessados criando um cursor com base `ContentProvider` em um URI exposto pelo aplicativo.

- **CursorAdapter** &ndash; `ContentProvider`Use `CursorAdapter` ou paraexibirdadosacessados`SimpleCursorAdapter` por meio de um.

A `ContentProvider` API permite que os consumidores realizem uma variedade de operações nos dados, como:

- Consultar dados para retornar listas ou registros individuais.
- Modificar registros individuais.
- Adicionar novos registros.
- Excluir registros.

Este documento contém um exemplo que usa um fornecido `ContentProvider`pelo sistema, bem como um exemplo simples de somente leitura que implementa um personalizado. `ContentProvider`
