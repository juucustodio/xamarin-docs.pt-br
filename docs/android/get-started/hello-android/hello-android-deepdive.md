---
title: 'Hello, Android: aprofundamento'
description: Neste guia de duas partes, você criará seu primeiro aplicativo Xamarin.Android e passará a entender os fundamentos de desenvolvimento de aplicativos Android com Xamarin. Ao longo do caminho, serão apresentados conceitos, ferramentas e etapas necessários para criar e implantar um aplicativo Xamarin.Android.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: EF0E110B-20EA-43F6-9476-1A0F41AFD298
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 10/05/2018
ms.openlocfilehash: ee72c51611503f92e7ede3a01a7918780652935c
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028001"
---
# <a name="hello-android-deep-dive"></a>Hello, Android: aprofundamento

_Neste guia de duas partes, você criará seu primeiro aplicativo Xamarin. Android e desenvolverá uma compreensão dos conceitos básicos do desenvolvimento de aplicativos Android com o Xamarin. Ao longo do caminho, você será apresentado às ferramentas, aos conceitos e às etapas necessárias para criar e implantar um aplicativo Xamarin. Android._

Em [Início rápido do Hello, Android](~/android/get-started/hello-android-multiscreen/hello-android-multiscreen-quickstart.md), você compilou e executou seu primeiro aplicativo Xamarin.Android. Agora é hora de desenvolver uma compreensão mais profunda de como funcionam os aplicativos Android para que você possa criar programas mais sofisticados. Este guia examina as etapas que você seguiu no passo a passo Hello, Android, para que você possa entender o que você fez e comece a desenvolver um entendimento fundamental do desenvolvimento de aplicativos Android.

Este guia citará os tópicos a seguir:

::: zone pivot="windows"

- **Introdução ao Visual Studio** &ndash; introdução ao Visual Studio e criação de um novo aplicativo Xamarin.Android.

- **Anatomia de um aplicativo Xamarin.Android** – apresentação das partes essenciais de um aplicativo Xamarin.Android.

- **Noções básicas de arquitetura e conceitos básicos de aplicativos** &ndash; introdução às atividades, ao manifesto do Android e ao desenvolvimento em Android em linhas gerais.

- **IU (interface do usuário)** &ndash; criação de interfaces do usuário com o Android Designer.

- **Atividades e o ciclo de vida da atividade** &ndash; uma introdução ao ciclo de vida da atividade e à preparação da interface do usuário em código.

- **Testes, implantação e toques finais** &ndash; conclua o aplicativo tirando proveito de conselhos sobre testes, implantação, criação de arte final e muito mais.

::: zone-end
::: zone pivot="macos"

- **Introdução ao Visual Studio para Mac** &ndash; introdução ao Visual Studio para Mac e criação de um novo aplicativo do Xamarin.Android.

- **Anatomia de um aplicativo Xamarin.Android** &ndash; apresentação das partes essenciais de um aplicativo Xamarin.Android.

- **Noções básicas de arquitetura e conceitos básicos de aplicativos** &ndash; introdução às atividades, ao manifesto do Android e ao desenvolvimento em Android em linhas gerais.

- **IU (interface do usuário)** &ndash; criação de interfaces do usuário com o Android Designer.

- **Atividades e o ciclo de vida da atividade** &ndash; uma introdução ao ciclo de vida da atividade e à preparação da interface do usuário em código.

- **Testes, implantação e toques finais** &ndash; conclua o aplicativo tirando proveito de conselhos sobre testes, implantação, criação de arte final e muito mais.

::: zone-end

Este guia ajudará você a desenvolver as habilidades e o conhecimento necessário para criar um aplicativo Android de uma única tela. Depois que trabalhar com ele, você deverá compreender as diferentes partes de um aplicativo Xamarin.Android e como elas se encaixam.

::: zone pivot="windows"

## <a name="introduction-to-visual-studio"></a>Introdução ao Visual Studio

O Visual Studio é um IDE avançado da Microsoft. Ele conta com um designer visual totalmente integrado, um editor de texto que inclui ferramentas de refatoração, um navegador de assembly, integração de código-fonte e muito mais. Neste guia, você aprenderá a usar algumas funcionalidades básicas do Visual Studio com o plug-in Xamarin.

