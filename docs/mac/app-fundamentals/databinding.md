---
title: "Associação de dados e a codificação de chave-valor"
description: "Este artigo aborda o uso de chave-valor de codificação e chave-valor observando para permitir a associação de dados para os elementos de interface do usuário no construtor de Interface do Xcode."
ms.topic: article
ms.prod: xamarin
ms.assetid: 72594395-0737-4894-8819-3E1802864BE7
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: b7ffd069a8c99c2cdfd0ecb58fe7ef762e5a46f3
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="data-binding-and-key-value-coding"></a>Associação de dados e a codificação de chave-valor

_Este artigo aborda o uso de chave-valor de codificação e chave-valor observando para permitir a associação de dados para os elementos de interface do usuário no construtor de Interface do Xcode._

## <a name="overview"></a>Visão geral

Ao trabalhar com c# e .NET em um aplicativo de Xamarin.Mac, você tem acesso para o mesmo valor de chave de codificação e técnicas de associação de dados que um desenvolvedor trabalhando em *Objective-C* e *Xcode* does. Como Xamarin.Mac se integra diretamente com o Xcode, você pode usar do Xcode _Interface construtor_ para associar dados com elementos de interface do usuário em vez de escrever código.

Usando técnicas de associação em seu aplicativo Xamarin.Mac de dados e codificação de chave-valor, você pode diminuir significativamente a quantidade de código que você precisa escrever e manter para preencher e trabalhar com elementos de interface do usuário. Você também tem a vantagem de desligamento ainda mais seus dados de backup (_modelo de dados_) do seu front end Interface do usuário (_Model-View-Controller_), à esquerda para mais fáceis de manter, mais flexível de aplicativos Design.

