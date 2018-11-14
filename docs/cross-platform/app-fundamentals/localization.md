---
title: Localização de Interface de usuário do aplicativo
description: Este documento descreve os conceitos de plataforma cruzada de internacionalização e localização e examina como elas afetam o design do aplicativo.
ms.prod: xamarin
ms.assetid: CC6847B2-23FB-4EDE-9F7E-EF29DD46A5C5
author: asb3993
ms.author: amburns
ms.date: 03/22/2017
ms.openlocfilehash: 85e4e98cfbc96936c19642a200885b54cf745aa4
ms.sourcegitcommit: 6be6374664cd96a7d924c2e0c37aeec4adf8be13
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2018
ms.locfileid: "51617508"
---
# <a name="localization"></a>Localização

Este guia apresenta os conceitos por trás *internacionalização* e *localização* e links para obter instruções sobre como produzir aplicativos móveis Xamarin com esses conceitos.

Se você quiser pular diretamente para os detalhes técnicos de localização de aplicativos Xamarin, comece com um dos seguintes artigos de instruções específicas da plataforma:

- [**Xamarin. Forms** ](~/xamarin-forms/app-fundamentals/localization/index.md) localização de plataforma cruzada usando arquivos RESX.
- [**Xamarin. IOS** ](~/ios/app-fundamentals/localization/index.md) localização de plataforma nativa.
- [**Xamarin. Android** ](~/android/app-fundamentals/localization.md) localização de plataforma nativa.

## <a name="i18n-and-l10n"></a>I18N e L10n

*Internacionalização* é o processo de tornar seu código capaz de exibir diferentes idiomas e adaptar sua exibição para localidades diferentes (como número e formatação de data). Isso é também conhecido como *globalização*.

*Localização* é a etapa seguinte – criação de recursos (como cadeias de caracteres e imagens) para cada idioma e agrupá-las com o aplicativo internacionalizar.

Internacionalização que é muitas vezes abreviada para i18n – uma abreviação para 18 letras entre "i" e "n". Localização da mesma forma é abreviada para L10n – para 10 letras entre "L" e "n".

## <a name="overview"></a>Visão geral

Este documento apresenta os conceitos associados à internacionalização e localização, e como elas se aplicam em geral ao desenvolvimento de aplicativos móveis.
Ao projetar e criar um aplicativo, as coisas que você pode ter anteriormente embutidos em código mas que deve ser parametrizado para localização incluem:

-   Layouts de tela e texto,
-   Ícones, gráficos e cores,
-   Arquivos de som e vídeo
-   Texto dinâmico e a formatação de texto (como números, moeda e datas),
 - As alterações de layout para idiomas do direita para esquerda (RTL), e
-   Classificação de dados.

Independentemente de qual plataformas móveis, seu aplicativo for destinado a estas dicas ajudarão você compilar um aplicativo localizado de alta qualidade.


## <a name="design-considerations"></a>Considerações de design

Arquitetura de um aplicativo para que seja possível localizar seu conteúdo é chamado de internacionalização. Fazendo a internacionalização corretamente é mais do que apenas permitindo para idiomas diferentes cadeias de caracteres a ser carregado no tempo de execução – um aplicativo bem projetado deve permitir todos os recursos a ser alterada com base no idioma e localidade (incluindo imagens, sons e vídeos) e pode se adaptar formatação e layout para lidar com diferentes dimensionada cadeias de caracteres.

Esta seção aborda algumas considerações de design para ser levadas em conta ao criar um aplicativo internacionalizado.

### <a name="layouts-and-string-length"></a>Layouts e comprimento da cadeia de caracteres

Cadeias de caracteres do chinês e japonês podem ser muito curtas – às vezes, um ou dois caracteres podem ser significativos para um rótulo de campo de entrada.

Alemãs cadeias de caracteres (por exemplo) podem ser muito longos; às vezes, uma palavra relativamente curta em inglês se torna muito longa em outros idiomas – ou se tornando recortadas ou outra coisa inesperadamente refluxo de seu layout.

Compare os comprimentos de cadeia de caracteres para alguns itens na tela inicial do iOS em inglês, alemão e japonês:

[![](localization-images/language-compare-sml.png "Comprimento da cadeia de caracteres japoneses vs alemão")](localization-images/language-compare.png#lightbox)

Observe que **configurações** em inglês (8 caracteres) requer 13 caracteres para a tradução para o alemão, mas só 2 caracteres em japonês.

Layouts de onde o rótulo de exibição e o campo de entrada estão lado a lado são difíceis de trabalhar com quando o tamanho do rótulo pode variar significativamente. Geralmente, um layout no qual o rótulo é exibido acima de um campo é fácil de localizar porque a largura total da tela está disponível para o rótulo e a entrada.

Como regra geral, se você estiver criando layouts fixos (especialmente a elementos de lado a lado) que pelo menos 50% mais largura de suas cadeias de caracteres em inglês exigem para rótulos e texto. Isso não resolve todos os problemas, mas fornecerá um buffer que funcionará em muitos casos.

### <a name="input-validation"></a>Validação de entrada

Tenha cuidado com as suposições ao escrever as regras de validação. Pode parecer válido para exigir que um campo de texto de entrada para "require" pelo menos três caracteres em inglês, como uma única letra muito raramente tem qualquer significado. Em chinês e japonês no entanto, um único caractere pode ser uma entrada válida e uma validação de mensagem "pelo menos 3 caracteres é necessária" não faz sentido para esses idiomas.

Outras tarefas aparentemente simples, como validar um endereço de email ou URL do site se tornar mais complicada com os caracteres não está limitado ao subconjunto ASCII.

Escreva suas regras de validação com internacionalização, lembre-se – escolha as regras de menos restritivas, ou escrever a lógica para que ele funcione de maneira diferente para cada idioma.

### <a name="images-and-color"></a>Imagens e cores

Nem toda imagem precisa ser alterado com base na preferência de idioma do usuário. Vários ícones ou fotos será adequado para todos os usuários, não importa qual linguagem que elas falam.
Alguns recursos fazem sentido para localizar no entanto, tais como:

 - Imagens que descrevam as pessoas ou locais específicos – seu aplicativo pode se sentir mais relevante para os usuários se ele mostra o local de pessoas/locais.
 - Ícones – alguns iconografia pode ser específicas da cultura e você pode tornar mais fácil de usar Localizando as imagens para refletir o local Noções básicas sobre seu aplicativo.
 - Cores – algumas culturas entender as cores de forma diferente – vermelho pode significar aviso em uma região, mas a boa sorte em outro. Verifique com alto-falantes nativos ao projetar seu aplicativo para determinar se você deve criar um mecanismo para localizar as cores.


### <a name="videos-and-sound"></a>Vídeos e som

Vídeos e som desafios especiais presentes na localização de um aplicativo, porque embora seja relativamente fácil de obter cadeias de caracteres convertidas, registrar vários voiceover controla ou clipes de vídeo pode ser caro e difícil.

Várias cópias dos arquivos de som e vídeos significativamente também podem aumentar o tamanho do seu aplicativo (especialmente se você estiver localizando em um grande número de idiomas ou tiver muitos arquivos de mídia). Você pode considerar baixando somente os ativos de idiomas necessários depois que o usuário instalou em seu aplicativo, mas isso também pode resultar em uma experiência ruim para o usuário em redes lentas.

Frequentemente, há várias maneiras de solucionar problemas de localização – a coisa mais importante é considerá-las inicial e garantir que seu aplicativo foi projetado para cuidar deles.


### <a name="dates-times-numbers-and-currency"></a>Datas, horas, números e moeda

Se você estiver usando funções de formatação do .NET, lembre-se de especificar a cultura para que os separadores decimais são analisados corretamente (e evitar exceções de conversão que está sendo lançadas). Por exemplo, 1.99 e 1,99 são representações decimais válidas, dependendo de sua localidade.

Quando os dados forem provenientes de uma fonte conhecida (ie. de seu próprio código ou um serviço web que você controle) você pode codificar um identificador de cultura que corresponda à formatação, como InvariantCulture que funcionará para formatação de idioma do inglês padrão.

```csharp
double.Parse("1,999.99", CultureInfo.InvariantCulture);
```

Se a dados está sendo inseridos pelo usuário do aplicativo, analisá-lo usando uma instância CultureInfo que reflete a localidade a:

```csharp
double.Parse("1 999,99", CultureInfo.CreateSpecificCulture("fr-FR"));
```

Consulte a [Analisando cadeias de caracteres numéricas](http://msdn.microsoft.com/library/xbtzcc4w(v=vs.110).aspx) e [Analisando cadeias de data e hora](http://msdn.microsoft.com/library/2h3syy57(v=vs.110).aspx) artigos do MSDN para obter mais informações.

<a name="rtl" />

### <a name="right-to-left-rtl-languages"></a>Idiomas da direita para esquerda (RTL)

Algumas linguagens, como árabe, hebraico e Urdu (por exemplo), são lidos da direita para esquerda.
Os aplicativos que dão suporte a esses idiomas devem usar designs de tela que se adaptam para os leitores da direita para esquerda, por exemplo:

 - Texto deve ser alinhado à direita.
 - Rótulos devem aparecer à direita dos campos de entrada.
 - Posicionamento de botão padrão geralmente é revertido.
 - Passar o dedo Navegação hierárquica e animação (e outros metáforas de navegação e animações) que usam a direção de contexto também deve ser invertido.

IOS e Android dão suporte a layouts da direita para esquerda e a renderização da fonte, com recursos internos que ajudam a fazer os ajustes acima. Automaticamente no momento, o xamarin. Forms não oferece suporte para a renderização de RTL.

### <a name="sorting"></a>Classificação

Idiomas diferentes definem a ordem de classificação dos seus alfabetos diferente, mesmo quando eles usam o mesmo conjunto de caracteres.

Consulte a [detalhes de comparação de cadeia de caracteres](http://msdn.microsoft.com/library/dd465121(v=vs.110).aspx#the_details_of_string_comparison) na [práticas recomendadas para usar cadeias de caracteres no .NET Framework](http://msdn.microsoft.com/library/dd465121(v=vs.110).aspx) para obter um exemplo em que o idioma (CultureInfo) afeta a ordem de classificação.

É improvável que os recursos internos do banco de dados em plataformas móveis dará suporte a ordenação para que você talvez precise implementar código adicional em sua lógica de negócios de classificação de idioma específico.

### <a name="text-search"></a>Pesquisa de texto

Verifique se você escrever e testar seu algoritmo de pesquisa com vários idiomas em mente. Pontos a serem considerados incluem:

 - Preenchimento automático – se você tiver criado uma função de preenchimento automático garantem a fontes de sugestões relevantes para o idioma do usuário.
 - A consulta correspondente aos dados – pesquisará consultas inseridas em uma determinada linguagem ser executadas em relação ao conteúdo apenas, escritos nessa linguagem, ou em relação a todo o conteúdo em seu aplicativo?
 - Lematização – se a pesquisa é criada para procurar palavras semelhantes, raízes do word e outras otimizações de pesquisa, é essas otimizações criadas para todos os idiomas que você oferece suporte?
 - Classificação – Verifique se os resultados são classificados corretamente (consulte acima).


### <a name="data-from-external-sources"></a>Dados de fontes externas

Muitos aplicativos baixar dados de fontes externas, do Twitter e RSS feeds ao clima, notícias ou preços de ações. Ao exibir isso a um usuário, você precisa considerar a possibilidade de que exibirá uma tela de informações irrelevantes ou ilegíveis para eles.

Há algumas estratégias que você pode usar para tentar e garantir que seu aplicativo exibe os dados relevantes para o usuário:

 - Fontes diferentes – seu aplicativo pode baixar os dados de uma fonte diferente dependendo do idioma ou localidade do usuário. Preços de notícias, clima e estoque de localidade podem fazer mais sentido que algo baixado de um feed na América do Norte.
 - Modo de exibição localizado – se você estiver exibindo um Twitter ou uma foto do feed, você deve exibir os metadados (como o tempo gasto) em seu próprio idioma, mesmo se o conteúdo em si permanece no idioma original.
 - Tradução – você pode criar uma opção de conversão em seu aplicativo faça uma tradução de dados de entrada. Isso pode ser automático ou a critério do usuário – não se esqueça de notificar o usuário se isso estiver ocorrendo, já que as traduções de máquina nunca são perfeitas!

Isso também pode afetar os links externos para faixas de áudio ou vídeos – ao projetar seu aplicativo não se esqueça de planejar com antecedência para fornecimento traduzida conteúdo ou garantir que os usuários serão informados adequadamente pela interface do usuário quando o conteúdo não aparecerá na sua idioma.


### <a name="dont-over-translate"></a>Não se convertem em excesso

Algumas cadeias de caracteres em seu aplicativo podem não precisar converter ou na pior das hipóteses precisam de atenção especial pelo conversor. Os exemplos podem incluir:

 - URLs – se você listar uma URL, ele pode ou talvez não precise ser ajustado por idioma. Por exemplo, facebook.com não requer conversão ele detecta automaticamente o idioma no site principal. Outros sites possuem conteúdo específico da localidade e você talvez queira oferecer uma URL diferente, como yahoo.com versus yahoo.fr ou yahoo.it.
 - Números de telefone – especialmente aqueles com códigos de país diferentes ou números para chamadores que falam um determinado idioma.
 - Detalhes de contato – endereços e outras informações podem variar por idioma ou localidade.
 - Marcas comerciais e nomes de produto – algumas cadeias de caracteres não é necessário converter porque eles sempre são escritos no mesmo idioma.

Por fim, certifique-se de incluir instruções detalhadas para o conversor, se determinadas cadeias de caracteres exigem tratamento especial.


### <a name="formatted-text"></a>Texto formatado

Não costumam ser um problema com aplicativos móveis porque cadeias de caracteres geralmente ricamente não estão formatadas. No entanto se RTF (como a formatação em negrito ou itálico) é necessária em seu aplicativo garantir que o tradutor sabe como para a formatação de entrada, os arquivos de cadeias de caracteres armazená-lo corretamente e está formatado corretamente antes de serem exibidos ao usuário (ie. não deixe acidentalmente os códigos de formatação em si ser apresentada ao usuário).



## <a name="translation-tips"></a>Dicas de tradução

Converter as cadeias de caracteres usadas por um aplicativo é considerado parte do processo de localização. Normalmente essa tarefa será terceirizada para um serviço de tradução e executada pela equipe multilíngue que talvez não saiba seu aplicativo ou seus negócios.

As dicas a seguir o ajudará a produzir cadeias de caracteres que são mais fáceis de traduzir com precisão e, portanto, melhorar a qualidade dos seus aplicativos localizados.



### <a name="localize-complete-strings-not-words"></a>Localizar cadeias de caracteres completas, e não de palavras

Às vezes, os desenvolvedores tirar a abordagem de tentativa de especificar palavras individuais ou a frase 'trechos de código' para que eles podem usá-los novamente em todo o aplicativo. Por exemplo, para o texto "você tem 5 mensagens." eles podem especificar seguintes cadeias de caracteres para tradução

**Ruim**:

```csharp
"You have"
"no"
"message"
"messages"
```

e, em seguida, tentar criar frase correta dinamicamente no código usando a concatenação de cadeia de caracteres:

**Ruim**:

```csharp
"You have" + " " + numMsgs + " " + "messages"
"You have" + " no " + "messages"
```

**Isso não é recomendável** porque ele não necessariamente funcionará para todos os idiomas e será difícil para o conversor compreender o contexto de cada segmento curto. Isso também leva a reutilização de cadeias de caracteres traduzidas, que podem causar problemas posteriormente se eles são usados em contextos diferentes (e, em seguida, são atualizados).


### <a name="allow-for-parameter-re-ordering"></a>Permitir a reordenação de parâmetro

Algumas linguagens de programação exigem sintaxe extra para especificar a ordem dos parâmetros em uma cadeia de caracteres, no entanto, .NET já suporta o conceito de espaços reservados numerados, portanto

**Boa**:

```csharp
"a {0} b {1} cde {3}"
```

pode ser convertida a seguir (em que a posição e a ordem dos espaços reservados é alterada)

```csharp
"{2} {3} f g h {0}"
```

e os tokens serão ordenados como o conversor que se destina. Certifique-se de incluir uma explicação do que contém cada espaço reservado ao enviar a cadeia de caracteres para um tradutor.


### <a name="use-multiple-strings-for-cardinality"></a>Usar várias cadeias de caracteres de cardinalidade

Evite cadeias de caracteres como `"You have {0} message/s."` usar cadeias de caracteres específicas para cada estado para proporcionar uma melhor experiência de usuário:

**Boa**:

```csharp
"You have no messages."
"You have 1 messages."
"You have 2 messages."
"You have {0} messages."
```

Você precisará escrever código em seu aplicativo para avaliar o número que está sendo exibido e escolha a cadeia de caracteres apropriada. Algumas plataformas (incluindo iOS e Android) tem recursos internos para escolher automaticamente a cadeia de caracteres no plural práticas com base nas preferências de idioma/localidade atual.


### <a name="allowing-for-gender"></a>Permitindo sexo

Às vezes, idiomas latinos usam palavras diferentes, dependendo do gênero do assunto. Se seu aplicativo sabe sobre sexo, você deve permitir que as cadeias de caracteres traduzidas refletir isso.

Também é o caso mais óbvio até mesmo em inglês, em que as cadeias de caracteres se referem a uma pessoa específica ou o usuário de seu aplicativo. Por exemplo, alguns sites mostram mensagens como `"Bob commented on his post"` para que você precisa de cadeias de caracteres para um sexo Masculino, feminino e não binários ou desconhecido:

**Boa**:

```csharp
"{0} commented on his post"
"{0} commented on her post"
"{0} commented on their post"
```

### <a name="dont-reuse-strings"></a>Não reutilize cadeias de caracteres

Ou, mais precisamente, não reutilize cadeias de caracteres apenas porque elas são semelhantes quando a cadeia de caracteres em si tem uma finalidade diferente ou significado.

Por exemplo: imagine que você tem uma chave liga/desliga em seu aplicativo e o controle de switch precisa o texto para 'on' e 'off' deve ser localizado. Você também exibir o valor dessa configuração em outro lugar no aplicativo em um rótulo de texto. Você deve usar cadeias de caracteres diferentes para a exibição do comutador em comparação com o status da opção (mesmo se eles forem a mesma cadeia de caracteres em seu idioma padrão) – por exemplo:

-   "Ligado" – exibido no comutador em si
-   "Off" – exibidas no comutador em si
-   "Ligado" – exibido em um rótulo
-   "Off" – exibidas em um rótulo

Isso fornece flexibilidade máxima para a tradução:

-   Por motivos de design, talvez a própria opção Use letras minúsculas de "on" e "desativado", mas o rótulo de exibição usa letras maiusculas "On" e "Desativado".
-   Algumas linguagens talvez seja necessário que o valor da opção deve ser abreviado para caber no controle de interface do usuário, enquanto a completar palavra (traduzida) pode aparecer no rótulo.
-   Como alternativa, para alguns idiomas a renderização de seu comutador pode ser usar "I" e "O" para cultura familiaridade, mas você ainda poderá ser necessário o rótulo a ser lido "On" ou "Desativado".

### <a name="translation-services"></a>Serviços de tradução

#### <a name="machine-translation"></a>Tradução automática

Para criar recursos de tradução em seu aplicativo, considere a [Azure API de tradução de texto](https://azure.microsoft.com/services/cognitive-services/translator-text-api/).

Para fins de teste, você pode usar uma das diversas ferramentas de tradução online para incluir algum texto localizado em seu aplicativo durante o desenvolvimento:

- [Bing Translator](https://www.bing.com/translator/)
- [Converter do Google](http://translate.google.com/)

Há muitos outros disponíveis. A qualidade da tradução automática geralmente não é considerada bom o suficiente lançar um aplicativo sem primeiro o que está sendo revisado e testado por tradutores profissionais ou alto-falantes nativos.

#### <a name="professional-translation"></a>Tradução profissional

Também há serviços de tradução profissional que usem suas cadeias de caracteres e distribuí-los aos seus próprios conversores, fornecendo a você terminar de traduções para uma taxa.

Um dos mais famosos serviços é [LionBridge](http://www.lionbridge.com/). Serviços profissionais mais suportam a todos os tipos de arquivo comuns incluindo cadeias de caracteres, XML, RESX e POT/OC.


## <a name="summary"></a>Resumo

Este artigo apresentou alguns dos conceitos que você deve estar familiarizado antes de internacionalização de seu aplicativo e, em seguida, a localização de seus recursos e também abordou como alterar as preferências de idioma para cada plataforma.

Esses conceitos podem ser aplicados às várias técnicas de internacionalização específicos da plataforma e de plataforma cruzada que são possíveis com o Xamarin.

Continue a ler os detalhes técnicos para a plataforma que você está interessado:

- [Xamarin. Forms](~/xamarin-forms/app-fundamentals/localization/index.md) localização de plataforma cruzada usando arquivos RESX.
- [Xamarin. IOS](~/ios/app-fundamentals/localization/index.md) localização de plataforma nativa.
- [Xamarin. Android](~/android/app-fundamentals/localization.md) localização de plataforma nativa.



## <a name="related-links"></a>Links relacionados

- [Visão geral de localização da Apple](https://developer.apple.com/internationalization/)
- [Lista de verificação de localização do Android](http://developer.android.com/distribute/tools/localization-checklist.html)
- [Práticas recomendadas para o desenvolvimento de aplicativos do mundo (MSDN)](http://msdn.microsoft.com/library/w7x1y988%28v=vs.90%29.aspx)