O Visual Studio organiza o código em _Soluções_ e _Projetos_. Uma solução é um contêiner que pode conter um ou mais Projetos. Um projeto pode ser um aplicativo (por exemplo, para iOS ou Android), uma biblioteca com suporte, um aplicativo de teste e muito mais. No aplicativo **Phoneword**, você adicionou um novo Projeto Android usando o modelo **Aplicativo Android** para a solução **Phoneword** criada no guia [Hello, Android](~/android/get-started/hello-android/hello-android-quickstart.md).

::: zone-end
::: zone pivot="macos"

## <a name="introduction-to-visual-studio-for-mac"></a>Introdução ao Visual Studio para Mac

O Visual Studio para Mac é um IDE de software livre gratuito semelhante ao Visual Studio. Ele conta com um designer visual totalmente integrado, um editor de texto complementado com ferramentas de refatoração, um navegador de assembly, integração de código-fonte e muito mais. Neste guia, você aprenderá a usar algumas funcionalidades básicas do Visual Studio para Mac. Se você for novo no Visual Studio para Mac, convém conferir a [Introdução ao Visual Studio para Mac](https://docs.microsoft.com/visualstudio/mac/), que é mais detalhada.

O Visual Studio para Mac segue a prática do Visual Studio de organizar o código em _Soluções_ e _Projetos_. Uma solução é um contêiner que pode conter um ou mais Projetos. Um projeto pode ser um aplicativo (por exemplo, para iOS ou Android), uma biblioteca com suporte, um aplicativo de teste e muito mais. No aplicativo **Phoneword**, você adicionou um novo Projeto Android usando o modelo **Aplicativo Android** para a solução **Phoneword** criada no guia [Hello, Android](~/android/get-started/hello-android/hello-android-quickstart.md).

::: zone-end

<a name="anatomy" />

## <a name="anatomy-of-a-xamarinandroid-application"></a>Anatomia de um aplicativo Xamarin.Android

::: zone pivot="windows"

A captura de tela a seguir lista o conteúdo da solução. Este é o Gerenciador de Soluções, que contém a estrutura de diretórios e todos os arquivos associados à solução:

[![Gerenciador de Soluções](hello-android-deepdive-images/vs/02-solution-structure-sml.png)](hello-android-deepdive-images/vs/02-solution-structure.png#lightbox)

::: zone-end
::: zone pivot="macos"

A captura de tela a seguir lista o conteúdo da solução. Este é o Painel de Soluções, que contém a estrutura de diretórios e todos os arquivos associados à solução:

[![Painel da Solução](hello-android-deepdive-images/xs/02-solution-structure-sml.png)](hello-android-deepdive-images/xs/02-solution-structure.png#lightbox)

::: zone-end

Uma solução chamada **Phoneword** foi criada e o projeto Android **Phoneword** foi colocado dentro dela.

Examine os itens dentro do projeto para ver cada pasta e sua finalidade:

- **Propriedades** &ndash; contém o arquivo [AndroidManifest.xml](~/android/platform/android-manifest.md), que descreve todos os requisitos para o aplicativo Xamarin.Android, incluindo nome, número de versão e permissões. A pasta **Propriedades** também hospeda o [AssemblyInfo.cs](xref:Microsoft.VisualBasic.ApplicationServices.AssemblyInfo), um arquivo de metadados de assembly do .NET. É uma boa prática preencher esse arquivo com algumas informações básicas sobre seu aplicativo.

- **Referências** &ndash; contém os assemblies necessários para compilar e executar o aplicativo. Se você expandir o diretório de referências, verá referências aos assemblies do .NET como [System](xref:System), System.Core e [System.Xml](xref:System.Xml), bem como uma referência ao assembly do Mono.Android do Xamarin.

- **Ativos** &ndash; contém os arquivos que o aplicativo precisa executar incluindo arquivos de texto, arquivos de dados locais e fontes. Os arquivos incluídos aqui são acessíveis por meio da classe `Assets` gerada. Para obter mais informações sobre ativos Android, veja o guia [Uso de ativos do Android](~/android/app-fundamentals/resources-in-android/android-assets.md) do Xamarin.

- **Recursos** &ndash; contém recursos de aplicativo como cadeias de caracteres, imagens e layouts. Você pode acessar esses recursos em código por meio da classe `Resource` gerada. O guia [Recursos do Android](~/android/app-fundamentals/resources-in-android/index.md) fornece mais detalhes sobre o diretório **Resources**. O modelo de aplicativo também inclui um guia conciso para os recursos no arquivo **AboutResources.txt**.

### <a name="resources"></a>Recursos

O diretório **Recursos** contém quatro pastas chamadas **drawable** (desenhável), **layout**, **mipmap** e **values** (valores), bem como um arquivo chamado **Resource.designer.cs**.

Os itens estão resumidos na tabela abaixo:

- **desenhável** &ndash; os diretórios desenháveis contêm [recursos desenháveis](https://developer.android.com/guide/topics/resources/drawable-resource.html), como imagens e bitmaps.

- **mipmap** &ndash; O diretório mipmap contém arquivos desenháveis para diferentes densidades de ícones do inicializador. No modelo padrão, o diretório desenhável contém o arquivo de ícone do aplicativo, **Icon.png**.

::: zone pivot="windows"

- **layout**&ndash; o diretório layout contém _arquivos do Android Designer_ (.axml) que definem a interface do usuário para cada tela ou atividade. O modelo cria um layout padrão chamado **activity_main.axml**.

::: zone-end
::: zone pivot="macos"

- **layout**&ndash; o diretório layout contém _arquivos do Android Designer_ (.axml) que definem a interface do usuário para cada tela ou atividade. O modelo cria um layout padrão chamado **Main.axml**.

::: zone-end

- **values** &ndash; esse diretório contém arquivos XML que armazenam valores simples como cadeias de caracteres, inteiros e cores. O modelo cria um arquivo para armazenar valores de cadeia de caracteres chamados **Strings.xml**.

- **Resource.designer.cs** &ndash; também conhecido como a classe `Resource`, esse arquivo é uma classe parcial que contém as IDs exclusivas atribuídas a cada recurso. Ele é criado automaticamente pelas ferramentas do Xamarin.Android e regenerado quando necessário. Esse arquivo deve não ser editado manualmente, pois o Xamarin.Android substituirá todas as alterações manuais que forem feitas.

## <a name="app-fundamentals-and-architecture-basics"></a>Noções básicas de arquitetura e conceitos básicos de aplicativos

Aplicativos do Android não têm nenhum ponto de entrada único, ou seja, não há nenhuma linha de código individual no aplicativo que o sistema operacional chame para iniciar o aplicativo. Em vez disso, um aplicativo é iniciado quando Android instancia uma de suas classes, período durante o qual o Android carrega o processo do aplicativo inteiro na memória.

Esse recurso exclusivo do Android pode ser extremamente útil ao projetar aplicativos complicados ou interagir com o sistema operacional Android. No entanto, essas opções também tornam o Android complexo ao lidar com um cenário básico como o do aplicativo **Phoneword**. Por esse motivo, a exploração da arquitetura Android é dividida em dois. Este guia analisa em detalhes um aplicativo que usa o ponto de entrada mais comum para um aplicativo do Android: a primeira tela. Em [Multitela do Hello, Android](~/android/get-started/hello-android-multiscreen/index.md), todas as complexidades da arquitetura Android são exploradas conforme diferentes maneiras de iniciar um aplicativo são discutidas.

### <a name="phoneword-scenario---starting-with-an-activity"></a>Cenário do Phoneword – início de uma atividade

Quando você abrir o aplicativo **Phoneword** pela primeira vez em um dispositivo ou emulador, o sistema operacional criará a primeira *Atividade*. Uma Atividade é uma classe especial Android que corresponde a uma única tela de aplicativo e é responsável por desenhar e acionar a interface do usuário. Quando o Android cria a primeira Atividade de um aplicativo, ele carrega o aplicativo inteiro:

[![Carga de atividade](hello-android-deepdive-images/01-activity-load-sml.png)](hello-android-deepdive-images/01-activity-load.png#lightbox)

Já que não há nenhuma progressão linear em um aplicativo do Android (você pode iniciar o aplicativo de vários pontos), o Android tem uma maneira exclusiva de controlar quais arquivos e classes constituem um aplicativo. No exemplo **Phoneword**, todas as partes que compõem o aplicativo são registradas com um arquivo XML especial chamado de **Manifesto do Android**. A função do **manifesto do Android** é manter o controle de conteúdo, propriedades e permissões do aplicativo e divulgá-los para o sistema operacional Android. Você pode pensar no aplicativo **Phoneword** como uma única atividade (tela) e uma coleção de arquivos de ajuda e de recursos vinculados pelo arquivo de Manifesto do Android, conforme é ilustrado pelo diagrama abaixo:

[![Auxiliares de recursos](hello-android-deepdive-images/02-resources-helpers-sml.png)](hello-android-deepdive-images/02-resources-helpers.png#lightbox)

As próximas seções exploram as relações entre as diversas partes do aplicativo **Phoneword**, o que deve fornecer uma compreensão melhor do diagrama acima. Essa exploração começa com a interface do usuário, pois ela aborda os arquivos de layout e do Android Designer.

## <a name="user-interface"></a>Interface do Usuário

> [!TIP]
> As versões mais recentes do Visual Studio dão suporte à abertura de arquivos .xml dentro do Android Designer.
>
> Tanto arquivos .axml quanto .xml são compatíveis com o Android Designer.

::: zone pivot="windows"

**activity_main.axml** é o arquivo de layout de interface do usuário para a primeira tela do aplicativo. O .axml indica que este é um arquivo do Android Designer (AXML significa *XML Android*). O nome *Main* é arbitrário do ponto de vista do Android &ndash; o arquivo de layout poderia ter recebido qualquer outro nome. Quando você abre **activity_main.axml** no IDE, ele abre o editor visual de arquivos de layout do Android, chamado *Android Designer*:

[![Designer Android](hello-android-deepdive-images/vs/03-android-designer-sml.png "Android Designer")](hello-android-deepdive-images/vs/03-android-designer.png#lightbox)

No aplicativo **Phoneword**, a ID do **TranslateButton** é definida como `@+id/TranslateButton`:

[![Configuração de ID do TranslateButton](hello-android-deepdive-images/vs/04-translatebutton-sml.png "Configuração de ID do TranslateButton")](hello-android-deepdive-images/vs/04-translatebutton.png#lightbox)

::: zone-end
::: zone pivot="macos"

**Main.axml** é o arquivo de layout de interface do usuário para a primeira tela do aplicativo. O .axml indica que este é um arquivo do Android Designer (AXML significa *XML Android*). O nome *Main* é arbitrário do ponto de vista do Android &ndash; o arquivo de layout poderia ter recebido qualquer outro nome. Quando você abre **Main.axml** no IDE, ele abrirá o editor visual para arquivos de layout do Android chamado de *Android Designer*:

[![Android Designer](hello-android-deepdive-images/xs/03-android-designer-sml.png)](hello-android-deepdive-images/xs/03-android-designer.png#lightbox)

No aplicativo **Phoneword**, a ID do **TranslateButton** é definida como `@+id/TranslateButton`:

[![Configuração de ID de TranslateButton](hello-android-deepdive-images/xs/04-translatebutton-sml.png)](hello-android-deepdive-images/xs/04-translatebutton.png#lightbox)

::: zone-end

Quando você define a propriedade `id` do **TranslateButton**, o Android Designer mapeia o controle **TranslateButton** para a classe `Resource` e atribui a ele a *ID de recurso*`TranslateButton`. Esse mapeamento de controle visual para classe torna possível localizar e usar o **TranslateButton** e outros controles no código do aplicativo. Isso será abordado em mais detalhes ao separar o código que acionar os controles. Tudo que você precisa saber por enquanto é que a representação de código de um controle está vinculada à representação visual do controle no designer pela propriedade `id`.

### <a name="source-view"></a>Exibição de fonte

Tudo que está definido na área de design é traduzido em XML para o Xamarin.Android usar. O Android Designer fornece uma exibição do código-fonte que contém o XML que foi gerado do designer visual. Você pode exibir esse XML alternando para o painel **Código-fonte** no canto inferior esquerdo da exibição de designer, conforme ilustrado na captura de tela abaixo:

::: zone pivot="windows"

[![Exibição de origem do designer](hello-android-deepdive-images/vs/05-source-view-sml.png "Exibição de origem do designer")](hello-android-deepdive-images/vs/05-source-view.png#lightbox)

::: zone-end
::: zone pivot="macos"

[![Exibição de fonte de designer](hello-android-deepdive-images/xs/05-source-view-sml.png)](hello-android-deepdive-images/xs/05-source-view.png#lightbox)

::: zone-end

Este código-fonte XML deve conter quatro elementos de controle: dois **TextView**s, um **EDITTEXT** e um elemento **Button** . Para fazer um tour mais detalhado sobre o Android Designer, consulte o guia [Visão geral do Designer](~/android/user-interface/android-designer/index.md) de Android do Xamarin.

As ferramentas e os conceitos por trás da parte visual da interface do usuário foram agora abordados. Em seguida, é hora de se aprofundar no código que aciona a interface do usuário conforme as atividades e o ciclo de vida da atividade são explorados.

## <a name="activities-and-the-activity-lifecycle"></a>Atividades e ciclo de vida da atividade

A classe `Activity` contém o código que acionar a interface do usuário.
A atividade é responsável por responder à interação do usuário e criar uma experiência de usuário dinâmica.
Esta seção apresenta a classe `Activity`, discute o ciclo de vida de atividade e apresenta o código que aciona a interface do usuário no aplicativo **Phoneword**.

### <a name="activity-class"></a>Classe de atividade

O aplicativo **Phoneword** tem apenas uma tela (Atividade). A classe que aciona a tela é chamada `MainActivity` e reside no arquivo **MainActivity.cs**. O nome `MainActivity` não tem nenhum significado especial no Android &ndash; embora a convenção seja nomear a primeira atividade em um aplicativo como `MainActivity`, não faz diferença para o Android se ela for nomeada diferentemente.

Ao abrir **MainActivity.cs**, você pode ver que a classe `MainActivity` é uma *subclasse* da classe `Activity` e que a Atividade é adornada pelo atributo [Activity](xref:Android.App.ActivityAttribute):

```csharp
[Activity (Label = "Phone Word", MainLauncher = true)]
public class MainActivity : Activity
{
  ...
}
```

O atributo `Activity` registra a atividade com o manifesto do Android. Isso permite que o Android saiba que essa classe faz parte do aplicativo **Phoneword** gerenciado por esse manifesto. A propriedade `Label` define o texto que será exibido na parte superior da tela.

A propriedade `MainLauncher` indica ao Android para exibir essa atividade quando o aplicativo é iniciado. Essa propriedade torna-se importante à medida que você adiciona mais atividades (telas) ao aplicativo, conforme é explicado no guia [Multitela do Hello, Android](~/android/get-started/hello-android-multiscreen/index.md).

Agora que os conceitos básicos de `MainActivity` foram abordados, é hora de se aprofundar no código da atividade, introduzindo o _Ciclo de vida da atividade_.

### <a name="activity-lifecycle"></a>Ciclo de vida da atividade

No Android, as atividades passam por diferentes estágios de um ciclo de vida, dependendo de suas interações com o usuário. Atividades podem ser criadas, iniciadas e colocadas em pausa, retomadas e destruídas e assim por diante. A classe `Activity` contém métodos que o sistema chama em determinados pontos no ciclo de vida da tela. O diagrama a seguir ilustra uma vida típica de uma atividade, bem como alguns dos métodos de ciclo de vida correspondentes:

[![Ciclo de vida da atividade](hello-android-deepdive-images/04-lifecycle-sml.png)](hello-android-deepdive-images/04-lifecycle.png#lightbox)

Substituindo `Activity` métodos de ciclo de vida, você pode controlar como a atividade é carregada, como ela reage ao usuário e até mesmo o que acontece depois que ela desaparece da tela do dispositivo. Por exemplo, você pode substituir os métodos de ciclo de vida no diagrama acima para executar algumas tarefas importantes:

- **OnCreate** &ndash; cria exibições, inicializa variáveis e executa outras tarefas de preparação que devem ser realizadas antes que o usuário veja a atividade. Este método é chamado apenas uma vez quando a atividade é carregada na memória. 

- **OnResume** &ndash; executa as tarefas que devem ocorrer sempre que a Atividade retornar para a tela do dispositivo.

- **OnPause** &ndash; executa as tarefas que devem ocorrer sempre que a Atividade sair da tela do dispositivo.

Quando você adiciona código personalizado para um método de ciclo de vida no `Activity`, você *substitui* a *implementação base* desse método de ciclo de vida. Você aproveita o método de ciclo de vida existente (que já tem algum código anexado) e amplia esse método com seu próprio código. Você chama a implementação base de dentro do método para garantir que o código original seja executado antes do novo código. Um exemplo disso é ilustrado na próxima seção.

O ciclo de vida da atividade é uma parte importante e complexa do Android. Se você gostaria de saber mais sobre as atividades após concluir a série _Introdução_, leia o guia [Ciclo de vida da atividade](~/android/app-fundamentals/activity-lifecycle/index.md). Neste guia, o próximo item em foco é o primeiro estágio do ciclo de vida de atividade, `OnCreate`.

### <a name="oncreate"></a>OnCreate

O Android chama o método `OnCreate` de `Activity` quando ele cria a atividade (antes da tela ser apresentada ao usuário). Você pode substituir o método de ciclo de vida `OnCreate` para criar exibições e preparar sua atividade para contato com o usuário:

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    // Set our view from the "main" layout resource
    SetContentView (Resource.Layout.Main);
    // Additional setup code will go here
}
```

::: zone pivot="windows"

No aplicativo **Phoneword**, a primeira coisa a fazer em `OnCreate` é carregar a interface do usuário criada no Android Designer. Para carregar a interface do usuário, chame `SetContentView` e passe para ele o *nome do recurso de layout* para o arquivo de layout: **activity_main.axml**. O layout está localizado em `Resource.Layout.activity_main`:

```csharp
SetContentView (Resource.Layout.activity_main);
```

Quando o `MainActivity` é iniciado, ele cria uma exibição com base no conteúdo do arquivo **activity_main.axml**.

::: zone-end
::: zone pivot="macos"

No aplicativo **Phoneword**, a primeira coisa a fazer em `OnCreate` é carregar a interface do usuário criada no Android Designer. Para carregar a interface do usuário, chame `SetContentView` e passe para ele o *nome do recurso de layout* para o arquivo de layout: **Main.axml**. O layout está localizado em `Resource.Layout.Main`:

```csharp
SetContentView (Resource.Layout.Main);
```

Quando `MainActivity` é iniciado, ele cria uma exibição com base no conteúdo do arquivo **Main.axml**. Observe que o nome do arquivo de layout é correspondente ao nome da atividade &ndash; *Main*.axml é o layout da atividade *Main*. Isso não é necessário do ponto de vista do Android, mas quando você começar a adicionar mais telas ao aplicativo, você descobrirá que essa convenção de nomenclatura facilita corresponder o arquivo de código ao arquivo de layout.

::: zone-end

Depois que o arquivo de layout é preparado, você pode começar a pesquisar controles.
Para pesquisar um controle, chame `FindViewById` e passar a ID de recurso do controle:

```csharp
EditText phoneNumberText = FindViewById<EditText>(Resource.Id.PhoneNumberText);
Button translateButton = FindViewById<Button>(Resource.Id.TranslateButton);
TextView translatedPhoneWord = FindViewById<TextView>(Resource.Id.TranslatedPhoneWord);
```

Agora que você tem referências para os controles no arquivo de layout, você pode começar a programá-los para responder à interação do usuário.

### <a name="responding-to-user-interaction"></a>Respondendo à interação do usuário

No Android, o evento `Click` escuta em busca do toque do usuário. Nesse aplicativo, o evento `Click` foi tratado com um lambda, mas também seria possível usar um manipulador de eventos nomeado ou delegado. O código do último **TranslateButton** era semelhante ao seguinte: 

```csharp
translateButton.Click += (sender, e) =>
{
    // Translate user's alphanumeric phone number to numeric
    translatedNumber = PhonewordTranslator.ToNumber(phoneNumberText.Text);
    if (string.IsNullOrWhiteSpace(translatedNumber))
    {
        translatedPhoneWord.Text = string.Empty;
    }
    else
    {
        translatedPhoneWord.Text = translatedNumber;
    }
};
```

## <a name="testing-deployment-and-finishing-touches"></a>Testes, implantação e toques finais

Ambos o Visual Studio para Mac e o Visual Studio oferecem várias opções para testar e implantar um aplicativo. Esta seção aborda as opções de depuração, demonstra o teste de aplicativos em um dispositivo e apresenta ferramentas para a criação de ícones do aplicativo personalizados para diferentes densidades de tela.

### <a name="debugging-tools"></a>Ferramentas de depuração

Problemas no código do aplicativo podem ser difíceis de diagnosticar. Para ajudar a diagnosticar problemas de código complexos, você pode [definir um ponto de interrupção](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/set_a_breakpoint), [examinar o código passo a passo](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/step_through_code) ou [enviar informações para a janela de Log](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/output_information_to_log_window).

### <a name="deploy-to-a-device"></a>Implantar em um dispositivo

O emulador é um bom começo para implantar e testar um aplicativo, mas os usuários não consumirão o aplicativo final em um emulador. É uma boa prática para testar aplicativos em um dispositivo real antecipadamente e com frequência.

Antes de um dispositivo Android poder ser usado para testar aplicativos, ele precisa ser configurado para desenvolvimento. O guia [Configurar o dispositivo para desenvolvimento](~/android/get-started/installation/set-up-device-for-development.md) fornece instruções completas de como se preparar um dispositivo para o desenvolvimento.

::: zone pivot="windows"

Depois que o dispositivo estiver configurado, você poderá implantar nele conectando-o, selecionando-o na caixa de diálogo **Selecionar Dispositivo** e iniciando o aplicativo:

![Selecionar dispositivo de depuração](hello-android-deepdive-images/vs/06-select-device.png "Selecionar dispositivo de depuração")

::: zone-end
::: zone pivot="macos"

Depois que o dispositivo estiver configurado, você poderá implantar nele conectando-o, pressionando **Iniciar (reproduzir)** , selecionando-o na caixa de diálogo **Selecionar Dispositivo** e pressionando **OK**:

[![Selecionar dispositivo de depuração](hello-android-deepdive-images/xs/06-select-device-sml.png)](hello-android-deepdive-images/xs/06-select-device.png#lightbox)

::: zone-end

Isso inicia o aplicativo no dispositivo:

[![Insira Phoneword](hello-android-deepdive-images/05-enter-phoneword-sml.png)](hello-android-deepdive-images/05-enter-phoneword.png#lightbox)

### <a name="set-icons-for-different-screen-densities"></a>Definir ícones para diferentes densidades de tela

Dispositivos Android vêm em diferentes tamanhos e resoluções de tela e nem todas as imagens têm uma boa aparência em todas as telas. Por exemplo, aqui está uma captura de tela de um ícone de baixa densidade em um Nexus 5 de alta densidade. Observe quão desfocado ele está em comparação com os ícones ao redor:

[![Ícone desfocado](hello-android-deepdive-images/06-blurry-icon-sml.png)](hello-android-deepdive-images/06-blurry-icon.png#lightbox)

Como precaução para isso, é recomendável adicionar ícones de resoluções diferentes à pasta **Resources**. O Android fornece versões diferentes da pasta **mipmap** para lidar com ícones do inicializador de diferentes densidades, incluindo *mdpi* para telas de densidade média, *hdpi* para telas de densidade alta e *xhdpi*, *xxhdpi* e *xxxhdpi* para telas de densidade muito alta. Ícones de tamanhos variados são armazenados nas pastas **mipmap-** apropriadas:

::: zone pivot="windows"

![pastas mipmap](hello-android-deepdive-images/vs/07-mipmap-folders.png "pastas mipmap")

::: zone-end
::: zone pivot="windows"

[![Pastas mipmap](hello-android-deepdive-images/xs/07-mipmap-folders-sml.png)](hello-android-deepdive-images/xs/07-mipmap-folders.png#lightbox)

::: zone-end

O Android selecionará o ícone com a densidade apropriada:

[![Ícones na densidade apropriada](hello-android-deepdive-images/07-appropriate-density-sml.png)](hello-android-deepdive-images/07-appropriate-density.png#lightbox)

### <a name="generate-custom-icons"></a>Gerar ícones personalizados

Nem todos têm um designer disponível para criar os ícones personalizados e iniciar imagens que um aplicativo precisa destacar. Aqui estão várias abordagens alternativas para gerar arte do aplicativo personalizado:

::: zone pivot="windows"

- [Android Asset Studio](https://romannurik.github.io/AndroidAssetStudio/index.html) &ndash; Um gerador baseado na Web e usado no navegador para todos os tipos de ícones do Android, com links para outras ferramentas úteis da comunidade. Ele funciona melhor no Google Chrome.

- Visual Studio &ndash; você pode usar isso para criar um conjunto de ícones simples para seu aplicativo diretamente no IDE.

- [Glyphish](https://www.glyphish.com/) &ndash; conjuntos de ícones predefinidos de alta qualidade para download gratuito e para compra.

- [Fiverr](https://www.fiverr.com/) &ndash; escolha dentre uma variedade de designers para criar um ícone definido para você, com valores partindo de US$ 5. Mesmo que os resultados com ele nem sempre sejam os esperados, trata-se de um bom recurso para quem precisa criar ícones rapidamente.

::: zone-end
::: zone pivot="macos"

- [Android Asset Studio](https://romannurik.github.io/AndroidAssetStudio/index.html) &ndash; Um gerador baseado na Web e usado no navegador para todos os tipos de ícones do Android, com links para outras ferramentas úteis da comunidade. Ele funciona melhor no Google Chrome.

- [Sketch 3](https://itunes.apple.com/us/app/sketch/id852320343?mt=12) &ndash; O Sketch é um aplicativo do Mac para a criação de interfaces do usuário, ícones e muito mais. Este é o aplicativo que foi usado para criar o conjunto de Ícones e imagens de inicialização para aplicativos Xamarin. O Sketch 3 está disponível na loja de aplicativos e custa aproximadamente US$ 80. Você pode experimentar a [Ferramenta Sketch](https://bohemiancoding.com/sketch/tool/) gratuita também.

- [Pixelmator](https://www.pixelmator.com/) &ndash; um aplicativo de edição de imagem versátil para Mac que custa aproximadamente US$ 30.

- [Glyphish](https://www.glyphish.com/) &ndash; conjuntos de ícones predefinidos de alta qualidade para download gratuito e para compra.

- [Fiverr](https://www.fiverr.com/) &ndash; escolha dentre uma variedade de designers para criar um ícone definido para você, com valores partindo de US$ 5. Mesmo que os resultados com ele nem sempre sejam os esperados, trata-se de um bom recurso para quem precisa criar ícones rapidamente.

::: zone-end

Para obter mais informações sobre tamanhos de ícones e requisitos, consulte o guia [Recursos do Android](~/android/app-fundamentals/resources-in-android/index.md).

::: zone pivot="macos"

### <a name="adding-google-play-services-packages"></a>Adicionando pacotes do Google Play Services

O _Google Play Services_ é um conjunto de bibliotecas de complementos que permite aos desenvolvedores Android tirar proveito dos recursos mais recentes do Google como o Google Maps, Google Cloud Messaging e cobrança no aplicativo.
Anteriormente, as associações para todas as bibliotecas do Google Play Services eram fornecidas pelo Xamarin na forma de um único pacote &ndash; do Visual Studio para Mac em diante, uma caixa de diálogo de novo projeto está disponível para selecionar quais pacotes do Google Play Services incluir em seu aplicativo.

Para adicionar uma ou mais bibliotecas do Google Play Services, clique com o botão direito do mouse no nó **Pacotes** na sua árvore de projeto e clique em **Adicionar Google Play Service...** :

[![Adicionar serviço do Google Play](hello-android-deepdive-images/xs/08-add-google-play-services-sml.png)](hello-android-deepdive-images/xs/08-add-google-play-services.png#lightbox)

Quando a caixa de diálogo **Adicionar Google Play Services** é apresentada, selecione os pacotes (nugets) que você deseja adicionar ao seu projeto:

[![Selecionar pacotes](hello-android-deepdive-images/xs/09-add-dialog-sml.png)](hello-android-deepdive-images/xs/09-add-dialog.png#lightbox)

Quando você seleciona um serviço e clica em **Adicionar Pacote**, o Visual Studio para Mac baixa e instala o pacote que você seleciona, bem como eventuais pacotes dependentes do Google Play Services exigidos por ele. Em alguns casos, você pode ver uma caixa de diálogo **Aceitação da Licença** que exige que você clique em **Aceitar** antes que os pacotes sejam instalados:

[![Aceitação da licença](hello-android-deepdive-images/xs/10-license-acceptance-sml.png)](hello-android-deepdive-images/xs/10-license-acceptance.png#lightbox)

::: zone-end

## <a name="summary"></a>Resumo

Parabéns! Agora você deve ter uma compreensão sólida dos componentes de um aplicativo Xamarin.Android, bem como das ferramentas necessárias para criá-lo.

No próximo tutorial da série _Introdução_, você ampliará seu aplicativo para lidar com várias telas conforme você explora conceitos e arquitetura Android mais avançados.