[![Um exemplo de aplicativo em execução](databinding-images/intro01.png "um exemplo de aplicativo em execução")](databinding-images/intro01-large.png#lightbox)

Neste artigo, vamos abordar os fundamentos de trabalhar com codificação de chave-valor e associação de dados em um aplicativo Xamarin.Mac. É altamente recomendável que você leia o [Hello, Mac](~/mac/get-started/hello-mac.md) artigo primeiro, especificamente o [Introdução ao construtor da Interface e Xcode](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder) e [tomadas e ações](~/mac/get-started/hello-mac.md#Outlets_and_Actions) seções, como ele aborda os principais conceitos e técnicas que será usado neste artigo.

Talvez você queira dar uma olhada a [classes expondo c# / métodos para Objective-C](~/mac/internals/how-it-works.md) seção o [Xamarin.Mac internos](~/mac/internals/how-it-works.md) de documento, ele explica o `Register` e `Export` atributos usado para conectar as classes c# objetos Objective-C e a interface do usuário elementos.

<a name="What_is_Key-Value_Coding" />

## <a name="what-is-key-value-coding"></a>O que é a codificação de chave-valor

Codificação de chave-valor (KVC) é um mecanismo para acessar propriedades de um objeto indiretamente, usando métodos de acessador ou chaves (especialmente formatadas cadeias de caracteres) para identificar as propriedades em vez de acessá-los por meio de variáveis de instância (`get/set`). Ao implementar acessadores compatível com codificação de chave-valor em seu aplicativo Xamarin.Mac, você obtém acesso a outros recursos de macOS (anteriormente conhecida como OS X) como chave-valor observando (KVO), associação de dados, dados principais, associações de Cocoa e ScriptableType.

Usando técnicas de associação em seu aplicativo Xamarin.Mac de dados e codificação de chave-valor, você pode diminuir significativamente a quantidade de código que você precisa escrever e manter para preencher e trabalhar com elementos de interface do usuário. Você também tem a vantagem de desligamento ainda mais seus dados de backup (_modelo de dados_) do seu front end Interface do usuário (_Model-View-Controller_), à esquerda para mais fáceis de manter, mais flexível de aplicativos Design. 

Por exemplo, vejamos a seguinte definição de classe de um objeto KVC compatível:

```csharp
using System;
using Foundation;

namespace MacDatabinding
{
    [Register("PersonModel")]
    public class PersonModel : NSObject
    {
        private string _name = "";
        
        [Export("Name")]
        public string Name {
            get { return _name; }
            set {
                WillChangeValue ("Name");
                _name = value;
                DidChangeValue ("Name");
            }
        }
        
        public PersonModel ()
        {
        }
    }
}
```

Primeiro, o `[Register("PersonModel")]` registra a classe de atributo e expõe a objetivo-C. Em seguida, a classe precisa herdar de `NSObject` (ou uma subclasse que herda de `NSObject`), isso adiciona várias base método que permitem que a classe seja KVC compatível. Em seguida, o `[Export("Name")]` atributo expõe o `Name` propriedade e define o valor de chave que será usado posteriormente para acessar a propriedade por meio de técnicas KVC e KVO. 

Por fim, para poder ser observado de chave-valor é alterado para o valor da propriedade, o acessador deve encapsular alterações para seu valor em `WillChangeValue` e `DidChangeValue` chamadas de método (especificando a mesma chave que o `Export` atributo).  Por exemplo:

```csharp
set {
    WillChangeValue ("Name");
    _name = value;
    DidChangeValue ("Name");
}
```

Esta etapa é _muito_ importante para associação de dados no Xcode do construtor de Interface (conforme veremos neste artigo).

Para obter mais informações, consulte da Apple [guia de programação de codificação de chave-valor](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueCoding/index.html).

### <a name="keys-and-key-paths"></a>Chaves e caminhos de chave

Um _chave_ é uma cadeia de caracteres que identifica uma propriedade específica de um objeto. Normalmente, uma chave corresponde ao nome de um método de acessador de um objeto compatível de codificação de chave-valor. As chaves devem usar codificação ASCII, normalmente começam com uma letra minúscula e não pode conter espaço em branco. Assim, considerando o exemplo acima, `Name` seria um valor de chave de `Name` propriedade o `PersonModel` classe. A chave e o nome da propriedade que eles expõem não precisam ser o mesmo, mas na maioria dos casos eles estão.

Um _caminho de chave_ é uma cadeia de caracteres de ponto separados chaves usadas para especificar uma hierarquia de desviar das propriedades do objeto. A propriedade da chave primeiro na sequência é relativo ao destinatário, e cada chave subsequente é avaliado em relação ao valor da propriedade anterior. Da mesma forma você pode usar a notação de ponto para percorrer um objeto e suas propriedades na classe c#.

Por exemplo, se você expandir o `PersonModel` classe e adicionado `Child` propriedade:

```csharp
using System;
using Foundation;

namespace MacDatabinding
{
    [Register("PersonModel")]
    public class PersonModel : NSObject
    {
        private string _name = "";
        private PersonModel _child = new PersonModel();
        
        [Export("Name")]
        public string Name {
            get { return _name; }
            set {
                WillChangeValue ("Name");
                _name = value;
                DidChangeValue ("Name");
            }
        }
        
        [Export("Child")]
        public PersonModel Child {
            get { return _child; }
            set {
                WillChangeValue ("Child");
                _child = value;
                DidChangeValue ("Child");
            }
        }
        
        public PersonModel ()
        {
        }
    }
}
```

O caminho da chave para o nome do filho seria `self.Child.Name` ou simplesmente `Child.Name` (com base em como o valor de chave estava sendo usado).

### <a name="getting-values-using-key-value-coding"></a>Obtendo valores usando a codificação de chave-valor

O `ValueForKey` método retornará o valor para a chave especificada (como uma `NSString`) relativo para a instância da classe KVC recebimento da solicitação. Por exemplo, se `Person` é uma ocorrência da `PersonModel` classe definida acima:

```csharp
// Read value 
var name = Person.ValueForKey (new NSString("Name"));
```

Isso retornará o valor de `Name` propriedade para a instância do `PersonModel`. 

### <a name="setting-values-using-key-value-coding"></a>Valores de configuração usando a codificação de chave-valor

Da mesma forma, o `SetValueForKey` o valor definido para a chave especificada (como uma `NSString`) relativo para a instância da classe KVC recebimento da solicitação. Novamente, usando uma instância do `PersonModel` de classe, conforme mostrado abaixo:

```csharp
// Write value
Person.SetValueForKey(new NSString("Jane Doe"), new NSString("Name"));
```

Alteraria o valor de `Name` propriedade `Jane Doe`.

<a name="Observing_Value_Changes" />

### <a name="observing-value-changes"></a>Observando as alterações de valor

Usando a chave-valor observando (KVO), você pode anexar um observador para uma chave específica de uma classe compatível com KVC e ser notificado sempre que o valor dessa chave é modificado (usando técnicas KVC ou acessar diretamente a propriedade especificada em código c#). Por exemplo:

```csharp
// Watch for the name value changing
Person.AddObserver ("Name", NSKeyValueObservingOptions.New, (sender) => {
    // Inform caller of selection change
    Console.WriteLine("New Name: {0}", Person.Name)
});
```

Agora, a qualquer momento o `Name` propriedade o `Person` instância do `PersonModel` classe for modificada, o novo valor é gravado no console. 

Para obter mais informações, consulte da Apple [Introdução ao guia de programação observando chave-valor](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueObserving/KeyValueObserving.html#//apple_ref/doc/uid/10000177i).

## <a name="data-binding"></a>Associação de dados

As seções a seguir mostrará como você pode usar uma codificação de chave-valor e chave-valor observando classe compatível para associar dados aos elementos de interface do usuário no construtor de Interface do Xcode, em vez de ler e gravar valores usando o código c#. Dessa forma você separar o _modelo de dados_ das exibições que são usadas para exibi-los, tornando o aplicativo Xamarin.Mac mais flexíveis e fáceis de manter. Você diminuir significativamente a quantidade de código que deve ser gravado.

<a name="Defining_your_Data_Model" />

### <a name="defining-your-data-model"></a>Definindo o modelo de dados

Antes de você pode associar dados de um elemento de interface do usuário no construtor de Interface, você deve ter uma classe compatível com KVC/KVO definida no seu aplicativo Xamarin.Mac para atuar como o _modelo de dados_ para a associação. O modelo de dados fornece todos os dados que serão exibidos na Interface do usuário e recebe todas as modificações de dados que o usuário faz na interface do usuário ao executar o aplicativo.

Por exemplo, se você estava gravando um aplicativo gerenciado de um grupo de funcionários, você pode usar a seguinte classe para definir o modelo de dados:

```csharp
using System;
using Foundation;
using AppKit;

namespace MacDatabinding
{
    [Register("PersonModel")]
    public class PersonModel : NSObject
    {
        #region Private Variables
        private string _name = "";
        private string _occupation = "";
        private bool _isManager = false;
        private NSMutableArray _people = new NSMutableArray();
        #endregion

        #region Computed Properties
        [Export("Name")]
        public string Name {
            get { return _name; }
            set {
                WillChangeValue ("Name");
                _name = value;
                DidChangeValue ("Name");
            }
        }

        [Export("Occupation")]
        public string Occupation {
            get { return _occupation; }
            set {
                WillChangeValue ("Occupation");
                _occupation = value;
                DidChangeValue ("Occupation");
            }
        }

        [Export("isManager")]
        public bool isManager {
            get { return _isManager; }
            set {
                WillChangeValue ("isManager");
                WillChangeValue ("Icon");
                _isManager = value;
                DidChangeValue ("isManager");
                DidChangeValue ("Icon");
            }
        }

        [Export("isEmployee")]
        public bool isEmployee {
            get { return (NumberOfEmployees == 0); }
        }

        [Export("Icon")]
        public NSImage Icon {
            get {
                if (isManager) {
                    return NSImage.ImageNamed ("group.png");
                } else {
                    return NSImage.ImageNamed ("user.png");
                }
            }
        }

        [Export("personModelArray")]
        public NSArray People {
            get { return _people; }
        }

        [Export("NumberOfEmployees")]
        public nint NumberOfEmployees {
            get { return (nint)_people.Count; }
        }
        #endregion

        #region Constructors
        public PersonModel ()
        {
        }

        public PersonModel (string name, string occupation)
        {
            // Initialize
            this.Name = name;
            this.Occupation = occupation;
        }

        public PersonModel (string name, string occupation, bool manager)
        {
            // Initialize
            this.Name = name;
            this.Occupation = occupation;
            this.isManager = manager;
        }
        #endregion

        #region Array Controller Methods
        [Export("addObject:")]
        public void AddPerson(PersonModel person) {
            WillChangeValue ("personModelArray");
            isManager = true;
            _people.Add (person);
            DidChangeValue ("personModelArray");
        }

        [Export("insertObject:inPersonModelArrayAtIndex:")]
        public void InsertPerson(PersonModel person, nint index) {
            WillChangeValue ("personModelArray");
            _people.Insert (person, index);
            DidChangeValue ("personModelArray");
        }

        [Export("removeObjectFromPersonModelArrayAtIndex:")]
        public void RemovePerson(nint index) {
            WillChangeValue ("personModelArray");
            _people.RemoveObject (index);
            DidChangeValue ("personModelArray");
        }

        [Export("setPersonModelArray:")]
        public void SetPeople(NSMutableArray array) {
            WillChangeValue ("personModelArray");
            _people = array;
            DidChangeValue ("personModelArray");
        }
        #endregion
    }
}
```

A maioria dos recursos desta classe foram abordada no [o que é a codificação de chave-valor](#What_is_Key-Value_Coding) seção acima. No entanto, vamos examinar alguns elementos específicos e algumas inclusões feitas para permitir que essa classe atue como um modelo de dados para **controladores de matriz** e **árvore controladores** (que será usado posteriormente para dados associar **modos de exibição de árvore**, **modos de exibição de estrutura de tópicos** e **exibições de coleção**).

Primeiro, porque um funcionário pode ser um gerente, usamos um `NSArray` (especificamente um `NSMutableArray` os valores podem ser modificadas) para permitir que os funcionários que eles gerenciados a ser anexado a eles:

```csharp
private NSMutableArray _people = new NSMutableArray();
...

[Export("personModelArray")]
public NSArray People {
    get { return _people; }
}
```

Duas coisas a observar aqui:

1. Usamos uma `NSMutableArray` em vez de um padrão c# matriz ou coleção, pois esse é um requisito para associar dados a controles de AppKit como **modos de exibição de tabela**, **modos de exibição de estrutura de tópicos** e **coleções** .
2. Estamos expostos a matriz de funcionários por convertê-la para um `NSArray` para dados para fins de associação e alterado seu c# formatadas como nome, `People`, para que se espera que a associação de dados, `personModelArray` no formulário **{class_name} matriz** (Observação Se o primeiro caractere foi feito minúsculas).

Em seguida, precisamos adicionar alguns especialmente nome métodos públicos para dar suporte a **controladores de matriz** e **árvore controladores**:

```csharp
[Export("addObject:")]
public void AddPerson(PersonModel person) {
    WillChangeValue ("personModelArray");
    isManager = true;
    _people.Add (person);
    DidChangeValue ("personModelArray");
}

[Export("insertObject:inPersonModelArrayAtIndex:")]
public void InsertPerson(PersonModel person, nint index) {
    WillChangeValue ("personModelArray");
    _people.Insert (person, index);
    DidChangeValue ("personModelArray");
}

[Export("removeObjectFromPersonModelArrayAtIndex:")]
public void RemovePerson(nint index) {
    WillChangeValue ("personModelArray");
    _people.RemoveObject (index);
    DidChangeValue ("personModelArray");
}

[Export("setPersonModelArray:")]
public void SetPeople(NSMutableArray array) {
    WillChangeValue ("personModelArray");
    _people = array;
    DidChangeValue ("personModelArray");
}
```

Essas opções permitem que os controladores solicitar e modificar os dados que elas sejam exibidas. Como o exposto `NSArray` acima, eles têm uma convenção de nomenclatura muito específica (que é diferente das convenções de nomenclatura típicas c#):

- `addObject:` -Adiciona um objeto para a matriz.
- `insertObject:in{class_name}ArrayAtIndex:` -Onde `{class_name}` é o nome da sua classe. Esse método insere um objeto na matriz de um determinado índice.
- `removeObjectFrom{class_name}ArrayAtIndex:` -Onde `{class_name}` é o nome da sua classe. Esse método Remove o objeto na matriz de um determinado índice.
- `set{class_name}Array:` -Onde `{class_name}` é o nome da sua classe. Esse método permite que você substitua o transportam existente com um novo.

Dentro desses métodos, integramos alterações para a matriz de `WillChangeValue` e `DidChangeValue` mensagens para fins de conformidade KVO.

Finalmente, desde o `Icon` baseia-se no valor da propriedade de `isManager` alteração na propriedade, o `isManager` propriedade pode não ser refletida no `Icon` para elementos de interface do usuário associado de dados (durante KVO):

```csharp
[Export("Icon")]
public NSImage Icon {
    get {
        if (isManager) {
            return NSImage.ImageNamed ("group.png");
        } else {
            return NSImage.ImageNamed ("user.png");
        }
    }
}
``` 

Para corrigir que, usamos o seguinte código:

```csharp
[Export("isManager")]
public bool isManager {
    get { return _isManager; }
    set {
        WillChangeValue ("isManager");
        WillChangeValue ("Icon");
        _isManager = value;
        DidChangeValue ("isManager");
        DidChangeValue ("Icon");
    }
}
```

Observe que, além de sua própria chave, o `isManager` acessador também está enviando o `WillChangeValue` e `DidChangeValue` mensagens para o `Icon` para que ele veja também a alteração da chave.

Usaremos o `PersonModel` modelo de dados em todo o restante deste artigo.

<a name="Simple_Data_Binding" />

### <a name="simple-data-binding"></a>Vinculação de dados simples

Com o modelo de dados definidas, vamos examinar um exemplo simples de associação de dados no construtor de Interface do Xcode. Por exemplo, vamos adicionar um formulário para nosso aplicativo Xamarin.Mac que pode ser usado para editar o `PersonModel` que definimos acima. Vamos adicionar alguns campos de texto e uma caixa de seleção para exibir e editar propriedades de nosso modelo.

Primeiro, vamos adicionar um novo **View Controller** para nosso **Main.storyboard** no construtor de Interface e nome de sua classe `SimpleViewController`: 

[![Adicionar um novo controlador de exibição](databinding-images/simple01.png "adicionando um novo controlador de exibição")](databinding-images/simple01-large.png#lightbox)

Em seguida, retorne ao Visual Studio para Mac, edite o **SimpleViewController.cs** arquivo (que foi adicionado automaticamente ao nosso projeto) e expor uma instância das `PersonModel` que será nosso formulário de associação de dados. Adicione o seguinte código:

```csharp
private PersonModel _person = new PersonModel();
...

[Export("Person")]
public PersonModel Person {
    get {return _person; }
    set {
        WillChangeValue ("Person");
        _person = value;
        DidChangeValue ("Person");
    }
}
```

Em seguida quando a exibição é carregada, vamos criar uma instância do nosso `PersonModel` e preenchê-lo com este código:

```csharp
public override void ViewDidLoad ()
{
    base.AwakeFromNib ();

    // Set a default person
    var Craig = new PersonModel ("Craig Dunn", "Documentation Manager");
    Craig.AddPerson (new PersonModel ("Amy Burns", "Technical Writer"));
    Craig.AddPerson (new PersonModel ("Joel Martinez", "Web & Infrastructure"));
    Craig.AddPerson (new PersonModel ("Kevin Mullins", "Technical Writer"));
    Craig.AddPerson (new PersonModel ("Mark McLemore", "Technical Writer"));
    Craig.AddPerson (new PersonModel ("Tom Opgenorth", "Technical Writer"));
    Person = Craig;

}
```

Agora, precisamos criar nosso formulário, clique duas vezes o **Main.storyboard** arquivo para abri-lo para edição no construtor de Interface. Layout de formulário para a seguinte aparência:

[![Editando o storyboard no Xcode](databinding-images/simple02.png "editando o storyboard no Xcode")](databinding-images/simple02-large.png#lightbox)

Para vincular dados do formulário para o `PersonModel` que é exposto por meio de `Person` chave, faça o seguinte:

1. Selecione o **nome de funcionário** campo de texto e alternar para o **Inspetor associações**.
2. Verifique o **associar a** caixa e selecione **simples View Controller** na lista suspensa. Em seguida digite `self.Person.Name` para o **caminho de chave**: 

    [![Inserir o caminho da chave](databinding-images/simple03.png "digitando o caminho da chave")](databinding-images/simple03-large.png#lightbox)
3. Selecione o **ocupação** campo de texto e verifique se o **associar a** caixa e selecione **simples View Controller** na lista suspensa. Em seguida digite `self.Person.Occupation` para o **caminho de chave**:  

    [![Inserir o caminho da chave](databinding-images/simple04.png "digitando o caminho da chave")](databinding-images/simple04-large.png#lightbox)
4. Selecione o **funcionário for um gerente** caixa de seleção e verifique o **associar a** caixa e selecione **simples View Controller** na lista suspensa. Em seguida digite `self.Person.isManager` para o **caminho de chave**:  

    [![Inserir o caminho da chave](databinding-images/simple05.png "digitando o caminho da chave")](databinding-images/simple05-large.png#lightbox)
5. Selecione o **número de funcionários gerenciados** campo de texto e verifique se o **associar a** caixa e selecione **simples View Controller** na lista suspensa. Em seguida digite `self.Person.NumberOfEmployees` para o **caminho de chave**:  

    [![Inserir o caminho da chave](databinding-images/simple06.png "digitando o caminho da chave")](databinding-images/simple06-large.png#lightbox)
6. Se o funcionário não for um gerente, queremos ocultar o número de funcionários gerenciados rótulo e o campo de texto.
7. Selecione o **número de funcionários gerenciados** rótulo, expanda o **Hidden** turndown e verifique se o **associar a** caixa e selecione **simples View Controller** na lista suspensa. Em seguida digite `self.Person.isManager` para o **caminho de chave**:  

    [![Inserir o caminho da chave](databinding-images/simple07.png "digitando o caminho da chave")](databinding-images/simple07-large.png#lightbox)
8. Selecione `NSNegateBoolean` do **valor transformador** suspensa:  

    ![Selecionar a transformação de chave NSNegateBoolean](databinding-images/simple08.png "selecionando a transformação de chave NSNegateBoolean")
9. Isso informa a associação de dados que o rótulo ficará oculta se o valor da `isManager` é de propriedade `false`.
10. Repita as etapas 7 e 8 para o **número de funcionários gerenciados** campo de texto.
11. Salve suas alterações e retornar ao Visual Studio para Mac sincronizar com o Xcode.

Se você executar o aplicativo, os valores da `Person` propriedade preencherá automaticamente o formulário:

[![Mostrando um formulário preenchido automaticamente](databinding-images/simple09.png "mostrando um formulário preenchido automaticamente")](databinding-images/simple09-large.png#lightbox)

As alterações que faz com que os usuários para o formulário serão gravadas de volta para o `Person` propriedade no controlador de exibição. Por exemplo, se **funcionário é um Gerenciador de** atualizações a `Person` instância do nosso `PersonModel` e **número de funcionários gerenciados** rótulo e o campo de texto estão ocultos automaticamente (por meio de associação de dados):

[![Ocultando o número de funcionários não gerentes de](databinding-images/simple10.png "ocultando o número de funcionários não gerentes")](databinding-images/simple10-large.png#lightbox)

<a name="Table_View_Data_Binding" />

### <a name="table-view-data-binding"></a>Associação de dados de exibição de tabela

Agora que temos os fundamentos da associação de dados fora do caminho, vamos dar uma olhada em uma tarefa de vinculação de dados mais complexa usando um _controlador de matriz_ e associação de dados para uma exibição de tabela. Para obter mais informações sobre como trabalhar com modos de exibição de tabela, consulte nosso [modos de exibição de tabela](~/mac/user-interface/table-view.md) documentação.

Primeiro, vamos adicionar um novo **View Controller** para nosso **Main.storyboard** no construtor de Interface e nome de sua classe `TableViewController`:

[![Adicionar um novo controlador de exibição](databinding-images/table01.png "adicionando um novo controlador de exibição")](databinding-images/table01-large.png#lightbox)

Em seguida, vamos editar o **TableViewController.cs** arquivo (que foi adicionado automaticamente ao nosso projeto) e expõe uma matriz (`NSArray`) de `PersonModel` classes que será nosso formulário de associação de dados. Adicione o seguinte código:

```csharp
private NSMutableArray _people = new NSMutableArray();
...

[Export("personModelArray")]
public NSArray People {
    get { return _people; }
}
...

[Export("addObject:")]
public void AddPerson(PersonModel person) {
    WillChangeValue ("personModelArray");
    _people.Add (person);
    DidChangeValue ("personModelArray");
}

[Export("insertObject:inPersonModelArrayAtIndex:")]
public void InsertPerson(PersonModel person, nint index) {
    WillChangeValue ("personModelArray");
    _people.Insert (person, index);
    DidChangeValue ("personModelArray");
}

[Export("removeObjectFromPersonModelArrayAtIndex:")]
public void RemovePerson(nint index) {
    WillChangeValue ("personModelArray");
    _people.RemoveObject (index);
    DidChangeValue ("personModelArray");
}

[Export("setPersonModelArray:")]
public void SetPeople(NSMutableArray array) {
    WillChangeValue ("personModelArray");
    _people = array;
    DidChangeValue ("personModelArray");
}
```

Como no `PersonModel` classe acima no [definindo o modelo de dados](#Defining_your_Data_Model) seção, nós já exposto quatro métodos públicos especialmente nomeados para que os dados do controlador de matriz e leitura e gravação do nosso conjunto de `PersonModels`.

Em seguida quando a exibição é carregada, nós precisamos preencher a matriz com este código:

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Build list of employees
    AddPerson (new PersonModel ("Craig Dunn", "Documentation Manager", true));
    AddPerson (new PersonModel ("Amy Burns", "Technical Writer"));
    AddPerson (new PersonModel ("Joel Martinez", "Web & Infrastructure"));
    AddPerson (new PersonModel ("Kevin Mullins", "Technical Writer"));
    AddPerson (new PersonModel ("Mark McLemore", "Technical Writer"));
    AddPerson (new PersonModel ("Tom Opgenorth", "Technical Writer"));
    AddPerson (new PersonModel ("Larry O'Brien", "API Documentation Manager", true));
    AddPerson (new PersonModel ("Mike Norman", "API Documenter"));

}
```

Agora, precisamos criar nossa tabela de exibição, clique duas vezes o **Main.storyboard** arquivo para abri-lo para edição no construtor de Interface. Layout de tabela para a seguinte aparência:

[![Layout de um novo modo de exibição de tabela](databinding-images/table02.png "dispor um novo modo de exibição de tabela")](databinding-images/table02-large.png#lightbox)

Precisamos adicionar um **controlador de matriz** para fornecer os dados associados à nossa tabela, faça o seguinte:

1. Arraste um **controlador de matriz** do **Inspetor de biblioteca** até o **Editor de Interface**:  

    ![Selecionar um controlador de matriz da biblioteca](databinding-images/table03.png "selecionando um controlador de matriz da biblioteca")
2. Selecione **controlador de matriz** no **hierarquia Interface** e alterne para o **Inspetor de atributo**:  

    [![Selecionando o Inspetor de atributos](databinding-images/table04.png "selecionando o Inspetor de atributos")](databinding-images/table04-large.png#lightbox)
3. Digite `PersonModel` para o **nome da classe**, clique no **mais** botão e adicione três chaves. Nomeá-los `Name`, `Occupation` e `isManager`:  

    ![Adicionando os caminhos de chaves necessários](databinding-images/table05.png "adicionando os caminhos de chave necessários")
4. Isso indica que o controlador de matriz que ele está gerenciando uma matriz, e as propriedades que ele deve expor (por meio de chaves).
5. Alterne para o **Inspetor associações** e, em **matriz conteúdo** selecione **associar a** e **controlador de exibição de tabela**. Insira um **caminho da chave de modelo** de `self.personModelArray`:  

    ![Inserir um caminho de chave](databinding-images/table06.png "inserir um caminho de chave")
6. Isso vincula o controlador de matriz para a matriz de `PersonModels` que é exposta em nosso controlador de exibição.

Agora é preciso associar nossa tabela de exibição para o controlador de matriz, faça o seguinte:

1. Selecione o modo de exibição de tabela e o **associação Inspetor**:  

    [![Selecionando o Inspetor de associação](databinding-images/table07.png "selecionando o Inspetor de associação")](databinding-images/table07-large.png#lightbox)
2. Sob o **sumário** turndown, selecione **associar a** e **controlador de matriz**. Digite `arrangedObjects` para o **chave controlador** campo:  

    ![Definir a chave de controlador](databinding-images/table08.png "definindo a chave do controlador")
3. Selecione o **célula de exibição de tabela** sob o **funcionário** coluna. No **Inspetor associações** sob o **valor** turndown, selecione **associar a** e **exibição de célula de tabela**. Digite `objectValue.Name` para o **caminho da chave do modelo**:  

    [![Definir o caminho da chave modelo](databinding-images/table09.png "definindo o caminho da chave de modelo")](databinding-images/table09-large.png#lightbox)
4. `objectValue` é o atual `PersonModel` na matriz que está sendo gerenciada pelo controlador de matriz.
5. Selecione o **célula de exibição de tabela** sob o **ocupação** coluna. No **Inspetor associações** sob o **valor** turndown, selecione **associar a** e **exibição de célula de tabela**. Digite `objectValue.Occupation` para o **caminho da chave do modelo**:  

    [![Definir o caminho da chave modelo](databinding-images/table10.png "definindo o caminho da chave de modelo")](databinding-images/table10-large.png#lightbox)
6. Salve suas alterações e retornar ao Visual Studio para Mac sincronizar com o Xcode.

Se o aplicativo é executado, a tabela será preenchida com a matriz de `PersonModels`:

[![Executando o aplicativo](databinding-images/table11.png "executando o aplicativo")](databinding-images/table11-large.png#lightbox)

<a name="Outline_View_Data_Binding" />

### <a name="outline-view-data-binding"></a>Associação de dados de exibição de estrutura de tópicos

associação de dados em uma exibição de estrutura de tópicos é muito semelhante à associação em uma exibição de tabela. A principal diferença é que usaremos um **árvore controlador** em vez de um **controlador de matriz** para fornecer os dados associados para o modo de exibição de estrutura de tópicos. Para obter mais informações sobre como trabalhar com modos de exibição de estrutura de tópicos, consulte nosso [modos de exibição de estrutura de tópicos](~/mac/user-interface/outline-view.md) documentação.

Primeiro, vamos adicionar um novo **View Controller** para nosso **Main.storyboard** no construtor de Interface e nome de sua classe `OutlineViewController`: 

[![Adicionar um novo controlador de exibição](databinding-images/outline01.png "adicionando um novo controlador de exibição")](databinding-images/outline01-large.png#lightbox)

Em seguida, vamos editar o **OutlineViewController.cs** arquivo (que foi adicionado automaticamente ao nosso projeto) e expõe uma matriz (`NSArray`) de `PersonModel` classes que será nosso formulário de associação de dados. Adicione o seguinte código:

```csharp
private NSMutableArray _people = new NSMutableArray();
...

[Export("personModelArray")]
public NSArray People {
    get { return _people; }
}
...

[Export("addObject:")]
public void AddPerson(PersonModel person) {
    WillChangeValue ("personModelArray");
    _people.Add (person);
    DidChangeValue ("personModelArray");
}

[Export("insertObject:inPersonModelArrayAtIndex:")]
public void InsertPerson(PersonModel person, nint index) {
    WillChangeValue ("personModelArray");
    _people.Insert (person, index);
    DidChangeValue ("personModelArray");
}

[Export("removeObjectFromPersonModelArrayAtIndex:")]
public void RemovePerson(nint index) {
    WillChangeValue ("personModelArray");
    _people.RemoveObject (index);
    DidChangeValue ("personModelArray");
}

[Export("setPersonModelArray:")]
public void SetPeople(NSMutableArray array) {
    WillChangeValue ("personModelArray");
    _people = array;
    DidChangeValue ("personModelArray");
}
```

Como no `PersonModel` classe acima no [definindo o modelo de dados](#Defining_your_Data_Model) seção, nós já exposto quatro métodos públicos especialmente nomeados para que os dados do controlador de árvore e leitura e gravação do nosso conjunto de `PersonModels`.

Em seguida quando a exibição é carregada, nós precisamos preencher a matriz com este código:

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Build list of employees
    var Craig = new PersonModel ("Craig Dunn", "Documentation Manager");
    Craig.AddPerson (new PersonModel ("Amy Burns", "Technical Writer"));
    Craig.AddPerson (new PersonModel ("Joel Martinez", "Web & Infrastructure"));
    Craig.AddPerson (new PersonModel ("Kevin Mullins", "Technical Writer"));
    Craig.AddPerson (new PersonModel ("Mark McLemore", "Technical Writer"));
    Craig.AddPerson (new PersonModel ("Tom Opgenorth", "Technical Writer"));
    AddPerson (Craig);

    var Larry = new PersonModel ("Larry O'Brien", "API Documentation Manager");
    Larry.AddPerson (new PersonModel ("Mike Norman", "API Documenter"));
    AddPerson (Larry);

}
```

Agora, precisamos criar nossa modo de exibição de estrutura de tópicos, clique duas vezes o **Main.storyboard** arquivo para abri-lo para edição no construtor de Interface. Layout de tabela para a seguinte aparência:

[![Criando o modo de exibição de estrutura de tópicos](databinding-images/outline02.png "criar o modo de exibição de estrutura de tópicos")](databinding-images/outline02-large.png#lightbox)

Precisamos adicionar um **árvore controlador** para fornecer os dados associados ao nosso esboço, faça o seguinte:

1. Arraste um **árvore controlador** do **Inspetor de biblioteca** até o **Editor de Interface**:  

    ![Selecionar um controlador de árvore da biblioteca](databinding-images/outline03.png "selecionando um controlador de árvore da biblioteca")
2. Selecione **árvore controlador** no **hierarquia Interface** e alterne para o **Inspetor de atributo**:  

    [![Selecionando o Inspetor de atributo](databinding-images/outline04.png "selecionando o Inspetor de atributo")](databinding-images/outline04-large.png#lightbox)
3. Digite `PersonModel` para o **nome da classe**, clique no **mais** botão e adicione três chaves. Nomeá-los `Name`, `Occupation` e `isManager`:  

    ![Adicionando os caminhos de chaves necessários](databinding-images/outline05.png "adicionando os caminhos de chave necessários")
4. Isso indica que o controlador de árvore que ele está gerenciando uma matriz, e as propriedades que ele deve expor (por meio de chaves).
5. Sob o **árvore controlador** seção, digite `personModelArray` para **filhos**, insira `NumberOfEmployees` sob o **contagem** e insira `isEmployee` em  **Folha**:  

    ![Definir os caminhos de chaves do controlador de árvore](databinding-images/outline05.png "definindo os caminhos de chaves do controlador de árvore")
6. Isso informa o controlador de árvore onde encontrar qualquer filho de nós, são quantos nós filho existe e se o nó atual tem nós filhos.
7. Alterne para o **Inspetor associações** e, em **matriz conteúdo** selecione **associar a** e **proprietário do arquivo**. Insira um **caminho da chave de modelo** de `self.personModelArray`:  

    ![Editar o caminho da chave](databinding-images/outline06.png "editando o caminho da chave")
8. Isso vincula o controlador de árvore para a matriz de `PersonModels` que é exposta em nosso controlador de exibição.

Agora, precisamos ligação nosso modo de estrutura de tópicos com o controle de árvore, faça o seguinte:

1. Selecione o modo de exibição de estrutura de tópicos e no **Inspetor associação** selecione:  

    [![Selecionando o Inspetor de associação](databinding-images/outline07.png "selecionando o Inspetor de associação")](databinding-images/outline07-large.png#lightbox)
2. Sob o **exibir conteúdo da estrutura de tópicos** turndown, selecione **associar a** e **árvore controlador**. Digite `arrangedObjects` para o **chave controlador** campo:  

    ![Definindo a chave de controlador](databinding-images/outline08.png "definindo a chave do controlador")
3. Selecione o **célula de exibição de tabela** sob o **funcionário** coluna. No **Inspetor associações** sob o **valor** turndown, selecione **associar a** e **exibição de célula de tabela**. Digite `objectValue.Name` para o **caminho da chave do modelo**:  

    [![Inserir o caminho da chave modelo](databinding-images/outline09.png "inserindo o caminho de chave do modelo")](databinding-images/outline09-large.png#lightbox)
4. `objectValue` é o atual `PersonModel` na matriz que está sendo gerenciada pelo controlador de árvore.
5. Selecione o **célula de exibição de tabela** sob o **ocupação** coluna. No **Inspetor associações** sob o **valor** turndown, selecione **associar a** e **exibição de célula de tabela**. Digite `objectValue.Occupation` para o **caminho da chave do modelo**:  

    [![Inserir o caminho da chave modelo](databinding-images/outline10.png "inserindo o caminho de chave do modelo")](databinding-images/outline10-large.png#lightbox)
6. Salve suas alterações e retornar ao Visual Studio para Mac sincronizar com o Xcode.

Se o aplicativo é executado, a estrutura de tópicos será preenchida com nosso conjunto de `PersonModels`:

[![Executando o aplicativo](databinding-images/outline11.png "executando o aplicativo")](databinding-images/outline11-large.png#lightbox)

### <a name="collection-view-data-binding"></a>Associação de dados de exibição de coleção

Associação de dados com uma exibição de coleção é muito parecida com a associação à exibição de tabela, como um controlador de matriz é usado para fornecer dados para a coleção. Como o modo de exibição de coleção não tem um formato de exibição predefinido, mais trabalho é necessário para fornecer comentários de interação do usuário e controlar a seleção do usuário.

> [!IMPORTANT]
> Devido a um problema no Xcode 7 e macOS 10.11 (e superior), as exibições de coleção são não pode ser usado dentro de um arquivo de Storyboard (.storyboard). Como resultado, você precisará continuar a usar arquivos .xib para definir suas exibições de coleção para seus aplicativos Xamarin.Mac. Confira nosso [exibições de coleção](~/mac/user-interface/collection-view.md) documentação para obter mais informações.

<!--KKM 012/16/2015 - Once Apple fixes the issue with Xcode and Collection Views in Storyboards, we can uncomment this section.

First, let's add a new **View Controller** to our **Main.storyboard** file in Interface Builder and name its class `CollectionViewController`: 

![](databinding-images/collection01.png)

Next, let's edit the **CollectionViewController.cs** file (that was automatically added to our project) and expose an array (`NSArray`) of `PersonModel` classes that we will be data binding our form to. Add the following code:

```csharp
private NSMutableArray _people = new NSMutableArray();
...

[Export("personModelArray")]
public NSArray People {
    get { return _people; }
}
...

[Export("addObject:")]
public void AddPerson(PersonModel person) {
    WillChangeValue ("personModelArray");
    _people.Add (person);
    DidChangeValue ("personModelArray");
}

[Export("insertObject:inPersonModelArrayAtIndex:")]
public void InsertPerson(PersonModel person, nint index) {
    WillChangeValue ("personModelArray");
    _people.Insert (person, index);
    DidChangeValue ("personModelArray");
}

[Export("removeObjectFromPersonModelArrayAtIndex:")]
public void RemovePerson(nint index) {
    WillChangeValue ("personModelArray");
    _people.RemoveObject (index);
    DidChangeValue ("personModelArray");
}

[Export("setPersonModelArray:")]
public void SetPeople(NSMutableArray array) {
    WillChangeValue ("personModelArray");
    _people = array;
    DidChangeValue ("personModelArray");
}
```

Just like we did on the `PersonModel` class above in the [Defining your Data Model](#Defining_your_Data_Model) section, we've exposed four specially named public methods so that the Array Controller and read and write data from our collection of `PersonModels`.

Next when the View is loaded, we need to populate our array with this code:

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Build list of employees
    AddPerson (new PersonModel ("Craig Dunn", "Documentation Manager", true));
    AddPerson (new PersonModel ("Amy Burns", "Technical Writer"));
    AddPerson (new PersonModel ("Joel Martinez", "Web & Infrastructure"));
    AddPerson (new PersonModel ("Kevin Mullins", "Technical Writer"));
    AddPerson (new PersonModel ("Mark McLemore", "Technical Writer"));
    AddPerson (new PersonModel ("Tom Opgenorth", "Technical Writer"));
    AddPerson (new PersonModel ("Larry O'Brien", "API Documentation Manager", true));
    AddPerson (new PersonModel ("Mike Norman", "API Documenter"));

}
```

Now we need to create our Collection View, double-click the **Main.storyboard** file to open it for editing in Interface Builder. Layout the Collection View to look something like the following:

![](databinding-images/collection02.png)

When you add a Collection View to a User Interface design, two extra elements are also added:

1.  **Collection View Item** -  That manages a single instance of an item in the collection.
2.  **View** - A custom view that provides the visual size and appearance of each item in the collection. This view is tied to and managed by the **Collection View Item**.

Select the view and make it look like the following using an Image View and two Text Fields:

![](databinding-images/collection03.png)

One thing to note here, a `NSBox` was added behind everything in the view with the following attributes:

![](databinding-images/collection04.png)

We'll be using this box to provide feedback to the user when an item is selected in the Collection View.

We need to add an **Array Controller** to provide bound data to our collection, do the following:

1. Drag an **Array Controller** from the **Library Inspector** onto the **Interface Editor**: <br/>![](databinding-images/table03.png)
2. Select **Array Controller** in the **Interface Hierarchy** and switch to the **Attribute Inspector**: <br/>![](databinding-images/collection05.png)
3. Enter `PersonModel` for the **Class Name**, click the **Plus** button and add four Keys. Name them `Icon`, `Name`, `Occupation` and `isManager`: <br/>![](databinding-images/table05.png)
4. This tells the Array Controller what it is managing an array of, and which properties it should expose (via Keys).
5. Switch to the **Bindings Inspector** and under **Content Array** select **Bind to** and **File's Owner**. Enter a **Model Key Path** of `self.personModelArray`: <br/>![](databinding-images/table06.png)
6. This ties the Array Controller to the array of `PersonModels` that we exposed on our View Controller.

Now we need to bind our Collection View to the Array Controller, do the following:

1. Select the Collection View and the **Binding Inspector**: <br/>![](databinding-images/collection06.png)
2. Under the **Contents** turndown, select **Bind to** and **Array Controller**. Enter `arrangedObjects` for the **Controller Key** field: <br/>![](databinding-images/collection07.png)
3. Under the **Selection Indexes** turndown, select **Bind to** and **Array Controller**. Enter `selectionIndexes` for the **Controller Key** field: <br/>![](databinding-images/collection08.png)
4. Select the **Image View**. In the **Bindings Inspector** under the **Value** turndown, select **Bind to** and **Person** (the name of our Collection View Item). Enter `representedObject.Icon` for the **Model Key Path**: <br/>![](databinding-images/collection09.png)
5. `representedObject` is the current `PersonModel` in the array being managed by the Array Controller.
6. Select the first **Label**. In the **Bindings Inspector** under the **Value** turndown, select **Bind to** and **Collection View Item**. Enter `representedObject.Name` for the **Model Key Path**: <br/>![](databinding-images/collection10.png)
7. Select the second **Label**. In the **Bindings Inspector** under the **Value** turndown, select **Bind to** and **Collection View Item**. Enter `representedObject.Occupation` for the **Model Key Path**: <br/>![](databinding-images/collection11.png)
8. Select the `NSBox`. In the **Bindings Inspector** under the **Hidden** turndown, select **Bind to** and **Collection View Item**. Enter `selected` for the **Model Key Path** and `NSNegateBoolean` for the **Value Transformer**: <br/>![](databinding-images/collection12.png)
9. Save your changes and return to Visual Studio for Mac to sync with Xcode.

If we run the application, the table will be populated with our array of `PersonModels`:

![](databinding-images/collection13.png)

For more information on working with Collection Views, please see our [Collection Views](~/mac/user-interface/collection-view.md) documentation.-->

## <a name="debugging-native-crashes"></a>Depuração nativas falhas

Cometer um erro em suas associações de dados pode resultar em uma _falhas nativo_ em código não gerenciado e fazer com que seu aplicativo Xamarin.Mac completamente falhe com um `SIGABRT` erro:

[![Exemplo de uma caixa de diálogo Falha nativo](databinding-images/debug01.png "exemplo de uma caixa de diálogo Falha nativo")](databinding-images/debug01-large.png#lightbox)

Normalmente há quatro causas principais para nativo falhas durante a associação de dados:

1. O modelo de dados não herda de `NSObject` ou uma subclasse de `NSObject`.
2. Você não expôs sua propriedade Objective-C usando o `[Export("key-name")]` atributo.
3. Não encapsular alterações para valor do acessador em `WillChangeValue` e `DidChangeValue` chamadas de método (especificando a mesma chave que o `Export` atributo).
4. Você tem uma chave incorreta ou erros de digitação **Inspetor associação** no construtor de Interface.

### <a name="decoding-a-crash"></a>Decodificação de uma falha

Vamos causa uma falha de nativo em nossa associação de dados, para que possa mostrar como localizar e corrigi-lo. No construtor de Interface, vamos alterar nossa associação do primeiro rótulo no exemplo a exibição de coleção do `Name` para `Title`:

[![Editar a chave de associação](databinding-images/debug02.png "editando a chave de associação")](databinding-images/debug02-large.png#lightbox)

Vamos salvar a alteração, retorne ao Visual Studio para Mac sincronizar com o Xcode e executar o nosso aplicativo. Quando o modo de exibição de coleção é exibido, o aplicativo momentaneamente falhará com um `SIGABRT` erro (conforme mostrado no **saída do aplicativo** no Visual Studio para Mac) desde o `PersonModel` não expõe uma propriedade com a chave `Title`:

[![Exemplo de um erro de associação](databinding-images/debug03.png "exemplo de um erro de associação")](databinding-images/debug03-large.png#lightbox)

Se podemos rolar para o início do erro no **saída do aplicativo** podemos ver a chave para solucionar o problema:

[![Encontrando o problema no log de erros](databinding-images/debug04.png "encontrando o problema no log de erros")](databinding-images/debug04-large.png#lightbox)

Esta linha está dizendo que a chave `Title` não existe no objeto que estamos ligando. Se podemos alterar a associação de volta para `Name` no construtor de Interface, salvar, sincronização, recriar e executado, o aplicativo será executado como esperado sem problemas.

## <a name="summary"></a>Resumo

Este artigo obteve uma visão detalhada de como trabalhar com codificação de chave-valor em um aplicativo Xamarin.Mac e associação de dados. Primeiro, ele observou expor uma classe do c# para Objective-C usando a codificação de chave-valor (KVC) e chave-valor observando (KVO). Em seguida, ele mostrou como usar uma classe compatível com KVO e dados associação-lo a elementos de interface do usuário no construtor de Interface do Xcode. Finalmente, mostramos associação de dados complexos com **controladores de matriz** e **árvore controladores**.


## <a name="related-links"></a>Links relacionados

- [MacDatabinding Storyboard (exemplo)](https://developer.xamarin.com/samples/mac/MacDatabinding-Storyboard/)
- [MacDatabinding XIBs (exemplo)](https://developer.xamarin.com/samples/mac/MacDatabinding-XIBs/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Controles padrão](~/mac/user-interface/standard-controls.md)
- [Modos de exibição de tabela](~/mac/user-interface/table-view.md)
- [Modos de exibição de estrutura de tópicos](~/mac/user-interface/outline-view.md)
- [Exibições de coleção](~/mac/user-interface/collection-view.md)
- [Guia de programação de codificação de chave-valor](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueCoding/index.html)
- [Introdução ao guia de programação de chave-valor observando](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueObserving/KeyValueObserving.html)
- [Introdução às associações Cocoa tópicos de programação](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/CocoaBindings/CocoaBindings.html)
- [Introdução à referência de associações de Cocoa](https://developer.apple.com/library/content/documentation/Cocoa/Reference/CocoaBindingsRef/CocoaBindingsRef.html)
- [NSCollectionView](https://developer.apple.com/documentation/appkit/nscollectionview)
- [macOS diretrizes de Interface Humana](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
