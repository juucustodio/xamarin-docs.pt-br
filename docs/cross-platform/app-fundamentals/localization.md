---
title: "Localização de Interface de usuário do aplicativo"
ms.topic: article
ms.prod: xamarin
ms.assetid: CC6847B2-23FB-4EDE-9F7E-EF29DD46A5C5
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/22/2017
ms.openlocfilehash: 510e8a6b0b2839a1a191538e7fb4e49bd005b450
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="localization"></a>Localização

Este guia apresenta os conceitos por trás de *internacionalização* e *localização* e links para obter instruções sobre como gerar aplicativos móveis do Xamarin usando esses conceitos.

Se você quiser vá direto para os detalhes técnicos de localização de aplicativos Xamarin, começar com uma desses artigos de instrução específico da plataforma:

- [**Xamarin. Forms** ](~/xamarin-forms/app-fundamentals/localization.md) localização de plataforma cruzada usando arquivos RESX.
- [**Xamarin** ](~/ios/app-fundamentals/localization/index.md) localização de plataforma nativo.
- [**Xamarin** ](~/android/app-fundamentals/localization.md) localização de plataforma nativo.

## <a name="i18n-and-l10n"></a>I18N e L10n

*Internacionalização* é o processo de tornar o seu código capaz de exibir diferentes idiomas e adaptar sua exibição para localidades diferentes (como número e a formatação de data). Isso é também conhecido como *globalização*.

*Localização* é a etapa seguinte – criação de recursos (como cadeias de caracteres e imagens) para cada idioma e agrupamento-los com o aplicativo internacionalizar.

Internacionalização que ele geralmente é reduzido a i18n – abreviação para 18 letras entre "i" e "n". Localização da mesma forma é abreviada para L10n – para 10 letras entre "L" e "n".

## <a name="overview"></a>Visão geral

Este documento apresenta os conceitos associados à internacionalização e localização, e como elas se aplicam em geral para desenvolvimento de aplicativos móveis.
Ao projetar e criar um aplicativo, as coisas que você pode ter anteriormente fixo, mas que deve parametrizadas para localização incluem:

-   Layouts de tela e texto,
-   Ícones, gráficos e cores,
-   Vídeo e arquivos de som
-   Texto dinâmico e formatação de texto (como números, moeda e datas),
 - Alterações de layout para idiomas do direita para esquerda (RTL), e
-   Classificação de dados.

Independentemente de qual plataformas móveis, seu aplicativo tem como alvo estas dicas ajudarão você compilar um aplicativo localizado de alta qualidade.


## <a name="design-considerations"></a>Considerações de design

A arquitetura de um aplicativo para que seja possível localizar seu conteúdo é chamada de internacionalização. Internacionalização corretamente é mais do que apenas permitir para idiomas diferentes cadeias de caracteres a ser carregado no tempo de execução – um aplicativo bem projetado deve permitir todos os recursos a serem alterados com base no idioma e localidade (incluindo imagens, sons e vídeos) e pode adaptar formatação e layout de lidar com diferentes dimensionado cadeias de caracteres.

Esta seção aborda algumas considerações de design a ser levada em conta ao criar um aplicativo internacionalizado.

### <a name="layouts-and-string-length"></a>Comprimento de cadeia de caracteres e layouts

Cadeias de caracteres do chinês e japonês podem ser muito curtas – às vezes, um ou dois caracteres podem ser significativos para um rótulo de campo de entrada.

Cadeias de caracteres Alemanha (por exemplo) podem ser muito longos. às vezes, uma palavra relativamente curta em inglês torna-se muito em outros idiomas – ou se tornar cortado ou else inesperadamente refluxo seu layout.

Compare os comprimentos de cadeia de caracteres para alguns itens na tela inicial do iOS em inglês, alemão e japonês:

