---
title: Localização no xamarin. IOS
description: Este documento descreve os recursos de localização de iOS e como usar esses recursos em aplicativos xamarin. IOS. Ele aborda o idioma, localidade, arquivos de cadeias de caracteres, imagens de inicialização e muito mais.
ms.prod: xamarin
ms.assetid: DFD9EB4A-E536-18E4-C8FD-679BA9C836D8
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 04/28/2017
ms.openlocfilehash: 0c52db61689dd640332fb1e02e2260dda08e4686
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50115920"
---
# <a name="localization-in-xamarinios"></a>Localização no xamarin. IOS

_Este documento aborda os recursos de localização do SDK do iOS e como acessá-los com o Xamarin._

Consulte a [codificações de internacionalização](encodings.md) para obter instruções sobre a inclusão de páginas de código/conjuntos de caracteres em aplicativos que devem processar dados não-Unicode.

## <a name="ios-platform-features"></a>recursos da plataforma iOS

Esta seção descreve alguns dos recursos de localização no iOS. Vá para o [próxima seção](#basics) para ver exemplos e código específicos.

### <a name="language"></a>Idioma

Os usuários escolher seu idioma na **configurações** aplicativo. Essa configuração afeta a cadeias de caracteres de idioma e as imagens exibidas pelo sistema operacional e nos aplicativos. 

Para determinar o idioma que está sendo usado em um aplicativo, obter o primeiro elemento da `NSBundle.MainBundle.PreferredLocalizations`:

```csharp
var lang = NSBundle.MainBundle.PreferredLocalizations[0];
```

Esse valor será um código de idioma, como `en` para inglês `es` para espanhol, `ja` para japonês, etc. O valor retornado é restrito a um entre as localizações com suporte do aplicativo (usando regras de fallback para determinar a melhor correspondência).

O código do aplicativo não precisa sempre verificar esse valor – o Xamarin e ambos os iOS fornecem recursos que ajudam a fornecer automaticamente a cadeia de caracteres correta ou o recurso para o idioma do usuário. Esses recursos são descritos no restante deste documento.

> [!NOTE]
> Use `NSLocale.PreferredLanguages` para determinar as preferências de idioma do usuário, independentemente de suas localizações com suporte no aplicativo. Os valores retornados por esse método alterado no iOS 9; ver [TN2418 de observação técnica](https://developer.apple.com/library/content/technotes/tn2418/_index.html) para obter detalhes.

### <a name="locale"></a>Localidade

Os usuários escolhem a localidade na **configurações** aplicativo. Essa configuração afeta a maneira que as datas, horas, números e moeda são formatados.

Isso permite aos usuários escolher se eles veem os formatos de hora de 12 horas ou 24 horas, se seu separador decimal é uma vírgula ou um ponto e a ordem do dia, mês e ano na exibição de data.

Com o Xamarin, você tem acesso às classes de iOS da Apple, ambos os (`NSNumberFormatter`), bem como as classes do .NET em System. Globalization. Os desenvolvedores devem avaliar qual é mais adequado às suas necessidades, pois há diferentes recursos disponíveis em cada. Em particular, se você estiver recuperando e exibindo os preços de compra no aplicativo usando StoreKit, você deve usar classes de formatação da Apple para obter as informações de preço retornadas.

A localidade atual pode ser consultada por qualquer uma das duas maneiras:

- `NSLocale.CurrentLocale.LocaleIdentifier`
- `NSLocale.AutoUpdatingCurrentLocale.LocaleIdentifier`

O primeiro valor pode ser armazenados em cache pelo sistema operacional e então, talvez nem sempre reflete a localidade do usuário selecionada no momento. Use o segundo valor para obter a localidade selecionada no momento.

> [!NOTE]
> Mono (o tempo de execução .NET no qual se baseia o xamarin. IOS) e APIs do iOS da Apple não dão suporte a conjuntos idênticos de combinações de idioma/região.
> Por isso, é possível selecionar uma combinação de idioma/região no iOS **configurações** aplicativo que não é mapeado para um valor válido em Mono. Por exemplo, a definição de idioma de um iPhone para inglês e sua região Espanha fará com que as seguintes APIs produzir valores diferentes:
> 
> - `CurrentThead.CurrentCulture`: en-US (API do Mono)
> - `CurrentThread.CurrentUICulture`: en-US (API do Mono)
> - `NSLocale.CurrentLocale.LocaleIdentifier`: en_ES (API da Apple)
>
> Como usa o Mono `CurrentThread.CurrentUICulture` para selecionar os recursos e `CurrentThread.CurrentCulture` para formatar datas e moedas, com base em Mono localização (por exemplo, com arquivos. resx) não pode produzir resultados esperados para essas combinações de idioma/região. Nessas situações, dependem das APIs da Apple para localizar conforme necessário.

### <a name="nscurrentlocaledidchangenotification"></a>NSCurrentLocaleDidChangeNotification

iOS gera um `NSCurrentLocaleDidChangeNotification` quando o usuário atualiza sua localidade. Aplicativos podem ouvir para essa notificação enquanto eles estão em execução e podem fazer as alterações apropriadas na interface do usuário.

## <a name="localization-basics-in-ios"></a>Noções básicas de localização no iOS

Os seguintes recursos do iOS serão facilmente utilizados no Xamarin para fornecer recursos localizados para exibição ao usuário. Consulte a [TaskyL10n exemplo](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n) para ver como implementar essas ideias.

### <a name="specifying-default-and-supported-languages-in-infoplist"></a>Especificando padrão e idiomas com suporte no Info. plist

Na [técnica p e R QA1828: como iOS determina o idioma para seu aplicativo](https://developer.apple.com/library/content/qa/qa1828/_index.html), Apple descreve como o seleciona um idioma a ser usado em um aplicativo iOS. Os seguintes fatores afetam a linguagem que é exibida:

- Idiomas preferencial do usuário (encontrada na **configurações** app)
- Entre as localizações agrupado com o aplicativo (.lproj pastas)
- `CFBundleDevelopmentRegion` (**Info. plist** valor que especifica o idioma padrão para o aplicativo)
- `CFBundleLocalizations` (**Info. plist** matriz especificando localizações todos com suporte)

Conforme indicado na técnica p e R, `CFBundleDevelopmentRegion` representa o idioma e região padrão de um aplicativo. Se o aplicativo não explicitamente dá suporte a qualquer um dos idiomas de preferência do usuário, ele usará o idioma especificado por esse campo. 

> [!IMPORTANT]
> iOS 11 se aplica a esse mecanismo de seleção de idioma mais estritamente que foram a versões anteriores do sistema operacional. Por isso, qualquer aplicativo iOS 11 que não declara explicitamente suas localizações com suporte – incluindo .lproj pastas ou definindo um valor para `CFBundleLocalizations` – pode ser exibido um idioma diferente no iOS 11, do que tinha no iOS 10.

Se `CFBundleDevelopmentRegion` não foi especificado o **Info. plist** arquivo, as ferramentas de build do xamarin. IOS no momento, usam um valor padrão de `en_US`. Embora isso possa mudar em uma versão futura, isso significa que o idioma padrão é inglês.

Para garantir que seu aplicativo seleciona um idioma esperado, execute as seguintes etapas:

- Especifica um idioma padrão. Abra **Info. plist** e usar o **fonte** modo de exibição para definir um valor para o `CFBundleDevelopmentRegion` chave; no XML, ele deve ser semelhante ao seguinte:

```xml
<key>CFBundleDevelopmentRegion</key>
<string>es</string>
```

Este exemplo usa "es" para especificar que, quando nenhuma do usuário preferencial idiomas têm suporte, o padrão para o espanhol.

- Declare suas localizações todos com suporte. Na **Info. plist**, use o **fonte** exibição para definir uma matriz para o `CFBundleLocalizations` chave; no XML, ele deve ser semelhante ao seguinte:

```xml
<key>CFBundleLocalizations</key>
<array>
    <string>en</string>
    <string>es</string>
    ...
</array>
```

Aplicativos xamarin. IOS que foram localizados usando mecanismos de .NET, como arquivos. resx devem fornecer essas **Info. plist** valores também.

Para obter mais informações sobre esses **Info. plist** chaves, dê uma olhada da Apple [referência de chave de lista de propriedades de informações](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/CoreFoundationKeys.html).

### <a name="getlocalizedstring-method"></a>Método GetLocalizedString

O `NSBundle.MainBundle.GetLocalizedString` método procura texto localizado que foi armazenado no **.strings** arquivos no projeto. Esses arquivos são organizados por linguagem, nos diretórios especialmente nomeadas com um **.lproj** sufixo (Observe que a primeira letra da extensão é um "L" em letras minúsculas).

#### <a name="strings-file-locations"></a>locais de arquivo .strings

- **Base.lproj** é o diretório que contém recursos para o idioma padrão.
  Ele geralmente está localizado na raiz do projeto (mas também pode ser colocado na **recursos** pasta).
- **&lt;linguagem&gt;.lproj** diretórios são criados para cada idioma suportado, normalmente na **recursos** pasta.

Pode haver um número de diferentes **.strings** arquivos em cada diretório de idioma:

- **Localizable.strings** – lista principal de texto localizado.
- **InfoPlist.strings** – determinadas chaves específicas são permitidas neste arquivo para converter as coisas, como o nome do aplicativo.
- **< nome do storyboard > .strings** – arquivo opcional que contém traduções para elementos de interface do usuário em um storyboard.

O **ação de compilação** para esses arquivos devem ser **recursos de pacote**.

#### <a name="strings-file-format"></a>formato de arquivo .strings

A sintaxe para valores de cadeia de caracteres localizada é:

```console
/* comment */
"key"="localized-value";
```

Você deve escapar os seguintes caracteres em cadeias de caracteres:

* `\"` aspas
* `\\` barra invertida
* `\n` nova linha

Este é um exemplo **es/Localizable.strings** (ie. Arquivo de espanhol) de exemplo:

```console
"<new task>" = "<new task>";
"Task Details" = "Detalles de la tarea";
"Name" = "Nombre";
"task name" = "nombre de la tarea";
"Notes" = "Notas";
"other task info"= "otra información de tarea";
"Done" = "Completo";
"Save" = "Guardar";
"Delete" = "Eliminar";
```

### <a name="images"></a>Imagens

Para localizar uma imagem no iOS:

1. Consulte a imagem no código, por exemplo:

    ```csharp
    UIImage.FromBundle("flag");
    ```

2. Coloque o arquivo de imagem padrão **flag.png** na **Base.lproj** (o diretório de linguagem de desenvolvimento nativo).

3. Opcionalmente, coloque as versões localizadas da imagem no **.lproj** pastas para cada idioma (por exemplo. **es.lproj**, **ja.lproj**). Use o mesmo nome de arquivo **flag.png** em cada diretório de idioma.

Se não houver uma imagem de um idioma específico, o iOS o fallback para a pasta de linguagem nativa padrão e carregar a imagem a partir daí.

#### <a name="launch-images"></a>Imagens de inicialização

Use as convenções de nomenclatura padrão para as imagens de inicialização (e o XIB ou Storyboard para modelos de iPhone 6) ao colocá-las na **.lproj** diretórios para cada idioma.

```console
Default.png
Default@2x.png
Default-568h@2x.png
LaunchScreen.xib
```

### <a name="app-name"></a>Nome do aplicativo

Colocando um **InfoPlist.strings** do arquivo em um **.lproj** diretório permite que você substitua alguns valores do aplicativo do **Info. plist**, incluindo o nome do aplicativo:

```console
"CFBundleDisplayName" = "LeónTodo";
```

Outras chaves que você pode usar para [localizar cadeias de caracteres específica do aplicativo](https://developer.apple.com/library/ios/documentation/MacOSX/Conceptual/BPInternational/LocalizingYourApp/LocalizingYourApp.html#//apple_ref/doc/uid/10000171i-CH5-SW21) são:

- CFBundleName
- CFBundleShortVersionString
- NSHumanReadableCopyright

### <a name="dates-and-times"></a>Datas e horas

Embora seja possível usar as funções internas de data e hora do .NET (juntamente com a atual `CultureInfo`) para formatar datas e horas para uma localidade, isso ignoraria usuário-configurações específicas à localidade (que podem ser definidas separadamente de linguagem).

Usar o iOS `NSDateFormatter` para produzir saída que corresponde à preferência de localidade do usuário. O código de exemplo a seguir demonstra a data básica e opções de formatação de hora:

```csharp
var date = NSDate.Now;
var df = new NSDateFormatter ();
df.DateStyle = NSDateFormatterStyle.Full;
df.TimeStyle = NSDateFormatterStyle.Long;
Debug.WriteLine ("Full,Long: " + df.StringFor(date));
df.DateStyle = NSDateFormatterStyle.Short;
df.TimeStyle = NSDateFormatterStyle.Short;
Debug.WriteLine ("Short,Short: " + df.StringFor(date));
df.DateStyle = NSDateFormatterStyle.Medium;
df.TimeStyle = NSDateFormatterStyle.None;
Debug.WriteLine ("Medium,None: " + df.StringFor(date));
```

Resultados para inglês dos Estados Unidos:

```console
Full,Long: Friday, August 7, 2015 at 10:29:32 AM PDT
Short,Short: 8/7/15, 10:29 AM
Medium,None: Aug 7, 2015
```

Resultados para espanhol da Espanha:

```console
Full,Long: viernes, 7 de agosto de 2015, 10:26:58 GMT-7
Short,Short: 7/8/15 10:26
Medium,None: 7/8/2015
```

Consulte a maçã [formatadores data](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/DataFormatting/Articles/dfDateFormatting10_4.html) documentação para obter mais informações. Ao testar sensíveis à localidade formatação de data e hora, marque ambos **iPhone Language** e **região** configurações.

<a name="rtl" />

### <a name="right-to-left-rtl-layout"></a>No layout para direita para esquerda (RTL)

iOS fornece inúmeros recursos para ajudá-lo na criação de aplicativos com suporte a RTL:

- Do layout do uso automático `leading` e `trailing` atributos para aligment de controle (que corresponde à esquerda e direita para inglês, mas é revertido para idiomas RTL).
  O [ `UIStackView` ](~/ios/user-interface/controls/uistackview.md) controle é particularmente útil para dispor controles a serem consideradas ao RTL.
- Use `TextAlignment = UITextAlignment.Natural` para alinhamento de texto (que será deixado para a maioria das linguagens, mas logo para RTL).
- `UINavigationController` Inverte o botão Voltar e inverte a direção de passar o dedo automaticamente.

As capturas de tela a seguir mostram os [localizado exemplo Tasky](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n) em árabe e hebraico (embora foi inserido em inglês nos campos):

[![](images/rtl-ar-sml.png "Localização em árabe")](images/rtl-ar.png#lightbox "Arabic") 

[![](images/rtl-he-sml.png "Localização em hebraico")](images/rtl-he.png#lightbox "Hebrew")

iOS é revertida automaticamente a `UINavigationController`, e os outros controles são posicionados dentro de `UIStackView` ou alinhado com Layout automático.
Texto RTL é localizado usando **.strings** arquivos da mesma forma como o texto LTR.

<a name="code"/>

## <a name="localizing-the-ui-in-code"></a>Localizando a interface do usuário no código

O [Tasky (localizado no código)](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n) exemplo mostra como localizar um aplicativo em que a interface do usuário é criada no código (em vez de XIBs ou storyboards).

### <a name="project-structure"></a>Estrutura do projeto

![](images/solution-code.png "Árvore de recursos")

### <a name="localizablestrings-file"></a>Arquivo Localizable.strings

Conforme descrito acima, o **Localizable.strings** formato de arquivo consiste em pares chave-valor. A chave descreve a intenção da cadeia de caracteres e o valor é o texto traduzido para ser usado no aplicativo.

Espanhol (**es**) localizações para o exemplo são mostradas abaixo:

```console
"<new task>" = "<new task>";
"Task Details" = "Detalles de la tarea";
"Name" = "Nombre";
"task name" = "nombre de la tarea";
"Notes" = "Notas";
"other task info"= "otra información de tarea";
"Done" = "Completo";
"Save" = "Guardar";
"Delete" = "Eliminar";
```

### <a name="performing-the-localization"></a>Executar a localização

No código do aplicativo, onde quer que o texto de exibição da interface do usuário é definido (seja o texto do rótulo para um, ou o espaço reservado de uma entrada, etc) o código usa o iOS `GetLocalizedString` função para recuperar a tradução correta para exibir:

```csharp
var localizedString = NSBundle.MainBundle.GetLocalizedString ("key", "optional");
someControl.Text = localizedString;
```

<a name="storyboard"/>

## <a name="localizing-storyboard-uis"></a>Localizando o storyboard interfaces do usuário

O exemplo [Tasky (localizada storyboard)](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10nStoryboard) mostra como localizar texto em controles em um storyboard.

### <a name="project-structure"></a>Estrutura do projeto

O **Base.lproj** diretório contém o storyboard e também deve conter todas as imagens usadas no aplicativo.

Contenham outros diretórios de linguagem uma **Localizable.strings** arquivo para quaisquer recursos de cadeia de caracteres referenciados no código, bem como um **MainStoryboard.strings** arquivo que contém traduções para texto no Storyboard.

![](images/solution-storyboard.png "Árvore de recursos")

Os diretórios de idioma devem conter uma cópia de todas as imagens que foram localizados, para substituir presente no **Base.lproj**.

### <a name="object-id--localization-id"></a>ID de objeto / ID de localização

Durante a criação e edição de controles em um storyboard, selecione cada controle e verifique a ID a ser usado para localização:

- No Visual Studio para Mac, ele está localizado na **painel de propriedades** e é chamado **ID de localização**.
- No Xcode, ele é chamado **ID do objeto**.

Geralmente, esse valor de cadeia de caracteres tem um formulário, como "NF3-h8-xmR", conforme mostrado na seguinte captura de tela:

![](images/xs-designer-localization-id.png "Modo de exibição do Xcode da localização de Storyboard")

Esse valor é usado na **.strings** arquivo para atribuir o texto traduzido automaticamente a cada controle.

### <a name="mainstoryboardstrings"></a>MainStoryboard.strings

O formato do arquivo de tradução de storyboard é semelhante para o **Localizable.strings** do arquivo, exceto que a chave (o valor à esquerda) não pode ser definido pelo usuário, mas em vez disso, deve ter um formato muito específico: `ObjectID.property`.

No exemplo **Mainstoryboard.strings** abaixo você pode ver `UITextField`s têm uma `placeholder` propriedade de texto que pode ser localizada; `UILabel`s têm uma `text` propriedade; e `UIButton`texto do padrão de s é definido usando `normalTitle`:

```console
"SXg-TT-IwM.placeholder" = "nombre de la tarea";
"Pqa-aa-ury.placeholder"= "otra información de tarea";
"zwR-D9-hM1.text" = "Detalles de la tarea";
"bAM-2j-Rzw.text" = "Notas";           /* Notes */
"NF3-h8-xmR.text" = "Completo";        /* Done */
"MWt-Ya-pMf.normalTitle" = "Guardar";  /* Save */
"IGr-pR-05L.normalTitle" = "Eliminar"; /* Delete */
```

> [!IMPORTANT]
> Usando um storyboard com classes de tamanho pode resultar em traduções que não aparecem no aplicativo. [Notas de versão do Xcode da Apple](https://developer.apple.com/library/content/releasenotes/DeveloperTools/RN-Xcode/Chapters/Introduction.html) indicam que um storyboard ou XIB vai não localizar corretamente se três itens forem verdadeiros: ele usa as classes de tamanho, a localização de base e o destino de build são definidos como Universal e o build direciona iOS 7.0. A correção é duplicar seu arquivo do storyboard cadeias de caracteres em dois arquivos idênticos: **MainStoryboard~iphone.strings** e **MainStoryboard~ipad.strings**, conforme mostrado na seguinte captura de tela:
> 
> ![](images/xs-dup-strings.png "Arquivos de cadeias de caracteres")

<a name="appstore" />

## <a name="app-store-listing"></a>Listagem da App Store

Segue FAQ da Apple [a localização de aplicativos Store](https://itunespartner.apple.com/en/apps/faq/App%20Store_Localization) Inserir traduções para cada país em que seu aplicativo está à venda. Observe seu aviso de que as traduções só aparecerá se o seu aplicativo também contém um localizada **.lproj** diretório para o idioma.

## <a name="summary"></a>Resumo

Este artigo aborda os conceitos básicos de localização de aplicativos iOS usando os recursos de storyboard e manipulação de recursos internos.

Você pode aprender mais sobre i18n e L10n para aplicativos iOS, Android e plataforma cruzada (incluindo o xamarin. Forms) em [este guia de plataforma cruzada](~/cross-platform/app-fundamentals/localization.md).

## <a name="related-links"></a>Links relacionados

- [Tasky (localizado no código) (amostra)](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n)
- [Tasky (localizada storyboard) (amostra)](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10nStoryboard)
- [Guia de localização da Apple](https://developer.apple.com/library/ios/documentation/MacOSX/Conceptual/BPInternational/InternationalizingYourUserInterface/InternationalizingYourUserInterface.html)
- [Visão geral de localização de plataforma cruzada](~/cross-platform/app-fundamentals/localization.md)
- [Localização do xamarin. Forms](~/xamarin-forms/app-fundamentals/localization/index.md)
- [Localização do Android](~/android/app-fundamentals/localization.md)