[![](localization-images/language-compare-sml.png "Comprimento de cadeia de caracteres japoneses vs alemão")](localization-images/language-compare.png#lightbox)

Observe que **configurações** em inglês (8 caracteres) requer 13 caracteres para a tradução para o alemão, mas só 2 caracteres em japonês.

Layouts de onde o rótulo de exibição e o campo de entrada estão lado a lado são difíceis de trabalhar com quando o tamanho do rótulo pode variar significativamente. Geralmente, um layout em que o rótulo é exibido acima de um campo é fácil de localizar porque a largura total da tela está disponível para o rótulo e a entrada.

Como regra geral, se você estiver criando layouts fixos (especialmente elementos lado a lado) que pelo menos 50% requerem mais largura de suas cadeias de caracteres em inglês para rótulos e texto. Isso não resolve todos os problemas, mas fornece um buffer que funcione em muitos casos.

### <a name="input-validation"></a>Validação de entrada

Tenha cuidado com considerações quando escrever regras de validação. Pode parecer válido para exigir um campo de texto de entrada "exigir" pelo menos três caracteres em inglês, como uma única letra muito raramente tem qualquer significado. Em chinês e japonês no entanto, um único caractere pode ser uma entrada válida e uma validação de mensagem "pelo menos 3 caracteres é necessária" não faz sentido para esses idiomas.

Outras tarefas aparentemente simples como validar um endereço de email ou URL do site se tornam mais complicado com os caracteres não está limitado ao subconjunto de ASCII.

Grave suas regras de validação com internacionalização em mente, escolha as regras menos restritivas, ou escrever a lógica de forma que funciona de forma diferente para cada idioma.

### <a name="images-and-color"></a>Imagens e cores

Nem toda imagem precisa ser alterado com base na escolha do idioma do usuário. Muitos ícones ou fotos será adequado para todos os usuários, não importa qual idioma relatado.
Alguns recursos fazem sentido para localizar, tais como:

 - Imagens ilustrando pessoas ou locais específicos – seu aplicativo pode achar mais relevante para os usuários se ele mostra o local de pessoas/locais.
 - Ícones – alguns iconografia pode ser específicos da cultura e você pode tornar seu aplicativo mais fácil de usar Localizando as imagens para refletir as Noções básicas sobre local.
 - Cores – algumas culturas entender cores diferente – vermelho pode significar aviso em uma região, mas boa sorte em outro. Verifique com alto-falantes nativos ao projetar seu aplicativo para determinar se você deve criar um mecanismo para localizar as cores.


### <a name="videos-and-sound"></a>Vídeos e som

Vídeos e som desafios especiais presentes na localização de um aplicativo, porque embora seja relativamente fácil de obter as cadeias de caracteres traduzidas, registrar vários voiceover rastreia ou videoclipes podem ser caro e difícil.

Várias cópias de arquivos de vídeo e som significativamente também podem aumentar o tamanho do seu aplicativo (especialmente se você está localizando um grande número de idiomas ou tiver uma grande quantidade de arquivos de mídia). Convém baixar somente os recursos de idioma necessário depois que o usuário instalou em seu aplicativo, mas isso também pode resultar em uma experiência de usuário ruim em redes lentas.

Geralmente, há várias maneiras de solucionar problemas de localização – o mais importante é considerar inicial e certifique-se de que seu aplicativo é projetado para lidar com eles.


### <a name="dates-times-numbers-and-currency"></a>Datas, horas, números e moeda

Se você estiver usando funções de formatação do .NET, lembre-se de especificar a cultura para que os separadores decimais são analisados corretamente (e evitar exceções de conversão que está sendo lançadas). Por exemplo, 1.99 e 1,99 são representações decimais válidas dependendo de sua localidade.

Quando os dados estão vindo de uma origem conhecida (ie. de seu próprio código ou um serviço web que você controle) você pode codificar um identificador de cultura que corresponde a formatação, como InvariantCulture que funcionará para formatação do idioma inglês padrão.

```csharp
double.Parse("1,999.99", CultureInfo.InvariantCulture);
```

Se os dados forem sendo de entrada do usuário do aplicativo, analisá-lo usando uma instância CultureInfo que reflete a localidade:

```csharp
double.Parse("1 999,99", CultureInfo.CreateSpecificCulture("fr-FR"));
```

Consulte o [Analisando cadeias de caracteres numéricas](http://msdn.microsoft.com/en-us/library/xbtzcc4w(v=vs.110).aspx) e [análise Date and Time Strings](http://msdn.microsoft.com/en-us/library/2h3syy57(v=vs.110).aspx) artigos do MSDN para obter informações adicionais.

<a name="rtl" />

### <a name="right-to-left-rtl-languages"></a>Idiomas da direita para esquerda (RTL)

Alguns idiomas, como árabe e hebraico Urdu (por exemplo), são lidos da direita para a esquerda.
Aplicativos que dão suporte a esses idiomas devem usar designs de tela que se adaptar para leitores da direita para esquerda, por exemplo:

 - Texto deve ser alinhado à direita.
 - Rótulos devem aparecer à direita dos campos de entrada.
 - Posicionamento de botão padrão geralmente é revertido.
 - Passar o dedo Navegação hierárquica e animação (e outros metáforas de navegação e animações) que usam a direção de contexto também deve ser revertido.

IOS e Android suportam a layouts da direita para esquerda e renderização de fonte, com recursos internos que ajudam a tornar os ajustes acima. Xamarin. Forms automaticamente no momento não oferece suporte para renderização RTL.

### <a name="sorting"></a>Classificação

Idiomas diferentes definem a ordem de classificação de seus alfabetos diferente, mesmo quando eles usam o mesmo conjunto de caracteres.

Consulte o [detalhes de comparação de cadeia de caracteres](http://msdn.microsoft.com/en-us/library/dd465121(v=vs.110).aspx#the_details_of_string_comparison) na [práticas recomendadas para usar cadeias de caracteres no .NET Framework](http://msdn.microsoft.com/en-us/library/dd465121(v=vs.110).aspx) para obter um exemplo onde o idioma (CultureInfo) afeta a ordem de classificação.

É improvável que os recursos de banco de dados internos nas plataformas móveis dará suporte a ordenação para que pode ser necessário para implementar o código adicional em sua lógica de negócios de classificação de idioma específico.

### <a name="text-search"></a>Pesquisa de texto

Certifique-se de escrever e testar seu algoritmo de pesquisa com vários idiomas em mente. Itens a serem considerados incluem:

 - Preenchimento automático – se você criou uma função de preenchimento automático Certifique-se de fontes de sugestões relevantes para o idioma do usuário.
 - Consulta correspondente aos dados – pesquisará consultas inseridas em um determinado idioma ser executado apenas conteúdo gravado nesse idioma, ou em relação a todo o conteúdo em seu aplicativo?
 - Lematização – se a pesquisa é criada para procurar palavras semelhantes, as raízes de palavra e outras otimizações de pesquisa, tem essas otimizações criadas para todos os idiomas que você oferece suporte?
 - Classificação – Verifique se os resultados são classificados corretamente (veja acima).


### <a name="data-from-external-sources"></a>Dados de fontes externas

Muitos aplicativos baixar dados de fontes externas, do Twitter e RSS feeds clima, notícias ou os preços de estoque. Ao exibir isso a um usuário que você precisa considerar a possibilidade de que você exibirá uma tela de informações irrelevantes ou ilegíveis para eles.

Há algumas estratégias que você pode usar para testar e certifique-se de que seu aplicativo exibe os dados relevantes para o usuário:

 - Fontes diferentes – seu aplicativo pode baixar os dados de uma fonte diferente dependendo do idioma ou localidade do usuário. Os preços de notícias, clima e estoque localidade podem fazer sentido mais que algo baixado de um feed de América do Norte.
 - Exibição localizado – se você estiver exibindo um Twitter ou uma foto feed, você deve exibir os metadados (como o tempo gasto) em seu próprio idioma, mesmo que o próprio conteúdo permaneça no idioma original.
 - Tradução – você pode criar uma opção de conversão em seu aplicativo para fazer uma tradução de dados de entrada. Isso pode ser automático ou a critério do usuário – apenas certifique-se notificar o usuário se isso estiver ocorrendo, como conversões de máquina nunca são perfeitos!

Isso também pode afetar os links externos para faixas de áudio ou vídeos – ao projetar seu aplicativo Lembre-se de planejar com antecedência para fornecimento traduzida conteúdo ou para garantir que os usuários serão informados adequadamente pela interface do usuário quando o conteúdo não aparecerá na sua idioma.


### <a name="dont-over-translate"></a>Excesso não traduzir

Algumas cadeias de caracteres em seu aplicativo não podem precisa converter ou pelo menos precisam de atenção especial, o conversor. Os exemplos podem incluir:

 - URLs – se você listar uma URL, ele pode ou não precisa ser ajustado por idioma. Por exemplo, facebook.com não requer conversão ele detecta automaticamente o idioma no site principal. Outros sites têm conteúdo específico de localidade e que talvez você queira oferecer uma URL diferente, como yahoo.com versus yahoo.fr ou yahoo.it.
 - Números de telefone – especialmente aqueles com códigos de países diferentes ou números para chamadores que falam um determinado idioma.
 - Detalhes de contato – endereços e outras informações podem variar por idioma ou localidade.
 - Marcas comerciais e nomes de produtos – algumas cadeias de caracteres não é necessário converter porque eles sempre estão gravados no mesmo idioma.

Por fim, certifique-se de incluir instruções detalhadas para o conversor se determinadas cadeias de caracteres requerem tratamento especial.


### <a name="formatted-text"></a>Texto formatado

Geralmente não é um problema com aplicativos móveis como cadeias de caracteres geralmente ricamente não são formatadas. No entanto se RTF (como formatação em negrito ou itálico) é necessário em seu aplicativo garantir o tradutor saberá como para a formatação de entrada, os arquivos de cadeias de caracteres armazená-lo corretamente e está formatado corretamente antes de ser exibido para o usuário (ie. acidentalmente não permitem os códigos de formatação próprios ser apresentada ao usuário).



## <a name="translation-tips"></a>Dicas de tradução

Converter as cadeias de caracteres usadas por um aplicativo é considerado parte do processo de localização. Normalmente essa tarefa será terceirizada para um serviço de tradução e executada pela equipe multilíngue que talvez não saiba seu aplicativo ou sua empresa.

As dicas a seguir o ajudará a produzir cadeias de caracteres que são mais fáceis de traduzir com precisão e, portanto, melhorar a qualidade de seus aplicativos localizados.



### <a name="localize-complete-strings-not-words"></a>Localizar cadeias de caracteres completas, não palavras

Às vezes, os desenvolvedores de usam a abordagem de tentar especificar palavras únicas ou sentença 'trechos de código' para que eles podem usá-los novamente em todo o aplicativo. Por exemplo, para o texto "você tem 5 mensagens." eles podem especificar seguintes cadeias de caracteres de conversão

**Bad**:

```csharp
"You have"
"no"
"message"
"messages"
```

e, em seguida, tentar criar frase correta dinamicamente no código usando a concatenação de cadeia de caracteres:

**Bad**:

```csharp
"You have" + " " + numMsgs + " " + "messages"
"You have" + " no " + "messages"
```

**Isso não é recomendável** porque ele não funcionará necessariamente para todos os idiomas e será difícil para o conversor compreender o contexto de cada segmento curto. Também leva a reutilização de cadeias de caracteres traduzidas, que podem causar problemas mais tarde se eles são usados em contextos diferentes (e, em seguida, são atualizados).


### <a name="allow-for-parameter-re-ordering"></a>Permitir reordenação de parâmetro

Algumas linguagens de programação exigem extra sintaxe para especificar a ordem dos parâmetros em uma cadeia de caracteres, mas .NET já oferece suporte ao conceito de espaços reservados numerados, portanto

**Boa**:

```csharp
"a {0} b {1} cde {3}"
```

pode ser convertida a seguir (onde a posição e a ordem dos espaços reservados é alterada)

```csharp
"{2} {3} f g h {0}"
```

e os tokens serão ordenados como o tradutor pretendido. Certifique-se de incluir uma explicação do que contém cada espaço reservado ao enviar a cadeia de caracteres para um conversor.


### <a name="use-multiple-strings-for-cardinality"></a>Use várias cadeias de caracteres de cardinalidade

Evitar cadeias de caracteres como `"You have {0} message/s."` usar cadeias de caracteres específicas para cada estado para fornecer uma melhor experiência de usuário:

**Boa**:

```csharp
"You have no messages."
"You have 1 messages."
"You have 2 messages."
"You have {0} messages."
```

Você precisará escrever o código em seu aplicativo para avaliar o número que está sendo exibido e escolha a cadeia de caracteres apropriada. Algumas plataformas (incluindo iOS e Android) tem recursos internos para escolher automaticamente a cadeia de caracteres no plural melhor com base nas preferências de idioma/localidade atual.


### <a name="allowing-for-gender"></a>Permitindo sexo

Idiomas latinos, às vezes, usam palavras diferentes dependendo do sexo do assunto. Se seu aplicativo conhece sexo, você deve permitir que as cadeias de caracteres traduzidas refletir isso.

Também é o caso mais óbvio até mesmo em inglês, onde as cadeias de caracteres se referem a uma pessoa específica ou o usuário do seu aplicativo. Por exemplo, alguns sites mostram mensagens como `"Bob commented on his post"` para que você precisa de cadeias de caracteres para um sexo Masculino, feminino e não binários ou desconhecido:

**Boa**:

```csharp
"{0} commented on his post"
"{0} commented on her post"
"{0} commented on their post"
```

### <a name="dont-reuse-strings"></a>Não reutilizar cadeias de caracteres

Ou, mais precisamente, não reutilize cadeias de caracteres apenas porque eles são semelhantes quando a cadeia de caracteres tem uma finalidade diferente ou ao significado.

Por exemplo: imagine que você tem uma chave liga/desliga em seu aplicativo e o controle de comutador precisa o texto para 'em' e 'off' a ser localizada. Também é exibir o valor de configuração em outro lugar no aplicativo em um rótulo de texto. Você deve usar cadeias de caracteres diferentes para a exibição de comutador em comparação com o status do comutador (mesmo se eles forem a mesma cadeia de caracteres em seu idioma padrão) – por exemplo:

-   "Em" – exibido no switch
-   "Off" – exibidas no switch
-   "Em" – exibido em um rótulo
-   "Off" – exibidas em um rótulo

Isso proporciona flexibilidade máxima para o conversor:

-   Por razões de design, talvez o próprio switch usa minúsculas "on" e "desativado" mas o rótulo de exibição usa letras maiusculas "On" e "Desativado".
-   Alguns idiomas talvez seja necessário que o valor da opção deve ser abreviado para caber no controle de interface do usuário, enquanto a completar palavra (traduzida) pode aparecer no rótulo.
-   Como alternativa, para alguns idiomas o processamento de seu comutador pode ser usar "I" e "O" para familiaridade cultura, mas ainda poderá ser necessário o rótulo a ser lido "On" ou "Off".

<!--
# Testing

Once you’ve build and localized your app, you’ll want to be able to test. That means setting your emulator/simulator or device to use another locale or language.

> [!IMPORTANT]
> **WARNING:** Be careful when you set your device to a language you cannot read, as you may not be able to navigate the menu system to return it to your native language!


## iOS

Use Settings.app to switch the language and locale of the iOS Simulator or an iOS device.

On the iOS Simulator you can use the Reset Content and Settings menu item (if the device is in a foreign language and you can’t navigate back to your native tongue).

![]( "ios settings to change language")

## Android

To change the locale on a device

**Home > Menu > Settings > **

Then depending on Android version

**Locale & text > Select locale**

or

**Language & Input > Select language**

![]( "android settings to change language")

When you are testing on the emulator, you can navigate using the settings app as above, or you can reset the locale using the ADB tool command. Using Command Prompt on Windows or Terminal on OS X, start `adb shell` then send commands to set the emulator’s locale. **adb** can usually be found on the Mac in `/Users/YOURNAME/Library/Developer/Xamarin/android-sdk-mac_x86/platform-tools/adb`

### Spanish (Mexico)
setprop persist.sys.language es;setprop persist.sys.country MX;stop;sleep 5;start

### French (France)
setprop persist.sys.language fr;setprop persist.sys.country FR;stop;sleep 5;start

### Japanese (Japan)
setprop persist.sys.language ja;setprop persist.sys.country JP;stop;sleep 5;start

### Portuguese (Brazil)
setprop persist.sys.language pt;setprop persist.sys.country BR;stop;sleep 5;start

### English (USA)
setprop persist.sys.language en;setprop persist.sys.country US;stop;sleep 5;start

**TIP:** the default location of ADB on Mac OS X is
`/Users/[USERNAME]/Library/Developer/Xamarin/android-sdk-mac_x86/platform-tools/adb shell`

-->


### <a name="translation-services"></a>Serviços de tradução

#### <a name="machine-translation"></a>Tradução automática

Para criar recursos de conversão em seu aplicativo, considere o [API do Azure conversor texto](https://azure.microsoft.com/en-au/services/cognitive-services/translator-text-api/).

Para fins de teste, você pode usar uma das diversas ferramentas de tradução online para incluir um texto localizado em seu aplicativo durante o desenvolvimento:

- [Bing Translator](https://www.bing.com/translator/)
- [Traduzir do Google](http://translate.google.com/)

Há muitas outras disponíveis. A qualidade da tradução automática geralmente não é considerada bom o bastante lançar um aplicativo sem primeiro sendo revisado e testado por tradutores professional ou alto-falantes nativos.

 <!--Microsoft's Multilingual Application Toolkit helps you automatically translate strings, and is demonstrated with Xamarin.Forms in [this sample]().-->

#### <a name="professional-translation"></a>Tradução profissional

Também há serviços de tradução profissional que se suas cadeias de caracteres e distribuí-los aos seus próprios tradutores, fornecendo a você terminar de traduções para uma taxa.

Um dos mais conhecidos serviços é [LionBridge](http://www.lionbridge.com/). Serviços profissionais mais suportam a todos os tipos de arquivo comuns incluindo cadeias de caracteres, XML, RESX e POTE/PO.


## <a name="summary"></a>Resumo

Este artigo introduziu alguns dos conceitos que você deve se familiarizar antes de internacionalização do seu aplicativo e, em seguida, localizando seus recursos e também abordou como alterar as preferências de idioma para cada plataforma.

Esses conceitos podem ser aplicados às várias técnicas de internacionalização de plataforma e de plataforma cruzada que são possíveis com o Xamarin.

Continue a ler os detalhes técnicos para a plataforma que você está interessado em:

- [Xamarin. Forms](~/xamarin-forms/app-fundamentals/localization.md) localização de plataforma cruzada usando arquivos RESX.
- [Xamarin](~/ios/app-fundamentals/localization/index.md) localização de plataforma nativo.
- [Xamarin](~/android/app-fundamentals/localization.md) localização de plataforma nativo.



## <a name="related-links"></a>Links relacionados

- [Visão geral de localização da Apple](https://developer.apple.com/internationalization/)
- [Lista de verificação de localização do Android](http://developer.android.com/distribute/tools/localization-checklist.html)
- [Práticas recomendadas para o desenvolvimento de aplicativos prontos para o mundo (MSDN)](http://msdn.microsoft.com/en-us/library/w7x1y988%28v=vs.90%29.aspx)
